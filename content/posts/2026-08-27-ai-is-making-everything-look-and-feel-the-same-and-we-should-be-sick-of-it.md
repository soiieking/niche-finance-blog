---
title: AI Is Making Everything Look the Same, and It's a Problem
date: '2026-08-27T08:00:33+08:00'
draft: false
tags:
- selfhosted
- ai
- design
- philosophy
summary: AI is making everything clean, crisp, and painfully boring. Why we should
  care as selfhosters and tech hobbyists.
---

## The AI Sameness Problem
You’ve seen it. Probably contributed to it. That wave of content or UI that just feels... pre-sanded and soulless. AI-generated blog posts that read like they came out of a checklist. Logos that all look like rearranged Figma templates. Open-source UIs that imitate Google Material Design so hard you think you’re still logged in at work.
Someone in r/selfhosted called it "sterile minimalism," and they’re not wrong. AI, for all its potential, has put creative decision-making into a narrow funnel. And this isn’t just some philosophical gripe—it’s showing up everywhere you look, from auto-generated documentation to UI components in your next Docker-compose web panel.
Why does this matter now? Because we’re at a point where the tools we host, the designs we ship, and the content we count on all run the risk of becoming painfully generic. And the more repetition AI adds, the worse the signal-to-noise ratio gets for people who actually care about quality.
## Example: The Curse of Over-Styled Dashboards
Think about open-source dashboards. Tools like Heimdall, Organizr, or Portainer are staples of selfhosting. They're functional, sure, but they’re also starting to look like clones of each other. Why? Because contributors grab the same AI-recommended color schemes, hit up the same Material Design kits, and—dare I say it—prompt ChatGPT for UX advice. The result: pretty, responsive, and boring as hell.
And it’s definitely not just an OSS problem. Commercial tools are infected, too. Look at any random SaaS tool: Notion-style typography. ChatGPT-like chatbots. You could argue these trends follow user demand, but when *everything* looks the same, none of it feels innovative. Call me a crank, but I miss the days when software could be ugly and yet filled with personality (yeah, I'm looking at you, early Syncthing).
## AI ≠ Creativity (And Selfhosters Should Know Better)
The irony is, selfhosters are *supposed to be the rebels*. We’re the ones tweaking configs and going with off-brand alternatives simply because we can. And yet here we are, leaning into aesthetics and UX choices dictated by recommendation algorithms. Even the landing pages of small VPS providers like Hetzner and Scaleway are guilty. They're polished, sure, but indistinguishable—like AI spit out an SEO-optimized SaaS design template and everyone ran with it.
This issue goes way deeper than UI, though. Ever tried generating privacy policies or README contents with AI tools? They’re functional-ish, but they all read the same. "We value your security and privacy." Blah blah. Great! Who doesn’t? But it’s just a thin layer of marketing glaze over the same cookie-cutter language. Tools like ChatGPT or Claude are amazing for boilerplate, but we’ve stretched them so far that the cracks are starting to show.
## But Isn't AI Just a Tool?
Sure, AI is *just* a tool—and a damn powerful one. But its constraints aren’t as obvious as people think. Because AI models like GPT-4 aren’t “freeform creativity” machines; they’re *statistical prediction* engines. That means they prioritize patterns—what’s worked, what fits, what’s in the middle of the bell curve.
In other words, they default to the least interesting choice.
It’s like if you asked a carpenter to build you a chair, and they checked the "average dimensions of all chairs ever made" before even touching wood. You wouldn’t be getting a handmade antique; you'd be getting IKEA (with slightly wonky assembly instructions).
The worst part? It reinforces itself. As more people rely on AI for design, content, or optimization, models might start training on their own outputs. That’s a recursive creativity failure—and that’s terrifying.
## What Should Selfhosted Folks Do About It?
1. **Resist Default Choices.** Open-source home-lab tools have always thrived on scrappiness. Lean into that. Just because Material UI is the easiest choice doesn’t mean it’s the most fun—or the most useful.
2. **Contribute Personality.** If you’re pushing code, tweak things. Build in unexpected features. Screw around with unused fonts. Turn sterile sameness into something weird (I’m still a fan of Uptime Kuma’s emoji-laden whimsy).
3. **Avoid Over-Automation.** Sure, AI can crank out Dockerfiles, YAML manifests, and Markdown READMEs faster than you can. But check it. Refine it. Add nuance the AI misses.
4. **Look for Non-Obvious Alternatives.** I tested Homarr a while back as an alternative to Organizr. It did some unusual, boundary-pushing stuff with module interactions—even though it’s still rough around the edges. Same with Vaultwarden as a leaner alternative to Bitwarden. Imperfect tools, but their identities stand out.
## Final Thought: Fight the Homogeny
AI making everything look the same might not seem like an immediate crisis, but it’s a slow erosion of creativity. When every tool, dashboard, or README starts blending together, it numbs us to new ideas. And if selfhosters—the ultimate tinkerers—aren’t willing to push back, who will?
Sometimes it’s worth spending an extra hour breaking things just to add a little weirdness.
