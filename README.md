# 🛡️ Secure Enterprise Network: RIPv2, ACL, and DNS Implementation

> A comprehensive network infrastructure simulation demonstrating dynamic routing, traffic filtering policies, and local domain resolution using Cisco Packet Tracer. 

This project is built to showcase practical skills in network engineering, specifically focusing on secure inter-VLAN/subnet communication and server access management.

---

## 🏗️ Network Architecture
<img width="1083" height="352" alt="{6EEC2178-E6E8-45BB-B1E5-E7FCBE71333A}" src="https://github.com/user-attachments/assets/e45f0591-54dc-41d4-bdb4-32f9931642e3" />


The infrastructure is segmented into three primary networks connected via a backbone link:
*   **Site A (Router 4):**
    *   **Client Network:** `192.168.33.0/24` (Gateway: `192.168.33.1`)
    *   **Data Center / Server Network:** `192.166.33.0/24` (Gateway: `192.166.33.1`)
*   **Site B (Router 5):**
    *   **Client Network:** `192.167.33.0/24` (Gateway: `192.167.33.1`)
*   **Core Backbone:** `199.166.33.0/30` connecting Site A and Site B.

---

## ⚙ Core Technologies & Services

1.  **Dynamic Routing (RIPv2):** Implemented across all routers to establish end-to-end connectivity without manual static route interventions. Auto-summary is disabled to support classless subnetting.
2.  **Access Control List (ACL):** Security policies enforced at the router interface level to restrict specific unauthorized hosts from accessing critical network resources.
3.  **DNS & Web Services:** A centralized server is configured to host the intranet web portal and resolve the local domain name, allowing clients to access services via a standard URL rather than raw IP addresses.

---

## 🎯 Security Policy Implementation (ACL)

To simulate a real-world enterprise security policy, a specific traffic filtering rule is applied to isolate an unauthorized endpoint:
*   🔴 **Restricted Host (PC1):** `192.168.33.2` (Site A)

**Policy Objective:** This specific host is explicitly **denied** access to the Server farm (`192.166.33.0/24`), while all other authenticated client PCs on the network (including Site B clients) retain full communication privileges.

---

## 📝 Configuration Highlights & DNS Setup

### 1. DNS Server Configuration
![DNS Setup]
<img width="1176" height="71" alt="{5CB7CEF1-7B7A-4C9F-B073-2446BCD596E8}" src="https://github.com/user-attachments/assets/00b2bf59-17fe-4c9b-b9fc-eae2286b253a" />

![Web Browser]
<img width="1307" height="676" alt="{C625A347-43DA-4FF7-9219-9CBBC440538C}" src="https://github.com/user-attachments/assets/a6914774-903f-4190-889f-faa63e2aa4f4" />



*   **Server IP Address:** `192.166.33.2`
*   **DNS Service:** ON
*   **A-Record:** `qyv.com` ➔ `192.166.33.2`
*   *Note: All endpoint devices are configured to use `192.166.33.2` as their primary DNS server.*

### 2. Router 4 (Site A) Configuration

    ! Routing RIPv2
    Router(config)# router rip
    Router(config-router)# version 2
    Router(config-router)# network 192.168.33.0
    Router(config-router)# network 192.166.33.0
    Router(config-router)# network 199.166.33.0
    Router(config-router)# no auto-summary

    ! ACL to Block PC1 from accessing Server Network
    Router(config)# access-list 1 deny host 192.168.33.2
    Router(config)# access-list 1 permit any

    ! Apply ACL 
    Router(config)# interface gigabitEthernet 0/0
    Router(config-if)# ip access-group 1 in

### 3. Router 5 (Site B) Configuration

    ! Routing RIPv2
    Router(config)# router rip
    Router(config-router)# version 2
    Router(config-router)# network 192.167.33.0
    Router(config-router)# network 199.166.33.0
    Router(config-router)# no auto-summary

---

## 🧪 Validation & Testing Procedures

To ensure network reliability and security compliance, the following verification tests were conducted:

- [x] **DNS Resolution:** Navigated to the domain name from an authorized PC's web browser; the intranet page loaded successfully.
- [x] **Routing Verification:** Successfully executed ICMP ping requests between Site A clients (`192.168.33.3`) and Site B clients (`192.167.33.2`).
- [x] **Security Compliance:** Attempted to ping and access the web server from **PC0**.
    - **Result:** `Destination Host Unreachable` (Traffic successfully dropped by ACL).
      <img width="380" height="175" alt="{15D554D1-D222-46F5-A63D-3BDF66D67C57}" src="https://github.com/user-attachments/assets/e77edb42-0871-4517-ac94-782968ab5d88" />

