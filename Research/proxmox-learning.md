# Proxmox Learning
Some notes about Proxmox installing and setup

## Questions that came up
- How to remote connect to Proxmox (only LAN)? Through the webserver, just surf to https://ipadress:8006
- Setup security (Hardening, auto-updates, ... ). Yes, use OS-hardening as for Debian.
- Firewall on Proxmox only handles Proxmox and not the VM's or you should edit the bridge settings.
- Other questions I had are answered below.
- Enable notifications: "datacenter" -> "notifications" to setup emails.

--- 

## Configure Updates
Setup the repositories correctly to get the newest (security) updates
- Enterprise repository is for a payed subscription (disabled it)
- Add the "No-Subscription" repository
- Ceph Quincy or Ceph Reef are for three node clusters (n.a. for me at this moment)
- Leave the debian repositories because Proxmox is built on Debian
- Sometimes the nodes need a reboot

--- 

## Trusted TLS Certificates
[Proxmox Wiki Certificate Management](https://pve.proxmox.com/wiki/Certificate_Management)

I will try to use the ACME but first need to install a DNS-server container and also VM's with a reverse proxy and Step CA.
So I will be using a self-signed certificate because I will make Proxmox accessable through the internet or it should be sitting behind a VPN (maybe). At the end, I will not actually need access from the internet.

From Copilot:
- Proxmox has `ACME` integration with "Let's Encrypt". This means that Proxmox can request for free Thrusted TLS-certificate and automatically renew these when you have a domain that resolves to the Proxmox node and you setup the right DNS or HTTP validation.
- Let's Encrypt is a standard CA that delivers free worldwide thrusted SSL/TLS-certificates.
- Use DNS-01 challenge so you don't have to expose port 80 to the internet
- Proxmox ACME can only manage its own certificates and not enroll them to the VM's running on it. Use the reverse proxy service (running on a VM) for ACME/Let's encrypt to handle the underlying services and other VM's connected to it.

From Tutorials:
- Best to use a domain name and DNS-provider.
- DNS Host for Proxmox (not sure about this, I want to keep the Proxmox itself only LAN accessible)
- Datacenter -> ACME : Uses Let's Encrypt and can be used to setup the online certificates like Cloudflare
- Add the certificates for each proxmox node (use DNS challenge Type?)
- Setup a cluster DNS-name and also node DNS names. Use a DNS-server with health checks when using the cluster name.
- A cluster can have maximum 3 nodes.

--- 

## Storage
- Slimbook One mini-pc with 1TB SSD at start which will run Proxmox.
- External USB-C Kingston 4TB SSD as extra cold storage
- Synology NAS

Setup in datacenter storage menu
- Needs also a content, so Proxmox now what to store
- Storage for backup files, iso images and container templates
- Storage for VM disk images, containers
- Use a NFS drive on a NAS (also for external disk?)

Setup according to Copilot
- Proxmox host disk (Slimbook) for OS and VM-disks (hot storage = fast) -> ZSF File system
- External SSD for weekly full backups off everything and extra cold storage (slower) but SSD is also very fast). -> LVM-thin
- NAS for daily imcremental backups with NFS-share -> Independent off ZSF or LVM-thin
- When a VM needs more data, link with NAS through NFS/iSCSI -> Independent off ZSF or LVM-thin

File system options:
- ZFS : Uses 1GB RAM for 1TB disk storage. Most realible for snapshots with better integrity.
- LVM-thin : Lightweight, lesser overhead but also lesser features. Good when you don't need ZFS.

My setup for now:
- Slimbook 1TB SSD (ZFS) for active VM-disks.
- External SSD (ext4) for weekly full backups. Safer because ZFS does not like I/O errors which could happen with an USB-drive.
- NAS (LAN) for daily snapshots

Format external SSD to `ext4`:
Connect to Slimbook and then with the console from within Proxmox:
```bash
lsblk                           # Check which device ex.: /dev/sdb
mkfs.ext4 /dev/sdb              # Format drive to ext4
mkdir /mnt/backupssd            # Make a directory for backups
mount /dev/sdb /mnt/backupssd   # Mount the disk to the directory
```


--- 

## Backup jobs
Backing up everything is VERY IMPORTANT!
- Setup with notifications.
- Setup retention, no need to keep the older ones
- Setup retention to limit amount off backups
- 

Backup options
- Stop mode: VM will be stopped and then a fill consisten backup taken
- Suspend mode: VM will be paused, then a backup from memory and disk are taken
- Snapshot mode: Most used because VM stays active. Not all filesystems allow this (LVM-thin or ZFS)

--- 

## Enable PCI passthrough
To virtualise effective hardware to a VM like a network card, GPU, USB-controller. Instead of an emulation, the VM will see the actual hardware. 
This is done by IOMMU (Intel VT-d or AMD-Vi) who regulate the isolation and mapping off hardware to VM's
Usefull for better performance, special hardware for gaming/AI-workloads, USB-dongles and network cards for firewalls.
Only possible when activated through BIOS/UEFI and supported by CPU. 

You can check this from Proxmox if it is enabled but not really needed in my case here.

--- 

## Email notifications
Proxmox uses Postfix to send emails and because Debian is it's backbone, you can also use this service to send notifications from Debian itself. For example when you install fail2ban as extra protection.

---

## YouTube Tutorials
Link: [Don’t run Proxmox without these settings!](https://www.youtube.com/watch?v=VAJWUZ3sTSI&list=WL&index=22&t=31s)

VM Best practices (according to tutorial)
- When creating a new VM and selected the OS, make sure to select the correct OS-type
- For Windows select the correct version and also the VirtIO Drivers. These need to be downloaded from the Proxmox website and also added as an ISO-Image under Guest OS. So you will have 2x ISO's. Under "Use CD/DVD.." and under "Guest OS" when Windows is selected. Windows does not recognise the virtual drivers by default.
- Under System:
  - As SCSI Controller select VirtIO SCSI single, this is newer and faster then IDE/SATA, single is less overhead.
  - Select the Qemu Agent. For better integration: shutdown/reboot from Proxmox, IP's reporting, etc...
  - For Windows also select TPM. Windows 11 requires TMP 2.0, Proxmox offers a virtual TPM.
  - If using a graphics card also select VirtIO-GPU. Not needed for headless servers.
  - For machine: "q35" is the newer (PCI support), default "i440fx" is the older one (for older machines). With a new OS, always q35
  - For BIOS: "OVMF (UEFI)" is the newer one, default "seaBIOS" is the older one. Only seaBIOS for an OS that does not know UEFI.
- Under network:
  - Setup bridge
  - Model: VirtIO. This is faster then the emulation of an Intel E1000 or Realtek. Windows also needs VirtIO drivers for this. Linux does not.
 
--- 


