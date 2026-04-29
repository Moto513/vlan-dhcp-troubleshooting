# vlan-dhcp-troubleshooting

# DHCP Decline and IP Exhaustion in Multi-VLAN Environment

## Overview

This document describes a network issue where DHCP-assigned IP addresses are immediately released (DHCP Decline), resulting in apparent IP exhaustion.

The environment consists of multiple VLANs with partially overlapping IP subnets.

---

## Network Design (Abstracted)

| VLAN   | Subnet           |
| ------ | ---------------- |
| VLAN A | 192.168.224.0/21 |
| VLAN B | 192.168.232.0/21 |
| VLAN C | 192.168.240.0/21 |
| VLAN D | 192.168.232.0/21 |

Note: VLAN B and VLAN D share the same subnet.

---

## Observed Issue

* Clients obtain IP addresses via DHCP
* Immediately release them (DHCP Decline)
* IP pool becomes depleted over time

---

## Key Behavior

### ARP-based Duplicate Detection

After receiving an IP address, clients perform an ARP probe:

1. Send ARP request for assigned IP
2. If response is detected, assume duplicate
3. Send DHCP Decline
4. Release the IP address

---

## Root Cause (Hypothesis)

Although VLANs are isolated at Layer 2, overlapping subnets at Layer 3 introduce inconsistencies:

* Multiple VLANs appear to belong to the same IP network
* ARP responses may be misinterpreted
* Clients falsely detect IP conflicts

---

## Impact

* Increased DHCP Decline events
* IP addresses marked unusable
* Apparent IP exhaustion

---

## Temporary Mitigation

Disabling unnecessary Layer 3 interfaces on switches may reduce the frequency of the issue.

---

## Key Takeaways

* VLAN isolation does not guarantee Layer 3 separation
* Subnet design must align with VLAN boundaries
* ARP-based validation can introduce unexpected behavior in complex environments

---

## Conclusion

This issue highlights the importance of maintaining consistency between Layer 2 segmentation and Layer 3 addressing design.



## Network Topology

```mermaid
graph TD
    A[Client VLAN B] --> C[Access Point]
    B[Client VLAN D] --> C
    C --> D[Switch L2]
    D --> E[Gateway DHCP Server]
