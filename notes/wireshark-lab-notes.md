# Wireshark Lab Notes

I use Wireshark extensively to validate protocols and understand packet flows.

## IPsec + GRE (T1)

- Capture points:
  - R1 Fa0/1 (underlay)
  - EDGE G1/0 (underlay)
- What I look for:
  - ESP packets with VLAN tags
  - Inner GRE + original IP header
  - Tunnel source/destination IPs matching design

## OSPF Area 0 (MD5)

- Capture on GNS3 interfaces.
- Verify:
  - OSPF Hello/DBD packets with MD5 auth.

## EIGRP between ASAv ↔ GNS3 Edge

- Capture on the ASAv outside and EDGE inside.
- Confirm:
  - EIGRP neighbor establishment

## 802.1X / RADIUS

- Capture on the switch uplink or RADIUS server.
- Inspect:
  - EAPOL on the wire
  - PEAP TLS tunnel establishment
  - RADIUS Access-Request/Accept with MSCHAPv2.

## SNMPv3

- Capture SNMPv3 traffic to validate:
  - Auth/priv in use (encrypted PDU)
