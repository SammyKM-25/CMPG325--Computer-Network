# CMPG325--Computer-Network
CMPG325-2026-048
# CMPG325-2026-048 — Tshegofatso Distribution Centre Network Design

Individual project for CMPG 325 (Computer Networks). This repository contains the design, implementation, and testing evidence for a segmented departmental network built for **Tshegofatso Distribution Centre**, a logistics and warehousing client based in Potchefstroom.
**Name:** Kulani Maphosa
**Student Number:** 45154597 
**Assigned Technical Challenge:** Router-on-a-Stick (sub-interface inter-VLAN routing)
**Addressing Block:** 192.168.29.0/24

---

## Project Overview

Tshegofatso Distribution Centre requires a network connecting three departments — Administration, Warehouse, and Logistics — plus a centralised file/application server, with two site-specific requirements:

1. **Constraint:** The warehouse/factory floor has no existing network points at all. It must be connected wirelessly, with no new cabling.
2. **Change Request CR4:** A new file/application server must be reachable only by authorised departments (Admin and Logistics), not by Warehouse.

The network uses **VLAN segmentation** with **Router-on-a-Stick** for inter-VLAN routing, **VLSM** for efficient IP allocation, a **wireless access point** in the warehouse, and a **standard ACL** to enforce CR4.

---

## Network Design Summary

| VLAN | Department | Subnet | CIDR | Gateway | Usable Hosts |
|------|------------|--------|------|---------|---------------|
| 10 | Admin | 192.168.29.64 | /27 | 192.168.29.65 | 30 |
| 20 | Warehouse | 192.168.29.0 | /26 | 192.168.29.1 | 62 |
| 30 | Logistics | 192.168.29.96 | /27 | 192.168.29.97 | 30 |
| 40 | Servers | 192.168.29.128 | /28 | 192.168.29.129 | 14 |

**Routing:** Single ISR4321 router (`Router1`) with four sub-interfaces (`G0/0.10`–`G0/0.40`), each running 802.1Q encapsulation, trunked to `Core-SW`.

**Warehouse constraint:** A wireless access point (`WH-AP` / WRT300N) serves the warehouse area. All warehouse hosts connect wirelessly — no wired network points exist in that area.

**CR4 enforcement:** The file server sits on its own VLAN (40), isolated from all departments. A standard ACL applied inbound on `G0/0.40` permits traffic only from the Admin and Logistics subnets and denies all other traffic, including Warehouse.

```
access-list 10 permit 192.168.29.64 0.0.0.31
access-list 10 permit 192.168.29.96 0.0.0.31
access-list 10 deny any

interface g0/0.40
 ip access-group 10 in
```

---

## Repository Structure

```
├── README.md                          # This file
├── milestone-1/
│   ├── Client_Requirements_and_Network_Design.docx
│   ├── physical-topology.png
│   ├── logical-topology.png
│   └── ip-addressing-plan.docx        # (or included within the requirements doc)
├── milestone-2/
│   ├── Tshegofatso-Distribution-Centre.pkt
│   ├── device-configs/                # Router and switch running-configs
│   ├── testing-evidence/
│   │   ├── same-vlan-ping.png
│   │   ├── inter-vlan-ping.png
│   │   ├── acl-permit-admin.png
│   │   ├── acl-permit-logistics.png
│   │   └── acl-deny-warehouse.png
│   └── README.md                      # Milestone 2 specific notes (optional)
└── final-submission/
    ├── technical-report.docx
    └── video-demonstration-link.md
```

*(Adjust the tree above to match what you actually upload — the important thing is that every milestone's required files are in a clearly labelled folder.)*

---

## Milestone 1 — Client Design Review (28 August 2026)

- [x] Client requirements analysis
- [x] Physical topology diagram
- [x] Logical topology diagram
- [x] IP addressing plan (VLSM)
- [x] Initial GitHub repository

## Milestone 2 — Client Implementation Review (2 October 2026)

- [ ] Working Packet Tracer file (.pkt)
- [ ] Router-on-a-Stick fully configured and functional
- [ ] Testing evidence (same-VLAN pings, inter-VLAN pings, ACL verification)
- [ ] Updated GitHub portfolio

## Final Submission (16 October 2026)

- [ ] Packet Tracer project (.pkt)
- [ ] GitHub portfolio of evidence
- [ ] Technical report
- [ ] 15–20 minute inset video demonstration

---

## Testing Plan (Milestone 2)

| Test | Expected Result |
|------|------------------|
| Admin-PC1 → Admin-PC2 (same VLAN) | Success |
| Admin-PC1 → Logistics-PC1 (inter-VLAN) | Success |
| WH-PC1 → WH-PC2 (same VLAN, wireless) | Success |
| Admin-PC1 → File-Server | Success (permitted by ACL) |
| Logistics-PC1 → File-Server | Success (permitted by ACL) |
| WH-PC1 → File-Server | Fails (denied by ACL) |

---


I understand that I am responsible for the correctness, understanding, verification, and academic integrity of everything I submit. Any AI use complies with the applicable NWU AI Policy. AI assistance does not transfer responsibility for the submitted work from me.
