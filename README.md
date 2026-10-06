# 🛡️ Secure Enterprise Network: RIPv2, ACL, and DNS Implementation

> A comprehensive network infrastructure simulation demonstrating dynamic routing, traffic filtering policies, and local domain resolution using Cisco Packet Tracer. 

This project is built to showcase practical skills in network engineering, specifically focusing on secure inter-VLAN/subnet communication and server access management.

---

## 🏗️ Network Architecture
<img width="1113" height="351" alt="{5B2AB00E-CECC-4D75-AC13-046CB7E23ADE}" src="https://github.com/user-attachments/assets/31e55786-7f00-472a-be61-2dc35b691745" />

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
`![DNS Setup](<img width="1176" height="71" alt="{5CB7CEF1-7B7A-4C9F-B073-2446BCD596E8}" src="https://github.com/user-attachments/assets/00b2bf59-17fe-4c9b-b9fc-eae2286b253a" />
)`)*

![Web Browser]
<img width="1366" height="651" alt="{7630F389-710B-4759-AA6E-F296C3E66FF7}" src="https://github.com/user-attachments/assets/08ac26c3-9d9e-4346-a515-8cab9d895eb3" />


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

    ! Apply ACL to Server Interface (Outbound to Server)
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
- [x] **Security Compliance:** Attempted to ping and access the web server from **PC1**.
    - **Result:** `Destination Host Unreachable` (Traffic successfully dropped by ACL).
