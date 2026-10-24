# Industrial OT/ICS Network Segmentation & Defense-in-Depth Lab (Purdue Model / IEC 62443)


## Project Overview

Designed and implemented a segmented **IT / IDMZ / OT network architecture** in Cisco Packet Tracer modeled after the **Purdue Enterprise Reference Architecture** and **ISA/IEC 62443 (Zones and Conduits)** principles. This lab demonstrates how to isolate critical Level 0–2 Industrial Control Systems (SCADA/IoT actuators) from corporate IT threats while maintaining operational visibility.

## Architecture & Zones

• **Level 4 (Corporate IT Zone - 192.168.10.0/24):** Engineering Workstation (PC0) connected to R1 (IT-Edge).
• **Level 3.5 (Industrial DMZ / IDMZ - 172.16.2.0/24):** Data Historian / Jump Host (DMZ_Server) connected via an SFP GLC-TE (Gig0/0/2) routed interface to broker traffic between IT and OT.
• **Transit Conduit (172.16.1.0/30):** Point-to-point static-routed link between R1 and R2 for deterministic pathing (zero dynamic routing overhead).
• **Level 0–2 (OT Cell Zone - 10.10.50.0/24):** SCADA Registration Controller (10.10.50.100) managing physical field actuators (Industrial Fan .101 and Door .102).

## Security Controls Implemented (Defense-in-Depth)

**1. Deterministic Static Routing:** Configured explicit static routes across R1 and R2 to eliminate route-injection vectors and convergence jitter.
**2. Layer 3/4 Conduit Enforcement (Extended ACLs):** Deployed an Extended ACL (OT-FIREWALL) on R2 permitting TCP Port 80/502 strictly from the DMZ_Server (172.16.2.100) to the OT_Server (10.10.50.100), completely blocking direct Corporate IT (192.168.10.0/24) access.
**3. Passive OT Network Monitoring (SPAN / Port Mirroring):** Configured Local SPAN (monitor session 1) on the OT switch (Switch1) to mirror ingress/egress SCADA traffic (Fa0/2) to a passive packet sniffer (Fa0/24), enabling zero-latency anomaly detection without active scanning.
**4. Layer 2 Rogue Device Mitigation (Port Security):** Hardened the OT access switch (Switch1) using switchport port-security mac-address sticky (maximum 1, violation shutdown) and administratively disabled all unused ports (Fa0/5–23) to prevent rogue contractor laptops from bypassing perimeter firewalls.

## Real-World Troubleshooting Highlights

**• CIDR Boundary Mismatch (/30 vs /24):** Diagnosed an ARP failure where the OT gateway (10.10.50.1) responded to ICMP Echo requests while .100 returned Destination host unreachable due to a /30 (255.255.255.252) mask misconfiguration on the LAN interface. Isolated via tracert and fixed via CLI inspection.
**• Hardware Modularity (ISR 4331):** Differentiated between Layer 2 switching NIM cards (NIM-ES2-4 requiring SVIs) and native Layer 3 SFP cages (GLC-TE on Gig0/0/2) to build a dedicated physical IDMZ interface.
