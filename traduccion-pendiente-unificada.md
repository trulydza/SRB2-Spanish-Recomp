# 📋 Todo lo que Falta por Traducir — Reporte Unificado

Combina la auditoría de multijugador/netplay + pendientes de GUIA.md, sin duplicados.

---

## 1. `hu_stuff.c` — Join / Leave / Kick / Chat 🔴

Estos los ve **TODO el mundo** en cada partida online:

- [ ] `"%s joined the game"` → `"%s se unió a la partida"`
- [ ] `"%s left the game"` → `"%s salió de la partida"`
- [ ] `"%s was kicked from the game"` → `"%s fue expulsado de la partida"`
- [ ] `"%s timed out"` → `"%s perdió la conexión"`
- [ ] `"%s was kicked from the game (Consistency failure)"` → `"%s fue expulsado (fallo de sincronización)"`
- [ ] `"Chat mode (Press ENTER to talk)"` → `"Modo chat (Pulsa ENTER para hablar)"`
- [ ] `"Team Chat mode (Press ENTER to talk)"` → `"Chat de equipo (Pulsa ENTER para hablar)"`
- [ ] `"(team chat): "` → `"(equipo): "`
- [ ] `"set your name with /name <name>"` → `"cambia tu nombre con /name <nombre>"`
- [ ] `"You can't say that while in a level."` → `"No puedes decir eso dentro de un nivel."`
- [ ] `"You can't say that in a multiplayer match."` → `"No puedes decir eso en una partida multijugador."`

---

## 2. `m_menu.c` — Menús y UI 🔴🟠

### Pausa en Record Attack / Contrarreloj
- [x] `"Emblem Hints..."` → `"Pistas de emblemas..."`
- [x] `"Continue"` → `"Continuar"`
- [x] `"Retry"` → `"Reintentar"`
- [x] `"Abort"` → `"Abandonar"`

### Submenús de Equipos
- [x] `"Scramble Method"` → `"Método de mezcla"`
- [x] `"Select Team"` → `"Elegir equipo"`
- [x] `"Confirm"` → `"Confirmar"`

### Descripciones de modos de juego (8 textos completos)
- [x] Co-op, Competition, Race, Match, Team Match, Tag, Hide & Seek, CTF

### Estilos de cámara y control
- [x] Descripciones de: *Automático*, *Manual*, *Simple*, *Legacy*, *Strafe*

### Requisitos de Extras/Desbloqueables (parcialmente en Lote 18)
- [ ] `"Beat %s"` / `"Beat %s with all emeralds"` / `"Beat %s in Ultimate mode"`
- [ ] `"Get all rings in %s"`
- [ ] `"Beat the game %d times%s"` / `"Collect %s%d emblems"`
- [ ] `"Get %d points over all maps"` / `"Get a total time of less than %d:%02d.%02d"`

### Buscador de servidores y salas online
- [ ] `"Select a room"` → `"Selecciona una sala"`
- [ ] `"Don't connect to the Master Server."` → `"No conectar al servidor maestro."`
- [ ] `"Checking for updates..."` → `"Buscando actualizaciones..."`
- [ ] `"Fetching room info..."` → `"Obteniendo info de sala..."`
- [ ] `"Please wait."` → `"Por favor espera."`
- [ ] `"Server settings"` / `"<Select to change>"` / `"<Unlisted Mode>"`
- [ ] `"No servers found"` → `"No se encontraron servidores"`
- [ ] `"Searching for servers..."` → `"Buscando servidores..."`
- [ ] `"Ping: %u"` / `"Players: %02d/%02d"` / `"Gametype: %s"`

### Pistas de emblemas en menú
- [ ] `"%d of %d"` (paginación)
- [ ] `"No hint available for this emblem."`

### Prompts de rebind de controles
- [x] `"Hit the new key for\n%s\nESC for Cancel"` → `"Pulsa la nueva tecla para\n%s\nESC para Cancelar"`
- [x] Avisos de tecla de pausa en Record Attack y gamepad ocupado

---

## 3. `d_clisrv.c` — Conexión y errores del servidor 🔴

