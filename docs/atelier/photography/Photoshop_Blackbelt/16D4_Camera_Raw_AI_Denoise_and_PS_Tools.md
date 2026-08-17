---
description: Noise Deep Dive Part 4 — a tool-level deep dive into Adobe Camera Raw noise reduction (Luminance, Luminance Detail, Luminance Contrast, Color, Color Detail, Color Smoothness), the Sharpening group and its Masking interaction with noise, Adobe AI Denoise as a learned joint demosaic-and-denoise producing an enhanced DNG, and the pixel-domain Photoshop noise tools (Reduce Noise, Gaussian Blur, Median, Dust & Scratches, Surface Blur, Smart Blur, Camera Raw Filter) mapped to problem type, with global vs local application throughout.
---

# Noise Deep Dive Part 4: Camera Raw, AI Denoise, and Photoshop Noise Tools

This is Part 4 of the eight-part Noise Deep Dive that expands [Chapter 16: Noise Reduction](16_Noise_Reduction.md). Where Chapter 16 established the concepts, this series takes each concept apart and rebuilds it at professional depth. The three preceding parts built the foundation: [Part 1](16D1_Physics_of_Digital_Noise.md) established the physics of digital noise (shot noise, read noise, SNR); [Part 2](16D2_Old_Sensors_and_Visual_Diagnosis.md) taught visual diagnosis and the specific pathologies of older sensors; [Part 3](16D3_Denoising_Mindset_RAW_vs_JPEG.md) established the denoising mindset and why the RAW-versus-JPEG distinction changes everything about how much you can do.

This part is the **tool chapter**. It answers a single question in three domains: given that you have diagnosed the noise (Parts 1-3), *which control do you reach for, what does it actually do to the data, and how do you know where to set it?* The three domains are (A) the Adobe Camera Raw noise-reduction and sharpening parameters, (B) Adobe AI Denoise, and (C) the pixel-domain noise tools inside Photoshop proper. Masking mechanics themselves — how to build and refine the masks that make noise reduction selective — belong to [Part 5](16D5_Frequency_Masking_and_Sharpening.md), and are cross-referenced rather than duplicated. AI upscaling and hallucinated detail belong to [Part 6](16D6_AI_Upscaling_and_Hallucinated_Detail.md).

---

## Learning Objectives

After completing this part, you will be able to:

1. Explain what each of the six Camera Raw noise-reduction sliders (Luminance, Luminance Detail, Luminance Contrast, Color, Color Detail, Color Smoothness) does to the image data, name its characteristic side effect, and identify the specific image characteristic that tells you how to set it — not merely which direction to drag it.
2. Explain how the Camera Raw Sharpening group (Amount, Radius, Detail, Masking) interacts with noise, and use the Masking slider with the Alt/Option preview as the primary control for sharpening genuine edges without sharpening noise.
3. Describe, at the level of publicly documented principle, how modern machine-learning raw denoising works — a learned prior applied to a joint demosaic-and-denoise operation that outputs an enhanced DNG — and distinguish it from classical parametric noise reduction that smooths based on local pixel statistics.
4. Decide when Adobe AI Denoise is worth using and when it is either unnecessary or too aggressive, predicting its characteristic effects on textures, skin, foliage, sky, and distant fine detail.
5. Situate AI Denoise and Super Resolution as sibling "Enhance" operations that both produce DNG files, and integrate them correctly into a modern RAW-to-DNG workflow without stacking redundant passes.
6. Map each Photoshop pixel-domain noise tool (Reduce Noise including per-channel Advanced mode, Gaussian Blur, Median, Dust & Scratches, Surface Blur, Smart Blur, Camera Raw Filter) to the specific problem type it actually suits, rather than treating them as interchangeable "smoothing" filters.
7. Apply any of these tools locally rather than globally by combining Smart Objects, Smart Filters, layers, and masks, and articulate why local application is almost always superior for noise work.
8. Choose deliberately between doing noise reduction in Camera Raw versus Photoshop for a given file, based on file type (RAW vs rendered), where the file is in the pipeline, and whether the treatment must be selective.
9. Diagnose the characteristic failure signatures of each tool (waxiness, color bleed, posterization, edge halos, hallucinated texture, blotching) and trace each signature back to the control that caused it.

---

## Conceptual Foundation

Every noise-reduction tool in the Adobe ecosystem does the same fundamental thing: it decides, for each location in the image, how much to trust the local pixel values and how much to replace them with an estimate derived from the neighborhood. That is the entire game. A tool is "good" when its decision about *what is noise and what is signal* is accurate, and "bad" when it confuses the two — smoothing genuine detail (over-reduction, the waxy look) or preserving noise it should have removed (under-reduction, residual grit).

The tools differ along three axes, and understanding those axes is what lets you choose deliberately rather than by habit.

**Axis 1 — What data does the tool see?** A Camera Raw slider operates on the raw sensor mosaic before it becomes RGB pixels, with full bit depth and before the tone curve has stretched shadow noise. A Photoshop filter operates on rendered, gamma-encoded RGB pixels, after demosaicing and after the tone curve has already amplified the shadows. This is the single largest determinant of effectiveness. [Part 3](16D3_Denoising_Mindset_RAW_vs_JPEG.md) established this in detail; here it becomes the practical reason Camera Raw is always your first line and Photoshop is the specialist follow-up.

**Axis 2 — How does the tool decide what to smooth?** Classical parametric tools use hand-designed rules: average pixels that are similar in value, protect pixels that differ (an edge). Median filters take the middle value of a neighborhood. Surface Blur and Smart Blur add explicit thresholds that say "only blend pixels within this tonal distance of the center." AI Denoise, by contrast, uses a *learned* decision — a neural network that has been trained on millions of noisy/clean pairs and has effectively learned what real photographic detail looks like versus what noise looks like. The classical tools apply a fixed formula; the learned tool applies a statistical prior about the appearance of real images.

**Axis 3 — Global or local?** A tool applied to an entire layer treats the well-exposed highlight (no visible noise) exactly like the crushed shadow (severe noise). Because noise visibility varies enormously across a single frame — this was the SNR argument of [Part 1](16D1_Physics_of_Digital_Noise.md) — treating the frame uniformly is almost always wrong. Local application, via masks, is not an advanced flourish; it is the default correct approach for serious noise work. This part emphasizes the global/local distinction for every tool, and points to [Part 5](16D5_Frequency_Masking_and_Sharpening.md) for the mask-building mechanics.

Hold these three axes in mind and the sprawling set of controls collapses into a decision tree: *What data do I have (raw or rendered)? How smart a decision do I need (parametric or learned)? Where does the treatment need to apply (everywhere or somewhere)?*

---

## Terminology

### Parametric Noise Reduction

**Definition:** Parametric (or "classical," or "algorithmic") noise reduction is any noise-suppression method that decides how to smooth each pixel by a fixed, hand-designed rule governed by numeric parameters (the sliders). The rule typically compares each pixel to its neighbors and blends them together in proportion to how similar they are, with the sliders controlling the strength of blending and the thresholds that distinguish "similar enough to average" from "different enough to protect as an edge."

**Meaning in photographic practice:** All six Camera Raw noise sliders and every Photoshop pixel-domain noise filter (Reduce Noise, Surface Blur, Smart Blur, Median) are parametric. They are fast, predictable, and completely deterministic — the same settings on the same file always produce the same result. Their limitation is that the rule is fixed: it has no knowledge of what real photographic detail looks like. A bilateral-style filter cannot know that a particular cluster of pixel variations is an eyelash rather than noise; it only knows the pixels differ from their neighbors by a certain amount. This is why parametric tools inevitably trade detail for smoothness — the rule cannot distinguish fine detail from noise when both produce the same local statistics.

**Related concepts:** Bilateral filter, edge-aware smoothing, AI Denoise, the fundamental trade-off (Chapter 16).

**Common misconception:** "Parametric noise reduction is obsolete now that AI exists." Parametric tools remain the right choice for moderate noise, for fast iteration, for selective application, and for any situation where you need a predictable, non-hallucinating result. See the AI Denoise section for when each approach wins.

---

### Learned Prior (Machine-Learning Denoise)

**Definition:** A learned prior is the statistical knowledge about "what real images look like" that a neural network acquires during training on large datasets. In the context of raw denoising, the model is trained on pairs of noisy and clean images (or synthetically noised versions of clean images) and learns to map noisy input to a clean estimate. The "prior" is the model's internalized expectation of natural image structure, which it uses to reconstruct plausible detail where the noise has obscured it.

