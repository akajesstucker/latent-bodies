---
dg-publish: true
permalink: /notes/week-2/denoising-and-animatediff/
---
# WK2 Guide: Denoising and AnimateDiff

---

## Download Models

Four models are needed for this week's workflows. Each goes into a specific subfolder of your ComfyUI directory.

| File | Folder |
|------|--------|
| `vae-ft-mse-840000-ema-pruned.ckpt` | `models/vae/` |
| `AnimateLCM_sd15_t2v.ckpt` | `models/animatediff_models/` |
| `v3_sd15_adapter.ckpt` | `models/loras/` |
| `AnimateLCM_sd15_t2v_lora.safetensors` | `models/loras/` |

### Cloud (RunPod)

Open a JupyterLab terminal (Connect tab → JupyterLab → File → New → Terminal) and paste the block below all at once, or one wget command at a time, then press enter:

```bash
wget "https://huggingface.co/stabilityai/sd-vae-ft-mse-original/resolve/main/vae-ft-mse-840000-ema-pruned.ckpt" -O /workspace/runpod-slim/ComfyUI/models/vae/vae-ft-mse-840000-ema-pruned.ckpt

wget "https://huggingface.co/wangfuyun/AnimateLCM/resolve/main/AnimateLCM_sd15_t2v.ckpt" -O /workspace/runpod-slim/ComfyUI/models/animatediff_models/AnimateLCM_sd15_t2v.ckpt

wget "https://huggingface.co/guoyww/animatediff/resolve/main/v3_sd15_adapter.ckpt" -O /workspace/runpod-slim/ComfyUI/models/loras/v3_sd15_adapter.ckpt

wget "https://huggingface.co/wangfuyun/AnimateLCM/resolve/main/AnimateLCM_sd15_t2v_lora.safetensors" -O /workspace/runpod-slim/ComfyUI/models/loras/AnimateLCM_sd15_t2v_lora.safetensors
```

After all downloads finish, click the browser's **refresh button** on the open ComfyUI window.

### Local (Windows)

Download each file by clicking the links below, then move each file to the folder shown in the table above, inside your `ComfyUI_windows_portable\ComfyUI\` directory. All destination folders already exist — just place each file directly into the folder shown in the table above.

- [vae-ft-mse-840000-ema-pruned.ckpt](https://huggingface.co/stabilityai/sd-vae-ft-mse-original/resolve/main/vae-ft-mse-840000-ema-pruned.ckpt)
- [AnimateLCM_sd15_t2v.ckpt](https://huggingface.co/wangfuyun/AnimateLCM/resolve/main/AnimateLCM_sd15_t2v.ckpt)
- [v3_sd15_adapter.ckpt](https://huggingface.co/guoyww/animatediff/resolve/main/v3_sd15_adapter.ckpt)
- [AnimateLCM_sd15_t2v_lora.safetensors](https://huggingface.co/wangfuyun/AnimateLCM/resolve/main/AnimateLCM_sd15_t2v_lora.safetensors)

After placing all files, click the browser's **refresh button** on the open ComfyUI window. If models are still not showing, open ComfyUI Manager and click Restart.

---

## Download Workflows

Download both workflow files to your computer by clicking each link below, then clicking the download icon at the top right of the page (or press `ctrl + shift + s`):

- [Latent-Bodies_Workflow-2_Image-Denoising.json](https://github.com/akajesstucker/latent-bodies/blob/main/Latent-Bodies_Workflow-2_Image-Denoising.json)
- [Latent-Bodies_Workflow-2_Video-Denoising.json](https://github.com/akajesstucker/latent-bodies/blob/main/Latent-Bodies_Workflow-2_Video-Denoising.json)

Save them somewhere that makes sense (e.g. a Latent Bodies class folder on your desktop). To load a workflow, drag and drop the `.json` file into the ComfyUI browser tab. This works the same whether you are on RunPod or running locally.

### Installing Missing Custom Nodes

When you first load the Image Denoising workflow, you will likely see some nodes highlighted in red. This means those nodes require custom node packages that are not yet installed.

To fix this:

1. Click **Manager** in the ComfyUI top bar
2. Click **"Install Missing Custom Nodes"**
3. ComfyUI Manager will scan the workflow and show a list of missing packages — click **Install** on each one
4. Once all installs complete, click **Restart** to restart ComfyUI and refresh the window
5. After ComfyUI reloads, the red nodes should now appear normally

If any nodes are still red after restarting, repeat the process. Occasionally a package requires a second pass.

---
## Troubleshooting
If nothing seems to generate, especially in the video workflow:
- In the manager, go to install custom nodes and search for ComfyUI-Impact-Pack. Update it.
- In the Lora nodes model dropdown menu, reselect each Lora.
- Restart ComfyUI and try again.