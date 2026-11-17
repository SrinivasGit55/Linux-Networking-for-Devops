# CCNA Day 6 -- DHCP, TCP Transmission, Port Numbers, and Network Data Flow

## Overview

This document covers three important networking topics:

1.  Dynamic Host Configuration Protocol (DHCP)
2.  Transmission Control Protocol (TCP) and TCP windowing
3.  Common TCP and UDP port numbers

It then combines these concepts in an end-to-end example showing how a
client communicates with a remote FTP server.

The goal is to understand how a device obtains network configuration,
establishes a TCP connection, identifies the destination application,
and sends data across multiple networks.

------------------------------------------------------------------------

## 1. DHCP -- Dynamic Host Configuration Protocol

### 1.1 What is DHCP?

DHCP stands for **Dynamic Host Configuration Protocol**. It
automatically provides network configuration to devices when they
connect to a network.

Without DHCP, an administrator would need to configure IP addresses and
other network settings manually on individual devices.

DHCP can provide:

-   IP address
-   Subnet mask
-   Default gateway
-   DNS server addresses
-   Other network configuration parameters

**Example:** When a laptop connects to a Wi-Fi network and receives an
IP address automatically, DHCP is commonly responsible for providing
that configuration.

### 1.2 DHCP communication process -- DORA

The common initial DHCP address-allocation process uses four messages:

-   **Discover**
-   **Offer**
-   **Request**
-   **Acknowledgement**

**Step 1 -- DHCP Discover**

When a device joins a network without an IPv4 address, it sends a DHCP
Discover message to locate available DHCP servers. The initial Discover
is normally broadcast because the client does not yet know the DHCP
server's address.

**Step 2 -- DHCP Offer**

The DHCP server responds with an offer containing proposed network
configuration.

``` text
Offered IP Address: 192.168.1.2
DHCP Server:        192.168.1.1
```

The server makes the offered address temporarily unavailable to other
clients while the offer is being handled, according to its allocation
process.

**Step 3 -- DHCP Request**

The client selects an offer and sends a DHCP Request indicating which
server and offered configuration it wants to use.

**Step 4 -- DHCP ACK**

The selected DHCP server sends a DHCP Acknowledgement confirming the
configuration. The client can then configure and use the assigned
address.

### DHCP DORA flow

``` text
Client                         DHCP Server
  |                                 |
  | -------- Discover ------------> |
  |                                 |
  | <--------- Offer -------------- |
  |                                 |
  | -------- Request -------------> |
  |                                 |
  | <---------- ACK --------------- |
  |                                 |
  |       IP configuration          |
  |          completed              |
```

### 1.3 Multiple DHCP servers

A network can have multiple DHCP servers. For example:

``` text
DHCP Server 1:
IP Address: 192.168.1.1
Offer:      192.168.1.2

DHCP Server 2:
IP Address: 192.168.1.200
Offer:      192.168.1.50
```

Both servers may receive the client's Discover message and send offers.
The client can select one offer and request that configuration.

Multiple DHCP servers can provide redundancy and availability, but their
address pools must be configured carefully to prevent conflicting
assignments.

### 1.4 Other DHCP messages

**DHCPINFORM**

A client that already has an IP address can use DHCPINFORM to request
additional configuration parameters. The server responds with DHCPACK
containing the requested information.

**DHCPRELEASE**

A client can send DHCPRELEASE when it wants to release its assigned IP
address. This is not mandatory for every client session. A device may
disconnect or shut down without sending a release message. The DHCP
server handles the lease according to its expiration and renewal rules.

### 1.5 DHCP address pool

A DHCP pool defines the range of IP addresses that a DHCP server can
assign to clients.

``` text
Network:          192.168.1.0/24
Default Gateway:  192.168.1.1

DHCP Pool:
192.168.1.100 – 192.168.1.200
```

An administrator configures the pool according to the network
requirements. Addresses already assigned to gateways, servers, printers,
or other statically configured devices should be excluded from dynamic
allocation.

