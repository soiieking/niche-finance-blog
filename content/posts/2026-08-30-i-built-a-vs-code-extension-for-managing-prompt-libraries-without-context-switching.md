---
title: I Built a VS Code Extension to Manage Prompt Libraries — Here's What Reddit
  Thinks
date: '2026-08-30T00:00:46+08:00'
draft: false
tags:
- indie-hacker
- side-project
- technology
summary: Tired of copy-pasting prompts? This VS Code extension streamlines managing
  prompt libraries—but does it solve the right problem?
---

# I Built a VS Code Extension to Manage Prompt Libraries — Here's What Reddit Thinks
Managing prompt libraries is one of those problems that lowkey kills your flow but barely gets attention unless you’re neck-deep in prompt engineering. Like, I get it: copying and pasting blocks of text across tools just feels wrong in 2026. So someone on r/sideproject went and built a VS Code extension for this exact problem.  
The question is: does it actually make life better? Here's the community’s take—some hype, some skepticism, and a sprinkle of over-engineering accusations.
## What This Thing Does
The creator, **u/aiworkflowguy**, described the extension as "a faster way for devs like me to save and re-use GPT prompts without leaving VS Code." Basically, it lets you store, organize, and search through your prompts in one place—right inside your editor. No more "where did I leave that ChatGPT tab open?" moments.
It’s early days (v0.3.2 at the time of writing). Features include:
- **Shortcut hotkeys:** No mouse moves required to yank up your prompt library.  
- **Markdown support:** Because who doesn’t write README.md-level documentation for their GPT conversations these days?  
- **Fast fuzzy search:** Think Command+T but for all your cleverly-crafted prompt engineering hacks.  
- **Export to JSON:** For the Dropbox-and-spreadsheets crowd who fear platform lock-in.
Sounds cool, right? But cool doesn’t always mean useful.
## Redditors Weigh In
### The Good: “It Just Gets It”  
Several commenters loved the idea, especially for devs trying to wrangle prompts into GPT workflows without wasting brainpower.
**u/codingzebra** nailed it:  
> "VS Code is where I live anyway. Anything that saves me an alt-tab wins."
Other folks chimed in on how this tool simplifies iterative workflows. "I’ve been using Scratchpad to save GPT edits, but folding it into VS Code saves me steps," said **u/devopsdaydream.** The Markdown support also got applause—it’s great for tweaking hierarchical prompts.  
For gig-economy ghostwriters and freelance consultants? A game-changer. "I juggle prompts for 15 different client personas. This would save me *hours*," wrote **u/freelancelass.**  
### The Bad: Is This Overkill?
But to the surprise of no one, the side project crew called out scope creep.
**u/dappermantis** had a juicy hot take:  
> "Do you *really* need a VS Code extension for this? Just use Obsidian or Notion. Same UX, but they work cross-device."
Similarly, **u/nocturnaura** pointed out the classic trade-off: “Installing yet another VS Code extension just adds bloat. Why not pipe your prompts into a JSON powered by a simple CLI script?”
There’s also the browser-first camp. If your workflow is 90% ChatGPT UI anyway, no extension will fix the real pain points: clunky OpenAI interfaces and proprietary ecosystems. “I’ve seen projects like this pop up, and they all die when GPT gets its act together,” sighed **u/apihermit.** Hard not to agree.  
### The Ugly: Who Owns the Prompts?
One practical concern showed up repeatedly: data portability. Sure, you can export prompts to JSON (cool on paper), but that doesn’t help if your day-to-day lives in GPT-4 or Claude.
“Dropping something like LangChain into this would be 1000x more powerful,” complained **u/fullstackpotato.** Their point? A local library is neat, but if you can’t integrate it into agents *or* automate it across projects, you'll hit a ceiling fast.
## Verdict: Perfect for Some, Overkill for Most  
If you’re:
1. A dev already living in VS Code, and  
2. You hate switching contexts to manage your prompts...  
...try this out. It’s a one-day-install, tops. But if your workflow sprawls across devices or tools, or you’re more GPT-native than editor-bound, this might feel like a half-step solution. Personally, I’d wait until it’s baked into something meatier, like LangChain or widely-used LLM frameworks.
That said? Respect to **u/aiworkflowguy** for scratching their own itch. Side projects like this push the ecosystem forward. Just keep an eye on actual adoption vs. dev geek hype.
## FAQs
### How do I install the extension?  
Search for “Prompt Manager” in the VS Code extensions marketplace. Current version is v0.3.2, so expect some quirks.
### Does this work offline?  
Partially. You can manage and search your prompt library offline, but obviously you’ll need an internet connection to use GPT APIs.
### Are there alternatives?  
Depending on your needs:  
- **Obsidian**: Great for rich text organization; Markdown-native.
- **Notion**: Overkill for most devs but plays well with everything.  
- **LangChain integrations**: Heavier lift if you need state persistence and automation.
