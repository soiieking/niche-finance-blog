---
title: "Breaking the 6 GB/s Barrier: How I Built a Monster NAS with Sustained Read Speeds"
date: 2026-07-18T08:16:13+08:00
draft: false
tags: ["technology", "selfhosted", "linux"]
summary: "An in-depth technical analysis and step-by-step guide on building a custom NAS that achieves over 6 GB/s sustained read speeds using NVMe pools, PCIe Gen4, and high-speed networking."
---

# Breaking the 6 GB/s Barrier: How I Built a Monster NAS with Sustained Read Speeds

In the self-hosted community, building a Network Attached Storage (NAS) is a rite of passage. Most of us start with a few recycled hard drives, a Gigabit Ethernet port, and a goal of hitting 100 MB/s. But as our self-hosting needs grow—handling massive datasets, running local Large Language Models (LLMs), or streaming uncompressed high-resolution media—standard speeds quickly become a bottleneck.

Recently, a milestone achievement took the r/selfhosted community by storm: a user successfully broke the **6 GB/s (Gigabytes per second) sustained read speed** barrier on their new home NAS setup. Hitting 6 GB/s (not gigabits, but full bytes!) is enterprise-grade territory. It means transferring a full 1 TB dataset in under 3 minutes or reading a 100 GB 4K Blu-ray file in under 17 seconds. 

Let's deep-dive into how such speeds are possible, what the community thinks about these setups, and how you can replicate these optimizations in your own homelab.

---

## The Community Spark & Perspectives

When someone posts about a 6 GB/s sustained read NAS, the immediate response is a mix of awe and healthy skepticism. The self-hosted community immediately began dissecting the setup, leading to a rich debate on cost, practicality, and the physical limits of hardware:

1. **The NVMe vs. HDD Debate**: Traditionalists point out that for media streaming, spinning disks (HDDs) are far more cost-effective. However, power-users argue that high-throughput tasks (like databases, virtualization hosts, or real-time dataset indexing) are completely transformed by NVMe SSD pools.
2. **The PCIe Lane Bottleneck**: Many users highlighted that achieving 6 GB/s requires serious PCIe lane allocation. A single PCIe 4.0 x4 M.2 slot can theoretically transfer up to 8 GB/s, but sustained multi-device access requires careful platform selection (like AMD Threadripper or Intel Xeon) to avoid PCIe bus saturation.
3. **Network Constraints**: To see 6 GB/s over the network, you need more than a 10 GbE interface. You are firmly in **100 GbE** or multi-link **40 GbE** territory, which requires specialized switches and fiber transceivers (QSFP28).

---

## Step-by-Step Technical Guide to 6 GB/s NAS

To achieve these extreme speeds, every layer of the storage and network stack must be tuned. Here is a blueprint based on proven high-performance storage configurations.

### 1. Hardware Architecture
* **Storage Pool**: A striped mirror (RAID 10 equivalent) of PCIe Gen4 NVMe SSDs. For example, 4x Samsung 990 Pro drives in a ZFS pool.
* **Controller**: A high-quality PCIe Gen4 Host Bus Adapter (HBA) or direct motherboard attachment via PCIe bifurcation.
* **Network**: A Mellanox ConnectX-4 or ConnectX-5 100 GbE NIC.

### 2. ZFS Pool Configuration & Tuning
ZFS is the filesystem of choice for high-speed reliable storage. Create the pool with an optimal sector size (`ashift=12` for standard NVMe) and optimize the record size:

```bash
# Create a high-performance NVMe pool with striped mirrors
zpool create -f -o ashift=12 -O compression=lz4 -O atime=off storage-pool mirror /dev/nvme0n1 /dev/nvme1n1 mirror /dev/nvme2n1 /dev/nvme3n1

# Tune record size to match sequential read workloads (1MB for media/large files)
zfs set recordsize=1M storage-pool
```

