# Architecture

## Software Stack

| Device        | Software                | Description                                          | Link                                                                                  |
| :---          | :---                    | :---                                                 | :---                                                                                  |
| Slimbook One  | Proxmox                 | Hypervisor for running VM's                          | [Website](https://www.proxmox.com/en/products/proxmox-virtual-environment/overview)   |
| VM01          | NGINX Proxy Manager     | Reverse proxy to isolate homelab services & VM's     | [Website](https://nginxproxymanager.com/), [Github](https://github.com/NginxProxyManager/nginx-proxy-manager) |
| VM02          | Step CA                 | Internal Certificate Manager for services & devices  | [Website](https://smallstep.com/docs/step-ca/)   |
| VM03          | Authentik               | Authentication & Identity Management (AIM)           | [Website](https://goauthentik.io)                |




## Still to check
- [Let's Encrypt](https://letsencrypt.org/) : For public domain names, to link up with the Proxy on the WAN side?
- [Duck DNS](https://www.duckdns.org/) : Free dynamic DNS hosted on AWS?
- [No-IP](https://www.noip.com/) : Free dynamic DNS
- [Dynu](https://www.dynu.com/) : Free dynamic DNS
- [Vimexx](https://www.vimexx.be/) : Not free, but cheap dynamic DNS
- [Keycloak](https://www.keycloak.org/) or [Authentik](https://goauthentik.io/) : Authentication & Identity Management (AIM) service 
