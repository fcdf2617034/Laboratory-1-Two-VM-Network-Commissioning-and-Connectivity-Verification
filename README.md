# WADF105 Laboratory 1: Two VM Network Commissioning and Connectivity Verification
**Course:** WADF105 – Web Application & Network Security Fundamentals  
**Student Name:** Kenneth Chigozie Ukata[cite: 31]  
**Registration Number:** FCDF2617034[cite: 31]  
**Host Environment:** Kali Linux (`KaliLinuxKen`) / VirtualBox Network (`ICDFA-LAN`)[cite: 31]  

---

## 📌 Lab Overview
This repository contains evidence, command logs, troubleshooting documentation, and packet analysis for **WADF105 Lab 1**[cite: 31]. The primary objective of this practical exercise was to commission a two-VM network environment consisting of an **OPNsense firewall** and an **Ubuntu client**, configure internal and external network adapters, resolve interface mapping errors, verify routing tables, and capture baseline traffic using Wireshark[cite: 31, 35, 37].

## 🛠️ Infrastructure & Tools Used
* **Firewall Gateway:** OPNsense Virtual Firewall (`ICDFA-NSLAB-Firewall` with Adapter 1: NAT, Adapter 2: Internal Network `ICDFA-LAN`)[cite: 32]
* **Client Host:** Ubuntu Client (`icdfa-nslab-client-v1` at `10.10.10.241/24`)[cite: 33]
* **Analysis Tools:** `ip`, `ping`, `getent`, `curl`, NetworkManager, Wireshark[cite: 33, 37, 39]

---

## 📂 Key Practical Modules Covered

### Part A & B: VirtualBox Network Commissioning & Firewall Bootup
* Configured VirtualBox adapter settings, mapping Adapter 1 to NAT (WAN) and Adapter 2 to an internal network segment named `ICDFA-LAN` for both the firewall and client VMs[cite: 32].
* Booted `ICDFA-NSLAB-Firewall` and recorded initial IP assignments and default gateway bindings[cite: 32].

### Part C: Ubuntu Client Network Commissioning & Troubleshooting
* Started the Ubuntu client VM (`icdfa-nslab-client-v1`) and verified its DHCP-assigned IPv4 address (`10.10.10.241/24`) and default route (`10.10.10.1`)[cite: 33].
* **Issue Resolved:** Encountered initial 100% packet loss and destination unreachable errors due to inverted LAN/WAN interface bindings[cite: 34, 35]. 
* **Resolution Steps:** Restarted NetworkManager (`sudo systemctl restart NetworkManager`), corrected the physical adapter bindings in the OPNsense firewall console (`Assignments`), and re-applied the firewall script to restore proper routing[cite: 35, 37].

### Part D: Connectivity & Resolution Testing
* Executed baseline connectivity tests in sequence:
  1. `ping -c 4 10.10.10.1` (Successful local gateway communication)[cite: 34, 39]
  2. `ping -c 4 1.1.1.1` (Successful internet routing via outbound NAT)[cite: 37, 39]
  3. `getent hosts opnsense.org` (Successful DNS resolution)[cite: 38, 39]
  4. `curl -I https://opnsense.org` (Successful HTTP/HTTPS response headers retrieval)[cite: 38, 39]
* Visited `https://10.10.10.1` via browser to inspect the OPNsense dashboard and confirm WAN/LAN interface status[cite: 38, 39].

### Part E: Wireshark Packet Analysis
* Captured active interface traffic on the `10.10.10.x` network segment using Wireshark[cite: 40, 41].
* Filtered and identified core network primitives:
  * **ARP (`arp`):** Observed Address Resolution Protocol request packets discovering the firewall MAC address (`Who has 10.10.10.1?`)[cite: 40, 42].
  * **ICMP (`icmp`):** Captured echo request/reply packets exchanged between Ubuntu and the gateway[cite: 40, 42].
  * **DNS (`dns`):** Inspected standard query responses resolving `opnsense.org`[cite: 41, 43].

---

## ❓ Lab Review & Assessment Questions

1. **What is the difference between the OPNsense WAN and LAN interfaces?**  
   *WAN connects to the external internet via VirtualBox NAT with a dynamic address, while LAN connects to the private local network (`10.10.10.1/24`), acting as the gateway and DHCP server for internal clients[cite: 43].*

2. **Why must both internal adapters use the same VirtualBox network name?**  
   *The internal network name serves as a virtual switch. Both adapters must connect to the exact same named segment to pass Layer 2 traffic between the firewall and client[cite: 43].*

3. **What information does the default route provide to Ubuntu?**  
   *It specifies the default gateway (`10.10.10.1`), directing traffic destined for external subnets toward the firewall for proper routing[cite: 43].*

4. **Which packet exchange allows Ubuntu to learn the firewall MAC address?**  
   *The Address Resolution Protocol (ARP) request and reply exchange[cite: 43].*

5. **Why does a successful ping to 1.1.1.1 not automatically prove that DNS is working?**  
   *Pinging a raw IP address tests basic routing and NAT without domain resolution. DNS is evaluated separately when translating domain names into IP addresses[cite: 43].*
