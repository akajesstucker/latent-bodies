---
dg-publish: true
permalink: /notes/week-4/civitai-lora-training/
---
# Week 4 Guide: Training Your LoRA with Civitai

Civitai is the largest public platform for sharing and training image generation models. It is worth knowing what it actually is before you use it: the site built its community primarily around NSFW and uncensored image generation. You will see explicit content throughout the site unless you opt out in your account settings. In April 2025, under pressure from payment processors, Civitai shifted its main site toward PG-13 content and adult content migrated to a separate red-themed site. SD 1.5 LoRA training is unaffected by these changes and still works the same way.

Civitai is a reasonable fallback option if Kohya SS is giving you trouble. It handles setup and configuration for you: no command line, no installation, no pod required. The trade-off is less control and some privacy considerations (see below).

---

## Privacy: Should You Upload Your Images Here?

Before uploading personal photos, understand what happens to them:

- **Training images are retained for 30 days** after a training job completes, then deleted according to Civitai's stated policy.
- **Captions you write are stored** as metadata on your output model file and may persist if you share or publish the model.
- **If you publish your LoRA publicly**, your training data may be retained longer or used to improve Civitai's systems.
- **Civitai is a public commercial platform.** Your images pass through their servers and you are trusting their data policy.

For this course: uploading images of yourself is your choice. If you are comfortable with a 30-day retention window on a commercial platform, Civitai is usable. If you prefer to keep your training data fully local, use the Kohya SS guide instead. Do not upload images of other people without their consent.

Civitai's data policy is at civitai.com/content/training/data-policy.

---

## Buzz: Civitai's Currency

Civitai uses an in-platform currency called Buzz to pay for training runs. There are two types:

- **Blue Buzz** — earned for free through site activity: logging in daily, rating images, engaging with the site. You accumulate this passively.
- **Yellow Buzz** — purchased with real money. As of May 2025, **Civitai no longer accepts credit or debit cards** (Visa and Mastercard cut off the platform over its adult content). Yellow Buzz must be purchased with cryptocurrency through Coinbase. If you do not have a crypto wallet, Yellow Buzz is not accessible to you.

**What a training run costs:** typically 1,000-3,000 Buzz. Blue Buzz earned through daily site activity may be enough for one run if you have been active on the site. Check your balance under your profile icon before starting.

**Supporter tiers** ($5-10/month) provide a monthly Buzz allowance and are paid via crypto.

**Bottom line:** if you do not want to deal with cryptocurrency, you will need to earn enough Blue Buzz through free site activity before training. This may take a few days of visiting and rating images on the site.

---

## Step 1: Prepare Your Images

Good data is the most important part of training. Civitai handles captioning automatically, so you do not need to create caption files or folder structures. Just focus on collecting good images.

### How many images

| Subject | Recommended count |
|---|---|
| Face / portrait | 15-25 images |
| Body / full figure | 25-50 images |
| Style or aesthetic | 50-150 images |

More images are not always better. 15 well-varied images will outperform 60 taken in the same spot with the same lighting.

### What makes a good dataset

- **Varied angles and framing:** front, side, three-quarter, high and low — avoid all images looking identical
- **Varied lighting:** indoor, outdoor, soft, harder light
- **Varied expression, pose, or movement:** if training on a body or movement quality, capture the range of it, not just one frozen pose
- **Clean subject:** what you are training on should be clearly visible and not obscured
- **No duplicates or near-duplicates:** if two images look essentially the same, delete one

Resolution: at least 512x512 pixels. Save your images as JPG or PNG.

### Your trigger word

Before uploading, decide on a **trigger word** — the word you will type in prompts to activate the LoRA. Choose something that would not appear in a normal image prompt. Your name, or an invented word like `xprsn7`, both work. You will add this to each image's caption in the next step.

---

## Step 2: Create a Civitai Account

Go to civitai.com and sign up. Verify your email. Be aware the site contains explicit content by default — adjust your content settings under your profile if needed.

---

## Step 3: Check Your Buzz Balance

Log in and check your Buzz balance under your profile icon. If you have enough Blue Buzz (typically 1,000-3,000 for one run), you can proceed. If not, spend a few days on the site earning free Buzz through activity, or set up a Coinbase account to purchase Yellow Buzz with cryptocurrency.

