# Networking for DevOps — Switching, VLANs, Network Design & Linux Networking

## Overview

This note builds on basic IP addressing, subnetting, TCP/UDP, and network-device concepts. It focuses on how switches forward traffic, how VLANs segment a network, how networks are designed for offices and services, and how to inspect or troubleshoot networking from Linux.

These concepts are useful when working with virtual machines, cloud networks, application servers, Jenkins agents, containers, and hybrid environments.

## 1. Switching Fundamentals

A switch primarily operates at Layer 2 (Data Link layer) and forwards Ethernet frames inside a local network based on MAC addresses.

When a frame arrives, the switch:
1. Reads the source MAC address and learns which port it arrived on.
2. Updates its MAC address table with the source MAC-to-port association.
3. Looks up the destination MAC address.
4. For a known destination, forwards the frame through the associated port.
5. For an unknown destination, generally floods the frame out other eligible ports in the same VLAN (except the incoming port).

Switches learn from **source** MAC addresses, not by learning the destination MAC from a frame.

## 2. MAC Address Learning and Forwarding

A switch maintains a MAC address table (also called a forwarding table).

| Situation | Switch behavior |
|---|---|
| Source MAC received | Learn/update the MAC and incoming port |
| Destination MAC known | Forward toward the mapped port |
| Destination MAC unknown | Flood within that VLAN |
| Broadcast frame | Flood within that VLAN |

MAC learning helps avoid sending every unicast frame to every port. Entries are commonly aged out when they have not been used for a period of time.

## 3. Broadcast Domains

A broadcast domain is the set of devices that receive a Layer 2 broadcast from one another.

A traditional switch without VLAN segmentation forwards broadcasts within its connected broadcast domain. VLANs allow a switch infrastructure to be divided into multiple logical broadcast domains.

Routers or Layer 3 routing interfaces are needed to route traffic between different IP networks/VLANs.

## 4. VLANs (Virtual LANs)

A VLAN logically separates devices into different Layer 2 networks even when they are connected to the same physical switching infrastructure.

### Why VLANs are used

- Separate departments, workloads, or device types.
- Reduce the scope of Layer 2 broadcasts.
- Apply different access and security policies.
- Organize networks without requiring separate physical switches for every group.

Example:

| VLAN | Purpose | Example subnet |
|---|---|---|
| VLAN 10 | Sales | 192.168.10.0/24 |
| VLAN 20 | Marketing | 192.168.20.0/24 |
| VLAN 30 | Servers | 192.168.30.0/24 |
| VLAN 99 | Management | 192.168.99.0/24 |

VLANs are Layer 2 segmentation; IP subnets are Layer 3 addressing. They are commonly mapped one-to-one in enterprise designs, but VLAN and subnet are not the same thing.

### Communication between VLANs

Devices in different VLANs normally cannot communicate directly at Layer 2. Traffic between their IP networks must be routed by a Layer 3 device, such as a router or Layer 3 switch, with suitable routes and security rules.

Example: a workstation in VLAN 10 communicating with a server in VLAN 30 sends traffic to its default gateway. The gateway routes the packet to the destination subnet if permitted.

## 5. Access Ports

An access port is generally configured for one data VLAN and connects to an endpoint such as a workstation, printer, or server.

A Cisco-style illustrative configuration:

```text
interface gigabitEthernet0/1
 switchport mode access
 switchport access vlan 10
```

The commands above are vendor-specific examples. The concept is that frames from the endpoint are associated with VLAN 10 on that access port.

## 6. Trunk Ports and 802.1Q

A trunk port carries traffic for multiple VLANs between network devices, such as switches or a switch and a router/firewall.

IEEE 802.1Q tagging identifies the VLAN associated with an Ethernet frame as it travels over a trunk. This allows multiple VLANs to share a physical link while remaining logically separated.

Illustrative Cisco-style configuration:

```text
interface gigabitEthernet0/24
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,99
```

The allowed VLAN list limits which VLANs can traverse that trunk. Exact syntax and defaults depend on the platform.

## 7. VLANs Across Multiple Switches

For a VLAN to carry traffic across several switches, the VLAN must be supported and allowed along the path, and the inter-switch links must be configured appropriately.

If VLAN 20 exists on Switch A and Switch C but is missing or not allowed on an intermediate Switch B/link, traffic for VLAN 20 may not reach the other side.

