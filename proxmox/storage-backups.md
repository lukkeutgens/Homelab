# Storage & Backup Strategy
My Slimbook One mini‑PC currently has a 1TB internal SSD (set up by the Proxmox installer). Additionally, I use a fast external 4TB Kingston XS2000 SSD for backups.

- Bare‑metal full backups → [Rescuezilla](https://rescuezilla.com/) to the external SSD.
- VM backups → Proxmox to the external SSD.
- I also own a Synology NAS DS420+, but for now I leave it out of scope.

## 1. External SSD for VM backups
I will use my Kingston XS2000 4TB USB-C SSD as backup for the full VM's. But there are issues, see the warning below.

> ⚠️ Note: The external SSD is not detected by Linux immediately after a reboot. To prevent boot issues, I configured `/etc/fstab` with safe options (`nofail`, `x-systemd.automount`) so Proxmox can start even if the drive is not connected. When the SSD is connected, it is mounted automatically after a short delay.
> Additionally, I adjusted the Slimbook BIOS settings under USB devices: changed Device power‑up delay from Auto to 30 seconds to give the SSD enough time to initialise. But sometimes it still does not mount automatically.

### 1.1 Prepare the SSD through the console
Identify the drive:
```bash
lsblk                 # Overview of all drives
dmesg | tail -n 20    # Show kernel messages for newly connected devices
blkid                 # Lookup UUIDs of partitions
```
- Internal Slimbook SSD → nvme0n1 (set up by Proxmox installer)
- External SSD → sda (with partition sda1)

Partition and format:
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
```
#### Configure fstab
Lookup the UUID of the external SSD:
```bash
blkid /dev/sda1
```
Edit /etc/fstab and add:
```bash
UUID=052d0156-b8bd-4da3-84a9-b6d092f58fdd /mnt/ssd-backup ext4 defaults,nofail,x-systemd.automount,x-systemd.device-timeout=15 0 2
```
Explanation of options
- UUID=… → stable identifier, independent of /dev/sdX naming
- /mnt/ssd-backup → mountpoint for external storage
- ext4 → filesystem type
- defaults,nofail → system boots even if SSD is missing
- x-systemd.automount → mount on demand when accessed
- x-systemd.device-timeout=15 → wait up to 15s for device to appear
- 0 2 → fsck order (root first, then secondary disks)

Verify
```bash
mount -a                 # Mount all filesystems listed in fstab
systemctl daemon-reload  # Reload systemd configuration
df -h | grep ssd-backup  # Verify SSD is mounted and available
```
After reboot, check again:
```bash
lsblk
df -h
```

### 1.2 Add the SSD to Proxmox Datacenter Storage
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

## 2. Rescuezilla for Full Backup
Let's take a full backup off the Proxmox server as it is now. 

### 2.1 Prepare an USB-stick with Rescuezilla on
Prepare a USB-stick with Rescuezilla on:
1. Download the Rescuezilla installer at their [website](https://rescuezilla.com/download)
2. Get yourself a seperate USB-stick for installing it to (Be aware! The USB-stick will be completely erased)
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

### 2.2 Take the backup with Rescuezilla
Now take a backup off the Proxmox server:
1. Shutdown the server
2. Remove the external SSD
3. Insert the Rescuezilla USB-stick
4. Startup the server and go into the boot-menu (F7 for my Slimbook One)
5. Choose the USB-stick with Rescuezilla on
6. Check the disks (use menu left corner below), if the external SSD is not in there, reconnect it.
7. Then go to take a backup and follow the steps

---




