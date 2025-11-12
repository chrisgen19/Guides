# macOS Terminal Guide

## Table of Contents

1. [Opening the Terminal](#opening-the-terminal)
2. [Understanding the Shell](#understanding-the-shell)
3. [Basic Navigation](#basic-navigation)
4. [File Operations](#file-operations)
5. [File Permissions](#file-permissions)
6. [Text Editing](#text-editing)
7. [Process Management](#process-management)
8. [Package Management](#package-management)
9. [macOS-Specific Features](#macos-specific-features)
10. [Useful Tips](#useful-tips)
11. [Common Issues](#common-issues)

## Opening the Terminal

### Method 1: Spotlight Search

1. Press `Cmd + Space` to open Spotlight
2. Type "Terminal"
3. Press `Enter`

### Method 2: Finder

1. Open Finder
2. Go to `Applications` → `Utilities` → `Terminal`

### Method 3: Launchpad

1. Open Launchpad (F4 or pinch gesture)
2. Search for "Terminal" in the search bar

### Alternative: iTerm2

Many macOS users prefer [iTerm2](https://iterm2.com/), a more feature-rich terminal emulator.

## Understanding the Shell

### Default Shells

- **macOS Catalina (10.15) and later**: **Zsh** (Z Shell)
- **Earlier versions**: **Bash** (Bourne Again Shell)

### The Prompt

#### Zsh Prompt
```
computername:~ username%
```

#### Bash Prompt
```
computername:~ username$
```

- `computername` - Your Mac's name
- `~` - Current directory (~ means home directory)
- `%` or `$` - Regular user (# for root)

### Switching Shells

```bash
# Check current shell
echo $SHELL

# Switch to Bash
chsh -s /bin/bash

# Switch to Zsh
chsh -s /bin/zsh
```

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
ls -G          # Colorized output

# Change directory
cd /path/to/directory
cd ~           # Go to home directory
cd ..          # Go up one directory
cd -           # Go to previous directory

# Create directory
mkdir newfolder
mkdir -p path/to/nested/folders

# View directory structure
tree           # Install with Homebrew: brew install tree
```

### Important Directories

```bash
~              # Your home directory (/Users/username)
/Applications  # Installed applications
/Library       # System-wide library files
~/Library      # Your user library files
/System        # System files (don't modify!)
/usr/local     # User-installed programs (Homebrew default)
/opt/homebrew  # Homebrew on Apple Silicon Macs
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

# Quick Look from terminal
qlmanage -p file.pdf 2>/dev/null

# Open with default app
open file.txt
open -a "TextEdit" file.txt
open .            # Open current directory in Finder
```

### Copying, Moving, and Deleting

```bash
# Copy files
cp source.txt destination.txt
cp -R source_dir/ dest_dir/    # Copy directory recursively

# Move or rename
mv oldname.txt newname.txt
mv file.txt /path/to/destination/

# Delete files
rm file.txt
rm -r directory/        # Delete directory recursively
rm -rf directory/       # Force delete (use carefully!)

# Move to Trash (safer than rm)
# Install with: brew install trash
trash file.txt

# Find files
find /path -name "filename"
find . -name "*.txt"
mdfind "searchterm"     # Spotlight search from terminal
```

## File Permissions

### Understanding Permissions

```bash
ls -l file.txt
# Output: -rwxr-xr-- 1 user group 1234 Jan 01 12:00 file.txt
#         ^^^^^^^^^ permissions
#         - = file, d = directory
#         rwx = read, write, execute (owner)
#         r-x = read, execute (group)
#         r-- = read only (others)
```

### Changing Permissions

```bash
# Symbolic method
chmod u+x file.sh      # Add execute for user
chmod g-w file.txt     # Remove write for group
chmod o+r file.txt     # Add read for others

# Numeric method
chmod 755 script.sh    # rwxr-xr-x
chmod 644 file.txt     # rw-r--r--
chmod 600 secret.txt   # rw-------

# Change ownership
sudo chown user:staff file.txt
sudo chown -R user:staff directory/
```

### Extended Attributes

macOS uses extended attributes:

```bash
# View extended attributes
ls -l@
xattr -l file.txt

# Remove extended attributes
xattr -c file.txt

# Remove quarantine attribute (for downloaded files)
xattr -d com.apple.quarantine file.app
```

## Text Editing

### Command Line Editors

```bash
# Nano (beginner-friendly)
nano filename.txt
# Ctrl+O to save, Ctrl+X to exit

# Vim (comes pre-installed)
vim filename.txt
# Press 'i' to insert, 'Esc' to exit insert mode
# Type ':wq' to save and quit, ':q!' to quit without saving

# Open in TextEdit
open -e filename.txt
```

## Process Management

```bash
# View running processes
ps aux
top               # Interactive process viewer
htop              # Better viewer (install: brew install htop)

# Activity Monitor from terminal
open -a "Activity Monitor"

# Find process by name
ps aux | grep processname
pgrep processname

# Kill process
kill PID          # Graceful termination
kill -9 PID       # Force kill
killall processname
pkill processname

# Run in background
command &
nohup command &   # Keep running after logout

# View background jobs
jobs
fg %1             # Bring job 1 to foreground
bg %1             # Resume job 1 in background
```

## Package Management

### Homebrew (Recommended)

Homebrew is the de facto package manager for macOS.

#### Installation

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

#### Basic Usage

```bash
# Update Homebrew
brew update

# Upgrade all packages
brew upgrade

# Install package
brew install package-name

# Install GUI application
brew install --cask google-chrome

# Uninstall package
brew uninstall package-name

# Search for package
brew search keyword

# List installed packages
brew list

# Show package info
brew info package-name

# Clean up old versions
brew cleanup
```

### MacPorts (Alternative)

```bash
# Update MacPorts
sudo port selfupdate

# Install package
sudo port install package-name

# Uninstall package
sudo port uninstall package-name

# Search for package
port search keyword
```

## macOS-Specific Features

### System Commands

```bash
# System information
sw_vers            # macOS version
system_profiler    # Detailed system info
uname -a           # Kernel info

# Disk usage
df -h              # Filesystem usage
diskutil list      # List all disks
du -sh *           # Directory sizes

# Memory usage
top -l 1 | grep PhysMem
vm_stat

# Battery status (laptops)
pmset -g batt

# Network
ifconfig           # Network interfaces
networksetup -listallhardwareports
scutil --dns       # DNS configuration
```

### System Preferences via Terminal

```bash
# Show hidden files in Finder
defaults write com.apple.finder AppleShowAllFiles YES
killall Finder

# Hide hidden files
defaults write com.apple.finder AppleShowAllFiles NO
killall Finder

# Disable screenshot shadows
defaults write com.apple.screencapture disable-shadow -bool true
killall SystemUIServer

# Change screenshot format
defaults write com.apple.screencapture type jpg

# Show Library folder
chflags nohidden ~/Library

# Disable Dashboard
defaults write com.apple.dashboard mcx-disabled -bool true
```

### Spotlight Control

```bash
# Rebuild Spotlight index
sudo mdutil -E /

# Disable Spotlight indexing
sudo mdutil -a -i off

# Enable Spotlight indexing
sudo mdutil -a -i on
```

### Clipboard Commands

```bash
# Copy to clipboard
echo "Hello" | pbcopy
cat file.txt | pbcopy

# Paste from clipboard
pbpaste
pbpaste > file.txt
```

### Screen Capture

```bash
# Capture entire screen
screencapture screen.png

# Capture with delay (5 seconds)
screencapture -T 5 screen.png

# Interactive selection
screencapture -i selection.png
```

### System Maintenance

```bash
# Flush DNS cache
sudo dscacheutil -flushcache; sudo killall -HUP mDNSResponder

# Purge memory
sudo purge

# Verify disk
diskutil verifyVolume /

# Repair disk permissions (older macOS)
diskutil repairPermissions /
```

## Useful Tips

### Keyboard Shortcuts

- `Cmd + K` - Clear terminal screen
- `Ctrl + C` - Cancel current command
- `Ctrl + D` - Exit terminal / EOF
- `Ctrl + A` - Move to beginning of line
- `Ctrl + E` - Move to end of line
- `Ctrl + U` - Delete from cursor to beginning
- `Ctrl + K` - Delete from cursor to end
- `Ctrl + R` - Search command history
- `Tab` - Auto-complete commands and paths
- `↑/↓` - Navigate command history
- `Cmd + T` - New tab
- `Cmd + N` - New window

### Piping and Redirection

```bash
# Redirect output to file
command > output.txt         # Overwrite
command >> output.txt        # Append

# Redirect errors
command 2> errors.txt
command &> all_output.txt    # Both stdout and stderr

# Pipe output to another command
ls -l | grep ".txt"
cat file.txt | wc -l         # Count lines
```

### Aliases and Functions

Add to `~/.zshrc` (Zsh) or `~/.bash_profile` (Bash):

```bash
# Aliases
alias ll='ls -lah'
alias ..='cd ..'
alias ...='cd ../..'
alias update='brew update && brew upgrade'
alias cleanup='brew cleanup'

# Function to create and enter directory
mkcd() {
    mkdir -p "$1" && cd "$1"
}

# After editing, reload config
source ~/.zshrc  # or source ~/.bash_profile
```

### Path Management

```bash
# View current PATH
echo $PATH

# Add to PATH (temporarily)
export PATH="/new/path:$PATH"

# Add to PATH (permanently)
echo 'export PATH="/new/path:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

## Common Issues

### Command Not Found

```bash
# Install with Homebrew
brew install package-name

# Check if command exists
which command-name

# Update PATH
echo $PATH
```

### Permission Denied

```bash
# Use sudo for admin tasks
sudo command

# Change permissions
chmod +x script.sh

# Check file ownership
ls -l file.txt
```

### Homebrew Issues

```bash
# Fix permissions
sudo chown -R $(whoami) /usr/local/Cellar

# Run diagnostics
brew doctor

# Reinstall command line tools
xcode-select --install
```

### "Operation not permitted" on macOS Catalina+

This is due to System Integrity Protection (SIP). Generally, don't disable SIP unless absolutely necessary.

```bash
# Check SIP status
csrutil status
```

### Terminal Not Opening

1. Try opening from Safe Mode
2. Reset terminal preferences: Delete `~/Library/Preferences/com.apple.Terminal.plist`
3. Restart your Mac

---

[← Back to Main Guide](./README.md)
