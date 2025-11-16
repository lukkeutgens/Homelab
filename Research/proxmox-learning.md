# Proxmox Learning
Some notes about Proxmox installing and setup

## Questions that come up
- How to remote connect to Proxmox (only LAN)?
- Setup security (Hardening, auto-updates, ... )
- Certificates (self-signed)
- Setup backups for Proxmox itself and all the running VM's

--- 

## Tips about settings
### Configure Updates
Setup the repositories correctly to get the newest (security) updates
- Enterprise repository is for a payed subscription (disabled it)
- Add the "No-Subscription" repository
- Ceph Quincy or Ceph Reef are for three node clusters (n.a. for me at this moment)
- Leave the debian repositories because Proxmox is built on Debian
- Sometimes the nodes need a reboot

### Trusted TLS Certificates
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

### Storage
- Slimbook One mini-pc with 1TB SSD at start which will run Proxmox.
- External USB-C Kingston 4TB SSD as extra cold storage
- Synology NAS

Setup in datacenter storage menu
- Needs also a content, so Proxmox now what to store
- Storage for backup files, iso images and container templates
- Storage for VM disk images, containers
- Use a NFS drive on a NAS (also for external disk?)

Setup according to Copilot
- Proxmox host disk (Slimbook) for OS and VM-disks (hot storage = fast)
- External SSD for weekly full backups off everything and extra cold storage (slower) but SSD is also very fast).
- NAS for daily imcremental backups with NFS-share
- When a VM needs more data, link with NAS through NFS/iSCSI.

### Backup jobs
Backing up everything is VERY IMPORTANT!


### Other settings
- Enable notifications: "datacenter" -> "notifications"
- 

--- 

## YouTube Tutorials
- [Don’t run Proxmox without these settings!](https://www.youtube.com/watch?v=VAJWUZ3sTSI&list=WL&index=22&t=31s)
- 
