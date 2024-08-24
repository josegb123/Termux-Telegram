Aquí tienes la traducción para tu archivo `README-EN.md`:

---

# Termux-Telegram

Managing Telegram Downloads on Debian with Termux

## The Problem

I needed the files downloaded from Telegram to be saved with their original names, making them easier to locate and manage. Additionally, I wanted to be able to perform other tasks while the files were downloading.

## The Solution

I opted to create a Proot instance with Debian, as Ubuntu does not include the `telegram-desktop` package (at least not in version 24.04).

## Requirements

- RealVNC or a client of your choice
- Termux with xfce4
- Some knowledge of Linux, although I will explain step by step how to do it
- 6 GB of free space as a base for installation and use (3.5 GB for the Termux environment and the rest for Debian in Proot)
- Internet connection
- Tigervnc as a VNC server

## Instructions

1. **Install the Yisus graphical environment (if you already have one, it’s not necessary):**
   - Follow the instructions in the [Yisus7u7 - Termux Desktop XFCE](https://github.com/Yisus7u7/termux-desktop-xfce) repository.

2. **Create a Debian instance and install the necessary packages:**
   - Packages: `telegram-desktop`, `fonts-open-sans`, `noto-sans`.

3. **Set up the DISPLAY variable and link the download folder:**
   - Set the `DISPLAY=:1` variable in both Termux and the Debian instance:
     ```bash
     export DISPLAY=:1
     ```
   - Start the Debian instance with the following options:
     ```bash
     proot-distro login debian --env DISPLAY=:1 --shared-tmp --bind $HOME/storage/Download:/root/Downloads
     ```
   - **Explanation of the parameters:**
     - `--env DISPLAY=:1`: Enables the necessary display variable.
     - `--shared-tmp`: Necessary if you want pulseaudio sound inside `proot-distro`.
     - `--bind`: Sets the download folder in internal storage.

Now, simply connect to `localhost:1` or `127.0.0.1:1` with RealVNC or the VNC client of your choice.

### Process Automation

#### 1. **Add the `DISPLAY` variable to the `.bashrc` or `.zshrc` file**

To ensure that the `DISPLAY` variable is always set when starting a new session, add it at the end of your `.bashrc` or `.zshrc` file:

```bash
echo 'export DISPLAY=:1' >> ~/.bashrc
# or if you use zsh
echo 'export DISPLAY=:1' >> ~/.zshrc
```

Then, run `source` to apply the changes:

```bash
source ~/.bashrc
# or if you use zsh
source ~/.zshrc
```

#### 2. **Create a script to start VNC and Telegram-Desktop**

You can create a script to automate the entire process. Name the file `telegram`:

```bash
nano telegram
```

Add the following content:

```bash
#!/data/data/com.termux/files/usr/bin/bash
# Kill the first 5 instances of VNC if they are running
for i in {1..5}
do
    vncserver -kill :$i
done

# Start the VNC server
vncserver -listen tcp :1

# Start Telegram-Desktop on Debian
proot-distro login debian --env DISPLAY=:1 --shared-tmp --bind $HOME/storage/Download:/root/Downloads -- telegram-desktop
```

**3.** Make the script executable:

```bash
chmod +x telegram
```

**4.** Place the script in `/usr/bin`:

```bash
cp telegram /data/data/com.termux/files/usr/bin/
```

**5.** Restart your shell:

```bash
exec bash
```

Then, open RealVNC and connect to `localhost:1`.

## Using Termux X11

Some people prefer Termux X11 for its speed, though I personally prefer VNC for ease of use (virtual mouse and keyboard). As a prerequisite, you must have installed and configured both `termux-x11.deb` and the Termux X11 app. You can Google it to learn how.

### 1. Export the `DISPLAY` Variable

First, export the `DISPLAY` variable:

```bash
export DISPLAY=:1
```

### 2. Launch the Termux X11 App

Open the **Termux X11** app from your device. Ensure that it is configured to use display `:1`.

### 3. Return to Termux and Execute Termux X11

Once the app is open, return to Termux and run the following command to start the XFCE desktop session:

```bash
termux-x11 :1 -xstartup "xfce4-session" -legacy-drawing
```

### 4. Launch Telegram-Desktop

With the graphical environment active, start Telegram-Desktop in the Proot Debian instance:

```bash
proot-distro login debian --env DISPLAY=:1 --shared-tmp --bind $HOME/storage/Download:/root/Downloads -- telegram-desktop
```

### 5. Automating the Process

Follow these steps to automate the process:

#### a) Add the `DISPLAY` Variable to the `.bashrc` or `.zshrc` File

Open your `.bashrc` or `.zshrc` file and add the line to export the `DISPLAY` variable:

```bash
echo 'export DISPLAY=:1' >> ~/.bashrc
# or if you use zsh
echo 'export DISPLAY=:1' >> ~/.zshrc
```

Then, run `source` to apply the changes:

```bash
source ~/.bashrc
# or if you use zsh
source ~/.zshrc
```

#### b) Create the Automation Script

Create a script named `telegramx11`:

```bash
nano telegramx11
```

Add the following:

```bash
#!/data/data/com.termux/files/usr/bin/bash

# Start Termux X11
termux-x11 :1 -xstartup "xfce4-session" -legacy-drawing &

# Wait a moment to ensure X11 is ready
sleep 5

# Start Telegram-Desktop on Debian
proot-distro login debian --env DISPLAY=:1 --shared-tmp --bind $HOME/storage/Download:/root/Downloads -- telegram-desktop
```

**3.** Make the script executable:

```bash
chmod +x telegramx11
```

**4.** Place the script in `/usr/bin`:

```bash
cp telegramx11 /data/data/com.termux/files/usr/bin/
```

**5.** Restart your shell:

```bash
exec bash
```

## Now you can start Telegram in X11 with the command:

```bash
telegramx11
```

---
