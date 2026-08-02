---
layout: post
title: "Raspberry Pi 5: Booting From NVMe and Auto-Starting Services"
date: 2026-08-02 11:00:00 +0200
description: "The technical details behind my headless Pi AI agent: flashing the SSD with Raspberry Pi Imager, pre-setting WiFi and SSH, EEPROM boot order, PARTUUID root filesystem, PCIe Gen 3, systemd services, and Raspberry Pi Connect."
tags: [raspberry-pi, nvme, systemd, homelab, linux, headless]
categories: [technology]
---

# Raspberry Pi 5: Booting From NVMe and Auto-Starting Services

*2026-08-02 · 10 min read · [raspberry-pi] [nvme] [systemd] [homelab] [linux] [headless]*

This is the technical companion to my [Raspberry Pi AI agent overview](https://erikzocher.github.io/technology/2026/08/02/raspberry-pi-ai-agent.html). That post covers the hardware and the big picture. This one covers the whole machine: from flashing the SSD and setting up WiFi before the first boot, to booting from NVMe, auto-starting services, and getting a GUI when I need one.

## 1. First Boot: Flashing the SSD and Pre-Setting WiFi

Before any customization, the Pi needs an operating system, and for a headless setup the trick is to configure everything **before** the first boot, so you never need a monitor or keyboard.

### Flash the OS directly to the SSD

[Raspberry Pi Imager](https://www.raspberrypi.com/software/) is the official tool and it does the whole job in one go. It runs on Windows, macOS, and Linux, and it can write the OS straight to the NVMe drive.

1. **Connect the SSD to your computer.** An NVMe-to-USB adapter or a small USB enclosure is all you need. The drive shows up like a big USB stick.
2. **Open Raspberry Pi Imager** and click *Choose Device* → Raspberry Pi 5.
3. **Choose OS** → Raspberry Pi OS (64-bit) or Raspberry Pi OS Lite if you want no desktop at all.
4. **Choose Storage** → pick the NVMe SSD, not your computer's own disk!
5. **Click the gear icon** (or press Ctrl+Shift+X) to open the advanced options. This is the headless magic:
   - **Enable SSH** and set it to allow password login (or paste an SSH key for key-only access).
   - **Set the username and password** you want to log in with.
   - **Configure WiFi**: enter the SSID and password, and pick the WiFi country. The Imager writes these into the image, so the Pi connects to your network on its very first boot, with no keyboard needed.
   - Optionally set a **hostname** (mine is a plain `ezocher` box, but something like `piagent` is nicer) and your timezone.
6. **Click Write** and wait. The Imager flashes the OS, then verifies the write.
7. **Unplug the SSD from the computer**, mount it on the Pi with the M.2 HAT+, connect power, and wait about a minute.

That is the whole first-boot ritual: no monitor, no keyboard, no HDMI cable. The Pi appears on your WiFi, reachable by SSH, with the OS already on the NVMe drive.

If you are curious what the Imager actually did, it wrote a file called `userconfig.txt` (or a first-run script) onto the boot partition of the SSD with your SSH and WiFi settings. That file is read once on first boot and then consumed. You can pre-seed the same settings manually, but the Imager dialog is less error-prone.

## 2. Booting From the NVMe SSD

The Pi 5 does not boot from the microSD in my setup. It boots from the NVMe drive, and there are three pieces to that: the EEPROM boot order, the root filesystem in fstab, and the PCIe speed.

### EEPROM boot order

The Pi 5's boot order lives in its EEPROM. Mine reads:

```bash
$ sudo rpi-eeprom-config | grep BOOT_ORDER
BOOT_ORDER=0xf146
```

That hex value is read from right to left, each digit a boot source:

| Digit | Meaning |
|-------|---------|
| `6` | Restart from the top of the list |
| `4` | USB mass storage (where the NVMe appears) |
| `1` | SD card (fallback) |
| `f` | Loop back to the beginning |

So the Pi tries the NVMe first, falls back to the SD card, and if neither works it keeps cycling. To change it, either use the menu:

```bash
sudo raspi-config   # Advanced Options → Boot Order → NVMe/USB Boot
```

or edit the EEPROM directly:

```bash
sudo rpi-eeprom-config --edit
# change BOOT_ORDER to 0xf146, save, reboot
```

### Root filesystem by PARTUUID

The kernel finds the root partition by its PARTUUID, not by device name. That matters because device names (`/dev/sda`, `/dev/mmcblk0`) can change between boots, while PARTUUIDs are burned into the partition table.

```bash
$ cat /etc/fstab
PARTUUID=957b593b-02  /               ext4    defaults,noatime  0  1
PARTUUID=957b593b-01  /boot/firmware  vfat    defaults          0  2
UUID=76602090-4e70-4a14-a6c2-ffd91561de93 /mnt/nvme ext4 defaults,auto,users,rw,nofail 0 0
```

The `957b593b` PARTUUIDs belong to the NVMe partition table. You can confirm which disk they point at:

```bash
$ lsblk -o NAME,PARTUUID,MOUNTPOINT
nvme0n1
├─nvme0n1p1  957b593b-01  /boot/firmware
└─nvme0n1p2  957b593b-02  /
```

The old microSD is demoted to a plain data disk, mounted at `/media/ezocher/bootfs`. The `nofail` option on the `/mnt/nvme` mount is important: it lets the system boot even if that drive is missing, instead of hanging at a mount prompt.

### PCIe Gen 3 for full NVMe speed

The Pi 5 runs its PCIe bus at Gen 2 by default, which caps the NVMe throughput. Bumping to Gen 3 is one line in `/boot/firmware/config.txt`:

```ini
dtparam=pciex1_gen=3
```

Reboot and `sudo dmesg | grep nvme` should show the drive negotiating at Gen 3 speeds.

## 3. Auto-Starting Services With systemd

The "turn it on and it just works" part is systemd. Three services run on my Pi, all enabled at boot and all set to restart on failure:

```bash
$ ls /etc/systemd/system/*.service
hermes-gateway.service    # the AI agent
ollama.service            # local model serving (legacy, see the overview)
kindle-dashboard.service  # serves the e-ink dashboard image
```

The agent service shows the pattern:

```ini
[Unit]
Description=Hermes Agent Gateway
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
User=ezocher
ExecStart=/home/ezocher/.hermes/hermes-agent/venv/bin/python -m hermes_cli.main gateway run
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

Three details worth copying:

- **`After=network-online.target`** with **`Wants=`** waits until the network is actually up, so the agent does not start half-connected.
- **`Restart=always`** with **`RestartSec=5`** brings the service back within seconds of any crash. This is the real resilience trick for a headless box.
- **`WantedBy=multi-user.target`** starts the service at boot without needing anyone to log in.

Enable any service with:

```bash
sudo systemctl enable --now <service-name>
```

The result: after a power cut, the kernel finds the NVMe, mounts the root partition, and systemd brings the whole stack up without a human in the loop. Power on, wait a minute, message the agent on Telegram. That is the whole ritual.

## 4. Raspberry Pi Connect for GUI Access

The Pi runs headless, but sometimes you need a graphical interface. **Raspberry Pi Connect** is the Foundation's own remote access service: free, encrypted, and it works from any browser with no port forwarding.

**Install it** (on recent Raspberry Pi OS images it is already there, so check first):

```bash
rpi-connect --help
```

If that says "command not found":

```bash
sudo apt update && sudo apt install rpi-connect
```

**Link the Pi to your account:**

```bash
rpi-connect signin
```

It prints a URL. Open it in any browser, log in with your Raspberry Pi ID, and the Pi is linked.

**Enable the service** so it survives reboots:

```bash
sudo systemctl enable --now rpi-connect
```

**Connect:** go to [connect.raspberrypi.com](https://connect.raspberrypi.com), log in, and your Pi appears in the list. Click it and you get the desktop in a browser tab, or a shell. No port forwarding, no VPN, no dynamic DNS, and it works from outside your home network too.

Useful commands while you are at it: `rpi-connect status` shows the connection state, `rpi-connect restart` fixes a stuck client, and `rpi-connect doctor` runs diagnostics.

## Putting It All Together

The full boot story is: EEPROM says "try NVMe first", fstab points the root filesystem at the NVMe partition, config.txt unlocks Gen 3 PCIe, and systemd starts the agent, the dashboard server, and Connect automatically. That is the difference between a Pi you have to poke at and a Pi that just runs.

*This is the technical half of the story. For the hardware list, the agent setup, and why I use a cloud model instead of a local one, see the [overview post](https://erikzocher.github.io/technology/2026/08/02/raspberry-pi-ai-agent.html).*
