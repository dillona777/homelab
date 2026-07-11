# Configs

Sanitized, feature-focused configuration snippets for the lab's **physical** devices — the stanzas that implement a specific feature, with context, rather than full running-config dumps.

> Keys, secrets, and certificate bodies are redacted or replaced with placeholders.

## Devices

- [`routers/R1-2811_sanitized.cfg`](routers/R1-2811_sanitized.cfg) — 2811 WAN edge: PAT, GRE-over-IPsec tunnel endpoint, AAA, NTP
- [`switches/SW2-3560X_sanitized.cfg`](switches/SW2-3560X_sanitized.cfg) — L3 core switch: inter-VLAN routing (SVIs), per-VLAN DHCP, Rapid-PVST, LACP EtherChannel
- [`switches/SW1-2960X_sanitized.cfg`](switches/SW1-2960X_sanitized.cfg) — L2 access switch: 802.1X port auth, Rapid-PVST, LACP uplink

Configs for the GNS3 virtual devices were removed as part of rebuilding the virtual site.
