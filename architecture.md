# Homelab Architecure

## 1. Physical Infrastructure
My physical devices (modem, gateway, hypervisor, NAS, etc.).

| Device                | Role/Function        | IP Address      | Notes                           |
| :---                  | :---                 | :---            | :---                            |
| ISP Fiber Modem       | Internet uplink      | n/a             | Brigde mode / passthrough       |
| Asus ZenWifi BQ16     | Gateway, DHCP, WiFi  | 192.168.50.1    | Firewall, AIProtection          |
| Slimbook One mini-pc  | Proxmox Hypervisor   | 192.168.50.157  | Main node (pve01.homelab.local  |


## 2. Containers
For lightweight services such as DNS and management tools.
> I still need to set these up

| Name          | Role/Service    | FQDN                 | IP Address      | Cert. Source    | Notes                 |
| :---          | :---            | :---                 | :---            | :---            | :---                  |
| dns01         | Technitium DNS  | dns01.homelab.local  | 192.168.50.xxx  | Step CA         | Internal DNS resolver |

## 3. Virtual Machines
Used virtual machines in the homelab
> I still need to set these up
> 
| VM Name        | Role/Service           | FQDN                | IP Address      | Cert Source      | Notes                           |
| :---           | :---                   | :---                | :---            | :---             | :---                            |
| proxy01        | Reverse Proxy (Caddy)  | proxy01.public.net  | 192.168.50.xxx  | Let's Encrypt    | Entry point for public services |
| ca01           | Step CA                | ca01.homelab.local  | 

