+++ 
draft = true
date = 2025-06-27T20:44:20-07:00
title = ""
description = ""
slug = ""
authors = []
tags = []
categories = []
externalLink = ""
series = []
+++

## Setting Up Linux with MSYS2 and QEMU on Windows 10/Tiny10

If you're running Windows 10 or a modified version like Tiny10 and need a lightweight Linux environment, MSYS2 with QEMU provides an excellent solution. This setup is particularly useful when Hyper-V isn't available or when you need more control over your virtualization environment.

## Why MSYS2 + QEMU?

- **Lightweight**: Works on minimal Windows installations like Tiny10
- **No Hyper-V dependency**: Perfect for systems where Hyper-V is disabled/unavailable
- **Full control**: Complete virtualization without vendor lock-in
- **Performance**: Direct hardware access through KVM-like features

## Prerequisites

- Windows 10 (including Tiny10 or other modified versions)
- At least 4GB RAM (8GB recommended)
- 20GB+ free disk space
- Administrator access for initial setup

## Step 1: Install MSYS2

### Download and Install MSYS2

```bash
# Download MSYS2 from https://www.msys2.org/
# Run the installer and follow the setup wizard
```

### Update MSYS2 System

```bash
# Open MSYS2 terminal and update the system
pacman -Syu

# Close terminal when prompted, reopen and run:
pacman -Su
```

## Step 2: Install QEMU and Dependencies

```bash
# Install QEMU and related packages
pacman -S mingw-w64-x86_64-qemu
pacman -S mingw-w64-x86_64-qemu-system-x86_64
pacman -S mingw-w64-x86_64-spice-gtk3

# Install additional utilities
pacman -S wget curl unzip
```

## Step 3: Download Linux ISO

For this guide, we'll use Ubuntu Server (lightweight option):

```bash
# Create a directory for VM files
mkdir -p ~/vms/ubuntu
cd ~/vms/ubuntu

# Download Ubuntu Server ISO (adjust URL for latest version)
wget https://releases.ubuntu.com/22.04/ubuntu-22.04.3-live-server-amd64.iso
```

## Step 4: Create Virtual Disk

```bash
# Create a 20GB virtual disk
qemu-img create -f qcow2 ubuntu.qcow2 20G
```

## Step 5: Install Linux

```bash
# Start the installation
qemu-system-x86_64 \
  -m 2048 \
  -cpu host \
  -smp 2 \
  -hda ubuntu.qcow2 \
  -cdrom ubuntu-22.04.3-live-server-amd64.iso \
  -boot d \
  -netdev user,id=net0 \
  -device e1000,netdev=net0 \
  -vga std
```

### Installation Process

1. **Boot from ISO**: The VM will boot from the Ubuntu ISO
2. **Language/Keyboard**: Select your preferences
3. **Network**: Configure network (usually auto-detected)
4. **Storage**: Use entire disk (virtual disk)
5. **User Setup**: Create your non-root user with sudo privileges

#### Creating a Sudo User During Installation

```bash
# During Ubuntu installation:
# Username: your_username
# Password: your_secure_password
# 
# The installer automatically adds the first user to sudo group
```

## Step 6: Post-Installation Setup

After installation completes, restart without the ISO:

```bash
# Boot the installed system
qemu-system-x86_64 \
  -m 2048 \
  -cpu host \
  -smp 2 \
  -hda ubuntu.qcow2 \
  -netdev user,id=net0,hostfwd=tcp::2222-:22 \
  -device e1000,netdev=net0 \
  -vga std \
  -daemonize
```

### Enable SSH Access

```bash
# Inside the VM, install and enable SSH
sudo apt update
sudo apt install openssh-server
sudo systemctl enable ssh
sudo systemctl start ssh
```

### Connect via SSH from Windows

```bash
# From MSYS2 terminal on Windows
ssh your_username@localhost -p 2222
```

## Step 7: Create Startup Scripts

