# Cisco IOS Command Reference

Quick reference guide for common Cisco IOS commands used in this lab.

## Table of Contents
- [Basic Navigation](#basic-navigation)
- [VLAN Commands](#vlan-commands)
- [Trunk Commands](#trunk-commands)
- [Router Configuration](#router-configuration)
- [DHCP Commands](#dhcp-commands)
- [Verification Commands](#verification-commands)
- [Troubleshooting Commands](#troubleshooting-commands)

---

## Basic Navigation

### Command Modes
```cisco
Router>                          # User EXEC mode
Router> enable                   # Enter privileged mode
Router#                          # Privileged EXEC mode
Router# configure terminal       # Enter global config mode
Router(config)#                  # Global configuration mode
Router(config)# interface gi0/0  # Interface configuration mode
Router(config-if)#               # Interface config mode
Router(config-if)# exit          # Go back one level
Router(config)# end              # Return to privileged mode
```

### Essential Commands
```cisco
Router# show running-config      # View current configuration
Router# show startup-config      # View saved configuration
Router# write memory             # Save configuration
Router# copy running-config startup-config  # Alternative save
Router# reload                   # Restart device
Router# show version             # View IOS version and hardware
Router# show ip interface brief  # Quick interface overview
Router# configure terminal       # Enter configuration mode
Router(config)# hostname Router1 # Set device hostname
Router(config)# no ip domain-lookup  # Disable DNS lookup (optional)
```

---

## VLAN Commands

### Creating VLANs
```cisco
Switch(config)# vlan 10
Switch(config-vlan)# name Users
Switch(config-vlan)# exit

Switch(config)# vlan 20
Switch(config-vlan)# name Servers
Switch(config-vlan)# exit

Switch(config)# vlan 99
Switch(config-vlan)# name Management
Switch(config-vlan)# exit
```

### Assigning Ports to VLANs
```cisco
Switch(config)# interface fastEthernet 0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
Switch(config-if)# no shutdown
Switch(config-if)# exit
```

### Configuring Multiple Ports at Once
```cisco
Switch(config)# interface range fa0/1-10
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 10
Switch(config-if-range)# exit
```

### Verification
```cisco
Switch# show vlan brief           # View VLAN assignments
Switch# show vlan id 10           # View specific VLAN
Switch# show interfaces status    # View port status with VLANs
Switch# show running-config | section vlan  # View VLAN config
```

---

## Trunk Commands

### Configuring Trunk Ports
```cisco
Switch(config)# interface gigabitEthernet 0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20,99
Switch(config-if)# no shutdown
Switch(config-if)# exit
```

### Trunk Verification
```cisco
Switch# show interfaces trunk              # View trunk status
Switch# show interfaces gi0/1 switchport   # Detailed switchport info
Switch# show interfaces gi0/1 trunk        # Specific trunk details
```

### Trunk Troubleshooting
```cisco
# If trunk not working, check:
Switch# show interfaces gi0/1 status
Switch# show interfaces gi0/1
Switch# show running-config interface gi0/1
```

---

## Router Configuration

### Basic Router Setup
```cisco
Router(config)# hostname Router1
Router(config)# no ip domain-lookup
Router(config)# enable secret cisco
Router(config)# line console 0
Router(config-line)# password console
Router(config-line)# login
Router(config-line)# exit
```

### Router-on-a-Stick (Subinterfaces)
```cisco
# Physical interface (no IP address)
Router(config)# interface gigabitEthernet 0/0
Router(config-if)# no ip address
Router(config-if)# no shutdown
Router(config-if)# exit

# Subinterface for VLAN 10
Router(config)# interface gigabitEthernet 0/0.10
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0
Router(config-subif)# exit

# Subinterface for VLAN 20
Router(config)# interface gigabitEthernet 0/0.20
Router(config-subif)# encapsulation dot1Q 20
Router(config-subif)# ip address 192.168.20.1 255.255.255.0
Router(config-subif)# exit

# Subinterface for VLAN 99
Router(config)# interface gigabitEthernet 0/0.99
Router(config-subif)# encapsulation dot1Q 99
Router(config-subif)# ip address 192.168.99.1 255.255.255.0
Router(config-subif)# exit
```

### Router Verification
```cisco
Router# show ip interface brief           # View all interfaces
Router# show interfaces gi0/0.10          # View subinterface details
Router# show ip route                     # View routing table
Router# show running-config | section interface  # View interface config
```

---

## DHCP Commands

### DHCP Configuration
```cisco
# Exclude addresses (FIRST!)
Router(config)# ip dhcp excluded-address 192.168.10.1 192.168.10.10
Router(config)# ip dhcp excluded-address 192.168.20.1 192.168.20.10
Router(config)# ip dhcp excluded-address 192.168.99.1 192.168.99.5

# Create DHCP pool for VLAN 10
Router(config)# ip dhcp pool VLAN10
Router(dhcp-config)# network 192.168.10.0 255.255.255.0
Router(dhcp-config)# default-router 192.168.10.1
Router(dhcp-config)# dns-server 8.8.8.8
Router(dhcp-config)# domain-name lab.local
Router(dhcp-config)# lease 7
Router(dhcp-config)# exit

# Create DHCP pool for VLAN 20
Router(config)# ip dhcp pool VLAN20
Router(dhcp-config)# network 192.168.20.0 255.255.255.0
Router(dhcp-config)# default-router 192.168.20.1
Router(dhcp-config)# dns-server 8.8.8.8
Router(dhcp-config)# exit
```

### DHCP Verification
```cisco
Router# show ip dhcp pool                 # View pool information
Router# show ip dhcp binding              # View active leases
Router# show ip dhcp server statistics    # View DHCP stats
Router# show ip dhcp conflict             # View IP conflicts
Router# show running-config | section dhcp  # View DHCP config
```

### DHCP Management
```cisco
Router# clear ip dhcp binding *           # Clear all bindings
Router# clear ip dhcp binding 192.168.10.11  # Clear specific binding
Router# clear ip dhcp conflict *          # Clear all conflicts
Router(config)# no service dhcp           # Disable DHCP service
Router(config)# service dhcp              # Enable DHCP service
```

---

## Verification Commands

### Interface Status
```cisco
show ip interface brief          # Quick interface overview
show interfaces                  # Detailed interface info
show interfaces status           # Port status summary
show interfaces gi0/0            # Specific interface details
show ip interface gi0/0          # IP-specific interface info
```

### VLAN Verification
```cisco
show vlan brief                  # VLAN summary
show vlan id 10                  # Specific VLAN details
show interfaces trunk            # Trunk port information
show interfaces switchport       # Switchport configuration
show mac address-table           # MAC address table
show mac address-table vlan 10   # MAC addresses in VLAN 10
```

### Routing Verification
```cisco
show ip route                    # Routing table
show ip route connected          # Connected routes only
show ip protocols                # Routing protocol info
ping 192.168.10.1                # Test connectivity
traceroute 192.168.20.1          # Trace packet path
```

### General Verification
```cisco
show running-config              # Current configuration
show startup-config              # Saved configuration
show version                     # System information
show clock                       # System time
show processes                   # CPU processes
show memory                      # Memory usage
```

---

## Troubleshooting Commands

### Basic Troubleshooting
```cisco
ping IP_ADDRESS                  # Test connectivity
traceroute IP_ADDRESS            # Trace route to destination
show arp                         # View ARP table
show cdp neighbors               # View connected Cisco devices
show cdp neighbors detail        # Detailed CDP information
```

### Interface Troubleshooting
```cisco
show interfaces gi0/0                    # Check interface status
show ip interface gi0/0                  # Check IP configuration
show controllers gi0/0                   # Hardware details
clear counters gi0/0                     # Reset interface counters
```

### VLAN Troubleshooting
```cisco
show vlan brief                          # Check VLAN assignments
show interfaces trunk                    # Verify trunk status
show interfaces gi0/1 switchport         # Detailed port config
show spanning-tree                       # Check spanning tree
```

### Configuration Troubleshooting
```cisco
show running-config                      # View current config
show startup-config                      # View saved config
show running-config | include keyword    # Search config
show running-config | section interface  # View interface section
show running-config | begin line vty     # Start from specific line
```

### Debug Commands (Use with Caution!)
```cisco
debug ip icmp                    # Debug ICMP (ping)
debug ip routing                 # Debug routing
debug ip dhcp server events      # Debug DHCP
undebug all                      # Stop all debugging
no debug all                     # Alternative stop command
```

---

## Save and Exit

### Saving Configuration
```cisco
Router# write memory                              # Save config
Router# copy running-config startup-config        # Alternative save
Router# write                                     # Short version
```

### Exiting
```cisco
Router(config-if)# exit          # Exit interface mode
Router(config)# exit             # Exit config mode
Router(config)# end              # Jump to privileged mode
Router# exit                     # Logout
```

### Erasing Configuration
```cisco
Router# write erase              # Erase startup config
Router# erase startup-config     # Alternative erase
Router# reload                   # Restart (will load default config)
```

---

## Keyboard Shortcuts

```
Ctrl+A    # Move cursor to beginning of line
Ctrl+E    # Move cursor to end of line
Ctrl+U    # Erase line
Ctrl+W    # Erase word
Ctrl+Z    # Exit to privileged mode
Ctrl+C    # Interrupt command
Ctrl+Shift+6  # Interrupt ping/traceroute
Tab       # Complete command
?         # Context-sensitive help
Space     # Next page in output
Enter     # Next line in output
Q         # Quit output display
```

---

## Configuration File Management

### Backup Configuration
```cisco
# Copy to TFTP server
Router# copy running-config tftp
Router# copy startup-config tftp

# Copy to USB (if available)
Router# copy running-config usbflash0:backup.cfg

# Display config
Router# show running-config
Router# show startup-config
```

### Restore Configuration
```cisco
# From TFTP
Router# copy tftp running-config
Router# copy tftp startup-config

# From USB
Router# copy usbflash0:backup.cfg running-config
```

---

## Tips and Best Practices

1. **Always save your configuration** after making changes: `write memory`
2. **Use `?` liberally** to explore available commands
3. **Use Tab completion** to speed up command entry
4. **Use `show running-config`** frequently to verify changes
5. **Test connectivity** with `ping` after configuration changes
6. **Document your changes** as you go
7. **Use `no` before a command** to remove configuration
8. **Be careful with `reload`** - save first!
9. **Use `do` in config mode** to run show commands: `do show ip int brief`
10. **Configure `enable secret`** before enabling remote access

---

*For complete command reference, visit [Cisco Command Reference](https://www.cisco.com/c/en/us/support/ios-nx-os-software/ios-15-4m-t/products-command-reference-list.html)*
