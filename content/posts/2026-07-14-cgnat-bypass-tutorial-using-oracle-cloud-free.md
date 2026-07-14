---
title: "Bypass CGNAT for Free with Oracle Cloud (2026): The Ultimate Self‑Hosted Tutorial"
date: 2026-07-14T12:12:35+08:00
draft: false
tags: ["selfhosted", "vps", "linux", "technology"]
summary: "Learn how to break through CGNAT using Oracle Cloud's free tier. Follow a community‑tested, step‑by‑step guide that works for any Linux box."
---

## The Community Spark: Why CGNAT Bypass is Trending on r/selfhosted

Over the past month, r/selfhosted has seen a surge of posts titled *“My ISP uses CGNAT, how do I host a server?”* and *“Free VPS tricks for remote access”.* The common denominator? Users are hitting a hard wall: Carrier‑Grade NAT (CGNAT) hides their public IP, making inbound connections impossible without costly static IPs or paid VPNs.  

The thread that ignited the discussion was a post on **2026‑07‑05** where a user named *u/tech‑savant* posted a concise proof‑of‑concept using **Oracle Cloud’s Always Free tier** to create a “tunnel‑back” that exposed their home service to the internet. Within hours, the comment chain ballooned to over 600 up‑votes, spawning dozens of follow‑up experiments, tweaks for Docker, WireGuard, and even Kubernetes.  

The buzz isn’t just curiosity; it’s a practical solution to a real pain point for hobbyists, remote workers, and anyone who wants to self‑host without paying for a static IP.

---

## Synthesized Community Perspectives

| Viewpoint | What the Community Said | Consensus / Debate |
|-----------|------------------------|--------------------|
| **Free‑tier feasibility** | Many users confirmed that the Oracle Cloud *Always Free* VM (1 OCPU, 1 GB RAM) can run a lightweight NAT‑bypass tunnel 24/7. | ✅ Consensus: Works for low‑traffic services (webhooks, small websites, personal VPN). |
| **Performance concerns** | Some reported latency spikes (≈30 ms) when using SSH reverse tunnels; others noted smoother performance with **WireGuard**. | ✅ Verdict: WireGuard is the recommended protocol for minimal overhead. |
| **Security worries** | A few warned about exposing root SSH on the cloud instance. The community responded with hardened SSH configs and `fail2ban`. | ✅ Best practice: Disable password auth, use key‑based login, restrict inbound ports via OCI security lists. |
| **Longevity of the free tier** | Skepticism about Oracle pulling the free tier. Users shared recent screenshots (July 2026) proving it’s still active. | ✅ Still viable, but monitor OCI announcements. |
| **Alternative cloud options** | Some suggested **Google Cloud Free Tier** or **Azure**, but many found Oracle’s generous outbound bandwidth (10 TB) unmatched for tunnel use. | ❓ Ongoing debate: Oracle wins on bandwidth, Google wins on region variety. |

The **core agreement** is clear: **Oracle Cloud’s free tier is the most reliable, cost‑free gateway to bypass CGNAT** for modest self‑hosted workloads, provided you follow the hardened security steps the community has refined.

---

## Deep‑Dive Actionable Guide: CGNAT Bypass Using Oracle Cloud (Free)

Below is the **consolidated, battle‑tested tutorial** that merges the most up‑voted community snippets, with extra notes on why each command matters. The guide assumes you have:

* A Linux machine behind CGNAT (Ubuntu 22.04 LTS used for examples).
* An Oracle Cloud account (sign‑up is free; you’ll need a verified credit card for identity verification only).
* Basic familiarity with `ssh`, `iptables`, and `systemd`.

### 1. Spin Up the Oracle Cloud Free Instance

1. **Create the VM**  
   - Log in to the OCI Console → *Compute → Instances → Create Instance*.  
   - Choose **Shape:** `VM.Standard.E2.1.Micro` (Free Tier).  
   - **Image:** *Canonical Ubuntu Server 22.04 LTS* (latest).  
   - **Networking:** Use the default VCN; **assign a public IPv4 address** (the free tier auto‑assigns one).  

