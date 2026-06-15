# SECURE ENTERPRISE ARCHITECTURE CONFIGURATIONS

HERE ARE THE COMPLETE PRODUCTION SCRIPTS APPLIED TO THE INFRASTRUCTURE.

## 1. CORE MULTILAYER SWITCH CONFIGURATION

```text
enable

configure terminal

hostname Core-Switch

vlan 10
 name HR

vlan 20
 name IT_Security

vlan 30
 name Finance

vlan 99
 name Management

exit

interface vlan 10
 ip address 10.10.10.1 255.255.255.0
 description Gateway_for_HR

interface vlan 20
 ip address 10.10.20.1 255.255.255.0
 description Gateway_for_IT

interface vlan 30
 ip address 10.10.30.1 255.255.255.0
 description Gateway_for_Finance

interface vlan 99
 ip address 10.10.99.1 255.255.255.0
 description Gateway_for_Management

exit

interface range g1/0/1 - 2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 99

exit

interface g1/0/24
 no switchport
 ip address 10.10.100.1 255.255.255.0
 no shutdown

exit

ip route 0.0.0.0 0.0.0.0 10.10.100.2

end

write memory

ip access-list extended BLOCK-HR
 deny ip 10.10.10.0 0.0.0.255 10.10.30.0 0.0.0.255
 permit ip any any

ip access-list extended BLOCK-FINANCE
 deny ip 10.10.30.0 0.0.0.255 10.10.10.0 0.0.0.255
 permit ip any any

interface vlan 10
 ip access-group BLOCK-HR in

exit

interface vlan 30
 ip access-group BLOCK-FINANCE in

exit

end

clear ip route *

write memory
