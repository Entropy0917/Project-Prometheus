# Skills

A running inventory of skills built through this project, grouped by domain. Each links back to where it was exercised.

## Linux Administration

- Ubuntu Server and Ubuntu Desktop installation, configuration, and administration across a growing multi-node fleet
- Manual OS imaging and installation at scale (USB provisioning of dozens of machines)
- SSH key-based access management across the fleet
- netplan network configuration, including a universal glob-based config valid on any hardware ([writeup](challenges.md#provisioning-dozens-of-nodes-by-hand))
- Package and service management (apt, snap channels, systemd), log analysis with journalctl
- Hardware/driver troubleshooting, including kernel module and DKMS work ([N24 Wi-Fi](challenges.md#lenovo-n24-wi-fi-drivers-on-ubuntu-server))

## Networking

- Multi-router NAT topologies and network-inside-a-network design ([networking](networking.md))
- Cisco Catalyst switch configuration via console cable and IOS CLI (PuTTY)
- DHCP administration with MAC-based reservations
- QoS traffic prioritization
- Wi-Fi band planning (2.4 GHz / 5 GHz)
- Router firmware flashing and OpenWrt administration
- Mesh VPNs (Tailscale/WireGuard) and least-privilege access policy

## Kubernetes & Orchestration

- MicroK8s cluster deployment, node joins (control plane vs. `--worker`), and full rebuild/recovery
- Diagnosing broken joins: CA/TLS trust failures, zombie node objects, CNI (Calico) log analysis ([Trinity](challenges.md#trinity-the-node-that-nuked-the-cluster))
- Version skew resolution with cordon/refresh/uncordon and snap channel pinning ([writeup](challenges.md#kubernetes-version-skew-across-the-fleet))
- Ingress controllers: nginx vs. Traefik, upstream bare-metal deployment, basic-auth secrets ([writeup](challenges.md#the-ingress-controller-that-changed-underneath-us))
- Distributed storage with Longhorn, including replica-safety checks before disruptive operations
- YAML manifest authoring and workload placement across heterogeneous hardware
- Kubernetes API machinery: CRD lifecycle (stored vs. Established), API discovery debugging with `api-resources` ([writeup](challenges.md#the-observability-install-that-failed-five-different-ways))
- Helm internals: chart hooks (pre/post-install), release state, hook-cleanup failure modes and zombie-job forensics
- Full observability stack deployment: kube-prometheus-stack, Grafana provisioning sidecars, Loki, Tempo
- CNI (Calico) credential debugging: expired token diagnosis via sandbox-creation failures, `hostNetwork` as a differential
- DaemonSet rollout mechanics (`maxUnavailable` wedging on unreachable nodes) and safe pod force-deletion / namespace finalization

## Security

- Least-privilege access design (Tailscale, default-deny)
- Ingress authentication
- Debugging application security mechanisms through NAT ([Nextcloud lockout](challenges.md#nextcloud-brute-force-lockout-behind-source-nat))
- TLS/X.509 internals: certificate extensions (`keyUsage`), strict-validation differences across language runtimes, and reasoned trade-offs when relaxing verification

## Collaboration & Project Management

- Two-person team operating shared infrastructure: division of labor (cluster/orchestration vs. physical infrastructure and switching), coordinated troubleshooting, and shared runbooks
- Agile practice: Scrum-inspired workflow with a Kanban board (**To-do → In-Progress → Testing → Done**) — a Testing column before Done enforces that infrastructure changes are verified, not just applied
- Planning a phased 40-node rollout: staging hardware, sequencing deployments, and deliberately deferring automation (PXE) when the cost outweighed the benefit
- Post-incident thinking: turning failures like Trinity into documented lessons ([challenges](challenges.md))

## Systems Thinking

- Capacity planning and bottleneck analysis (1GbE inference limits)
- Matching orchestration and workloads to real hardware constraints
- Root-cause analysis and knowing when to re-architect instead of patch
- Technical documentation (this repository)
