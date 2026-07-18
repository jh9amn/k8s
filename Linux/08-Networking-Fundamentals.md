# 🌐 Chapter 8 - Networking Fundamentals

> **"Everything in modern computing communicates over a network."**

Whether you open a website, use Docker, connect to a database, call an API, or deploy Kubernetes, networking makes it possible.

This chapter builds your networking knowledge from the ground up.

---

# 📖 Table of Contents

1. What is Networking?
2. Why Networking Matters
3. Types of Networks
4. Network Components
5. OSI Model
6. TCP/IP Model
7. Data Encapsulation
8. IP Addresses
9. IPv4
10. IPv6
11. Public vs Private IP
12. Loopback Address
13. Broadcast Address
14. Learning Summary

---

# 🎯 Learning Objectives

After this chapter you'll understand:

- How computers communicate
- The OSI model
- TCP/IP
- IPv4 & IPv6
- Public and private IPs
- Broadcast communication
- Loopback networking
- How networking relates to Docker and Kubernetes

---

# 1. What is Networking?

A **computer network** is a collection of devices connected together to exchange data.

Devices include:

- Computers
- Mobile phones
- Servers
- Routers
- Switches
- Printers
- IoT devices

Example:

```
Laptop
    │
    ▼
Wi-Fi Router
    │
    ▼
Internet
    │
    ▼
Google Server
```

When you open:

```
https://google.com
```

Your computer sends packets across many networks until they reach Google's servers.

---

# Real-Life Analogy

Think of networking like a postal system.

- Device → Person
- IP Address → Home address
- Data → Letter
- Router → Post office
- Internet → Road network

Without an address, a letter cannot be delivered.

Without an IP address, data cannot be delivered.

---

# 2. Why Networking Matters

Networking powers:

- Web browsing
- Email
- Cloud computing
- Docker containers
- Kubernetes clusters
- Databases
- APIs
- SSH
- File sharing
- Streaming

Without networking:

```
No Internet

No Docker

No Kubernetes

No Cloud
```

---

# 3. Types of Networks

## PAN (Personal Area Network)

Very small range.

Examples:

- Bluetooth headset
- Smart watch
- Wireless keyboard

```
Phone
   │
Bluetooth
   │
Headphones
```

---

## LAN (Local Area Network)

Small geographical area.

Examples:

- Home Wi-Fi
- Office
- College Lab

```
Laptop
    │
Switch
    │
Desktop
```

---

## MAN (Metropolitan Area Network)

Connects multiple LANs across a city.

Example:

```
College Campus

↓

Multiple Buildings

↓

Connected Network
```

---

## WAN (Wide Area Network)

Connects cities and countries.

Largest WAN:

```
Internet
```

---

# 4. Network Components

## Client

Requests a service.

Example:

```
Chrome Browser
```

---

## Server

Provides a service.

Examples:

- Nginx
- Apache
- MySQL
- Kubernetes API Server

---

## Switch

Connects devices inside the same LAN.

```
Laptop
   │
Switch
   │
Desktop
```

Switches operate mainly at **OSI Layer 2 (Data Link)** using MAC addresses.

---

## Router

Connects different networks.

```
Home Network
      │
Router
      │
Internet
```

Routers operate mainly at **OSI Layer 3 (Network)** using IP addresses.

---

## Modem

Converts signals between your ISP and your local network.

```
ISP

↓

Modem

↓

Router
```

Many home devices combine the modem and router into one unit.

---

# 5. OSI Model

The **OSI (Open Systems Interconnection)** model is a conceptual framework describing how data travels from one device to another.

It contains **7 layers**.

```
+----------------------+
| 7 Application        |
+----------------------+
| 6 Presentation       |
+----------------------+
| 5 Session            |
+----------------------+
| 4 Transport          |
+----------------------+
| 3 Network            |
+----------------------+
| 2 Data Link          |
+----------------------+
| 1 Physical           |
+----------------------+
```

---

## Layer 7 – Application

User-facing network services.

Examples:

- HTTP
- HTTPS
- FTP
- DNS
- SMTP
- SSH

---

## Layer 6 – Presentation

Responsible for:

- Encryption
- Compression
- Data formatting

Examples:

- TLS/SSL
- JPEG
- PNG
- UTF-8

---

## Layer 5 – Session

