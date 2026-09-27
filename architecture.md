# Homelab Architecture

An overview of my homelab architecture. This document is a work in progress and will be updated as the setup evolves.

---

## 1. Physical Infrastructure

My physical devices (modem, gateway, hypervisor, NAS, etc.).

| Device                  | Role/Function          | IP Address     | Notes                                               |
|:----------------------- |:---------------------- |:-------------- |:--------------------------------------------------- |
| ISP Fiber Modem         | Internet uplink        | n/a            | Bridge mode / passthrough                           |
| Asus ZenWifi BQ16       | Gateway, DHCP, WiFi    | 192.168.50.1   | Firewall, AIProtection                              |
| Slimbook One Mini-PC    | Proxmox Hypervisor     | 192.168.50.157 | pve01.homelab.local (16 Threads, 32GB RAM, 1TB SSD) |
| Synology NAS            | Storage                | 192.168.50.149 | Daily snapshots                                     |
| Kingston XS2000 SSD 4TB | Storage (External SSD) | n/a            | Weekly full backups                                 |

---

## 2. Naming Convention

All Proxmox nodes (LXC containers and VM's) follow `<3-letter role code><2-digit sequence>`, e.g. `DNS01`. The sequence number allows for future scaling (e.g. a second DNS resolver would become `DNS02`).

| Code | Meaning                        |
|:---- |:------------------------------ |
| DNS  | DNS server                     |
| PRX  | Reverse Proxy                  |
| PKI  | Internal Certificate Authority |
| IAM  | Identity & Access Management   |
| DCK  | Docker host                    |
| CKP  | Cockpit (host/node management) |

---

## 3. Containers (LXC — directly on Proxmox)

> I still need to set these up

Services that need no isolation beyond what Proxmox's own LXC layer provides run directly as LXC containers on the hypervisor.

| Name  | Role/Service   | FQDN                | IP Address     | Cert Source | Notes                                                                                                                 |
|:----- |:-------------- |:------------------- |:-------------- |:----------- |:--------------------------------------------------------------------------------------------------------------------- |
| DNS01 | Technitium DNS | dns01.homelab.local | 192.168.50.xxx | Step CA     | Internal DNS resolver                                                                                                 |
| CKP01 | Cockpit        | ckp01.homelab.local | 192.168.50.xxx | Step CA     | Server management — LAN-only, restricted source IP's, native install (not containerized: manages the host it runs on) |

> Note: DNS stays on the LAN, not behind the reverse proxy, because DNS queries do not use HTTP/HTTPS but their own protocols (UDP/TCP port 53). Only the web interface could be proxied, not the resolver itself.
> 
> Note: Cockpit stays on the LAN as well, not behind the reverse proxy and not exposed on `public.net`. It manages other nodes via SSH (cockpit-bridge), independent of Caddy/HTTP — reachable regardless of whether the target node sits on the LAN or the proxy subnet, as long as the firewall allows SSH between segments. Deliberately kept on its own node, not on PRX01/PKI01/IAM01/DCK01, so a compromise of Cockpit's SSH access doesn't also hand over the CA, identity store, or any other high-value node. Not placed behind Authentik (IAM01) either — the added protection against a stolen local password was judged smaller than the risk of being locked out of Cockpit if Authentik itself goes down.

---

## 4. Virtual Machines

> I still need to set these up

Services that need full kernel-level isolation from the Proxmox host run as VM's on the proxy subnet.

| VM Name | Role/Service          | FQDN                | IP Address | Cert Source   | Notes                                                                   |
|:------- |:--------------------- |:------------------- |:---------- |:------------- |:----------------------------------------------------------------------- |
| PRX01   | Reverse Proxy (Caddy) | prx01.public.net    | 10.0.10.10 | Let's Encrypt | Entry point for homelab services                                        |
| PKI01   | Step CA               | pki01.homelab.local | 10.0.10.11 | Step CA       | Internal PKI                                                            |
| IAM01   | Authentik AIM         | iam01.public.net    | 10.0.10.12 | Let's Encrypt | Public authentication service                                           |
| DCK01   | Docker host (Debian)  | dck01.homelab.local | 10.0.10.13 | Step CA       | Runs Docker + Portainer CE; hosts lower-isolation-need services (below) |

## 5. Docker containers on DCK01

Services with no need for their own VM-level isolation run as Docker containers inside `DCK01`, managed via Portainer CE. They sit behind `PRX01` the same as any other backend on the proxy subnet.

| Container     | Role/Service            | Notes                                                    |
|:------------- |:----------------------- |:-------------------------------------------------------- |
| Portainer     | Container management UI | Web UI for managing all containers on DCK01              |
| LubeLogger    | Vehicle maintenance log | Migrating from Synology NAS                              |
| HomeAssistant | Home automation         | Migrating from Synology NAS                              |
| MQTT Broker   | Message broker          | Candidate still to be chosen (EMQX / HiveMQ / Mosquitto) |

---

## 6. Domains and Certificates

> public.net is just a placeholder for my real domain name, which I'm not documenting on GitHub.

| Domain        | Scope           | Cert Source   | Managed By        |
|:------------- |:--------------- |:------------- |:----------------- |
| homelab.local | Internal only   | Step CA       | Technitium DNS    |
| public.net    | Public services | Let's Encrypt | Registrar + Caddy |

---

## 7. Network

Basically there is the local LAN-network regulated by the Asus ZenWifi, and there is the proxy subnet where all services from the homelab will run except Proxmox and the DNS server.

| Segment      | CIDR            | Gateway      | Notes                                                                     |
|:------------ |:--------------- |:------------ |:------------------------------------------------------------------------- |
| LAN          | 192.168.50.0/24 | 192.168.50.1 | DHCP by Asus ZenWifi                                                      |
| Proxy subnet | 10.0.10.0/24    | 192.168.50.1 | All web traffic in 10.0.10.x is routed through PRX01 for TLS termination. |
| Internet     | ISP Uplink      | Fiber modem  | AIProtection & Firewall active on gateway                                 |

---
