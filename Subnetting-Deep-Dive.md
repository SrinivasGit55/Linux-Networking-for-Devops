# Subnetting Deep Dive –
## 1. Title and Learning Objectives

Title: Subnetting Deep Dive 
### Learning Objectives
By the end of this lesson, you should be able to:
- Explain what subnetting is and why it became necessary
- Differentiate between public IPs, private IPs, and NAT
- Understand Class C /24 behavior: network ID, broadcast ID, valid host range
- Explain CIDR and classless addressing
- Borrow bits from the host portion to create smaller subnets
- Calculate subnets for /25, /26, /27, /28, /29, /30 in Class C
- Memorize the Class C magic table
- Solve CCNA-style subnetting questions without a calculator
- Understand VLSM (Variable Length Subnet Mask) and use it for efficient IP planning
- Apply subnetting in DevOps scenarios such as AWS VPCs, Kubernetes, Docker, and firewalls

---

## 2. Why subnetting matters

Imagine a large office with one big LAN:
- 192.168.100.0/24 gives 256 addresses total
- usable hosts are 254
- if the company only needs 20 devices, the remaining 234 IPs are wasted

This was acceptable in the early internet era, but as the number of connected devices exploded, IPv4 address space became a serious problem.

### The real problem
IPv4 has around 4.3 billion addresses. That sounded huge in the 1980s, but with:
- personal computers
- mobile phones
- printers
- IoT devices
- servers
- cloud workloads

the address pool became insufficient.

### What changed?
The networking industry introduced:
- private addressing
- NAT
- subnetting
- CIDR
- IPv6

Subnetting became mandatory for efficient IP address planning.

---

## 3. Private vs Public IPs

### Public IPs
- Routable on the Internet
- Assigned by ISP or cloud provider
- Can be reached directly from outside the network

### Private IPs
- Not meant for direct Internet routing
- Used only inside private networks
- Common ranges:
  - 10.0.0.0/8
  - 172.16.0.0/12
  - 192.168.0.0/16

### NAT (Network Address Translation)
NAT lets devices with private IP addresses access the Internet through a public IP on the router/firewall.

Example:
- home laptop: 192.168.1.25
- home router: public IP from ISP
- browser request goes out via router
- router changes the source address to public IP
- response comes back to the router
- router translates it back to 192.168.1.25

This is why private IPs are common in offices, home networks, and labs.

### Important idea
A private IP can communicate inside an internal network, but it cannot go to the public Internet directly.

---

## 4. Classful vs Classless Addressing

### Classful addressing
Originally, IPv4 was divided into classes:
- Class A
- Class B
- Class C

Example:
- 192.168.1.0/24 is a Class C network by default

But classful addressing was rigid and wasted many addresses.

### Classless addressing / CIDR
CIDR (Classless Inter-Domain Routing) changed the approach.

Instead of fixed classes, we use a prefix length:
- /24
- /25
- /26
- /27

This is classless because the network/host boundary is not fixed by class rules anymore.

Example:
- 192.168.100.225/24
- 192.168.100.225/25
- 192.168.100.225/26

The network boundary moves as needed.

---

## 5. Review of Class C /24

Take this example:
- IP: 192.168.100.225
- Mask: 255.255.255.0
- CIDR: /24

### Binary mask
255.255.255.0 =

11111111.11111111.11111111.00000000

This means:
- first 24 bits = network part
- last 8 bits = host part

### Number of bits
- Network bits = 24
- Host bits = 8

### Formula
Usable hosts = 2^(host bits) - 2

For /24:
- 2^8 - 2 = 256 - 2 = 254

### Network ID
Set all host bits to 0:

192.168.100.0

### Broadcast ID
Set all host bits to 1:

192.168.100.255

### Valid host range
- 192.168.100.1 to 192.168.100.254

### Practical note
In a subnet:
- first address = network ID
- last address = broadcast ID
- addresses in-between are usable hosts

---

## 6. What is subnetting?

Subnetting = dividing one large network into multiple smaller networks by borrowing bits from the host portion.

