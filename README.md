# Ubuntu 24.04 LTS — Complete Setup Guide

> ⚠️ **Important**: this guide is under continuous revision. Not all commands have been tested on every hardware configuration, therefore before running anything on your main system, try it in a VM first (e.g. GNOME Boxes). Commands with `sudo` modify your system: always read them before running.
>
> Tested on: Acer Swift 3 SF314, ASUS TUF F15 (RTX 4070 Mobile).

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


### Useful tricks

`Tab` autocompletes commands and file names. `↑` and `↓` scroll through previous commands. `Ctrl+C` interrupts a running command. `Ctrl+L` clears the screen.

---

## 2. Installation and Initial Setup

### Why Ubuntu 24.04 LTS?

It's the easiest option for anyone who wants stability without chasing updates every six months. Support is guaranteed until 2029, the community is huge, and almost everything works out of the box.

If you're already comfortable with Linux and want something more cutting-edge: Fedora Workstation is excellent. If you want something Ubuntu-like but lighter: Linux Mint (currently still X11-based, Wayland support expected around 2027).

### Installation

1. Download Ubuntu 24.04 LTS from the [official website](https://ubuntu.com/download/desktop)
2. Create a bootable USB with [Balena Etcher](https://etcher.balena.io/) or [Rufus](https://rufus.ie/) (Windows only)
3. Enter your BIOS (usually F2, F12, Del or Esc at boot, it depends on the manufacturer), temporarily disable Secure Boot, and set the USB as the first boot device
4. Boot from USB, choose "Try or Install Ubuntu"
5. Select "Extended selection" for more preinstalled apps, and enable third-party repositories (important for codecs and drivers but fixable later)


### After installation — follow this order

**1. Ubuntu Pro (free for personal use)**
Register at [ubuntu.com/pro](https://ubuntu.com/pro) to enable Livepatch: receive kernel security updates without rebooting.

**2. System updates**

```bash
sudo apt update && sudo apt upgrade -y   # update the system and apt packages
sudo snap refresh                        # update snap packages (you can remove snapd if you want)
sudo reboot                              # reboot system to apply the updates

-y is there to automatically accept the upgrade, if you don't write -y, it will be simply asked if you want to proceed with a Y/n choice.

# after reboot
sudo apt autoremove -y
sudo apt autoclean
```

The reboot matters, kernel updates require a restart to take effect.

**3. Re-enable Secure Boot**
Go back into BIOS and re-enable Secure Boot. It protects against boot-level malware.

**4. Install Timeshift (essential)**

```bash
sudo apt install timeshift -y
```

Open it from the application menu, choose RSYNC as the backup type, set a destination (preferably an external drive or separate partition), and create your first snapshot immediately. It's the Linux equivalent of Windows System Restore.

**5. Account integration (optional)**
Settings → Online Accounts to connect Google or Microsoft (calendar, contacts, cloud). Firefox Sync is configured separately in Firefox settings.

---

## 3. Package Management

### Philosophy

This guide uses **Flatpak** as the first choice for desktop apps because versions are more up-to-date, startup times are fast, and desktop integration has improved a lot in recent years.

### Set up Flatpak

```bash
sudo apt install flatpak -y
sudo apt install gnome-software-plugin-flatpak -y
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
sudo reboot
```

### Essential system components

```bash
# Multimedia codecs and proprietary drivers
sudo apt install ubuntu-restricted-extras -y

# Fonts for better Microsoft Office document compatibility
sudo apt install fonts-crosextra-caladea fonts-crosextra-carlito -y

# Video thumbnails in the file manager
sudo apt install ffmpegthumbnailer -y

# Archive format support
sudo apt install unrar p7zip-full p7zip-rar -y
```

---

## 4. Hardware Configuration

### NVIDIA GPU (for laptops like ASUS TUF F15)

If your are in WSL2 on Windows go directly to CUDA Toolkit, you have to install NVIDIA drivers on Windows.

**Option 1 — Ubuntu Driver Manager (recommended for beginners)**

```bash
software-properties-gtk --open-tab=4
```

Select the recommended proprietary driver, apply and reboot.

**Option 2 — Command line**

```bash
sudo add-apt-repository ppa:graphics-drivers/ppa -y
sudo apt update
sudo ubuntu-drivers install
sudo reboot

# verify
nvidia-smi
```

#### CUDA Toolkit

Required for GPU-accelerated computing (AI/ML, video editing, simulations).

```bash
sudo apt install nvidia-cuda-toolkit -y
nvcc --version
```

For a specific CUDA version, download directly from [developer.nvidia.com/cuda-downloads](https://developer.nvidia.com/cuda-downloads).

Add CUDA to your PATH in `~/.zshrc` or `~/.bashrc`:

```bash
export PATH=/usr/local/cuda/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH
```

### ASUS laptops — asusctl

`asusctl` lets you control fan curves, performance profiles, and battery charge limits. **Important**: it's not available in Ubuntu's official repositories and does not officially support Ubuntu. It needs to be compiled from source.

If you want to try it, find the official build instructions at [asus-linux.org](https://asus-linux.org/guides/asusctl-install/) or community-built scripts for Ubuntu 24.04 at [github.com/dariomncs/asus-ubuntu](https://github.com/dariomncs/asus-ubuntu). Test on a non-critical system first.



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

- **Disable Ubuntu Dock** — cleaner overview mode
- **GSConnect** — Android integration: file sharing, notifications, SMS
- **Clipboard Indicator** — clipboard history with `Super + W`
- **Removable Drive Menu** — quick access to USB drives and external disks
- **DDTerm** — drop-down terminal with `Super + ~`
- **Top Bar Organizer** — customize the top bar layout

### Firefox — Container tabs

One of Firefox's most useful features: each container has separate cookies and sessions.

Right-click the `+` button → Manage Containers, create containers for Work, Personal, Shopping, etc. You can have multiple Google accounts or social media accounts open in parallel without interference.

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
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
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
  zsh-syntax-highlighting
  sudo                   # press Esc twice to add/remove sudo
  colored-man-pages
  command-not-found
)
```

```bash
source ~/.zshrc
chsh -s $(which zsh)    # set zsh as the default shell
sudo reboot
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

# System info (actively maintained neofetch replacement)
sudo apt install fastfetch -y

source ~/.zshrc
```

### Terminal font

A good programming font improves readability and supports ligatures. Download [JetBrains Mono](https://fonts.google.com/specimen/JetBrains+Mono) or [Fira Code](https://github.com/tonsky/FiraCode), then:

```bash
mkdir -p ~/.fonts
cp ~/Downloads/JetBrainsMono-*.ttf ~/.fonts/
fc-cache -f -v
```

Configure in GNOME Terminal: Preferences → Profiles → Font.

---

## 7. Essential Software

### Media and productivity

```bash
# Media player
sudo apt install vlc -y

# Markdown editor
flatpak install flathub org.gnome.gitlab.somas.Apostrophe -y

# Project management
flatpak install flathub io.github.alainm23.planify -y

# Video conferencing
flatpak install flathub us.zoom.Zoom -y

# PDF management
flatpak install flathub com.github.jeromerobert.pdfarranger -y

# Virtual machines
flatpak install flathub org.gnome.Boxes -y
```

## 8. Development Environment

### Build tools

Install these first, many things depend on them to compile from source.

```bash
sudo apt install build-essential -y
sudo apt install libssl-dev libffi-dev libncurses5-dev zlib1g-dev \
                 libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm \
                 libncursesw5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev \
                 libffi-dev liblzma-dev -y
```

### VS Code

```bash
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | \
  gpg --dearmor > packages.microsoft.gpg
sudo install -D -o root -g root -m 644 packages.microsoft.gpg \
  /etc/apt/keyrings/packages.microsoft.gpg

echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/keyrings/packages.microsoft.gpg] \
  https://packages.microsoft.com/repos/code stable main" | \
  sudo tee /etc/apt/sources.list.d/vscode.list > /dev/null

rm -f packages.microsoft.gpg
sudo apt install apt-transport-https -y
sudo apt update
sudo apt install code -y
```

### Python — uv

uv is a Python package and environment manager written in Rust, orders of magnitude faster than pip. It's become the recommended tool for most modern Python projects.

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh

# The installer automatically updates PATH in your shell profile.
# If it doesn't work, add manually:
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc

uv --version
```

Basic usage:

```bash
uv venv                    # create a virtual environment in .venv
source .venv/bin/activate  # activate it
uv pip install package     # install (much faster than pip)
uv python install 3.12     # install a specific Python version
```

### Python — Miniconda (alternative for data science)

If you work heavily with data and have complex dependencies (numpy, pytorch, etc.), Conda handles non-Python packages like drivers and system libraries better.

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
sudo apt install libfuse2 -y

# AppImage manager with desktop integration
flatpak install flathub it.mijorus.gearlever -y
```

To use an AppImage without Gear Lever:

```bash
chmod +x ~/Downloads/App.AppImage
./App.AppImage
```

With Gear Lever: open the app, drag in the AppImage or open it with "Unlock", then "Add to menu" to integrate it in the launcher like a regular app.

### Docker

```bash
sudo apt install docker.io -y
sudo usermod -aG docker $USER   # avoid needing sudo every time
sudo systemctl enable docker
sudo systemctl start docker

# log out and back in to apply group changes
docker --version
docker run hello-world

sudo apt install docker-compose -y
```

### Local AI with llama.cpp

Run language models directly on your machine: no internet, no API key, no data sent anywhere.
I also suggest to start with a GUI app such as LM Studio (you can install it as an appimage app). This app include a lot of functions and the possibility to check before the download if the model is gonna run on your machine.

Install llama.cpp:
```bash
cd ~
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp

sudo apt install cmake libcurl4-openssl-dev -y

# Build with CUDA (NVIDIA GPU)
mkdir build && cd build
cmake .. -DGGML_CUDA=ON
cmake --build . --config Release -j$(nproc)

# CPU-only build: remove the -DGGML_CUDA=ON line
```

---

## 11. GRUB and Dual Boot

If you dual-boot with Windows, GRUB is the menu that appears at startup. You can configure it to remember the last OS choice and hide the menu by default.

**Always back up before touching GRUB**:

```bash
sudo cp /etc/default/grub /etc/default/grub.backup
```

**Edit the configuration**:

```bash
sudo nano /etc/default/grub
```

Recommended configuration for dual boot:

```bash
GRUB_DEFAULT=saved          # remember last choice
GRUB_SAVEDEFAULT=true
GRUB_TIMEOUT=5              # seconds to wait
GRUB_TIMEOUT_STYLE=hidden   # hide menu (press ESC to show it)
GRUB_HIDDEN_TIMEOUT_QUIET=false
GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
GRUB_CMDLINE_LINUX=""
```

**Apply changes**:

```bash
sudo update-grub
sudo reboot
```

At boot: `ESC` shows the hidden menu, `↑/↓` to navigate, `Enter` to boot.

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

# Always show menu (no hiding)
GRUB_TIMEOUT_STYLE=menu

# Show all kernels in the main menu
GRUB_DISABLE_SUBMENU=y
```

For a graphical interface, GRUB Customizer is on Flathub: `flatpak install flathub com.github.grub-customizer.GrubCustomizer -y`.

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
command 2>/dev/null

# Save both output and errors
command > all.txt 2>&1

# Pipe: pass output of one command as input to the next
ls -la | grep ".txt"            # filter for .txt only
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
find . -size -1k            # files smaller than 1KB

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
grep -c "word" file.txt           # count occurrences

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
find . -name "*.tmp" | xargs rm                        # delete all .tmp files
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
Ctrl+B %        split vertically
Ctrl+B "        split horizontally
Ctrl+B arrows   navigate between panes
Ctrl+B d        detach (session stays alive)
Ctrl+B [        scroll mode (q to exit)
```

### SSH — remote connections

```bash
ssh user@ip-address
ssh -p 2222 user@server           # specific port

Configure `~/.ssh/config` to avoid remembering IPs and options:

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
wsl --install
```

This installs WSL2 with Ubuntu by default. Reboot when prompted.

Often you have to redo wsl --install after the reboot to actually download and install the distro. 

To choose a different distribution:

```powershell
wsl --list --online              # see available distros
wsl --install -d Ubuntu-24.04
wsl --install -d Debian
```

Upgrading from WSL1:

```powershell
wsl --set-default-version 2
wsl --set-version Ubuntu 2
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

nvidia-smi    # verify
```

**Enable TRIM for SSDs**
(Only tested on the Acer)

```bash
sudo systemctl enable fstrim.timer && sudo systemctl start fstrim.timer
```
---

*Guide in progress — last updated: April 2026*