### 1.6 DHCP server on another subnet

A DHCP server does not always need to exist in the same subnet as the
client. A **DHCP relay agent** can forward DHCP messages between a
client subnet and a remote DHCP server.

``` text
Client Network
192.168.10.0/24
       |
       v
Router / DHCP Relay
       |
       v
DHCP Server
192.168.100.10
```

This is useful when an organization has multiple VLANs but wants to
manage DHCP centrally.

------------------------------------------------------------------------

## 2. TCP -- Transmission Control Protocol

### 2.1 What is TCP?

TCP stands for **Transmission Control Protocol**. It is a
connection-oriented transport-layer protocol.

Before normal TCP data transmission begins, the communicating devices
establish a connection.

TCP provides mechanisms for:

-   Reliable data delivery
-   Ordered data delivery
-   Acknowledgements
-   Retransmission
-   Flow control
-   Congestion control

UDP is connectionless. It does not establish a TCP-style connection
before sending data.

### 2.2 TCP vs UDP

  -----------------------------------------------------------------------
  TCP                                 UDP
  ----------------------------------- -----------------------------------
  Connection-oriented                 Connectionless

  Uses connection establishment       No TCP-style handshake

  Provides reliable, ordered          Does not guarantee delivery or
  byte-stream delivery                ordering

  Uses acknowledgements               No built-in acknowledgement
                                      mechanism

  Uses retransmission                 No built-in retransmission
                                      mechanism

  Uses flow and congestion control    No TCP-style flow and congestion
                                      control
  -----------------------------------------------------------------------

**Examples:**

-   TCP is commonly used for SSH and traditional web traffic over
    HTTP/1.1 and HTTP/2.
-   UDP is commonly used for DNS queries, voice communication, and
    real-time applications.
-   QUIC, used by HTTP/3, runs over UDP.

### 2.3 TCP three-way handshake

TCP uses a three-way handshake to establish a connection.

The three messages are:

1.  SYN
2.  SYN-ACK
3.  ACK

**Step 1 -- SYN:** The client initiates the connection by sending a TCP
segment with the SYN flag. It also communicates its initial sequence
number.

