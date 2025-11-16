# Architecture

## Software Stack

| Device        | Software                | Description                                          | Link                                                                                  |
| :---          | :---                    | :---                                                 | :---                                                                                  |
| Slimbook One  | Proxmox                 | Hypervisor for running VM's                          | [Website](https://www.proxmox.com/en/products/proxmox-virtual-environment/overview)   |
| Container     | DNS-Server              | To research which one                                  |          |
| VM          | Caddy Reverse Proxy    | Reverse proxy to isolate homelab services & VM's     | [Website](https://nginxproxymanager.com/), [Github](https://github.com/NginxProxyManager/nginx-proxy-manager) |
| VM          | Step CA                 | Internal Certificate Manager for services & devices  | [Website](https://smallstep.com/docs/step-ca/)   |
| VM          | Authentik               | Authentication & Identity Management (AIM)           | [Website](https://goauthentik.io)                |
| ???           | Cockpit                 | Web based server management                          | [Website](https://cockpit-project.org/), [Github](https://github.com/cockpit-project/cockpit)  |
| ???           | Portainer CE            | Manage Containers (Docker, Kubernetes, ...)          | [Github](https://github.com/portainer/portainer) |

---

## DNS-Server
Some information on how to setup the DNS-server. I still need to research wich service I will use.

### Notes
- DNS-server to run on the local LAN network for internal and internet use. For example, Proxmox need's this to be able to connect to the Step CA service which will be behind a reverse proxy.
- This service can run as a container in Proxmox because it is not exposed to the internet like the services we will run behind a reverse proxy.
- The DNS-server must be in my local LAN and NOT behind the reverse proxy
- My Asus Zenwifi will distribute the DNS-server to the LAN-clients

### Possible software
All open-source (free) and ACME compatible (renewing certificates)
- [AdGuard Home Github](https://github.com/AdguardTeam/AdGuardHome) : More modern UI, more options but heavier
- [Pi‑hole / Unbound](https://docs.pi-hole.net/) : Lightweight with Web-UI. Primarly focus on adblocking
- [CoreDNS](https://coredns.io/) : 
- [technitium](https://technitium.com/dns/) : Full DNS-server with modern UI, support for DNSSEC, DoH/DoT and caching. More control then Pi-hole but also lightweight.

### Flow example
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

## Reverse Proxy
I will setup a reverse proxy as an extra security step in my homelab. All VM's will run behind this proxy.

### Notes
- Must run as VM for better isolation.
- Must have health checks to see if nodes are online
- Must be compatible with Step CA (ACME-endpoint), self-signed and from the net
- Direct installable in Debian is a big plus. So no need for container software.

### Software to consider:
- [NGINX Proxy Manager](https://nginxproxymanager.com/) : Well known, but no health checks of nodes
- [HAProxy](https://www.haproxy.org/) : Robust load balancer with health checks
- [Traefik](https://traefik.io/traefik) : Modern reverse proxy with service discovery and health checks
- [Caddy](https://caddyserver.com/docs/quick-starts/reverse-proxy) : Modern webserver/reverse proxy with Let's encrypt built in

---

## Software to check
- [Keycloak](https://www.keycloak.org/) or [Authentik](https://goauthentik.io/) : Authentication & Identity Management (AIM) service
- [Vaultwarden/Server](https://github.com/dani-garcia/vaultwarden) : Password manager server software
- [Portainer Business](https://www.portainer.io/) or [Portainer CE](https://github.com/portainer/portainer) : Business is free for max 5 nodes, CE is always free but no SSO via OIDC
- [OpenObserve](https://github.com/openobserve/openobserve) : For monitoring servers (Link with Authentik with [Dex](https://github.com/dexidp/dex) as SSO-bridge)

## Other things to check

### Domain Names
Pricing are not really clear presented. At Easyhost it is shown €0,49 but then jumps to €2,99 a year? So I need to study these websites further.
- [Let's Encrypt](https://letsencrypt.org/) : For public domain names, to link up with the Proxy on the WAN side?
- [Combell](https://www.combell.com/nl/domeinnamen) : €2,99 a year
- [EasyHost](https://www.easyhost.be/nl/domeinnaam-kopen) : €2,99 a year

### Dynamic DNS
- [Cloudflare](https://www.cloudflare.com/) : Free dynamic DNS
- [Duck DNS](https://www.duckdns.org/) : Free dynamic DNS hosted on AWS?
- [No-IP](https://www.noip.com/) : Free dynamic DNS
- [Dynu](https://www.dynu.com/) : Free dynamic DNS
- [Vimexx](https://www.vimexx.be/) : Not free, but cheap dynamic DNS

### Certificate Management
- Step CA for the internal-only services like Cockpit for server management.
- Step CA uses also ACME for automatic renewal off certificates wite Step CLI agent. Step CA is a ACME server.
- Online for services that will be exposed to the internet like a VPN and Authentik

Online services for certificates:
I've looked for a European alternative for free certificate management but as for now, there are none!
[Let's Encrypt](https://letsencrypt.org/) : Free best known service, thrusted world-wide

---

## Notes for Cockpit
Can not simple be connected with Authentik. This needs to be done with NGINX Proxy manager

https://cockpit-project.org/guide/latest/authentication

---

## Some Keywords to Remember
- **SAML** : Security Assertion Markup Language - Open standard for Single Sign-On (SSO) and identity federation. Used to authenticate multiple users with multiple services through a central identity provider (Authentik).
- **ACME** :


