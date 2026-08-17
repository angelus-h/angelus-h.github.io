---
description: Noise Deep Dive — an eight-part supplement to Chapter 16, covering the physics of digital noise, visual diagnosis, RAW-vs-JPEG denoising, Camera Raw and AI Denoise, frequency/masking/sharpening interaction, AI upscaling and hallucinated detail, complete workflows (including a concrete Olympus E-520 project), and final quality control.
---

# Noise Deep Dive (Supplement to Chapter 16)

This is an eight-part deep-dive supplement to [Chapter 16: Noise Reduction](16_Noise_Reduction.md). Chapter 16 establishes the core concepts — noise vs grain, luminance vs chrominance, SNR, the noise/detail trade-off, and the essential Camera Raw and Photoshop techniques. This supplement goes considerably deeper and wider.

## Purpose

The goal is not to hand you a recipe book. It is to make you able to open an **unknown** image and independently:

1. **Recognize** the type of noise present.
2. **Understand** where that noise came from.
3. **Decide** how much of it is worth removing — and how much is real detail you must protect.
4. **Choose** the appropriate tool for that specific problem.
5. **Verify** that your noise reduction did not destroy the real detail.

Numeric settings appear throughout. Treat every number as a **starting point calibrated to the example, not a universal recipe.** The reasoning behind the number is what transfers between images; the number itself rarely does.

## The core principle

> You do not need to make the whole image noise-free. You remove the *disturbing* noise while *preserving* real detail — and then you confirm the real detail survived.

## The eight parts

| Part | Title | Focus |
|------|-------|-------|
| 1 | [The Physics of Digital Noise](16D1_Physics_of_Digital_Noise.md) | SNR, shot/read/thermal/fixed-pattern noise, banding, hot/stuck pixels, quantization, demosaicing and JPEG artifacts, and why "high ISO causes noise" is an oversimplification |
| 2 | [Old Sensors and the Visual Diagnosis of Noise](16D2_Old_Sensors_and_Visual_Diagnosis.md) | The late-2000s / Four Thirds (E-520) era, what old RAWs can still yield, and how to visually identify every noise and artifact type on screen |
| 3 | [The Denoising Mindset and RAW vs JPEG](16D3_Denoising_Mindset_RAW_vs_JPEG.md) | Content-dependent noise reduction, the local-NR philosophy, and why RAW-stage denoising beats TIFF and JPEG |
| 4 | [Camera Raw, AI Denoise, and Photoshop Noise Tools](16D4_Camera_Raw_AI_Denoise_and_PS_Tools.md) | Every ACR noise/sharpening parameter, AI Denoise, and the Photoshop pixel-domain toolset mapped to problem type |
| 5 | [Frequency Separation, Masking, and the Noise/Sharpening Balance](16D5_Frequency_Masking_and_Sharpening.md) | Frequency-domain separation, selective masking (strong on sky / minimal on rock), and the two-way noise↔sharpening interaction |
| 6 | [AI Upscaling and the Hallucinated-Detail Problem](16D6_AI_Upscaling_and_Hallucinated_Detail.md) | Real-ESRGAN, Remacri, 4x-UltraSharp, Super Resolution, Topaz — and how to tell restoration from invention |
| 7 | [Practical Workflows and the Olympus E-520 Project](16D7_Workflows_and_E520_Project.md) | Eight end-to-end workflows, a concrete E-520 ORF → Fine Art America pipeline, and a controlled comparison protocol |
| 8 | [Final Quality Control and the Black Belt Cheat Sheet](16D8_Quality_Control_and_Cheat_Sheet.md) | A professional QC checklist, how to choose between two similar results, and the decision cheat sheet |

## How to read this supplement

Read Parts 1–3 for the mental model, Parts 4–6 for the tools, and Parts 7–8 to put it into practice and to verify your results. Once you understand the framework, Part 8's cheat sheet and Part 7's workflows are the pages you will return to most often.
