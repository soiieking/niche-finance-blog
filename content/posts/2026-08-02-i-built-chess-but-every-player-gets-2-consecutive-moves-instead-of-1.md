---
title: "I Broke Chess by Giving Every Player 2 Moves Instead of 1"
date: 2026-08-02T22:27:05+08:00
draft: false
tags: ["indie-hacker", "game-design", "technology"]
summary: "A redditor forgot that doubling the moves in chess doesn't add strategy, it nukes the board. Here is why Double-Move chess collapses."
---

Saw a post on r/sideproject this week from a dev who proudly shipped a custom chess engine. The twist? Every player gets two consecutive moves per turn. 

The author seemed genuinely shocked that the game wasn't taking off. They had a slick UI built in React, deployed the whole thing to Vercel, and were getting zero retention. They asked the community if the UI was the problem. 

It wasn't the UI. The game is fundamentally broken.

## The Fatal Math of the First Move

Standard chess is a massive state space, but it is fundamentally built on a bedrock of mutual King safety. If I move my Knight, you can react to my Knight. 

Give every player two moves, and that social contract evaporates. 

As user u/brd_placeholder pointed out in the thread (and anyone who has tried to invent game variants as a bored kid already knows), White wins on turn one almost every time. 

You move your pawn up two squares, then you slide your Bishop out. Black is instantly in checkmate. If Black blocks the check with their first move, White uses their second move to capture the King. Game over in 12 seconds. 

The project author admitted they tried to patch this by forcing the first player to only make one move on turn one. It didn't help. You are still handing one player a tactical nuke. The moment White gets a full two-move turn, they use their first move to expose a line, and their second move to attack the King. Black cannot defend against a threat that is created and executed in the exact same turn. 

## Reinventing the Rules Engine

Here is where the side project turned into a total nightmare. The dev didn't actually write a native chess engine. They imported `chess.js` v1.0.0 via npm—a rock-solid, standard JavaScript library for move generation and validation—and wrapped their two-move logic around it.

This is the classic indie hacker trap. Reaching for an existing library saves you 80 hours of writing board logic, but standard libraries assume standard rules. The `chess.js` API is explicitly designed to end the game the second a King is captured or a valid checkmate position is reached. When you try to force two moves, the library throws unhandled promise rejections because it thinks the game state is already over. 

The dev spent three weekends debugging the WebSockets back-and-forth, when the actual game design was dead on arrival. 

### What Actually Works (If You Must Tweak Chess)

If you want to build a chess variant that people actually play, you have to understand game theory. 
- **Cheat Prevention is Mandatory:** You must enforce a rule that prevents a player from making a move that results in their own King being in check at the end of their two-move block. Without this, you are just playing a sandbox where people accidentally kill themselves.
- **Look at existing variants:** "Double-Move Chess" is a known mathematical novelty. Look into Marseillais Chess instead. The French have been playing this variant for a century, and they solved the White-advantage problem by giving White only one move on turn one. Even then, the game is notoriously unbalanced.
- **Look at Four Player Chess:** If you want chaos without the fragility, look at how Chess.com or Lichess handles their 4-player modes. The board scales to 14x14, and the extra players naturally balance the aggressive tempo. 

## Why This Matters for Your Next Weekend Build

I love that people build weird things. Our entire ecosystem relies on someone playing around with existing WebSockets and APIs on a Saturday night. Deployment is cheaper than ever—I host my own micro-projects on a $4 Hetzner CX22 and it handles thousands of concurrent connections without breaking a sweat.

But you have to test the core loop before you build the engine. 

Grab a physical chessboard. Play your variant with a friend for ten minutes. If the game ends before you finish your coffee, don't write the React wrapper. Don't install `chess.js`. Just scrap it and move to the next idea. 

Your mileage may vary, but if you ship a game where White wins in two moves, don't blame the UI for the bounce rate.