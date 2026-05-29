Hybrid Network Design using VLSM, Static Routing, RIPv2, DHCP, DNS, and Email Services

## Project Overview
This project simulates a complete enterprise-style network in Cisco Packet Tracer based on the *Malcolm in the Middle* family structure.
The network was designed to solve communication problems across multiple locations using VLSM subnetting, hybrid routing, DHCP services, DNS, web hosting, and email communication.

The base network used was: 10.14.0.0/16
The second octet (`14`) was derived from the student ID **23101314**.
# Network Design
## VLSM Addressing Scheme
The `/16` network was subnetted using VLSM from largest to smallest requirements.

| Location                   | Network        | Subnet Mask     | Usable Hosts | Required Hosts |
| -------------------------- | -------------- | --------------- | ------------ | -------------- |
| Malcolm's School Lab       | 10.14.0.0/22   | 255.255.252.0   | 1022         | 840            |
| Francis's Military Academy | 10.14.4.0/23   | 255.255.254.0   | 510          | 390            |
| Dewey's Creative Lab       | 10.14.6.0/23   | 255.255.254.0   | 510          | 300            |
| Reese's Kitchen Zone       | 10.14.8.0/24   | 255.255.255.0   | 254          | 210            |
| Hal's Workplace            | 10.14.9.0/25   | 255.255.255.128 | 126          | 72             |
| Family Home                | 10.14.9.128/27 | 255.255.255.224 | 30           | 25             |

# WAN Connections
Seven `/30` subnets were created for point-to-point WAN serial links.
* `/30` networks provide:
4 total IP addresses
2 usable host addresses
Ideal for router-to-router communication

---

# Router Configuration

## Routers Used

* R_Home
* R_Malcolm
* R_Francis
* R_Reese
* R_Hal
* R_Dewey

Each router required a **WIC-2T serial module** to enable serial interfaces before booting in Cisco Packet Tracer.


# Serial Connections
Routers were connected using **Serial DCE cables**.

The DCE routers:

* `R_Home`
* `R_Malcolm`

These routers were configured with:
clock rate 64000 to provide timing synchronization for the serial links.

# Hybrid Routing Architecture
## Central Router = `R_Malcolm` acts as the **central hub router** and the most critical device in the topology.

It connects directly to four routers and uses a **hybrid routing approach**:

### Dynamic Routing (RIPv2)

Used with:

* R_Hal
* R_Reese

### Static Routing

Used toward:

* R_Home
* R_Francis
* R_Dewey


# RIPv2 Configuration

Features used:

* Version 2
* `no auto-summary`
* Route redistribution

### Why `no auto-summary`?

Without disabling auto-summary, RIP would summarize all networks into classful `/8` boundaries, which would break VLSM subnet communication.

---

# Route Redistribution

`R_Malcolm` redistributes static routes into RIP using:

```bash
redistribute static metric 1
```

This allows RIP routers (`R_Hal` and `R_Reese`) to automatically learn all remote static networks without manually configuring routes.

---

# Passive Interfaces

Passive interfaces were configured on:

* R_Malcolm → R_Home
* R_Malcolm → R_Dewey

Purpose:

* Prevent unnecessary RIP updates
* Improve efficiency
* Increase security

---

# Static Routing Features

## R_Home Configuration

### Standard Static Route

Bidirectional static routes were configured between:

* Home
* Francis

### Default Route

A default route was configured:

```bash
0.0.0.0 0.0.0.0
```

Unknown traffic is forwarded to `R_Malcolm`.

---

# Floating Static Route

R_Home includes:

* Primary route to Dewey via direct link
* Backup floating static route via R_Malcolm

The floating route uses:

```text
Administrative Distance = 25
```

Behavior:

* Direct route (AD=1) is preferred
* If the direct link fails, the floating route becomes active
* Still preferred over RIP routes (AD=120)

---

# DHCP Configuration

## Static IP Zone

### Family Home

Uses only manually assigned static IP addresses.

---

## Router-Based DHCP

Configured on:

* Malcolm's Lab
* Reese's Kitchen Zone

Routers provide IP addresses using:

```bash
ip dhcp pool
```

---

## Server-Based DHCP

Configured on:

* Francis's Academy
* Hal's Workplace
* Dewey's Lab

Dedicated DHCP servers assign addresses.

Routers use:

```bash
ip helper-address
```

to forward DHCP broadcasts across router boundaries.

---

# DNS and Web Services

## WilkersonHub Server

IP Address:

```text
10.14.9.130
```

Services hosted:

* DNS Server
* Web Server

### Website Message

```text
"Life is unfair... but the network works!"
```

### DNS Resolution Examples

* `www.wilkersonhub.com`
* `malcolm.lab`

---

# Email Services

Email servers were configured in:

* Malcolm's Lab
* Dewey's Lab

Protocols used:

* SMTP
* POP3

Users can send emails between labs to verify successful inter-network communication.

---

# Connectivity Verification

The network was tested using:

* `ping`
* `show ip route`

Verification ensured:

* Full connectivity between all locations
* All six LAN networks appear in every routing table
* Proper RIP and static route operation
* Successful failover behavior
* Correct DHCP and DNS functionality

---

# Technologies Used

* Cisco Packet Tracer
* VLSM Subnetting
* Static Routing
* RIPv2
* Route Redistribution
* Floating Static Routes
* DHCP
* DNS
* SMTP/POP3 Email Services
* WAN Serial Communication



# Conclusion

This project demonstrates a complete hybrid enterprise network implementation combining:

* Efficient VLSM subnetting
* Static and dynamic routing
* Route redistribution
* DHCP architectures
* DNS and web hosting
* Email communication
* WAN redundancy and failover mechanisms

The topology successfully achieves scalable and reliable communication across all locations.