### Mensajes de conexión (visibles al unirse)
- [ ] `"%s has joined the game (player %d)"` → `"%s se unió a la partida (jugador %d)"`
- [ ] `"%s renamed to %s"` → `"%s cambió su nombre a %s"`
- [x] `"Connection Timeout"` / `"Server Timeout"` → `"Tiempo de espera agotado"` *(Lote 20)*
- [ ] `"Downloading game state..."` → `"Descargando estado del juego..."`
- [ ] `"Waiting to download game state..."` → `"Esperando para descargar..."`
- [ ] `"Waiting for the server..."` → `"Esperando al servidor..."`
- [ ] `"Cannot connect to the server."` → `"No se pudo conectar al servidor."`
- [ ] `"Connection refused... Probably, the server is full."` → `"Conexión rechazada... Probablemente el servidor está lleno."`
- [ ] `"The server is not responding"` → `"El servidor no responde"`

### Errores de servidor (visibles al intentar unirse)
- [ ] `"Server not found!"` → `"¡Servidor no encontrado!"`
- [x] `"The server is too old to connect"` → `"Servidor desactualizado..."` *(Lote 20)*
- [x] `"The server is using a different game modification"` → `"Versiones incompatibles..."` *(Lote 20)*
- [x] `"Server full!"` → `"¡Este servidor está lleno!"` *(Lote 20)*
- [x] `"You have been banned from the server."` → `"Has sido baneado del servidor."` *(Lote 20)*
- [ ] `"You can't join because the game is over."` → `"No puedes unirte, la partida terminó."`
- [x] `"Server is not accepting joins for the moment."` → `"El servidor no está aceptando nuevas conexiones por ahora."` *(Lote 20)*
- [x] `"Server Shutdown"` / `"Server has shutdown..."` → `"El servidor se ha cerrado..."` *(Lote 20)*

---

## 4. `mserv.c` — Master Server / Buscador 🔴

- [ ] `"Sending registration to master server..."` → `"Enviando registro al servidor maestro..."`
- [ ] `"Registration successful."` → `"Registro exitoso."`
- [ ] `"Master server registration failed."` → `"Falló el registro en el servidor maestro."`
- [ ] `"Requesting server list from master server..."` → `"Solicitando lista de servidores..."`
- [ ] `"Server list received."` → `"Lista de servidores recibida."`
- [ ] `"Failed to get server list from master server."` → `"Error al obtener la lista de servidores."`
- [ ] `"Unregistering from master server..."` → `"Eliminando registro del servidor maestro..."`
- [ ] `"Could not resolve master server address: %s"` → `"No se pudo resolver la dirección: %s"`
- [ ] `"Could not connect to master server: %s"` → `"No se pudo conectar al servidor maestro: %s"`
- [ ] `"No servers currently running."` → `"No hay servidores activos."`
- [ ] `"%d server(s) found:"` → `"%d servidor(es) encontrado(s):"`
- [ ] `"Server: %s"` → `"Servidor: %s"`
- [ ] `"Players: %d/%d"` → `"Jugadores: %d/%d"`
- [ ] `"Game Type: %s"` → `"Tipo de juego: %s"`
- [ ] `"Map: %s"` → `"Mapa: %s"`
- [ ] `"Internal error contacting master server."` → `"Error interno al contactar el servidor maestro."`
- [ ] `"Master server timed out."` → `"Tiempo de espera agotado."`
- [ ] `"Could not reach master server."` → `"No se pudo contactar el servidor maestro."`

---

## 5. `st_stuff.c` — HUD especial y alertas 🟠

### Respawn y Vidas
- [ ] `"Respawn in %d..."`
- [ ] `"You'll steal a life on respawn..."`
- [ ] `"Wait to respawn..."`
- [ ] `"%d player%s remaining"`

### Tag / Escondite
- [ ] `"Waiting for players to hide..."`
- [ ] `"Hide before time runs out!"`
- [ ] `"Flee before you are hunted!"`
- [ ] `"You cannot move while hiding."`

### Espectador
- [ ] `"Viewpoint:"`

---

## 6. `f_finale.c` y `g_game.c` — Finales y guardado 🟡

- [ ] `"Unlocked:"` (pantalla de recompensas finales)
- [ ] `"Save game from different version...\n\nPress ESC\n"`
- [ ] `"Savegame file corrupted\n\nPress ESC\n"`
- [ ] `"Game saved.\n"`
- [ ] `"*Bot %s has been removed"`
- [ ] `"The round has ended.\n"`

---

## 7. `p_inter.c` — Combate PvP y peligros 🟡 *(Opcional)*

