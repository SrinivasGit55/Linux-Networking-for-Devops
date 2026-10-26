#IPv4 Addressing Basics

## 1. Learning Objectives

By the end of this lesson, you should be able to:

- Explain what an IPv4 address is and why it is used
- Convert between binary and decimal values in an IP address
- Identify the network and host portions of an address using a subnet mask
- Understand classful addressing (A, B, C, D, E)
- Explain valid host ranges, network IDs, broadcast IDs, and default gateway rules
- Recognize common mistakes while configuring IP addresses

---

## 2. Introduction

An IP address is a logical address used by devices to identify each other on a network. In IPv4, every device gets a 32-bit address, usually written in dotted decimal format such as:

`192.168.100.25`

This address is not random. It carries important information:

- which network the device belongs to
- which host/device within that network it is
- how it reaches other networks through a gateway (router)

The source video explains these ideas using simple analogies. This note restates the concepts clearly and accurately.

---

## 3. Core Concepts

### 3.1 IPv4 is a 32-bit address

IPv4 uses 32 bits total, split into four octets:

`11000000.10101000.01100100.00011001`

Each octet is 8 bits:

- 8 bits = 1 octet
- 4 octets = 32 bits

In dotted decimal form this becomes:

`192.168.100.25`

---

### 3.2 Octet

An octet is a group of 8 bits.

Examples:

- `11000000` = `192`
- `10101000` = `168`
- `01100100` = `100`
- `00011001` = `25`

So:

`11000000.10101000.01100100.00011001`

becomes:

`192.168.100.25`

---

### 3.3 Binary to Decimal Quick Method

Use this binary value chart:

`128 64 32 16 8 4 2 1`

Example:

`11000000`

Bits:

- 128 = 1
- 64 = 1
- 32 = 0
- 16 = 0
- 8 = 0
- 4 = 0
- 2 = 0
- 1 = 0

Calculation:

`128 + 64 = 192`

So:

`11000000` = `192`

Another example:

`10101000`

`128 + 32 + 8 = 168`

So:

`10101000` = `168`

This is the basic method for converting binary to decimal.

---

## 4. IP Address, Subnet Mask, and Gateway

When a device is configured with an IP address, it usually also has:

- Subnet mask
- Default gateway

These values work together:

- IP address: identifies the host
- Subnet mask: identifies the network portion
- Default gateway: the exit point to reach another network

Example:

- IP: `192.168.100.25`
- Subnet mask: `255.255.255.0`
- Gateway: `192.168.100.1`

The subnet mask is often written in binary as:

`11111111.11111111.11111111.00000000`

This means:

- first 24 bits are network
- last 8 bits are host

So:

- network portion = `192.168.100`
- host portion = `.25`

---

## 5. Network and Host Portion

An IPv4 address has two parts:

- Network part
- Host part

The subnet mask decides where the split happens.

Example:

- IP: `192.168.100.25`
- Subnet: `255.255.255.0`

Network bits:

`192.168.100`

Host bits:

`25`

This means the device belongs to the network:

`192.168.100.0/24`

The host is:

`25`

### Important rule

If a device wants to reach another host on the same network, it can do so directly.

If it needs to reach a host on a different network, it must send the packet to the default gateway.

The gateway must be in the same network as the device.

Example:

- Device IP: `192.168.100.25`
- Gateway: `192.168.100.1`
- Valid because both are in `192.168.100.0/24`

This would be invalid:

- Device IP: `192.168.100.25`
- Gateway: `10.0.0.1`

Because the network bits are different.

---

## 6. IPv4 Address Range

Each octet can range from:

`0` to `255`

Example valid IPv4 addresses:

- `10.0.0.1`
- `172.16.45.77`
- `192.168.1.10`

Invalid IPv4 addresses:

- `192.168.100.315`
- Why? Because `315` is greater than `255`

---

## 7. Classful IPv4 Addressing

The video explains classful addressing:

- Class A: `1.0.0.0` to `126.255.255.255`
- Class B: `128.0.0.0` to `191.255.255.255`
- Class C: `192.0.0.0` to `223.255.255.255`
- Class D: `224.0.0.0` to `239.255.255.255` (multicast)
- Class E: `240.0.0.0` to `255.255.255.255` (reserved/experimental)

### Important note

This is a legacy way of thinking. In modern networking, CIDR and subnetting are more common than relying only on classful ranges.

### Special addresses

- `127.x.x.x` = loopback address
- Used for testing local network stack
- Example: `127.0.0.1`

---

## 8. Class A, B, and C Summary

### Class A

First octet: `1-126`

Default subnet mask: `255.0.0.0` (`/8`)

Example:

`10.50.20.30 /8`

Network portion:

`10.0.0.0`

Host portion:

`50.20.30`

Class A networks are very large and are not usually used for small office or home networks.

---

### Class B

First octet: `128-191`

Default subnet mask: `255.255.0.0` (`/16`)

Example:

`172.16.10.20 /16`

Network:

`172.16.0.0`

Host:

`10.20`

Class B networks are medium-sized but still quite large.

---

### Class C

First octet: `192-223`

Default subnet mask: `255.255.255.0` (`/24`)

Example:

`192.168.100.25 /24`

Network:

`192.168.100.0`

Host:

`25`

Class C is the most common default network size in small LANs.

---

## 9. Network ID and Broadcast ID

Every network has:

- Network ID: first address in the network
- Broadcast ID: last address in the network

Example:

`192.168.100.0/24`

- Network ID: `192.168.100.0`
- Broadcast ID: `192.168.100.255`

Valid host range:

`192.168.100.1` to `192.168.100.254`

Why not `.0` and `.255`?

- `.0` is reserved as the network address
- `.255` is reserved as the broadcast address

### Formula for valid hosts

`2^(host bits) - 2`

