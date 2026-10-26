# IP Addressing Technology: Complete Beginner's Introduction

## 1. What Is IP Addressing?

IP stands for Internet Protocol.

IP addressing is a system used to logically identify network interfaces so that devices can send data to the correct destinations across interconnected networks.

An IPv4 address looks like this:

`192.168.1.10`

Imagine sending a parcel to someone's house. You need an address to identify the destination. Similarly, computers need addressing information to communicate across networks.

However, an IP address is not a permanent physical identity. It can change, and a device can have multiple IP addresses.

In simple words: IP addressing helps networking devices determine where data should go.

---

## 2. Why Was IP Addressing Needed?

Imagine connecting ten computers inside one room. They can communicate through a local network.

Now imagine connecting thousands of networks across different cities and countries.

Questions arise:

- How does a computer identify a destination?
- How does data travel across multiple networks?
- How does a router know where to forward a packet?
- How can different network technologies communicate with one another?

IP provides a common addressing and packet-forwarding system for interconnected networks.

Without a common internetworking protocol, communication between different networks would be much more difficult.

---

## 3. What Was Used Before IP?

Before today's Internet architecture became established, early computer networks used different communication protocols.

One important example was ARPANET, an early packet-switched network.

ARPANET initially used NCP (Network Control Protocol) for host-to-host communication. On January 1, 1983, ARPANET transitioned to TCP/IP.

IP enabled packets to travel across interconnected networks, while TCP provided transport services for applications requiring reliable, ordered delivery.

Before DNS became widely used, systems also maintained mappings between hostnames and network addresses using files such as `HOSTS.TXT`.

### Important distinction

NCP was a predecessor to TCP/IP in ARPANET. It was not simply another version of IPv4.

IP addressing developed as part of a broader effort to connect different networks into an internetwork.

---

## 4. What Are the Main Types of IP?

There are two principal versions of Internet Protocol used today.

| Feature | IPv4 | IPv6 |
| --- | --- | --- |
| Address size | 32 bits | 128 bits |
| Representation | Dotted decimal | Hexadecimal groups |
| Example | `192.168.1.10` | `2001:db8::10` |
| Address capacity | About 4.29 billion possible bit patterns | 2^128 possible bit patterns |
| Main motivation | Original widely deployed Internet addressing | Expanded address capacity and other protocol improvements |

IPv4 remains widely used. IPv6 was designed as its successor, providing a vastly larger address space.

Both protocols can operate on modern networks. IPv6 adoption does not mean IPv4 has disappeared.

---

## 5. Are There Alternatives to IPv4?

Yes, but not all alternatives perform the same job.

### A. IPv6 — Alternative IP Version

IPv6 provides network-layer addressing using 128-bit addresses.

It is the principal successor to IPv4.

### B. MAC Addresses — Local Network Identification

A MAC address identifies a network interface at the link layer in common Ethernet and Wi-Fi networking.

Example:

`00:1A:2B:3C:4D:5E`

MAC addresses and IP addresses serve different purposes.

- IP addresses support logical addressing and routing across networks.
- MAC addresses support frame delivery on a local link.

A MAC address is not a universal replacement for an IP address.

### C. DNS Names — Human-Friendly Names

People generally prefer names such as:

`example.com`

rather than memorizing numerical IP addresses.

DNS translates domain names into IP addresses when required.

DNS does not replace IP addressing. It helps applications discover the addresses associated with names.

### D. Other Network-Layer Protocols

Other networking technologies and protocols exist, including legacy network-layer protocols such as IPX and AppleTalk.

However, IPv4 and IPv6 are the principal IP versions used in modern Internet networking.

Key takeaway: IPv6 is an alternative IP version. MAC addresses and DNS names solve different problems.

---

## 6. Where Does IP Addressing Operate?

IP operates at the Network Layer (Layer 3) of the OSI model.

In the TCP/IP model, IP belongs to the Internet layer.

A simplified networking stack looks like this:

| OSI Layer | Example | Main Purpose |
| --- | --- | --- |
| Application | HTTP, DNS | Application communication |
| Transport | TCP, UDP | Communication between application endpoints |
| Network | IPv4, IPv6 | Logical addressing and routing |
| Data Link | Ethernet, Wi-Fi | Delivery across a local link |
| Physical | Cables, radio signals | Transmission of bits |

This is a simplified mapping between the OSI and TCP/IP models.

### What happens at Layer 3?

Routers examine IP packet information and use routing tables to determine the next hop toward a destination.

Switches commonly forward Ethernet frames using MAC addresses at Layer 2.

Some devices combine routing and switching functions.

---

## 7. How Does IP Addressing Work?

Consider this example:

- Computer A: `192.168.1.10/24`
- Computer B: `192.168.1.20/24`
- Router gateway: `192.168.1.1`

