# Network Topology

## Lab Network Design

This lab implements a hierarchical network design with VLAN segmentation and inter-VLAN routing.

## Topology Diagram

```
                       Internet
                          |
                    (Future - Project 3)
                          |
                  ┌───────┴────────┐
                  │  Router 2911   │
                  │   (Router0)    │
                  │   Gi0/0 Trunk  │
                  └───────┬────────┘
                          │ 802.1Q Trunk
                          │ VLANs: 10, 20, 99
                          │
                  ┌───────┴────────┐
                  │  Switch 2960   │
                  │   (Switch0)    │
                  │   Gi0/1 Trunk  │
                  └───┬────────┬───┘
                      │        │
            Fa0/1     │        │     Fa0/2
           VLAN 10    │        │    VLAN 20
                      │        │
                  ┌───┴───┐  ┌─┴────┐
                  │  PC1  │  │ PC2  │
                  │ Users │  │Servers│
                  └───────┘  └──────┘
```

## Detailed Connection Table

| Device | Interface | Connected To | Type | VLAN/Encapsulation |
|--------|-----------|--------------|------|-------------------|
| Router0 | Gi0/0 | Switch0 Gi0/1 | Trunk | 802.1Q (physical) |
| Router0 | Gi0/0.10 | - | Subinterface | VLAN 10 |
| Router0 | Gi0/0.20 | - | Subinterface | VLAN 20 |
| Router0 | Gi0/0.99 | - | Subinterface | VLAN 99 |
| Switch0 | Gi0/1 | Router0 Gi0/0 | Trunk | 802.1Q |
| Switch0 | Fa0/1 | PC1 | Access | VLAN 10 |
| Switch0 | Fa0/2 | PC2 | Access | VLAN 20 |

## IP Addressing Scheme

### VLANs

| VLAN ID | Name | Subnet | Network | Broadcast | Usable IPs | Purpose |
|---------|------|--------|---------|-----------|------------|---------|
| 10 | Users | 192.168.10.0/24 | 192.168.10.0 | 192.168.10.255 | 192.168.10.1-254 | End user devices |
| 20 | Servers | 192.168.20.0/24 | 192.168.20.0 | 192.168.20.255 | 192.168.20.1-254 | Server devices |
| 99 | Management | 192.168.99.0/24 | 192.168.99.0 | 192.168.99.255 | 192.168.99.1-254 | Device management |

### Device IP Assignments

#### Router Interfaces
| Interface | IP Address | Subnet Mask | Purpose |
|-----------|------------|-------------|---------|
| Gi0/0 | No IP | - | Physical trunk interface |
| Gi0/0.10 | 192.168.10.1 | 255.255.255.0 | VLAN 10 gateway |
| Gi0/0.20 | 192.168.20.1 | 255.255.255.0 | VLAN 20 gateway |
| Gi0/0.99 | 192.168.99.1 | 255.255.255.0 | VLAN 99 gateway |

#### Switch Management
| Interface | IP Address | Subnet Mask | Gateway | Purpose |
|-----------|------------|-------------|---------|---------|
| VLAN 99 | 192.168.99.2 | 255.255.255.0 | 192.168.99.1 | Management access |

#### End Devices
| Device | VLAN | IP Address | Subnet Mask | Gateway | DNS |
|--------|------|------------|-------------|---------|-----|
| PC1 | 10 | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 | 8.8.8.8 |
| PC2 | 20 | 192.168.20.10 | 255.255.255.0 | 192.168.20.1 | 8.8.8.8 |

### Reserved IP Addresses (Excluded from DHCP)

When DHCP is implemented in Project 2:

| VLAN | Reserved Range | Purpose |
|------|---------------|---------|
| 10 | 192.168.10.1 - 192.168.10.10 | Router, static devices |
| 20 | 192.168.20.1 - 192.168.20.10 | Router, servers |
| 99 | 192.168.99.1 - 192.168.99.5 | Router, switch |

## Traffic Flow

### Inter-VLAN Communication Example

**PC1 (VLAN 10) → PC2 (VLAN 20)**

1. PC1 sends packet to 192.168.20.10
2. PC1 realizes destination is not local subnet
3. PC1 sends packet to default gateway (192.168.10.1)
4. Switch receives packet on Fa0/1 (VLAN 10)
5. Switch tags packet with VLAN 10 and sends up trunk (Gi0/1)
6. Router receives on Gi0/0, examines VLAN tag
7. Packet processed by subinterface Gi0/0.10
8. Router checks routing table, finds route to 192.168.20.0/24
9. Router forwards to subinterface Gi0/0.20
10. Router tags packet with VLAN 20
11. Packet sent down trunk to switch
12. Switch receives packet, reads VLAN 20 tag
13. Switch forwards packet out Fa0/2 (VLAN 20)
14. PC2 receives packet

### Same VLAN Communication Example

**PC1 (VLAN 10) → Another device in VLAN 10**

1. PC1 realizes destination is in same subnet
2. PC1 sends packet directly (no router needed)
3. Switch receives on Fa0/1 (VLAN 10)
4. Switch forwards only to ports in VLAN 10
5. Destination receives packet

## Scalability

### Adding More Devices

**To add another PC in VLAN 10:**
1. Connect to any available switch port
2. Configure port as access port in VLAN 10
3. Assign IP from 192.168.10.11-254

**To add a new VLAN:**
1. Create VLAN on switch
2. Create subinterface on router with new VLAN ID
3. Assign IP address to subinterface
4. Add VLAN to trunk allowed list
5. Assign switch ports to new VLAN

### Limitations of Router-on-a-Stick

- Single point of failure (one link between router and switch)
- Bandwidth bottleneck (all inter-VLAN traffic uses one link)
- Not suitable for very large networks
- Better alternatives: Layer 3 switching, separate router interfaces

## VLAN Design Rationale

### Why These VLANs?

**VLAN 10 (Users):**
- Purpose: End user workstations
- Security: Separate from servers
- Flexibility: Easy to add/remove users

**VLAN 20 (Servers):**
- Purpose: Server devices
- Security: Isolated from regular users
- Control: Restrict access via ACLs (Project 4)

**VLAN 99 (Management):**
- Purpose: Network device management
- Security: Separate management traffic
- Access: Can manage devices from any VLAN

## Future Expansion (Later Projects)

### Project 2 Additions:
- DHCP pools for automatic IP assignment
- Dynamic IP allocation

### Project 3 Additions:
- Internet connection
- NAT/PAT configuration
- Outside interface on router

### Project 4 Additions:
- ACLs to control inter-VLAN traffic
- Security policies

### Project 5 Additions:
- SSH access to devices
- Enhanced security features

## Physical vs Packet Tracer Differences

### Packet Tracer:
- Uses 2911 router (GigabitEthernet interfaces)
- Uses 2960 switch
- Simulated environment
- Easy to reset and rebuild

### Physical Equipment:
- Uses 800 series router (FastEthernet interfaces)
- Uses 2940 switch
- Real hardware
- More realistic experience

Both implementations achieve the same learning objectives!

## Best Practices Implemented

✅ Management VLAN separate from user VLANs
✅ Descriptive VLAN names
✅ Consistent IP addressing scheme
✅ Reserved addresses excluded from DHCP
✅ Documentation of all assignments
✅ Scalable design for future growth

---

*This topology serves as the foundation for all 6 lab projects.*
