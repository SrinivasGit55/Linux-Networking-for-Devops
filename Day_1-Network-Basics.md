# Network Basics

## 1. Network

A computer network is formed when two or more computers are connected in some way to share information or resources more broadly.

A network is the interconnection of computing devices, from computers, to phones, to printers, to smart devices, and so on.

---

## 2. Introduction to Data Communication

Data communication refers to the exchange of data between two or more devices via some form of transmission medium, such as:

- cable
- wireless signal
- optical fiber
- radio communication

### Fundamental Components of Data Communication

- Message: The information to be communicated, such as text, numbers, pictures, audio, or video.
- Sender: The device that sends the message.
- Receiver: The device that receives the message.
- Transmission Medium: The physical path by which the message travels.
- Protocol: A set of rules that govern communication, such as TCP, IP, or HTTP.

---

## 3. Types of Computer Networks

Networks are categorized based on their geographical scale and coverage area.

### PAN (Personal Area Network)

- Small range, usually within a few meters
- Example: smartphone to smartwatch or Bluetooth headphones

### LAN (Local Area Network)

- Covers a home, office, or building
- High data transfer rates
- Privately owned and typically secure

### WLAN (Wireless Local Area Network)

- A LAN that uses wireless communication like Wi-Fi instead of cables

### MAN (Metropolitan Area Network)

- Covers a city or town
- Larger than a LAN

### WAN (Wide Area Network)

- Covers large geographic areas such as countries or continents
- The Internet is the largest WAN

---

## 4. Network Topologies

Topology refers to the physical or logical arrangement of devices and links in a network.

### Bus Topology

- Devices connect to one central cable
- Simple but vulnerable if the main cable fails

### Star Topology

- All devices connect to a central hub or switch
- Easy to manage and isolate issues
- Central device failure affects all connected devices

### Ring Topology

- Devices connect in a circular loop
- A break in the ring may disrupt the whole network

### Mesh Topology

- Devices connect with multiple paths
- Highly redundant and fault tolerant
- More expensive and complex

### Tree Topology

- Hierarchical structure combining star-like branches

### Hybrid Topology

- A mix of two or more topology types

---

## 5. Client-Server Model

Networks are usually made up of clients and servers.

### Server

A server is a computer or system that provides shared resources or services to other devices.

Examples:

- web server
- file server
- database server
- application server

### Client

A client is a device or application that requests services from a server.

Examples:

- laptop
- phone
- browser application
- user software

---

## 6. Web Server vs Application Server

### Web Server

A web server delivers static files such as HTML, CSS, JavaScript, and images.

It commonly uses:

- HTTP
- HTTPS
- Nginx
- Apache

### Application Server

An application server handles business logic such as:

- login processing
- payment workflows
- database queries
- application-specific rules

Examples include:

- Tomcat
- JBoss
- WebLogic
- Gunicorn

---

## 7. Physical Server vs Virtual Server

### Physical Server

A physical server is a dedicated machine that uses its own hardware resources.

### Virtual Server

A virtual server runs on a hypervisor and shares the physical hardware resources with other virtual machines.

---

## 8. Private vs Public Network

### Private Network

A private network is restricted to authorized users or internal systems.

Examples:

- internal office network
- intranet
- private lab network

### Public Network

A public network allows broad access, such as the Internet.

The Internet is the most common public network.

---

## 9. OSI Model

The OSI (Open Systems Interconnection) model is a conceptual framework that standardizes networking functions into 7 layers.

### Layer 1: Physical

- Transmission of raw bits over media
- Examples: cables, hubs, repeaters

### Layer 2: Data Link

- MAC addressing
- Framing
- Error detection
- Examples: Ethernet, switches

### Layer 3: Network

- Logical addressing and routing
- Examples: IP, ICMP, routers

### Layer 4: Transport

- End-to-end communication and error handling
- Examples: TCP, UDP

### Layer 5: Session

- Session management between applications
- Examples: RPC, NetBIOS

### Layer 6: Presentation

- Encryption, compression, data formatting
- Examples: SSL/TLS, JPEG

### Layer 7: Application

- Network services used by applications
- Examples: HTTP, HTTPS, FTP, DNS

---

## 10. TCP/IP Model

The TCP/IP model is a practical model used in real networks and the Internet.

### Application Layer

Combines the OSI application, presentation, and session layers.

Examples:

- HTTP
- FTP
- DNS

### Transport Layer

Responsible for end-to-end communication.

Examples:

- TCP
- UDP

### Internet Layer

Handles logical addressing and routing.

Examples:

- IPv4
- IPv6
- ICMP

### Network Access Layer

Combines the OSI data link and physical layers.

Examples:

- Ethernet
- Wi-Fi
- network media

---

## 11. OSI vs TCP/IP

- OSI is a conceptual reference model.
- TCP/IP is the practical model used on real networks.

Both help explain how data moves through a network.

---

## 12. Key Takeaways

- A network is a group of connected devices.
- Data communication requires a sender, receiver, medium, and protocol.
- Networks can be local or wide-area.
- Topology defines how devices connect.
- Client-server architecture is common in networking.
- OSI and TCP/IP models explain how communication occurs in layers.

---

## 13. Interview Questions

### Q1: What is a network?

A network is a collection of connected devices that share information or resources.

### Q2: What is the difference between LAN and WAN?

LAN is local and limited in scope; WAN covers larger geographic distances.

### Q3: What is a server?

A server is a system that provides resources or services to clients.

### Q4: What is the OSI model?

It is a 7-layer conceptual model that explains networking functions.

### Q5: What is the difference between a web server and an application server?

A web server serves content; an application server runs business logic.

---

## 14. Official References

- Cisco Networking Academy: https://www.netacad.com
- Cisco: https://www.cisco.com
- IANA: https://www.iana.org
- RFC 791: https://datatracker.ietf.org/doc/html/rfc791

---

This note gives a practical and beginner-friendly introduction to networking basics.
