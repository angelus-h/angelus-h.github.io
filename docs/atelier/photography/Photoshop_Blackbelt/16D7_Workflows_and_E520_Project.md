---
description: Practical, end-to-end noise reduction workflows for eight common scenarios (old JPEG, mild and heavy RAW noise, landscapes, selective sky/foreground, AI upscaling, fine-art print, stock inspection), plus a complete Olympus E-520 archival-to-print pipeline and a controlled eight-treatment comparison protocol.
---

# Noise Deep Dive Part 7: Practical Workflows and the Olympus E-520 Project

The previous six parts of this deep dive built the theory. Part 1 ([16D1_Physics_of_Digital_Noise.md](16D1_Physics_of_Digital_Noise.md)) established where noise comes from. Part 2 ([16D2_Old_Sensors_and_Visual_Diagnosis.md](16D2_Old_Sensors_and_Visual_Diagnosis.md)) taught you to read the noise of older sensors by eye. Part 3 ([16D3_Denoising_Mindset_RAW_vs_JPEG.md](16D3_Denoising_Mindset_RAW_vs_JPEG.md)) framed the strategic difference between raw and rendered starting material. Part 4 ([16D4_Camera_Raw_AI_Denoise_and_PS_Tools.md](16D4_Camera_Raw_AI_Denoise_and_PS_Tools.md)) covered the tools. Part 5 ([16D5_Frequency_Masking_and_Sharpening.md](16D5_Frequency_Masking_and_Sharpening.md)) covered frequency separation, masking, and the noise-sharpening relationship. Part 6 ([16D6_AI_Upscaling_and_Hallucinated_Detail.md](16D6_AI_Upscaling_and_Hallucinated_Detail.md)) covered AI upscaling and the difference between resolution and real detail.

This part is where all of that becomes procedure. It is the practical chapter: concrete, ordered, end-to-end workflows you can follow from the moment you open a file to the moment you deliver it. It is deliberately less about *why* (that is the job of Parts 1-6, which are cross-referenced at every step) and more about *what to do, in what order, and what each step is for*. Part 8 ([16D8_Quality_Control_and_Cheat_Sheet.md](16D8_Quality_Control_and_Cheat_Sheet.md)) closes the series with quality control and a printable cheat sheet.

---

## Learning Objectives

After completing this part, you will be able to:

1. Read any noise-reduction workflow as a sequence of *purposeful* steps rather than a recipe, identifying the intent behind each operation so that you can adapt, reorder, or skip steps when the image demands it.
2. Select the correct workflow for a given image based on three diagnostic axes — source type (RAW vs JPEG), noise severity and distribution, and output target — rather than applying one procedure to everything.
3. Execute the eight canonical workflows (A through H) end to end, stating for any step what it accomplishes and what would go wrong if it were omitted or misordered.
4. Apply the fundamental order-of-operations principle — denoise before sharpen, denoise on raw data when available, resize and output-sharpen last — and explain the specific failure mode that each ordering rule prevents.
5. Process a real Olympus E-520 ORF file through a complete archival-to-print pipeline: preserving the original, applying the correct DCP camera profile, developing, denoising, upscaling, finishing, and exporting for a Fine Art America print target under a hard file-size ceiling.
6. Distinguish nominal pixel count from resolvable detail, and judge realistically when a ~10 MP original can and cannot yield a convincing 20-30+ MP final image.
7. Control the three levers that determine JPEG file size — pixel dimensions, quality/quantization, and chroma subsampling — to bring a large export under a fixed byte ceiling (25 MB) with the least visible cost.
8. Design and run a controlled eight-treatment comparison on a single image, holding crop and output size constant, naming outputs unambiguously, and inspecting the correct image features at 100% and 200%.
9. Diagnose the characteristic failure signatures of each noise-reduction and upscaling method by inspecting smooth areas, fine texture, edges, foliage, and any text or repeating pattern.

---

## Conceptual Foundation

### How to read a workflow

A workflow is not a recipe. A recipe says "add Luminance 40." A workflow says "reduce luminance noise to the point where the smooth areas read as clean at the intended output size, using whatever value achieves that on *this* image." The number is a consequence of the goal, not the goal itself. Every step in the workflows below is written as **purpose first**: what the step is for, then how to do it. If you understand the purpose, you can adapt the step — increase it, decrease it, split it across two layers, or skip it entirely when the image does not need it.

**RECOMMENDATION:** Read each workflow twice. The first pass, follow it literally on a practice image. The second pass, ask of every step, "what happens if I skip this?" The steps whose omission you cannot predict are the steps you do not yet understand.

### The three diagnostic axes

Before you choose a workflow, diagnose the image along three axes. These axes are what distinguish workflow A from workflow H, and getting them right is more than half the job.

| Axis | Question | Consequence |
|------|----------|-------------|
| Source | Do you have the RAW (ORF), or only a rendered JPEG? | RAW gives you pre-gamma noise reduction and AI Denoise access; JPEG forces you onto rendered pixels with baked-in processing. See Part 3. |
| Noise severity and distribution | Is the noise mild and uniform, heavy and shadow-concentrated, or confined to a specific region (sky) or channel (blue)? | Determines global vs selective treatment, and how much detail you must be willing to sacrifice. See Part 2. |
| Output target | Web, fine-art print, stock agency inspection, or AI-upscaled enlargement? | Determines how clean is clean enough, how much sharpening the medium will absorb, and whether upscaling is in scope. See [22_Output_Delivery.md](22_Output_Delivery.md). |

**INTERPRETATION:** Most workflow mistakes are diagnosis mistakes. Someone applies the heavy-shadow-noise workflow to a mildly noisy file and wax-smooths it; someone applies the web workflow to a stock submission and it fails inspection at 100%. Diagnose first. The workflow follows from the diagnosis.

### Order-of-operations principles

Six ordering rules govern every workflow in this part. They are not arbitrary conventions; each prevents a specific, predictable failure. Chapter 16 ([16_Noise_Reduction.md](16_Noise_Reduction.md)) established the first two; Parts 5 and 6 extended the rest.

| Rule | Statement | Failure it prevents |
|------|-----------|---------------------|
| 1 | Denoise on raw data when the RAW exists, before gamma encoding. | Gamma encoding stretches shadow values apart and amplifies shadow noise; denoising afterward fights amplified noise. See Part 3 and [03_Camera_Raw.md](03_Camera_Raw.md). |
| 2 | Denoise before sharpening. | Sharpening amplifies high-frequency variation, which includes noise. Sharpen-then-denoise both amplifies noise and softens the sharpening. See Part 5 and [15_Sharpening.md](15_Sharpening.md). |
| 3 | Address chrominance noise before, and more aggressively than, luminance noise. | Colored speckles are the most objectionable defect and carry no real detail; leaving them until last invites over-treating luminance to compensate. |
| 4 | Denoise (and, ideally, upscale) at or near full quality/bit-depth, before the final downsample and 8-bit conversion. | Working late in the pipeline on 8-bit, resized, compressed data starves every algorithm of the information it needs. |
| 5 | Resize (resample) to the output dimensions, then apply output sharpening as the final imaging step. | Output sharpening is calibrated to a pixel structure and medium; resizing after sharpening destroys the calibration. See [22_Output_Delivery.md](22_Output_Delivery.md). |
| 6 | Never modify the archival master or the original ORF; always work on copies and derivatives. | A single destructive save can cost you the ability to re-output for a new medium later. See [06_Non_Destructive_Workflow.md](06_Non_Destructive_Workflow.md). |

**FACT:** Where a step must happen simultaneously rather than sequentially — as noise reduction and sharpening do inside the Camera Raw Detail panel — the rule still holds conceptually. Camera Raw applies both to the raw data at once, which is why the Detail panel is the single best place to satisfy rules 1 and 2 together.

### The denoise-then-upscale question

Two of the eight comparison treatments, and the entire E-520 project, hinge on one ordering choice: do you denoise first and upscale second, or upscale first and denoise second? This is worth stating up front as a principle, because it recurs throughout the workflows.

