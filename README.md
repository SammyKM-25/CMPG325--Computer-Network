# CMPG 325 – Computer Networks
## Individual Semester Project – Milestone 2

**Student:** Maphosa Kulani  
**Student Number:** 45154597  
**Project ID:** CMPG325-2026-048  
**Client ID:** CLI-048  
**Organisation:** Tshegofatso Distribution Centre (Potchefstroom)  
**Industry:** Logistics  
**Date:** September 2026

---

## 1. Project Overview

This project designs and simulates a computer network for **Tshegofatso Distribution Centre**, a logistics company in Potchefstroom. The network connects four departments:

- **Administration** – management, office staff, paperwork
- **Warehouse** – inventory management, floor operations
- **Logistics** – tracking, dispatch schedules, delivery coordination
- **Servers** – file server for authorised departments

The network uses **Router-on-a-Stick** with sub-interface inter-VLAN routing, as assigned in the project brief.

---

## 2. Client Requirements

| Requirement | Detail |
|---|---|
| Addressing Block | 192.168.29.0/24 |
| Technical Challenge | Router-on-a-Stick (sub-interface inter-VLAN routing) |
| Departments | Administration, Warehouse, Logistics, Servers |
| Constraint | Warehouse has no existing network points |
| Change Request CR4 | File Server reachable by authorised departments only |

---

## 3. Network Design

### 3.1 VLANs and IP Addressing

| VLAN | Name | Subnet | Gateway | Usable Hosts |
|---|---|---|---|---|
| 10 | Administration | 192.168.29.64/27 | 192.168.29.65 | 30 |
| 20 | Warehouse | 192.168.29.0/26 | 192.168.29.1 | 62 |
| 30 | Logistics | 192.168.29.96/27 | 192.168.29.97 | 30 |
| 40 | Servers | 192.168.29.128/28 | 192.168.29.129 | 14 |

### 3.2 Device IP Addressing

| Device | VLAN | IP Address | Subnet Mask | Gateway |
|---|---|---|---|---|
| Router G0/0/0.10 | 10 | 192.168.29.65 | 255.255.255.224 | N/A |
| Router G0/0/0.20 | 20 | 192.168.29.1 | 255.255.255.192 | N/A |
| Router G0/0/0.30 | 30 | 192.168.29.97 | 255.255.255.224 | N/A |
| Router G0/0/0.40 | 40 | 192.168.29.129 | 255.255.255.240 | N/A |
| Admin-PC1 | 10 | 192.168.29.66 | 255.255.255.224 | 192.168.29.65 |
| Admin-PC2 | 10 | 192.168.29.67 | 255.255.255.224 | 192.168.29.65 |
| Admin-Printer | 10 | 192.168.29.68 | 255.255.255.224 | 192.168.29.65 |
| WH-PC1 | 20 | 192.168.29.2 | 255.255.255.192 | 192.168.29.1 |
| WH-PC2 | 20 | 192.168.29.3 | 255.255.255.192 | 192.168.29.1 |
| WH-Printer | 20 | 192.168.29.4 | 255.255.255.192 | 192.168.29.1 |
| WH-AP (management) | 20 | 192.168.29.5 | 255.255.255.192 | 192.168.29.1 |
| Logistics-PC1 | 30 | 192.168.29.98 | 255.255.255.224 | 192.168.29.97 |
| Logistics-PC2 | 30 | 192.168.29.99 | 255.255.255.224 | 192.168.29.97 |
| Logistics-Printer | 30 | 192.168.29.100 | 255.255.255.224 | 192.168.29.97 |
| File-Server | 40 | 192.168.29.130 | 255.255.255.240 | 192.168.29.129 |

---

## 4. Devices Used

| Device | Model | Quantity |
|---|---|---|
| Router | Cisco 4321 (ISR4321) | 1 |
| Switch | Cisco 2960-24TT | 3 |
| Access Point | AccessPoint-PT | 1 |
| PC | PC-PT | 6 |
| Printer | Printer-PT | 3 |
| Server | Server-PT | 1 |

---

## 5. Technical Challenge: Router-on-a-Stick

### 5.1 Configuration

interface g0/0
no shutdown
!
interface g0/0.10
encapsulation dot1Q 10
ip address 192.168.29.65 255.255.255.224
!
interface g0/0.20
encapsulation dot1Q 20
ip address 192.168.29.1 255.255.255.192
!
interface g0/0.30
encapsulation dot1Q 30
ip address 192.168.29.97 255.255.255.224
!
interface g0/0.40
encapsulation dot1Q 40
ip address 192.168.29.129 255.255.255.240
!


### 5.2 Justification

Router-on-a-Stick was selected because:
- It is the assigned technical challenge
- It is cost-effective (one physical interface)
- It is scalable (easy to add more VLANs)
- It is an industry-standard practice

---

## 6. ACL for Change Request CR4

### 6.1 Requirement

> A new application/file server is installed and must be reachable by authorised departments only.

### 6.2 ACL Configuration

access-list 10 permit 192.168.29.64 0.0.0.31
access-list 10 permit 192.168.29.96 0.0.0.31
access-list 10 deny any
!
interface g0/0.40
ip access-group 10 out
!


### 6.3 ACL Rules

| Rule | Action | Source | Purpose |
|---|---|---|---|
| 10 | Permit | 192.168.29.64/27 | Administration access |
| 20 | Permit | 192.168.29.96/27 | Logistics access |
| 30 | Deny | any | Block all others |