Manages communication sessions.

Responsibilities:

- Open session
- Maintain session
- Close session

---

## Layer 4 – Transport

Responsible for:

- Reliability
- Segmentation
- Flow control
- Error recovery

Protocols:

- TCP
- UDP

---

## Layer 3 – Network

Responsible for routing packets between networks.

Protocols:

- IP
- ICMP

Devices:

- Routers

---

## Layer 2 – Data Link

Responsible for communication inside the same LAN.

Uses:

- MAC Addresses
- Ethernet
- Wi-Fi Frames

Devices:

- Switches

---

## Layer 1 – Physical

Physical transmission of bits.

Examples:

- Ethernet cable
- Fiber optic
- Wi-Fi radio
- Electrical signals

---

# Easy Memory Trick

```
Application

Presentation

Session

Transport

Network

Data Link

Physical
```

Mnemonic:

**"All People Seem To Need Data Processing."**

---

# 6. TCP/IP Model

The TCP/IP model is the practical networking model used by the Internet.

```
Application

Transport

Internet

Network Access
```

Relationship:

| TCP/IP | OSI |
|---------|-----|
| Application | 5,6,7 |
| Transport | 4 |
| Internet | 3 |
| Network Access | 1,2 |

---

# Why TCP/IP Won

The Internet is built on TCP/IP because it is:

- Simpler
- Practical
- Scalable
- Vendor-independent

---

# 7. Data Encapsulation

When data travels through the network, each layer adds its own header.

```
Application Data

↓

TCP Header

↓

IP Header

↓

Ethernet Header

↓

Bits
```

On the receiving side, these headers are removed in reverse order.

This process is called **decapsulation**.

---

# 8. IP Address

An **IP (Internet Protocol) address** uniquely identifies a device on a network.

Example:

```
192.168.1.10
```

Think of it as a postal address for your computer.

Without an IP address, devices cannot send or receive packets.

---

# 9. IPv4

IPv4 is a 32-bit addressing scheme.

Example:

```
192.168.1.100
```

It is divided into four octets:

```
192 . 168 . 1 . 100
```

Each octet ranges from:

```
0–255
```

Maximum IPv4 addresses:

```
2^32 ≈ 4.3 Billion
```

Due to Internet growth, IPv4 addresses became scarce.

---

# 10. IPv6

IPv6 is a 128-bit addressing scheme designed to replace IPv4.

Example:

```
2001:db8:85a3::8a2e:370:7334
```

Advantages:

- Vast address space
- Better routing
- Built-in support for modern networking features
- Reduced need for NAT

---

# IPv4 vs IPv6

| Feature | IPv4 | IPv6 |
|---------|------|------|
| Size | 32-bit | 128-bit |
| Address Space | ~4.3 Billion | 3.4 × 10³⁸ |
| NAT Required | Often | Usually No |
| Format | Decimal | Hexadecimal |

---

# 11. Public vs Private IP

## Public IP

Assigned by an Internet Service Provider (ISP).

Reachable over the Internet.

Example:

```
203.0.113.25
```

---

## Private IP

Used within local networks.

Not routable on the public Internet.

Private IPv4 ranges:

```
10.0.0.0/8

172.16.0.0 – 172.31.255.255

192.168.0.0/16
```

Your home Wi-Fi devices typically use one of these ranges.

---

# 12. Loopback Address

The loopback interface lets a computer communicate with itself.

IPv4:

```
127.0.0.1
```

Hostname:

```
localhost
```

Example:

```bash
ping 127.0.0.1
```

If this fails, the local TCP/IP stack has a problem.

---

# 13. Broadcast Address

A broadcast sends a packet to **every device** on the local network.

Example:

```
192.168.1.255
```

Routers generally do not forward broadcast traffic to other networks.

---

# 14. Learning Summary

In this part, you learned:

- What networking is
- Why networking matters
- Types of networks (PAN, LAN, MAN, WAN)
- Basic networking devices (Switch, Router, Modem)
- OSI Model (7 layers)
- TCP/IP Model (4 layers)
- Data encapsulation
- IP addresses
- IPv4 and IPv6
- Public and private IPs
- Loopback
- Broadcast addresses

---

---

# 15. Subnet Mask

A **Subnet Mask** determines which part of an IP address represents the **network** and which part represents the **host**.

