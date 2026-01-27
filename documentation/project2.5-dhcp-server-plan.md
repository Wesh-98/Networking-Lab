# Project 2.5: DHCP as Dedicated Server

## 🎯 Overview

Instead of using the router as a DHCP server (Project 2), we'll configure a **dedicated server** to handle DHCP services. This is more realistic for enterprise networks.

**Benefits of Dedicated DHCP Server:**
- ✅ Reduces router CPU load
- ✅ Centralized IP management
- ✅ More realistic enterprise setup
- ✅ Easier to manage large networks
- ✅ Additional learning about DHCP relay/helper addresses

---

## 🏗️ Network Design

### Current Setup (Project 2):
```
Router (192.168.10.1, .20.1, .99.1)
  ↓ (DHCP Server)
  ├─ VLAN 10: PC1 gets IP from router
  ├─ VLAN 20: PC2 gets IP from router
  └─ VLAN 99: Management
```

### New Setup (Project 2.5):
```
Router (192.168.10.1, .20.1, .99.1)
  ↓ (DHCP Relay Agent)
  ├─ VLAN 10: PC1 ───┐
  ├─ VLAN 20: PC2 ───┼─→ Forwarded to DHCP Server
  └─ VLAN 99: DHCP Server (192.168.99.10)
```

---

## 📋 Equipment Needed

### In Packet Tracer:
- ✅ Existing Router (Cisco 2911)
- ✅ Existing Switch (Cisco 2960)
- ✅ Existing PC1 (VLAN 10)
- ✅ Existing PC2 (VLAN 20)
- **➕ NEW: Server** (add to VLAN 99)

### Server Configuration:
- **Type:** Generic Server
- **VLAN:** 99 (Management)
- **Static IP:** 192.168.99.10
- **Subnet Mask:** 255.255.255.0
- **Gateway:** 192.168.99.1
- **Services:** DHCP Server

---

## 🔧 Configuration Steps

### Step 1: Add Server to Topology

**In Packet Tracer:**
1. Add **Server** from device list
2. Connect to switch port (e.g., Fa0/10)
3. Configure switch port for VLAN 99

**Switch Configuration:**
```cisco
Switch> enable
Switch# configure terminal
Switch(config)# interface fastEthernet 0/10
Switch(config-if)# description DHCP-Server
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 99
Switch(config-if)# no shutdown
Switch(config-if)# exit
Switch(config)# exit
Switch# write memory
```

---

### Step 2: Configure Static IP on Server

**On the Server:**
1. Click Server → Desktop → IP Configuration
2. Select **Static**
3. Configure:
   - **IP Address:** 192.168.99.10
   - **Subnet Mask:** 255.255.255.0
   - **Default Gateway:** 192.168.99.1
   - **DNS Server:** 8.8.8.8

**Verify connectivity:**
```
Server> ping 192.168.99.1    (should reach router)
Server> ping 192.168.99.2    (should reach switch)
```

---

### Step 3: Configure DHCP Service on Server

**On the Server:**
1. Click Server → **Services** tab
2. Select **DHCP** from left menu
3. **Service:** Turn ON

**Configure Pool for VLAN 10 (hometurf.local):**
- Click **Pool Name:** `VLAN10-Pool`
- **Default Gateway:** 192.168.10.1
- **DNS Server:** 8.8.8.8
- **Start IP Address:** 192.168.10.11
- **Subnet Mask:** 255.255.255.0
- **Maximum Number of Users:** 244
- Click **Add**

**Configure Pool for VLAN 20 (backbone.local):**
- Click **Pool Name:** `VLAN20-Pool`
- **Default Gateway:** 192.168.20.1
- **DNS Server:** 8.8.8.8
- **Start IP Address:** 192.168.20.11
- **Subnet Mask:** 255.255.255.0
- **Maximum Number of Users:** 244
- Click **Add**

**Save configuration**

---

### Step 4: Remove DHCP from Router

**On Router:**
```cisco
Router> enable
Router# configure terminal

! Disable DHCP service
Router(config)# no service dhcp

! Remove DHCP pools
Router(config)# no ip dhcp pool VLAN10
Router(config)# no ip dhcp pool VLAN20
Router(config)# no ip dhcp pool VLAN99

! Remove excluded addresses (optional)
Router(config)# no ip dhcp excluded-address 192.168.10.1
Router(config)# no ip dhcp excluded-address 192.168.20.1
Router(config)# no ip dhcp excluded-address 192.168.99.1

Router(config)# exit
Router# write memory
```

---