**INTERPRETATION:** Denoise-then-upscale is the default and the safer choice in the large majority of cases. The reasoning: an upscaler is a detail-synthesis engine. If you feed it noise, it will faithfully enlarge the noise, and — worse — some upscalers interpret noise grain as texture and *amplify* or *elaborate* it into a busy, artificial surface. Denoising first gives the upscaler a clean signal to enlarge. Upscale-then-denoise is occasionally preferable when the denoiser is exceptionally detail-destructive and the upscaler is noise-tolerant, because upscaling first spreads the noise across more pixels (lowering its spatial frequency) so a gentler denoise can remove it. But this is the exception. The comparison protocol at the end of this part exists precisely so you can verify, per image, which ordering your tools favor. See Part 6 for the full treatment.

---

## The Workflows

Each workflow below is a complete, ordered sequence. Every step names its purpose. The starting slider values given are **a starting point, not a universal recipe** — the correct value is always the one that achieves the stated purpose on your specific image, evaluated at 100% zoom. Where a specific number cannot be responsibly generalized, the text says so with **UNCERTAINTY** rather than inventing one.

The workflows share a common spine (diagnose, denoise, sharpen, finish, output). What differs is the source, the severity, the selectivity, and the target. Read them as variations on that spine.

### Workflow A: Old high-ISO JPEG (no RAW available)

**When to use:** You have only a rendered JPEG — an old high-ISO frame where the RAW was never saved, deleted long ago, or the camera shot JPEG-only. This is the hardest starting point because every advantage of raw data (rule 1) is gone: the noise is post-gamma, the in-camera noise reduction and sharpening are already baked in, and 8x8 DCT compression artifacts (see [22_Output_Delivery.md](22_Output_Delivery.md)) are interleaved with the sensor noise. See Part 3 for why this ceiling is lower and Part 2 for reading the resulting texture.

1. **Duplicate the file on disk before anything else.** *Purpose:* preserve the only copy you have; a JPEG re-saved is a JPEG degraded (generational loss, rule 6). Work on the copy.
2. **Open and immediately convert to a lossless working format (16-bit TIFF or PSD).** *Purpose:* stop any further JPEG generational loss during editing. Converting to 16-bit does not recover lost data, but it prevents banding when you push tones and stops re-quantization on every save. See [22_Output_Delivery.md](22_Output_Delivery.md).
3. **Assess the damage at 100% and 200%.** *Purpose:* distinguish what is sensor noise (random, fine) from what is JPEG artifact (8x8 blocking in smooth areas, mosquito ringing at edges) and from baked-in over-sharpening (halos). Your treatment differs for each. See Part 2.
4. **Try AI Denoise on the JPEG first, despite the handicap.** *Purpose:* even on rendered pixels, a learned denoiser often out-performs classic sliders and can partially suppress both noise and blocking. **FACT:** AI Denoise produces its best results on RAW; on JPEG it has less information to work with but is still frequently the strongest available tool. Evaluate critically — hallucinated detail is a real risk on already-degraded input (Part 4, Part 6).
5. **If AI Denoise is unavailable or unconvincing, use the Camera Raw Filter on a Smart Object.** *Purpose:* gain the Detail-panel controls on rendered pixels. Set Color first (rule 3) high enough to kill the color speckle — often higher than you would need on RAW because the JPEG's chroma subsampling has already smeared color and left blotches. Then raise Luminance only until the grain is unobtrusive.
6. **Consider a light per-channel pass if the blue channel is visibly the worst.** *Purpose:* concentrate smoothing where the noise lives without softening the cleaner green channel that carries most luminance detail. Use Reduce Noise (Advanced, Per Channel). See Chapter 16 and Part 4.
7. **Attack residual 8x8 blocking with a tiny, masked surface smoothing if needed.** *Purpose:* JPEG blocking in skies is a low-frequency artifact that noise reduction does not fully remove; a very small Gaussian or Surface Blur masked to the sky can hide the grid. **UNCERTAINTY:** the right radius depends on the block visibility at your output size; test at output size, do not guess.
8. **Sharpen conservatively, and only after denoising (rule 2).** *Purpose:* recover apparent crispness lost to smoothing without re-summoning the noise or the mosquito halos. Because the JPEG likely already carries in-camera sharpening, use less than you would on RAW. High Pass on a masked layer restricted to genuine edges is safer than global USM here. See Part 5 and [15_Sharpening.md](15_Sharpening.md).
9. **Add a trace of monochromatic grain as a finishing layer.** *Purpose:* a uniform, pleasant grain masks the residual patchiness and the "denoised JPEG" plastic look far better than chasing perfect smoothness. Apply after all NR and sharpening (Chapter 16, grain). Keep it subtle.
10. **Resize to the output target, then output-sharpen (rule 5).** *Purpose:* match the delivery medium. Downsampling itself is your friend here — it averages noise and hides artifacts, so a JPEG that looks rough at 100% can look clean at web size. See [22_Output_Delivery.md](22_Output_Delivery.md).
11. **Export in the target format and set realistic expectations.** *Purpose:* an old high-ISO JPEG has a hard quality ceiling. It can be made good for web and small prints; it will not become a large fine-art print. Do not over-process trying to reach a quality the source cannot support. See Part 3.

**RECOMMENDATION:** For an old JPEG, the biggest single quality gain is usually *reducing the output size*. A frame that is unusable at 100% is often perfectly good downsampled to 1500-2000 px. Choose the smallest output that satisfies the use.

### Workflow B: Old RAW/ORF, mild noise

**When to use:** You have the RAW (for the E-520, an ORF), and the noise is mild and fairly uniform — a low or moderate ISO frame, correctly exposed, no heavy shadow recovery. This is the easiest and most common case, and it is the baseline the other RAW workflows modify.

1. **Copy the ORF into the project's archival folder; never edit the original in place.** *Purpose:* the ORF is your negative (rule 6). All development is non-destructive metadata anyway, but keeping a pristine archived copy is the discipline.
2. **Open in Camera Raw and assign the correct camera profile.** *Purpose:* the DCP camera profile is the correct color and tone starting point for the sensor; it also gives the demosaic and rendering the intended baseline. **FACT:** ACR provides DCP camera profiles for the E-520. Choose the profile in the Profile browser before adjusting anything. See [03_Camera_Raw.md](03_Camera_Raw.md).
3. **Set white balance and exposure.** *Purpose:* correct exposure lets the noise reduction algorithm evaluate the true signal-to-noise characteristics; denoising a wrongly-exposed file wastes its effort on values you are about to move. See Part 4.
4. **Address chrominance noise in the Detail panel first (rule 3).** *Purpose:* remove the color speckle. The RAW default Color of 25 is often already enough for a mild file; nudge up only if speckle remains in smooth shadows at 100%.
5. **Apply light luminance noise reduction.** *Purpose:* smooth the mild grain just enough to be unobtrusive at output size, preserving fine detail. A Luminance value in the low-to-mid range is a typical starting point for mild noise; raise Luminance Detail if fine texture softens. These are starting points, not a recipe — set by eye at 100%.
6. **Apply capture sharpening in the same panel.** *Purpose:* compensate for the sensor's anti-aliasing filter and demosaic softness. Because NR and sharpening operate together on the raw data here, this satisfies rules 1 and 2 simultaneously. Use the Masking slider to keep sharpening off smooth areas. See Part 5.
7. **Open into Photoshop as a Smart Object.** *Purpose:* keep the Camera Raw development editable (double-click the Smart Object to return to ACR) — non-destructive, per rule 6. See [06_Non_Destructive_Workflow.md](06_Non_Destructive_Workflow.md).
8. **Do tonal, color, and retouching work.** *Purpose:* finish the image. With mild noise already handled at the raw stage, you rarely need any further NR in Photoshop.
9. **Resize to output and output-sharpen on a flattened copy (rule 5).** *Purpose:* deliver. See [22_Output_Delivery.md](22_Output_Delivery.md).

**INTERPRETATION:** Workflow B is the "do less" workflow. Mild noise tempts over-treatment. Resist it: a mildly noisy file that keeps its detail beats a plastic-smooth one every time (Chapter 16, common mistakes).

### Workflow C: Old RAW/ORF, heavy shadow noise (recovered shadows)

**When to use:** You have the RAW, and the noise is concentrated in the shadows — either because the frame was underexposed and you are recovering it, or because the scene had deep shadows at moderate-to-high ISO. This is the classic low-SNR-shadow problem from Chapter 16: read noise dominates the darkest values, and lifting them amplifies that noise.

