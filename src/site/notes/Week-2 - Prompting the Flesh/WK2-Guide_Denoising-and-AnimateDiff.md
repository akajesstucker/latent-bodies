---
dg-publish: true
permalink: /notes/week-2/denoising-and-animatediff/
---
# WK2 Guide: Denoising and AnimateDiff

---

## Download Models

Four models are needed for this week's workflows. Follow the section for your setup: Comfy Cloud or Local (Windows).

### Comfy Cloud

Three of this week's models are already in Comfy Cloud's library, so you do not need to download them:

- **VAE:** `stabilityai/sd-vae-ft-mse-original - vae-ft-mse-840000-ema-pruned`
- **AnimateLCM motion module:** `AnimateLCM_sd15_t2v.ckpt` (it appears in the AnimateDiff Loader's model list)
- **AnimateLCM LoRA:** `wangfuyun/AnimateLCM - AnimateLCM_sd15_t2v_lora`

You only need to import one file, the AnimateDiff v3 adapter. Importing requires the **Creator plan**, which you should already have from the setup guide.

1. Click **Models** in the left sidebar to open the Model Library, then click **Import**.
2. Paste this link into the box and click **Continue**:

   `https://huggingface.co/guoyww/animatediff/blob/9cfaa8a83b89eec48b4b0ee8c970e8b9f69f2089/v3_sd15_adapter.safetensors`
3. Under "What type of model is this?", choose **LoRA**. Comfy Cloud will guess "AnimateDiff Model", which is wrong for this file, so change it.
4. Click **Import** and wait for the download to finish. The file appears under **Imported** in the Model Library.

The Image Denoising workflow only needs the VAE. The Video Denoising workflow needs the VAE, the motion module, and both LoRAs.

Import the adapter *before* loading the Video Denoising workflow, so the workflow can find it.

### Local (Windows)

The folder for each file is shown below.

| File | Folder |
|------|--------|
| `vae-ft-mse-840000-ema-pruned.ckpt` | `models/vae/` |
| `AnimateLCM_sd15_t2v.ckpt` | `models/animatediff_models/` |
| `v3_sd15_adapter.ckpt` | `models/loras/SD15/` |
| `AnimateLCM_sd15_t2v_lora.safetensors` | `models/loras/SD15/` |

**Create an `SD15` folder inside `models/loras/` first** (right-click, New, Folder) and put both LoRA files in it. LoRAs only work with the type of model they were trained for, and these two are for Stable Diffusion 1.5. Keeping SD 1.5 LoRAs in an `SD15` folder makes it easy to tell which LoRAs are compatible with which models, as your collection grows. If any other destination folder is missing, create it too.

Download each file by clicking the links below, then move each file to the folder shown in the table above, inside your `ComfyUI_windows_portable\ComfyUI\` directory.

- [vae-ft-mse-840000-ema-pruned.ckpt](https://huggingface.co/stabilityai/sd-vae-ft-mse-original/resolve/main/vae-ft-mse-840000-ema-pruned.ckpt)
- [AnimateLCM_sd15_t2v.ckpt](https://huggingface.co/wangfuyun/AnimateLCM/resolve/main/AnimateLCM_sd15_t2v.ckpt)
- [v3_sd15_adapter.ckpt](https://huggingface.co/guoyww/animatediff/resolve/main/v3_sd15_adapter.ckpt)
- [AnimateLCM_sd15_t2v_lora.safetensors](https://huggingface.co/wangfuyun/AnimateLCM/resolve/main/AnimateLCM_sd15_t2v_lora.safetensors)

After placing all files, click the browser's **refresh button** on the open ComfyUI window. If models are still not showing, open ComfyUI Manager and click Restart.

---

## Download Workflows

There are two versions of each workflow, one for Comfy Cloud and one for Local (Windows). They are identical except for the model names, which differ between the two setups, so make sure you download the right one.

To download a file, click its link, then click the download icon at the top right of the GitHub page (or press `ctrl + shift + s`). Save them somewhere that makes sense (e.g. a Latent Bodies class folder on your desktop).

### Comfy Cloud

- [Latent-Bodies_Workflow-2_Image-Denoising_ComfyCloud.json](https://github.com/akajesstucker/latent-bodies/blob/main/Latent-Bodies_Workflow-2_Image-Denoising_ComfyCloud.json)
- [Latent-Bodies_Workflow-2_Video-Denoising_ComfyCloud.json](https://github.com/akajesstucker/latent-bodies/blob/main/Latent-Bodies_Workflow-2_Video-Denoising_ComfyCloud.json)

To load a workflow, drag and drop the `.json` file onto the ComfyUI canvas in your browser. All the custom nodes these workflows use are already installed on Comfy Cloud, and the model names in these files already match Comfy Cloud's library, so nothing should be flagged as missing.

### Local (Windows)

- [Latent-Bodies_Workflow-2_Image-Denoising_Local.json](https://github.com/akajesstucker/latent-bodies/blob/main/Latent-Bodies_Workflow-2_Image-Denoising_Local.json)
- [Latent-Bodies_Workflow-2_Video-Denoising_Local.json](https://github.com/akajesstucker/latent-bodies/blob/main/Latent-Bodies_Workflow-2_Video-Denoising_Local.json)

To load a workflow, drag and drop the `.json` file into the ComfyUI browser tab.

#### Installing Missing Custom Nodes

When you first load the Image Denoising workflow, you will likely see some nodes highlighted in red. This means those nodes require custom node packages that are not yet installed.

To fix this:

1. Click **Manager** in the ComfyUI top bar
2. Click **"Install Missing Custom Nodes"**
3. ComfyUI Manager will scan the workflow and show a list of missing packages. Click **Install** on each one
4. Once all installs complete, click **Restart** to restart ComfyUI and refresh the window
5. After ComfyUI reloads, the red nodes should now appear normally

If any nodes are still red after restarting, repeat the process. Occasionally a package requires a second pass.

---
## Troubleshooting
If nothing seems to generate, especially in the video workflow:
- **Comfy Cloud:** make sure you downloaded the `_ComfyCloud` version of the workflow and that your v3 adapter import finished downloading. If the adapter is flagged as missing in the Video workflow, check that you chose **LoRA** as the model type when importing it, then click the dropdown on that LoRA node and select `guoyww/animatediff - v3_sd15_adapter`. If the Issues panel still lists a model you have already selected, try Run anyway, as the warning can lag behind.
- **Local:** in the manager, go to install custom nodes and search for ComfyUI-Impact-Pack. Update it.
- In the Lora nodes model dropdown menu, reselect each Lora.
- Restart ComfyUI and try again.

---

## Before Week 3 Checklist

- [ ] Experiment with the Week 2 Image and Video workflows, and share a few of your outputs to the Discord channel
- [ ] Bring to class at least 3 images + 3 short videos (max 5 seconds)
- [ ] At least 1 of each should be of a humanoid body, but you can also bring in other bodies and forms that you would like to use to shape the composition of your generations
- [ ] These can be found or self-produced

If you get stuck at any step, post in the class Discord for help.