Imagine an IP address like a postal address:

```
192.168.1.25
```

- **192.168.1** → Street (Network)
- **25** → House Number (Host)

The subnet mask tells the computer where the network portion ends.

Example:

```
IP Address

192.168.1.25

Subnet Mask

255.255.255.0
```

---

## Binary Representation

```
IP

11000000.10101000.00000001.00011001

Mask

11111111.11111111.11111111.00000000
```

The `1`s represent the **network bits**.

The `0`s represent the **host bits**.

---

# Why Subnetting?

Suppose an office has:

```
500 Computers
```

Instead of putting every device in one huge network:

```
192.168.1.0
```

We divide it into smaller networks.

Benefits:

- Better performance
- Better security
- Easier management
- Reduced broadcast traffic

---

# 16. CIDR Notation

CIDR stands for:

```
Classless Inter-Domain Routing
```

Instead of writing:

```
255.255.255.0
```

Linux usually writes:

```
/24
```

Meaning:

```
24 Network Bits
```

Examples:

| CIDR | Subnet Mask |
|------|-------------|
| /8 | 255.0.0.0 |
| /16 | 255.255.0.0 |
| /24 | 255.255.255.0 |
| /32 | Single Host |

---

## Example

```
192.168.1.0/24
```

Contains:

```
256 Addresses

254 Usable Hosts
```

---

# Common CIDR Blocks

| CIDR | Hosts |
|------|-------|
| /30 | 2 |
| /29 | 6 |
| /28 | 14 |
| /27 | 30 |
| /26 | 62 |
| /25 | 126 |
| /24 | 254 |

---

# Kubernetes Example

Pod Network

```
10.244.0.0/16
```

Service Network

```
10.96.0.0/12
```

Understanding CIDR is essential when configuring Kubernetes clusters.

---

# 17. MAC Address

A MAC (Media Access Control) Address is a **hardware address** assigned to a network interface.

Example:

```
08:00:27:12:34:56
```

Characteristics:

- 48 bits
- Layer 2 address
- Used inside the same LAN
- Assigned by the manufacturer

---

## IP vs MAC

| IP Address | MAC Address |
|------------|-------------|
| Logical Address | Physical Address |
| Layer 3 | Layer 2 |
| Can Change | Usually Fixed |
| Used Across Networks | Used in Local Network |

---

# 18. ARP (Address Resolution Protocol)

Question:

```
I know the IP.

How do I find the MAC address?
```

ARP answers this.

---

## Example

Laptop wants to communicate with:

```
192.168.1.20
```

It broadcasts:

```
Who has 192.168.1.20?
```

Target replies:

```
I do.

My MAC is:

08:00:27:12:34:56
```

The mapping is stored in the ARP cache.

---

## View ARP Table

```bash
arp -a
```

or

```bash
ip neigh
```

---

# ARP Flow

```
Laptop

↓

Broadcast ARP Request

↓

Switch

↓

Destination Device

↓

ARP Reply

↓

Communication Starts
```

---

# 19. DNS (Domain Name System)

Humans remember names.

Computers use IP addresses.

DNS converts:

```
google.com

↓

142.250.x.x
```

Think of DNS as the Internet's phonebook.

---

## DNS Flow

```
Browser

↓

DNS Resolver

↓

DNS Server

↓

IP Address

↓

Website
```

---

## Linux Commands

Check DNS:

```bash
cat /etc/resolv.conf
```

Lookup:

```bash
nslookup google.com
```

or

```bash
dig google.com
```

---

# 20. DHCP (Dynamic Host Configuration Protocol)

DHCP automatically assigns:

- IP Address
- Gateway
- DNS Server
- Subnet Mask

Without DHCP:

You would configure every computer manually.

---

## DHCP Process

Known as **DORA**:

```
Discover

↓

Offer

↓

Request

↓

Acknowledge
```

---

# 21. Gateway

A gateway connects one network to another.

Example:

```
Laptop

↓

Router

↓

Internet
```

Default Gateway:

```
192.168.1.1
```

If a destination is outside the local network, packets are sent to the default gateway.

---

# 22. Routing

Routing is the process of selecting the best path for packets.

Example:

```
Laptop

↓

Home Router

↓

ISP

↓

Internet

↓

Google
```

Each router examines the destination IP and forwards the packet.

---