**Troubleshooting checklist:**
- Confirm the VLAN exists on each relevant switch.
- Confirm endpoint ports are assigned to the intended VLAN.
- Confirm trunk links are operational.
- Confirm the VLAN is allowed on every trunk along the path.
- Check the access/trunk configuration and link status.

## 8. Dynamic Trunking Protocol (DTP)

DTP is a Cisco-proprietary protocol used on some switch ports to negotiate trunking.

Dynamic trunk negotiation can create a security risk if an untrusted device is connected and the port negotiates trunk mode. Where trunking is not required, configure the port deliberately as an access port and disable negotiation where the platform supports it and policy requires it.

Understand DTP's purpose and risk; detailed Cisco command memorization is not required for cloud networking fundamentals.

## 9. Native VLAN

On a traditional IEEE 802.1Q trunk, the native VLAN is the VLAN associated with untagged frames received on the trunk. Cisco switch configurations commonly use a native VLAN concept.

Native VLAN mismatches across a trunk can cause unexpected traffic handling and warnings. In network design, keep trunk settings consistent and avoid using the default VLAN as the native VLAN where security policy recommends a dedicated, unused native VLAN.

## 10. VLAN Trunking Protocol (VTP)

VTP is a Cisco-proprietary protocol used to distribute VLAN database information among switches in a VTP domain. It is not the same as 802.1Q, which tags frames on trunks.

Common VTP modes:
- **Server:** Can create, modify, and distribute VLAN information within the domain.
- **Client:** Receives VLAN information and, in classic VTP behavior, does not independently manage the VLAN database.
- **Transparent:** Does not participate in synchronizing the VLAN database in the same way; local VLAN configuration is maintained, while VTP advertisements may be forwarded.

VTP can simplify VLAN administration in certain Cisco environments, but misconfiguration or an incorrect domain/revision situation can affect VLAN information across switches. Treat it as a platform-specific feature and understand the operational risks before enabling it.

## 11. VLAN Pruning

VLAN pruning limits the VLANs carried on trunk links to those needed downstream. This can reduce unnecessary broadcast and unknown-unicast traffic across parts of a switched network.

Pruning is about restricting VLAN propagation over links; it does not replace VLAN configuration, routing, or security controls.

## 12. Frame Forwarding Methods

### Cut-through switching
Begins forwarding a frame after reading enough of it to identify the destination, potentially reducing latency. It may forward a frame before receiving and checking the complete frame.

### Store-and-forward switching
Receives the complete frame before forwarding and can check frame integrity (such as the Ethernet FCS). This adds some processing/latency but allows invalid frames to be discarded before forwarding.

## 13. Layer 2 Loops and STP

Redundant Layer 2 links can create switching loops. Ethernet frames do not have a Layer 2 TTL field that automatically stops them circulating.

Loops may cause:
- Broadcast storms.
- MAC address table instability (a MAC appears to move between ports).
- High network utilization and packet loss.
- Poor or interrupted connectivity.

Spanning Tree Protocol (STP) helps prevent Layer 2 loops by placing redundant paths into a non-forwarding state while maintaining a loop-free topology. Redundant links can be available as alternate paths when topology changes, subject to STP variant and convergence behavior.

## 14. Link Speed, Duplex, and Troubleshooting

Ethernet interfaces negotiate speed and duplex in many environments. Duplex mismatch can cause poor performance, errors, collisions (on half-duplex segments), or retransmissions depending on the link and technology.

When troubleshooting a slow or unstable connection:
- Inspect link status and negotiated speed/duplex at both ends.
- Check interface error counters and drops.
- Check cabling, transceivers, and physical link quality.
- Check congestion, CPU, and application-level symptoms.
- Avoid forcing speed or duplex without confirming both ends and platform requirements.

## 15. Port Security

Port security is a switch feature that can limit which MAC addresses are allowed on a port and what happens when the configured limit or policy is violated.

It can help restrict unauthorized devices on access ports. It is not a substitute for broader authentication, network access control, or firewall policies.

## 16. Small Office Network Design

Network design starts with requirements, not switch commands.

Questions to ask:
- What departments, users, servers, printers, phones, and services exist?
- Which groups need to communicate?
- What traffic should be isolated?
- What IP ranges and subnets are needed?
- How will management access be secured?
- What redundancy, capacity, and growth are expected?

