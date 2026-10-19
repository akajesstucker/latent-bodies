---
dg-publish: true
permalink: /notes/week-4/lora-training/
---
# Week 4 Guide: Training Your LoRA

A LoRA (Low-Rank Adaptation) is a small file that teaches an image model to recognize a specific subject, style, or movement quality. For this course the focus is on the body — your own face or figure, a character you perform as, a movement style, or a visual style drawn from your own work. You will train it on your own images, download a `.safetensors` file, and [use it in ComfyUI](/notes/week-4/using-custom-loras/).

**Start with Civitai.** It is web-based, requires no setup, and is the only reliably working cloud training option for SD 1.5 right now. If you have a capable local NVIDIA GPU and want more control, Kohya SS is the advanced option, but it requires local hardware. If you'd rather not upload your images to any commercial platform and don't have a local GPU, Jess can train it for you instead — see below.

- [Civitai](/notes/week-4/civitai-lora-training/) **(recommended)** — web-based, no setup, works immediately. Start here if you have no local GPU and are comfortable with Civitai's data terms (see that guide's privacy section).
- [Kohya SS](/notes/week-4/kohya-lora-training/) **(local GPU only)** — more control over training, and your images never leave your machine. Requires a local NVIDIA GPU (RTX 3080 or better) and a local install. Cloud-hosted Kohya is not reliably working at this time.
- **Jess trains it for you** — for students with no local GPU who would rather not use Civitai. See "Have Jess Train It For You" below.

---
## Have Jess Train It For You

If you don't have a local GPU and would rather not upload your images to Civitai, Jess can run the training on her own machine instead. Your images are used only for that training run and are not uploaded anywhere, published, or kept past it.

**What to send:** a single `.zip` file containing:

- Your training images (`.jpg`, `.png`, or `.webp`)
- **One matching `.txt` caption file per image, with the exact same filename as its image** (e.g. `portrait01.jpg` needs `portrait01.txt` alongside it) — prepare these yourself before sending. See the [dataset prep and caption guidance in the Kohya SS guide](/notes/week-4/kohya-lora-training/#step-1-prepare-your-dataset) for how many images to include, what makes a good dataset, and how to write captions. Pick your trigger word the same way described there.
- Do not send a repeat-count folder — Jess will set that based on your image count.

**How to send it:** email the `.zip` to jesstucker.studio@gmail.com.

Do not include images of other people without their consent.

---
