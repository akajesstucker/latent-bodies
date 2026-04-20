# Kohya SS on RunPod (Archived)

This path is archived as of April 2026. The Kohya SS RunPod template has consistent reliability issues: pressing Start Training does nothing, and the Gradio interface fails to communicate with the backend through RunPod's URL proxy layer. Pod restarts and reconfiguration do not resolve the issue. Restore this to the main guide when a stable solution is confirmed.

---

## Path A: RunPod

This path uses a dedicated Kohya SS pod separate from your ComfyUI pod. You train on the Kohya pod, then switch back to your ComfyUI pod to test it. You do not need both pods running at the same time.

### Privacy

RunPod: your images are uploaded to a cloud GPU pod. If using a network volume, your dataset will persist on it; if using a volume disk only, all data is deleted permanently when you terminate the pod. Either way, RunPod does not retain your files after termination. The `.safetensors` LoRA file you download or save to your volume contains learned model weights, not your original images.

### A1. Launch the Kohya SS pod

1. Log in to runpod.io
2. In the left sidebar under **The Hub**, click **Pod templates**
3. Search for `Kohya` in the search bar
4. Select **Kohya_ss GUI** (`ashleykza/kohya:cu124-py311-25.2.1`) — Community template
5. Click **Configure Pod**
6. On the GPU selection page, choose a GPU with at least 24 GB VRAM. Preferred options in order of cost:

   | GPU | VRAM | Approx. cost |
   |---|---|---|
   | RTX 3090 | 24 GB | ~$0.46/hr |
   | RTX 4090 | 24 GB | ~$0.69/hr |
   | RTX PRO 4500 | 32 GB | ~$0.64/hr |
   | L40S | 48 GB | ~$0.86/hr |
   | RTX A6000 | 48 GB | ~$0.77/hr |

   Availability changes constantly -- work down the list until you find one available. The RTX 5090 also appears in the list but requires a different template; do not use it with this guide.

   **If you plan to use your existing network volume (recommended):** click the **Network volume** filter at the top of the GPU list and select your volume. This automatically restricts the list to GPUs in the same datacenter region as your volume -- only those will be compatible.

7. Click the GPU card to select it -- this takes you to the storage configuration page
8. Under **Storage configuration**, choose your storage option:
   - **If you have a network volume from your ComfyUI setup (recommended):** select it under Network volume. The volume mounts at `/workspace/` on the Kohya pod, the same as on your ComfyUI pod. You can point Kohya's output folder directly to `/workspace/runpod-slim/ComfyUI/models/loras/` and your LoRA will already be in the right place when you relaunch ComfyUI.
   - **If you don't have a network volume:** use Volume disk (100 GB default). You will download your LoRA manually after training. This is also an option if no suitable GPUs are available in your network volume's region.
9. Click **Deploy On-Demand** and wait for the pod to reach "Running" status

### A2. Open the Kohya SS interface

1. Click your pod's name in the Pods list -- a panel opens on the right, showing the **Connect** tab by default
2. Under **HTTP services**, click the link next to **Port 3000 (Kohya_ss GUI)**. Wait until it shows "Ready" before clicking -- it may say "Initializing" for a minute or two after the pod starts
3. The Kohya SS web interface will open in your browser

### A3. Upload your dataset

In the pod panel, under the Connect tab, click the link next to **Port 8888 (Jupyter Lab)**. Navigate to `/workspace/` in the file browser, then drag and drop your entire `myname-lora/` project folder (containing `dataset/` and `output/`) directly into it.

You should end up with:

```
/workspace/myname-lora/
  dataset/
    15_myname/
      myname_01.jpg
      myname_01.txt
      ...
  output/
```

### RunPod-specific Folders configuration (Step 3)

| Field | Value |
|---|---|
| Image folder | `/workspace/myname-lora/dataset/` (type the path directly -- the folder browser does not work on RunPod) |
| Output directory (network volume) | `/workspace/runpod-slim/ComfyUI/models/loras/` |
| Output directory (volume disk) | `/workspace/myname-lora/output/` |

### Getting your LoRA files from RunPod

**RunPod with network volume:** your checkpoints are already in `/workspace/runpod-slim/ComfyUI/models/loras/`. Terminate the Kohya pod, relaunch your ComfyUI pod with the same network volume, and they will appear immediately.

**RunPod with volume disk:** in JupyterLab, navigate to `/workspace/myname-lora/output/`, right-click each `.safetensors` file and choose **Download**. Terminate the Kohya pod. When you are ready to test in ComfyUI, relaunch your ComfyUI pod and upload the files to `/workspace/runpod-slim/ComfyUI/models/loras/` via JupyterLab.

### Using the LoRA in ComfyUI (RunPod)

Deploy your ComfyUI pod (following the Week 1 setup guide), open JupyterLab, and upload your `.safetensors` files to `/workspace/runpod-slim/ComfyUI/models/loras/`.

---

## Notes on troubleshooting attempts (April 2026)

- ashleykza template (`cu124-py311-25.2.1`): Start Training button unresponsive. Gradio WebSocket fails through RunPod proxy. Pod restarts do not fix it.
- AI-Dock template (`ghcr.io/ai-dock/kohya_ss:latest`): Different rendering issues, "no api found" errors on folder selection. Same underlying proxy conflict.
- Terminal training attempted: `accelerate` not found in PATH on ashleykza template; `venv/bin/activate` not present.
- `train_network.py` located at `/workspace/kohya_ss/sd-scripts/train_network.py`.
- Google Colab alternative (hollowstrawberry): discontinued as of February 2026.
- Kaggle alternative (FurkanGozukara): SD 1.5 notebook paywalled on Patreon.
