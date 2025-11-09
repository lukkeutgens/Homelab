# Research: Homelab Hardware
> I refuse to send money to countries that threaten Taiwan or are actively engaged in military aggression. Therefore, the following brands are excluded: Minisforum, Beelink, GMKtec, Geekom, and others with Chinese or Russian origins.

This document tracks potential hardware options for my homelab setup. Since the system will run 24/7, total wattage draw (TWD) and energy efficiency are key considerations. I'm primarily exploring mini-PCs that strike a good balance between performance, expandability, and power consumption.

- The system must function as a Proxmox server for virtualization, so the more RAM and CPU threads (used as vCPUs in VMs), the better. I require at least 16 threads and 32GB of RAM, though 64GB is preferred.
- Networking should support at least 2.5Gbps, even though Belgian ISPs currently cap residential connections at 1Gbps.
- Storage must be at least 1TB NVMe SSD.

## List
| Brand         | Model                    | CPU                       | Threads | Memory | Storage | Network             | Power | Price     | Country    | State       |
| :---          | :---                     | :---                      | :---:   | :---:  | :---:   | :---                | :---:  | :---     | :---       | :---        |
| PCSpecialist  | AZENA® NUC DDR5          | AMD Ryzen 7 PRO8 8845HS   | 16      | 64GB   | 1TB     | 1x RJ45-2.5G        | 75W   | **€732**  | UK         | New         |
| Slimbook      | One AMD Ryzen 7 8845HS   | AMD Ryzen 7 8845HS        | 16      | 32GB   | 1TB     | 2x 2.5G RJ45        | 60W   | **€849**  | Spain      | New         |
| ASUS          | NUC 14 PRO Core Ultra 7  | Intel Core Ultra 7        | 22      | ???    | 1TB     | 1x RJ45-2.5G        | 70W   | **€848**  | Taiwan     | New         |
| Intel         | NUC 13 Linux Mini-pc     | Intel i7-1360P            | 16      | 64GB   | 1TB     | 1x RJ45-2.5G        | 50W   | **€991**  | US         | New         |
| Tuxedo        | Nano Pro - Gen14         | AMD Ryzen AI 5 340        | 12      | 64GB   | 1TB     | RJ45-1G + RJ45-2.5G | 45W   | **€1118** | Germany    | New         | 
| System 76     | Meerkat (Meer10)         | Intel Ultra 7 255H        | 22      | 64GB   | 1TB     | 2x 2.5G RJ45        | 65W   | **€1312** | US         | New         | 

## Why I chose the Slimbook One
I decided to go with the Slimbook One, even though it's slightly more expensive than the PCS Azena, for the following reasons:
- The extra 4 threads on Intel models don’t justify the significant price difference.
- The build quality of the Slimbook appears superior compared to PCS.
- It offers dual 2.5GbE RJ45 ports, plus WiFi 6 and Bluetooth, which adds flexibility.
- The Slimbook can be pre-installed with Proxmox, providing extra assurance for compatibility and support.
- It’s expandable with additional RAM and SSD if needed in the future.

---

## Mini PC's Considered
This section documents all models that were evaluated during the selection process.

### TUXEDO Nano Pro - Gen14 (New)
Too expensive for my needs
- [Product Link](https://www.tuxedocomputers.com/en/TUXEDO-Nano-Pro-Gen14-AMD.tuxedo)
- Country of origin: **Germany**
- Price: **€1118**
- CPU: AMD Ryzen AI 5 340 (6 Cores, 12 Threads)
- RAM: 64GB
- Storage: 1TB NVMe SSD
- Network: 1x 1G RJ45 + 1x 2.5G RJ45
- Power Consumption: TDP 28W, Idle ~8W, Load ~45W

---

### Intel NUC13 Linux mini-pc (New)
Too expensive for my needs
- [Product Link 1](https://laptopmetlinux.nl/product/intel-nuc13-linux-mini-computer/)
- Country of origin: **US**
- Price: **€991**
- CPU: Intel i7-1360P (4x P-Cores, 8x E-Cores, (16x Threads)
- RAM: 64GB
- Storage: 1TB NVMe SSD
- Network: 1x 2.5G RJ45
- Power Consumption: TDP 28W, Idle ~10W, Load ~50W

---

### PCS AZENA® NUC DDR5 (New)
A potential candidate.
- [Product Link](https://www.pcspecialist.be/computers/pcs-azena-nuc/)
- Country of origin: UK
- Price: **€732**
- CPU: AMD Ryzen 7 PRO8 8845HS (8x Cores, 16x Chreads)
- RAM: 64GB
- Storage: 1TB NVMe SSD
- Network: 1x 2.5G RJ45
- Power Consumption: TDP 35–54W, Idle ~10W, Load ~75W

---

### ASUS NUC 14 PRO Core Ultra 7 
Barebone, likely more expensive than the AMD option once RAM and SSD are added. The SSD alone adds at least €128, and RAM still needs to be included. Also, the website isn't very clear about the configuration options.
- [Product Link](https://www.coolblue.be/nl/product/951316/asus-nuc-14-pro-core-ultra-7-rnuc14rvhu700002i.html)
- Country of origin: Taiwan
- Price: **€720**
- CPU: Intel Core Ultra 7 (6x P-Cores, 8x E-Cores, 22x Threads) (not more info about it) 
- RAM: Not clear on their website
- Storage: Not clear on their website
- Network: 1x 2.5G RJ45
- Power Consumption: TDP 28-65W, Idle ~10W, Load ~70W

---

### Slimbook One AMD Ryzen 7 8845HS
This is the one I ordered.
- [Product Link](https://slimbook.com/en/shop/product/one-amd-ryzen-7-8845hs-1417?category=14)
- Country of origin: Spain
- Price: **€849**
- CPU AMD Ryzen 7 8845HS (8x Cores, 16x Threads)
- RAM: 32GB
- Storage: 1TB NVMe SSD
- Network: 2x 2.5G RJ45 + Wifi 6 + Bluethooth
- Power Consumption: TDP 35-54W, Idle ~12W, Load ~60W

---

### System76 Meerkat
Too expensive for my needs
- [Product Link](https://system76.com/desktops/meer10/configure)
- Country of origin: US
- Price: **€1312**
- CPU: Intel Ultra 7 255H (6x P-Cores, 8x E-Cores, 2x LPE-cores, 22x Threads)
- RAM: 32GB
- Storage: 1TB NVMe SSD
- Network: 2x 2.5G RJ45 + Wifi 7 + Bluethooth 5.4
- Power Consumption: TDP 28-65W, Idle ~10W, Load ~65W

---