**Meaning in photographic practice:** This is the mechanism that lets AI Denoise preserve detail that a parametric filter would smooth away. Because the model has effectively seen what eyelashes, foliage, fabric weave, and skin texture look like, it can distinguish those structures from noise even when their local pixel statistics are similar. The cost is that the output is a *prediction*, not a measurement — where the true signal is genuinely below the noise floor, the model outputs its best guess about what should have been there, which may or may not match reality. **INTERPRETATION:** the reconstruction is trustworthy where enough real signal survives for the model to anchor to, and progressively more speculative as the signal weakens. Distant fine detail at extreme ISO is exactly where the model has the least real signal to anchor to, and therefore where speculative reconstruction is most likely.

**Related concepts:** Parametric noise reduction, hallucinated detail ([Part 6](16D6_AI_Upscaling_and_Hallucinated_Detail.md)), neural network, joint demosaic-and-denoise.

**Common misconception:** "The AI recovers detail that was in the file." More precisely, it reconstructs a plausible estimate of detail. Where real signal survives, that estimate is close to truth. Where it does not, the estimate is invention that happens to look convincing.

---

### Joint Demosaic-and-Denoise

**Definition:** A single processing operation that performs demosaicing (interpolating the full RGB value for each pixel from the single-color Bayer or X-Trans samples) and denoising at the same time, rather than as two separate sequential steps.

**Meaning in photographic practice:** Traditional pipelines demosaic first and denoise afterward (or denoise the mosaic, then demosaic). Doing both jointly is advantageous because demosaicing and denoising are entangled problems — demosaicing errors and noise interact, and an algorithm that solves them together can make better decisions than one that solves them in sequence. **FACT:** Adobe describes AI Denoise as a machine-learning model that performs demosaicing and denoising together and outputs a new DNG. **UNCERTAINTY:** The precise internal architecture, training data, and per-manufacturer handling of Adobe's model are not publicly documented in detail; claims beyond "it is an ML model that jointly demosaics and denoises and outputs a DNG" should be treated as unverified.

**Related concepts:** Demosaicing (Chapter 03), Bayer array, learned prior, enhanced DNG.

**Common misconception:** "AI Denoise is just a stronger version of the Luminance slider." It is a structurally different operation applied at a different stage of the pipeline (the demosaic itself), not a more aggressive setting of the same parametric smoothing.

---

### Enhance / Enhanced DNG

**Definition:** "Enhance" is the family of Camera Raw and Lightroom operations that run a computationally heavy model over a raw file and write the result as a new DNG file. The current members of this family are **Denoise** (AI noise reduction), **Super Resolution** (roughly doubling linear resolution), and **Raw Details** (improved demosaicing). Each produces a new `.dng` alongside the original.

**Meaning in photographic practice:** The Enhance operations are not slider adjustments stored in metadata; they bake a new, larger raw file to disk. This has three consequences: they consume time and disk space, they are performed once (not re-tuned interactively like a slider), and their output is itself a raw file that you then develop normally with all the usual sliders. **FACT:** Denoise and Super Resolution are both Enhance operations that output DNG files. **VERSION NOTE:** The membership, naming, and behavior of the Enhance menu have changed across releases; verify against your version.

**Related concepts:** AI Denoise, Super Resolution ([Part 6](16D6_AI_Upscaling_and_Hallucinated_Detail.md)), DNG, non-destructive workflow ([Chapter 06](06_Non_Destructive_Workflow.md)).

**Common misconception:** "Enhancing is non-destructive like the sliders." It is non-destructive in the sense that the original raw is untouched, but unlike a slider it produces a new physical file and cannot be dialed back after the fact — you would re-run it at a different strength, producing yet another DNG.

---

### Masking (Sharpening)

**Definition:** In the Camera Raw Sharpening group, the Masking slider builds an edge mask from the image and restricts sharpening to the areas the mask identifies as edges. At 0 the mask is uniformly white and sharpening is applied everywhere; as the value rises, progressively only the strongest edges remain white (sharpened) while flat areas turn black (protected).

**Meaning in photographic practice:** Masking is the single most important control for preventing sharpening from amplifying noise, because noise lives in the flat areas (skies, skin, shadows) that the mask protects, while genuine detail lives at the edges the mask sharpens. Holding Alt/Option while dragging the slider displays the mask itself in grayscale, turning an abstract number into a direct visual decision: you drag until the flat, noisy areas have gone black and only the edges you want crisp remain white. This is covered as an interaction here and developed further as a frequency/masking concept in [Part 5](16D5_Frequency_Masking_and_Sharpening.md).

**Related concepts:** Sharpening (Chapter 15), the noise-sharpening tension (Chapter 16), Amount/Radius/Detail.

**Common misconception:** "Masking reduces sharpening." It does not reduce the strength of sharpening on the edges it keeps; it reduces the *area* over which sharpening is applied. Edges are sharpened at full Amount; flats are excluded entirely.

---

### Global vs Local Application

**Definition:** A global application of a noise tool affects every pixel of the layer uniformly. A local application confines the effect to specific regions through a mask, a selection, or a separate layer, so that different parts of the image receive different treatment (or none).

**Meaning in photographic practice:** Because noise visibility tracks the signal-to-noise ratio, and SNR varies across the frame by orders of magnitude (bright highlights versus crushed shadows), the "correct" amount of noise reduction is different in every region. A global setting is necessarily a compromise calibrated to the worst region, which over-treats every better-exposed region. Local application — the same tool, confined by a mask — resolves this by applying strong treatment only where noise is severe and none where the image is already clean. Camera Raw supports local noise reduction through its local-adjustment (Masking) tools; Photoshop supports it through Smart Filters plus layer masks. The mechanics of building those masks are the subject of [Part 5](16D5_Frequency_Masking_and_Sharpening.md).

**Related concepts:** Luminosity masks (Chapter 13), layer masks (Chapter 05), Smart Filters (Chapter 06).

**Common misconception:** "Local noise reduction is only for advanced work." It is the baseline professional approach. Global noise reduction is the shortcut you take only when the noise happens to be uniform or the output is small enough to hide the cost.

---

## Theory

### The Two Places Noise Reduction Can Happen, and Why the Order Is Fixed

There are exactly two stages at which you can reduce noise in the Adobe pipeline: on the raw data inside Camera Raw (during development, or via the Camera Raw Filter on a Smart Object), and on rendered RGB pixels inside Photoshop (the Noise filters and the blur family). [Part 3](16D3_Denoising_Mindset_RAW_vs_JPEG.md) argued the general principle; here is the concrete operational consequence.

**FACT:** Raw sensor data is captured in a near-linear encoding. When Camera Raw renders the image for display and output, it applies a tone curve that stretches the compressed shadow region across a much larger portion of the output range. That stretch amplifies whatever noise is in the shadows by the same factor it amplifies the shadow signal.

**INTERPRETATION:** Therefore noise reduction applied *before* the tone curve (in Camera Raw, on the raw data) fights the noise while it is still compact and un-amplified. Noise reduction applied *after* the tone curve (in Photoshop, on rendered pixels) fights an enemy the tone curve has already enlarged. The same nominal amount of smoothing does more good, with less collateral detail loss, when applied to raw data. This is not a preference; it is a direct consequence of where each tool sits relative to the tone curve.

This dictates the canonical order:

| Priority | Stage | Tool | Role |
|---|---|---|---|
| 1 | RAW development | Camera Raw Detail panel, or AI Denoise (Enhance) | Primary noise reduction on raw data, before the tone curve amplifies shadows |
| 2 | RAW development | Camera Raw Sharpening (same panel) | Capture sharpening, tuned against whatever noise remains, using Masking to protect flats |
| 3 | Photoshop | Camera Raw Filter / Reduce Noise / blur family, on masked Smart-Object layers | Targeted, local cleanup of regions the global raw pass could not perfectly solve |

The Photoshop stage is not a competitor to the Camera Raw stage; it is a specialist that handles what the global raw pass cannot: a single noisy channel, a localized patch, a rendered file that never had a raw origin, or a composite element that arrived as pixels.

### Why the Camera Raw Sliders Come in Luminance and Color Groups

[Part 1](16D1_Physics_of_Digital_Noise.md) and Chapter 16 established that digital noise decomposes into a luminance component (monochromatic brightness scatter, grain-like, relatively tolerable) and a chrominance component (random colored speckles, no film analog, always objectionable). Camera Raw mirrors this decomposition exactly: three sliders for luminance noise, three for color noise. The design is not arbitrary — the two noise types have different visual impact, different spatial character, and different tolerance for aggressive treatment, so they get independent controls.

**FACT:** Adobe applies a nonzero default Color value (25) to raw files and a zero default Luminance value. **INTERPRETATION:** this encodes the professional consensus that some color-noise suppression is always appropriate (chrominance noise is universally objectionable and almost never carries real detail), whereas luminance smoothing should be a deliberate decision made per image (it directly costs fine detail). You are meant to leave color noise reduction working quietly in the background and reach for the luminance sliders consciously.

