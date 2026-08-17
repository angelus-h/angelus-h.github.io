---
description: Noise Deep Dive Part 5 — the frequency domain and Frequency Separation for noise work (and why it is mostly a retouching tool, not a denoiser), masking for selective noise reduction (luminosity masks, Blend If, Color Range, Camera Raw AI masks, gradient and inverted masks) with a full worked sky-vs-rocks recipe, and the two-way interaction between noise reduction and sharpening — correct order, masked sharpening, and distinguishing genuine edge detail from noise.
---

# Noise Deep Dive Part 5: Frequency Separation, Masking, and the Noise/Sharpening Balance

This is Part 5 of the eight-part Noise Deep Dive that expands [Chapter 16: Noise Reduction](16_Noise_Reduction.md). The earlier parts established the physics of noise ([Part 1](16D1_Physics_of_Digital_Noise.md)), the visual diagnosis of noise on old sensors ([Part 2](16D2_Old_Sensors_and_Visual_Diagnosis.md)), the RAW-versus-JPEG denoising mindset ([Part 3](16D3_Denoising_Mindset_RAW_vs_JPEG.md)), and the Camera Raw AI Denoise engine together with the traditional Photoshop tools ([Part 4](16D4_Camera_Raw_AI_Denoise_and_PS_Tools.md)). Parts 3 and 4 repeatedly deferred one topic to this part: *how* to apply noise reduction selectively, to one region and not another, and *how* noise reduction and sharpening constrain each other. This part delivers that mechanism in full.

Three tightly related subjects are treated here. First, the frequency domain and the Frequency Separation technique — what it is, how to build it, and an honest account of where it helps noise work and where it is useless or actively harmful. Second, masking for selective noise reduction — the complete toolkit, with two independent worked recipes for the flagship problem of strong noise reduction on a sky and minimal noise reduction on rocks in a single frame. Third, the two-way interaction between noise reduction and sharpening — why the order is not arbitrary, why sharpening a noisy image is destructive, and how to sharpen only the edges that are real.

This part assumes fluency with [Chapter 05: Channels, Masks, and Alpha Channels](05_Channels_and_Masks.md), [Chapter 13: Advanced Masking](13_Advanced_Masking.md), [Chapter 14: Blending Modes](14_Blending_Modes.md), and [Chapter 15: Sharpening Theory and Practice](15_Sharpening.md). Those chapters are the foundation; this part applies their concepts to the specific problem of noise.

---

## Learning Objectives

After completing this part, you will be able to:

1. Define the spatial frequency domain and classify image content — smooth tone, gradients, texture, edges, and noise — by the spatial frequencies it occupies, and explain why noise and fine real detail overlap in the high-frequency band.
2. Identify Gaussian Blur as a low-pass filter and High Pass as (approximately) a high-pass filter, and predict what each does to noise and to real detail.
3. Build a Frequency Separation stack correctly in both the 8-bit (Apply Image, Add, Scale 2, Offset 128) and 16-bit (Apply Image, Subtract, Scale 2, Offset 0, then Linear Light) variants, and explain the arithmetic that makes the recomposition lossless.
4. State precisely when Frequency Separation is genuinely useful for noise-related work and when it is useless or actively dangerous as a denoiser, justifying the claim that separating frequencies does not by itself remove any noise.
5. Select the correct masking primitive — layer mask, luminosity mask, Blend If, Select > Color Range, gradient mask, inverted mask, or a Camera Raw AI mask — for a given selective-noise-reduction problem, and explain the trade-offs of each.
6. Execute a full selective noise reduction that applies strong noise reduction to a sky and minimal noise reduction to rocks in a single image, using at least two independent methods (a luminosity/Color-Range mask in Photoshop and a Camera Raw Select Sky mask), and generalise the pattern to other subjects.
7. Explain rigorously why noise reduction must precede sharpening, and why sharpening a noisy image before denoising is destructive in a way that cannot be fully undone afterward.
8. Mask sharpening so that it strengthens only genuine edges — using the Camera Raw Masking slider, edge/luminosity masks, and Smart Sharpen's own noise controls — and articulate the visual and statistical criteria that distinguish a real edge from a noise fluctuation.
9. Specify a recommended noise-and-sharpening pipeline order with defensible starting values, framed explicitly as starting points rather than universal recipes.

---

## Conceptual Foundation

Every image can be described in two equivalent ways. The familiar description is the *spatial* description: a grid of pixels, each with a position and a value. The second description is the *frequency* description: the same image expressed as a sum of patterns that vary across the frame at different rates. A vast, even sky is a low-rate variation — it changes slowly, or not at all, from one side of the frame to the other. A field of fine gravel is a high-rate variation — the value changes from pixel to pixel. These two descriptions contain exactly the same information. Nothing is lost in translating between them. But they make different things easy to see and easy to manipulate, and that is the entire reason the frequency domain matters to a photographer.

[Chapter 15](15_Sharpening.md) introduced spatial frequency in the context of sharpening: sharpening is the selective amplification of high-frequency content at edges. This part extends the same vocabulary to noise. The central and uncomfortable fact — established physically in [Part 1](16D1_Physics_of_Digital_Noise.md) and stated repeatedly in [Chapter 16](16_Noise_Reduction.md) — is that **noise and fine real detail live in the same frequency band.** Both are high-frequency. A leaf vein and a noise speckle both consist of a value that differs sharply from its immediate neighbours. This overlap is not a limitation of any particular tool; it is a property of the signal. It is the reason no denoiser can be perfect, the reason sharpening amplifies noise, and the reason the frequency domain — despite its power — cannot magically separate noise from detail.

Frequency Separation is the Photoshop technique that splits an image into a low-frequency layer (broad tone and colour) and a high-frequency layer (fine texture and edges) so that each can be edited independently. It is a genuinely powerful retouching tool, covered in its retouching context in [Chapter 17: Retouching Techniques](17_Retouching.md). Many photographers, having learned that "noise is high frequency" and "Frequency Separation isolates the high frequencies," reach the intuitive but wrong conclusion that Frequency Separation is therefore a noise reduction technique. It is not — not by itself, and not in any way that a plain masked blur does not already achieve. A major goal of this part is to make that clear, with the arithmetic to back it up, so that you neither waste effort on it nor damage images by misapplying it.

The second half of the conceptual foundation is *selectivity*. Noise is not uniform across an image — [Chapter 16](16_Noise_Reduction.md) established that noise concentrates in the shadows and in smooth low-signal regions, and is largely masked in bright, textured areas. It follows that noise reduction should not be uniform either. The professional applies strong noise reduction exactly where the signal-to-noise ratio is poor and the content is smooth (skies, shadows, water, out-of-focus backgrounds) and applies little or none where the content is detailed and the noise is both less visible and more costly to smooth away (foliage, stone, fabric, eyes). This is a masking problem, and it is the same masking problem [Chapter 13](13_Advanced_Masking.md) solved for tonal adjustments. Here we solve it for noise, and we solve it twice, by two independent routes, so that you learn the pattern rather than memorising one recipe.

The third subject, the noise/sharpening interaction, closes the loop. Sharpening and noise reduction are mathematical opposites — one amplifies high-frequency variation, the other suppresses it. Applying them in the wrong order, or applying either without regard to the other, produces images that are simultaneously noisy and mushy: the worst of both. The order, the masking of sharpening, and the discipline of sharpening only true edges are what separate a clean, crisp result from a gritty, over-processed one.

---

## Terminology

### Spatial Frequency

**Definition:** Spatial frequency is the rate at which pixel values change across space in an image, measured in cycles per unit distance (for example, cycles per pixel, or line pairs per millimetre on a print). Low spatial frequency describes slow, gradual variation — broad areas of even tone, smooth gradients. High spatial frequency describes rapid variation — fine texture, sharp edges, and the pixel-to-pixel fluctuation that constitutes noise.

**Meaning in photographic practice:** Thinking in terms of spatial frequency reframes the entire noise-and-sharpening problem. Instead of asking "where is the noise," you ask "what frequencies is the noise occupying, and what real content shares those frequencies." The answer, always, is that noise is broadband but weighted toward the highest frequencies, and that it overlaps completely with the frequencies of fine real detail. Broad tone (sky, skin, water) is almost purely low-frequency, which is exactly why noise is so visible there: the noise is the only high-frequency content present, so there is nothing to mask it. In a field of gravel, the high-frequency real detail is loud enough that the high-frequency noise hides within it. This single idea explains selective noise reduction: treat the high frequencies aggressively where there is no real high-frequency detail to lose, and treat them gently where there is.

**Photoshop implementation:** Photoshop does not display a frequency spectrum, but several tools operate explicitly in the frequency domain. Gaussian Blur removes high frequencies (a low-pass operation). High Pass removes low frequencies (approximately a high-pass operation). Unsharp Mask and Smart Sharpen amplify a chosen band of high frequencies. The Frequency Separation technique splits an image into an explicit low-frequency layer and high-frequency layer.

**Related concepts:** Low-pass filter, high-pass filter, convolution (Chapter 15), Frequency Separation, noise, fine detail.

**Common misconception:** "Noise is purely high-frequency, so a high-pass tool can isolate it perfectly." Noise has energy across a range of frequencies, and — more importantly — the highest frequencies also contain the finest real detail. Isolating the high-frequency band isolates noise *and* detail together, inseparably. There is no frequency cut that catches all the noise and none of the detail, because at the top of the band they are the same kind of signal.

---

### Low-Pass Filter (Gaussian Blur)

