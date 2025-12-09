# PKI, AAA, and 802.1X Design

## Root CA (Cisco 2811)

- Acts as Root CA for the lab.
- SCEP enabled for automated enrollment (ASA, EDGE, etc.).
- Used for:
  - IPsec tunnel certs (T1)
  - HTTPS / ASDM
  - 802.1X PEAP server certs

## AAA / RADIUS

- FreeRADIUS on VLAN 10.
- Used for:
  - Device login (SSH)
  - ASA/ASDM auth
  - 802.1X PEAP + MSCHAPv2

## 802.1X

- Switchports in access mode for wired 802.1X testing.
- EAP type: PEAP (outer) + MSCHAPv2 (inner).
- Server certificate issued by 2811 Root CA via SCEP.
