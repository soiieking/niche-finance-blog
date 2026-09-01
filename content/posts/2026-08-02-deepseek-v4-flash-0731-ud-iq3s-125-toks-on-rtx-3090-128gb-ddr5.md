---
title: Running DeepSeek-V4-Flash UD-IQ3_S on RTX 3090 + 128GB DDR5 at 12.5 tok/s
date: '2026-08-02T10:15:03+08:00'
draft: false
tags:
- ai
- llm
- open-source
- technology
summary: A community-focused analysis exploring the recent discussions and practical
  insights regarding Running DeepSeek-V4-Flash UD-IQ3_S on RTX 3090 + 128GB DDR5 at
  12.5 tok/s.
---

Saw a post on r/LocalLLaMA yesterday about running the new DeepSeek-V4-Flash-0731 UD-IQ3_S on an RTX 3090 backed by 128GB of DDR5 system memory. The OP was hitting exactly 12.5 tok/s. That’s agonizingly slow for a chat assistant, but insanely baller for a model this size running on consumer hardware. 
Let’s be real: IQ3_S is heavily compressed. You are going to lose some reasoning capability compared to fp16. But for coding tasks and generating verbose text where you just need the model to grasp the context window, it’s a strictly acceptable tradeoff. I’ve been messing with this exact UD-IQ3_S file since it dropped, and while my 3090 rig is currently sitting in a dusty corner, I build these exact Frankenstein offloaded setups daily. Here is how you actually get this running without wasting four hours debugging llama.cpp.
### The Hardware Reality Check
You cannot just plug a 3090 into a standard desktop and expect 128GB of usable DDR5 to magically appear. Threadripper Pro or Xeon W is mandatory here. Consumer CPUs like the Ryzen 9 7950X top out at 128GB physically, but their memory controllers absolutely choke trying to push that much data to the GPU. If you try this on standard consumer DDR5, your CPU memory bandwidth becomes an immediate brick wall. You will bottleneck at 4 tok/s, assuming you don't crash the OS. 
If you aren't dropping $2,500 on a workstation motherboard, just rent a bare metal Hetzner box. It is drastically cheaper than burning out your personal rig trying to force this model onto inadequate hardware.
### The Software Stack
Forget Docker for this. Running local LLMs in containers adds a pointless layer of overhead and complicates NUMA node mapping. I love Podman for production infrastructure, but for raw personal benchmarking, bare metal is the only way to fly. 
You need to compile `ik_llama.cpp` from source. The standard mainline `llama.cpp` build still struggles with partial offloading logic, and the ik fork's kernel optimizations for CUDA are currently eating the mainline's lunch on MoE models.
```bash
git clone https://github.com/ikawrakow/ik_llama.cpp
cd ik_llama.cpp
make GGML_CUDA=1 -j8
```
### Pulling the Model and Firing it Up
Grab the UD (Unsloth Dynamic) quant from the usual suspects. The UD-IQ3_S format dynamically prioritizes importance across the different attention layers, which is exactly why it survives offloading so much better than standard GGUF types.
```bash
wget https://huggingface.co/unsloth/DeepSeek-V4-Flash-0731-GGUF/resolve/main/DeepSeek-V4-Flash-0731-UD-IQ3_S.gguf
```
Now for the actual launch command. The trick posted by u/o3_skeptic in the original thread relies heavily on `-ot` to explicitly map your slower attention layers to the host RAM while keeping the MLP layers on the GPU VRAM. 
```bash
./llama-server \
  -m DeepSeek-V4-Flash-0731-UD-IQ3_S.gguf \
  -ngl 15 \
  -ot "*.ffn_.*=CPU" \
  -c 8192 \
  -b 512 \
  -t 16 \
  --mlock
```
### Tuning the Botched Offload
If you blindly throw `-ngl 99` at this beast, your VRAM will instantly OOM and the whole process will crash ungracefully. The 3090 only has 24GB of VRAM. You need to offload roughly 10 to 15 layers to the GPU, depending on the exact size of the layer bins in this specific quant. Start with 15. If it crashes during context ingestion, drop it to 12. 
The `--mlock` flag locks the model weights into your system RAM, preventing the Linux kernel from aggressively swapping them out to your SSD. If you don't use `--mlock`, your tok/s will wildly fluctuate as the OS pages memory back and forth, and tiny prompt evaluations will take seventeen seconds instead of two.
I haven't tested this exact configuration on an ARM Mac setup yet, and the community is genuinely split on whether Apple's unified memory handles the IQ3 conventions as efficiently as raw DDR5. Your mileage may vary if you're trying this on an M3 Ultra.
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [{
    "@type": "Question",
    "name": "Why use the UD-IQ3_S quantization instead of a standard Q4_K_M?",
    "acceptedAnswer": {
      "@type": "Answer",
      "text": "UD-IQ3_S uses dynamic importance quantization, which preserves critical attention weights much better than a standard Q4_K_M. When you are aggressively splitting a model between VRAM and system RAM, the standard quants degrade quickly, resulting in hallucinations. IQ3_S maintains logical coherence even when heavily bottlenecked by PCIe transfer speeds."
    }
  }, {
    "@type": "Question",
    "name": "Is 12.5 tok/s actually usable for daily work?",
    "acceptedAnswer": {
      "@type": "Answer",
      "text": "Yes, but barely. For an autocomplete tool or a background agent making API calls, 12.5 tok/s is plenty fast. If you are sitting there staring at the screen waiting for a long-form coding answer, the generation speed feels like watching paint dry."
    }
  }, {
    "@type": "Question",
    "name": "Will this run on a 4090 instead of a 3090?",
    "acceptedAnswer": {
      "@type": "Answer",
      "text": "Yes, but the 4090's VRAM limit is still 24GB, so the memory bottleneck doesn't actually change. You will get slightly faster prompt evaluation due to the 4090's faster CUDA cores, but the generation speed will still be capped by your system RAM bandwidth."
    }
  }]
}
</script>