### Chrominance Noise Is Cheap to Remove; Luminance Noise Is Expensive

This asymmetry underlies every setting decision in the Detail panel. Real color information in a photograph changes gradually — a blue sky, a red wall, and a skin tone all vary smoothly over many pixels. Chrominance noise, by contrast, changes randomly from one pixel to the next. That difference in spatial frequency lets a color-noise filter remove the high-frequency color scatter while leaving the low-frequency real color almost untouched. This is why aggressive color noise reduction rarely costs visible detail.

Luminance detail is different. Genuine fine detail — a pore, a hair, a leaf vein, a fabric thread — *is* high-frequency luminance variation, statistically indistinguishable at the pixel level from luminance noise. A luminance-noise filter that removes the noise cannot avoid removing detail of the same scale. This is the fundamental trade-off of Chapter 16, and it is why the luminance sliders must be handled with restraint while the color sliders can be handled with confidence.

### Sharpening and Noise Are the Same Operation Run in Opposite Directions

Chapter 15 and Chapter 16 both make the point; it bears one more framing here because it governs the Sharpening group. Sharpening amplifies high-frequency local contrast. Noise *is* high-frequency local contrast. A sharpening kernel that does not know the difference will amplify noise as enthusiastically as it amplifies edges. The Sharpening group's Detail and Masking sliders exist precisely to inject that missing knowledge: Detail controls how much fine (high-frequency) structure gets sharpened, and Masking controls *where* sharpening is allowed to act. Masking is the more powerful of the two for noise control, because it can remove the flat, noisy regions from the sharpening operation entirely.

### How AI Denoise Differs Structurally, Not Just in Degree

A parametric denoiser and an AI denoiser are not two settings of the same dial. The parametric tool runs a fixed formula over already-demosaiced (or about-to-be-demosaiced) pixels. The AI tool replaces the demosaic-and-denoise step itself with a learned model that maps the raw mosaic directly to a clean, full-color result, using its trained prior about the appearance of real images to decide what is detail and what is noise. **FACT:** the output is a new DNG. **INTERPRETATION:** because the model brings outside knowledge (the learned prior) to the decision, it can hold the detail/noise boundary in places where a formula cannot — but for the same reason it can also *fabricate* structure where the real signal has vanished, since a prior about "what images look like" will happily fill a void with plausible-looking texture. The parametric tool can only ever smooth what is there; the AI tool can also invent what is not. That is the entire trade, and it is a trade of kind, not degree.

---

## Photoshop Implementation

### Part A — Camera Raw Noise Reduction, Slider by Slider

Access: open a raw file to enter Camera Raw, or apply **Filter > Camera Raw Filter** to a Smart Object in Photoshop. Go to the **Detail** panel. **Always evaluate at 100% zoom or higher** — noise is a pixel-level phenomenon and any lower zoom level lets display interpolation lie to you about how much noise there is and how much detail you are losing. Position the view on a smooth shadow area to judge noise suppression, then jump to a detailed area to judge detail cost. You will move between these two views constantly.

The six sliders, each explained by what it does technically, its side effect, and the image characteristic that tells you where to set it:

#### Luminance

**What it does:** Sets the overall strength of luminance (brightness) noise reduction, blending each pixel toward its neighbors to suppress monochromatic brightness scatter. Range 0-100, default 0.

**Side effect:** Directly trades fine luminance detail for smoothness. Pushed too far it produces the waxy, "watercolor," or "wax-figure" look — plasticky skin, painted-looking skies, textures that dissolve.

**What tells you where to set it:** The amount of *grain-like brightness scatter visible in the smooth mid-to-dark areas* at 100%, weighed against the *fineness of the real texture the image needs to keep*. A portrait where skin should be smooth tolerates a high setting; a landscape or macro full of fine texture demands restraint. **RECOMMENDATION:** raise it only until the scatter in smooth areas stops being distracting at your intended output size — not until it reaches zero. Then immediately check a detailed area and back off if the texture has gone plastic. Hold Alt/Option while dragging to see a grayscale preview that removes color from the judgment and lets you assess the smoothing on brightness alone.

#### Luminance Detail

**What it does:** Sets the threshold that distinguishes "this variation is detail, protect it" from "this variation is noise, smooth it." Range 0-100, default 50. Higher values preserve more fine structure (and retain more noise); lower values smooth more aggressively (and erase more fine structure).

**Side effect:** Set too high, it lets noise survive as though it were detail — the Luminance slider appears to stop working. Set too low, it strips fine texture even at modest Luminance values.

**What tells you where to set it:** Whether, after you have set Luminance, the *detailed areas have lost texture that matters* (raise Detail to recover it) or the *smooth areas still show grit the Luminance slider should have removed* (lower Detail to let it through). It is the fine-tune that decides where on the detail/smoothness continuum the Luminance strength lands. **INTERPRETATION:** think of Luminance as "how hard to push" and Luminance Detail as "how carefully to distinguish detail from noise while pushing."

#### Luminance Contrast

**What it does:** Controls how much local tonal contrast within the noise/texture pattern is preserved. Range 0-100, default 0. Higher values retain contrast variations (and some noise), giving a more textured, three-dimensional result; lower values flatten those variations for a smoother, more uniform look.

**Side effect:** Raised too far it can produce a mottled, blotchy result — clumps of retained contrast that read as low-frequency lumpiness rather than clean texture.

**What tells you where to set it:** Whether the noise-reduced result looks *flat and lifeless* (raise Contrast to restore some local texture and depth) or is *acceptable as-is* (leave at the 0 default, which is correct for most images). This is the least-used of the three luminance sliders; reach for it only when heavy Luminance reduction has flattened the image and you want some structure back without reintroducing raw grit. Watch for mottling as you raise it.

#### Color

**What it does:** Sets the strength of chrominance (color) noise reduction, removing the random colored speckles. Range 0-100, default 25.

**Side effect:** Pushed very high it can cause fine color detail to bleed or desaturate — small colored features (a distant flower, colored fabric threads) can lose their color distinctness. In practice this threshold is high, because real color varies slowly and noise varies fast.

**What tells you where to set it:** The *density and vividness of colored speckles in smooth dark areas* at 100%. Raise it until they disappear. **RECOMMENDATION:** treat this slider with confidence, not caution — the color scatter it removes is almost never real information. For most high-ISO files, 25-50 is enough; extreme cases go higher. This is the slider you set first, before touching luminance, because clearing the color speckles makes the remaining luminance noise easier to judge.

#### Color Detail

**What it does:** Controls how aggressively color *edges* are preserved versus smoothed during color noise reduction. Range 0-100, default 50. Higher values preserve fine color edges (and retain some color noise near them); lower values smooth more, but can let color bleed across edges.

**Side effect:** Too low, and color bleeds across boundaries (a colored edge smears into its neighbor, or fine colored features lose their edges). Too high, and colored noise survives along edges.

**What tells you where to set it:** Whether, after setting Color, *fine colored edges have smeared or bled* (raise Color Detail to sharpen the color boundary) or *colored fringing/noise persists along edges* (lower it). The relevant image characteristic is the presence of small, saturated, edge-bounded color features — the more of them, the more you care about this slider. For images without such features, the 50 default is fine.

#### Color Smoothness

**What it does:** Controls the smoothness of large-scale color transitions after color noise reduction, suppressing low-frequency color mottling — the soft, blotchy color unevenness that can appear in large flat areas after color NR. Range 0-100, default 50. Higher values blend large color patches more smoothly.

**Side effect:** Very high values can flatten subtle real color gradation over large areas.

**What tells you where to set it:** Whether large smooth areas (a clear sky, a wall) show *soft color blotches or mottling* after color noise reduction (raise Smoothness to blend them out). If there is no mottling, leave the 50 default. **INTERPRETATION:** Color Detail addresses color noise at the *small/edge* scale; Color Smoothness addresses it at the *large/patch* scale. They are the two ends of the color-noise frequency spectrum.

**Camera Raw noise-reduction sliders at a glance:**

| Slider | Range | Default (RAW) | Governs | Characteristic side effect | Set it by watching |
|---|---|---|---|---|---|
| Luminance | 0-100 | 0 | Luminance NR strength | Waxiness / detail loss | Grit in smooth areas vs texture in detailed areas |
| Luminance Detail | 0-100 | 50 | Detail-vs-noise threshold | Retained noise (high) / stripped texture (low) | Detail cost after setting Luminance |
| Luminance Contrast | 0-100 | 0 | Local contrast retention | Mottling / blotchiness | Flatness of the smoothed result |
| Color | 0-100 | 25 | Chrominance NR strength | Color bleed / desaturation (only if very high) | Colored speckles in dark smooth areas |
| Color Detail | 0-100 | 50 | Color edge preservation | Color bleed (low) / edge noise (high) | Smearing of fine colored edges |
| Color Smoothness | 0-100 | 50 | Large-scale color blending | Flattened real color gradation (very high) | Soft color mottling in large flats |

