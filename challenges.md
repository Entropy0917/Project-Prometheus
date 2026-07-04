# Challenges & Solutions

Every entry follows the same template (see [UPDATING.md](UPDATING.md)) so new problems are easy to add as the project grows.

---

## Running a Cluster on a Network We Don't Control

**Status:** Solved

**Constraint:** This cluster lives in a dorm. The building network offers no router access, no port forwarding, no static IPs, and no DHCP control: nothing a cluster needs.

**Solution:** Built a self-managed network *behind* the campus network. A TP-Link AX1500 Wi-Fi 6 portable router connects to campus Wi-Fi as its WAN and NATs a private cluster LAN. Behind it: dual-band access points ("spark" on 2.4 GHz, "flame" on 5 GHz), a 48-port Cisco Catalyst switch for wired node distribution, QoS to prioritize traffic classes, and MAC-based DHCP reservations giving every node a stable address. Inbound access (impossible through campus NAT) is handled entirely by Tailscale.

**Skills:** network design, NAT/multi-router topologies, QoS, DHCP administration

---

## Trinity: The Node That Nuked the Cluster

**Status:** Solved (the hard way)

**Problem:** A new node, `trinity` (a Dell Latitude 7490 running Ubuntu Desktop, 8 cores), joined the cluster and immediately began a cascade of failures: it had joined as a *control-plane* node instead of a worker (the `--worker` flag was omitted), developed a TLS certificate mismatch (`x509: certificate signed by unknown authority` in the Calico CNI logs), reported `Ready` while scheduling no pods, and its kubelet endlessly logged `node "trinity" not found`. The machine may also have carried residue from its previous institutional imaging. The diagnosis: an incomplete control-plane join that left trinity with a mismatched certificate authority and a zombie node object in the datastore.

