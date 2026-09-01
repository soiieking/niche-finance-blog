---
title: 'Passing NVIDIA GPUs to Proxmox LXC Containers: The Ultimate r/selfhosted Guide'
date: '2026-07-25T11:02:41+08:00'
draft: false
tags:
- selfhosted
- vps
- linux
- technology
summary: 'A community-focused analysis exploring the recent discussions and practical
  insights regarding Passing NVIDIA GPUs to Proxmox LXC Containers: The Ultimate r/selfhosted
  Guide.'
---

## The Community Spark: Why GPU Passthrough in LXC is Trending
Recently, the r/selfhosted community has been buzzing about a specific architectural challenge: running hardware-accelerated workloads (like Jellyfin, Plex, or Frigate) in Proxmox. While full Virtual Machines (VMs) have well-documented PCI passthrough, users are increasingly turning to Linux Containers (LXC) for their minimal overhead. 
The core problem? NVIDIA's proprietary drivers aggressively guard GPU access. Passing a GPU to an LXC container isn't natively intuitive. The community consensus is clear: doing this incorrectly results in `nvidia-smi` throwing "No devices were found" errors, wasting hours of troubleshooting.
## Synthesized Community Perspectives
Diving into the Reddit threads, a few key perspectives emerged:
**The "Unprivileged is the Only Way" Camp:** Veteran r/selfhosted users strongly advocate against privileged containers. While privileged containers make GPU access trivially easy, they expose the host root filesystem to the container. The community consensus is to stick to unprivileged containers and use `cgroup` device permissions.
**The Host/Container Driver Match Debate:** A common pitfall highlighted by users is driver mismatch. If your Proxmox host uses NVIDIA driver 535, but your container installs 545, the GPU will fail to initialize. The community strongly recommends downloading the exact same `.run` or `.deb` files for both host and container, or leveraging the RPM Fusion/Debian repositories to lock versions.
**The `lxc.mount.entry` vs. `lxc.cgroup2` Approach:** Older tutorials relied heavily on `lxc.mount.entry` to manually bind `/dev/nvidia0`. Modern community discussions point out that with Proxmox 8+ and cgroups v2, properly configuring `lxc.cgroup2.devices.allow` is the cleaner, more persistent method.
## Deep-Dive Actionable Guide: NVIDIA GPU in Proxmox LXC
Based on the synthesized community fixes, here is the definitive, modern step-by-step guide for passing an NVIDIA GPU to an unprivileged LXC container.
### Step 1: Install Host Drivers
Install the NVIDIA drivers on the Proxmox host. Do not run `nvidia-smi` in a container until this works on the host.
```bash
apt update
apt install nvidia-driver nvidia-utils
reboot
```
### Step 2: Identify Device IDs
Find the major and minor device numbers for your NVIDIA hardware. The community reminds us that you need permissions for the card, the control device, and the UVM (Unified Virtual Memory) device.
```bash
ls -l /dev/nvidia* /dev/nvidia-uvm
# Expected output groups (major, minor):
# nvidia0    195,   0
# nvidiactl  195, 255
# nvidia-uvm 510,   0
```
### Step 3: Configure the LXC Container
Edit your container's configuration file on the Proxmox host (replace `100` with your container ID).
```bash
nano /etc/pve/lxc/100.conf
```
Add the following lines to grant cgroup access and bind mount the devices:
```text
lxc.cgroup2.devices.allow: c 195:0 rwm
lxc.cgroup2.devices.allow: c 195:255 rwm
lxc.cgroup2.devices.allow: c 510:0 rwm
lxc.mount.entry: /dev/nvidia0 dev/nvidia0 none bind,optional,create=file
lxc.mount.entry: /dev/nvidiactl dev/nvidiactl none bind,optional,create=file
lxc.mount.entry: /dev/nvidia-uvm dev/nvidia-uvm none bind,optional,create=file
```
### Step 4: Install Container Drivers
Start your container and install the *exact same* NVIDIA driver version inside the container. Crucially, you do not need the kernel modules inside the container, just the userspace libraries.
```bash
apt update
apt install nvidia-utils
```
## Pros & Cons: LXC vs. VM GPU Passthrough
| Feature | Proxmox LXC Container | Proxmox VM |
| :--- | :--- | :--- |
| **Overhead** | Minimal (near bare-metal) | Higher (full OS virtualization) |
| **Setup Difficulty** | Moderate (manual cgroup config) | Easy (Native PCI Passthrough GUI) |
| **Driver Matching** | Strict (Host & Container must match) | Independent (VM has its own kernel) |
| **Resource Sharing** | Can share GPU with host easily | Requires complex VFIO/MIG setup |
| **Security** | Good (if unprivileged) | Excellent (Hardware isolation) |
## The Verdict / Expert Advice
If you are deploying a standalone media server like Plex or Frigate, **use an LXC container**. The resource savings are significant, and once the `cgroup` configuration is understood, it is highly stable. 
However, if you are running an AI/ML training box where you need to frequently swap out CUDA toolkits, kernel modules, or experiment with different driver versions, **stick to a VM**. The isolation of a VM's own kernel means you can upgrade NVIDIA drivers inside the VM without risking the stability of your Proxmox hypervisor.
## Frequently Asked Questions (FAQ)
**Does Netflix/Plex require a GPU in LXC?**
No, but hardware transcoding via Intel QuickSync or NVIDIA NVENC significantly reduces CPU usage when streaming to devices that require different codecs. It is highly recommended for larger self-hosted media deployments.
**Can I share one NVIDIA GPU across multiple containers?**
Yes. Because LXC shares the host's kernel, as long as all containers have the correct `cgroup` permissions and bind mounts, they can access the same GPU simultaneously. Memory is shared, so monitor VRAM usage to prevent out-of-memory crashes.
**Why does `nvidia-smi` show "No devices were found" in my container?**
This usually means either the host drivers aren't loaded, the `lxc.cgroup2.devices.allow` major/minor numbers are incorrect, or the container's driver version does not match the host's driver version exactly.
**Is it better to use privileged containers for easier GPU access?**
No. The r/selfhosted community strongly advises against it. While a privileged container makes `/dev` instantly accessible, it strips away the security boundaries of the container, making it a massive security risk.
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Does Netflix/Plex require a GPU in LXC?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "No, but hardware transcoding via Intel QuickSync or NVIDIA NVENC significantly reduces CPU usage when streaming to devices that require different codecs. It is highly recommended for larger self-hosted media deployments."
      }
    },
    {
      "@type": "Question",
      "name": "Can I share one NVIDIA GPU across multiple containers?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes. Because LXC shares the host's kernel, as long as all containers have the correct cgroup permissions and bind mounts, they can access the same GPU simultaneously. Memory is shared, so monitor VRAM usage to prevent out-of-memory crashes."
      }
    },
    {
      "@type": "Question",
      "name": "Why does nvidia-smi show 'No devices were found' in my container?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "This usually means either the host drivers aren't loaded, the lxc.cgroup2.devices.allow major/minor numbers are incorrect, or the container's driver version does not match the host's driver version exactly."
      }
    },
    {
      "@type": "Question",
      "name": "Is it better to use privileged containers for easier GPU access?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "No. The r/selfhosted community strongly advises against it. While a privileged container makes /dev instantly accessible, it strips away the security boundaries of the container, making it a massive security risk."
      }
    }
  ]
}
</script>
