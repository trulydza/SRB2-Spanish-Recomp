# Sonic Robo Blast 2 en Español (Recompilación v2.2.15)

Traducción al español de **Sonic Robo Blast 2** hecha directamente en el código fuente en C. 

A diferencia de los mods tradicionales basados en scripts de Lua o archivos SOC (que suelen desincronizar o bloquear la entrada a partidas multijugador), esta versión modifica los textos en el propio motor. El resultado es que **puedes jugar online en cualquier servidor oficial o vanilla sin problemas de compatibilidad**.

---

### ✨ ¿Qué incluye?

- **Menús y configuración:** Menú principal, opciones de video, audio, controles, partida guardada y estadísticas.
- **En partida:** Marcadores (puntos, tiempo, anillos), mensajes del HUD, modo espectador y tiempos extra.
- **Niveles:** Los nombres de las 66 zonas vanilla y etapas especiales adaptados directamente.
- **Historia y extras:** Cinemática inicial completa (las 16 páginas), créditos y las frases clásicas al cerrar el juego.
- **Compatible con netplay:** Puedes entrar a servidores públicos sin que te desconecte por discrepancias de archivos.

---

### 📦 ¿Cómo instalarlo para jugar?

1. Ve a la sección de [**Releases**](https://github.com/trulydza/SRB2-Spanish-Recomp/releases) y descarga:
   - `SRB2_Spanish_Recomp_v2.2.15.zip`
   - `srb2_spanish_gfx.pk3` (parche gráfico complementario)

2. **Instalación (elige la que prefieras):**
   - **En tu carpeta actual de SRB2:**  
     Descomprime el contenido del `.zip` dentro de tu carpeta donde ya tienes instalado SRB2 v2.2. Si Windows te pide reemplazar archivos, dale que sí (actualizará los ejecutables y librerías necesarias a 64-bit; tus partidas y configuraciones seguirán a salvo).
   - **En una carpeta nueva (recomendado si no quieres tocar tu SRB2 original):**  
     Crea una carpeta en cualquier lugar (por ejemplo `SRB2 Español`), extrae el `.zip` allí, y copia desde tu juego original los 4 archivos base: `srb2.pk3`, `zones.pk3`, `characters.pk3` y `music.pk3`.

3. **Colocar los gráficos:**  
   Mueve el archivo `srb2_spanish_gfx.pk3` dentro de la carpeta `addons/` (si no la tienes, simplemente crea una carpeta llamada `addons` en el mismo directorio del juego).

4. **Listo para jugar:**
   - Abre **`Jugar_online.bat`** para jugar con los textos y gráficos traducidos (funciona tanto solo como en servidores online).
   - Abre **`Jugar_vanilla.bat`** si prefieres jugar sin complementos visuales.

---

### 🛠️ Documentación técnica

Si te interesa ver cómo se estructuraron las traducciones en el código o quieres compilarlo por tu cuenta, puedes revisar [`GUIA.md`](./GUIA.md).

---

### Créditos y agradecimientos

- **Adaptación y traducción:** Daza ([@trulydza](https://github.com/trulydza))
- **Juego base:** Sonic Team Junior ([STJr / SRB2](https://github.com/STJr/SRB2))
