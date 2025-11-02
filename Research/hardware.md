# Research Hardware
This document tracks potential hardware options for my homelab setup. Since the system will run 24/7, total wattage draw (TWD) and energy efficiency are important considerations. I'm primarily exploring refurbished mini-PCs that offer a good balance between performance, expandability, and power consumption.

## List
| Brand         | Model                    | CPU                       | Threads | Memory | Storage | Power | Price     | Country    | State       |
| :---          | :---                     | :---                      | :---:   | :---:  | :---:   | :---:  | :---     | :---       | :---        |
| PCSpecialist  | AZENA® NUC DDR5          | AMD Ryzen 7 PRO8 8845HS   | 16      | 64GB   | 1TB     | 75W   | **€722**  | UK         | New         |
| Slimbook      | One AMD Ryzen 7 8845HS   | AMD Ryzen 7 8845HS        | 16      | 64GB   | 1TB     | 60W   | **€849**  | Spain      | New         |
| Intel         | NUC 13 Linux Mini-pc     | Intel i7-1360P            | 16      | 64GB   | 1TB     | 50W   | **€991**  | US         | New         |
| Tuxedo        | Nano Pro - Gen14         | AMD Ryzen AI 5 340        | 12      | 64GB   | 1TB     | 45W   | **€1118** | Germany    | New         | 
| System 76     | Meerkat (Meer10)         | Intel Ultra 7 255H        | 22      | 64GB   | 1TB     | ???   | **€1312** | US         | New         | 
| ASUS          | NUC 14 PRO Core Ultra 7  | Intel Core Ultra 7        | 22      | ???    | ???     | 70W   | **€720**  | Taiwan     | New         |

## Notes
- Not sending money to a country that wants to attack Taiwan, so following brands are a no-go: **Minisforum**, **Beelink**, **GMKtec**, **Geekom**, ...
- They need to work as Proxmox server for virualization, so the more RAM and CPU Threads (in VM will be a vCPU) the better. At least 16x threads in total and 32GB of RAM, but I would prefer more.
- Network at least 2.5Gbps. Our Belgium internet providers are not giving more then 1Gbps for homes at this point.
- For storage 1TB will be the minimum.

---

## Model Information
More info about each model

### TUXEDO Nano Pro - Gen14 (New)
Too expensive
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
- [Product Link](https://www.pcspecialist.be/computers/pcs-azena-nuc/)
- Country of origin: (unknown for now) 
- Price: **€722**
- CPU: AMD Ryzen 7 PRO8 8845HS (8x Cores, 16x Chreads)
- RAM: 64GB
- Storage: 1TB NVMe SSD
- Network: 1x 2.5G RJ45
- Power Consumption: TDP 35–54W, Idle ~10W, Load ~75W

---

### ASUS NUC 14 PRO Core Ultra 7 
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
- [Product Link](https://system76.com/desktops/meer10/configure)
- Country of origin: US
- Price: **€1312**
- CPU: Intel Ultra 7 255H (6x P-Cores, 8x E-Cores, 2x LPE-cores, 16x Threads)
- RAM: 32GB
- Storage: 1TB NVMe SSD
- Network: 2x 2.5G RJ45 + Wifi 7 + Bluethooth 5.4
- Power Consumption:

---





