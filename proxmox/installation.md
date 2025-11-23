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

### 2. Storage
I have an external 4TB SSD which I will use for bare-metal backups and a Synology NAS for the VM backups.

#### Setup External SSD
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
-> Command: g            # Delete old and create new partition table
-> Command: n            # Create a new partition
-> Partition number: 1   # Default setting
-> First sector:         # Leave empty en press enter for default sector
-> Last sector:          # Leave empty en press enter for default sector
-> Command: w            # Write to disk and close fdisk interactive menu

lsblk                    # You should now see the new drive with 1 partition, in my case "sda1"
mkfs.ext4 /dev/sda1      # Format the partition to EXT4 fileformat
mkdir /mnt/ssd-backup    # Directory to mount drive sda1 to. We use "/mnt" because it's an external drive.

echo "/dev/sda1 /mnt/ssd-backup ext4 defaults 0 2" >> /etc/fstab    # Add line to /etc/fstab to automatically mount drive
mount -a                 # Mount all from the file /ect/fstab -> this will give a warning (hint)
systemctl daemon-reload  # Reload like the warning said to do
df -h | grep ssd-backup  # To check if disk is correctly mounted and ready to use
```