**Recommended working order in the Detail panel:**

1. Zoom to 100%+, park on a smooth shadow area.
2. Set **Color** to clear the colored speckles (start near the 25 default, raise as needed).
3. If fine color edges smeared, adjust **Color Detail**; if large flats mottled, adjust **Color Smoothness**.
4. Raise **Luminance** gradually until the brightness scatter is no longer distracting. Hold Alt/Option to judge in grayscale.
5. Jump to a detailed area. If texture is gone, raise **Luminance Detail**; if grit remains in the flats, lower it.
6. Only if the result looks flat, nudge **Luminance Contrast** upward, watching for mottling.

**Starting points, framed honestly.** The table below is a starting point, not a universal recipe — the actual right values depend on sensor size, sensor generation, exposure accuracy, and the specific tonal region, not on ISO alone. A well-exposed modern full-frame ISO 6400 file may need less than an underexposed older APS-C ISO 1600 file. Evaluate every image individually at 100%.

| ISO range (a starting point only) | Luminance | Luminance Detail | Color | Note |
|---|---|---|---|---|
| 100-400 | 0-10 | 50 | 25 | Often no luminance NR at all |
| 800-1600 | 15-35 | 50 | 25-35 | Check shadows specifically |
| 3200-6400 | 35-60 | 40-50 | 35-50 | Accept some shadow detail loss; consider AI Denoise |
| 12800+ | consider AI Denoise first | — | 50-75 | Parametric luminance NR this high is usually inferior to AI Denoise |

**Global vs local in Camera Raw.** Everything above is a global adjustment — it affects the whole frame. Camera Raw also supports **local noise reduction** through its masking tools (Brush, Linear/Radial Gradient, and the AI-assisted Select Subject / Select Sky), each of which exposes a local Noise (and, in current versions, local color-noise) control. Use these to push extra noise reduction into a masked region — the crushed shadows, the sky — without touching the rest. The mask-building mechanics live in [Part 5](16D5_Frequency_Masking_and_Sharpening.md); the point here is simply that *local noise reduction exists inside Camera Raw itself* and is preferable to a heavy global setting whenever the noise is concentrated. **VERSION NOTE:** the exact set of local noise controls has expanded across ACR versions; verify what your version exposes.

### Part A (continued) — The Sharpening Group and Its Interaction with Noise

The Sharpening group lives in the same Detail panel, directly above noise reduction, and the two are designed to work together in a single pass on the raw data. Four sliders:

#### Amount

**What it does:** Strength of sharpening — how much local edge contrast is added. Range 0-150, default 40.

**Interaction with noise:** Amount amplifies *everything* the other three sliders let through, including noise. Raising Amount without controlling Detail and Masking will make a noisy image dramatically noisier. Amount is the accelerator; Detail and Masking are the steering.

#### Radius

**What it does:** The size, in pixels, of the sharpening halo around each edge. Range 0.5-3.0, default 1.0. Small radius for fine detail; larger radius for coarse subjects.

**Interaction with noise:** Small radii (near the default 1.0 or below) target fine detail — which is also the scale of noise, so small-radius sharpening on a noisy file is prone to crunching up noise grains into visible texture. There is no radius that is safe on a noisy image without Masking; radius only changes the scale of the problem.

#### Detail

**What it does:** Controls how much fine, high-frequency detail is sharpened versus only prominent edges. Range 0-100, default 25. Higher values sharpen fine textures (and noise); lower values restrict sharpening to strong edges.

**Interaction with noise:** This is a direct noise control. **RECOMMENDATION:** on noisy files, keep Detail low (below the default, sometimes near 0-15). Low Detail tells the sharpener to enhance only well-defined edges and ignore the fine, noise-scale variation. High Detail is for clean, low-ISO files where you want to bring out genuine fine texture.

#### Masking

**What it does:** Builds an edge mask and confines sharpening to it. Range 0-100, default 0. At 0, sharpening is applied uniformly to every pixel. As you raise it, flat areas are progressively excluded (protected) and only the strongest edges remain sharpened.

**Interaction with noise:** This is *the* key control for not sharpening noise. Noise lives in the flat areas — skies, skin, shadows — and Masking is exactly the tool that removes flat areas from the sharpening operation. Raising Masking lets you keep a healthy Amount on the genuine edges while completely protecting the noisy flats.

**The Alt/Option preview — use it every time.** Hold Alt/Option while dragging Masking and Camera Raw shows the mask itself in grayscale: **white = will be sharpened, black = protected.** At Masking 0 the whole preview is white. As you drag right, the flat areas turn black and only edges stay white. **RECOMMENDATION:** drag until the smooth, noisy regions (sky, skin, shadow) have gone solidly black and only the edges you want crisp remain white. This converts an abstract number into a direct visual decision and is the most reliable way to sharpen a noisy image. The same Alt/Option preview works on Amount, Radius, and Detail (showing a grayscale preview of the sharpening effect itself) — use it to see what each slider is doing without the distraction of color.

**Sharpening group, noise-oriented summary:**

| Slider | Range | Default | Noise-relevant role | On a noisy file |
|---|---|---|---|---|
| Amount | 0-150 | 40 | Amplifies everything let through | Keep moderate; it multiplies any noise the others pass |
| Radius | 0.5-3.0 | 1.0 | Halo size / detail scale | No safe value without Masking |
| Detail | 0-100 | 25 | Fine-detail vs edges-only | Keep low to ignore noise-scale variation |
| Masking | 0-100 | 0 | Confines sharpening to edges | Raise (with Alt/Option) until flats go black |

**FACT:** Because Camera Raw applies noise reduction and sharpening simultaneously in a single operation on the raw data, this is the correct place to resolve the noise-sharpening tension — the tools were built to be balanced against each other here, before the image is ever rendered to pixels. This is developed further in [Part 5](16D5_Frequency_Masking_and_Sharpening.md).

### Part B — Adobe AI Denoise

**VERSION NOTE:** AI Denoise was introduced to Adobe Camera Raw and Lightroom in 2023 and continues to evolve. The interface, performance, and exact behavior may differ in your version; verify current specifics against Adobe's documentation.

**What it is, in verifiable terms. FACT:** AI Denoise is a machine-learning feature, accessed as an **Enhance** operation, that performs joint demosaicing and denoising on raw data and writes the result as a new, enhanced **DNG** file. It presents an **Amount** slider and a preview, and it is computationally heavy (it runs a neural network over the whole image, benefiting greatly from GPU acceleration). **UNCERTAINTY:** Adobe has not published the model's architecture, training data, or per-camera handling in detail; any description of its internal mechanism beyond "a learned model that jointly demosaics and denoises a raw mosaic and outputs a DNG" is inference, and specific internal-algorithm claims should be treated as unverified.

#### How modern ML raw denoising works, in principle

A learned raw denoiser is trained on large collections of image pairs — a noisy capture and its clean counterpart (real or synthetically generated). Through training it acquires a *learned prior*: an internalized model of what real photographic structure looks like. At inference time it takes the noisy raw mosaic and produces a clean, demosaiced estimate, using that prior to decide, at each location, whether a given variation is detail to preserve or noise to remove. Because the prior encodes the appearance of real eyelashes, foliage, weave, and grain of stone, the model can hold the detail/noise boundary in places where a fixed formula cannot. The output is a *reconstruction* — a best estimate — not a measurement, and its trustworthiness scales with how much real signal survived for the model to anchor to.

#### AI Denoise versus classical parametric noise reduction

| Dimension | Parametric (Luminance/Color sliders) | AI Denoise |
|---|---|---|
| Decision rule | Fixed, hand-designed formula on local statistics | Learned prior from training data |
| Where it acts | On/around demosaiced pixels | Replaces the demosaic step (joint demosaic+denoise) |
| Output | Slider values in metadata, rendered on the fly | A new physical DNG file |
| Speed | Instant, interactive | Slow, one-shot (heavy compute) |
| Detail retention at a given noise level | Lower | Higher |
| Predictability | Fully deterministic, never invents | Can fabricate plausible detail where signal is gone |
| Best for | Moderate noise, fast iteration, selective work | Severe noise where sliders cannot balance detail vs smoothness |

#### When AI Denoise is worth using

**RECOMMENDATION:** reach for AI Denoise when the parametric Luminance slider cannot find an acceptable balance — that is, when the file is noisy enough that the Luminance value needed to clean the smooth areas destroys detail you need to keep. This is the extreme-ISO, severely-underexposed, or old-noisy-sensor regime described in [Part 2](16D2_Old_Sensors_and_Visual_Diagnosis.md) and [Part 3](16D3_Denoising_Mindset_RAW_vs_JPEG.md). For those files, AI Denoise routinely rescues images that would otherwise be unusable.

