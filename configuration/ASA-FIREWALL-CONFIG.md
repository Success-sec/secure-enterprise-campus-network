
---

## 📋 **CISCO ASA FIREWALL - FORMATTED**

```markdown
# CISCO ASA FIREWALL CONFIGURATION

**Device**: Cisco ASA Firewall  
**Purpose**: Perimeter security, stateful inspection & internet gateway  
**Security Level**: 100 (Inside/Trusted), 0 (Outside/Untrusted)  
**Critical**: Firewall ONLY connects to Core-Switch (10.10.100.1) for internal network access

---

## Configuration Script

```text
enable

configure terminal

hostname ASA-Firewall

! ===== INSIDE INTERFACE (CONNECTED TO CORE SWITCH) =====
interface GigabitEthernet1/1
 nameif inside
 security-level 100
 ip address 10.10.100.2 255.255.255.0
 no shutdown

exit

! ===== STATIC ROUTE FOR ALL INTERNAL NETWORKS =====
! This tells the firewall: All traffic to 10.10.0.0/16 goes through Core-Switch (10.10.100.1)
route inside 10.10.0.0 255.255.0.0 10.10.100.1

! ===== ENABLE ICMP INSPECTION (FOR PING/DIAGNOSTICS) =====
policy-map global_policy
 class inspection_default
 inspect icmp

exit

end

write memory