Illustrative segmentation:

| Network | Example VLAN | Example subnet |
|---|---:|---|
| Marketing | 10 | 192.168.10.0/26 |
| Management | 20 | 192.168.20.0/27 |
| Accounts | 30 | 192.168.30.0/27 |
| HR | 40 | 192.168.40.0/27 |
| Voice | 50 | 192.168.50.0/27 |
| Servers | 60 | 192.168.60.0/27 |

These are example ranges for planning, not a required design. Allocate subnet sizes based on expected hosts, growth, and requirements.

### Management network and out-of-band access

Network-device management should be separated from user/data traffic where possible and protected with restricted access.

An out-of-band management network uses a separate management path/interface and infrastructure, where available, so administrators may reach equipment even when the production data network has problems. A terminal server can provide remote console access to serial console ports in some environments.

## 17. How This Maps to AWS and Azure

Cloud networking uses logical networks and managed infrastructure rather than requiring the cloud user to configure physical access switches.

| Traditional networking concept | Cloud networking concept (functional comparison) |
|---|---|
| Logical network boundary | AWS VPC / Azure VNet |
| IP subnet | VPC/VNet subnet |
| Route table | Cloud route table |
| Network access policy | Security group / NSG and other controls |
| Router/gateway | Cloud routing and gateway resources |
| Network segmentation | Separate subnets, routing, and security policy |

These are functional comparisons, not exact equivalents. VLANs are Layer 2 constructs, while cloud VPC/VNet subnets are IP-layer logical segments with cloud-provider-defined behavior.

Illustrative cloud network:

```text
VPC/VNet: 10.0.0.0/16

Web subnet:        10.0.1.0/24
Application subnet: 10.0.2.0/24
Database subnet:    10.0.3.0/24
Management subnet:  10.0.4.0/24
```

Security rules and routes should restrict access between tiers. For example, a database should normally accept connections only from authorized application workloads, not directly from the public Internet.

## 18. Linux Networking Commands

These commands help inspect networking from Linux VMs, servers, and cloud instances.

| Command | Purpose |
|---|---|
| `ip addr` | Show interface addresses and state |
| `ip route` | Show routing table |
| `ip neigh` | Show neighbor/ARP cache |
| `ping <host>` | Test IP reachability using ICMP |
| `nslookup <name>` | Query DNS |
| `dig <name>` | Detailed DNS query |
| `ss -tuln` | Show listening TCP/UDP sockets |
| `ss -tulnp` | Show sockets and processes (permissions may be needed) |
| `traceroute <host>` | Trace path using traceroute |
| `tracepath <host>` | Trace path with path-MTU discovery behavior on supported systems |

Useful first checks:

```bash
ip addr
ip route
ip neigh
ping -c 4 <destination>
ss -tuln
```

A ping failure alone does not prove a service is unavailable; ICMP may be filtered. Test the actual destination port and service when troubleshooting applications.

## 19. SSH — Secure Remote Administration

SSH (Secure Shell) provides encrypted remote access and command execution. It is commonly used by administrators, automation tools, and DevOps workflows to access Linux servers.

SSH normally listens on TCP port 22, unless configured otherwise.

### 19.1 Install and start the SSH server

**Ubuntu/Debian:**

```bash
sudo apt update
sudo apt install openssh-server
sudo systemctl status ssh
sudo systemctl start ssh
sudo systemctl enable ssh
```

**Amazon Linux (where the package is not already installed):**

```bash
sudo dnf install openssh-server
sudo systemctl status sshd
sudo systemctl start sshd
sudo systemctl enable sshd
```

Service names differ by distribution: Ubuntu commonly uses `ssh`; Amazon Linux commonly uses `sshd`.

Check whether port 22 is listening:

```bash
sudo ss -tulnp | grep :22
```

### 19.2 Connect to a remote server

With username/password authentication (if enabled and permitted):

```bash
ssh username@server-ip
```

With a private key:

```bash
ssh -i path/to/private-key.pem ec2-user@EC2-PUBLIC-IP
```

For Ubuntu EC2 images, the default login user is commonly `ubuntu`; Amazon Linux commonly uses `ec2-user`. Confirm the image documentation and account configuration.

Protect private keys and set restrictive permissions where appropriate:

