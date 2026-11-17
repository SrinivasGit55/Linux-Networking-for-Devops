# CCNA Day 03 – Subnetting and Supernetting

## 1. Understanding Subnetting Through a Digital Clock

Subnetting calculations become easier when we understand how values carry over between positions.

Consider a digital clock:

```text
13:45:59 + 1 second = 13:46:00

13:59:59 + 1 second = 14:00:00
```

The same principle applies to IPv4 addresses. When an octet reaches its boundary, the previous octet changes.

For example:

```text
172.16.64.0 - 1

= 172.16.63.255
```

This concept is useful when calculating network ranges, broadcast addresses, and subnet boundaries.

## 2. Dividing a Class B Network into 2 Subnets

Original network:

```text
Network:       172.16.0.0/16
Subnet Mask:   255.255.0.0
```

We need to divide this network into two equal subnets.

### Step 1: Calculate the borrowed bits

Formula:

```text
2^n = Number of subnets

2^1 = 2
```

We borrow one bit from the host portion.

### Step 2: Calculate the new prefix

```text
Original prefix: /16
Borrowed bits:   1
New prefix:      /17
```

New subnet mask:

```text
255.255.128.0
```

### Step 3: Calculate the host count

Remaining host bits:

```text
32 - 17 = 15
```

Traditional usable host calculation:

```text
2^15 - 2 = 32,766
```

### Step 4: Identify the subnet ranges

| Subnet | Network address | Broadcast address |
| ------ | --------------- | ----------------- |
| 1      | 172.16.0.0/17   | 172.16.127.255    |
| 2      | 172.16.128.0/17 | 172.16.255.255    |

Each subnet contains 32,768 total addresses and 32,766 traditionally usable host addresses.

## 3. Dividing a Class B Network into 4 Subnets

Original network:

```text
172.16.0.0/16
```

We need four equal subnets.

### Step 1: Borrow bits

```text
2^2 = 4
```

We borrow two bits.

### Step 2: New prefix and mask

```text
Original prefix: /16
Borrowed bits:   2
New prefix:      /18

Subnet Mask:     255.255.192.0
```

### Step 3: Calculate host addresses

Remaining host bits:

```text
32 - 18 = 14
```

```text
2^14 - 2 = 16,382
```

### Step 4: Calculate subnet ranges

The interesting octet is the third octet.

```text
Subnet mask: 255.255.192.0
Increment:   256 - 192 = 64
```

| Subnet | Network address | Broadcast address |
| ------ | --------------- | ----------------- |
| 1      | 172.16.0.0/18   | 172.16.63.255     |
| 2      | 172.16.64.0/18  | 172.16.127.255    |
| 3      | 172.16.128.0/18 | 172.16.191.255    |
| 4      | 172.16.192.0/18 | 172.16.255.255    |

Each subnet has 16,384 total addresses and 16,382 traditionally usable host addresses.

## 4. Dividing a Class A Network into 2 Subnets

Original network:

```text
Network:      10.0.0.0/8
Subnet Mask:  255.0.0.0
```

We need two equal subnets.

Borrow one bit:

```text
2^1 = 2
```

New prefix:

```text
/8 + 1 = /9
```

New subnet mask:

```text
255.128.0.0
```

Remaining host bits:

```text
32 - 9 = 23
```

Usable hosts:

```text
2^23 - 2 = 8,388,606
```

Subnet ranges:

| Subnet | Network address | Broadcast address |
| ------ | --------------- | ----------------- |
| 1      | 10.0.0.0/9      | 10.127.255.255    |
| 2      | 10.128.0.0/9    | 10.255.255.255    |

## 5. Dividing a Class A Network into 4 Subnets

Original network:

```text
10.0.0.0/8
```

Borrow two bits:

```text
2^2 = 4
```

New prefix:

```text
/10
```

Subnet mask:

```text
255.192.0.0
```

Remaining host bits:

```text
32 - 10 = 22
```

Usable hosts:

```text
2^22 - 2 = 4,194,302
```

The increment is:

```text
256 - 192 = 64
```

Subnet ranges:

| Subnet | Network address | Broadcast address |
| ------ | --------------- | ----------------- |
| 1      | 10.0.0.0/10     | 10.63.255.255     |
| 2      | 10.64.0.0/10    | 10.127.255.255    |
| 3      | 10.128.0.0/10   | 10.191.255.255    |
| 4      | 10.192.0.0/10   | 10.255.255.255    |

