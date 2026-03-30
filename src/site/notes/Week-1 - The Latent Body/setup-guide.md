---
dg-publish: true
permalink: /notes/week-1/setup-guide/
---
# Latent Bodies — ComfyUI Setup Guide

Welcome! This guide will walk you through getting ComfyUI running before our first session. Read through the whole thing before starting so you understand what path is right for you.

---

## Step 1: Choose Your Setup Path

**Option A — Cloud GPU (recommended for most students)**
You rent a powerful GPU online by the hour. No installation on your computer. Works on any laptop or desktop with a modern browser. You pay only while the GPU is running (roughly $0.30–1.00/hr depending on the service).

**Option B — Local Windows PC**
You run everything on your own machine. Requires a dedicated NVIDIA graphics card with at least 8GB VRAM (e.g. RTX 3070, 3080, 4070, etc.). No ongoing costs beyond electricity.

If you are not sure which GPU you have: right-click your desktop → Display Settings → Advanced Display → your GPU name will be listed. If it says NVIDIA and has 8GB+ VRAM, you can use Option B. Otherwise, use Option A.

---

## Option A: Cloud GPU Setup (RunPod)

RunPod is the recommended cloud GPU service for this course. We will also use it later for LoRA training.

**1. Create an account**
Go to runpod.io and sign up. Verify your email.

**2. Add credits**
Go to Billing and add at least $10 to start. RunPod charges by the hour only while a pod is running — the GPU meter stops as soon as you terminate. Your Network Volume (set up in the next step) costs a small flat fee regardless: about $0.07/GB/month, so 50GB costs roughly $3.50/month total.

Note: You can set billing to charge in local currency for your bank account — the exchange rate from USD to EUR has historically been favorable, so check this when setting up payment.

**3. Create a Network Volume**
A Network Volume is persistent storage that lives independently of any pod. When you terminate a pod, the volume — and everything on it (models, workflows, LoRA files) — survives. You attach it to a new pod next session and pick up exactly where you left off.

The volume is tied to a specific region. You will deploy all your pods into this same region. You can change GPU types between sessions (e.g. use an RTX 4090 one week, a different card the next) as long as you stay in the same region.

- In the left sidebar, click **"Storage"**
- Click **"Create Network Volume"**
- **Choose a region.** A panel on the right shows GPU availability for each region — click through regions to compare before committing. This is important: the volume is permanently locked to one region, and GPU availability varies significantly by region and changes over time.

  General guidance by location:
  - **US students:** US regions (such as US-TX-3, US-GA-1, US-CA-1) tend to have reliable availability of L4 and RTX A5000 cards. Start by checking these if you are in North America.
  - **EU students:** EU-RO-1 (Romania) consistently has RTX 4090 availability and is the most reliable EU region for this course. EU-SE-1 (Sweden) is a secondary option if EU-RO-1 is congested.

  You are not strictly limited to your nearest region — pick whichever has the best availability for the GPUs listed in the Deploy Pod section. A slightly more distant region will not affect your experience; latency to the pod is negligible for browser-based use.

  Note the tip shown on the page: you can click a GPU type in the right panel to filter the region list to only regions where that GPU is available. Use this to compare options quickly.
- Set the network volume size to **50 GB**
- The name is auto-generated — you can leave it or change it to something memorable (e.g. `comfyui-workspace`)
- Click **Create Network Volume**
Note: You can cancel your storage subscription at any time, so you only need to plan on paying for it for the duration of the course (approx. 5 USD total for 50GB). At the end of the course, you can download your files and close the network volume, unless you plan on still working on projects there.

**4. Deploy a ComfyUI pod**
- In the left sidebar under "The Hub", click **"Pod templates"**
- Find and click the **"ComfyUI"** template (published by Runpod, showing `runpod/comfyui:latest`)

Note: the Home page has a "ComfyUI Endpoint" card — do not use that. It is a serverless API product, not the interactive ComfyUI interface. Always start from Pod templates in the sidebar.