**Solution:** After `microk8s leave`, a forced `remove-node`, and a clean re-join attempt, we made the engineering call to stop patching and rebuild the entire cluster from scratch (trinity's Desktop OS also mismatched the fleet standard, compounding the case for retirement) this time with `d1` as the sole control plane and every worker joined with an explicit `--worker` flag. The rebuild was dramatically faster than the original build: proof the first build's lessons stuck.

In keeping with the project's naming theme (see the README), trinity is now referred to as *ground zero* — the node that made us nuke our own setup.

**Skills:** Kubernetes control-plane internals (CA trust, node registration), CNI debugging, disaster recovery, knowing when re-architecting beats patching

---

## Kubernetes Version Skew Across the Fleet

**Status:** Solved

**Problem:** The control plane was tracking MicroK8s's `1.34/stable` snap channel while workers installed later had auto-selected `1.35/stable`: putting worker kubelets *ahead* of the API server, a violation of Kubernetes' version-skew policy. Root cause: an install-time channel default change between when the first node was provisioned and when later nodes joined.

**Solution:** Verified Longhorn replica safety first, then cordoned the control plane, ran `snap refresh microk8s --channel=1.35/stable`, waited for ready status, and uncordoned — bringing all nodes into alignment with zero data loss. Prevention going forward: explicitly pin the channel at every join.

**Skills:** Kubernetes upgrade/skew policy, cordon-drain operational discipline, safe change sequencing around stateful storage

---

## Provisioning Dozens of Nodes by Hand

**Status:** Solved (deliberate trade-off)

**Problem:** Every node needs Ubuntu installed and network configuration applied. PXE/network boot infrastructure couldn't exist before the network itself did and per-node netplan edits (hardcoded interface names like `eno1`) made each new node a manual chore.

**Solution:** Two-part answer. Installs are done via manual USB — evaluated PXE and cloud-init autoinstall later, and made the deliberate call that retrofitting them mid-project cost more than it saved. The per-node configuration pain, however, had a root fix: a **universal netplan config** using an interface glob (`match: name: "en*"` with DHCP) instead of hardcoded interface names, making one config file valid on every machine regardless of hardware.

**Skills:** netplan/Ubuntu networking, provisioning strategy, cost-benefit engineering judgment

---

## The Ingress Controller That Changed Underneath Us

**Status:** Solved

**Problem:** After the rebuild, the Longhorn UI's basic-auth ingress stopped working. Investigation revealed the rebuilt cluster's `microk8s enable ingress` had deployed **Traefik**, while the ingress manifest was written with **nginx** annotations: the original cluster had used the nginx addon. Same command, different controller, silently incompatible.

**Solution:** Disabled the Traefik addon and deployed the upstream nginx ingress controller directly from its bare-metal provider manifest, then recreated the basic-auth secret (cleaning up a duplicate entry in the process). The Longhorn UI is now reachable cluster-wide behind authentication.

**Skills:** ingress controllers (nginx vs. Traefik), Kubernetes secrets, reading between the lines of "it worked before"

---

## Reviving a Linksys WRT1900AC with OpenWrt

**Status:** Solved

**Problem:** A Linksys WRT1900AC v1 purchased for a separate personal-device network wouldn't work reliably. After systematic firmware troubleshooting, the root cause turned out not to be a fixable misconfiguration: the v1's stock firmware is abandoned and incompatible with modern Wi-Fi client behavior.

**Solution:** Flashed [OpenWrt](https://openwrt.org/) onto the router, learned its configuration model and LuCI interface, and rebuilt the network on it. Dual-band access points and DHCP reservations for personal devices. Dead consumer hardware became a modern, fully-supported open-source router.

**Skills:** root-cause analysis, firmware flashing, OpenWrt administration, knowing when to stop fixing and start replacing

---

## Choosing MicroK8s over Vanilla Kubernetes

**Status:** Solved (design decision)

**Problem:** Much of the planned fleet runs on slow eMMC storage. Vanilla Kubernetes uses etcd as its datastore, and etcd is notoriously latency-sensitive (slow disks cause leader-election churn and cluster instability).

**Solution:** Selected MicroK8s, whose dqlite datastore is substantially more tolerant of slow storage. Snap-based installation also simplifies provisioning and upgrading at 40-node scale.

**Skills:** evaluating orchestration platforms against real hardware constraints, understanding Kubernetes control-plane internals (etcd vs. dqlite)

---

## Lenovo N24 Wi-Fi Drivers on Ubuntu Server

**Status:** Solved (N24s currently staged, not deployed)

**Problem:** The Lenovo N24s' Realtek wireless chipset (RTL8821CE family) lacks in-kernel support on Ubuntu Server, leaving fresh installs with no connectivity.

**Solution:** Diagnosed the chipset and evaluated DKMS driver builds versus USB-Ethernet adapters, establishing a connectivity path for when the N24 fleet deploys.

**Skills:** Linux hardware diagnosis, kernel modules/DKMS, pragmatic engineering trade-offs

---

## Onboarding a Collaborator with Tailscale

**Status:** Solved

**Problem:** Granting a partner remote access to the cluster: but scoped to exactly what's needed, on infrastructure that must otherwise stay locked down, with no public exposure.

**Solution:** Used Tailscale to grant access scoped to the control plane node (`d1`) only, expressed in policy rather than shared credentials. Default-deny, least-privilege: the same zero-trust pattern used in production environments.

**Skills:** access-control policy design, mesh VPN administration, least-privilege thinking

---

## Nextcloud Brute-Force Lockout Behind Source NAT

**Status:** Solved

**Problem:** Nextcloud's brute-force protection locked out logins. The interesting part: because traffic reaches Nextcloud through source NAT, every client appeared to come from the same internal IP: so the protection mechanism couldn't distinguish a real attacker from a legitimate user, and one lockout locked out everyone.

**Solution:** Cleared the active lockout, then fixed the root cause: configured proxy-aware client-IP handling (forwarded headers and Nextcloud's trusted-proxy settings) so the application sees real client addresses instead of the NAT gateway's. Brute-force protection now throttles per actual client, and access works normally. <!-- TODO: paste the exact config lines if you want the writeup to be reproducible -->

**Skills:** reverse-proxy header forwarding, application security mechanisms, debugging through NAT layers

---

## Distributed LLM Inference over 1GbE

**Status:** Solved (design decision)

**Problem:** The obvious dream for a 40-node cluster is sharding a large language model across it. Analysis showed the 1GbE interconnect makes this impractical: inter-node tensor traffic becomes the bottleneck long before compute does.

**Solution:** Run small, quantized models on a single GPU node (`liver`) via Ollama instead of distributing inference. The cluster's parallelism is spent on horizontally-scalable workloads that actually fit its network profile.

**Skills:** performance analysis, understanding distributed-systems bottlenecks, matching workloads to infrastructure
