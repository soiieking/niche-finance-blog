---
title: 'How Successful App Creators Got Past the Hardest Part: Getting Started'
date: '2026-09-01T16:00:16+08:00'
draft: false
tags:
- indie-hacker
- app-development
- side-project
summary: Breaking the inertia of your side project is brutal. Here's how others actually
  did it—and how you can too.
---

## The Question That Kills 90% of Side Projects
*“How do I overcome this?”*
That’s the moment every app developer stares into the abyss. You have an idea. Hell, you might even have a repo halfway filled with ugly code. But your project feels like it’s going nowhere. The hard part isn’t deploying to the App Store or getting traffic—it’s that creeping sense of "maybe I should quit because I'll never finish this."
People on r/sideproject asked this, and the answers aren’t what you’d expect. Hint: it’s NOT about finding the perfect tech stack.
## Step 1: Decide Why You’re Building This (and Write It Down Somewhere)
You’re either solving **your own problem** or solving a problem **someone else wishes you'd solve.** If you don’t know which it is, stop. You’re not stuck because you can’t finish your project; you’re stuck because it doesn’t mean anything to you yet.
One commenter in the thread nailed this perfectly: *“I was scratch-building logistics software for my dad’s small business. When I wanted to quit, I’d just remember how much better his life would be if I finished it.”*
Practical tip: Create a one-sentence "user" story. Like, *“I hate how slow I am at tracking expenses, so I’ll build a tool that automates it for me.”* Write it on a sticky note. Stick it somewhere you’ll see every day.
Overkill? No way. If you can’t describe your app’s reason to exist in under 15 seconds, you’re much more likely to quit. 
## Step 2: Ignore What Everyone Else Is Using
Every r/sideproject thread turns into a tech-stack popularity contest: “Try Next.js!” “Bro, build it no-code on Bubble!” *Stop.* Picking the “wrong” framework is procrastination; the real blockers are finishing and launching.
Want receipts? One user dove straight into Nest.js, got clobbered by the complexity, and eventually rewrote the project in Flask because “it got me back to finishing instead of fiddling.” They launched in a week after switching.
### Frameworks: Pick What You Already Know
- **Web apps:** If you’ve used anything like Django, Rails, or Express before, go with that. Already familiar? Great.
- **Mobile apps:** React Native or Flutter are good defaults. React Native works better if you hate writing separate UIs for iOS and Android, but Flutter’s declarative UI can save hours once you get past the initial learning curve.
- **No-code build tools?** These are great for testing ideas quickly—*but only for MVPs.* Tools like Bubble or Adalo are more limiting than they look if you plan to scale.
TL;DR: Pick boring tools you already know. Polish later.
## Step 3: Keep Momentum by Shipping Junk Early
Perfectionism is death. Screw finishing the whole app—just **finish a bad version of the core feature first.** A surprising chunk of successful apps in r/sideproject launched as ugly, half-baked messes before iterating.
- Example: **That guy who built the app "Shoebox" to scan receipts.** His first release only scanned B&W images, missed half the receipts, and had zero export features. It still got ~150 downloads on day one. Why? People just wanted *something* that sort of worked.
You can fix bugs or build new pages later. Early users are shockingly patient when you level with them: "Hey, this is super raw, but here's where I want to take it. Interested?"
### Git Version of This Mentality: Ship, Don’t Hoard
Don’t hide your code in a private repo until it's perfect. Push it to GitHub (private or public) and make an MVP branch. It’s a mental trick that makes you accountable for finishing v1. If you’re nervous about exposing your junk code, make it public later.
## Step 4: Choose Tiny Wins Over Everlasting Slogs
One r/sideproject user had an iconic answer: “I broke my app down into $10, $100, and $1,000 tasks.” Here’s how it works:
- $10 tasks are easy dopamine hits: styling a button, tweaking CSS, or updating a ReadMe file.
- $100 tasks are mid-level challenges: building an API connection, integrating with Stripe.
- $1,000 tasks make you sweat bullets: fully implementing analytics, or launching your app into production the first time.
If you chase $1,000 tasks every time you feel stuck, you’re going to burn out. Balance it. Spend a day smashing $10 or $100 tasks just for that sweet feeling of crossing stuff out.
## FAQ
### Why is shipping "bad code" a good idea?
Shipping bad code isn't an endorsement of technical debt. It's just being real: most first-time app builders get stuck aiming too high. Iterations fix bad code, but nothing fixes an app that never ships.
### Will switching stacks help me finish faster?
Usually not. Your main enemy isn’t the stack—it’s that **shiny new stack** comes with learning curves. Stick with what you already know unless your current setup is **actively blocking progress.**
### How do I know if my app idea will work?
You don’t. That’s not snark—it’s reality. But if you’re solving your own problem or talking to even two other humans who said, “Wow, that’d be useful,” it’s probably worth trying. Worst case? You learned new skills building it.
```json
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Why is shipping 'bad code' a good idea?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Shipping bad code isn't an endorsement of technical debt. It's just being real: most first-time app builders get stuck aiming too high. Iterations fix bad code, but nothing fixes an app that never ships."
      }
    },
    {
      "@type": "Question",
      "name": "Will switching stacks help me finish faster?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Usually not. Your main enemy isn’t the stack—it’s that shiny new stack comes with learning curves. Stick with what you already know unless your current setup is actively blocking progress."
      }
    },
    {
      "@type": "Question",
      "name": "How do I know if my app idea will work?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "You don’t. That’s not snark—it’s reality. But if you’re solving your own problem or talking to even two other humans who said, 'Wow, that’d be useful,' it’s probably worth trying. Worst case? You learned new skills building it."
      }
    }
  ]
}