## 6. Magic Table and Subnet Mask Calculation

A magic table helps us understand the binary values of an IPv4 octet.

| Bit position | Decimal value |
| ------------ | ------------: |
| 1            |           128 |
| 2            |            64 |
| 3            |            32 |
| 4            |            16 |
| 5            |             8 |
| 6            |             4 |
| 7            |             2 |
| 8            |             1 |

### Valid subnet mask octet values

```text
0
128
192
224
240
248
252
254
255
```

These values represent consecutive binary ones followed by zeros.

For example:

```text
192 = 11000000
224 = 11100000
240 = 11110000
```

### Invalid subnet mask example

```text
197 = 11000101
```

This is invalid because the binary ones are not continuous.

### Magic number / increment formula

```text
Increment = 256 - Interesting mask octet
```

Example:

```text
Mask:      255.255.192.0
Increment: 256 - 192 = 64
```

This tells us that the third octet increases in steps of 64.

## 7. Practice Problem: 172.10.21.21/24

Given:

```text
IP Address: 172.10.21.21/24
```

Subnet mask:

```text
255.255.255.0
```

Network address:

```text
172.10.21.0
```

Broadcast address:

```text
172.10.21.255
```

Usable host range:

```text
172.10.21.1 - 172.10.21.254
```

### Additional clarification – Private IP addressing

Although this address starts with `172`, it is not within the RFC 1918 private range.

The private IPv4 ranges are:

| Private range                 | CIDR           |
| ----------------------------- | -------------- |
| 10.0.0.0 – 10.255.255.255     | 10.0.0.0/8     |
| 172.16.0.0 – 172.31.255.255   | 172.16.0.0/12  |
| 192.168.0.0 – 192.168.255.255 | 192.168.0.0/16 |

Therefore, `172.10.21.21` is not an RFC 1918 private address.

## 8. Practice Problem: 10.210.11.170/23

Given:

```text
IP Address: 10.210.11.170/23
```

Subnet mask:

```text
255.255.254.0
```

Interesting octet:

```text
Third octet = 254
```

Calculate increment:

```text
256 - 254 = 2
```

The third octet increases in steps of two:

```text
10.210.8.0
10.210.10.0
10.210.12.0
10.210.14.0
```

The IP address `10.210.11.170` belongs to the network starting at `10.210.10.0`.

Final result:

```text
Network Address:   10.210.10.0
Broadcast Address: 10.210.11.255

First Usable IP:   10.210.10.1
Last Usable IP:    10.210.11.254
```

## 9. Introduction to Supernetting

Subnetting divides a larger network into smaller networks.

Supernetting combines multiple smaller, contiguous networks into a larger address block.

It is also called:

- Route aggregation
- Route summarization
- CIDR aggregation

### Why is supernetting useful?

Imagine a router managing many individual network routes.

Instead of advertising every route separately, a router may advertise a single summarized route.

This can reduce routing table entries and simplify routing information exchange.

However, the networks must satisfy the required address alignment and aggregation conditions.

## 10. Combining Four /26 Networks into One /24

Consider these four networks:

```text
192.168.100.0/26
192.168.100.64/26
192.168.100.128/26
192.168.100.192/26
```

Each `/26` contains 64 total IPv4 addresses.

Four networks:

```text
4 x 64 = 256
```

Together, they cover the complete range:

```text
192.168.100.0 - 192.168.100.255
```

The common summary network is:

```text
192.168.100.0/24
```

Subnet mask:

```text
255.255.255.0
```

This is an exact summary of the four listed networks.

## 11. Understanding Supernetting Through Binary

Consider the following assumed networks:

```text
172.168.197.0/24
172.168.198.0/24
172.168.199.0/24
172.168.200.0/24
172.168.201.0/24
172.168.202.0/24
```

A possible covering aggregate is:

```text
172.168.192.0/20
```

Subnet mask:

```text
255.255.240.0
```

### Why /20?

The third octet of the aggregate is:

```text
192
```

Its binary representation is:

```text
11000000
```

The `/20` prefix uses the first four bits of the third octet as network bits.

The increment is:

```text
256 - 240 = 16
```

Therefore, the aggregate covers third-octet values:

```text
192 - 207
```

