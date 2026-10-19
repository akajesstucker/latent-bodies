---
dg-publish: true
permalink: /notes/week-4/civitai-lora-training/
---
# Week 4 Guide: Training Your LoRA with Civitai

Civitai is the largest public platform for sharing and training image generation models. As of an April 2026 platform split, Civitai now operates as two separate sites: **civitai.com**, which is SFW-only, and **civitai.red**, which hosts NSFW content and generation. Which site a piece of content appears on is based on that content's own rating, not your account. SFW content you post appears on both sites, NSFW content only appears on Red. For this course, you'll only ever need civitai.com: LoRA training is treated as SFW infrastructure work regardless of what Buzz currency funds it, so there's no need to visit civitai.red or interact with NSFW content at any point in this workflow.

Civitai handles setup and configuration for you: no command line, no installation, no pod required. The trade-off is less control and some privacy considerations (see below).

---

## Privacy: Should You Upload Your Images Here?

Training files get a shorter lifecycle than the rest of Civitai: your training images and the resulting `.safetensors` are each retained for 30 days, then deleted — download your file as soon as training finishes, don't wait. Captions you write persist permanently as metadata on the output model.

That 30-day window is about *storage*, not *rights*. Civitai's Terms of Service grant them a broad, sublicensable license over anything you upload, whether marked private or not, and nothing in their Terms or Privacy Policy says whether your photos are used to train their own models — it's genuinely unaddressed, not ruled out. At upload you'll attest you're the person depicted or have their consent.

If a 30-day storage window and a broad usage license on a commercial platform doesn't sit right with you, use the Kohya SS guide instead and keep your data fully local. Do not upload images of other people without their consent.

Details: civitai.com/content/training/data-policy, civitai.com/content/tos, civitai.com/content/privacy.

---

## Buzz: Civitai's Currency

Civitai uses an in-platform currency called Buzz to pay for training runs. There are three types, and which one you use doesn't matter for training — all three work:

- **Green Buzz** — purchased with a regular credit card, processed through Stripe. This is the straightforward paid option.
- **Blue Buzz** — earned for free through site activity: logging in daily, rating images, viewing ads. You accumulate this passively, and it's sufficient to fund LoRA training.
- **Yellow Buzz** — crypto-only (via Coinbase), and only usable for NSFW generation on the separate civitai.red site.

**What a training run costs:** 
10,000 Buzz costs $10 USD (~9 EUR). 
Training an SD1.5 LoRA costs 500 Buzz
So 1 LoRA for this course costs $0.50 USD (0,45 EUR)

---

## Step 1: Prepare Your Images

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

Resolution: at least 512x512 pixels. Save your images as PNG, JPG, JPEG, WEBP, GIF, or BMP.

### Your trigger word

Before uploading, decide on a **trigger word** — the word you will type in prompts to activate the LoRA. Choose something that would not appear in a normal image prompt. A special word or name, or an invented code word like `xprsn7`, all work. You will add this to each image's caption in the next step.

---

## Step 2: Create a Civitai Account

Go to civitai.com and sign up. Verify your email. civitai.com is SFW-only, so you won't encounter explicit content there by default.

---

## Step 3: Check Your Buzz Balance

Log in and check your Buzz balance under your profile icon. Select 'Buy Buzz'

---

## Step 4: Start a Training Job

1. Log in to civitai.com
2. Go to the **Create** menu and select **Train a LoRA**
3. Click **+ New Training**

---

## Step 5: Configure Your Training Job

**Part 1: Main settings**

- Choose Media: Image
- Choose Type: Character, Style, or Concept
- Choose a Base Model:  SD 1.5 (see more)
- Choose Version: Standard
- Click 'Continue to data'

**Part 2: Data & labels**

- Upload your images with **Upload files**, or — if you already have a dataset prepared as matching image + `.txt` caption file pairs (see the [Kohya SS guide's dataset prep section](/notes/week-4/kohya-lora-training/#step-1-prepare-your-dataset) for that format) — use **Import .zip**, which accepts a zip of images with matching `.txt` labels directly.
- Accepted image formats: PNG, JPG, JPEG, WEBP, GIF, BMP.
- Labeling defaults to **Automatic**, tagged in booru-style tags (SD 1.5's native format) at no extra cost. Switch to **Manual** if you'd rather write your own captions.
- A **"Never apply"** field lets you exclude specific tags from auto-labeling.
- Use the 'trigger word' to specify your special prompt cue to activate your LoRA when you are using it in Comfy UI to generate new images.
- 'Manage Tags' to see what tags have been applied throughout and adjust them
- When manually tagging, you can experiment with applying very specific different tags to each image, or the same set of tags to all of them. 
- 'Continue to review' when finished tagging.

**Part 3: Review & start**

- Name your LoRA
- Try first with default Advanced Settings and Steps 2000
- Set sample prompts -- these will be used to generate sample images using your LoRA that you can preview epochs with (versions of your LoRA that will come out at the end)

---

## Step 6: Wait for Training to Complete

You will receive an email when it is done, typically 15-45 minutes depending on queue time. You do not need to keep the browser open. Check the status by going to your profile and looking at your training history.

---

## Step 7: Download Your LoRA

1. Go to your profile and click on your completed training job
2. Click **Download** to save the `.safetensors` files to your computer

If multiple checkpoints were saved, try the middle epoch first, as the final epoch is sometimes overfitted.

---