## View Routing Table

```bash
ip route
```

Example output:

```
default via 192.168.1.1 dev wlan0
192.168.1.0/24 dev wlan0
```

---

# 23. NAT (Network Address Translation)

Private IP addresses cannot communicate directly over the Internet.

Your router translates them into a public IP.

Example:

```
Laptop

192.168.1.20

↓

Router

↓

203.0.113.5

↓

Internet
```

Advantages:

- Conserves IPv4 addresses
- Adds a layer of isolation
- Allows multiple devices to share one public IP

---

# 24. Ports

An IP identifies a device.

A **Port** identifies an application on that device.

Example:

```
192.168.1.10

↓

Port 80

↓

Web Server
```

---

## Common Ports

| Port | Protocol | Service |
|------|----------|----------|
| 20/21 | TCP | FTP |
| 22 | TCP | SSH |
| 25 | TCP | SMTP |
| 53 | TCP/UDP | DNS |
| 80 | TCP | HTTP |
| 110 | TCP | POP3 |
| 143 | TCP | IMAP |
| 443 | TCP | HTTPS |
| 3306 | TCP | MySQL |
| 5432 | TCP | PostgreSQL |
| 6379 | TCP | Redis |
| 8080 | TCP | Alternative HTTP |
| 9090 | TCP | Prometheus |
| 3000 | TCP | Grafana |

---

# 25. TCP vs UDP

## TCP

Characteristics:

- Reliable
- Connection-oriented
- Ordered delivery
- Retransmits lost packets

Examples:

- HTTP
- HTTPS
- SSH
- FTP
- SMTP

---

## UDP

Characteristics:

- Faster
- Connectionless
- No guarantee of delivery
- Lower overhead

Examples:

- DNS Queries
- Video Streaming
- Online Gaming
- VoIP

---

## Comparison

| Feature | TCP | UDP |
|----------|-----|-----|
| Reliable | Yes | No |
| Speed | Slower | Faster |
| Handshake | Yes | No |
| Error Recovery | Yes | No |
| Streaming | Less Suitable | Better |

---

# 26. TCP Three-Way Handshake

Before TCP sends data, it establishes a connection.

```
Client

↓

SYN

↓

Server

↓

SYN + ACK

↓

Client

↓

ACK

↓

Connection Established
```

This ensures both sides are ready to communicate.

---

# 27. Common Networking Commands

## Show Interfaces

```bash
ip addr
```

---

## Show Routes

```bash
ip route
```

---

## Test Connectivity

```bash
ping google.com
```

---

## Show Open Ports

```bash
ss -tuln
```

---

## Show Listening Processes

```bash
ss -tulpn
```

---

## Download a File

```bash
wget https://example.com/file.zip
```

---

## Make an HTTP Request

```bash
curl https://google.com
```

---

## Display Hostname

```bash
hostname
```

---

## Show ARP Cache

```bash
ip neigh
```

---

# 28. Learning Summary

In this part, you learned:

- Subnet Masks
- CIDR Notation
- MAC Addresses
- ARP
- DNS
- DHCP
- Gateways
- Routing
- NAT
- Ports
- TCP vs UDP
- Three-Way Handshake
- Essential Linux networking commands

---

---

# 29. Firewalls

A firewall controls which network traffic is allowed or blocked.

Think of it as a **security guard** at the entrance to your system.

```
Internet
    │
    ▼
+-------------+
|  Firewall   |
+-------------+
    │
    ▼
 Linux Server
```

A firewall can:

- Allow traffic
- Block traffic
- Restrict specific ports
- Restrict specific IP addresses

---

# 30. UFW (Uncomplicated Firewall)

UFW is the default firewall tool on Ubuntu.

## Enable Firewall

```bash
sudo ufw enable
```

---

## Disable Firewall

```bash
sudo ufw disable
```

---

## Check Status

```bash
sudo ufw status
```

---

## Allow SSH

```bash
sudo ufw allow 22
```

---

## Allow HTTP

```bash
sudo ufw allow 80
```

---

## Allow HTTPS

```bash
sudo ufw allow 443
```

---

## Deny a Port

```bash
sudo ufw deny 3306
```

---

## Delete Rule

```bash
sudo ufw delete allow 80
```

---

# 31. iptables

`iptables` is the traditional Linux firewall framework.

