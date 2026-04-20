---
dg-publish: true
permalink: /notes/week-4/kohya-lora-training/
---
# Week 4 Guide: Training Your LoRA with Kohya SS

Kohya SS gives you full control over training parameters and runs on your own machine. This guide is for students with a capable local NVIDIA GPU (RTX 3080 or better, 10 GB+ VRAM).

**If you do not have local GPU hardware, use the [Civitai guide](/notes/week-4/civitai-lora-training/) instead.** Cloud-hosted Kohya options are not reliably working at this time.

## Privacy

Your images never leave your machine. This is the most private training option available.

---

## Step 1: Prepare Your Dataset

Good data is the most important part of training. Take time here.

### How many images

| Subject | Recommended count |
|---|---|
| Face / portrait | 15-25 images |
| Body / full figure | 25-50 images |
| Style or aesthetic | 50-150 images |

More images are not always better. 15 well-varied images will outperform 60 taken in the same spot with the same lighting.

### What makes a good dataset

- **Varied angles and framing:** front, side, three-quarter, high/low — avoid all images looking identical
- **Varied lighting:** indoor, outdoor, soft, harder light
- **Varied expression, pose, or movement:** if training on a body or movement quality, capture the range of it, not just one frozen pose
- **Clean subject:** what you are training on should be clearly visible and not obscured
- **No duplicates or near-duplicates:** if two images look essentially the same, delete one

Resolution: at least 512x512 pixels. Larger is fine. Kohya will handle resizing.

### Folder structure

Create a folder named for this specific LoRA, as you will eventually train several and each should have its own folder. Use the trigger word or a short descriptive name:

```
myname-lora/
  dataset/
    15_myname/
  output/
```

The `dataset/` folder is what you point Kohya at. Inside it, you will create one subfolder containing your images and caption files — you will set this up in the next two steps.

The `output/` folder is where your trained LoRA files will be saved — create it now so it is ready when you configure training.

The `15_myname` image subfolder name encodes two pieces of information separated by an underscore:

- **The number** (`15`) is the **repeat count** — how many times each image is shown per training epoch. Kohya multiplies your image count by this number to get the effective training size per epoch. The goal is roughly 200-300 effective samples per epoch, so you adjust the repeat count based on how many images you have:

| Images in dataset | Use this repeat count |
| ----------------- | --------------------- |
| 15-25             | `15`                  |
| 25-50             | `10`                  |
| 50-100            | `5`                   |

For example: 20 images at repeat `15` means each epoch sees 300 effective samples. Without a repeat count, a small dataset would give the model too little to learn from per epoch.

**The word** (`myname`) is your **trigger word** — the word you will type in prompts to activate the LoRA. Choose something that would not appear in a normal image prompt. Your name, or an invented word like `xprsn7`, both work.

### Rename your images

This step and the next one together populate your image subfolder. Each image needs two files: the image itself and a matching `.txt` caption file with the same filename. This pairing is how training works: for every image the model sees, it reads the caption alongside it, learning to associate your trigger word with the visual patterns in your images.

#### 1. Check your file types

Kohya accepts `.jpg` and `.png` only — other formats will be ignored or cause errors. Navigate into your image subfolder and list the files:

**Windows (PowerShell):**
```powershell
cd "C:\lora-training\dataset\15_myname"
dir
```

**Mac (Terminal):**
```bash
cd /path/to/dataset/15_myname
ls
```

Look at the extensions. Common issues:
- **`.DNG`, `.CR2`, `.ARW`, or other raw formats** — must be converted to JPG before training
- **Mixed `.jpg` and `.JPG` and `.JPEG`** — all the same format; handled by the renaming script below

#### 2. Convert files if needed

The simplest approach is manual conversion:
- **Mac:** open in Preview, then File > Export and choose JPEG or PNG
- **Photoshop or any image editor:** File > Save As / Export As
- Or just start with images already in `.jpg` or `.png` — this avoids the conversion step entirely

If you have many files to convert and prefer a scripted approach, see [converting files with the command line](/notes/week-4/kohya-file-conversion/), then come back here.

#### 3. Rename your images

Rename your images to clean, consistent names to avoid issues with spaces, special characters, or duplicates during training.

Replace `myname` with your trigger word. The scripts below assume your images are JPGs — if yours are PNGs, replace `.jpg` with `.png`.

**Windows (PowerShell):**
```powershell
$i = 1
Get-ChildItem -Filter *.jpg | ForEach-Object {
    Rename-Item $_.Name -NewName ("myname_{0:D2}.jpg" -f $i)
    $i++
}
```