```bash
chmod 400 private-key.pem
```

### SSH Key-Based Authentication

SSH keys provide an alternative to password-based remote login. A key pair consists of a **private key** kept on the client and a **public key** installed on the remote account. Never share your private key.

#### 1. Generate a key pair with ssh-keygen

On the client machine:

```bash
ssh-keygen -t ed25519
```

Accept the default file path (typically `~/.ssh/id_ed25519`) or choose another. The public key is saved as `~/.ssh/id_ed25519.pub`. You may protect the private key with a passphrase.

View the public key:

```bash
cat ~/.ssh/id_ed25519.pub
```

#### 2. Copy the public key with ssh-copy-id

```bash
ssh-copy-id username@server-ip
```

Example:

```bash
ssh-copy-id ec2-user@192.168.1.20
```

This installs your client public key into the remote account's `~/.ssh/authorized_keys`. Initial access must already be possible (often using a password or another permitted authentication method). The `ssh-copy-id` utility must be available on the client.

#### 3. Connect using the key

```bash
ssh username@server-ip
```

If the key is in a non-default location:

```bash
ssh -i ~/.ssh/my_key username@server-ip
```

The private key stays on the client. SSH proves possession of the matching private key; the key itself is not sent to the server.

#### 4. Remote key file and permissions

The remote account stores authorized public keys in:

```text
~/.ssh/authorized_keys
```

Typical permissions:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

Ensure the files and directory are owned by the remote account. Do not overwrite existing `authorized_keys` entries when adding another key.

#### 5. Manual setup if ssh-copy-id is unavailable

On the client, display and copy the full public-key line:

```bash
cat ~/.ssh/id_ed25519.pub
```

On the remote server, using an already-working login:

```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
nano ~/.ssh/authorized_keys
```

Paste the public key as one line, save, and set:

```bash
chmod 600 ~/.ssh/authorized_keys
```

Append the new key; preserve any existing keys.

#### 6. SSH key authentication flow

```text
Client                         Remote server
  |--- SSH connection request --->|
  |<-- authentication challenge --|
  |--- proof using private key ->|
  |<---- authentication OK ------|
  |==== encrypted session =======|
```

#### 7. Troubleshooting key login

- Verify username, server address, and private-key path.
- Confirm the matching public key is in the remote account's `authorized_keys`.
- Check ownership and permissions of the remote home directory, `.ssh`, and `authorized_keys`.
- Confirm server configuration permits public-key authentication.
- Use verbose client output and server logs:

```bash
ssh -v username@server-ip
sudo journalctl -u ssh --no-pager -n 50
sudo journalctl -u sshd --no-pager -n 50
ls -ld ~/.ssh
ls -l ~/.ssh/authorized_keys
```

Service name and log location can vary by Linux distribution. Keep a working SSH session open while making remote SSH changes and test a second connection before closing it.

### 19.3 SSH server configuration

Server configuration is typically in `/etc/ssh/sshd_config`. Make a backup before editing.

After changing SSH server settings, validate the configuration before restarting/reloading:

```bash
sudo sshd -t
```

If validation succeeds, reload or restart the SSH service using the correct service name for the distribution. Keep your current remote session open until a new connection is tested successfully; a bad SSH configuration can lock you out.

### 19.4 SSH troubleshooting

Check:
- Correct server IP/DNS name and username.
- Route and network reachability.
- SSH server process/service status.
- Port 22 listener.
- Linux firewall rules.
- Cloud firewall/security rules (AWS Security Group/NACL, Azure NSG/route).
- Key file, ownership, and permissions.
- Server logs.

Examples:

```bash
sudo systemctl status ssh
sudo systemctl status sshd
sudo journalctl -u ssh --no-pager -n 50
sudo journalctl -u sshd --no-pager -n 50
ssh -v username@server-ip
```

Use the service/log commands that match your Linux distribution.

## 20. Telnet — Legacy Remote Access and Port Testing

Telnet is an older remote terminal protocol. It does not encrypt the session; usernames, passwords, and data can be exposed on the network. Do not use Telnet for remote administration over untrusted networks. SSH is the secure replacement.

Telnet uses TCP port 23 by default, but the Telnet client can also attempt a TCP connection to another port.

### 20.1 Install a Telnet client

**Ubuntu/Debian:**

