# Cisco Show Command Syntax Reference

Quick reference for common "show" command patterns and syntax.

---

## 🔍 **Basic Show Commands**

### **View Entire Running Configuration**
```cisco
Router# show running-config
Router# show run                    (short form)
```

### **View Specific Configuration Sections**
```cisco
Router# show running-config | section dhcp
Router# show running-config | section interface
Router# show running-config | section vlan
```

---

## 🎯 **Interface-Specific Commands**

### **❌ WRONG - These Don't Work:**
```cisco
Router# show running-config interface GigabitEthernet0/0.10
% Invalid input detected at '^' marker.

Router# show run interface gi0/0.10
% Invalid input detected at '^' marker.
```

### **✅ CORRECT - Use These Instead:**

#### **Method 1: Section (Best for seeing full interface config)**
```cisco
Router# show running-config | section interface GigabitEthernet0/0.10
```

**Output:**
```
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
 ip helper-address 192.168.99.10
```

#### **Method 2: Short Form (Quick and easy)**
```cisco
Router# show run int gi0/0.10
```

**Output:** (Same as above)

#### **Method 3: Begin (Shows from that interface onward)**
```cisco
Router# show running-config | begin interface GigabitEthernet0/0.10
```

**Output:** (Shows that interface and everything after it)

---

## 🔎 **Search and Filter Commands**

### **Include (Show lines containing keyword)**
```cisco
Router# show running-config | include helper
Router# show running-config | include dhcp
Router# show running-config | include vlan
Router# show running-config | include ip address
```

**Example Output:**
```
 ip helper-address 192.168.99.10
 ip helper-address 192.168.99.10
 ip helper-address 192.168.99.10
```

### **Section (Show entire config section)**
```cisco
Router# show running-config | section dhcp
Router# show running-config | section interface
Router# show running-config | section line vty
```

### **Begin (Start from specific line)**
```cisco
Router# show running-config | begin interface GigabitEthernet0/0
Router# show running-config | begin ip dhcp
Router# show running-config | begin line vty
```

### **Exclude (Omit lines containing keyword)**
```cisco
Router# show running-config | exclude !
Router# show running-config | exclude unassigned
```

---

## 📊 **Multiple Interface Commands**

### **View All Subinterfaces**
```cisco
Router# show run | section interface GigabitEthernet0/0.10
Router# show run | section interface GigabitEthernet0/0.20
Router# show run | section interface GigabitEthernet0/0.99
```

### **Or Use Loop (if needed)**
```cisco
Router# show run int gi0/0.10
Router# show run int gi0/0.20
Router# show run int gi0/0.99
```

### **Search All Interfaces for Specific Config**
```cisco
Router# show run | include helper
Router# show run | include encapsulation
Router# show run | include ip address
```

---

## 🎯 **Interface Status Commands**

### **Quick Status View**
```cisco
Router# show ip interface brief
Router# show ip int br              (short form)
```

### **Detailed Interface Info**
```cisco
Router# show interfaces GigabitEthernet0/0.10
Router# show int gi0/0.10           (short form)
```

### **IP-Specific Interface Info**
```cisco
Router# show ip interface GigabitEthernet0/0.10
Router# show ip int gi0/0.10        (short form)
```

---

## 📋 **DHCP Commands**

### **View DHCP Configuration**
```cisco
Router# show running-config | section dhcp
Router# show running-config | include dhcp
Router# show running-config | include excluded
```

### **View DHCP Pools**
```cisco
Router# show ip dhcp pool
Router# show ip dhcp pool VLAN10
```

### **View DHCP Bindings**
```cisco
Router# show ip dhcp binding
```

### **View DHCP Statistics**
```cisco
Router# show ip dhcp server statistics
```

### **View DHCP Conflicts**
```cisco
Router# show ip dhcp conflict
```

---

## 🔌 **VLAN Commands (Switch)**

### **View VLAN Configuration**
```cisco
Switch# show vlan brief
Switch# show vlan id 10
Switch# show vlan name Users
```

### **View VLAN Config in Running-Config**
```cisco
Switch# show running-config | include vlan
Switch# show running-config | section vlan
```

### **View Trunk Status**
```cisco
Switch# show interfaces trunk
Switch# show interfaces gi0/1 switchport
Switch# show interfaces gi0/1 trunk
```

---

## 🔧 **Routing Commands**

### **View Routing Table**
```cisco
Router# show ip route
Router# show ip route connected
Router# show ip route static
```

### **View Specific Routes**
```cisco
Router# show ip route 192.168.10.0
Router# show ip route 192.168.20.0
```

---

## 🛡️ **Helper Address / DHCP Relay**

### **View Helper Addresses (Multiple Methods)**

#### **Method 1: Include (Quick search)**
```cisco
Router# show running-config | include helper
```
**Output:**
```
 ip helper-address 192.168.99.10
 ip helper-address 192.168.99.10
 ip helper-address 192.168.99.10
```

#### **Method 2: Section (See full interface config)**
```cisco
Router# show running-config | section interface GigabitEthernet0/0.10
```
**Output:**
```
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
 ip helper-address 192.168.99.10    ← Here it is!
```

#### **Method 3: Short form per interface**
```cisco
Router# show run int gi0/0.10
Router# show run int gi0/0.20
Router# show run int gi0/0.99
```

