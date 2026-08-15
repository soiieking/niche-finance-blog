## Introduction to Side Out
I just spent the last hour reading through the "Side Out - 8-player survival Pong" thread on r/sideproject, and I'm still trying to wrap my head around the chaos that ensues when the arena loses a wall every time someone dies. One commenter, u/LudicrousSpeed23, mentioned that "this adds a whole new level of strategy to the classic Pong gameplay," and I have to agree - it's a game-changer.

The concept is simple: 8 players, one arena, and a whole lot of ping-pong balls flying everywhere. But when a player dies, the arena shrinks, making it even harder for the remaining players to survive. I love this idea, but I have to wonder - is it overkill for most people? I mean, who needs this much stress in their life?

## The Tech Behind Side Out
From what I can gather, the game is built using a combination of Python and the Pygame library. One of the devs mentioned that they're using version 2.1.2 of Pygame, which is a bit outdated - the latest version is 2.2.0. Not a huge deal, but it's something to consider if you're planning on building your own version of Side Out.

I also noticed that some commenters were discussing the use of Docker vs Podman for deployment. Personally, I'm a fan of Podman - it's lighter, faster, and just as secure as Docker. But hey, your mileage may vary. If you're planning on deploying Side Out on a cloud provider like DigitalOcean or Hetzner, you might want to consider using a containerization platform like Kubernetes.

## Why This Matters Now
So why should you care about Side Out? For one, it's a great example of how a simple game concept can be turned into something truly unique and challenging. It's also a testament to the creativity of the indie game dev community - these guys are pushing the boundaries of what's possible with minimal resources.

But what really matters is the community engagement. The thread has over 500 comments, with people sharing their own versions of the game, discussing strategies, and even offering to help with development. This is what the r/sideproject community is all about - collaboration, innovation, and a healthy dose of competition.

## The Future of Side Out
I haven't tested this on ARM, but I'm curious to see how it would perform on a Raspberry Pi or other single-board computer. The community is genuinely split on this - some people think it would be a great way to play Side Out on the go, while others are skeptical about the performance.

As for the future of Side Out, I think it has a lot of potential. With some polish and refinement, this could be a really popular game at indie game jams and tournaments. And who knows - maybe we'll even see a commercial release. Stranger things have happened, right?

## Conclusion-ish
I'm not going to wrap this up with a neat bow, because let's be real - Side Out is a messy, chaotic game that defies neat conclusions. It's a work in progress, a labor of love, and a testament to the power of community-driven development. So if you haven't already, go check out the thread and join the conversation. And if you're feeling brave, try building your own version of Side Out. Just don't say I didn't warn you.

### FAQ
Here are a few questions that came up during my research:
* Q: What is the current version of Pygame used in Side Out?
A: The current version of Pygame used in Side Out is 2.1.2.
* Q: Can I deploy Side Out on a cloud provider like DigitalOcean or Hetzner?
A: Yes, you can deploy Side Out on a cloud provider like DigitalOcean or Hetzner using a containerization platform like Kubernetes.
* Q: Is Side Out available on ARM devices like the Raspberry Pi?
A: I haven't tested this on ARM, but it's possible that it could work with some modifications. 

{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "What is the current version of Pygame used in Side Out?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "The current version of Pygame used in Side Out is 2.1.2."
      }
    },
    {
      "@type": "Question",
      "name": "Can I deploy Side Out on a cloud provider like DigitalOcean or Hetzner?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes, you can deploy Side Out on a cloud provider like DigitalOcean or Hetzner using a containerization platform like Kubernetes."
      }
    },
    {
      "@type": "Question",
      "name": "Is Side Out available on ARM devices like the Raspberry Pi?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "I haven't tested this on ARM, but it's possible that it could work with some modifications."
      }
    }
  ]
}