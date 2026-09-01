---
title: 'Showcasing AI Side Projects: What Works and What Doesn''''t'
date: '2026-08-16T12:00:08+08:00'
draft: false
tags:
- indie-hacker
- business
- technology
summary: 'A community-focused analysis exploring the recent discussions and practical
  insights regarding Showcasing AI Side Projects: What Works and What Doesn''''t.'
---

I've spent countless hours browsing r/sideproject, and one thing that always catches my eye is people showing off their AI creations. Recently, a user asked, "What have you built with AI that you're actually proud of?" which got me thinking about my own experiences. One commenter mentioned building a chatbot using Rasa 3.2 that could understand natural language queries, which is pretty impressive.
## The Good and the Bad of AI Frameworks
When it comes to building AI projects, the choice of framework can make or break your experience. I love TensorFlow 2.x, but it has one fatal flaw: it's a massive resource hog. I mean, who needs 16GB of RAM just to run a simple model? This is overkill for most people, especially those just starting out. On the other hand, PyTorch 1.12 is a great alternative that's much more lightweight and easier to use. However, the community is genuinely split on this, with some swearing by TensorFlow's flexibility and others preferring PyTorch's simplicity.
### Containerization: A Necessary Evil
Once you've chosen your framework, you need to think about deployment. I've tried both Docker and Podman, and while Docker is the more popular choice, I think Podman is the way to go. It's just as powerful, but without the bloat. Plus, it's included in most Linux distributions, so you don't need to worry about installing extra software. For example, I was able to get a PyTorch model up and running on a Hetzner CX11 server with 2GB of RAM for just $3.50/month – a steal compared to DigitalOcean's prices.
Another user in the thread mentioned using Hugging Face's Transformers library to build a text classification model. This is a great choice, as the library is incredibly easy to use and has pre-trained models for a wide range of tasks. However, I haven't tested this on ARM, so your mileage may vary if you're using a Raspberry Pi or other single-board computer.
## The Dark Side of AI
One thing that's often overlooked in AI side projects is ethics. I mean, think about it: you're creating a machine that can potentially replace human jobs or even make decisions that affect people's lives. This is not something to be taken lightly. As one commenter pointed out, "it's not just about building something cool, it's about building something responsible." I couldn't agree more. For instance, I was experimenting with a face recognition model using OpenCV 4.5, and I realized just how easy it is to misuse this technology.
In terms of performance, I've found that optimizing your model can make a huge difference. For example, I was able to reduce the inference time of my chatbot from 500ms to 50ms just by using a more efficient algorithm and pruning the model. This may seem like a small thing, but it can be the difference between a usable product and one that's just frustrating to use.
### Real-World Applications
So, what have I built with AI that I'm actually proud of? Well, I created a simple image classification model using Keras 2.4 that can identify different types of flowers. It's not going to change the world, but it's a fun project that showcases the power of AI. I've also been experimenting with using AI to generate music, which is surprisingly easy to do using libraries like Music21 and TensorFlow.
In conclusion – just kidding, I'm not going to say that. The point is, building AI projects can be a wild ride, full of ups and downs. But with the right framework, a bit of creativity, and a lot of patience, you can create something truly amazing.
## FAQ
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "What is the best AI framework for beginners?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "PyTorch 1.12 is a great choice for beginners due to its simplicity and ease of use."
      }
    },
    {
      "@type": "Question",
      "name": "How much does it cost to deploy an AI model?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "The cost of deployment can vary widely, but you can get started with a basic server for around $3.50/month."
      }
    },
    {
      "@type": "Question",
      "name": "What are the ethical considerations of building AI projects?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "It's essential to consider the potential impact of your project on society and ensure that you're building something responsible and fair."
      }
    }
  ]
}
