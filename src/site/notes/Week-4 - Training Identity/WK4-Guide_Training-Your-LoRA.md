---
dg-publish: true
permalink: /notes/week-4/lora-training/
---
# Week 4 Guide: Training Your LoRA

A LoRA (Low-Rank Adaptation) is a small file that teaches an image model to recognize a specific subject, style, or movement quality. For this course the focus is on the body — your own face or figure, a character you perform as, a movement style, or a visual style drawn from your own work. You will train it on your own images, download a `.safetensors` file, and use it in ComfyUI.

**Start with Civitai.** It is web-based, requires no setup, and is the only reliably working cloud training option for SD 1.5 right now. If you have a capable local NVIDIA GPU and want more control, Kohya SS is the advanced option, but it requires local hardware.

- [[WK4-Guide_Civitai-Lora-Training|Civitai]] **(recommended)** — web-based, no setup, works immediately. Start here if you have no local GPU.
- [[WK4-Guide_Kohya-Lora-Training|Kohya SS]] **(local GPU only)** — more control over training. Requires a local NVIDIA GPU (RTX 3080 or better) and a local install. Cloud-hosted Kohya is not reliably working at this time.
- [Krea](https://www.krea.ai/) **(FLUX only, not this course's pipeline)** — web-based, free tier available. Krea trains FLUX LoRAs, not SD 1.5. The output requires a FLUX base model and is not compatible with AnimateDiff. Only use this if you intend to work outside the SD 1.5 pipeline used throughout this course.
