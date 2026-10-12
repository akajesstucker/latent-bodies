---
dg-publish: true
permalink: /notes/week-3/controlnet-and-ipadapter/
---
# WK3 Guide: ControlNet and IPAdapter

---

## TLDR: Everything You Need to Download

**Comfy Cloud users:** models are not downloaded to a folder — they are imported through the Model Library instead. Skip the model rows below and follow the Comfy Cloud subsection inside Part 1 and Part 2. You still need the Comfy Cloud workflow files, linked in Part 4.

**Local users:** download these files at the start of class and place each one in the subfolder shown, inside your `ComfyUI/` directory.

| File | Folder | Link |
|------|--------|------|
| `control_v11p_sd15_openpose_fp16.safetensors` | `models/controlnet/` | [Download](https://huggingface.co/comfyanonymous/ControlNet-v1-1_fp16_safetensors/resolve/main/control_v11p_sd15_openpose_fp16.safetensors) |
| `control_v11f1p_sd15_depth_fp16.safetensors` | `models/controlnet/` | [Download](https://huggingface.co/comfyanonymous/ControlNet-v1-1_fp16_safetensors/resolve/main/control_v11f1p_sd15_depth_fp16.safetensors) |
| `ip-adapter-plus_sd15.safetensors` | `models/ipadapter/` | [Download](https://huggingface.co/h94/IP-Adapter/resolve/main/models/ip-adapter-plus_sd15.safetensors) |
| `CLIP-ViT-H-14-laion2B-s32B-b79K.safetensors` | `models/clip_vision/` | **Right-click → Save link as** — [Download](https://huggingface.co/h94/IP-Adapter/resolve/main/models/image_encoder/model.safetensors) — then rename to `CLIP-ViT-H-14-laion2B-s32B-b79K.safetensors` |
| `vae-ft-mse-840000-ema-pruned.safetensors` | `models/vae/` | [Download](https://huggingface.co/stabilityai/sd-vae-ft-mse-original/resolve/main/vae-ft-mse-840000-ema-pruned.safetensors) — you should already have this from Week 2; only redownload if yours is still the `.ckpt` version |
| `Latent-Bodies_Snapshot-3_ControlNet-IPAdapter-WORKING.json` | `user/_manager/snapshots/` | [Download](https://github.com/akajesstucker/latent-bodies/blob/main/Latent-Bodies_Snapshot-3_ControlNet-IPAdapter-WORKING.json) |

Workflow files (one pair per platform — Comfy Cloud and Local need different versions) are linked in Part 4.

Note: The `ipadapter` folder may not exist yet — create it inside `models/` if it is missing.

The ControlNet files above are the **fp16 safetensors** versions, not the original `.pth` files — same models, half the size, and the only format Comfy Cloud's importer accepts. Using them on both setups means one less thing to keep track of.

The rest of this guide explains each step in detail. Use it during class if you get stuck, or on your own afterward to go deeper.

---

## Overview

This week introduces two techniques for guiding image and video generation with structural and visual references:

- **ControlNet** uses an image you provide (a photo of yourself, a 3D model, a sketch, a video frame) to extract structural information — your body's pose, or the depth layout of a scene — and uses that structure to guide generation.
- **IPAdapter** uses a reference image to transfer visual qualities — color palette, texture, style, mood — into the generation, without describing them in text.

Both techniques require additional model files beyond the base checkpoint. This guide walks through getting all required models onto Comfy Cloud and local Windows setups.

---

## VAE (carried over from Week 2)

This week's workflows use the same VAE as Week 2 — `vae-ft-mse-840000-ema-pruned.safetensors`. 

**If you set up before this correction:** Week 2's guide originally linked to the `.ckpt` version of this file. If your `models/vae/` folder has `vae-ft-mse-840000-ema-pruned.ckpt` instead of the `.safetensors` version, download the safetensors version below and update the `Load VAE` node in this week's workflow to point to it.

**Comfy Cloud:** already pre-loaded in the Model Library — no action needed.

**Local:** [vae-ft-mse-840000-ema-pruned.safetensors](https://huggingface.co/stabilityai/sd-vae-ft-mse-original/resolve/main/vae-ft-mse-840000-ema-pruned.safetensors) → `models/vae/`

---

## Part 1: ControlNet Models

ControlNet models go in the `models/controlnet/` folder inside your ComfyUI directory.

### Models Needed for Class

These two are required. Get them at the beginning of class (directions below).

| File                                          | Size    | What it does                             |
| ---------------------------------------------- | ------- | ---------------------------------------- |
| `control_v11p_sd15_openpose_fp16.safetensors` | ~700 MB | Detects body keypoints and skeleton pose |
| `control_v11f1p_sd15_depth_fp16.safetensors`  | ~700 MB | Estimates distance/depth from image      |

These are the fp16 safetensors versions of the standard ControlNet-v1-1 models, not the original `.pth` files — same models, half the file size, and the only format Comfy Cloud's importer accepts. Using them on both setups keeps Local on the same files as Comfy Cloud.

#### Comfy Cloud

1. Click **Models** in the left sidebar to open the Model Library, then click **Import**.
2. Paste one of the links below, click **Continue**, and confirm the detected type is **ControlNet** (confirmed correct for the openpose file in testing; double-check it for the depth file too, since that one hasn't been tried live yet):

   - `https://huggingface.co/comfyanonymous/ControlNet-v1-1_fp16_safetensors/blob/main/control_v11p_sd15_openpose_fp16.safetensors`
   - `https://huggingface.co/comfyanonymous/ControlNet-v1-1_fp16_safetensors/blob/main/control_v11f1p_sd15_depth_fp16.safetensors`
3. Click **Import** and wait for both downloads to finish. They appear under **Imported** in the Model Library.

Import both before loading the workflow, so it can find them in the `ACN_ControlNetLoaderAdvanced` node's dropdown.

#### Local (Windows)

Download each file by clicking the links below, then move them into the `controlnet` subfolder:

- [control_v11p_sd15_openpose_fp16.safetensors](https://huggingface.co/comfyanonymous/ControlNet-v1-1_fp16_safetensors/resolve/main/control_v11p_sd15_openpose_fp16.safetensors)
- [control_v11f1p_sd15_depth_fp16.safetensors](https://huggingface.co/comfyanonymous/ControlNet-v1-1_fp16_safetensors/resolve/main/control_v11f1p_sd15_depth_fp16.safetensors)

Place them in: `ComfyUI_windows_portable\ComfyUI\models\controlnet\`

---

### Other ControlNet Models to Try on Your Own

These are optional — not needed for class, but worth experimenting with after the session. Each installs the same way: download and place in `models/controlnet/`.

| File | What it does |
|------|-------------|
| `control_v11p_sd15_canny.pth` | Detects sharp edges and outlines |
| `control_v11p_sd15_lineart.pth` | Extracts clean line drawing from a photo |
| `control_v11p_sd15_softedge.pth` | Softer, less rigid edge detection — more room for the model to interpret |
| `control_v11p_sd15_normalbae.pth` | Surface normals — encodes how surfaces face the light, useful for sculptural or 3D-feeling forms |
| `control_v11p_sd15_scribble.pth` | Takes rough hand-drawn sketches as input |

All are from the same repository. Replace the filename in the download link above with the file you want.

**Local:** [Direct download links for all ControlNet-v1-1 models](https://huggingface.co/lllyasviel/ControlNet-v1-1/tree/main)

**Comfy Cloud:** these optional models haven't been needed for class, so their fp16 safetensors versions haven't been confirmed yet. Check the [comfyanonymous/ControlNet-v1-1_fp16_safetensors](https://huggingface.co/comfyanonymous/ControlNet-v1-1_fp16_safetensors/tree/main) repo for a matching `_fp16.safetensors` file before trying to import one of these — `.pth` files will be rejected.

---

### How Preprocessors Work

ControlNet requires two things to function:

1. **The ControlNet model** — the safetensors file you downloaded above
2. **A preprocessed conditioning image** — a processed version of your input image that extracts just the structural information (a skeleton outline, a depth map, an edge trace, etc.)

You do not generate the conditioning image manually. In ComfyUI, **preprocessor nodes** do this automatically. They take your input image and output the structural map that feeds into ControlNet.

Preprocessor nodes come from the `comfyui_controlnet_aux` custom node package. When you load the workflows provided this week, they will already be connected, but here is what each one does, so you understand what you are seeing:

#### OpenPose: OpenposePreprocessor

The **OpenposePreprocessor** node analyzes your image and detects body keypoints: shoulders, elbows, wrists, hips, knees, face. It outputs a simplified skeleton drawing over a black background.

- Input: your photo or video frame
- Output: a skeleton image (colored dots and lines on black)
- This skeleton is what ControlNet uses to understand body position

This is a classic, whole-image OpenPose estimator — no separate person-detection step before it looks for keypoints, and no model to download or manage; it runs on the ComfyUI install's existing torch setup. The workflows used to use a newer node, `DWPreprocessor`, which was swapped out after it produced a fully black output on a borderline reference image (a CUDA/onnxruntime issue, not a class-content one). If you ever rebuild a workflow from scratch, use `OpenposePreprocessor`, not `DWPreprocessor`.

#### Depth: DepthAnythingPreprocessor

The **DepthAnythingPreprocessor** node analyzes your image and generates a grayscale depth map: white where things are close to the camera, black where things are far.

- Input: your photo or video frame
- Output: a grayscale depth map
- ControlNet uses this to understand the spatial layout of the scene

When you first run it, it will automatically download the model file it needs (`depth_anything_vitl14.pth`). This is normal — it happens once and you do not need to manage that file manually, on any platform.

**Comfy Cloud:** this model only exists in `.pth` format, so it is not imported through the Model Library like the other models this week — there is nothing to import ahead of time. The node handles it the same way Local does, fetching it itself on first use. Confirmed working live: dropped the node onto a test canvas, ran it against a reference photo, and it produced a correct depth map in under 3 seconds with no setup step at all.

#### Connecting it in ComfyUI

The flow in both workflows looks like this:

```
[Load Image] ──► [Preprocessor Node] ──────────────────► [ControlNetApplyAdvanced]
                                                                    ▲
[ACN_ControlNetLoaderAdvanced] ───────────────────────────────────► │
                                                                    │
[Text Conditioning (positive)] ──────────────────────────────────► │
                                                                    ▼
                                                              [KSampler]
```

The preprocessor's output image goes into the `image` input of `ControlNetApplyAdvanced`. The loaded ControlNet model goes into the `control_net` input. The result replaces the positive conditioning that would otherwise go directly into the KSampler.

To swap ControlNet models in the workflow, click the model file dropdown in the `ACN_ControlNetLoaderAdvanced` node and select a different file. To swap preprocessors, replace the preprocessor node (right-click → change to type) or use the `AIO Aux Preprocessor` node, which lets you select from all available preprocessors in one dropdown.

**Strength** is controlled by a value in the `ControlNetApplyAdvanced` node (between 0 and 1). Lower values give the generation more freedom to deviate from the structure; higher values hold it more tightly. Start around 0.7–0.9 and adjust.

---

## Part 2: IPAdapter Models

IPAdapter requires two separate model files: the adapter itself, and a CLIP Vision encoder that it uses to interpret your reference image. In the workflow, these are loaded by the **`IPAdapterUnifiedLoader`** node — it automatically selects the right adapter based on the mode you set (`PLUS (high strength)` in this case, which uses `ip-adapter-plus_sd15.safetensors`). You do not need to configure the loader manually; just make sure both files are in the correct folders before starting.

### Folder locations

| File type | Folder |
|-----------|--------|
| IPAdapter model | `models/ipadapter/` |
| CLIP Vision encoder | `models/clip_vision/` |

The `ipadapter` folder may not exist yet. If it does not, you will need to create it inside `models/` before downloading.

### Models Needed for Class

| File | Folder | Size | What it does |
|------|--------|------|-------------|
| `ip-adapter-plus_sd15.safetensors` | `models/ipadapter/` | ~800 MB | Transfers visual style, texture, and composition from a reference image |
| `CLIP-ViT-H-14-laion2B-s32B-b79K.safetensors` | `models/clip_vision/` | ~2.4 GB | Vision encoder IPAdapter uses to "read" your reference image |

Neither of these is in Comfy Cloud's pre-loaded library — both need to be imported through the Model Library.

#### Comfy Cloud

1. Click **Models** in the left sidebar to open the Model Library, then click **Import**.
2. Paste this link, click **Continue**, and let it auto-detect the type (confirmed correct as "CLIP Vision"):

   `https://huggingface.co/h94/IP-Adapter/blob/main/models/image_encoder/model.safetensors`
3. Paste this link, click **Continue** — this one does **not** auto-detect correctly, so under "What type of model is this?" manually choose **IPAdapter** from the dropdown (it's a long list; type to filter):

   `https://huggingface.co/h94/IP-Adapter/blob/main/models/ip-adapter-plus_sd15.safetensors`
4. Click **Import** on each and wait for the downloads to finish. They appear under **Imported** in the Model Library.

Import both before loading the workflow, so the `IPAdapterUnifiedLoader` node can find them.

#### Local (Windows)

Download each file, then place in the folders shown in the table above:

- [ip-adapter-plus_sd15.safetensors](https://huggingface.co/h94/IP-Adapter/resolve/main/models/ip-adapter-plus_sd15.safetensors) → `models/ipadapter/`
- CLIP Vision encoder → `models/clip_vision/`: **right-click [this link](https://huggingface.co/h94/IP-Adapter/resolve/main/models/image_encoder/model.safetensors) and choose "Save link as"**, then manually type the filename as `CLIP-ViT-H-14-laion2B-s32B-b79K.safetensors` before saving. If you just click the link, it will download as `model.safetensors` which will not be recognised by the workflow.

Note: If `models/ipadapter/` does not exist inside your `ComfyUI_windows_portable\ComfyUI\` folder, create it.

---

### Other IPAdapter Models to Try on Your Own

| File | What it does |
|------|-------------|
| `ip-adapter_sd15.safetensors` | The base (lighter) adapter — less fidelity, faster |
| `ip-adapter-plus-face_sd15.safetensors` | Tuned specifically for faces — stronger identity transfer from portrait reference images |

**Local:** download and place in `ComfyUI_windows_portable\ComfyUI\models\ipadapter\`

The CLIP Vision encoder you already downloaded is shared — it works with all IPAdapter variants. You do not need a separate one for each model.

---

## Part 3: Restore the Class Snapshot

**Comfy Cloud: skip this whole part.** There is no snapshot concept on Comfy Cloud — node versions are managed platform-wide, not per-workflow. Every node this week's workflows use (`ACN_ControlNetLoaderAdvanced`, `OpenposePreprocessor`, the full IPAdapter suite) is already installed and confirmed working. Go straight to Part 4.

Local: before loading the workflows, restore the class snapshot. This installs all the custom node packages the workflows depend on, pinned to versions that are known to work together.

**1. Download the snapshot file**

- [Latent-Bodies_Snapshot-3_ControlNet-IPAdapter-WORKING.json](https://github.com/akajesstucker/latent-bodies/blob/main/Latent-Bodies_Snapshot-3_ControlNet-IPAdapter-WORKING.json)

**2. Place the snapshot file in the correct folder**

Open Windows Explorer and navigate to `ComfyUI_windows_portable\ComfyUI\user\_manager\snapshots\`. Drag and drop the file there.

**3. Restore the snapshot**

1. Click **Manager** in the ComfyUI top bar
2. Click **"Snapshot Manager"**
3. Find `Latent-Bodies_Snapshot-3_ControlNet-IPAdapter-WORKING` in the list and click **"Restore"**
4. ComfyUI will install all required custom nodes automatically
5. Click **Restart** when prompted, then refresh the browser tab

---

## Part 4: Download Workflows

There are two versions of each workflow, one for Comfy Cloud and one for Local. They are identical except for how the models are referenced, so make sure you download the right one.

To download a file, click its link, then click the download icon at the top right of the GitHub page (or press `ctrl + shift + s`). Save them somewhere that makes sense (e.g. a Latent Bodies class folder on your desktop).

### Comfy Cloud

- [Latent-Bodies_Workflow-3_Image-Controlnet-IPAdapter_ComfyCloud.json](https://github.com/akajesstucker/latent-bodies/blob/main/Latent-Bodies_Workflow-3_Image-Controlnet-IPAdapter_ComfyCloud.json)
- [Latent-Bodies_Workflow-3_Video-Controlnet-IPAdapter_ComfyCloud.json](https://github.com/akajesstucker/latent-bodies/blob/main/Latent-Bodies_Workflow-3_Video-Controlnet-IPAdapter_ComfyCloud.json)

To load a workflow, drag and drop the `.json` file onto the ComfyUI canvas in your browser.

### Local

- [Latent-Bodies_Workflow-3_Image-Controlnet-IPAdapter_Local.json](https://github.com/akajesstucker/latent-bodies/blob/main/Latent-Bodies_Workflow-3_Image-Controlnet-IPAdapter_Local.json)
- [Latent-Bodies_Workflow-3_Video-Controlnet-IPAdapter_Local.json](https://github.com/akajesstucker/latent-bodies/blob/main/Latent-Bodies_Workflow-3_Video-Controlnet-IPAdapter_Local.json)

To load a workflow, drag and drop the `.json` file into the ComfyUI browser tab. You can load either workflow at any time this way.

**Note on loading input files:** When you first open either workflow, the image and video input nodes will show a missing file error — this is normal. The workflows are set up with placeholder inputs that you replace with your own. In the image workflow, click each `Load Image` node and select your own photo. In the video workflow, click the `Load Video` node and upload your own clip. You will be doing this together in class.

Both workflows have Note nodes built directly into the canvas explaining the LoRA setup, the CLIP Set Last Layer / LCM sampler settings, and (video only) how to control output resolution — look for them near the relevant nodes once the workflow is loaded.

---

## Part 5: Prepare Your Input Images

For this session you will use your own images or video frames as the input driving ControlNet. Have at least one of the following ready before class:

- A photo of yourself or another person, clearly showing the body or pose you want to work from
- A short video clip (if you want to try video generation with ControlNet)

To load an input image in ComfyUI, use the `Load Image` node — click the image area inside the node to open a file browser, or drag and drop an image file directly onto the node.

**Comfy Cloud users:** no separate upload step — click the `Load Image` node's image area and upload directly from there, same as Local.

---

## Try a Different Base Model (Optional)

Curious what these workflows look like with a different checkpoint instead of the course default (`v1-5-pruned-emaonly.safetensors`)? You can swap it in the `Load Checkpoint` node.

**It must be an SD1.5 checkpoint.** Every other model in these workflows — the ControlNet files, IPAdapter, the AnimateDiff motion module — is built for SD1.5's architecture. An SDXL, Flux, or other-architecture checkpoint will not work with any of them; you'd need an entirely different set of ControlNet/IPAdapter/motion models to match, which is outside this course's scope.

**Comfy Cloud:** check the Model Library search first — many popular checkpoints are already pre-loaded, no import needed. If what you want isn't there, use Import and paste a Civitai or Hugging Face link, same as the ControlNet/IPAdapter models in Part 1 and 2 — only `.safetensors` files are accepted.

**Local:** browse [Civitai](https://civitai.com) (filter by Base Model: SD 1.5) or [Hugging Face](https://huggingface.co) for a checkpoint, download it, and place it in `models/checkpoints/`.

---

## Installing Missing Custom Nodes

**Comfy Cloud:** custom node installation is not self-service here yet — clicking Extensions just opens a "coming soon" waitlist dialog. This isn't a problem for this week's workflows since every node they use is already installed, but if you ever see a red node on Comfy Cloud, it won't be fixable by the steps below; ask in Discord instead.

Local: when you first load the workflows, some nodes may appear highlighted in red. This means required custom node packages are not yet installed.

To fix this:

1. Click **Manager** in the ComfyUI top bar
2. Click **"Install Missing Custom Nodes"**
3. ComfyUI Manager will scan the workflow and list what is missing — click **Install** on each one
4. Once all installs complete, click **Restart**
5. After ComfyUI reloads, the red nodes should appear normally

If any nodes are still red after restarting, repeat the process. Some packages need a second pass.

---

## Troubleshooting

**ControlNet has no effect on the output**
Check that the `ControlNetApply` node is actually connected into the KSampler's positive conditioning input. Also check that the strength value is not set to 0.

**Preprocessor node is red or missing**
The `comfyui_controlnet_aux` package is not installed. Use Manager → Install Missing Custom Nodes.

**IPAdapter node is red or missing**
The `ComfyUI_IPAdapter_plus` package is not installed. Use Manager → Install Missing Custom Nodes.

**IPAdapter node shows an error about CLIP Vision**
Make sure `CLIP-ViT-H-14-laion2B-s32B-b79K.safetensors` is in `models/clip_vision/` and that you refreshed ComfyUI after placing it. Click the CLIP Vision loader node and reselect the file from its dropdown. **Comfy Cloud:** click the node and reselect from the imported models dropdown instead.

**Comfy Cloud: model import rejected with "Only SafeTensor format is allowed"**
You pasted a `.pth` or `.ckpt` link. Use the `_fp16.safetensors` versions linked in Part 1 and 2 instead — Comfy Cloud only accepts safetensors.

**Comfy Cloud: a model imported but the wrong type got auto-detected**
Known for the IPAdapter model — Comfy Cloud guesses something else by default. Delete the import and redo it, manually choosing **IPAdapter** from the type dropdown this time (see Part 2).

**Models are not showing in dropdowns after downloading or importing**
Click the browser's refresh button on the ComfyUI tab. If still missing, open Manager and click Restart (Local) or just reload the page (Comfy Cloud).
