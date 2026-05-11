# Auditorías GD

Este repositorio contiene la aplicación Android para auditorías y un workflow de GitHub Actions que genera un APK `release` firmado y lo sube al Release cuando se crea un tag `v*`.

## Qué hace el workflow
- Decodifica un keystore proporcionado como secret `KEYSTORE_BASE64`.
- Escribe `signing.properties` usando los secrets: `KEYSTORE_PASSWORD`, `KEY_ALIAS`, `KEY_PASSWORD`.
- Ejecuta `./gradlew assembleRelease` para compilar el APK firmado.
- Crea un Release en GitHub y sube el `app-release-<tag>.apk`.

El workflow está en: `.github/workflows/build-release.yml`

## Secrets necesarios (GitHub → Settings → Secrets → Actions)
- `KEYSTORE_BASE64` : contenido del `keystore/release.jks` en base64 (sin saltos de línea).
- `KEYSTORE_PASSWORD` : contraseña del keystore.
- `KEY_ALIAS` : alias de la clave (key alias).
- `KEY_PASSWORD` : contraseña de la clave.

### Cómo generar `KEYSTORE_BASE64`
En Linux / macOS:
```bash
base64 -w0 keystore/release.jks
```
Copiar la salida y pegarla en el secret `KEYSTORE_BASE64`.

En PowerShell (Windows):
```powershell
[Convert]::ToBase64String([IO.File]::ReadAllBytes('keystore\\release.jks'))
```
Copia la salida y pégala en el secret `KEYSTORE_BASE64`.

> Nota: el keystore ya existe en `keystore/release.jks` del repositorio. Si prefieres usar otro keystore, genera uno y usa los comandos anteriores.

## Cómo disparar la compilación y publicar un Release
1. Crea un tag semántico localmente (ejemplo `v1.0.1`):
```bash
git tag -a v1.0.1 -m "Release v1.0.1"
```
2. Empuja el tag al remoto:
```bash
git push origin v1.0.1
```
Al hacer push del tag, GitHub Actions ejecutará el workflow, compilará el APK firmado y creará un Release con el APK adjunto.

## Recomendaciones
- Revisa que los secrets estén correctamente pegados y sin espacios finales.
- Si necesitas volver a ejecutar para un tag existente, crea un nuevo tag (p. ej. `v1.0.2`) o borra/re-crea el tag remoto.

## Descarga del APK
- El workflow adjunta el APK al Release; después de que termine, ve a la página de Releases en GitHub y descarga el `app-release-<tag>.apk`.

Si quieres, puedo:
- Añadir instrucciones para firmar con otro keystore,
- Automatizar la subida del `keystore` al secret desde aquí (necesitaría tu token),
- O crear un `release` manual ahora usando la API si me facilitas un token con permisos `repo`.
