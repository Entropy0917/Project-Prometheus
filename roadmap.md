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
- [x] **Observability stack: Prometheus + Grafana + Loki + Tempo** via the MicroK8s `observability` addon — metrics, logs, and traces, with ~29 provisioned
- [x] Deploy the next 8 Asus BR1100FKA nodes
- [x] Kubernetes dashboards ([the five-failure writeup](challenges.md#the-observability-install-that-failed-five-different-ways))
- [x] Stage and join the remaining fleet, including the Lenovo N24 tier (toward the full 40)

## In Progress
- [ ] Physical infrastructure system for the full fleet (battery removal, layout, power) — [Braxton's (BraxtonC360)](https://github.com/BraxtonC360) design, currently a working rough draft
- [ ] High-availability control plane (promote two nodes alongside `d1`)
- [ ] Expose Grafana through the ingress controller (replacing ad-hoc `port-forward` access)
- [ ] Investigate recurring node-exporter restarts on `d5`

## Planned

- [ ] Minecraft network with Velocity proxy in front of multiple server instances
- [ ] Backups / disaster-recovery plan for Longhorn volumes and cluster state
- [ ] Re-apply the observability addon's `--authentication-kubeconfig` apiserver flag on `a09` when it returns to service
- [ ] Investigate recurring node-exporter restarts on `d5`
- [ ] Regenerate the cluster CA with proper X.509 extensions (`keyUsage`) — removes the need for `SKIP_TLS_VERIFY` on Grafana's sidecars, but invalidates every node credential, so it waits for a planned maintenance window

## Considered and Deliberately Deferred

- **PXE / autoinstall provisioning** — evaluated after the network was up; retrofitting it mid-project costs more than manual USB installs save. The universal netplan config removed most of the per-node pain instead. Revisit if the remaining rollout makes it worthwhile.

## Ideas / Someday

- [ ] <!-- ambitions -->
