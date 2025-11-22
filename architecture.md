# Homelab Architecure
An overview off my homelab architecture.
It's a work in progress and I will update everything as I proceed.


---

## 1. Physical Infrastructure
My physical devices (modem, gateway, hypervisor, NAS, etc.).

| Device                | Role/Function        | IP Address      | Notes                           |
| :---                  | :---                 | :---            | :---                            |
| ISP Fiber Modem       | Internet uplink      | n/a             | Brigde mode / passthrough       |
| Asus ZenWifi BQ16     | Gateway, DHCP, WiFi  | 192.168.50.1    | Firewall, AIProtection          |
| Slimbook One mini-pc  | Proxmox Hypervisor   | 192.168.50.157  | Main node (pve01.homelab.local  |

---

## 2. Containers
For lightweight services such as DNS and management tools.
> I still need to set these up

| Name          | Role/Service    | FQDN                 | IP Address      | Cert. Source    | Notes                 |
| :---          | :---            | :---                 | :---            | :---            | :---                  |
| dns01         | Technitium DNS  | dns01.homelab.local  | 192.168.50.xxx  | Step CA         | Internal DNS resolver |

---

## 3. Virtual Machines
Used virtual machines in the homelab
> I still need to set these up

| VM Name    | Role/Service           | FQDN                | IP Address      | Cert Source      | Notes                           |
| :---       | :---                   | :---                | :---            | :---             | :---                            |
| proxy01    | Reverse Proxy (Caddy)  | proxy01.public.net  | 10.0.0.10       | Let's Encrypt    | Entry point for public services |
| ca01       | Step CA                | ca01.homelab.local  | 10.0.0.11       | Step CA          | Internal PKI                    |
| auth01     | Authentik AIM          | auth01.public.net   | 10.0.0.12       | Let's Encrypt    | Public login service            |

---

## 4. Domains and Certificates
Separation between internal and external domains. 
> public.net is just a placeholder for my real domain name, which I'm not documenting on GitHub.

| Domain         | Scope            | Cert Source      | Managed By         |
| :---           | :---             | :---             | :---               | 
| homelab.local  | Internal only    | Step CA          | Technitium DNS     |
| public.net     | Public services  | Let's Encrypt    | Registrar + Caddy  | 

---

## 5. Network
Basically there is the local LAN-network regulated by the Asus ZenWifi, and there is the proxy subnet where all services from the homelab will run except Proxmox and the DNS server.

| Segment        | CIDR             | Gateway        | DNS             | Notes                                      |
| :---           | :---             | :---           | :---            | :---                                       |
| LAN            | 192.168.50.0/24  | 192.168.50.1   |                 | DHCP By Asus ZenWifi                       |
| Proxy subnet   | 10.0.0.0/24      | proxy01        |                 | All services behind reverse proxy          |
| Internet       | ISP Uplink       | Fiber modem    |                 | AIProtection & Firewall active on gateway  |

---


