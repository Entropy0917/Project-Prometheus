# Project-Prometheus
40-node MicroK8s homelab built from a dorm room. This includes networking, storage, and orchestration from scratch.

Project Prometheus is a self-funded, self-taught homelab at genuine scale, built by **[Chris (Entropy0917)](https://github.com/Entropy0917)** and **[Braxton (BraxtonC360)](https://github.com/BraxtonC360)**. Every layer of the stack (physical networking, Linux administration, Kubernetes orchestration, storage, identity, and remote access) was researched, deployed, broken, and fixed by hand. This repository documents the architecture, the problems solved along the way, and the skills built in the process.

## Why "Prometheus"?

Prometheus is the Titan who gave mortals fire. This project is our fire: computing, storage, a learning platform, and an outlet for creativity. The theme runs through everything like the GPU node is `liver` (the price Prometheus paid daily), the Wi-Fi networks are **spark** (2.4 GHz) and **flame** (5 GHz), and the node that forced us to nuke and rebuild the entire cluster is remembered, via *American Prometheus*, as **Trinity: ground zero**.

---

## At a Glance

| | |
|---|---|
| **Fleet** | 40-node target across Dell Latitude 3340s, Asus BR1100FKAs, and Lenovo N24s, plus supporting machines |
| **Currently deployed** | 8 nodes: control plane `d1`, workers `d2`–`d6`, GPU node `liver`, and `a09` all running Ubuntu 26.04 LTS, MicroK8s v1.35 |
| **Orchestration** | [MicroK8s](https://microk8s.io/) (chosen over vanilla Kubernetes: see [why](docs/challenges.md#choosing-microk8s-over-vanilla-kubernetes)) |
| **Networking** | Isolated NAT'd LAN behind a TP-Link AX1500 travel router + 48-port Cisco Catalyst switch, with QoS and static DHCP reservations |
| **Remote access** | Tailscale mesh VPN with least-privilege collaborator access |
| **Storage** | Longhorn distributed block storage behind an authenticated nginx ingress |
| **Services** | Nextcloud private cloud, Ollama LLM inference (Gemma), and more in the [roadmap](docs/roadmap.md) |

## Architecture

Full details, diagrams, and design decisions: **[docs/architecture.md](docs/architecture.md)**

- **[Hardware](docs/hardware.md)** : node inventory, roles, and the tiered allocation strategy
- **[Networking](docs/networking.md)** : how a full cluster network was built inside a dorm room: the router-behind-a-router design, Cisco Catalyst configuration over console cable, QoS, and an OpenWrt rebuild of a dead-firmware consumer router
- **[Software stack](docs/software-stack.md)** : Kubernetes, storage, ingress, VPN, and application layers

## Problems Solved

Real infrastructure means real failures. Each entry links to a full writeup : see **[docs/challenges.md](docs/challenges.md)**.

| Challenge | One-line summary |
|---|---|
| [No control of the upstream network](docs/challenges.md#running-a-cluster-on-a-network-we-dont-control) | Built an isolated, self-managed network behind a travel router NAT'd to campus Wi-Fi |
| [Trinity: the node that nuked the cluster](docs/challenges.md#trinity-the-node-that-nuked-the-cluster) | An incomplete control-plane join with a mismatched CA forced (and justified) a full rebuild |
| [Version skew across the fleet](docs/challenges.md#kubernetes-version-skew-across-the-fleet) | Worker kubelets outran the API server; fixed with cordon → channel refresh → uncordon, zero data loss |
| [Provisioning dozens of nodes](docs/challenges.md#provisioning-dozens-of-nodes-by-hand) | Universal glob-based netplan config made one file valid on every machine |
| [The ingress that changed underneath us](docs/challenges.md#the-ingress-controller-that-changed-underneath-us) | The rebuild silently swapped nginx for Traefik; replaced with the upstream nginx controller |
| [Dead consumer router firmware](docs/challenges.md#reviving-a-linksys-wrt1900ac-with-openwrt) | Flashed OpenWrt onto a Linksys WRT1900AC whose stock firmware couldn't handle modern Wi-Fi |
| [Choosing MicroK8s over vanilla K8s](docs/challenges.md#choosing-microk8s-over-vanilla-kubernetes) | dqlite tolerates slow eMMC storage far better than etcd |
| [Lenovo N24 Wi-Fi drivers](docs/challenges.md#lenovo-n24-wi-fi-drivers-on-ubuntu-server) | Diagnosed unsupported Realtek chipsets and engineered around them |
| [Least-privilege collaborator access](docs/challenges.md#onboarding-a-collaborator-with-tailscale) | Tailscale access scoped to a single node, expressed in policy |
| [Nextcloud lockout behind NAT](docs/challenges.md#nextcloud-brute-force-lockout-behind-source-nat) | Brute-force protection tripped by source NAT masking client IPs; fixed with proxy-aware IP forwarding |
| [Distributed AI inference limits](docs/challenges.md#distributed-llm-inference-over-1gbe) | Why 1GbE makes multi-node LLM inference impractical and the architecture that works instead |

## Skills Demonstrated

Linux system administration (Ubuntu Server & Desktop) · Kubernetes cluster operations and recovery · network design (NAT, DHCP, QoS) · managed switch configuration (Cisco IOS via console) · router firmware (OpenWrt) · mesh VPNs and least-privilege access (Tailscale) · distributed storage (Longhorn) · ingress and authentication · hardware diagnosis and refurbishment · two-person team planning with a Scrum/Kanban workflow · technical documentation

Full breakdown: **[docs/skills.md](docs/skills.md)**

## Team

- **[Chris — Entropy0917](https://github.com/Entropy0917)** — cluster architecture, Kubernetes operations, networking design, and Linux administration across the fleet
- **[Braxton — BraxtonC360](https://github.com/BraxtonC360)** — deployment, configuration, and troubleshooting partner throughout; deployed and configured cloud storage system and Cisco Catalyst switch via console CLI, and leads physical infrastructure management for the fleet

We run the project with a Scrum-inspired workflow on a Kanban board (**To-do → In-Progress → Testing → Done**)  the infrastructure changes pass through Testing before they're called Done.

## Roadmap

The cluster is a living project (see **[docs/roadmap.md](docs/roadmap.md)** ) for what's deployed, in progress, and planned (including scaling to the full 40 nodes and a monitoring stack).

## Resources & References

Documentation, guides, and tools that made this possible: **[docs/resources.md](docs/resources.md)**

---

# Private Cloud Architecture
<img width="401" height="736" alt="cloudArch" src="https://github.com/user-attachments/assets/6d3d46c0-b632-434b-bcd3-c7cf8c6445ff" />