**Step 2 -- SYN-ACK:** The server receives the SYN and responds with a
segment containing SYN (server connection information) and ACK
(acknowledgement of the client's SYN).

**Step 3 -- ACK:** The client sends an ACK acknowledging the server's
SYN. The connection is now established.

``` text
Client                         Server
  |                               |
  | -------- SYN --------------> |
  |                               |
  | <------ SYN-ACK ------------ |
  |                               |
  | -------- ACK --------------> |
  |                               |
  |      Connection Established   |
```

### 2.4 Other TCP flags

-   **FIN:** Used to initiate an orderly connection termination.
-   **RST:** Used to reset or abort a TCP connection.

The three-way handshake is used for connection establishment. TCP
connection termination follows a separate process.

------------------------------------------------------------------------

## 3. TCP Windowing

### 3.1 What is TCP windowing?

TCP uses a sliding-window mechanism to manage data transmission.

Instead of requiring an acknowledgement for every individual packet
before sending the next one, TCP can allow multiple bytes of data to
remain unacknowledged. This improves communication efficiency.

The file-transfer example illustrates how transmission can become more
efficient as the sender learns how much data can be sent.

### 3.2 Receiver flow control

TCP flow control prevents a sender from overwhelming the receiving
device.

The receiver advertises its available receive-buffer capacity using the
**receive window (`rwnd`)**.

For example:

``` text
Receiver's advertised window: 9000 bytes
```

The sender must respect the receiver's advertised window. If the
receiver has limited buffer space, the sender must limit the amount of
outstanding data accordingly.

### 3.3 Congestion control

TCP also needs to protect the network itself.

The **congestion window (`cwnd`)** controls how much unacknowledged data
the sender can have in the network.

TCP congestion control can initially increase the congestion window
using a mechanism called **slow start**. This is why the amount of data
transmitted can increase as acknowledgements arrive.

### 3.4 Flow control vs congestion control

  -----------------------------------------------------------------------
  Mechanism                           Purpose
  ----------------------------------- -----------------------------------
  Flow control                        Prevents overwhelming the receiver

  Congestion control                  Prevents overwhelming the network

  Sliding window                      Manages outstanding data using
                                      sequence numbers and window limits
  -----------------------------------------------------------------------

In simplified terms, the sender's outstanding data is constrained by the
smaller of the two windows:

\[ `\text{Send Limit}`{=tex} =
`\min`{=tex}(`\text{rwnd}`{=tex},`\text{cwnd}`{=tex}) \]

### 3.5 TCP sequence and acknowledgement numbers

TCP sequence numbers count **bytes, not packets**.

Example:

``` text
Sender transmits:
Bytes 1–10

Receiver responds:
ACK 11
```

ACK 11 means that the receiver has cumulatively acknowledged the bytes
up to 10 and expects byte 11 next.

This distinction is important when analyzing TCP communication using
Wireshark or other packet-capture tools.

------------------------------------------------------------------------

## 4. Common Port Numbers

### 4.1 What is a port number?

A port number identifies a transport-layer communication endpoint
associated with an application or service.

A single server can run multiple applications and services:

``` text
Server
 |
 |-- Web Server
 |-- FTP Server
 |-- SSH Service
 |-- DNS Service
 |-- Database Service
```

The destination port helps the receiving operating system identify which
service or application should receive the transport-layer data.

The source port helps identify the originating communication endpoint
and supports the response traffic.

### 4.2 Source port and destination port

Consider this connection:

``` text
Source IP:        10.1.1.10
Destination IP:   30.1.1.10

Source Port:      25000
Destination Port: 21
```

Here:

-   Source port `25000` is an example of a dynamically selected
    client-side port.
-   Destination port `21` identifies the traditional FTP control
    service.

The operating system selects an available ephemeral source port. The
actual range depends on the operating system's configuration.

### 4.3 IANA port-number ranges

Port numbers are 16-bit values, giving a range of 0--65535.

  -----------------------------------------------------------------------
  Port Range              Classification          Purpose
  ----------------------- ----------------------- -----------------------
  0--1023                 System / Well-Known     Standard service
                          Ports                   assignments

  1024--49151             User / Registered Ports Registered application
                                                  services

  49152--65535            Dynamic / Private /     Common dynamic
                          Ephemeral Ports         client-side use
  -----------------------------------------------------------------------

The actual ephemeral port range used by an operating system can differ
from the IANA dynamic/private range.

### 4.4 Common port numbers

    Port Protocol   Service
  ------ ---------- -------------------------------------
      20 TCP        FTP Data -- traditional active mode
      21 TCP        FTP Control
      22 TCP        SSH
      23 TCP        Telnet
      25 TCP        SMTP
      53 TCP/UDP    DNS
      67 UDP        DHCP Server
      68 UDP        DHCP Client
      80 TCP        HTTP
     110 TCP        POP3
     123 UDP        NTP
     143 TCP        IMAP
     443 TCP        HTTPS
     443 UDP        Commonly QUIC / HTTP/3
    3306 TCP        MySQL
    3389 TCP/UDP    RDP

These are common assignments, not proof that a particular application is
running on a port. Actual services can use other ports depending on
configuration.

### 4.5 TCP and UDP port spaces

TCP and UDP maintain separate port spaces.

``` text
TCP Port 53
UDP Port 53
```

These are different transport-layer endpoints.

A service using a particular TCP port does not automatically mean that
the same service uses the corresponding UDP port.

HTTP/3 is a useful example:

-   Traditional HTTP traffic commonly uses TCP port 80.
-   HTTPS commonly uses TCP port 443.
-   HTTP/3 uses QUIC over UDP, normally port 443.

### 4.6 FTP control and data connections

FTP uses separate connections for control and data.

-   TCP port 21 is used for the control connection.
-   FTP data transfer uses a separate connection.
-   Traditional active FTP uses TCP port 20 for its data connection.
-   Passive FTP negotiates a separate server-side data port.

This distinction is useful when configuring firewalls and cloud security
groups.

------------------------------------------------------------------------

## 5. Complete Network Data Flow Example

This example combines DHCP, TCP, ports, ARP, routing, and Ethernet
framing.

### 5.1 Network topology

Assume a client wants to communicate with a remote FTP server.

``` text
Client Network: 10.1.1.0/24

Client IP:        10.1.1.10
Default Gateway:  10.1.1.1

Remote Server Network: 30.1.1.0/24

FTP Server IP:    30.1.1.10
FTP Port:         TCP 21
```

The client and server belong to different networks. Therefore, the
client must use its default gateway to reach the remote server.

### 5.2 Step 1 -- Client creates a TCP connection request

The client application wants to establish an FTP control connection.

It creates a TCP segment with:

``` text
Source Port:       25000
Destination Port:  21

TCP Flag:          SYN
```

The operating system selects the source port, while the destination port
corresponds to the FTP control service.

### 5.3 Step 2 -- IP packet creation

The TCP segment is encapsulated inside an IPv4 packet.

``` text
Source IP:        10.1.1.10
Destination IP:   30.1.1.10
```

The destination IP identifies the remote server.

### 5.4 Step 3 -- Client identifies the remote network

The client checks the destination IP against its own network
configuration.

``` text
Client Network: 10.1.1.0/24
Server Network: 30.1.1.0/24
```

Since the destination is outside the local subnet, the client forwards
the packet to its default gateway.

``` text
Default Gateway: 10.1.1.1
```

### 5.5 Step 4 -- ARP resolves the default gateway MAC address

The client needs the gateway's MAC address to construct the Ethernet
frame.

If that address is not already available in the ARP cache, the client
sends an ARP Request.

``` text
Who has 10.1.1.1?
```

The gateway responds with its MAC address.

``` text
Gateway IP:  10.1.1.1
Gateway MAC: AA:AA:AA:AA:AA:AA
```

The client can now construct the Ethernet frame.

**Important:** The client resolves the MAC address of the local default
gateway, not the MAC address of the remote FTP server.

### 5.6 Step 5 -- Ethernet frame creation

The client creates an Ethernet frame.

``` text
Source MAC:       Client MAC
Destination MAC:  Gateway MAC

Payload:          IPv4 Packet
FCS:              CRC-32
```

The FCS is placed at the end of the Ethernet frame. Ethernet uses CRC-32
for error detection. It is not an MD5 hash or a cryptographic security
mechanism.

### 5.7 Step 6 -- Router receives the frame

The router receives the Ethernet frame and performs several operations:

1.  Checks the incoming frame's FCS.
2.  Processes the Ethernet header.
3.  Removes the incoming Layer 2 framing.
4.  Examines the destination IP address.
5.  Checks its routing table.
6.  Selects the outgoing interface and next hop.

Example routing-table entry:

``` text
Destination Network: 30.1.1.0/24
Next Hop:            20.1.1.2
```

The router knows that the destination network is reachable through the
next hop `20.1.1.2`.

Routes can be learned dynamically through routing protocols or
configured manually using static routes.

### 5.8 Step 7 -- Router forwards the packet

Before forwarding the packet, the router performs the necessary Layer 3
processing.

For normal IPv4 forwarding:

-   TTL is decreased by one.
-   IPv4 header checksum is updated.
-   Source and destination IP addresses normally remain unchanged.

The router then creates a new Ethernet frame for the outgoing network.

``` text
Source MAC:       BBB
Destination MAC:  CCC

Payload:          Forwarded IP Packet
FCS:              Newly calculated CRC-32
```

The new Ethernet frame has different Layer 2 addresses.

If the router does not know the next-hop MAC address, it uses ARP on the
outgoing IPv4 network to resolve it.

### 5.9 Step 8 -- Destination server receives the packet

The packet eventually reaches the server's network.

The final router forwards it using the destination server's MAC address.

The server processes the received frame:

1.  Checks the Ethernet FCS.
2.  Processes the Ethernet header.
3.  Examines the destination IP address.
4.  Processes the TCP segment.
5.  Checks the destination port.

The destination TCP port is 21, so the traffic is directed to the FTP
control service.

The SYN flag indicates that the client is requesting a TCP connection.

### 5.10 Step 9 -- TCP three-way handshake completes

The server responds with SYN-ACK. The client then sends ACK.

``` text
Client                         FTP Server
  |                                 |
  | -------- SYN ----------------> |
  |                                 |
  | <------ SYN-ACK -------------- |
  |                                 |
  | -------- ACK ----------------> |
  |                                 |
  |       Connection Established    |
```

The basic TCP connection is now established. FTP data transfer uses its
separate data connection.

### 5.11 What changes and what remains the same?

  Information               Behavior during routing
  ------------------------- -----------------------------------------------
  Source IP address         Normally remains unchanged
  Destination IP address    Normally remains unchanged
  Source MAC address        Changes at each Ethernet hop
  Destination MAC address   Changes at each Ethernet hop
  IPv4 TTL                  Decreases at each router
  IPv4 header checksum      Updated when the header changes
  TCP source port           Normally remains unchanged
  TCP destination port      Normally remains unchanged
  Ethernet FCS              Recalculated for each outgoing Ethernet frame

**NAT clarification:** When Network Address Translation is used, IP
addresses and potentially TCP/UDP port numbers can also change.

This is important when studying cloud NAT gateways, firewalls, and
private-to-public network communication.

------------------------------------------------------------------------

## 6. Practical DevOps Connections

These networking concepts are useful when deploying and troubleshooting
applications in AWS, Azure, and other environments.

  -----------------------------------------------------------------------
  Concept                             DevOps application
  ----------------------------------- -----------------------------------
  DHCP                                Understanding dynamic network
                                      configuration of devices and VMs

  TCP handshake                       Troubleshooting failed application
                                      connections

  TCP windowing                       Understanding network throughput
                                      and performance

  Port numbers                        Configuring cloud firewall and
                                      security rules

  ARP                                 Understanding local network address
                                      resolution

  Default gateway                     Understanding communication outside
                                      a subnet

  Routing table                       Troubleshooting connectivity
                                      between subnets

  FCS/CRC                             Understanding Ethernet frame error
                                      detection

  NAT                                 Understanding communication between
                                      private and public networks
  -----------------------------------------------------------------------

### Practical scenario -- EC2 application connectivity

Suppose an application is deployed on an AWS EC2 instance and listens on
TCP port `8080`.

The application works locally, but a remote client cannot access it.

A systematic troubleshooting approach would be:

1.  Verify that the application is running.
2.  Check whether the application listens on port 8080.
3.  Verify the EC2 Security Group inbound rules.
4.  Check Network ACL rules.
5.  Verify the subnet route table.
6.  Check the operating system firewall.
7.  Verify that the application is listening on the correct network
    interface.

Useful Linux command:

``` bash
sudo ss -tulnp | grep 8080
```

This checks whether a process is listening on TCP or UDP port 8080.

Understanding TCP, ports, routing, and gateways helps narrow down the
cause instead of assuming that every connectivity issue is an
application problem.

------------------------------------------------------------------------

## 7. Important Revision Points

1.  DHCP dynamically provides IP configuration to network clients.
2.  DORA represents Discover, Offer, Request, and Acknowledgement.
3.  DHCP relay allows a DHCP server to serve clients on other subnets.
4.  TCP is connection-oriented, while UDP is connectionless.
5.  TCP establishes a connection using SYN, SYN-ACK, and ACK.
6.  TCP sequence and acknowledgement numbers count bytes, not packets.
7.  Flow control protects the receiver; congestion control protects the
    network.
8.  Port numbers help identify transport-layer application endpoints.
9.  IANA classifies ports into System, User, and Dynamic/Private ranges.
10. TCP and UDP have separate port spaces.
11. ARP resolves a local IPv4 next-hop address to an Ethernet MAC
    address.
12. A client normally sends remote-network traffic to its default
    gateway.
13. Routers replace Layer 2 framing when forwarding between Ethernet
    networks.
14. IPv4 TTL decreases at every router.
15. Ethernet FCS uses CRC-32 for error detection.
16. FTP port 21 is the control connection, not the entire FTP
    data-transfer mechanism.

------------------------------------------------------------------------

## 8. Interview and Self-Assessment Questions

1.  What is DHCP, and why is it required?
2.  Explain the DHCP DORA process.
3.  What happens when multiple DHCP servers respond to a Discover
    message?
4.  What is a DHCP relay agent?
5.  What is the difference between DHCPINFORM and DHCPRELEASE?
6.  Why is TCP called a connection-oriented protocol?
7.  Explain the TCP three-way handshake.
8.  What is the difference between SYN, ACK, FIN, and RST?
9.  What is TCP sliding-window communication?
10. What is the difference between flow control and congestion control?
11. What does TCP acknowledgement number 11 indicate?
12. What is a port number?
13. What is the difference between a source port and destination port?
14. Explain the three IANA port-number ranges.
15. Why do TCP and UDP maintain separate port spaces?
16. What is the purpose of ARP?
17. Why does a client use the default gateway for remote communication?
18. What happens to MAC addresses when a packet passes through a router?
19. What is Ethernet FCS, and how does CRC-32 help detect transmission
    errors?
20. Which IP packet fields change when a router forwards an IPv4 packet?

------------------------------------------------------------------------

## 9. Key Technical Corrections Incorporated

  -----------------------------------------------------------------------
  Area                                Verified understanding
  ----------------------------------- -----------------------------------
  DHCP                                DHCP relay allows remote DHCP
                                      servers to serve clients on other
                                      subnets.

  Multiple DHCP servers               Multiple servers can provide
                                      redundancy when configured
                                      correctly.

  DHCPINFORM                          Requests configuration parameters
                                      when the client already has an IP
                                      address.

  TCP windowing                       Receiver flow control and network
                                      congestion control are separate
                                      mechanisms.

  TCP acknowledgements                TCP sequence and acknowledgement
                                      numbers count bytes, not packets.

  Port ranges                         IANA System Ports are 0--1023; User
                                      Ports are 1024--49151;
                                      Dynamic/Private Ports are
                                      49152--65535.

  Ephemeral ports                     Operating-system ephemeral ranges
                                      can differ from the IANA
                                      dynamic/private range.

  HTTP                                HTTP/3 uses QUIC over UDP, normally
                                      port 443.

  FTP                                 Port 21 is the control connection;
                                      data uses a separate connection.

  ARP                                 A client resolves the local
                                      next-hop MAC address, normally the
                                      default gateway for remote traffic.

  FCS/CRC                             Ethernet FCS uses CRC-32 and is
                                      located at the end of the Ethernet
                                      frame.

  Routing                             IPv4 TTL decreases at each router
                                      and the header checksum is updated.

  NAT                                 NAT can modify IP addresses and
                                      transport-layer port numbers.
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## 10. Learning Outcome

After studying this lesson, I should be able to:

-   Explain how DHCP automatically provides network configuration.
-   Describe the DHCP DORA process and the role of a DHCP relay.
-   Explain TCP connection establishment using the three-way handshake.
-   Distinguish TCP flow control from congestion control.
-   Identify common TCP and UDP port numbers.
-   Explain how ARP resolves the default gateway's MAC address.
-   Describe how a router forwards packets between different networks.
-   Identify which Layer 2 and Layer 3 fields change during forwarding.
-   Explain the purpose of Ethernet FCS and CRC-32.
-   Relate basic networking concepts to cloud infrastructure
    troubleshooting.