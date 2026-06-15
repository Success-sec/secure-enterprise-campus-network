## 2. ACCESS SWITCH 01 CONFIGURATION (LEFT SWITCH)

**Device**: Access-A (Access Layer L2 Switch)  
**Purpose**: Connects end-user devices (HR, IT, Finance workstations)  
**Connection**: Trunk to Core-Switch GigabitEthernet 1/0/1

```text
enable

configure terminal

hostname Access-A

! ===== VLAN CREATION =====
vlan 10
 name HR

vlan 20
 name IT_Security

vlan 30
 name Finance

vlan 99
 name Management

exit

! ===== TRUNK PORT TO CORE SWITCH =====
interface FastEthernet0/1
 switchport mode trunk
 switchport trunk native vlan 99
 no shutdown

exit

! ===== ACCESS PORTS FOR HR (VLAN 10) =====
interface range FastEthernet0/2 - 4
 switchport mode access
 switchport access vlan 10
 no shutdown

exit

! ===== ACCESS PORTS FOR IT SECURITY (VLAN 20) =====
interface range FastEthernet0/5 - 7
 switchport mode access
 switchport access vlan 20
 no shutdown

exit

! ===== ACCESS PORTS FOR FINANCE (VLAN 30) =====
interface range FastEthernet0/8 - 10
 switchport mode access
 switchport access vlan 30
 no shutdown

exit

end

write memory
