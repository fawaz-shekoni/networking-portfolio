# Project 02 — Connected Home Office

**Status:** Built; evidence documented below  
**Packet Tracer file:** [`project-02-connected-home-office.pkt`](../packet-tracer/project-02-connected-home-office.pkt)  
**Topology:** ![Three-department SOHO topology](../images/project-02-topology.png)

## Case study

GreenFarm Foods Nigeria Ltd is opening a regional office in Enugu, separate from its Lagos headquarters. Design a small-office network using one Cisco router and one Cisco switch for three departments: Admin / IT, Finance / HR, and Customer Service / Reception.

Each department must have its own VLAN and wireless access point. The branch router provides DHCP and inter-VLAN routing so devices in the three branch departments can communicate. The branch has no connection to the corporate HQ network.

## Design assumptions

The case study provides `192.168.4.0` without a prefix. This implementation assumes the allocated parent network is `192.168.4.0/24`, then divides it into four `/26` networks. Three are assigned to departments; one remains spare. If the ISP allocation uses a different prefix, the addressing plan must be recalculated.

## VLAN and IPv4 plan

| Department | VLAN | Subnet | Router gateway | Usable host range | Broadcast |
|---|---:|---|---|---|---|
| Admin / IT | 10 | `192.168.4.0/26` | `192.168.4.1` | `192.168.4.1–192.168.4.62` | `192.168.4.63` |
| Finance / HR | 20 | `192.168.4.64/26` | `192.168.4.65` | `192.168.4.65–192.168.4.126` | `192.168.4.127` |
| Customer Service / Reception | 30 | `192.168.4.128/26` | `192.168.4.129` | `192.168.4.129–192.168.4.190` | `192.168.4.191` |
| Spare | — | `192.168.4.192/26` | — | `192.168.4.193–192.168.4.254` | `192.168.4.255` |

Subnet mask for each department: `255.255.255.192` (`/26`). Each subnet has 62 usable IPv4 addresses.

## Topology and implementation

The topology contains one Cisco ISR 4331 router, one Cisco 2960 switch, and three access points—one per department. Each department includes a PC, printer, and wireless client (smartphone, laptop, or tablet). The router uses 802.1Q subinterfaces on `GigabitEthernet0/0/0` for VLANs 10, 20, and 30. DHCP pools are configured for all three departmental subnets; the Customer Service / Reception pool is labeled `CS/R` in the router configuration.

The topology labels show the three `/26` departmental networks. The switch configuration screenshot shows access ports assigned to VLANs 10, 20, and 30, plus a trunk-mode port.

## Evidence

- [Topology](../images/project-02-topology.png)
- [Admin / IT PC DHCP lease](../images/project-02-admin-dhcp.png): `192.168.4.2/26`, gateway `192.168.4.1`; DHCP request successful.
- [Finance / HR PC DHCP lease](../images/project-02-finance-dhcp.png): `192.168.4.66/26`, gateway `192.168.4.65`; DHCP request successful.
- [Router subinterfaces](../images/project-02-router-subinterfaces.png): 802.1Q subinterfaces and gateway addresses for VLANs 10, 20, and 30.
- [Router DHCP pools](../images/project-02-dhcp-pools.png): pools for all three department subnets; `CS/R` is Customer Service / Reception.
- [Switch VLANs and trunk port](../images/project-02-switch-vlans-trunk.png): access ports for VLANs 10, 20, and 30 and a port configured as a trunk.
- [Inter-VLAN ping](../images/project-02-inter-vlan-ping.png): PC0 pings `192.168.4.131`; the first request times out and the next three receive replies (3 of 4 received, 25% loss).

## Verification record

The supplied screenshots confirm successful DHCP assignment for the Admin / IT and Finance / HR PCs. The router configuration shows pools for all three VLANs, including `CS/R`, and the switch configuration shows the VLAN access ports and a trunk-mode port. The ping screenshot shows reachability from PC0 to `192.168.4.131` after an initial timeout; it does not show a clean 0% loss result. A Customer Service endpoint DHCP lease and wireless association details are not included in the evidence.

## Reflection

This lab practices VLAN-based department separation, subnetting, router-on-a-stick, and centralized DHCP. The three VLANs remain distinct at Layer 2 while the router provides the Layer 3 path for inter-department communication. The Enugu branch is designed to operate without a link to the Lagos HQ.