#### When it is unnecessary or too aggressive

For moderately noisy files where Luminance at, say, 20-40 already gives a clean, detailed result, AI Denoise is overkill: it costs time and disk space and its improvement is marginal. And at high Amount values, or on files where the real signal has genuinely collapsed, it becomes *too aggressive* — the learned prior fills the void with invented structure. Evaluate the preview at 100% and look specifically for the following subject-dependent behaviors:

| Subject | Characteristic AI Denoise effect | What to watch for |
|---|---|---|
| Skin | Cleans beautifully, often better than any slider | Over-smoothing into plastic; loss of pore texture at high Amount |
| Foliage | Usually excellent — the model knows leaf structure | Invented leaf edges / regularized texture where real detail was noise |
| Sky | Cleans gradients superbly, removes color mottle | Rare banding or an unnaturally perfect gradient |
| Textures (fabric, stone) | Strong detail retention | "Regularized" or repeating-looking texture at extreme settings |
| Distant fine detail | The riskiest case | Hallucinated micro-structure — text, tree branches, or fine edges that look plausible but are invented, because real signal there was below the noise floor |

**INTERPRETATION:** the distant-fine-detail row is the one to internalize. That is precisely where the model has the least real signal to anchor to, so it is where reconstruction shades into invention. On images where distant fine detail must be *truthful* (documentary, forensic, evidentiary, or anything where a viewer will scrutinize small structures for real information), treat AI-reconstructed distant detail with suspicion and, if in doubt, prefer a more conservative parametric result. This connects directly to the hallucinated-detail discussion in [Part 6](16D6_AI_Upscaling_and_Hallucinated_Detail.md).

#### Its relationship to Super Resolution

**FACT:** AI Denoise and Super Resolution are both **Enhance** operations, both driven by machine-learning models, and both output DNG files. Super Resolution increases resolution (roughly doubling linear dimensions); Denoise removes noise. They are siblings in the same menu, share the "produces a new DNG" behavior, and share the risk profile of any generative reconstruction (they can invent plausible detail). **RECOMMENDATION:** if you intend to both denoise and upscale, denoise first and upscale second, so the upscaler works from a clean input rather than amplifying noise — but see [Part 6](16D6_AI_Upscaling_and_Hallucinated_Detail.md) for the full upscaling treatment, which is out of scope here. Do not stack redundant Enhance passes hoping for a cleaner result; each pass bakes another DNG and compounds any invention.

#### Fitting AI Denoise into a modern RAW-to-DNG workflow

1. Do minimal but real global development first — set exposure and white balance — so the model evaluates the signal correctly. (Detailed development can follow on the DNG.)
2. Run **Enhance > Denoise**. Set **Amount**, judging the preview at 100% across smooth areas (noise gone?), detailed areas (detail kept?), and distant fine detail (anything invented?).
3. Let it write the enhanced **DNG**.
4. Develop the DNG normally — all the usual Basic, Curve, and Detail-panel controls apply. Because the DNG is now clean, you can sharpen more confidently: revisit the Sharpening group and raise Amount / lower Masking than you would have dared on the noisy original, because there is little noise left to amplify.
5. Do *not* also apply heavy parametric Luminance noise reduction on top — that is a redundant second pass that only costs detail. A small residual Color value is harmless; a large Luminance value on an already-denoised file is the classic double-reduction mistake from Chapter 16.

**Global vs local with AI Denoise. FACT:** AI Denoise processes the entire frame; it is not itself a local tool. If only part of the image needed it, the way to localize is to take both the AI-denoised DNG and a version without it into Photoshop as layers and mask between them — treating "AI Denoise on / off" as two source images blended by a mask. That mask-building is [Part 5](16D5_Frequency_Masking_and_Sharpening.md)'s subject.

### Part C — Photoshop Pixel-Domain Noise Tools

Everything in Part C operates on rendered RGB pixels, after the tone curve. Per the Theory section, this makes these tools the *specialist follow-up*, not the primary noise reducer for raw files. They earn their place in four situations: the file never had a raw origin (a scan, a JPEG, a rendered composite element); the noise is confined to one channel; the treatment must be surgically local to a patch; or you need a filter behavior (Median, Dust & Scratches) that Camera Raw does not offer.

**Make every one of them non-destructive and local by default. RECOMMENDATION:** before applying any Photoshop noise filter, convert the target layer to a **Smart Object** (right-click layer > Convert to Smart Object). The filter then applies as a **Smart Filter** — re-editable, with its own blend options, and, crucially, with an automatic **filter mask** you can paint on to confine the effect to exactly the region that needs it. This single habit turns every global filter below into a local tool and makes all of them re-tunable. See [Chapter 06](06_Non_Destructive_Workflow.md) for the Smart Object/Smart Filter architecture and [Part 5](16D5_Frequency_Masking_and_Sharpening.md) for painting the masks.

#### Filter > Noise > Reduce Noise

**What it suits:** general luminance + chrominance noise on rendered files, and — its unique value — *per-channel* noise reduction when one channel is far noisier than the others.

**Basic mode controls:**

| Control | Range | Function | Noise note |
|---|---|---|---|
| Strength | 0-10 | Overall luminance NR intensity | The main smoothing dial; start moderate (4-6) |
| Preserve Details | 0-100% | Detail retention vs smoothing | Higher keeps detail and noise; start ~50% |
| Reduce Color Noise | 0-100% | Chrominance NR | Push confidently (50-75%) — color scatter is not detail |
| Sharpen Details | 0-100% | Compensatory sharpening during NR | Leave at 0; sharpening during NR reintroduces the texture you are removing |

**Advanced mode — per-channel.** Switch to Advanced and select the **Per Channel** tab. You can then apply independent Strength and Preserve Details to Red, Green, and Blue. **FACT:** in a Bayer sensor the blue channel typically has the worst SNR (fewer blue photons, half as many green-versus-blue samples), so blue is frequently the noisiest channel — see [Part 1](16D1_Physics_of_Digital_Noise.md). Per-channel mode lets you smooth blue hard while barely touching the clean green channel, which carries most of the luminance detail. This is the single reason Reduce Noise still matters after Camera Raw exists: **Camera Raw does not expose per-channel noise reduction; Reduce Noise does.**

**Typical per-channel strategy:**

| Channel | Usual noise level | Approach |
|---|---|---|
| Green | Lowest (most samples, best SNR) | Minimal Strength, high Preserve Details |
| Red | Moderate | Moderate Strength |
| Blue | Highest (fewest photons) | Highest Strength, accept smoothing — detail loss here costs little because green carries the luminance detail |

**Global vs local:** apply as a Smart Filter and paint the filter mask to confine it to the noisy region. Per-channel treatment applied globally still over-smooths blue in the well-exposed areas; masking fixes that.

#### Gaussian Blur (used locally, via masks/blend) — for color noise

**What it suits:** *not* general noise reduction — a raw Gaussian blur destroys detail indiscriminately. Its legitimate noise use is a targeted attack on **chrominance noise**, exploiting the fact that color varies slowly and color noise varies fast. Blur can be applied so that it affects only color, not detail, in either of two ways:

- **On a single channel:** in the Channels panel, select the noisy channel (usually Blue), apply **Filter > Blur > Gaussian Blur** at a small radius (roughly 0.5-1.5 px). This smooths the color scatter carried by that channel while the luminance detail in the other channels is untouched. Return to the RGB composite to judge. **FACT:** this is crude compared to Camera Raw color NR but demonstrates the mechanism directly — chrominance noise originates in individual channels, and the green channel carries most luminance detail, so smoothing blue barely costs sharpness.
- **On a Color-blend layer:** duplicate the layer, Gaussian-blur it, and set its blend mode to **Color**. Now only the hue/saturation of the blurred layer is used, over the luminance of the layers beneath — so the blur smooths color while luminance detail is preserved entirely. Mask it to the areas with color noise.

**Global vs local:** always local. Global Gaussian blur is not a noise tool; it is a detail-destruction tool. Confined to a channel or to a Color-blend layer, and masked, it becomes a precise chrominance-noise instrument.

#### Median — for salt-and-pepper noise, hot pixels, and speckle

**What it suits:** **Filter > Noise > Median** replaces each pixel with the median value of its neighborhood at a chosen Radius. Because the median ignores outliers, it excels at removing *isolated extreme pixels* — hot/stuck pixels, dust speckle, salt-and-pepper impulse noise — while a mean-based blur would smear them. It preserves edges better than Gaussian blur at equal radius, but at larger radii it produces a characteristic "posterized" or "plastic-blob" look as fine detail collapses into flat patches.