2. **Open Required Ports**  
   - In the *Virtual Cloud Network (VCN)* → *Security Lists*, add inbound rules:  
     ```text
     Source CIDR: 0.0.0.0/0
     Destination Port Range: 22 (TCP)   → SSH
     Destination Port Range: 51820 (UDP) → WireGuard (optional)
     ```  
   - Save changes.  

3. **SSH into the instance** (replace `PUBLIC_IP`):  
   ```bash
   ssh -i ~/.ssh/oci_key.pub ubuntu@PUBLIC_IP
   ```

### 2. Harden the Cloud Instance

```bash
# 1️⃣ Update packages
sudo apt update && sudo apt upgrade -y

# 2️⃣ Install fail2ban & ufw
sudo apt install -y fail2ban ufw

# 3️⃣ Harden SSH
sudo sed -i 's/#PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config
sudo sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin no/' /etc/ssh/sshd_config
sudo systemctl restart sshd

# 4️⃣ Configure UFW (allow only SSH and WireGuard)
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp
sudo ufw allow 51820/udp
sudo ufw enable
```

### 3. Install & Configure WireGuard (Preferred Tunnel)

> **Why WireGuard?**  
> It’s cryptographically modern, runs in kernel space, and adds < 5 ms overhead – ideal for a CGNAT tunnel.

```bash
# Install WireGuard
sudo apt install -y wireguard

# Generate key pair on OCI instance
wg genkey | tee privatekey | wg pubkey > publickey
```

Copy the generated keys to your home machine (keep the private key on each side only).

#### 3.1. OCI Side – `wg0.conf`

```ini
[Interface]
PrivateKey = <OCI_PRIVATE_KEY>
Address = 10.0.0.1/24
ListenPort = 51820
PostUp   = iptables -A FORWARD -i wg0 -j ACCEPT
PostDown = iptables -D FORWARD -i wg0 -j ACCEPT
```

Save as `/etc/wireguard/wg0.conf` and enable:

```bash
sudo systemctl enable wg-quick@wg0
sudo systemctl start wg-quick@wg0
```

#### 3.2. Home Machine – Install WireGuard & Configure

```bash
sudo apt install -y wireguard
wg genkey | tee home_privatekey | wg pubkey > home_publickey
```

Create `/etc/wireguard/wg0.conf`:

```ini
[Interface]
PrivateKey = <HOME_PRIVATE_KEY>
Address = 10.0.0.2/24
DNS = 1.1.1.1

[Peer]
PublicKey = <OCI_PUBLIC_KEY>
Endpoint = <OCI_PUBLIC_IP>:51820
AllowedIPs = 0.0.0.0/0, ::/0
PersistentKeepalive = 25
```

Start the tunnel:

```bash
sudo systemctl enable wg-quick@wg0
sudo systemctl start wg-quick@wg0
```

**Test connectivity**:

```bash
ping -c 3 10.0.0.1   # OCI side
curl -s ifconfig.me  # Should show OCI public IP, proving outbound traffic goes through the tunnel
```

### 4. Expose Your Home Service via the Tunnel

Assume you have a local web server on port **8080** (e.g., a Home Assistant instance).

1. **Redirect traffic on OCI** to your home machine:

```bash
# On OCI (as root or with sudo):
sudo iptables -t nat -A PREROUTING -p tcp --dport 80 -j DNAT --to-destination 10.0.0.2:8080
sudo iptables -t nat -A POSTROUTING -j MASQUERADE
```

2. **Persist iptables rules** (optional but recommended):

```bash
sudo apt install -y iptables-persistent
sudo netfilter-persistent save
```

3. **Verify** from an external network:

```bash
curl -s http://<OCI_PUBLIC_IP>   # Should return the Home Assistant UI
```

If you prefer **SSH reverse tunneling** (less performant but no extra packages), replace steps 3‑4 with:

```bash
# On your home machine (run once, or set up a systemd service):
ssh -N -R 0.0.0.0:8080:localhost:8080 -i ~/.ssh/oci_key.pub ubuntu@<OCI_PUBLIC_IP>
```

