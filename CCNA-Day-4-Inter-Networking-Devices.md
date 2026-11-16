# CCNA Day 4 – Internetworking Devices and Data Transmission

**Video:** [200-301 CCNA v3.0 | Day 4: Inter-Networking Devices](https://www.youtube.com/watch/dd_fKRA7lBA)  
**Topics:** Hubs, bridges, switches, routers, collision and broadcast domains, ARP, NDP, and packet forwarding.

---

## 1. Overview

This lesson introduces three common network devices:

1. Hub
2. Switch
3. Router

It also explains how data travels:

- Between devices on the same IP network.
- Between devices on different IP networks.

To understand the examples, keep these address types separate:

| Address | OSI layer | Purpose |
|---|---:|---|
| IP address | Layer 3 – Network | Identifies the source and destination across networks |
| MAC address | Layer 2 – Data Link | Delivers an Ethernet frame on a local link |

---

## 2. Hub

A hub is a basic, non-intelligent Layer 1 device. It repeats incoming signals out through its other ports.

### How a hub behaves

- It does not learn or use MAC addresses to select an outgoing port.
- When it receives data on one port, it repeats the signal to the other ports.
- All connected devices share the same collision domain.
- A hub does not separate broadcast traffic; connected devices share one broadcast domain.
- Hub links typically use half-duplex Ethernet.

### Collision domain

A collision domain is a network segment where simultaneous transmissions can collide. Hubs are typically associated with half-duplex Ethernet, where collisions can occur.

> In modern switched, full-duplex Ethernet, collisions are generally not expected on the link.

### Summary

- Repeats signals to other ports.
- Does not make forwarding decisions using a MAC address table.
- One shared collision domain.
- One shared broadcast domain.

---

## 3. Bridge

A bridge connects and filters traffic between network segments. It can use MAC addresses to make forwarding decisions, unlike a hub.

Bridges are important historically and conceptually, but modern Ethernet LANs commonly use switches, which perform similar functions with more ports and hardware support.

---

## 4. Switch

A Layer 2 switch forwards Ethernet frames using destination MAC addresses.

### MAC address table

A switch builds a MAC address table, also known as a CAM table. It learns dynamically by examining the **source MAC address** of incoming frames and recording the port on which each source was seen.

The table is learned dynamically as traffic arrives; it is not necessarily populated in a fixed number of seconds after the switch starts.

### Switch MAC address operations

A switch uses three basic operations:

1. **Learning** — Reads the source MAC address of an incoming frame and associates it with the port where the frame arrived.
2. **Forwarding** — Sends a frame with a known destination MAC address out the associated port.
3. **Flooding** — Sends a frame with an unknown destination MAC address out eligible ports in the same VLAN, except the incoming port. Broadcast frames are also flooded within their VLAN.

### Unknown unicast

An unknown unicast is a frame addressed to a specific MAC address that is not currently in the switch’s MAC address table. The switch floods it within the VLAN, except through the port where it arrived. If the destination host receives the frame, its reply can help the switch learn the host’s source MAC address and port.

### Forwarding behavior

- **Known destination MAC:** Forward the frame out the port associated with that MAC address.
- **Unknown destination MAC:** Flood the frame out the other eligible ports in the same VLAN.
- **Broadcast frame:** Flood it within the same VLAN, except back out the port it arrived on.

### Collision and broadcast domains

- Each switch port is normally its own collision domain.
- Without VLANs, a Layer 2 switch’s ports generally belong to one broadcast domain.
- Each VLAN is a separate broadcast domain.

### Duplex and collisions

- **Hub links** typically use half-duplex Ethernet, where devices share the medium and collisions can occur.
- **Switch links** typically use full-duplex Ethernet, allowing simultaneous transmission and reception.
- Collisions are not normally expected on full-duplex Ethernet links.

### Summary

A switch:

- Learns source MAC addresses and associates them with ports.
- Uses the destination MAC address to forward known unicast frames.
- Floods unknown unicast and broadcast frames within the VLAN as appropriate.
- Separates collision domains per port.
- Does not, by itself, route traffic between IP networks.

---

## 5. Router

A router forwards packets between IP networks. It makes forwarding decisions using the Layer 3 destination IP address and its routing table.

### Router interfaces and networks

A router interface connects to a network. In a basic routed design, different router interfaces connect to different IP networks.

Routers separate broadcast domains: a Layer 2 broadcast received on one interface is not normally forwarded as a Layer 2 broadcast through another interface.

A router can have multiple interfaces, with each interface connected to a different network.

### Summary

A router:

- Examines the destination IP address.
- Uses a routing table to choose a next hop or outgoing interface.
- Separates broadcast domains.
- Rebuilds the Layer 2 frame for each outgoing link.

---

## 6. Comparing the Devices

| Device | Main function | Uses MAC addresses to forward? | Uses IP routes? | Collision domains | Broadcast domains |
|---|---|---:|---:|---|---|
| Hub | Repeats signals | No | No | One shared domain; typically half-duplex | One shared domain |
| Bridge | Connects and filters Layer 2 segments | Yes | No | Separates segments | Normally one per bridged LAN |
| Switch | Forwards Ethernet frames within a LAN/VLAN | Yes | No, unless it is a multilayer switch | One per port; links typically use full-duplex | One per VLAN |
| Router | Forwards packets between IP networks | Uses Layer 2 on each link | Yes | Each interface/link is separate | Separate per routed interface/network |

> Collision-domain descriptions are useful for understanding older shared-medium and half-duplex Ethernet. Modern full-duplex switched links do not normally experience Ethernet collisions.

---

## 7. ARP: Finding a Local MAC Address

ARP stands for **Address Resolution Protocol**. On an IPv4 Ethernet network, ARP is used to discover the MAC address associated with a local IPv4 address.

For IPv6 over Ethernet, **Neighbor Discovery Protocol (NDP)** performs address resolution and related neighbor-discovery functions using ICMPv6. IPv6 does not use ARP.

### Example: Two hosts on the same subnet

Assume:

- Host A: `10.1.1.10/24`
- Host B: `10.1.1.11/24`

Host A wants to send data to Host B.

1. Host A checks whether `10.1.1.11` is in its local subnet.
2. Since both addresses are in `10.1.1.0/24`, Host A will send directly to Host B.
3. If Host A does not already know Host B’s MAC address, it sends an ARP request.
4. The ARP request is a Layer 2 broadcast within the VLAN.
5. The switch floods the request within that VLAN.
6. Host B recognizes its IP address and sends an ARP reply containing its MAC address.
7. Host A can now send an Ethernet frame addressed to Host B’s MAC address.

An ARP request is similar to calling out a name in a room: everyone in the local broadcast domain receives the request, but the host with the requested IP address replies.

### ARP cache

Hosts keep recently learned IPv4-to-MAC mappings in an ARP cache. This helps avoid sending a new ARP request for every packet.

### IPv4 versus IPv6 address resolution

| IP version | Address-resolution method |
|---|---|
| IPv4 | ARP |
| IPv6 | Neighbor Discovery Protocol (NDP), using ICMPv6 |

---

## 8. Sending Data Within the Same Network

Using the example above:

- Source IP: `10.1.1.10`
- Destination IP: `10.1.1.11`
- Source MAC: Host A’s MAC address
- Destination MAC: Host B’s MAC address

The sender creates an IP packet and places it inside an Ethernet frame. The switch uses the destination MAC address to forward the frame to the appropriate port when it knows that MAC-to-port mapping.

The destination host receives the frame, processes the Layer 2 header, and passes the IP packet up the protocol stack.

### Key point

For communication within the same subnet, the Ethernet frame is addressed to the destination host’s MAC address.

---

## 9. Sending Data to Another Network

Assume:

- Host A: `10.1.1.10/24`
- Remote destination: `30.1.1.1`
- Host A’s default gateway: an address on `10.1.1.0/24`, such as `10.1.1.1`

Host A compares the destination IP address with its own address and subnet mask. Since `30.1.1.1` is outside `10.1.1.0/24`, Host A sends the packet to its default gateway.

### Important: The gateway must be on the local subnet

The transcript gives `10.1.1.255` as the default gateway. With a `/24` mask, `10.1.1.255` is normally the subnet’s broadcast address, so it is not a suitable ordinary host gateway address.

Use the actual configured router-interface address in the subnet—for example, `10.1.1.1`—rather than assuming a gateway value.

### Forwarding steps

1. Host A determines that the destination IP is on another network.
2. Host A selects its configured default gateway as the next hop.
3. If needed, Host A uses ARP to learn the gateway interface’s MAC address.
4. Host A sends an Ethernet frame to the gateway’s MAC address.
5. The router removes the incoming Layer 2 header and examines the destination IP address.
6. The router checks its routing table and selects an outgoing interface or next hop.
7. The router builds a new Layer 2 frame for the next link.
8. The next router repeats the routing and frame-rebuilding process as needed.
9. On the destination network, the final router resolves the destination host’s MAC address if necessary and delivers the frame.

### What changes at each routed hop?

| Information | Typical behavior when routing |
|---|---|
| Source and destination IP addresses | Remain the same from source to destination, unless a function such as NAT changes them |
| Ethernet source and destination MAC addresses | Replaced for each Layer 2 link |
| TTL / Hop Limit | Decremented by each router |

The Layer 2 frame is local to a link. Routers remove and rebuild that frame as the packet crosses networks.

---

## 10. Default Gateway

A **default gateway** is the router interface a host uses when the destination is outside the host’s local IP network.

For example, a host with `10.1.1.10/24` might use `10.1.1.1` as its default gateway if that address belongs to the router interface on the same subnet.

The host does not ARP for the remote destination’s MAC address. It ARPs for the MAC address of the local next hop—the gateway.

---

## 11. Key Concepts to Remember

1. A hub repeats signals and does not make MAC-based forwarding decisions.
2. A switch learns source MAC addresses and forwards frames based on destination MAC addresses.
3. A switch floods broadcasts within a VLAN.
4. A switch floods unknown unicast frames within the same VLAN, except through the incoming port.
5. Each VLAN is a separate Layer 2 broadcast domain.
6. A router forwards packets between IP networks using a routing table.
7. A router does not normally forward Layer 2 broadcasts between interfaces.
8. ARP resolves a local IPv4 address to a MAC address on an Ethernet network.
9. IPv6 uses NDP, not ARP, for neighbor discovery and address resolution.
10. For a remote destination, a host sends the frame to its default gateway’s MAC address.
11. The IP packet usually keeps its source and destination IP addresses across routed hops.
12. The Layer 2 frame and MAC addresses are rebuilt at each routed hop.
13. With a `/24` subnet mask, the network address ends in `.0` and the broadcast address normally ends in `.255`; `.255` is not ordinarily a host gateway address.
14. Full-duplex Ethernet links do not normally experience collisions.

---

## 12. Quick Review Questions

### 1. What does a hub do with incoming data?

It repeats the signal out through its other ports rather than selecting a destination using a MAC address table.

### 2. How does a switch learn a MAC address?

It reads the source MAC address of an incoming frame and records the associated port.

### 3. What are the three basic switch MAC address operations?

Learning, forwarding, and flooding.

### 4. What does a switch do with a broadcast frame?

It floods the frame within the same VLAN, except out of the port where it arrived.

### 5. What does a switch do with an unknown unicast frame?

It floods the frame within the same VLAN, except out of the port where the frame arrived.

### 6. What is a broadcast domain?

A Layer 2 area in which a broadcast frame is propagated. Routers normally separate broadcast domains.

### 7. What does ARP do?

For IPv4 over Ethernet, ARP discovers the MAC address associated with a local IPv4 address.

### 8. Does IPv6 use ARP?

No. IPv6 uses Neighbor Discovery Protocol (NDP), which operates through ICMPv6, for address resolution and other neighbor-discovery functions.

### 9. What does a host do when the destination is outside its subnet?

It sends the packet to its default gateway, using the gateway’s MAC address for the local Ethernet frame.

### 10. Do the source and destination MAC addresses stay the same from end to end?

No. They apply to a Layer 2 link and are replaced as the packet is forwarded through routers.

### 11. Does a router forward ordinary Layer 2 broadcasts between interfaces?

Normally, no.

### 12. How many broadcast domains does a switch have?

Without VLANs, the switch’s ports generally belong to one broadcast domain. Each VLAN creates a separate broadcast domain.

### 13. What is the difference between half-duplex and full-duplex Ethernet?

Half-duplex devices share a medium and cannot transmit and receive simultaneously. Full-duplex links allow simultaneous transmission and reception; collisions are not normally expected on them.

### 14. Why is `10.1.1.255` not a typical gateway for a `/24` network?

It is normally the broadcast address for `10.1.1.0/24`, not an ordinary host address for a router interface.

---

## 13. Simple Forwarding Diagrams

### Same subnet

```text
Host A                         Switch                         Host B
10.1.1.10  -- Ethernet frame -->  |  -- Ethernet frame ------> 10.1.1.11
              destination MAC: Host B
```

### Different subnet

```text
Host A                 Router 1                 Router 2          Remote Host
10.1.1.10 -- frame --> gateway -- routed packet --> next hop --> destination
             MACs for       New Layer 2 frame
             local link     for each link
```

---

## 14. Sources and Additional Documentation

### Video source

- [CCNA Day 4: Inter-Networking Devices – NetworKing](https://www.youtube.com/watch/dd_fKRA7lBA)

### Cisco and Cisco Press references

- [The MAC Address Table – Cisco Press](https://www.ciscopress.com/articles/article.asp?p=3089352&seqNum=6) — Switch MAC address learning and forwarding.
- [Network Tables: MAC, Routing, ARP – Cisco Community](https://community.cisco.com/t5/networking-knowledge-base/network-tables-mac-routing-arp/ta-p/4184148) — MAC, routing, and ARP tables and how they relate.
- [Cisco IOS XE IP Addressing Services Configuration Guide](https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst9300/software/release/17-15/configuration_guide/ip_addr/b_1715_ip-address-c9300.html) — Cisco platform documentation that includes IPv6 client address learning and neighbor-discovery topics.

### Internet standards references

- [RFC 826: An Ethernet Address Resolution Protocol](https://www.rfc-editor.org/rfc/rfc826) — IPv4 address resolution for Ethernet.
- [RFC 4861: Neighbor Discovery for IP version 6 (IPv6)](https://www.rfc-editor.org/rfc/rfc4861) — IPv6 Neighbor Discovery, including address resolution.
- [RFC 1812: Requirements for IP Version 4 Routers](https://www.rfc-editor.org/rfc/rfc1812) — IPv4 router behavior and packet forwarding.

---

## 15. Final Takeaways

- A hub is a basic repeater and does not forward intelligently.
- A switch uses MAC learning, forwarding, and flooding.
- A router forwards based on IP routing decisions and changes the Layer 2 encapsulation on each link.
- IPv4 uses ARP; IPv6 uses NDP.
- A default gateway must be a valid address on the local subnet.
- Full-duplex switched links do not usually experience collisions.
- Understanding the MAC/IP behavior at each hop is essential for CCNA routing and switching concepts.
