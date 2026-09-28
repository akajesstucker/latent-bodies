---
dg-publish: true
permalink: /notes/week-1/setup-guide/
---
# Latent Bodies — ComfyUI Setup Guide

Welcome! This guide will walk you through getting ComfyUI running. Read through the whole thing before starting so you understand what path is right for you.

---

## Step 1: Choose Your Setup Path

Two cloud options, plus a local option if you have a capable GPU of your own. Read the comparison, then jump to the matching section below.

**Option A — Comfy Cloud** *(recommended starting point: zero setup, official ComfyUI platform)*
Sign up and ComfyUI opens instantly in your browser. For the custom explorations this course is focused on, you will need to subscribe to the Creator tier, which is $35 USD for a month — one month's subscription covers the whole course, so you can subscribe any time during or after Session 1.

**Option B — RunPod** *(full control, pay only for time that you use, known learning curve and limited GPU availability issues)*
You rent a GPU by the hour and manage the setup yourself: picking a region, picking a GPU, downloading the class model, restoring the class node snapshot. This most closely emulates a local install experience where you manage all files and dependencies yourself. The tradeoff: more steps to learn, and GPU availability changes minute to minute — it's normal to find your usual region showing mostly "Out of capacity" on a given day. This isn't a sign you're doing something wrong; it means try a different GPU tier, check back in a few minutes, or fall back to Comfy Cloud for that session if you're in a time crunch.

**Option C — Your Own Windows PC** *(no ongoing cost)*
Run everything on your own machine. Requires a dedicated NVIDIA graphics card with at least 8GB VRAM (e.g. RTX 3070, 3080, 4070, etc.). No ongoing costs beyond electricity.

If you are not sure which GPU you have: right-click your desktop → Display Settings → Advanced Display → your GPU name will be listed. If it says NVIDIA and has 8GB+ VRAM, you can use Option C. Otherwise, pick A or B based on the comparison below.


---
## Option A: Comfy Cloud Setup

Comfy Cloud is the official cloud version of ComfyUI, run by the same team that builds ComfyUI itself. It's the simplest option to get started with.

**1. Create an account**
Go to [comfy.org/cloud](https://comfy.org/cloud) and sign up

**2. Choose a plan**
For this course, you need the **Creator plan ($35/mo)** — the free tier cannot import any custom models, and Creator is confirmed to cover everything Week 3 (ControlNet/IP-Adapter) needs. Go to your account menu (top right) → **Plans & pricing** → **For Personal** → subscribe to Creator. One month's subscription covers the whole course, so you can subscribe any time during or after Session 1.

**3. Load the Week 1 class workflow**
Download the workflow `.json` (same files used on the RunPod/Local paths — see Step 4 below), then drag it directly onto the Comfy Cloud canvas in your browser.

**4. If you see a red-bordered or "missing" node:** This shouldn't happen with any workflow this course provides, since everything needed is already pre-installed. The Week 1 text-to-image workflow has no image inputs, so this shouldn't come up here — later weeks (starting Week 3) use image input nodes, and a red border there means you need to connect an image file.

**5. If you see other errors:** this is usually a matter of a missing model. We will address this in more depth later in the course, but basically you'll need to manually select a model from the node dropdown, or import the model using a link.

**6. Run a generation**
Click **Run** in the top right. Generations are billed by credits (included in your plan), not by the hour.

---

## Option B: Cloud GPU Setup (RunPod)

**1. Create an account**
Go to runpod.io and sign up. Verify your email.

**2. Add credits**
Go to Billing and add at least $10 to start. RunPod charges by the hour only while a pod is running — the GPU meter stops as soon as you terminate. Your Network Volume (set up in the next step) costs a small flat fee regardless: about $0.07/GB/month, so 50GB costs roughly $3.50/month total.

**3. Create a Network Volume**
A Network Volume is persistent storage that lives independently of any pod. When you terminate a pod, the volume — and everything on it (models, workflows, LoRA files) — survives. You attach it to a new pod next session and pick up exactly where you left off.

The volume is tied to a specific region. You will deploy all your pods into this same region. You can change GPU types between sessions (e.g. use an RTX 4090 one week, a different card the next) as long as you stay in the same region.

- In the left sidebar, click **"Storage"**
- Click **"New volume"**
- You'll be asked to pick a **Storage type** — choose **"Network volume"** (not "Global volume," a newer elastic option we don't need for this course)
- **Choose a data center.** A panel on the right shows live GPU availability by type — click a GPU type there to filter the data center list down to regions where it's currently available. This is important: the volume is permanently locked to one region, and GPU availability varies significantly by region and **changes minute to minute**, not just session to session — so treat any specific region recommendation as a starting point to check live, not a guarantee.

  Starting points to check first:
  - **US students:** try US-TX-3, or other US regions such as US-CA-2, US-IL-1, US-MO-2, US-NC-2, US-NE-1, US-CO-1
  - **EU students:** try EU-RO-1 (Romania), or other EU regions such as EU-FR-1, EU-NL-1, EUR-NO-1/2, EUR-IS-1/3

  You are not strictly limited to your nearest region — pick whichever shows good GPU availability right now in the panel on the right. A slightly more distant region will not affect your experience; latency to the pod is negligible for browser-based use.
