# Comprehensive School Network Project Documentation

**Cisco Packet Tracer Simulation**  
**Date: December 18, 2025**  
**Author: [Clifford Kingsley Baidoo]** 

## Project Overview

This Cisco Packet Tracer project simulates a complete, multi-site school network with two campuses (Main and Branch). The design incorporates VLAN segmentation, inter-VLAN routing, dynamic routing, DHCP, internal DNS, email services, file sharing, and a school website.

### Key Features Implemented
- VLAN segmentation across multiple buildings
- Layer 3 switching for efficient inter-VLAN routing
- Dynamic routing with RIP version 2 for inter-site connectivity
- Centralized DHCP on multilayer switches
- Internal DNS server for name resolution
- External-like email service (SMTP/POP3)
- Internal FTP server for file sharing
- Internal web server hosting the school website
- Full end-to-end connectivity between main and branch campuses
- NAT for access to simulated external services

## Network Topology

### Main Campus
- **Core/Distribution Device**: Multilayer Switch (3560-24PS)  
  - Performs inter-VLAN routing, DHCP, and RIP participation
- **Access Layer**: Three Layer 2 switches (2960) for Buildings A, B, and C  
  - Connected via 802.1Q trunks to the central multilayer switch
- **Edge Router**: Main Router (Router-PT)  
  - Connected via routed link to multilayer switch

### Branch Campus
- Multilayer switch with access switches
- Connected via routed link to Branch Router

### External/Internet Simulation
- **Email Server-PT** connected through Cloud-PT
- **Cloud Router** handling NAT and external interface

### Routing Domain
- Three routers (Cloud, Main, Branch) + two multilayer switches
- All participating in RIP version 2

## IP Addressing Scheme

### Main Campus VLANs (172.12.155.0/24 – Subnetted with /27 masks)

| VLAN | Purpose/Example   | Network            | Usable Hosts         | Gateway (SVI)     | DHCP Pool |
|------|-------------------|--------------------|----------------------|-------------------|-----------|
| 10   | Admin             | 172.12.155.0/27    | .1 – .30             | 172.12.155.1      | VLAN10    |
| 20   | HR                | 172.12.155.32/27   | .33 – .62            | 172.12.155.33     | VLAN20    |
| 30   | Finance           | 172.12.155.64/27   | .65 – .94            | 172.12.155.65     | VLAN30    |
| 40   | Business          | 172.12.155.96/27   | .97 – .126           | 172.12.155.97     | VLAN40    |
| 50   | E&C               | 172.12.155.128/27  | .129 – .158          | 172.12.155.129    | VLAN50    |
| 60   | A&D               | 172.12.155.160/27  | .161 – .190          | 172.12.155.161    | VLAN60    |
| 70   | Students LAB      | 172.12.155.192/27  | .193 – .222          | 172.12.155.193    | VLAN70    |
| 80   | IT                | 172.12.155.224/27  | .225 – .254          | 172.12.155.225    | VLAN80    |

### Server Static IPs (in VLAN50 – IT)
- DNS Server: `172.12.155.230`
- FTP Server: `172.12.155.231`
- Web Server: `172.12.155.232`

### Point-to-Point Routed Links (/30)
- Main L3 Switch ↔ Main Router: `172.16.0.0/30`
- Branch L3 Switch ↔ Branch Router: `172.16.1.0/30`
- Cloud Router ↔ Main Router: `10.0.0.0/30`
- Main Router ↔ Branch Router: `10.0.1.0/30`

### External Email Network
- Network: `20.0.0.0/30`
- Email Server: `20.0.0.2`
- Cloud Router interface: `20.0.0.1`

## Device Configuration Highlights

### Central Multilayer Switch (Main Campus)
- `ip routing` enabled
- VLANs and SVIs created
- Trunk ports to access switches (native VLAN 1, restricted allowed VLANs)
- Routed port to Main Router (`no switchport`)
- DHCP pools for all 8 VLANs with exclusions, default-router, and dns-server (internal DNS)
- RIP v2 participation

### Access Switches
- Access ports assigned to correct VLANs
- Trunk uplinks with matching native VLAN

### Routers
- Serial/Ethernet interfaces configured with appropriate IPs and clock rates
- NAT overload on Cloud Router
- RIP v2 configured on all routers

### Servers

#### DNS Server (172.12.155.230)
- DNS service enabled
- Records:
  - `myschool.site` → 172.12.155.232 (Web)
  - `file.server` → 172.12.155.231 (FTP)
  - `myschool.edu` → 20.0.0.1 (Email)

#### FTP Server (172.12.155.231)
- FTP service enabled
- Users configured (student, teacher, admin)
- Sample files and directories created

#### Web Server (172.12.155.232)
- HTTP and HTTPS enabled
- Custom HTML pages:
  - `index.html` – School homepage
  - `students.html`, `teachers.html` – Additional pages

#### Email Server (20.0.0.1 – External)
- SMTP and POP3 enabled
- SMTP authentication disabled (required for PT client compatibility)
- Domain: `myschool.edu`
- Multiple test user accounts

## Routing – RIP Version 2
- Enabled on all routers and multilayer switches
- `version 2` and `no auto-summary` configured
- All internal subnets, inter-router links, and email network dynamically advertised
- Static routes removed after RIP convergence

## Services Testing Results

| Service      | Test Description                          | Result  | Notes                              |
|--------------|-------------------------------------------|---------|------------------------------------|
| DHCP         | Clients receive IP, gateway, DNS          | Success | All VLANs                              |
| Inter-VLAN   | Communication within campus               | Success | Local on L3 switches                   |
| Inter-Site   | Main ↔ Branch connectivity                | Success | Via RIP                                |
| DNS          | Name resolution (www, ftp, mail)          | Success | Internal DNS used                      |
| Email        | Send/receive across campuses              | Success | SMTP auth disabled                     |
| FTP          | Upload/download files                     | Success | Authenticated & anonymous access       |
| Web          | Access school website via browser         | Success | HTTP/HTTPS with custom content         |

## Best Practices & Design Principles
- Hierarchical design (access → distribution → core)
- VLAN segmentation for security and broadcast control
- Layer 3 switching for high-performance inter-VLAN routing
- Routed links to routers (avoiding router-on-a-stick)
- Static IPs for servers with DHCP exclusions
- Dynamic routing for scalability
- Internal DNS for centralized name management
- NAT for private-to-external access simulation

## Conclusion

This project successfully demonstrates a realistic, enterprise-grade school network incorporating modern Cisco technologies. It supports typical school operations including web access, email communication, file sharing, and administrative segmentation while maintaining full connectivity across multiple sites.

The implementation aligns with CCNA-level concepts: VLANs, trunking, inter-VLAN routing, DHCP, DNS, FTP, HTTP, RIP routing, and NAT.

**Packet Tracer File**: `school-network-final.pkt` (included in repository)

**End of Documentation**