**What tells you where to set it:** the size of the speckle you are killing. Radius 1 removes single-pixel outliers with minimal detail cost; larger radii are for coarser speckle but rapidly flatten real texture. **RECOMMENDATION:** keep radius as low as the defect allows, and apply locally through a masked Smart Filter — Median is superb on a few hot pixels in a dark frame and destructive across a detailed image.

**Global vs local:** overwhelmingly local. Median's ideal use is a small radius confined to a small defect (a cluster of hot pixels, a patch of impulse noise), not a whole-frame treatment.

#### Dust & Scratches — for discrete blemishes, not sensor noise

**What it suits:** **Filter > Noise > Dust & Scratches** has a Radius and a **Threshold**. It finds pixels that differ from their neighbors by more than the Threshold and blends them in over the Radius. It is designed for *discrete defects* — dust spots on a scan, scratches, sensor-dust blobs — not for the fine, pervasive grain of high-ISO capture. The Threshold is what makes it usable: it protects everything that differs from its neighbors by *less* than the Threshold (i.e. genuine fine detail and low-amplitude noise), and only attacks the strong outliers.

**What tells you where to set it:** set Radius just large enough to cover the largest blemish, then raise Threshold until real detail stops being affected while the blemishes are still caught. **RECOMMENDATION:** this filter is a scan-restoration and dust tool, not a high-ISO denoiser; for pervasive grain it will either miss it (high Threshold) or destroy detail (low Threshold). Use it locally on the blemished region.

**Global vs local:** local, on the blemished area. Globally it is a blunt detail-destroyer.

#### Surface Blur — edge-preserving smoothing for flat areas

**What it suits:** **Filter > Blur > Surface Blur** is a threshold-based edge-preserving blur: it smooths pixels that are within a **Threshold** tonal distance of the center pixel and leaves pixels beyond the Threshold (edges) untouched. This is a bilateral-style filter, and it is genuinely useful for reducing luminance noise in *flat areas while keeping edges crisp* — a smoother sky or wall with the horizon and objects still sharp.

**What tells you where to set it:** **Radius** controls how large an area is averaged (bigger = smoother flats, more risk to fine texture); **Threshold** controls what counts as an edge to protect (bigger threshold protects fewer edges and smooths more; smaller protects more and smooths less). The image characteristic that guides you is the *tonal gap between your noise amplitude and your real edges*: set Threshold above the noise amplitude but below your real edge contrast, so noise gets averaged and edges survive.

**Side effect:** at high Radius/Threshold it produces a plasticky, poster-like look and can create hard "steps" where the threshold cuts, giving a cel-shaded appearance.

**Global vs local:** good globally for uniformly noisy flat-heavy images, but better masked to the flats. Compared with Reduce Noise, Surface Blur gives you more direct control over the edge threshold and is often more effective for cleaning skies and skin while holding edges.

#### Smart Blur — threshold smoothing with a radius/quality model

**What it suits:** **Filter > Blur > Smart Blur** is another threshold-based edge-preserving smoother, with Radius, Threshold, Quality, and a Mode control (Normal, plus edge-only modes that output the detected edges). Like Surface Blur, it smooths within-threshold variation and protects across-threshold edges, and it suits luminance-noise cleanup of flat areas. Its distinctive tendency is a slightly more "processed," sometimes waxy result and, at aggressive settings, visible flat plateaus.

**What tells you where to set it:** same logic as Surface Blur — Threshold above noise amplitude, below edge contrast; Radius as small as gives acceptable smoothing. **INTERPRETATION:** Surface Blur and Smart Blur overlap heavily; in practice most photographers pick whichever gives the cleaner look on the specific image. Surface Blur tends to be the more predictable of the two for photographic noise. **UNCERTAINTY:** the precise internal difference between the two filters' edge-detection is not documented in a way that supports a firmer recommendation than "test both."

**Global vs local:** local, masked to flats, for the same reason as Surface Blur.

#### Camera Raw Filter as a Smart Filter — parametric NR inside Photoshop

**What it suits:** applying the *full Camera Raw Detail panel* (all six noise sliders, the Sharpening group with its Masking preview, and local Camera Raw masks) to a rendered layer inside Photoshop. **Filter > Camera Raw Filter** on a Smart Object gives you the best parametric noise reduction Adobe offers, re-editable as a Smart Filter, with a paintable filter mask for local application.

**When to use it over Reduce Noise:** almost always, when you want general (non-per-channel) parametric NR in Photoshop. The Camera Raw Filter's noise sliders are more sophisticated than Reduce Noise's, and you get the Masking-slider Alt/Option workflow for sharpening. Reduce Noise wins only for its per-channel mode.

**Important limitation. INTERPRETATION:** the Camera Raw Filter operates on the rendered, gamma-encoded pixels of the layer, not on raw data — so it does *not* recover the raw-stage advantage described in the Theory section. If the file originated as raw, you should have done primary NR during raw development; the Camera Raw Filter in Photoshop is for follow-up, for rendered-origin files, and for local masked treatment. **FACT:** AI Denoise (the Enhance operation) is *not* available through the Camera Raw Filter on rendered pixels — it is a raw-only Enhance operation. Do not expect the in-Photoshop Camera Raw Filter to give you AI Denoise.

**Global vs local:** as a Smart Filter it is trivially local — paint the filter mask. This is the most common professional way to do selective, re-editable noise reduction inside Photoshop: one Camera Raw Filter Smart Filter, masked to the noisy region.

#### Photoshop noise tools vs problem type

| Tool | Best-fit problem | Mechanism | Global or local | Characteristic failure |
|---|---|---|---|---|
| Reduce Noise (Basic) | General lum + color noise on rendered files | Edge-aware smoothing + color NR | Local via Smart Filter mask | Waxiness; weak vs severe noise |
| Reduce Noise (Per Channel) | One channel far noisier (usually blue) | Independent NR per channel | Local via mask | Over-smoothed blue in clean areas if global |
| Gaussian Blur on a channel / Color-blend | Chrominance noise specifically | Blur color, keep luminance | Always local | Color bleed if radius too large |
| Median | Salt-and-pepper, hot/stuck pixels, speckle | Neighborhood median (rejects outliers) | Local, small radius | Posterized blobs at large radius |
| Dust & Scratches | Discrete dust/scratches on scans | Threshold-gated blend of outliers | Local | Misses grain or destroys detail |
| Surface Blur | Luminance noise in flat areas, keep edges | Threshold bilateral-style blur | Local (or global on flat-heavy images) | Plastic/cel-shaded look, threshold steps |
| Smart Blur | Luminance noise in flat areas, keep edges | Threshold edge-preserving blur | Local | Waxy plateaus |
| Camera Raw Filter (Smart Filter) | General parametric NR + sharpening in PS | Full Detail panel on rendered pixels | Local via filter mask | Rendered-stage, so less effective than raw-stage NR |

---

## Professional Workflow

### The default decision path

For any given file, walk the three axes from the Conceptual Foundation:

1. **Is it raw?** If yes, do primary noise reduction in Camera Raw (parametric sliders, or AI Denoise for severe noise), then capture-sharpen with Masking in the same pass. If no (JPEG, TIFF, scan, composite element), you are in Photoshop's pixel domain from the start — expect less headroom (see [Part 3](16D3_Denoising_Mindset_RAW_vs_JPEG.md)).
2. **How severe is the noise?** Moderate → parametric sliders. Severe (extreme ISO, deep recovery, old noisy sensor) → AI Denoise, then develop the resulting DNG.
3. **Is the noise localized?** Almost always yes. Prefer local application — Camera Raw local masks, or Photoshop Smart Filters with painted filter masks — over a heavy global setting. Mask mechanics: [Part 5](16D5_Frequency_Masking_and_Sharpening.md).

### A canonical modern RAW-to-DNG pipeline

| Step | Operation | Tool | Why here |
|---|---|---|---|
| 1 | Minimal exposure + WB | Camera Raw Basic panel | Lets AI Denoise judge signal correctly |
| 2 | Primary NR | AI Denoise (severe) or Detail-panel sliders (moderate) | Raw-stage NR, before the tone curve amplifies shadows |
| 3 | (if Denoise) develop the DNG | Camera Raw on the enhanced DNG | Clean base for the rest of development |
| 4 | Capture sharpening | Detail-panel Sharpening, Masking via Alt/Option | Balanced against residual noise, in the same raw pass; more aggressive if AI-denoised |
| 5 | Tonal / color editing | Photoshop adjustment layers | After a clean, sharp base exists |
| 6 | Local cleanup | Camera Raw Filter or Reduce Noise (per-channel) on masked Smart-Object layer | Handles the one channel / one patch the global pass could not |
| 7 | Creative + output sharpening | Chapter 15 tools on masked layers / output copy | Last, on the cleanest possible image |

