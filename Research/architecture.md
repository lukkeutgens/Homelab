# Architecture

## Software Stack

| Device        | Software                | Description                                          | Link                                                                                  |
| :---          | :---                    | :---                                                 | :---                                                                                  |
| Slimbook One  | Proxmox                 | Hypervisor for running VM's                          | [Website](https://www.proxmox.com/en/products/proxmox-virtual-environment/overview)   |
| VM01          | NGINX Proxy Manager     | Reverse proxy to isolate homelab services & VM's     | [Website](https://nginxproxymanager.com/), [Github](https://github.com/NginxProxyManager/nginx-proxy-manager) |
| VM02          | Step CA                 | Internal Certificate Manager for services & devices  | [Website](https://smallstep.com/docs/step-ca/)   |
| VM03          | Authentik               | Authentication & Identity Management (AIM)           | [Website](https://goauthentik.io)                |
| ???           | Cockpit                 | Web based server management                          | [Website](https://cockpit-project.org/), [Github](https://github.com/cockpit-project/cockpit)  |
| ???           | Portainer CE            | Manage Containers (Docker, Kubernetes, ...)          | [Github](https://github.com/portainer/portainer) |

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
- [Duck DNS](https://www.duckdns.org/) : Free dynamic DNS hosted on AWS?
- [No-IP](https://www.noip.com/) : Free dynamic DNS
- [Dynu](https://www.dynu.com/) : Free dynamic DNS
- [Vimexx](https://www.vimexx.be/) : Not free, but cheap dynamic DNS

### Certificat Management
- Let's encrypt for services that will be exposed to the internet
- Step CA for the internal-only services.

---

## Notes for Cockpit
Can not simple be connected with Authentik. This needs to be done with NGINX Proxy manager

https://cockpit-project.org/guide/latest/authentication

---

## Some Keywords to Remember
- **SAML** : Security Assertion Markup Language - Open standard for Single Sign-On (SSO) and identity federation. Used to authenticate multiple users with multiple services through a central identity provider (Authentik).
- 