**Mac (Terminal):**
```bash
i=1; for f in *.jpg; do mv "$f" "$(printf 'myname_%02d.jpg' $i)"; ((i++)); done
```

Run `dir` (Windows) or `ls` (Mac) to confirm the files have been renamed before moving on.

### Caption files

Each image needs a matching `.txt` file with the exact same filename. The scripts below create one for every image in the folder, each containing just your trigger word.

Replace `myname` with your trigger word. If your images are PNGs, replace `.jpg` with `.png`.

**Windows (PowerShell):**
```powershell
Get-ChildItem -Filter *.jpg | ForEach-Object {
    Set-Content -Path "$($_.BaseName).txt" -Value "myname"
}
```

**Mac (Terminal):**
```bash
for f in *.jpg; do echo "myname" > "${f%.jpg}.txt"; done
```

You should end up with one `.txt` file for every image — same name, different extension.

If you want more flexibility in how the trained LoRA behaves, you can write fuller captions like:

```
myname, portrait, looking forward, soft lighting
```

Fuller captions help the LoRA specialize on the subject itself rather than also locking in the lighting, pose, or setting from your training images — making it easier to vary those things in prompts later. For a first LoRA, the single trigger word is fine and produces reliable results.

---

## Step 2: Install Kohya SS

Before starting, make sure you have:

- **Python 3.10** — from python.org; enable "Add to PATH" during install. Note: Python 3.11 or newer will not work — Kohya SS requires specifically Python 3.10.
- **Git for Windows** — from git-scm.com

Then in PowerShell or Command Prompt:

```cmd
git clone --recursive https://github.com/bmaltais/kohya_ss.git
cd kohya_ss
.\gui-uv.bat
```

On first run, the script may prompt you to install `uv` (a package manager it depends on) — press Y to allow it. It will then install all required dependencies automatically. This takes a few minutes.

Once finished, open `http://127.0.0.1:7860` in your browser.

**Note:** If installation fails, the most common cause is a mismatch between your CUDA driver version and the default PyTorch build in the installer. Check your driver version in the NVIDIA control panel and ask your instructor if you hit this.

### VRAM considerations

| GPU | VRAM | Notes |
|---|---|---|
| RTX 3080 | 10 GB | Works at batch size 1, resolution 512 |
| RTX 3090 / 4090 | 24 GB | Works well at these settings |
| RTX 4080 | 16 GB | Works well at these settings |
| RTX 3070 / 4070 | 8 GB | Tight but possible — close other GPU applications if you get OOM errors |

---

## Step 3: Configure and Run Training

> **Important:** Kohya defaults to Dreambooth training, not LoRA. Before doing anything else, click the **LoRA** tab at the top of the interface. If you skip this step, the Network Rank, Network Alpha, and other LoRA-specific fields will not appear and you will be configuring the wrong training method.

The Kohya interface is organised into collapsible sections. Work through them in order.

**Accelerate launch**

Find the **Accelerate launch** section and set:

| Field | Value |
|---|---|
| Machine type | No distributed training |
| Dynamo backend | no |
| Mixed precision | fp16 |
| num_cpu_threads_per_process | 2 |

**Model**

| Field | Value |
|---|---|
| Pretrained model name or path | `runwayml/stable-diffusion-v1-5` — Kohya fetches it automatically if not already cached |
| Trained Model output name | `myname-lora` |
| Save trained model as | `safetensors` |
| Save precision | `fp16` |

**Folders**

