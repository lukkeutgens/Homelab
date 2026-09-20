Research which services to use and how they are linked up with each other

## Software Stack
Overview off the services choosen.

| Device       | Software            | Description                                                                    | Link                                                                                                          |
| :----------- | :------------------ | :----------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------ |
| Slimbook One | Proxmox             | Hypervisor for running VM's                                                    | [Website](https://www.proxmox.com/en/products/proxmox-virtual-environment/overview)                           |
| Container    | Technitium DNS      | DNS-server as container in Proxmox                                             | [Website](https://technitium.com/dns/)                                                                        |
| VM           | Caddy Reverse Proxy | Reverse proxy to isolate homelab services & VM's, and can handle Let's Encrypt | [Website](https://nginxproxymanager.com/), [Github](https://github.com/NginxProxyManager/nginx-proxy-manager) |
| VM           | Step CA             | Internal Certificate Manager for services & devices                            | [Website](https://smallstep.com/docs/step-ca/)                                                                |
| VM           | Authentik           | Authentication & Identity Management (AIM)                                     | [Website](https://goauthentik.io)                                                                             |
| ???          | Cockpit             | Web based server management                                                    | [Website](https://cockpit-project.org/), [Github](https://github.com/cockpit-project/cockpit)                 |
| ???          | Portainer CE        | Manage Containers (Docker, Kubernetes, ...)                                    | [Github](https://github.com/portainer/portainer)                                                              |
|              |                     |                                                                                |                                                                                                               |

---
## External Public Services
Services we are going to use in the cloud for a secure setup off the homelab

| Service                                                                                                                 | Name                                      | Used By        | Description                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------------- | ----------------------------------------- | :------------- | :----------------------------------------------------------------------------------------------------------------------------------- |
| [[Research/Architecture#Role 1 — Recursive Resolver (Security / Filtering)\|Recursive DNS Resolver]]                    | [dns0.eu](https://www.dns0.eu)            | Technitium DNS | Blocks malicious, phishing, and tracking domains before any device can ever connect to them. This protects **outbound** traffic.     |
| [[Research/Architecture#Role 2 — Authoritative DNS Hosting (ACME / Certificate Validation)\|Authoritative DNS Hosting]] | [deSEC.io](https://desec.io)              | Caddy          | Hosts the DNS zone of the own domain, with an API Caddy uses to prove domain ownership (DNS-01 validation) without opening any ports |
| [[Research/Architecture#Certificate Management\|Trusted Certificates]]                                                  | [Let's Encrypt](https://letsencrypt.org/) | Caddy          | Provides trusted online certificates                                                                                                 |

---
## DNS-Server
Some information on how to setup the DNS-server. I still need to research wich service I will use.
#### Notes
- DNS-server to run on the local LAN network for internal and internet use. For example, Proxmox need's this to be able to connect to the Step CA service which will be behind a reverse proxy.
- This service can run as a container in Proxmox because it is not exposed to the internet like the services we will run behind a reverse proxy.
- The DNS-server must be in my local LAN and NOT behind the reverse proxy
- My Asus Zenwifi will distribute the DNS-server to the LAN-clients
#### Possible software
All open-source (free) and ACME compatible (renewing certificates)
- [AdGuard Home Github](https://github.com/AdguardTeam/AdGuardHome) : More modern UI, more options but heavier
- [Pi‑hole / Unbound](https://docs.pi-hole.net/) : Lightweight with Web-UI. Primarly focus on adblocking
- [CoreDNS](https://coredns.io/) : 
- [Technitium](https://technitium.com/dns/) : Full DNS-server with modern UI, support for DNSSEC, DoH/DoT and caching. More control then Pi-hole but also lightweight.

For now the selection is **Technitium**.
#### Flow example
An example how Proxmox will renew it's internal certificate through ACME
```text
[Proxmox Host] 192.168.50.150
   │  (ACME client requests cert at stepca.homelab.lan)
   ▼
[DNS-server container] 192.168.50.151
   │  (resolves stepca.homelab.lan → 192.168.50.152)
   ▼
[Reverse Proxy VM] 192.168.50.152
   │  (vhost: stepca.homelab.lan → backend 10.0.0.10:9000)
   ▼
[Step CA VM] 10.0.0.10
   │  (ACME provisioner processes request, gives back new certificate)
   ▼
[Reverse Proxy VM] 192.168.50.152
   │  (Sends answer back to Proxmox)
   ▼
[Proxmox Host] 192.168.50.150
   │  (Installs new TLS-certificate)
```

---
## Reverse Proxy
I will setup a reverse proxy as an extra security step in my homelab. All VM's will run behind this proxy.
#### Notes
- Must run as VM for better isolation.
- Must have health checks to see if nodes are online
- Must be compatible with Step CA (ACME-endpoint), self-signed and from the net
- Direct installable in Debian is a big plus. So no need for container software.

#### Software to consider:
- [NGINX Proxy Manager](https://nginxproxymanager.com/) : Well known, but no health checks of nodes
- [HAProxy](https://www.haproxy.org/) : Robust load balancer with health checks
- [Traefik](https://traefik.io/traefik) : Modern reverse proxy with service discovery and health checks
- [Caddy](https://caddyserver.com/docs/quick-starts/reverse-proxy) : Modern webserver/reverse proxy with Let's encrypt built in

For now added **Caddy** as reverse Proxy.

---
## Domain Names
Pricing are not really clear presented. At Easyhost it is shown €0,49 but then jumps to €2,99 a year? So I need to study these websites further.

- [Let's Encrypt](https://letsencrypt.org/) : For public domain names, to link up with the Proxy on the WAN side?
- [Combell](https://www.combell.com/nl/domeinnamen) : €2,99 a year
- [EasyHost](https://www.easyhost.be/nl/domeinnaam-kopen) : €2,99 a year

For now we added **Let's Encrypt** as certificate provider

---
## DNS Providers

There are actually **two separate roles** here that happen to share the term "DNS provider" but have nothing to do with each other. Keeping them apart avoids confusion.
#### Role 1 — Recursive Resolver (Security / Filtering)
This is the service that **Technitium** forwards to for every DNS query leaving the home network. This layer blocks malicious, phishing, and tracking domains before any device can ever connect to them. This protects **outbound** traffic.

| Option                                              | Origin                  | Notes                                                                                                                                |
| :-------------------------------------------------- | :---------------------- | :----------------------------------------------------------------------------------------------------------------------------------- |
| [Cloudflare (1.1.1.1)](https://www.cloudflare.com/) | US                      | Fast, free, no strong focus on malware blocking                                                                                      |
| [NextDNS](https://nextdns.io)                       | US/FR                   | Free tier, configurable blocklists, malware/tracker blocking                                                                         |
| [dns0.eu](https://www.dns0.eu/)                     | EU (France, non-profit) | European, GDPR-compliant, built-in malware/phishing blocking (via the `zero.dns0.eu` variant), founded by former NextDNS co-founders |
**Used by**: Technitium (as upstream forwarder)
**Why**: Technitium remains the local DNS server on the main LAN; this external resolver is simply the "backing" source Technitium forwards to for domains it can't resolve locally.
**Status**: still to choose between Cloudflare, NextDNS, and dns0.eu — dns0.eu is currently the strongest European candidate.

#### Role 2 — Authoritative DNS Hosting (ACME / Certificate Validation)
This is the service that hosts the **DNS zone of the own domain** (the A/CNAME/TXT records for e.g. `owndomain.be`), with an **API** that lets Caddy automatically add a temporary TXT record to prove ownership of the domain — this is called **DNS-01 validation**. This lets Let's Encrypt issue/renew a certificate **without ever needing to open port 80/443 to the public internet**.

**How it actually works**: Caddy never accepts an inbound connection from Let's Encrypt or the DNS provider. Instead, Caddy itself initiates two **outbound** connections: one to the DNS provider's API (to add the proof-of-ownership TXT record), and one to Let's Encrypt (to request the certificate). Let's Encrypt then checks the TXT record on its own, via the public DNS system — it never needs to reach back into the home network. The finished certificate is delivered to Caddy as the response to its own outbound request. Nothing needs to be reachable from the outside at any point.

| Option                                              | Origin                   | Notes                                                                        |
| :-------------------------------------------------- | :----------------------- | :--------------------------------------------------------------------------- |
| [Cloudflare](https://www.cloudflare.com/)           | US                       | Free, most widely used option, broad plugin support                          |
| [deSEC.io](https://desec.io/)                       | EU (Germany, non-profit) | Free, open source, full REST API, dedicated Caddy module (`caddy-dns/desec`) |
| [Hetzner DNS](https://www.hetzner.com/dns-console/) | EU (Germany)             | Free DNS API, Caddy support via community plugin                             |
**Used by**: Caddy (reverse proxy, ACME DNS-01 challenge)
**Why**: Caddy has Let's Encrypt built in, but the default validation method (HTTP-01) requires an open port 80. Using DNS-01 through a provider with an API instead keeps everything behind the firewall.
**Status**: leaning toward **deSEC.io** — European, non-profit, free, and has a ready-made Caddy plugin (no custom build needed beyond plugging in the module).
#### Important to remember
These two roles are chosen fully independently of each other — e.g. combining dns0.eu for Role 1 with deSEC.io for Role 2 is entirely possible, and is currently also the most likely choice.

---
## Certificate Management
- Step CA for the internal-only services like Cockpit for server management.
- Step CA uses also ACME for automatic renewal off certificates wite Step CLI agent. Step CA is a ACME server.
- Online for services that will be exposed to the internet like a VPN and Authentik

Online services for certificates:
I've looked for a European alternative for free certificate management but as for now, there are none!
[Let's Encrypt](https://letsencrypt.org/) : Free best known service, thrusted world-wide and can be handled by [Caddy](https://caddyserver.com/docs/quick-starts/reverse-proxy) 

---
## Backup Tools
To create a full backup from the Proxmox nodes, should everything fail.
- [Rescuezilla](https://rescuezilla.com/) : An open-source easy-to-use disk imaging app that's fully compatible with Clonezilla
- [TimeShift](https://github.com/linuxmint/timeshift) : Timeshift for Linux is an application that provides functionality similar to the System Restore feature in Windows
- [Clonezilla](https://clonezilla.org/) : Partition and disk imaging/cloning program similar to True Image® or Norton Ghost®.
- [FSArchiver](https://www.fsarchiver.org/) : System tool that allows you to save the contents of a file-system to a compressed archive file.

Notes:
- Rescuezilla can be installed on a bootable USB stick. Boot the PC from it to create a full backup to an external hard drive or similar storage like a NAS.
- Timeshift is primarily a snapshot tool for Debian configuration files. It cannot restore the entire system, it’s more suited for quick rollbacks after configuration mistakes.
- Clonezilla offers the same functionality as Rescuezilla but without a graphical interface.
- FSArchiver creates archives of disk partitions. It is not intended for bare-metal restores.

The chosen solution will be Rescuezilla on a USB stick, with the optional possibility of storing backups on a NAS.

---
## Software to check
- [Keycloak](https://www.keycloak.org/) or [Authentik](https://goauthentik.io/) : Authentication & Identity Management (AIM) service
- [Vaultwarden/Server](https://github.com/dani-garcia/vaultwarden) : Password manager server software
- [Portainer Business](https://www.portainer.io/) or [Portainer CE](https://github.com/portainer/portainer) : Business is free for max 5 nodes, CE is always free but no SSO via OIDC
- [OpenObserve](https://github.com/openobserve/openobserve) : For monitoring servers (Link with Authentik with [Dex](https://github.com/dexidp/dex) as SSO-bridge)

---
## Notes for Cockpit
Can not simple be connected with Authentik. This needs to be done with NGINX Proxy manager

https://cockpit-project.org/guide/latest/authentication

---
## Some Keywords to Remember
- **SAML** : Security Assertion Markup Language - Open standard for Single Sign-On (SSO) and identity federation. Used to authenticate multiple users with multiple services through a central identity provider (Authentik).
- **ACME** : Automatic Certificate Management Environment


