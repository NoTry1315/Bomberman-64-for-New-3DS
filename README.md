
# Bomberman 64 for New 3DS

**[Español](#español) · [English](#english)**

---

## Español

Port de Bomberman 64 a New Nintendo 3DS a partir de [BM64Recomp](https://github.com/RevoSucks/BM64Recomp)
([N64Recomp](https://github.com/N64Recomp/N64Recomp)), con un mundo nuevo (Purple Moon) y Bomber World.

**Este repositorio no contiene el juego.** El instalador construye el juego en tu ordenador a partir de **tu propia
ROM** de Bomberman 64 (versión USA), volcada de tu cartucho. El código, las texturas, los modelos y los retratos se
sacan de esa ROM en tu máquina y no se suben a ningún sitio. Las pocas piezas propias que van incluidas están cifradas
con la ROM: sin ella son ruido.

### Lo que necesitas

1. Tu ROM de **Bomberman 64 (USA)**, en `.z64`, `.v64` o `.n64`.
2. **Docker**, abierto y funcionando:
   - Windows y macOS: [Docker Desktop](https://www.docker.com/products/docker-desktop/)
   - macOS (alternativa): [OrbStack](https://orbstack.dev/)
   - Linux: Docker Engine
3. Unos **4 GB libres** y conexión a internet (la primera vez descarga ~1 GB de herramientas).
4. Una **New 3DS, New 3DS XL o New 2DS XL** con Luma3DS y el Homebrew Launcher
   ([guía](https://3ds.hacks.guide/es_ES/)).

### Paso 1: construir el juego

Descarga este repositorio (botón verde **Code → Download ZIP**, y descomprímelo) o clónalo con git.

- **Windows**: haz doble clic en `instalar.bat` y elige tu ROM (o arrastra la ROM encima de `instalar.bat`).
- **macOS / Linux**: abre una terminal en la carpeta y escribe:
  ```bash
  ./instalar.sh ruta/a/tu/rom.z64
  ```

La primera vez tarda entre 20 y 40 minutos (deja la ventana abierta); las siguientes, unos minutos. Al terminar
aparece una carpeta `salida`.

### Paso 2: copiar a la tarjeta SD

Copia el contenido de `salida/SD` a la raíz de tu tarjeta SD:

| Archivo | Qué es |
| --- | --- |
| `SD:/3ds/bm64.3dsx` | el juego |
| `SD:/3ds/bm64/` | tu ROM y los mapas de la pantalla inferior |

También necesitas el firmware de sonido `SD:/3ds/dspfirm.cdc`. Si no lo tienes, abre una vez la app **DSP1** desde
el Homebrew Launcher (sin él no hay sonido).

### Paso 3: jugar

- Desde el Homebrew Launcher, elige **Bomberman 64**.
- Opcional, icono en el menú HOME: descarga `bm64_forwarder.cia` de
  [Releases](../../releases), cópialo a la SD e instálalo con FBI. Aparecerá *Bomberman 64 For 3DS*.
- Para salir del juego: **SELECT + START**.
- Tus partidas se guardan en `SD:/3ds/bm64/saves/`.

Para actualizar: descarga la versión nueva, vuelve a ejecutar el instalador y reemplaza `SD:/3ds/bm64.3dsx`. Tus
partidas no se tocan.

### Si algo falla

- **"Hace falta Docker"**: instala Docker Desktop, ábrelo, espera a que diga que está en marcha y vuelve a probar.
- **"La ROM no es Bomberman 64 (USA) 1.0"**: tu ROM es de otra región o está modificada. Hace falta la versión USA
  original.
- **Se cortó a medias**: vuelve a ejecutarlo; sigue donde se quedó.

> Nunca compartas tu ROM ni la carpeta `salida` (lleva tu ROM).

### Qué hace el instalador

Todo ocurre dentro de un contenedor de Docker (`instalador/Dockerfile`, con devkitARM fijado a una versión), por eso
funciona igual en los tres sistemas, y el `bm64.3dsx` que sale es **idéntico, byte a byte**, al del juego tal como se
probó. `instalador/construir.sh`:

1. comprueba que la ROM es Bomberman 64 (USA) 1.0 sin modificar (`instalador/rom.py`);
2. descarga BM64Recomp en un commit fijo y la herramienta de descompresión de la
   [decompilación](https://github.com/Bomberhackers/bm64), y descomprime tu ROM;
3. compila N64Recomp y traduce el código del juego a C (con los parches de `recursos/parches`);
4. saca de tu ROM las texturas de las salas (`recursos/texturas_rom.json`), los retratos, las páginas de los diálogos
   y la pantalla de inicio;
5. abre con tu ROM las piezas que no se pueden sacar de ella (`recursos/cerrado`: el modelo de Nitros, el logo de la
   pantalla inferior y sus mapas);
6. compila `bm64.3dsx` con `build_rt.sh` y prepara la carpeta para la SD.

Sin Docker, en Linux o macOS con devkitPro (devkitARM, libctru, citro3d), clang, lld, cmake, ninja, Python 3 con
Pillow y la fuente Liberation Serif, puedes ejecutar directamente `instalador/construir.sh tu_rom.z64 salida`.

### Para desarrollar

Todo lo que difiere del escritorio vive aquí; los submódulos de `BM64Recomp/lib` no se tocan. `build_rt.sh` compila
desde BM64Recomp (variable `BM64`) y prefiere las copias de este directorio (`*-src`, `*-include`) a las originales.
Resultado: `$BUILD_TMP/bm64.3dsx`. Las rutas se configuran con variables (ver la cabecera de `build_rt.sh`).

- `main/`: punto de entrada, entrada, sonido (ndsp), informe por segundo, interruptores de arranque.
- `render/`: renderer citro3d con el intérprete Fast3D de libultraship (`render/fast`, MIT).
- `audio-hle/`: comandos de audio de mupen64plus-rsp-hle (GPLv2 o posterior), en lugar del `aspMain` recompilado.
- `librecomp-src/`, `ultramodern-src/`, `*-include/`: copias parcheadas del runtime (cambios bajo `__3DS__`).
- `game/`: Purple Moon, Bomber World y los cambios al juego.
- `recursos/`: los modelos, texturas y sonidos propios del proyecto.
- `instalador/`, `instalar.sh`, `instalar.bat`, `instalar.ps1`: el instalador.
- `forwarder/`: el icono del menú HOME (CIA).
- `tools/`: herramientas (salas, modelos, música, diálogos, análisis); ver `tools/README.md`.

Ningún archivo derivado de la ROM entra en el repositorio sin cifrar (ver `.gitignore`).

---

## English

A port of Bomberman 64 to the New Nintendo 3DS, built on [BM64Recomp](https://github.com/RevoSucks/BM64Recomp)
([N64Recomp](https://github.com/N64Recomp/N64Recomp)), with a new world (Purple Moon) and Bomber World.

**This repository does not contain the game.** The installer builds the game on your computer from **your own ROM**
of Bomberman 64 (USA version), dumped from your cartridge. The code, textures, models and portraits are extracted from
that ROM on your machine and are never uploaded anywhere. The few original pieces that are included are encrypted with
the ROM: without it they are just noise.

### What you need

1. Your **Bomberman 64 (USA)** ROM, as `.z64`, `.v64` or `.n64`.
2. **Docker**, open and running:
   - Windows and macOS: [Docker Desktop](https://www.docker.com/products/docker-desktop/)
   - macOS (alternative): [OrbStack](https://orbstack.dev/)
   - Linux: Docker Engine
3. About **4 GB of free space** and an internet connection (the first run downloads ~1 GB of tools).
4. A **New 3DS, New 3DS XL or New 2DS XL** with Luma3DS and the Homebrew Launcher
   ([guide](https://3ds.hacks.guide/)).

### Step 1: build the game

Download this repository (green **Code → Download ZIP** button, then unzip it) or clone it with git.

- **Windows**: double-click `instalar.bat` and pick your ROM (or drag the ROM onto `instalar.bat`).
- **macOS / Linux**: open a terminal in the folder and run:
  ```bash
  ./instalar.sh path/to/your/rom.z64
  ```

The first run takes 20 to 40 minutes (keep the window open); later runs take a few minutes. When it finishes, a
`salida` ("output") folder appears.

### Step 2: copy to the SD card

Copy the contents of `salida/SD` to the root of your SD card:

| File | What it is |
| --- | --- |
| `SD:/3ds/bm64.3dsx` | the game |
| `SD:/3ds/bm64/` | your ROM and the bottom-screen maps |

You also need the sound firmware `SD:/3ds/dspfirm.cdc`. If you don't have it, open the **DSP1** app once from the
Homebrew Launcher (without it there is no sound).

### Step 3: play

- From the Homebrew Launcher, choose **Bomberman 64**.
- Optional, HOME menu icon: download `bm64_forwarder.cia` from [Releases](../../releases), copy it to the SD card and
  install it with FBI. *Bomberman 64 For 3DS* will appear.
- To quit the game: **SELECT + START**.
- Your saves are stored in `SD:/3ds/bm64/saves/`.

To update: download the new version, run the installer again and replace `SD:/3ds/bm64.3dsx`. Your saves are kept.

### Troubleshooting

- **"Hace falta Docker"** (Docker is needed): install Docker Desktop, open it, wait until it says it is running, and
  try again.
- **"La ROM no es Bomberman 64 (USA) 1.0"** (the ROM is not Bomberman 64 USA 1.0): your ROM is from another region or
  has been modified. You need the original USA version.
- **It stopped halfway**: run it again; it resumes where it left off.

> Never share your ROM or the `salida` folder (it contains your ROM).

### What the installer does

Everything runs inside a Docker container (`instalador/Dockerfile`, with a pinned devkitARM version), so it works the
same on all three systems, and the resulting `bm64.3dsx` is **byte-for-byte identical** to the tested game.
`instalador/construir.sh`:

1. checks that the ROM is an unmodified Bomberman 64 (USA) 1.0 (`instalador/rom.py`);
2. downloads BM64Recomp at a fixed commit and the decompression tool from the
   [decompilation](https://github.com/Bomberhackers/bm64), and decompresses your ROM;
3. builds N64Recomp and translates the game's code to C (with the patches in `recursos/parches`);
4. extracts from your ROM the room textures (`recursos/texturas_rom.json`), the portraits, the dialogue pages and the
   title screen;
5. unlocks with your ROM the pieces that can't be extracted from it (`recursos/cerrado`: Nitros' model, the
   bottom-screen logo and its maps);
6. builds `bm64.3dsx` with `build_rt.sh` and prepares the SD folder.

Without Docker, on Linux or macOS with devkitPro (devkitARM, libctru, citro3d), clang, lld, cmake, ninja, Python 3
with Pillow and the Liberation Serif font, you can run `instalador/construir.sh your_rom.z64 salida` directly.

### For developers

Everything that differs from the desktop version lives here; the `BM64Recomp/lib` submodules are left untouched.
`build_rt.sh` builds from BM64Recomp (variable `BM64`) and prefers this directory's copies (`*-src`, `*-include`)
over the originals. Output: `$BUILD_TMP/bm64.3dsx`. Paths are set with variables (see the header of `build_rt.sh`).

- `main/`: entry point, input, sound (ndsp), per-second report, boot switches.
- `render/`: citro3d renderer using libultraship's Fast3D interpreter (`render/fast`, MIT).
- `audio-hle/`: audio commands from mupen64plus-rsp-hle (GPLv2 or later), instead of the recompiled `aspMain`.
- `librecomp-src/`, `ultramodern-src/`, `*-include/`: patched copies of the runtime (changes under `__3DS__`).
- `game/`: Purple Moon, Bomber World and the changes to the game.
- `recursos/`: the project's own models, textures and sounds.
- `instalador/`, `instalar.sh`, `instalar.bat`, `instalar.ps1`: the installer.
- `forwarder/`: the HOME menu icon (CIA).
- `tools/`: tools (rooms, models, music, dialogues, analysis); see `tools/README.md`.

No ROM-derived file enters the repository unencrypted (see `.gitignore`).

---

## Licencia / License

GPLv3 (como N64ModernRuntime y BM64Recomp, de los que deriva parte del código / like N64ModernRuntime and BM64Recomp,
from which part of the code derives), ver / see `LICENSE`. Bomberman 64 es de / is owned by Hudson Soft / Konami;
este proyecto no está afiliado a ellos / this project is not affiliated with them.
