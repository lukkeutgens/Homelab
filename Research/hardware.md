# Research: Homelab Hardware
> I refuse to send money to countries that threaten Taiwan or are actively engaged in military aggression. Therefore, the following brands are excluded: Minisforum, Beelink, GMKtec, Geekom, and others with Chinese or Russian origins.

This document tracks potential hardware options for my homelab setup. Since the system will run 24/7, total wattage draw (TWD) and energy efficiency are key considerations. I'm primarily exploring mini-PCs that strike a good balance between performance, expandability, and power consumption.

- The system must function as a Proxmox server for virtualization, so the more RAM and CPU threads (used as vCPUs in VMs), the better. I require at least 16 threads and 32GB of RAM, though 64GB is preferred.
- Networking should support at least 2.5Gbps, even though Belgian ISPs currently cap residential connections at 1Gbps.
- Storage must be at least 1TB NVMe SSD.

## List
| Brand         | Model                    | CPU                       | Threads | Memory | Storage | Power | Price     | Country    | State       |
| :---          | :---                     | :---                      | :---:   | :---:  | :---:   | :---:  | :---     | :---       | :---        |
| PCSpecialist  | AZENA® NUC DDR5          | AMD Ryzen 7 PRO8 8845HS   | 16      | 64GB   | 1TB     | 75W   | **€732**  | UK         | New         |
| Slimbook      | One AMD Ryzen 7 8845HS   | AMD Ryzen 7 8845HS        | 16      | 32GB   | 1TB     | 60W   | **€849**  | Spain      | New         |
| Intel         | NUC 13 Linux Mini-pc     | Intel i7-1360P            | 16      | 64GB   | 1TB     | 50W   | **€991**  | US         | New         |
| Tuxedo        | Nano Pro - Gen14         | AMD Ryzen AI 5 340        | 12      | 64GB   | 1TB     | 45W   | **€1118** | Germany    | New         | 
| System 76     | Meerkat (Meer10)         | Intel Ultra 7 255H        | 22      | 64GB   | 1TB     | 65W   | **€1312** | US         | New         | 
| ASUS          | NUC 14 PRO Core Ultra 7  | Intel Core Ultra 7        | 22      | ???    | ???     | 70W   | **€720**  | Taiwan     | New         |

## Model Comparison Notes
- The PCS Azena with AMD Ryzen 7 PRO 8845HS offers double the RAM compared to the Slimbook, and is €100 cheaper. However, build quality might be lower?
- The Slimbook One AMD can be pre-installed with Proxmox, which could become the backbone of the setup — a small advantage.
- Intel-based options easily exceed the €1000 mark, but offer more CPU threads, which translates to more vCPUs for virtual machines.
- Some have more networking ports, but do I need that?


---

## Mini PC's Considered
This section documents all models that were evaluated during the selection process.

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
- Country of origin: UK
- Price: **€732**
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
- CPU: Intel Ultra 7 255H (6x P-Cores, 8x E-Cores, 2x LPE-cores, 22x Threads)
- RAM: 32GB
- Storage: 1TB NVMe SSD
- Network: 2x 2.5G RJ45 + Wifi 7 + Bluethooth 5.4
- Power Consumption: TDP 28-65W, Idle ~10W, Load ~65W

---

## Self Build
Maybe building it myself will be cheaper and more powerfull?

### CPU's
| Brand    | Model                            | Cores | Threads  | TDP   | iGPU  | Price | Link    |
| :---     | :---                             | :---: | :---:    | :---  | :---: | :---  | :---    |
| AMD      | Ryzen™ 9 7900X Desktop Processor | 12    | 24       | 170W  | Yes   | €279  | [Link](https://www.amd.com/en/products/processors/desktops/ryzen/7000-series/amd-ryzen-9-7900x.html) |
| AMD      | Ryzen™ 9 5950X Desktop Processor | 16    | 32       | 105W  | No    | €259  | [Link](https://www.amd.com/en/products/processors/desktops/ryzen/5000-series/amd-ryzen-9-5950x.html) |




AMD Ryzen™ 9 7900X Desktop Processor: 12x Cores, 24x Threads, 



