---
dg-publish: true
permalink: /notes/week-3/controlnet-and-ipadapter/
---
# WK3 Guide: ControlNet and IPAdapter

---

## TLDR: Everything You Need to Download

Download these files at the start of class. Place each file in the subfolder shown, inside your `ComfyUI/` directory (local) or `/workspace/runpod-slim/ComfyUI/` (RunPod).

| File | Folder | Link |
|------|--------|------|
| `control_v11p_sd15_openpose.pth` | `models/controlnet/` | [Download](https://huggingface.co/lllyasviel/ControlNet-v1-1/resolve/main/control_v11p_sd15_openpose.pth) |
| `control_v11f1p_sd15_depth.pth` | `models/controlnet/` | [Download](https://huggingface.co/lllyasviel/ControlNet-v1-1/resolve/main/control_v11f1p_sd15_depth.pth) |
| `ip-adapter-plus_sd15.safetensors` | `models/ipadapter/` | [Download](https://huggingface.co/h94/IP-Adapter/resolve/main/models/ip-adapter-plus_sd15.safetensors) |
| `CLIP-ViT-H-14-laion2B-s32B-b79K.safetensors` | `models/clip_vision/` | [Download](https://huggingface.co/h94/IP-Adapter/resolve/main/models/image_encoder/model.safetensors) |
| `Latent-Bodies_Snapshot-3_ControlNet-IPAdapter.json` | `user/_manager/snapshots/` | [Download](https://github.com/akajesstucker/latent-bodies/blob/main/Latent-Bodies_Snapshot-3_ControlNet-IPAdapter.json) |
| `Latent-Bodies_Workflow-3_Image-Controlnet-IPAdapter.json` | anywhere | [Download](https://github.com/akajesstucker/latent-bodies/blob/main/Latent-Bodies_Workflow-3_Image-Controlnet-IPAdapter.json) |
| `Latent-Bodies_Workflow-3_Video-Controlnet-IPAdapter.json` | anywhere | [Download](https://github.com/akajesstucker/latent-bodies/blob/main/Latent-Bodies_Workflow-3_Video-Controlnet-IPAdapter.json) |

Note: The `ipadapter` folder may not exist yet — create it inside `models/` if it is missing.

The rest of this guide explains each step in detail. Use it during class if you get stuck, or on your own afterward to go deeper.

---

## Overview

This week introduces two techniques for guiding image and video generation with structural and visual references:

- **ControlNet** uses an image you provide (a photo of yourself, a 3D model, a sketch, a video frame) to extract structural information — your body's pose, or the depth layout of a scene — and uses that structure to guide generation.
- **IPAdapter** uses a reference image to transfer visual qualities — color palette, texture, style, mood — into the generation, without describing them in text.

Both techniques require additional model files beyond the base checkpoint. This guide walks through downloading and placing all required models for both RunPod and local Windows setups.

---

## Part 1: ControlNet Models

ControlNet models go in the `models/controlnet/` folder inside your ComfyUI directory.

### Models Needed for Class

These two are required. Download them at the beginning of class (directions below).

| File                             | Size    | What it does                             |
| -------------------------------- | ------- | ---------------------------------------- |
| `control_v11p_sd15_openpose.pth` | ~1.4 GB | Detects body keypoints and skeleton pose |
| `control_v11f1p_sd15_depth.pth`  | ~1.4 GB | Estimates distance/depth from image      |

#### Cloud (RunPod)

Open a JupyterLab terminal (Connect tab → JupyterLab → File → New → Terminal) and paste both commands, then press enter:

```bash
wget "https://huggingface.co/lllyasviel/ControlNet-v1-1/resolve/main/control_v11p_sd15_openpose.pth" -O /workspace/runpod-slim/ComfyUI/models/controlnet/control_v11p_sd15_openpose.pth

wget "https://huggingface.co/lllyasviel/ControlNet-v1-1/resolve/main/control_v11f1p_sd15_depth.pth" -O /workspace/runpod-slim/ComfyUI/models/controlnet/control_v11f1p_sd15_depth.pth
```

#### Local (Windows)

Download each file by clicking the links below, then move them into the `controlnet` subfolder:

- [control_v11p_sd15_openpose.pth](https://huggingface.co/lllyasviel/ControlNet-v1-1/resolve/main/control_v11p_sd15_openpose.pth)
- [control_v11f1p_sd15_depth.pth](https://huggingface.co/lllyasviel/ControlNet-v1-1/resolve/main/control_v11f1p_sd15_depth.pth)

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

All are from the same repository. Replace the filename in the `wget` URL or download link above with the file you want.

**Cloud (RunPod) — example for canny:**
```bash
wget "https://huggingface.co/lllyasviel/ControlNet-v1-1/resolve/main/control_v11p_sd15_canny.pth" -O /workspace/runpod-slim/ComfyUI/models/controlnet/control_v11p_sd15_canny.pth
```

**Local:** [Direct download links for all ControlNet-v1-1 models](https://huggingface.co/lllyasviel/ControlNet-v1-1/tree/main)

---

### How Preprocessors Work

ControlNet requires two things to function:

1. **The ControlNet model** — the `.pth` file you downloaded above
2. **A preprocessed conditioning image** — a processed version of your input image that extracts just the structural information (a skeleton outline, a depth map, an edge trace, etc.)

You do not generate the conditioning image manually. In ComfyUI, **preprocessor nodes** do this automatically. They take your input image and output the structural map that feeds into ControlNet.

Preprocessor nodes come from the `comfyui_controlnet_aux` custom node package. When you load the workflows provided this week, they will already be connected, but here is what each one does, so you understand what you are seeing:

#### OpenPose: DWPreprocessor

The **DWPreprocessor** node analyzes your image and detects body keypoints: shoulders, elbows, wrists, hips, knees, face. It outputs a simplified skeleton drawing over a black background.

- Input: your photo or video frame
- Output: a skeleton image (colored dots and lines on black)
- This skeleton is what ControlNet uses to understand body position

When you first run DWPreprocessor, it will automatically download a small set of ONNX model files it needs. This is normal — it happens once and you do not need to manage those files.

#### Depth: DepthAnythingPreprocessor

The **DepthAnythingPreprocessor** node analyzes your image and generates a grayscale depth map: white where things are close to the camera, black where things are far.

- Input: your photo or video frame
- Output: a grayscale depth map
- ControlNet uses this to understand the spatial layout of the scene

When you first run DepthAnythingPreprocessor, it will automatically download the model file it needs (`depth_anything_vitl14.pth`). This is normal — it happens once and you do not need to manage that file manually.

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

To swap ControlNet models in the workflow, click the model file dropdown in the `ACN_ControlNetLoaderAdvanced` node and select a different `.pth` file. To swap preprocessors, replace the preprocessor node (right-click → change to type) or use the `AIO Aux Preprocessor` node, which lets you select from all available preprocessors in one dropdown.

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

#### Cloud (RunPod)

In your JupyterLab terminal, first create the ipadapter folder if it does not exist, then download both files:

```bash
mkdir -p /workspace/runpod-slim/ComfyUI/models/ipadapter

wget "https://huggingface.co/h94/IP-Adapter/resolve/main/models/ip-adapter-plus_sd15.safetensors" -O /workspace/runpod-slim/ComfyUI/models/ipadapter/ip-adapter-plus_sd15.safetensors

wget "https://huggingface.co/h94/IP-Adapter/resolve/main/models/image_encoder/model.safetensors" -O /workspace/runpod-slim/ComfyUI/models/clip_vision/CLIP-ViT-H-14-laion2B-s32B-b79K.safetensors
```

#### Local (Windows)

Download each file, then place in the folders shown in the table above:

- [ip-adapter-plus_sd15.safetensors](https://huggingface.co/h94/IP-Adapter/resolve/main/models/ip-adapter-plus_sd15.safetensors) → `models/ipadapter/`
- [CLIP-ViT-H-14-laion2B-s32B-b79K.safetensors](https://huggingface.co/h94/IP-Adapter/resolve/main/models/image_encoder/model.safetensors) → `models/clip_vision/`

Note: If `models/ipadapter/` does not exist inside your `ComfyUI_windows_portable\ComfyUI\` folder, create it.

---

### Other IPAdapter Models to Try on Your Own

| File | What it does |
|------|-------------|
| `ip-adapter_sd15.safetensors` | The base (lighter) adapter — less fidelity, faster |
| `ip-adapter-plus-face_sd15.safetensors` | Tuned specifically for faces — stronger identity transfer from portrait reference images |

**Cloud (RunPod) — both go in `models/ipadapter/`:**
```bash
wget "https://huggingface.co/h94/IP-Adapter/resolve/main/models/ip-adapter_sd15.safetensors" -O /workspace/runpod-slim/ComfyUI/models/ipadapter/ip-adapter_sd15.safetensors

wget "https://huggingface.co/h94/IP-Adapter/resolve/main/models/ip-adapter-plus-face_sd15.safetensors" -O /workspace/runpod-slim/ComfyUI/models/ipadapter/ip-adapter-plus-face_sd15.safetensors
```

**Local:** download and place in `ComfyUI_windows_portable\ComfyUI\models\ipadapter\`

The CLIP Vision encoder you already downloaded is shared — it works with all IPAdapter variants. You do not need a separate one for each model.

---

## Part 3: Restore the Class Snapshot

Before loading the workflows, restore the class snapshot. This installs all the custom node packages the workflows depend on, pinned to versions that are known to work together.

**1. Download the snapshot file**

- [Latent-Bodies_Snapshot-3_ControlNet-IPAdapter.json](https://github.com/akajesstucker/latent-bodies/blob/main/Latent-Bodies_Snapshot-3_ControlNet-IPAdapter.json)

**2. Place the snapshot file in the correct folder**

- **Cloud (RunPod):** Use the JupyterLab file browser in the left sidebar and navigate to `/runpod-slim/ComfyUI/user/_manager/snapshots/`. Drag and drop the file there.
- **Local (Windows):** Open Windows Explorer and navigate to `ComfyUI_windows_portable\ComfyUI\user\_manager\snapshots\`. Drag and drop the file there.

**3. Restore the snapshot**

1. Click **Manager** in the ComfyUI top bar
2. Click **"Snapshot Manager"**
3. Find `Latent-Bodies_Snapshot-3_ControlNet-IPAdapter` in the list and click **"Restore"**
4. ComfyUI will install all required custom nodes automatically
5. Click **Restart** when prompted, then refresh the browser tab

---

## Part 4: Download Workflows

Download both workflow files below, then drag and drop each `.json` file directly into the ComfyUI browser tab to load it.

- [Latent-Bodies_Workflow-3_Image-Controlnet-IPAdapter.json](https://github.com/akajesstucker/latent-bodies/blob/main/Latent-Bodies_Workflow-3_Image-Controlnet-IPAdapter.json)
- [Latent-Bodies_Workflow-3_Video-Controlnet-IPAdapter.json](https://github.com/akajesstucker/latent-bodies/blob/main/Latent-Bodies_Workflow-3_Video-Controlnet-IPAdapter.json)

Save them somewhere accessible (a class folder on your desktop works well). You can load either workflow at any time by dragging the file into ComfyUI.

**Note on loading input files:** When you first open either workflow, the image and video input nodes will show a missing file error — this is normal. The workflows are set up with placeholder inputs that you replace with your own. In the image workflow, click each `Load Image` node and select your own photo. In the video workflow, click the `Load Video` node and upload your own clip. You will be doing this together in class.

**Note on LoRA nodes in the image workflow:** You will see two LoRA nodes in the image workflow — they are bypassed (greyed out) and have no effect on generation. These LoRAs are used in the video workflow for AnimateDiff motion generation and are not needed for still images. They are left in the image workflow as a reference point for Week 4, when we will work with LoRAs directly.

---

## Part 5: Prepare Your Input Images

For this session you will use your own images or video frames as the input driving ControlNet. Have at least one of the following ready before class:

- A photo of yourself or another person, clearly showing the body or pose you want to work from
- A short video clip (if you want to try video generation with ControlNet)

To load an input image in ComfyUI, use the `Load Image` node — click the image area inside the node to open a file browser, or drag and drop an image file directly onto the node.

**RunPod users:** Use the JupyterLab file browser to upload images to your network volume before the session. Navigate to `/workspace/runpod-slim/ComfyUI/input/` and drag your files there. They will then be available from within ComfyUI's Load Image node.

---

## Installing Missing Custom Nodes

When you first load the workflows, some nodes may appear highlighted in red. This means required custom node packages are not yet installed.

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
Make sure `CLIP-ViT-H-14-laion2B-s32B-b79K.safetensors` is in `models/clip_vision/` and that you refreshed ComfyUI after placing it. Click the CLIP Vision loader node and reselect the file from its dropdown.

**DWPose downloads are slow on first run**
Normal. DWPose downloads its ONNX models the first time it runs. Wait for it to complete before expecting output.

**Models are not showing in dropdowns after downloading**
Click the browser's refresh button on the ComfyUI tab. If still missing, open Manager and click Restart.