For `/24`:

- host bits = 8
- `2^8 - 2 = 254`

This is why a `192.168.100.0/24` network can support 254 usable hosts.

---

## 10. Example: `/24` Network

IP: `192.168.100.25`

Subnet mask: `255.255.255.0`

Binary subnet:

`11111111.11111111.11111111.00000000`

Network:

`192.168.100.0`

Broadcast:

`192.168.100.255`

Valid hosts:

`192.168.100.1` to `192.168.100.254`

Gateway should be inside the same network:

`192.168.100.1`

This is valid:

- IP: `192.168.100.25`
- Mask: `255.255.255.0`
- Gateway: `192.168.100.1`

This is invalid:

- IP: `192.168.100.25`
- Mask: `255.255.255.0`
- Gateway: `10.0.0.1`

---

## 11. Why We Use Subnetting

The video points out that large class-based networks are inefficient.

Example:

- Class B network: `172.16.0.0/16`
- Total usable IPs: about 65,534 hosts
- This is too large for many networks

Subnetting helps to divide a large network into smaller segments and reduce:

- broadcast traffic
- network congestion
- inefficient address usage
- operational complexity

This is why subnetting matters in real network design.

---

## 12. Default Gateway Rule

The default gateway must be in the same subnet as the host.

Example:

- Host: `192.168.1.50/24`
- Gateway: `192.168.1.1`
- Valid

Example:

- Host: `192.168.1.50/24`
- Gateway: `192.168.2.1`
- Invalid, because different subnet

This is a common beginner mistake.

---

## 13. Common Mistakes

### Mistake 1: IP octet greater than 255

Example:

`192.168.100.315`

Invalid because 315 > 255

### Mistake 2: Gateway not in same network

Example:

- Host: `10.0.0.10/24`
- Gateway: `192.168.1.1`

Invalid because different network

### Mistake 3: Using network ID or broadcast ID as a host

Example:

- `192.168.100.0/24`
- `192.168.100.255/24`

These are not usable host addresses.

### Mistake 4: Believing class alone determines everything

In modern networking, the subnet mask matters more than the class.

Example:

`172.16.10.20/16`

This is not the same as assuming all `172.x.x.x` networks are large default Class B networks. Subnetting changes actual network boundaries.

---

## 14. Practice Questions

### Question 1

What is the network and host portion of:

`192.168.10.15/24`?

Answer:

- Network: `192.168.10.0`
- Host: `15`

---

### Question 2

Is `10.0.0.100/8` valid?

Answer:

- Yes
- Network: `10.0.0.0/8`
- Host: `0.0.100` in terms of the remaining bits

---

### Question 3

Is `192.168.1.200` with gateway `192.168.2.1` valid?

Answer:

- No, because different subnets

---

### Question 4

What is the broadcast address of `172.16.5.0/24`?

Answer:

`172.16.5.255`

---

### Question 5

What is the valid host range of `10.10.10.0/24`?

Answer:

`10.10.10.1` to `10.10.10.254`

---

## 15. Interview-Oriented Questions and Answers

### Q1: What is an IPv4 address?

An IPv4 address is a unique logical address assigned to a device so it can communicate over a network.

### Q2: What is the difference between an IP address and a subnet mask?

The IP address identifies the device; the subnet mask identifies how much of that address is the network portion.

### Q3: What is the default gateway?

It is the device used to exit the local network and reach other networks, typically a router.

### Q4: What is the network ID?

The first address in a network; it represents the network itself.

### Q5: What is the broadcast address?

The last address in a network; it is used to send a message to all hosts in that network.

### Q6: Why is `255` the maximum value in an octet?

Because an octet is 8 bits, and the maximum value of 8 bits is `11111111`, which is `255` in decimal.

### Q7: Why do we use subnetting?

To divide large networks into smaller, manageable segments and reduce broadcast overhead.

---

## 16. Real-World Use Cases

- Small office LANs often use `192.168.x.x/24`
- Home routers commonly use `192.168.0.0/24` or `192.168.1.0/24`
- Enterprises use VLSM and CIDR for efficient address planning
- Cloud platforms use private IP ranges such as:
  - `10.0.0.0/8`
  - `172.16.0.0/12`
  - `192.168.0.0/16`

---

## 17. Best Practices

- Always verify the subnet mask and gateway together
- Do not assign the network ID or broadcast ID to a host
- Use validation before saving configuration
- Prefer structured subnet planning over random addressing
- Understand CIDR and subnetting rather than relying only on classful logic

---

## 18. Key Takeaways

- IPv4 is a 32-bit address written as four octets
- Each octet is 8 bits and ranges from 0 to 255
- Subnet mask determines network and host portions
- Default gateway must be in the same subnet
- Network ID and broadcast ID are reserved
- Valid hosts = total addresses minus 2
- Classful addressing is a conceptual foundation, but modern networks use CIDR and subnetting

---

## 19. Revision Checklist

Before the next interview or lab, confirm that you can:

- Convert binary to decimal
- Convert decimal to binary
- Identify network and host parts
- Calculate network, broadcast, and valid hosts
- Explain why the gateway must be in the same subnet
- Differentiate between classful and modern CIDR-based networking

---

## 20. Official References

- Cisco: https://www.cisco.com
- Cisco Networking Academy: https://www.netacad.com
- IANA IPv4 Special-Purpose Address Registry: https://www.iana.org/assignments/iana-ipv4-special-registry/iana-ipv4-special-registry.xhtml
- RFC 791: Internet Protocol: https://datatracker.ietf.org/doc/html/rfc791
- RFC 1918: Private Address Allocation: https://datatracker.ietf.org/doc/html/rfc1918

---

This note simplifies the source video while correcting the oversimplified or outdated classful explanation and clarifying the basic rules for IP configuration.
