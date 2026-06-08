# Wireshark and Network Traffic Analysis

## 1. Introduction to Wireshark

### What is Wireshark?

Wireshark is an open-source **network protocol analyzer** used to capture, inspect, and analyze network traffic in real time. It helps network administrators, security analysts, and forensic investigators understand how devices communicate over a network.

### Uses of Wireshark

* Network troubleshooting.
* Security analysis and incident response.
* Protocol analysis (HTTP, DNS, TCP, UDP, ICMP, etc.).
* Digital forensics investigations.
* Network performance monitoring.
* Learning networking concepts.

---

## 2. Port Spanning (Port Mirroring)

### What is Port Spanning?

Port Spanning, also known as **Port Mirroring**, is a switch feature that copies network traffic from one or more ports and sends it to a designated monitoring port.

### Why is it Used?

* Monitor traffic from other devices.
* Capture packets using Wireshark.
* Troubleshoot network issues.
* Perform security monitoring and forensic analysis.

### Example

A switch mirrors traffic from a server port to a monitoring port where Wireshark is connected, allowing traffic analysis without affecting normal network operations.

---

## 3. Ettercap

### What is Ettercap?

Ettercap is an open-source network security tool used for network traffic analysis, host discovery, and authorized security assessments.

### Uses of Ettercap

* Network analysis.
* Host discovery.
* Protocol testing.
* Security assessments.
* Demonstrating Man-in-the-Middle (MITM) attacks in controlled environments.

### Features

* Packet sniffing.
* Traffic filtering.
* ARP spoofing support.
* Plugin support.

---

## 4. ARP Spoofing

### What is ARP Spoofing?

ARP (Address Resolution Protocol) Spoofing is a technique where forged ARP messages are sent on a local network to associate an attacker's MAC address with another device's IP address.

### How it Works

1. Fake ARP replies are sent.
2. Devices update their ARP tables with incorrect information.
3. Traffic is redirected through another machine.
4. The traffic can then be observed or relayed.

### Security Impact

* Traffic interception.
* Session hijacking.
* Data theft on insecure protocols.
* Man-in-the-Middle (MITM) attacks.

---

## 5. Packet Capturing

### What is Packet Capturing?

Packet capturing is the process of collecting and recording network packets as they travel across a network. Each packet contains information about communication between devices.

### Why is it Used?

* Monitor network activity.
* Troubleshoot connectivity issues.
* Analyze suspicious traffic.
* Investigate security incidents.

---

## 6. Capturing Packets from Other Sources

By default, Wireshark captures traffic only from the device on which it is running. To capture traffic from other devices, additional methods such as Port Spanning or authorized network testing techniques may be used.

### Why is it Used?

* Monitor server traffic.
* Analyze communication between hosts.
* Investigate network-wide incidents.
* Perform centralized traffic analysis.

---

## 7. Capture Filters

### What is a Capture Filter?

A Capture Filter is applied before packet capture begins. It determines which packets Wireshark captures and stores.

### Why is it Used?

* Reduce unnecessary traffic.
* Improve performance.
* Save storage space.
* Focus on relevant traffic.

### Examples

```bash
host 192.168.1.10
```

```bash
port 80
```

```bash
tcp
```

---

## 8. Display Filters

### What is a Display Filter?

A Display Filter is applied after packets have been captured. It allows analysts to view only specific packets from the captured traffic.

### Why is it Used?

* Simplify packet analysis.
* Focus on specific protocols or hosts.
* Quickly locate relevant traffic.

### Examples

```bash
http
```

```bash
ip.addr == 192.168.1.10
```

---

## 9. Network Analysis Concepts

### Latency

Latency is the time delay between sending a request and receiving a response across a network. It is usually measured in milliseconds (ms).

### Time Delta

Time Delta is the time difference between two captured packets. It helps identify delays and performance issues during network communication.

### Investigation

Investigation is the process of analyzing captured traffic, logs, and network events to determine the cause of network problems, attacks, or suspicious activities.

### Logical Error

A Logical Error is a flaw in a program's logic that causes incorrect results while the program continues to run normally without crashing.


Wireshark is a powerful network analysis tool used for packet capturing, troubleshooting, security monitoring, and digital forensics. Techniques such as Port Spanning and ARP Spoofing can be used to observe network traffic in controlled environments, while Capture Filters and Display Filters help analysts focus on relevant packets. Together, these concepts provide a strong foundation for network analysis and cybersecurity investigations.