### **View DHCP Relay Statistics**
```cisco
Router# show ip dhcp relay information
Router# debug ip dhcp server events  (be careful with debug!)
```

---

## 💾 **Configuration Comparison**

### **Compare Running vs Startup**
```cisco
Router# show running-config
Router# show startup-config
```

### **Show Differences (Archive feature)**
```cisco
Router# show archive config differences
Router# show archive config differences system:running-config nvram:startup-config
```

---

## 📝 **Common Syntax Patterns**

### **Interface Commands Pattern:**
```
WRONG: show running-config interface <interface>
RIGHT: show running-config | section interface <interface>
RIGHT: show run int <interface>
```

### **Search Patterns:**
```
| include <keyword>    - Lines containing keyword
| section <keyword>    - Entire section containing keyword
| begin <keyword>      - Start from keyword
| exclude <keyword>    - Omit lines with keyword
```

### **Short Forms:**
```
show running-config  = show run
interface            = int
GigabitEthernet      = gi
FastEthernet         = fa
ip interface brief   = ip int br
```

---

## 🎯 **Project 2.5 Specific Commands**

### **Verify DHCP Server Setup**
```cisco
! On Router - Check helper addresses
Router# show run | include helper
Router# show run int gi0/0.10
Router# show run int gi0/0.20
Router# show run int gi0/0.99

! Verify DHCP is disabled on router
Router# show run | include "service dhcp"
Router# show ip dhcp pool
! Should show: no pools or error

! Check connectivity to server
Router# ping 192.168.99.10
```

### **On Switch - Verify Server Port**
```cisco
Switch# show vlan id 99
Switch# show run int fa0/3
Switch# show interfaces fa0/3 status
```

### **On Server (GUI - Packet Tracer)**
- Services → DHCP → View pools
- Check lease assignments

---

## 🐛 **Troubleshooting Commands**

### **If Helper Address Not Working**
```cisco
! Check it's configured
Router# show run int gi0/0.10

! Check interface is up
Router# show ip int br

! Check routing to server
Router# ping 192.168.99.10

! Enable debug (carefully!)
Router# debug ip dhcp server events
! To stop:
Router# undebug all
```

### **If DHCP Not Working**
```cisco
! On router
Router# show run | include helper
Router# ping 192.168.99.10

! On switch
Switch# show vlan id 99
Switch# show int fa0/3 status

! On server (check GUI)
Services → DHCP → Service: ON?
```

---

## 📚 **Command Hierarchy**

```
show
├── running-config
│   ├── (entire config)
│   ├── | include <keyword>
│   ├── | section <keyword>
│   ├── | begin <keyword>
│   └── | exclude <keyword>
│
├── ip
│   ├── interface brief
│   ├── interface <interface>
│   ├── route
│   ├── dhcp pool
│   ├── dhcp binding
│   └── dhcp server statistics
│
├── interfaces
│   ├── <interface>
│   ├── trunk
│   ├── status
│   └── switchport
│
└── vlan
    ├── brief
    ├── id <number>
    └── name <name>
```

---

## 💡 **Pro Tips**

### **Tip 1: Use Tab Completion**
```cisco
Router# show run<TAB>
Router# show running-config | sec<TAB>
Router# show running-config | section int<TAB>
```

### **Tip 2: Use Question Mark**
```cisco
Router# show ?
Router# show running-config ?
Router# show ip ?
```

### **Tip 3: Combine Filters**
```cisco
Router# show run | section interface | include helper
Router# show run | begin interface GigabitEthernet0/0 | include ip address
```

### **Tip 4: Save Output to File (Real Cisco)**
```cisco
Router# show running-config | redirect flash:backup.cfg
Router# show running-config > tftp://192.168.1.100/router-config.txt
```

### **Tip 5: Use "do" in Config Mode**
```cisco
Router(config)# do show ip int br
Router(config)# do show run | include helper
Router(config-if)# do show ip dhcp binding
```

---

## 📖 **Quick Reference Card**

**Print this and keep handy:**

```
INTERFACE CONFIG:
show run | section interface Gi0/0.10  ← Best
show run int gi0/0.10                   ← Quick

SEARCH:
show run | include <word>               ← Find lines
show run | section <word>               ← Full section
show run | begin <word>                 ← Start from here

DHCP:
show run | include dhcp                 ← DHCP config
show ip dhcp pool                       ← Pool status
show ip dhcp binding                    ← Active leases

HELPER ADDRESS:
show run | include helper               ← Find all helpers
show run int gi0/0.10                   ← Check one interface

INTERFACE STATUS:
show ip int br                          ← Quick overview
show interfaces gi0/0.10                ← Detailed info

VLAN (SWITCH):
show vlan brief                         ← All VLANs
show interfaces trunk                   ← Trunk status

ROUTING:
show ip route                           ← Routing table
ping <IP>                               ← Test connectivity
```

---

## ✅ **Most Common Commands for This Lab**

```cisco
# Router
show run | include helper
show run int gi0/0.10
show run int gi0/0.20
show run int gi0/0.99
show ip int br
show ip route
ping 192.168.99.10

# Switch
show vlan brief
show run int fa0/3
show interfaces trunk

# Both
show run
write memory
```

---

*Keep this guide handy for quick reference!*
