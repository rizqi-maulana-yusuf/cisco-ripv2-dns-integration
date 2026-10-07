# 🛡️ Secure Enterprise Network: RIPv2, ACL, and DNS Implementation

> An enterprise-grade network infrastructure simulation demonstrating dynamic routing, traffic filtering policies, and local domain resolution using Cisco Packet Tracer. 

This project showcases practical network engineering skills, specifically focusing on secure inter-site communication, endpoint isolation, and centralized server management.

---

## 🏗️ Network Architecture & Topology

<img width="1083" height="352" alt="Network Topology" src="[https://github.com/user-attachments/assets/e45f0591-54dc-41d4-bdb4-32f9931642e3](https://github.com/user-attachments/assets/e45f0591-54dc-41d4-bdb4-32f9931642e3)" />

The infrastructure is segmented into three primary areas connected via a high-speed backbone link. 

### IP Addressing Table
| Location | Network / Subnet | Default Gateway | Purpose / Role |
| :--- | :--- | :--- | :--- |
| **Site A** | `192.168.33.0/24` | `192.168.33.1` | Employee Client Network |
| **Site A** | `192.166.33.0/24` | `192.166.33.1` | Data Center / Server Farm |
| **Site B** | `192.167.33.0/24` | `192.167.33.1` | Remote Branch Client Network |
| **Core** | `199.166.33.0/30` | N/A | Point-to-Point Backbone Link |

---

## 🚀 How to Run This Lab

To test the configurations and explore the topology:
1. Ensure you have **Cisco Packet Tracer** installed (Version 8.0 or newer recommended).
2. Download the `.pkt` file included in this repository.
3. Open the file and wait for the STP (Spanning Tree Protocol) ports to converge (turn green).
4. Follow the **Validation & Testing Procedures** below to verify connectivity and security policies.

---

## ⚙ Core Technologies & Services

1. **Dynamic Routing (RIPv2):** Implemented across all distribution routers to establish end-to-end connectivity dynamically. Auto-summary is disabled to support Variable Length Subnet Masking (VLSM).
2. **Access Control List (ACL):** Standard security policies enforced at the router interface level to restrict unauthorized hosts from accessing critical server resources.
3. **DNS & Web Services:** A centralized server (`192.166.33.2`) is configured to host the intranet web portal and resolve the local domain name (`qyv.com`), allowing clients to access services via standard URLs.

---

## 📝 Configuration Highlights

### 1. Site A (Router 4) - Routing & Security
This router handles local traffic, connects to the backbone, and filters unauthorized traffic (PC0: `192.168.33.2`) from entering the core network.

```text
! Enable RIPv2 Routing
Router(config)# router rip
Router(config-router)# version 2
Router(config-router)# network 192.168.33.0
Router(config-router)# network 192.166.33.0
Router(config-router)# network 199.166.33.0
Router(config-router)# no auto-summary

! Configure Standard ACL to Isolate PC0
Router(config)# access-list 1 deny host 192.168.33.2
Router(config)# access-list 1 permit any

! Apply ACL Inbound on Client Gateway Interface
Router(config)# interface gigabitEthernet 0/1
Router(config-if)# ip access-group 1 in
```

### 2. Site B (Router 5) - Routing
```text
! Enable RIPv2 Routing
Router(config)# router rip
Router(config-router)# version 2
Router(config-router)# network 192.167.33.0
Router(config-router)# network 199.166.33.0
Router(config-router)# no auto-summary
```

### 3. Command Line Analysis
*   **`no auto-summary`**: Prevents the router from summarizing routes to classful boundaries, ensuring accurate routing tables when using custom subnet masks.
*   **`access-list 1 permit any`**: Explicitly allows all other traffic. Without this, Cisco's *implicit deny* rule would drop all packets traversing the interface.
*   **`ip access-group 1 in`**: Applies the filtering rule immediately as the packet enters the interface, saving router CPU cycles compared to outbound filtering.

---

## 🧪 Validation & Testing Procedures

The following verifications were conducted to ensure network reliability and security compliance:

### ✅ Scenario 1: Authorized Access & DNS Resolution
An authorized client successfully queried the DNS server and accessed the intranet web portal (`qyv.com`).

<img width="1176" height="71" alt="DNS Setup" src="[https://github.com/user-attachments/assets/00b2bf59-17fe-4c9b-b9fc-eae2286b253a](https://github.com/user-attachments/assets/00b2bf59-17fe-4c9b-b9fc-eae2286b253a)" />

<img width="1307" height="676" alt="Web Browser Verification" src="[https://github.com/user-attachments/assets/a6914774-903f-4190-889f-faa63e2aa4f4](https://github.com/user-attachments/assets/a6914774-903f-4190-889f-faa63e2aa4f4)" />

### ❌ Scenario 2: Security Compliance (ACL Block)
The restricted endpoint (**PC0**) attempted to access the web server. 
*   **Result:** The router successfully dropped the traffic. The browser returned a **"Host Name Unresolved"** error, confirming the inbound ACL is functioning as intended.

<img width="380" height="175" alt="ACL Block Verification" src="[https://github.com/user-attachments/assets/e77edb42-0871-4517-ac94-782968ab5d88](https://github.com/user-attachments/assets/e77edb42-0871-4517-ac94-782968ab5d88)" />

---

## 💡 Lessons Learned & Troubleshooting

During the implementation of this project, I encountered and resolved several architectural challenges:
* **ACL Placement Strategy:** Initially, placing the ACL close to the destination (Server) caused unnecessary backbone traffic. By applying the Standard ACL directly at the source interface (`GigabitEthernet 0/1` on Router 4), bandwidth efficiency across the Core link was significantly improved.
* **DNS Resolution Issues:** DNS queries were failing until all client PCs were properly updated to point to `192.166.33.2` as their primary DNS server, reinforcing the importance of proper DHCP/Static IP configuration management.