This includes all the assumed networks from 197 through 202.

### Important clarification

This `/20` aggregate covers 16 possible `/24` networks, not only the six listed networks.

It also includes:

```text
172.168.192.0/24
172.168.193.0/24
172.168.194.0/24
172.168.195.0/24
172.168.196.0/24
172.168.203.0/24
172.168.204.0/24
172.168.205.0/24
172.168.206.0/24
172.168.207.0/24
```

Therefore, this is a covering aggregate rather than an exact summary.

When a router advertises an aggregate, it must handle traffic for destinations inside the aggregate that do not have a more-specific route. A discard or null route is commonly used to prevent routing loops.

Also remember: `172.168.x.x` is not part of the RFC 1918 private address range.

## 12. Important Modern Networking Clarifications

### Classful addressing and CIDR

Class A, Class B, and Class C are historical IPv4 addressing classes.

They remain useful for learning traditional subnetting concepts, but modern networks use CIDR (Classless Inter-Domain Routing).

CIDR identifies the network portion using an explicit prefix length.

For example:

```text
10.0.0.0/8
10.0.0.0/16
10.0.0.0/24
```

These represent different network sizes.

### Subnet count

The modern subnet count formula is:

```text
2^n
```

Where `n` is the number of borrowed bits.

Modern subnetting allows the all-zero and all-ones subnets. Do not subtract two from the subnet count.

### Host count

The traditional formula is:

```text
2^h - 2
```

Where `h` is the number of host bits.

However, there are two important exceptions:

| Prefix | Meaning                                                         |
| ------ | --------------------------------------------------------------- |
| /31    | Can provide two endpoint addresses on point-to-point links      |
| /32    | Represents a single IPv4 address, commonly used as a host route |

### Broadcast address

For conventional IPv4 subnets, the broadcast address is calculated by setting all host bits to one.

An IP address containing `255` in an octet is not automatically a broadcast address. The prefix determines the network and broadcast boundaries.

### Longest Prefix Match

When a router has multiple matching routes for a destination, it generally selects the route with the longest matching prefix.

For example:

```text
10.0.0.0/8
10.10.0.0/16
10.10.10.0/24
```

Traffic destined for `10.10.10.25` matches all three routes, but `/24` is the most specific match.

## 13. Practical Relevance to DevOps and Cloud Networking

Subnetting and supernetting are important when designing and managing cloud infrastructure.

Some practical applications include:

- **AWS VPC:** Planning CIDR blocks and dividing a VPC into public and private subnets.
- **Azure VNet:** Allocating address spaces and creating application, database, and management subnets.
- **Routing tables:** Understanding how traffic moves between networks.
- **VPN connectivity:** Avoiding overlapping IP address ranges between connected networks.
- **Kubernetes:** Understanding Pod CIDR and Service CIDR ranges.
- **Network security:** Separating workloads and controlling communication between network segments.

Understanding IPv4 addressing is a foundation for working with cloud networking, routing, firewalls, gateways, and infrastructure automation.

## 14. Practice and Revision Questions

1. What is subnetting?
2. What is supernetting?
3. How many subnets can be created by borrowing 3 bits?
4. What is the subnet mask of `/18`?
5. How many host bits are available in a `/26` network?
6. What is the increment for the subnet mask `255.255.240.0`?
7. Find the network and broadcast addresses of `192.168.10.50/27`.
8. Find the network and broadcast addresses of `10.10.15.200/23`.
9. Why is `172.10.21.21` not a private IPv4 address?
10. What is the difference between subnetting and supernetting?
11. What is CIDR?
12. Why is `/31` treated differently from traditional IPv4 subnets?
13. What is a `/32` route?
14. What is Longest Prefix Match?
15. Why is route summarization useful in large networks?

---

## Key Takeaways

- Subnetting divides a network into smaller networks.
- Supernetting combines contiguous network blocks into a larger aggregate.
- The number of subnets is calculated using `2^n`.
- Traditional usable host count is calculated using `2^h - 2`, with `/31` and `/32` exceptions.
- The magic number method helps calculate subnet increments.
- CIDR is the modern approach to IPv4 network addressing.
- Route summarization reduces routing information, while Longest Prefix Match helps routers select the most specific route.
- These concepts are essential for understanding AWS VPC, Azure VNet, routing, VPNs, and Kubernetes networking.
