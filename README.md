# Networking-Lab-Project

Love this idea—Packet Tracer is *perfect* for building real, job-ready network skills at home. I’ll walk you through a **clean, progressive home lab** that starts simple and grows into something a **network engineer** would actually brag about in interviews.

No fluff. Just practical skills 👇

---

##  Network Engineer Home Lab (Cisco Packet Tracer)

### 🎯 Lab Goal

Build a **small enterprise-style network** that includes:

* VLANs
* Inter-VLAN routing
* DHCP
* NAT
* ACLs
* Basic troubleshooting

By the end, you’ll understand **how data actually moves**.

---

##  Step 1: Lab Topology (Start Simple)

### Devices to Add

In Packet Tracer, drag these into the workspace:

* **1 Router** → *Cisco 2911*
* **1 Switch** → *Cisco 2960*
* **2 PCs** → End devices

### Physical Connections

Use **Copper Straight-Through** cables:

```
PC1 → Switch
PC2 → Switch
Switch → Router (G0/0)
```

---

##  Step 2: Basic IP Addressing

### PC IP Configuration

**PC1**

```
IP: 192.168.1.10
Subnet: 255.255.255.0
Gateway: 192.168.1.1
```

**PC2**

```
IP: 192.168.1.20
Subnet: 255.255.255.0
Gateway: 192.168.1.1
```

### Router Configuration

```
enable
configure terminal
interface g0/0
 ip address 192.168.1.1 255.255.255.0
 no shutdown
exit
```

### ✅ Test

From PC1:

```
ping 192.168.1.1
ping 192.168.1.20
```

If this works—congrats, you just built your first LAN 🎉

---

##  Step 3: Add VLANs (Now We Get Serious)

### Create VLANs on the Switch

```
enable
configure terminal
vlan 10
 name SALES
vlan 20
 name IT
exit
```

### Assign Ports

```
interface fa0/1
 switchport mode access
 switchport access vlan 10

interface fa0/2
 switchport mode access
 switchport access vlan 20
```

Assign:

* PC1 → VLAN 10
* PC2 → VLAN 20

---

##  Step 4: Inter-VLAN Routing (Router-on-a-Stick)

### Configure Trunk Port

```
interface g0/1
 switchport mode trunk
```

### Router Subinterfaces

```
interface g0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0

interface g0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
```

### Update PC IPs

**PC1 (VLAN 10)**

```
192.168.10.10 /24
GW: 192.168.10.1
```

**PC2 (VLAN 20)**

```
192.168.20.10 /24
GW: 192.168.20.1
```

### ✅ Test

```
ping between VLANs
```

Boom—**Layer 3 routing achieved** 💪

---

##  Step 5: DHCP (Automated IPs)

### Router DHCP Configuration

```
ip dhcp excluded-address 192.168.10.1 192.168.10.10
ip dhcp excluded-address 192.168.20.1 192.168.20.10

ip dhcp pool VLAN10
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1

ip dhcp pool VLAN20
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
```

Set PCs to **DHCP** and test.

---

##  Step 6: ACLs (Real Security Skills)

### Example: Block VLAN 10 from VLAN 20

```
access-list 100 deny ip 192.168.10.0 0.0.0.255 192.168.20.0 0.0.0.255
access-list 100 permit ip any any

interface g0/0.10
 ip access-group 100 in
```

Now VLAN 10 can’t access IT 👀

---

##  Step 7: Internet Simulation (NAT)

Add:

* **Cloud**
* Connect Router G0/1 → Cloud

### NAT Configuration

```
access-list 1 permit 192.168.0.0 0.0.255.255

interface g0/1
 ip nat outside

interface g0/0
 ip nat inside

ip nat inside source list 1 interface g0/1 overload
```


---

##  Step 8: Troubleshooting Practice (Critical Skill)

Common issues:

* Wrong VLAN
* Missing gateway
* ACL blocking traffic
* Trunk misconfiguration

Commands to check:

```
show ip route
show vlan brief
show interfaces trunk
show ip dhcp binding
show access-lists
```
