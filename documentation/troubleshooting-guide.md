# Network Troubleshooting Guide

Systematic approach to troubleshooting common network issues in this lab.

## 🔍 General Troubleshooting Methodology

### The 7-Step Approach

1. **Define the problem** - What's not working?
2. **Gather information** - Run verification commands
3. **Analyze information** - Look for patterns
4. **Eliminate possibilities** - Rule out working components
5. **Propose hypothesis** - What do you think is wrong?
6. **Test hypothesis** - Make changes and test
7. **Document solution** - Record what fixed it

---

## 🚨 Common Issues and Solutions

### Issue 1: PCs Cannot Ping Each Other

**Symptom:** PC1 in VLAN 10 cannot ping PC2 in VLAN 20

**Troubleshooting Steps:**

1. **Verify PC configuration**
   ```
   PC> ipconfig
   ```
   - Check IP address is correct
   - Check subnet mask
   - Check default gateway

2. **Test local connectivity**
   ```
   PC1> ping 192.168.10.1    (ping own gateway)
   ```
   - If this fails, problem is between PC and router

3. **Check VLAN assignment**
   ```
   Switch# show vlan brief
   Switch# show interfaces fa0/1 switchport
   ```
   - Ensure PC1 port is in VLAN 10
   - Ensure PC2 port is in VLAN 20

4. **Check trunk configuration**
   ```
   Switch# show interfaces trunk
   ```
   - Ensure trunk port exists
   - Verify VLANs are allowed on trunk
   - Check trunk is "trunking" not "auto"

5. **Check router subinterfaces**
   ```
   Router# show ip interface brief
   ```
   - All subinterfaces should be "up/up"
   - Physical interface Gi0/0 must be "up/up"

6. **Check routing**
   ```
   Router# show ip route
   ```
   - Should see connected routes for all VLANs

**Common Fixes:**
- `Router(config)# interface gi0/0` then `no shutdown`
- Re-check VLAN assignments on switch ports
- Verify trunk allowed VLANs: `switchport trunk allowed vlan 10,20,99`

---

### Issue 2: Trunk Not Working

**Symptom:** Trunk shows "down" or VLANs not passing

**Troubleshooting Steps:**

1. **Check physical connection**
   ```
   Switch# show interfaces gi0/1 status
   ```
   - Should show "connected"

2. **Verify trunk configuration**
   ```
   Switch# show interfaces gi0/1 switchport
   ```
   - Administrative Mode should be "trunk"
   - Operational Mode should be "trunk"

3. **Check allowed VLANs**
   ```
   Switch# show interfaces trunk
   ```
   - Look at "Vlans allowed on trunk"
   - Look at "Vlans allowed and active"

4. **Check for duplex mismatch**
   ```
   Switch# show interfaces gi0/1
   ```
   - Look for errors, drops, or collisions

**Common Fixes:**
```cisco
Switch(config)# interface gi0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20,99
Switch(config-if)# no shutdown
```

**Note:** On 2960 switches, do NOT use `switchport trunk encapsulation dot1q` (not supported)

---

### Issue 3: Router Subinterfaces Down

**Symptom:** Subinterfaces show "administratively down" or "down/down"

**Troubleshooting Steps:**

1. **Check physical interface status**
   ```
   Router# show ip interface brief
   ```
   - Gi0/0 MUST be "up/up" for subinterfaces to work

2. **Bring up physical interface**
   ```
   Router(config)# interface gi0/0
   Router(config-if)# no shutdown
   ```

3. **Verify subinterface configuration**
   ```
   Router# show running-config interface gi0/0.10
   ```
   - Check encapsulation dot1Q is present
   - Check IP address is configured

4. **Check for configuration errors**
   ```
   Router# show ip interface gi0/0.10
   ```
   - Look for any error messages

**Common Fix:**
```cisco
Router(config)# interface gi0/0
Router(config-if)# no shutdown
Router(config-if)# exit
```
This brings up physical interface, which brings up all subinterfaces

---

### Issue 4: DHCP Not Working

**Symptom:** PCs not receiving IP addresses via DHCP

**Troubleshooting Steps:**

1. **Verify DHCP service is enabled**
   ```
   Router# show running-config | include service dhcp
   ```
   - Should see `service dhcp`

2. **Check DHCP pool configuration**
   ```
   Router# show ip dhcp pool
   ```
   - Verify network, gateway, DNS are configured

3. **Check for IP conflicts**
   ```
   Router# show ip dhcp conflict
   ```
   - Clear conflicts if any: `clear ip dhcp conflict *`

4. **Verify excluded addresses**
   ```
   Router# show running-config | section dhcp
   ```
   - Ensure gateway IPs are excluded

5. **Check DHCP bindings**
   ```
   Router# show ip dhcp binding
   ```
   - Should see leased addresses

6. **On PC, release and renew**
   ```
   PC> ipconfig /release
   PC> ipconfig /renew
   ```

**Common Fixes:**
```cisco
Router(config)# service dhcp
Router# clear ip dhcp binding *
```

---

### Issue 5: Cannot Access Switch Management Interface

**Symptom:** Cannot ping or SSH to switch IP

**Troubleshooting Steps:**

1. **Verify SVI configuration**
   ```
   Switch# show ip interface brief
   ```
   - VLAN 99 interface should be "up/up"

2. **Check VLAN 99 exists**
   ```
   Switch# show vlan brief
   ```
   - VLAN 99 should be "active"

