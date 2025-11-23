# Homelab Architecture
An overview of my homelab architecture. This document is a work in progress and will be updated as the setup evolves.

---

## 1. Physical Infrastructure
My physical devices (modem, gateway, hypervisor, NAS, etc.).

| Device                   | Role/Function           | IP Address      | Notes                           |
| :---                     | :---                    | :---            | :---                            |
| ISP Fiber Modem          | Internet uplink         | n/a             | Bridge mode / passthrough       |
| Asus ZenWifi BQ16        | Gateway, DHCP, WiFi     | 192.168.50.1    | Firewall, AIProtection          |
| Slimbook One Mini-PC     | Proxmox Hypervisor      | 192.168.50.157  | pve01.homelab.local (16 Threads, 32GB RAM, 1TB SSD) |
| Synology NAS             | Storage                 | 192.168.50.149  | Daily snapshots                 |
| Kingston XS2000 4TB SSD  | Storage (External SSD)  | n/a             | Weekly full backups             |

---

## 2. Containers
> I still need to set these up

| Name          | Role/Service    | FQDN                 | IP Address      | Cert Source    | Notes                 |
| :---          | :---            | :---                 | :---            | :---            | :---                  |
| dns01         | Technitium DNS  | dns01.homelab.local  | 192.168.50.xxx  | Step CA         | Internal DNS resolver |

> Note: DNS is not behind the reverse proxy, because DNS queries do not use HTTP/HTTPS but their own protocols (UDP/TCP port 53). Only the web interface could be proxied, not the resolver itself.

---

## 3. Virtual Machines
> I still need to set these up

| VM Name    | Role/Service           | FQDN                | IP Address      | Cert Source      | Notes                            |
| :---       | :---                   | :---                | :---            | :---             | :---                             |
| proxy01    | Reverse Proxy (Caddy)  | proxy01.public.net  | 10.0.0.10       | Let's Encrypt    | Entry point for homelab services |
| ca01       | Step CA                | ca01.homelab.local  | 10.0.0.11       | Step CA          | Internal PKI                     |
| auth01     | Authentik AIM          | auth01.public.net   | 10.0.0.12       | Let's Encrypt    | Public authentication service    |

---

## 4. Domains and Certificates
> public.net is just a placeholder for my real domain name, which I'm not documenting on GitHub.

| Domain         | Scope            | Cert Source      | Managed By         |
| :---           | :---             | :---             | :---               | 
| homelab.local  | Internal only    | Step CA          | Technitium DNS     |
| public.net     | Public services  | Let's Encrypt    | Registrar + Caddy  | 

---

## 5. Network
Basically there is the local LAN-network regulated by the Asus ZenWifi, and there is the proxy subnet where all services from the homelab will run except Proxmox and the DNS server.

| Segment        | CIDR             | Gateway        | Notes                                      |
| :---           | :---             | :---           | :---                                       |
| LAN            | 192.168.50.0/24  | 192.168.50.1   | DHCP By Asus ZenWifi                       |
| Proxy subnet   | 10.0.0.0/24      | 192.168.50.1   | all web traffic in 10.0.0.x is routed through proxy01 for TLS termination. |
| Internet       | ISP Uplink       | Fiber modem    | AIProtection & Firewall active on gateway  |

---


