# Ubuntu 24.04 LTS Complete Setup Guide

> **⚠️ Important Notice**: This guide is comprehensive but not necessarily in perfect sequential order. Please read through sections before executing commands. Some steps require specific ordering (especially Steps 1-3). When in doubt, test commands in a virtual machine first using for example GNOME Boxes.

> **🔒 Security Warning**: Commands with `sudo` grant administrative privileges and can modify your system. Always verify commands before executing them, especially from internet sources. This guide has been tested on Acer Swift 3 SF314 and ASUS TUF F15 (RTX 4070 Mobile) but hardware variations may require adjustments.

## Table of Contents

0. [Terminal Basics for Beginners](#0-terminal-basics-for-beginners)
1. [Installation and Initial Setup](#1-installation-and-initial-setup)
2. [Hardware-Specific Configuration](#2-hardware-specific-configuration)
3. [Desktop Customization](#3-desktop-customization)
4. [Package Management Setup](#4-package-management-setup)
5. [Terminal Enhancement](#5-terminal-enhancement)
6. [Essential Software](#6-essential-software)
7. [Development Environment](#7-development-environment)
8. [Gaming Setup](#8-gaming-setup)
9. [Advanced Tools](#9-advanced-tools)
10. [GRUB Dual Boot Configuration](#10-grub-dual-boot-configuration)

---

## 0. Terminal Basics for Beginners

### What is the Terminal?

The terminal (or command line) is a text-based interface to your computer. While it may seem intimidating at first, it's a powerful tool for system administration and development.

### Opening the Terminal

- **Keyboard shortcut**: `Ctrl + Alt + T`
- **Application menu**: Search for "Terminal" in Activities
- **DDTerm extension**: `Super + ~` (after installation)

### Basic Commands

```bash
# Navigate directories
pwd                 # Print current directory
ls                  # List files and folders
ls -la              # List all files with details
cd folder_name      # Change to folder
cd ..               # Go up one level
cd ~                # Go to home directory

# File operations
mkdir folder_name   # Create new folder
touch file.txt      # Create new file
cp source dest      # Copy file
mv old new          # Move/rename file
rm file             # Delete file (careful!)
rm -r folder        # Delete folder (very careful!)

# Viewing files
cat file.txt        # Display entire file
less file.txt       # View file page by page (q to quit)
head file.txt       # Show first 10 lines
tail file.txt       # Show last 10 lines

# System information
whoami              # Your username
uname -a            # System information
df -h               # Disk space
free -h             # Memory usage
top                 # Running processes (q to quit)
```

### Understanding `sudo`

**What it does**: `sudo` (Super User DO) grants temporary administrative privileges to execute commands that modify system files or settings.

**When to use it**:
- Installing/removing software
- Modifying system configuration
- Accessing protected files
- Managing system services

**Security considerations**:
- Always read the command before executing it
- Never blindly copy-paste commands with `sudo` from untrusted sources
- Ubuntu will ask for your password (you won't see characters as you type - this is normal)
- Your password grants full system access - be careful!

**Example**:
```bash
# Without sudo - permission denied
apt update
# Permission denied

# With sudo - works
sudo apt update
# [sudo] password for username: [type your password]
```

### Package Managers Quick Reference

```bash
# APT (system packages)
sudo apt update              # Update package lists
sudo apt upgrade             # Upgrade installed packages
sudo apt install package     # Install package
sudo apt remove package      # Remove package
sudo apt autoremove          # Remove unused dependencies

# Flatpak (universal apps)
flatpak search name          # Search for apps
flatpak install flathub app  # Install app
flatpak update               # Update all Flatpak apps
flatpak list                 # List installed apps

# Snap (pre-installed apps)
sudo snap refresh            # Update Snap packages
snap list                    # List installed snaps
```

### Getting Help

```bash
command --help          # Show command help
man command            # Manual page (detailed)
apropos keyword        # Search for commands
```

### Tips for Beginners

1. **Tab completion**: Press `Tab` to autocomplete commands and file names
2. **Command history**: Use `↑` and `↓` arrows to navigate previous commands
3. **Copy/Paste**: `Ctrl + Shift + C` to copy, `Ctrl + Shift + V` to paste (note the Shift!)
4. **Cancel command**: `Ctrl + C` to stop a running command
5. **Clear screen**: `Ctrl + L` or type `clear`

---

## 1. Installation and Initial Setup

### Why Ubuntu 24.04 LTS?

Ubuntu 24.04 LTS (Long Term Support) is recommended because:
- Everything works out of the box
- Extensive community support and documentation
- Long-term stability and security updates (until 2029)
- Better compatibility than newer non-LTS versions

**References**:
- [Ubuntu 24.04 LTS Release Notes](https://ubuntu.com/blog/ubuntu-24-04-noble-numbat-released)
- [Ubuntu Desktop Guide](https://help.ubuntu.com/stable/ubuntu-help/index.html)

**Alternative considerations**:
- **Linux Mint**: Great alternative but currently X11-based (Wayland support coming ~2027)
- **Fedora Workstation**: Cutting-edge but requires more maintenance
- **EndeavourOS**: Arch-based, powerful but needs technical expertise

### Installation Process

1. **Download**: Get Ubuntu 24.04 LTS from [official website](https://ubuntu.com/download/desktop)
2. **Create bootable USB**: Use [Rufus](https://rufus.ie/) (Windows) or [Balena Etcher](https://etcher.balena.io/) (Linux/Mac/Windows)
3. **BIOS Setup**: 
   - Boot from USB (usually F2, F12, Del, or Esc to access BIOS - varies by manufacturer)
   - Disable Secure Boot temporarily
   - Set USB as first boot device
4. **Installation**:
   - Select "Try or Install Ubuntu"
   - Choose language and keyboard layout
   - Connect to WiFi
   - Select "Extended selection" for more pre-installed apps
   - Enable third-party repositories (important for codecs and drivers)
   - Choose installation type (dual boot or clean install)
   - Create user account and wait for installation

### Dual SSD Setup (Optional)

If you have two SSDs (common in laptops like ASUS TUF F15), you can optimize your setup:

**Recommended configuration**:
- **Primary SSD**: Operating system (Ubuntu + swap)
- **Secondary SSD**: `/home` directory for personal files and projects

**During installation**:
1. Select "Something else" in installation type
2. Create partitions:
   - **Primary SSD**: 
     - `/` (root) - ext4 - remaining space
     - `swap` - swap area - 8-16GB (equal to RAM)
   - **Secondary SSD**:
     - `/home` - ext4 - entire disk

**Post-installation mounting** (if not done during install):
```bash
# Identify your second SSD
lsblk

# Create mount point
sudo mkdir -p /mnt/second-ssd

# Get UUID of the partition
sudo blkid

# Edit fstab for automatic mounting
sudo nano /etc/fstab

# Add this line (replace UUID with yours):
# UUID=your-uuid-here /mnt/second-ssd ext4 defaults 0 2

# Test the mount
sudo mount -a
```

**Reference**: [Ubuntu Partitioning Guide](https://help.ubuntu.com/community/PartitioningSchemes)

### Post-Installation Setup

**⚠️ Follow this order carefully:**

#### 1. Ubuntu Pro Registration (Free for Personal Use)
- Open Firefox and register at [Ubuntu Pro](https://ubuntu.com/pro)
- Enter the provided code to enable Livepatch (receive security updates without rebooting)
- **Reference**: [Ubuntu Pro Documentation](https://ubuntu.com/security/livepatch)

#### 2. System Updates
```bash
# Update package lists and upgrade system
sudo apt update && sudo apt upgrade -y

# Update snap packages
sudo snap refresh

# Reboot to apply updates (especially kernel updates)
sudo reboot

# Clean up after reboot
sudo apt autoremove -y
sudo apt autoclean
```

**Why reboot?**: Kernel updates require a restart to take effect. Check if reboot is needed:
```bash
[ -f /var/run/reboot-required ] && cat /var/run/reboot-required
```

#### 3. Re-enable Secure Boot (Recommended)
- Enter BIOS and re-enable Secure Boot after first reboot
- This enhances system security against boot-level malware
- **Reference**: [Secure Boot for Linux](https://wiki.ubuntu.com/UEFI/SecureBoot)

#### 4. Install Timeshift (Essential Backup Tool)
```bash
sudo apt install timeshift -y
```

**Configuration**:
- Launch Timeshift from applications menu
- Select RSYNC backup type
- Choose backup location (preferably external drive or separate partition)
- Configure schedule: 2 daily, 1 weekly, 3 monthly snapshots
- Create your first system snapshot immediately

**Why Timeshift?**: It's a system restore tool similar to Windows System Restore, saving you from potential disasters.

**Reference**: [Timeshift GitHub](https://github.com/linuxmint/timeshift)

#### 5. Account Integration (Optional)
- Go to Settings → Online Accounts
- Add Google/Microsoft accounts for calendar, contacts, and cloud storage access
- Firefox Sync can be configured separately in Firefox settings

---

## 2. Hardware-Specific Configuration

### NVIDIA GPU Setup (for laptops like ASUS TUF F15)

#### Driver Installation

**Check your GPU**:
```bash
lspci | grep -i nvidia
```

**Option 1: Ubuntu's Driver Manager (Recommended for beginners)**
```bash
# Launch graphical driver manager
software-properties-gtk --open-tab=4
```
- Select recommended proprietary driver (usually latest)
- Apply changes and reboot

**Option 2: Command Line Installation**
```bash
# Add graphics drivers PPA for latest drivers
sudo add-apt-repository ppa:graphics-drivers/ppa -y
sudo apt update

# Install recommended driver (usually nvidia-driver-XXX where XXX is version)
sudo ubuntu-drivers install

# Or install specific version
sudo apt install nvidia-driver-565 -y

# Reboot to load driver
sudo reboot

# Verify installation
nvidia-smi
```

**Expected output of `nvidia-smi`**:
```
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 565.xx.xx    Driver Version: 565.xx.xx    CUDA Version: 12.x    |
|-------------------------------+----------------------+----------------------+
```

**References**:
- [Ubuntu NVIDIA Drivers Guide](https://ubuntu.com/server/docs/nvidia-drivers-installation)
- [NVIDIA Linux Driver Documentation](https://download.nvidia.com/XFree86/Linux-x86_64/565.57.01/README/)

#### CUDA Toolkit Installation

**CUDA** is required for GPU-accelerated computing (AI/ML, scientific computing, video editing).

```bash
# Install CUDA toolkit
sudo apt install nvidia-cuda-toolkit -y

# Verify CUDA installation
nvcc --version

# Alternative: Install specific CUDA version from NVIDIA
# Download from: https://developer.nvidia.com/cuda-downloads
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt update
sudo apt install cuda-toolkit-12-6 -y
```

**Add CUDA to PATH** (add to `~/.zshrc` or `~/.bashrc`):
```bash
export PATH=/usr/local/cuda/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH
```

**Test CUDA**:
```bash
# Compile and run CUDA sample
cd /usr/local/cuda/samples/1_Utilities/deviceQuery
sudo make
./deviceQuery
```

**References**:
- [CUDA Installation Guide for Linux](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/)
- [CUDA Toolkit Documentation](https://developer.nvidia.com/cuda-toolkit)

#### NVIDIA Optimus/Prime (Hybrid Graphics)

For laptops with both Intel/AMD integrated graphics and NVIDIA discrete GPU:

```bash
# Install prime-select utility (usually pre-installed)
sudo apt install nvidia-prime -y

# Check current mode
prime-select query

# Switch modes:
sudo prime-select nvidia    # Performance mode (NVIDIA GPU always on)
sudo prime-select intel     # Power saving (Intel/AMD iGPU)
sudo prime-select on-demand # Hybrid mode (recommended)

# Reboot after switching
sudo reboot
```

**On-demand mode usage**:
```bash
# Run specific application with NVIDIA GPU
prime-run application_name

# Example:
prime-run steam
prime-run blender
```

**Reference**: [Ubuntu NVIDIA Prime Documentation](https://wiki.ubuntu.com/X/Config/HybridGraphics)

### ASUS Laptop Specific Configuration

#### ASUS Control Software

For controlling fan curves, RGB lighting, and performance profiles on ASUS TUF/ROG laptops:

```bash
# Install asusctl (for ASUS ROG/TUF control)
sudo apt install asusctl -y

# Install supergfxctl (for GPU switching on ASUS)
sudo apt install supergfxctl -y

# Enable services
sudo systemctl enable --now power-profiles-daemon.service
sudo systemctl enable --now supergfxd

# Reboot
sudo reboot
```

**GUI application**:
```bash
# Install ROG Control Center GUI
flatpak install flathub com.github.flxzt.rog-control-center
```

**Basic commands**:
```bash
# Check battery charge limit (preserves battery health)
asusctl -c

# Set charge limit to 80% (recommended for plugged-in use)
asusctl -c 80

# Fan profiles
asusctl profile -l    # List profiles
asusctl profile -P balanced  # Set balanced profile
asusctl profile -P performance  # Performance mode
asusctl profile -P quiet  # Quiet mode

# Anime Matrix control (if available)
asusctl anime -e   # Enable
asusctl anime -d   # Disable
```

**References**:
- [asusctl GitHub](https://github.com/flukejones/asusctl)
- [ROG Control Center](https://gitlab.com/asus-linux/rog-control-center)

#### Additional ASUS Optimizations

```bash
# Install TLP for advanced power management (optional)
# Note: May conflict with asusctl - use one or the other
sudo apt install tlp tlp-rdw -y
sudo systemctl enable tlp
sudo systemctl start tlp

# Check TLP status
sudo tlp-stat -s
```

**Reference**: [TLP Documentation](https://linrunner.de/tlp/)

### Laptop-Specific Power Management

```bash
# Install laptop-mode-tools for better battery life
sudo apt install laptop-mode-tools -y

# Install powertop for power consumption analysis
sudo apt install powertop -y

# Run powertop calibration (takes ~15 minutes, on battery)
sudo powertop --calibrate

# Apply powertop auto-tune (apply power-saving tweaks)
sudo powertop --auto-tune
```

**Make powertop auto-tune permanent**:
```bash
# Create systemd service
sudo nano /etc/systemd/system/powertop.service
```

Add this content:
```ini
[Unit]
Description=Powertop tunings

[Service]
Type=oneshot
ExecStart=/usr/sbin/powertop --auto-tune

[Install]
WantedBy=multi-user.target
```

```bash
# Enable service
sudo systemctl enable powertop.service
sudo systemctl start powertop.service
```

**Reference**: [Powertop Documentation](https://github.com/fenrus75/powertop)

---

## 3. Desktop Customization

### GNOME Tweaks
```bash
sudo apt install gnome-tweaks -y
```

**Key Settings**:
- **Windows** → **Focus on Hover**: Enable for mouse-over window activation
- **Top Bar** → Customize clock, calendar, and indicators
- **Appearance** → Change themes and icons

**Reference**: [GNOME Tweaks Documentation](https://wiki.gnome.org/Apps/Tweaks)

### GNOME Extensions
```bash
sudo apt install gnome-shell-extension-manager -y
```

**Disable default Ubuntu features** (Settings → Ubuntu Desktop):
- Disable "Window Tiling" (third option) if you prefer traditional window behavior

**Recommended Extensions** (install via Extension Manager):
- **Disable Ubuntu Dock**: For cleaner overview mode (use search instead)
- **GSConnect**: Android device integration (file sharing, notifications, SMS)
- **Clipboard Indicator**: Clipboard history with `Super + W`
- **Removable Drive Menu**: Quick access to USB drives and external disks
- **DDTerm**: Drop-down terminal with `Super + ~`
- **Top Bar Organizer**: Customize top bar layout and remove unwanted icons

**References**:
- [GNOME Extensions](https://extensions.gnome.org/)
- [GSConnect GitHub](https://github.com/GSConnect/gnome-shell-extension-gsconnect)

### Firefox Customization

#### Container Tabs
One of the most powerful Firefox features for managing multiple accounts:

1. Right-click the + button → **Manage Containers**
2. Create custom containers (e.g., "Personal", "Work", "University", "Shopping")
3. Open links in specific containers
4. Each container has separate cookies and sessions

**Use cases**:
- Multiple email accounts (Gmail personal + work)
- Separate social media accounts
- Isolate shopping sites for privacy
- Development vs production environments

**Installation**: Containers are built-in, but you can enhance them:
- Install "Multi-Account Containers" extension for better management
- Install "Temporary Containers" for automatic isolation

**References**:
- [Firefox Containers Guide](https://support.mozilla.org/en-US/kb/containers)
- [Multi-Account Containers](https://addons.mozilla.org/en-US/firefox/addon/multi-account-containers/)

---

## 4. Package Management Setup

### Philosophy

This guide prioritizes **Flatpak** over Snap for:
- Better performance and startup times
- More up-to-date applications
- Better desktop integration
- Active community support

**Installation sources in order of preference**:
1. **Flatpak** ([Flathub](https://flathub.org/)) - Universal and up-to-date
2. **APT** (official repositories) - System integration
3. **Direct downloads** - For specific software like VS Code
4. **Snap** - Only for pre-installed apps (Firefox, Ubuntu App Center)

**References**:
- [Flatpak Documentation](https://docs.flatpak.org/)
- [Flathub](https://flathub.org/)
- [Ubuntu APT Guide](https://ubuntu.com/server/docs/package-management)

### Install Flatpak

```bash
# Install Flatpak package manager
sudo apt install flatpak -y

# Add GNOME Software integration
sudo apt install gnome-software-plugin-flatpak -y

# Add Flathub repository
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo

# Reboot for complete integration
sudo reboot
```

**Update Flatpak apps**:
```bash
flatpak update
```

### Essential System Components

```bash
# Multimedia codecs and proprietary drivers
sudo apt install ubuntu-restricted-extras -y

# Additional fonts for better compatibility with Microsoft Office documents
sudo apt install fonts-crosextra-caladea fonts-crosextra-carlito -y

# Video/audio thumbnails in file manager
sudo apt install ffmpegthumbnailer -y

# Archive format support
sudo apt install unrar p7zip-full p7zip-rar -y
```

**Reference**: [Ubuntu Restricted Extras](https://help.ubuntu.com/community/RestrictedFormats)

---

## 5. Terminal Enhancement

### Prerequisites

```bash
sudo apt install curl git -y
```

### Zsh Setup

Zsh offers better autocompletion, theming, and plugin support than Bash.

```bash
# Install Zsh shell
sudo apt update
sudo apt install zsh -y

# Install Oh My Zsh framework
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

**References**:
- [Zsh Documentation](https://zsh.sourceforge.io/)
- [Oh My Zsh](https://ohmyz.sh/)

### Zsh Plugins

```bash
# Autosuggestions plugin (fish-like autosuggestions)
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

# Syntax highlighting plugin
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

# Edit configuration
nano ~/.zshrc
```

**In `~/.zshrc`**, find the line with `plugins=(git)` and change to:
```bash
plugins=(
  git
  zsh-autosuggestions
  zsh-syntax-highlighting
  sudo           # Press Esc twice to add/remove sudo
  colored-man-pages
  command-not-found
)
```

```bash
# Apply changes
source ~/.zshrc

# Set Zsh as default shell
chsh -s $(which zsh)

# Restart system to see effects everywhere
sudo reboot
```

### Additional Terminal Tools

```bash
# Better cat command with syntax highlighting
sudo apt install bat -y
echo "alias bat='batcat'" >> ~/.zshrc

# Fuzzy file finder (Ctrl+R for history, Ctrl+T for files)
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
~/.fzf/install

# System monitoring tools
sudo apt install htop btop -y

# Modern text editor
sudo apt install vim neovim -y

# Disk usage analyzer (NCurses-based)
sudo apt install ncdu -y

# Terminal file manager
sudo apt install nnn -y

# Modern alternatives to common commands
sudo apt install ripgrep fd-find exa -y
echo "alias rg='rg'" >> ~/.zshrc
echo "alias fd='fdfind'" >> ~/.zshrc
echo "alias ls='exa'" >> ~/.zshrc
echo "alias ll='exa -lah'" >> ~/.zshrc

# Fun tools
sudo apt install cowsay fortune neofetch -y

# Reload configuration
source ~/.zshrc
```

**References**:
- [bat GitHub](https://github.com/sharkdp/bat)
- [fzf GitHub](https://github.com/junegunn/fzf)
- [btop GitHub](https://github.com/aristocratos/btop)
- [exa GitHub](https://github.com/ogham/exa)

### Terminal Font

A proper programming font improves readability and supports ligatures.

1. Download [JetBrains Mono](https://fonts.google.com/specimen/JetBrains+Mono) or [Fira Code](https://github.com/tonsky/FiraCode)
2. Extract and copy to `~/.fonts/` (create if doesn't exist):
```bash
mkdir -p ~/.fonts
cp ~/Downloads/JetBrainsMono-*.ttf ~/.fonts/
fc-cache -f -v
```
3. Configure in GNOME Terminal: Preferences → Profiles → Font

**Reference**: [JetBrains Mono](https://www.jetbrains.com/lp/mono/)

---

## 6. Essential Software

### Media and Productivity

```bash
# Media player
sudo apt install vlc -y

# Screenshot and screen recording
flatpak install flathub org.flameshot.Flameshot -y

# Markdown editor
flatpak install flathub org.gnome.gitlab.somas.Apostrophe -y

# Project management
flatpak install flathub io.github.alainm23.planify -y

# Video conferencing
flatpak install flathub us.zoom.Zoom -y

# PDF editor
flatpak install flathub com.github.jeromerobert.pdfarranger -y

# Virtual machines
flatpak install flathub org.gnome.Boxes -y

# Image editing
flatpak install flathub org.gimp.GIMP -y

# Video editor
flatpak install flathub org.kde.kdenlive -y
```

**References**:
- [VLC Documentation](https://www.videolan.org/doc/)
- [GIMP Documentation](https://www.gimp.org/docs/)

### Cloud Storage

#### pCloud (AppImage)
Download from [pCloud website](https://www.pcloud.com/download-free-online-cloud-file-storage.html)

See [AppImage section](#appimage-support) for setup instructions.

#### Alternative Cloud Storage

```bash
# Dropbox
flatpak install flathub com.dropbox.Client -y

# Nextcloud client
sudo apt install nextcloud-desktop -y

# Google Drive integration (via GNOME Online Accounts)
# Go to Settings → Online Accounts → Google
```

---

## 7. Development Environment

### Essential Build Tools

**CRITICAL**: Required for compiling software from source.

```bash
# Compiler and development tools
sudo apt install build-essential -y

# Additional development libraries
sudo apt install libssl-dev libffi-dev libncurses5-dev zlib1g-dev \
                 libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm \
                 libncursesw5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev \
                 libffi-dev liblzma-dev -y
```

**Reference**: [Ubuntu Build Essentials](https://packages.ubuntu.com/noble/build-essential)

### VS Code (Official Microsoft Repository)

```bash
# Add Microsoft GPG key
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
sudo install -D -o root -g root -m 644 packages.microsoft.gpg /etc/apt/keyrings/packages.microsoft.gpg

# Add repository
echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" | sudo tee /etc/apt/sources.list.d/vscode.list > /dev/null

# Clean up and install
rm -f packages.microsoft.gpg
sudo apt install apt-transport-https -y
sudo apt update
sudo apt install code -y
```

**Reference**: [VS Code on Linux](https://code.visualstudio.com/docs/setup/linux)

### Python Environment Management

#### UV - Modern Python Package Installer

UV is a blazingly fast Python package installer and resolver, written in Rust. It's significantly faster than pip and conda.

```bash
# Install UV
curl -LsSf https://astral.sh/uv/install.sh | sh

# Add to PATH (add to ~/.zshrc or ~/.bashrc)
echo 'export PATH="$HOME/.cargo/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc

# Verify installation
uv --version

# Basic usage
uv venv                    # Create virtual environment
source .venv/bin/activate  # Activate environment
uv pip install package     # Install package (much faster than pip)
uv pip compile requirements.in  # Compile requirements
```

**UV advantages**:
- 10-100x faster than pip
- Better dependency resolution
- Drop-in replacement for pip
- Compatible with existing requirements.txt

**References**:
- [UV Documentation](https://github.com/astral-sh/uv)
- [UV Announcement](https://astral.sh/blog/uv)

#### Miniconda (Alternative Python Environment)

For data science and complex dependency management:

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

**Basic conda usage**:
```bash
# Create environment
conda create -n myenv python=3.11

# Activate environment
conda activate myenv

# Install packages
conda install numpy pandas matplotlib

# Deactivate
conda deactivate
```

**Reference**: [Conda Documentation](https://docs.conda.io/en/latest/)

#### Virtual Environment Comparison

| Tool | Best For | Speed | Pros | Cons |
|------|----------|-------|------|------|
| **UV** | Modern Python development | ⚡⚡⚡ | Extremely fast, simple | Newer tool |
| **venv** | Simple projects | ⚡ | Built-in, no dependencies | Basic features |
| **Conda** | Data science, complex deps | ⚡ | Cross-platform packages | Slower, larger |
| **Poetry** | Package development | ⚡⚡ | Good dependency management | Learning curve |

### Text Editors

```bash
# Modern Vim (install from official sources, not apt for latest version)
sudo apt install neovim -y

# Configure Neovim (optional, for better experience)
# Install vim-plug
sh -c 'curl -fLo "${XDG_DATA_HOME:-$HOME/.local/share}"/nvim/site/autoload/plug.vim --create-dirs \
       https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim'
```

**Reference**: [Neovim Documentation](https://neovim.io/doc/)

---

## 8. Gaming Setup

### Steam Installation (Official Method)

**⚠️ Note**: Steam through Flatpak has limited performance and game compatibility. Use official repository method.

#### Step 1: Download GPG Key
Visit [Steam Repository](https://repo.steampowered.com/steam/) and download `steam.gpg`

#### Step 2: Install Steam

```bash
# Move GPG key to system location
sudo cp ~/Downloads/steam.gpg /usr/share/keyrings/
rm ~/Downloads/steam.gpg

# Add Steam repository
sudo tee /etc/apt/sources.list.d/steam-stable.list <<'EOF'
deb [arch=amd64,i386 signed-by=/usr/share/keyrings/steam.gpg] https://repo.steampowered.com/steam/ stable steam
deb-src [arch=amd64,i386 signed-by=/usr/share/keyrings/steam.gpg] https://repo.steampowered.com/steam/ stable steam
EOF

# Enable 32-bit architecture (required for many games)
sudo dpkg --add-architecture i386

# Install Steam and dependencies
sudo apt update
sudo apt install \
  libgl1-mesa-dri:amd64 \
  libgl1-mesa-dri:i386 \
  libgl1:amd64 \
  libgl1:i386 \
  steam-launcher -y
```

**References**:
- [Steam for Linux Documentation](https://help.steampowered.com/en/faqs/view/0689-74B9-92AC-18BE)
- [Valve's Linux Gaming Guide](https://www.gamingonlinux.com/)

### Proton GE (Enhanced Windows Compatibility)

Proton GE (GloriousEggroll) is a custom version of Proton with additional patches, codec support, and game-specific fixes not included in the official Valve Proton.

**Why use Proton GE?**:
- Better compatibility with some Windows games
- Latest Wine patches and improvements
- Additional codecs for video cutscenes
- Game-specific fixes and optimizations

#### Step 1: Download Proton GE
Get the latest version from [GitHub Releases](https://github.com/GloriousEggroll/proton-ge-custom/releases)

#### Step 2: Install Proton GE

```bash
# Navigate to Downloads
cd ~/Downloads

# Extract downloaded file (replace X.X with your version)
tar -xf GE-Proton*.tar.gz
rm GE-Proton*.tar.gz

# Create Steam compatibility tools directory if it doesn't exist
mkdir -p ~/.steam/root/compatibilitytools.d

# Move Proton GE to Steam directory
mv GE-Proton* ~/.steam/root/compatibilitytools.d/

# Verify installation
ls ~/.steam/root/compatibilitytools.d/
```

#### Step 3: Configure in Steam

1. Restart Steam if it's running
2. Go to **Steam** → **Settings** → **Compatibility**
3. Enable "Enable Steam Play for all other titles"
4. Select **GE-Proton** from the dropdown menu
5. For individual games: Right-click game → **Properties** → **Compatibility** → Force the use of a specific Steam Play compatibility tool → Select GE-Proton version

**Update Proton GE**:
```bash
# Download new version
cd ~/Downloads
tar -xf GE-Proton*.tar.gz
rm GE-Proton*.tar.gz

# Remove old version (optional)
rm -rf ~/.steam/root/compatibilitytools.d/GE-Proton*

# Move new version
mv GE-Proton* ~/.steam/root/compatibilitytools.d/

# Restart Steam
```

**References**:
- [Proton GE GitHub](https://github.com/GloriousEggroll/proton-ge-custom)
- [ProtonDB](https://www.protondb.com/) - Game compatibility database

### Gaming Performance Optimization (NVIDIA)

```bash
# Install GameMode for automatic performance optimizations
sudo apt install gamemode -y

# Install MangoHud for performance overlay
sudo apt install mangohud -y

# Use GameMode with Steam games:
# Right-click game → Properties → Launch Options
# Add: gamemoderun %command%

# Use MangoHud for FPS counter:
# Add to launch options: mangohud %command%

# Combine both:
# gamemoderun mangohud %command%
```

**NVIDIA-specific optimizations**:
```bash
# Force performance mode for gaming
sudo nvidia-settings

# In NVIDIA Settings:
# PowerMizer → Prefer Maximum Performance
# OR use command line:
sudo nvidia-settings -a "[gpu:0]/GpuPowerMizerMode=1"

# Make it persistent (add to startup applications)
nvidia-settings -a "[gpu:0]/GpuPowerMizerMode=1"
```

**References**:
- [GameMode GitHub](https://github.com/FeralInteractive/gamemode)
- [MangoHud GitHub](https://github.com/flightlessmango/MangoHud)

### Lutris (Non-Steam Games)

Lutris is a gaming platform for installing and managing games from various sources (Epic, GOG, Battle.net, etc.).

```bash
# Add Lutris PPA
sudo add-apt-repository ppa:lutris-team/lutris -y
sudo apt update

# Install Lutris
sudo apt install lutris -y

# Install Wine dependencies for better compatibility
sudo apt install wine64 wine32 -y

# Install additional libraries for gaming
sudo apt install libvulkan1 libvulkan1:i386 -y
```

**References**:
- [Lutris Website](https://lutris.net/)
- [Lutris Documentation](https://github.com/lutris/lutris/wiki)

---

## 9. Advanced Tools

### AppImage Support

AppImages are portable applications that run on any Linux distribution without installation.

```bash
# CRITICAL: Install libfuse2 (NOT libfuse3 or fuse2/3)
# libfuse2 is required for AppImage compatibility
sudo apt install libfuse2 -y

# AppImage manager with desktop integration
flatpak install flathub it.mijorus.gearlever -y
```

**Using AppImages**:
1. Download AppImage files (e.g., from official websites)
2. Make executable: `chmod +x filename.AppImage`
3. Run directly: `./filename.AppImage`
4. **OR** Open with Gear Lever for better integration:
   - Launch Gear Lever
   - Click "Unlock" on the AppImage
   - Click "Add to menu" for desktop integration
   - Access from applications menu like any other app

**Managing AppImages**:
```bash
# Manual method
chmod +x ~/Downloads/App.AppImage
mv ~/Downloads/App.AppImage ~/Applications/
~/Applications/App.AppImage

# Create desktop shortcut manually
nano ~/.local/share/applications/myapp.desktop
```

Example `.desktop` file:
```ini
[Desktop Entry]
Name=My Application
Exec=/home/username/Applications/App.AppImage
Icon=/home/username/Applications/app-icon.png
Type=Application
Categories=Utility;
```

**References**:
- [AppImage Documentation](https://docs.appimage.org/)
- [Gear Lever GitHub](https://github.com/mijorus/gearlever)

### Local AI with Llama.cpp

Run large language models locally on your machine for privacy and offline use.

**System requirements**:
- **CPU-only**: Any modern CPU (slower)
- **NVIDIA GPU**: RTX 3060 or better (recommended)
- **RAM**: 16GB minimum, 32GB recommended
- **Storage**: 4-20GB per model

#### Install Llama.cpp

```bash
# Clone repository
cd ~
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp

# Install build dependencies
sudo apt install cmake libcurl4-openssl-dev -y

# Build with CUDA support (NVIDIA GPU)
mkdir build && cd build
cmake .. -DGGML_CUDA=ON
cmake --build . --config Release -j$(nproc)

# OR build CPU-only version
mkdir build && cd build
cmake ..
cmake --build . --config Release -j$(nproc)

# Test installation
./llama-cli --version
```

#### Download Models

```bash
# Create models directory
mkdir -p ~/llama.cpp/models

# Download a small model for testing (Llama 3.2 1B, ~1GB)
cd ~/llama.cpp/models
wget https://huggingface.co/bartowski/Llama-3.2-1B-Instruct-GGUF/resolve/main/Llama-3.2-1B-Instruct-Q4_K_M.gguf

# For better quality, download larger models:
# Llama 3.2 3B (~2GB): Good balance
# Llama 3.1 8B (~5GB): Better responses
# Llama 3.1 70B (~40GB): Requires high-end GPU
```

#### Run Llama.cpp

```bash
# Navigate to build directory
cd ~/llama.cpp/build

# Run interactive chat (adjust paths and parameters)
./llama-cli \
  -m ../models/Llama-3.2-1B-Instruct-Q4_K_M.gguf \
  -n 512 \
  -c 4096 \
  --color \
  -i \
  -r "User:" \
  --in-prefix " " \
  -ngl 35

# Parameters explained:
# -m: model file path
# -n: maximum tokens to generate
# -c: context size
# -ngl: GPU layers (higher = more GPU usage, faster)
# -i: interactive mode
```

**Web UI alternative** (easier to use):
```bash
# Install LM Studio (AppImage with GUI)
# Download from: https://lmstudio.ai/
# Or use text-generation-webui:

cd ~
git clone https://github.com/oobabooga/text-generation-webui
cd text-generation-webui
./start_linux.sh
```

**Performance tips**:
- Use quantized models (Q4_K_M is good balance)
- Increase `-ngl` parameter for NVIDIA GPUs (offload to GPU)
- More RAM = larger context windows
- SSD recommended for model loading

**References**:
- [Llama.cpp GitHub](https://github.com/ggerganov/llama.cpp)
- [Hugging Face Model Hub](https://huggingface.co/models)
- [Text Generation WebUI](https://github.com/oobabooga/text-generation-webui)

### Docker (Containerization)

```bash
# Install Docker
sudo apt install docker.io -y

# Add user to docker group (avoid using sudo)
sudo usermod -aG docker $USER

# Enable Docker service
sudo systemctl enable docker
sudo systemctl start docker

# Verify installation (logout/login required for group changes)
docker --version

# Test Docker
docker run hello-world
```

**Docker Compose**:
```bash
# Install Docker Compose
sudo apt install docker-compose -y

# Verify
docker-compose --version
```

**References**:
- [Docker Documentation](https://docs.docker.com/)
- [Docker Get Started](https://docs.docker.com/get-started/)

### Performance Monitoring

```bash
# Install system monitoring tools
sudo apt install neofetch htop btop nvtop -y

# nvtop for NVIDIA GPU monitoring (alternative to nvidia-smi)
# btop for beautiful system monitoring
# htop for process management
```

### System Maintenance Scripts

Create a maintenance script for regular system updates:

```bash
# Create script
nano ~/update-system.sh
```

Add this content:
```bash
#!/bin/bash

echo "=== System Update Script ==="
echo "Starting system maintenance..."

# Update APT packages
echo -e "\n[1/5] Updating APT packages..."
sudo apt update && sudo apt upgrade -y

# Update Flatpak
echo -e "\n[2/5] Updating Flatpak applications..."
flatpak update -y

# Update Snap
echo -e "\n[3/5] Updating Snap packages..."
sudo snap refresh

# Update Conda (if installed)
if command -v conda &> /dev/null; then
    echo -e "\n[4/5] Updating Conda..."
    conda update -n base -c defaults conda -y
fi

# Cleanup
echo -e "\n[5/5] Cleaning up..."
sudo apt autoremove -y
sudo apt autoclean
flatpak uninstall --unused -y

echo -e "\n=== Update Complete ==="
echo "Please reboot if kernel was updated."
```

```bash
# Make executable
chmod +x ~/update-system.sh

# Run the script
~/update-system.sh
```

---

## 10. GRUB Dual Boot Configuration

If you have dual boot with Windows, you can configure GRUB to remember your last choice and optionally hide the menu.

### Auto-select Last OS & Hide Menu

**⚠️ Backup current configuration first**:
```bash
sudo cp /etc/default/grub /etc/default/grub.backup
```

**Edit GRUB configuration**:
```bash
sudo nano /etc/default/grub
```

**Key settings to modify**:
```bash
# Remember last selected OS
GRUB_DEFAULT=saved
GRUB_SAVEDEFAULT=true

# Set timeout (seconds)
GRUB_TIMEOUT=5

# Hide menu (show with ESC)
GRUB_TIMEOUT_STYLE=hidden

# Don't show countdown
GRUB_HIDDEN_TIMEOUT_QUIET=false
```

**Complete example configuration**:
```bash
# If you change this file, run 'update-grub' afterwards to update
# /boot/grub/grub.cfg.

GRUB_DEFAULT=saved
GRUB_SAVEDEFAULT=true
GRUB_TIMEOUT=5
GRUB_TIMEOUT_STYLE=hidden
GRUB_HIDDEN_TIMEOUT_QUIET=false
GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
GRUB_CMDLINE_LINUX=""

# Uncomment to enable BadRAM filtering, modify to suit your needs
# GRUB_BADRAM="0x01234567,0xfefefefe,0x89abcdef,0xefefefef"

# Uncomment to disable graphical terminal (grub-pc only)
#GRUB_TERMINAL=console

# Uncomment to get a beep at grub start
#GRUB_INIT_TUNE="480 440 1"
```

**Apply changes**:
```bash
# Update GRUB configuration
sudo update-grub

# Reboot to test
sudo reboot
```

**GRUB menu controls**:
- **ESC**: Show hidden menu during boot
- **↑/↓**: Navigate between OS options
- **Enter**: Select and boot
- **E**: Edit boot parameters (advanced)
- **C**: GRUB command line (advanced)

### Advanced GRUB Customization

**Change default timeout**:
```bash
# Edit /etc/default/grub
GRUB_TIMEOUT=10  # Wait 10 seconds before auto-boot
```

**Always show menu** (disable hidden menu):
```bash
# Comment out or remove these lines:
# GRUB_TIMEOUT_STYLE=hidden
# GRUB_HIDDEN_TIMEOUT_QUIET=false

# Or set:
GRUB_TIMEOUT_STYLE=menu
```

**Change GRUB resolution**:
```bash
# Add to /etc/default/grub
GRUB_GFXMODE=1920x1080  # Change to your screen resolution
GRUB_GFXPAYLOAD_LINUX=keep
```

**Disable submenu** (show all entries in main menu):
```bash
# Add to /etc/default/grub
GRUB_DISABLE_SUBMENU=y
```

**Remember to update GRUB after any changes**:
```bash
sudo update-grub
```

### Restore Backup (if something goes wrong)

```bash
# Restore original configuration
sudo cp /etc/default/grub.backup /etc/default/grub

# Update GRUB
sudo update-grub

# Reboot
sudo reboot
```

### GUI Alternative - GRUB Customizer

For users who prefer graphical interface:

```bash
# Install GRUB Customizer
sudo add-apt-repository ppa:danielrichter2007/grub-customizer -y
sudo apt update
sudo apt install grub-customizer -y

# OR via Flatpak
flatpak install flathub com.github.grub-customizer.GrubCustomizer -y
```

**Features**:
- Visual menu editor
- Drag and drop to reorder entries
- Change themes and appearance
- Edit boot parameters
- Preview changes before applying

**⚠️ Warning**: Always create backups before making changes with GRUB Customizer.

**References**:
- [GNU GRUB Manual](https://www.gnu.org/software/grub/manual/grub/)
- [Ubuntu GRUB2 Guide](https://help.ubuntu.com/community/Grub2)
- [GRUB Customizer](https://launchpad.net/grub-customizer)

---

## Additional Tips and Troubleshooting

### Common Issues and Solutions

#### Issue: "No WiFi adapter found" after installation
```bash
# Install additional firmware
sudo apt install linux-firmware -y
sudo reboot
```

#### Issue: Screen tearing (NVIDIA)
```bash
# Enable Force Full Composition Pipeline
nvidia-settings
# Go to X Server Display Configuration → Advanced → Force Full Composition Pipeline
# Save to X Configuration File
```

#### Issue: Bluetooth not working
```bash
# Restart Bluetooth service
sudo systemctl restart bluetooth

# Check status
sudo systemctl status bluetooth

# If still not working, reinstall
sudo apt install --reinstall bluez -y
```

#### Issue: Slow boot time
```bash
# Check what's slowing boot
systemd-analyze blame

# Disable unnecessary services
sudo systemctl disable service-name
```

### Performance Tips

**❌ Avoid these popular but ineffective tweaks**:
- **preload**: Minimal impact on modern systems with SSD
- **tlp** (on ASUS laptops): Conflicts with asusctl
- **zram**: Already included in Ubuntu 24.04
- Aggressive swappiness tweaking: Default is optimized

**✅ Actually helpful optimizations**:
- Use SSD for system drive
- Enable TRIM for SSDs: `sudo systemctl enable fstrim.timer`
- Keep system updated
- Remove unnecessary startup applications
- Use lightweight alternatives to heavy apps

### Backup Strategy

**What to backup**:
1. `/home` directory (all personal files)
2. List of installed packages: `dpkg --get-selections > packages.txt`
3. Flatpak list: `flatpak list > flatpak-apps.txt`
4. Custom configurations in `/etc`
5. Timeshift system snapshots

**Backup tools**:
- **Timeshift**: System snapshots
- **Déjà Dup**: User file backup (pre-installed)
- **Rsync**: Command-line backup
- **Cloud**: Google Drive, Dropbox, pCloud

```bash
# Create full backup with rsync
rsync -aAXv --exclude={"/dev/*","/proc/*","/sys/*","/tmp/*","/run/*","/mnt/*","/media/*","/lost+found"} / /path/to/backup/
```

### Recommended Browser Extensions

**Firefox**:
- uBlock Origin (ad blocking)
- Bitwarden (password manager)
- Multi-Account Containers (already mentioned)
- Privacy Badger (tracking protection)
- Enhancer for YouTube

### System Monitoring Commands

```bash
# Disk usage
df -h                    # Disk space
du -sh *                 # Directory sizes
ncdu                     # Interactive disk analyzer

# Memory
free -h                  # RAM usage
vmstat                   # Virtual memory stats

# CPU
lscpu                    # CPU information
htop                     # Interactive process viewer

# GPU (NVIDIA)
nvidia-smi               # GPU stats
watch -n1 nvidia-smi     # Real-time monitoring

# Network
ip a                     # Network interfaces
ping google.com          # Test connectivity
speedtest-cli            # Internet speed test

# System
neofetch                 # System info (pretty)
uname -a                 # Kernel version
lsb_release -a           # Ubuntu version
```

---

## Resources and Community

### Official Documentation
- [Ubuntu Documentation](https://help.ubuntu.com/)
- [Ubuntu Community Help](https://help.ubuntu.com/community/CommunityHelpWiki)
- [Ask Ubuntu](https://askubuntu.com/) - Q&A site
- [Ubuntu Forums](https://ubuntuforums.org/)

### News and Updates
- [OMG! Ubuntu!](https://www.omgubuntu.co.uk/)
- [It's FOSS](https://itsfoss.com/)
- [Linux Unplugged Podcast](https://linuxunplugged.com/)
- [r/Ubuntu subreddit](https://www.reddit.com/r/Ubuntu/)

### Learning Resources
- [Linux Journey](https://linuxjourney.com/)
- [The Linux Command Line book](http://linuxcommand.org/tlcl.php) (free)
- [Linux Academy](https://linuxacademy.com/)
- [Ubuntu Tutorials](https://ubuntu.com/tutorials)

---

## Contributing and Support

### 🚧 Status: Actively Maintained

This guide is a **living document**, regularly updated and improved based on:
- User feedback and suggestions
- Ubuntu updates and changes
- New software and tools
- Community contributions

### How to Contribute

**Found an issue?**
- Check if commands work on your hardware
- Test in virtual machine if unsure
- Report what went wrong and your hardware specs

**Want to improve?**
- Submit corrections for outdated information
- Add missing sections or software
- Improve explanations and clarity
- Share your hardware-specific configurations

**Have questions?**
- Open an issue for clarification
- Share your setup and experiences
- Help others in discussions

### Tested Hardware

This guide has been tested on:
- **Acer Swift 3 SF314** (Intel integrated graphics)
- **ASUS TUF F15** (Intel + NVIDIA RTX 4070 Mobile)

Your mileage may vary with different hardware. Always test in virtual machine first if unsure.

### Disclaimer

⚠️ **Important**: 
- This guide is provided as-is for educational purposes
- Always verify commands before executing with `sudo`
- Test in virtual machine when in doubt
- Keep backups (Timeshift) before major changes
- Hardware variations may require adjustments
- Author is not responsible for any system damage

**Best practices**:
1. Read the entire section before starting
2. Understand what each command does
3. Create Timeshift snapshot before major changes
4. Test in VM if hardware is different
5. Keep backups of important data

---

## License and Credits

### License
This guide is provided under MIT License - free to use, modify, and distribute with attribution.

### Credits and Acknowledgments

- Ubuntu and Canonical Ltd.
- GNOME Project
- All open-source software developers mentioned
- Linux community for endless support and documentation
- Contributors who have improved this guide

### Version History

- **v1.0** (Initial): Basic Ubuntu setup guide
- **v2.0** (Current): Complete rewrite with hardware-specific configs, terminal basics, UV/Python, NVIDIA/CUDA, ASUS tools, and comprehensive references

**Last updated**: December 2025

---

## Quick Reference Card

### Essential Commands
```bash
# System
sudo apt update && sudo apt upgrade    # Update system
sudo reboot                            # Restart
sudo poweroff                          # Shutdown

# Package Management
sudo apt install package               # Install via APT
flatpak install flathub app           # Install via Flatpak
sudo snap install app                 # Install via Snap

# Files
ls -lah                               # List files
cd /path/to/dir                       # Change directory
cp source destination                 # Copy
mv old new                            # Move/rename
rm file                               # Delete file

# System Info
neofetch                              # Pretty system info
htop                                  # Process monitor
nvidia-smi                            # GPU info (NVIDIA)
df -h                                 # Disk space

# NVIDIA
nvidia-smi                            # GPU monitoring
prime-select query                    # Check GPU mode
prime-run app                         # Run with NVIDIA GPU

# ASUS Laptops
asusctl -c                            # Check battery limit
asusctl profile -l                    # List fan profiles
asusctl profile -P performance        # Performance mode

# Backup
sudo timeshift --create              # Create snapshot
sudo timeshift --restore             # Restore snapshot
```

### Keyboard Shortcuts
```
Ctrl + Alt + T          # Open terminal
Super + L               # Lock screen
Super + A               # Show applications
Super + S               # Quick settings
Alt + F2                # Run command
Ctrl + Shift + C/V      # Copy/paste in terminal
Ctrl + C                # Cancel command
Ctrl + L                # Clear terminal
```

---

**🎉 Congratulations!** You now have a fully configured Ubuntu 24.04 LTS system optimized for productivity, development, and gaming.

**Remember**: Linux is a journey of continuous learning. Don't be afraid to experiment (with backups!), ask questions, and explore the vast ecosystem of open-source software.

**Happy Linux-ing! 🐧**
