# Network Lab Documentation

A comprehensive hands-on networking lab guide for learning Cisco routing, switching, VLANs, DHCP, NAT, ACLs, and security fundamentals.

## 📚 Overview

This repository contains complete documentation for building and configuring a multi-project network lab using either **Cisco Packet Tracer** (simulation) or **physical Cisco equipment**. The lab progressively builds networking skills through 6 practical projects.

## 🎯 Learning Objectives

- Configure VLANs and inter-VLAN routing (Router-on-a-Stick)
- Implement DHCP services per VLAN
- Configure NAT/PAT for internet connectivity
- Apply Access Control Lists (ACLs) for security
- Secure network devices (SSH, passwords, port security)
- Troubleshoot common network issues

## 🛠️ Equipment

### Packet Tracer Setup
- **Router:** Cisco 2911 (with GigabitEthernet interfaces)
- **Switch:** Cisco 2960
- **End Devices:** 2 PCs/Laptops

### Physical Equipment Setup
- **Router:** Cisco 800 series
- **Switch:** Cisco 2940
- **Cables:** Console cable, Ethernet cables
- **End Devices:** 2 test PCs/Laptops

## 📖 Lab Projects

### Project 1: VLANs and Inter-VLAN Routing ✅
**Status:** Completed  
**Duration:** 2-3 hours  
**Skills Learned:**
- Creating and configuring VLANs
- Assigning switch ports to VLANs
- Configuring trunk ports
- Implementing Router-on-a-Stick
- Configuring management VLAN for remote access

**Key Concepts:**
- VLAN segmentation
- 802.1Q trunking
- Subinterface configuration
- Switch Virtual Interface (SVI)

### Project 2: DHCP Per VLAN ✅
**Status:** Completed - January 2026  
**Duration:** 1-2 hours  
**Skills Learned:**
- Configuring DHCP pools per VLAN
- Excluding IP addresses
- Setting DNS and gateway options
- Verifying DHCP leases
- Implementing custom domain naming (soccer-themed: hometurf.local, backbone.local, gaffer.local)

**Key Concepts:**
- DHCP server configuration
- IP address management
- Domain name distribution
- Lease management
- Creative network design with thematic naming

### Project 3: NAT/PAT Configuration
**Duration:** 1-2 hours  
**Skills Learned:**
- Configuring NAT/PAT for internet access
- Inside/outside interface designation
- Access list configuration for NAT

### Project 4: Access Control Lists (ACLs)
**Duration:** 2-3 hours  
**Skills Learned:**
- Standard ACLs
- Extended ACLs
- ACL placement best practices
- Traffic filtering

### Project 5: Network Security
**Duration:** 2-3 hours  
**Skills Learned:**
- SSH configuration
- Password security
- Port security
- DHCP snooping
- Banner configuration

### Project 6: Troubleshooting
**Duration:** 1-2 hours  
**Skills Learned:**
- Common network issues
- Verification commands
- Systematic troubleshooting approach

## 📁 Repository Structure

```
networking-lab/
├── README.md                          # This file
├── guides/                            # Step-by-step lab guides
│   ├── packet-tracer-guide.md        # Packet Tracer version
│   ├── physical-equipment-guide.md   # Physical equipment version
│   └── original-guides/              # Original Word documents
├── configs/                           # Configuration files
│   ├── router-base-config.txt
│   ├── switch-base-config.txt
│   ├── project1-configs.txt
│   └── project2-configs.txt
├── screenshots/                       # Configuration verification screenshots
│   ├── project1-vlan-verification.png
│   ├── project1-trunk-config.png
│   └── project1-routing-test.png
├── packet-tracer-files/              # .pkt files for each project
│   ├── project1-vlans.pkt
│   ├── project2-dhcp.pkt
│   └── ...
└── documentation/                     # Additional reference materials
    ├── command-reference.md
    ├── troubleshooting-guide.md
    └── network-topology.md
```

## 🚀 Getting Started

### For Packet Tracer Users:
1. Download and install [Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer)
2. Clone this repository
3. Open the guide: `guides/packet-tracer-guide.md`
4. (Optional) Open the corresponding .pkt file from `packet-tracer-files/`
5. Follow the step-by-step instructions

### For Physical Equipment Users:
1. Gather required equipment (see Equipment section)
2. Clone this repository
3. Open the guide: `guides/physical-equipment-guide.md`
4. Connect equipment according to topology diagram
5. Follow the step-by-step instructions

