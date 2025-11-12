# WSL (Windows Subsystem for Linux) Terminal Guide

## Table of Contents

1. [What is WSL?](#what-is-wsl)
2. [Installation and Setup](#installation-and-setup)
3. [Opening the Terminal](#opening-the-terminal)
4. [Understanding the Shell](#understanding-the-shell)
5. [Basic Navigation](#basic-navigation)
6. [File Operations](#file-operations)
7. [Windows-Linux Integration](#windows-linux-integration)
8. [File Permissions](#file-permissions)
9. [Text Editing](#text-editing)
10. [Process Management](#process-management)
11. [Package Management](#package-management)
12. [WSL-Specific Features](#wsl-specific-features)
13. [Useful Tips](#useful-tips)
14. [Common Issues](#common-issues)

## What is WSL?

Windows Subsystem for Linux (WSL) allows you to run a Linux environment directly on Windows without the need for a virtual machine or dual-boot setup.

### WSL 1 vs WSL 2

- **WSL 1**: Translation layer, better cross-OS file system performance
- **WSL 2**: Full Linux kernel, better Linux app compatibility, faster performance

**Recommendation**: Use WSL 2 for most use cases.

## Installation and Setup

### Prerequisites

- Windows 10 version 2004+ (Build 19041+) or Windows 11

### Installation Methods

#### Method 1: Quick Install (Windows 11 or Windows 10 version 2004+)

```powershell
# Open PowerShell as Administrator
wsl --install
```

This installs WSL 2 with Ubuntu by default.

#### Method 2: Install Specific Distribution

```powershell
# List available distributions
wsl --list --online

# Install specific distro
wsl --install -d Ubuntu-22.04
wsl --install -d Debian
```

#### Post-Installation

1. Restart your computer
2. Launch Ubuntu (or your chosen distro) from Start Menu
3. Create a username and password (Linux credentials, not Windows)

### Update to WSL 2

```powershell
# Check WSL version
wsl -l -v

# Set WSL 2 as default
wsl --set-default-version 2

# Convert existing distro to WSL 2
wsl --set-version Ubuntu-22.04 2
```

## Opening the Terminal

### Method 1: Start Menu

- Search for your distribution name (e.g., "Ubuntu")
- Click to launch

### Method 2: Windows Terminal (Recommended)

1. Install from Microsoft Store: [Windows Terminal](https://aka.ms/terminal)
2. Open Windows Terminal
3. Select your WSL distribution from the dropdown

### Method 3: Command Line

```powershell
# From PowerShell or CMD
wsl

# Launch specific distro
wsl -d Ubuntu-22.04
```

### Method 4: VS Code

1. Install [VS Code](https://code.visualstudio.com/)
2. Install "Remote - WSL" extension
3. Open folder in WSL or connect to WSL

## Understanding the Shell

WSL typically uses **Bash** by default, though you can install and use **Zsh** or other shells.

### The Prompt

```
username@hostname:/mnt/c/Users/YourName$
```

- `username` - Your Linux username
- `hostname` - Your WSL distribution hostname
- `/mnt/c/Users/YourName` - Current directory
- `$` - Regular user (# for root)

## Basic Navigation

### Essential Commands

```bash
# Print current directory
pwd

# List files and directories
ls
ls -l          # Long format with details
ls -la         # Include hidden files
ls -lh         # Human-readable file sizes

# Change directory
cd /path/to/directory
cd ~           # Go to Linux home directory
cd ..          # Go up one directory
cd -           # Go to previous directory

# Create directory
mkdir newfolder
mkdir -p path/to/nested/folders
```

### Important Directories

```bash
# Linux filesystem
~                          # Your Linux home: /home/username
/                          # Linux root

# Windows filesystem (mounted)
/mnt/c/                    # Windows C: drive
/mnt/c/Users/YourName      # Your Windows user folder
/mnt/d/                    # Windows D: drive (if available)

# WSL-specific
/usr/bin                   # Linux binaries
/etc                       # Configuration files
```

## File Operations

### Creating and Viewing Files

```bash
# Create empty file
touch filename.txt

# View file contents
cat filename.txt
less filename.txt    # Page through file (q to quit)
head filename.txt    # First 10 lines
tail filename.txt    # Last 10 lines
tail -f logfile.log  # Follow file updates

# Open with Windows app
explorer.exe .       # Open current dir in Windows Explorer
notepad.exe file.txt # Open in Notepad
code file.txt        # Open in VS Code (if installed)
```

### Copying, Moving, and Deleting

```bash
# Copy files
cp source.txt destination.txt
cp -r source_dir/ dest_dir/    # Copy directory recursively

# Move or rename
mv oldname.txt newname.txt
mv file.txt /path/to/destination/

# Delete files
rm file.txt
rm -r directory/        # Delete directory recursively
rm -rf directory/       # Force delete (use carefully!)

# Find files
find /path -name "filename"
find . -name "*.txt"
```

## Windows-Linux Integration

### Accessing Windows Files from WSL

```bash
# Navigate to Windows drives
cd /mnt/c/Users/YourName/Documents
cd /mnt/c/projects

# Copy from Windows to Linux
cp /mnt/c/Users/YourName/file.txt ~/

# Best practice: Work in /mnt/c/ for Windows-WSL shared projects
cd /mnt/c/projects
```

### Accessing WSL Files from Windows

#### Method 1: File Explorer

- Open File Explorer
- Type in address bar: `\\wsl$\Ubuntu\home\username`
- Or navigate to Network → WSL

#### Method 2: Direct Path

```bash
# In WSL, open current directory in Explorer
explorer.exe .

# Copy WSL path to Windows clipboard
pwd | clip.exe
```

### Running Windows Commands from WSL

```bash
# Run Windows executables (add .exe)
notepad.exe
cmd.exe
powershell.exe
explorer.exe

# Run Windows commands with wsl command
ipconfig.exe
netstat.exe

# Open URLs
powershell.exe start https://google.com
```

### Running WSL Commands from Windows

```powershell
# From PowerShell or CMD
wsl ls -la
wsl pwd
wsl cat /etc/os-release

# Pipe between Windows and WSL
dir | wsl grep txt
wsl ls -la | findstr "txt"
```

## File Permissions

### Understanding Permissions

```bash
ls -l file.txt
# Output: -rwxr-xr-- 1 user group 1234 Jan 01 12:00 file.txt
```

### Changing Permissions

```bash
# Symbolic method
chmod u+x file.sh      # Add execute for user
chmod g-w file.txt     # Remove write for group

# Numeric method
chmod 755 script.sh    # rwxr-xr-x
chmod 644 file.txt     # rw-r--r--

# Change ownership
sudo chown user:user file.txt
```

### Windows File Permissions in WSL

Files on `/mnt/c/` (Windows drives) have different permission handling:

```bash
# Set default permissions in /etc/wsl.conf
sudo nano /etc/wsl.conf

# Add:
[automount]
options = "metadata,umask=22,fmask=11"
```

## Text Editing

### Command Line Editors

```bash
# Nano (beginner-friendly)
nano filename.txt
# Ctrl+O to save, Ctrl+X to exit

# Vim
vim filename.txt
# Press 'i' to insert, 'Esc' to exit insert mode
# Type ':wq' to save and quit

# VS Code (if installed)
code filename.txt
code .               # Open current directory
```

## Process Management

```bash
# View running processes
ps aux
top               # Interactive process viewer
htop              # Better viewer (install: sudo apt install htop)

# Find process
ps aux | grep processname

# Kill process
kill PID
kill -9 PID
killall processname

# Run in background
command &
nohup command &

# View background jobs
jobs
fg %1             # Foreground
bg %1             # Background
```

## Package Management

WSL distributions typically use their native package managers.

### Ubuntu/Debian (APT)

```bash
# Update package list
sudo apt update

# Upgrade packages
sudo apt upgrade

# Install package
sudo apt install package-name

# Remove package
sudo apt remove package-name

# Search for package
apt search keyword

# Common packages to install
sudo apt install git curl wget build-essential
```

### Other Distributions

```bash
# Fedora (DNF)
sudo dnf update
sudo dnf install package-name

# Arch (Pacman)
sudo pacman -Syu
sudo pacman -S package-name

# OpenSUSE (Zypper)
sudo zypper refresh
sudo zypper install package-name
```

## WSL-Specific Features

### WSL Commands (from PowerShell)

```powershell
# List installed distributions
wsl --list --verbose
wsl -l -v

# Set default distribution
wsl --set-default Ubuntu-22.04

# Terminate distribution
wsl --terminate Ubuntu-22.04

# Shutdown all WSL instances
wsl --shutdown

# Unregister (delete) a distribution
wsl --unregister Ubuntu-22.04

# Export distribution
wsl --export Ubuntu-22.04 D:\backup\ubuntu.tar

# Import distribution
wsl --import MyUbuntu D:\WSL\MyUbuntu D:\backup\ubuntu.tar

# Update WSL
wsl --update
```

### WSL Configuration

#### Global Config: `C:\Users\YourName\.wslconfig`

```ini
[wsl2]
memory=4GB
processors=2
swap=8GB
localhostForwarding=true
```

#### Per-Distro Config: `/etc/wsl.conf`

```ini
[automount]
enabled = true
root = /mnt/
options = "metadata,umask=22,fmask=11"

[network]
generateHosts = true
generateResolvConf = true

[interop]
enabled = true
appendWindowsPath = true

[user]
default = username
```

After editing, restart WSL:
```powershell
wsl --shutdown
```

### Networking

```bash
# WSL 2 uses virtual networking
# Access Windows localhost from WSL
curl http://localhost:8080

# Access WSL services from Windows
# Use localhost:port (auto-forwarded)
```

#### Port Forwarding Issues

If localhost forwarding doesn't work:

```bash
# Get WSL IP
ip addr | grep eth0

# Access from Windows using WSL IP
# Example: http://172.x.x.x:8080
```

### Systemd Support (WSL 2)

Enable systemd in `/etc/wsl.conf`:

```ini
[boot]
systemd=true
```

Then restart:
```powershell
wsl --shutdown
```

### GPU Support (WSL 2)

For CUDA, machine learning:

```bash
# Install NVIDIA drivers in Windows (not in WSL)
# Then install CUDA toolkit in WSL
sudo apt install nvidia-cuda-toolkit

# Verify
nvidia-smi
```

## Useful Tips

### Keyboard Shortcuts

- `Ctrl + C` - Cancel current command
- `Ctrl + D` - Exit terminal
- `Ctrl + L` - Clear screen
- `Ctrl + A` - Beginning of line
- `Ctrl + E` - End of line
- `Ctrl + R` - Search history
- `Tab` - Auto-complete
- `↑/↓` - Command history

### Aliases

Add to `~/.bashrc`:

```bash
# Aliases
alias ll='ls -lah'
alias ..='cd ..'
alias win='cd /mnt/c/Users/YourName'
alias projects='cd /mnt/c/projects'

# Reload bashrc
source ~/.bashrc
```

### Path Conversion

```bash
# Convert Windows path to WSL path
wslpath 'C:\Users\YourName\file.txt'
# Output: /mnt/c/Users/YourName/file.txt

# Convert WSL path to Windows path
wslpath -w /home/username/file.txt
# Output: \\wsl$\Ubuntu\home\username\file.txt
```

### Clipboard Integration

```bash
# Copy to Windows clipboard
echo "Hello" | clip.exe
cat file.txt | clip.exe

# Paste from Windows clipboard (PowerShell)
powershell.exe Get-Clipboard
```

### Performance Tips

```bash
# Store projects on Linux filesystem for better performance
# Bad:  /mnt/c/projects (Windows FS)
# Good: ~/projects (Linux FS)

# Or use Windows filesystem with native Windows tools
# and WSL for Linux-specific tasks
```

## Common Issues

### Slow File System Performance

**Issue**: Files on `/mnt/c/` are slow.

**Solution**:
- Store files in Linux filesystem (`~` directory) for better performance
- Or use Windows tools for Windows files

### Permission Issues

**Issue**: Can't modify Windows files.

**Solution**:
```bash
sudo nano /etc/wsl.conf
# Add:
[automount]
options = "metadata,umask=22,fmask=11"
```

### Network Issues

**Issue**: Can't connect to internet.

**Solution**:
```bash
# Regenerate resolv.conf
sudo rm /etc/resolv.conf
sudo bash -c 'echo "nameserver 8.8.8.8" > /etc/resolv.conf'
sudo bash -c 'echo "nameserver 8.8.4.4" >> /etc/resolv.conf'

# Or disable auto-generation
sudo nano /etc/wsl.conf
# Add:
[network]
generateResolvConf = false
```

### WSL 2 Not Starting

**Solution**:
```powershell
# Enable required Windows features
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart

# Restart computer
# Update WSL
wsl --update
```

### Can't Access Windows Files

**Solution**:
```bash
# Check if interop is enabled
cat /etc/wsl.conf

# Should have:
[interop]
enabled = true
```

### Memory Issues

**Solution**: Limit memory in `C:\Users\YourName\.wslconfig`:
```ini
[wsl2]
memory=4GB
```

### "Reference is not valid" Error

**Solution**:
```powershell
# Restart WSL
wsl --shutdown
wsl

# If persists, restart Windows
```

---

[← Back to Main Guide](./README.md)
