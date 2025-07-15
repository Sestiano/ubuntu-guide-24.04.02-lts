# Ubuntu 24.04 LTS Complete Setup Guide

> **⚠️ Important Notice**: This guide is comprehensive but not necessarily in perfect sequential order. Please read through sections before executing commands. Some steps require specific ordering (especially Steps 1-3). When in doubt, test commands in a virtual machine first using for example GNOME Boxes.

## Table of Contents

1. [Installation and Initial Setup](#1-installation-and-initial-setup)
2. [Desktop Customization](#3-desktop-customization)
3. [Package Management Setup](#4-package-management-setup)
4. [Terminal Enhancement](#5-terminal-enhancement)
5. [Essential Software](#6-essential-software)
6. [Development Environment](#7-development-environment)
7. [Gaming Setup](#8-gaming-setup)
8. [Advanced Tools](#9-advanced-tools)
9. [GRUB Dual Boot Configuration](#10-grub-dual-boot-configuration)

---

## 1. Installation and Initial Setup

### Why Ubuntu 24.04 LTS?

Ubuntu 24.04 LTS (Long Term Support) is recommended because:
- Everything works out of the box
- Extensive community support and documentation
- Long-term stability and security updates
- Better compatibility than newer non-LTS versions with some applications

**Alternative considerations:**
- **Linux Mint**: Great alternative but currently X11-based (Wayland support coming ~2027)
- **Fedora 42 Workstation**: Cutting-edge but requires more maintenance
- **EndeavourOS**: Arch-based, powerful but needs technical expertise

### Installation Process

1. **Download**: Get Ubuntu 24.04 LTS from the official website
2. **Create bootable USB**: Use Rufus (Windows) or similar tools like Balena Etcher (Linux)
3. **BIOS Setup**: 
   - Boot from USB (F2 or similar to access BIOS)
   - Disable Secure Boot temporarily
   - Set USB as first boot device
4. **Installation**:
   - Select "Try or Install Ubuntu"
   - Choose language and keyboard layout
   - Connect to WiFi
   - Select "Extended selection" for more pre-installed apps
   - Enable third-party repositories (important for codecs)
   - Choose installation type (dual boot or clean install)
   - Create user account and wait for installation

### Post-Installation Setup

**⚠️ Follow this order carefully:**

1. **Ubuntu Pro Registration** (free):
   - Open Firefox and register for Ubuntu Pro
   - Enter the provided code to enable Livepatch

2. **System Updates**:
```bash
# Update package lists and upgrade system
sudo apt update && sudo apt upgrade

# Update snap packages
sudo snap refresh

# Reboot to apply updates
sudo reboot

# Clean up after reboot
sudo apt autoremove
sudo apt autoclean
```

3. **Re-enable Secure Boot** (recommended):
   - Enter BIOS and re-enable Secure Boot after first reboot

4. **Install Timeshift** (Essential backup tool):
```bash
sudo apt install timeshift
```
   - Configure Timeshift with recommended settings: 2 daily, 1 weekly, 3 monthly snapshots
   - Create your first system snapshot

5. **Account Integration**:
   - Add Google/Microsoft accounts in Settings for calendar, drive access, and Firefox sync

---

## 2. Desktop Customization

### GNOME Tweaks
```bash
sudo apt install gnome-tweaks
```

**Key Settings:**
- **Windows** → **Focus on Hover**: Enable for mouse-over window activation

### GNOME Extensions
```bash
sudo apt install gnome-shell-extension-manager
```
One thing I do in system Settings is to disable in Ubuntu Desktop the option Window Tiling that is too similar to Windows and I do not like that. (Third option)

**Recommended Extensions:**
- **Disable Ubuntu Dock**: For cleaner overview mode
- **GSConnect**: Android device integration
- **Clipboard Indicator**: Access with `Super + W`
- **Removable Drive Menu**: Quick drive access
- **DDTerm**: Drop-down terminal
- **Top Bar Organizer**: Customize top bar layout

### Firefox Customization
One of my favourite customization in Firefox is the use of containers.
**Container Setup:**
- Right-click the + button → Manage Containers
- Create custom containers for different accounts (e.g., university email)
- Use containers to manage multiple accounts in one browser

---

## 3. Package Management Setup

### Philosophy
This guide prioritizes **Flatpak** over Snap for better performance and updates. Installation sources in order of preference:
1. **Flatpak** (flathub.org) - Universal and up-to-date
2. **APT** (official repositories) - System integration
3. **Direct downloads** - For specific software like VS Code
4. **Snap** - Only for pre-installed apps

### Install Flatpak
```bash
# Install Flatpak package manager
sudo apt install flatpak

# Add GNOME Software integration
sudo apt install gnome-software-plugin-flatpak

# Add Flathub repository
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo

# Reboot for complete integration
sudo reboot
```

**Update Flatpak apps:**
```bash
flatpak update
```

### Essential System Components
```bash
# Multimedia codecs and proprietary drivers
sudo apt install ubuntu-restricted-extras

# Additional fonts for better compatibility
sudo apt install fonts-crosextra-caladea fonts-crosextra-carlito

# Video/audio thumbnails in file manager
sudo apt install ffmpegthumbnailer
```

---

## 4. Terminal Enhancement

### Prerequisites
```bash
sudo apt install curl git
```

### Zsh Setup
```bash
# Install Zsh shell
sudo apt update
sudo apt install zsh

# Install Oh My Zsh framework
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

### Zsh Plugins
```bash
# Autosuggestions plugin
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

# Syntax highlighting plugin
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

# Edit configuration
nano ~/.zshrc
```

**In ~/.zshrc**, change:
```bash
plugins=(git)
```
to:
```bash
plugins=(git zsh-autosuggestions zsh-syntax-highlighting)
```

```bash
# Apply changes
source ~/.zshrc

# Set Zsh as default shell
chsh -s $(which zsh)

# Restart system to see effects
sudo reboot
```

### Additional Terminal Tools
```bash
# Better cat command
sudo apt install bat
echo "alias bat='batcat'" >> ~/.zshrc

# Fuzzy file finder
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
~/.fzf/install

# System monitoring tools
sudo apt install htop btop vim

# Disk usage analyzer
sudo apt install ncdu

# Terminal file manager
sudo apt install nnn

# Fun tools
sudo apt install cowsay fortune neofetch

# Reload configuration
source ~/.zshrc
```

### Terminal Font
1. Download [JetBrains Mono](https://fonts.google.com/specimen/JetBrains+Mono)
2. Copy to `~/.fonts/`
3. Configure in GNOME Terminal preferences

---

## 5. Essential Software

### Media and Productivity
```bash
# Media player
sudo apt install vlc

# Markdown editor
flatpak install flathub org.gnome.gitlab.somas.Apostrophe

# Project management
flatpak install flathub io.github.alainm23.planify

# Video conferencing
flatpak install flathub us.zoom.Zoom

# PDF editor
flatpak install flathub com.github.jeromerobert.pdfarranger

# Virtual machines
flatpak install flathub org.gnome.Boxes
```

### Cloud Storage
- **pCloud**: Download AppImage (see AppImage section for setup)

---

## 6. Development Environment

### Essential Build Tools
```bash
# Compiler and development tools (CRITICAL)
sudo apt install build-essential
```

### VS Code (Official Microsoft Repository)
```bash
# Add Microsoft GPG key
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
sudo install -D -o root -g root -m 644 packages.microsoft.gpg /etc/apt/keyrings/packages.microsoft.gpg

# Add repository
echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" | sudo tee /etc/apt/sources.list.d/vscode.list > /dev/null

# Clean up and install
rm -f packages.microsoft.gpg
sudo apt install apt-transport-https
sudo apt update
sudo apt install code
```

### Python Environment - Miniconda
```bash
# Download and install Miniconda
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm ~/miniconda3/miniconda.sh

# Initialize conda
source ~/miniconda3/bin/activate
conda init --all
conda config --set auto_activate_base false

# Restart terminal to see changes
```

### Text Editors
```bash
# Modern Vim (install from official sources, not apt)
sudo apt install neovim
```

---

## 7. Gaming Setup

### Steam Installation (Official Method)

1. **Download GPG Key**: Visit [Steam Repository](https://repo.steampowered.com/steam/) and download `steam.gpg`

2. **Install Steam**:
```bash
# Move GPG key to system location
sudo cp ~/Downloads/steam.gpg /usr/share/keyrings/
rm ~/Downloads/steam.gpg

# Add Steam repository
sudo tee /etc/apt/sources.list.d/steam-stable.list <<'EOF'
deb [arch=amd64,i386 signed-by=/usr/share/keyrings/steam.gpg] https://repo.steampowered.com/steam/ stable steam
deb-src [arch=amd64,i386 signed-by=/usr/share/keyrings/steam.gpg] https://repo.steampowered.com/steam/ stable steam
EOF

# Enable 32-bit architecture
sudo dpkg --add-architecture i386

# Install Steam and dependencies
sudo apt update
sudo apt install \
  libgl1-mesa-dri:amd64 \
  libgl1-mesa-dri:i386 \
  libgl1:amd64 \
  libgl1:i386 \
  steam-launcher
```

### Proton GE (Enhanced Windows Compatibility)

1. **Download**: Get latest Proton GE from [GitHub Releases](https://github.com/GloriousEggroll/proton-ge-custom/releases)

2. **Install**:
```bash
# Extract downloaded file
cd ~/Downloads
tar -xf GE-Proton*.tar.gz
rm GE-Proton*.tar.gz

# Create Steam compatibility tools directory
mkdir -p ~/.steam/root/compatibilitytools.d

# Move Proton GE to Steam directory
mv GE-Proton* ~/.steam/root/compatibilitytools.d/
```

3. **Configure**: Open Steam → Settings → Compatibility → Select new Proton version

---

## 8. Advanced Tools

### AppImage Support
```bash
# CRITICAL: Install libfuse2 (NOT libfuse3 or fuse2/3)
sudo apt install libfuse2

# AppImage manager
flatpak install flathub it.mijorus.gearlever
```

**Using AppImages:**
1. Download AppImage files
2. Open with Gear Lever
3. Click "Unlock" and "Add to menu" for integration

### Local AI - Llama.cpp
```bash
# Clone repository
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp

# Install dependencies
sudo apt install cmake libcurl4-openssl-dev

# Build
mkdir build && cd build
cmake ..
make -j$(nproc)
```

### Performance Notes
- **Avoid**: preload and tlp packages (despite popular claims)
- **GPU drivers**: Follow official NVIDIA/AMD guides for CUDA/ROCm support

---

## 9. GRUB Dual Boot Configuration

### Auto-select Last OS & Hide Menu

**Backup current configuration:**
```bash
sudo cp /etc/default/grub /etc/default/grub.backup
```

**Edit GRUB configuration:**
```bash
sudo nano /etc/default/grub
```

**Key settings to modify:**
```bash
# Remember last selection
GRUB_DEFAULT=saved
GRUB_SAVEDEFAULT=true

# Set timeout
GRUB_TIMEOUT=5

# Hide menu (show with ESC)
GRUB_TIMEOUT_STYLE=hidden
GRUB_HIDDEN_TIMEOUT_QUIET=false
```

**Complete example configuration:**
```bash
GRUB_DEFAULT=saved
GRUB_SAVEDEFAULT=true
GRUB_TIMEOUT=5
GRUB_TIMEOUT_STYLE=hidden
GRUB_HIDDEN_TIMEOUT_QUIET=false
GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
GRUB_CMDLINE_LINUX=""
```

**Apply changes:**
```bash
sudo update-grub
sudo reboot
```

**Controls:**
- **ESC**: Show hidden menu
- **↑/↓**: Navigate options
- **Enter**: Select option

**Restore backup if needed:**
```bash
sudo cp /etc/default/grub.backup /etc/default/grub
sudo update-grub
```

---

## Support and Contribution

This guide represents a comprehensive Ubuntu setup optimized for productivity, development, and gaming. If you encounter issues or have suggestions:

1. Test problematic commands in a virtual machine first
2. Check official documentation for the specific software
3. Verify you're following the correct sequence for system setup steps

**Alternative GUI tool**: Consider "GRUB Customizer" from Flathub for graphical GRUB configuration.

---
## 🚧 Status: Actively Maintained

This guide is a **living document**, regularly updated and improved.  
Contributions, corrections, and suggestions are very welcome!

- Found a mistake? Open an **issue**.
- Want to improve it? Submit a **pull request**.
- Just want to say hi or share ideas? Reach out in the repo discussions.

Last update: **July 15, 2025**

## License

This guide is provided as-is for educational purposes. Always verify commands and adapt them to your specific needs and hardware configuration.
