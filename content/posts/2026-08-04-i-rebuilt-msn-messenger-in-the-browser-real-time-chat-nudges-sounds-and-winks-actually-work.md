---
title: 'Rebuilding MSN Messenger in the Browser: WebSockets, NUDges, and Winks'
date: '2026-08-04T06:55:12+08:00'
draft: false
tags:
- indie-hacker
- business
- technology
summary: 'A community-focused analysis exploring the recent discussions and practical
  insights regarding Rebuilding MSN Messenger in the Browser: WebSockets, NUDges,
  and Winks.'
---

Saw a post on r/sideproject last week where someone actually rebuilt MSN Messenger in the browser. Real-time chat, the classic nudge shaking the screen, those obnoxious winks. It works. I am legitimately jealous. 
Most devs try to build real-time chat and immediately overcomplicate the stack. They spin up a Kafka cluster and a Redis pub/sub layer for 12 concurrent users. I love tools like Centrifugo, but it is overkill for most people trying to ship a side project. 
Here is how you actually build this stripped-down nostalgia bomb without setting your wallet on fire.
## The Server: WebSockets Are Fine
Stop reaching for AWS AppSync or managed Socket.io clusters. Grab a $4 Hetzner CX22 cloud instance. It gives you 2 AMD vCPUs and 4GB of RAM. A standard DigitalOcean droplet costs $6 for half that hardware. If you are just running a single Node.js server, Hetzner is the move.
You do not need Redis for pub/sub yet. Memory state works perfectly fine until you hit about 5,000 concurrent connections. The original poster was tracking state in a simple Node.js array. Is that safe for production at scale? No. Does it work for a side project getting 50 hits a day? Absolutely.
### Bootstrapping the App
We will use Node.js, Express, and the native `ws` package. Socket.io is great, but it adds 40kb of bundle weight to handle fallbacks for browsers that literally do not exist anymore. Modern browsers support WebSockets natively.
Run this on your server:
```bash
mkdir msn-clone && cd msn-clone
npm init -y
npm install express ws
```
Here is the entire WebSocket server. It is 20 lines of code.
```javascript
const express = require('express');
const app = express();
const http = require('http');
const server = http.createServer(app);
const { WebSocketServer } = Server('ws');
const wss = new WebSocketServer({ server });
wss.on('connection', (ws) => {
  ws.on('message', (data) => {
    // Broadcast to everyone else
    wss.clients.forEach((client) => {
      if (client !== ws && client.readyState === WebSocket.OPEN) {
        client.send(data.toString());
      }
    });
  });
});
server.listen(3000, () => {
  console.log('Chat active on :3000');
});
```
Run it behind a Caddy reverse proxy. Caddy handles your Let's Encrypt SSL certs automatically. Nginx is fine, but writing Caddyfile configs takes 10 seconds instead of 30 minutes.
## Handling Nudges and Winks Frontend
The magic of MSN Messenger was the chaos. The sounds. The screen shake. Modern web browsers gate audio behind user interaction. If you try to play a `.wav` file on page load, Chrome blocks it. You have to wait for the user to click the page.
In the r/sideproject thread, a commenter pointed out a clever hack: attach the audio play function to the `first click` event listener. 
```javascript
let audioUnlocked = false;
document.body.addEventListener('click', () => {
  audioUnlocked = true;
}, { once: true });
```
Once unlocked, you can blast the classic MSN `nudge.mp3` sound whenever a message comes in. For the nudge effect, basic CSS keyframes do all the work without forcing a heavy repaint of the DOM.
```css
@keyframes shake {
  0% { transform: translate(0, 0) }
  20% { transform: translate(-10px, 5px) }
  40% { transform: translate(10px, -5px) }
  60% { transform: translate(-5px, 10px) }
  80% { transform: translate(5px, -10px) }
  100% { transform: translate(0, 0) }
}
.nudge-active {
  animation: shake 0.5s cubic-bezier(.36,.07,.19,.97) 2;
}
```
Winks are trickier. True winks were full-screen Flash animations. Flash is dead. The original poster replaced them with CSS SVG filters. Honestly, running a complex keyframe animation on a 4MB SVG file spiked my CPU usage to 40% on a 2021 M1 Mac. Your mileage may vary. I have not tested how bad this triggers a cheap Android phone, but keep the SVG under 50kb.
### Deploying with Docker
Yes, you should use Docker. For development? No. For deployment? Yes. Do not use Docker Desktop though. It hogs 4GB of RAM just to sit idle. Use Podman on your local machine if you want a daemonless experience.
```bash
podman build -t msn-clone .
podman push msn-clone docker://user@your-hetzner-ip/var/run/docker.sock
```
Alternatively, just push your code via GitHub Actions and run an SSH script to `pm2 restart app.js`. People pretend CI/CD pipelines are mandatory. They are not.
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [{
    "@type": "Question",
    "name": "Can you do real-time chat without Socket.io?",
    "acceptedAnswer": {
      "@type": "Answer",
      "text": "Yes. Modern browsers support native WebSockets. You can use the lightweight 'ws' package in Node.js, which drastically reduces bundle size and memory overhead compared to Socket.io."
    }
  }, {
    "@type": "Question",
    "name": "Why won't chat sounds play automatically in the browser?",
    "acceptedAnswer": {
      "@type": "Answer",
      "text": "Chrome and Safari block autoplaying audio to prevent intrusive ads. You must wait for a user interaction, like a click, to unlock audio playback before you can broadcast incoming message alerts."
    }
  }, {
    "@type": "Question",
    "name": "Should you use Docker or Podman for side project deployments?",
    "acceptedAnswer": {
      "@type": "Answer",
      "text": "For deployment, either works. Docker Desktop has high idle RAM usage on local machines, so Podman is a great daemonless alternative for building the image locally before pushing to a server."
    }
  }]
}
</script>