1. **Archive the ORF (rule 6).**
2. **In Camera Raw, assign the DCP camera profile and set white balance.** *Purpose:* correct starting point, as in Workflow B.
3. **Run AI Denoise on the raw data before recovering the shadows.** *Purpose:* this is the pivotal move. AI Denoise on the raw file works on the pre-gamma signal, where shadow noise is still compact (rule 1). Denoising *before* you lift the shadows means you are cleaning the noise before you amplify it, not after. **FACT:** AI Denoise operates on raw data and produces its best results there. See Part 4.
4. **Now recover the shadows** (Shadows slider up, Exposure/Blacks as needed). *Purpose:* the tonal lift now amplifies a signal that has already been substantially cleaned, so the recovered shadows are far quieter than if you had lifted first. See Part 3 for why order matters here.
5. **Re-check chrominance noise after the lift (rule 3).** *Purpose:* shadow recovery can reveal residual color blotches that were invisible when the shadows were dark. Raise Color if needed.
6. **Fine-tune luminance NR for the shadows specifically.** *Purpose:* the shadows may still be the noisiest region even after AI Denoise. Rather than pushing global Luminance high (which would over-smooth the well-exposed midtones), plan to finish selectively in Photoshop (next steps). See Part 5.
7. **Apply capture sharpening with heavy Masking.** *Purpose:* on a shadow-noisy image, aggressive sharpening would re-expose the residual shadow noise. High Masking confines sharpening to real edges. See Part 5 and [15_Sharpening.md](15_Sharpening.md).
8. **Open into Photoshop as a Smart Object.** *Purpose:* editable development (rule 6).
9. **Add a selective, shadow-targeted NR layer.** *Purpose:* clean the residual shadow noise without touching the midtones and highlights. Stamp a merged layer, apply Camera Raw Filter or Reduce Noise at a strength suited to the shadows, and mask it with a shadow *luminosity mask* (white in shadows, black in highlights) so it applies only where noise lives. See Part 5 and Chapter 16, selective NR.
10. **Sharpen creatively on the subject only, excluding the shadows.** *Purpose:* draw the eye to genuine detail without amplifying whatever residual grain remains in the recovered shadows. See Part 5.
11. **Resize and output-sharpen on a flattened copy (rule 5).**

**RECOMMENDATION:** For heavy shadow recovery on an older sensor, AI Denoise on the raw file *before* the lift is the single highest-leverage step in this entire part. It is the difference between a usable recovered shadow and a mush of amplified read noise. See Part 4.

### Workflow D: Noisy landscape

**When to use:** A landscape at moderate-to-high ISO where fine detail matters everywhere — foliage, rock texture, distant tree lines — but smooth regions (sky, water) are noisy. The tension is acute: landscapes reward maximum detail, and noise reduction is the enemy of detail (Chapter 16, the fundamental trade-off).

1. **Archive the RAW (rule 6).**
2. **Assign the DCP profile, set white balance and exposure in Camera Raw.** *Purpose:* correct baseline (Workflow B).
3. **Prefer AI Denoise over heavy classic Luminance.** *Purpose:* landscapes are exactly where the detail-preservation advantage of AI Denoise pays off — it suppresses sky and shadow noise while keeping foliage and rock micro-texture that a high classic Luminance value would wax over. Evaluate foliage at 100% for hallucinated or mushy detail. See Part 4 and Part 6.
4. **Keep classic Luminance modest even after AI Denoise.** *Purpose:* over-cleaning destroys the very texture that makes a landscape read as sharp. Aim for "clean sky, intact foliage," not "clean everything."
5. **Address chrominance noise fully (rule 3).** *Purpose:* color speckle in sky and shadow is pure defect with no landscape value; suppress it aggressively.
6. **Capture-sharpen with Masking.** *Purpose:* sharpen the foliage and rock edges, not the sky. High Masking keeps the smooth sky out of the sharpening. See Part 5.
7. **Open as Smart Object; do tonal and color work.** *Purpose:* finish; landscapes often get local contrast and dodge/burn here (Chapter 08).
8. **Selective NR on the sky and any smooth water, via luminosity or a painted mask.** *Purpose:* the sky is the noise-visible region and holds no detail worth preserving, so it can take stronger smoothing than the land. Mask the NR to sky only. See Part 5, and Workflow E below for the detailed sky technique.
9. **Selective creative sharpening on the detailed land, excluding sky.** *Purpose:* maximize apparent detail where it exists without roughening the smoothed sky. See Part 5.
10. **Resize and output-sharpen for the target medium (rule 5).** *Purpose:* a landscape headed for a large print needs different output sharpening than a web version. See [22_Output_Delivery.md](22_Output_Delivery.md).

**INTERPRETATION:** The landscape workflow is fundamentally a *selectivity* workflow. The single global setting that cleans the sky will destroy the foliage; the single global setting that preserves the foliage will leave the sky gritty. Splitting the treatment by region is the whole point.

### Workflow E: Noisy sky + detailed foreground (selective NR)

**When to use:** The archetypal split-treatment image — a large smooth sky (very noise-visible, zero detail to protect) above a detailed foreground (buildings, trees, figures — full detail to protect). This workflow is Workflow D's selective core, expanded, and it is the template for any image with a clean divide between "smooth and noisy" and "detailed and clean-looking."

1. **Archive the RAW; develop in Camera Raw with the DCP profile and correct exposure (rules 1, 6).**
2. **Apply a moderate global NR in ACR calibrated to the foreground, not the sky.** *Purpose:* set the *baseline* to what the detailed foreground can tolerate without losing texture. The sky will still be a little noisy at this setting — that is intentional; you will clean it separately. See Part 5.
3. **Capture-sharpen with high Masking.** *Purpose:* sharpen foreground edges, keep the sky untouched. See [15_Sharpening.md](15_Sharpening.md).
4. **Open into Photoshop as a Smart Object.** *Purpose:* editable development (rule 6).
5. **Stamp a merged layer and apply strong NR suited to the sky.** *Purpose:* create an aggressively cleaned version — too smooth for the foreground, correct for the sky.
6. **Mask that strong-NR layer to the sky only.** *Purpose:* apply the aggressive smoothing exclusively where it belongs. Build the mask by whichever method fits the edge: a luminosity/color-range selection of the sky, Select Sky (Chapter 12), or a hand-painted gradient. Refine the horizon transition so it is invisible. See Part 5 and Chapter 13.
7. **Check the horizon and any objects crossing it (branches, spires) at 100%.** *Purpose:* the mask boundary is where selective NR betrays itself — a haloed or smeared branch against a smoothed sky is an obvious tell. Refine the mask edge or paint the mask by hand around fine intrusions. See Chapter 13.
8. **Optionally add a faint grain to the smoothed sky.** *Purpose:* a completely dead-smooth sky next to a textured foreground can look artificial; a whisper of grain reunifies the two. See Chapter 16, grain.
9. **Creative-sharpen the foreground only.** *Purpose:* maximize foreground detail without roughening the sky. Mask the sharpening to the foreground — the inverse of the sky mask. See Part 5.
10. **Resize and output-sharpen on a flattened copy (rule 5).**

**FACT:** Selective noise reduction via masks produces a result no single global setting can achieve, because noise visibility and detail value are inversely distributed across this kind of image: the sky has high noise visibility and zero detail value, the foreground the reverse. See Chapter 16, selective NR.

### Workflow F: Old image that must be AI-upscaled

