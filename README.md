# Termux-Telegram
Telegram Desktop on Termux Desktop
Aquí tienes un ejemplo de cómo podría quedar el archivo README en español:

---

# Gestión de Descargas de Telegram en Debian con Termux

## El Problema

Necesitaba que los archivos descargados de Telegram se guardaran con el nombre original y que no me diera tanto problema ubicarlos y manejarlos. Además, quería poder realizar otras tareas mientras se descargaban.

## La Solución

Opté por crear una instancia de Proot con Debian, ya que Ubuntu no trae el paquete `telegram-desktop` (al menos no en la versión 24.04).

## Instrucciones

1. **Instala este entorno gráfico de Yisus (si ya tienes uno, no es necesario):**
   - [Termux Desktop XFCE](https://github.com/Yisus7u7/termux-desktop-xfce)

2. **Crea una instancia de Debian e instala los paquetes necesarios:**
   - `telegram-desktop`
   - `fonts-open-sans`
   - `noto-sans`

3. **Configura la variable DISPLAY y realiza el enlace de la carpeta de descargas:**
   - Establece la variable `DISPLAY=:1` tanto en Termux como en la instancia de Debian:
     ```sh
     export DISPLAY=:1
     ```
   - Inicia la instancia de Debian con las siguientes opciones:
     ```sh
     proot-distro login debian --env DISPLAY=:1 --shared-tmp --bind $HOME/storage/Download:/root/Downloads
     ```
   - **Explicación de los parámetros:**
     - `--env DISPLAY=:1`: Habilita la variable que necesitamos para la pantalla.
     - `--shared-tmp`: Necesaria si quieres el audio de `pulseaudio` dentro del `proot-distro`.
     - `--bind`: Establece la carpeta de descargas en el almacenamiento interno.
¡Perfecto! Para continuar, te muestro cómo podrías ajustar y automatizar los pasos mencionados:

### Automatización del Proceso

#### 1. **Agregar la variable `DISPLAY` al archivo `.bashrc` o `.zshrc`**
   Para asegurarnos de que la variable `DISPLAY` esté siempre configurada cuando inicies una nueva sesión, puedes agregarla al final de tu archivo `.bashrc` o `.zshrc`:


#### 2. **Crear un script para iniciar VNC y Telegram-Desktop**

   Puedes crear un script que realice todo el proceso de forma automática. Aquí te muestro cómo:

   **1.** Primero, asegúrate de eliminar cualquier instancia anterior de VNC que pueda estar ejecutándose:

  ```bash
   vncserver -list
   ```
  *si tienes instancias corriendo cierralas con*
  
   ```bash
   vncserver -kill :1 o la instancia que tengas abierta
   ```

   **2.** Luego, inicias el servidor VNC y ejecutas Telegram-Desktop:

   ```bash
   vncserver -listen tcp :1
   proot-distro login debian --env DISPLAY=:1 --shared-tmp --bind $HOME/storage/Download:/root/Downloads -- telegram-desktop
   ```

   **Script completo:**
   Crea un archivo que mas creas conveniente, en mi caso `telegram` 
   ```bash
   nano telegram
   ```
   y agrega el siguiente contenido:

   ```bash
   #!/bin/bash
   # Matar las primeras 5 instancias de VNC si están corriendo
   for i in {1..5}
   do
       vncserver -kill :$i
   done

   # Iniciar el servidor VNC
   vncserver -listen tcp :1

   # Iniciar Telegram-Desktop en Debian
   proot-distro login debian --env DISPLAY=:1 --shared-tmp --bind $HOME/storage/Download:/root/Downloads -- telegram-desktop
   ```

   **3.** Haz que el script sea ejecutable:

   ```bash
   chmod +x telegram
   ```
  **4.** Ponlo en /usr/bin
  ```bash
   cp telegram $PREFIX/bin/
   ```
**Por ultimo solo reinicia tu shell**
  ```bash
  exec bash 
   ```
   Ahora, cada vez que quieras iniciar el entorno gráfico con Telegram-Desktop, simplemente ejecuta el script con:

   ```bash
   telegram
   ```
--- 