- Click **"Configure Pod"**
- On the GPU selection page, leave the cloud type set to **Secure cloud** (the default).
- Use the **"Any Region"** filter to select the same region where you created your Network Volume. The GPU list will update to show only what is available in that region.
- Choose a GPU from the table below. The key requirement is **VRAM: 20GB minimum, 24GB or more recommended**. System RAM is bundled with the GPU on RunPod — you do not choose it separately, but check that the listing shows at least **16GB RAM**. Avoid anything showing **Unavailable**.

  GPUs confirmed to work for this course, best options first:

  | GPU | VRAM | Approx. price | Notes |
  |-----|------|---------------|-------|
  | RTX PRO 4500 | 32GB | ~$0.54/hr | Recommended — most headroom for course workflows |
  | RTX 4090 | 24GB | ~$0.50/hr | Recommended — common in EU-RO-1 |
  | RTX A5000 | 24GB | ~$0.20/hr | Great value if available |
  | L4 | 24GB | ~$0.32/hr | Good — common in US regions |
  | L40S | 48GB | ~$0.71/hr | Excellent if budget allows |
  | RTX 3090 | 24GB | ~$0.34/hr | Solid option |
  | RTX A4500 | 20GB | ~$0.19/hr | Minimum viable — tight for video workflows |
  | RTX 4000 Ada | 20GB | ~$0.20/hr | Minimum viable — tight for video workflows |

  GPUs you may see that are **not recommended**: anything under 16GB VRAM (too tight for our workflows); RTX 5090, H100, H200, A100, MI300X (these work technically but are expensive or overkill cards — no benefit for this course, and very new hardware like the 5090 can have compatibility issues with older CUDA dependencies).

  Availability changes from session to session. If your preferred GPU is unavailable, check back in a few hours or choose the next option in the table.
- On the "Configure Deployment" page, scroll down to **Storage Configuration**. Under the **Network Volume** section, select the volume you just created from the dropdown.
- Leave the Container Disk at 150GB.
- Scroll down and click **"Deploy On-Demand"**

**5. Wait for ComfyUI to finish starting**
After deploying, the right panel opens automatically and you are taken to the Pods page. Click the **"Logs"** tab (the "Container" sub-tab is selected by default, which is correct). On first boot, ComfyUI copies itself to your workspace and finishes loading — this takes about 3-4 minutes. Wait until you see this line appear:

```
[ComfyUI-Manager] All startup tasks have been completed.
```

This is your signal that ComfyUI is ready. Do not try to connect before this line appears.

**6. Open ComfyUI**
- In the right panel, click the **"Connect"** tab
- Ignore the line about SSH -- you do not need this.
- Under "HTTP services", click **"ComfyUI"** next to Port 8188
- ComfyUI will open in your browser in a new tab

**7. Terminate your pod when done**
When you finish a session, go back to RunPod and click **"Terminate"** on your pod. This deletes the pod and stops all billing immediately. Your files are safe — everything in `/workspace` is on your Network Volume, which is not affected by termination. 

Next time you connect: repeat from step 4. Your volume will be there, with all your models and files intact.

---