- [ ] Mensajes de muerte ambiental (pinchos, lava, agua, abismo, aplastamiento, ahogamiento)
- [ ] Mensajes de armas Ringslinger (bombas, raíl, anillos)
- [ ] Mensajes de Tag / Hide & Seek / CTF (`"%s is now IT!"`, `"%s picked up the flag!"`, etc.)

---

## 📊 Resumen Total

| # | Archivo | Strings Pendientes | Prioridad |
|---|---------|---------------------|-----------|
| 1 | `hu_stuff.c` | ~11 | 🔴 Alta |
| 2 | `m_menu.c` | ~28 | 🔴🟠 Alta/Media |
| 3 | `d_clisrv.c` | ~11 | 🔴 Alta |
| 4 | `mserv.c` | ~18 | 🔴 Alta |
| 5 | `st_stuff.c` | ~9 | 🟠 Media |
| 6 | `f_finale.c` / `g_game.c` | ~6 | 🟡 Baja |
| 7 | `p_inter.c` | ~20+ | 🟡 Opcional |
| | **Total pendientes** | **~75-80** | |

> [!IMPORTANT]
> Algunos de estos strings pueden ya estar traducidos en el Lote 20 (mensajes de red). Hay que verificar contra el código actual antes de tocarlos.

---

## 🛠️ Flujo de Trabajo para Traducir, Compilar y Probar en Linux (CachyOS)

Para continuar implementando las traducciones pendientes desde este sistema Linux (CachyOS), sigue estos pasos:

### 1. Localizar y comprobar el string en el código C
```bash
grep -rn "texto a traducir" SRB2/src/
```
Verifica que no sea una palabra clave de consola (cvar) o un comando de red estricto. Revisa si las cadenas vecinas calculan ancho con `V_StringWidth` o `V_SmallStringWidth`.

### 2. Modificar con seguridad
Usa un script Python con `assert data.count(b'...') == 1` o edita con cuidado preservando terminadores nulos y especificadores de formato (`%s`, `%d`).
Recuerda las sustituciones de glifos en hex para acentos/eñes:
- `\xE1` = á, `\xE9` = é, `\xED` = í, `\xF3` = ó, `\xFA` = ú
- `\xF1` = ñ, `\xD1` = Ñ, `\xBF` = ¿, `\xA1` = ¡
- *(Si la siguiente letra es un carácter hexadecimal válido como a-f, separar el literal: `"\xE9""ditos"`)*.

### 3. Compilar en Linux (CachyOS)
Desde la raíz del proyecto:
```bash
make -C SRB2/src LINUX64=1 NOUPX=1 -j$(nproc)
```
*(Gracias a que el Makefile ya incluye `-std=gnu11`, compilará limpiamente con GCC 16 en pocos segundos).*

### 4. Sincronizar el binario compilado
```bash
cp SRB2/bin/lsdl2srb2 dist/srb2linux_es
cp SRB2/bin/lsdl2srb2 dist/srb2linux
cp SRB2/bin/lsdl2srb2 srb2linux_es
cp SRB2/bin/lsdl2srb2 run/srb2linux_es
chmod +x dist/srb2linux_es dist/srb2linux srb2linux_es run/srb2linux_es
```

### 5. Probar cambios en el juego
- **Lanzamiento rápido con addon de fuentes/glifos:**
  ```bash
  ./dist/Jugar_online.sh
  # O usando el atajo de fish:
  srb2
  ```
- **Probar un nivel directo (ej. Greenflower 1):**
  ```bash
  ./dist/srb2linux_es -file dist/addons/srb2_spanish_gfx.pk3 -warp 1
  ```
- **Probar mensajes de red y multijugador:**
  - En una terminal, levanta un servidor dedicado local:
    ```bash
    ./dist/srb2linux_es -dedicated -warp 1
    ```
  - En otra ventana, únete al servidor local para verificar los mensajes de conexión/join/kick/chat de `hu_stuff.c` y `d_clisrv.c`:
    ```bash
    ./dist/srb2linux_es -connect 127.0.0.1
    ```
- **Comandos útiles dentro de la consola del juego (pulsando `~`):**
  - `exitlevel`: Termina el nivel inmediatamente para probar pantallas de intermisión (`y_inter.c`).
  - `devmode 1`: Habilita trampas y comandos de prueba.
  - `god`: Modo invencible.