### Windows Batch Script

```batch
@echo off
REM filepath: c:\msys64\home\%USERNAME%\start-ubuntu.bat
cd /d C:\msys64\home\%USERNAME%\vms\ubuntu
C:\msys64\mingw64\bin\qemu-system-x86_64.exe ^
  -m 2048 ^
  -cpu host ^
  -smp 2 ^
  -hda ubuntu.qcow2 ^
  -netdev user,id=net0,hostfwd=tcp::2222-:22 ^
  -device e1000,netdev=net0 ^
  -vga std ^
  -daemonize
```

### MSYS2 Shell Script

```bash
#!/bin/bash
# filepath: ~/vms/start-ubuntu.sh
cd ~/vms/ubuntu
qemu-system-x86_64 \
  -m 2048 \
  -cpu host \
  -smp 2 \
  -hda ubuntu.qcow2 \
  -netdev user,id=net0,hostfwd=tcp::2222-:22 \
  -device e1000,netdev=net0 \
  -vga std \
  -daemonize

echo "Ubuntu VM started. Connect via: ssh your_username@localhost -p 2222"
```

Make it executable:

```bash
chmod +x ~/vms/start-ubuntu.sh
```

## Managing Your Linux VM

### Start the VM

```bash
./start-ubuntu.sh
```

### Connect via SSH

```bash
ssh your_username@localhost -p 2222
```

### Stop the VM

```bash
# From inside the VM
sudo shutdown -h now

# Or kill QEMU process from Windows
taskkill /f /im qemu-system-x86_64.exe
```

### Check VM Status

```bash
# List running QEMU processes
tasklist | findstr qemu
```

## Sudo User Management Inside Linux

### Verify Sudo Access

```bash
# Test sudo access
sudo whoami
# Should return: root
```

### Add Additional Sudo Users

```bash
# Create new user
sudo useradd -m -s /bin/bash newuser
sudo passwd newuser

# Add to sudo group
sudo usermod -aG sudo newuser

# Verify
groups newuser
```

### Configure Passwordless Sudo (Optional)

```bash
# Edit sudoers file
sudo visudo

# Add line:
your_username ALL=(ALL) NOPASSWD:ALL
```

## Troubleshooting

### Common Issues on Tiny10

1. **Missing Components**: Install Visual C++ Redistributables
2. **Performance**: Reduce RAM allocation if system has limited memory
3. **Network Issues**: Try different network backends:

```bash
-netdev user,id=net0,hostfwd=tcp::2222-:22
# Or
-netdev tap,id=net0,ifname=tap0,script=no,downscript=no
```

### QEMU Not Found

```bash
# Add MSYS2 to PATH or use full path
export PATH="/mingw64/bin:$PATH"
```

### Slow Performance

```bash
# Enable KVM-like features (if supported)
qemu-system-x86_64 -accel whpx  # Windows Hypervisor Platform
# Or
qemu-system-x86_64 -accel hax   # Intel HAXM
```

## Security Considerations

1. **Change default passwords** immediately
2. **Configure firewall** inside the VM
3. **Keep the VM updated**: `sudo apt update && sudo apt upgrade`
4. **Use SSH keys** instead of passwords for remote access
5. **Limit sudo access** to necessary users only

## Conclusion

This setup gives you a full Linux environment on Windows 10/Tiny10 without requiring Hyper-V or WSL. The combination of MSYS2 and QEMU provides excellent flexibility and performance, making it ideal for development, testing, or learning Linux administration.

The sudo user setup ensures you have proper privilege separation while maintaining administrative access when needed. This approach is particularly valuable on minimal Windows installations where traditional virtualization solutions might not be available.

## Next Steps

- Install development tools in your Linux VM
- Set up file sharing between Windows and Linux
- Configure automated backups of your VM
- Explore QEMU's advanced networking features
- Set up multiple VMs for testing different distributions

Happy virtualizing!
