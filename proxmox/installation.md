# Proxmox Installation
Documenting my steps after first booting and installing Proxmox on my Slimbook One mini-pc.

## Install steps
Installing is actually very easy. I used the graphical installer.
1. Accept the licence blabla
2. Setup hard-drive settings
3. Enter password and email adress
4. Setup the network interface. Be aware, you need a full domain name. For example: pve.homelab.local
5. Let installer do it's thing

After installation you can login using the user `root` with the set password:
- On server console to login to Debian Host OS
- With a web browser to the internet adress + port 8006. For example: https://192.168.0.150:8006

## Post install steps
Steps to take after the first installation

### 1. Repositories & Update
I don't have an enterprise subscription so I need to change the repositories to get the latest updates. These are found under: `Datacenter -> node -> Updates -> Repositories`.

Change the repositories:
- Disable `https://enterprise.poxmox.com/debian/ceph-squid`
- Disable `https://enterprise.proxmox.com/deboan/pve`
- Add the **No subscription** repository `http://download.proxmox.com/debian/pve`

Go back to Updates and then above you can click `refresh`. Proxmox will then read the new repositories for updates.
When done click `>... Upgrade` and a console will open to update the full Proxmox node. Sometimes you will need to reboot. Close the console and reboot from the Proxmox-UI.