**When to use:** An old, low-resolution and/or noisy image (the E-520's ~10 MP, or worse, a downsized JPEG) that must be enlarged well beyond its native pixel count — for a larger print or a high-resolution delivery. This is where denoise-then-upscale ordering (see the Conceptual Foundation) becomes the central decision, and where the resolution-vs-real-detail distinction (Part 6) governs your expectations.

1. **Archive the source (rule 6).**
2. **Denoise first, thoroughly, at the raw stage if you have the RAW.** *Purpose:* an upscaler enlarges whatever you feed it. Feed it noise and it enlarges the noise — or, worse, elaborates the grain into fake texture. A clean input is the single most important precondition for a good upscale. AI Denoise on RAW is ideal here. See Part 6 and the Conceptual Foundation.
3. **Do essential tonal/color correction before upscaling, but defer fine finishing.** *Purpose:* the upscaler should work on a correctly-toned image, but heavy local retouching is easier and cheaper on the smaller file only if it will survive enlargement — generally, do global corrections now, detailed retouching after.
4. **Export a clean, high-quality intermediate for the upscaler.** *Purpose:* feed the upscaler a lossless or near-lossless file (16-bit TIFF or high-quality PNG), not a re-compressed JPEG, so it is not enlarging compression artifacts. See [22_Output_Delivery.md](22_Output_Delivery.md).
5. **Upscale by the smallest factor that reaches your target.** *Purpose:* every doubling multiplies both real and synthesized content; a smaller factor means less hallucination risk (Part 6). For Adobe Super Resolution this is a fixed 2x; for external models (Real-ESRGAN, Remacri) choose the factor deliberately.
6. **Inspect the upscaled result at 100% and 200% for hallucinated detail.** *Purpose:* upscalers invent detail. Check foliage, hair, fabric, and especially any text or repeating pattern (bricks, tiles, fences) for invented, warped, or nonsensical structure. See Part 6 and the Experimental Protocol below.
7. **If noise survived or the upscaler introduced texture, apply a light second denoise now.** *Purpose:* this is the upscale-then-denoise correction step — used as cleanup, not as the primary denoise. Keep it gentle; the primary work was step 2. See Part 6.
8. **Do detailed finishing (retouching, dodge/burn, local contrast) on the upscaled file.** *Purpose:* finish at final resolution so retouching matches the delivered pixels.
9. **Resize to the exact output dimensions and output-sharpen (rule 5).** *Purpose:* the upscale may overshoot the target; a final controlled resample plus output sharpening tuned to the medium finishes the job. See [22_Output_Delivery.md](22_Output_Delivery.md).

**INTERPRETATION:** The order that matters most in Workflow F is denoise (step 2) before upscale (step 5). If you remember one thing from this workflow, remember that. See Part 6 for the full rationale and the exceptions.

### Workflow G: Fine-art print target

**When to use:** The output is a physical fine-art print — inkjet on cotton rag or baryta, gallery-viewed, possibly large. The medium changes the noise calculus in two directions at once: the print's dot gain and paper texture *hide* fine noise (forgiving), but the large size and close inspection *reveal* both noise and over-smoothing (unforgiving). See [21_Color_Management_Output.md](21_Color_Management_Output.md) and [22_Output_Delivery.md](22_Output_Delivery.md).

1. **Archive the RAW; develop with the DCP profile (rules 1, 6).**
2. **Denoise for a *natural* result, not a clinically clean one.** *Purpose:* on fine-art paper, a trace of luminance grain reads as organic texture, even pleasingly film-like, while over-smoothing reads as plastic and cheap. Aim for restrained luminance NR and full chrominance NR (rule 3). Prefer AI Denoise for its detail retention, then back off if it looks too clean. See Part 4.
3. **Preserve maximum real detail.** *Purpose:* the print will be inspected closely; every bit of genuine texture matters. Keep Luminance Detail high; keep classic Luminance modest. See Chapter 16, the trade-off.
4. **Capture-sharpen carefully with Masking.** *Purpose:* build a detailed base without roughening smooth tones. See Part 5.
5. **Open as Smart Object; finish tonally and locally.** *Purpose:* dodge/burn, local contrast, and color grading for the print (Chapters 08, 10).
6. **Soft-proof for the paper/printer profile.** *Purpose:* see how the print's gamut and black point will render your tones and whether shadow noise will be crushed or revealed. See [21_Color_Management_Output.md](21_Color_Management_Output.md).
7. **Consider adding a fine, film-like grain as the finishing texture.** *Purpose:* on fine-art paper, deliberate grain unifies the surface and disguises any residual patchiness far more elegantly than chasing perfect smoothness. Apply after all NR and sharpening. See Chapter 16, grain.
8. **On a flattened copy: convert to the delivery color space/bit depth as the lab requires, resize to print pixels, output-sharpen for the paper (rule 5).** *Purpose:* matte/cotton rag needs stronger output sharpening than glossy because it absorbs more ink and spreads dots. **RECOMMENDATION:** ask the lab whether they apply their own sharpening — some high-end labs want an *unsharpened* file. See [22_Output_Delivery.md](22_Output_Delivery.md).
9. **Make a small test print of a representative crop before committing to the full print.** *Purpose:* the only reliable judge of print noise and sharpening is a print. Screen evaluation systematically misjudges both. See [22_Output_Delivery.md](22_Output_Delivery.md).

**INTERPRETATION:** The fine-art workflow inverts a reflex from web work: here, a little visible noise/grain is an asset, and perfect smoothness is a liability. Print the crop and let the paper tell you.

### Workflow H: Stock-photo target (must pass agency inspection at 100%)

**When to use:** The image is destined for a stock agency whose reviewers inspect at 100% (and sometimes higher) and reject for noise, over-smoothing ("mushiness" / "artifacts"), and hallucinated or smeared detail. This is the *strictest* target: unlike a print (viewed as a whole) or web (downsampled), stock is judged at the pixel level on the full-resolution file. Both too much noise and too much noise reduction are rejection reasons.

1. **Archive the RAW; develop with the DCP profile (rules 1, 6).**
2. **Denoise to the *narrow* window between "noisy" and "over-smoothed."** *Purpose:* stock reviewers reject on both sides. You must remove objectionable noise while leaving no plastic, waxy, or smeared regions. This is the hardest balance in the whole part. Prefer AI Denoise for its wider window, but inspect relentlessly. See Part 4 and Part 6.
3. **Fully suppress chrominance noise (rule 3).** *Purpose:* color speckle is an instant rejection and carries no value.
4. **Inspect every region type at 100% specifically for over-smoothing.** *Purpose:* the failure mode unique to stock is not leftover noise (you will catch that) but the waxy look — skin, skies, and out-of-focus backgrounds that have lost their micro-texture. Back off luminance NR wherever you see it. See the Experimental Protocol and Part 8.
5. **Be cautious with AI upscaling; prefer to submit at native resolution.** *Purpose:* upscaler hallucination — invented foliage, warped patterns, smeared edges — is a classic stock rejection. If the agency accepts the native ~10 MP, submit native. If you must upscale, use the smallest factor and inspect obsessively (Workflow F, Part 6).
6. **Sharpen for crispness without halos or amplified noise.** *Purpose:* stock wants crisp but clean. Halos, oversharpening ringing, and sharpening-amplified noise are all rejection reasons. Masked, moderate sharpening only. See Part 5 and [15_Sharpening.md](15_Sharpening.md).
7. **Clone out sensor dust, hot pixels, and any stuck-pixel artifacts.** *Purpose:* stock reviewers reject for dust spots and hot pixels — common on older sensors like the E-520. Inspect the whole frame at 100% and heal them. See Chapter 17.
8. **Deliver at the agency's required color space, bit depth, and format — usually 8-bit sRGB JPEG at maximum quality.** *Purpose:* meet spec exactly. Do not resize down unless the agency requires it; stock generally wants the largest clean resolution you can honestly deliver. See [22_Output_Delivery.md](22_Output_Delivery.md).
9. **Do a final full-frame 100% pan before submitting.** *Purpose:* one pass across the entire image at 100% catches the single missed dust spot, mushy corner, or color blotch that causes a rejection. See Part 8 for the QC checklist.

**RECOMMENDATION:** For stock, when in doubt, choose slightly *less* noise reduction and slightly *smaller* output over aggressive cleaning and upscaling. Reviewers forgive a whisper of honest grain far more readily than they forgive plastic smoothing or hallucinated detail. See Part 8.

---

## The Olympus E-520 Project

This is the concrete project the whole deep-dive series has been building toward: taking a real Olympus E-520 ORF file, shot in 2008, and processing it in 2026 into a natural, photographic fine-art print for Fine Art America (FAA). It exercises Workflows B/C (RAW development and shadow handling), F (AI upscaling), and G (fine-art print), and it forces the two hard, honest questions this part must answer: *how far can you realistically enlarge ~10 MP*, and *how do you keep the export under a hard file-size ceiling*.

### The camera and the material

**FACT:** The Olympus E-520 is an entry-level DSLR from 2008 with a Four Thirds sensor (~17.3 x 13.0 mm, 2.0x crop factor), approximately 10 effective megapixels on a Live MOS sensor, producing roughly 3648 x 2736 pixel images. Native ISO is approximately 100-1600. Its raw format is ORF (12-bit). ACR provides DCP camera profiles for it. Noise typically becomes visible around ISO 400-800 and is heavy at ISO 1600 — but the precise thresholds are image-dependent, so treat those numbers as guidance and judge each frame by eye. See Part 2 for reading this sensor's noise character.

**UNCERTAINTY:** Exact figures such as the sensor's read-noise floor in electrons, its precise dynamic range in stops, and the exact per-ISO noise magnitudes are not asserted here; they vary by measurement methodology and by individual sample, and are not reliably generalizable. Where a specific number would be needed, measure your own file or consult a reputable sensor-measurement source rather than trusting a remembered figure.

### Project goals

| Goal | Requirement |
|------|-------------|
| Archival | Preserve the original ORF untouched as the negative. |
| Color foundation | Use the Olympus E-520 DCP camera profile in ACR. |
| Development | Full ACR development (WB, exposure, tone, profile). |
| Noise | Reduce noise to a natural level appropriate to the sensor and ISO. |
| Enlargement | AI upscale toward a larger final image. |
| Finishing | Photoshop tonal/local/retouch finishing. |
| Print target | Fine Art America, ~6000-7000 px on the long edge. |
| File ceiling | Final JPEG under 25 MB. |
| Aesthetic | Natural, photographic — not over-processed, not plastic, not obviously AI-upscaled. |

### The full ordered pipeline

**Stage 0 — Archive (rule 6).**

1. **Copy the original ORF into a read-only archival folder and never edit it in place.** *Purpose:* the ORF is the irreplaceable negative. All ACR edits are stored as non-destructive metadata (XMP sidecar or in the catalog), so the pixels are never touched — but a pristine archived copy protects against accidental overwrite, corruption, or a future tool that rewrites the file. See [06_Non_Destructive_Workflow.md](06_Non_Destructive_Workflow.md).

**Stage 1 — Camera Raw development (satisfies rules 1, 2, 3 together).**

2. **Open the ORF in Camera Raw and select the Olympus E-520 DCP camera profile.** *Purpose:* the DCP profile is the correct color and tone baseline for this specific sensor; it drives the demosaic and rendering to the intended starting point. Choosing it first means every subsequent adjustment builds on correct color. See [03_Camera_Raw.md](03_Camera_Raw.md).
3. **Set white balance from a neutral, then set exposure, highlights, shadows, blacks, whites.** *Purpose:* establish correct tone before denoising so the algorithm evaluates true SNR (Part 4). If the frame needs shadow recovery, hold the heavy lift until after denoise (Stage 2), following Workflow C.
4. **Address chrominance noise first (rule 3).** *Purpose:* remove color speckle; the RAW default Color is a floor, not a ceiling — raise it until smooth shadows are speckle-free at 100%.
5. **Run AI Denoise on the raw data.** *Purpose:* this is the primary noise reduction, done at the most effective possible stage — pre-gamma, on raw sensor data, with maximum information (rules 1, 4). For a 2008 Four Thirds sensor at anything above base ISO, AI Denoise typically yields a dramatically cleaner and more detailed result than classic sliders. It produces a new DNG. Evaluate at 100% for detail retention and any hallucination. See Part 4.
6. **If shadows need recovery, recover them now, after AI Denoise.** *Purpose:* lift a signal that is already cleaned, so the amplified shadows stay quiet (Workflow C, Part 3).
7. **Apply capture sharpening with Masking.** *Purpose:* build a detailed base while keeping smooth areas (sky, out-of-focus) unsharpened; because this happens in the same raw pipeline, denoise-before-sharpen (rule 2) is satisfied. See Part 5.
8. **Keep classic Luminance NR modest on top of AI Denoise.** *Purpose:* AI Denoise has done the heavy lifting; adding a high classic Luminance now would wax over the detail AI Denoise preserved. Add only a touch if residual grain remains. See Chapter 16.

**Stage 2 — Decide the enlargement path (the resolution-vs-detail decision, below).**

9. **Judge whether and how far to upscale before committing.** *Purpose:* the honest assessment in the next section governs this. Decide the target long edge (within the 6000-7000 px FAA window) and the upscale factor now, so the pipeline is deliberate rather than a series of hopeful enlargements.

**Stage 3 — AI upscale (Workflow F; rule 4 — work at high quality before final downsample).**

10. **Export a clean 16-bit intermediate (TIFF or high-quality PNG) from the denoised, developed file.** *Purpose:* feed the upscaler a lossless, fully-cleaned image so it enlarges signal, not noise or compression artifacts (Part 6). Denoise-then-upscale (see Conceptual Foundation) is the chosen order.
11. **Upscale by the smallest factor that reaches the target.** *Purpose:* fewer synthesized pixels means less hallucination. **FACT:** Adobe Super Resolution produces a fixed 2x linear enlargement — from ~3648 x 2736 that is ~7296 x 5472, i.e. roughly 40 MP nominal, which comfortably exceeds the 6000-7000 px long-edge target and can then be downsampled to it. External models (Real-ESRGAN, Remacri) let you choose the factor. See Part 6.
12. **Inspect the upscaled file at 100% and 200% for hallucinated or smeared detail.** *Purpose:* verify the enlargement did not invent foliage, warp patterns, or plasticize edges. See Part 6 and the Experimental Protocol.

**Stage 4 — Photoshop finishing (Workflow G).**

13. **Open the upscaled file in Photoshop; do local tonal work, dodge/burn, color grading.** *Purpose:* finish at (near) final resolution so adjustments match the delivered pixels. See Chapters 08, 10.
14. **Retouch: heal sensor dust, hot/stuck pixels, and distractions at 100%.** *Purpose:* 2008-era sensors and long-stored files commonly show dust and hot pixels; these are glaring in a large print. See Chapter 17.
15. **If the upscale left any texture or residual noise, apply a light, possibly masked cleanup NR.** *Purpose:* gentle post-upscale tidy-up only — the primary denoise was Stage 1 (Part 6).
16. **Optionally add a fine, film-like grain as the finishing texture.** *Purpose:* on a fine-art print, a subtle uniform grain reads as natural photographic texture and disguises any residual patchiness or upscaler smoothness — directly serving the "natural, not over-processed" goal. Apply after all NR and sharpening (Chapter 16, grain).

**Stage 5 — Output for Fine Art America (rules 4, 5; [22_Output_Delivery.md](22_Output_Delivery.md), [21_Color_Management_Output.md](21_Color_Management_Output.md)).**

17. **On a flattened copy, resize to the target long edge (6000-7000 px).** *Purpose:* hit the FAA dimension window. If the 2x upscale overshot (e.g. 7296 px), downsample to the target with Bicubic Sharper — downsampling from a slightly larger, detailed file is high quality and also averages away faint residual noise. See [22_Output_Delivery.md](22_Output_Delivery.md).
18. **Convert to sRGB and 8-bit.** *Purpose:* FAA and general web/print-on-demand delivery expect sRGB 8-bit JPEG; sRGB prevents desaturated rendering in non-color-managed contexts. **RECOMMENDATION:** verify FAA's current color-space and format requirements at submission time. See [21_Color_Management_Output.md](21_Color_Management_Output.md).
19. **Apply output sharpening as the final imaging step (rule 5).** *Purpose:* compensate for downsample and medium softening, tuned so it looks right at output — not baked into any master. See [22_Output_Delivery.md](22_Output_Delivery.md).
20. **Export JPEG under 25 MB using the three levers (below).** *Purpose:* meet the hard byte ceiling with the least visible cost.
21. **Verify the delivered file:** dimensions within the window, sRGB embedded, 8-bit, under 25 MB, and — at 100% — clean, detailed, and natural. *Purpose:* final QC before submission (Part 8).

### Realistic vs unrealistic: 10 MP to 20-30+ MP

This is the honest core of the project. FAA sells prints; a buyer may order a large size; the ~6000-7000 px target implies a nominal file of roughly 20-40 MP from a ~10 MP original. Is that legitimate?

**FACT:** Pixel count and resolvable detail are not the same thing. A 2x Super Resolution enlargement of a ~10 MP file yields a ~40 MP nominal file, but it does not contain 40 MP of *captured* detail. The real resolvable detail is bounded by what the original optics, sensor, and exposure actually recorded — the lens's resolving power, the sensor's ~10 MP sampling, the anti-aliasing filter, diffraction, focus accuracy, and the noise floor. Upscaling adds pixels and *synthesizes* plausible detail between the real samples; it cannot recover detail that was never captured. See Part 6 and [22_Output_Delivery.md](22_Output_Delivery.md).

**INTERPRETATION:** The distinction that matters is between *nominal resolution* (the pixel count) and *effective resolution* (the amount of genuine, non-invented detail). A good upscale raises nominal resolution honestly and raises effective resolution only slightly (by cleanly interpolating and gently reconstructing edges). A bad or over-aggressive upscale raises nominal resolution while *lowering* the honesty of the image — filling the new pixels with invented texture that looks like detail but is fabrication (Part 6).

When it is **realistic** to make a convincing 20-30+ MP final from ~10 MP:

- The original is sharp, well-focused, and shot at low-to-moderate ISO with a good lens — i.e. the ~10 MP is *fully resolved*, not already soft.
- The subject is forgiving of synthesized detail: organic textures, foliage at a distance, atmospheric scenes, painterly or soft-focus aesthetics, where the eye does not demand crisp micro-structure.
- The print will be viewed at a normal distance for its size. A large print is viewed from farther away, and required PPI drops with viewing distance (see the viewing-distance table in [22_Output_Delivery.md](22_Output_Delivery.md)). A 6000-7000 px file makes a large print that looks excellent at gallery distance even if it would not survive a loupe.
- You have denoised well *before* upscaling, so the upscaler enlarged clean signal (Workflow F).

When it is **unrealistic**:

- The original is soft, mis-focused, motion-blurred, or heavily noisy — upscaling a soft ~10 MP file just makes a bigger soft image, and denoising heavy noise before upscaling costs real detail, leaving little for the upscaler to enlarge honestly.
- The subject demands crisp fine structure at close inspection: architectural detail with fine repeating patterns, text/signage, product shots, or any image a stock reviewer will inspect at 100% (Workflow H). Here synthesized detail reads as fabrication or warping (Part 6).
- The buyer/medium implies close scrutiny of a very large print (a huge print viewed up close). No amount of upscaling manufactures captured detail that is not there.

**RECOMMENDATION:** For the E-520 FAA project, treat 6000-7000 px as a *nominal* target that lets FAA offer a range of print sizes, and be candid with yourself that the effective detail is that of a well-processed ~10 MP file. Choose subjects and print sizes where that is not just acceptable but genuinely beautiful. Do not market it as something it is not, and do not push the upscale so hard that it starts inventing. A clean, natural, honestly-enlarged ~10 MP frame beats an over-upscaled 40 MP hall of hallucinated detail every time. See Part 6.

### Keeping the exported JPEG under 25 MB

A 6000-7000 px 8-bit JPEG can easily exceed 25 MB at maximum quality on a detailed image, because JPEG size scales with pixel count *and* with the amount of high-frequency detail (JPEG spends bits where the image is busy; see [22_Output_Delivery.md](22_Output_Delivery.md)). Three levers control the final size, in order of preference:

| Lever | Effect on size | Cost | When to use |
|-------|----------------|------|-------------|
| Quality (quantization) | Large; the primary control | Detail/gradient artifacts at low quality | First lever — drop from max toward the 80-90% range; usually enough. |
| Pixel dimensions | Large; size scales roughly with pixel count | Less nominal resolution | Second lever — trim toward the low end of the 6000-7000 px window if quality reduction alone is not enough. |
| Chroma subsampling | Moderate | Color-edge softening (usually invisible on photos) | Third lever — enabling 4:2:0 subsampling shrinks the file with little visible cost on photographic content. |

**FACT:** JPEG chroma subsampling (4:2:0 / 4:2:2) downsamples the color channels while keeping full luminance resolution, exploiting the eye's lower sensitivity to color detail; it reduces file size at a cost usually invisible on photographs but visible on hard-edged colored graphics or text. See the JPEG compression discussion in [22_Output_Delivery.md](22_Output_Delivery.md).

**RECOMMENDATION:** Reach the ceiling in this order, checking the size after each: (1) set JPEG quality to a high but non-maximum level (start around 85-90% and verify no visible artifacts at 100%); (2) if still over 25 MB, downsample toward the lower end of the 6000-7000 px window; (3) if still marginal, allow chroma subsampling. Use Save for Web (Legacy) or Export As so you can see the exact file size before committing (see [22_Output_Delivery.md](22_Output_Delivery.md)). **INTERPRETATION:** on most E-520 fine-art frames, lever 1 alone brings a 6000-7000 px JPEG well under 25 MB with no visible quality loss; the other two levers are rarely needed. **UNCERTAINTY:** the exact quality percentage that lands under 25 MB depends entirely on the image's detail content — a busy foliage frame compresses far less than a soft sky — so read the size in the export dialog rather than trusting a fixed quality number.

---

## Experimental Protocol

To choose tools intelligently rather than by habit, you must compare them on *your* images under controlled conditions. This protocol sets up a fair, repeatable comparison of eight noise-reduction and upscaling treatments on a single image. Part 8 ([16D8_Quality_Control_and_Cheat_Sheet.md](16D8_Quality_Control_and_Cheat_Sheet.md)) provides the scoring criteria; this section provides the experimental setup and the inspection checklist.

### The eight treatments

| # | Treatment | Category | Reference |
|---|-----------|----------|-----------|
| 1 | ACR classic NR (Luminance/Color sliders) | Denoise, classic | Part 4, Chapter 16 |
| 2 | ACR AI Denoise | Denoise, AI | Part 4 |
| 3 | Photoshop Reduce Noise filter | Denoise, rendered-pixel | Chapter 16 |
| 4 | Real-ESRGAN | Upscale (with denoise) | Part 6 |
| 5 | Remacri | Upscale (with denoise) | Part 6 |
| 6 | Adobe Super Resolution | Upscale (2x, with denoise) | Part 6 |
| 7 | Denoise-then-Upscale (ordering test) | Combined | Part 6, Conceptual Foundation |
| 8 | Upscale-then-Denoise (ordering test) | Combined | Part 6, Conceptual Foundation |

**INTERPRETATION:** Treatments 1-3 are denoisers, 4-6 are upscalers (which also denoise implicitly), and 7-8 test the *ordering* question directly. Because some treatments change pixel dimensions (the upscalers) and some do not (the denoisers), the single most important fairness control is normalizing output size — see below.

### Setting up a controlled comparison

1. **Choose one representative image.** *Purpose:* the comparison is only valid within one image's noise and content. Pick a frame that contains all the feature types you will inspect: a large smooth area (sky), fine texture (foliage or fabric), hard edges (a roofline against sky), and — if at all possible — some text or a regular repeating pattern (a sign, brickwork, a fence). See the inspection list below.
2. **Start every treatment from the *same* source state.** *Purpose:* a fair test isolates the treatment as the only variable. For RAW-based treatments, develop the ORF once (profile, WB, exposure) and apply that identical development to every version — set noise reduction and sharpening to a fixed baseline, then vary only the treatment under test. Export an identical clean 16-bit intermediate for the external upscalers so they all receive the same input.
3. **Fix one comparison crop, chosen before you look at results.** *Purpose:* prevent cherry-picking. Define one crop region (by pixel coordinates) that includes the feature types from step 1, and inspect that same region in every treatment.
4. **Normalize all outputs to the same output size before comparing.** *Purpose:* this is the crux of fairness. The denoisers keep the native ~3648 x 2736; the upscalers enlarge (e.g. 2x). Comparing a native denoise against a 2x upscale at "100%" compares different things. **RECOMMENDATION:** pick one common comparison size — either upsample the denoise-only results to match the upscalers, or downsample the upscaler results to native — and view every treatment at that identical size and zoom. State which normalization you chose; the honest default for judging *detail* is to bring everything to the upscaled size so you see what each path delivers at the enlargement you actually want. See [22_Output_Delivery.md](22_Output_Delivery.md).
5. **Name outputs unambiguously and consistently.** *Purpose:* a comparison is worthless if you lose track of which file is which. Use a scheme that encodes the treatment, order, and size, for example: `E520_frame07__t1_ACRclassic__nativeUP2x.tif`, `E520_frame07__t7_DENOISE-then-UPSCALE__2x.tif`, `E520_frame07__t8_UPSCALE-then-DENOISE__2x.tif`. Keep the base name identical so the files sort together.
6. **Assemble a side-by-side view.** *Purpose:* the eye compares far better side by side than sequentially from memory. Place the eight versions as layers in one Photoshop document (or a contact sheet), aligned on the fixed crop, and toggle or tile them. **RECOMMENDATION:** view pairs that answer a specific question together — e.g. treatment 7 vs 8 (ordering), or 4 vs 5 vs 6 (upscalers) — rather than all eight at once.
7. **Inspect at 100% and at 200%.** *Purpose:* 100% shows the delivered pixel-level result; 200% exaggerates artifacts (hallucination, smearing, blocking) so they are easier to spot and attribute. Inspect the same crop at both zooms for every treatment.
8. **Score against Part 8's criteria and record the results.** *Purpose:* turn impressions into a decision. Use the scoring rubric in [16D8_Quality_Control_and_Cheat_Sheet.md](16D8_Quality_Control_and_Cheat_Sheet.md) and note which treatment wins for *this kind of image* — the conclusion is image-class-specific, not universal.

### What to inspect, at 100% and 200%

Inspect each of these feature types in every treatment. Each reveals a different failure mode; the table names what to look for and which treatments are prone to failing it.

| Feature | What to look for | Failure signature | Especially watch |
|---------|------------------|-------------------|------------------|
| Smooth areas (sky, water, out-of-focus) | Clean, even tone; no speckle, no blotch, no waxiness | Residual chroma blotches (under-denoise) or plastic/waxy patches (over-denoise); JPEG blocking if source was JPEG | Classic NR (waxiness), Reduce Noise (blotch) |
| Fine texture (foliage, fabric, hair) | Preserved micro-detail that still looks organic | Smeared/mushy texture (over-denoise) or invented, repeating, "crunchy" texture (upscaler hallucination) | Upscalers (invention), classic Luminance (mush) |
| Edges (rooflines, horizons, contours) | Clean, defined, no halo, no ringing | Softening/smearing, dark-light halos, stair-stepping | Upscalers (smear/halo), any over-sharpen |
| Foliage specifically | Leaves that stay individual and natural | Clumping into blobs (denoise) or fractal-like fake leaves (upscale) | Real-ESRGAN/Remacri tuning differences (Part 6) |
| Text / repeating pattern (signs, brick, tile, fence) | Legible text; regular, undistorted pattern | Warped, melted, or invented characters; pattern that drifts or fabricates | All upscalers — this is the clearest hallucination tell (Part 6) |
| Transitions (edge of denoised region, mask boundaries, tone gradients) | Seamless; no banding, no visible NR boundary | Banding in gradients; a visible line where selective NR stops | Selective workflows (E), 8-bit conversion done too early |

**RECOMMENDATION:** The text/repeating-pattern check is the fastest, most decisive test for upscaler honesty. If a sign becomes unreadable or a brick wall grows nonsensical mortar lines, that upscaler is inventing detail on this image and you should distrust its "detail" everywhere else in the frame. See Part 6.

**INTERPRETATION:** The denoise-then-upscale (7) vs upscale-then-denoise (8) pair usually resolves cleanly at 200%: treatment 8 tends to show noise the upscaler amplified into structured texture that the later denoise then only partly removes, leaving a busy, artificial surface; treatment 7 tends to look cleaner because the upscaler enlarged an already-clean signal. When treatment 8 wins, it is usually because treatment 7's denoiser was too detail-destructive on that image — note it and adjust. See the Conceptual Foundation and Part 6.

---

## Common Mistakes

**Choosing a workflow before diagnosing the image.** The workflows are answers to the three diagnostic questions (source, severity/distribution, target). Applying the heavy-shadow workflow to a mild file over-smooths it; applying the web workflow to a stock submission fails inspection. Diagnose, then choose.

**Denoising after gamma encoding when the RAW exists.** Editing an exported TIFF/JPEG when you still have the ORF throws away the biggest advantage you have — pre-gamma denoise on raw data (rule 1). Go back to the ORF. See Part 3.

**Sharpening before denoising.** Sharpening amplifies noise, and denoising afterward fights amplified noise while softening the sharpening (rule 2). Denoise first, always. See Part 5.

**Upscaling noisy input.** Feeding noise to an upscaler enlarges the noise and often elaborates it into fake texture. Denoise first (Workflow F, rule of denoise-then-upscale). See Part 6.

**Confusing pixel count with detail.** A 40 MP nominal file from a ~10 MP original does not contain 40 MP of captured detail. Do not promise or expect resolvable detail that was never captured. See Part 6 and [22_Output_Delivery.md](22_Output_Delivery.md).

**Over-processing toward "clean."** The E-520 project's goal is *natural*, not clinically smooth. Waxy skies, plastic foliage, and hallucinated texture all read as over-processed. A trace of honest grain beats perfect smoothness on a fine-art print (Workflow G) and passes stock inspection more often (Workflow H).

**Comparing treatments at different output sizes.** Judging a native denoise against a 2x upscale at "100%" compares different pixel scales. Normalize output size first (Experimental Protocol, step 4).

**Editing the master or the ORF in place.** One destructive save can cost you future re-outputs. Archive the original and work on copies and Smart Objects (rule 6). See [06_Non_Destructive_Workflow.md](06_Non_Destructive_Workflow.md).

**Resizing after output sharpening.** Output sharpening is calibrated to a pixel structure and medium; resampling afterward destroys the calibration (rule 5). Resize first, sharpen last. See [22_Output_Delivery.md](22_Output_Delivery.md).

**Guessing the JPEG quality that lands under 25 MB.** File size depends on the image's detail content, not just dimensions. Read the actual size in the export dialog and adjust the three levers in order. See [22_Output_Delivery.md](22_Output_Delivery.md).

**Skipping the dust-and-hot-pixel pass on old-sensor files.** The E-520 is a 2008 sensor; dust and hot pixels are common and glaring in a large print or a stock review. Pan the full frame at 100% and heal them (Workflows G, H; Chapter 17).

---

## Summary

A workflow is a sequence of purposeful steps, not a recipe. Every step in this part is written purpose-first so you can adapt it: if you understand what a step is *for*, you can change its strength, split it, or skip it when the image does not need it.

Choose the workflow by diagnosing three axes — **source** (RAW vs JPEG), **noise severity and distribution** (mild, heavy-shadow, or region/channel-specific), and **output target** (web, fine-art print, stock inspection, or AI-upscaled enlargement). Most workflow failures are diagnosis failures.

Six ordering rules govern every workflow, each preventing a specific failure: denoise on raw data before gamma (rule 1), denoise before sharpen (rule 2), chrominance before and more aggressively than luminance (rule 3), work at full quality before the final downsample (rule 4), resize before output-sharpen (rule 5), and never edit the master or original (rule 6). Where denoise and sharpen must happen together, the Camera Raw Detail panel satisfies rules 1 and 2 at once.

The eight workflows (A-H) are variations on a common spine — diagnose, denoise, sharpen, finish, output — differentiated by source, severity, selectivity, and target. The old-JPEG workflow (A) works against a hard ceiling and leans on downsampling and grain. The two RAW workflows (B, C) differ mainly in whether shadow recovery is needed — and if it is, AI Denoise *before* the lift is the highest-leverage step. The landscape and sky/foreground workflows (D, E) are fundamentally about *selectivity*: splitting treatment by region because noise visibility and detail value are inversely distributed. The upscaling workflow (F) hinges on denoise-then-upscale ordering. The fine-art print workflow (G) inverts the web reflex — a little grain is an asset, plastic smoothness a liability. The stock workflow (H) is the strictest, rejecting on both leftover noise and over-smoothing, judged at 100% on the full-resolution file.

The **Olympus E-520 project** is the concrete synthesis: archive the ORF, develop with the DCP profile, AI-denoise on the raw data (before any shadow lift), upscale denoise-first by the smallest sufficient factor, finish and retouch in Photoshop, then output to sRGB 8-bit JPEG at 6000-7000 px under 25 MB. The honest core is the distinction between **nominal resolution** (pixel count) and **effective resolution** (captured, non-invented detail): a 2x upscale of ~10 MP yields ~40 MP of pixels but only slightly more real detail. It is realistic to make a convincing 20-30+ MP final when the original is sharp and well-exposed, the subject forgives synthesized detail, and the print is viewed at appropriate distance; it is unrealistic when the original is soft or noisy, the subject demands crisp micro-structure, or the print will be scrutinized up close. The 25 MB ceiling is met with three levers in order — quality, then dimensions, then chroma subsampling — reading the actual file size rather than guessing.

The **experimental protocol** turns tool choice into evidence: one image, one fixed crop, identical source state, outputs normalized to the same size and named unambiguously, inspected side by side at 100% and 200%. Inspect smooth areas, fine texture, edges, foliage, any text or repeating pattern, and transitions — each reveals a distinct failure mode, and the text/pattern check is the fastest test for upscaler hallucination. Score with Part 8's criteria; the winning treatment is specific to the image class, not universal.

---

## Exercises

### Exercise 1: Read a workflow for purpose

Take Workflow B (mild RAW noise) and, without opening Photoshop, write next to each of its nine steps the single sentence answering "what would go wrong if I skipped this step?" Then identify the two steps whose omission you cannot confidently predict — those are the steps whose theory you should re-read (in the cross-referenced Parts 1-6) before proceeding.

**Success criterion:** You can articulate the failure consequence of every step in Workflow B, and you have identified which underlying theory you still need to shore up.

### Exercise 2: Diagnose and route

Assemble five of your own (or borrowed) files: one old high-ISO JPEG, one well-exposed low-ISO RAW, one underexposed RAW needing shadow recovery, one high-ISO landscape, and one frame with a smooth noisy sky over a detailed foreground. For each, write down the three diagnostic axes (source, severity/distribution, target) and name which workflow (A-H) you would use and why. Where two workflows could apply, explain the deciding factor.

**Success criterion:** You can route an arbitrary image to the correct workflow by diagnosis rather than habit, and justify the choice.

### Exercise 3: The ordering rules in the wild

Take one moderately noisy RAW file and produce two versions: one following rules 1, 2, and 5 correctly (denoise on raw, denoise before sharpen, resize before output-sharpen), and one deliberately violating each (denoise a rendered TIFF, sharpen before denoise, sharpen before resize). Compare at 100%. Document the specific visual difference each violation produced and match it to the failure the rule was meant to prevent.

**Success criterion:** You can see, not just recite, why each ordering rule exists.

### Exercise 4: Under the ceiling

Take any detailed 6000-7000 px image and export it to JPEG under 25 MB using only lever 1 (quality). Record the quality percentage and the resulting size. Then repeat on a busy foliage-heavy frame and on a soft sky-dominant frame. Compare the quality percentages needed for each to hit the same byte ceiling, and explain the difference in terms of JPEG's detail-dependent compression (see [22_Output_Delivery.md](22_Output_Delivery.md)).

**Success criterion:** You understand that the quality setting needed to hit a byte ceiling depends on image content, and you read the size in the dialog rather than trusting a fixed number.

---

## Advanced Exercises

### Advanced Exercise 1: Run the eight-treatment protocol

Execute the full Experimental Protocol on one real E-520 ORF (or the nearest old-sensor file you have). Produce all eight treatments from an identical source state, normalize them to a common comparison size, name them per the scheme, and assemble a side-by-side. Inspect all six feature types at 100% and 200%. Write a one-paragraph verdict naming the winning treatment for this image class and the single most decisive observation that determined it. Cross-reference Part 8's scoring criteria.

**Success criterion:** You produce a defensible, evidence-based tool choice for a specific image class, and you can point to the exact feature and zoom level that decided it.

### Advanced Exercise 2: The complete E-520 pipeline

Process one E-520 ORF through the entire project pipeline (Stages 0-5) to a delivered FAA-ready JPEG: archived original, DCP profile, AI Denoise on raw, shadow recovery after denoise if needed, denoise-then-upscale to within 6000-7000 px, Photoshop finishing including a dust/hot-pixel pass, sRGB 8-bit conversion, output sharpening, and export under 25 MB. Verify every delivery attribute. Then write an honest assessment: how much of the final file's pixel count is captured detail versus synthesized, and what largest print size you would confidently sell from it.

**Success criterion:** You can execute the full archival-to-print pipeline end to end and make an honest, defensible statement about the file's effective (not just nominal) resolution.

### Advanced Exercise 3: Ordering showdown on a hard case

Find or create a genuinely noisy old frame where denoising costs visible detail. Produce treatment 7 (denoise-then-upscale) and treatment 8 (upscale-then-denoise) with matched final settings. Determine which wins, and — critically — explain *why* in terms of the specific image: was the denoiser too detail-destructive (favoring 8), or did the upscaler amplify noise into fake texture (favoring 7)? Then adjust the losing pipeline (a gentler denoiser, a different upscaler, a masked cleanup) and see whether you can make it win.

**Success criterion:** You can predict and then verify which ordering a given image favors, and you understand the ordering choice as image-dependent rather than a fixed rule. See Part 6.

---

## Blackbelt Challenge

You are handed three E-520 files and three incompatible delivery briefs, and you must design a complete, ordered plan for each *without touching the software* — specifying workflow choice, every major step with its purpose, the noise/upscale/sharpen decisions, and the predicted failure modes if the plan is done wrong.

**File 1 — "Harbour at dusk."** ISO 1600 ORF, deliberately shot for atmosphere: a smooth graduated sky (heavy luminance and chroma noise), silhouetted masts and rigging against the sky (fine hard edges), and dark water with recovered-shadow reflections. **Brief:** a 30-inch fine-art print on cotton rag for a gallery, viewed at gallery distance.

**File 2 — "Market stall."** ISO 400 ORF, well-exposed, sharp: fruit and fabric texture (fine detail), a hand-lettered price sign (text), and a striped awning (regular repeating pattern). **Brief:** submission to a stock agency that inspects at 100% and rejects for noise, over-smoothing, hallucinated detail, dust, and warped patterns.

**File 3 — "Forgotten festival."** An old ISO 800 *JPEG only* — the ORF is gone — with baked-in in-camera noise reduction and sharpening, visible 8x8 blocking in the sky, and mosquito halos around the flags. **Brief:** a web feature at 2000 px on the long edge, plus a small 8x10 inch print.

For each file, address:

1. **Diagnosis.** State the three axes (source, severity/distribution, target) and the workflow (A-H) you select. Where more than one applies, name the deciding factor.
2. **Ordered plan with purposes.** Write the full ordered step sequence, each step naming its purpose. Make the ordering rules explicit — at each transition, which rule are you satisfying?
3. **The pivotal decision.** For File 1, justify whether and when to denoise relative to shadow recovery, and how you split treatment between sky, rigging, and water. For File 2, justify whether you upscale at all (and if so how you protect the text and the awning stripes from hallucination), and how you hit the narrow window between noisy and over-smoothed. For File 3, justify how much you can realistically achieve from a lossy JPEG, and how the two output sizes change your approach.
4. **The upscaling honesty question.** For any file you choose to enlarge, state the nominal target resolution, your estimate of the effective (captured) resolution, and the largest print size you would honestly stand behind. For any file you choose *not* to enlarge, justify that too.
5. **File-size / delivery constraints.** For File 1, plan the color space, bit depth, and whether to send the lab an output-sharpened or unsharpened file (state your assumption and how you would confirm it). For File 3's web version, plan the JPEG delivery. Reference the relevant output-preparation steps.
6. **Predicted failure modes.** For each file, describe precisely what the delivered image would look like if you (a) sharpened before denoising, (b) upscaled before denoising, and (c) over-cleaned toward maximum smoothness. Be specific about the artifact, where it would appear, and why — tie each back to the rule or principle violated.

This challenge requires you to synthesize the whole deep-dive series — the physics of noise (Part 1), reading an old sensor (Part 2), the RAW-vs-JPEG mindset (Part 3), the tools (Part 4), frequency, masking and sharpening (Part 5), upscaling and hallucination (Part 6), and the output discipline of [22_Output_Delivery.md](22_Output_Delivery.md) and [21_Color_Management_Output.md](21_Color_Management_Output.md) — into three coherent, defensible plans for three genuinely different problems, predicting both the intended results and the failure modes, without opening Photoshop. Score your own plans against the quality-control criteria in Part 8 ([16D8_Quality_Control_and_Cheat_Sheet.md](16D8_Quality_Control_and_Cheat_Sheet.md)).