3. **Check default gateway**
   ```
   Switch# show running-config | include ip default-gateway
   ```
   - Should point to router: 192.168.99.1

4. **Test from same VLAN first**
   ```
   Router# ping 192.168.99.2
   ```
   - If this works, routing is fine

5. **Check SVI is not shutdown**
   ```
   Switch(config)# interface vlan 99
   Switch(config-if)# no shutdown
   ```

**Common Fix:**
```cisco
Switch(config)# interface vlan 99
Switch(config-if)# ip address 192.168.99.2 255.255.255.0
Switch(config-if)# no shutdown
Switch(config-if)# exit
Switch(config)# ip default-gateway 192.168.99.1
```

---

### Issue 6: Wrong VLAN Encapsulation

**Symptom:** Error: "Invalid input detected" when configuring trunk

**Troubleshooting Steps:**

1. **Identify switch model**
   ```
   Switch# show version
   ```

2. **Check if command is supported**
   - 2960 switches only support 802.1Q
   - Do NOT use: `switchport trunk encapsulation dot1q`

**Fix for 2960:**
```cisco
Switch(config)# interface gi0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20,99
```

**Fix for 2950/3560 (if needed):**
```cisco
Switch(config)# interface gi0/1
Switch(config-if)# switchport trunk encapsulation dot1q
Switch(config-if)# switchport mode trunk
```

---

### Issue 7: Interface Administratively Down

**Symptom:** Interface shows "administratively down/down"

**Cause:** Interface is manually shut down

**Fix:**
```cisco
Router(config)# interface gi0/0
Router(config-if)# no shutdown
```

**Verify:**
```cisco
Router# show ip interface brief
```
Should now show "up/up"

---

### Issue 8: Routing Table Missing Routes

**Symptom:** No connected routes in routing table

**Troubleshooting Steps:**

1. **Check routing table**
   ```
   Router# show ip route
   ```

2. **Verify interfaces are up**
   ```
   Router# show ip interface brief
   ```
   - All subinterfaces must be "up/up"

3. **Check interface has IP address**
   ```
   Router# show running-config interface gi0/0.10
   ```

**Fix:**
```cisco
# Bring up interfaces
Router(config)# interface gi0/0
Router(config-if)# no shutdown

# Verify subinterface config
Router(config)# interface gi0/0.10
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0
```

---

## 🛠️ Diagnostic Command Workflow

### For Connectivity Issues:

```
1. Start at the source (PC)
   PC> ipconfig
   PC> ping 192.168.10.1 (gateway)

2. Check switch
   Switch# show vlan brief
   Switch# show interfaces status
   Switch# show mac address-table

3. Check trunk
   Switch# show interfaces trunk

4. Check router
   Router# show ip interface brief
   Router# show ip route
   Router# ping 192.168.20.1

5. Test end-to-end
   PC1> ping 192.168.20.10
```

---

## 📊 Quick Reference Tables

### Interface Status Meanings

| Status | Line Protocol | Meaning | Action |
|--------|---------------|---------|--------|
| up | up | Working ✅ | None |
| up | down | Layer 2 problem | Check encapsulation, cables |
| down | down | Physical problem | Check cables, power |
| administratively down | down | Manually shutdown | `no shutdown` |

### Common Error Messages

| Error | Meaning | Fix |
|-------|---------|-----|
| "Invalid input detected" | Command not recognized | Check syntax, model support |
| "Incomplete command" | Missing parameters | Use `?` to see options |
| "Ambiguous command" | Not enough typed | Type more characters |
| "% VLAN does not exist" | VLAN not created | Create VLAN first |

---

## 🔬 Advanced Troubleshooting

### Using Debug (Carefully!)

```cisco
# Debug ICMP (ping)
Router# debug ip icmp

# Test
PC> ping 192.168.20.1

# Stop debugging
Router# undebug all
```

**⚠️ Warning:** Debug commands can overwhelm the console. Use sparingly!

### Packet Tracer Simulation Mode

1. Click "Simulation" at bottom right
2. Click "Add Simple PDU" (envelope icon)
3. Click source device, then destination
4. Click "Play" to watch packet travel
5. Look for red X's (failures)
6. Click on packet to see details

---

## 📝 Documentation Template

When you solve an issue, document it:

```
Date: January 23, 2026
Issue: PCs could not ping each other
Symptoms: PC1 could ping gateway, but not PC2
Root Cause: Physical interface Gi0/0 was shutdown
Solution: Router(config)# interface gi0/0 → no shutdown
Verification: PC1> ping 192.168.20.10 → Success
Lesson Learned: Always check physical interface status first
```

---

## 🎯 Prevention Tips

1. **Always save configs:** `write memory` after changes
2. **Verify before moving on:** Test each step
3. **Document as you go:** Keep notes
4. **Use consistent naming:** Makes troubleshooting easier
5. **Label physical cables:** Prevents confusion
6. **Keep configs backed up:** Copy to TFTP/USB
7. **Test incrementally:** Don't configure everything at once

---

## 📞 When to Ask for Help

If you've tried everything and still stuck:
1. Document what you've tried
2. Show your configuration files
3. Share verification command outputs
4. Explain expected vs actual behavior

**Places to get help:**
- Cisco Learning Network
- Reddit: r/ccna, r/networking
- Discord: Cisco certification servers
- Stack Overflow

---

*Remember: Every network engineer has been stuck troubleshooting. It's part of learning!*
