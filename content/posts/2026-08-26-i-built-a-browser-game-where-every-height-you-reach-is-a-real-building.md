---
title: Building a Browser Game Where Every Height is a Real Building
date: '2026-08-26T16:00:30+08:00'
draft: false
tags:
- indie-hacker
- business
- technology
summary: I built a browser game that lets you climb real buildings, but it's not all
  fun and games. Here's what I learned.
---

I'm still recovering from the project that nearly killed me. I built a browser game where every height you reach is a real building. Sounds cool, right? I thought so too, until I realized I'd bitten off more than I could chew. I'm talking hours of research, days of coding, and a healthy dose of frustration.
## The Idea
I'd been following the r/sideproject thread on using OpenStreetMap (OSM) data to create a browser game. One user, u/throwawayuser123, had mentioned using the OSM API to fetch building heights and create a game around it. I thought, "why not?" – I love a good challenge. I downloaded the OSM API documentation, version 0.6.1, and started digging in.
### The Technical Challenges
First off, the OSM API is a beast. It's not like a simple REST API where you can just send a GET request and get a response. No, it's a complex mess of XML and JSON responses that require parsing and handling. I spent hours figuring out how to fetch the building data, only to realize that the API has a rate limit of 100 requests per hour. That's not a lot, considering I wanted to fetch data for every building in a city.
## The Tools
I decided to use a combination of JavaScript and Python to build the game. I chose JavaScript because it's what I'm most comfortable with, and Python because it's great for data processing and manipulation. I used the Leaflet library to create the game's map, and the D3.js library to create the building heights visualization.
### The Fatal Flaw
Here's where things started to go wrong. I realized that fetching building data for every building in a city would take way too long. I mean, we're talking hours, even days, of processing time. And that's not even considering the API rate limit. I needed a way to speed up the data fetching process, but I didn't want to sacrifice accuracy.
## The Solution (Sort Of)
I decided to use a combination of caching and data processing to speed up the game. I used Redis to cache the building data, and a Python script to process the data and create the game's visualization. It worked, but it was a hack. I mean, it was a great hack, but it was still a hack. And it required a lot of tweaking and testing to get right.
### The Results
So, what did I end up with? A game that lets you climb real buildings, but with a lot of caveats. The game is slow, and the data fetching process is a pain. But, it's still a cool idea, and I'm proud of what I accomplished. I learned a lot about the OSM API, and about the importance of caching and data processing in game development.
## The Takeaway
Building a browser game where every height is a real building is not for the faint of heart. It requires a lot of research, coding, and testing. But, if you're up for the challenge, it can be a really rewarding experience. Just be prepared to deal with a lot of frustration and technical debt.
### The Future
I'm not sure if I'll continue working on this project, but I'm glad I learned from the experience. I'll definitely be keeping an eye on the r/sideproject thread for inspiration and ideas. And who knows, maybe one day I'll create a game that's faster, more accurate, and more fun.
FAQ
----
### Q: How did you handle the OSM API rate limit?
A: I used a combination of caching and data processing to speed up the game. I also implemented a retry mechanism to handle API rate limit errors.
### Q: What libraries did you use to build the game?
A: I used Leaflet for the map, D3.js for the building heights visualization, and Redis for caching.
### Q: Is the game available to play?
A: Not yet. I'm still working on optimizing the game and fixing some technical issues. But I'll definitely make it available to play when it's ready.
```json
{
  "@context": "https://schema.org",
  "type": "FAQPage",
  "name": "Building a Browser Game Where Every Height is a Real Building",
  "description": "A narrative about building a browser game that uses real building heights",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "How did you handle the OSM API rate limit?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "I used a combination of caching and data processing to speed up the game. I also implemented a retry mechanism to handle API rate limit errors."
      }
    },
    {
      "@type": "Question",
      "name": "What libraries did you use to build the game?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "I used Leaflet for the map, D3.js for the building heights visualization, and Redis for caching."
      }
    },
    {
      "@type": "Question",
      "name": "Is the game available to play?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Not yet. I'm still working on optimizing the game and fixing some technical issues. But I'll definitely make it available to play when it's ready."
      }
    }
  ]
}
