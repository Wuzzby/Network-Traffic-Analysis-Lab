# Network-Traffic-Analysis-Lab
This repository contains a lab for analyzing network traffic and simulating attacks like SYN floods, ARP spoofing, and ICMP reconnaissance

This project demonstrates how to capture and analyze both **normal** and **malicious network traffic** using tools like **Wireshark**, **hping3**, and **arpspoof**. The lab simulates **real-world attack scenarios**, allowing for the identification of critical **network threats** such as **ICMP reconnaissance**, **SYN floods**, and **ARP spoofing**.

---

## **Objective**

The goal of this lab is to simulate attack patterns and network behavior to:
- Develop skills in **network traffic analysis**.
- Detect key attack indicators such as:
  - **Ping sweeps** (ICMP reconnaissance).
  - **Denial-of-Service (DoS)** attacks (SYN floods).
  - **Man-in-the-Middle (MitM)** attacks (via ARP spoofing).
- Improve threat detection and response capabilities using **Wireshark** and related tools.

---

### **Lab Setup**

### **Environment Overview**
This lab is conducted on two virtual machines connected via **Host-Only Networking** in VirtualBox.

| **Machine**       | **Role**         | **IP Address**      |
|-------------------|------------------|---------------------|
| Kali Linux        | Attacker/Monitor | `192.168.56.10`     |
| Ubuntu Linux      | Victim           | `192.168.56.103`    |

### **Network Configuration**
- **Host-Only Adapter:** The virtual machines are isolated on a local subnet (`192.168.56.0/24`).
- **Packet Forwarding:** Enabled on **Kali** to route traffic between interfaces:
  ```bash
  echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward

---

### **Tools and Technologies**
The following tools were used in the lab:

Tool	| Purpose
Wireshark	| Packet capture and network traffic analysis
hping3 |	SYN flood attack simulation
arpspoof |	ARP cache poisoning attack
tcpdump	| Command-line packet capture verification
Python	| HTTP server for generating normal traffic

---

### **Traffic Simulations**
Below are the simulated traffic types and how they were generated:

---

## **1. ICMP Traffic (Reconnaissance)**
Attackers often use ICMP (ping sweeps) to perform network reconnaissance, identifying active hosts on a network.

Steps to Generate ICMP Traffic
  1. Run the following command on Kali:
  ```bash
  ping 192.168.56.103
  ```

 2. In Wireshark, apply this filter to capture ICMP traffic:
  ```plaintext
  icmp
  ```

**Analysis**
- Continuous ICMP echo requests and replies indicate network reconnaissance.
- Detecting such activity helps network defenders identify early signs of an attack.

---

## **2. SYN Flood (Denial-of-Service Attack)**
This simulation replicates a TCP SYN flood, where a target is bombarded with TCP SYN packets, overwhelming its ability to respond to legitimate connection requests.

**Steps to Generate SYN Flood Traffic**
1. Install hping3 on Kali if needed
  ```bash
  sudo apt install hping3 -y
  ```

2. Execute the following command to launch the attack:
  ```bash
  sudo hping3 -S --flood -V -p 80 192.168.56.103
  ```

3. Apply this filter in Wireshark:
  ```plaintext
  tcp.flags.syn == 1 && tcp.flags.ack == 0
  ```

**Analysis**
- The attack is visible through a high volume of SYN packets without corresponding ACK responses.
- This signature is a common indicator of Denial-of-Service attacks.
  
---

## **3. ARP Spoofing (Man-in-the-Middle Attack)**
ARP spoofing enables an attacker to perform a Man-in-the-Middle (MitM) attack by sending fake ARP replies, poisoning the victim's ARP cache.

**Steps to Generate ARP Spoofing Traffic**
1. Install ```dsniff``` on Kali if needed:
  ```bash
  sudo apt install dsniff -y
  ```

2. Execute the arpspoof command:
  ```bash
  sudo arpspoof -i eth0 -t 192.168.56.103 192.168.56.1
  ```

3. Apply this filter in Wireshark to capture ARP traffic:
  ```plaintext
  arp
  ```

**Analysis**
- Duplicate ARP replies with conflicting MAC addresses indicate ARP poisoning.
- This attack can intercept or manipulate sensitive traffic on the network.

---

### **Analysis and Findings**
## **1. ICMP Traffic Analysis**
- Observation: Continuous ping requests and responses indicate reconnaissance.
- Detection Strategy: Monitor for unusual ICMP activity, such as ping sweeps.

---

## **2. SYN Flood Analysis**
- Observation: Multiple SYN packets without corresponding ACKs suggest a Denial-of-Service attempt.
- Detection Strategy: Implement rate limiting, SYN cookies, or firewall rules to mitigate SYN floods.

---
  
## **3. ARP Spoofing Analysis**
- Observation: Duplicate ARP replies reveal attempts to alter ARP tables.
- Detection Strategy: Use static ARP entries, DHCP snooping, or port security to prevent MitM attacks.

---

### **Lessons Learned**
- **Interface Selection Matters:** Misconfigured interfaces and adapters can prevent the capture of key traffic.
- **Packet Forwarding:** Enabling packet forwarding on multi-VM setups is essential for proper traffic flow.
- **Threat Awareness:** Familiarity with traffic patterns helps identify and respond to threats such as **DoS** and **MitM** attacks.

---

### **How to Run This Lab**
1. Set up Kali Linux and Ubuntu Linux VMs in VirtualBox.
2. Configure Host-Only Networking and assign appropriate IPs.
3. Enable packet forwarding on Kali:
  ```bash
    echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward
  ```
4. Generate traffic using the commands listed above.
5. Analyze the captured traffic in Wireshark.

---

### **License**
This project is licensed under the **MIT License.** See the LICENSE file for details.
