---
title: I Made a Robot and It's Brain
date: '2026-08-18T12:00:00+08:00'
draft: false
tags:
- technology
- selfhosted
summary: A community-focused analysis exploring the recent discussions and practical
  insights regarding I Made a Robot and It's Brain.
---

## I Made a Robot and It's Brain
Did you think that's a line from a western? Haha kidding, it's for real.
### The Project
[Ghostrunner](https://github.com/biffer-co/good-software), my latest sideproject, is a workshops tool that helps you configure hardware setup, software installation, and infrastructure provisioning using a single YAML file. The idea? Your infrastructure as code.
### The Brain
The "brain" behind the server - an AMD Ryzen 9 5950X cutting-edge processing powerhouse. RAM: 128GB DDR4. And the OS - Arch Linux, of course.
### Why I Built It
Frustration with setup and configuration complexity, tweaking, and troubleshooting. Ghostrunner automates much of this voodoo.
### What's in the Brain
My Raspberry Pi 4 Model B + 8GB RAM for the headless server, running Raspberry Pi OS.
### How Long Did It Take
4 weeks of coding + testing. Plus 2 days assembly, and 5 days troubleshooting. Hardware assembly could go either way - depends on your level.
### Can I Build It?
For node-based things, yeah, in under a week.
### What Did I Learn
That you can laptop-mod hackers use Arch for infrastructure. So hardware, software, & OS altogether. The big twist here? It's not for the faint of heart.
### Troubleshooting
Spent a morning trying to rev the processor over 3.8 GHz. Set power limits, updated BIOS, and the Like. Made me a fan of B1 That'll teach me to check the compatibility with different software.
### Pricing vs Alternatives
Ghostrunner is a single-time setup. Free if you host it yourself. Else, it costs $xx. Compare to Spinach. Plus it compiles code right in your browser
### Step-by-step guideedo
Install Ansible using `pip install ansible``ansible-galaxy collections install -n ansible.net.pool`Run the following Ansible playbooks`ansible-playbook provision.yml --ask-vault-pass`Enter Vault password when prompted and follow the instructions. Recursively replacing: # Generate keys for provisioning and SSHaccesskey_leader
### Install Docker
Follow the Docker installation guide.
### Create a Volume
```
docker volume create myapp-data
```
### Set Owner
```
docker run -d -itv --rm --name myapp/myblog --volumes-from myapp-data goodsoftware/blog:latest /bin/sh -c "
  cd /app && owner -h . && useradd -u app user/app; dnf install git epyc;
"
```
## Summary
Building Ghostrunner is straightforward with a bit of Linux know-how. It automates server configuration, saves time, and gives you the ability to version control infrastructure. Not perfect, but worth it for the time saved in infrastructure management.
