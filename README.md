# 🔐 Dynamic ARP Inspection & IP Source Guard | Cisco Packet Tracer

## 📌 Project Overview

This project demonstrates the concepts and configuration of **Dynamic ARP Inspection (DAI)** and **IP Source Guard (IPSG)** using Cisco networking technologies.

The lab builds on the previous **DHCP Snooping** configuration because DHCP Snooping provides the binding information required by both DAI and IP Source Guard.

The three Layer 2 security technologies work together:

```text
DHCP Snooping
      │
      ├──────────────► Dynamic ARP Inspection
      │
      └──────────────► IP Source Guard
```

The project focuses on protecting a switched network against **ARP spoofing, ARP poisoning, IP address spoofing, and unauthorized Layer 2 traffic**.

---

# 🎯 Project Objectives

The main objectives of this lab are to:

* Understand Dynamic ARP Inspection.
* Understand IP Source Guard.
* Understand the relationship between DHCP Snooping, DAI, and IPSG.
* Prevent invalid and malicious ARP packets.
* Reduce the risk of ARP spoofing and ARP poisoning attacks.
* Prevent IP address spoofing on untrusted Layer 2 interfaces.
* Configure trusted ports for Dynamic ARP Inspection.
* Configure IP Source Guard on access ports.
* Verify the Layer 2 security configuration.

---

# 🛡️ Technologies Covered

* Cisco Packet Tracer
* DHCP Snooping
* Dynamic ARP Inspection (DAI)
* IP Source Guard (IPSG)
* ARP Spoofing Prevention
* ARP Poisoning Prevention
* IP Address Spoofing Prevention
* Layer 2 Security
* VLAN 1
* Cisco IOS CLI

---

# 🔗 Relationship Between the Technologies

These security features are designed to work together.

### 1. DHCP Snooping

DHCP Snooping builds a **DHCP binding database** containing information such as:

```text
MAC Address
IP Address
VLAN
Interface
```

### 2. Dynamic ARP Inspection

DAI uses the DHCP Snooping binding database to validate ARP packets.

```text
ARP Packet
     │
     ▼
Dynamic ARP Inspection
     │
     ▼
Compare with DHCP Snooping Binding Database
     │
 ┌───┴────┐
 ▼        ▼
Valid    Invalid
 │        │
Allow    Drop
```

### 3. IP Source Guard

IP Source Guard uses DHCP Snooping information to restrict which IP address a host can use on an untrusted interface.

```text
Host
 │
 ▼
Untrusted Port
 │
 ▼
IP Source Guard
 │
 ├── Valid IP → Allow
 │
 └── Invalid IP → Block
```

---

# 🖥️ Lab Topology

The lab uses the same topology from the previous DHCP Snooping project.

The topology contains:

* Cisco Router
* Layer 2 Switch
* Legitimate DHCP Server
* Rogue/Fake DHCP Server
* Client PCs

Example structure:

```text
              Legitimate DHCP Server
                       │
                       │
                 Trusted Port
                       │
                  +---------+
                  | Switch  |
                  +---------+
                   │   │   │
                   │   │   │
                  PC  PC  PC
                   
                       │
                 Untrusted Port
                       │
                 Rogue DHCP Server
```

---

# ⚙️ Configuration Process

## Step 1 — Build the Network Topology

Create and label the topology in Cisco Packet Tracer.

The lab continues from the previous **DHCP Snooping** configuration.

Before configuring DAI or IP Source Guard, DHCP Snooping should already be configured.

---

# Step 2 — Configure DHCP Snooping

DHCP Snooping acts as a prerequisite for the other security technologies.

The previous lab configured the required trusted ports and DHCP Snooping on VLAN 1.

Example:

```cisco
enable
configure terminal

ip dhcp snooping
ip dhcp snooping vlan 1
```

Configure the legitimate DHCP-facing interfaces as trusted:

```cisco
interface range fastEthernet 0/1 - 2
ip dhcp snooping trust
exit
```

All remaining switch ports are untrusted by default.

---

# 🔍 Step 3 — Enable Dynamic ARP Inspection

Dynamic ARP Inspection is enabled on the VLAN currently being used.

In this lab, the default VLAN is **VLAN 1**.

```cisco
enable
configure terminal

ip arp inspection vlan 1
```

This enables DAI for VLAN 1.

---

# 🔒 Step 4 — Configure Trusted Ports for DAI

The interfaces that are trusted for DHCP Snooping are also configured as trusted for Dynamic ARP Inspection.

Example:

```cisco
interface range fastEthernet 0/1 - 2
ip arp inspection trust
exit
```

These ports are now trusted for DAI.

---

# 🔎 Step 5 — Verify Dynamic ARP Inspection

Use the following command to check the DAI interface status:

```cisco
show ip arp inspection interfaces
```

The trusted interfaces should appear as trusted.

Other switch ports remain untrusted.

---

# 🛡️ Dynamic ARP Inspection — How It Works

DAI checks incoming ARP packets against the DHCP Snooping binding database.

A valid binding contains information such as:

```text
MAC Address
IP Address
VLAN
Interface
```

If the ARP information does not match the binding database, the switch can drop the ARP packet.

### Example

```text
Legitimate Host
IP: 192.168.1.10
MAC: Legitimate-MAC
        │
        ▼
      Switch
        │
        ▼
DHCP Snooping Binding
        │
        ▼
     Match ✅
        │
        ▼
      Allow
```

A spoofed ARP packet:

```text
Attacker
IP: 192.168.1.1
Fake MAC Address
        │
        ▼
      Switch
        │
        ▼
DHCP Snooping Binding
        │
        ▼
     No Match ❌
        │
        ▼
      Drop
```

