# SRB2 Recomp en Español — Guía del Proyecto

> **Nombre del proyecto:** SRB2 Spanish Recomp
> **Versión base:** SRB2 v2.2.15 (tag `SRB2_release_2.2.15`, commit `8701ef41`)
> **Versión actual:** v1.3.1
> **Plataformas:** Windows 64-bit / Linux 64-bit (CachyOS, Arch, etc.) / Android (arm64-v8a)
> **Última actualización:** 2026-10-27

---

## Índice

1. [¿Qué es este proyecto?](#1-qué-es-este-proyecto)
2. [Arquitectura y estrategia](#2-arquitectura-y-estrategia)
3. [Estructura de carpetas](#3-estructura-de-carpetas)
4. [Cómo compilar](#4-cómo-compilar)
5. [Cómo editar traducciones](#5-cómo-editar-traducciones)
6. [Reglas de traducción](#6-reglas-de-traducción)
7. [Glosario de vocabulario fijado](#7-glosario-de-vocabulario-fijado)
8. [Mapa de archivos fuente](#8-mapa-de-archivos-fuente)
9. [Historial de lotes](#9-historial-de-lotes)
10. [Lo que ya esta traducido](#10-lo-que-ya-esta-traducido)
11. [Compatibilidad online](#11-compatibilidad-online)
12. [Cómo jugar](#12-cómo-jugar)
13. [Problemas conocidos y trampas](#13-problemas-conocidos-y-trampas)
14. [FAQ](#14-faq)

---

## 1. ¿Qué es este proyecto?

SRB2 (Sonic Robo Blast 2) tiene todos sus textos hardcodeados en C: menús, HUD, mensajes de confirmación, intermisiones, títulos de nivel, etc. Un mod `.pk3` puede cambiar gráficos y agregar Lua/SOC, pero **no puede tocar esos strings en C**.

Este proyecto **recompila el motor** con los textos traducidos al español. El resultado es un binario nativo (`srb2win_es.exe` en Windows, `srb2linux_es` en Linux y APK instalable en Android) que:

- Muestra todo en español (menús, HUD, mensajes, tutorial, intermisiones y pistas)
- **Sigue siendo compatible con servidores vanilla** (se puede jugar online)
- Se complementa con un `.pk3` cosmético para los gráficos traducidos

### Origen

Previamente existía un mod de traducción (`srb2_spanish.pk3`) con:

- Gráficos de menú/HUD traducidos
- Scripts Lua para el HUD en juego (`hud_es.lua`, `titlecard_es.lua`)
- SOC para nombres de niveles, unlockables y cutscenes

El problema: **el Lua y el SOC bloquean el multijugador** (el servidor exige que todos tengan los mismos archivos). La recomp mueve todo lo posible a C para que funcione online.

---

## 2. Arquitectura y estrategia

```
┌───────────────────────────────────────────────┐
│                JUEGO TRADUCIDO                │
├────────────────────┬──────────────────────────┤
│  srb2win_es.exe    │  srb2_spanish_gfx.pk3    │
│  (motor en C)      │  (gráficos cosméticos)   │
│                    │                          │
│  • Menús           │  • Cabeceras de menú     │
│  • HUD             │  • Iconos HUD            │
│  • Mensajes        │  • Fuentes extendidas:   │
│  • Intermisiones   │    12 LTFNT (Titlecard)  │
│  • Titlecards      │    16 STCFN (texto std)  │
│  • Unlockables     │  • Parches visuales      │
│  • Soporte Latin-1 │                          │
│  • Fallback font   │  100 archivos, todos     │
│                    │  lumps cosméticos        │
├────────────────────┴──────────────────────────┤
│  RESULTADO: Compatible con servidores         │
│  vanilla. Se puede jugar online.              │
└───────────────────────────────────────────────┘
```

### ¿Qué va en cada parte?

| Contenido                     | Dónde vive               | Por qué                             |
| ----------------------------- | ------------------------ | ----------------------------------- |
| Textos de menú, HUD, mensajes | C (exe/app recompilada)  | No se pueden cambiar con pk3        |
| Gráficos de menú/HUD          | pk3 cosmético            | Los lumps cosméticos no bloquean MP |
| Nombres de niveles (SOC)      | C (tablas de traducción) | El SOC bloquea MP                   |
| Lua de HUD                    | C (portado)              | El Lua bloquea MP                   |
| Tutorial (SOC)                | C (portado en Lote 16)   | El SOC bloquea MP                   |
| Cutscenes de historia (SOC)   | Pendiente de portar a C  | El SOC bloquea MP                   |

---

## 3. Estructura de carpetas

Layout del espacio de trabajo local:

```
SRB2-recomp\
├── SRB2\                          ← Código fuente (tag 2.2.15)
│   ├── src\                       ← Archivos .c/.h editados
│   │   ├── m_menu.c               ← Menús (el archivo más editado)
│   │   ├── st_stuff.c             ← HUD in-game
│   │   ├── y_inter.c              ← Pantallas de intermisión
│   │   ├── f_finale.c             ← Créditos, intro, continue
│   │   ├── deh_soc.c              ← Parser SOC + tablas de traducción
│   │   ├── m_cond.c               ← Condiciones de desbloqueo
│   │   ├── g_game.c               ← Récords, replays, mensajes
│   │   ├── p_inter.c              ← Mensajes de combate/items
│   │   └── command.c              ← Cvars (On/Off/Yes/No)
│   ├── bin\                       ← Salida del build
│   │   ├── srb2win64.exe          ← Exe Windows recién compilado
│   │   └── lsdl2srb2              ← Binario Linux recién compilado
│   └── libs\                      ← DLLs del proyecto (Windows)
│
├── dist\                          ← DISTRIBUCIÓN LISTA
│   ├── srb2win_es.exe             ← Exe Windows actual (último build, = lote 20)
│   ├── srb2linux_es               ← Binario ejecutable Linux 64-bit (traducido)
│   ├── srb2linux                  ← Copia/alias ejecutable Linux
│   ├── srb2win_es_p1.exe          ← Snapshot lote 1
│   ├── ...                        ← Snapshots lote 2-12
│   ├── srb2win_es_p13.exe         ← Snapshot lote 13
│   ├── srb2win_es_p16.exe         ← Snapshot lote 16 (último snapshot numerado)
│   ├── *.dll                      ← Todas las DLLs necesarias (Windows)
│   ├── addons\
│   │   └── srb2_spanish_gfx.pk3   ← Gráficos + fuentes LTFNT/STCFN (100 archivos)
│   ├── Jugar_online.bat           ← Windows: EN USO (Exe + pk3 cosmético, MP)
│   ├── Jugar_traducido.bat        ← Windows: legado (ya no se usa)
│   ├── Jugar_vanilla.bat          ← Windows: legado (ya no se usa)
│   ├── Jugar_online.sh            ← Linux: EN USO (Lanzador + pk3 cosmético, MP)
│   ├── Jugar_traducido.sh         ← Linux: legado (ya no se usa)
│   └── Jugar_vanilla.sh           ← Linux: legado (ya no se usa)
│
├── build_patchN.log               ← Un log por cada build
└── GUIA.md                        ← Documentación central del proyecto (este archivo)
```

> **En el repositorio de GitHub:** la carpeta `SRB2\` corresponde a la raíz del repo (`src/`, `libs/`, etc.). `dist\` y los ejecutables (`.exe` / `srb2linux_es`) **no se versionan**: los binarios se publican en [Releases](https://github.com/trulydza/SRB2-Spanish-Recomp/releases).

### Ecosistema de Repositorios

El proyecto se distribuye en 4 repositorios oficiales interconectados:
1. **[SRB2-Spanish](https://github.com/trulydza/SRB2-Spanish)**: Portal central / Hub de distribución, descargas de releases y extras/mods traducidos.
2. **[SRB2-Spanish-Recomp](https://github.com/trulydza/SRB2-Spanish-Recomp)**: Repositorio maestro del motor C (Windows x64 / MinGW-w64).
3. **[SRB2-Spanish-Recomp-Linux](https://github.com/trulydza/SRB2-Spanish-Recomp-Linux)**: Repositorio de distribución para Linux (binarios ELF nativos y scripts de ejecución).
4. **[SRB2-Spanish-Recomp-Android](https://github.com/trulydza/SRB2-Spanish-Recomp-Android)**: Port oficial para Android (arm64-v8a) con controles táctiles en pantalla y aceleración OpenGL ES (`gl4es`).

### Carpetas externas (SOLO LECTURA)

| Carpeta                       | Contenido                           | Regla           |
| ----------------------------- | ----------------------------------- | --------------- |
| `carpeta_de_instalacion_SRB2` | Juego original, saves, gamedata.dat | **NUNCA TOCAR** |
| `carpeta_del_mod\`            | Mod original `srb2_spanish.pk3`     | Solo lectura    |

---

## 4. Cómo compilar

### Compilación en Windows

#### Requisitos (Windows)

- **MSYS2** instalado en `C:\msys64`
- Toolchain **MinGW-w64 x86_64** (64-bit)
- Paquetes: `mingw-w64-x86_64-gcc`, `make`, `mingw-w64-x86_64-SDL2`, `mingw-w64-x86_64-libgme`, `mingw-w64-x86_64-libopenmpt`

#### Comando de build (Windows)

Desde una terminal MSYS2 MinGW64, o con el siguiente argv:

```bash
# Opción 1: desde una terminal MSYS2 MinGW64
cd /ruta/hacia/SRB2-recomp/SRB2
CFLAGS="-Wno-error=incompatible-pointer-types" make -C src MINGW64=1 -j4

# Opción 2: desde Windows (argv para herramientas)
C:\msys64\usr\bin\env.exe MSYSTEM=MINGW64 \
  CFLAGS=-Wno-error=incompatible-pointer-types \
  C:\msys64\usr\bin\bash.exe -lc \
  "cd /ruta/hacia/SRB2-recomp/SRB2 && make -C src MINGW64=1 -j4"
```

> **Importante:** el flag `-Wno-error=incompatible-pointer-types` es necesario porque GCC 16 trata esos warnings como error y el código de SRB2 los tiene.

#### Después del build (Windows)

```bash
# Copiar el exe a dist (sobrescribir)
cp SRB2/bin/srb2win64.exe dist/srb2win_es.exe

# Guardar snapshot con el número de lote (no sobrescribir si existe)
cp -n dist/srb2win_es.exe dist/srb2win_es_pN.exe

# Verificar
md5sum dist/srb2win_es.exe dist/srb2win_es_pN.exe
```

(`N` = número del lote.)

#### Build limpio (Windows)

```bash
cd /ruta/hacia/SRB2-recomp/SRB2
make -C src MINGW64=1 clean
CFLAGS="-Wno-error=incompatible-pointer-types" make -C src MINGW64=1 -j4
```

---

### Compilación en Linux (CachyOS / Arch Linux / Ubuntu)

#### Requisitos y Dependencias (Linux)

En distribuciones basadas en Arch Linux (CachyOS, EndeavourOS, Manjaro) con `pacman`:
```bash
sudo pacman -S --needed base-devel miniupnpc sdl2_mixer libpng libgme libopenmpt curl mesa glu
```
*(Nota: En CachyOS/Arch, SDL2 viene proporcionado de forma moderna por `sdl2-compat` o `sdl2`).*

En Debian / Ubuntu:
```bash
sudo apt install build-essential libsdl2-mixer-dev libminiupnpc-dev libpng-dev libgme-dev libopenmpt-dev libcurl4-openssl-dev libgl1-mesa-dev libglu1-mesa-dev
```

#### Compatibilidad con GCC 15/16 y C23
En compiladores recientes como GCC 16 (predeterminado en CachyOS/Arch rolling), el compilador activa el estándar C23 por defecto, donde `true` y `false` son palabras reservadas del lenguaje. Esto causaría un conflicto con `enum {false = 0, true = 1};` en `doomtype.h`.
Para garantizar compilación limpia sin errores de tipos o palabras clave, el Makefile ya incluye `-std=gnu11` en la variable `opts`.

#### Comando de Build en Linux
Entrar a la carpeta `SRB2/src/` y ejecutar:
```bash
cd SRB2/src
make LINUX64=1 NOUPX=1 -j$(nproc)
```
El ejecutable resultante se genera en: `SRB2/bin/lsdl2srb2`.

#### Sincronización después del Build en Linux
```bash
cp SRB2/bin/lsdl2srb2 dist/srb2linux_es
cp SRB2/bin/lsdl2srb2 dist/srb2linux
cp SRB2/bin/lsdl2srb2 srb2linux_es
cp SRB2/bin/lsdl2srb2 run/srb2linux_es
chmod +x dist/srb2linux_es dist/srb2linux srb2linux_es run/srb2linux_es
```

#### Build incremental en Linux
Solo recompila los archivos modificados. Tarda unos pocos segundos gracias al paralelismo (`-j$(nproc)`).

#### Build limpio en Linux (si algo falla o tras cambios estructurales)
```bash
cd SRB2/src
make LINUX64=1 clean
make LINUX64=1 NOUPX=1 -j$(nproc)
```

---

### Compilación en Android (arm64-v8a)

El port para Android se compila desde el subproyecto `SRB2/android-port/` en la rama `android-port` (o el repositorio independiente `SRB2-Spanish-Recomp-Android`).

#### Requisitos y Toolchain
- **JDK**: Eclipse Temurin 17 (o superior) en `/home/daza/android/jdk`
- **Android SDK**: `/home/daza/android/sdk` (API 34/36)
- **Android NDK**: r25c (`25.1.8937393`) en `/home/daza/android/sdk/ndk/25.1.8937393`
- **CMake**: `3.22.1`

#### Comando de Build (Release)
```bash
cd "SRB2/android-port"
export JAVA_HOME="/home/daza/android/jdk"
export ANDROID_HOME="/home/daza/android/sdk"
export ANDROID_NDK_HOME="/home/daza/android/sdk/ndk/25.1.8937393"
./gradlew assembleRelease
```
El APK firmado resultante se genera en:
`SRB2/android-port/app/build/outputs/apk/release/app-release.apk`

---

## 5. Cómo editar traducciones

### Método seguro: script Python con validación

Todos los lotes se hicieron con un script Python que:

```python
path = r"SRB2-recomp\SRB2\src\ARCHIVO.c"

with open(path, 'rb') as f:
    data = f.read()

# PASO 1: validar que el texto original existe y aparece N veces
original = b'"TEXTO ORIGINAL"'
assert data.count(original) == 1, f"Esperaba 1, encontré {data.count(original)}"

# PASO 2: reemplazar
data = data.replace(original, b'"TEXTO TRADUCIDO"')

# PASO 3: escribir solo si todo es válido
with open(path, 'wb') as f:
    f.write(data)

print("OK")
```

### ¿Por qué este método?

- Si el texto original cambió o no existe, el `assert` falla y **no se escribe nada**
- Trabaja en binario, así que preserva los finales de línea (`\r\n` en Windows)
- Se puede ejecutar con `python` desde Windows

### Alternativa: edición directa

Para cambios simples se puede editar con cualquier editor, pero **siempre verificar con grep** antes:

```bash
grep -n 'TEXTO_ORIGINAL' src/ARCHIVO.c
```

---

## 6. Reglas de traducción

### Caracteres permitidos

| Permitido                                                   | Notas                                                                                   |
| ----------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| ASCII 32-126 (letras sin acento, números, símbolos básicos) | Siempre disponible                                                                      |
| Vocales acentuadas: á é í ó ú (y mayúsculas Á É Í Ó Ú)      | Via hex: `\xE1`, `\xE9`, `\xED`, `\xF3`, `\xFA`, `\xC1`, `\xC9`, `\xCD`, `\xD3`, `\xDA` |
| Eñe: ñ Ñ                                                    | Via hex: `\xF1`, `\xD1`                                                                 |
| Diéresis: ü Ü                                               | Via hex: `\xFC`, `\xDC`                                                                 |
| Signos de apertura: ¡ ¿                                     | Via hex: `\xA1`, `\xBF`                                                                 |
| Códigos de control del motor (`\x82`, `\x83`, etc.)         | Formato visual                                                                          |

> Se crearon glifos nativos para **LTFNT** (fuente de Titlecard, 12 glifos) y **STCFN** (fuente estándar de menús/HUD, 16 glifos) empaquetados en `srb2_spanish_gfx.pk3`. El motor también tiene `V_FontFallback` que sustituye por la letra base sin acento si el glifo no existe.

### Estilo

- **Resumir si es largo.** Se prefieren textos cortos que quepan en pantalla. Ejemplo: `"Complete 1P mode with all emeralds"` → `"Completa 1P con todas las esmeraldas"`.
- **No traducir literal** si no suena natural en español.
- **Signos de apertura:** ahora se pueden usar `¡` y `¿` con hex escapes: `"\xA1""Conquista..."`, `"\xBF""Seguro...?"`.

### Cosas que NO se tocan

| Qué                    | Ejemplo                                                           | Por qué                                      |
| ---------------------- | ----------------------------------------------------------------- | -------------------------------------------- |
| Format specifiers      | `%d`, `%s`, `%hu`, `%hd`                                          | El motor los necesita                        |
| Newlines               | `\n`                                                              | Estructura del texto                         |
| Códigos de color       | `\x82` (amarillo), `\x83` (verde), `\x85` (rojo), `\x80` (blanco) | Formato visual                               |
| Flechas de ejes        | `\x17`, `\x18`                                                    | Iconos del gamepad                           |
| Teclas de confirmación | `'Y'` / `'N'`                                                     | El motor lee las teclas físicas Y/N, nunca S |

### Cuidado con el hex después de `\xNN`

Si el carácter que sigue a un `\xNN` es un dígito hex (0-9, a-f, A-F), el compilador lo interpreta como parte del escape. Solución: cerrar y reabrir el string.

```c
// MAL: \x82Etapa se lee como \x82E (0x82E no existe)
"\x82Etapa Especial"

// BIEN: separar el string
"\x82""Etapa Especial"
```

### Buffers con tamaño fijo

Algunos strings van a arrays de tamaño fijo. **No exceder el límite:**

| Variable                  | Tamaño máx. | Dónde                                                                |
| ------------------------- | ----------- | -------------------------------------------------------------------- |
| `passed1`                 | 21 chars    | `y_inter.c` (nombre del personaje en intermisión)                    |
| `passed2`                 | 16 chars    | `y_inter.c` (texto de estado coop: `"zona completa"` = 13 chars, OK) |
| `spec.passed2`            | 17 chars    | `y_inter.c` (Special Stage: `"Etapa Especial"` = 14 chars, OK)       |
| `unlockables[].name`      | 64 chars    | `deh_soc.c`                                                          |
| `unlockables[].objective` | 64 chars    | `deh_soc.c`                                                          |
| `levelname`               | 22 chars    | `deh_soc.c` (límite del header SOC)                                  |

---

## 7. Glosario de vocabulario fijado

Estas traducciones ya están aplicadas en el código y deben mantenerse consistentes. Los nombres de niveles y desbloqueables usan hex escapes para acentos (`\xE1`, `\xF1`, etc.); los strings de menú y HUD también pueden usarlos si hay glifos disponibles:

| Inglés        | Español                    | Notas                             |
| ------------- | -------------------------- | --------------------------------- |
| Zone          | Zona                       |                                   |
| Act           | Acto                       |                                   |
| Ring          | Anillo                     |                                   |
| Spin          | Girar                      |                                   |
| Add-ons       | Complementos               |                                   |
| Record Attack | Contrarreloj               |                                   |
| Item Monitors | Monitores                  |                                   |
| Starposts     | Checkpoints                |                                   |
| Special Stage | Etapa Especial             |                                   |
| Options       | Opciones                   |                                   |
| Multiplayer   | Multijugador               |                                   |
| 1 Player      | 1 Jugador                  |                                   |
| Quit          | Salir                      |                                   |
| Secrets       | Extras                     |                                   |
| Pandora's Box | Caja de Pandora            |                                   |
| Emblem Hints  | Pistas de emblemas         |                                   |
| Pause         | Pausa                      |                                   |
| Sound Test    | Prueba de sonido           |                                   |
| Lives         | Vidas                      | No aparece como texto, es gráfico |
| Emerald       | Esmeralda                  |                                   |
| Save          | Guardar / Partida guardada | Según contexto                    |
| Continue      | Continuar                  | En menús de pausa                 |
| Game Over     | Game Over                  | Se mantiene en inglés (es marca)  |

---

## 8. Mapa de archivos fuente

Qué archivo controla qué parte del juego:

| Archivo          | Qué contiene                                                                                                                                                                                                       | Líneas aprox. |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------- |
| `src/m_menu.c`   | **Todo el sistema de menús**: principal, submenús, opciones, Record Attack, NiGHTS, Marathon, servidor, controles, video, sonido, datos, partidas guardadas, quit messages, mensajes de confirmación, estadísticas | ~14000        |
| `src/st_stuff.c` | **HUD in-game**: SCORE/TIME/RINGS (gráficos), titlecard (Zona/Acto), NiGHTS HUD, Game Over, textos de estado, overtime                                                                                             | ~2900         |
| `src/y_inter.c`  | **Intermisiones**: pantalla post-nivel (coop, match, CTF, competition, special stage), conteo de puntaje                                                                                                           | ~1400         |
| `src/f_finale.c` | **Finale/créditos**: texto de introducción (16 páginas), créditos, pantalla Continue, evaluación final, Marathon, cutscenes                                                                                        | ~4700         |
| `src/deh_soc.c`  | **Parser SOC**: lee los archivos SOC del pk3. Aquí viven las tablas de traducción (`M_UnlockTr()`, `M_LevelTr()`, `M_LevelSubTr()`)                                                                                | ~3500         |
| `src/m_cond.c`   | **Condiciones de desbloqueo**: lógica de unlockables y mensaje de "desbloqueado!"                                                                                                                                  | ~400          |
| `src/g_game.c`   | **Lógica del juego**: récords, replays, mensajes de consola (NEW RECORD, Earned emblems)                                                                                                                           | ~4000         |
| `src/p_inter.c`  | **Interacción de jugadores**: mensajes de muerte/combate, CTF flags, power-ups NiGHTS                                                                                                                              | ~2200         |
| `src/command.c`  | **Cvars**: valores On/Off/Yes/No/True/False (delicado, la consola los parsea)                                                                                                                                      | ~1700         |
| `src/hu_stuff.c` | **Chat y mensajes HUD**: comandos say/sayteam/csay                                                                                                                                                                 | ~700          |
| `src/v_video.c`  | **Renderizado de texto**: `V_FontFallback` (fallback de glifos acentuados a letra base)                                                                                                                            | ~4000         |

---

## 9. Historial de lotes

Cada lote es un conjunto de traducciones que se hicieron juntas, se compilaron y se guardaron como snapshot.

### Lote 1 — Menú principal

- **Archivo:** `m_menu.c`
- **Exe:** `dist/srb2win_es_p1.exe`
- **Contenido:** menú principal (1 Jugador, Multijugador, Complementos, Opciones, Salir). Level Select: `ACT %d` → `ACTO %d`, `THE ACT` → `EL ACTO`.
- **Verificado:** sí (confirmado visualmente)

### Lote 2 — Submenús

- **Archivo:** `m_menu.c`
- **Exe:** `dist/srb2win_es_p2.exe`
- **Contenido:** submenús de 1 Jugador, Multijugador, Opciones (principal, controles J1/J2), menús de pausa SP y MP.
- **Verificado:** sí

### Lote 3 — Controles y opciones avanzadas

- **Archivo:** `m_menu.c`
- **Exe:** `dist/srb2win_es_p3.exe`
- **Contenido:** lista de controles, gamepad, Video, Sonido, Datos (capturas de pantalla, borrar datos).
- **Verificado:** sí (confirmado visualmente)

### Lote 4 — Extras y desbloqueables

- **Archivos:** `deh_soc.c`, `m_menu.c`, `m_cond.c`
- **Exe:** `dist/srb2win_es_p4.exe`
- **Contenido:** tabla `M_UnlockTr()` con 27 nombres + 24 objetivos de unlockables, aplicada al parser SOC. Caja de Pandora. Pistas de emblemas. Aviso "desbloqueado!". Nombres de zona tomados del mod de traducción previo.
- **Verificado:** parcialmente (falta confirmar en pantalla que todo cabe)

### Lote 5 — Opciones adicionales

- **Archivo:** `m_menu.c`
- **Exe:** `dist/srb2win_es_p5.exe`
- **Contenido:** Mouse, Cámara, Color, OpenGL, Servidor, Monitores sorpresa (resumidos).
- **Verificado:** no (visualmente)

### Lote 6 — Mensajes y confirmaciones

- **Archivo:** `m_menu.c`
- **Exe:** `dist/srb2win_es_p6.exe`
- **Contenido:** Record Attack/NiGHTS/Marathon, Multijugador, ~35 mensajes de confirmación (`M_StartMessage`), etiquetas dibujadas (`"Juego en pausa"`, `"Pulsa ESC para salir"`).
- **Verificado:** no (visualmente)

### Lote 7 — HUD e intermisiones

- **Archivos:** `st_stuff.c`, `y_inter.c`
- **Exe:** `dist/srb2win_es_p7.exe`
- **Contenido:**
  - HUD: coordenadas de `hudinfo` ajustadas para textos más largos
  - Titlecard: `Zone` → `Acto`/`Zona` según tenga actnum
  - Intermisión coop: nombre real del personaje + `"completo acto"` / `"zona completa"`
  - Special Stage: `"Etapa Especial"`, `"obtuvo las 7!"`, `"ya puede ser"`, `"su forma Super"`, `"una Esmeralda"`
- **Verificado:** no (falta ver la alineación del HUD en pantalla)

### Lote 8 — Prioridad alta (Load Game, Records, HUD NiGHTS, Continue, Rebind, Quit Messages)

- **Archivos:** `m_menu.c`, `st_stuff.c`, `f_finale.c`
- **Exe:** `dist/srb2win_es_p8.exe`
- **Contenido:**
  - Load Game: `NUEVA PARTIDA`, `NO GUARDAR`, `ADDON NO CARGADO`, `ERROR AL CARGAR`, `COMPLETADO!`
  - Etiquetas: `PUNTAJE:`, `TIEMPO:`, `ANILLOS:`, `(ninguno)`, `RECORD PTS:`, `MEJOR TIEMPO:` (en pistas de emblemas, Record Attack, stats de NiGHTS y estadísticas)
  - Pantalla Continue: `"Continuar?"`
  - Controles y tutorial: avisos de cambio de controles y jugador 2
  - HUD in-game y NiGHTS: `"LLEGA A LA META!"`, `"MULTIPLICADOR DE PUNTAJE!"`, `"ESFERAS:"`, `"FICHAS:"`, `"TERMINADO!"`, `"Vuelta:"`, `"PUNTO DE VISTA: Cambiar vista"`, `"Modo espectador:"`, `"TIEMPO EXTRA!"`
  - Quit messages: las frases humorísticas adaptadas con `(Pulsa 'Y' para salir)`
- **Verificado:** compilado e integrado

### Lote 9 — Prioridad media (Intermisión MP y mensajes de consola)

- **Archivos:** `y_inter.c`, `g_game.c`
- **Exe:** `dist/srb2win_es_p9.exe`
- **Contenido:**
  - Intermisión multijugador: cabeceras de columnas (`NOMBRE`, `PUNTAJE`, `TIEMPO`, `AROS`, `TOTAL`, `CAJA`, `PTS`)
  - Avisos en pantalla: `"empieza en %d segundos"` y `"Los equipos se mezclaran la proxima ronda!"`
  - Mensajes de consola / récords: `"NUEVO RECORD DE TIEMPO!"`, `"NUEVO RECORD DE PUNTAJE!"`, `"NUEVO RECORD DE ANILLOS!"`, `"Replay guardado como"`
  - Emblemas ganados: por Contrarreloj, NiGHTS y completar nivel
  - Avisos de reinicio: para jugar Contrarreloj y guardar progreso
- **Verificado:** compilado e integrado

### Lote 10 — Prioridad baja (Intro narrativa, Créditos y Maratón)

- **Archivo:** `f_finale.c`
- **Exe:** `dist/srb2win_es_p10.exe`
- **Contenido:**
  - Introducción narrativa: las 16 páginas de la historia portadas al motor, respetando los códigos de tiempo y la estructura del mod original
  - Créditos: cabeceras de sección (`Creditos`, `Diseno del juego`, `Programacion`, `Arte`, `Musica y diseno sonoro`, `Diseno de niveles`, `Diseno de jefes`, `Control de calidad`, `Agradecimientos especiales`, `Producido por`, `Publicado por`, `Gracias por jugar!`)
  - Pantalla de Maratón: `Tiempo in-game`, `Tiempo RTA`, `con cinematicas`
- **Verificado:** compilado e integrado

### Lote 11 — Prioridad alta (Nombres de niveles y subtítulos SOC en C)

- **Archivo:** `deh_soc.c`
- **Exe:** `dist/srb2win_es_p11.exe`
- **Contenido:**
  - Nombres de los 66 niveles vanilla traducidos de forma nativa en el parser SOC (`readlevelheader`) usando la tabla `M_LevelTr()`
  - Subtítulos de etapas traducidos mediante `M_LevelSubTr()` (`Etapa Especial 1` a `8`)
  - Totalmente compatible con netplay y servidores vanilla al no requerir archivos SOC externos
- **Verificado:** compilado e integrado

### Lote 12 — Parche de fuente para tildes y ñ

- **Archivos:** `hu_stuff.h`, `v_video.c`, `console.c`, `hu_stuff.c`
- **Exe:** `dist/srb2win_es_p12.exe`
- **Contenido:**
  - Expandido `FONTEND` de `'~'` (126) a `250` para soportar Latin-1
  - Arreglada detección de color codes en 10 ubicaciones: de `& 0x80` (cualquier byte ≥128) a rango explícito `0x80-0x8F` (los 16 color codes reales)
  - Arreglada máscara de 7 bits en `V_DrawFontCharacter`: `c &= 0x7f` → `c &= 0xFF`
  - Arreglada sign-extension de `char` en 4 funciones de renderizado: cast a `(UINT8)` antes de `toupper()` y resta de FONTSTART
  - Arreglados filtros de chat (validación, input, dibujo) para permitir caracteres Latin-1
  - Generados 14 glifos como patches Doom: á é í ó ú Á É Í Ñ Ó Ú ñ ¿ ¡
  - Empaquetados en `srb2_spanish_font.pk3` (compatible con NMU, no bloquea MP). Más adelante este pk3 se unificó en `srb2_spanish_gfx.pk3` (ver lotes 14 y 15)
  - Los strings traducidos ahora pueden usar `\xE1` (á), `\xE9` (é), `\xED` (í), `\xF1` (ñ), `\xF3` (ó), `\xFA` (ú), `\xBF` (¿), `\xA1` (¡)
- **Verificado:** compilado e integrado; pendiente verificar glifos visualmente en juego

### Lote 13 — Strings sueltos de finales y transiciones

- **Archivo:** `f_finale.c`
- **Exe:** `dist/srb2win_es_p13.exe`
- **Contenido:**
  - Prompt de salto de intro: `"Press ENTER to skip..."` $\to$ `"Pulsa ENTER para saltar..."`
  - Evaluación final: `"GRACIAS POR JUGAR!"` (Marathon), `"FELICITACIONES!"` (Good ending), `"INTENTA DE NUEVO..."` (Bad ending)
  - Sala de espera multijugador: `"You will join next level..."` $\to$ `"Entraras en el proximo nivel..."`
- **Verificado:** compilado e integrado

### Lote 14 — Parche tipográfico: glifos LTFNT para Titlecards

- **Archivos:** `v_video.c`, `dist/addons/srb2_spanish_gfx.pk3`
- **Exe:** `dist/srb2win_es.exe` en su momento (hoy reemplazado por el lote 16)
- **Contenido:**
  - Implementado decodificador/codificador de Doom Patches en Python para crear glifos auténticos
  - Generados **12 glifos LTFNT** (fuente de Titlecard de nivel) usando los colores oficiales (índices 3=blanco, 14=bisel, 26=sombra):
    - Mayúsculas (20x24px): Á(193) É(201) Í(205) Ñ(209) Ó(211) Ú(218) — base de glifos mayúsculos nativos + acentos grandes (7px acute / 11px tilde)
    - Minúsculas (15-16x19px): á(225) é(233) í(237) ñ(241) ó(243) ú(250) — base de glifos **minúsculos** nativos + acentos proporcionales reducidos (5px acute / 8px tilde)
  - Añadida función `V_FontFallback` en `v_video.c`: si una fuente no tiene el glifo acentuado, hace fallback a la letra base sin acento
  - Inyectados en `dist/addons/srb2_spanish_gfx.pk3` (ruta `Graphics/Fonts/LTFNT/`)
- **Verificado:** compilado e integrado; la Titlecard muestra "Cañón Árido" con tildes correctas y minúsculas/mayúsculas proporcionales

### Lote 15 — Glifos STCFN y corrección ortográfica de nombres de niveles

- **Archivos:** `deh_soc.c`, `dist/addons/srb2_spanish_gfx.pk3`
- **Exe:** `dist/srb2win_es.exe` en su momento (hoy reemplazado por el lote 16)
- **Contenido:**
  - Generados **16 glifos STCFN** (fuente estándar 8x8 de menús/HUD) con paleta nativa (3=blanco, 26=sombra):
    - Mayúsculas con acento: Á(193) É(201) Í(205) Ñ(209) Ó(211) Ú(218) Ü(220)
    - Minúsculas con acento: á(225) é(233) í(237) ñ(241) ó(243) ú(250) ü(252)
    - Puntuación española: ¡(161) ¿(191)
  - Corregidos **20 nombres de niveles** en `leveltr_table` con acentos hex correctos:
    - Volcán, Núcleo, Tuberías, Jardín Aéreo, Tóxica, Satélite, Navideñas, Sueño, Paraíso, Relojería, Rápidos, Cañón Aislado, Fábrica, Crepúsculo, Montaña Gélida, Escalofrío, Prístinas, Almacén, Aéreo, y "de" minúscula en "Castillo de Eggman"
  - Corregidas **12 entradas** en `unlocktr_table`:
    - Maestría de desafíos, Jardín Aéreo (×2), Sueño, Paraíso (×2), Navideñas, Tuberías, Selección, créditos, ¡Conquista...desafío!, Cañón Árido
  - Separación correcta de literales hex con `""` cuando el siguiente carácter es un dígito hex (ej: `\xF1""as`, `\xE9""ditos`, `\xE1""brica`)
  - Todos los glifos inyectados en `dist/addons/srb2_spanish_gfx.pk3` (ruta `Graphics/Fonts/STCFN/`)
- **Verificado:** compilado e integrado

### Lote 16 — Tutorial completo y descripciones de personajes

- **Archivos:** `src/deh_soc.c`
- **Exe:** `dist/srb2win_es_p16.exe` (último snapshot numerado; `dist/srb2win_es.exe` ya incluye los lotes 17 a 20)
- **Contenido:**
  - **Tutorial completo:** 51 páginas de diálogos y guías de Amy Rose (`PROMPT 201` a `PROMPT 221`) y 8 nombres de cartel HUD (`Moverse`, `Control de cámara`, `Moverse y mirar`, `Saltar`, `Spin Dash y rodar`, `Habilidad especial`, `Consigue el escudo`, `Habilidad de escudo`) interceptados nativamente en el parser SOC (`readtextpromptpage`).
  - **Descripciones de personajes vanilla:** los 7 personajes oficiales (`sonic&tails`, `sonic`, `tails`, `knuckles`, `amy`, `fang`, `metalsonic`) traducidos con acentos ortográficos y formateo limpio en `readPlayer` (`M_PlayerTextTr`).
  - Todos los strings integrados con códigos de control del motor (`\x82`, `\x83`, `\x80`, `\xBA`, `\xB6`), signos de apertura (`\xA1`) y separación segura de literales hex con `""`.
- **Verificado:** compilado e integrado

### Lote 17 — Selector de niveles e intermisión

- **Archivos:** `src/m_menu.c`, `src/y_inter.c`
- **Exe:** `dist/srb2win_es.exe` (sin snapshot numerado)
- **Commits:** `3fd34293c`, `510f72fc6`
- **Contenido:**
  - Selector de niveles: se quitó el sufijo `" ZONE"` que el motor añadía a las cabeceras (`selectheading`), porque los nombres traducidos ya no lo llevan.
  - Intermisión coop: el número de acto ya no va en una posición fija (`244`). Ahora se calcula a partir del ancho del nombre traducido (`data.coop.actnumx`), para que no se superponga con nombres largos.

### Lote 18 — HUD, powerups y requisitos de NiGHTS

- **Archivos:** `src/st_stuff.c`, `src/p_inter.c`, `src/m_menu.c`
- **Exe:** `dist/srb2win_es.exe` (sin snapshot numerado)
- **Commit:** `02ec49ebd`
- **Contenido:**
  - **HUD de NiGHTS:** el aviso `GET %d SPHERE/CHIP` se reescribió con una variable `itemname`: `¡OBTÉN %d ESFERA(S)!` / `FICHA(S)`, y `¡OBTÉN %d ESFERA(S) MÁS!` para `NTV_GETMORESPHERES`. En etapas especiales: `¡OBTÉN %d ESFERA(S)!`.
  - **Récord:** `NEW RECORD` pasa a `NUEVO RECORD`.
  - **Powerups de NiGHTS (`p_inter.c`):** `Recarga de Drill`, `Ayudante Nightopian`, `Tiempo Extra`, `Congela Links`.
  - **Requisitos de Extras (`M_DrawSecrets`):** `Consigue %d puntos en %s`, `Supera %s en %d:%02d.%02d` y `Consigue nota %c en %s`, cada una con su variante `, mare %d`. Solo se tradujeron estas rutas; el resto de requisitos sigue en la sección 10.
  - **Selector de mares:** `Overall` pasa a `General` (cvar oculto `dummymares`).

### Lote 19 — Intro: tildes, signos y comillas

- **Archivos:** `src/f_finale.c`
- **Exe:** `dist/srb2win_es.exe` (sin snapshot numerado)
- **Commits:** `69738d041`, `f24217907`
- **Contenido:**
  - Tildes, eñes y signos `¿` `¡` en la intro narrativa del Lote 10 (53 líneas).
  - Corregidas las comillas dobles y un desbordamiento de texto en el parlamento de la cuenta regresiva; los literales se separan con `""` según la regla del hex de la sección 6.

### Lote 20 — Pistas de emblemas y mensajes de red

- **Archivos:** `src/deh_soc.c`, `src/m_menu.c`, `src/netcode/client_connection.c`, `src/netcode/d_clisrv.c`, `src/netcode/d_netcmd.c`, `src/netcode/mserv.c`
- **Exe:** `dist/srb2win_es.exe` (sin snapshot numerado)
- **Commit:** `7506a6d70`
- **Contenido:**
  - **Pistas de emblemas (95 haikus):** nueva tabla `emblemhinttr_table` en `deh_soc.c` (reutiliza `struct leveltr_s`, par inglés/español).
  - **Pantalla de conexión:** `Conectando al servidor...`, `Pulsa ESC para cancelar`, estados de espera y descarga de archivos (`Descargando`, `Esperando para descargar archivos...`, contador de archivos).
  - **Errores al unirse:** servidor lleno, descarga adicional requerida, demasiados WAD, complementos incompatibles, versiones incompatibles, baneo, servidor que no acepta conexiones, límite de jugadores y tiempo de espera de 5 minutos.
  - **Desconexiones:** cierre por fallo de sincronización, límite de ping o inactividad; baneado o expulsado; `Tiempo de espera agotado`; `El servidor se ha cerrado`; `Iniciando servidor...`.
  - **Archivos añadidos por el servidor (`d_netcmd.c`):** avisos de archivo o carpeta que falta, suma de verificación distinta y demasiados archivos cargados.
  - **Menú (`m_menu.c`):** aviso de complementos cargados al unirse y `Debes especificar una dirección IP.`

---

## 10. Lo que ya esta traducido

> ✅ **Prioridad alta completada (Lote 8):** Load Game, etiquetas de récords/stats, Continue, prompt de rebind de controles, quit messages y HUD NiGHTS/espectador/carreras/overtime.

> ✅ **Prioridad media completada (Lote 9):** cabeceras de intermisión MP, alertas de scramble/inicio y mensajes de consola por nuevos récords y emblemas.

> ✅ **Prioridad baja completada (Lote 10):** intro narrativa (16 páginas en C), cabeceras de créditos y pantalla de Maratón.

> ✅ **Prioridad alta completada (Lote 11):** nombres de los 66 niveles vanilla y subtítulos de etapas especiales integrados de forma nativa en `deh_soc.c`.

> ✅ **Transiciones y evaluación completadas (Lote 13):** prompt de saltar intro, textos de evaluación final y sala de espera en `f_finale.c`.

> ✅ **Parche tipográfico completado (Lote 14):** 12 glifos LTFNT generados como Doom Patches auténticos (mayúsculas y minúsculas con acentos proporcionales), función `V_FontFallback`, Titlecards con tildes y ñ nativos.

> ✅ **Glifos STCFN y ortografía completados (Lote 15):** 16 glifos STCFN (fuente de menú/HUD) para todos los caracteres españoles. Corregidos 20 nombres de nivel y 12 entradas de unlockables con acentos ortográficos correctos mediante hex escapes.

> ✅ **Tutorial y Personajes completados (Lote 16):** 51 páginas de prompts del mapa tutorial, 8 carteles de aviso HUD y descripciones completas de habilidades y consejos para los 7 personajes vanilla en `deh_soc.c`.

> ✅ **Selector de niveles e intermisión (Lote 17):** sin sufijo `ZONE` en las cabeceras y número de acto posicionado según el ancho del nombre traducido.

> ✅ **HUD, powerups y NiGHTS (Lote 18):** avisos de esferas y fichas, `NUEVO RECORD`, los 4 powerups de NiGHTS y parte de los requisitos de Extras.

> ✅ **Intro con tildes y signos (Lote 19):** tildes, eñes, `¿` y `¡` en la intro, más corrección de comillas y desbordamiento.

> ✅ **Pistas de emblemas y mensajes de red (Lote 20):** 95 haikus de pistas, pantalla de conexión, errores al unirse, desconexiones y avisos de archivos añadidos por el servidor.

> ✅ **Extras y Mods traducidos (MRCE):** Adaptación completa de Mystic Realm Community Edition (*MRCE*) con nombres de zonas en español, interfaz gráfica del HUD (`TIEMPO`, `ANILLOS`, `ESCUDO` / `GUARDIA`), intermisiones, menús y textos de jugabilidad. Disponible en el repositorio Hub [SRB2-Spanish](https://github.com/trulydza/SRB2-Spanish/releases/tag/mrce-es).

### Lista de tareas pendientes

> La lista detallada con ~75-80 strings pendientes, traducciones sugeridas y checkboxes se encuentra en [traduccion-pendiente-unificada.md](traduccion-pendiente-unificada.md).

---

### Regla estricta: Cvars que NUNCA se deben tocar (`src/command.c`)

> [!CAUTION]
> **No modificar `command.c` (líneas 78-82):** Los valores literales de cvars (`"On"`, `"Off"`, `"Yes"`, `"No"`, `"True"`, `"False"`) **deben permanecer obligatoriamente en inglés**. Traducir estos valores rompería el analizador de comandos de la consola, la carga y guardado de `config.cfg` y la sincronización de variables de red entre clientes.

---

## 11. Compatibilidad online

### ¿Cómo funciona?

SRB2 clasifica los archivos del pk3 en dos categorías:

- **Cosméticos** (gráficos, sonidos de menú, fuentes): el servidor los ignora, cada cliente puede tener los suyos
- **Importantes** (Lua, SOC, mapas): el servidor exige que sean idénticos

La función `W_VerifyNMUSlumps` en `src/w_wad.c` tiene la lista blanca de prefijos cosméticos:
`M_`, `TTL`, `STT`, `LT`, `CONT`, `STCFN`, `K_ST`, `INTERSCR`, `TITLSCR`, etc.

### ¿Qué significa para nosotros?

- Los textos hardcodeados en C van **dentro del exe**: el servidor no compara exes, solo WADs/pk3
- Los gráficos del pk3 cosmético tienen prefijos en la lista blanca: **no bloquean**
- El Lua y el SOC del mod original **sí bloquean**: por eso se portan a C

### Confirmado

- Se entró a servidores vanilla con el exe traducido sin mod ✓
- Confirmado con `Jugar_online.bat` (exe + pk3 cosmético): se entró a servidores vanilla ✓
- También probado en servidores con mods: con un mod de HUD, el HUD nativo traducido se reemplaza limpio y no quedan textos superpuestos (con el enfoque anterior, por mod, sí se superponían) ✓

---

## 12. Cómo jugar

> [!IMPORTANT]
> **Lanzador en uso:** el proyecto se trabaja y soporta **únicamente con `Jugar_online.bat`** (Windows) y **`Jugar_online.sh`** (Linux). Los modos `Jugar_traducido` y `Jugar_vanilla` se conservan solo como legado y ya no se incluyen en las distribuciones.

### Cómo jugar en Windows

#### Instalación desde Release (Windows)

Descarga `SRB2_Spanish_Recomp_v2.2.15.zip` y `srb2_spanish_gfx.pk3` desde [Releases](https://github.com/trulydza/SRB2-Spanish-Recomp/releases) y sigue las instrucciones del README. Solo `Jugar_online.bat` viene en el zip; los `.bat` legado ya no se incluyen.

#### Modo online en Windows (compatible con servidores) — EN USO

Usar `Jugar_online.bat`:

```batch
@echo off
start "" "srb2win_es.exe" -file addons\srb2_spanish_gfx.pk3
```

Carga el exe traducido + gráficos cosméticos. Se puede entrar a cualquier servidor vanilla.

#### Modo traducción completa en Windows (offline) — legado

Usar `Jugar_traducido.bat`:

```batch
@echo off
start "" "srb2win_es.exe" -file "RUTA_AL_MOD\srb2_spanish.pk3"
```

Carga todo: exe traducido + mod completo con Lua/SOC. Solo para jugar en solitario.

#### Modo exe solo en Windows (sin mods) — legado

Usar `Jugar_vanilla.bat`: corre el exe traducido sin ningún mod adicional. Los menús y el HUD están en español; los gráficos de cabecera siguen en inglés.

---

### Cómo jugar en Linux (CachyOS / Arch Linux / distribuciones modernas)

#### Binarios y Scripts en Linux

En Linux se utiliza el binario nativo de 64 bits `srb2linux_es` (ubicado en `dist/` o en la raíz del proyecto) junto con los scripts `.sh` ejecutables equivalentes a los `.bat`.

Asegurarse de que tienen permisos de ejecución:
```bash
chmod +x dist/srb2linux_es dist/Jugar_*.sh
```

#### Modo online en Linux (compatible con servidores) — EN USO

Ejecutar [`Jugar_online.sh`](file:///home/daza/Proyecto%20SRB2/SRB2-recomp/dist/Jugar_online.sh):
```bash
./dist/Jugar_online.sh
# O desde la raíz:
./Jugar_online.sh
```
Carga automáticamente el binario nativo traducido junto con el addon gráfico cosmético `srb2_spanish_gfx.pk3`. Permite entrar a servidores multijugador oficiales y vanilla sin problemas de consistencia.

#### Modo traducción completa en Linux (offline) — legado

Ejecutar [`Jugar_traducido.sh`](file:///home/daza/Proyecto%20SRB2/SRB2-recomp/dist/Jugar_traducido.sh):
```bash
./dist/Jugar_traducido.sh
```
Carga el mod completo `addons/srb2_spanish.pk3` con Lua y SOC para una experiencia 100% en español en modo un jugador.

#### Modo vanilla en Linux (sin mods adicionales) — legado

Ejecutar [`Jugar_vanilla.sh`](file:///home/daza/Proyecto%20SRB2/SRB2-recomp/dist/Jugar_vanilla.sh):
```bash
./dist/Jugar_vanilla.sh
```

#### Detección Inteligente de Recursos (Fallback Flatpak / Sistema)

Los lanzadores `.sh` de Linux cuentan con búsqueda inteligente de assets:
1. Si los archivos `.pk3` oficiales (`srb2.pk3`, `zones.pk3`, etc.) están en `dist/` o la carpeta local, los carga directamente.
2. Si no están en la carpeta local, detecta y monta automáticamente la instalación oficial de **Flatpak** (`/var/lib/flatpak/app/org.srb2.SRB2/current/active/files/share/games/srb2` o en `~/.local/share/flatpak/...`) mediante la variable `SRB2WADDIR`.
3. Si están instalados a nivel de sistema (`/usr/share/games/SRB2`), los toma de ahí.

#### Atajo con Shell Fish (Abrir y Cerrar Terminal Automáticamente)

Para abrir el juego desde la terminal con un atajo rápido y que la ventana de la terminal se cierre de inmediato sin cerrar el juego:
Se definen las funciones `srb2` o `srb2-online` en `~/.config/fish/functions/srb2.fish`:
```fish
function srb2 --description "Iniciar SRB2 Spanish Recomp (Online) y cerrar terminal"
    setsid -f "/ruta/hacia/SRB2-recomp/dist/Jugar_online.sh" $argv >/dev/null 2>&1
    exit
end
```
Con esto, basta con escribir `srb2` (o `srb2 -warp 1`) en la terminal: el juego se inicia en una nueva sesión en segundo plano y la terminal se cierra automáticamente.

---

### Datos del juego

Tanto en Windows como en Linux, el juego guarda su configuración y partidas guardadas de forma estándar:
- **Windows:** En la carpeta local o en `%APPDATA%\Sonic Robo Blast 2`.
- **Linux:** En `~/.srb2/` (o si corre vía Flatpak sandbox en `~/.var/app/org.srb2.SRB2/.srb2/`). No altera tus partidas de otras versiones.

---

## 13. Problemas conocidos y trampas

### Entorno de build

| Problema                                               | Solución                                                              |
| ------------------------------------------------------ | --------------------------------------------------------------------- |
| `bash -s` no encuentra `sed`, `grep`, etc.             | Usar `bash -lc` (login shell)                                         |
| `git` no está en el PATH de MSYS2                      | Usar el `git` de Windows directamente                                 |
| `python` no existe en el bash de MSYS                  | Invocar `python` desde Windows                                        |
| `find /` cuelga la herramienta                         | No escanear fuera de las carpetas del proyecto                        |
| Los números de línea de notas anteriores pueden variar | Verificar siempre con grep, no confiar ciegamente en números de línea |
| En Linux con GCC 15/16: error de `false`/`true` en C23 | Añadir `-std=gnu11` al `Makefile` en la variable `opts` (ya incluido) |
| Permiso denegado al ejecutar scripts `.sh` en Linux    | Dar permisos con `chmod +x dist/srb2linux_es dist/Jugar_*.sh`         |
| Assets no encontrados en Linux sin copiar los `.pk3`   | Instalar SRB2 vía Flatpak; los `.sh` exportan `SRB2WADDIR` automáticamente |

### Código

| Problema                                            | Solución                                                                                                              |
| --------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| `#ifdef ALAM_LIGHTING` contiene menús OpenGL        | No traducir: está desactivado en vanilla                                                                              |
| `grep` del exe no encuentra strings                 | GCC compila `strcpy` con inmediatos de 8 bytes; el texto no queda contiguo. Buscar trozos cortos o confiar en el diff |
| Un hex después de `\xNN` se come la siguiente letra | Cerrar y reabrir el string: `"\x82""Texto"`                                                                           |
| `M_GetText()` parece una función de i18n            | Es un **stub** que devuelve el mismo string. No traduce nada                                                          |

### Traducción

| Problema                                        | Solución                                                                                   |
| ----------------------------------------------- | ------------------------------------------------------------------------------------------ |
| Glifos de LTFNT y STCFN para acentos            | ✅ Resuelto: glifos generados con Doom Patch encoder e inyectados en `srb2_spanish_gfx.pk3` |
| Hex escape seguido de carácter hex              | Separar el literal: `"\xF1""as"` para ñas, `"\xE9""ditos"` para éditos                     |
| Confirmaciones con Y/N                          | El motor lee la tecla física. Escribir `'Y'`/`'N'`, nunca `'S'`                            |
| Textos de menú con `strcmp`                     | Se verificó: `m_menu.c` NO compara strings de menú por contenido                           |
| `config.cfg` guarda valores de cvars como texto | No traducir `"On"`/`"Off"` en `command.c` sin separar display de parse                     |
| Fuente sin glifo para un carácter Latin-1       | `V_FontFallback` sustituye automáticamente por la letra base sin acento                    |

---

## 14. FAQ

### ¿Se pueden agregar tildes o la letra ñ?

**Sí, ya están implementadas.** Se crearon glifos nativos como Doom Patches para ambas fuentes del juego:

- **LTFNT** (fuente de Titlecard): 12 glifos (Á É Í Ñ Ó Ú á é í ñ ó ú) usando las bases originales mayúsculas/minúsculas de `srb2.pk3`, con acentos proporcionales al tamaño de cada letra
- **STCFN** (fuente estándar de menús/HUD): 16 glifos (Á É Í Ñ Ó Ú Ü á é í ñ ó ú ü ¡ ¿) manteniendo el estilo 8x8 nativo

Los glifos se inyectan en `srb2_spanish_gfx.pk3` como lumps cosméticos (no bloquean MP). En el código C, se usan hex escapes: `\xF1` para ñ, `\xE1` para á, etc. Si un glifo no existe, `V_FontFallback` muestra la letra base sin acento.

### Si modifico un archivo .c, ¿tengo que recompilar todo?

No. El build es incremental: solo recompila los `.c` que cambiaron. En Linux tarda unos pocos segundos con `make LINUX64=1 NOUPX=1 -j$(nproc)`, y en Windows 1-3 minutos.

### ¿Se puede jugar online con la traducción?

Sí, usando `Jugar_online.bat` (en Windows) o `./Jugar_online.sh` / `srb2` (en Linux). El ejecutable traducido es compatible con servidores vanilla. El pk3 cosmético no bloquea la conexión.

### ¿Se puede compilar y jugar de forma nativa en Linux / CachyOS / Steam Deck?

**Sí, completamente nativo.** El recompilado compila limpiamente en Linux x86_64 usando GCC y Make. Además, cuenta con scripts lanzadores (`Jugar_online.sh`, etc.) que detectan si tienes la versión oficial de Flatpak instalada (`org.srb2.SRB2`) para aprovechar sus archivos `.pk3` sin necesidad de duplicarlos en disco.

### ¿Hay riesgo de romper algo al traducir?

Sí. Los riesgos principales son:

1. **Exceder un buffer** (ej.: meter más de 21 chars en `passed1`)
2. **Romper un format specifier** (borrar un `%d` o `%s`)
3. **Romper un escape hex** (poner una letra hex después de `\xNN`)
4. **Traducir algo que la consola parsea** (ej.: `"On"`/`"Off"` en cvars)

Por eso se usa el script Python con `assert` antes de escribir.

### ¿Dónde están los respaldos o versiones anteriores?

En `dist\` del espacio de trabajo local:

- `srb2win_es_p1.exe` hasta `srb2win_es_p13.exe`, más `srb2win_es_p16.exe`
- `srb2win_es.exe` = último build Windows (lote 20)
- `srb2linux_es` = último build nativo Linux

Si algo sale mal, se puede volver a cualquier snapshot anterior. Los binarios publicados están en [Releases](https://github.com/trulydza/SRB2-Spanish-Recomp/releases).

### ¿Qué pasará cuando SRB2 se actualice?

Este proyecto está basado en el tag `SRB2_release_2.2.15`. Si sale una versión nueva, habría que:

1. Crear un nuevo branch desde el tag nuevo
2. Re-aplicar los parches (idealmente como commits cherry-pickable)
3. Resolver conflictos

Por ahora no es un problema porque 2.2.15 es la versión estable actual.
