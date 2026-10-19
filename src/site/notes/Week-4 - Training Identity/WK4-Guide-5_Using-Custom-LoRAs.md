---
dg-publish: true
permalink: /notes/week-4/using-custom-loras/
---
# Week 4 Guide: Using Custom LoRAs in ComfyUI

Once you have a trained `.safetensors` file — from Civitai, from Kohya SS, or from Jess — here's how to load it into ComfyUI and use it in a generation. Follow the section for your platform.

---

## Local

**1. Place the file.** If you trained with Kohya SS, your checkpoints are already in `ComfyUI_windows_portable\ComfyUI\models\loras\` — no transfer needed. If you downloaded a `.safetensors` from Civitai or received one from Jess, move it there yourself.

**2. Refresh ComfyUI:** click the browser refresh button, or restart via Manager if the file isn't showing.

**3. Add a Load LoRA (Model and CLIP) node to your workflow:**
- Right-click the canvas, search for `Load LoRA`, and pick **Load LoRA (Model and CLIP)** specifically — the plain "Load LoRA" node is model-only and has no `clip` socket, which you need
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

**6. Compare checkpoints:** load each saved checkpoint one at a time with the same seed and prompt. Earlier checkpoints are often better — they generalise more naturally and overfit less.

---

## Comfy Cloud

Comfy Cloud's Import feature only accepts a Civitai or Hugging Face link — there's no direct file upload. For your own trained LoRA, **use Hugging Face as a free, private host**, even if you trained on Civitai: Civitai's own private-model option requires a paid subscription, and a freshly-trained model that hasn't been published isn't importable at all. Hugging Face's private repos are free with no subscription needed.

**1. Create a Hugging Face account** (if you don't already have one) at huggingface.co.

**2. Create a Hugging Face access token.** Go to huggingface.co/settings/tokens → **Create new token** → **Read** type (read access is all that's needed, no write/delete). This isn't tied to any specific repo, so it's easiest to do now while you're already setting up your account.

**Hugging Face shows you the token value exactly once, right after you create it — copy it and paste it somewhere you can get back to (a notes app, a password manager) before closing that screen.** If you close it without copying, there's no way to view it again — you'd have to delete the token and create a new one. You'll need this value in step 6.

**3. Create a new model repository:** go to huggingface.co/new, give it a name, and select **Private** — only you can see or commit to it. Click **Create model**.

**4. Upload your `.safetensors` file** to the repo, using the web interface's upload option.

**5. Copy the direct link to the file itself** — not the repo's main page. Click the `.safetensors` file in your repo's file list to open it, then copy that page's URL (it looks like `huggingface.co/your-username/your-repo-name/blob/main/your-file.safetensors`).

**6. Add the token to Comfy Cloud.** In Comfy Cloud, go to **Settings → Secrets**, add a new secret with **Provider: Hugging Face**, and paste in the token from step 2. Without this, the import fails.

**7. Import the file into Comfy Cloud.** Click **Models** in the left sidebar, then **Import**, and paste the file link from step 5. Confirmed live, full pipeline tested end to end: the import succeeds and the file shows up immediately in a Load LoRA node's dropdown. Unlike a Civitai import, **the model type isn't auto-detected** — you'll need to select **LoRA** yourself from the type dropdown before clicking Import. The stored filename comes through as `yourusername__yourrepo__yourfilename.safetensors` (a mangled combination of your username, repo, and filename) — this is expected, not an error.

**8. Add a Load LoRA (Model and CLIP) node to your workflow:**
- Right-click the canvas, search for `Load LoRA`, and pick **Load LoRA (Model and CLIP)** specifically — the plain "Load LoRA" node is model-only and has no `clip` socket, which you need
- Connect it between the `Load Checkpoint` node and your `CLIP Text Encode` and `KSampler` nodes
- Select your LoRA file from the dropdown

**9. Set strength values:**
- **Model strength:** 0.7-1.0 is a good starting range
- **CLIP strength:** 0.7-1.0
- Lower values blend the LoRA more subtly; higher values push it harder

**10. Use your trigger word in the positive prompt:**

```
myname, portrait, natural lighting, detailed face
```

**11. Compare checkpoints:** if you have multiple epochs, load them one at a time with the same seed and prompt. Earlier checkpoints are often better — they generalise more naturally and overfit less.

---

## Using Public LoRAs from Civitai (or Elsewhere)

Training your own LoRA isn't the only option — Civitai hosts thousands of LoRAs other people have already trained and shared, for styles, aesthetics, objects, and more. You can combine one of these with your own trained LoRA in the same workflow (e.g. your face LoRA + someone else's style LoRA).

Finding one uses the same Civitai browse/filter process as Week 3's ["Try a Different Base Model"](/notes/week-3/controlnet-and-ipadapter/#try-a-different-base-model-optional) — filter by **Type: LoRA** and **Base Model: SD 1.5** (this course's workflows are SD1.5 only). **A public LoRA is different from your own private one here:** since it's already published, you can import it directly from its Civitai page link on Comfy Cloud — no Hugging Face detour needed, and no API key required. Confirmed live: a Civitai LoRA import resolves to the model's original filename, with no renaming needed.

Two things to do differently for a public LoRA, not covered above:

- **Check the license** on the model's page before using it beyond personal class experimentation — not every LoRA allows commercial use or skips crediting requirements.
- **Use the trigger word or prompt phrasing the model's page recommends**, in place of your own.