Think of it like this:

Large network:
- 192.168.100.0/24

Split it into two smaller networks:
- 192.168.100.0/25
- 192.168.100.128/25

You are not changing the original address range completely; you are only redefining where the network boundary begins.

### Why do we borrow bits?
Because the network part needs more bits if we want more subnets.

Example:
- /24 has 8 host bits
- if we borrow 1 bit, network becomes /25
- now we have 7 host bits left

### The most important idea
Subnetting:
- increases the number of networks
- decreases the number of hosts per network

---

## 7. Place values in the last octet

This is the most important table to memorize.

Last octet bits:
128 64 32 16 8 4 2 1

These values are the weights of each bit.

Example:
- 10000000 = 128
- 11000000 = 192
- 11100000 = 224
- 11110000 = 240
- 11111000 = 248
- 11111100 = 252

These mask values are used heavily in Class C subnetting.

---

## 8. Block size concept

When you borrow bits, the block size is the value of the last borrowed bit.

Examples:
- borrow 1 bit -> block size 128
- borrow 2 bits -> block size 64
- borrow 3 bits -> block size 32
- borrow 4 bits -> block size 16
- borrow 5 bits -> block size 8
- borrow 6 bits -> block size 4

### Formula
Number of subnets = 2^(bits borrowed)

### Example
Borrow 2 bits:
- subnets = 2^2 = 4
- block size = 64

---

## 9. Why subtract 2?

Every subnet has:
- network ID
- broadcast ID

These two addresses cannot be assigned to hosts.

So:

usable hosts = block size - 2

This is why:
/26
- block size = 64
- usable = 62

/25
- block size = 128
- usable = 126

---

## 10. Example 1: Borrow 1 bit -> /25

Original:
- 192.168.100.225 /24
- mask: 255.255.255.0

Borrow 1 bit:
- new mask: 255.255.255.128
- binary last octet: 10000000
- CIDR: /25

### Calculations
- bits borrowed = 1
- subnets = 2^1 = 2
- block size = 128
- usable hosts = 128 - 2 = 126

### Network IDs
- 192.168.100.0
- 192.168.100.128

### Broadcast IDs
- 192.168.100.127
- 192.168.100.255

### Subnet ranges
- 192.168.100.0/25 = 192.168.100.0 to 192.168.100.127
- 192.168.100.128/25 = 192.168.100.128 to 192.168.100.255

### Classroom explanation
Borrowing 1 bit creates 2 subnets. Each subnet is half the size of the original /24.

---

## 11. Example 2: Borrow 2 bits -> /26

Original network:
- 192.168.100.0/24

Borrow 2 bits:
- new mask: 255.255.255.192
- binary last octet: 11000000
- CIDR: /26

### Calculations
- bits borrowed = 2
- subnets = 2^2 = 4
- block size = 64
- usable hosts = 64 - 2 = 62

### Network IDs
- 192.168.100.0
- 192.168.100.64
- 192.168.100.128
- 192.168.100.192

### Broadcast IDs
- 192.168.100.63
- 192.168.100.127
- 192.168.100.191
- 192.168.100.255

### Subnet ranges
- 192.168.100.0/26
- 192.168.100.64/26
- 192.168.100.128/26
- 192.168.100.192/26

### Classroom explanation
Borrowing 2 bits creates 4 subnets, each with 64 addresses total and 62 usable hosts.

---

## 12. Example 3: Borrow 3 bits -> /27

Borrow 3 bits:
- mask = 255.255.255.224
- binary last octet = 11100000
- CIDR = /27

### Calculations
- bits borrowed = 3
- subnets = 2^3 = 8
- block size = 32
- usable hosts = 32 - 2 = 30

### Network IDs in last octet
0, 32, 64, 96, 128, 160, 192, 224

### Example IP
192.168.225.212 /27

Find the block:
- 212 is between 192 and 223
- therefore it belongs to the 192 subnet

### Result
- Network ID = 192.168.225.192/27
- Broadcast ID = 192.168.225.223
- Usable hosts = 192.168.225.193 to 192.168.225.222