All three addresses belong to the same IPv4 subnet.

When Computer A sends data to Computer B:

1. The application creates data to send.
2. The transport layer adds its protocol information.
3. IP adds source and destination IP addresses.
4. The data-link layer prepares a frame for the local network.
5. The network delivers the frame to Computer B.
6. Computer B processes the received data through its networking stack.

If Computer A needs to contact a destination on another network, it normally sends the packet to its default gateway.

The router examines the destination IP address and forwards the packet toward the next hop.

The source and destination IP addresses generally remain the same across routed hops, although mechanisms such as Network Address Translation (NAT) can change them.

---

## 8. What Is an IPv4 Address?

IPv4 uses a 32-bit address divided into four 8-bit groups called octets.

Example:

`192.168.1.10`

Each octet ranges from 0 to 255.

IPv4 addresses contain 32 bits, allowing 2^32, or 4,294,967,296, possible bit patterns.

However, not all addresses are available for ordinary host assignment. Some are reserved for special purposes, and others have specific routing or allocation rules.

---

## 9. Public and Private IPv4 Addresses

### Private IPv4 Addresses

Private addresses are intended for use within private networks.

The three RFC 1918 ranges are:

| Range | CIDR |
| --- | --- |
| `10.0.0.0` – `10.255.255.255` | `10.0.0.0/8` |
| `172.16.0.0` – `172.31.255.255` | `172.16.0.0/12` |
| `192.168.0.0` – `192.168.255.255` | `192.168.0.0/16` |

These addresses are commonly used in home networks, offices, data centers, and cloud virtual networks.

Private addresses are not directly routed across the public Internet.

### Public IPv4 Addresses

Public addresses are globally coordinated for Internet use and are subject to allocation and routing policies.

A public IP address may identify a network interface or a service reachable through the Internet, subject to routing, firewall, and NAT configurations.

---

## 10. Why Do We Need Subnet Masks?

An IP address alone does not tell a host the complete network boundary.

A subnet mask or CIDR prefix identifies which bits represent the network prefix.

Example:

`192.168.1.10/24`

Here, `/24` means the first 24 bits are the network prefix.

The corresponding subnet mask is:

`255.255.255.0`

For this subnet:

- Network address: `192.168.1.0`
- Typical usable host range: `192.168.1.1` – `192.168.1.254`
- Broadcast address: `192.168.1.255`

Subnetting divides address space into manageable networks and supports efficient routing and network design.

---

## 11. What Is the Difference Between IP, TCP, and MAC?

These technologies perform different functions.

- IP: Provides logical addressing and forwards packets between networks.
- TCP: Provides a reliable, ordered byte stream between endpoints.
- UDP: Provides datagram transport without TCP's delivery and ordering guarantees.
- MAC: Supports frame delivery across a local link.
- DNS: Helps resolve domain names to IP addresses.

They work together, but none of these terms is interchangeable.

IP by itself does not guarantee that a packet will arrive successfully.

---

## 12. How Is IP Addressing Used in DevOps?

IP addressing is fundamental to many DevOps activities.

### Linux

Configure addresses, inspect network interfaces, and troubleshoot connectivity.

### Docker

Containers can receive addresses on container networks, allowing communication with other containers and external networks.

### Kubernetes

Pods and services use networking and addressing mechanisms to enable application communication.

### AWS

VPCs, subnets, route tables, security groups, and network interfaces form part of cloud networking.

### Azure

Virtual networks, subnets, network interfaces, and routing provide connectivity for cloud resources.

### Jenkins and CI/CD

Build agents and deployment targets need network connectivity to communicate with repositories, servers, APIs, and other services.

Understanding IP addressing makes cloud networking and application troubleshooting much easier.

---

## 13. Learning Roadmap

Study IP addressing in this order:

1. Networking fundamentals
2. IPv4 structure and binary conversion
3. Network and host portions
4. Subnet masks and CIDR notation
5. Network IDs and broadcast addresses
6. Public and private IP addresses
7. Default gateways and routing
8. Subnetting exercises
9. IPv6 fundamentals
10. Practical networking in Linux and cloud environments

---

## 14. Official References

- RFC 791 — Internet Protocol (IPv4)
- RFC 1918 — Address Allocation for Private Internets
- RFC 8200 — Internet Protocol, Version 6 (IPv6)
- Internet Society — A Brief History of the Internet

---

## Final Takeaway

IP addressing is a foundational part of modern networking.

It provides logical addressing that allows devices and routers to communicate across interconnected networks. IPv4 remains widely deployed, while IPv6 provides a much larger address space.

To become confident in DevOps networking, understand not only how to calculate an IP address, but also how addressing, subnetting, routing, DNS, MAC addresses, and transport protocols work together.
