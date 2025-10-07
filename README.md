# Cisco Packet Tracer Command Cheat Sheet

## Basic Device Configuration

### Entering Configuration Modes
```
Router> enable                          # Enter privileged EXEC mode
Router# configure terminal              # Enter global configuration mode
Router(config)# interface fa0/0         # Enter interface configuration mode
Router(config-if)# exit                 # Return to previous mode
Router(config)# end                     # Return to privileged EXEC mode
```

### Setting Hostname and Domain Name
```
Router(config)# hostname R1             # Set device hostname
R1(config)# ip domain-name example.com  # Set domain name (required for SSH)
```

## Password Configuration

### Console Password
```
Router(config)# line console 0
Router(config-line)# password cisco123
Router(config-line)# login
Router(config-line)# exit
```

### Enable Password (Privileged EXEC Mode)
```
Router(config)# enable password cisco123        # Unencrypted (not recommended)
Router(config)# enable secret strongpass        # Encrypted (recommended)
```

### VTY Lines (Telnet/SSH Access)
```
Router(config)# line vty 0 4
Router(config-line)# password cisco123
Router(config-line)# login
Router(config-line)# transport input ssh        # SSH only
Router(config-line)# transport input telnet     # Telnet only
Router(config-line)# transport input all        # Both SSH and Telnet
Router(config-line)# exit
```

### Encrypt All Passwords
```
Router(config)# service password-encryption     # Encrypts plaintext passwords
```

## SSH Configuration

### Complete SSH Setup
```
Router(config)# hostname R1
Router(config)# ip domain-name example.com
Router(config)# crypto key generate rsa
# When prompted, enter modulus size: 1024 or 2048

Router(config)# username admin privilege 15 secret adminpass
Router(config)# line vty 0 4
Router(config-line)# login local               # Use local username database
Router(config-line)# transport input ssh
Router(config-line)# exit
Router(config)# ip ssh version 2               # Use SSH version 2
```

### Verify SSH
```
Router# show ip ssh
Router# show ssh
```

## Interface Configuration

### Basic Interface Setup
```
Router(config)# interface gigabitEthernet 0/0
Router(config-if)# ip address 192.168.1.1 255.255.255.0
Router(config-if)# description Link to LAN
Router(config-if)# no shutdown                  # Enable interface
Router(config-if)# exit
```

### Switch Interface Configuration
```
Switch(config)# interface fastEthernet 0/1
Switch(config-if)# description PC1 Connection
Switch(config-if)# no shutdown
Switch(config-if)# exit
```

## Static Routing

### IPv4 Static Route
```
Router(config)# ip route 192.168.2.0 255.255.255.0 10.0.0.2
# Format: ip route [destination_network] [subnet_mask] [next_hop_ip]

Router(config)# ip route 192.168.3.0 255.255.255.0 serial 0/0/0
# Using exit interface instead of next-hop IP
```

### Default Static Route
```
Router(config)# ip route 0.0.0.0 0.0.0.0 10.0.0.1
# Sends all unknown traffic to 10.0.0.1
```

### Verify Static Routes
```
Router# show ip route
Router# show ip route static
```

## Dynamic Routing

### RIP (Routing Information Protocol)
```
Router(config)# router rip
Router(config-router)# version 2
Router(config-router)# network 192.168.1.0
Router(config-router)# network 10.0.0.0
Router(config-router)# no auto-summary
Router(config-router)# exit
```

### EIGRP (Enhanced Interior Gateway Routing Protocol)
```
Router(config)# router eigrp 100              # 100 is the AS number
Router(config-router)# network 192.168.1.0 0.0.0.255
Router(config-router)# network 10.0.0.0 0.0.0.3
Router(config-router)# no auto-summary
Router(config-router)# exit
```

### OSPF (Open Shortest Path First)
```
Router(config)# router ospf 1                 # 1 is the process ID
Router(config-router)# network 192.168.1.0 0.0.0.255 area 0
Router(config-router)# network 10.0.0.0 0.0.0.3 area 0
Router(config-router)# exit
```

### Verify Dynamic Routing
```
Router# show ip protocols
Router# show ip route rip
Router# show ip route eigrp
Router# show ip route ospf
Router# show ip eigrp neighbors
Router# show ip ospf neighbor
```

## VLAN Configuration

### Creating VLANs
```
Switch(config)# vlan 10
Switch(config-vlan)# name Sales
Switch(config-vlan)# exit

Switch(config)# vlan 20
Switch(config-vlan)# name Engineering
Switch(config-vlan)# exit
```

### Assigning Ports to VLANs
```
Switch(config)# interface fastEthernet 0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
Switch(config-if)# exit

Switch(config)# interface range fa0/2-5
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 20
Switch(config-if-range)# exit
```

### Trunk Port Configuration
```
Switch(config)# interface gigabitEthernet 0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20,30
Switch(config-if)# switchport trunk native vlan 99
Switch(config-if)# exit
```

### Verify VLAN Configuration
```
Switch# show vlan brief
Switch# show vlan id 10
Switch# show interfaces trunk
Switch# show interfaces fa0/1 switchport
```

## Router-on-a-Stick (Inter-VLAN Routing)

### Configuring Subinterfaces
```
Router(config)# interface gigabitEthernet 0/0
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# interface gigabitEthernet 0/0.10
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0
Router(config-subif)# exit

Router(config)# interface gigabitEthernet 0/0.20
Router(config-subif)# encapsulation dot1Q 20
Router(config-subif)# ip address 192.168.20.1 255.255.255.0
Router(config-subif)# exit
```

## DHCP Configuration

### DHCP Server on Router
```
Router(config)# ip dhcp excluded-address 192.168.1.1 192.168.1.10
Router(config)# ip dhcp pool LAN_POOL
Router(dhcp-config)# network 192.168.1.0 255.255.255.0
Router(dhcp-config)# default-router 192.168.1.1
Router(dhcp-config)# dns-server 8.8.8.8
Router(dhcp-config)# exit
```

### Verify DHCP
```
Router# show ip dhcp binding
Router# show ip dhcp pool
```

## Saving Configuration

### Save Running Configuration
```
Router# write memory                    # Save config
Router# copy running-config startup-config
Router# wr                              # Shortcut for write memory
```

### Erase Configuration
```
Router# erase startup-config
Router# reload
```

## Useful Show Commands

```
Router# show running-config             # Current configuration
Router# show startup-config             # Saved configuration
Router# show ip interface brief         # Interface status summary
Router# show interfaces                 # Detailed interface info
Router# show version                    # IOS version and hardware info
Router# show ip route                   # Routing table
Router# show protocols                  # Routed protocols status
Router# show arp                        # ARP table
Router# show mac-address-table          # MAC address table (switches)
Router# show cdp neighbors              # Cisco Discovery Protocol neighbors
```

## Troubleshooting Commands

```
Router# ping 192.168.1.1               # Test connectivity
Router# traceroute 192.168.1.1         # Trace route path
Router# debug ip routing               # Debug routing updates
Router# no debug all                   # Turn off all debugging
Router# show logging                   # View system logs
```

## Quick Tips

- Use Tab for command completion
- Use ? for help at any point
- Use Ctrl+C to abort commands
- Use Ctrl+Z to return to privileged EXEC mode from any config mode
- Use "do" prefix in config mode to run privileged EXEC commands (e.g., `do show ip route`)