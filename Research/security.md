# Research Cyber Security Notes
Some notes to look up how to build my homelab secure

## Needed Software Stack
Research which software I need to run to keep everything safe...

### Reverse Proxy
- Centralizes access to internal services via a single entry point (usually port 443)
- Handles SSL/TLS termination and certificate management
- Obscures internal IPs and service layout (security through abstraction)
- Enables URL routing and subdomain management

Popular packages:
- **NGINX**: Lightweight, robust, widely supported
- **NGINX Proxy Manager**: GUI-based management, automatic Let's Encrypt
- **Traefik**: Dynamic config, ideal for containerized workloads
- **HAProxy**: High performance, suitable for load balancing

---

### Secrets Management
- Prevents credentials, tokens, and API keys from leaking into plaintext or Git
- Supports rotation, auditing, and encryption
- Required for ISO 27001 domains like Cryptographic Controls and Access Management

Popular packages:
- **Vault (HashiCorp)**: Full-featured ecosystem, policies, audit logging
- **Vaultwarden**: Lightweight, Bitwarden-compatible
- **Step CA + Secrets**: Combines PKI with secrets via JSON/YAML
- **SOPS + GPG/Age**: Encrypts config files for Git workflows

---

### Authentication & Identity Management
- Centralized user, role, and access control
- Supports SSO, OAuth2, OpenID Connect
- Required for ISO 27001 domains like Access Control and User Access Management

Popular packages:
- **Keycloak**: Enterprise-grade IAM, RBAC, LDAP integration
- **Authentik**: Lightweight, modern UI, great for homelabs
- **Authelia**: 2FA, reverse proxy integration, YAML-based config

---

### VPN / Secure Access
- Provides encrypted tunnels for secure remote access
- Required for ISO 27001 domains like Communications Security and Remote Access

Popular packages:
- **WireGuard**: Fast, kernel-integrated, simple to configure
- **OpenVPN**: Mature, widely supported
- **Tailscale**: Zero-config mesh VPN based on WireGuard
- **Cloudflare Tunnel**: External access without opening ports

---

## Handy links
- [Github Proxmox Homelab Template](https://github.com/Khizar-Hussian/proxmox-homelab-template): Full setup, not really for learning
- [Cloudflare-DNS](https://www.cloudflare.com/application-services/products/dns/): They have a free DNS-plan. Need to look for more...
- [Selfhosting SSO with NGINX / Keycloak](https://joeeey.com/blog/selfhosting-sso-with-nginx-keycloak-part-1/): Blog post about setup.
- [Github Keycloak Production](https://github.com/anqorithm/keycloak-production/blob/main/README.md): Diagram drawing setup


