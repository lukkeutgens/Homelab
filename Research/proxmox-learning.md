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

- Best to use a domain name and DNS-provider.
- DNS Host for Proxmox (not sure about this, I want to keep the Proxmox itself only LAN accessible)
- Datacenter -> ACME : Uses Let's Encrypt and can be used to setup the online certificates like Cloudflare
- Add the certificates for each proxmox node (use DNS challenge Type?)
- 



### Other settings
- Enable notifications: "datacenter" -> "notifications"
- 

--- 

## YouTube Tutorials
- [Don’t run Proxmox without these settings!](https://www.youtube.com/watch?v=VAJWUZ3sTSI&list=WL&index=22&t=31s)
- 