### Step 5: Configure DHCP Relay (Helper Address)

**Critical:** PCs in VLAN 10 and 20 won't know where the DHCP server is. The router needs to relay DHCP requests.

**On Router:**
```cisco
Router> enable
Router# configure terminal

! Configure helper address on VLAN 10 interface
Router(config)# interface gigabitEthernet 0/0.10
Router(config-subif)# ip helper-address 192.168.99.10
Router(config-subif)# exit

! Configure helper address on VLAN 20 interface
Router(config)# interface gigabitEthernet 0/0.20
Router(config-subif)# ip helper-address 192.168.99.10
Router(config-subif)# exit

! VLAN 99 doesn't need helper (server is on same subnet)

Router(config)# exit
Router# write memory
```

**What this does:**
- When PC1 (VLAN 10) sends DHCP broadcast
- Router receives it on Gi0/0.10
- Router forwards (unicast) to 192.168.99.10 (DHCP server)
- Server responds
- Router forwards response back to PC1

---

### Step 6: Test DHCP

**On PC1:**
```
1. Desktop → IP Configuration
2. Select DHCP
3. Should receive IP from server (192.168.10.11 or similar)
```

**On PC2:**
```
1. Desktop → IP Configuration
2. Select DHCP
3. Should receive IP from server (192.168.20.11 or similar)
```

---

## ✅ Verification Commands

### On Router:
```cisco
! Verify helper address is configured
! Method 1: Section view (recommended)
Router# show running-config | section interface GigabitEthernet0/0.10
Router# show running-config | section interface GigabitEthernet0/0.20

! Method 2: Short form
Router# show run int gi0/0.10
Router# show run int gi0/0.20

! Method 3: Search for helper addresses
Router# show run | include helper

! Should see:
!  ip helper-address 192.168.99.10

! Verify DHCP service is disabled
Router# show running-config | include service dhcp
! Should see: no service dhcp (or nothing)

! Check for DHCP relay statistics
Router# show ip dhcp relay information
```

### On Server (Packet Tracer):
1. Click Server → Services → DHCP
2. View **Pool Usage** section
3. Should show leases for PC1 and PC2

### On PCs:
```
PC1> ipconfig
! Should show IP from 192.168.10.x range

PC2> ipconfig
! Should show IP from 192.168.20.x range

! Test connectivity
PC1> ping 192.168.10.1     (gateway)
PC1> ping 192.168.99.10    (DHCP server)
PC1> ping 192.168.20.2     (PC2)
```

---

## 📊 Comparison: Router DHCP vs Server DHCP

| Aspect | Router DHCP (Project 2) | Dedicated Server (Project 2.5) |
|--------|------------------------|--------------------------------|
| **Setup Complexity** | Simple | More complex |
| **Router CPU Load** | Higher | Lower (just relay) |
| **Scalability** | Limited | High |
| **Management** | CLI-based | GUI + CLI options |
| **Enterprise Reality** | Small networks | Large networks |
| **Single Point of Failure** | Router | Server (can add redundancy) |
| **DHCP Features** | Basic | Advanced (reservations, etc.) |
| **Learning Value** | DHCP basics | DHCP relay, client-server |

---

## 🎓 Concepts Learned

### DHCP Relay Agent (ip helper-address):
- **Problem:** DHCP uses broadcasts, which don't cross VLANs
- **Solution:** Router forwards DHCP requests to server
- **How:** Converts broadcast to unicast to server IP
- **Protocols Relayed:** DHCP, TFTP, DNS, TACACS, NetBIOS

### Why Use Dedicated DHCP Server:
1. **Centralization:** One place to manage all IPs
2. **Scalability:** Can handle thousands of clients
3. **Redundancy:** Can have backup DHCP servers
4. **Features:** Advanced options (reservations, classes, etc.)
5. **Monitoring:** Easier to monitor and log
6. **Performance:** Router can focus on routing

---

## 🔧 Troubleshooting

### Issue: PC not getting IP from server

**Check 1: Server reachable?**
```cisco
Router# ping 192.168.99.10
```

**Check 2: Helper address configured?**
```cisco
Router# show running-config interface gi0/0.10
! Should see: ip helper-address 192.168.99.10
```

**Check 3: DHCP service on server?**
- Click Server → Services → DHCP
- Ensure service is ON
- Verify pools are configured

**Check 4: Switch port correct VLAN?**
```cisco
Switch# show vlan brief
! Server port should be in VLAN 99
```

**Check 5: PC can ping gateway?**
```
PC1> ping 192.168.10.1
```

---

### Issue: Router still acting as DHCP server

