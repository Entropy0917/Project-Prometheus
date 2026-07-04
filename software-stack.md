# Software Stack

## Orchestration

- **[MicroK8s](https://microk8s.io/)**: lightweight Kubernetes distribution. Chosen deliberately: its dqlite datastore handles the Lenovo N24s' slow eMMC storage far better than etcd would ([full rationale](challenges.md#choosing-microk8s-over-vanilla-kubernetes)).
- Single control plane on `d1` following the Trinity-triggered rebuild; workers join via `microk8s join --worker` (the flag that experience taught us to never omit). Snap channels are explicitly pinned at join to prevent version skew.

## Storage

- **[Longhorn](https://longhorn.io/)**: distributed block storage across cluster nodes, with a web UI protected behind ingress basic-auth.

## Ingress & Access

- **Upstream nginx ingress controller** (bare-metal deployment) with basic-auth protection for administrative UIs: replaced the MicroK8s Traefik addon after the rebuild ([the story](challenges.md#the-ingress-controller-that-changed-underneath-us))
- **[Tailscale](https://tailscale.com/)** mesh VPN for all remote access — no services exposed to the public internet. Tag-based ACLs implement least-privilege access (see [networking](networking.md#remote-access-tailscale)).

## Applications

| Service | Purpose |
|---|---|
| **[Nextcloud](https://nextcloud.com/)** | Self-hosted private cloud (files, sync) |
| **[Ollama](https://ollama.com/)** + [Gemma](https://deepmind.google/models/gemma/) | Local LLM inference on the GPU node | |
<!-- TODO: other running services — Minecraft (Velocity proxy?), monitoring, dashboards? --> 

## Tooling

<!-- TODO: fill in what we actually use day-to-day. Candidates: -->
- `kubectl` / `microk8s kubectl`
- SSH (key-based administration across all nodes)

## Configuration Samples

Sanitized manifests and configs live in [`/configs`](../configs/) <!-- TODO: create this folder when ready: publishing a few sanitized YAML manifests (deployment, ingress, Longhorn storage class) substantially strengthens the repo for technical reviewers -->.