- Set the network volume size to **50 GB**
- The name is auto-generated — you can leave it or change it to something memorable (e.g. `comfyui-workspace`)
- Click **Create**
Note: You can cancel your storage subscription at any time, so you only need to plan on paying for it for the duration of the course (approx. 5 USD total for 50GB). At the end of the course, you can download your files and close the network volume, unless you plan on still working on projects there.

**4. Deploy a ComfyUI pod**
- Click **"Hub"** in the left sidebar, then find and click the **"ComfyUI"** card
- You'll see two versions — pick **"ComfyUI - CUDA 13.0"** (the newer of the two; if it's ever having problems, "ComfyUI - CUDA 12.8" is the fallback)

Note: the Home page has a "ComfyUI Endpoint" card — do not use that. It is a serverless API product, not the interactive ComfyUI interface. Always start from the Hub.

- Click the **"Deploy ComfyUI - CUDA 13.0"** button. This opens a single **"Deploy a Pod"** page with a few sections: Workload, Region, Compute, Storage, plus a running cost summary on the right.
- In the **Region** section, open the volume dropdown (top right of that section) and select the Network Volume you created in Step 3. This both attaches your volume and locks the region to match it.
- In the **Compute** section, leave the cloud type at **Secure** (the default, under the filter options). Click the **"Available"** tab — this shows what's actually deployable right now, as opposed to "Recommended," which can list GPUs that turn out to be out of capacity.
- **Pick anything with at least 20GB VRAM** (24GB+ preferred). Do not chase a specific model name — GPU availability on RunPod changes minute to minute, not just session to session, so a card that's here today may be gone in an hour and vice versa. Skip anything that says "Out of capacity." Also skip the very expensive datacenter-class cards if you see them (H100, H200, A100, B200, B300, MI300X, RTX PRO 6000) — massive overkill and cost for this course; if one of those is all that's showing, try a different region or check back shortly.
- In the **Storage** section, confirm your Network Volume is listed and mounted at `/workspace`, and leave the Container Disk at its default **150GB**.
- Click **"Deploy Pod"** in the right-hand summary panel. If you get an "instance not available" error, the GPU you picked just got taken by someone else — pick a different one from the Available tab and try again.

**5. Wait for ComfyUI to finish starting**
After deploying, the right panel opens automatically and you are taken to the Pods page. Click the **"Logs"** tab, then click the **"Container"** sub-tab (it may not be selected by default — check, and switch to it if "System" is showing instead). On first boot, ComfyUI copies itself to your workspace, then fetches its node registry data, then finishes loading — this can take **5-10 minutes** on a fresh pod, longer than you might expect. You'll see a long stream of `FETCH ComfyRegistry Data: N/191` lines partway through — this is normal, just let it run.

The easiest way to check readiness: click the **"Connect"** tab instead of watching logs. Once Port 8188 (ComfyUI), Port 8888 (JupyterLab), and Port 8080 (FileBrowser) all show a green **"Ready"** label, you're good to go — you don't need to hunt for a specific log line.