**Fix: Ensure router DHCP is disabled**
```cisco
Router# show ip dhcp binding
! Should show: % No bindings (or error if service disabled)

Router(config)# no service dhcp
```

---

## 📸 Screenshots to Take

For documentation:
1. **Topology** - Show server added to VLAN 99
2. **Server DHCP Config** - Services tab showing pools
3. **Router Helper Address** - show run interface gi0/0.10
4. **PC1 DHCP** - ipconfig showing IP from server
5. **PC2 DHCP** - ipconfig showing IP from server
6. **Server Lease Table** - Pool usage on server
7. **Successful Pings** - PC to server, PC to PC

---

## 💾 Configuration Files to Save

### Router Configuration:
```
show running-config | include helper
show running-config interface gi0/0.10
show running-config interface gi0/0.20
```

Save as: `configs/project2.5-router-dhcp-relay.txt`

### Switch Configuration:
```
show running-config interface fa0/10
show vlan id 99
```

Save as: `configs/project2.5-switch-server-port.txt`

### Server Configuration:
- Screenshot of DHCP service configuration
- Note pool settings in text file

Save as: `configs/project2.5-server-dhcp-pools.txt`

---

## 🎯 Benefits of This Approach

### Technical Benefits:
- ✅ More realistic enterprise setup
- ✅ Learn DHCP relay (ip helper-address)
- ✅ Understand client-server architecture
- ✅ Practice server configuration
- ✅ Reduced router load

### Career Benefits:
- 💼 Show understanding of scalable design
- 💼 Demonstrate both approaches (router vs server)
- 💼 Enterprise-level thinking
- 💼 More comprehensive portfolio

---

## 📚 Additional Configurations (Optional)

### DHCP Reservations on Server:
Reserve specific IPs for devices (like printers):
1. Server → Services → DHCP
2. Find device MAC address
3. Add reservation for specific IP

### Redundant DHCP Servers:
Add second server for failover:
```cisco
Router(config-subif)# ip helper-address 192.168.99.10
Router(config-subif)# ip helper-address 192.168.99.11
```

### DHCP Options:
Configure additional options:
- Option 66: TFTP server
- Option 150: Cisco IP phone server
- Custom options for specific needs

---

## 🎮 Packet Tracer Notes

### Server DHCP Limitations:
- Domain names not configurable (limitation of PT)
- Lease time fixed at default
- Some advanced options unavailable

### Workarounds:
- Document intended configuration in comments
- Mention limitations in documentation
- Explain how it would work on real hardware

---

## 📝 Documentation Updates

### For GitHub:
- Create: `configs/project2.5-dhcp-server-complete.txt`
- Update: README.md with Project 2.5
- Add: Comparison section (router vs server DHCP)
- Include: Helper address explanation

### Resume Bullet:
```
• Configured dedicated DHCP server for enterprise network with router acting 
  as DHCP relay agent using ip helper-address for inter-VLAN DHCP services
• Demonstrated understanding of scalable network design by migrating from 
  router-based DHCP to centralized server-based architecture
```

---

## ⚽ Soccer Theme Integration

### Server Name:
Call the server: **"Referee"**
- Like the ref in soccer, it manages the rules (IPs)
- Neutral party that keeps track of everything
- Makes decisions fairly for all VLANs

**Or:** **"Dugout"**
- Where substitutes (available IPs) wait
- Central management location
- Supports the whole team

---

## 🚀 Ready to Implement?

**Estimated Time:** 1 hour

**Difficulty:** Intermediate (builds on Project 2)

**Prerequisites:**
- ✅ Project 1 complete (VLANs working)
- ✅ Project 2 complete (understand DHCP)
- ✅ Understanding of broadcasts vs unicasts

---

## 📋 Quick Setup Checklist

- [ ] Add server to topology
- [ ] Connect server to switch port
- [ ] Configure switch port for VLAN 99
- [ ] Set static IP on server (192.168.99.10)
- [ ] Test connectivity (ping gateway)
- [ ] Enable DHCP service on server
- [ ] Configure DHCP pools (VLAN 10, 20)
- [ ] Remove DHCP from router
- [ ] Configure ip helper-address on router subinterfaces
- [ ] Test PC1 gets IP from server
- [ ] Test PC2 gets IP from server
- [ ] Verify inter-VLAN connectivity
- [ ] Take screenshots
- [ ] Save configurations
- [ ] Document results

---

**Want to start Project 2.5? This will make your lab even more realistic!** 🎯

*Next level: Add redundant DHCP server for high availability!*
