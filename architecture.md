# Architecture

## Overview

Project Prometheus is a MicroK8s cluster of up to 40 nodes running on an isolated, self-managed network inside a dorm room. The design has three layers:

1. **Physical/network layer** — a travel router bridges the campus Wi-Fi (WAN) to a private, NAT'd cluster LAN distributed through a managed Cisco switch.
2. **Cluster layer** — MicroK8s with a single control plane (`d1`) and worker nodes, plus a dedicated GPU node for inference workloads.
3. **Access layer** — a Tailscale mesh VPN provides secure remote administration and least-privilege collaborator access without exposing anything to the public internet.

## Diagrams

### Hardware Architecture
<!-- Keep the existing diagram; re-upload here if the image link breaks -->
![Hardware architecture diagram](../images/hardware-architecture.png)

### Private Cloud Architecture
![Private cloud architecture diagram](../images/cloud-architecture.png)

## Node Topology

| Node | Hardware | Role |
|---|---|---|
| `d1` | Dell Latitude 3340 | Sole control plane (for now); also the only node collaborator access policy permits |
| `d2`–`d6` | Dell Latitude 3340 | Workers (compute-heavy workloads) |
| `a09` | Asus BR1100FKA (Celeron N4500, 4 GB DDR4) | Lightweight worker — first of the Asus tier |
| `liver` | GTX 1650 Super, 32 GB DDR3, 500 GB HDD | GPU node for LLM inference (Ollama) |
| *(staged)* | Remaining Dell 3340s, Asus BR1100FKAs, Lenovo N24s | Rollout in progress — see [roadmap](roadmap.md) |

All deployed nodes run Ubuntu 26.04 LTS with MicroK8s v1.35.

## Two-Tier Node Strategy

The two hardware classes have very different capabilities, so workloads are allocated in tiers:

- **Tier 1 — Dell Latitude 3340s**: SSD storage, stronger CPUs. Control plane and compute-heavy or storage-sensitive workloads.
- **Tier 2 — Asus BR1100FKAs and Lenovo N24s**: modest CPUs, small/slow storage. Lightweight, stateless, horizontally-scaled workloads only. These storage constraints directly drove the choice of MicroK8s (dqlite) over vanilla Kubernetes (etcd): see [challenges](challenges.md#choosing-microk8s-over-vanilla-kubernetes).

## Key Design Decisions

| Decision | Rationale |
|---|---|
| Single control plane on `d1` | Simplicity and stability after the Trinity-triggered full rebuild ([the story](challenges.md#trinity-the-node-that-nuked-the-cluster)); HA control plane is a roadmap item |
| MicroK8s over kubeadm/vanilla K8s | dqlite datastore tolerates slow eMMC; snap-based installs simplify managing 40 nodes |
| Isolated NAT'd LAN | Full control of DHCP, QoS, and addressing without touching the upstream network |
| Tailscale instead of port forwarding | The upstream network can't be port-forwarded anyway; a mesh VPN with ACLs is both the workaround and the more secure design |
| Single strong GPU node for inference | 1GbE interconnect makes distributed LLM inference impractical; quantized models on one capable node outperform sharding across many weak ones |
