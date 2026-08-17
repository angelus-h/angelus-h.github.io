---
description: Why late-2000s DSLRs and small sensors are a special denoising case, what old RAW files can and cannot be rescued to, and how to visually diagnose every class of noise and artifact on screen — luminance, chroma, banding, blotching, shadow and highlight noise, JPEG blocking, mosquito noise, demosaicing artifacts, oversharpening, and AI-hallucinated texture — including the "noise looks worse in the highlights" paradox.
---

# Noise Deep Dive Part 2: Old Sensors and the Visual Diagnosis of Noise

This is Part 2 of the eight-part Noise Deep Dive supplement to [Chapter 16: Noise Reduction](16_Noise_Reduction.md). Part 1 ([16D1_Physics_of_Digital_Noise.md](16D1_Physics_of_Digital_Noise.md)) established where noise comes from at the level of photons and electrons. This part turns to two practical questions that Part 1's physics makes answerable: first, what is genuinely different about the files produced by an older, smaller-sensor camera such as the Olympus E-520, and what can modern processing realistically recover from them; and second, how do you actually *see* and *name* the specific defect in front of you, so that you can choose the right treatment in the parts that follow.

Diagnosis precedes treatment. Every subsequent part of this series — the RAW-versus-JPEG mindset in Part 3 ([16D3_Denoising_Mindset_RAW_vs_JPEG.md](16D3_Denoising_Mindset_RAW_vs_JPEG.md)), the AI Denoise and Photoshop tooling in Part 4 ([16D4_Camera_Raw_AI_Denoise_and_PS_Tools.md](16D4_Camera_Raw_AI_Denoise_and_PS_Tools.md)), frequency-selective work in Part 5 ([16D5_Frequency_Masking_and_Sharpening.md](16D5_Frequency_Masking_and_Sharpening.md)), upscaling in Part 6 ([16D6_AI_Upscaling_and_Hallucinated_Detail.md](16D6_AI_Upscaling_and_Hallucinated_Detail.md)), and the assembled workflows in Part 7 ([16D7_Workflows_and_E520_Project.md](16D7_Workflows_and_E520_Project.md)) — assumes you can first look at an image and correctly classify what is wrong with it. Applying color noise reduction to a banding problem, or luminance smoothing to a demosaicing maze artifact, wastes detail and fixes nothing. This part builds the eye that the rest of the series relies on.

## Learning Objectives

After completing this part, you will be able to:

