# Sonic Robo Blast 2 — Spanish Recompilation (v2.2.15)

<p align="center">
  <b>Recompilación nativa de SRB2 v2.2.15 con traducción completa al español en el motor en C.</b><br>
  <i>100% compatible con servidores vanilla y multijugador online.</i>
</p>

---

## 📌 ¿De qué trata este proyecto?

**Sonic Robo Blast 2** contiene prácticamente todos sus textos (menús, HUD, pantallas de intermisión, mensajes de confirmación, créditos, nombres de zonas y desbloqueables) directamente integrados en el código fuente en C.

Tradicionalmente, las traducciones que usan scripts de **Lua** o archivos **SOC** externos no pueden entrar a servidores normales porque el juego bloquea el multijugador si los archivos de juego difieren.

**SRB2 Spanish Recomp** soluciona esto traduciendo los textos directamente en el ejecutable en C.
- ✅ **Totalmente compatible con servidores normales / netplay:** Los servidores sólo verifican archivos `.pk3`/`.wad`, no ejecutables de cliente.
- ✅ **Menús, HUD y mensajes en español nativo.**
- ✅ **Nombres de niveles vanilla traducidos** en el parser nativo sin desincronizaciones.
- ✅ **16 páginas completas de la cinemática de introducción** portadas con sus tiempos exactos.

---

## 🚀 ¿Cómo jugar?

1. Descarga el paquete `SRB2_Spanish_Recomp_v2.2.15.zip` y el complemento de gráficos `srb2_spanish_gfx.pk3` desde la sección de [**Releases**](https://github.com/trulydza/SRB2-Spanish-Recomp/releases).
2. Extrae `SRB2_Spanish_Recomp_v2.2.15.zip` dentro de tu carpeta donde tengas instalado SRB2 v2.2 (o en una carpeta con acceso a `srb2.pk3`).
3. Coloca `srb2_spanish_gfx.pk3` dentro de la carpeta `addons/` (si no existe la carpeta `addons`, créala).
4. Ejecuta `Jugar_online.bat` para disfrutar con los gráficos cosméticos traducidos en servidores online, o `Jugar_vanilla.bat` para jugar sin mods cosméticos.

---

## 🛠️ Contenido traducido (Lotes 1 al 11)

- **Menú Principal y Submenús:** 1 Jugador, Multijugador, Opciones (Video, Sonido, Controles, Mouse, Cámara, Datos, Pantalla dividida).
- **Cargar Partida y Estadísticas:** Guardado/Nueva Partida, marcadores de tiempo, puntaje y anillos.
- **HUD in-game:** HUD de NiGHTS, mensajes de tiempo extra, multiplicadores, puntajes, modo espectador.
- **Intermisiones:** Pantallas post-nivel en cooperativo, etapas especiales (Special Stages), conteos y tablas de resultados multijugador.
- **Nombres de Niveles y Zonas:** Los 66 niveles vanilla y etapas especiales adaptados en el parser de cabeceras C (`deh_soc.c`).
- **Narrativa y Créditos:** Cinemática inicial completa (16 páginas) en `f_finale.c`, créditos y pantalla de Maratón.
- **Mensajes Humorísticos:** Las 24 frases clásicas al salir del juego adaptadas al español.

---

## 📖 Documentación técnica

Para consultar la arquitectura interna, glosario, mapas de archivos y proceso de compilación paso a paso, abre el archivo local interactivo [`guia.html`](./guia.html) en cualquier navegador web.

---

## 👤 Créditos

- **Proyecto y Traducción:** Daza ([@trulydza](https://github.com/trulydza))
- **Base original:** Sonic Team Junior ([STJr / SRB2](https://github.com/STJr/SRB2)) — Versión v2.2.15