| Field | Value |
|---|---|
| Image folder (containing training images subfolders) | `C:\myname-lora\dataset\` |
| Output directory for trained model | `C:\ComfyUI_windows_portable\ComfyUI\models\loras\` |

**Parameters — Basic**

Leave LoRA type set to **Standard**. Change only the fields listed below:

| Field | Value | Notes |
|---|---|---|
| Train batch size | `1` | |
| Max train epoch | `10` | Start here; adjust after reviewing samples |
| Max train steps | leave blank | Kohya calculates this automatically — do not set it manually |
| Save every N epochs | `1` | Saves checkpoints so you can compare |
| Cache latents | checked | Leave it on |
| LR Scheduler | `cosine` | |
| Optimiser | `AdamW8bit` | |
| Learning rate | `0.0001` | |
| LR warmup (% of total steps) | `0` | Set slider to 0 |
| Max resolution | `512,512` | |
| Enable buckets | checked | |
| Network Rank (Dimension) | `32` | |
| Network Alpha | `16` | Half the rank value |

Network Rank and Network Alpha are near the bottom of the Basic section, below the LoRA+ ratio fields.

**Parameters — Samples**

| Field | Value |
|---|---|
| Sample every n steps | `0` |
| Sample every n epochs | `1` |
| Sample sampler | `euler_a` |

In the **Sample prompts** text box, enter one prompt per line. Kohya generates one image per line each time it samples — four lines gives you four images per epoch to track progress:

```
myname, portrait, looking at camera --n blurry, bad anatomy, watermark --w 512 --h 512 --l 7 --s 20 --d 42
myname, portrait, three quarter view --n blurry, bad anatomy, watermark --w 512 --h 512 --l 7 --s 20 --d 123
myname, portrait, side profile --n blurry, bad anatomy, watermark --w 512 --h 512 --l 7 --s 20 --d 777
myname, portrait, looking down --n blurry, bad anatomy, watermark --w 512 --h 512 --l 7 --s 20 --d 999
```

What each flag does:

- **`--n`** (negative prompt): what to avoid in sample images
- **`--w`** / **`--h`** (width / height): resolution of each sample image in pixels. `512 512` matches the training resolution and is a good baseline. You can vary these across prompts to check how the LoRA holds up at different sizes — for example, try one line at `512 512`, one at `512 768` (portrait crop), one at `768 512` (landscape). Avoid going below 512 on either axis.
- **`--l`** (CFG scale): how strictly the model follows the prompt — `7` is a balanced default
- **`--s`** (steps): denoising steps per sample — `20` is fast and sufficient for checking progress
- **`--d`** (seed): fixes the random seed for that image. Using a fixed seed per line means each epoch generates the exact same four prompts under the same conditions, so differences between epochs reflect what the LoRA has learned rather than random variation.

Replace `portrait` with something that fits your subject: `full body, dancing`, `illustration style`, etc.

### Start training

Click **Start Training**. For a 20-image dataset at these settings, expect roughly 5-15 minutes on a 3090 or 4090.

Watch the log output in the terminal. Loss values should decrease over time and eventually plateau. If the sample images start looking plastic or like exact copies of your training photos, you are overfitting — stop early and use an earlier checkpoint.

You will also notice `.npz` files appearing in your dataset folder. These are cached latents — Kohya pre-encodes your images into the model's latent space and saves them so it does not have to repeat that work each epoch. They are not output files and you do not need to do anything with them.

### Get your LoRA files

When training finishes, there will be one `.safetensors` per saved checkpoint (e.g. `myname-lora-000001.safetensors` through `myname-lora-000010.safetensors`) in your ComfyUI loras folder. No download or transfer needed — they are already in the right place.

---

## Using Your LoRA in ComfyUI

Your checkpoints are already in `ComfyUI_windows_portable\ComfyUI\models\loras\`. In ComfyUI:

1. Click the browser refresh button (or restart via Manager if models are not showing)
2. Right-click the canvas, search for `Load LoRA`, and add the node
3. Connect it between the `Load Checkpoint` node and your `CLIP Text Encode` and `KSampler` nodes
4. Select your LoRA file from the dropdown
5. Set strength values — **Model strength** and **CLIP strength** both start at 0.7-1.0; lower values blend the LoRA more subtly, higher values push it harder
6. Use your trigger word in the positive prompt:
   ```
   myname, portrait, natural lighting, detailed face
   ```
7. **Compare checkpoints:** load each saved checkpoint one at a time with the same seed and prompt. Earlier checkpoints are often better — they generalise more naturally and overfit less.

---

## Common Problems

**Output looks melted, distorted, or like a copy of one training image**
You likely overtrained or the dataset lacked variety. Try an earlier checkpoint, or retrain with fewer epochs or more varied images.

**Trigger word does not seem to activate the LoRA**
Check that the trigger word in your prompt exactly matches what is in your caption files. Check that LoRA strength is above 0.5.

**LoRA strength is high but likeness is weak**
Training may not have fully converged. Retrain with a higher repeat count (e.g. change `10_myname` to `20_myname` in your folder name).

**Sample images show the wrong person or wrong features**
The base model has demographic biases that can bleed through when the LoRA is still learning. This usually improves with more epochs. Fixed seeds in your sample prompts (the `--d` flag) help you track whether the same prompt is consistently failing — if so, that seed's prior is strong and should be overridden by later training.

**Network Rank, Network Alpha, and other fields are missing from the interface**
You are in Dreambooth mode, not LoRA mode. Click the **LoRA** tab at the top of the Kohya interface and reconfigure from there.

**Out of memory error during training**
Close other GPU applications and try again. If it persists, reduce Max resolution to `448,448`.

**Installation fails**
Most likely a CUDA driver version mismatch. Check your NVIDIA driver version and ask your instructor.
