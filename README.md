---

# Termux-Telegram

Gestión de Descargas de Telegram en Debian con Termux

## El Problema

Necesitaba que los archivos descargados de Telegram se guardaran con su nombre original, facilitando su ubicación y manejo. Además, quería poder realizar otras tareas mientras se descargaban.

## La Solución

Opté por crear una instancia de Proot con Debian, ya que Ubuntu no trae el paquete `telegram-desktop` (al menos no en la versión 24.04).

## Requisitos

- RealVNC o un cliente de tu preferencia
- Termux con xfce4
- Algo de conocimiento en Linux, aunque explicaré paso a paso cómo hacerlo
- 6 GB de espacio libre como base para instalar y usar (3.5 GB son para el entorno de Termux y el resto para Debian en Proot)
- Conexión a Internet
- Tigervnc como servidor VNC

## Instrucciones

1. **Instala el entorno gráfico de Yisus (si ya tienes uno, no es necesario):**
   - Sigue las instrucciones en el repositorio de [Yisus7u7 - Termux Desktop XFCE](https://github.com/Yisus7u7/termux-desktop-xfce).

2. **Crea una instancia de Debian e instala los paquetes necesarios:**
   - Paquetes: `telegram-desktop`, `fonts-open-sans`, `noto-sans`.

3. **Configura la variable DISPLAY y realiza el enlace de la carpeta de descargas:**
   - Establece la variable `DISPLAY=:1` tanto en Termux como en la instancia de Debian:
     ```bash
     export DISPLAY=:1
     ```
   - Inicia la instancia de Debian con las siguientes opciones:
     ```bash
     proot-distro login debian --env DISPLAY=:1 --shared-tmp --bind /storage/emulated/0/Download:/root/Downloads -- telegram-desktop
     ```
   - **Explicación de los parámetros:**
     - `--env DISPLAY=:1`: Habilita la variable necesaria para la pantalla.
     - `--shared-tmp`: Necesaria si quieres el audio de `pulseaudio` dentro de `proot-distro`.
     - `--bind`: Establece la carpeta de descargas en el almacenamiento interno.

Ahora solo resta conectarte a `localhost:1` o `127.0.0.1:1` con RealVNC o el cliente VNC de tu preferencia.

### Automatización del Proceso

#### 1. **Agregar la variable `DISPLAY` al archivo `.bashrc` o `.zshrc`**

Para asegurarte de que la variable `DISPLAY` esté siempre configurada al iniciar una nueva sesión, agrégala al final de tu archivo `.bashrc` o `.zshrc`:

```bash
echo 'export DISPLAY=:1' >> ~/.bashrc
# o si usas zsh
echo 'export DISPLAY=:1' >> ~/.zshrc
```

Luego, ejecuta `source` para aplicar los cambios:

```bash
source ~/.bashrc
# o si usas zsh
source ~/.zshrc
```

#### 2. **Crear un script para iniciar VNC y Telegram-Desktop**

Puedes crear un script para automatizar todo el proceso. Nombra el archivo como `telegram`:

```bash
nano telegram
```

Agrega el siguiente contenido:

```bash
#!/data/data/com.termux/files/usr/bin/bash
# Matar las primeras 5 instancias de VNC si están corriendo
for i in {1..5}
do
    vncserver -kill :$i
done

# Iniciar el servidor VNC
vncserver -listen tcp :1

# Iniciar Telegram-Desktop en Debian
proot-distro login debian --env DISPLAY=:1 --shared-tmp --bind /storage/emulated/0/Download:/root/Downloads -- telegram-desktop
```

**3.** Haz que el script sea ejecutable:

```bash
chmod +x telegram
```

**4.** Coloca el script en `/usr/bin`:

```bash
cp telegram /data/data/com.termux/files/usr/bin/
```

**5.** Reinicia tu shell:

```bash
exec bash
```

Luego, abre RealVNC y conéctate a `localhost:1`.



## Con Termux X11

Algunos prefieren Termux X11 por su velocidad, aunque personalmente prefiero VNC por su facilidad de uso (ratón y teclado virtuales).
como requisito previo debes haber instalado y configurado tanto termux-x11.deb como la app de termux x11
Puedes googlearlo para hacerlo.

### 1. Exportar la Variable `DISPLAY`

Primero, exporta la variable `DISPLAY`:

```bash
export DISPLAY=:1
```

### 2. Iniciar la App de Termux X11

Abre la app **Termux X11** desde tu dispositivo. Asegúrate de que esté configurada para utilizar el display `:1`.

### 3. Regresar a Termux y Ejecutar Termux X11

Una vez abierta la app, regresa a Termux y ejecuta el siguiente comando para iniciar la sesión de escritorio XFCE:

```bash
termux-x11 :1 -xstartup "xfce4-session" -legacy-drawing
```

### 4. Iniciar Telegram-Desktop

Con el entorno gráfico activo, inicia Telegram-Desktop en la instancia de Proot con Debian:

```bash
proot-distro login debian --env DISPLAY=:1 --shared-tmp --bind /storage/emulated/0/Download:/root/Downloads -- telegram-desktop
```

### 5. Automatización del Proceso

Sigue estos pasos para automatizar el proceso:

#### a) Agregar la Variable `DISPLAY` al archivo `.bashrc` o `.zshrc`

Abre tu archivo `.bashrc` o `.zshrc` y añade la línea para exportar la variable `DISPLAY`:

```bash
echo 'export DISPLAY=:1' >> ~/.bashrc
# o si usas zsh
echo 'export DISPLAY=:1' >> ~/.zshrc
```

Luego, ejecuta `source` para aplicar los cambios:

```bash
source ~/.bashrc
# o si usas zsh
source ~/.zshrc
```

#### b) Crear el Script de Automatización

Crea un script llamado `telegramx11`:

```bash
nano telegramx11
```

Agrega lo siguiente:

```bash
#!/data/data/com.termux/files/usr/bin/bash

# Iniciar Termux X11
termux-x11 :1 -xstartup "xfce4-session" -legacy-drawing &

# Esperar un momento para asegurarse de que X11 está listo
sleep 5

# Iniciar Telegram-Desktop en Debian
proot-distro login debian --env DISPLAY=:1 --shared-tmp --bind /storage/emulated/0/Download:/root/Downloads -- telegram-desktop
```

**3.** Haz que el script sea ejecutable:

```bash
chmod +x telegramx11
```

**4.** Coloca el script en `/usr/bin`:

```bash
cp telegramx11 #!/data/data/com.termux/files/usr/bin/
```

**5.** Reinicia tu shell:

```bash
exec bash
```

## Ahora puedes iniciar Telegram en X11 con el comando:

```bash
telegramx11
```

---