## Option B: Local Windows Setup
[Link to Video Tutorial](https://www.youtube.com/watch?v=VymoG_UVkxk)

**Requirements:**
- Windows 10 or 11
- NVIDIA GPU with 8GB+ VRAM
- 32GB+ RAM
- At least 50GB of free disk space

**1. Download ComfyUI Portable**
Download the latest portable release from:
`https://github.com/comfyanonymous/ComfyUI/releases`

Look for the file named something like `ComfyUI_windows_portable_nvidia.7z`

You will need a tool that can extract `.7z` files. Any of these work:
- **7-Zip** (free) — 7-zip.org
- **WinRAR** — if you already have it installed

**2. Extract the files**
Right-click the downloaded `.7z` file and extract it using your tool (7-Zip: "Extract to current folder", WinRAR: "Extract Here"). This creates a folder called `ComfyUI_windows_portable`.

**3. Launch ComfyUI**
Open the `ComfyUI_windows_portable` folder and double-click:
```
run_nvidia_gpu.bat
```
A terminal window will open. Wait for it to finish loading — you will see a line like `To see the GUI go to: http://127.0.0.1:8188`. Then open your browser and go to that address (it might open automatically).

**4. Leave the terminal window open**
ComfyUI runs as long as that terminal window is open. Do not close it while using ComfyUI.

**5. Install ComfyUI Manager**
ComfyUI Manager lets you install and manage extra tools (called "custom nodes") that we will use in class.

- Download `install-manager-for-portable-version.bat` from: `https://github.com/ltdrdata/ComfyUI-Manager/raw/main/scripts/install-manager-for-portable-version.bat`
- Move the downloaded file into your `ComfyUI_windows_portable` folder
- Double-click it to run it

Then restart ComfyUI. A "Manager" button will appear in the top bar of the interface.

---

## Note on Managing Files

ComfyUI's folder structure is the same whether you are running locally or on RunPod. You will regularly need to place files (models, LoRAs, workflows) in specific subfolders. Here is how to do that on each path.

### Folder structure reference

```
ComfyUI/
  models/
    checkpoints/    ← base model (.safetensors)
    loras/          ← LoRA files (.safetensors)
    controlnet/     ← ControlNet models
  custom_nodes/     ← installed via Manager
  input/            ← images you want to use as inputs
  output/           ← generated images are saved here
```

### Cloud (RunPod)

Use the JupyterLab terminal to download files directly to your network volume. Your instructor will provide direct download URLs for all class files, and you'll follow the steps in the next section (Download the Class Model) for other models and assets in the future.

### Local (Windows)

Open Windows Explorer and navigate to your `ComfyUI_windows_portable\ComfyUI\` folder. The subfolders above are all inside it. When you download models, you'll put them Drag and drop files into the appropriate subfolder.

---

## Step 2: Download the Class Model

We will use a specific SD 1.5 checkpoint for the course. 

### Cloud (RunPod)
- In the RunPod right panel, click the **"Connect"** tab
- Under "HTTP services", click **"JupyterLab"** next to Port 8888
- In JupyterLab, go to **File → New → Terminal** (or click the Terminal icon on the launcher)
- Copy and paste the script below and press enter to run it and download the model:

```bash
wget "https://huggingface.co/stable-diffusion-v1-5/stable-diffusion-v1-5/resolve/main/v1-5-pruned-emaonly.safetensors" -O /workspace/runpod-slim/ComfyUI/models/checkpoints/v1-5-pruned-emaonly.safetensors
```

Note: After adding any file to your volume, click the browser's **refresh button** of the open ComfyUI window. You do not need to restart.

### Local (Windows)
[Download the model by clicking this URL](https://huggingface.co/stable-diffusion-v1-5/stable-diffusion-v1-5/resolve/main/v1-5-pruned-emaonly.safetensors) and place it in the `ComfyUI_windows_portable\ComfyUI\models\checkpoints` folder.

Note: After adding any file to your ComfyUI installation, click the browser's **refresh button** on the open ComfyUI window to make sure the new files load. If still not showing, you can go into the ComfyUI Manager and click Restart. This will restart ComfyUI entirely, which may take a couple of minutes.

---

## Step 3: Restore the Class Node Snapshot

We use a specific set of tools pinned to versions that work together reliably. Instead of installing nodes one by one, you will restore a "snapshot" that installs everything at once.

**1. Download the class snapshot file**
- For week 1 setup and testing, you will use a simple text-to-image workflow. 
	Download it here: [Latent-Bodies_Snapshot-1_Text-to-Image](https://github.com/akajesstucker/latent-bodies/blob/main/Latent-Bodies_Snapshot-1_Text-to-Image.json)
	(click download icon at the top right of the page, or press `ctrl + shift + s` on keyboard)

**2. Place the snapshot .json file in the correct subfolder of your ComfyUI folder**
- **Cloud (RunPod)**: Use the JupyterLab file browser in the left bar and navigate to `/runpod-slim/ComfyUI/user/_manager/snapshots`. Drag and drop the `Latent-Bodies_Snapshot-1_Text-to-Image.json` file there.
- **Local (Windows)**: Open Windows Explorer and navigate to your `ComfyUI_windows_portable\ComfyUI\user\_manager\snapshots` folder. Drag and drop the `Latent-Bodies_Snapshot-1_Text-to-Image.json` file there.

**2. Restore the snapshot**
- Click "Manager" in ComfyUI
- Click "Snapshot Manager"
- Click "Restore" and select the `Latent-Bodies_Snapshot-1_Text-to-Image.json` file
- ComfyUI will install all required nodes automatically (you probably won't need any for this one, but the process will be the same in the future as we start building more complex workflows)
- Restart ComfyUI when prompted using the Restart button

---

## Step 4: Test Your Setup

**1. Use the demo workflow**
If you don't see it already after loading the snapshot, [download the workflow .json file here](https://github.com/akajesstucker/latent-bodies/blob/main/Latent-Bodies_Workflow-1_Text-to-Image.json):

You don't have to put this in a ComfyUI subfolder, but save it somewhere that makes sense for you (i.e. a Latent Bodies class folder).

Drag and drop the file into the ComfyUI interface. 
You should see a workflow appear like below:
![_Latent-Bodies_Workflow-1_Text-to-Image - ComfyUI — Mozilla Firefox 3_29_2026 8_09_34 PM.png](/img/latent-bodies-workflow-1-text-to-image-comfyui-mozilla-firefox-3-29-2026-8-09-34-pm.png)

**2. Run a generation**
Click "Run". You should see progress in the terminal and an image appear in ComfyUI in the final "Save Image" node within 20–60 seconds depending on your GPU.

**3. Experiment**
Generate a few images using the prompts loaded into the sample workflow (Positive prompt: body, Negative Prompt: nsfw). See what the model associated with body. Then try adding to or changing the prompt to your liking, but especially explore what kinds of bodies you can make with prompts alone.

**4. Check Output Images**
Your files will save automatically to the 'Output' subfolder of your ComfyUI directory.
RunPod Users: navigate to the output folder in the Jupyter page and you can download them to your own computer. 

Share a few of your generated images to the class discord and feel free to discuss your findings there. We will follow up in class next week!


---

## Troubleshooting

**CLIP Text Encode (Prompt) nodes empty**
These two nodes are your positive and negative text prompt boxes. The snapshot will load these with the positive prompt "body" and the negative prompt "nsfw." If you don't see the text boxes inside these 2 nodes, you need to right-click on one at a time and select "Reload Node"

**"Model not found" error**
The checkpoint file is not in the right folder, or ComfyUI has not been refreshed. Check the folder path and click the refresh button.

**"Custom node missing" or red nodes in the workflow**
The snapshot was not fully restored, or a node failed to install. Open Manager → "Install Missing Custom Nodes" and restart.

**ComfyUI loads but generation never starts (cloud)**
Your pod may have run out of VRAM. Try a machine with more VRAM, or restart the pod.

**RunPod pod is "Running" but port 8188 is not showing or the link does not open**
ComfyUI copies itself to your workspace and loads on first boot, which takes about 3-4 minutes after the pod reaches "Running". Check the Logs tab (Container sub-tab) and wait for `[ComfyUI-Manager] All startup tasks have been completed.` before trying to connect.

**"Machine doesn't have the right resources" error when deploying**
Click Deploy again — you will be assigned a different machine and it usually succeeds. If it keeps failing on the same GPU type, choose a different one (e.g. RTX 3090 or L40S).

---

## Cost Estimates (Cloud Users)

| Item | Cost |
|---|---|
| Network Volume (50GB) | ~$3.50/month — flat fee, always running |
| Weekly 2hr class session (A40) | ~$0.40–$0.80 per session (GPU time only) |
| Weekly 2hr class session (RTX 4090) | ~$1.00–$1.50 per session (GPU time only) |
| LoRA training run (Week 4) | ~$0.40–$1.50 depending on GPU and duration |
| Independent practice | ~$0.20–$0.50/hr depending on GPU |

With a Network Volume, you terminate your pod after each session and pay nothing between sessions except the ~$3.50/month storage flat fee.

Keep an eye on your credit balance. RunPod sends low-balance warnings. Add credits before class sessions so you are not interrupted.

---

## Before Week 2 Checklist

- [ ] Account created on RunPod (cloud), OR ComfyUI portable extracted (local)
- [ ] ComfyUI opens in your browser and loads without errors
- [ ] ComfyUI Manager installed
- [ ] Class snapshot restored
- [ ] SD 1.5 model downloaded and in the correct folder
- [ ] Test workflow runs and produces an image
- [ ] Experiment with "body" images using text prompts
- [ ] Share a few image outputs to the class Discord
- [ ] Read "A Body in Motion" by Corey Keller and/or "Identification of a Photograph With a Person at Liberty" by Josh Ellenbogen to discuss in next week's class

If you get stuck at any step, post in the class Discord for help.
