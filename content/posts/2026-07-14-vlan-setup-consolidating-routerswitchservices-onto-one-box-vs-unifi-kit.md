---
title: "VLAN Setup Showdown: One‑Box Router/Switch vs. UniFi Kit – Real r/selfhosted Experiences & the Ultimate Guide"
date: 2026-07-14T05:02:52+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "Explore the real‑world pros and cons of consolidating router, switch, and services on a single box versus a UniFi kit – complete guide, configs, and community insights."
---

## The Community Spark  

In the last month the r/selfhosted front page has been flooded with the phrase **“VLAN setup”**. Users are wrestling with a classic dilemma: *Should I roll everything—router, L3 switch, DHCP, DNS, and services—into a single Linux‑based box, or invest in a Ubiquiti UniFi ecosystem (Dream Machine Pro + Switches) and let the hardware do the heavy lifting?*  

The conversation surged after a high‑upvoted post that asked for “real‑world performance and maintenance stories” from anyone who’s migrated from a DIY pfSense‑on‑a‑Raspberry‑Pi to a UniFi Dream Machine Pro (UDM‑Pro). The thread now has over 2 500 comments, dozens of screenshots, and a living checklist that members keep updating. That’s the pulse we’ll synthesize and turn into a concrete, step‑by‑step guide you can copy‑paste today.

---

## Synthesized Community Perspectives  

### What Everyone Agrees On  

| Consensus Point | Why It Matters |
|-----------------|----------------|
| **VLANs are non‑negotiable** | Home labs are no longer flat‑wired; IoT, guests, and homelab traffic need isolation. |
| **Documentation saves lives** | Users who kept a change‑log (e.g., in a Git repo) could roll back a mis‑configured VLAN in seconds. |
| **Power‑over‑Ethernet (PoE) matters** | Many IoT devices (security cameras, access points) draw power from the switch, influencing hardware choice. |
| **Future‑proofing > initial cost** | Community members who chose the cheapest option often hit a wall when adding more subnets. |

### The Core Debate  

| Aspect | One‑Box (pfSense/OPNsense on Mini‑PC) | UniFi Kit (UDM‑Pro + US‑8‑150W) |
|--------|--------------------------------------|---------------------------------|
| **Initial CAPEX** | $120–$250 for a NUC‑style PC, plus a cheap 5‑port managed switch. | $399 for UDM‑Pro + $199 per US‑8‑150W. |
| **Learning Curve** | Steeper – requires Linux networking, firewall rules, and manual VLAN tagging. | Shallower – UniFi UI abstracts most L2/L3 tasks. |
| **Flexibility** | Unlimited – you can run Docker, KVM, Pi‑hole, AdGuard Home, etc., on the same box. | Limited – UniFi OS is closed; third‑party services need separate hardware. |
| **Reliability** | Depends on OS stability; community reports ~99.7% uptime with proper backups. | Enterprise‑grade hardware, 99.95% uptime, built‑in failover. |
| **Performance** | CPU‑bound when running many services; 4‑core Intel NUC can handle ~1 Gbps NAT. | Dedicated ASIC for routing; handles >3 Gbps without CPU spikes. |
| **Support** | Community‑driven (forums, Reddit). | Official Ubiquiti support + active community. |

**Key takeaways from the thread:**  

* Users who **value total control** (e.g., running custom DNS over TLS, self‑hosted Nextcloud, Pi‑hole) gravitate toward the one‑box model.  
* Those who **prioritize plug‑and‑play reliability** and have a modest budget for PoE devices lean toward UniFi.  
* A hybrid approach—UniFi for core L2/L3, plus a dedicated “service box” for containers—was the most common recommendation for power users.

---

## Deep‑Dive Actionable Guide  

Below you’ll find **two complete reference implementations** that you can adapt to your own home lab. Both setups assume a /24 LAN (192.168.1.0/24) and three VLANs:

| VLAN ID | Name          | Subnet          |
|---------|---------------|-----------------|
| 10      | `iot`         | 192.168.10.0/24 |
| 20      | `guest`       | 192.168.20.0/24 |
| 30      | `servers`     | 192.168.30.0/24 |

> **Tip:** Store every configuration file in a Git repo (`git init home‑network && git add . && git commit -m "Initial commit"`). This satisfies the “Documentation” consensus and makes rollbacks painless.

### 1️⃣ One‑Box Solution (OPNsense on an Intel NUC)

#### Hardware Checklist  

| Item | Model | Approx. Cost |
|------|-------|--------------|
| Compute | Intel NUC 11 (i5‑1135G7, 8 GB RAM, 256 GB SSD) | $220 |
| Managed Switch | Netgear GS108T (8‑port, 802.1Q) | $65 |
| PoE Injector (if needed) | TP‑Link TL‑POE150S | $30 |

#### Step‑by‑Step Setup  

1. **Install OPNsense**  
   ```bash
   # Download the latest OPNsense ISO
   curl -L -o OPNsense-23.7-OpenSSL-dvd-amd64.iso https://downloads.opnsense.org/releases/23.7/OPNsense-23.7-OpenSSL-dvd-amd64.iso
   # Write to USB (Linux/macOS)
   sudo dd if=OPNsense-23.7-OpenSSL-dvd-amd64.iso of=/dev/sdX bs=4M status=progress && sync
   ```  
   Boot from USB, choose “Install (UFS)” and accept defaults. Assign **WAN** to the NIC that connects to your ISP modem and **LAN** to the NIC that will link to the Netgear switch.

