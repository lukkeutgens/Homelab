# Proxmox Learning
Some notes about installing and setting up 

## Questions that came up
- How to connect remotely to Proxmox (LAN only)? Use the web interface: https://ipaddress:8006
- How to set up security (hardening, auto-updates, etc.)? Apply OS hardening as you would on Debian.
- The Proxmox firewall only protects the host itself, not the VMs, unless you configure rules on the bridge.
- Enable notifications: go to "Datacenter → Notifications" to configure email alerts.
- Other questions I had are answered below.

--- 

## Configure Updates
Set up the repositories correctly to receive the latest security updates:
- The Enterprise repository requires a paid subscription (disabled in my setup).
- Add the **No-Subscription** repository.
- Ceph Quincy or Ceph Reef are intended for three-node clusters (not applicable for me at the moment).
- Keep the Debian repositories enabled because Proxmox is built on Debian.
- Sometimes nodes need to be rebooted after updates.

--- 

## Trusted TLS Certificates
Proxmox Wiki: [Proxmox Wiki Certificate Management](https://pve.proxmox.com/wiki/Certificate_Management)

I plan to use ACME, but first I need to install a DNS server container as well as VMs with a reverse proxy and Step CA.  
For now, I will use a self-signed certificate because Proxmox will either be accessible through the internet or placed behind a VPN. In the end, I may not actually need direct internet access.

### Notes from Copilot
- Proxmox has built-in `ACME` integration with Let's Encrypt. This allows Proxmox to request free **trusted TLS certificates** and automatically renew them when you have a domain that resolves to the Proxmox node and configure the correct DNS or HTTP validation.
- Let's Encrypt is a widely trusted CA that provides free SSL/TLS certificates worldwide.
- Use the **DNS-01 challenge** so you don’t need to expose port 80 to the internet.
- Proxmox ACME can only manage its own certificates and cannot issue them for VMs running on it. Use a reverse proxy service (running in a VM) with ACME/Let’s Encrypt to handle certificates for underlying services and other VMs.

### Notes from Tutorials
- Best practice is to use a domain name and a DNS provider.
- Configure a DNS host for Proxmox (though I prefer to keep Proxmox LAN-only).
- **Datacenter → ACME**: integrates with Let’s Encrypt and can be set up with providers like Cloudflare.
- Add certificates for each Proxmox node (using DNS challenge type).
- Configure both a cluster DNS name and individual node DNS names. Use a DNS server with health checks when referencing the cluster name.
- A Proxmox cluster can have a maximum of 3 nodes.

--- 

## User Management
- Proxmox Wiki: [Proxmox User Management](https://pve.proxmox.com/wiki/User_Management)
- Tutorial: [Proxmox VE Made Easy – Complete Training Series (Part 9 - User Management)](https://www.youtube.com/watch?v=frnILOGmATs) 
- Tutorial: [How to create users and set permissions in Proxmox](https://www.youtube.com/watch?v=DLh_j1CAj44)

Users are linked to realms in Proxmox. Pam-users are root users, so use this as little as possible. Setup normal Proxmox users with the necessary permissions they need. Also link them with an AIM-service (Access & Identity Management) for best practices.

### Notes from Tutorials
- Standard there are 2 Realms:
  - `Linux PAM (Plug-in Authentication Module) standard authentication` : Linux login and authentication system. PAM is not synec across clusters. The default root user sits in PAM. Passwords need to be locally managed on these systems.
  - `Proxmox VE authentication server` : Proxmox own login system. Works best if you have multiple Proxmox systems because it's synced across clusters.
- You can add the following realms to Proxmox:
  - `Active Directory Server` : User management system from Microsoft Windows
  - `LDAP Server` : Lightweight Directory Access Protocol like OpenLDAP.
  - `OpenID Connect Server`: OpenID Connect (OIDC) is an authentication protocol built on top of the OAuth 2.0 framework that verifies user identities for access to protected endpoints. 
- Users created under the Realm `Linux PAM standard authentication` are NOT added to the Linux user system (Debian).
- If you go into the Linux Shell from Proxmox, you are automatically logged in as `root@servername`.

 

---

## Storage
- Slimbook One mini-PC with a 1TB SSD to run Proxmox.
- External USB-C Kingston 4TB SSD for additional cold storage.
- Synology NAS.

### Datacenter Storage Menu Setup
- Each storage entry must also define a content type so Proxmox knows what to store.
- Storage can be used for backup files, ISO images, and container templates.
- Storage can also be used for VM disk images and containers.
- Use an NFS share on the NAS (possible for the external disk as well).

### Setup According to Copilot
- **Proxmox host disk (Slimbook)**: OS and VM disks (hot storage = fast) → ZFS filesystem.
- **External SSD**: weekly full backups of everything and extra cold storage (slower, but still fast as SSD) → LVM-thin.
- **NAS**: daily incremental backups via NFS share → independent of ZFS or LVM-thin.
- When a VM requires more storage, connect it to the NAS via NFS/iSCSI → independent of ZFS or LVM-thin.

### Filesystem Options
- **ZFS**: Uses ~1GB RAM per 1TB of disk storage. Most reliable for snapshots with strong data integrity.
- **LVM-thin**: Lightweight, less overhead but fewer features. Suitable when ZFS is not required.

### My Planned Setup
- Slimbook 1TB SSD (ZFS or LVM-thin?) for active VM disks.
- External SSD (ext4) for weekly full backups. Safer because ZFS does not handle I/O errors well, which can occur with USB drives.
- NAS (LAN) for daily snapshots.

### Format External SSD to `ext4`
Connect the SSD to the Slimbook and format it via the Proxmox console:
```bash
lsblk                           # Check which device ex.: /dev/sdb
mkfs.ext4 /dev/sdb              # Format drive to ext4
mkdir /mnt/backupssd            # Make a directory for backups
mount /dev/sdb /mnt/backupssd   # Mount the disk to the directory
```

--- 

## Backup jobs
Backing up everything is **VERY IMPORTANT**!
- Configure backups with notifications enabled.
- Set up retention policies; there is no need to keep very old backups.
- Limit the number of backups stored to avoid unnecessary disk usage.

### Backup Options
- **Stop mode**: The VM will be stopped and then a fully consistent backup will be taken.
- **Suspend mode**: The VM will be paused, and a backup of both memory and disk will be created.
- **Snapshot mode**: The most commonly used option because the VM remains active. Not all filesystems support this (only LVM-thin or ZFS).

--- 

## Enable PCI Passthrough
PCI passthrough allows assigning physical hardware directly to a VM, such as a network card, GPU, or USB controller.  
Instead of emulated hardware, the VM interacts with the actual device.

This is achieved through **IOMMU** (Intel VT-d or AMD-Vi), which manages the isolation and mapping of hardware to VMs.  
It is useful for:
- Better performance.
- Special hardware for gaming or AI workloads.
- USB dongles.
- Network cards for firewall VMs.

Requirements:
- Must be enabled in BIOS/UEFI.
- Supported by the CPU.

You can check from within Proxmox if IOMMU is enabled, but in my case this is not strictly necessary.
I do not plan to use it for now.

--- 

## Email notifications
Proxmox uses Postfix to send emails. Since Debian is its backbone, you can also use this service to send notifications directly from Debian itself. For example, Fail2ban can use Postfix to notify you when it blocks suspicious activity.

---

## YouTube Tutorials
Link: [Don’t run Proxmox without these settings!](https://www.youtube.com/watch?v=VAJWUZ3sTSI&list=WL&index=22&t=31s)

### VM Best Practices (according to tutorial)
- When creating a new VM and selecting the OS, make sure to choose the correct OS type.
- For Windows, select the correct version and also add the VirtIO drivers. These need to be downloaded from the Proxmox website and mounted as an ISO image under Guest OS. You will therefore have two ISOs: one under **Use CD/DVD** and one under **Guest OS**. Windows does not recognize the virtual drivers by default.
- **System settings:**
  - **SCSI Controller:** Select VirtIO SCSI single. This is newer and faster than IDE/SATA, with less overhead.
  - **Qemu Agent:** Enable for better integration (shutdown/reboot from Proxmox, IP reporting, etc.).
  - **TPM:** Required for Windows 11 (TPM 2.0). Proxmox provides a virtual TPM.
  - **VirtIO-GPU:** Select if using a graphics card. Not needed for headless servers.
  - **Machine type:** “q35” is newer (PCIe support). “i440fx” is older (legacy PCI). Always use q35 for modern OSes.
  - **BIOS:** “OVMF (UEFI)” is newer. “seaBIOS” is older and should only be used for OSes that do not support UEFI.
- **Network settings:**
  - Configure a bridge.
  - **Model:** VirtIO. This is faster than emulating an Intel E1000 or Realtek NIC. Windows requires VirtIO drivers for this, while Linux supports it natively.
 
--- 