### 6.4 Result

| Department | Access to File Server |
|---|---|
| Administration (VLAN 10) | ✅ Permitted |
| Logistics (VLAN 30) | ✅ Permitted |
| Warehouse (VLAN 20) | ❌ Denied |

---

## 7. Constraint: Warehouse Has No Network Points

### 7.1 Solution

A wireless Access Point (AccessPoint-PT) was placed in the warehouse and connected to Core-SW Fa0/4 (VLAN 20).

### 7.2 Wireless Devices

| Device | Connection |
|---|---|
| WH-PC1 | Wireless to AccessPoint-PT |
| WH-PC2 | Wireless to AccessPoint-PT |
| WH-Printer | Wireless to AccessPoint-PT |

### 7.3 SSID and Security

| Setting | Value |
|---|---|
| SSID | Warehouse-WiFi |
| Authentication | WPA2-PSK |
| Passphrase | Warehouse123 |

---

## 8. Trunk Links

| Switch | Port | Connects To | VLANs Allowed |
|---|---|---|---|
| Core-SW | G0/1 | Router1 | 10,20,30,40 |
| Core-SW | G0/2 | Admin-SW | 10,40 |
| Core-SW | Fa0/1 | Logistics-SW | 30 |
| Admin-SW | G0/1 | Core-SW | 10,40 |
| Logistics-SW | G0/1 | Core-SW | 30 |

---

## 9. Testing Evidence

### 9.1 Router Verification

- `show ip interface brief` – all sub-interfaces up/up
- `show access-lists` – ACL rules with match counts

### 9.2 Switch Verification

- `show interfaces trunk` – all trunks working
- `show vlan brief` – all VLANs created and active

### 9.3 Ping Tests

| Test | From | To | Result |
|---|---|---|---|
| 1 | Admin-PC1 | 192.168.29.65 | ✅ Success |
| 2 | Admin-PC1 | 192.168.29.67 | ✅ Success |
| 3 | Admin-PC1 | 192.168.29.100 | ✅ Success |
| 4 | Admin-PC1 | 192.168.29.130 | ✅ Success |
| 5 | Logistics-PC1 | 192.168.29.130 | ✅ Success |
| 6 | WH-PC1 | 192.168.29.1 | ✅ Success |
| 7 | WH-PC1 | 192.168.29.3 | ✅ Success |
| 8 | WH-PC1 | 192.168.29.130 | ❌ Fail (ACL) |

### 9.4 ACL Verification

| Test | From | To | Result |
|---|---|---|---|
| Admin to File Server | Admin-PC1 | 192.168.29.130 | ✅ Permitted |
| Logistics to File Server | Logistics-PC1 | 192.168.29.130 | ✅ Permitted |
| Warehouse to File Server | WH-PC1 | 192.168.29.130 | ❌ Denied |

---

## 10. Repository Structure
├── README.md
├── milestone-1/
│ ├── Client_Requirements_and_Network_Design.docx
│ ├── physical-topology.png
│ ├── logical-topology.png
│ └── ip-addressing-plan.docx
├── milestone-2/
│ ├── CMPG325-2026-048-Milestone2.pkt
│ ├── Milestone2-Implementation-Testing.pdf
│ 
│ │ ├── router1-config.txt
│ │ ├── core-sw-config.txt
│ │ ├── admin-sw-config.txt
│ │ └── logistics-sw-config.txt
│ └── testing-evidence/
│ ├── 01-router-config/
│ ├── 02-switch-config/
│ ├── 03-ping-tests/
│ └── 04-acl-tests/
└── final-submission/
├── technical-report.docx
└── video-demonstration-link.md


---

## 11. Milestone Checklists

### Milestone 1 — Client Design Review (28 August 2026)

- [x] Client requirements analysis
- [x] Physical topology diagram
- [x] Logical topology diagram
- [x] IP addressing plan (VLSM)
- [x] Initial GitHub repository

### Milestone 2 — Client Implementation Review (2 October 2026)

- [x] Working Packet Tracer file (.pkt)
- [x] Router-on-a-Stick fully configured and functional
- [x] Testing evidence (same-VLAN pings, inter-VLAN pings, ACL verification)
- [x] Updated GitHub portfolio

### Final Submission (16 October 2026)

- [ ] Packet Tracer project (.pkt)
- [ ] GitHub portfolio of evidence
- [ ] Technical report
- [ ] 15–20 minute inset video demonstration

---

## 12. How to Open and Test

1. Open `CMPG325-2026-048-Milestone2.pkt` in Cisco Packet Tracer.
2. Wait for all links to turn green.
3. Click on any PC → Desktop → Command Prompt.
4. Test with:
   - `ping 192.168.29.65` (gateway)
   - `ping 192.168.29.130` (File Server)
   - `ping 192.168.29.2` (WH-PC1)
5. Verify the ACL:
   - From Admin-PC1: `ping 192.168.29.130` → succeeds
   - From WH-PC1: `ping 192.168.29.130` → fails

---

## 13. Reflection

This project reinforced the importance of:
- Proper planning before configuration
- VLSM for efficient IP addressing
- VLANs for network segmentation
- ACLs for security
- Testing and documentation

The network meets all client requirements and addresses the constraint and change request.

---