**6. Open ComfyUI**
- In the right panel, click the **"Connect"** tab
- Ignore the line about SSH -- you do not need this.
- Under "HTTP services", click **"ComfyUI"** next to Port 8188
- ComfyUI will open in your browser in a new tab
- **The first time it loads, a "Templates" gallery will pop up over the canvas.** Close it with the X in the top right — you don't need a template, you'll load the class workflow file directly in Step 4 below.


**7. Terminate your pod when done**
When you finish a session, go back to RunPod's Pods page. This is now a two-step process:
- Click the **⋮** menu next to your pod and choose **"Stop Pod"** (confirm in the dialog). This stops GPU billing immediately — the cost shown drops to $0.00/hr.
- Then click the **⋮** menu again and choose **"Terminate Pod"** (only appears once a pod is stopped) to fully delete it and free up the listing.

Either way, your files are safe once "Stop" is done — everything in `/workspace` is on your Network Volume, which is not affected by stopping or terminating the pod itself. (The confirmation dialog will warn "ALL DATA will be lost" if you don't have a volume attached — that warning doesn't apply to your Network Volume data, only to anything you saved outside it.)

Next time you connect: repeat from step 4. Your volume will be there, with all your models and files intact.

---

## Option C: Local Windows Setup
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

**Comfy Cloud:** there's no folder structure to manage — everything goes through the Model Library (left sidebar → **Import**, paste a Civitai/HuggingFace link). Skip this section and go to Step 2 below.

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

### Cloud (Comfy Cloud)
Nothing to do here — the model is already pre-loaded. Skip to Step 3.

---

## Step 3: Restore the Class Node Snapshot (RunPod / Local only)

**Comfy Cloud users: skip this step entirely** — there is no snapshot concept on Comfy Cloud. Node versions are managed platform-wide, not per-workflow, and everything this course uses is already installed. Go straight to Step 4.

For RunPod and Local: we use a specific set of tools pinned to versions that work together reliably. Instead of installing nodes one by one, you will restore a "snapshot" that installs everything at once.