This is the same skeleton as Chapter 16's pipeline, refined with the AI-Denoise-first branch and the explicit per-channel/local follow-up. [Part 7](16D7_Workflows_and_E520_Project.md) works a complete project end to end on a real file; [Part 8](16D8_Quality_Control_and_Cheat_Sheet.md) provides the QC checklist and cheat sheet.

### Choosing Camera Raw vs Photoshop for the noise step

| Situation | Do it in | Reason |
|---|---|---|
| RAW file, primary NR | Camera Raw (sliders or AI Denoise) | Raw-stage, pre-tone-curve, most effective |
| One channel far noisier than others | Photoshop Reduce Noise, per-channel | Camera Raw has no per-channel NR |
| Localized noisy patch on a composite | Photoshop, masked Smart Filter | Camera Raw cannot mask an arbitrary composited element as flexibly |
| JPEG / TIFF / scan (no raw) | Photoshop (Camera Raw Filter or Reduce Noise) | No raw data to return to |
| Hot pixels / dust speckle | Photoshop Median or Dust & Scratches, local | Camera Raw sliders do not target discrete outliers |
| Severe high-ISO raw | Camera Raw AI Denoise | Parametric cannot balance detail vs smoothness at this level |

### Non-destructive discipline

**RECOMMENDATION:** every Photoshop noise operation should be a Smart Filter on a Smart Object, and every noise layer should carry a mask. This gives you three things at once: re-editability (change the settings later), locality (paint the mask), and a clean audit trail of what was done. It also enforces the single-pass discipline of Chapter 16 — because your one noise operation is visible and re-tunable, you are far less tempted to pile a second and third pass on top. See [Chapter 06](06_Non_Destructive_Workflow.md).

---

## Common Mistakes

**Doing primary noise reduction in Photoshop on a raw file.** If the file is raw, the Camera Raw stage is strictly more effective because it acts before the tone curve amplifies shadow noise. Reaching for Filter > Noise > Reduce Noise on a raw-origin image throws away that advantage. Reduce Noise is the specialist follow-up, not the first line.

**Treating the six Camera Raw sliders as one "noise" dial.** Luminance and Color are different problems with different tolerances. Color can be pushed confidently because color noise is not detail; Luminance must be handled with restraint because luminance detail *is* high-frequency variation. Collapsing them into "just raise noise reduction" produces either waxy images (Luminance too high) or surviving color speckle (Color too low).

**Sharpening a noisy image with Masking at 0.** At Masking 0, the sharpener amplifies noise across every flat area. The Masking slider — dragged with Alt/Option held until the flats go black — is the difference between crisp edges and a crunchy, noise-amplified mess. Skipping the Alt/Option preview is skipping the one control that makes sharpening safe on noisy files.

**Expecting Median or Dust & Scratches to remove high-ISO grain.** These are discrete-defect tools (hot pixels, dust, scratches, impulse noise). Point them at pervasive sensor grain and they either miss it or posterize the image into plastic blobs. Grain is a job for Camera Raw NR or AI Denoise.

**Using a raw Gaussian blur as a noise reducer.** Gaussian blur destroys detail indiscriminately. It has a legitimate, narrow noise use — on a single channel or a Color-blend layer to kill *chrominance* noise — but applied straight to the composite it is not noise reduction, it is detail removal.

**Stacking AI Denoise and heavy parametric Luminance NR.** AI Denoise already produced a clean DNG. Piling a high Luminance value on top is a redundant second pass that only costs detail — the double-reduction error from Chapter 16 in a modern guise. After AI Denoise, back *off* noise reduction and, if anything, sharpen more.

**Trusting AI-reconstructed distant fine detail as if it were real.** Where the real signal fell below the noise floor, AI Denoise's learned prior invents plausible structure. For documentary, forensic, or any truth-critical distant detail, that invention is a liability. Evaluate at 100% and prefer a conservative parametric result when truth matters more than smoothness. See [Part 6](16D6_AI_Upscaling_and_Hallucinated_Detail.md).

**Applying every tool globally.** Noise visibility varies across the frame; a global setting is calibrated to the worst region and over-treats every better one. Convert to a Smart Object, apply as a Smart Filter, and paint the filter mask. Local is the default, not the advanced option.

**Ignoring per-channel noise differences.** When the blue channel is far noisier than green, uniform NR either under-treats blue or over-smooths green. Reduce Noise's per-channel Advanced mode smooths blue hard while sparing the detail-carrying green channel — a result no uniform setting can match.

**Raising Sharpen Details inside Reduce Noise.** Sharpening during noise reduction reintroduces exactly the high-frequency texture you are trying to remove. Leave it at 0 and do sharpening as its own, separately controlled step.

---

## Summary

Every Adobe noise tool makes the same decision — how much to trust each pixel versus replace it with a neighborhood estimate — and they differ along three axes: **what data they see** (raw, pre-tone-curve, versus rendered pixels), **how they decide** (a fixed parametric formula versus a learned prior), and **where they apply** (global versus local). Hold those three axes and tool choice becomes a decision tree instead of a habit.

**Camera Raw's six noise sliders** split into a Luminance group (Luminance, Luminance Detail, Luminance Contrast) and a Color group (Color, Color Detail, Color Smoothness), mirroring the luminance/chrominance decomposition. Color noise is cheap to remove because real color varies slowly while color noise varies fast; luminance noise is expensive because fine luminance detail is statistically the same as luminance noise. Each slider is set not by "drag right" but by a specific image characteristic — colored speckle density for Color, residual grit versus lost texture for Luminance Detail, mottling for Color Smoothness, and so on. The **Sharpening group** (Amount, Radius, Detail, Masking) interacts directly with noise, and **Masking — dragged with Alt/Option held until the flat, noisy areas turn black — is the key control** that lets you sharpen genuine edges without amplifying noise.

**AI Denoise** is structurally different from the sliders, not merely stronger: it is an Enhance operation that uses a machine-learning model to jointly demosaic and denoise the raw mosaic and outputs a new DNG, applying a learned prior about the appearance of real images. That prior lets it hold the detail/noise boundary where a formula cannot — but it also lets it *fabricate* plausible detail where the real signal has vanished, which is exactly the risk with distant fine detail at extreme noise. It is worth using when the parametric Luminance slider cannot balance detail against smoothness, and unnecessary or over-aggressive otherwise. It is a sibling of **Super Resolution** — both Enhance operations producing DNGs — and both fit a RAW-to-DNG workflow in which you enhance first and develop the resulting DNG, without stacking redundant passes.

**Photoshop's pixel-domain tools** operate after the tone curve and are the specialist follow-up, not the primary reducer for raw files. Each suits a specific problem: **Reduce Noise** for general and, uniquely, per-channel noise; **Gaussian Blur on a channel or Color-blend layer** for chrominance noise specifically; **Median** for salt-and-pepper and hot pixels; **Dust & Scratches** for discrete blemishes on scans; **Surface Blur** and **Smart Blur** for luminance noise in flat areas while preserving edges; and the **Camera Raw Filter as a Smart Filter** for the full parametric Detail panel inside Photoshop, re-editable and maskable. Applied as Smart Filters on Smart Objects with painted masks, every one of them becomes local and non-destructive — which is how noise work should almost always be done, because noise visibility varies across the frame and a global setting is a compromise calibrated to the worst region.

---

## Exercises

### Exercise 1: Set Each Camera Raw Slider by Its Diagnostic Characteristic

Open a high-ISO raw file (ISO 3200+), or underexpose a low-ISO file by 3-4 stops in Camera Raw to simulate noise. Set all six noise sliders to 0 and zoom to 200% on a smooth shadow area.

1. Raise **Color** until the colored speckles vanish. Note the value. Was it near, above, or below the default 25?
2. Find an area with a small saturated colored feature at an edge (a distant sign, colored fabric). Lower **Color Detail** until you see color bleed across the edge, then raise it until the edge is crisp again. You have just located the color-edge threshold.
3. Find a large flat colored area (sky, wall). If it shows soft color blotches, raise **Color Smoothness** until they blend out.
4. Raise **Luminance** until the brightness grit stops being distracting. Hold Alt/Option to judge in grayscale. Note the value.
5. Jump to a detailed area. Raise **Luminance Detail** until lost texture returns; then lower it and watch grit reappear in the flats. Find the balance.
6. Only if the result now looks flat, nudge **Luminance Contrast** up until some depth returns, stopping before it mottles.

**Success criterion:** for each slider you can state the specific image characteristic you watched to set it, not merely the direction you dragged.

### Exercise 2: Masking as the Anti-Noise-Sharpening Control

Open the same noisy file, with noise reduction set as in Exercise 1.

