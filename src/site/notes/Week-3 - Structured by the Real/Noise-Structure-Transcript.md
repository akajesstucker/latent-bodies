# Noise & Structure in Diffusion Models — Session Transcript

*Date: 2026-04-13*

---

**User:** i'm researching the technical functions of noise in machine learning, specifically diffusion based image generation (but i'm interested more broadly as well). i understand how gaussian noise is involved in the training process, the latent image's initial noise injection based on the seed value, and the iterative denoising process, basically. but i'm wondering if there's something fractal like or perlin noise like directly involved in or emerging from the denoising process -- since figures first form into these kind of abstract curvy noise-like patterns reminiscent of how perlin noise or fractal noise is used in computer graphics to simulate natural shapes and movement like clouds, water, fire, terrains, etc. can you explain?

---

**Claude:** The visual similarity you're noticing is real and has a precise technical explanation. It's not that diffusion models use anything like Perlin noise internally, but the statistics that emerge during denoising are genuinely analogous. Let me break it down.

---

## The Frequency Decomposition View

The key insight is that **different noise levels correspond to different spatial frequency scales**.

Pure Gaussian white noise has a flat power spectral density (PSD) -- equal energy at all frequencies. Natural images, by contrast, have a PSD that falls off roughly as 1/f^alpha (typically alpha ~2), meaning they are dominated by low-frequency content. Perlin and fractal noise are explicitly engineered to approximate this same 1/f spectrum -- that's precisely why they look "natural."

During the reverse diffusion process (denoising), the model is gradually transforming a white noise signal into something with a natural image spectrum. At each intermediate step, the latent has a spectrum somewhere between white noise and 1/f^2. Those intermediate states -- dominated by low frequencies but not yet fully resolved -- **are perceptually indistinguishable from colored/fractal noise**. That's what you're seeing.

---

## Why Low Frequencies Come First

The U-Net architecture used in most diffusion models processes features at multiple spatial resolutions (typically 4-8 levels via downsampling/upsampling). This has a structural consequence:

- The bottleneck (coarsest level) sets global, low-frequency structure
- Higher-resolution layers fill in progressively finer detail
- At high noise levels (t near T), the signal-to-noise ratio is only favorable for low-frequency components -- the high-frequency signal is entirely buried in noise
- So the model can only "see" and reconstruct coarse blobs early on; fine structure only becomes predictable as SNR improves

This is not incidental to the architecture -- it's a feature. The model is doing **coarse-to-fine generation**, and the coarse stages naturally produce smooth, correlated, blob-like patterns.

---

## The "Natural Image Statistics" Connection

Here's the deeper reason those early-stage blobs look like clouds, terrain, or smoke: **they share the same statistical distribution.**

Natural phenomena like clouds, fluid turbulence, and terrain relief all have approximately 1/f^2 power spectra (this is related to the Kolmogorov spectrum of turbulence, the fractal dimension of coastlines, etc.). The model, trained exclusively on natural images, has internalized these statistics. When it reconstructs from high noise, it first commits to the statistical skeleton of the image -- a low-frequency, correlated, smooth field -- which is the same distribution that Perlin noise and fractal noise are engineered to approximate.

So it's not that fractal noise is used or generated as such. It's that the early denoising trajectory passes through the region of signal space that looks like fractal noise, because that region is the low-frequency subspace of the natural image manifold.

---

## The Noise Schedule's Role

In DDPM and its descendants, the forward process adds noise according to a variance schedule: x_t = sqrt(alpha_bar_t) * x_0 + sqrt(1 - alpha_bar_t) * epsilon. As t increases, the original signal is progressively masked. During reverse denoising, the predicted x_0 at early timesteps is heavily blurred -- the model averages over all high-frequency possibilities consistent with the visible low-frequency signal. This blur is not random; it's a specific kind of smooth, multi-scale blur that again resembles fractal noise textures.

In EDM (Karras et al. 2022) and related work, this was formalized: the model's output at each noise level corresponds roughly to an optimal low-pass filter at the corresponding frequency scale.

---

## Summary

| What you see | Why it happens |
|---|---|
| Blob-like, smooth, curvy shapes | Low-frequency structure established first; high-freq masked by noise |
| Multi-scale structure | U-Net processes at multiple resolutions simultaneously |
| Looks like Perlin/fractal noise | Both have 1/f^alpha power spectra -- same statistical class |
| Resolves coarse-to-fine | SNR is frequency-dependent; coarse detail recoverable earlier |

The fractal-noise appearance is emergent and inevitable given the physics: any process that transforms white noise into a 1/f^2-distributed signal will pass through intermediate states that look like colored/fractal noise. Diffusion models don't use or produce Perlin noise, but they traverse exactly that statistical neighborhood during denoising.

---

**User:** is this exacerbated by animate diff?

---

**Claude:** Yes, in several compounding ways.

## What AnimateDiff Adds

AnimateDiff inserts **temporal attention modules** into the U-Net's spatial layers. These modules attend across the frame dimension, enforcing consistency across the video sequence. This interacts with the frequency structure in ways that amplify exactly the effect you're describing.

## How It Exacerbates the Fractal Appearance

**1. Temporal coherence favors low frequencies**

The temporal attention modules can more easily maintain consistency for low-frequency spatial structure (large blobs, smooth gradients) than for high-frequency detail. High-frequency content is harder to lock down temporally -- it requires precise per-frame agreement on fine details. So the motion modules effectively double down on the low-frequency skeleton, making the blob-like, Perlin-esque structure more dominant and more persistent across frames.

**2. Noise initialization correlation**

AnimateDiff needs the per-frame noise tensors to have some temporal relationship, otherwise each frame starts from an independent random state and the motion modules can't bridge them coherently. Many implementations use **partially shared or blended noise** across frames -- this introduces explicit temporal correlation into the initial noise, giving it a colored (non-white) spectrum in the temporal dimension before denoising even begins. The result is that the initial state already has the smooth, slow-varying quality of fractal noise, now in both space and time.

**3. The motion prior is itself 1/f in time**

The motion modules were trained on real video. Natural motion has approximately 1/f temporal frequency content -- smooth, gradual movement dominates over high-frequency jitter. So the learned motion prior biases generation toward slow, fluid changes in the latent. In the early denoising steps, this produces temporally coherent, smoothly evolving blobs that are visually nearly identical to animated Perlin noise or fluid simulations.

**4. The "boiling" artifact as evidence**

The characteristic AnimateDiff artifact -- fine details "swimming" or "boiling" while overall composition holds -- is literally the frequency decomposition made visible across time. Low frequencies are temporally locked (the motion modules handle them well); high frequencies are not (they remain partially independent per frame). What you perceive as instability in the texture is the high-frequency content failing to reach temporal coherence, while the low-frequency Perlin-like scaffold stays stable.

## The Net Effect

In a static diffusion generation, the fractal-noise phase is transient -- it resolves as denoising progresses. In AnimateDiff, the temporal attention layer essentially **animates that phase** and makes it load-bearing for motion. The flowing, organic quality that makes AnimateDiff outputs look fluid is directly related to temporal attention operating on the low-frequency subspace of the natural image manifold -- the same statistical territory as animated fractal noise.