---

## 13. Magic table for Class C

This is the most important shortcut for Class C subnetting.

| Borrowed Bits | Mask Value | Binary | Subnets | Block Size | Usable Hosts | CIDR |
|---|---:|---|---:|---:|---:|---|
| 0 | 0 | 00000000 | 1 | 256 | 254 | /24 |
| 1 | 128 | 10000000 | 2 | 128 | 126 | /25 |
| 2 | 192 | 11000000 | 4 | 64 | 62 | /26 |
| 3 | 224 | 11100000 | 8 | 32 | 30 | /27 |
| 4 | 240 | 11110000 | 16 | 16 | 14 | /28 |
| 5 | 248 | 11111000 | 32 | 8 | 6 | /29 |
| 6 | 252 | 11111100 | 64 | 4 | 2 | /30 |

### Simple memory trick
The mask values are:
- 128
- 192
- 224
- 240
- 248
- 252

They are cumulative:
- 128 = 1 bit
- 192 = 128 + 64
- 224 = 128 + 64 + 32
- 240 = 128 + 64 + 32 + 16
- 248 = +8
- 252 = +4

### Quick check
For a Class C network:
- /25 = 255.255.255.128
- /26 = 255.255.255.192
- /27 = 255.255.255.224
- /28 = 255.255.255.240
- /29 = 255.255.255.248
- /30 = 255.255.255.252

### Special cases
- /31 is used mainly for point-to-point links
- /32 is a host route

These are special-case exceptions and are not the common exam examples for beginners.

---

## 14. How to solve subnetting questions in seconds

### Step-by-step method

1. Identify the network type or CIDR
   - Example: 192.168.1.0/24

2. Determine how many bits are borrowed
   - Example: /26 means 2 bits borrowed

3. Find the mask
   - /26 -> 255.255.255.192

4. Calculate block size
   - 256 / 4 = 64

5. Calculate usable hosts
   - 64 - 2 = 62

6. List network IDs
   - 0, 64, 128, 192

7. Find broadcast IDs
   - 63, 127, 191, 255

8. For a given IP, decide which block it belongs to

---

## 15. CCNA-style example 1: Create 3 subnets

Question:
Create subnets for 192.168.1.0

### Important rule
Number of subnets must be a power of 2:
- 2, 4, 8, 16, ...

You cannot create exactly 3 subnets.

Choose 4 subnets.

### Result
- /26
- mask = 255.255.255.192
- block size = 64

Subnets:
- 192.168.1.0/26
- 192.168.1.64/26
- 192.168.1.128/26
- 192.168.1.192/26

### Why this matters
This is a classic exam trick. People often think “3 networks” means “3 subnets,” but the correct answer is usually 4 because subnet counts must be powers of 2.

---

## 16. CCNA-style example 2: Find subnet from an IP

Question:
Given:
- IP = 192.168.225.212
- Mask = 255.255.255.224

### Step 1: Convert mask to CIDR
255.255.255.224 = /27

### Step 2: Block size
/27 -> 8 subnets -> block size = 32

### Step 3: Network boundaries
Possible network starts in last octet:
0, 32, 64, 96, 128, 160, 192, 224

### Step 4: Determine block
212 is between 192 and 223.

### Answer
- Network = 192.168.225.192/27
- Broadcast = 192.168.225.223
- Usable host range = 192.168.225.193 to 192.168.225.222

This is a standard subnetting question that appears often in networking interviews.

---

## 17. VLSM (Variable Length Subnet Mask)

Equal subnetting is simple but wasteful.

Example:
- all subnets are /26
- every subnet gets 62 usable hosts
- if one team needs only 20 hosts, you still waste 42 addresses

VLSM solves this by allowing different subnet sizes in the same network.

### Why VLSM is important
In real networks, different teams or services need different numbers of addresses.

Examples:
- Dev team: 30 hosts
- QA team: 50 hosts
- DB team: 12 hosts
- point-to-point link: 2 addresses

Each one needs a different mask length.