1. In the Sharpening group set Amount to 80, Radius 1.0, Detail 25, Masking 0. Zoom to 100% on a flat area (sky or skin). Observe the noise being amplified.
2. Hold Alt/Option and drag **Masking** upward. Watch the flat areas turn black while edges stay white.
3. Release when the flat, noisy areas are solidly black and only the edges you want crisp remain white.
4. Now lower **Detail** toward 10-15 and observe the flats getting even calmer.
5. Compare the flat area at Masking 0 versus your final setting.

**Success criterion:** you can sharpen the genuine edges of a noisy image without amplifying the noise in the flats, and you used the Alt/Option mask preview to decide the Masking value visually.

### Exercise 3: Per-Channel Noise Reduction in Photoshop

Develop a high-ISO raw file with all noise reduction at 0 and open it into Photoshop (or open a high-ISO JPEG).

1. In the Channels panel, inspect Red, Green, Blue in grayscale. Confirm blue is noisiest, green cleanest.
2. Convert the layer to a Smart Object. Apply Filter > Noise > Reduce Noise in Advanced mode.
3. Per Channel: Blue Strength 8 / Preserve Details 40; Red Strength 4 / 60; Green Strength 2 / 80.
4. On a separate copy, apply Basic mode Strength 6 uniformly.
5. Compare at 100%. Which retained more overall detail?

**Success criterion:** you can identify the noisiest channel and treat it independently, preserving detail the uniform pass would have smoothed.

### Exercise 4: Right Tool for the Defect

Prepare (or find) three problem files: (a) a scan with visible dust spots; (b) a dark frame with a few bright hot/stuck pixels; (c) a high-ISO image with pervasive grain.

1. On (a), apply Dust & Scratches locally: set Radius to just cover the largest spot, then raise Threshold until real detail is spared but spots are caught.
2. On (b), apply Median at Radius 1 locally over the hot pixels only.
3. On (c), try Median and Dust & Scratches — observe that they either miss the grain or posterize it — then do it correctly with Camera Raw NR (or the Camera Raw Filter).

**Success criterion:** you can explain why Median and Dust & Scratches fix (a) and (b) but are wrong for (c), tracing the reason to their outlier/threshold mechanisms.

---

## Advanced Exercises

### Advanced Exercise 1: AI Denoise vs Parametric, Truth-Critical Distant Detail

Choose a high-ISO raw frame that contains **distant fine detail** (far-off signage, tree branches on a horizon, distant architecture).

1. Version A: develop with parametric NR only — set Luminance and Color to the best balance you can find.
2. Version B: run Enhance > Denoise at a moderate Amount, then develop the resulting DNG comparably.
3. Version C: run Enhance > Denoise at a high Amount.
4. At 100%, compare the distant fine detail across all three. Where is Version B better than A? Where in Version C do you suspect the model has *invented* structure that is not reliably real?
5. Write two sentences deciding which version you would deliver if the distant detail had to be *truthful* versus merely *attractive*.

**Success criterion:** you can articulate where AI reconstruction is trustworthy and where it shades into invention, and make a delivery decision on that basis. Cross-reference [Part 6](16D6_AI_Upscaling_and_Hallucinated_Detail.md).

### Advanced Exercise 2: Local Blend Between AI-Denoised and Original

Take a raw frame where only the shadows are severely noisy but the highlights carry fine texture you do not want the AI to touch.

1. Develop a normal version (no AI Denoise) and an AI-denoised DNG version.
2. Open both into one Photoshop document as two layers, aligned.
3. Put the AI-denoised layer on top and add a mask that reveals it only in the noisy shadows (build the mask per [Part 5](16D5_Frequency_Masking_and_Sharpening.md); a shadow luminosity mask is a good start).
4. The highlights now come from the untouched original; the shadows from the AI-denoised version.
5. Toggle the mask to compare with a global AI-Denoise-everywhere result.

**Success criterion:** you can localize a whole-frame AI operation by blending two source renderings through a mask, keeping AI reconstruction out of areas that did not need it.

### Advanced Exercise 3: Build the Complete Detail-Panel + Photoshop Follow-up

Process one severe high-ISO raw file end to end:

1. Camera Raw: minimal exposure/WB, then Enhance > Denoise. Develop the DNG. Sharpen with Amount raised and Masking set via Alt/Option (you can afford more now that it is clean).
2. Open into Photoshop as a Smart Object.
3. Inspect channels; if blue is still noisy in a specific region, add a masked Reduce Noise (per-channel) Smart Filter confined to that region.
4. If a flat area (sky) still shows faint mottle, add a masked Surface Blur Smart Filter to that area only.
5. Do creative and output sharpening last (Chapter 15).
6. Document, in a short note, which tool did what and why each was local rather than global.

**Success criterion:** you executed the full pipeline with each noise operation as a re-editable, masked Smart Filter, and can justify every tool choice against the three axes.

---

## Blackbelt Challenge

You are handed three files and must, **without opening Photoshop**, specify a complete tool-level noise strategy for each — naming the exact controls, the values as *starting points*, whether each step is global or local, and the failure mode you are guarding against. Then predict what each of two wrong approaches would do.

**File 1 — Concert, ISO 25600, modern full-frame, raw.** A singer under a hard spotlight (well-exposed face), a mid-lit band (moderate noise), and a near-black audience and rear stage (severe luminance *and* chrominance noise). The blue channel is visibly the worst. Deliverables: a 24-inch gallery print and a 1080 px social post.

1. Decide between parametric Detail-panel NR and AI Denoise for the primary pass, and justify it from the noise severity. If AI Denoise, describe the DNG workflow and how your sharpening changes on the clean DNG.
2. Specify the six Detail-panel slider intents (which you push confidently, which with restraint, and the image characteristic driving each) even if AI Denoise does the heavy lifting.
3. Describe the local strategy: how do you treat the near-black audience without waxing the well-lit face? Name the tool (Camera Raw local mask, or a masked Smart Filter in Photoshop) and the mask concept ([Part 5](16D5_Frequency_Masking_and_Sharpening.md)).
4. The blue channel is worst — where and how do you use per-channel Reduce Noise, and why can Camera Raw not do this step?
5. Set the Sharpening group for the clean result: Amount, Radius, Detail, and specifically how you would use the **Masking** Alt/Option preview on the singer's face versus the smooth background. How and why do the two output sharpenings differ?

**File 2 — Archival scan of a grainy color negative.** No raw data exists. The scan shows film grain, several dust specks, and a couple of scratches, plus some scanner chrominance noise in the shadows. Deliverable: a 12-inch archival print.

1. Because there is no raw file, where does *all* noise work necessarily happen, and what does that cost you (reference [Part 3](16D3_Denoising_Mindset_RAW_vs_JPEG.md))?
2. Map each problem to the correct pixel-domain tool: dust specks, scratches, chrominance shadow noise, and the film grain itself. Which of these do you *deliberately leave alone*, and why (recall the grain-vs-noise distinction from Chapter 16)?
3. Specify local application for each: which Smart Filter, masked to what, at what radius/threshold intent.
4. Explain why Median at large radius or Dust & Scratches at low threshold would be a disaster here.

**File 3 — Low-ISO landscape, raw, but shadows recovered +3 stops.** Bright sky and sunlit rock are clean; the recovered forest shadows show the tone-curve-amplified noise described in the Theory section, with fine foliage detail inside those shadows that must survive. Deliverable: a large fine-art print.

1. Explain, from the tone-curve argument, why the shadows are noisy despite the low ISO, and why this makes Camera Raw's raw-stage NR especially valuable here.
2. Design a *local* Detail-panel or masked strategy that cleans the shadow noise while preserving foliage detail — which is itself high-frequency luminance variation. Which slider is your enemy here if set globally, and why?
3. Decide whether AI Denoise helps or endangers the foliage detail, and justify it from the learned-prior/hallucination argument.
4. Set the Sharpening group so the foliage is crisp but the recovered-shadow noise is *not* amplified — specifically, what does the **Masking** slider buy you, and how would you confirm it with the Alt/Option preview?

**For all three, predict the failure modes.** Describe precisely what you would see if, on each file, someone instead (a) applied a single heavy *global* Luminance/Reduce-Noise pass calibrated to the worst region, and (b) ran AI Denoise (where available) at maximum Amount with no follow-up. Name the artifact (waxiness, color bleed, posterization, hallucinated detail, edge halos), where it would appear, and which control caused it.

This challenge requires you to synthesize the whole part: the raw-versus-rendered stage argument, the six-slider luminance/chrominance logic, the Masking-slider defense against noise sharpening, the parametric-versus-learned distinction and its hallucination risk, per-channel treatment, and — throughout — the discipline of local over global application. Do it on paper first; then, if you have the files, verify your predictions in software.
