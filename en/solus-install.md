# Before installing Solus

1. Download ISO-image from https://mirrors.rit.edu/solus/images/4.3/ or https://getsol.us/download/
2. Download Etcher from https://www.balena.io/etcher/
3. Insert your USB Flash Drive (clear needed) and write image Solus on this drive with Etcher
4. Restart your system
5. Select USB Flash to boot in UEFI menu (UEFI in name flash)

# Install Solus
## Partitioning

1. Open Gparted and create GPT partition table and first partition for boot (file system: fat32, volume: 1024 MB, flags (in menu "Manage flags"): boot, esp)
2. Create Second partition for system (root /) (file system: ext4)
3. Optionally create Third (/home) partition (file system: ext4)

## OS install

1. Choose your language and English keyboard (Important!)
2. In section "Disks" choose "assign mount points"
3. In section "Disk configuration" choose "mount point" for root (/) and (/home)
4. In next screen (section "System settings") name your host and choose disk for bootloader. 

# Known issues

1. If you choose non-English keyboard layout your password will be in your language. If you live in countries with CA, you should select screen keyboard in LightDM, press the button "Win" and  the layout will be switched to English

**Other languages**:

Русский: https://github.com/Solus-users/solus-guides/blob/main/ru/solus-install.md
