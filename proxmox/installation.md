# Proxmox Installation
Documenting my steps after first booting and installing Proxmox on my Slimbook One mini-pc.

## Install steps
Installing is actually very easy. I used the graphical installer.
1. Accept the licence blabla
2. Setup hard-drive settings
3. Enter password and email adress
4. Setup the network interface. Be aware, you need a full domain name. For example: pve.homelab.local
5. Let the installer do it's thing

After installation you can login using the user `root` with the set password:
- On the server console you can login into the Debian Host OS
- Login to Proxmox with a web browser to the internet adress + port 8006. For example: https://192.168.0.150:8006

---

## Post install steps
Steps to take after the first installation

### 1. Repositories & Update
I don't have an enterprise subscription so I need to change the repositories to get the latest updates. These are found under: `Datacenter -> node -> Updates -> Repositories`.

Change the repositories:
- Disable: `https://enterprise.poxmox.com/debian/ceph-squid`
- Disable: `https://enterprise.proxmox.com/deboan/pve`
- Add the `No subscription` repository: `http://download.proxmox.com/debian/pve`

Update the Proxmox server:
1. Go back to Updates and then above you can click `refresh`. Proxmox will check the repositories for updates.
2. When done click `>... Upgrade` and a console will open to update the full Proxmox node. Sometimes you will need to reboot. Close the console and reboot from the Proxmox-UI.

---

### 2. Storage
I have an external 4TB SSD which I will use for bare-metal backups.
> I also have a Synology DS420+ NAS but I'll need to buy first an extra HD because there is not enough room left

#### Prepare External SSD
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

#### Config External SSD as full VM backup
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