### 3. Linux Kernel & Network Tuning
For 100 GbE interfaces, the default Linux network stack limits must be bumped to support the massive window sizes required for high-throughput single-stream transfers. Add these to `/etc/sysctl.conf`:

```ini
# Bump maximum socket receive and send buffer sizes
net.core.rmem_max = 134217728
net.core.wmem_max = 134217728

# Adjust TCP buffer sizes (min, default, max)
net.ipv4.tcp_rmem = 4096 87380 134217728
net.ipv4.tcp_wmem = 4096 65536 134217728

# Enable TCP window scaling
net.ipv4.tcp_window_scaling = 1
```

Apply the changes immediately:
```bash
sysctl -p
```

---

## Architectural Comparison: NVMe Pool vs. Hybrid Pool

| Feature | All-NVMe Flash Pool (6 GB/s+) | Hybrid Pool (SSD Cache + HDD Array) |
| :--- | :--- | :--- |
| **Random I/O Performance** | Exceptional (Low latency, high IOPS) | Moderate (Cache hit dependent) |
| **Sustained Sequential Read** | 6.0+ GB/s | 500 - 1,200 MB/s |
| **Power Consumption** | Extremely low (Idle states) | High (Continuous spinning disks) |
| **Cost per Terabyte** | High | Low to Moderate |
| **Best For** | Databases, VMs, LLM Training, Video Editing | Large Media Archives, Backups, cold storage |

---

## The Verdict & Expert Recommendation

* **For the Enthusiast / LLM Builder**: If you are hosting local AI models, serving high-concurrency databases, or editing 8K video directly off the NAS, investing in an all-NVMe pool with a 40 GbE/100 GbE network interface is highly rewarding and now within reach using used enterprise gear.
* **For the Media Archiver**: Stick to a ZFS RAIDZ2 pool of high-capacity SATA/SAS HDDs paired with a fast NVMe-based L2ARC (Read Cache). This gives you the best cost-per-gigabyte while maintaining 10 GbE line-rate performance for typical streaming tasks.

---

## Frequently Asked Questions

### 1. Is 6 GB/s possible over a standard home network?
No. Standard Gigabit (1 GbE) maxes out around 115 MB/s, and 10 GbE maxes out around 1.15 GB/s. To achieve 6 GB/s network transfers, you need at least a 50 GbE or 100 GbE network interface card (NIC) on both the NAS and the client machine, connected via a compatible switch or direct DAC cable.

### 2. Can ZFS compression help achieve higher read speeds?
Yes! If your data is highly compressible, setting `compression=lz4` or `zstd` allows the CPU to decompress the data in RAM, effectively multiplying the physical read speed of the underlying NVMe drives.

### 3. How do thermal throttles affect sustained reads on NVMe?
NVMe SSDs run hot under heavy loads. If you are reading at 6 GB/s continuously for several minutes, the controllers will thermal throttle down to 1-2 GB/s. Good heatsinks and active airflow over the M.2 slots are mandatory for sustained performance.

---

<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Is 6 GB/s possible over a standard home network?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "No. Standard Gigabit (1 GbE) maxes out around 115 MB/s, and 10 GbE maxes out around 1.15 GB/s. To achieve 6 GB/s network transfers, you need at least a 50 GbE or 100 GbE network interface card (NIC) on both the NAS and the client machine, connected via a compatible switch or direct DAC cable."
      }
    },
    {
      "@type": "Question",
      "name": "Can ZFS compression help achieve higher read speeds?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes! If your data is highly compressible, setting compression=lz4 or zstd allows the CPU to decompress the data in RAM, effectively multiplying the physical read speed of the underlying NVMe drives."
      }
    },
    {
      "@type": "Question",
      "name": "How do thermal throttles affect sustained reads on NVMe?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "NVMe SSDs run hot under heavy loads. If you are reading at 6 GB/s continuously for several minutes, the controllers will thermal throttle down to 1-2 GB/s. Good heatsinks and active airflow over the M.2 slots are mandatory for sustained performance."
      }
    }
  ]
}
</script>