```bash
sudo apt update
sudo apt install telnet
```

**Amazon Linux (package availability may vary by release/repository):**

```bash
sudo dnf install telnet
```

### 20.2 Connect to a Telnet service

```bash
telnet server-ip 23
```

A port connectivity example (not necessarily a Telnet application):

```bash
telnet 192.168.1.50 8080
```

If the TCP connection opens, it shows that the connection to that port was accepted; it does **not** prove the application protocol is healthy or that a valid response will be returned.

A modern alternative for testing whether a TCP port is reachable is:

```bash
nc -vz 192.168.1.50 8080
```

### 20.3 Telnet server (legacy lab only)

Installing a Telnet client does not install or enable a Telnet server. A Telnet server is a separate service and is legacy/insecure. Server setup varies by Linux distribution and may rely on `inetd` or `xinetd` and additional packages.

If experimenting in a lab, keep it isolated and do not expose a Telnet server to the Internet or production network. Prefer SSH for administration.

## 21. Linux Firewall Basics

Linux hosts may use different firewall management tools depending on distribution and configuration:

- **UFW:** Commonly used on Ubuntu-based systems.
- **firewalld:** Common on RHEL-family systems, including many Amazon Linux environments.
- **nftables:** Linux firewall framework used directly or underneath other tools.

Cloud networking controls are separate layers. A service may be listening on the VM but still blocked by its host firewall, cloud security group/NSG, network ACL, or routing.

## 22. Linux VLAN Interfaces and Bridges (Conceptual)

Linux can work with VLAN-tagged interfaces and software bridges.

- A VLAN interface (for example, `ens5.10`) represents traffic associated with VLAN ID 10 on a parent interface, when configured for VLAN tagging.
- A Linux bridge forwards Layer 2 frames between attached interfaces, similar in concept to a software switch.
- The physical switch, hypervisor, or cloud network must support the required VLAN-tagging behavior.

Cloud VPC/VNet networks generally do not expose customer-managed VLAN trunks in the same way as traditional on-premises switches; use the provider's supported network constructs.

## 23. DevOps Troubleshooting Scenario

A Jenkins server cannot connect to an application server at `192.168.10.20:8080`.

Use a layered approach:

```bash
# 1. Check local IP and interface state
ip addr

# 2. Check routing
ip route

# 3. Test basic reachability (may be blocked by ICMP policy)
ping -c 4 192.168.10.20

# 4. Check whether the destination port is reachable
nc -vz 192.168.10.20 8080

# 5. On the application server, check the listener
sudo ss -tulnp | grep :8080
```

Then check:
1. Is the application running and bound to the expected interface/port?
2. Does the destination have the correct IP and route?
3. Are host firewall rules allowing the connection?
4. Are AWS Security Groups/NACLs or Azure NSGs/routes permitting the flow?
5. Are the Jenkins server and application server in networks with a valid route between them?
6. Do application logs show errors after the connection is established?

A useful rule: **verify the network path, port, security rules, and application separately.**

## 24. Practical Networking Checklist

- [ ] Identify source and destination IP addresses.
- [ ] Confirm subnet and default gateway.
- [ ] Check interface/link state.
- [ ] Check routing table.
- [ ] Check DNS resolution if hostnames are used.
- [ ] Test ICMP cautiously (it may be filtered).
- [ ] Test the actual TCP port.
- [ ] Confirm the service is listening on the correct address.
- [ ] Review host firewall and cloud security rules.
- [ ] Check application logs.

## 25. Key Takeaways

- Switches learn source MAC addresses and forward frames using their MAC table.
- VLANs create logical Layer 2 separation; inter-VLAN traffic requires Layer 3 routing.
- Access ports connect endpoints to a VLAN; trunks carry multiple VLANs using tagging.
- VLAN configuration must be consistent across the path.
- STP helps prevent Layer 2 loops.
- Network design starts with requirements, segmentation, IP planning, security, and growth.
- Linux commands provide visibility into interfaces, routes, neighbors, DNS, ports, and connectivity.
- SSH is the preferred secure remote-administration protocol; Telnet is insecure and legacy.
- DevOps troubleshooting should isolate network reachability, port access, service health, and security policy.

---

These are foundational concepts for later AWS VPC, Azure VNet, VM networking, Docker networking, Kubernetes networking, and CI/CD troubleshooting.
