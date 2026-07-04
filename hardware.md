# Hardware

## Compute Inventory

| Qty | Machine | Status | Role / Notes |
|---|---|---|---|
| 20 | [Dell Latitude 3340](https://www.dell.com/support/manuals/en-us/latitude-13-3340-2-in-1-laptop/latitude_3340_ss/specifications-of-latitude-3340?guid=guid-7c9f07ce-626e-44ca-be3a-a1fb036413f9&lang=en-us) | 6 deployed (`d1`–`d6`) | Tier-1 nodes: SSD storage, stronger CPUs. `d1` is the control plane |
| 9 | Asus BR1100FKA | 1 deployed (`a09`), 8 staging for deployment | Intel Celeron N4500 @ 1.10 GHz, 4 GB DDR4, ~128 GB storage. Lightweight worker tier |
| 20 | [Lenovo N24](https://support.lenovo.com/us/en/solutions/pd105072-product-overview-lenovo-n24-winbook) | Staged, not deployed | eMMC storage; Realtek Wi-Fi requires driver workarounds — see [challenges](challenges.md#lenovo-n24-wi-fi-drivers-on-ubuntu-server) |
| 1 | `liver` — custom-built GPU node | Deployed | NVIDIA GTX 1650 Super, 32 GB DDR3, 500 GB HDD. Runs Ollama LLM inference |
| 2 | [Dell Latitude 7490](https://www.dell.com/support/manuals/en-us/latitude-14-7490-laptop/latitude_7490_om/technical-specifications?guid=guid-bf51648f-378d-45af-818e-6931ba59d73e&lang=en-us) | One retired from the cluster | One 7490 served as `trinity` — the node whose failed join triggered the full rebuild ([the story](challenges.md#trinity-the-node-that-nuked-the-cluster)). |
| 1 | [Dell OptiPlex 3050](https://www.dell.com/support/manuals/en-us/optiplex-3050-desktop/optiplex-3050-desktop-tower-owners-manual/diagnostic-and-power-led-codes?guid=guid-7d615d96-eb00-4be6-b8dc-191949e0f418&lang=en-us) | Not currently in use | Held in reserve |

## Network Hardware

| Device | Role |
|---|---|
| TP-Link AX1500 Wi-Fi 6 Portable Router | Cluster gateway — NATs the cluster LAN behind campus Wi-Fi; hosts the **spark** (2.4 GHz) and **flame** (5 GHz) access points |
| Cisco Catalyst WS-C2960G-48TC-L (48-port Gigabit, managed) | Wired distribution for cluster nodes. Deployed and configured by Braxton over console cable using the IOS CLI (PuTTY) |
| Linksys WRT1900AC v1 (OpenWrt) | Personal-device network, separated from cluster infrastructure |

## Operating Systems

- All deployed nodes: **Ubuntu Server 26.04 LTS** (control plane originally provisioned with Ubuntu Desktop)
- MicroK8s v1.35, containerd 2.x

## Physical Management

Running dozens of machines in a dorm room imposes real limits: power circuits, heat, noise, and space all shape which nodes are online and when. Braxton leads the physical infrastructure side, including removing batteries from fleet laptops (long-term AC operation without battery swelling risk) and designing a physical organization system for the full 40-node rollout. <!-- TODO: expand when the physical setup system is finalized — the current setup is a working rough draft -->
