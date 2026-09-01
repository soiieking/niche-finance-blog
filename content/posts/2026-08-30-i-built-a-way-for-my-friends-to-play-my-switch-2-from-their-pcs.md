---
title: How I Let My Friends Play My Switch 2 Games from Their PCs
date: '2026-08-30T16:00:49+08:00'
draft: false
tags:
- indie-hacker
- gaming
- technology
summary: Host Switch 2 games from your living room and let PC-only friends tap in
  via streaming. My DIY (and hacky) solution.
---

Turns out, social gaming gets way more social when your friends don’t need the hardware *you* bought. Here’s exactly how I rigged my Switch 2 to stream games to my friends’ PCs. It’s not perfect, but it works. Mostly.
## The Setup
### What You’ll Need
- **A Switch 2** (duh): It’s the newer Nintendo hardware. I’m working with the rumored 32GB RAM SKU. Yours could differ.
- **A capture card**: I grabbed an Elgato HD60 X. It’s $199 on Amazon right now. Budget ~$100 for cheaper options, but you risk latency hell.
- **A gaming PC/host**: Mine’s running Windows 11, RTX 3070, 16GB RAM. You could likely go lighter, but encoding gets heavy.
- **Parsec**: Free streaming tool. More stable than Steam Remote Play for my needs.
- **Broadband connection**: We’re talking 10 Mbps up minimum. Don't joke around with potato-tier ISPs for this.
## **Step 1: Capture HDMI Out from the Switch 2**
First, connect the Switch 2’s dock HDMI out into your capture card. Then plug the card’s USB-C output into your gaming PC. 
Verify the input with something like OBS Studio—it’s free and fast. Open OBS, add a new *Video Capture Device,* and point it to your capture card. Boom, your Switch screen appears on your PC. 
No live video feed? Double-check you're running the latest card firmware (Elgato’s downloads page is obnoxious but necessary). Also, restart your Switch. Shocker: half my troubleshooting was just “Restart it again.”
## **Step 2: Configure Parsec on Your Host PC**
Download and install [Parsec](https://parsec.app/) on the PC hooked to your capture card. Make a free account if you don’t already have one. 
Go into Parsec’s host settings:
- Turn on *Hosting.*
- Adjust the **Bandwidth Limit** (set it to 30 Mbps for 1080p). Anything lower crushes video quality.
- Check *Display Cursor* and *Console Mode.* These stop guests from hijacking your PC completely.
If you get weird input lag in tests, tweak Encoder Settings. Use **NVENC** (NVIDIA cards) or **AMF** (AMD cards). CPU encoding is doable, but your mileage will vary.
### Latency Check
Parsec tends to hover around 20-40ms latency over LAN. WAN setups? Expect 50-100ms minimum. That’s why fast-paced games (Smash Bros., Splatoon 3) aren’t ideal here.
## **Step 3: Create a Dedicated Gaming Session**
This one’s optional but makes things smoother:
1. Add a secondary user profile on your gaming PC. Name it "Switch Streaming" or something obvious.
2. Log that user into Parsec *only.*
3. Lock down permissions so guests touch nothing but your Switch stream.
Why? Because I let my friend Alex in once, and the dude almost resized my god-tier Excel setup. Never again.
## **Step 4: Invite Friends**
Ask your friends to install Parsec on their PCs. They’ll also need free accounts. Share your Parsec session link (found under your Host tab).
When they connect, you’ll approve access manually. This step avoids random strangers in LAN matchmaking regions. Yes, that happens—nobody needs a creeper spectating your Zelda saves.
Heads up: Parsec limits free accounts to 60 fps at 1080p. For 4K, **someone** (maybe you) needs the Pro plan ($8/mo). I stuck to Free; 1080p is plenty crispy.
## **Key Considerations**
This is playable but not flawless. Inputs get a little squishy. Codec compression can artifact, especially during fast camera movement. It’s not native, but for casual Mario Kart lobbies, it holds up just fine. 
Another hurdle? Sound mixing. My setup inherently mixes system audio with Switch audio. Fixing this would involve extra hardware (like an audio mixer), and honestly, I couldn’t be bothered.
## FAQs
### Can this handle 4-player co-op over the internet?
Not officially. Switch 2 controllers are linked locally to the console. You’ll need tools like Xbox Cloud Gaming’s virtual controller tech—and no guarantees that works right. So, this solution is strictly “share screen, share input” on one controller.
### Why not Moonlight or Sunshine?
They’re better for native PC gaming, leveraging NVIDIA GameStream. But the Switch isn’t a traditional game setup—so Parsec or looking-glass.co stuff shines more here.
### Is streaming Switch games over the internet legal?
Eh. Ethically, if you're running legit hardware and not pirating ROMs, you’re fine. But you’re in a gray area when others use your hosted session. Don't let Nintendo subpoena your internet provider, cool?
