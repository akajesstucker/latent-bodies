---
dg-publish: true
tags: gardenEntry
pinned: true
---
# Latent Bodies —  Orientation

Welcome to **Latent Bodies**. This document covers a general overview of the course: who we are, how the course works, where things happen, and what you need to set up.

---

## The Course

**Latent Bodies** is a five-week online course exploring how generative AI diffusion tools reconstruct, represent, and transform the human body. We'll approach this technically and critically: building hands-on workflows in ComfyUI while asking what it means for a machine trained on human images to generate, control, and learn from bodies.

The course evolved from two previous editions of "Mediated Bodies" at School of Machines. This edition focuses specifically on Stable Diffusion, ControlNet, AnimateDiff, and LoRA training.

**Instructor:** Jess Tucker
**Facilitated by:** School of Machines (Rachel Uwa)

For the full course description, objectives, context, and week-by-week outline, see the [Syllabus](https://latentbodies.netlify.app/notes/syllabus/)

---

## Schedule

| Week | Date             | Title                      |
| ---- | ---------------- | -------------------------- |
| WK1  | Monday, March 30 | The Latent Body            |
| WK2  | Monday, April 6  | Prompting the Flesh        |
| WK3  | Monday, April 13 | Structured by the Real     |
| WK4  | Monday, April 20 | Training Identity          |
| --   | Monday, April 27 | NO CLASS                   |
| WK5  | Monday, May 4    | Bodies In and Out of Noise |

---

## Zoom

Sessions run **7:00–9:00pm CET** each Monday. Doors open at **6:50pm** for connection checks.

- **Join link:** https://us06web.zoom.us/j/89468681620?pwd=nQChyVjyfPaDTXK22bMGJb4C1QCA5P.1
- Meeting ID and passcode are pinned in Discord.

**Recordings:** every session is recorded, so if you have to leave early or miss a class you can catch up.

---

## Discord

Discord is our main hub for the course: questions, discussion, resource sharing, recordings, and community.

Join here: [check your welcome email]

Once you join, DM Rachel if your username is different from your real name so she can add you to the private class group.

---

## Code of Conduct

This course follows the **Berlin Code of Conduct**. Please read it before our first session.

https://berlincodeofconduct.org

---

## What to Expect Each Week

Each session is two hours and pairs a conceptual/historical discussion with a hands-on technical practice.

- **WK1** — Introduction and conceptual framing. You will *not* need any tools running yet.
- **WK2–4** — Hands-on in ComfyUI every week, building in complexity.
- **WK5** — Final presentations: share your finished work with the group.

Each week's assets, such as lecture slides, assigned readings, technical guides, example artists and artworks, and optional further references will be published here on the class site. Return to these after class if you want to review or go deeper.

---

## Technical Setup

You will need **ComfyUI** running before **Week 2**. You do not need it ready for the first session.

See the full setup guide: [Setup Guide](/notes/week-1/setup-guide/)

In brief, there are two paths:

**Cloud GPU (recommended for most students)**
Rent a GPU by the hour via RunPod or Vast.ai. Works on any modern laptop. No local installation required. Costs roughly $0.30–$1.00/hr while running.

**Local (Windows, NVIDIA GPU)**
Run everything on your own machine. Requires an NVIDIA GPU with 8GB+ VRAM (RTX 3070, 3080, 4070, etc.).

If you're not sure which path is right for you, the setup guide has a decision tree. Post in Discord if you get stuck before Week 2.

---

## What We'll Build

Over the five weeks you will:

1. Get oriented in **ComfyUI** and generate your first images and videos with SD 1.5
2. Build **AnimateDiff** workflows for short video generation
3. Use **ControlNet** to drive image generation from video of your own body
4. Train a **LoRA** on images of your own face or body — a small custom model that ComfyUI loads alongside the base model
5. Combine these tools to produce a short final project: still and/or moving image work

---