---

## 18. VLSM example with real requirements

Network:
- 192.168.1.0/24

Requirements:
- Sales = 100 hosts
- Marketing = 60 hosts
- Management = 34 hosts

### Step 1: Sort by largest need
Sales needs 100 hosts -> choose /25
- block size = 128
- usable = 126

Use:
- 192.168.1.0/25
- broadcast = 192.168.1.127

Remaining:
- 192.168.1.128 to 192.168.1.255

### Step 2: Marketing needs 60 hosts
Choose /26
- block size = 64
- usable = 62

Use:
- 192.168.1.128/26
- broadcast = 192.168.1.191

Remaining:
- 192.168.1.192 to 192.168.1.255

### Step 3: Management needs 34 hosts
Choose /26 again
- block size = 64
- usable = 62

Use:
- 192.168.1.192/26
- broadcast = 192.168.1.255

### Result
- Sales: 192.168.1.0/25
- Marketing: 192.168.1.128/26
- Management: 192.168.1.192/26

This is efficient and uses all available addresses without waste.

---

## 19. Why VLSM matters in DevOps

### AWS VPC
Suppose a VPC is:
- 10.0.0.0/16

You want:
- public subnet: 128 IPs
- app subnet: 64 IPs
- database subnet: 30 IPs
- management subnet: 16 IPs

This is a perfect VLSM scenario.

Example:
- public: /25
- app: /26
- DB: /27
- management: /28

This avoids wasting addresses across subnets.

### Kubernetes
Cluster CIDR planning often needs:
- control plane subnet
- worker node subnet
- service CIDR
- pod CIDR

Each segment may require different sizes.

### Docker
Docker networks often use ranges like:
- 172.17.0.0/16
- 172.18.0.0/16

Subnetting concepts help you design custom bridge networks and avoid overlapping CIDRs.

### Firewall rules
Example:
- allow 10.0.0.0/24 to access an app
- deny 10.0.0.0/26 for a restricted subnet

CIDR notation is directly used in Linux firewall and cloud security groups.

---

## 20. Practical Linux commands

### Install ipcalc
```bash
sudo apt install ipcalc
```

### Basic subnet checks
```bash
ipcalc 192.168.100.0/24
ipcalc 192.168.100.0/25
ipcalc 192.168.100.0/26
ipcalc 192.168.100.0/27
ipcalc 192.168.100.0/28
ipcalc 192.168.100.0/29
ipcalc 192.168.100.0/30
```

### Solve a real IP question
```bash
ipcalc 192.168.225.212/27
```

### Check interface and routes
```bash
ip a
ip r
```

### Quick Python conversion
```bash
python3 -c "print(bin(224))"
```

Output:
```text
0b11100000
```

### Formula check
```bash
python3 -c "print(2**6 - 2)"
```

Output:
```text
62
```

---

## 21. Example outputs from ipcalc

### Example: /25
```bash
ipcalc 192.168.100.0/25
```

Typical output:
```text
Address:   192.168.100.0
Netmask:   255.255.255.128 = 25
Wildcard:  0.0.0.127
Network:   192.168.100.0/25
HostMin:   192.168.100.1
HostMax:   192.168.100.126
Broadcast: 192.168.100.127
Hosts/Net: 126
```

### Example: /26
```bash
ipcalc 192.168.100.0/26
```

Typical output:
- 4 subnets
- each with 62 usable hosts

---

## 22. Common mistakes and corrections

| Mistake | Why it is wrong | Correct idea |
|---|---|---|
| Saying 3 subnets is always valid | Subnet count must be power of 2 | Choose 4 if needed |
| Forgetting to subtract 2 | Network and broadcast are not usable | Usable hosts = block size - 2 |
| Thinking /30 has no usable addresses | /30 has 2 usable hosts | /30 = 4 total addresses, 2 usable |
| Confusing mask values | 1 bit is not 64 | 1 bit = 128, 2 bits = 192 |
| Using equal subnet sizes when needs differ | Wastes IP range | Use VLSM |
| Mixing up network and broadcast | Off-by-one logic | Broadcast = next network - 1 |

