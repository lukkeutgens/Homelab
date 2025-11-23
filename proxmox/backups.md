# Storage & Backup Strategy
My Slimbook One mini-pc has only a 1TB SSD for now which I can expand in the future. For now I also have a fast external 4TB SSD which I will use for my backup strategy. 
For bare-metall full-backups I will use [Rescuezilla](https://rescuezilla.com/) also to that same external SSD.
I also have a Synology NAS DS420+ but I'm leaving it out off the picture for now. 

## Prepare External SSD
After connecting to the Slimbook, see which one it is and format it to EXT4 format. So open the shell from Proxmox:
```bash
lsblk                 # Gives an overview off the drives
dmesg | tail -n 20    # Can be used to show the new connected drive and where
```
- Slimbooks SSD is called "nvmeOn1" which was setup by the Proxmox installer
- External SSS is called sda on my system.

Now we need to format this disk and mount it.
```bash
fdisk /dev/sda           # Start fdisk interactive menu
-> Command: g            # Create a new GPT partition table (wipes old one)
-> Command: n            # Create a new partition
-> Partition number: 1   # Accept default (1)
-> First sector:         # Leave empty en press enter to accept default (start of disk)
-> Last sector:          # Leave empty en press enter to accept default (end of disk, full size)
-> Command: w            # Write changes to disk and exit fdisk

lsblk                    # Verify new partition exists, e.g. "sda1"
mkfs.ext4 /dev/sda1      # Format the partition with EXT4 filesystem
mkdir /mnt/ssd-backup    # Create mount directory. Convention: use "/mnt" for external/extra storage

echo "/dev/sda1 /mnt/ssd-backup ext4 defaults 0 2" >> /etc/fstab    # Add entry to /etc/fstab for automatic mounting at boot
mount -a                 # Mount all filesystems listed in /etc/fstab (may show a systemd hint)
systemctl daemon-reload  # Reload systemd configuration as suggested
df -h | grep ssd-backup  # Verify that the disk is mounted and available
```

---

## Config External SSD for full VM backups
I will use the external SSD for full bare-metal backups using Rescuezilla. Probably keep 2x full-backups.
The other space left (~2TB) will for now be used to backup full VM's with **vzdump** in Proxmox

1. Under `Datacenter -> Storage` choose `Add` to create a new directory
2. Enter following settings:
    - ID: ssd-backup
    - Directory: /mnt/ssd-backup
    - Content: Backup, ISO image (Yes, I will save the installer ISO's also here)
    - Nodes: pve01
    - Enable: Yes
    - Shared: No (It's an external SSD, so not shared between nodes)
  
3. Accept settings and storage is ready for use in Proxmox

---

## Rescuezilla Full Backup Steps
Let's take a full backup off the Proxmox server as it is now. 
1. Download the Rescuezilla installer at their [website](https://rescuezilla.com/download)
2. Prepare a seperate USB-stick for installing it to (Be aware! The USB-stick will be completely erased)
3. Download [Rufus](https://rufus.ie/) to write Rescuezilla to the USB-stick
4. Start Rufus and select the following
    - Device: Select the USB-stick
    - Boot selection: Use the Rescuezilla ISO
    - Persistent partition size: 0 (no persistence)
    - Partition scheme: MBR
    - Target system: BIOS (or UEFI)
    - Volume label: Rescuezilla
    - File system: FAT (default)
    - Cluster size: 64 KB (default)
5. When done, you can use the USB-stick to backup a full bare-metall




