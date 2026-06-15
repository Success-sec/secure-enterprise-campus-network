# Secure Enterprise Campus Network & Perimeter Firewall Design

##  Project Overview
This project demonstrates the design and implementation of a secure, production-ready enterprise network architecture using Cisco Packet Tracer. The infrastructure leverages a Layer 3 Multilayer Switch at the core to handle high-speed internal routing, isolates departmental traffic using Virtual Local Area Networks (VLANs), and enforces perimeter security via a Cisco ASA Firewall.

---

##  Network Architecture & VLAN Breakdown

The internal network is segmented into distinct broadcast domains to optimize bandwidth, restrict unnecessary broadcast traffic, and enforce security policies.

| VLAN ID | VLAN Name | Subnet Range | Security Strategy / Purpose |
| :--- | :--- | :--- | :--- |
| **VLAN 10** | `HR` | `10.10.10.0/24` | Restricted. Blocked from accessing Finance; allowed to reach IT and the external internet. |
| **VLAN 20** | `IT_Security` | `10.10.20.0/24` | Network Administration. Full access to all internal departments and edge boundaries for management. |
| **VLAN 30** | `Finance` | `10.10.30.0/24` | Highly Restricted. Blocked from accessing HR; allowed to reach IT and the external internet. |
| **VLAN 99** | `Management` | `10.10.99.0/24` | Isolated management lane for switches and infrastructure access. Holds the Native VLAN. |

### 🔒 The Security Design of VLAN 99 (Management & Native)
In a standard out-of-the-box configuration, all Cisco switch ports belong to VLAN 1 by default. Leaving a network this way exposes it to severe security flaws. In this project, infrastructure security was hardened using **VLAN 99**:

1. **Native VLAN Modification:** The Native VLAN on all 802.1Q trunk links was explicitly changed from VLAN 1 to VLAN 99. This mitigates **VLAN Hopping attacks**, preventing malicious users from tagging their own packets to sneak into other secure departments.
2. **Management Isolation:** Switch management interfaces (SVI) are assigned to this isolated network lane, ensuring that regular user traffic from HR or Finance cannot interact with or intercept device management streams.

---

##  Core Technical Implementation Details

### 1. Layer 3 Core Routing (Inter-VLAN)
Instead of relying on a traditional router (Router-on-a-Stick), a **Multilayer Switch** functions as the high-speed backbone core. 
* Enabled global routing capabilities using the `ip routing` command.
* Configured **Switched Virtual Interfaces (SVIs)** for VLANs 10, 20, and 30 to serve as the default gateways for end-user PCs.
* Established an explicit **Routed Port** connecting the switch directly to the ASA Firewall using a `/24` transit network (`10.10.100.0/24`).

### 2. Traffic Control & Security Enforcements (ACLs)
To prevent unauthorized departmental communication, **Named Extended Access Control Lists (ACLs)** were deployed directly on the core switch's virtual interfaces:
* **`BLOCK-HR` (Applied Inbound on SVI 10):** Evaluates traffic leaving the HR subnet. It drops packets destined for Finance (`10.10.30.0/24`) but permits traffic going anywhere else (`permit ip any any`), ensuring internet and IT access remain operational.
* **`BLOCK-FINANCE` (Applied Inbound on SVI 30):** Evaluates traffic leaving the Finance subnet, dropping packets destined for HR (`10.10.10.0/24`) while permitting all other communication.
* **IT Security Bypass:** No ACL rules are applied to SVI 20, allowing network administrators full, unhindered visibility across the enterprise environment.

### 3. Perimeter Firewall & Edge Integration
A **Cisco ASA Firewall** protects the boundary between the internal network and the public web. 
* **Static Return Route:** Because the firewall only directly knows about its transit interface network, a static summary route (`route inside 10.10.0.0 255.255.0.0 10.10.100.1`) was implemented. This instructs the firewall to securely pass all return packets destined for any internal VLAN back to the Multilayer Switch.
* **Modular Policy Framework (MPF) Tuning:** Cisco ASA firewalls block ICMP by default. The global security policy was modified via the command line (`inspect icmp`) to track and safely permit echo replies, facilitating reliable technical troubleshooting.

---

##  Verification & Proof of Connectivity

Network functionality and security enforcement were verified using targeted ICMP ping simulations within Packet Tracer:

* **HR to Finance:** Pings return `Destination host unreachable` (ACL Successfully Dropped).
* **HR to IT / Firewall:** Pings return `Success` (Traffic Correctly Permitted).
* **IT Security to All Networks:** Pings return `Success` (Admin Management Verified).

---

##  Repository Structure

---

##  Key Learning Outcomes

This lab demonstrates:
- ✅ Multi-layer enterprise network design
- ✅ VLAN creation and segmentation
- ✅ Inter-VLAN routing via Multilayer Switch
- ✅ Named Extended ACLs for traffic control
- ✅ VLAN hopping attack mitigation
- ✅ Firewall perimeter security
- ✅ Static routing and network troubleshooting
- ✅ Cisco ASA configuration fundamentals

---

##  How to Use This Lab

1. **Open Packet Tracer**: Load the `.pkt` file from the `packet-tracer/` folder
2. **Review Documentation**: Start with README.md (you're reading it!)
3. **Study Configurations**: Review CONFIGURATIONS.md for device setup
4. **Follow Deployment**: Use DEPLOYMENT_GUIDE.md for step-by-step implementation
5. **Test Connectivity**: Run ping tests to verify routing and ACL enforcement
6. **Troubleshoot Issues**: Consult TROUBLESHOOTING.md if problems arise

---

##  Technologies & Concepts Used

- **Cisco Packet Tracer** - Network simulation software
- **Multilayer Switches (L3)** - Core routing infrastructure
- **VLANs** - Departmental network segmentation
- **Switched Virtual Interfaces (SVIs)** - Logical routing interfaces
- **Named Extended ACLs** - Traffic filtering policies
- **Cisco ASA Firewall** - Perimeter security appliance
- **Static Routing** - Manual route configuration
- **802.1Q Trunking** - VLAN tagging across links

---

##  Network Design Principles

### Defense in Depth
Multiple security layers protect the network:
1. VLAN isolation (Layer 2 segmentation)
2. ACLs on SVIs (Layer 3 filtering)
3. Firewall policies (perimeter security)

### Principle of Least Privilege
- HR and Finance cannot communicate (minimum required access)
- IT Security has full administrative access
- Management VLAN isolated for infrastructure only

### Scalability & Best Practices
- Modular design allows easy addition of new departments
- Clear VLAN numbering scheme (10s = departments, 99 = management)
- Documented security policies for compliance

---

## 👤 Author

**Success Chidiebere** - Networking & Cybersecurity Student

---

## License

This project is open source and available under the MIT License.

---

**Last Updated**: June 15, 2026

*This lab is a practical demonstration of enterprise network security principles and is intended for educational purposes.*