The training tool will show the exact Buzz cost before charging you.

---

## Step 4: Start a Training Job

1. Log in to civitai.com
2. Click your profile icon and select **Train a Model**, or go to the **Create** menu and select **Train**
3. Click **Create New Training**

---

## Step 5: Configure Your Training Job

**Select model type** — choose **LoRA**

**Choose a base model** — select **SD 1.5**

**Upload your images** — click the upload area and select all your training images at once. Civitai will display them in a grid after uploading.

**Review and edit captions**

This is the most important step. Civitai auto-generates captions for each image using an AI tagger. You need to add your trigger word to each one:

- Click each image to see its auto-generated caption. You will see tags like `portrait, looking at viewer, soft lighting, brown hair`.
- **Add your trigger word to the beginning of each caption**, followed by a comma: `myname, portrait, looking at viewer, soft lighting, brown hair`
- Fix any badly wrong captions manually (wrong gender, wrong features)
- Use the batch edit tool to add your trigger word to all images at once, then review individually for errors

**Training settings**

- **LoRA name:** what appears on your Civitai profile if you share it
- **Number of epochs:** 10
- **Learning rate:** leave on default
- **Resolution:** 512
- Leave other settings at their defaults for a first run

**Review cost and confirm** — Civitai shows the total Buzz cost before charging. Click **Start Training** to confirm and submit.

---

## Step 6: Wait for Training to Complete

You will receive an email when it is done, typically 15-45 minutes depending on queue time. You do not need to keep the browser open. Check the status by going to your profile and looking at your training history.

---

## Step 7: Download Your LoRA

1. Go to your profile and click on your completed training job
2. Click **Download** to save the `.safetensors` file to your computer

If multiple checkpoints were saved, try the middle epoch first — the final epoch is sometimes overfitted.

---

## Using Your LoRA in ComfyUI

**1. Place the file in the correct folder:**

- **RunPod (ComfyUI pod):** upload to `/workspace/runpod-slim/ComfyUI/models/loras/` via JupyterLab
- **Local:** place in `ComfyUI_windows_portable\ComfyUI\models\loras\`

**2. Refresh ComfyUI:** click the browser refresh button, or restart via Manager if models are not showing.

**3. Add a Load LoRA node to your workflow:**
- Right-click the canvas, search for `Load LoRA`
- Connect it between the `Load Checkpoint` node and your `CLIP Text Encode` and `KSampler` nodes
- Select your LoRA file from the dropdown

**4. Set strength values:**
- **Model strength:** 0.7-1.0 is a good starting range
- **CLIP strength:** 0.7-1.0
- Lower values blend the LoRA more subtly; higher values push it harder

**5. Use your trigger word in the positive prompt:**

```
myname, portrait, natural lighting, detailed face
```

**6. Compare checkpoints:** if you downloaded multiple epochs, load them one at a time with the same seed and prompt. Earlier checkpoints are often better — they generalise more naturally and overfit less.

---

## Common Problems

**Training job failed**
Check the error message on the job status page. Common causes: images too small (below 512px), unsupported file format (use JPG or PNG), or account verification issue. You are not charged Buzz for failed jobs.

**Output looks melted, distorted, or like a copy of one training image**
You likely overtrained or the dataset lacked variety. Try an earlier checkpoint if available, or retrain with fewer epochs or more varied images.

**Trigger word does not seem to activate the LoRA**
Check that the trigger word in your prompt exactly matches what you added to the captions. Check that LoRA strength is above 0.5.

**LoRA strength is high but likeness is weak**
The training may not have fully converged. Try retraining with more images or a higher epoch count.

**Cannot purchase Buzz**
Credit and debit cards are not accepted as of May 2025. You need to earn Blue Buzz through free site activity, or purchase Yellow Buzz via Coinbase using cryptocurrency.

---

## Before Class Checklist

- [ ] Dataset collected: images of your chosen subject, varied, in JPG or PNG format
- [ ] Trigger word decided
- [ ] Civitai account created and Buzz balance checked
- [ ] Training job completed and `.safetensors` file downloaded
- [ ] File uploaded to ComfyUI `models/loras/` folder
- [ ] Test generation run in ComfyUI with trigger word — output shows subject likeness
