# Networking

This is the layer that makes the whole project possible. The cluster lives in a dorm with zero control over the building's network: no router access, no port forwarding, no static IPs, no say in DHCP. Running a 40-node cluster in that environment required building a network *inside* the network.

## Cluster Network

**Gateway: TP-Link AX1500 Wi-Fi 6 Portable Router**

The travel router connects to the campus Wi-Fi as its WAN uplink and NATs a private LAN behind it. From the campus network's perspective, the entire cluster is one wireless client. Behind the router, we control everything.

Configuration highlights:

- **Dual access points** — **spark** (2.4 GHz) and **flame** (5 GHz), keeping with the project's fire theme
- **Wired backbone** — a **Cisco Catalyst WS-C2960G-48TC-L** 48-port Gigabit managed switch, uplinked to the router, distributes wired connectivity to the nodes. Braxton deployed and configured the switch the traditional way: console cable and the Cisco IOS CLI via PuTTY — the same workflow used in production network operations.
- **QoS** — traffic prioritization so cluster chatter doesn't starve interactive/administrative traffic
- **Static DHCP reservations** — every node gets a fixed address by MAC reservation, giving the cluster stable addressing without static IP configuration on each machine

## Personal Network

**Linksys WRT1900AC v1 → OpenWrt**

To keep personal devices separate from cluster infrastructure, Chris set up a second router. The WRT1900AC v1's stock firmware turned out to be effectively abandoned — after troubleshooting confirmed it simply could not operate correctly with current Wi-Fi client technology. Rather than scrap the hardware:

- Flashed **[OpenWrt](https://openwrt.org/)** onto the router and learned its interface (LuCI) and configuration model
- Configured dual 2.4 GHz / 5 GHz access points
- Set DHCP reservations for personal devices

This turned a dead consumer router into a fully capable, modern, open-source network device and taught us firmware flashing, recovery procedures, and OpenWrt administration in the process.

## Remote Access: Tailscale

With no ability to port-forward, inbound access to the cluster is impossible through conventional means. **[Tailscale](https://tailscale.com/)** (a WireGuard-based mesh VPN) solves this cleanly so every enrolled device gets a stable address on a private mesh, regardless of the NATs in between.

What we've built with it:

- Remote administration of the cluster from anywhere
- **Least-privilege collaborator access** — access to the cluster is scoped in policy to the control plane node (`d1`) only, with everything else denied by default

This is the same access-control pattern (identity-based, default-deny) used in production zero-trust environments.

## Skills This Layer Built

NAT and multi-router topologies · DHCP administration and MAC-based reservations · QoS configuration · Cisco IOS switch configuration over console · Wi-Fi band planning (2.4/5 GHz) · router firmware flashing and OpenWrt · WireGuard-based mesh VPNs · least-privilege access policy design