---

## 23. Real-world classroom summary

### Main formula
Number of usable hosts in a subnet:
- 2^(host bits) - 2

### Important subnetting truth
When you borrow host bits:
- more subnets
- fewer hosts per subnet

### Important Class C magic values
- /25 -> 128 -> 2 subnets -> 126 hosts
- /26 -> 192 -> 4 subnets -> 62 hosts
- /27 -> 224 -> 8 subnets -> 30 hosts
- /28 -> 240 -> 16 subnets -> 14 hosts
- /29 -> 248 -> 32 subnets -> 6 hosts
- /30 -> 252 -> 64 subnets -> 2 hosts

### Most important skill
Be able to quickly identify:
- network
- broadcast
- first usable host
- last usable host

without using a calculator.

---

## 24. Interview Questions and Answers

### Q1: What is subnetting?
A: It is the process of dividing one larger network into smaller subnetworks by borrowing bits from the host portion.

### Q2: Why do we need subnetting?
A: To conserve IPv4 addresses, reduce waste, and organize networks logically.

### Q3: What is NAT?
A: NAT translates private IP addresses to public IP addresses so internal devices can access the Internet.

### Q4: What is CIDR?
A: CIDR is Classless Inter-Domain Routing. It uses slash notation like /24, /25, /26, and removes the rigid classful boundaries.

### Q5: What does /24 mean?
A: It means 24 bits are used for the network part, and 8 bits remain for hosts.

### Q6: What is the valid host formula?
A: 2^(host bits) - 2

### Q7: What is the mask for /27?
A: 255.255.255.224

### Q8: How many usable hosts does /29 have?
A: 6 usable hosts

### Q9: What is VLSM?
A: Variable Length Subnet Masking. It allows different subnet sizes in the same address space.

### Q10: Why use VLSM in cloud networking?
A: To avoid wasting IP addresses and match subnet sizes to actual workload needs.

---

## 25. Key Takeaways

- Subnetting is the act of breaking a large network into smaller logical networks
- We do this by borrowing bits from the host part
- /24 has 254 usable addresses
- /25 = 2 subnets, 126 hosts each
- /26 = 4 subnets, 62 hosts each
- /27 = 8 subnets, 30 hosts each
- /30 = 64 subnets, 2 hosts each
- The block size is the value of the last borrowed bit
- A subnet needs a network ID and broadcast ID
- Subnet count must be a power of 2
- VLSM is used when subnet sizes differ
- In DevOps, subnetting is essential for cloud networking, Docker, Kubernetes, and firewall design

---

## 26. Quick Revision Checklist

Can you:
- define subnetting simply?
- explain private vs public IPs?
- explain NAT?
- list private address ranges?
- describe /24, /25, /26, /27, /28, /29, /30?
- calculate network ID and broadcast ID?
- explain the reason for subtracting 2?
- memorize the Class C magic table?
- solve a given IP + mask question?
- explain why VLSM is better than equal subnetting?
- apply subnetting to AWS VPC or Kubernetes design?

---

## 27. Official References

### Networking
- RFC 1918: Private Address Allocation
  https://datatracker.ietf.org/doc/html/rfc1918

### Cisco
- Cisco subnetting documentation
  https://www.cisco.com/c/en/us/support/docs/ip/routing-information-protocol-rip/13788-3.html

### ipcalc
- https://gitlab.com/ipcalc/ipcalc

### Video reference
- https://youtu.be/Ct4PU6CyvTQ?si=Qs3slYbBFUihJnra

---

## 28. Final note for DevOps engineers

Subnetting is not just a theory topic. It is used in:
- cloud networking (AWS, Azure, GCP)
- Kubernetes networking
- Docker bridge design
- firewall and security policies
- on-premise datacenter segmentation
- IP planning for enterprise applications

If you understand subnetting, you can design networks that are efficient, easier to troubleshoot, and more secure.

This is why subnetting is one of the most important topics for Linux and DevOps professionals.

