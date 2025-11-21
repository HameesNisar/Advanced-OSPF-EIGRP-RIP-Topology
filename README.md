# 28-Router Multi-Protocol Redistribution Lab (GNS3)

This project showcases a large-scale enterprise-style routing scenario built on **GNS3**, involving **28 Cisco routers** configured with a mix of **OSPF**, **EIGRP**, and **RIP**.  
The objective of this lab was to simulate a complex multi-protocol environment with full redistribution, authentication, DR/BDR control, and route filtering using ACLs and route-maps.

---

## 📌 Topology Diagram

<img width="1861" height="895" alt="OSPF_Redistribution" src="https://github.com/user-attachments/assets/125b45d2-0ff0-4325-bdfa-4b8bbeda36f7" />


---

## 📘 Overview

This lab simulates a multi-area, multi-ASN environment where routing information must be exchanged reliably between:

- **OSPF Area 0, Area 134, Area 145, Area 155, Area 165**
- **EIGRP ASNs:** 140, 160, 167, 173, 174, 190, 199  
- **RIP domain:** Routers 23, 25, 26

The goal was to build a real-world-inspired network where multiple dynamic routing protocols coexist and require redistribution, authentication, and filtering to maintain routing stability.

> **Note:**  
> You may notice networks such as `1.0.0.0/8`, `2.0.0.0/8`, etc.  
> These are *public* address ranges.  
> They were intentionally used only inside the **lab environment** where using public ranges is allowed.  
> These do **not** represent real-world addressing and were purely for educational routing segmentation.

---

## 🎯 Lab Objectives

### **1. Configure all 28 routers**
- Basic interface IP configuration  
- Assign loopback networks to each router  
- Verify reachability inside each domain

### **2. Run routing protocols**
- **RIP** on routers: **R23, R25, R26**
- **EIGRP** on all other designated ASNs
- **OSPF** mainly in the central backbone  
- Multi-area OSPF with Area 0 interconnecting multiple regions

### **3. Perform full Redistribution**
Redistribution required between:
- **OSPF ↔ EIGRP**  
- **OSPF ↔ RIP**  
- **EIGRP ↔ EIGRP** (between different ASNs)

METRIC tuning and route-maps were used to control redistribution.

### **4. Enable Authentication**
- **EIGRP ASN 190:** MD5 between R5, R7, R8  
- **EIGRP ASN 199:** MD5 between R19, R21, R22  
- **RIP MD5:** Entire R23-R25-R26 region  
- Ensures integrity and prevents rogue route injection

### **5. Force DR/BDR Elections**
- OSPF priority manipulation performed manually  
- Higher priority = preferred DR  
- Routers configured intentionally to assign DR/BDR roles on specific multi-access links

### **6. Apply Route Filtering**
Each router had loopbacks that must NOT be advertised into certain routing domains.

Example:
- A route marked **“OSPF block”** must not enter OSPF  
- A route marked **“RIP block”** must not enter RIP  
- A route tagged for EIGRP block must not enter specific ASNs

Filtering was implemented using:
- Standard ACLs  
- Route-maps  
- Controlled redistribution

---

## 🗂 Routing Domains Overview

| Protocol | Routers |
|---------|---------|
| **RIP** | R23, R25, R26 |
| **EIGRP AS 140** | R6, SW3 |
| **EIGRP AS 160** | R10, R11, R12 |
| **EIGRP AS 167** | R24 |
| **EIGRP AS 173** | R18, R19 |
| **EIGRP AS 174** | R13, R14, SW6 |
| **EIGRP AS 190** | R5, R7, R8 |
| **EIGRP AS 199** | R19, R21, R22 |
| **OSPF Areas 0 / 134 / 145 / 155 / 165** | Core routers |

---

## 🔄 Redistribution Strategy

Each redistribution point used:

- **Inbound filtering** → route-maps matching ACLs  
- **Metric tuning** → preventing bad path selection  
- **Avoiding mutual unintelligent redistribution** → no routing loops  
- **Tagging and filtering when needed**

Redistribution examples:
- OSPF → EIGRP (with metrics: `100000 1000 1 1 1`)
- EIGRP (one ASN) → EIGRP (other ASN)
- OSPF → RIP and RIP → OSPF with route-maps

---

## 🛡 Authentication

### **EIGRP MD5**
```
Example (ASN 190):
key chain EIGRP_KEYS
key 1
key-string cisco123

interface s0/0
ip authentication mode eigrp 190 md5
ip authentication key-chain eigrp 190 EIGRP_KEYS
```

## 🔍 Verification Commands

### To verify reachability:
```
ping <loopback-ip>
```

### To verify routing:
```
show ip route
show ip route ospf
show ip route eigrp
show ip route rip
```

### To check protocol status:
```
show ip protocols
```

### To confirm authentication:
```
show ip eigrp neighbors detail
show ip rip database
```

### To confirm DR/BDR:
```
show ip ospf interface
```

---

## 🏁 Conclusion

This lab demonstrates full enterprise-grade routing features:

- Multi-protocol redistribution  
- Inter-ASN EIGRP routing  
- OSPF multi-area design  
- Authentication (MD5)  
- DR/BDR control  
- Advanced route filtering  

The project reflects strong understanding of Cisco routing behavior, protocol interoperability, and real-world network engineering practices.

---


