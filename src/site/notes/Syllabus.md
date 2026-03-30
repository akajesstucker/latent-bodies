---
dg-publish: true
permalink: /notes/syllabus/
pinned: true
---
# Latent Bodies — Syllabus

**Online, Mondays 7–9pm CET, March 30 – May 4 (no class April 27)**
**Instructor:** Jess | **Facilitated by:** School of Machines

---

## Course Description

This course invites us to think about how bodies perform with and within the latent spaces of technological media. We will consider how bodies are broken apart, analyzed, and reconstructed in generative systems, from dataset fragmentation to diffusion-based reassembly. We will make and share ou1r own experimental artworks while exploring radical and canonical artists and theorists from this multifaceted field.

We will consider the "diffused" body and the generative perspective: how we see and how we are seen within computational fields of representation. The mechanisms underpinning generative AI processes shape the meaning of the image they produce, putting us in a kind of fluid, probabilistic body moving through noise to emerge as a strange new signal. With generative AI, unique possibilities open up to create images that reproduce and fracture identity, merge anatomies, hallucinate motion, and manipulate the very schema of the human form. When we work in node-based generative environments like ComfyUI, we can choose to simulate, recombine, or transcend the real, bending bodies and perspectives. In this course, we will expand our understanding of embodiment with generative AI technology, learning and experimenting with diffusion workflows inside ComfyUI. We will create new image and video works that use these pipelines to remap, reinterpret, translate, and manipulate our own lived and experienced embodiment. We will reflect on the nature of these tools and their complicated histories, as well as their current applications in the world, in order to situate our own creations within this broader critical context.

---

## Course Objectives

- Broaden knowledge of the historical evolution of new image-generating machine learning technologies, their precursors, practical applications, and cultural effects, especially regarding representations of the body.
- Become familiar with a wide range of artists whose works address, exemplify, or employ mediation of the body, within and beyond the medium of generative AI.
- Gain understanding of node-based generative workflows in contemporary creative software (ComfyUI), and apply any combination of the demonstrated methods to a conceptually driven still or moving image project of your own.
- Develop a meaningful and applicable knowledge of how relationships between technologies, datasets, and bodies shape identity and society.
- Consider expansive definitions of embodiment and agency, questioning cultural constructions of the individual.

---

## Context

Since the dawn of photography, media technologies have rapidly developed to represent and transcend our embodied experiences. But even before these innovations, humans have been compelled to depict and leave traces of their bodies, adding virtual layers to reality by transforming materials into images. A hand print on a cave wall, a body carved from wood, a shadow play with puppets, a painted cathedral ceiling of a God whose hand looks just like ours and reaches towards us — all of these demonstrate the human being negotiating its own reality and coming to terms with its place in the world through its body.

In contemporary mediated culture, we contend increasingly with media that not only host virtual experiences, but as Trevor Paglen puts it, our images are looking back at us, and not with the same kind of eyes or body. With the rise of generative AI, our bodies are not just tracked but scraped, tokenized, and statistically modeled. Our physical forms are entangled within mechanized systems of prediction, control, and infinite reproduction.

What becomes possible when we use these generative technologies creatively? Can we shed light on the problematic logics and perhaps some promising potentials embedded in these tools? Can we break them open or apply them in unexpected, weird, radical ways to offer different visions of what it means to have/be/experience a body in this world?

---

## Who Is This Course For?

This course is suitable for all experience levels and will be exciting for anyone interested in how visual culture and technology shape our understanding and experience of our own bodies. Whether your interests are more with the body or more with the machine, our experiments with the meeting of the two will be enriching for anyone wanting to find more room for creative expression within our current multimediated reality. Those with prior experience in AI art or video will find new ways of thinking about and combining these tools in their work. All participants will leave with a broader understanding of how and why diffusion models work, and the possibilities for using them counter to the status quo.

---

## Course Outline

### WK1 — The Latent Body (March 30)

- Introduction to the class and to each other.
- Origins of facial recognition in early surveillance strategies and how they feed current datasets.
- Motion Studies as precursor to the fragmented, datafied body.
- How is the body reconstructed as mechanical, suspect, or predictable?
- Technical overview: getting oriented with ComfyUI. Setup is homework after this session.

### WK2 — Prompting the Flesh (April 6)

- Technical workshop: building simple ComfyUI networks. Generating images and short videos using text/image prompts and scheduling.
- Evolution and innovation in representing the body (painting to photography to film to VR) and how text-to-image models extend and disrupt this history.
- Exploring the "latent space" of the body: drifting between human, animal, and machine forms.
- How is the body represented in the way we query the system, rather than just as the thing we see?

### WK3 — Structured by the Real (April 13)

- Technical workshop: using ControlNets in ComfyUI to structure compositions through diffusion generation.
- Using input video of our own performing bodies to drive the composition of generated output.
- What does it mean to source a lived bodily experience for movement in a hallucinated virtual world?

### WK4 — Training Identity (April 20)

- Examining methods and case studies of deepfakes.
- Technical workshop: training a LoRA (Low-Rank Adaptation) on your own face/body and generating images. Sourcing community LoRAs by others online to craft new aesthetic combinations.
- How do we understand our relationship to the machine when it mimics us, or reinterprets us through the lens of other people's values and desires?

### (No class April 27)

### WK5 — Bodies In and Out of Noise (May 4)

- Final presentations: sharing of participants' final works. Final projects may incorporate still and/or moving images.
- How do the productions of our trained machines teach us about our own trained beliefs and expectations with respect to bodies?
- Discussion: diffusing the body as a form of digital resistance or submission.

---

## Tools

The course emphasizes node-based generative AI workflows, specifically **ComfyUI**. We will cover:

- **ControlNet** — structural guidance from real body and video input
- **AnimateDiff** — temporal consistency and short video generation
- **IP-Adapter** — image referencing and conditioning
- **LoRA training** — custom model training on your own face and body

All tools run inside ComfyUI. Students without local NVIDIA hardware (8GB+ VRAM) will use cloud GPU services (RunPod).

---

## Final Project

The final project is a short still and/or moving image work produced using the techniques covered in the course. It should be conceptually driven, rooted in your own relationship to embodiment, identity, or the themes of the course, and make use of at least one of the diffusion workflows we build together. More details will follow.

---

## Cloud GPU Costs

Students using cloud services rather than local hardware should budget approximately:

| Activity             | Estimated cost |
| -------------------- | -------------- |
| Weekly 2hr session   | $1.00–$3.00    |
| LoRA training (WK4)  | $1.00–$2.00    |
| Independent practice | ~$0.54/hr      |

