# GNS3 Lab: Internet Connectivity via VMware NAT
This project demonstrates how to connect a virtualized GNS3 network topology to the live internet using VMware Workstation Pro and the GNS3 VM. It covers manual static IP configuration, default routing, and Network Address Translation (PAT) on a Cisco router.

## Topology Details
* Edge Router: Cisco C7200 Series (R1)
* Outside Subnet: 192.168.134.0/24 (VMware VMnet8 NAT - In My Case)
* Inside Subnet: 10.10.10.0/24
* Gateway IP: 192.168.134.2 (NAT Interface - In My Case, Check yours at VM Pro->Edit->Virtual Network Editor->VMnet8)

Configuration
1. Router Interface Setup
Assigning static IPs and defining NAT boundaries.

```
cisco
interface GigabitEthernet0/0
 ip address 192.168.134.10 255.255.255.0
 ip nat outside
 no shutdown

interface GigabitEthernet1/0
 ip address 10.10.10.1 255.255.255.0
 ip nat inside
 no shutdown
```
 
2. NAT and Routing
Implementing a static default route and Port Address Translation (PAT).

Bash

! Default route pointing to the VMware NAT gateway
ip route 0.0.0.0 0.0.0.0 192.168.134.2

! ACL to permit internal LAN traffic
access-list 1 permit 10.10.10.0 0.0.0.255

! Dynamic NAT (Overload)
ip nat inside source list 1 interface GigabitEthernet0/0 overload
3. DNS Configuration
Required for resolving external domain names.

Bash

ip domain-lookup
ip name-server 8.8.8.8
Troubleshooting Windows 11 Connectivity
On personal laptops, even with correct CLI commands, traffic is often blocked at the host level.

Network Profile: Ensure the host network is set to Private.

ICMP Rules: Enable Inbound ICMPv4 (Echo Request) in Windows Defender Firewall to allow ping responses.

VMware Settings: Verify that the GNS3 VM is bound to the correct VMnet adapter (usually VMnet8 for NAT).

Verification Commands
Bash

show ip route                # Confirm static default route
show ip nat translations      # Monitor active NAT sessions
show arp                     # Verify Layer 2 connectivity to NAT gateway
ping google.com              # End-to-end connectivity test
How to use this on GitHub:



