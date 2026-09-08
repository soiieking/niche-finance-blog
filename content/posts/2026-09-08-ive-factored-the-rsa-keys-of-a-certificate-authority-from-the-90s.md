---
title: How I Factored RSA Keys from a 90’s Certificate Authority for Fun (and Chaos)
date: '2026-09-08 12:00:03+08:00'
draft: false
tags:
- indie-hacker
- security
- retro-computing
summary: Reverse-engineering the irreversibly broken crypto of the 90s. Here's the
  why, the how, and the hilarious mess along the way.
---

Every once in a while, you stumble across an idea so niche, so impractical, that you just *have* to do it. Factorizing RSA keys from a legacy 90’s Certificate Authority? That's vintage internet chaos.

This post isn’t just about nerd flexing. It’s about low-key proving how fragile early encryption was—and why no one should touch certificates that predate the Y2K panic. Let’s break cryptography the way you would in 1998, with the tools and compute power of 2026.

If you’re weirdly into dead CAs or just want to re-enact some crypto archeology, here’s a step-by-step sneak peek into how I factored the keys. Spoiler alert: it’s easier than you think, thanks to years of Moore's Law and one spicy factoring algorithm.

---

## A Quick Primer on Why 512-bit RSA is Useless Now

RSA keys under 2048 bits should come with a bumper sticker that says "Hack Me." Anything 1024 bits or less has been toast for over a decade. But back in the 90s, 512-bit encryption was “secure”—if you define secure as "nobody has $10M worth of compute to throw at it."

Today, breaking 512-bit RSA takes pocket change and a lazy afternoon. Specifically, you can use the Number Field Sieve (NFS) algorithm, which math nerds have optimized into a finely tuned weapon against old-school crypto.

By today’s standards, cryptographically weak CAs are the equivalent of leaving your vault door open with a “please don’t steal my crown jewels” sign. And yet, some of these certificates are *still floating around* in old equipment or forgotten systems. Yikes.

> Comment from r/sideproject: *"I found a 512-bit RSA key in an old Xerox printer firmware. That firmware also included root credentials. Security through obscurity at its finest!"*

---

## Breaking the RSA Keys: Tools and Setup

Here’s what I used to factor my 90’s CA key. Nothing fancy—this setup is accessible to anyone mildly determined.

### My Environment
- **OS:** Linux Mint 21.2 “Victoria” (but any Linux distro will work)
- **Hardware:** Ryzen 5700X, 32GB RAM (overkill, but hey, it was in the corner already)
- **Software:** [msieve](https://github.com/radii/msieve) and [yafu](https://sourceforge.net/projects/yafu/)
- **RSA Key:** Snagged from an archived repo of 90’s certs scavenged off floppy images. It was a 512-bit public key. Not naming specifics here because... reasons.

---

### 1. Install the Required Tools
First, get the software up and running. For msieve and yafu, you can either compile from source or grab prebuilt binaries.

```bash
# Install msieve via git
git clone https://github.com/radii/msieve.git
cd msieve
make all

# Install yafu (optional, for tasks msieve struggles with)
sudo apt install yafu
```

Note: On ARM devices (Raspberry Pi, etc.), the compilation might throw quirks. Haven’t tested extensively, so YMMV.

---

### 2. Feed the Public Key to Msieve
Msieve is the MVP here. If you’ve got the modulus (the big 'n' number in RSA), you can kick off the factoring hunt.

Here’s the workflow:

1. Save your RSA public modulus in a plaintext file. Example:
    ```
    n=12345678909876543210987654321098765432109876543210 (insert your actual modulus here)
    ```

2. Run msieve:
    ```bash
    ./msieve -v <modulus_file.txt>
    ```

Msieve will run through its stages—trial division, polynomial selection, sieving, and matrix solving. This typically takes a few hours for a 512-bit key on a modern CPU, but on my Ryzen machine, it was done in ~3 hours.

---

### 3. Verify the Factors
Once done, msieve spits out two prime numbers—the private keys' building blocks. You can test them:

```bash
python3 -c "print((factor1 * factor2) == modulus)"
```

If True, congrats—you’ve now shattered 90’s cryptography.

> Pro Tip: Want speed? Offload the sieving step to GPUs. People reportedly use NVIDIA cards to cut this step down drastically, though I personally stuck to CPU.

---

## Lessons from this Experiment

So, was this practical? God no. Fun? Hell yes. Here’s what I learned (and re-learned) in this rabbit hole:

- **Crypto ages like milk.** Certificates that were “secure” in 1997 are useless today. If you’re still running legacy hardware or software (IoT world, I’m looking at you), assume any sub-2048-bit key is compromised.
  
- **Modern tools make this trivial.** I’m recklessly bad at math and yet broke 512-bit keys in an evening. If I can do this, anyone can.

- **Broken CA keys can cascade.** If a CA’s root certificate is weak, any certificate issued by it is suspect. Chains of trust are only as strong as their weakest link.

---

## FAQ

### Why does RSA use such big numbers?
RSA’s security depends on the difficulty of factoring large primes. The larger the prime, the harder it is to break. Back in the 90s, 512-bit primes were considered safe. By 2005, that assumption was dead.

### Can I use this on 1024-bit keys?
Yes, but the resources scale exponentially. Breaking a 1024-bit RSA key using similar methods might take weeks or months unless you offload to a cluster or GPU farms. For anything stronger (2048+), you’re better off applying to a nation-state first.

### Is this even legal?
That depends. If you're breaking YOUR certificates as a learning experience, that's fine. But touching anything you don’t own—even ancient systems—is murky legal ground.

---

So yeah. Go grab an old cert, spin up msieve, and relive the early days of cryptographic breakdown without sweating on million-dollar data breaches. Or maybe just update your systems to use modern encryption. Up to you, really.