1. Explain why a late-2000s DSLR with a small sensor (using the Four Thirds Olympus E-520 as the running example) is a distinct denoising problem, in terms of photosite area, read-noise floor, dynamic range, and color depth, and articulate qualitatively how it differs from a modern APS-C sensor without fabricating exact figures.
2. Predict, before editing, which photographic errors in an old file are *compensable in post* (white balance, mild exposure error, mild noise, lens defects) and which are already *irreversible information loss* (clipped highlights, motion blur, missed focus, shadows crushed below the read-noise floor).
3. Justify why modern processing — AI denoise, improved demosaicing, and current camera profiles — can extract a genuinely good image from an old RAW that the camera's own JPEG engine could not, without believing that processing creates information that was never captured.
4. Visually identify, at the correct zoom level, each of the principal noise and artifact classes: luminance noise, chroma noise, banding (pattern/fixed-pattern noise), color blotching, shadow noise, apparent highlight noise, JPEG blocking, mosquito noise, demosaicing artifacts (maze/labyrinth, zippering, false color), oversharpening halos, and AI-hallucinated texture.
5. State, for each artifact class, what it looks like, where in the frame and in which tonal region it appears, at what magnification it becomes visible, and what physically or algorithmically causes it.
6. Explain why the *same* physical amount of noise is far more disturbing in one image than another, invoking texture masking, tonal region, output size, and subject expectation.
7. Resolve the common perception that "noise is worse in the highlights," distinguishing genuine highlight noise from optical/perceptual effects, tone-curve (gamma) redistribution, JPEG artifacts, and processing artifacts — and reconcile this with the Part 1 fact that real noise is worst in the shadows.
8. Build a repeatable Photoshop inspection procedure — 100%/200% zoom discipline, per-channel inspection, shadow-boosting to reveal hidden noise, and split-view comparison — that produces a written diagnosis before any correction is applied.
9. Separate a defect that lives in the *capture* from a defect that lives in a *prior processing step* (in-camera JPEG, a previous editor's export, an earlier denoise pass), because the two demand completely different responses.

---

## Conceptual Foundation

Two ideas organize this part, and they are connected.

The first is that **an old file is not a defective version of a new file; it is a different signal with a lower ceiling.** A modern sensor and an old small sensor, exposed to the same scene, do not produce the same data with the old one simply "noisier." They produce data with a genuinely lower information content in the shadows, a genuinely narrower dynamic range, and a genuinely lower color bit depth in the deep tones. The physics in Part 1 — shot noise scaling with the square root of collected photons, read noise as a fixed floor — means that a smaller photosite collecting fewer photons has a mathematically lower signal-to-noise ratio at equal exposure, and there is no algorithm that can retroactively supply the photons that were never collected. What modern processing *can* do is stop wasting the information that *was* captured. The camera's own JPEG engine from 2008 threw away most of the recoverable latitude; a 2024 RAW converter with a good profile and AI denoise does not. The gap between what an old sensor captured and what an old camera *delivered as a JPEG* is enormous, and that gap is where almost all "rescue" value lives.

The second idea is that **treatment is meaningless without diagnosis.** The word "noise" is used colloquially for a dozen distinct phenomena that look superficially similar — a rough, broken, or wrong-looking texture in a region that should be smooth — but that have completely different causes and completely different fixes. Random per-pixel brightness scatter (luminance noise), random per-pixel color scatter (chroma noise), horizontal or vertical striping (banding), soft patches of wrong color (blotching), 8×8 tiles (JPEG blocking), shimmer around edges (mosquito noise), zippered or maze-like edges (demosaicing artifacts), bright/dark rims on edges (oversharpening), and confident-but-invented micro-texture (AI hallucination) are not variations of one thing. They are separate diagnoses. Some are noise (random). Some are structured (pattern). Some are lossy-compression damage. Some are reconstruction errors. Some are things a previous *processing* step did, not the camera. A professional does not reach for a slider until the label is correct.

The connection between the two ideas: old files, and especially old small-sensor files pushed in post, are precisely the files where *several* of these phenomena appear at once and interact. An E-520 shadow, recovered two stops, can simultaneously show real shadow luminance noise, real chroma noise, faint horizontal banding from the readout, and — if it came to you as a JPEG rather than an ORF — blocking and mosquito noise layered on top of all of it. Learning to separate these on screen is the whole job of this part.

---

## Terminology

### Photosite Area (Pixel Pitch)

**Definition:** Photosite area is the physical light-collecting surface of a single sensor element, usually summarized by pixel pitch (the center-to-center spacing of photosites, in micrometres). For a given sensor generation and fill factor, collected photons per pixel at equal exposure scale roughly with photosite area, and shot-noise-limited signal-to-noise ratio scales with the square root of collected photons.

**Meaning in photographic practice:** Pixel pitch is the single most useful number for predicting a sensor's low-light behavior, more so than megapixel count or even sensor format in isolation. A small sensor divided into many pixels has tiny photosites; each collects few photons; each has a poor per-pixel SNR. The Four Thirds sensor in the Olympus E-520 is approximately 17.3 × 13.0 mm (**FACT**, per the anchor specification for this series) carrying roughly 10 effective megapixels, which yields a moderately small photosite by the standards of its own era and a distinctly small one compared to a modern APS-C sensor of similar resolution. **UNCERTAINTY:** The exact pixel pitch in micrometres and the exact full-well capacity of the E-520's Live MOS sensor are not part of the anchor facts for this series; do not cite a specific figure without confirming it against a sensor datasheet or a measurement source such as a published photosite analysis.

**Related concepts:** Full-well capacity, shot noise, signal-to-noise ratio, sensor format, crop factor, read noise.

**Common misconception:** "More megapixels means more noise." Not directly. What matters is photons per photosite and how you view the result. Downsampling a high-resolution file to a common output size averages neighboring photosites and recovers much of the per-pixel SNR deficit. The trap with old sensors is not that they had *too many* pixels — 10 MP is modest — but that each of those pixels sat on a small sensor and therefore started with few photons.

---

### Read-Noise Floor

**Definition:** The read-noise floor is the fixed, signal-independent noise added by the sensor's readout electronics on every exposure, expressed in electrons. It sets the darkest signal that can be distinguished from electronic randomness: signal below the read-noise floor is not recoverable, because it is buried in noise that is present whether or not any light arrived.

**Meaning in photographic practice:** The read-noise floor is the reason deep-shadow recovery has a hard limit rather than a soft one. Part 1 established shot noise; the read-noise floor is the other half. **FACT (general sensor physics):** modern sensor designs have driven read noise dramatically lower than late-2000s designs, which is precisely why shadow-pushing a current RAW yields cleaner results than shadow-pushing an old one. **UNCERTAINTY:** the specific read-noise value of the E-520 in electrons is not an anchor fact; treat any single number as unverified unless taken from a measurement source. The practical consequence stands regardless of the exact figure: the E-520's shadows go to unusable noise sooner, at a shallower push, than a modern sensor's.

**Photoshop implementation:** Photoshop cannot show you the read-noise floor as a number, but you can *find* it visually: boost the shadows aggressively (Camera Raw Shadows/Blacks, or an Exposure/Curves lift) and watch where detail stops resolving and only structureless noise remains. That plateau is the read-noise floor made visible. Everything below it is information loss, not a denoising target.

**Related concepts:** Shot noise, dynamic range, ETTR, shadow recovery, information loss.

**Common misconception:** "Enough noise reduction can recover crushed shadows." No. Denoising a region that is entirely below the read-noise floor produces smooth *nothing* — it removes the noise and leaves no detail, because there was no signal to reveal.

---

### Dynamic Range (Old-Sensor Context)

**Definition:** Dynamic range is the ratio between the largest signal a photosite can hold before clipping (full-well capacity) and the read-noise floor, usually expressed in stops. It defines how many stops of scene brightness the sensor can record with usable signal in a single exposure.

**Meaning in photographic practice:** An older small sensor has both a smaller full well (small photosite) and a higher read-noise floor (older electronics), so it is squeezed from both ends and delivers fewer usable stops than a modern sensor. **INTERPRETATION:** the practical experience of shooting an E-520-class camera is that highlights clip sooner *and* shadows turn to noise sooner than a modern photographer's instincts expect. This is why old-camera exposure discipline (protect the highlights, do not rely on shadow latitude) matters more, not less. **UNCERTAINTY:** the exact measured dynamic range of the E-520 in stops is not an anchor fact; do not quote a specific stop count without a measurement source.

**Related concepts:** Full-well capacity, read-noise floor, ETTR, clipping, highlight recovery, HDR bracketing.

**Common misconception:** "RAW always has lots of headroom to recover." Old RAW has *some* highlight headroom and *limited* shadow latitude, both smaller than modern RAW. The word "RAW" does not confer modern dynamic range on old hardware.

---

### Color Depth (Tonal/Color Bit Depth in Practice)

**Definition:** Color depth here means the number of distinct, noise-free tonal steps a sensor can actually distinguish per channel — the *effective* bit depth, which is bounded above by the file's nominal bit depth but bounded more tightly by noise. The E-520's ORF raw is 12-bit (**FACT**, per anchor specification), so it carries at most 4096 levels per channel, and fewer *usable* levels once noise consumes the lowest steps.

**Meaning in photographic practice:** Effective color depth governs how gracefully deep tones and subtle gradients survive editing. A 12-bit old file has less room for aggressive tone-curve stretching in the shadows before posterization or color breakup appears than a modern 14-bit file. **INTERPRETATION:** this is a background reason — alongside noise — to do heavy tonal moves in Camera Raw on the raw data rather than on a rendered 8-bit export, and to keep old files in 16-bit throughout the Photoshop stage. **UNCERTAINTY:** the E-520's measured color-depth figure (e.g., a bits-per-channel score from a lab) is not an anchor fact; do not cite one without a source.

**Related concepts:** Bit depth (Chapter 01, [01_Digital_Image_Fundamentals.md](01_Digital_Image_Fundamentals.md)), posterization, gamma encoding, banding, gradient smoothness.

**Common misconception:** "12-bit versus 14-bit is invisible." In the well-exposed midtones, usually yes. In pushed shadows and long smooth gradients — exactly where old files are already weakest — the difference compounds with the noise disadvantage and becomes visible.

---

### Compensable Error

**Definition:** A compensable error is a capture mistake whose corrective information still exists in the file, so that post-processing can substantially or fully restore the intended result. The signal is present; it is merely mis-mapped, mildly buried, or geometrically distorted in a recoverable way.

**Meaning in photographic practice:** The compensable set for a RAW file includes: white balance (RAW records the sensor's native response and defers the WB decision to development, so it is fully re-assignable); mild under- or over-exposure within the sensor's latitude (a tonal remap, not new information); mild noise (a masking problem, addressed by the whole rest of this series); and lens defects with known correction models (distortion, lateral chromatic aberration, vignetting), which Camera Raw can correct from a lens profile. **RECOMMENDATION:** when triaging an old shoot, sort images by whether their problems are compensable before investing editing time — a file whose only faults are WB, mild exposure error, and lens distortion is a full recovery; a file with the same faults *plus* missed focus is not worth the same effort.

**Photoshop implementation:** White balance — Camera Raw White Balance tool/sliders. Exposure — Camera Raw Exposure/Highlights/Shadows. Lens — Camera Raw Optics panel (Enable Lens Corrections; the E-520's Four Thirds lenses may or may not have a bundled profile — see the Version Note below). Chromatic aberration — Optics panel Defringe.

**Related concepts:** Information loss, RAW latitude, lens profile, DCP camera profile, white balance, Chapter 03 ([03_Camera_Raw.md](03_Camera_Raw.md)).

**Common misconception:** "Anything can be fixed in post." Only compensable errors can. The next term is the other half.

---

### Information Loss (Irreversible Error)

**Definition:** An irreversible error is a capture mistake that has destroyed the information needed to correct it. No processing can restore what was never recorded or what was overwritten by the capture failure; the best any tool can do is *plausibly invent* a substitute, which is a different thing from recovery.

**Meaning in photographic practice:** The irreversible set includes: **blown highlights** where all channels are clipped to maximum (the tonal separation is gone — recovery sliders can only darken a flat white patch or fabricate texture); **motion blur** and **camera shake** (the light from each scene point was smeared across many photosites; deconvolution can sometimes reduce mild, uniform blur but cannot reconstruct genuinely lost edge information); **missed focus** (the high-frequency detail was never projected onto the sensor sharply; sharpening enhances what is there, it does not create resolution that was not captured); and **deep-shadow crush below the read-noise floor** (the signal is buried in electronic noise, so denoising yields smooth emptiness). **INTERPRETATION:** the single most valuable diagnostic habit with old files is to decide, early and honestly, which faults are in this category, so you do not spend an hour fighting a problem that has no solution.

**Photoshop implementation:** There is no corrective tool for genuine information loss — there are only *replacement* tools (Generative Fill, AI upscaling, content-aware fill) that fabricate plausible content. Those belong to Part 6 ([16D6_AI_Upscaling_and_Hallucinated_Detail.md](16D6_AI_Upscaling_and_Hallucinated_Detail.md)) and Chapter 20 ([20_AI_Tools.md](20_AI_Tools.md)), and they must be understood as invention, not recovery.

**Related concepts:** Clipping, read-noise floor, deconvolution, hallucinated detail, Generative Fill.

**Common misconception:** "AI can un-blur and un-clip." AI can produce something that looks un-blurred or un-clipped by generating new pixels consistent with its training. That is a creative act, not a measurement recovery, and it must be disclosed and used deliberately.

---

### Luminance Noise (Diagnostic View)

**Definition:** Random, per-pixel variation in brightness only, with hue and saturation roughly intact — a monochromatic grit or grain overlaid on the image. (The physics and treatment are covered in [16_Noise_Reduction.md](16_Noise_Reduction.md); this entry is about *recognizing* it.)

**Meaning in photographic practice:** Luminance noise is the "acceptable" noise — it reads like grain, it is tolerated and sometimes wanted, and over-removing it produces the wax-figure look. In diagnosis, it is your baseline: nearly every noisy image has luminance noise, and your job is to gauge its *amount* relative to the local texture that might mask it.

**Photoshop implementation:** Best seen at 100–200% on a smooth mid-tone or shadow. To isolate it from color, view a single channel (usually Green is cleanest, Blue noisiest) or temporarily desaturate a stamped copy; what remains is luminance noise.

**Related concepts:** Chroma noise, grain, texture masking, per-channel inspection.

**Common misconception:** "It should all be removed." See Chapter 16 — restraint preserves detail.

---

### Chroma Noise (Diagnostic View)

**Definition:** Random, per-pixel variation in color — isolated red, green, blue, magenta, and cyan speckles scattered across a region that should be chromatically uniform, largely independent of the underlying brightness.

**Meaning in photographic practice:** Chroma noise reads instantly as a technical fault because the eye tolerates almost no random color in areas it expects to be uniform. It is worst in shadows and in the noisiest channel. On old sensors and in pushed old files it can be severe and can extend up into the midtones.

**Photoshop implementation:** Seen at 100% in any shadow. To confirm that a colored region is *noise* rather than *real color*, boost saturation temporarily (Vibrance/Saturation up) — random per-pixel color that intensifies into confetti is chroma noise; a coherent patch that stays coherent is real (or is blotching — see below).

**Related concepts:** Blotching, Bayer array, demosaicing, Color slider (Camera Raw).

**Common misconception:** "Color noise reduction will hurt real colors." Real color varies over many pixels; chroma noise varies pixel-to-pixel. Aggressive color NR removes the latter with little cost to the former.

---

### Banding (Pattern / Fixed-Pattern Noise)

**Definition:** Structured, non-random noise that appears as regular stripes — horizontal, vertical, or occasionally a grid — often aligned with the sensor's readout direction. Unlike random noise, banding is correlated across a whole row or column, which is why the eye locks onto it: the visual system is exquisitely sensitive to lines.

**Meaning in photographic practice:** Banding is a signature old-sensor and pushed-shadow problem. It arises from small, consistent offsets between readout amplifiers or columns/rows; at normal exposure it is buried, but a heavy shadow lift multiplies it into visible stripes. It also appears as *posterization banding* in smooth gradients — a different cause (insufficient effective bit depth for the tonal stretch) but a similar look. Distinguishing the two matters: readout banding is fine straight lines aligned to the sensor axes; posterization banding is broad steps following the tonal contour of a gradient (e.g., concentric arcs in a sky).

**Photoshop implementation:** Readout banding is easiest to confirm by boosting shadows hard and inspecting a flat dark area at 100%; the stripes will align to the frame edges. Per-channel inspection often shows banding concentrated in one channel. Posterization banding is confirmed by watching a smooth gradient (sky) at 100% as you push a tone curve — steps appear where the histogram is being stretched over too few levels.

**Related concepts:** Fixed-pattern noise, read noise, posterization, color depth, dithering, Add Noise.

**Common misconception:** "Banding is just strong noise, so a noise slider will fix it." Random-noise reduction does little to correlated stripes and can even make them *more* visible by removing the random component that partially camouflaged them. Banding needs pattern-specific treatment (covered in Part 5 [16D5_Frequency_Masking_and_Sharpening.md](16D5_Frequency_Masking_and_Sharpening.md)) or, for posterization, added dither/noise and higher bit depth.

---

### Color Blotching (Low-Frequency Chroma Mottling)

**Definition:** Soft, larger-than-a-pixel patches of wrong color — clouds of magenta, green, or other tints, several to many pixels across — in a region that should be uniform. Blotching is low-frequency chroma error, as distinct from chroma noise, which is high-frequency per-pixel speckle.

**Meaning in photographic practice:** Blotching is what heavy color noise *becomes* after aggressive color noise reduction: the NR smooths the pixel-level speckle into slow-moving color blobs rather than eliminating the color error entirely. It also arises directly in deep shadows of old files. It is more insidious than chroma noise because it survives ordinary color NR (which targets high-frequency color) and because at a glance it can be mistaken for a real color cast.

**Photoshop implementation:** Best seen at 100% on a large smooth shadow (a dark wall, a night sky). Because it is low-frequency, it does *not* get worse with 200% zoom the way speckle does — a diagnostic tell. Treatment usually means large-radius color-only correction: a Hue/Saturation or Camera Raw color NR with high Color Smoothness, or painting neutral over the region on a color-blend layer.

**Related concepts:** Chroma noise, Color Smoothness (Camera Raw), color cast, spatial frequency (Chapter 15, [15_Sharpening.md](15_Sharpening.md)).

**Common misconception:** "Turning the Color slider higher will remove it." Past a point, higher color NR *creates* blotching by spreading residual color error into blobs. Blotching often needs a different, low-frequency tool.

---

### Shadow Noise vs Apparent Highlight Noise

**Definition:** *Shadow noise* is the genuine, physics-driven noise concentrated in low-signal regions, where SNR is worst. *Apparent highlight noise* is the perception that bright regions are noisy — a perception that, on inspection, usually resolves to something other than real noise in the highlights.

**Meaning in photographic practice:** This pairing is the single most misdiagnosed thing in the whole subject, and it gets its own full treatment in the Theory and Implementation sections below. The short version: real per-pixel noise is almost always worst in the shadows (weak signal, fixed noise floor), yet photographers frequently report noise "in the bright parts." The report is real; the *cause* is usually not highlight noise. Resolving that paradox correctly determines whether you treat the shadows, adjust a tone curve, fix a JPEG, or leave the image alone.

**Photoshop implementation:** Covered in detail in "The Highlight-Noise Paradox" below.

**Related concepts:** SNR, gamma/tone curve, JPEG artifacts, perceptual contrast, Weber-Fechner.

**Common misconception:** The misconception *is* the term: that noise is worst where the image is brightest. It usually is not.

---

### JPEG Blocking (Macroblocking)

**Definition:** A defect specific to JPEG (and JPEG-derived) files: the image visibly breaks into 8 × 8 pixel tiles, most obviously in smooth gradients and flat areas, because JPEG compresses each 8 × 8 block independently and, at high compression, coarsens each block toward a flat or low-frequency approximation. (**FACT:** baseline JPEG uses an 8 × 8 discrete cosine transform block grid.)

**Meaning in photographic practice:** Blocking is a *processing* defect, not a capture defect. It tells you the file has been through lossy compression — either it is a camera JPEG (very relevant for old cameras, whose in-camera JPEG engines were crude) or it was exported at low quality by a previous editor. Recognizing blocking redirects your entire strategy: you are now restoring a damaged JPEG, not developing a RAW, and if the original ORF exists you should go back to it.

**Photoshop implementation:** At 100–200% on a sky or smooth wall, look for the regular square grid; snap the view to actual pixels. The tell is that the boundaries are perfectly horizontal/vertical and evenly spaced at 8-pixel intervals (at 100% and full-resolution files; resizing shifts the apparent spacing).

**Related concepts:** Mosquito noise, JPEG quantization, RAW vs JPEG (Part 3, [16D3_Denoising_Mindset_RAW_vs_JPEG.md](16D3_Denoising_Mindset_RAW_vs_JPEG.md)).

**Common misconception:** "Noise reduction will clean it up." NR softens block interiors but leaves the grid; blocking needs deblocking-specific handling and, ideally, a return to the RAW.

---

### Mosquito Noise (Ringing / Gibbs Artifact)

**Definition:** A JPEG artifact appearing as a shimmer, halo, or cloud of fine speckle *hugging high-contrast edges* — text, twigs against sky, a roofline — caused by the quantization of high-frequency DCT coefficients (a Gibbs/ringing phenomenon around sharp transitions). The name comes from its resemblance to a swarm of insects around the edge.

**Meaning in photographic practice:** Mosquito noise, like blocking, is a lossy-compression signature. It is easily mistaken for chroma noise or for a demosaicing artifact because it lives near edges and can be colored, but its defining trait is that it is *tethered to edges* and absent in truly flat areas, whereas random noise is present everywhere.

**Photoshop implementation:** At 200% on a hard edge over a smooth background (a dark branch against a bright sky is ideal). Real noise is uniform across the sky; mosquito noise is a band of disturbance that tracks the branch and fades away from it.

**Related concepts:** JPEG blocking, ringing, oversharpening halos (which are also edge-tethered — see below for the distinction).

**Common misconception:** "It's edge noise from high ISO." No — it is compression ringing. High-ISO noise is not edge-selective.

---

### Demosaicing Artifacts (Maze, Zippering, False Color)

**Definition:** Errors produced when the demosaicing algorithm reconstructs a full-color image from the single-color-per-photosite Bayer data. Three principal forms: **maze/labyrinth** (a wormy, brain-coral texture in fine detail where the interpolation cannot decide on edge direction); **zippering** (alternating light/dark or on/off pixels along a diagonal or near-horizontal/vertical edge, like a zipper); and **false color / color moiré** (spurious colored fringing or rainbow bands where high-frequency detail beats against the Bayer grid).

**Meaning in photographic practice:** These are *reconstruction* artifacts, and old files are more prone to them for two reasons: older bundled demosaicing was weaker, and older cameras' anti-aliasing choices interact with fine detail. The good news, and a central theme of this series, is that these are exactly the artifacts that a *modern* RAW converter's better demosaicing largely avoids — which is one concrete way that reprocessing an old ORF today beats both the old camera JPEG and old software. (**FACT (anchor spec):** ACR provides DCP camera profiles for the E-520, so a current ACR develop path is available for its files.)

**Photoshop implementation:** Seen at 100–200% in fine, contrasty, repetitive detail — fabric weave, distant foliage, roof tiles, brickwork. Maze looks like a shifting labyrinth in texture; zippering looks like a saw-tooth along an edge; false color looks like colored fringes or rainbow moiré not present in the scene. Re-developing the RAW in current ACR is the first-line "fix"; a Color Detail/Defringe adjustment addresses residual false color.

**Related concepts:** Bayer array, anti-aliasing filter, moiré, false color, demosaicing (Chapter 03, [03_Camera_Raw.md](03_Camera_Raw.md)).

**Common misconception:** "Maze texture is just noise." It is structured reconstruction error, not random noise; noise reduction blurs it but does not correct the underlying misinterpretation the way better demosaicing does.

---

### Oversharpening Artifacts

**Definition:** The visual signature of excessive or badly-parameterized sharpening: bright and dark rims (halos) flanking edges, exaggerated grit in flat areas where sharpening has amplified noise, and a crunchy, over-etched look. (Sharpening theory is Chapter 15, [15_Sharpening.md](15_Sharpening.md); this entry is about recognizing its overuse as a *defect* to diagnose.)

**Meaning in photographic practice:** Oversharpening is a *processing* defect, often already baked into an old camera JPEG (in-camera sharpening defaults of the era were frequently heavy) or applied by a previous editor. Diagnosing it matters because it is a strong argument to return to the RAW: you cannot un-sharpen cleanly, but you can start over from unsharpened raw data.

**Photoshop implementation:** At 100–200% on any high-contrast edge, look for a light halo on the bright side and a dark halo on the dark side — symmetric rims that are not part of the scene. In flat areas, oversharpening shows as accentuated noise with a directional, edge-following character near any structure.

**Related concepts:** Unsharp mask, halos, mosquito noise (both edge-tethered — distinguish by cause), capture vs output sharpening.

**Common misconception:** "Halos are lens flare / chromatic aberration." Halos from oversharpening are tonal rims that scale with the sharpening amount and radius and appear on *all* contrasty edges; CA is color-fringing that grows toward the frame corners and depends on the lens.

---

### AI-Hallucinated Texture

**Definition:** Detail that an AI denoiser or upscaler *invents* — plausible-looking micro-texture, edges, or patterns that were not present in the captured signal, generated because the model fills low-information regions with content consistent with its training rather than with the scene.

**Meaning in photographic practice:** This is the newest artifact class and the one most likely to fool the photographer, because it looks *good* — sharp, clean, detailed — right up until you realize it is fiction. It matters most for exactly the images this part is about: old, low-information, heavily-pushed files, where the model has the least real signal to anchor to and therefore invents the most. Recognizing it is an ethical as well as a technical skill (see Chapter 20, [20_AI_Tools.md](20_AI_Tools.md), and Part 6, [16D6_AI_Upscaling_and_Hallucinated_Detail.md](16D6_AI_Upscaling_and_Hallucinated_Detail.md)).

**Photoshop implementation:** At 100%, compare the AI output against the original noisy source side by side. Tells: texture that is *too* uniform or repetitive (skin, foliage, or fabric that looks generated); fine structures that are sharp but *wrong* (hair that merges or forks implausibly, text that becomes convincing gibberish); and detail that appears in regions the original showed as pure noise (which, per the read-noise-floor discussion, cannot contain recoverable detail — so any detail there is invented).

**Related concepts:** AI Denoise, upscaling, information loss, read-noise floor, plausibility vs fidelity.

**Common misconception:** "If it looks more detailed, it recovered more detail." Added apparent detail below the information floor is invention, not recovery. The correct question is not "does it look sharp" but "could this detail possibly have been in the captured signal."

---

## Theory

### Why Old Small-Sensor Files Are a Distinct Problem

Part 1 gives the mechanism; here is the consequence assembled for a specific class of camera. Consider the anchor example: the Olympus E-520, an entry-level DSLR announced in 2008 (**FACT, anchor**), with a Four Thirds sensor of roughly 17.3 × 13.0 mm and a 2.0× crop factor (**FACT, anchor**), about 10 effective megapixels on a Live MOS sensor at roughly 3648 × 2736 (**FACT, anchor**), native ISO of about 100–1600 (**FACT, anchor**), producing 12-bit ORF raw files (**FACT, anchor**).

Stack the physics against a modern APS-C sensor (roughly 23.5 × 15.6 mm, meaning about 60–65% more sensor area, **UNCERTAINTY** on the exact ratio for any specific modern body), and four disadvantages compound:

| Attribute | Old small sensor (E-520 class) | Modern APS-C | Why it matters (qualitative) |
|-----------|-------------------------------|--------------|------------------------------|
| Photosite area | Smaller (small sensor, ~10 MP) | Larger per pixel for similar MP, or better efficiency at higher MP | Fewer photons per pixel → lower shot-noise-limited SNR at equal exposure |
| Read-noise floor | Higher (older electronics) | Lower (modern low-noise readout) | Shadows turn to noise at a shallower push |
| Dynamic range | Narrower (squeezed both ends) | Wider | Highlights clip sooner, shadow latitude smaller |
| Effective color depth | 12-bit nominal, fewer usable deep-tone levels | 14-bit nominal, more usable levels | Less headroom for aggressive tonal/shadow stretching before posterization |
| Demosaicing era | Weaker if using old software/JPEG | Better bundled algorithms | More maze/zipper/false-color unless reprocessed in modern ACR |

**UNCERTAINTY:** Every quantitative comparison above (exact photosite area, read noise in electrons, dynamic range in stops, effective bits) is deliberately left qualitative. The anchor facts fix the E-520's format, resolution, ISO range, and bit depth; they do not fix its measured noise metrics. Do not fabricate specific numbers to fill this table — the *directions* are reliable, the magnitudes are not without a measurement source.

**INTERPRETATION — the correct expectation.** The honest professional expectation for an E-520-class file is: excellent at base ISO in good light, with genuinely pleasing files; usable and easily rescued through roughly ISO 400; visibly noisy but workable around ISO 800; and hard-pushed at ISO 1600, where modern denoising is the difference between usable and not (**UNCERTAINTY** on the exact ISO thresholds — they are image-dependent, per the anchor guidance). You should *not* expect a modern camera's clean high-ISO or its deep-shadow latitude. Expecting that leads to over-pushing files past their information floor and then blaming the denoiser.

### What Modern Processing Genuinely Adds — and Where the Line Is

There is a real, large improvement available from reprocessing old RAW files with current tools, and it comes from four distinct places, none of which violates the conservation of information:

1. **Better demosaicing.** Current ACR reconstructs the Bayer data with algorithms far better than a 2008 in-camera engine, reducing maze, zippering, and false color and extracting more true resolution from the *same* raw samples. This is not invented detail; it is a more faithful reconstruction of detail that the samples always implied.
2. **Better camera profiles.** A modern DCP profile (**FACT, anchor:** ACR provides DCP profiles for the E-520) maps the sensor's native response to accurate color better than the era's baked-in JPEG rendering.
3. **AI denoise.** By modeling noise statistically and preserving structure, AI denoise raises the usable ISO and the shadow-push latitude of an old file dramatically compared to slider-based NR — but only up to the information floor. Below that floor it invents (see next section).
4. **Non-destructive, high-bit-depth editing.** Doing the tonal work in Camera Raw on 12-bit linear data, in a 16-bit pipeline, avoids the posterization and clipping that the old 8-bit JPEG path introduced.

**The line, stated plainly:** processing can *stop wasting* captured information (points 1, 2, 4) and can *statistically suppress noise to reveal* captured information (point 3, up to the floor). It cannot *manufacture* captured information (that is point 3 *below* the floor, and all of Part 6's upscaling — invention, not recovery). The whole art of old-file work is operating hard on the first side of that line and honestly on the second.

### Why the Same Noise Is Not Equally Disturbing

The physical noise magnitude is only one input to *perceived* noise. Four modifiers dominate:

**Texture masking.** High-frequency scene detail hides high-frequency noise. Noise that is glaring in a clear blue sky is invisible in dense foliage, gravel, or fabric weave, because the eye cannot separate the random noise from the random texture. This is the same masking principle that Chapter 15 exploits for sharpening decisions. **INTERPRETATION:** this is why a "noisy" landscape often needs noise reduction *only* in its skies and smooth shadows, and why a portrait's smooth skin and out-of-focus background are the noise-critical regions while the sharp, textured hair hides noise freely.

**Tonal region.** The eye scrutinizes smooth mid-tones and expects them to be clean; it forgives roughness in very dark and very bright extremes, and it is most sensitive to fluctuation in the tones near the middle where contrast sensitivity peaks. Noise of equal amplitude reads as worse in a smooth mid-gray wall than in a near-black corner.

**Output size and viewing distance.** Per-pixel noise that is obvious at 100% on screen may be entirely invisible in a 1200-pixel web image or a print viewed at arm's length, because downsampling averages it away and the eye cannot resolve it. **RECOMMENDATION:** always judge noise at the *intended output size*, not only at 100% — 100% is for diagnosis, output size is for the decision about how much to treat.

**Subject expectation.** Viewers bring expectations. A gritty photojournalistic night frame is *expected* to be rough and the noise reads as authenticity; a commercial product shot or a beauty portrait is expected to be flawless and identical noise reads as a failure. The subject sets the tolerance.

### The Highlight-Noise Paradox

Here is the reconciliation the objectives demand. Part 1 and Chapter 16 both state, correctly, that real per-pixel noise is worst in the *shadows*: SNR falls with signal, the read-noise floor is fixed, so the darkest regions have the lowest SNR and the most visible noise. Yet photographers persistently report that noise "seems worse in the highlights" or "in the brighter areas." Both observations are true; they are describing different things. There are four non-shadow causes that produce *apparent* highlight noise:

**1. Optical / perceptual.** Bright, smooth areas are exactly where the eye is best able to *detect* small fluctuations, because the region is uniform and there is no texture to mask anything and the display is emitting the most light there. A given fluctuation amplitude is more *noticeable* on a bright uniform patch than on a dark one, even if the dark patch physically contains more noise. The noise you see in a slightly-noisy bright sky is real but small; it is conspicuous because nothing hides it.

**2. Tonal / gamma redistribution.** This is the subtle one. When you apply a tone curve that *reduces* contrast in the highlights (a shoulder that compresses bright tones, or a highlight roll-off), you compress the highlight signal while the noise rides along — and if you have simultaneously *lifted* midtones, the noise that was in the upper-midtones can be pushed into the visible bright region. More commonly, a strong highlight-recovery move (pulling down blown or near-blown highlights) *reveals* the poor, near-clipped, low-information data at the top of the range, which looks rough because it is near the sensor's ceiling where quantization and channel clipping distort it. The roughness appears "in the highlights" but it is an artifact of stretching marginal near-clip data, not of shadow-style noise.

**3. JPEG-related.** If the file is a JPEG, the smooth bright areas are where **blocking** is most visible (flat gradients coarsen into tiles) and edges in bright regions show **mosquito noise**. This looks like "highlight noise" but is compression damage, concentrated in the smooth bright zones precisely because that is where JPEG's block-DCT quantization is most conspicuous. The give-away is structure: 8×8 tiles or edge-tethered shimmer rather than random per-pixel scatter.

**4. Processing artifact.** Oversharpening amplifies whatever fluctuation exists on the bright side of edges and in bright flats; aggressive local-contrast or clarity moves do the same; a botched highlight-side denoise can leave blotching. All of these deposit visible disturbance in the bright areas and get misread as highlight noise.

**INTERPRETATION — the diagnostic rule.** When someone (including you) reports "highlight noise," test it: zoom to 200% on a genuinely smooth mid-shadow *and* on the bright area, and on a single channel. If the shadow is clearly noisier per-pixel than the highlight, the physics is intact and the "highlight noise" is one of the four effects above — find which (perceptual, tonal, JPEG, or processing) and treat *that*, not the highlights. If, exceptionally, the bright area genuinely shows more random per-pixel scatter than the shadow, suspect a specific cause: near-clip channel behavior, a strong positive exposure/curve push applied *after* capture that amplified an already-marginal highlight, or a hot/defective region — and verify against the raw. Real, physics-driven noise that is worse in the highlights than the shadows is the rare case, not the norm.

---

## Photoshop Implementation

This section is about *seeing*, not fixing. The output of every procedure here is a written diagnosis. The fixes live in Parts 4–7.

### The Inspection Setup

**FACT / RECOMMENDATION:** noise and artifacts are pixel-level phenomena and must be judged at 100% (actual pixels) or 200%. Below 100%, Photoshop's display downsampling *interpolates* the image and hides — or invents — texture, so any judgment made at fit-to-screen is unreliable for noise.

Baseline setup for a diagnosis pass:

1. Open the file and press Ctrl+1 / Cmd+1 for 100% (actual pixels). Use 200% (Ctrl++ / Cmd++ once more, or type 200 in the zoom field) for edge artifacts and demosaicing.
2. Open the Navigator panel (Window > Navigator) to move the 100% viewport quickly across the frame without zooming out.
3. Open the Channels panel (Window > Channels) — per-channel inspection is half of noise diagnosis.
4. Open the Histogram panel (Window > Histogram, Expanded View, All Channels View) to correlate what you see with the tonal distribution and to spot clipping and posterization combs.
5. If the file is a RAW/ORF, do the inspection in Camera Raw (or via Filter > Camera Raw Filter on a Smart Object) so you are looking at the actual raw-derived data, not a rendered JPEG. If it is a JPEG, note that fact first — it changes every subsequent interpretation.

### Zoom-Level Cheatsheet for Diagnosis

| Artifact | Best zoom to see it | Where to look | Confirming test |
|----------|--------------------|--------------|-----------------|
| Luminance noise | 100–200% | Smooth mid-tone / shadow | Desaturate a stamp — grit remains |
| Chroma noise | 100% | Any shadow | Raise saturation — confetti intensifies |
| Banding (readout) | 100% after shadow boost | Flat dark area, whole rows/cols | Stripes align to frame axes; per-channel isolates it |
| Posterization banding | 100% while pushing a curve | Smooth gradient (sky) | Steps follow tonal contour; histogram shows combing |
| Color blotching | 100% (does NOT worsen at 200%) | Large smooth shadow | Low-frequency; unchanged by extra zoom |
| Shadow noise | 100–200% | Darkest recoverable tones | Worst per-pixel of the whole frame |
| "Highlight noise" | 200%, compare vs shadow | Bright smooth area + a shadow | If shadow worse → not real highlight noise |
| JPEG blocking | 100–200% | Smooth gradient / flat | Regular 8×8 tile grid, axis-aligned |
| Mosquito noise | 200% | Hard edge over smooth bg | Shimmer tracks the edge, absent in flats |
| Demosaic maze | 100–200% | Fine repetitive texture | Wormy labyrinth in detail |
| Zippering | 200% | Near-horizontal/vertical or diagonal edges | Saw-tooth on/off along the edge |
| False color / moiré | 100–200% | High-freq detail (fabric, tiles) | Colors not in the scene; may shimmer |
| Oversharpening halos | 100–200% | High-contrast edges | Symmetric light/dark rims scaling with amount |
| AI-hallucinated texture | 100%, vs original | Low-info regions, skin, hair, text | Detail exists where original was pure noise |

### Procedure: Isolating Luminance from Chroma Noise

1. At 100% on a shadow, note the overall roughness.
2. In the Channels panel, click Green, then Red, then Blue in turn. On old small-sensor files, Green is usually cleanest and Blue noisiest (**FACT**, from Bayer photosite counts and blue-channel photon deficit; see Chapter 16). The grayscale grit you see per-channel is the luminance component of that channel.
3. Return to RGB. Create a stamped copy (Ctrl+Alt+Shift+E / Cmd+Option+Shift+E) and desaturate it (Ctrl+Shift+U / Cmd+Shift+U). What remains is luminance noise with the color removed. Toggle the layer to compare against the color version — the difference is your chroma noise.
4. Record two independent severities: luminance amount and chroma amount. They will drive two different sliders later.

### Procedure: Revealing Hidden Shadow Noise (and Finding the Floor)

The noise that will *appear* after you edit is often invisible in the delivered/flat version. Force it into view before you commit:

1. On the RAW in Camera Raw, or via a Curves/Exposure adjustment on a rendered file, lift the shadows hard — Shadows +100 and Blacks well up, or a steep curve on the lower quarter. This simulates the worst tonal move you might make.
2. At 100%, watch the deepest tones. Three things can appear: real recovered detail (good — the signal was there), escalating luminance+chroma noise (a treatable masking problem), and a *plateau* where pushing harder reveals only structureless noise with no new detail (that is the read-noise floor — information loss).
3. Note the tonal level at which the floor sets in. Anything you intend to darken *back* below that level need not be denoised (it will be dark). Anything you intend to hold *above* it and that shows only floor-noise is a region to leave dark, mask off, or accept as lost — not to fight.
4. Reset the exploratory adjustment. Its only purpose was diagnosis.

### Procedure: Per-Channel Banding Check

1. Boost shadows as above.
2. In Channels, inspect each channel alone at 100% over a large flat dark area. Readout banding frequently lives predominantly in one channel and shows as fine axis-aligned stripes.
3. Distinguish from posterization: watch the Histogram while pushing a curve on a smooth gradient. Gaps/combs opening in the histogram, with broad steps following the gradient's contour, mean posterization (a bit-depth/tonal issue). Fine straight stripes independent of the gradient's shape mean readout banding (a pattern-noise issue). The two go to different sections of Part 5.

### Procedure: Confirming a File Has Been Through JPEG

Before you treat "noise," rule out compression damage, because on old images the file you were handed is very often a camera JPEG:

1. At 100–200% on the smoothest gradient in the frame (sky, wall), look for the 8×8 tile grid. Its presence is definitive: this is JPEG (or JPEG-derived) data.
2. At 200% on a hard edge over a smooth background, look for mosquito shimmer tracking the edge. Present-at-edges, absent-in-flats confirms compression ringing rather than sensor noise.
3. If either is present and the original ORF exists, stop the JPEG-restoration path and go develop the RAW instead (Part 3, [16D3_Denoising_Mindset_RAW_vs_JPEG.md](16D3_Denoising_Mindset_RAW_vs_JPEG.md)). If only the JPEG exists, note that all subsequent treatment is damage-limitation, not development.

**RECOMMENDATION — a quick, single-line file check.** Before opening anything, you can triage a folder for whether the RAW originals still exist alongside the JPEGs:

`find . -type f \( -iname '*.orf' -o -iname '*.jpg' \) | sort`

And to confirm a specific file's real format and whether it carries a raw payload rather than trusting its extension (requires ExifTool):

`exiftool -FileType -FileTypeExtension -Compression -BitsPerSample yourfile.orf`

**UNCERTAINTY:** exact ExifTool tag availability varies by file and ExifTool version; if a tag is absent, it is omitted from the output rather than erroring. Verify against your installed version.

### Procedure: Distinguishing Edge-Tethered Artifacts

Three different defects all live on edges — mosquito noise, oversharpening halos, and false color — and they are constantly confused. Separate them at 200% on a high-contrast edge:

| You see | Diagnosis | Cause | Right response family |
|---------|-----------|-------|----------------------|
| Fine speckle/shimmer *cloud* around edge, fading with distance | Mosquito noise | JPEG DCT ringing | Deblock / return to RAW |
| Smooth bright rim on light side, dark rim on dark side, symmetric | Oversharpening halo | Excess USM/sharpening | Reduce/redo sharpening; return to RAW if baked in |
| Colored fringe along the edge (magenta/green/cyan) | False color or lateral CA | Demosaicing or lens CA | Defringe / better demosaic / lens profile |

**INTERPRETATION:** the fastest discriminator is color and symmetry. Halos are tonal and symmetric about the edge; false color/CA is chromatic and often side-specific or corner-growing; mosquito noise is a *textured* disturbance rather than a clean rim.

### Procedure: Auditing AI Denoise / Upscale Output for Hallucination

When you (or Part 4/Part 6) run AI denoise or upscaling, audit it before trusting it:

1. Keep the original noisy source open. Put the AI result on a layer above it and toggle.
2. At 100%, find regions the *original* showed as pure noise or as sub-floor darkness (use the shadow-boost procedure to know where those are). Any *detail* the AI shows there is invented — it cannot have come from captured signal.
3. Inspect texture-heavy regions (foliage, fabric, skin, hair) for the AI signature: over-uniform, too-regular, or subtly repeating micro-texture; hairs that merge or fork; text that becomes crisp nonsense.
4. Decide, per region, whether the invention is acceptable for the image's purpose (often fine for a small web image of a landscape; often not for a document reproduction, a scientific image, or anything represented as unmanipulated). This is a disclosure decision as much as a quality one — see Chapter 20 ([20_AI_Tools.md](20_AI_Tools.md)).

---

## Professional Workflow

### Triage Before Treatment: The Old-Shoot Sort

When you inherit a body of old files — the running case is a shoot's worth of E-520 ORFs and their camera JPEGs — do not open them in capture order and start editing. Sort them by *recoverability* first, because that determines where your time is worth spending.

1. **Confirm the source.** For each keeper, establish whether the ORF exists. Always develop from ORF over JPEG when both exist (Part 3). Use the `find` line above to map originals to JPEGs.
2. **Classify each fault as compensable or lost.** Run the compensable/irreversible test from the Terminology section. A file whose only faults are WB, mild exposure error, mild noise, and lens distortion is a full recovery. A file with blown highlights across all channels, obvious motion blur, or missed focus on the subject's eyes is triage-out or accept-as-is — no amount of denoising changes its ceiling.
3. **Find the information floor** on the keepers (shadow-boost procedure). Decide the darkest tone you will hold, and plan to let everything below it stay dark rather than fighting floor-noise.
4. **Diagnose the specific artifacts** with the zoom cheatsheet, and *write the diagnosis down* per image before choosing tools. The written diagnosis is the input to Part 7's workflow.

### A Diagnosis-First Habit

**RECOMMENDATION:** adopt a fixed, short written diagnosis format so the treatment steps become mechanical. For each image, record:

| Field | Example entry |
|-------|---------------|
| Source | ORF available / JPEG only |
| ISO / exposure error | ISO 800, ~ -1.3 stop, recoverable |
| Luminance noise | Moderate, shadows + mid-shadows |
| Chroma noise | Strong in shadows, some blotching |
| Banding | Faint horizontal, Blue channel, appears after +2 shadow push |
| Compression | None (RAW) / blocking + mosquito (JPEG) |
| Demosaic artifacts | Slight false color in distant foliage |
| Prior processing | Oversharpen halos baked into JPEG — go to RAW |
| Information floor | Detail dies ~2.5 stops below mid; hold shadows above that |
| Compensable faults | WB, exposure, lens distortion |
| Irreversible faults | None / clipped sky upper-left |
| Verdict | Full recovery / partial / accept-as-is / reject |

This is the artifact that Parts 4–7 consume. A treatment applied without it is guesswork.

### Where This Part Sits in the Series Pipeline

Diagnosis is step zero. The chain is: diagnose (this part) → decide RAW vs JPEG mindset (Part 3) → primary denoise via AI/ACR/PS tools (Part 4) → frequency-selective refinement and sharpening (Part 5) → any upscaling, with hallucination audit (Part 6) → assemble into the full E-520 project workflow (Part 7) → quality-control and cheat-sheet check (Part 8, [16D8_Quality_Control_and_Cheat_Sheet.md](16D8_Quality_Control_and_Cheat_Sheet.md)). Every arrow assumes the diagnosis from this part is correct.

---

## Common Mistakes

**Treating "noise" as one thing.** Reaching for the Luminance and Color sliders before classifying the defect. Banding, blocking, mosquito noise, demosaic maze, and hallucination are not random noise and do not respond to noise sliders; some get *worse*. Name the defect first.

**Diagnosing below 100%.** Judging noise at fit-to-screen, where display interpolation hides or fabricates texture. All noise diagnosis is at 100–200%; all *quantity* decisions are made additionally at the intended output size.

**Expecting modern quality from old hardware.** Pushing an E-520 ISO 1600 shadow as if it were a modern full-frame file, hitting the read-noise floor, and then blaming the denoiser for producing smooth mush. The mush is information loss; the fix is to not push past the floor, not stronger NR.

**Confusing "highlight noise" with real highlight noise.** Cranking highlight-region denoise or backing off exposure because the bright areas "look noisy," when the true cause is perceptual conspicuousness, a tone-curve stretch of near-clip data, JPEG blocking, or oversharpening. Real per-pixel noise is a shadow phenomenon; verify before treating the highlights.

**Fighting a JPEG as if it were a RAW.** Applying developer-grade tonal and noise moves to a camera JPEG that shows blocking and mosquito noise, when the ORF is sitting in the same folder. Go back to the RAW; reprocessing in modern ACR fixes demosaicing and avoids the JPEG damage entirely.

**Mistaking a prior processing artifact for a capture artifact.** Trying to denoise oversharpening halos, or color-NR-ing mosquito noise. These were added by a *processing* step (in-camera JPEG or a previous editor). The correct response is to return to the unprocessed raw data, not to layer a second correction on top of the first mistake.

**Over-color-reducing into blotching.** Pushing the Color slider so high that pixel-level chroma noise turns into low-frequency color blobs. Blotching needs a different, low-frequency treatment; more per-pixel color NR makes it worse.

**Trusting AI-added detail because it looks good.** Accepting invented micro-texture in regions the original showed as pure noise, then presenting the result as a recovered photograph. Audit AI output against the source; distinguish plausibility from fidelity; disclose invention where it matters.

**Spending recovery effort on irreversibly lost files.** Hours of careful work on a frame whose subject is out of focus or whose sky is clipped in all channels. Triage first; a fault in the irreversible set caps the image no matter how good the noise work is.

---

## Summary

Old small-sensor files, with the Olympus E-520 as the concrete anchor, are a distinct denoising problem not because they are "broken" but because they carry genuinely less information in exactly the regions where noise lives. Smaller photosites collect fewer photons (lower shot-noise-limited SNR), older electronics impose a higher read-noise floor (shallower shadow latitude), the two together narrow dynamic range, and 12-bit ORF data offers fewer usable deep-tone levels than a modern 14-bit file. The **directions** of these disadvantages are reliable; their **magnitudes** are image- and body-dependent and were kept as **UNCERTAINTY** rather than fabricated. The correct professional expectation is base-ISO excellence, easy rescue to roughly ISO 400, workable noise near ISO 800, and hard-pushed files at ISO 1600 where modern denoising is decisive — not modern high-ISO cleanliness.

Modern processing extracts a genuinely better image from old RAW than the old camera ever delivered, through better demosaicing, better DCP profiles, AI denoise up to the information floor, and high-bit-depth non-destructive editing. All of that operates on the honest side of a hard line: it stops wasting captured information and statistically reveals it, but it cannot manufacture information that was never captured. Below the read-noise floor, and beyond genuine resolution, any added detail is invention, not recovery.

Which errors you can fix depends on whether the corrective information survives. **Compensable:** white balance (fully deferred in RAW), mild exposure error, mild noise, and lens defects with known profiles. **Irreversible:** all-channel clipped highlights, motion blur, missed focus, and shadows crushed below the read-noise floor. Triage on this distinction before investing editing time.

Diagnosis is the skill this part builds, because treatment without it wastes detail. The defect classes — luminance noise, chroma noise, readout banding versus posterization banding, color blotching, shadow noise, apparent highlight noise, JPEG blocking, mosquito noise, demosaicing maze/zippering/false color, oversharpening halos, and AI-hallucinated texture — each have a distinct appearance, location, diagnostic zoom, and cause, and each demands a different response. Several appear together in pushed old files and must be separated on screen via 100–200% inspection, per-channel viewing, shadow-boosting to reveal hidden noise and locate the floor, and side-by-side comparison against the source.

The same physical noise is not equally disturbing: texture masking hides it in detail, tonal region governs how much the eye scrutinizes it, output size and viewing distance can erase it, and subject expectation sets tolerance. And the persistent "noise looks worse in the highlights" report is real but usually misattributed — it resolves to perceptual conspicuousness on smooth bright patches, tone-curve stretching of near-clip data, JPEG blocking and mosquito noise in bright smooth zones, or processing artifacts, rather than to shadow-style physics, which remains worst in the dark. Verify the shadow-versus-highlight per-pixel severity before ever treating the highlights.

---

## Exercises

### Exercise 1: Compensable vs Irreversible Triage

Gather ten of your own older files (or ten E-520-class files if available), mixing ISO and quality. Without editing, classify each fault you can find as compensable or irreversible using the Terminology tests. For each file, write a one-line verdict: full recovery, partial, accept-as-is, or reject.

**Success criterion:** You can predict, before touching a slider, which files are worth full effort and which are capped by an irreversible fault — and you can name the specific fault that caps each capped file.

### Exercise 2: Finding the Information Floor

Take one moderately underexposed old RAW. In Camera Raw, progressively lift Shadows and Blacks while watching a deep-shadow region at 100%. Identify the tonal level at which pushing harder stops revealing detail and only structureless noise appears.

**Success criterion:** You can point to the read-noise floor in a real file and state the darkest tone you would hold above it, distinguishing "noisy but recoverable" shadow from "below the floor" shadow.

### Exercise 3: Isolating Luminance and Chroma by Channel

On a noisy shadow, inspect the Red, Green, and Blue channels individually at 100%, then desaturate a stamped copy. Record which channel is noisiest and rate luminance and chroma severity independently.

**Success criterion:** You can separate the two noise components visually and predict which channel carries the worst noise before running any reduction.

### Exercise 4: Banding vs Posterization

Find or create banding two ways: (a) hard-push the shadows of an old RAW to reveal readout banding; (b) push a steep curve on a smooth 8-bit sky to induce posterization. Inspect both at 100% and watch the Histogram.

**Success criterion:** You can tell axis-aligned readout stripes (pattern noise) from contour-following tonal steps (posterization) and state that they need different treatments.

### Exercise 5: Is It a JPEG?

Take three files of unknown provenance. Using 100–200% inspection of a smooth gradient and a hard edge (and optionally the ExifTool line), determine which have been through lossy JPEG compression.

**Success criterion:** You can identify 8×8 blocking and edge-tethered mosquito noise and correctly separate compression damage from sensor noise.

---

## Advanced Exercises

### Advanced Exercise 1: The Highlight-Noise Autopsy

Find (or construct) an image that "looks noisy in the highlights." Systematically test the four non-shadow causes: (1) compare per-pixel severity of a smooth bright area against a smooth mid-shadow at 200% and on a single channel to check whether the shadow is actually worse (perceptual case); (2) inspect whether a highlight-recovery or tone-curve move is stretching near-clip data (tonal case); (3) check for blocking and mosquito noise (JPEG case); (4) check for oversharpening halos or over-clarity (processing case). Write which cause(s) apply and what the correct response would be for each.

**Success criterion:** You can diagnose apparent highlight noise to its true cause and explain why treating the highlights directly would be wrong for three of the four causes.

### Advanced Exercise 2: The Edge-Artifact Discrimination Drill

Assemble crops of high-contrast edges from: a heavy in-camera JPEG, an oversharpened export, a file with lateral chromatic aberration, and a clean RAW develop. At 200%, classify each edge as mosquito noise, oversharpening halo, false color/CA, or clean, using color and symmetry as discriminators. Then confirm your classification by its expected behavior (does the halo scale with a sharpening slider; does the CA grow toward corners; is the mosquito noise absent in flats).

**Success criterion:** You can reliably separate the three edge-tethered artifacts that are routinely confused, and you know which tool family each requires.

### Advanced Exercise 3: RAW Reprocessing Gain Audit

Take one older ORF (or equivalent) that you also have as the original camera JPEG. Develop the RAW in current Camera Raw with a modern DCP profile and note improvements over the JPEG in demosaicing (maze/zipper/false color), color, and tonal latitude. Then, in the same develop, deliberately push past the information floor and document where genuine recovery ends and where AI denoise, if applied, would begin inventing.

**Success criterion:** You can articulate, on a real file, exactly what modern reprocessing recovered (honest, information-preserving gains) versus where any further "detail" would be invention.

---

## Blackbelt Challenge

You are handed a folder from a 2009 trip shot on an Olympus E-520: a mix of ORF raw files and their in-camera JPEGs, ISO ranging from 100 to 1600, several frames underexposed, one sunset with a partly clipped sky, one handheld dusk street frame that may have motion blur, and one ISO 1600 interior with deep, colored shadows. The client wants a small print (roughly 25 cm on the long edge) of three images and web versions of eight. You may not open any editing sliders until you have produced a complete written diagnosis and plan.

Without treating anything yet, do the following:

1. **Source and triage.** Describe how you would map ORFs to JPEGs and decide, for each of the eleven candidate images, whether to develop from RAW or JPEG, and whether each is a full recovery, partial, accept-as-is, or reject. Give the single-line-command approach you would use to inventory the folder, and state what evidence in each file drives the RAW-vs-JPEG decision.

2. **The clipped sunset.** The sky is clipped in the brightest region. Determine, conceptually, how you would confirm whether it is clipped in all three channels or only one or two, and explain what that distinction means for recoverability. State plainly which part of this image is compensable and which is information loss, and what the *honest* options are for the lost region (leave it, darken the flat area, or fabricate — and what fabrication implies).

3. **The dusk street frame.** Describe how you would distinguish, on screen, missed focus from motion blur from mere high-ISO softness, and why the distinction determines whether this frame is worth the client's time at all. If it is motion blur, state what is recoverable and what is not.

4. **The ISO 1600 interior.** This frame has severe shadow noise, likely chroma noise and blotching, possibly readout banding after any shadow push, and — since it is dim — a real risk that the deepest shadows are below the read-noise floor. Produce the full written diagnosis using the diagnosis-first table from the Professional Workflow section. Locate the information floor procedurally. Separate every artifact class present, at the correct zoom, and state for each whether it is capture noise, pattern noise, or a compression/processing artifact. Explicitly test the client's likely complaint that "the bright lamp area looks noisy too" against the four non-shadow highlight causes and state your verdict.

5. **Old-versus-modern expectation.** For the ISO 1600 interior, explain to the client, in accurate but non-technical terms, why this file cannot be made to look like a modern camera's ISO 1600, what modern reprocessing *can* genuinely deliver, and where AI denoise would cross from revealing captured detail into inventing it. Be specific about the read-noise floor and about the difference between a 12-bit old file and a 14-bit modern one, without fabricating any measured numbers.

6. **Output-size reasoning.** For each of the three print candidates and eight web candidates, argue how the intended output size changes the *diagnosis-to-decision* step: which diagnosed defects will actually matter at 25 cm print size versus at 1200 px web, and which are safe to ignore at each size. Tie this explicitly to texture masking, tonal region, and viewing distance.

7. **Predict the failure modes.** For the ISO 1600 interior, predict what the image would look like if a careless editor: (a) ran maximum global color noise reduction (blotching); (b) hard-pushed the shadows below the floor and then AI-denoised (smooth invented mush); (c) treated the camera JPEG as the master and applied heavy tonal recovery (amplified blocking, mosquito noise, and posterization); and (d) sharpened aggressively to "recover detail" on a soft frame (halos, amplified noise, no real resolution gained). Be specific about what each failure would look like, where in the frame, and why it occurs.

This challenge requires you to synthesize old-sensor physics, the compensable-versus-irreversible distinction, the information floor, the full artifact taxonomy with its diagnostic zooms, the highlight-noise paradox, output-size-dependent perception, and the honest limits of modern and AI processing — producing a complete written diagnosis and recovery plan for a real, mixed, imperfect old shoot without touching a single slider, exactly as Part 7's workflow will consume it.
