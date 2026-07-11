# Hybrid Cisco Networking Homelab

A hybrid Cisco networking lab that pairs a physical Cisco + Proxmox rack with a virtual GNS3 site, joined by a site-to-site GRE-over-IPsec tunnel. I use it to build hands-on depth in enterprise routing and switching, with network services, secure management, and protocol validation layered on top.

> **Status:** Originally built while studying CCNP Security; now transitioning focus to **CCNP Enterprise (ENCOR)**. The physical core is stable; the GNS3 virtual site is being rebuilt around enterprise routing, switching, and automation. The security work is kept here as a documented foundation — see [Security Foundations](#security-foundations).

---

## Physical Core (On-Prem Rack)

The physical side is the stable foundation of the lab and where most of the switching work lives.

- **Cisco 2811 ISR** — WAN edge; static PAT to the home LAN; also the lab's GRE-over-IPsec tunnel endpoint.
- **Cisco Catalyst 3560X (L3)** — core switch and default gateway: inter-VLAN routing via SVIs, per-VLAN DHCP, Rapid-PVST with root-priority tuning, and an LACP EtherChannel to the access switch.
- **Cisco Catalyst 2960X (L2)** — access switch: 802.1X-enabled ports, Rapid-PVST, LACP uplink to the core.
- **Dell PowerEdge R720 + Lenovo RS140** — Proxmox VE hosts. The R720 runs the GNS3 VM that bridges the physical rack to the virtual site, and serves as the lab's single NTP source on the management VLAN.

**VLANs:** 10 (Servers) · 20 (Hosts / Endpoints) · 99 (Management / NTP) · 200 (IPsec/GRE transit)

**Core services:** centralized AAA/RADIUS (device login + 802.1X), authoritative DNS, and NTP from a single trusted host on VLAN 99.

Addressing detail: [`notes/ip-plan.md`](notes/ip-plan.md)

## Virtual Site (GNS3)

A virtual routed domain bridged to the physical core over the GRE-over-IPsec tunnel.

> **Being rebuilt.** The previous virtual site was an OSPF/EIGRP + ASAv security topology. It's being torn down and rebuilt around CCNP Enterprise topics (advanced routing incl. BGP, redistribution, and automation). This section will be updated as the new topology comes online.

## What I'm Practicing

**Routing & Switching (primary focus)**
- L3 switching: inter-VLAN routing, SVIs, per-VLAN DHCP
- Spanning Tree (Rapid-PVST) with root/priority tuning
- EtherChannel (LACP)
- OSPF and EIGRP, static routing — moving into BGP and redistribution
- GRE-over-IPsec site-to-site tunneling between the physical and virtual sites

**Network Services & Assurance**
- NTP from a single trusted source over a dedicated management VLAN
- SNMPv3 (auth/priv) validation
- Packet capture and protocol validation with Wireshark ([`notes/wireshark-lab-notes.md`](notes/wireshark-lab-notes.md))

**Security Foundations** — see below.

## Security Foundations

Built while studying CCNP Security and kept as a documented foundation rather than the current focus:

- **AAA / RADIUS** — centralized device login and 802.1X authentication (FreeRADIUS on VLAN 10)
- **802.1X** — PEAP + MS-CHAPv2 port authentication on the access switch
- **Cisco ASA** — inside/DMZ/outside segmentation, security levels, NAT, ACLs
- **PKI / SCEP** — 2811 as root CA issuing certificates for cert-based IPsec
- **Secure management** — SNMPv3 (auth/priv), NTP from a single trusted source

Design detail: [`notes/pki-aaa-design.md`](notes/pki-aaa-design.md)

## Repository Structure

```
docs/      Topology diagrams
configs/   Sanitized, feature-focused config snippets for the physical devices
notes/     Design decisions, IP plan, and protocol-validation notes
```

## Roadmap (CCNP Enterprise)

- Rebuild the GNS3 site for enterprise routing: OSPF areas, EIGRP, **BGP**, redistribution, and path control
- Scenario-based labs (build + break/fix), documented one per scenario
- Network assurance: syslog, NetFlow, IP SLA, SPAN
- Automation: Python + NETCONF/RESTCONF and Ansible against lab devices
- Refresh topology diagrams as the new virtual site comes online

## About Me

Hybrid homelab built and maintained while studying toward advanced Cisco certifications (**CCNP Enterprise** — currently working through ENCOR). More about my background: [linkedin.com/in/dillon-alfieri-network](https://www.linkedin.com/in/dillon-alfieri-network)
