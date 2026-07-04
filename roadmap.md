# Roadmap

The cluster is deliberately built to grow. This page tracks what's deployed, what's staged, and what's planned, we move items up as they land.

## Deployed

- [x] Cluster network (TP-Link AX1500 gateway, Cisco Catalyst 2960G switch, QoS, DHCP reservations)
- [x] Personal network (Linksys WRT1900AC on OpenWrt)
- [x] MicroK8s control plane (`d1`) + workers `d2`–`d6` on Ubuntu 26.04 LTS
- [x] First Asus BR1100FKA node (`a09`) joined
- [x] GPU node (`liver`) with Ollama inference
- [x] Longhorn distributed storage behind authenticated nginx ingress
- [x] Nextcloud
- [x] Tailscale mesh; collaborator access scoped to `d1`
- [x] Nextcloud brute-force lockout resolved (proxy-aware client-IP forwarding)

## In Progress

- [ ] Deploy the next 8 Asus BR1100FKA nodes
- [ ] Stage and join the remaining fleet, including the Lenovo N24 tier (toward the full 40)
- [ ] Physical infrastructure system for the full fleet (battery removal, layout, power) — [Braxton's (BraxtonC360)](https://github.com/BraxtonC360) design, currently a working rough draft

## Planned

- [ ] **Monitoring: Prometheus + Grafana.** The industry-standard Kubernetes observability stack: Prometheus scrapes metrics from every node and pod, Grafana turns them into dashboards and alerts. At 40 nodes, "which machines are struggling" stops being answerable by eye; this is also the data needed to plan the rollout intelligently (and yes: a cluster named Prometheus should run Prometheus)
- [ ] Minecraft network with Velocity proxy in front of multiple server instances
- [ ] High-availability control plane (promote two nodes alongside `d1`)
- [ ] Backups / disaster-recovery plan for Longhorn volumes and cluster state

## Considered and Deliberately Deferred

- **PXE / autoinstall provisioning** — evaluated after the network was up; retrofitting it mid-project costs more than manual USB installs save. The universal netplan config removed most of the per-node pain instead. Revisit if the remaining rollout makes it worthwhile.

## Ideas / Someday

- [ ] <!-- ambitions -->
