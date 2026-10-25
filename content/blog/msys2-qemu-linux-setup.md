+++
title = "Run Linux on Windows 10 or Tiny10 with MSYS2 and QEMU"
description = "A step-by-step guide to running an Ubuntu Server virtual machine on Windows 10, including stripped-down builds like Tiny10, using MSYS2 and QEMU, without WSL or Hyper-V Manager."
date = 2025-06-27
updated = 2026-10-25
template = "post.html"
aliases = ["posts/msys2-qemu-linux-setup/"]
+++

> **Updated October 2026:** fixed commands that do not work on Windows (`-daemonize`, `-cpu host` without acceleration, `taskkill` flags in MSYS2) and removed an install step for a package that does not exist.

If you run Windows 10, or a stripped-down build like Tiny10, and need a small Linux machine, MSYS2 and QEMU are a good fit. They work where WSL or Hyper-V Manager are missing or switched off, and you control every setting.

## Why MSYS2 and QEMU?

- **Light:** works on minimal Windows builds like Tiny10
- **No WSL or Hyper-V Manager needed:** QEMU runs as a normal program
- **Full control:** you choose the memory, CPU, disk and network
- **Faster with acceleration:** if your PC supports the Windows Hypervisor Platform, QEMU can use it

## What you need

- Windows 10, including Tiny10 and other modified builds
- At least 4 GB of RAM (8 GB is better)
- At least 20 GB of free disk space
- Administrator access, only if you want to turn on acceleration (step 2)

## Step 1: Install MSYS2

