# Linux Terminal Guide

## Table of Contents

1. [Opening the Terminal](#opening-the-terminal)
2. [Understanding the Shell](#understanding-the-shell)
3. [Basic Navigation](#basic-navigation)
4. [File Operations](#file-operations)
5. [File Permissions](#file-permissions)
6. [Text Editing](#text-editing)
7. [Process Management](#process-management)
8. [Package Management](#package-management)
9. [Useful Tips](#useful-tips)
10. [Common Issues](#common-issues)

## Opening the Terminal

### Keyboard Shortcuts

- **Ubuntu/Debian**: `Ctrl + Alt + T`
- **Most distributions**: `Ctrl + Alt + T` or search for "Terminal" in applications

### From GUI

1. Open your application menu
2. Search for "Terminal", "Console", or "Konsole"
3. Click to launch

## Understanding the Shell

Most Linux distributions use **Bash** (Bourne Again Shell) by default, though **Zsh** is becoming popular.

### The Prompt

A typical prompt looks like:
```
username@hostname:~$
```

- `username` - Your current user
- `hostname` - Your computer name
- `~` - Current directory (~ means home directory)
- `$` - Regular user (# for root user)

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
cd ~           # Go to home directory
cd ..          # Go up one directory
cd -           # Go to previous directory

# Create directory
mkdir newfolder
mkdir -p path/to/nested/folders

# View directory tree
tree           # Install with: sudo apt install tree
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

# Create file with content
echo "Hello World" > file.txt
cat > file.txt       # Type content, Ctrl+D to save
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
rm -rf directory/       # Force delete without confirmation (use carefully!)

# Find files
find /path -name "filename"
find . -name "*.txt"
locate filename         # Faster, uses database
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
sudo chown user:group file.txt
sudo chown -R user:group directory/
```

## Text Editing

### Command Line Editors

```bash
# Nano (beginner-friendly)
nano filename.txt
# Ctrl+O to save, Ctrl+X to exit

# Vim (powerful but steeper learning curve)
vim filename.txt
# Press 'i' to insert, 'Esc' to exit insert mode
# Type ':wq' to save and quit, ':q!' to quit without saving

# Emacs
emacs filename.txt
```

## Process Management

```bash
# View running processes
ps aux
top               # Interactive process viewer
htop              # Better interactive viewer (install: sudo apt install htop)

# Find process by name
ps aux | grep processname

# Kill process
kill PID          # Graceful termination
kill -9 PID       # Force kill
killall processname

# Run in background
command &
nohup command &   # Keep running after logout

# View background jobs
jobs
fg %1             # Bring job 1 to foreground
bg %1             # Resume job 1 in background
```

## Package Management

### Debian/Ubuntu (APT)

```bash
# Update package list
sudo apt update

# Upgrade all packages
sudo apt upgrade

# Install package
sudo apt install package-name

# Remove package
sudo apt remove package-name
sudo apt purge package-name    # Remove with config files

# Search for package
apt search keyword
```

### Red Hat/Fedora/CentOS (DNF/YUM)

```bash
# Update packages
sudo dnf update

# Install package
sudo dnf install package-name

# Remove package
sudo dnf remove package-name

# Search for package
dnf search keyword
```

### Arch Linux (Pacman)

```bash
# Update system
sudo pacman -Syu

# Install package
sudo pacman -S package-name

# Remove package
sudo pacman -R package-name

# Search for package
pacman -Ss keyword
```

## Useful Tips

### Keyboard Shortcuts

- `Ctrl + C` - Cancel current command
- `Ctrl + D` - Exit terminal / EOF
- `Ctrl + L` - Clear screen (same as `clear`)
- `Ctrl + A` - Move to beginning of line
- `Ctrl + E` - Move to end of line
- `Ctrl + U` - Delete from cursor to beginning
- `Ctrl + K` - Delete from cursor to end
- `Ctrl + R` - Search command history
- `Tab` - Auto-complete commands and paths
- `↑/↓` - Navigate command history

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

### Useful One-liners

```bash
# Disk usage
df -h              # Filesystem usage
du -sh *           # Directory sizes in current folder

# System information
uname -a           # Kernel information
lsb_release -a     # Distribution information
free -h            # Memory usage
uptime             # System uptime

# Network
ip a               # IP addresses
ping google.com    # Test connectivity
curl ifconfig.me   # Get public IP
netstat -tuln      # Open ports
```

## Common Issues

### Permission Denied

```bash
# Solution: Use sudo for administrative tasks
sudo command

# Or change file permissions
chmod +x script.sh
```

### Command Not Found

```bash
# Solution 1: Install the package
sudo apt install package-name

# Solution 2: Check if it's in PATH
which command-name

# Solution 3: Use full path
/full/path/to/command
```

### Terminal Freezing

- Press `Ctrl + Q` (might be paused with `Ctrl + S`)
- Press `Ctrl + C` to cancel current command

---

[← Back to Main Guide](./README.md)