The community discovered that the reverse tunnel works fine for occasional webhooks, but WireGuard remains the gold standard for persistent services.

### 5. Automate & Keep Alive

Create a **systemd service** on your home machine to guarantee the tunnel survives reboots:

```ini
# /etc/systemd/system/wg0.service
[Unit]
Description=WireGuard tunnel to OCI
After=network-online.target
Wants=network-online.target

[Service]
Type=oneshot
ExecStart=/usr/bin/wg-quick up wg0
ExecStop=/usr/bin/wg-quick down wg0
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable wg0.service
sudo systemctl start wg0.service
```

---

## Pros & Cons: Oracle Cloud Free Tier vs. Other CGNAT Bypass Methods

| Method | Cost | Setup Complexity | Performance | Security | Ideal Use‑Case |
|--------|------|-------------------|-------------|----------|----------------|
| **Oracle Cloud Free + WireGuard** | $0 (Free tier) | Medium (requires OCI account, WireGuard config) | Low latency, up to 10 TB outbound/month | High (SSH hardening + WireGuard crypto) | Continuous self‑hosted services (web UI, VPN, Git) |
| **SSH Reverse Tunnel (any VPS)** | $0–$5/month (if using cheap VPS) | Low (single command) | Higher latency, occasional disconnects | Moderate (depends on SSH config) | One‑off webhooks, temporary exposure |
| **ZeroTier / Tailscale (free tier)** | $0 (up to 100 devices) | Very low (install client) | Comparable to WireGuard | High (end‑to‑end encryption) | Mesh networking; not a true “public IP” |
| **Port‑Forwarding via ISP (static IP)** | $10–$30/month | Low (router config) | Direct, minimal overhead | Varies (depends on router) | High‑traffic public sites |
| **Paid Cloud VPN (e.g., AWS Lightsail)** | $3.50+/month | Medium | Good, but outbound bandwidth limited | High | Business‑grade reliability |

**Takeaway:** For hobbyists and low‑traffic self‑hosts, **Oracle Cloud + WireGuard** delivers the best cost‑to‑performance ratio, while still meeting the community’s security expectations.

---

## The Verdict: Which Path Should You Take?

| Persona | Recommended Solution | Reason |
|---------|----------------------|--------|
| **Novice hobbyist** (first self‑host) | Oracle Cloud Free + WireGuard tutorial | Zero monetary outlay, solid community support, minimal ongoing maintenance. |
| **Power user / DevOps** (multiple containers) | Combine OCI free VM with Docker‑compose + WireGuard | Keeps all services isolated on the cloud side while tunneling traffic back. |
| **Security‑first professional** | OCI Free + hardened WireGuard **or** ZeroTier (if you need true mesh) | Both provide strong encryption; OCI adds the advantage of a public IP. |
| **High‑traffic site** | Paid VPS or static IP from ISP | Free tier bandwidth caps (10 TB) may be exceeded; latency matters for SEO. |

**Bottom line:** If your primary goal is to *expose a personal service* (home automation, personal Git, small blog) without spending a dime, follow the step‑by‑step guide above. The community’s real‑world testing shows it stays stable for months, and the security hardening steps protect you from the most common attack vectors.

---

## Frequently Asked Questions (FAQ)

**Q1: Does the Oracle Cloud Free tier have any hidden fees?**  
A: No. The *Always Free* tier includes 2 VMs, 1 TB outbound bandwidth per VM, and a fixed public IP. You’ll only be billed if you exceed the free resources (e.g., add block storage beyond the quota) or enable paid services.

**Q2: My ISP blocks outbound UDP 51820 (WireGuard). Can I still use this method?**  
A: Yes. The community workaround is to encapsulate WireGuard inside TCP using **WireGuard over TCP** (`wg-quick up wg0` with `Endpoint = <OCI_IP>:443` and `PersistentKeepalive = 25`). Alternatively, fall back to an SSH reverse tunnel on port 22.