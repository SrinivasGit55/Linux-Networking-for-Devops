# CCNA Day 7 – Subnetting Practice Questions with Answers

## Overview

This note contains six IPv4 subnetting exercises. Each question is solved in the original order using the magic table, the working octet, and the block-size method.

The goal is to calculate:

- Network ID
- Broadcast ID
- Usable host range
- Total addresses and usable hosts

> **Important:** Subnetting is classless. The CIDR prefix (such as `/9`, `/13`, or `/20`) determines the network boundary. Do not assume that an address beginning with 192 must use a `/24` prefix.

---

## 1. Subnetting method used in these exercises

### Step 1: Identify the working octet

Each IPv4 address contains four octets. A prefix can use some bits from an octet while leaving the remaining bits for hosts.

- `/8`: first octet is fully used.
- `/16`: first two octets are fully used.
- `/24`: first three octets are fully used.
- A prefix between these values means the subnet boundary falls inside the next octet.

### Step 2: Find the block size using the magic table

The bit place values in an octet are:

| Bit position | Place value |
|---|---:|
| 1st | 128 |
| 2nd | 64 |
| 3rd | 32 |
| 4th | 16 |
| 5th | 8 |
| 6th | 4 |
| 7th | 2 |
| 8th | 1 |

The place value of the last network bit in the working octet gives the block size.

### Step 3: Find the network boundary

Network IDs increase by the block size in the working octet. Find the range that contains the given octet value.

### Step 4: Calculate the broadcast address

The broadcast address is one less than the next network ID.

### Step 5: Find the usable host range

For a normal IPv4 subnet:

- First usable host = network ID + 1
- Last usable host = broadcast address - 1

The total number of addresses is (2^{(32-prefix)}). For ordinary subnets, usable hosts are total addresses minus two (network and broadcast addresses).

---

## 2. Question 1 – `20.120.47.225/13`

**Find the network ID and broadcast ID.**

### Step 1: Identify the working octet

- `/8` completes the first octet.
- The remaining 5 bits are in the second octet.
- Therefore, the working octet is the **second octet**.

### Step 2: Find the block size

The fifth bit in the second octet has a place value of **8**.

So, the block size is **8**.

### Step 3: Find the network range

The second-octet network boundaries are:

`0, 8, 16, 24, 32, ... 112, 120, 128, ...`

The given second octet is `120`. It is exactly a network boundary.

- Network ID: `20.120.0.0`
- Next network ID: `20.128.0.0`

### Step 4: Calculate the broadcast address

One less than the next network ID:

- Broadcast ID: `20.127.255.255`

### Answer

| Item | Result |
|---|---|
| Network ID | `20.120.0.0` |
| First usable host | `20.120.0.1` |
| Last usable host | `20.127.255.254` |
| Broadcast ID | `20.127.255.255` |
| Total addresses | 524,288 |
| Usable hosts | 524,286 |

**Shortcut:** Since the block size is 8 and the second octet is 120, the address belongs to the 120–127 range.

---

## 3. Question 2 – `220.20.17.5/27`

**Find the network ID and broadcast ID.**

### Step 1: Identify the working octet

- `/24` uses the first three octets.
- The remaining 3 bits are in the fourth octet.
- Therefore, the working octet is the **fourth octet**.

### Step 2: Find the block size

The third bit in the fourth octet has a place value of **32**.

So, the block size is **32**.

### Step 3: Find the network range

The fourth-octet boundaries are:

`0, 32, 64, 96, 128, 160, 192, 224`

The given fourth octet is `5`, which belongs to the range `0–31`.

- Network ID: `220.20.17.0`
- Next network ID: `220.20.17.32`

### Step 4: Calculate the broadcast address

One less than `220.20.17.32`:

- Broadcast ID: `220.20.17.31`

### Answer

| Item | Result |
|---|---|
| Network ID | `220.20.17.0` |
| First usable host | `220.20.17.1` |
| Last usable host | `220.20.17.30` |
| Broadcast ID | `220.20.17.31` |
| Total addresses | 32 |
| Usable hosts | 30 |

---

## 4. Question 3 – `10.10.17.17/19`

**Find the network ID and broadcast ID.**

### Step 1: Identify the working octet

- `/16` uses the first two octets.
- The remaining 3 bits are in the third octet.
- Therefore, the working octet is the **third octet**.

### Step 2: Find the block size

The third bit in the third octet has a place value of **32**.

So, the block size is **32**.

### Step 3: Find the network range

The third-octet boundaries are:

`0, 32, 64, 96, 128, 160, 192, 224`

The given third octet is `17`, which belongs to the range `0–31`.

- Network ID: `10.10.0.0`
- Next network ID: `10.10.32.0`

### Step 4: Calculate the broadcast address

One less than `10.10.32.0`:

- Broadcast ID: `10.10.31.255`

### Answer

| Item | Result |
|---|---|
| Network ID | `10.10.0.0` |
| First usable host | `10.10.0.1` |
| Last usable host | `10.10.31.254` |
| Broadcast ID | `10.10.31.255` |
| Total addresses | 8,192 |
| Usable hosts | 8,190 |

---

## 5. Question 4 – `192.8.31.1/9`

**Find the network ID and broadcast ID.**

This question demonstrates an important point about classless addressing: the prefix is not restricted by the traditional class of the first octet. A `192.x.x.x` address can use `/9`.

### Step 1: Identify the working octet