Traffic flows through chains:

```
Incoming
      │
      ▼
INPUT Chain
      │
      ▼
Decision
```

Main chains:

- INPUT
- OUTPUT
- FORWARD

Example:

```bash
sudo iptables -L
```

Allow SSH:

```bash
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```

Block HTTP:

```bash
sudo iptables -A INPUT -p tcp --dport 80 -j DROP
```

---

# 32. nftables

Modern Linux distributions are moving toward **nftables**.

Advantages:

- Simpler rule syntax
- Better performance
- Unified IPv4 and IPv6 handling

View rules:

```bash
sudo nft list ruleset
```

---

# 33. Network Namespaces

Linux supports **network namespaces**, allowing multiple isolated network stacks on the same machine.

Each namespace has its own:

- Interfaces
- Routing table
- Firewall rules
- IP addresses

Containers rely on namespaces for network isolation.

---

## Create a Namespace

```bash
sudo ip netns add demo
```

List namespaces:

```bash
ip netns
```

Delete namespace:

```bash
sudo ip netns delete demo
```

---

# 34. Virtual Ethernet (veth)

Namespaces communicate using **virtual Ethernet pairs (veth)**.

```
Namespace A
   │
 veth0
==========
 veth1
   │
Namespace B
```

One end exists in one namespace, the other end in another namespace.

Docker uses veth pairs to connect containers to bridges.

---

# 35. Linux Bridge

A Linux bridge behaves like a virtual switch.

```
Container A
     │
Container B
     │
Container C
     │
   docker0
```

The bridge enables communication between connected interfaces.

View bridges:

```bash
ip link
```

or

```bash
bridge link
```

---

# 36. Docker Networking

Docker provides several network drivers.

## Bridge (Default)

```
Container
      │
 docker0 Bridge
      │
 Host
```

Default for standalone containers.

---

## Host

The container shares the host's network stack.

No network isolation.

---

## None

No networking is configured.

Useful for highly customized networking.

---

## Overlay

Used by Docker Swarm to connect containers across multiple hosts.

---

## Inspect Networks

```bash
docker network ls
```

Inspect one:

```bash
docker network inspect bridge
```

---

# 37. Kubernetes Networking

Kubernetes networking follows several important principles:

- Every Pod gets its own IP.
- Pods can communicate directly without NAT (within the cluster).
- Nodes can communicate with Pods.
- Services provide stable virtual IPs for Pods.

---

## Kubernetes Networking Model

```
+-----------------------+
| Node                  |
|                       |
| Pod A 10.244.0.2      |
| Pod B 10.244.0.3      |
+-----------------------+
           │
           ▼
+-----------------------+
| Service 10.96.0.10    |
+-----------------------+
           │
           ▼
      External Client
```

---

# 38. CNI (Container Network Interface)

Kubernetes uses **CNI plugins** to provide networking.

Popular CNI plugins:

- Flannel
- Calico
- Cilium
- Weave Net

Responsibilities:

- Assign Pod IPs
- Connect Pods
- Configure routing
- Apply network policies (plugin dependent)

---

# 39. Kubernetes Services

Pods are ephemeral—they can be recreated with new IPs.

A **Service** provides a stable endpoint.

Types:

- ClusterIP
- NodePort
- LoadBalancer
- ExternalName

Example:

```bash
kubectl get svc
```

---

# 40. Ingress

Ingress provides HTTP/HTTPS routing into a Kubernetes cluster.

Example:

```
Internet
      │
Ingress Controller
      │
+------+------+
|             |
App A      App B
```

Benefits:

- Path-based routing
- Host-based routing
- TLS termination
- Centralized access

---

# 41. Network Troubleshooting

## Check IP Address

```bash
ip addr
```

---

## Check Routes

```bash
ip route
```

---

## Test Connectivity

```bash
ping 8.8.8.8
```

---

## Test DNS

```bash
nslookup google.com
```

or

```bash
dig google.com
```

---

## View Listening Ports

```bash
ss -tulpn
```

---

## Test HTTP

```bash
curl http://localhost
```

---

## Trace Route

```bash
traceroute google.com
```

---

## View Network Interfaces

```bash
ip link
```

---

# 42. Kubernetes Networking Troubleshooting

List Pods:

```bash
kubectl get pods -o wide
```

---

