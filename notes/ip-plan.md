# IP Plan (Physical Core)

Addressing for the physical homelab: VLANs on the core, point-to-point links, and management/services. (The GNS3 side is being rebuilt from a clean slate; its addressing will be documented per scenario as it comes online.)

---

## VLANs – Physical Core

| VLAN | Purpose | Subnet | Notes |
|------|--------------------|------------------|--------------------------------------------------------|
| 10 | Servers | 192.168.10.0/24 | Proxmox VMs (AAA/RADIUS, DNS, GNS3 VM, etc.) |
| 20 | Hosts / Endpoints | 192.168.20.0/24 | Wired clients / test machines for 802.1X, etc. |
| 99 | Management / NTP | 192.168.99.0/24 | Switch SVIs, Proxmox mgmt, iDRAC; **NTP source** on the R720 Proxmox host. |

> Gateways for each VLAN are provided by Catalyst 3560X SVIs on the core.

---

## Point-to-Point Links (Physical)

### R1 ↔ SW2 (Core Link)
- Subnet: **192.168.100.0/30**
- R1 (Cisco 2811): `192.168.100.1/30`
- SW2 (Catalyst 3560X): `192.168.100.2/30`

### R1 ↔ Home Router / WAN
- Home LAN: **10.0.0.0/24**
- Home router: `10.0.0.1/24`
- R1 outside/WAN-facing interface: `10.0.0.50/24` (static PAT to home LAN)

---

## Management & Services

### Management (VLAN 99) — 192.168.99.0/24
- Core switch SVI (SW2): `192.168.99.1`
- L2 switch SVI (SW1): `192.168.99.2`
- Proxmox management (R720 / RS140): `192.168.99.100` / `192.168.99.150`
- iDRAC: `192.168.99.200`
- NTP sourced from the **R720 Proxmox host** (`192.168.99.100`); Cisco devices point here.

### Servers (VLAN 10) — 192.168.10.0/24
- AAA / RADIUS server
- Authoritative DNS for `*.gootas.org`
- GNS3 VM host

---

## Summary
- **VLAN 10 / 20 / 99** segment the physical core into servers, hosts, and management/NTP.
- **192.168.100.0/30** is the routed link between R1 (2811) and the core (3560X).
- All NTP is sourced from a single trusted host on VLAN 99.
