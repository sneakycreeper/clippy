# Clippy

[English](README.md) | [Español](README.es.md)

Grabador de repeticiones instantáneas para Windows, ultraliviano: graba siempre en segundo plano
(en RAM) y con una tecla (F9) guarda los últimos segundos como MP4. Todo el video pasa por la GPU
(DXGI Desktop Duplication → D3D11 NV12 → encoder H.264 por hardware de Media Foundation).

La interfaz está disponible en español, inglés y portugués. El modo Automático sigue el idioma de
Windows, y también se puede elegir un idioma específico en General → Idioma.

Descarga y novedades en [clpy.vercel.app](https://clpy.vercel.app). Las versiones se publican en
los [Releases](https://github.com/santiremedi/clippy/releases) del proyecto original, que también
usa el actualizador automático.

## Compilar

```bash
cargo build --release
```

El exe queda en `target/release/clippy.exe`. Para publicar se renombra a `Clippy.exe`.

Para probar mientras desarrollás, con los modos de prueba sin ventana:

```bash
cargo build --profile fast --features devtools
```

## Modos del mismo .exe

| Argumentos | Qué hace |
| --- | --- |
| (ninguno) / `--background` | Grabadora con ícono en la bandeja (`--background`: sin abrir la ventana, para el inicio con Windows). |
| `--settings` | Ventana principal / ajustes. |
| `--editor [archivo]` | Editor de clips. |
| `--apply-update <ruta>` | Lo usa el actualizador: reemplaza el exe instalado y lo vuelve a abrir. |

Solo con `--features devtools`:

| Argumentos | Qué hace |
| --- | --- |
| `--test <seg> <carpeta> [game\|desktop\|mic\|juegos\|720\|1080\|1440]` | Graba, guarda un clip y sale. |
| `--test-mic` | Cuánto audio entrega cada micrófono en 3 s. |
| `--test-download` | Descarga la versión publicada como el botón “Actualizar”. |
| `--test-export`, `--test-frame`, `--test-strip` | Pruebas del editor; ver `src/editor/devtools.rs`. |

## Archivos del usuario

Todo en `%APPDATA%\Clippy`: `config.json`, `editor.json`, `library.json`, `update.json`,
`clippy.log` y `cache\`.

## Licencia

[GPL-3.0-or-later](LICENSE). Podés usar, estudiar, cambiar y redistribuir Clippy; si distribuís una
versión modificada, tiene que ser con su código y bajo la misma licencia. El nombre “Clippy” y su
ícono no se pueden usar para identificar versiones modificadas.
