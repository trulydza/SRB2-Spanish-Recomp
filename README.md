# SRB2 en español (recompilación v2.2.15)

Traducción al español de Sonic Robo Blast 2 hecha directamente en el código C del juego, sobre la versión 2.2.15.

Los textos están dentro del ejecutable, no en un addon de Lua o SOC. Por eso se puede entrar a servidores vanilla sin que el juego pida cargar archivos extra. Además hay un pk3 opcional con gráficos y fuentes traducidos (`srb2_spanish_gfx.pk3`), que es solo cosmético.

## Qué está traducido

- Menús: principal, video, audio, controles, partidas guardadas, estadísticas y Caja de Pandora.
- HUD y partida: puntos, tiempo, anillos, mensajes en pantalla, modo espectador y tiempo extra.
- Nombres de las 66 zonas vanilla y de las etapas especiales, con sus acentos (*Cañón Árido*, *Montaña Gélida*, *Volcán*...).
- Tutorial completo: las 51 páginas de diálogo de Amy Rose y todos los carteles del mapa.
- Los 7 personajes vanilla: descripciones, habilidades y consejos.
- Intro completa (las 16 páginas), créditos y las frases al salir del juego.
- Tildes, eñes y signos en las fuentes LTFNT y STCFN. Los glifos vienen en el pk3 de gráficos; sin él, el juego usa un fallback automático.

Los valores de las variables de consola (On/Off, Yes/No) se dejan en inglés a propósito.

## Instalación

Descarga desde [Releases](https://github.com/trulydza/SRB2-Spanish-Recomp/releases):

- `SRB2_Spanish_Recomp_v2.2.15.zip`
- `srb2_spanish_gfx.pk3`

Hay dos formas de instalarlo:

**Sobre tu SRB2 actual.** Descomprime el zip en la carpeta donde tienes SRB2 2.2 y acepta reemplazar los archivos. Se actualizan el ejecutable y las librerías (ahora de 64 bits). Tus partidas y tu configuración no se tocan.

**En una carpeta aparte.** Extrae el zip en una carpeta nueva (por ejemplo `SRB2 Español`) y copia ahí desde tu juego original `srb2.pk3`, `zones.pk3`, `characters.pk3` y `music.pk3`.

Después, pon `srb2_spanish_gfx.pk3` dentro de la carpeta `addons/` (créala si no existe).

Para jugar:

- `Jugar_online.bat` carga los textos y los gráficos traducidos. Sirve tanto en solitario como en servidores.
- `Jugar_vanilla.bat` arranca sin el pk3 de gráficos.

## Compilar y documentación técnica

La arquitectura, las reglas de traducción, el historial de lotes, los pasos para compilar y la lista de tareas pendientes están en [`GUIA.md`](./GUIA.md).

## Créditos

- Traducción y adaptación: Daza ([@trulydza](https://github.com/trulydza))
- Juego base: Sonic Team Junior ([STJr/SRB2](https://github.com/STJr/SRB2))

Licencia GPL-2.0, igual que el juego original.
