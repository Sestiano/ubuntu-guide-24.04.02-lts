# Ubuntu 24.04 LTS — Complete Setup Guide

> ⚠️ **Important**: this guide is under continuous revision. I tested almost every command on my laptops, but hardware varies: before running anything on your main system, try it in a VM first (e.g. GNOME Boxes). Commands with `sudo` modify your system: always read them before running.
>
> Tested on: Acer Swift 3 SF314, ASUS TUF F15 (RTX 4070 Laptop GPU).
>
> Written and tested on Ubuntu 24.04 LTS. Most commands should also work on Ubuntu 26.04 LTS, but I haven't tested them there.

---

## Table of Contents

1. [Terminal Basics](#1-terminal-basics)
2. [Installation and Initial Setup](#2-installation-and-initial-setup)
3. [Package Management](#3-package-management)
4. [Hardware Configuration](#4-hardware-configuration)
5. [Desktop Customization](#5-desktop-customization)
6. [Terminal Enhancement](#6-terminal-enhancement)
7. [Essential Software](#7-essential-software)
8. [Development Environment](#8-development-environment)
9. [Gaming](#9-gaming)
10. [Advanced Tools](#10-advanced-tools)
11. [GRUB and Dual Boot](#11-grub-and-dual-boot)
12. [Advanced Bash](#12-advanced-bash)
13. [WSL2 on Windows](#13-wsl2-on-windows)

---

## 1. Terminal Basics

**How to open it**: `Ctrl + Alt + T`, or search for "Terminal" in the application menu.

### Navigation and files

```bash
pwd                 # where am I right now
ls                  # what's in this folder
ls -la              # everything, including hidden files, with details
cd folder           # enter a folder
cd ..               # go up one level
cd ~                # go to home directory

mkdir name          # create a folder
touch file.txt      # create an empty file
cp source dest      # copy
mv old new          # move or rename
rm file             # delete a file
rm -r folder        # delete a folder (be careful!)

cat file.txt        # show file contents
less file.txt       # browse contents (q to quit)
```

### `sudo` — what it does and when to use it

`sudo` (Super User DO) gives you administrator permissions for that single command. It will ask for your password.

Use it for: installing/removing software, modifying system configurations, managing services. Never use it "just in case", every command with `sudo` has full access to your system.

### Package managers — quick reference

```bash
# APT (system packages)
sudo apt update              # refresh package lists
sudo apt upgrade             # upgrade installed packages
sudo apt install package     # install
sudo apt remove package      # remove
sudo apt autoremove          # clean unused dependencies
```

### Useful tricks

`Tab` autocompletes commands and file names. `↑` and `↓` scroll through previous commands. `Ctrl+C` interrupts a running command. `Ctrl+L` clears the screen.

---

## 2. Installation and Initial Setup

### Why Ubuntu 24.04 LTS?

It's the easiest option for anyone who wants stability without chasing updates every six months. Standard support lasts until May 2029 (until 2034 with Ubuntu Pro), the community is huge, and almost everything works out of the box.

I wrote this guide when 24.04 was the latest LTS. Ubuntu 26.04 LTS came out in April 2026.

If you're already comfortable with Linux and want something more cutting-edge: Fedora Workstation is excellent. If you want something Ubuntu-like but lighter: Linux Mint.

### Installation

1. Download Ubuntu 24.04 LTS from [releases.ubuntu.com/24.04](https://releases.ubuntu.com/24.04/) (the main download page now offers 26.04)
2. Create a bootable USB with [Balena Etcher](https://etcher.balena.io/) or [Rufus](https://rufus.ie/) (Windows only)
3. Enter your BIOS (usually F2, F12, Del or Esc at boot, it depends on the manufacturer) and set the USB as the first boot device. Secure Boot can stay enabled: Ubuntu supports it. If you dual-boot and Windows uses BitLocker (device encryption), save the recovery key before changing anything in the BIOS
4. Boot from USB, choose "Try or Install Ubuntu"
5. Select "Extended selection" for more preinstalled apps, and enable "Install third-party software for graphics and Wi-Fi hardware" and "Download and install support for additional media formats" (important for drivers and codecs, but fixable later). If the installer asks for a Secure Boot password, choose one: at the first reboot select "Enroll MOK" and enter it

### After installation — follow this order

**1. Ubuntu Pro (free for personal use)**
Register at [ubuntu.com/pro](https://ubuntu.com/pro) to enable Livepatch: receive kernel security updates without rebooting.

**2. System updates**

```bash
# -y automatically accepts the upgrade; without it you are asked to confirm with Y/n
sudo apt update && sudo apt upgrade -y   # update the system and apt packages
sudo snap refresh                        # update snap packages
sudo reboot                              # reboot system to apply the updates

# after reboot
sudo apt autoremove -y
sudo apt autoclean
```

The reboot matters, kernel updates require a restart to take effect.

**3. Install Timeshift (essential)**

```bash
sudo apt install timeshift -y
```

Open it from the application menu, choose RSYNC as the backup type, set a destination (preferably an external drive or separate partition), and create your first snapshot immediately. It's the Linux equivalent of Windows System Restore.

Timeshift protects system files and settings, not your personal files: home folders are excluded by default. For documents and photos use a real backup tool, e.g. Déjà Dup (`sudo apt install deja-dup`).

**4. Account integration (optional)**
Settings → Online Accounts to connect Google or Microsoft (calendar, contacts, cloud). Firefox Sync is configured separately in Firefox settings.

---

## 3. Package Management

### Philosophy

This guide uses **Flatpak** as the first choice for desktop apps because versions are more up-to-date, startup times are fast, and desktop integration has improved a lot in recent years.

### Set up Flatpak

```bash
sudo apt install flatpak gnome-software-plugin-flatpak -y
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
sudo reboot
```

The plugin also installs GNOME Software ("Software"), next to the preinstalled App Center: Flatpak apps show up in Software, not in App Center.

### Essential system components

```bash
# Multimedia codecs, Microsoft fonts and unrar
# (a license screen for the fonts appears: accept it with Tab and Enter)
sudo apt install ubuntu-restricted-extras -y

# Fonts for better Microsoft Office document compatibility
sudo apt install fonts-crosextra-caladea fonts-crosextra-carlito -y

# Video thumbnails in the file manager
sudo apt install ffmpegthumbnailer -y

# 7z archive support (rar is already covered by ubuntu-restricted-extras)
sudo apt install 7zip 7zip-rar -y
```

---

## 4. Hardware Configuration

### NVIDIA GPU (for laptops like ASUS TUF F15)

If you are in WSL2 on Windows, skip this section: install the NVIDIA driver on Windows and follow [GPU and CUDA on WSL2](#gpu-and-cuda-on-wsl2).

**Option 1 — Ubuntu Driver Manager (recommended for beginners)**

```bash
software-properties-gtk --open-tab=4
```

Select the recommended proprietary driver, apply and reboot.

**Option 2 — Command line**

```bash
sudo ubuntu-drivers install
sudo reboot

# verify
nvidia-smi
```

#### CUDA Toolkit

Needed only to compile CUDA code (e.g. llama.cpp in [section 10](#local-ai-with-llamacpp)). PyTorch installed with pip, uv or conda ships its own CUDA libraries and only needs the driver.

```bash
sudo apt install nvidia-cuda-toolkit -y
nvcc --version
```

The apt package (CUDA 12.0) installs into `/usr/bin`, so no PATH changes are needed.

For a specific CUDA version, download directly from [developer.nvidia.com/cuda-downloads](https://developer.nvidia.com/cuda-downloads). Only in that case, add CUDA to your PATH in `~/.zshrc` or `~/.bashrc`:

```bash
export PATH=/usr/local/cuda/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

### ASUS laptops — asusctl

`asusctl` lets you control fan curves, performance profiles, and battery charge limits. **Important**: it's not available in Ubuntu's official repositories and does not officially support Ubuntu. It needs to be compiled from source.

If you want to try it, follow the build instructions linked from [asus-linux.org](https://asus-linux.org/guides/asusctl-install/). Test on a non-critical system first.

---

## 5. Desktop Customization

### GNOME Tweaks

```bash
sudo apt install gnome-tweaks -y
```

Most useful settings: enable "Focus on Hover" under Windows to activate windows by hovering the mouse, customize the clock in Top Bar, change themes and icons under Appearance.

### GNOME Extensions

```bash
sudo apt install gnome-shell-extension-manager -y
```

In Settings → Ubuntu Desktop you can disable "Window Tiling" if you prefer classic window behavior.

Recommended extensions (install via Extension Manager):

- **Ubuntu Dock** — preinstalled system extension: turn it off in Extension Manager (Installed tab) for a cleaner overview mode
- **GSConnect** — Android integration: file sharing, notifications, SMS
- **Clipboard Indicator** — clipboard history (default shortcut `Ctrl + F9`, I remapped it to `Super + W`)
- **Removable Drive Menu** — quick access to USB drives and external disks
- **DDTerm** — drop-down terminal (default shortcut `F12`, I remapped it to `Super + ~`)
- **Top Bar Organizer** — customize the top bar layout

### Firefox — Container tabs

One of Firefox's most useful features: each container has separate cookies and sessions.

Install Mozilla's [Firefox Multi-Account Containers](https://addons.mozilla.org/en-US/firefox/addon/multi-account-containers/) add-on, then right-click the `+` button → Manage Containers, and create containers for Work, Personal, Shopping, etc. You can have multiple Google accounts or social media accounts open in parallel without interference.

---

## 6. Terminal Enhancement

### Prerequisites

```bash
sudo apt install curl git -y
```

### Zsh + Oh My Zsh

Zsh has smarter autocompletion, themes, and a rich plugin ecosystem compared to Bash.

```bash
sudo apt install zsh -y
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

**Recommended plugins**:

```bash
# Fish-like autosuggestions while you type
git clone https://github.com/zsh-users/zsh-autosuggestions \
  ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

# Syntax highlighting
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git \
  ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

Edit `~/.zshrc`, find the line `plugins=(git)` and replace it:

```bash
plugins=(
  git
  zsh-autosuggestions
  sudo                   # press Esc twice to add/remove sudo
  colored-man-pages
  command-not-found
  zsh-syntax-highlighting   # must be the last plugin
)
```

The Oh My Zsh installer already offers to make zsh your default shell. If you skipped that step:

```bash
chsh -s $(which zsh)    # set zsh as the default shell
sudo reboot             # zsh loads ~/.zshrc automatically at the next login
```

### Additional tools

```bash
# cat with syntax highlighting
sudo apt install bat -y
echo "alias bat='batcat'" >> ~/.zshrc

# Fuzzy search: Ctrl+R for history, Ctrl+T for files
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
~/.fzf/install

# System monitors
sudo apt install htop btop -y

# Modern text editor
sudo apt install neovim -y

# Disk usage analyzer (ncurses)
sudo apt install ncdu -y

# Terminal file manager
sudo apt install nnn -y

# Fast content search
sudo apt install ripgrep fd-find -y
echo "alias fd='fdfind'" >> ~/.zshrc

source ~/.zshrc
```

On newer Ubuntu releases (25.04 and later) you can also install fastfetch, an actively maintained neofetch replacement: `sudo apt install fastfetch`.

### Terminal font

A good programming font improves readability. JetBrains Mono and Fira Code are in the Ubuntu repositories:

```bash
sudo apt install fonts-jetbrains-mono fonts-firacode -y
```

Configure in GNOME Terminal: Preferences → Profiles → Font. Their ligatures work in editors like VS Code, not in GNOME Terminal.

---

## 7. Essential Software

### Media and productivity

```bash
# Media player
sudo apt install vlc -y

# Markdown editor
flatpak install flathub org.gnome.gitlab.somas.Apostrophe -y

# To-do list
flatpak install flathub io.github.alainm23.planify -y

# Video conferencing
flatpak install flathub us.zoom.Zoom -y

# PDF management
flatpak install flathub com.github.jeromerobert.pdfarranger -y

# Virtual machines
flatpak install flathub org.gnome.Boxes -y
```

---

## 8. Development Environment

### Build tools

Install these first: they're needed to compile software from source (e.g. llama.cpp in section 10).

```bash
sudo apt install build-essential -y
```

### VS Code

Download the `.deb` package from [code.visualstudio.com](https://code.visualstudio.com/Download), then:

```bash
sudo apt install ~/Downloads/code_*.deb
```

During the installation you are asked whether to add Microsoft's repository: say yes, so VS Code updates together with the rest of the system.

### Python — uv

uv is a Python package and environment manager written in Rust, orders of magnitude faster than pip. I recommend it for most modern Python projects.

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

The installer adds `~/.local/bin` to your PATH. Open a new terminal and check:

```bash
uv --version
```

If the command is not found, add it manually:

```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

Basic usage:

```bash
uv venv                    # create a virtual environment in .venv
source .venv/bin/activate  # activate it
uv pip install package     # install (much faster than pip)
uv python install 3.12     # install a specific Python version
```

### Python — Miniconda (alternative for data science)

If you work heavily with data and have complex dependencies (numpy, pytorch, etc.), Conda also manages non-Python libraries, such as the CUDA runtime and C/C++ libraries.

```bash
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh \
  -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm ~/miniconda3/miniconda.sh

source ~/miniconda3/bin/activate
conda init --all
conda config --set auto_activate_base false
```

Basic usage:

```bash
conda create -n myenv python=3.11
conda activate myenv
conda install numpy pandas matplotlib
conda deactivate
```

---

## 9. Gaming

### Steam — official method

> ⚠️ Steam via Flatpak has performance and compatibility issues with some games. Use this method instead.

**Step 1**: go to [repo.steampowered.com/steam/](https://repo.steampowered.com/steam/) and download `steam.gpg`.

**Step 2**: install Steam.

```bash
sudo cp ~/Downloads/steam.gpg /usr/share/keyrings/
rm ~/Downloads/steam.gpg

sudo tee /etc/apt/sources.list.d/steam-stable.list <<'EOF'
deb [arch=amd64,i386 signed-by=/usr/share/keyrings/steam.gpg] https://repo.steampowered.com/steam/ stable steam
deb-src [arch=amd64,i386 signed-by=/usr/share/keyrings/steam.gpg] https://repo.steampowered.com/steam/ stable steam
EOF

sudo dpkg --add-architecture i386

sudo apt update
sudo apt install \
  libgl1-mesa-dri:amd64 \
  libgl1-mesa-dri:i386 \
  libgl1:amd64 \
  libgl1:i386 \
  steam-launcher -y
```

### Proton GE

Proton GE is a custom build of Proton (Valve's Windows compatibility layer) with extra patches, video cutscene codecs, and game-specific fixes for titles that don't work with the official Proton.

Download the latest release from [github.com/GloriousEggroll/proton-ge-custom/releases](https://github.com/GloriousEggroll/proton-ge-custom/releases), then:

```bash
cd ~/Downloads
tar -xf GE-Proton*.tar.gz
rm GE-Proton*.tar.gz

mkdir -p ~/.steam/root/compatibilitytools.d
mv GE-Proton* ~/.steam/root/compatibilitytools.d/

ls ~/.steam/root/compatibilitytools.d/   # verify
```

Restart Steam, then: Steam → Settings → Compatibility → enable Steam Play for all titles → select GE-Proton from the list.

For a single game: right-click → Properties → Compatibility → force a specific tool → select GE-Proton.

To update: download the new version, extract it, remove the old one from `compatibilitytools.d/`, move the new one in. Restart Steam.

Check game compatibility at [protondb.com](https://www.protondb.com/).

---

## 10. Advanced Tools

### AppImage support

AppImages are portable executables that run on any Linux distro without installation.

```bash
# Required for AppImage compatibility
sudo apt install libfuse2t64 -y

# AppImage manager with desktop integration
flatpak install flathub it.mijorus.gearlever -y
```

To use an AppImage without Gear Lever:

```bash
chmod +x ~/Downloads/App.AppImage
~/Downloads/App.AppImage
```

With Gear Lever: open the app, drag in the AppImage or open it with "Unlock", then "Add to menu" to integrate it in the launcher like a regular app.

### Docker

```bash
sudo apt install docker.io docker-compose-v2 -y   # docker-compose-v2 provides `docker compose`
sudo systemctl enable --now docker
sudo usermod -aG docker $USER   # avoid needing sudo every time
```

Members of the `docker` group have root-equivalent access to the system. Log out and back in to apply the group change, then:

```bash
docker --version
docker run hello-world
```

### Local AI with llama.cpp

Run language models directly on your machine: once a model is downloaded, no internet, no API key, no data sent anywhere.
I also suggest starting with a GUI app such as LM Studio (available as an AppImage). It includes many features and shows, before downloading, whether a model will run on your machine.

Install llama.cpp (needs the build tools from section 8; the CUDA build also needs the CUDA Toolkit from section 4):
```bash
cd ~
git clone https://github.com/ggml-org/llama.cpp
cd llama.cpp

sudo apt install cmake libssl-dev -y

# Build with CUDA (NVIDIA GPU)
cmake -B build -DGGML_CUDA=ON
cmake --build build --config Release -j$(nproc)

# CPU-only build: omit the -DGGML_CUDA=ON flag
```

Run a model (it's downloaded from Hugging Face on first use):

```bash
# chat in the terminal
./build/bin/llama-cli -hf ggml-org/Qwen3.5-0.8B-GGUF

# or start a server with a web UI at http://localhost:8080
./build/bin/llama-server -hf ggml-org/Qwen3.5-0.8B-GGUF
```

---

## 11. GRUB and Dual Boot

If you dual-boot with Windows, GRUB is the menu that appears at startup. You can configure it to remember the last OS choice.

**Always back up before touching GRUB**:

```bash
sudo cp /etc/default/grub /etc/default/grub.backup
```

**Edit the configuration**:

```bash
sudo nano /etc/default/grub
```

Recommended configuration for dual boot. Change only these lines (add them if missing) and leave the rest as it is:

```bash
GRUB_DEFAULT=saved             # remember last choice
GRUB_SAVEDEFAULT=true
GRUB_TIMEOUT=5                 # seconds to wait
GRUB_TIMEOUT_STYLE=menu        # always show the menu
GRUB_DISABLE_OS_PROBER=false   # always look for Windows (Ubuntu's default "auto" may skip it)
```

`GRUB_TIMEOUT_STYLE=hidden` has no effect in dual boot: when Ubuntu finds another operating system, it always shows the menu.

**Apply changes**:

```bash
sudo update-grub
sudo reboot
```

At boot: `↑/↓` to navigate, `Enter` to boot.

**If something goes wrong**:

```bash
sudo cp /etc/default/grub.backup /etc/default/grub
sudo update-grub && sudo reboot
```

**Additional customizations**:

```bash
# Change GRUB resolution — add to /etc/default/grub:
GRUB_GFXMODE=1920x1080
GRUB_GFXPAYLOAD_LINUX=keep

# Show all kernels in the main menu
GRUB_DISABLE_SUBMENU=y
```

---

## 12. Advanced Bash

This section covers commands and techniques that make a real difference in daily work. You don't need to memorize everything, just know these tools exist and come back to reference them when needed.

### Redirection and pipes

Every command has three channels: stdin (input), stdout (output), stderr (errors). You can redirect them.

```bash
# Save command output to a file
ls -la > list.txt               # overwrite
ls -la >> list.txt              # append

# Discard errors
cmd 2>/dev/null

# Save both output and errors
cmd > all.txt 2>&1

# Pipe: pass output of one command as input to the next
ls -la | grep '\.txt'           # filter for .txt only
ps aux | grep firefox           # find the firefox process
cat file.txt | sort | uniq      # sort and remove duplicates
```

### find — search for files

```bash
# by name
find /home -name "*.pdf"
find . -name "config.txt"

# by type
find . -type f              # files only
find . -type d              # directories only

# by size
find . -size +100M          # files larger than 100MB
find . -size -1024c         # files smaller than 1KB

# find and do something
find . -name "*.log" -delete               # delete all .log files
find . -name "*.py" -exec wc -l {} \;     # count lines in each .py file
```

### grep — search inside files

```bash
grep "word" file.txt              # search in a file
grep -r "word" folder/            # recursive search
grep -i "word" file.txt           # case insensitive
grep -n "word" file.txt           # show line numbers
grep -v "word" file.txt           # show lines that do NOT contain it
grep -c "word" file.txt           # count matching lines

# with pipe
journalctl | grep -i "error"
cat file.txt | grep "warning"
```

### awk — process structured text

`awk` is ideal for files with columns (CSV, logs, command output).

```bash
awk '{print $2}' file.txt               # print the second column
awk '{print $1, $3}' file.txt           # print first and third columns
awk '/error/ {print}' file.txt          # filter lines containing "error"
awk '{sum += $1} END {print sum}' n.txt # sum values in the first column
awk -F',' '{print $1}' data.csv         # custom separator (CSV)
```

### sed — stream editing

```bash
sed 's/old/new/' file.txt          # replace first occurrence per line
sed 's/old/new/g' file.txt         # replace all occurrences
sed -i.bak 's/old/new/g' file.txt  # edit file in place (with backup)
sed '/word/d' file.txt             # delete lines containing "word"
sed -n '5,10p' file.txt            # print only lines 5-10
```

### xargs — pass output as arguments

```bash
find . -name "*.tmp" -print0 | xargs -0 rm             # delete all .tmp files (safe with spaces in names)
cat list.txt | xargs -I {} echo "Processing: {}"       # run a command per line
cat urls.txt | xargs -P 4 -I {} wget {}                # parallel, 4 at a time
```

### tmux — persistent terminal sessions

tmux lets you have multiple windows in one terminal and, most importantly, detach from a session while leaving processes running. Essential for remote servers.

```bash
sudo apt install tmux -y

tmux                          # new session
tmux new -s name              # new named session
tmux ls                       # list active sessions
tmux attach -t name           # reconnect to a session
```

Main shortcuts (prefix: `Ctrl+B`):

```
Ctrl+B c        new window
Ctrl+B n / p    next / previous window
Ctrl+B %        split into left / right panes
Ctrl+B "        split into top / bottom panes
Ctrl+B arrows   navigate between panes
Ctrl+B d        detach (session stays alive)
Ctrl+B [        scroll mode (q to exit)
```

### SSH — remote connections

```bash
ssh user@ip-address
ssh -p 2222 user@server           # specific port
```

Configure `~/.ssh/config` to avoid remembering IPs and options:

```
Host myserver
    HostName 192.168.1.10
    User user
    Port 2222
```

Then connect with `ssh myserver`.

### System monitoring

```bash
# Processes
htop                          # interactive
ps aux | grep name            # find a specific process
kill PID                      # terminate (get PID from ps)
kill -9 PID                   # force terminate

# Disk
df -h                         # space per partition
du -sh *                      # size of items in current directory
ncdu                          # interactive disk analyzer

# Logs
journalctl -f                 # real-time
journalctl -u service-name    # specific service
journalctl --since "1 hour ago"
```

---

## 13. WSL2 on Windows

WSL2 (Windows Subsystem for Linux 2) runs a real Linux kernel inside Windows, not emulation. It's great if you need to work across both systems, or if you don't want to set up dual boot.

### Installation

On Windows 11, open PowerShell as administrator:

```powershell
wsl --install -d Ubuntu-24.04
```

This installs WSL2 with Ubuntu 24.04. Reboot when prompted. A plain `wsl --install` installs the generic `Ubuntu` distro, which follows the latest LTS.

Often you have to redo `wsl --install` after the reboot to actually download and install the distro.

To choose a different distribution:

```powershell
wsl --list --online              # see available distros
wsl --install -d Debian
```

Upgrading from WSL1:

```powershell
wsl --set-default-version 2
wsl -l -v                        # see your distro names
wsl --set-version Ubuntu-24.04 2
```

### First launch

On first open you'll be prompted to create a username and password. Then update:

```bash
sudo apt update && sudo apt upgrade -y
```

### Navigating between filesystems

Inside WSL2, the Windows C: drive is at `/mnt/c/`:

```bash
ls /mnt/c/Users/YourName/Desktop
cp file.txt /mnt/c/Users/YourName/Desktop/
```

From Windows Explorer, access Linux files by typing `\\wsl$` in the address bar.

> Keep Linux project files inside the Linux filesystem (`~/`). Operations on `/mnt/c/` are significantly slower.

### VS Code integration

1. Install VS Code on Windows
2. Install the [WSL extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl)
3. Inside WSL2, in your project folder:

```bash
code .
```

VS Code opens on Windows but is connected directly to the Linux filesystem. The integrated terminal is your Linux shell.

### GPU and CUDA on WSL2

**On Windows**: install the [NVIDIA driver with WSL2 support](https://developer.nvidia.com/cuda/wsl) (included in recent standard drivers).

**Inside WSL2** — do NOT install NVIDIA drivers inside WSL2, only the toolkit:

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt update
sudo apt install cuda-toolkit-12-6 -y

nvidia-smi    # verify that the Windows driver is visible
```

The toolkit installs into `/usr/local/cuda`, which is not in your PATH. Add it to `~/.bashrc` (or `~/.zshrc`) and verify:

```bash
echo 'export PATH=/usr/local/cuda/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
nvcc --version
```

---

## Disclaimer

The commands in this guide modify your system: you run them at your own risk, and I am not responsible for data loss, system instability or hardware damage. Take a Timeshift snapshot before major changes and keep a separate backup of your personal files, test in a VM when possible, and refer to the official documentation ([Ubuntu](https://help.ubuntu.com/), [NVIDIA](https://docs.nvidia.com/)) and to the licenses of the third-party software you install.

---

*Guide in progress — last updated: September 2026*
