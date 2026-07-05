# Resources & References

Everything that taught us something along the way. Add new entries under the matching heading.

## Official Documentation

- [MicroK8s docs](https://microk8s.io/docs)
- [Kubernetes docs](https://kubernetes.io/docs/)
- [Ubuntu Server docs](https://ubuntu.com/server/docs)
- [OpenWrt docs](https://openwrt.org/docs/start)
- [Tailscale docs](https://tailscale.com/kb) — especially [ACLs](https://tailscale.com/kb/1018/acls)
- [Longhorn docs](https://longhorn.io/docs/)
- [Nextcloud admin manual](https://docs.nextcloud.com/server/latest/admin_manual/)
- [Ollama](https://ollama.com/)
- [kube-prometheus-stack Helm chart](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack)
- [Grafana provisioning docs](https://grafana.com/docs/grafana/latest/administration/provisioning/)

## Hardware References

- [Dell Latitude 3340 specifications](https://www.dell.com/support/manuals/en-us/latitude-13-3340-2-in-1-laptop/latitude_3340_ss/specifications-of-latitude-3340?guid=guid-7c9f07ce-626e-44ca-be3a-a1fb036413f9&lang=en-us)
- [Lenovo N24 product overview](https://support.lenovo.com/us/en/solutions/pd105072-product-overview-lenovo-n24-winbook)
- [Dell OptiPlex 3050 manual](https://www.dell.com/support/manuals/en-us/optiplex-3050-desktop/optiplex-3050-desktop-tower-owners-manual/diagnostic-and-power-led-codes?guid=guid-7d615d96-eb00-4be6-b8dc-191949e0f418&lang=en-us)
- [Dell Latitude 7490 specifications](https://www.dell.com/support/manuals/en-us/latitude-14-7490-laptop/latitude_7490_om/technical-specifications?guid=guid-bf51648f-378d-45af-818e-6931ba59d73e&lang=en-us)
- [TP-Link AX1500 Wi-Fi 6 Portable Router](https://www.tp-link.com/us/home-networking/wifi-router/tl-wr1502x/)
- Cisco Catalyst 2960-G series (WS-C2960G-48TC-L) documentation
- [OpenWrt WRT1900AC device page](https://openwrt.org/toh/linksys/wrt1900ac)

## Guides, Threads & Troubleshooting Trails

AI-assisted troubleshooting was a real part of this project's workflow — used the way an engineer uses documentation and a rubber duck, with every fix verified on real hardware.

- [Installing Prometheus in a Kubernetes cluster (IBM TXSeries docs)](https://www.ibm.com/docs/en/txseries/11.1.0?topic=grafana-installing-prometheus-in-kubernetes-cluster) — the starting guide for the observability deployment; superseded by the MicroK8s `observability` addon, but useful orientation on the Prometheus/Grafana architecture
- [Deploying Helm Charts: The Importance of Installing CRDs First (kislow, Medium)](https://kislow.medium.com/deploying-helm-charts-the-importance-of-installing-crds-first-5beefc2ef215) — what CRDs are and why Helm ordering matters
- [How to Handle CRDs That Must Be Installed Before Operators (OneUptime)](https://oneuptime.com/blog/post/2026-02-26-argocd-crds-before-operators/view) — the CRD-before-operator ordering problem in depth
- [Kubernetes CRDs explained (YouTube)](https://www.youtube.com/watch?v=TScDYMym7LA) — video walkthrough of Custom Resource Definitions


## Tools

- PuTTY (serial console access to the Cisco Catalyst)
- `microk8s kubectl`
- [upstream ingress-nginx bare-metal manifests](https://github.com/kubernetes/ingress-nginx)
- <!-- TODO: k9s? Helm? Etcher/Ventoy/Rufus for USB imaging? -->