2. **Create VLAN Interfaces** (Web UI → *Interfaces → Other Types → VLAN*)  

   For each VLAN ID, bind it to the LAN physical interface (e.g., `igb0`). Example for VLAN 10:  

   ```yaml
   interface: igb0
   vlan_tag: 10
   description: "VLAN 10 – IoT"
   ```

3. **Assign VLAN Interfaces** (Interfaces → Assignments)  

   - `LAN` → `LAN` (192.168.1.1/24) – management network.  
   - `VLAN10` → `VLAN10` (192.168.10.1/24) – DHCP enabled, DNS forwarder disabled (use Pi‑hole).  
   - `VLAN20` → `VLAN20` (192.168.20.1/24) – captive‑portal for guests (optional).  
   - `VLAN30` → `VLAN30` (192.168.30.1/24) – static routes only, hosts run Docker.

4. **Configure DHCP** (Services → DHCP Server) for each VLAN. Example for VLAN 10:  

   ```text
   Range: 192.168.10.100 – 192.168.10.200
   DNS Servers: 127.0.0.1 (Pi‑hole)
   ```

5. **Inter‑VLAN Routing Rules** (Firewall → Rules → [VLAN])  

   - **IoT → Internet**: Allow `any → any` (but block IoT → Servers).  
   - **Guest → Internet**: Allow `any → any`, *log* all traffic.  
   - **Servers → All**: Allow full, but **deny** IoT → Servers.  

   Example rule (VLAN 10 → any):  

   ```yaml
   action: pass
   interface: VLAN10
   source: any
   destination: any
   protocol: any
   description: "IoT devices may reach internet, not servers"
   ```

6. **Deploy Services on the Same Box** (Docker Compose example for Pi‑hole & AdGuard Home):  

   ```yaml
   version: "3.8"
   services:
     pihole:
       image: pihole/pihole:latest
       container_name: pihole
       environment:
         TZ: "America/Los_Angeles"
         WEBPASSWORD: "securepassword"
       volumes:
         - ./pihole/etc-pihole:/etc/pihole
         - ./pihole/etc-dnsmasq.d:/etc/dnsmasq.d
       ports:
         - "53:53/tcp"
         - "53:53/udp"
         - "80:80"
       restart: unless-stopped
   ```

   Bind the container to the `VLAN10` bridge (create a Docker network with `--subnet=192.168.10.0/24`).

7. **Back‑up Config** (System → Configuration → Backups) and push to Git:  

   ```bash
   opnsense-backup > /home/backup/opnsense-config.xml
   git add .
   git commit -m "Backup after VLAN config"
   ```

#### Validation  

- Verify VLAN tagging on the switch: `show vlan` (Netgear).  
- Ping across subnets: `ping -c 3 192.168.30.1` from an IoT device (should be blocked).  
- Use `tcpdump -i igb0 vlan 10` to confirm traffic is correctly tagged.

---

### 2️⃣ UniFi Kit Solution (UDM‑Pro + US‑8‑150W)

#### Hardware Checklist  

| Item | Model | Approx. Cost |
|------|-------|--------------|
| Router + L3 Switch | Ubiquiti Dream Machine Pro (UDM‑Pro) | $399 |
| PoE Switch | UniFi US‑8‑150W (8‑port PoE+) | $199 |
| Optional – EdgeRouter for DMZ | Ubiquiti EdgeRouter 4 | $149 |

#### Step‑by‑Step Setup  

1. **Adopt Devices in UniFi Network Controller**  

   - Connect UDM‑Pro to your ISP modem via WAN port.  
   - Plug US‑8‑150W into one of the UDM‑Pro’s LAN ports.  
   - Launch the UniFi Network app (or UI at `https://<UDM‑Pro IP>`) and adopt the switch automatically.  

2. **Create VLANs** (Settings → Networks → Create New Network)  

   - **VLAN 10 – IoT**: Enable VLAN, set ID 10, subnet 192.168.10.0/24, DHCP enabled.  
   - **VLAN 20 – Guest**: ID 20, subnet 192.168.20.0/24, enable *Guest Control* (captive portal).  
   - **VLAN 30 – Servers**: ID 30, subnet 192.168.30.0/24, DHCP disabled (static only).  

3. **Port Profile Mapping** (Devices → Switch → Ports)  

   - **Port 1 (Uplink)**: Set as *All (Tagged)* – carries every VLAN.  
   - **Port 2 (IoT devices)**: Profile **VLAN 10 (Tagged + Untagged)** if you have a mix of PoE APs and plain devices.  
   - **Port 3 (Guest)**: Profile **VLAN 20 (Untagged)** – easy plug‑and‑play for visitor laptops.  
   - **Port 4–8**: Set to *All* or individual VLANs depending on device placement.  

4. **Inter‑VLAN Firewall Rules** (Settings → Traffic & Security → Firewall)  

   - **Rule 1:** `Allow LAN → All` (default).  
   - **Rule 2:** `Block VLAN10 → VLAN30` (IoT cannot reach servers).  
   - **Rule 3:** `Allow VLAN20 → Internet` (guest).  
   - **Rule 4:** `Deny VLAN20 → LAN & VLAN30` (isolation).  

   Example JSON representation for Rule 2 (exportable via UI):  

   ```json
   {
     "action": "drop",
     "protocol": "all",
     "src_network_id": "vlan10",
     "dst_network_id": "vlan30",
     "log": true,
     "description": "Block IoT → Servers"
   }
   ```

5. **Deploy Services on a Separate Box (Optional)**  

   Many community members run **TrueNAS SCALE** or a **Proxmox** node for containers. Connect that node to **Port 5** on the US‑8‑150W, assign it to **VLAN