1. Download the installer from [msys2.org](https://www.msys2.org/) and run it.
2. Open **MSYS2 MINGW64** from the Start menu. Use this terminal for every command in this guide, because QEMU is installed into the MINGW64 environment.
3. Update everything:

```bash
pacman -Syu
# If the terminal closes, open MSYS2 MINGW64 again and run:
pacman -Su
```

## Step 2: Install QEMU (and turn on acceleration if you can)

```bash
pacman -S mingw-w64-x86_64-qemu wget
```

The `mingw-w64-x86_64-qemu` package already includes `qemu-system-x86_64` and `qemu-img`.

**Optional, but much faster:** turn on the Windows Hypervisor Platform. Open PowerShell as Administrator and run:

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName HypervisorPlatform
```

Restart Windows afterwards. Some Tiny10 builds remove this feature. If the command fails, skip it and use the slower `tcg` mode shown below.

## Step 3: Download a Linux ISO

This guide uses Ubuntu Server. Get the current LTS ISO from [ubuntu.com/download/server](https://ubuntu.com/download/server) and save it into a folder for your VM:

```bash
mkdir -p ~/vms/ubuntu
cd ~/vms/ubuntu
# Move the downloaded ISO here, then rename it to keep the commands short:
mv ubuntu-*-live-server-amd64.iso ubuntu-server.iso
```

## Step 4: Create a virtual disk

```bash
qemu-img create -f qcow2 ubuntu.qcow2 20G
```

The `qcow2` format only uses space on your drive as the VM fills it.

## Step 5: Install Linux

With acceleration (Windows Hypervisor Platform turned on):

```bash
qemu-system-x86_64 \
  -accel whpx \
  -m 2048 -smp 2 \
  -drive file=ubuntu.qcow2,format=qcow2 \
  -cdrom ubuntu-server.iso -boot d \
  -netdev user,id=net0 -device e1000,netdev=net0 \
  -vga std
```

Without acceleration, replace `-accel whpx` with `-accel tcg -cpu max`. It works everywhere, but it is several times slower.

If you see an error about `kernel-irqchip`, use `-accel whpx,kernel-irqchip=off`.

In the installer:

1. Choose your language and keyboard.
2. Keep the default network settings.
3. Use the entire (virtual) disk.
4. Create your user. The first user is added to the `sudo` group automatically.
5. Tick **Install OpenSSH server** when asked. This saves a step later.

## Step 6: Start the installed system

After the installer finishes, close QEMU and start again without the ISO. This time, forward port 2222 on Windows to port 22 (SSH) in the VM:

```bash
qemu-system-x86_64 \
  -accel whpx \
  -m 2048 -smp 2 \
  -drive file=ubuntu.qcow2,format=qcow2 \
  -netdev user,id=net0,hostfwd=tcp::2222-:22 -device e1000,netdev=net0 \
  -vga std &
```

The `&` at the end runs QEMU in the background so you get your terminal back. (QEMU's `-daemonize` option does not work on Windows.)

If you did not install OpenSSH during setup, log in through the QEMU window and run:

```bash
sudo apt update
sudo apt install openssh-server
sudo systemctl enable --now ssh
```

Then connect from Windows:

```bash
ssh your_username@localhost -p 2222
```

## Step 7: Make start-up scripts

### Batch file (double-click to start)

Save this as `C:\msys64\home\%USERNAME%\start-ubuntu.bat`:

```batch
@echo off
cd /d C:\msys64\home\%USERNAME%\vms\ubuntu
start "Ubuntu VM" C:\msys64\mingw64\bin\qemu-system-x86_64.exe ^
  -accel whpx ^
  -m 2048 -smp 2 ^
  -drive file=ubuntu.qcow2,format=qcow2 ^
  -netdev user,id=net0,hostfwd=tcp::2222-:22 -device e1000,netdev=net0 ^
  -display none
echo Ubuntu VM started. Connect with: ssh your_username@localhost -p 2222
```

`start` runs QEMU in its own process, and `-display none` hides the window, because you will use SSH.

### Shell script (from MSYS2 MINGW64)

Save this as `~/vms/start-ubuntu.sh`:

```bash
#!/bin/bash
cd ~/vms/ubuntu
qemu-system-x86_64 \
  -accel whpx \
  -m 2048 -smp 2 \
  -drive file=ubuntu.qcow2,format=qcow2 \
  -netdev user,id=net0,hostfwd=tcp::2222-:22 -device e1000,netdev=net0 \
  -display none &
echo "Ubuntu VM started. Connect with: ssh your_username@localhost -p 2222"
```

Make it executable and run it:

```bash
chmod +x ~/vms/start-ubuntu.sh
~/vms/start-ubuntu.sh
```

## Managing your VM

**Check that it is running:**

```bash
tasklist | grep -i qemu
```

**Shut it down cleanly** (from inside the VM):

```bash
sudo shutdown -h now
```

**Force it to stop** (only if it hangs). In MSYS2, Windows-style flags need a double slash, because MSYS2 turns `/F` into a file path:

```bash
taskkill //F //IM qemu-system-x86_64.exe
```

From Command Prompt, the normal `taskkill /F /IM qemu-system-x86_64.exe` works.

## Managing sudo users inside Linux

**Check your access:**

```bash
sudo whoami
# Should print: root
```

**Add another sudo user:**

```bash
sudo adduser newuser
sudo usermod -aG sudo newuser
groups newuser
```

**Passwordless sudo (optional, and only on a test VM):**

```bash
sudo visudo
# Add this line at the end:
your_username ALL=(ALL) NOPASSWD:ALL
```

## Troubleshooting

**`qemu-system-x86_64: command not found`**
You are in the wrong terminal. Open **MSYS2 MINGW64**, or add it to the path:

```bash
export PATH="/mingw64/bin:$PATH"
```

**`WHPX: No accelerator found` or the VM will not start with `-accel whpx`**
The Windows Hypervisor Platform is off or missing (common on Tiny10). Turn it on (step 2), or use `-accel tcg -cpu max`.

**Very slow VM**
You are probably running without acceleration. Give it fewer jobs: `-smp 2`, 1 to 2 GB of RAM, and Ubuntu Server without a desktop.

**Missing DLL errors on Tiny10**
Install the latest Microsoft Visual C++ Redistributable.

**SSH says "connection refused"**
The VM may still be booting. Wait a minute. Check that SSH is running inside the VM with `systemctl status ssh`.

## Security tips

1. Use a strong password, or better, SSH keys.
2. Turn on the firewall inside the VM: `sudo ufw allow OpenSSH && sudo ufw enable`.
3. Keep it updated: `sudo apt update && sudo apt upgrade`.
4. Give sudo only to users who need it.

## Wrapping up

You now have a full Linux server on Windows 10 or Tiny10, with no WSL and no Hyper-V Manager. It is great for learning Linux administration, testing software, or running tools that only work on Linux.

Next ideas:

- Share a folder between Windows and the VM
- Back up `ubuntu.qcow2` before big changes (or use `qemu-img snapshot`)
- Try other distributions with the same steps
