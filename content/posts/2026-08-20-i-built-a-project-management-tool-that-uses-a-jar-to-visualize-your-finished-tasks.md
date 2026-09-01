---
title: 'I Built a Task Jar for Project Management: Does It Even Make Sense?'
date: '2026-08-20T08:00:28+08:00'
draft: false
tags:
- indie-hacker
- sideproject
- productivity
summary: 'A community-focused analysis exploring the recent discussions and practical
  insights regarding I Built a Task Jar for Project Management: Does It Even Make
  Sense?.'
---

Ever wanted to feel *visceral satisfaction* every time you finish a task? Like, physically see your work piling up like coins in a piggy bank? That was the galaxy-brain idea that led me to build a task management tool... with a jar.
Yes, a jar. I’m not talking about a virtual jar animation on your screen. I mean an old-school, actual glass jar that sits on your desk. Every time you finish a task, press a button on your app, and it drops a token into the jar. The thunk noise? *Chef's kiss.* It forces you to notice progress in a way Notion or Asana never will.
## But Why a Jar?
Honestly, I was burnt out on standard to-do apps. Trello, TickTick, ClickUp... you name it. All of them break down for me eventually because the virtual checkmarks stop feeling real. You’re working hard, crossing items off, but it’s like trying to track progress in a vacuum. There’s no dopamine loop.
I first saw a “task jar” concept drifting around on r/sideproject. The idea wasn’t even tech-centric — the original post was from someone who physically wrote tasks on slips of paper, finished them, and threw the slips into a mason jar, claiming it felt way more gratifying. My brain? Immediately: *What if I automate this?*
## The Build: MVP Energy All the Way
To turn the idea into reality, I used a Raspberry Pi 4 (because I had one lying around) with a cute little servo motor that opens a chute to drop a token from a hopper into the jar. Add a cheapo Bluetooth speaker for the satisfying “clink” sound. All hooked up to a quick-and-dirty React app hosted on Netlify.
The app is bare-bones:
1. Write your tasks into a list.
2. Click “Done” when you complete one.
3. Done = servo motor + clink.
At first, I thought the mechanical jar might be overkill, but you know what? It works. Testing it with 20 rounds of random tasks (everything from “clear inbox” to “schedule dentist”) was surprisingly fun. Cringe-worthy fun, maybe, but still.
Took about two weekends to build, $80 in parts. If you’re curious, most of that cost was the Pi ($45 in 2026 prices!) and the servo setup. Hosting the app is pennies, thanks to Netlify’s free tier.
## Does It Solve a Real Problem?
That’s the million-dollar question. After two weeks of trying to actually live with this monstrosity, here’s how I see it:
**What works:**
- **Visibility:** Seeing those tokens visibly pile up is *way* more motivating than a digital graph.
- **Tactility:** If you’re the kind of person who loves mechanical keyboards, fidget spinners, or anything purposeful you can touch, this scratches the same itch.
- **Focus:** Weirdly, it made me pick fewer, more meaningful tasks. Adding “Make coffee” doesn’t feel worth the pomp and ceremony of earning a token.
**Where it sucks:**
- **Overkill for Most People:** This is not scalable for teams unless you want 10 jars clinking in an open office. One commenter on my Reddit post said this feels like “a solution that imagines the problem way harder than it is,” and yeah, fair.
- **Setup Hassle:** If you’re not already handy with electronics, this isn’t exactly plug-and-play. There’s wiring. Debugging Python scripts. If that makes you sweat, skip it.
- **Space:** Physical jars are cute for a while, but they will become clutter for 95% of people. Let’s be real.
## Alternatives Worth Considering
If the jar idea is too much (valid), you can still steal the psychology behind it:
1. **Streaks on iOS:** Gamifies habits with clean visuals.
2. **Habitica:** Turns tasks into actual RPG quests. Little over-the-top but fun.
3. **Tangible Progress:** One redditor recommended manual paper tracker systems like Bullet Journaling — still analog, but no servo nonsense.
Do any of these give you the *thunk*? No. But maybe that’s an indulgence, not a need.
## Final Take: Silly, But Not Pointless 
I didn’t build this thinking it’ll revolutionize productivity, and it won’t. But it’s a quirky, fun experiment worth trying if traditional productivity tools leave you cold. Will I keep using it long-term? Probably not. But every time I walked past that jar, full of clinking coins of “finished work,” it made me smile. And in this productivity hellscape, that counts for something.
### FAQ
#### How hard is it to set up the mechanical jar?
If you’ve worked with Raspberry Pis or Arduino before, it’s intermediate difficulty. The hardest part is wiring the servo and troubleshooting. If not, it’s a learning curve.
#### Why not just simulate the jar and tokens digitally?
You absolutely could, and I imagine the effect might still work *somewhat*. But the physicality — the sound, the act of dropping a token — is really the selling point here. Without it, it’s just another bar graph.
#### Will this work for teams or workplaces?
Not really. It’s too niche, and you’d probably need one jar per person, which gets messy fast. It’s more suited for individual use.