- `/8` uses the first octet.
- The remaining 1 bit is in the second octet.
- Therefore, the working octet is the **second octet**.

### Step 2: Find the block size

The first bit in the second octet has a place value of **128**.

So, the block size is **128**.

### Step 3: Find the network range

The second-octet ranges are:

- `0–127`
- `128–255`

The given second octet is `8`, so it belongs to the `0–127` range.

- Network ID: `192.0.0.0`
- Next network ID: `192.128.0.0`

### Step 4: Calculate the broadcast address

One less than `192.128.0.0`:

- Broadcast ID: `192.127.255.255`

### Answer

| Item | Result |
|---|---|
| Network ID | `192.0.0.0` |
| First usable host | `192.0.0.1` |
| Last usable host | `192.127.255.254` |
| Broadcast ID | `192.127.255.255` |
| Total addresses | 8,388,608 |
| Usable hosts | 8,388,606 |

**Calculation note:** The working octet for `/9` is the second octet, not the third octet. A block size of 64 in the third octet would not match a `/9` prefix.

---

## 6. Question 5 – `172.1.4.5/20`

**Find the network ID and broadcast ID.**

### Step 1: Identify the working octet

- `/16` uses the first two octets.
- The remaining 4 bits are in the third octet.
- Therefore, the working octet is the **third octet**.

### Step 2: Find the block size

The fourth bit in the third octet has a place value of **16**.

So, the block size is **16**.

### Step 3: Find the network range

The third-octet boundaries are:

`0, 16, 32, 48, 64, 80, ...`

The given third octet is `4`, which belongs to the range `0–15`.

- Network ID: `172.1.0.0`
- Next network ID: `172.1.16.0`

### Step 4: Calculate the broadcast address

One less than `172.1.16.0`:

- Broadcast ID: `172.1.15.255`

### Answer

| Item | Result |
|---|---|
| Network ID | `172.1.0.0` |
| First usable host | `172.1.0.1` |
| Last usable host | `172.1.15.254` |
| Broadcast ID | `172.1.15.255` |
| Total addresses | 4,096 |
| Usable hosts | 4,094 |

**Calculation note:** The next network ID is `172.1.16.0`, not `172.16.0.0`. The block size of 16 is added to the third octet.

---

## 7. Question 6 – `172.138.75.5/12`

**Find the network ID and broadcast ID.**

This question demonstrates the shortcut for locating a network range without listing every network boundary from zero.

### Step 1: Identify the working octet

- `/8` uses the first octet.
- The remaining 4 bits are in the second octet.
- Therefore, the working octet is the **second octet**.

### Step 2: Find the block size

The fourth bit in the second octet has a place value of **16**.

So, the block size is **16**.

### Step 3: Find the network range

The second-octet boundaries include:

`0, 16, 32, 48, 64, 80, 96, 112, 128, 144, ...`

The given second octet is `138`.

- `138` is greater than `128`.
- The next boundary is `144`.
- Therefore, `138` belongs to the range `128–143`.

Network ID: `172.128.0.0`

Next network ID: `172.144.0.0`

### Step 4: Calculate the broadcast address

One less than `172.144.0.0`:

- Broadcast ID: `172.143.255.255`

### Answer

| Item | Result |
|---|---|
| Network ID | `172.128.0.0` |
| First usable host | `172.128.0.1` |
| Last usable host | `172.143.255.254` |
| Broadcast ID | `172.143.255.255` |
| Total addresses | 1,048,576 |
| Usable hosts | 1,048,574 |

**Shortcut:** Because the block size is 16, use 128 as a convenient reference boundary. Since 138 falls between 128 and 144, the subnet range is 128–143.

---

## 8. Quick revision table

| Question | IP address / Prefix | Working octet | Block size | Network ID | Broadcast ID |
|---|---|---|---:|---|---|
| 1 | `20.120.47.225/13` | 2nd | 8 | `20.120.0.0` | `20.127.255.255` |
| 2 | `220.20.17.5/27` | 4th | 32 | `220.20.17.0` | `220.20.17.31` |
| 3 | `10.10.17.17/19` | 3rd | 32 | `10.10.0.0` | `10.10.31.255` |
| 4 | `192.8.31.1/9` | 2nd | 128 | `192.0.0.0` | `192.127.255.255` |
| 5 | `172.1.4.5/20` | 3rd | 16 | `172.1.0.0` | `172.1.15.255` |
| 6 | `172.138.75.5/12` | 2nd | 16 | `172.128.0.0` | `172.143.255.255` |

## 9. Key learning points

1. Always identify the working octet from the CIDR prefix first.
2. Use the magic table to find the place value of the last network bit.
3. That place value gives the block size for the working octet.
4. The network ID is the beginning of the block containing the given IP.
5. The broadcast ID is one less than the next network ID.
6. Subnetting is classless: use the CIDR prefix, not assumptions based on the first octet.
7. The shortcut method becomes faster with practice, but listing the boundaries is a useful way to understand the calculation.

## 10. Practical networking connection

Subnet calculations are useful when planning IP ranges for cloud and DevOps environments. For example, when creating subnets in an AWS VPC or Azure virtual network, the CIDR range determines which IP addresses belong to each subnet. Correct network and broadcast boundaries help avoid overlapping address ranges and support clear network segmentation.

For additional practice on Linux, the `ipcalc` utility can be used to check subnet calculations after solving them manually.