**1. Download the class snapshot file**
- For week 1 setup and testing, you will use a simple text-to-image workflow. 
	Download it here: [Latent-Bodies_Snapshot-1_Text-to-Image](https://raw.githubusercontent.com/akajesstucker/latent-bodies/main/Latent-Bodies_Snapshot-1_Text-to-Image.json) (right-click the link and choose "Save link as..." to download the file)

**2. Place the snapshot .json file in the correct subfolder of your ComfyUI folder**
- **Cloud (RunPod)**: Use the JupyterLab file browser in the left bar and navigate to `/workspace/runpod-slim/ComfyUI/user/__manager/snapshots` (note: `__manager` with a *double* underscore). Drag and drop the `Latent-Bodies_Snapshot-1_Text-to-Image.json` file there.
- **Local (Windows)**: Open Windows Explorer and navigate to your `ComfyUI_windows_portable\ComfyUI\user\__manager\snapshots` folder (note: `__manager` with a *double* underscore). Drag and drop the `Latent-Bodies_Snapshot-1_Text-to-Image.json` file there.

**2. Restore the snapshot**
- Click "Manager" in ComfyUI
- Click "Snapshot Manager"
- Click "Restore" and select the `Latent-Bodies_Snapshot-1_Text-to-Image.json` file
- ComfyUI will install all required nodes automatically (you probably won't need any for this one, but the process will be the same in the future as we start building more complex workflows)
- Restart ComfyUI when prompted using the Restart button

---

## Step 4: Test Your Setup

**1. Use the demo workflow**
If you don't see it already after loading the snapshot, [download the workflow .json file here](https://raw.githubusercontent.com/akajesstucker/latent-bodies/main/Latent-Bodies_Workflow-1_Text-to-Image.json) (right-click the link and choose "Save link as..." to download the file):

You don't have to put this in a ComfyUI subfolder, but save it somewhere that makes sense for you (i.e. a Latent Bodies class folder).

Drag and drop the file into the ComfyUI interface. 
You should see a workflow appear like below:
![_Latent-Bodies_Workflow-1_Text-to-Image - ComfyUI — Mozilla Firefox 3_29_2026 8_09_34 PM.png](/img/latent-bodies-workflow-1-text-to-image-comfyui-mozilla-firefox-3-29-2026-8-09-34-pm.png)

**2. Run a generation**
Click "Run". You should see progress in the terminal and an image appear in ComfyUI in the final "Save Image" node within 20–60 seconds depending on your GPU.

**3. Experiment**
Generate a few images using the prompts loaded into the sample workflow (Positive prompt: body, Negative Prompt: nsfw). See what the model associated with body. Then try adding to or changing the prompt to your liking, but especially explore what kinds of bodies you can make with prompts alone.

**4. Check Output Images**
**RunPod / Local:** files save automatically to the 'Output' subfolder of your ComfyUI directory. RunPod users: navigate to the output folder in the Jupyter page and you can download them to your own computer.
**Comfy Cloud:** there's no output folder — your generation appears as a thumbnail in the Job Queue panel. Click it, then use the download icon to save it to your computer.

Note: every image ComfyUI saves has the full workflow embedded in it by default (so you or anyone else can drag it back into ComfyUI to reload exactly how it was made). If you're sharing a generated image outside of class — posting it, sending it to someone — be aware that workflow data travels with the file unless you strip it first.

Share a few of your generated images to the class discord and feel free to discuss your findings there. We will follow up in class next week!


---

## Troubleshooting

**CLIP Text Encode (Prompt) nodes empty**
These two nodes are your positive and negative text prompt boxes. The snapshot will load these with the positive prompt "body" and the negative prompt "nsfw." If you don't see the text boxes inside these 2 nodes, you need to right-click on one at a time and select "Reload Node"

**"Model not found" error**
The checkpoint file is not in the right folder, or ComfyUI has not been refreshed. Check the folder path and click the refresh button.

**"Custom node missing" or red nodes in the workflow**
- **RunPod / Local:** The snapshot was not fully restored, or a node failed to install. Open Manager → "Install Missing Custom Nodes" and restart.
- **Comfy Cloud:** There's no self-service fix for this yet — custom node installation is still in beta there. This shouldn't happen with any workflow this course provides (everything needed is already pre-installed and confirmed working), so if you do see it, double check you loaded the right workflow file, then ask in Discord. If a node is genuinely unavailable on Comfy Cloud, switch to RunPod or Local for that workflow instead.

**ComfyUI loads but generation never starts (cloud)**
Your pod may have run out of VRAM. Try a machine with more VRAM, or restart the pod.

**RunPod pod is "Running" but port 8188 is not showing or the link does not open**
ComfyUI copies itself to your workspace and loads on first boot, which takes about 3-4 minutes after the pod reaches "Running". Check the Logs tab (Container sub-tab) and wait for `[ComfyUI-Manager] All startup tasks have been completed.` before trying to connect.

**"Machine doesn't have the right resources" error when deploying**
Click Deploy again — you will be assigned a different machine and it usually succeeds. If it keeps failing on the same GPU type, choose a different one (e.g. RTX 3090 or L40S).

---

## Cost Estimates (RunPod)

RunPod is pay-per-hour, based on whichever GPU is actually available when you deploy (see Step 4) — so exact weekly cost varies. As a rough range: a 2-hour class session typically runs **$0.50–$1.50** in GPU time, and terminating your pod between sessions means you pay nothing while not actively using it.

Keep an eye on your credit balance. RunPod sends low-balance warnings. Add credits before class sessions so you are not interrupted.

**Comfy Cloud, for comparison,** is a flat monthly subscription rather than pay-per-hour: **$35/mo for the Creator tier** this course needs — one month covers the whole course, so subscribe any time during or after Session 1. Generations are billed by credits included in your plan, not by session length.

---

## Before Week 2 Checklist

- [ ] Account created on Comfy Cloud or RunPod (cloud), OR ComfyUI portable extracted (local)
- [ ] ComfyUI opens in your browser and loads without errors
- [ ] RunPod/Local only: ComfyUI Manager installed and class snapshot restored
- [ ] SD 1.5 model downloaded and in the correct folder (or confirmed pre-loaded, on Comfy Cloud)
- [ ] Test workflow runs and produces an image
- [ ] Experiment with "body" images using text prompts
- [ ] Share a few image outputs to the class Discord
- [ ] Read "A Body in Motion" by Corey Keller and/or "Identification of a Photograph With a Person at Liberty" by Josh Ellenbogen to discuss in next week's class

If you get stuck at any step, post in the class Discord for help.
