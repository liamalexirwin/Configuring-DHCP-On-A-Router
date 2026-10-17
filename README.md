DHCP Configuration on a Router Lab

Overview

Built on top of the previous two-network routing lab (Lab 3), this lab configures a router to act as a DHCP server for two separate networks. Instead of manually assigning static IP addresses to each PC, the router automatically distributes IP addresses to all devices, simulating how real office networks operate.

Network Topology



Environment
Component	Details
Software	Cisco Packet Tracer
Router	Cisco 2911 (Router0)
Switches	2x Cisco 2960-24TT (Switch A, Switch B)
End Devices	4x PCs (PC1, PC2, PC3, PC4)
Network A	192.168.1.0/24
Network B	192.168.2.0/24
Objectives
Configure the router as a DHCP server for two separate networks
Create DHCP pools for Network A and Network B
Exclude router gateway addresses from the DHCP pool
Switch all PCs from static IP addressing to automatic DHCP
Verify each PC received a DHCP-assigned IP address using ipconfig
Confirm cross-network connectivity is maintained using ping
Verify DHCP bindings on the router using show ip dhcp binding
What is DHCP?

DHCP (Dynamic Host Configuration Protocol) automatically assigns IP addresses to devices on a network. Without DHCP, an IT technician would need to manually configure the IP address, subnet mask, default gateway, and DNS server on every single device, which is impractical in any real office environment. DHCP eliminates this by letting the router handle IP assignment automatically whenever a device connects to the network.

Physical Connections
Device	Interface	Connected To	Interface
Router0	GigabitEthernet0/0	Switch A	GigabitEthernet0/1
Router0	GigabitEthernet0/1	Switch B	GigabitEthernet0/1
Switch A	FastEthernet0/1	PC1	—
Switch A	FastEthernet0/2	PC2	—
Switch B	FastEthernet0/1	PC3	—
Switch B	FastEthernet0/2	PC4	—
Router Interface Configuration
Interface	IP Address	Subnet Mask	Purpose
GigabitEthernet0/0	192.168.1.254	255.255.255.0	Gateway for Network A
GigabitEthernet0/1	192.168.2.254	255.255.255.0	Gateway for Network B
Original Static IP Addressing (Before DHCP)
Device	IP Address	Subnet Mask	Default Gateway
PC1	192.168.1.1	255.255.255.0	192.168.1.254
PC2	192.168.1.2	255.255.255.0	192.168.1.254
PC3	192.168.2.1	255.255.255.0	192.168.2.254
PC4	192.168.2.2	255.255.255.0	192.168.2.254
DHCP Configuration Steps
Step 1 — Exclude Router Gateway Addresses

Before creating DHCP pools, excluded the router's own IP addresses so they are never handed out to a PC:

ip dhcp excluded-address 192.168.1.254
ip dhcp excluded-address 192.168.2.254
Step 2 — Create DHCP Pool for Network A
ip dhcp pool NETWORK-A
network 192.168.1.0 255.255.255.0
default-router 192.168.1.254
dns-server 8.8.8.8
exit
Step 3 — Create DHCP Pool for Network B
ip dhcp pool NETWORK-B
network 192.168.2.0 255.255.255.0
default-router 192.168.2.254
dns-server 8.8.8.8
exit
Step 4 — Save the Configuration
end
write memory
Step 5 — Switch PCs from Static to DHCP

On each PC: Desktop → IP Configuration → Selected DHCP

Each PC displayed a DHCP request successful message and automatically received:

An IP address from its network pool
The correct subnet mask
The correct default gateway
Google's DNS server (8.8.8.8)
Verification
PC Side — ipconfig

Ran ipconfig on each PC to confirm DHCP assigned addresses correctly:

Show Image

PC3 received 192.168.2.2 automatically from the router, confirming the DHCP pool for Network B was working correctly.

Router Side — show ip dhcp binding

Verified all IP assignments from the router CLI:

show ip dhcp binding

This command displays a table of every IP address the router has handed out and which device received it, a critical command for troubleshooting DHCP issues in real IT support environments.

Connectivity Test — ping

Ran ping between PCs across both networks to confirm DHCP did not break routing:

Show Image

PC1 successfully pinged PC3 (192.168.2.2) across networks, confirming full connectivity was maintained after switching from static to DHCP. ✅

Router DHCP Commands Reference
ip dhcp excluded-address <ip>              # Exclude an IP from the pool
ip dhcp pool <name>                        # Create and name a DHCP pool
network <network> <subnet>                 # Define the network range
default-router <gateway-ip>               # Set the default gateway for clients
dns-server <dns-ip>                        # Set the DNS server for clients
exit                                       # Exit pool configuration
show ip dhcp binding                       # View all DHCP assigned addresses
show ip dhcp pool                          # View pool configuration and usage
show running-config                        # Verify full router configuration
What I Learned
How to configure a Cisco router as a DHCP server for multiple networks
Why excluding gateway and reserved addresses from DHCP pools prevents conflicts
How DHCP pools are scoped per network, each network needs its own pool
How to verify DHCP is working from both the client side (ipconfig) and the router side (show ip dhcp binding)
That cross-network routing continues to work correctly after switching from static to dynamic IP addressing
The difference between static and dynamic IP assignment and when each is appropriate
Why This Matters for IT Support

"I didn't get an IP address" and "I can't connect to the network" are among the most common help desk tickets in any IT support role. Understanding how DHCP works, how to configure it, verify it, and troubleshoot it, is a fundamental skill for any network support technician or IT administrator. The show ip dhcp binding command alone is something IT support techs run daily when diagnosing connectivity issues.

Lab Series
Lab	Description
Lab 1	Simple 2 PC network
Lab 2	Switch and 4 PC LAN
Lab 3	Two networks communicating through a router
Lab 4 (This Lab)	DHCP configuration on a router
Lab 5	DNS configuration
Lab 6	VLAN configuration
Next Steps
Configure DHCP relay agent for networks where the DHCP server is on a different subnet
Practice troubleshooting DHCP failures, wrong pool, excluded ranges, service not running
Combine DHCP with VLAN configuration for a more complex network simulation