Describe Pod:

```bash
kubectl describe pod POD_NAME
```

---

View Logs:

```bash
kubectl logs POD_NAME
```

---

Check Services:

```bash
kubectl get svc
```

---

Check Endpoints:

```bash
kubectl get endpoints
```

---

Open Shell:

```bash
kubectl exec -it POD_NAME -- /bin/sh
```

---

Test DNS from a Pod:

```bash
nslookup kubernetes.default
```

---

# 43. Hands-on Lab

## Find Your IP

```bash
ip addr
```

---

## Show Routes

```bash
ip route
```

---

## Ping Google DNS

```bash
ping 8.8.8.8
```

---

## Test DNS Resolution

```bash
nslookup google.com
```

---

## Display Listening Ports

```bash
ss -tulpn
```

---

## Show ARP Cache

```bash
ip neigh
```

---

## List Docker Networks

```bash
docker network ls
```

---

## Inspect Default Bridge

```bash
docker network inspect bridge
```

---

## List Kubernetes Services

```bash
kubectl get svc
```

---

## Show Pod IPs

```bash
kubectl get pods -o wide
```

---

# 44. Interview Questions

## Beginner

1. What is an IP address?
2. Difference between IPv4 and IPv6?
3. What is DNS?
4. What is DHCP?
5. What is a subnet mask?

---

## Intermediate

1. Explain ARP.
2. Difference between TCP and UDP?
3. What is NAT?
4. What is CIDR notation?
5. Difference between a switch and a router?

---

## Advanced

1. Explain the TCP three-way handshake.
2. How does Kubernetes networking work?
3. What is a CNI plugin?
4. Why does every Pod get its own IP?
5. Difference between ClusterIP and NodePort?
6. How would you troubleshoot a Pod that cannot reach another Service?
7. Compare `iptables`, `nftables`, and `ufw`.

---

# 45. One-Page Networking Cheat Sheet

| Command | Purpose |
|---------|---------|
| `ip addr` | Show IP addresses |
| `ip route` | Show routing table |
| `ip link` | Show network interfaces |
| `ip neigh` | Show ARP cache |
| `ping host` | Test connectivity |
| `curl URL` | Make HTTP request |
| `wget URL` | Download files |
| `ss -tulpn` | Show listening ports |
| `nslookup host` | DNS lookup |
| `dig host` | Detailed DNS lookup |
| `hostname` | Show hostname |
| `traceroute host` | Show packet path |
| `docker network ls` | List Docker networks |
| `docker network inspect bridge` | Inspect bridge network |
| `kubectl get svc` | List Services |
| `kubectl get pods -o wide` | Show Pod IPs |
| `kubectl describe pod` | Inspect Pod networking |
| `kubectl logs pod` | View Pod logs |
| `kubectl exec -it pod -- /bin/sh` | Open shell in Pod |

---

# 46. Common Mistakes

❌ Confusing IP addresses with MAC addresses.

❌ Assuming `ping` tests only the Internet—it primarily tests IP connectivity.

❌ Forgetting that DNS issues can make a network appear "down" even when connectivity exists.

❌ Opening unnecessary ports in the firewall.

❌ Assuming Pods keep the same IP forever; Pod IPs are typically ephemeral.

❌ Exposing applications with `NodePort` in production when an Ingress or LoadBalancer is more appropriate.

---

# 47. Best Practices

- Prefer DNS names over hard-coded IP addresses.
- Use CIDR planning before creating Kubernetes clusters.
- Keep firewalls enabled and expose only required ports.
- Verify both network connectivity (`ping`) and DNS resolution (`nslookup`/`dig`) during troubleshooting.
- Use Services instead of Pod IPs when connecting applications in Kubernetes.
- Monitor network traffic and latency in production environments.

---

# 48. Chapter Summary

In this chapter, you learned:

- Networking fundamentals
- OSI and TCP/IP models
- IPv4 and IPv6
- Subnetting and CIDR
- MAC addresses and ARP
- DNS and DHCP
- Gateways and routing
- NAT
- TCP vs UDP
- Ports and common protocols
- Firewalls (`ufw`, `iptables`, `nftables`)
- Network namespaces
- Docker networking
- Kubernetes networking
- CNI plugins
- Services and Ingress
- Troubleshooting techniques
- Hands-on networking commands
