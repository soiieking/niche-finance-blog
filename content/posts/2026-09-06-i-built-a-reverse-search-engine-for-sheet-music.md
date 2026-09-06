---
title: I Built a Reverse-Search Engine for Sheet Music. It Actually Works.
date: '2026-09-06 20:00:03+08:00'
draft: false
tags:
- indie-hacker
- side-project
- technology
summary: How I built a reverse-search engine for sheet music that identifies pieces
  from a quick image — and the technical quirks I ran into.
---

## A crazy idea and way too much free time

I play guitar badly and piano slightly less badly. At some point, you end up with this huge pile of sheet music, some printed, some handwritten, and some just random screenshots whipped out at band practice. Keeping track of it is a nightmare.

So I thought: **What if I could reverse-search sheet music?** Like Shazam, but for visual clues instead of audio. Snap a photo, upload it, and bam—the software spits out the name of the piece or the sheet it's from. Spoiler: I actually built this. It mostly works.

## Step 1: OCR, the obvious starting point

Optical Character Recognition (OCR) tools have gotten stupidly good over the years. I started with open-source classics like [Tesseract](https://github.com/tesseract-ocr/tesseract), but it turns out Tesseract struggles with music notation. Why? Because it's trained on text, not squiggly note heads and wild ledger lines.

Enter Audiveris. It’s open source, specifically trained for Optical Music Recognition (OMR), and pretty solid—for classical pieces. Toss a clean, printed score at it and you’ll get back a surprisingly accurate MusicXML file. Handwritten stuff, or jazz charts? You’re rolling the dice. But it was enough for a proof of concept.

**Tip for fellow builders:** Audiveris is a gloriously powerful mess. Setting it up locally LOOKS like a two-liner from their docs but takes about an hour if you’re missing Java dependencies. I ran it in Docker to save the pain (`openjdk:11-jdk-slim` worked fine). RAM-wise, it’s not greedy—512MB did the trick—but the processing time on large files is a bit of a drag. Think 7-10 seconds per image.  

## Step 2: Building the database

Here’s the catch—you need something to compare the scanned data to. It's not like there’s a Google for sheet music you can just plug into. So, I went full masochist mode and scraped a few free libraries (IMSLP and Mutopia, specifically). Yes, their ToS allows it if you aren’t redistributing content, don’t @ me.

I indexed about 50,000 pieces from IMSLP, processed into simplified MusicXML fragments. That gave me ~1.4GB of raw search data. Is this overkill? Probably. But surprisingly, PostgreSQL handled the dataset like a champ—even basic `LIKE` queries returned results in under 200ms with a few clever indexes.

Oh, and remember permissions. Even though IMSLP files are public domain, some doofus is going to use your app to find copyrighted Hal Leonard charts. A heads-up that you “make no guarantees about legality” in your Terms goes a long way.

## Step 3: Matching algorithms (yep, this part is a black hole)

Matching music fragments is… weirdly subjective. At first, I went nuclear: basic Levenshtein distance. "How similar is this note sequence to others I've indexed?" It worked *okayish*, but falsely flagged a lot of near-matches—like comparing two piano reductions of the same symphony.

The "aha moment" came when I switched to n-gram analysis. Instead of comparing a whole score at once, I started treating it like chunks of melody: "scan groups of 4 notes and try to line them up." This made results way more robust for partial matches (someone taking a photo of *just* page 3 of a 10-page piece).

**Nerd corner:** Python’s `difflib.SequenceMatcher` feels wildly underrated here. It performed better on OMR output than some of the fancier machine-learning experiments I tried. ML is overkill for most searches unless you want full-on polyphonic pattern recognition. For now, this is good enough.

## Things that went wrong (and stuff I still hate)

1. **Handwritten Music Is Hell.** Audiveris just craps itself with messy scores. There’s no simple workaround—trying anything handwritten requires way more human clean-up than it’s worth.
   
2. **Latency.** Cloud processing introduces enough lag (think 3 seconds to upload, then 5-7 for OCR + search) that it feels bad UX-wise. Go local, or pre-process popular files.

3. **File formats are chaos.** PDF? Cool. PNG? Okay. Weirdly cropped JPEG with Dutch copyright watermarks? Kill me.

4. **Who pays for this?** Indie musicians are *cheap*. I threw up a free MVP, but monetizing is a whole other nightmare. Some dude in the r/sideproject thread suggested targeting choir directors—I think that’s the play. Sell convenience to schools or orchestras.

## Where it’s at now

The app runs. You can upload a photo or PDF, and if it's something mainstream like Bach, it’s magic. For niche stuff, results are… hit or miss. But hey, even bad sources usually return a close-enough match. I’ve got ~2,000 users kicking it around casually—mostly sheet music hobbyists.

If I were scaling this long-term, I’d clean up the handwritten recognition (or skip it entirely) and throw more effort into partnerships with publishers. But as a solo project, I’m happy. It scratches the itch.

---

### FAQ

#### How accurate is the tool overall?
Depends. For clean, printed sheet music: ~85-90%, especially for classical pieces. Handwritten or modern stuff? Lucky if you hit 50%.

#### What makes Audiveris better than Tesseract for this?
Tesseract is optimized for text. Audiveris was specifically trained for music notation, making it way more accurate on things like staves, dynamics, and noteheads.

#### Can I run this on my own server?
Sure, but you’ll need Java, PostgreSQL, and probably Docker. Expect about 2GB of setup files plus whatever library of sheet music you scrape. RAM usage peaks around 1GB for the largest jobs.