This helps protect against **ARP spoofing and ARP poisoning attacks**.

---

# 🔐 Step 6 — Configure IP Source Guard

IP Source Guard is used to prevent **IP address spoofing** on untrusted Layer 2 interfaces.

The configuration command is:

```cisco
interface fastEthernet 0/3
ip verify source
exit
```

The same configuration can be applied to the required untrusted access ports.

Example:

```cisco
interface range fastEthernet 0/3 - 24
ip verify source
exit
```

> **Important:** The training material notes that Cisco Packet Tracer does not support IP Source Guard configuration/functionality in the lab environment. Therefore, the command is shown as the correct configuration concept, but it cannot be fully demonstrated in Packet Tracer.

---

# 🧠 How IP Source Guard Works

IP Source Guard relies on the DHCP Snooping binding database.

After a legitimate client receives an IP address through DHCP, the switch has binding information for that host.

IP Source Guard then allows traffic that matches the expected IP information.

```text
DHCP Snooping
      │
      ▼
Binding Database
      │
      ▼
IP Source Guard
      │
 ┌────┴────┐
 ▼         ▼
Valid     Invalid
IP        IP
 │         │
 ▼         ▼
Allow     Block
```

This helps prevent hosts from using unauthorized or spoofed IP addresses.

---

# 🧪 Security Testing

The lab demonstrates the relationship between legitimate hosts and unauthorized devices.

### Legitimate Host

```text
Client
  │
  ▼
DHCP Request
  │
  ▼
Legitimate DHCP Server
  │
  ▼
Valid DHCP Binding
  │
  ▼
DAI / IP Source Guard
  │
  ▼
Traffic Allowed ✅
```

### Unauthorized/Spoofed Host

```text
Attacker
  │
  ▼
Spoofed IP / ARP Information
  │
  ▼
Switch
  │
  ▼
Security Validation
  │
  ▼
Invalid Binding ❌
  │
  ▼
Traffic Blocked
```

---

# 🔍 Verification Commands

### Verify DHCP Snooping

```cisco
show ip dhcp snooping
```

### Verify DHCP Snooping Bindings

```cisco
show ip dhcp snooping binding
```

### Verify Dynamic ARP Inspection

```cisco
show ip arp inspection
```

### Verify DAI Interfaces

```cisco
show ip arp inspection interfaces
```

### Verify Running Configuration

```cisco
show running-config
```

---

# 📋 Key Configuration Commands

```cisco
! DHCP Snooping
ip dhcp snooping
ip dhcp snooping vlan 1

! DHCP trusted ports
interface range fastEthernet 0/1 - 2
ip dhcp snooping trust

! Dynamic ARP Inspection
ip arp inspection vlan 1

! DAI trusted ports
interface range fastEthernet 0/1 - 2
ip arp inspection trust

! IP Source Guard
interface fastEthernet 0/3
ip verify source
```

---

# 📊 Security Features Comparison

| Technology             | Main Purpose                   | Uses DHCP Snooping Binding   |
| ---------------------- | ------------------------------ | ---------------------------- |
| DHCP Snooping          | Prevent rogue DHCP servers     | Creates the binding database |
| Dynamic ARP Inspection | Prevent ARP spoofing/poisoning | ✅ Yes                        |
| IP Source Guard        | Prevent IP address spoofing    | ✅ Yes                        |

---

# 🧩 Layer 2 Security Architecture

```text
                 Layer 2 Security
                       │
              ┌────────┴────────┐
              │                 │
       DHCP Snooping       Binding Database
              │                 │
              └───────┬─────────┘
                      │
             ┌────────┴────────┐
             │                 │
            DAI               IPSG
             │                 │
      ARP Protection      IP Protection
             │                 │
      ARP Spoofing        IP Spoofing
      ARP Poisoning       IP Spoofing
```

---

# 🎯 Skills Demonstrated

* Cisco Packet Tracer
* Cisco IOS CLI
* DHCP Snooping
* Dynamic ARP Inspection
* IP Source Guard
* Layer 2 Security
* ARP Spoofing Prevention
* ARP Poisoning Prevention
* IP Address Spoofing Prevention
* Trusted/Untrusted Port Configuration
* VLAN Security
* Network Troubleshooting
* Security Feature Verification

---

# ⚠️ Packet Tracer Limitation

During the lab, the training material identifies a limitation with **IP Source Guard support in Cisco Packet Tracer**.

Therefore:

* DHCP Snooping → Configured and demonstrated
* Dynamic ARP Inspection → Configured and verified
* IP Source Guard → Configuration command demonstrated, but full functionality cannot be tested in Packet Tracer

The relevant configuration command is:

```cisco
ip verify source
```

---

# 🚀 Project Outcome

This project demonstrates how **DHCP Snooping, Dynamic ARP Inspection, and IP Source Guard** can work together as Layer 2 security mechanisms.

The lab establishes the following security model:

```text
DHCP Snooping
      ↓
Creates trusted DHCP bindings
      ↓
 ┌────┴─────┐
 ↓          ↓
DAI        IPSG
 ↓          ↓
ARP        IP
Protection Protection
```

The overall objective is to restrict unauthorized DHCP, ARP, and IP-based activity within a switched network.

---

## 📁 Project Type

**Cisco Packet Tracer — Layer 2 Network Security Lab**

### 🔐 Focus

**DHCP Snooping | Dynamic ARP Inspection | IP Source Guard | ARP Spoofing Prevention | IP Spoofing Prevention | Layer 2 Security**
