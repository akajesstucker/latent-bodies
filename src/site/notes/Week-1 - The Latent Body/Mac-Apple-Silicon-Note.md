---
dg-publish: true
permalink: /notes/week-1/mac-apple-silicon-note/
---
# A Note on Macs / Apple Silicon (M-series)

The main [setup guide](/notes/week-1/setup-guide/) only lists a Windows + NVIDIA local option (Option C), because that's still the most reliable local path for this course. Here's the Mac situation, short version:

**It does work now.** ComfyUI runs natively on Apple Silicon through Comfy Desktop (comfy.org) or Comfy CLI, using Apple's Metal backend for real GPU acceleration, not just CPU.

**Whether it's worth it depends on your chip and RAM:**
- **M4 Pro / M4 Max with 32GB+ unified memory:** viable for local use. Week 1 (text-to-image) and Week 4 (LoRA) should run fine. ControlNet, IP-Adapter, and video denoising (Weeks 2-3) will run, but noticeably slower than a Windows/NVIDIA setup, and can hit memory limits on the heavier workflows.
- **Base M4, or under 32GB unified memory:** not recommended for this course. You'll be able to install ComfyUI and poke at simple text-to-image, but the class workflows will likely stall out or be too slow to use in a session.

**Bottom line:** if you have a capable Mac, local is worth trying for the lighter weeks, but don't rely on it for the whole course. Comfy Cloud (Option A) or RunPod (Option B) are the safer default for Mac users, especially once we hit video and ControlNet.