**Definition:** A low-pass filter is an operation that passes low spatial frequencies through unchanged while attenuating or removing high spatial frequencies. Gaussian Blur is the canonical low-pass filter in Photoshop: it replaces each pixel with a weighted average of its neighbours, where the weights follow a Gaussian (bell) curve whose width is set by the Radius. The result retains broad tone and gradients while smoothing away fine texture, edges, and noise.

**Meaning in photographic practice:** A Gaussian Blur is, quite literally, a noise reduction operation — the crudest one. Averaging neighbouring pixels suppresses random pixel-to-pixel variation (noise) but equally suppresses meaningful pixel-to-pixel variation (detail). This is the fundamental trade-off of [Chapter 16](16_Noise_Reduction.md) in its simplest form. The Radius controls the cutoff: a small radius removes only the very highest frequencies (fine noise and the finest detail); a large radius removes progressively lower frequencies (softening broader structure). Blur is a poor general denoiser precisely because it is *not* edge-aware — it smooths edges as readily as it smooths noise — which is why dedicated denoisers (Camera Raw's Detail panel, AI Denoise) use edge-aware algorithms instead. But blur remains the building block from which Frequency Separation is constructed, and it is a legitimate, controllable denoiser when confined to a masked smooth region where there is no edge or detail to protect.

**Photoshop implementation:** Filter > Blur > Gaussian Blur. The Radius (in pixels) sets the cutoff frequency: larger radius = lower cutoff = more aggressive smoothing. Surface Blur and Median (Filter > Noise > Median) are related edge-preserving or rank-based variants that behave differently but occupy the same conceptual role.

**Related concepts:** High-pass filter, Radius, convolution kernel (Chapter 15), Frequency Separation low layer, Surface Blur, Median filter.

**Common misconception:** "Gaussian Blur at a low radius is a safe way to reduce noise." Even at a low radius, Gaussian Blur is not edge-aware — it softens genuine fine detail everywhere it is applied, indiscriminately. It is safe only when *masked* to a region that contains no detail worth keeping, or when the radius is so small that the effect is negligible (in which case it is also barely reducing noise). Its value in noise work is as a component of Frequency Separation and as a masked, region-confined smoother, not as a global denoiser.

---

### High-Pass Filter (High Pass)

**Definition:** A high-pass filter passes high spatial frequencies while attenuating or removing low frequencies. Photoshop's High Pass filter (Filter > Other > High Pass) approximates this: it subtracts a blurred (low-pass) version of the image from the original, leaving only the high-frequency content — edges and fine texture — as deviations from a neutral 50% grey. Where the image is smooth (low frequency only), High Pass returns flat grey; where the image has edges or texture (high frequency), High Pass returns light and dark fringes around them.

**Meaning in photographic practice:** High Pass is the frequency-domain complement of Gaussian Blur. It is best known as a sharpening technique (blend a High Pass layer in Overlay or a similar contrast mode to boost edge contrast, as in [Chapter 15](15_Sharpening.md)), and as the high-frequency layer generator in Frequency Separation. For noise work specifically, the crucial and sobering observation is this: **because noise is high-frequency, a High Pass layer contains the noise in full, sitting right alongside the real fine detail.** Viewing a High Pass layer of a noisy image is, in fact, an excellent *diagnostic* — it shows you the noise starkly, isolated from the distracting tone and colour of the full image (see [Part 2](16D2_Old_Sensors_and_Visual_Diagnosis.md) on visual diagnosis). But isolating the noise onto a layer is not removing it. The moment you try to clean that High Pass layer — by blurring it, for instance — you are blurring the real detail that shares the layer with the noise. You have gained nothing over blurring the original directly.

**Photoshop implementation:** Filter > Other > High Pass. The Radius sets which frequencies are kept: a small radius keeps only the very finest detail and noise; a larger radius keeps progressively coarser structure. Applied on a duplicate layer set to Overlay, Soft Light, or Linear Light, High Pass sharpens. In Frequency Separation, it can generate the high-frequency layer directly, or the high layer can be generated by the Apply Image subtraction method described later.

**Related concepts:** Low-pass filter, Overlay/Linear Light blending (Chapter 14), High Pass sharpening (Chapter 15), Frequency Separation high layer, noise diagnosis (Part 2).

**Common misconception:** "If noise is on the High Pass layer, I can just clean the High Pass layer and keep the sharp low layer." The low-frequency layer was never sharp — it is the blurred version, containing broad tone only. All of the image's real fine detail is on the *high* layer, inextricably mixed with the noise. Cleaning the high layer degrades the detail. There is no free lunch hiding in the frequency split.

---

### Frequency Separation

**Definition:** Frequency Separation (FS) is a technique that decomposes an image into two layers — a low-frequency layer holding broad tone, colour, and gradients, and a high-frequency layer holding fine texture, pores, edges, and (unavoidably) noise — such that recombining the two layers with the correct blending mode reproduces the original image exactly. Once separated, tone and colour can be edited on the low layer without disturbing texture, and texture can be edited on the high layer without disturbing tone.

**Meaning in photographic practice:** FS is fundamentally a *retouching* tool, and its home is [Chapter 17: Retouching Techniques](17_Retouching.md). Its legitimate purpose is to let a retoucher smooth uneven skin tone and colour blotches on the low layer while preserving genuine skin texture (pores, fine hair) on the high layer — or, conversely, to heal a distracting texture on the high layer without creating a tonal smudge. For *noise reduction* it is far less useful than photographers assume, and understanding why is more valuable than any recipe. Noise contaminates both layers, but its most visible high-frequency component lands on the high-frequency layer, right on top of the real texture you separated the layers to protect. FS does not denoise; it merely relocates the problem. The narrow, genuine noise-related uses of FS are discussed at length in the Theory section: they exist, but they are the exception, not the rule.

**Photoshop implementation:** Build it manually (detailed in Photoshop Implementation) via a blurred low layer plus a high layer generated by Apply Image (Subtract in 16-bit, Add with inversion in 8-bit) set to Linear Light. There is no single menu command; the technique is a composition of primitives.

**Related concepts:** Low-pass filter, high-pass filter, Apply Image (Chapter 13), Linear Light blending (Chapter 14), retouching (Chapter 17), Gaussian Blur.

**Common misconception:** "Frequency Separation is a professional noise reduction technique." It is a professional *retouching* technique. Used as a denoiser, it reduces — at best — to a masked blur of the low layer (which a masked blur of the original does equally well) or a blur of the high layer (which destroys the fine detail you wanted to keep). It offers no denoising capability that simpler, safer methods do not, and it introduces real risks (halos, tonal smearing at contrast edges) if built or applied carelessly.

---

### Selective Noise Reduction

**Definition:** Selective noise reduction is the application of noise reduction to some regions or tonal ranges of an image and not others (or at differing strengths across the image), controlled by a mask. It contrasts with global noise reduction, which applies uniform smoothing everywhere regardless of local noise level or content.

**Meaning in photographic practice:** Selective noise reduction is the professional default for any image whose noise is not uniform — which is nearly every image, because noise concentrates in shadows and smooth low-signal areas (see [Chapter 16](16_Noise_Reduction.md) and [Part 1](16D1_Physics_of_Digital_Noise.md)). The logic is straightforward: apply strong smoothing where the signal-to-noise ratio is poor and there is no fine detail to lose (skies, shadows, water, defocused backgrounds), and apply little or none where detail matters and noise is already masked by texture (foliage, stone, eyes, fabric). The gain is twofold — cleaner smooth areas *and* better-preserved detail — which no single global setting can deliver.

**Photoshop implementation:** Apply a strongly-denoised layer (Camera Raw Filter, Reduce Noise, or a duplicate processed by AI Denoise) and control where it shows through with a layer mask derived by any masking method: painted, luminosity (Chapter 13), Blend If, Color Range, gradient, or a Camera Raw AI mask. Alternatively, perform the selectivity inside Camera Raw itself using local masks with per-mask noise reduction.

**Related concepts:** Layer mask, luminosity mask (Chapter 13), Blend If (Chapter 14), Color Range, Camera Raw masking, signal-to-noise ratio (Chapter 16).

**Common misconception:** "Selective noise reduction is only worth the effort for difficult images." It is worth the effort for most images, because most images have a smooth region (sky, shadow, background) that tolerates and benefits from strong smoothing and a detailed region that is harmed by it. The effort is modest once the pattern is learned, and the quality difference is visible at any serious output size.

---

### Blend If

**Definition:** Blend If is a pair of tonal sliders in the Layer Style dialog (This Layer / Underlying Layer) that make a layer's pixels visible or hidden based on their brightness (or the brightness of a chosen channel), producing a real-time, tone-dependent blend without an explicit mask. Split-dragging a slider (Alt/Opt-drag) feathers the transition across a tonal range.

**Meaning in photographic practice:** For selective noise reduction, Blend If offers the fastest possible way to confine a denoised layer to a tonal range — typically the shadows, where noise lives. Place a fully denoised layer on top, then use "This Layer" Blend If to hide its highlight pixels, so the denoised layer contributes only in the darker tones while the sharp original shows through in the highlights. It is the live, no-channel equivalent of a Darks luminosity mask, with the same limitation noted in [Chapter 13](13_Advanced_Masking.md): the transition is a simple tonal ramp, not a mask derived from image structure, and it cannot be combined with spatial masks as flexibly as an explicit alpha channel.

**Photoshop implementation:** Double-click a layer (or Layer > Layer Style > Blending Options) to open Blend If. Choose the blend channel (Gray or a colour channel), then drag the This Layer / Underlying Layer black and white sliders; Alt/Opt-drag to split a slider for a graduated transition.

**Related concepts:** Luminosity mask (Chapter 13), blending modes (Chapter 14), selective noise reduction, tonal selection.

**Common misconception:** "Blend If and a luminosity mask are interchangeable." They overlap but differ: Blend If is live and non-destructive but not editable as a paintable mask and not directly combinable with spatial masks; a luminosity mask is an explicit alpha channel you can paint on, intersect, and refine. Use Blend If for speed, a luminosity mask for control.

---

### Color Range (for Noise Masking)

**Definition:** Select > Color Range builds a selection from sampled colours or from preset tonal ranges (Highlights, Midtones, Shadows, Skin Tones, Out-of-Gamut, or Sampled Colors with an eyedropper), with a Fuzziness slider controlling how far the selection spreads from the sampled values. The output is a graduated (partial) selection, not a hard binary one.

**Meaning in photographic practice:** Color Range is an efficient way to build a mask for a chromatically or tonally coherent region — a blue sky, a stretch of skin, a green backdrop — for the purpose of confining noise reduction to it (or excluding it). For the flagship sky-versus-rocks problem, sampling the sky's blue with a generous Fuzziness produces a mask that isolates the sky from the (differently coloured, more textured) rocks in one step. Because the selection is graduated, the resulting noise reduction transitions smoothly rather than cutting off at a hard line.

**Photoshop implementation:** Select > Color Range. Choose Sampled Colors and click the sky with the eyedropper (Shift-click or use the plus eyedropper to add more samples), adjust Fuzziness and Range, preview with a matte or grayscale view, then output as a selection and apply it as the layer mask of the denoised layer. The Highlights/Midtones/Shadows presets provide quick tonal masks similar in spirit to luminosity masks but built by a different, threshold-plus-feather algorithm.

**Related concepts:** Luminosity mask (Chapter 13), tonal selection vs spatial selection (Chapter 13), Fuzziness, selective noise reduction, Camera Raw Select Sky.

**Common misconception:** "Color Range's Shadows preset is the same as a Darks luminosity mask." They target similar tones but by different mathematics — Color Range's presets are threshold-based with feathering, whereas a true luminosity mask is a continuous selection derived directly from pixel luminance ([Chapter 13](13_Advanced_Masking.md)). For critical, seamless transitions, the luminosity mask is more precise; for speed, Color Range is often good enough.

---

### Camera Raw Masking (AI Masks, Select Sky, Select Subject, Range Masks)

**Definition:** Camera Raw's Masking panel provides local adjustment masks that can carry their own noise reduction (and sharpness, and every other local slider). Masks can be created by AI detection (Select Sky, Select Subject, Select Objects, Select People, Select Background), by brush, by linear or radial gradient, or by Range Masks (Luminance Range, Color Range, Depth Range where supported), and can be combined by intersection and subtraction.

**Meaning in photographic practice:** This is the RAW-stage route to selective noise reduction, and for RAW files it is frequently the best route because — as [Chapter 16](16_Noise_Reduction.md) and [Part 3](16D3_Denoising_Mindset_RAW_vs_JPEG.md) establish — noise reduction is more effective on raw data before gamma encoding amplifies shadow noise. Select Sky builds a mask of the sky in one click; you then push that mask's local Noise Reduction (and Luminance/Color sliders where exposed) hard, while the rest of the frame — the rocks — receives little or no noise reduction from the global sliders. Because the whole operation is on raw data and non-destructive, it is both higher quality and more revisable than the Photoshop equivalent.

**VERSION NOTE:** The set of local sliders exposed inside a Camera Raw mask has expanded over successive releases, and the exact roster of local noise-reduction controls, as well as which AI masks are available (Select Sky, Select Subject, Select Objects, Select People, Depth Range), depends on your Camera Raw version. **UNCERTAINTY:** The precise numeric range and default of the *local* Noise Reduction slider inside a Camera Raw mask, and whether local Color noise reduction is exposed separately in your version, should be verified against your installed Camera Raw — do not assume they match the global Detail-panel sliders.

**Photoshop implementation:** In Camera Raw (or Filter > Camera Raw Filter on a Smart Object), open the Masking panel, choose Select Sky (or another mask type), then adjust the local sliders for that mask — including its Noise Reduction. Add, intersect, or subtract additional mask components as needed.

**Related concepts:** Global Detail-panel noise reduction (Chapter 16), AI Denoise (Part 4), Color Range, luminosity mask, selective noise reduction.

**Common misconception:** "The Masking panel's local Noise Reduction is the same engine as global AI Denoise." Local mask noise reduction and the dedicated AI Denoise feature are different mechanisms; local mask NR is a per-region adjustment, whereas AI Denoise is a whole-image neural reconstruction that produces a new DNG ([Part 4](16D4_Camera_Raw_AI_Denoise_and_PS_Tools.md)). They can be used together — AI Denoise for the base, local masks for regional fine-tuning — but they are not interchangeable.

---

### Gradient Mask and Inverted Mask

**Definition:** A gradient mask is a layer mask filled with a black-to-white gradient (linear or radial), producing a smooth, directional falloff of an effect. An inverted mask is any mask whose values have been reversed (Ctrl/Cmd-I, or starting from a black-filled "hide all" mask and painting white to reveal), so that an effect is hidden by default and revealed only where painted.

**Meaning in photographic practice:** For noise work, a gradient mask suits scenes where noise varies smoothly with position rather than with tone or colour — most commonly a landscape where the sky at the top needs strong smoothing that should fade toward the detailed horizon and foreground. The inverted ("hide all") mask embodies the *subtractive* mindset that is often correct for noise: start with the denoised layer fully hidden, then paint the smoothing in only where you actually see objectionable noise, rather than applying it everywhere and painting it out. Painting the effect *in* keeps you honest about how much of the image you are actually smoothing.

**Photoshop implementation:** Add a layer mask; select the Gradient tool with a black-to-white gradient and drag across the region for a gradient mask. For an inverted mask, Alt/Opt-click the Add Layer Mask button to create a black (hide all) mask, or add a white mask and press Ctrl/Cmd-I, then paint white with a soft low-opacity brush to reveal.

**Related concepts:** Layer mask, gradient tool, selective noise reduction, luminosity mask (Chapter 13).

**Common misconception:** "A gradient mask is too crude for serious work." For content that genuinely varies with position — a sky darkening and cleaning toward the top of the frame — a gradient mask is not crude; it is the *correct* shape, because it matches the spatial structure of the problem. Crudeness comes from using a positional gradient where the problem is actually tonal (use a luminosity mask) or chromatic (use Color Range), not from the gradient itself.

---

### The Noise/Sharpening Order Dependency

**Definition:** The noise/sharpening order dependency is the principle that noise reduction and sharpening are non-commutative — applying them in different orders produces different, non-equivalent results — and that the correct order is noise reduction first, sharpening second, because sharpening amplifies whatever high-frequency content is present, including noise.

**Meaning in photographic practice:** This is the single most consequential workflow rule in the noise/sharpening relationship. Sharpen a noisy image first and you amplify the noise into hard, high-contrast speckle and edge halos; denoising afterward must then fight amplified noise and simultaneously softens the sharpening you just applied — you get an image that is both mushy and gritty. Denoise first and the sharpener operates on a cleaner signal, so its amplification lands mostly on real edges. The rule holds across tools, but its cleanest expression is in Camera Raw, where noise reduction and sharpening in the Detail panel are computed together on raw data and are designed to cooperate ([Chapter 16](16_Noise_Reduction.md)).

**Photoshop implementation:** In a layer stack, place noise-reduction layers below sharpening layers. In Camera Raw, rely on the simultaneous Detail-panel computation. When sharpening a noisy image is unavoidable in Photoshop, use Smart Sharpen's Reduce Noise slider and mask the sharpening to edges.

**Related concepts:** Sharpening (Chapter 15), noise reduction (Chapter 16), Camera Raw Masking slider, edge mask, Smart Sharpen Reduce Noise.

**Common misconception:** "Order does not matter much if the noise is mild." Even with mild noise, sharpening-first converts previously invisible noise into visible, sharpened speckle that subsequent denoising cannot cleanly remove without softening the real detail. The order rule is cheap to follow and expensive to violate; follow it regardless of noise level.

---

## Theory

### The Frequency Domain: What "Low" and "High" Actually Mean

A grayscale image is a two-dimensional array of numbers. The frequency-domain view says that this array can be reconstructed exactly as a sum of sinusoidal patterns of varying spatial frequency and orientation — the two-dimensional analogue of decomposing a sound into its constituent pitches. A perfectly flat grey field is a single "DC" term: zero frequency, no variation. A smooth gradient from dark to light is dominated by very low frequencies. A checkerboard of alternating single pixels is the highest frequency the grid can represent (the Nyquist frequency: one cycle per two pixels). Real photographs are mixtures: the sky contributes energy overwhelmingly at low frequencies, the gravel contributes energy across a wide band including the highest frequencies, and the sharp edge of a rooftop against sky contributes a burst of energy across many frequencies at once (a sharp step is "broadband").

**FACT:** A sharp edge in an image is not a single frequency; it is a superposition of many frequencies. This is why sharpening (which boosts a band of high frequencies) enhances edges, and why blur (which removes high frequencies) softens them — the edge's high-frequency components are what make it crisp.

**INTERPRETATION:** For the working photographer, the useful mental model is a three-band split, even though the true spectrum is continuous:

| Band | Rate of variation | Typical image content | Noise present? |
|------|-------------------|-----------------------|----------------|
| Low frequency | Slow / gradual | Broad tone, colour, gradients, sky, skin, out-of-focus areas | Some (large-scale blotchy/chroma mottling, especially old sensors — see [Part 2](16D2_Old_Sensors_and_Visual_Diagnosis.md)) |
| Mid frequency | Moderate | Soft structure, coarse texture, form shading | Yes |
| High frequency | Rapid / pixel-to-pixel | Fine texture, pores, hair, edges, small detail | Yes — the dominant, most visible noise component |

The critical row is the last one. **The high-frequency band contains both the finest real detail and the most visible noise, and no frequency-selective operation can separate the two,** because at that scale they are statistically and structurally alike. This is the frequency-domain restatement of the fundamental trade-off from [Chapter 16](16_Noise_Reduction.md).

### Gaussian Blur as a Low-Pass Filter

**FACT:** Gaussian Blur convolves the image with a Gaussian kernel — each output pixel becomes a bell-curve-weighted average of its neighbourhood. In the frequency domain, convolution with a Gaussian is multiplication by a Gaussian: high frequencies are multiplied by values near zero (removed), low frequencies by values near one (kept). The Radius parameter sets the width of the spatial kernel and, inversely, the cutoff frequency — a larger radius keeps a narrower band of low frequencies (blurs more).

Because noise is concentrated in the high frequencies, Gaussian Blur suppresses noise — but because fine detail is also high-frequency, it suppresses detail by the same mechanism. Blur cannot tell them apart; it removes high frequencies wherever they occur. This is precisely why blur is a *crude* denoiser and why edge-aware denoisers (bilateral filters, the Camera Raw Detail engine, AI Denoise) exist: they attempt to *not* blur across edges. Plain Gaussian Blur has no such discrimination.

**RECOMMENDATION:** Reserve Gaussian Blur for noise work in two situations only: (1) as a component of a Frequency Separation build, and (2) as a *masked* smoother confined to a genuinely detail-free region (a clear sky, a smooth shadow) where there is no fine detail to protect. Never use unmasked global Gaussian Blur as a denoiser on a whole photograph.

### High Pass as a High-Pass Filter, and Why It Contains the Noise

**FACT:** Photoshop's High Pass filter computes, in effect, the original image minus a Gaussian-blurred version of itself, then re-centres the result on 50% grey. What remains is the high-frequency content — the part of the image that the blur removed. The Radius sets the same cutoff as the corresponding blur.

Because the removed high frequencies include the noise, **the High Pass result contains the noise in full.** You can verify this directly: apply High Pass to a noisy image on a duplicate layer and view that layer. The flat regions, which looked merely "a bit noisy" in the full image, now show their noise starkly against neutral grey, because all the distracting tone and colour has been subtracted away. This makes High Pass an outstanding *diagnostic* for noise character and distribution ([Part 2](16D2_Old_Sensors_and_Visual_Diagnosis.md) uses exactly this kind of isolation to read a sensor's noise signature).

**INTERPRETATION:** The diagnostic value is real; the denoising value is illusory. Isolating the noise onto a layer does not remove it from the image — recomposing the layers restores the noise exactly. And you cannot clean the High Pass layer without harming detail, because the real fine detail lives on that same layer. Blurring the High Pass layer to "remove the noise from it" is mathematically almost identical to blurring the original image directly: you have taken a detour and arrived at the same crude, detail-destroying blur.

### Building Frequency Separation: The Arithmetic

Frequency Separation splits the image so that **low + high = original, exactly.** The low layer is a blur; the high layer must therefore be "everything the blur removed," stored in a way that, when blended back over the low layer, reconstructs the original without loss. The reconstruction blending mode is **Linear Light**, whose formula makes the arithmetic work out.

**FACT:** Linear Light blends a top layer over a bottom layer as, per channel (values normalised 0–1):

```
result = base + 2 x (blend - 0.5)
```

Equivalently, `result = base + 2 x blend - 1`. For the high-frequency layer stored around a mid-grey pivot, this adds back exactly the deviations that the blur removed, provided the high layer was generated with the matching scale and offset.

There are two standard construction methods, differing because 8-bit and 16-bit files handle the subtraction arithmetic differently.

**16-bit method (preferred):**

1. Duplicate the base image layer twice. Name the lower copy "Low" and the upper copy "High."
2. On the "Low" layer, apply Filter > Blur > Gaussian Blur at the radius that defines your separation point (small radius = only the finest detail goes to the high layer; larger radius = coarser structure moves up).
3. On the "High" layer, go to Image > Apply Image. Set Source to the document, Layer to "Low," Blending to **Subtract**, **Scale: 2**, **Offset: 0**. (This computes High = 2 x (Original − Low), which stores the high-frequency deviations at full precision.)

**UNCERTAINTY:** The exact Scale and Offset that yield a mathematically perfect reconstruction depend on Photoshop's internal handling of the Subtract blend in the current bit depth and version; the Scale 2 / Offset 0 (16-bit) and Scale 2 / Offset 128 (8-bit) conventions below are the widely used values, but verify perfect reconstruction on your version by toggling the high layer and checking that the result matches the original.

4. Set the "High" layer's blending mode to **Linear Light.** The stack now reproduces the original exactly.

**8-bit method:**

1. Duplicate as above into "Low" and "High."
2. Gaussian Blur the "Low" layer.
3. On the "High" layer, Image > Apply Image with Source Layer "Low," **Blending: Add**, **Scale: 2**, **Offset: 128**, and check **Invert.** (In 8-bit, Subtract can clip negative values to zero, losing the darker-than-mid deviations; the inverted-Add-with-offset-128 form keeps the high-frequency data centred on mid-grey 128 without clipping.)
4. Set the "High" layer to **Linear Light.**

**FACT:** In both methods, the high layer appears as a mostly flat mid-grey image with edge and texture information (and noise) visible as lighter and darker deviations around that grey — exactly like a High Pass result. The difference from a plain High Pass sharpening layer is that FS stores the high frequencies at a scale calibrated for *lossless* recomposition with the specific blurred low layer beneath it, so that edits to either layer are exact and reversible.

### Why Frequency Separation Does Not Denoise

Here is the crux of this part, argued rigorously.

**Claim:** Frequency Separation, by itself, removes zero noise. Recomposing the unedited low and high layers reproduces the original — noise included — bit for bit. Any noise reduction you achieve within an FS workflow comes entirely from an *additional* smoothing operation you apply to one of the layers, and that smoothing has an exact, simpler equivalent outside FS.

Consider the two things you could do to reduce noise within FS:

**Option 1 — Blur (or otherwise smooth) the low-frequency layer.** The low layer is already a blur of the original; blurring it further removes the low-frequency component of the noise (the broad chroma mottling and tonal blotches). This is legitimate and occasionally useful — it can attack large-scale colour noise on old sensors without touching fine detail, because the fine detail lives on the untouched high layer. But note carefully: **the fine, high-frequency luminance noise — the most visible component in most images — is on the high layer and is completely unaffected.** So blurring the low layer denoises only the coarse component, and it does so no better than a masked Surface Blur or a Color-noise reduction pass applied directly. FS buys you nothing here that a direct low-frequency smoother does not.

**Option 2 — Blur (or otherwise smooth) the high-frequency layer.** This is where photographers hope FS will shine, and it is where FS is *actively dangerous* as a denoiser. The high layer holds the noise, yes — but it also holds *every bit of real fine detail in the image.* Blurring the high layer to knock down the noise blurs the pores, the hair, the leaf veins, the edge crispness, in equal measure. Recomposed, the result is exactly what you would get by blurring the original: a mushy image. The frequency split has not helped; it has merely disguised a plain destructive blur as a sophisticated technique.

**FACT:** Therefore, no arrangement of FS layers denoises the high-frequency band without destroying high-frequency detail, because the split does not — cannot — separate high-frequency noise from high-frequency detail. They occupy the same layer for the same reason they occupy the same frequencies.

**INTERPRETATION:** The reason FS *works for retouching* but *fails for denoising* is that retouching problems are usually *localised* — a blemish, a stray hair, a blotch of uneven colour — and the retoucher heals or clones a *specific spot* on the appropriate layer while leaving the rest of that layer alone. That is targeted surgery, not band-wide smoothing. Noise, by contrast, is *everywhere* in the smooth regions, so "fixing" it means smoothing the whole band, which is where the detail loss becomes unavoidable. FS is a scalpel; denoising a noisy sky needs a filter, and FS provides no filter that a masked blur does not provide more directly.

### The Narrow, Legitimate Noise Uses of Frequency Separation

To be fair and complete: there are genuine, if narrow, cases where FS touches noise work usefully.

- **Coarse chroma-blotch removal on old sensors.** As noted in [Part 2](16D2_Old_Sensors_and_Visual_Diagnosis.md), some older sensors produce large-scale, low-frequency colour mottling. Splitting frequencies and gently blurring the *low* (or a colour-only version of the low) layer can reduce this blotching while leaving fine detail on the untouched high layer. But a direct approach — Camera Raw's Color and Color Smoothness sliders, or a masked blur on a chroma layer — usually does the same job with less setup.
- **Localised noise-spot retouching.** If a single region has an isolated noise artefact (a cluster of hot pixels, a patch of amp glow — see [Part 2](16D2_Old_Sensors_and_Visual_Diagnosis.md)), the FS retouching toolkit (heal/clone on the appropriate layer) can address that *specific spot* the way it addresses a blemish. This is retouching, not denoising, but it is a real noise-related use.
- **Separating denoising from texture re-injection.** An advanced workflow denoises the low-frequency structure aggressively and then re-injects a *synthetic* fine texture (or the original high layer at reduced opacity) to restore the *appearance* of detail. This is closer to the grain-management ideas in [Chapter 16](16_Noise_Reduction.md) and the hallucinated-detail discussion in [Part 6](16D6_AI_Upscaling_and_Hallucinated_Detail.md) than to true denoising, and it must be used with the same honesty caveats.

**RECOMMENDATION:** For general noise reduction, do not reach for Frequency Separation. Use AI Denoise or the Camera Raw Detail panel on raw data ([Part 4](16D4_Camera_Raw_AI_Denoise_and_PS_Tools.md)), applied selectively with masks (below). Reserve FS for retouching ([Chapter 17](17_Retouching.md)) and for the narrow coarse-chroma and spot cases above, and reserve it *knowingly*, understanding that you are not getting a free denoiser.

### Masking Theory for Noise: Tonal, Chromatic, Spatial, and Semantic

[Chapter 13](13_Advanced_Masking.md) distinguished tonal from spatial selection. For noise, four masking *criteria* matter, each matching a different way noise correlates with image content:

| Masking criterion | Mask type | Matches when noise correlates with... | Primary tools |
|-------------------|-----------|----------------------------------------|---------------|
| Tonal | Luminosity mask, Blend If, Color Range presets | Brightness (noise in shadows/midtones) | Chapter 13 luminosity masks, Blend If, Color Range Shadows |
| Chromatic | Color Range (Sampled Colors) | A colour region (blue sky, green backdrop) | Select > Color Range |
| Spatial | Painted mask, gradient mask | Position in frame (sky at top, foreground clean) | Brush, Gradient tool |
| Semantic | AI mask (Select Sky/Subject) | A recognisable object (the sky, the subject) | Camera Raw Masking, Photoshop Select Subject/Sky |

**INTERPRETATION:** The flagship sky-versus-rocks problem can be attacked through *any* of these criteria, because in that image the sky differs from the rocks tonally (usually brighter), chromatically (blue vs earth tones), spatially (top vs bottom), and semantically (sky vs ground). That over-determination is why it is the canonical teaching example — and why we solve it two independent ways below, to show that the *pattern* (denoise a layer, mask it to the smooth region) is what matters, not the particular mask.

### The Noise/Sharpening Interaction, Rigorously

Sharpening and noise reduction are mathematical opposites in the frequency domain: sharpening multiplies a band of high frequencies by a factor greater than one (amplification); noise reduction multiplies high frequencies by a factor less than one (attenuation). [Chapter 15](15_Sharpening.md) and [Chapter 16](16_Noise_Reduction.md) both state the consequence; here we make the order-dependence precise.

**FACT:** These operations do not commute. Denoise-then-sharpen and sharpen-then-denoise produce different results, and the difference is not cosmetic:

| Order | What happens to noise | What happens to detail | Net result |
|-------|-----------------------|------------------------|------------|
| Sharpen first, then denoise | Noise amplified into hard speckle and halos, then partly smoothed | Real edges sharpened, then partly re-softened by the denoise | Both gritty *and* mushy; sharpening wasted; residual amplified-noise texture that denoise cannot cleanly remove |
| Denoise first, then sharpen | Noise suppressed before amplification; only residual noise gets sharpened | Edges sharpened on a clean signal | Clean and crisp; the correct order |
| Simultaneous (edge-aware) | Denoise and sharpen computed together, sharpening steered away from flat/noisy areas toward edges | Edges sharpened, flats smoothed, in one pass | Generally best; what Camera Raw's Detail panel does on raw data |

**INTERPRETATION:** The reason sharpen-first is destructive *and irreversible* is that sharpening is a lossy amplification. Once a noise speckle has been amplified into a hard black-and-white dot with a halo, no denoiser can distinguish that amplified artefact from a genuine amplified edge — the sharpener has manufactured fake "edges" out of noise, and the denoiser, being edge-aware, may even try to *preserve* them. You cannot subtract the sharpening back out cleanly. Denoise-first avoids manufacturing those fake edges in the first place.

**FACT:** This is the technical justification for the pipeline order given in [Chapter 16](16_Noise_Reduction.md): primary noise reduction (Camera Raw Detail panel or AI Denoise) → capture sharpening (simultaneously, in Camera Raw) → tonal/colour editing → selective noise reduction if needed → creative sharpening (masked) → output sharpening (last, on a resized copy).

### Sharpening Only True Edges: Why Masking the Sharpener Matters

The complement of "denoise before you sharpen" is "sharpen only where there is a real edge." Even on a well-denoised image, residual noise remains in the flat areas; an unmasked sharpener will amplify it. The solution is to *mask the sharpening* so that it strengthens edges and leaves flat regions alone.

**FACT:** This is exactly what Camera Raw's **Masking** slider (in the Detail panel's Sharpening group) does. At Masking 0, sharpening is applied uniformly. As Masking increases, Camera Raw builds an edge mask from the image and progressively restricts sharpening to the strongest edges, protecting flat areas (skies, skin, defocused backgrounds) from being sharpened — and therefore from having their noise amplified. Hold Alt/Opt while dragging Masking to see the mask: white areas will be sharpened, black areas protected. This is the most important and most under-used control for sharpening any image that has noise.

**FACT:** Photoshop's Smart Sharpen has its own **Reduce Noise** slider, which suppresses the sharpening of low-contrast (noise-like) variation while still sharpening high-contrast (edge-like) variation. High Pass sharpening ([Chapter 15](15_Sharpening.md)) can be masked with a luminosity or edge mask so that only edges receive the contrast boost.

### Distinguishing a Real Edge from Noise

To sharpen only real edges, the sharpener (or you, when building an edge mask) must distinguish a genuine edge from a noise fluctuation. The criteria:

**FACT:** A genuine edge and a noise speckle differ in three measurable ways:

| Property | Genuine edge | Noise fluctuation |
|----------|--------------|-------------------|
| Spatial extent | Coherent along a line or contour; persists across multiple pixels in a consistent direction | Isolated; a single pixel or tiny cluster with no directional coherence |
| Contrast magnitude | Usually higher local contrast (a real tonal boundary) | Usually lower contrast (small deviation from the local mean), though high-ISO speckle can be large |
| Repeatability | Present in the same place in repeated exposures of the same scene | Random; differs between exposures (this is the operational definition of noise — see [Part 1](16D1_Physics_of_Digital_Noise.md)) |

**INTERPRETATION:** Edge-detection-based masking (Camera Raw's Masking slider, Smart Sharpen's Reduce Noise, a High Pass or Find Edges-derived luminosity mask) exploits the first two properties — spatial coherence and contrast magnitude — because those are computable from a single image. The third property, repeatability across frames, is the ground truth of what noise *is*, and it is what image-averaging and multi-frame techniques exploit (touched on in [Part 1](16D1_Physics_of_Digital_Noise.md) and revisited in [Part 7](16D7_Workflows_and_E520_Project.md)); but within a single frame you must rely on coherence and contrast, which is why no single-image edge mask is perfect: a high-contrast, spatially-coincidental noise cluster can masquerade as an edge, and a low-contrast genuine texture can be mistaken for noise.

---

## Photoshop Implementation

### Building and Inspecting a Frequency Separation Stack (16-bit)

The purpose here is not to denoise but to *understand* the split and to have the stack available for the legitimate narrow uses above. Work in 16-bit (Image > Mode > 16 Bits/Channel) for clean arithmetic.

1. Open the image. Duplicate the base layer twice (Ctrl/Cmd-J twice). Name the lower duplicate "Low" and the upper duplicate "High." Hide "High" for now.
2. Select "Low." Apply Filter > Blur > Gaussian Blur. Choose a radius at the boundary you want between "coarse" and "fine." A small radius (for example, 2–4 px on a full-resolution file — **a starting point, not a universal recipe**) sends only the finest detail and noise to the high layer; a larger radius moves coarser structure up. Watch the preview: the blur radius should be just large enough that the texture/noise you want on the high layer disappears from the low layer, leaving smooth tone.
3. Show and select "High." Image > Apply Image. Source: this document. Layer: "Low." Channel: RGB. Blending: **Subtract**. Scale: **2**. Offset: **0**. Click OK.
4. Set the "High" layer blending mode to **Linear Light.** The composite should now look identical to the original.
5. **Verify losslessness:** toggle the "High" layer visibility off and on, or add a temporary Difference-mode flattened comparison against the original. If reconstruction is perfect, the difference is pure black. If it is not (a version/bit-depth arithmetic quirk — see the **UNCERTAINTY** note in Theory), adjust the method or switch to the 8-bit build.
6. **Inspect the high layer:** Alt/Opt-click into the "High" layer thumbnail or set it to Normal temporarily. You will see the noise starkly on mid-grey. This is your noise diagnostic ([Part 2](16D2_Old_Sensors_and_Visual_Diagnosis.md)). Set it back to Linear Light when done.

**RECOMMENDATION:** Convert both "Low" and "High" to Smart Objects before editing them, so any blur or heal you apply for the narrow noise uses remains re-editable and you preserve the non-destructive discipline of [Chapter 06](06_Non_Destructive_Workflow.md).

### Building Frequency Separation (8-bit variant)

If the file is 8-bit and you cannot convert:

1. Duplicate into "Low" and "High" as above.
2. Gaussian Blur the "Low" layer.
3. Select "High." Image > Apply Image. Source Layer: "Low." Blending: **Add**. Scale: **2**. Offset: **128**. Check **Invert.** Click OK. The high layer becomes mid-grey (128) with deviations.
4. Set "High" to **Linear Light.** Verify reconstruction as above.

**FACT:** The 8-bit form uses Offset 128 (mid-grey) and Add-with-Invert precisely because 8-bit Subtract clips negatives to zero, discarding the below-mid deviations; centring on 128 keeps both positive and negative deviations representable.

### Demonstrating That FS Does Not Denoise (a Teaching Exercise You Can Run)

To internalise the argument, run the comparison yourself:

1. On a noisy image, build the FS stack. Blur the *high* layer with a small Gaussian Blur "to remove its noise."
2. Flatten a copy of the result.
3. Separately, take the *original* image and apply the *same* Gaussian Blur directly.
4. Compare the two flattened results at 100% (use a Difference layer). They will be nearly identical — the FS route produced essentially the same detail-destroying blur as the direct route.

**INTERPRETATION:** This is the empirical demonstration of the Theory claim: blurring the high layer is blurring the image. FS added ceremony, not capability.

### Selective Noise Reduction — Method A: Luminosity / Color-Range Mask in Photoshop

This is the flagship recipe: **strong noise reduction on the sky, minimal on the rocks,** done entirely in Photoshop on a rendered file (use this route for TIFF/JPEG, or when the file is already composited; for a RAW original, prefer Method B). The pattern is: make one strongly-denoised layer, then mask it to the sky.

**Stage 1 — create the strongly-denoised layer:**

1. With the image open, create a merged stamp on top (Ctrl/Cmd-Alt/Opt-Shift-E). Convert it to a Smart Object (right-click > Convert to Smart Object) so the noise reduction stays re-editable. Name it "NR — Sky (strong)."
2. Apply strong noise reduction sized for the *worst* area (the sky). Use Filter > Camera Raw Filter and push the Detail panel Luminance and Color sliders high (for a noisy sky, Luminance in the 50–70 range and Color 40–60 is **a starting point, not a universal recipe** — see [Chapter 16](16_Noise_Reduction.md) for how to calibrate to the actual noise). Alternatively use Filter > Noise > Reduce Noise, or an AI Denoise output brought in as a layer ([Part 4](16D4_Camera_Raw_AI_Denoise_and_PS_Tools.md)). Do not worry that this is too strong for the rocks — the mask will exclude them.

**Stage 2 — build the sky mask (chromatic route, Color Range):**

3. With the "NR — Sky (strong)" layer active but before masking, go to Select > Color Range.
4. Set Select to **Sampled Colors.** Click the sky with the eyedropper. Shift-click (or use the plus eyedropper) additional sky samples across its tonal range (near the horizon it is lighter and less saturated; up high it is deeper). Raise **Fuzziness** until the sky is well selected (shown white in the grayscale preview) while the rocks stay dark. Watch for the rocks or any blue-ish elements creeping in; back off Fuzziness or refine samples if they do.
5. Click OK to load the selection. With the "NR — Sky (strong)" layer active, click the Add Layer Mask button. The selection becomes the mask: the strong noise reduction now shows only in the sky.

**Stage 2 (alternative) — build the sky mask (tonal route, luminosity mask):** If the sky is reliably the brightest region, build a Lights luminosity mask instead (Ctrl/Cmd-click the RGB composite, narrow with L2/L3 as in [Chapter 13](13_Advanced_Masking.md)) and apply it as the layer mask. Use this route when colour is unreliable (a grey overcast sky) but tone separates cleanly.

**Stage 3 — refine and protect the rocks:**

6. Inspect the mask edge along the horizon and around rock silhouettes at 100%. If the strong smoothing bleeds onto rock edges, paint black on the mask along those edges with a soft, low-opacity brush to restore the rocks' full detail.
7. If any bright rock faces were caught by a luminosity mask, or any blue-grey rock was caught by Color Range, paint them out (black) on the mask. This is the spatial-restriction step from [Chapter 13](13_Advanced_Masking.md): tonal/chromatic mask for the bulk, painted correction for the exceptions.
8. **Optionally add a minimal-NR layer for the rocks.** If the rocks themselves have some noise you want gently reduced (but far less than the sky), create a second merged-stamp Smart Object named "NR — Rocks (light)," apply a light noise reduction (Luminance ~15–25, Color ~25 — **a starting point**), and mask it with the *inverse* of the sky mask (Ctrl/Cmd-click the sky mask, Select > Inverse, add mask; or copy the sky mask to the new layer and Ctrl/Cmd-I to invert). Now the sky gets strong NR, the rocks get light NR, and the horizon transitions smoothly between them.

**RECOMMENDATION:** Keep both NR layers as Smart Objects with Camera Raw Filter smart-filters. You can then re-open either and re-tune its strength after seeing the whole image, without rebuilding anything — the non-destructive ideal of [Chapter 06](06_Non_Destructive_Workflow.md).

### Selective Noise Reduction — Method B: Camera Raw Select Sky Mask (RAW-stage)

This is the second, independent route to the *same* result, done on raw data where noise reduction is most effective ([Chapter 16](16_Noise_Reduction.md), [Part 3](16D3_Denoising_Mindset_RAW_vs_JPEG.md)). Prefer this for RAW originals.

1. Open the RAW file in Camera Raw (or apply Filter > Camera Raw Filter to a Smart Object of the base image).
2. **Set global noise reduction for the rocks (the detailed region).** In the Detail panel, apply only light global Luminance NR and appropriate Color NR — enough for the rocks and shadows, not more. The rocks are your baseline; you will add extra NR to the sky locally.
3. Open the **Masking** panel. Create a mask with **Select Sky.** Camera Raw detects and masks the sky automatically. Inspect the mask overlay; refine with Add/Subtract (brush) if the horizon or any rock spires are miscategorised. You can also intersect the sky mask with a Luminance or Color Range component to tighten it.
4. **With the sky mask active, push its local Noise Reduction.** In the mask's local sliders, increase the local Noise Reduction substantially (and local Color/Luminance noise controls if your version exposes them — see the **VERSION NOTE** and **UNCERTAINTY** in Terminology). The sky now receives strong smoothing; the rocks, outside the mask, keep the light global setting.
5. **Optionally add a Select Subject or inverted-sky mask for the rocks** if you want to actively *protect* them or apply local sharpening to them (see sharpening below). Subtracting the sky mask from a full-frame mask, or using Select Subject, isolates the rocks for their own treatment.
6. Set capture sharpening in the Detail panel with a high **Masking** value so that sharpening concentrates on the rock edges and spares the smoothed sky (see next section). Open into Photoshop when done.

**INTERPRETATION:** Methods A and B reach the same destination — clean sky, detailed rocks — by chromatic/tonal masking on rendered pixels versus semantic (AI) masking on raw data. Learning both teaches the transferable pattern: *isolate the smooth region by whatever criterion separates it best, and confine strong smoothing to it.* For a portrait it would be Select Subject to protect the face and strong NR on the background; for an interior it would be a shadow luminosity mask; the mask changes, the pattern does not.

### Blend If for Fast Shadow-Confined Noise Reduction

When the noise correlates with tone (shadows) rather than region, Blend If is the quickest route:

1. Create a merged-stamp Smart Object and apply strong noise reduction (sized for the shadows).
2. Double-click the layer to open Blending Options (Blend If).
3. On **This Layer**, drag the white-point slider left to hide the denoised layer's highlight pixels, so it contributes only in darker tones. **Alt/Opt-drag to split the slider** and feather the transition across the midtones, avoiding a hard cutoff.
4. The denoised layer now shows through only in the shadows; the sharp original shows in the highlights. This is the live equivalent of a Darks luminosity mask ([Chapter 13](13_Advanced_Masking.md)), ideal when noise is tonal rather than spatial.

### Gradient-Masked Noise Reduction for Skies

When noise varies with position (a sky that is noisiest and smoothest at the top, transitioning to detailed horizon):

1. Create the strongly-denoised merged-stamp Smart Object.
2. Add a layer mask. Select the Gradient tool, black-to-white (or white-to-black) linear gradient.
3. Drag from the horizon (where you want the NR to fade out, so the mask is black there) up into the sky (where you want full NR, mask white). The strong smoothing now fades in smoothly from horizon to sky top, matching the spatial structure of the problem.
4. Refine by painting on the mask where the straight gradient does not follow an irregular horizon.

### Masking the Sharpening to True Edges

Having denoised (selectively), sharpen — but confine the sharpening to real edges so residual noise in the flats is not amplified.

**Camera Raw Masking slider (capture sharpening, on raw data):**

1. In the Detail panel, set Sharpening Amount, Radius, and Detail for the subject ([Chapter 15](15_Sharpening.md)).
2. Hold **Alt/Opt and drag the Masking slider.** The preview turns to a black-and-white edge mask: white = will be sharpened, black = protected. Increase Masking until the flat areas (sky, smooth shadow) turn black and only the genuine edges (rock texture, contours) remain white. Release. Sharpening now avoids the smoothed sky and its residual noise.

**Smart Sharpen with Reduce Noise (Photoshop, rendered pixels):**

1. Convert the layer to a Smart Object. Filter > Sharpen > Smart Sharpen.
2. Set Amount and Radius for the edges you want to strengthen.
3. Raise the **Reduce Noise** slider until the flat-area speckle stops being sharpened while the edges stay crisp. This suppresses the sharpening of low-contrast (noise-like) variation while preserving it on high-contrast (edge-like) variation.

**Edge/luminosity-masked High Pass sharpening (maximum control):**

1. Create a merged-stamp Smart Object. Filter > Other > High Pass at a small radius. Set the layer to Overlay (or Linear Light) to sharpen ([Chapter 15](15_Sharpening.md)).
2. Add a layer mask. Build an **edge mask**: duplicate a high-contrast channel, apply Filter > Stylize > Find Edges (or use a High Pass-derived, contrast-boosted mask), invert/adjust so edges are white and flats black, and blur it slightly to feather. Load it as the sharpening layer's mask.
3. Now the High Pass sharpening applies only along genuine edges; the smoothed sky and its residual noise are protected. This is the [Chapter 13](13_Advanced_Masking.md) combination of a derived mask with a sharpening layer, applied to the noise problem.

---

## Professional Workflow

### The Integrated Noise-and-Sharpening Pipeline

The following consolidates the order established in [Chapter 16](16_Noise_Reduction.md) with the selective and masked-sharpening techniques of this part. Every numeric value is **a starting point, not a universal recipe** — calibrate to the specific image at 100% as [Chapter 16](16_Noise_Reduction.md) instructs.

| Step | Operation | Stage / Tool | Notes |
|------|-----------|--------------|-------|
| 1 | Correct exposure and white balance | Camera Raw | Denoisers evaluate SNR better on correctly-exposed data ([Part 4](16D4_Camera_Raw_AI_Denoise_and_PS_Tools.md)) |
| 2 | Primary noise reduction | Camera Raw Detail panel or AI Denoise, on raw data | Most effective before gamma encoding ([Chapter 16](16_Noise_Reduction.md)) |
| 3 | Capture sharpening with high Masking | Camera Raw Detail panel (simultaneous with NR) | Alt/Opt-drag Masking to protect flats/sky |
| 4 | Local selective noise reduction | Camera Raw Masking (Select Sky, etc.) or Photoshop masked NR layer | Strong on smooth regions, minimal on detail |
| 5 | Tonal and colour editing | Photoshop adjustment layers | Watch for shadow-lift re-amplifying noise; re-check flats |
| 6 | Creative sharpening, masked to edges | Smart Sharpen / High Pass on a masked Smart Object | Never unmasked on a noisy-flat image |
| 7 | Output sharpening | On a resized copy, last | Sized to output medium ([Chapter 22](22_Output_Delivery.md)) |

**RECOMMENDATION:** Do as much of steps 2–4 as possible in Camera Raw on raw data, because both noise reduction and edge-masked sharpening are more effective there and remain non-destructive. Push into Photoshop for the region-specific masking that Camera Raw's tools cannot express, and for creative and output sharpening.

### Deciding Which Masking Criterion to Use

| The smooth region is distinguished by... | Use | Fallback |
|------------------------------------------|-----|----------|
| A distinct colour (blue sky, green screen) | Color Range (Sampled Colors) or Camera Raw Color Range mask | Painted mask |
| Brightness (shadows, bright sky) | Luminosity mask (Chapter 13) or Blend If | Color Range tonal presets |
| Position (top of frame, one corner) | Gradient mask | Painted mask |
| Being a recognisable object (the sky, the subject) | Camera Raw Select Sky/Subject, or Photoshop Select Subject | Manual selection + Select and Mask |
| Nothing reliable / complex boundary | Painted mask, refined in Select and Mask (Chapter 13) | Channel-based mask (Chapter 13) |

**INTERPRETATION:** When several criteria apply (as in sky-vs-rocks), pick the one that gives the cleanest separation with the least cleanup, and refine with painting. Combining criteria — a Color Range sky mask intersected with a Lights luminosity mask — can sharpen the separation further, exactly as [Chapter 13](13_Advanced_Masking.md) combines masks by intersection.

### Where Frequency Separation Fits (and Does Not)

**RECOMMENDATION:** In a professional noise workflow, Frequency Separation appears only in these slots, if at all: (1) as a *diagnostic* (inspect the high layer to read noise character, as in [Part 2](16D2_Old_Sensors_and_Visual_Diagnosis.md)); (2) as a *retouching* step for isolated artefacts (hot-pixel clusters, amp-glow patches) handled by heal/clone on the appropriate layer ([Chapter 17](17_Retouching.md)); (3) for *coarse chroma-blotch* reduction on old sensors by gently smoothing the low layer. It never appears as the general denoiser — that role belongs to Camera Raw / AI Denoise applied selectively. Making this explicit in your own workflow prevents the common, costly mistake of building an elaborate FS stack that quietly does nothing a masked blur would not do, while risking halos and tonal smearing.

---

## Common Mistakes

**Treating Frequency Separation as a denoiser.** The central error this part exists to correct. Separating frequencies removes no noise; only an added smoothing does, and blurring the high layer merely blurs the detail while blurring the low layer only touches the coarse component. Use AI Denoise or the Camera Raw Detail panel, applied selectively, for actual noise reduction; reserve FS for retouching.

**Blurring the high-frequency layer to "clean" it.** This destroys the fine real detail that shares the high layer with the noise, producing the same mush a direct blur produces. If you catch yourself blurring the high layer for noise, stop and use a masked denoiser on the original instead.

**Building the FS stack in 8-bit with the Subtract method.** In 8-bit, Subtract clips the negative deviations and the reconstruction is not lossless. Use the Add / Invert / Offset 128 method in 8-bit, or work in 16-bit with Subtract / Offset 0. Always verify losslessness with a Difference check.

**Sharpening before denoising.** Amplifies noise into hard speckle and halos that no subsequent denoise can cleanly remove, and simultaneously the denoise re-softens the sharpening. Denoise first, always — the rule is cheap to follow and its violation is effectively irreversible.

**Sharpening the whole image after selective noise reduction.** Even after you have cleaned the sky, an unmasked sharpener re-amplifies its residual noise, undoing the selective work. Mask the sharpening to edges — Camera Raw's Masking slider, Smart Sharpen's Reduce Noise, or an edge mask.

**Applying strong global noise reduction sized for the sky to the whole frame.** This is the mistake selective NR exists to avoid: it smooths the detailed rocks unnecessarily, destroying texture that had little visible noise to begin with (the texture was masking it). Size global NR for the *detailed* region and add strong NR only to the smooth region via a mask.

**Using a hard-edged selection for the sky mask.** A hard cut along the horizon produces a visible seam between the smoothed sky and the sharp rocks. Use graduated masks (Color Range is inherently graduated; luminosity masks are continuous; feather painted masks) so the transition follows image structure, exactly as [Chapter 13](13_Advanced_Masking.md) prescribes.

**Choosing the wrong masking criterion.** Using a positional gradient where the noise correlates with tone (or a luminosity mask where it correlates with colour) forces the mask to fight the image. Match the criterion to how the noise actually correlates with content — colour, tone, position, or object — per the decision table above.

**Ignoring the coarse (low-frequency) chroma component on old sensors.** Fine luminance NR alone leaves large-scale colour blotches untouched ([Part 2](16D2_Old_Sensors_and_Visual_Diagnosis.md)). Address it with Color / Color Smoothness or a smoothed low-frequency layer, not by pushing luminance NR higher (which only smears fine detail without touching the chroma blotches).

**Over-sharpening the rocks to "compensate" for the smoothed sky.** The two regions do not need to match in apparent sharpness; a smooth sky next to detailed rocks is natural. Sharpen the rocks to what they need and leave the sky smooth — do not chase a uniform "crispness" that reintroduces sky noise.

---

## Summary

The frequency-domain view describes an image as a sum of patterns varying at different spatial rates. Smooth tone is low-frequency; fine texture, edges, and — crucially — noise are high-frequency. **Gaussian Blur is a low-pass filter** (keeps low frequencies, removes high, thereby smoothing both noise and detail); **High Pass is a high-pass filter** (keeps high frequencies, and therefore contains the noise in full alongside the real fine detail). This overlap of noise and detail in the high band is the frequency-domain statement of the fundamental noise/detail trade-off: no frequency-selective operation can separate them, because at the finest scale they are the same kind of signal.

**Frequency Separation** splits the image into a blurred low layer and a high layer such that Linear Light recomposition is lossless (Apply Image with Subtract/Scale 2/Offset 0 in 16-bit, or Add/Invert/Scale 2/Offset 128 in 8-bit). It is a powerful *retouching* tool but, honestly assessed, **not a denoiser**: the split removes no noise, blurring the high layer destroys detail, and blurring the low layer touches only the coarse component that simpler tools handle as well. FS's legitimate noise-adjacent uses are narrow — diagnosis of noise character, spot-retouching of isolated artefacts, and coarse chroma-blotch reduction on old sensors. For real denoising, use AI Denoise or the Camera Raw Detail panel, applied selectively.

**Selective noise reduction** — the "how" that Parts 3 and 4 deferred — applies strong smoothing only where the signal-to-noise ratio is poor and detail is absent (skies, shadows, water, backgrounds) and little or none where detail matters. The masking toolkit spans tonal (luminosity masks, Blend If), chromatic (Color Range), spatial (painted and gradient masks), and semantic (Camera Raw Select Sky/Subject, Photoshop Select Subject) criteria. The flagship sky-versus-rocks problem was solved two independent ways: a Color-Range/luminosity mask on a strongly-denoised layer in Photoshop, and a Camera Raw Select Sky mask carrying strong local noise reduction on raw data. Both teach the same transferable pattern — isolate the smooth region by whatever criterion separates it best, and confine strong smoothing to it — with the transition always graduated, never a hard seam.

**Noise reduction and sharpening are mathematical opposites and do not commute.** Denoise first, sharpen second — because sharpening amplifies whatever high frequencies are present, and sharpening a noisy image manufactures hard speckle and fake edges that no later denoise can cleanly remove. After denoising (selectively), **mask the sharpening to genuine edges** using Camera Raw's Masking slider, Smart Sharpen's Reduce Noise, or an edge/luminosity mask, so residual noise in the flats is not re-amplified. A real edge is distinguished from noise by spatial coherence, contrast magnitude, and (across frames) repeatability; single-image edge masks exploit the first two, which is why they help greatly but are never perfect. The recommended pipeline — expose/WB, primary NR, capture sharpening with high Masking, local selective NR, tonal/colour editing, edge-masked creative sharpening, and output sharpening last — puts every operation in the order its mathematics demands.

---

## Exercises

### Exercise 1: Reading the Frequency Bands

Open a noisy image (ISO 3200+ or a low-ISO image underexposed and recovered).

1. Duplicate the base layer. Apply Filter > Other > High Pass at a radius of about 3 px. Set the layer to Normal and view it.
2. Identify, on the grey field: the real fine detail (coherent, following contours) and the noise (isolated, directionless speckle). Note that they coexist on the same layer.
3. Increase the High Pass radius to 10 px, then 25 px. Observe how progressively coarser structure joins the high layer as the cutoff lowers.
4. Delete the High Pass layer. Duplicate the base again and apply Gaussian Blur at 3 px. Observe that this removes exactly the band the 3 px High Pass isolated — the two are complementary.

**Success criterion:** You can point to noise versus real detail on a High Pass layer, and you understand that Gaussian Blur and High Pass at the same radius split the image at the same frequency.

### Exercise 2: Frequency Separation Is Not a Denoiser

Open a noisy image with genuine fine detail (foliage, fabric, or hair).

1. Build a 16-bit Frequency Separation stack (Low = Gaussian Blur; High = Apply Image Subtract, Scale 2, Offset 0; High set to Linear Light). Verify losslessness with a Difference check against the original.
2. Blur the *high* layer with a 1.5 px Gaussian Blur. Flatten a copy. Call it "FS route."
3. On the original, apply the same 1.5 px Gaussian Blur directly. Flatten a copy. Call it "Direct route."
4. Compare "FS route" and "Direct route" with a Difference layer at 100%.

**Success criterion:** The two results are nearly identical, demonstrating that blurring the high layer is equivalent to blurring the image — FS added no denoising capability.

### Exercise 3: Sky-vs-Rocks with a Color Range Mask

Open a landscape with a noisy sky and detailed rocks (or foreground).

1. Create a merged-stamp Smart Object; apply strong noise reduction sized for the sky.
2. Select > Color Range; sample the sky; raise Fuzziness until the sky is white and the rocks dark in the preview. Load as selection, add as the layer's mask.
3. At 100%, check the horizon and rock edges; paint black on the mask where smoothing bleeds onto the rocks.
4. Toggle the layer to compare with the untreated original.

**Success criterion:** The sky is visibly cleaner while the rocks retain full detail, with no hard seam at the horizon.

### Exercise 4: The Sharpening Masking Slider

Open a RAW file with a smooth sky and detailed foreground.

1. In Camera Raw Detail panel, set Sharpening Amount to about 60 and Radius to about 1.0.
2. Hold Alt/Opt and drag the **Masking** slider from 0 upward. Watch the edge mask appear.
3. Stop when the sky is black (protected) and the foreground edges are white (sharpened).
4. Release and compare the sky's noise with Masking at 0 versus your chosen value.

**Success criterion:** You can set Masking so that sharpening spares the sky (avoiding noise amplification) while still sharpening genuine edges, and you can read the black-and-white edge-mask preview.

---

## Advanced Exercises

### Advanced Exercise 1: Two Independent Routes to the Same Selective Result

Take one RAW landscape (noisy sky, detailed rocks) and produce the clean-sky/detailed-rocks result twice.

1. **Route A (Photoshop):** Render the RAW with only light global NR. In Photoshop, build a strongly-denoised merged-stamp Smart Object and mask it to the sky with either a Color Range mask or a Lights luminosity mask ([Chapter 13](13_Advanced_Masking.md)). Add a light-NR layer for the rocks masked with the inverse.
2. **Route B (Camera Raw):** From the same RAW, set light global NR for the rocks, then add a Select Sky mask and push its local noise reduction hard. Set capture sharpening with high Masking.
3. Export both at the same size and compare at 100%: sky cleanliness, rock detail, horizon transition, and any artefacts.

**Success criterion:** Both routes deliver a clean sky and detailed rocks; you can articulate the trade-offs (raw-stage effectiveness and non-destructiveness of B versus the region-specific mask control of A) and choose deliberately for a given file.

### Advanced Exercise 2: Edge-Masked Creative Sharpening on a Denoised Image

Take a denoised high-ISO image (from AI Denoise or heavy Detail-panel NR).

1. Create a merged-stamp Smart Object; apply High Pass at ~1.2 px; set to Overlay.
2. Build an edge mask: duplicate the highest-contrast channel, Find Edges, adjust with Levels so edges are white and flats black, blur slightly to feather. Load it as the sharpening layer's mask.
3. Compare with the same High Pass sharpening applied unmasked, at 100% on a flat (previously noisy) area.

**Success criterion:** The masked version sharpens edges without re-amplifying residual noise in the flats; the unmasked version visibly re-roughens the smoothed areas.

### Advanced Exercise 3: Order-of-Operations Proof

Take a moderately noisy image and produce three versions:

1. **Denoise then sharpen:** Camera Raw Filter (Luminance 35, Color 30), then Smart Sharpen (Amount 120, Radius 0.8, Reduce Noise 0).
2. **Sharpen then denoise:** Smart Sharpen first (same settings), then Camera Raw Filter (same settings).
3. **Simultaneous:** In Camera Raw on the RAW, set NR and sharpening (with high Masking) together in the Detail panel.

Compare all three at 100% in both a flat area and along edges.

**Success criterion:** You can demonstrate and explain why denoise-then-sharpen beats sharpen-then-denoise, and why the simultaneous raw-stage approach is generally best.

---

## Blackbelt Challenge

You are handed a single high-ISO RAW landscape and must design — without touching Photoshop until the plan is complete — a full noise, masking, and sharpening strategy, then justify every choice against the theory of this part.

**The image:** Shot at ISO 6400 on an ageing APS-C body (the sensor generation discussed in [Part 2](16D2_Old_Sensors_and_Visual_Diagnosis.md)). It contains: (a) a large, deep-blue twilight sky occupying the top 40% of the frame, showing strong luminance noise and visible coarse chroma blotching; (b) a dark, jagged rock ridge across the middle, with fine lichen and crack texture that must be preserved; (c) a foreground of wet gravel in open shade, moderately noisy but full of fine specular highlights that must stay crisp; and (d) a thin band of bright water reflecting the sky along the base of the ridge. Output: a 24x36 inch exhibition print and a 2048 px web version.

Address each of the following:

1. **Frequency-domain assessment.** Classify each of the four regions by the spatial frequencies of its *content* and of its *noise*. In which regions do noise and real detail overlap most dangerously? In which is the sky's coarse chroma blotching a *low*-frequency problem that fine luminance NR will not touch, and why?

2. **The Frequency Separation temptation.** A colleague proposes building a Frequency Separation stack and blurring the high layer to denoise the whole image. Explain precisely, with the arithmetic, why this will not denoise the rocks and gravel without destroying their fine texture. Then identify the *one* narrow place in this specific image where FS (or an equivalent low-frequency smoothing) is legitimately useful, and say exactly which layer you would edit and how.

3. **Masking strategy.** For each region that needs different treatment, choose a masking criterion (tonal, chromatic, spatial, or semantic) and the specific tool, and justify the choice. The sky, the ridge, the gravel, and the water reflection each separate from the others by *different* criteria — say which and why. How would you keep the bright water (which shares the sky's colour and brightness) from being swept into the sky's strong noise reduction? Describe at least one mask *intersection* or *subtraction* you would use ([Chapter 13](13_Advanced_Masking.md)).

4. **Selective noise reduction plan.** Specify, as starting points, the noise reduction strength for each region and the stage (Camera Raw global, Camera Raw local mask, or Photoshop masked layer) at which you would apply it. Treat luminance and chroma noise separately, and say how you would attack the sky's coarse chroma blotching specifically. Explain why sizing the *global* NR for the sky would be a mistake for the gravel and lichen.

5. **Sharpening plan.** Given the aggressive NR the sky needs, design a masked sharpening strategy that crisps the lichen, cracks, and gravel speculars without re-amplifying the smoothed sky's residual noise. Specify how you would use Camera Raw's Masking slider (what the Alt/Opt preview should look like when correct), and what edge or luminosity mask you would use for any Photoshop creative sharpening. State the pipeline order and defend it.

6. **Output divergence.** Explain why the noise/sharpening balance for the 24x36 print differs from the 2048 px web version. Which residual noise becomes invisible at web size, and which sharpening decisions must change? Where does output sharpening sit in the order, and why on a resized copy ([Chapter 22](22_Output_Delivery.md))?

7. **Failure-mode prediction.** Describe, specifically, what the image would look like if you (a) sharpened globally before denoising; (b) applied strong global luminance NR sized for the sky to the entire frame; (c) built the FS stack and blurred the high layer as the colleague proposed; and (d) masked the sky's NR with a hard-edged selection. For each, name the artefact, where it would appear, and the theory from this part that predicts it.

A complete answer synthesises the frequency-domain model, the honest limits of Frequency Separation, the full masking toolkit matched to how noise correlates with content, the non-commutativity of noise reduction and sharpening, and the divergent demands of print and web output — into one coherent, defensible plan for a genuinely difficult single frame, with the failure modes predicted before a single slider is touched.