## 🔧 Prerequisites

### Knowledge Prerequisites:
- Basic understanding of IP addressing
- Familiarity with subnetting
- Basic command line experience
- Understanding of OSI model (helpful but not required)

### Software Prerequisites:
- **For Simulation:** Cisco Packet Tracer 8.x or higher
- **For Physical:** Terminal emulator (PuTTY, Tera Term, or similar)

## 📊 Network Topology

### Lab Network Design:
```
                    Internet (Simulated)
                           |
                      [Router 2911/800]
                      Gi0/0 (Trunk)
                           |
                    [Switch 2960/2940]
                     /            \
            Fa0/1 (VLAN 10)    Fa0/2 (VLAN 20)
                /                  \
             [PC1]                [PC2]
           Users VLAN          Servers VLAN
```

### IP Addressing Scheme:
| VLAN | Name | Network | Gateway | Purpose |
|------|------|---------|---------|---------|
| 10 | Users | 192.168.10.0/24 | 192.168.10.1 | End user devices |
| 20 | Servers | 192.168.20.0/24 | 192.168.20.1 | Server devices |
| 99 | Management | 192.168.99.0/24 | 192.168.99.1 | Device management |

## 📸 Screenshots

See the `screenshots/` directory for configuration verification examples including:
- VLAN configuration verification
- Trunk port status
- Routing table verification
- Successful inter-VLAN ping tests

## 🎓 Skills Developed

By completing this lab series, you will gain hands-on experience with:

**Switching:**
- ✅ VLAN creation and management
- ✅ Trunk configuration (802.1Q)
- ✅ Switch port security
- ✅ Management VLAN configuration

**Routing:**
- ✅ Router-on-a-Stick configuration
- ✅ Subinterface creation
- ✅ Inter-VLAN routing
- ✅ Default gateway configuration

**Services:**
- ✅ DHCP server configuration
- ✅ DNS server configuration
- ✅ NAT/PAT implementation

**Security:**
- ✅ SSH configuration
- ✅ Access Control Lists
- ✅ Password encryption
- ✅ Port security

## 📝 Configuration Examples

### Quick Start - Basic VLAN Configuration:
```cisco
! Create VLANs
Switch(config)# vlan 10
Switch(config-vlan)# name Users
Switch(config-vlan)# exit

! Assign port to VLAN
Switch(config)# interface fastEthernet 0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
```

See `documentation/command-reference.md` for complete command reference.

## 🐛 Troubleshooting

Common issues and solutions:

**Issue: PCs can't ping each other**
- Check VLAN assignments
- Verify trunk configuration
- Ensure router subinterfaces are up
- Verify default gateway on PCs

**Issue: Trunk not working**
- Check for `switchport mode trunk` on both ends
- Verify allowed VLANs on trunk
- Ensure physical interface is up

See `documentation/troubleshooting-guide.md` for detailed troubleshooting steps.

## 🤝 Contributing

This is a personal learning documentation project, but suggestions and improvements are welcome! Feel free to:
- Open an issue for questions or corrections
- Submit a pull request for improvements
- Share your own lab variations

## 📄 License

This project is open source and available for educational purposes.

## 👤 Author

**Richard Njoroge**
- GitHub: [@Wesh-98](https://github.com/Wesh-98)
- LinkedIn: [Richard Njoroge](https://www.linkedin.com/in/richard-njoroge-895787210/)
- Email: wawerunjoroge98@gmail.com

## 🙏 Acknowledgments

- Cisco Networking Academy for Packet Tracer
- Online networking communities for troubleshooting help
- Various networking tutorials and resources

## 📚 Additional Resources

- [Cisco IOS Command Reference](https://www.cisco.com/c/en/us/support/ios-nx-os-software/ios-15-4m-t/products-command-reference-list.html)
- [Cisco Packet Tracer Tutorials](https://www.netacad.com/courses/packet-tracer)
- [SubnettingPractice.com](https://subnettingpractice.com/)

## 🗓️ Project Timeline

- **January 2026:** Project 1 completed (VLANs & Router-on-a-Stick)
- **January 2026:** Project 2 completed (DHCP Per VLAN with soccer-themed domains)
- **Ongoing:** Projects 3-6 in progress

---

**⭐ If you find this helpful, please give it a star!**

*Last Updated: January 2026*
