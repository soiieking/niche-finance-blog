---
title: Be Careful When Creating Gmail Accounts for Your Projects
date: '2026-08-31T22:00:05+08:00'
draft: false
tags:
- indie-hacker
- business
- technology
summary: Your new app doesn’t need 12 Gmail accounts, and copying passwords into Notion
  will haunt you later. Here’s how to do it right.
---

## Gmail: Your First Side Project Mistake
It starts innocently. You whip up a new project — let’s call it “SuperCalcGPT.” Naturally, the first thing you do is create a Gmail account for it: `supercalcgpt@gmail.com`.
Two months later, you start another project. What’s the easiest move? Another Gmail account: `project2@gmail.com`. Rinse and repeat. Suddenly you’ve got 7 of these accounts, each with its own password stored in some sketchy Notion page called “Logins 🗝️.”
Here’s the catch: It feels like you're saving time, but you’re doing future-you zero favors. This stuff can (and will) spiral out of control.
## Why You’re Doing This in the First Place
Let’s be real: Gmail is free. If you’re building a side project that’s bleeding money, free email feels like a perfect no-brainer. Plus, it’s so easy — spin up a new account in five minutes, attach it somewhere, and done.
But free can be expensive. A random Reddit comment in that r/sideproject thread nailed it: “I had 14 Gmail accounts across old projects, and now I don’t even know which two-factor codes go to which ones. NIGHTMARE.”
They’re not wrong.
## Option 1: Stick to One Account with Aliases
Google has a feature most people forget exists: email aliases. If your main email is `you@gmail.com`, you can create a virtually unlimited number of “accounts” by appending a plus sign and keyword:  
- `you+supercalcgpt@gmail.com`  
- `you+project2@gmail.com`
The inbox still lands in your primary Gmail account, but you can filter or label incoming mail by alias. Clean. Simple.
But here’s the issue: Aliases are **not** separate Google Accounts. You can't use `you+supercalcgpt@gmail.com` to create its own Stripe or AWS account. If you need completely distinct identities for different tools, this doesn’t cut it.
If your tools don’t care, though? This setup will save you hours. Plus, no juggling 5,000 TOTP tokens.
## Option 2: Use a Custom Domain
I can already hear some of you saying, “But that costs money!” Yes, it does. About $6/month if you use Google Workspace. But you get so much in return:
- An email like `hi@supercalcgpt.com` looks legit.  
- You can add team accounts later (`dev@`, `support@`) without duct-taping five Gmail accounts together.  
- No dependency on your personal Google account.  
This is overkill for some people. If you’re testing a throwaway MVP that might die in two weeks, skip the domain. But if you have even a 10% chance of turning the project into a real thing, just buy the domain. It’ll save you the pain of switching later.
Bonus tip: If Google Workspace is too much, Zoho Mail offers free custom domain email for solo users. The UI isn’t amazing, but hey, it’s free.
## Option 3: Password Managers and Disposable Accounts
Let’s say you *really* want separate accounts but don’t want chaos. You need two things:  
1. A password manager.  
2. A clear plan to delete abandoned accounts.  
Use a manager like Bitwarden or 1Password. Seriously, don’t copy Gmail passwords into Notion. Even if you lock the page, it’s hacky and hard to search later.
For disposable accounts, consider SimpleLogin or AnonAddy. These services create “burner” email addresses you can forward to your main inbox. When a project fizzles, delete the alias. Clean slate. Done.
## The Gotchas
- **You’ll regret random names.**  
  Don’t create an account like `supercalcgpt2021@gmail.com`. Because when you come back in 2024, it might feel like an outdated version of your own project. Just call it `supercalcgpt@gmail.com`.
- **Recovery emails can crush you.**  
  If you’re creating Gmail accounts, you’re required to set a recovery email. You think you’ll remember which account is tied to which, but you won’t. Make a central tracker (and no, it’s not Notion).
- **Don’t mix personal and project accounts.**  
  Let’s say you use your main Google account, `you@gmail.com`, to test Firebase on Project A. Then you need a different Firebase setup for Project B. Too bad — Firebase won’t let you juggle easily in a single account.
## So, What’s the Move?
Start with one Gmail and aliases if your project is lightweight.  
Upgrade to a custom domain when you’re locked in.  
Use a password manager from Day 1, even if you think it’s overkill.  
And above all, don’t spawn Gmail accounts like you’re hoarding NFTs. There’s nothing cool about trying to scrape 2FA tokens off an old phone because you lost track of one out of nine Gmail logins.
### FAQ
#### Why not use just one Gmail for everything?  
You can… until you need project separation. Services like Firebase, AWS, and Stripe often require distinct accounts. Managing everything from a single Gmail gets ugly fast.
#### Are aliases okay for production?  
Yes — but only if you don’t need full account isolation. Aliases can’t stand in as separate accounts for most tools.
#### Is Google Workspace worth it for a side project?  
It depends. For a serious project, yes. If this is just a weekend hackathon thing, stick with Gmail aliases or Zoho’s free tier.
