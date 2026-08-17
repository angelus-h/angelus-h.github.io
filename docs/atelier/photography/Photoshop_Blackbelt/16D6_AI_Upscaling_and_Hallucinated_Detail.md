---
description: Noise Deep Dive Part 6 — AI upscaling for photography and the hallucinated-detail problem. Real-ESRGAN, Remacri, 4x-UltraSharp, Adobe Super Resolution (ACR Enhance), Topaz Photo AI/Gigapixel, and the modern upscaler landscape. Why upscaling a noisy image is dangerous, the correct denoise-then-upscale order, and how to recognize when an AI upscaler stops restoring real information and starts inventing it.
---

# Noise Deep Dive Part 6: AI Upscaling and the Hallucinated-Detail Problem

This is Part 6 of the eight-part Noise Deep Dive supplement to [Chapter 16: Noise Reduction](16_Noise_Reduction.md). It assumes the physics from [Part 1](16D1_Physics_of_Digital_Noise.md), the diagnostic eye from [Part 2](16D2_Old_Sensors_and_Visual_Diagnosis.md), the RAW-versus-JPEG mindset from [Part 3](16D3_Denoising_Mindset_RAW_vs_JPEG.md), the ACR AI Denoise workflow from [Part 4](16D4_Camera_Raw_AI_Denoise_and_PS_Tools.md), and the frequency-and-sharpening theory from [Part 5](16D5_Frequency_Masking_and_Sharpening.md). It feeds directly into the workflow assembly in [Part 7](16D7_Workflows_and_E520_Project.md) and the quality-control checklist in [Part 8](16D8_Quality_Control_and_Cheat_Sheet.md).

Part 6 is where noise reduction and AI upscaling collide. Upscaling and denoising are not separate topics that happen to sit near each other in the pipeline -- they are causally entangled. An upscaler is a machine that turns small structure into large structure. Noise is small structure. If you feed noise to an upscaler, it will faithfully turn your noise into large, confident, permanent structure. This chapter is about why that happens, how to prevent it, and how to recognize the moment an AI upscaler stops *restoring* your photograph and starts *inventing* a new one.

## Learning Objectives

After completing this part, you will be able to:

1. Define AI upscaling (single-image super-resolution) and distinguish it from traditional interpolation (bicubic, Lanczos), explaining why interpolation can only redistribute existing information while a trained model synthesizes plausible new information.
2. Survey the major photographic upscalers -- Real-ESRGAN, Remacri, 4x-UltraSharp, Adobe Super Resolution (ACR Enhance), and Topaz Photo AI / Gigapixel -- and compare them on photographic realism versus "digital-art" crispness, tendency to over-texture, face handling, and suitability for photographs versus illustration/game-art sources.
3. Explain precisely why upscaling a noisy image is dangerous: how a super-resolution model interprets noise grains as fine texture and "solidifies" random fluctuation into false, permanent structure.
4. Compare the two processing orders -- RAW -> Denoise -> Upscale versus RAW -> Upscale -> Denoise -- with their respective failure modes, and justify why the professionally correct default is to denoise at the RAW stage (ACR AI Denoise) *before* upscaling.
5. Describe how Adobe Super Resolution (ACR Enhance) fits a Photoshop/ACR workflow (produces a new DNG, roughly doubles linear dimensions), and how the ESRGAN-family and Topaz tools -- which are standalone or plugin-based -- slot into that same workflow honestly.
6. Define and visually recognize the specific artifacts of the false-detail problem: hallucinated detail, oversharpening, texture synthesis, edge ringing, halos, plastic/waxy skin, plastic foliage, and manufactured rock/stone texture.
7. Apply concrete tells to decide when an upscale has crossed from restoration into invention: tiling and repeating textures, invented lettering and faces, edge patterns inconsistent with the lens's optics, detail finer than the capturing lens could physically resolve, and texture uniformity that no real surface exhibits.
8. Construct a defensible upscaling pipeline for a real photograph, choosing tool, scale factor, and order of operations based on the image's noise character and its intended output ([Chapter 22: Resizing, Output Sharpening, and Delivery](22_Output_Delivery.md)).
9. Articulate the authenticity and ethics line that separates recovery from generation for fine-art, stock, documentary, and competition work, cross-referencing the framework in [Chapter 20: AI-Assisted Editing](20_AI_Tools.md) and the QC standards in [Part 8](16D8_Quality_Control_and_Cheat_Sheet.md).
10. Predict the failure modes of a given tool-and-order combination before running it, so that upscaling becomes a deliberate decision rather than a hopeful one.

---

## Conceptual Foundation

Every discussion of "increasing resolution" hides a category error. There are two completely different operations that both make an image bigger, and confusing them is the root of nearly every upscaling disaster.

The first operation is **interpolation**. When you enlarge an image with bicubic or Lanczos resampling, the software must invent values for the new pixels that sit between the original ones. It does this by mathematical estimation from the neighboring known pixels -- a weighted average, essentially. Interpolation cannot add information that was not captured. It can only redistribute and smoothly blend the information that already exists. Enlarge a photograph 4x with bicubic, and you get a photograph that is 4x larger and 4x softer -- the same total information spread across sixteen times as many pixels. This is honest. The result looks soft because it *is* soft; the fine detail was never there to begin with, and interpolation does not pretend otherwise. [Chapter 22](22_Output_Delivery.md) covers interpolation in the output-delivery context.

The second operation is **AI super-resolution**. A neural network trained on millions of paired low-resolution and high-resolution images has learned a statistical mapping: given a small, soft patch that looks like *this*, the corresponding high-resolution patch usually looks like *that*. When you feed it a low-resolution image, it does not average neighboring pixels. It predicts what the high-resolution version "should" look like, based on everything it saw during training. This is the same fundamental mechanism described for AI Denoise in [Chapter 16](16_Noise_Reduction.md) and [Chapter 20](20_AI_Tools.md): the output is a *prediction*, informed by external training data, not a *recovery* of information the sensor captured.

This distinction is the entire chapter. Interpolation is arithmetic on your photograph. Super-resolution is a model's best guess, shaped by a training set you have never seen, at what a sharper version of your photograph might plausibly contain. When the guess is good -- when the input signal is strong and clean and resembles the training data -- the result can be extraordinary, sharper and more detailed than bicubic could ever produce. When the guess is bad -- when the input is noisy, unusual, or ambiguous -- the model fills the gap with invention, and that invention looks exactly as confident and photorealistic as the parts that were real.

**INTERPRETATION.** The correct mental model for an AI upscaler is not "a better enlarger." It is "a very fast, very literal illustrator who has seen a lot of photographs and will draw a plausible high-resolution version of whatever you show it, including the parts you did not mean to show it." That illustrator does not know which parts of your image are signal and which are noise. It draws all of it with equal conviction. Your job is to make sure that by the time the illustrator sees your image, it contains only signal.

That single sentence -- *make sure the upscaler only ever sees signal* -- is why this topic belongs in the noise series and not merely in the output chapter. Noise reduction is not a step that happens to precede upscaling. It is the precondition that makes upscaling safe. An upscaler applied to a clean image restores and enhances. The same upscaler applied to a noisy image manufactures. The difference between a magnificent 4x enlargement and a hallucinated fantasy is, very often, nothing more than whether you denoised first.

---

## Terminology

### AI Upscaling (Single-Image Super-Resolution)

**Definition:** AI upscaling, or single-image super-resolution (SISR), is the use of a trained neural network to increase the pixel dimensions of an image while synthesizing plausible high-frequency detail that was not present in the input. Unlike interpolation, which estimates in-between pixel values from neighbors by fixed mathematical rules, super-resolution predicts high-resolution structure from patterns learned during training on large datasets of low/high-resolution image pairs.

**Meaning in photographic practice:** AI upscaling lets a photographer produce large prints or high-resolution deliverables from files that lack the native pixel count -- old low-megapixel cameras, tight crops, or archival scans. The trade-off is that the added detail is *generated*, not *recovered*. On strong, clean input the generated detail is usually a faithful reconstruction of what a higher-resolution sensor would have captured. On weak, noisy, or unusual input the generated detail may be fabrication that looks photographic but corresponds to nothing in the scene. This is the same recovery-versus-generation spectrum introduced in [Chapter 20](20_AI_Tools.md), applied to resolution rather than noise.

**Photoshop implementation:** Photoshop does not contain a general-purpose AI upscaler in the ESRGAN sense. The AI upscaling available inside the Adobe ecosystem is Adobe Super Resolution (ACR Enhance), which lives in Camera Raw and produces a new DNG. The ESRGAN-family tools (Real-ESRGAN and its community models such as Remacri and 4x-UltraSharp) are standalone command-line or GUI applications, or run through third-party front-ends. Topaz Photo AI and Gigapixel are standalone applications that also offer a Photoshop plug-in bridge. In every non-Adobe case, the image leaves Photoshop as a rendered file (typically 16-bit TIFF), is processed externally, and returns as a new file. The integration is a hand-off, not a native filter -- and this chapter is honest about that.

**Related concepts:** Interpolation (bicubic, Lanczos), ESRGAN, GAN (generative adversarial network), diffusion upscaler, hallucinated detail, Adobe Super Resolution.

**Common misconception:** "AI upscaling recovers detail the sensor missed." It does not recover; it predicts. At high input quality the prediction closely matches what a higher-resolution capture would have shown, so the distinction feels academic. At low input quality the prediction is dominated by the model's training priors, and the "detail" is invented. The pixels look equally real in both cases. Only the photographer knows which is which.

---

### Interpolation (Bicubic, Lanczos)

**Definition:** Interpolation is the traditional, deterministic method of resampling an image to new dimensions by computing each new pixel value as a weighted function of nearby original pixels. Nearest-neighbor takes the closest pixel; bilinear averages the four nearest; bicubic uses a 4x4 neighborhood with a cubic weighting; Lanczos uses a larger windowed-sinc kernel that better preserves edges at the cost of some ringing.

**Meaning in photographic practice:** Interpolation is the honest baseline. It never invents structure; it only spreads existing structure across more pixels. The result of a large interpolated enlargement is soft, because the captured information is finite and interpolation cannot exceed it. Interpolation has no hallucination risk whatsoever -- its failure mode is softness and, for Lanczos, mild edge ringing, not fabrication. For any context where pixel-level truth-value is paramount, interpolation is the safe enlargement method even though it looks less impressive.

**Photoshop implementation:** Image > Image Size, with the Resample method dropdown. "Preserve Details 2.0" is Adobe's edge-aware enlargement method and sits between pure interpolation and AI super-resolution -- it uses some learned components but is far more conservative than a GAN upscaler. Bicubic Smoother is the classic choice for enlargement. See [Chapter 22](22_Output_Delivery.md) for the full resampling discussion.

**Related concepts:** AI upscaling, resampling, Preserve Details 2.0, edge ringing, sinc function.

**Common misconception:** "Bicubic is obsolete now that AI upscalers exist." Bicubic is not obsolete; it is *safe*. When you need a modest size increase (10-40%), when truth-value matters, or when the source is already clean and detailed, interpolation introduces zero fabrication risk. AI upscaling is the right tool for large factors on strong input, not a universal replacement.

---

### ESRGAN and the ESRGAN Family

**Definition:** ESRGAN (Enhanced Super-Resolution Generative Adversarial Network) is a super-resolution architecture that uses a generative adversarial network -- a generator that produces high-resolution output and a discriminator that judges whether that output looks like a real high-resolution image. Training the two against each other pushes the generator toward output that is perceptually convincing rather than merely numerically close to the target. Real-ESRGAN is a widely used practical successor designed to handle real-world degraded images. Remacri and 4x-UltraSharp are community-trained ESRGAN-architecture models with different training data and aesthetic characteristics.

**Meaning in photographic practice:** GAN-based upscalers produce crisp, high-contrast, detailed output that looks impressive at a glance. Because the discriminator rewards "looks real and sharp," these models have a strong bias toward *adding* texture and edge contrast -- which is exactly the bias that makes them dangerous on noisy input and prone to the "digital-art" look on photographs. Many of these community models were trained partly or heavily on illustration, anime, game textures, or heavily processed imagery, which biases their output toward a rendered, synthetic aesthetic rather than the softer, tonally continuous look of a real photograph.

**UNCERTAINTY.** The exact training datasets, scale factors, and internal parameters of specific community models (Remacri, 4x-UltraSharp, and the many other named ESRGAN variants circulating in the community) are not authoritatively documented in a form this manual can cite with confidence. Model names, versions, and behavior change frequently, and the same name may refer to different weights in different distributions. Treat all specific claims about these models as descriptions of *commonly reported behavior*, not verified specifications, and test the actual model version you have on your own images.

**Photoshop implementation:** None natively. Real-ESRGAN and its relatives run as standalone executables (a command-line binary is common) or through GUI front-ends. The Photoshop workflow is: export a clean 16-bit TIFF from Photoshop/ACR, process it in the external tool, and open the result back in Photoshop for finishing. See the Photoshop Implementation section below for the hand-off details and an illustrative command line.

**Related concepts:** GAN, Real-ESRGAN, Remacri, 4x-UltraSharp, discriminator bias, texture synthesis, digital-art look.

**Common misconception:** "A GAN upscaler is objectively better than interpolation because the numbers are better." GAN upscalers are optimized for *perceptual* plausibility, not fidelity to the actual scene. They can score worse on strict fidelity metrics while looking sharper, precisely because they add convincing detail that may not correspond to reality. Perceptual sharpness and factual accuracy are different goals, and GANs optimize the former.

---

### Real-ESRGAN

**Definition:** Real-ESRGAN is a practical super-resolution model built on the ESRGAN architecture and trained with a synthetic degradation pipeline intended to mimic the blur, noise, and compression artifacts of real-world images, so that it handles imperfect input better than the original ESRGAN. It commonly ships with a general model and a variant tuned for anime/illustration, and is frequently distributed as a portable executable.

**Meaning in photographic practice:** Real-ESRGAN is a reasonable general-purpose upscaler with some built-in tolerance for degraded input. Because it was trained with degradation modeling, it will attempt to clean up minor noise and compression as it upscales -- which is a double-edged property. On mildly imperfect input it can produce clean, sharp results. On genuinely noisy input its degradation modeling is not a substitute for proper denoising, and it will still solidify residual noise into texture. Its general model tends toward a somewhat smoothed, "processed" look on skin and fine texture.

**UNCERTAINTY.** Specific scale factors (commonly 4x, sometimes with a 2x option), model filenames, and the presence or absence of a built-in denoise-strength control depend on the exact release you are using. Do not assume; check the version's own documentation and options.

**Photoshop implementation:** Standalone; hand-off as described above. An illustrative invocation appears in the Photoshop Implementation section.

**Related concepts:** ESRGAN family, degradation modeling, denoise-then-upscale order, anime model bias.

**Common misconception:** "Real-ESRGAN denoises, so I don't need to denoise first." Its degradation-aware training gives it some noise tolerance, not a proper denoiser. Feeding it a high-ISO file and relying on the model to sort out the noise produces solidified noise texture. Denoise at the RAW stage first (see the ordering discussion below).

---

### Remacri

**Definition:** Remacri is a community-trained ESRGAN-architecture upscaling model popular for producing sharp, detailed enlargements with a strong tendency to enhance and add fine texture. It is one of many named community models distributed as loadable weights for ESRGAN-compatible front-ends.

**Meaning in photographic practice:** Remacri is prized in the upscaling community for "crunchy," detailed output. That same aggressiveness is its risk on photographs: it leans toward adding texture and micro-contrast, which flatters some subjects (fabric, foliage at a distance, architectural detail) and destroys others (smooth skin, gradients, out-of-focus areas) by injecting texture where the scene had none. It is more of a "detail-enhancer" aesthetic than a neutral reconstructor.

**UNCERTAINTY.** Remacri's training data, exact scale factor, and provenance are not authoritatively documented in a citable form. Reports of its behavior are community consensus, not verified specification. Different distributions of "Remacri" may not be identical weights. Verify on your own test images.

**Photoshop implementation:** Loaded as a model into an ESRGAN-compatible tool; standalone hand-off.

**Related concepts:** ESRGAN family, 4x-UltraSharp, texture synthesis, over-texturing, digital-art look.

**Common misconception:** "Remacri is the best upscaler because it's the sharpest." Sharpest is not best for photography. Remacri's texture-adding bias is frequently *too much* for realistic photographic output, producing the manufactured-texture and over-crisp look discussed in the false-detail section. It can be an excellent choice for illustration or stylized work and a poor one for a natural portrait.

---

### 4x-UltraSharp

**Definition:** 4x-UltraSharp is another widely circulated community ESRGAN-architecture model, generally reported to produce clean, sharp 4x enlargements with somewhat more restraint than the most aggressive texture-adding models, aiming for a balance between detail and naturalness.

**Meaning in photographic practice:** 4x-UltraSharp is often recommended as a more photograph-friendly community model than the most aggressive options, producing crisp results with fewer of the extreme over-texturing artifacts. It still carries the general GAN bias toward adding edge contrast and can still oversharpen and manufacture texture on ambiguous or noisy input. "More restrained than Remacri" is not the same as "safe on noise."

**UNCERTAINTY.** As with all community models, the exact training data, provenance, and even which weights a given download actually contains are not authoritatively documented. The "4x" in the name indicates the intended scale factor; treat everything else as reported behavior to be verified.

**Photoshop implementation:** Loaded as a model into an ESRGAN-compatible tool; standalone hand-off.

**Related concepts:** ESRGAN family, Remacri, Real-ESRGAN, GAN bias.

**Common misconception:** "4x-UltraSharp is neutral." No GAN upscaler is neutral. It is *comparatively* restrained, which is a real and useful property, but it still generates detail and still requires clean input to behave.

---

### Adobe Super Resolution (ACR Enhance)

**VERSION NOTE:** Adobe introduced Super Resolution as part of the Enhance feature in Adobe Camera Raw and Lightroom in 2021. Its behavior, interface placement, processing speed, and the exact conditions under which it is available have evolved across updates and are bundled with the broader "Enhance" panel (which also includes Raw Details / demosaic enhancement and, in current versions, AI Denoise). Verify the current implementation against your Camera Raw version.

**Definition:** Adobe Super Resolution is a machine-learning upscaling feature within Camera Raw's Enhance function. **FACT.** It approximately doubles the image's linear dimensions -- roughly 2x width and 2x height, which yields approximately 4x the total pixel count -- and writes the result to a new DNG file rather than modifying the original non-destructively in place.

**Meaning in photographic practice:** Adobe Super Resolution is the most conservative and most workflow-integrated of the AI upscalers a photographer is likely to use. Because it operates within the RAW pipeline and outputs a DNG, it preserves the raw-editing latitude of the file and can be combined with ACR's other Enhance operations. It is deliberately restrained compared to the aggressive community GAN models: it aims for a clean, natural doubling rather than a dramatic detail-invention. This restraint makes it the safer default for realistic photographic work, at the cost of the more spectacular (and more fabricated) results the ESRGAN family can produce.

**Photoshop implementation:** In Camera Raw, select the image, choose Enhance (right-click, or the Enhance button/menu), and enable Super Resolution in the Enhance dialog. **VERSION NOTE:** In current versions the Enhance dialog exposes Super Resolution together with Raw Details and Denoise; the available checkboxes depend on file type (RAW versus rendered) and version. The output is a new DNG that then develops normally in ACR. Because it is RAW-stage, it is the natural place to combine denoise and upscale in a single controlled pass -- see the ordering discussion.

**Related concepts:** Enhance (ACR), Raw Details, AI Denoise ([Part 4](16D4_Camera_Raw_AI_Denoise_and_PS_Tools.md)), DNG, denoise-then-upscale order.

**Common misconception:** "Adobe Super Resolution and Real-ESRGAN do the same job, so use whichever." They occupy different points on the aggressiveness/fidelity spectrum. Adobe's is conservative, RAW-integrated, and produces natural photographic results at a fixed roughly-2x-linear factor. The ESRGAN family is more aggressive, standalone, offers larger factors and swappable aesthetics, and carries higher hallucination risk. Choose based on how much fabrication you can tolerate and how large a factor you need.

---

### Topaz Photo AI / Gigapixel

**VERSION NOTE:** Topaz Labs distributes several relevant products. Gigapixel AI is a dedicated upscaler; Photo AI combines upscaling ("Enhance Resolution"), noise reduction, and sharpening into one application with automatic subject/model detection. The product lineup, model names, feature boundaries, and pricing have changed repeatedly, and Topaz periodically renames and reorganizes models and even products. Verify the current product and model set against your installed version.

**Definition:** Topaz Photo AI and Gigapixel are commercial standalone applications (also offering a Photoshop plug-in bridge) that use proprietary neural models for upscaling, denoising, and sharpening. They typically offer multiple named models tuned for different subject types (for example, general photographic content, faces, low-resolution sources, compression-damaged sources, and sometimes "art"/CG sources) and allow user-adjustable strength.

**Meaning in photographic practice:** Topaz's key practical advantages are (1) subject-aware model selection, including dedicated face-recovery handling, (2) an integrated denoise-plus-upscale pipeline in a single application, and (3) user controls over strength so the photographer can dial back the aggressiveness. The integrated pipeline is convenient but can also blur the line between denoise and upscale in ways the photographer must supervise: if Topaz denoises internally *and* upscales, the order and strength of those internal steps matter and are not always fully transparent. The face models can produce excellent results on degraded portraits and can also produce the uncanny "different person" effect when the input face is too small or noisy to constrain the model.

**UNCERTAINTY.** The specific internal architectures, training data, and exact model behaviors of Topaz's proprietary models are not publicly documented in a citable form. Model names and their behavior change between releases. Do not represent any specific Topaz model's internals as known; describe only observed output behavior on your own tests.

**Photoshop implementation:** Standalone application, or invoked as a Photoshop plug-in (Filter menu) that round-trips the current layer through the external engine and returns the result. For RAW work the cleaner path is usually to denoise in ACR first, export a 16-bit TIFF, and process in Topaz -- rather than letting Topaz both denoise and upscale a rendered file blind.

**Related concepts:** Gigapixel, face-recovery models, integrated denoise-upscale, plug-in bridge, subject-aware models.

**Common misconception:** "Topaz does everything, so I can throw a noisy JPEG at it and get a clean large print." Topaz is powerful, but a noisy, compressed JPEG is the worst-case input: low signal, baked-in artifacts, and no RAW latitude. The best results come from giving Topaz the cleanest possible input (RAW-denoised, 16-bit), not from asking it to rescue a degraded file in one shot.

---

### Other Modern Photographic Upscalers (SwinIR, SUPIR, and Beyond)

**Definition:** Beyond the ESRGAN family and the commercial tools, the super-resolution field includes transformer-based models (SwinIR uses a Swin Transformer backbone) and diffusion-based restorers (SUPIR and similar approaches use large generative diffusion priors, sometimes guided by text). These represent newer architectural directions than the GAN-based ESRGAN family.

**Meaning in photographic practice:** These tools exist and can produce excellent or spectacular results, and the field moves quickly. Diffusion-based restorers in particular can synthesize very convincing detail because they draw on extremely large generative priors -- which is also precisely why they carry the *highest* hallucination risk of any category discussed here. The more powerful the generative prior, the more confidently the model will invent plausible detail where the input is ambiguous. A text-guided diffusion upscaler can be steered toward or away from fabrication, but by its nature it is generating, not recovering.

**UNCERTAINTY.** This manual mentions these tools for completeness and does not benchmark them. Their capabilities, availability, hardware requirements, and behavior vary enormously and change rapidly. Do not overclaim about any specific model's quality or safety; the architectural point -- diffusion priors maximize both quality and fabrication risk -- is the durable takeaway.

**Related concepts:** SwinIR, SUPIR, diffusion models, generative prior, text-guided restoration, hallucination risk.

**Common misconception:** "The newest, most powerful model is always the best choice for photography." For fidelity-sensitive work, the most powerful generative prior is often the *worst* choice, because it fabricates most confidently. Power and safety are inversely related in this domain.

---

### Hallucinated Detail

**Definition:** Hallucinated detail is high-frequency structure -- texture, edges, features -- that appears in an AI-processed image but did not exist in the original captured signal. In upscaling, it is the detail the model invents to fill the gap between what the low-resolution input actually resolved and what a high-resolution image "should" look like. The term is borrowed from the machine-learning sense of "hallucination": confident output not grounded in the input.

**Meaning in photographic practice:** Hallucinated detail is the defining risk of AI upscaling. It is not a bug or an artifact in the traditional sense -- it is the *intended function* of the model, applied where it should not be. The model was built to synthesize plausible detail; hallucination is that function operating in a region where no real detail existed to guide it. The danger is that hallucinated detail is photorealistic and confident. It does not look like an error. It looks like fine detail. Only by comparison with the source, by reasoning about the optics, or by spotting statistical impossibilities (repetition, uniformity) can it be identified. This is the resolution-domain analogue of the AI Denoise hallucination discussed in [Chapter 20](20_AI_Tools.md).

**Photoshop implementation:** The defense is procedural, not a filter. Inspect at 100% and compare against the source ([Part 8](16D8_Quality_Control_and_Cheat_Sheet.md) provides a checklist). Where hallucination is unacceptable, use conservative tools (interpolation, Adobe Super Resolution) rather than aggressive GANs or diffusion.

**Related concepts:** Texture synthesis, false detail, recovery vs generation, optical resolution limit, model prior.

**Common misconception:** "If I can't tell it's hallucinated, it isn't a problem." Undetectability is precisely what makes it a problem in truth-value-sensitive contexts. The fact that a viewer cannot distinguish invented detail from captured detail is the reason disclosure and deliberate tool choice matter -- the photographer is the only one who knows.

---

### Texture Synthesis and Over-Texturing

**Definition:** Texture synthesis is the generation of surface texture (grain, weave, pores, stippling, foliage micro-structure, rock granularity) by a model. Over-texturing is the application of that synthesized texture too strongly, or in places where the real surface had little or no texture, producing surfaces that read as artificially busy or uniformly patterned.

**Meaning in photographic practice:** GAN upscalers, especially the aggressive community models, are strongly biased toward adding texture because their discriminators learned that "real high-resolution images have lots of fine texture." Applied to a genuinely detailed surface, this can be flattering. Applied to a smooth surface (sky, skin, water, defocused background) or to noise, it fabricates texture that never existed. Over-textured foliage and rock are the most common tells because natural foliage and rock have *irregular, non-repeating* structure that a model tends to render as *regular, repeating, uniform* structure.

**Related concepts:** Over-texturing, plastic foliage, manufactured rock texture, hallucinated detail, uniformity tell.

**Common misconception:** "More texture means more detail means better." Real detail is *structured and meaningful* (this leaf, this pore, this crack). Synthesized texture is *statistically plausible but arbitrary*. The eye can often feel the difference even when it cannot articulate it: the over-textured image looks busy and slightly wrong rather than genuinely detailed.

---

### The Digital-Art Look

**Definition:** The "digital-art" look is the characteristic appearance of over-processed AI upscaling: excessive edge contrast, high micro-contrast everywhere, slightly plasticky surfaces, uniform crispness across the whole frame regardless of the original focus plane, and a general resemblance to a rendered illustration or game asset rather than a photograph. It arises when a model biased toward crispness and texture is applied to photographic content.

**Meaning in photographic practice:** Photographs have properties that distinguish them from illustration: a focus plane with real depth-of-field falloff, tonal continuity in smooth areas, optically consistent detail that respects the lens's resolution, and non-uniform sharpness. Aggressive upscalers erase these properties -- they sharpen the out-of-focus background as confidently as the in-focus subject, they add texture to smooth gradients, and they render everything at the same high micro-contrast. The result no longer reads as a photograph, even if no single region is obviously wrong.

**Related concepts:** Over-texturing, oversharpening, uniform crispness, depth-of-field violation, plastic skin.

**Common misconception:** "It just looks 'HD' / high quality." The digital-art look is not high quality; it is a specific *style* of degradation that happens to superficially resemble sharpness. To a trained eye -- and increasingly to a general audience -- it reads as obviously AI-processed.

---

## Theory

### Why Upscaling Amplifies Noise into Structure

Recall the core physics from [Part 1](16D1_Physics_of_Digital_Noise.md): noise is random, high-spatial-frequency variation. It has no consistent structure from pixel to pixel; a bright noise pixel next to a dark noise pixel is pure chance. Recall also, from [Part 5](16D5_Frequency_Masking_and_Sharpening.md), that fine detail also lives in the high spatial frequencies, and that this frequency overlap is the fundamental reason no denoiser and no sharpener can perfectly separate noise from detail.

An AI upscaler is, in effect, a very sophisticated high-frequency *generator* guided by the input. It looks at local patterns and asks, "what high-resolution structure usually corresponds to a low-resolution patch that looks like this?" When the patch contains real, structured detail, the model extends that structure plausibly. When the patch contains noise, the model has a problem it is not designed to recognize: it cannot tell that the pixel-to-pixel variation it sees is meaningless. To the model, a cluster of noise grains *looks like* the low-resolution appearance of some fine texture. So it does what it always does -- it synthesizes the high-resolution version of that "texture."

**FACT.** A super-resolution model does not contain a noise detector. It maps input patches to output patches. Noise in the input is treated as signal because the model has no mechanism to classify it otherwise.

This is where the word "solidify" earns its place. Noise is *temporary and random*: shoot the same scene twice and the noise pattern is completely different, because it is stochastic. When you upscale noisy input, the model takes that random, ephemeral pattern and renders a large, confident, high-resolution interpretation of it. The random speckle becomes a definite little clump of "texture." The chance arrangement of three bright pixels becomes a rendered "detail." The noise, which was formless and would have been trivially removed by a denoiser a moment earlier, is now baked into permanent large-scale structure that no denoiser can remove -- because it is no longer noise. It has been promoted to structure. It looks exactly like real detail, at the wrong scale, corresponding to nothing.

**INTERPRETATION.** Think of it as the difference between a smudge on a window and a painting of that smudge. The smudge (noise) can be wiped away. Once an artist has faithfully painted the smudge at high resolution and hung it on the wall (upscaled), it is a permanent feature of the artwork. You cannot "denoise" a painting of noise; you can only paint over it. Upscaling noise converts a removable defect into an unremovable one.

There is a second, compounding mechanism. Many aggressive upscalers effectively sharpen as they enlarge -- they boost local contrast to sell the impression of detail. Sharpening amplifies high-frequency variation, and noise *is* high-frequency variation (this is the noise-sharpening tension established in [Chapter 15](15_Sharpening.md) and [Chapter 16](16_Noise_Reduction.md)). So a noisy input to an aggressive upscaler is hit twice: the noise is first interpreted as texture and synthesized into structure, and then that structure is contrast-boosted to make it "pop." The result is not merely noisy -- it is aggressively, decoratively, permanently noisy, with the noise dressed up as intricate detail.

### Chrominance Noise Under Upscaling

[Chapter 16](16_Noise_Reduction.md) established that chrominance noise -- random colored speckle -- is more objectionable than luminance noise and has no aesthetic value. Under upscaling, chrominance noise is especially destructive, because the model may synthesize *colored structure* from it: little regions of consistent false color, color fringing along invented edges, and blotchy chromatic patches in what should be neutral smooth areas. Where a denoiser would have collapsed the random color speckle toward the correct local color, the upscaler instead may commit to and enlarge the wrong colors. **RECOMMENDATION.** Because chrominance noise can be suppressed aggressively with little loss of real detail (it is almost never real color information), always ensure color noise is thoroughly handled at the RAW stage before any upscale. It is the cheapest noise to remove and the ugliest to have amplified.

### The Two Orders: Denoise-Then-Upscale vs Upscale-Then-Denoise

There are two possible orderings when an image needs both operations, and they are not equivalent.

**Order A -- RAW -> Denoise -> Upscale.** Clean the signal first, then enlarge the clean signal. The upscaler sees only structure it should try to reconstruct. No noise is available to be interpreted as texture. This is the order that respects the physics: remove the random variation while it is still random and removable, then let the model work on what remains, which is signal.

**Order B -- RAW -> Upscale -> Denoise.** Enlarge first, then try to clean up afterward. By the time the denoiser runs, the noise has already been solidified into structure by the upscaler. The denoiser now faces a much harder problem: the "noise" is no longer high-frequency random speckle that a smoothing algorithm targets -- it is coherent, mid-frequency, structured false detail that looks like real texture. A denoiser cannot remove it without also removing the real detail at the same scale, because they are now genuinely indistinguishable (the whole point of the false-detail problem). Order B also enlarges the noise before removing it, meaning any residual noise is now physically larger and coarser, which is exactly the kind of noise that is most visible and hardest to hide.

**FACT.** The two orders produce different results because both operations are nonlinear and neither commutes with the other. Denoising and upscaling do not "cancel" or "average out" regardless of order; the order determines what each model sees as its input, and each model's output is entirely determined by its input.

| Aspect | Order A: Denoise then Upscale | Order B: Upscale then Denoise |
|--------|-------------------------------|-------------------------------|
| What the upscaler sees | Clean signal only | Signal plus noise |
| Noise interpreted as texture? | No -- noise already removed | Yes -- noise solidified into false structure |
| Denoiser's task | Standard, on original-scale random noise (easy) | Remove structured false detail (near-impossible without killing real detail) |
| Residual noise scale | Small (removed before enlargement) | Large (enlarged before removal) |
| Detail preservation | Good -- upscaler reconstructs real structure | Poor -- final denoise must smooth the false structure and takes real detail with it |
| Recommended? | Yes | No |

**RECOMMENDATION.** Denoise first, upscale second. This is not a stylistic preference; it follows directly from how the two models process their inputs. The only reason to ever consider Order B is a tool that offers *only* upscaling and no upstream denoise option -- in which case the correct fix is to add a denoise step upstream, not to accept Order B.

### The Ideal: Denoise at the RAW Stage, Then Upscale

The best available order is more specific than "denoise first." It is: **denoise at the RAW stage with ACR AI Denoise, then upscale.** The reasoning combines everything from the earlier parts:

1. **RAW-stage denoise is the most effective denoise** ([Chapter 16](16_Noise_Reduction.md), [Part 3](16D3_Denoising_Mindset_RAW_vs_JPEG.md), [Part 4](16D4_Camera_Raw_AI_Denoise_and_PS_Tools.md)). It operates on the linear sensor data before gamma encoding amplifies shadow noise, with full bit depth and before demosaicing artifacts are locked in. AI Denoise at this stage preserves the most real detail at a given level of noise suppression.
2. **The upscaler then receives the cleanest, most detail-rich signal possible.** It has real structure to reconstruct and no noise to mistake for texture.
3. **Because the input is clean, you can use a more aggressive or higher-factor upscale safely.** The whole danger of aggressive upscaling is that it amplifies whatever ambiguity it finds; a clean input has little ambiguity.

**INTERPRETATION.** There is an elegant convergence here: Adobe Super Resolution and AI Denoise both live in the Camera Raw Enhance panel and both output DNGs. In current versions you can perform AI Denoise and then run Super Resolution (or vice versa within the RAW pipeline) so that both the denoise and the upscale happen on RAW data. **VERSION NOTE.** Whether the two Enhance operations can be applied in a single Enhance pass or must be run sequentially (denoise to a DNG, then Super Resolution on that DNG) depends on your Camera Raw version; verify. When using an external upscaler (ESRGAN family, Topaz), the equivalent ideal is: ACR AI Denoise -> export clean 16-bit TIFF -> external upscale -> return to Photoshop for finishing.

### Why the Model Interprets Noise as Real Texture -- In Depth

It is worth dwelling on the mechanism, because understanding it is what lets you predict failure before it happens.

A super-resolution model is trained on pairs: a high-resolution image and a degraded, downscaled version of it. During training, the model learns to map the degraded version back to the high-resolution original. Crucially, the degradation applied during training (downscaling, and in some models synthetic noise/blur/compression) defines what the model expects its input degradation to look like. When you feed the model input whose degradation matches the training assumptions, it inverts that degradation well. When you feed it input whose degradation does *not* match -- for example, heavy real-sensor noise that the training pipeline did not model, or noise at a strength beyond the training range -- the model does not recognize the noise as degradation. It treats those pixel values as *content* and reconstructs a high-resolution version of that content.

The model has, in a sense, learned a strong prior about "what fine texture looks like at high resolution." Noise, at the local patch level, has statistical properties -- high variance, high frequency, low spatial correlation -- that partly overlap with the appearance of certain fine textures (sand, fabric, foliage, stippling) at low resolution. So a noisy patch activates the same internal responses that a real fine-texture patch would, and the model generates the corresponding high-resolution texture. It is not malfunctioning. It is doing exactly what it was trained to do, on an input that lies outside the region where doing that is correct.

**FACT.** The model's behavior is determined by its training distribution. Input that falls outside that distribution ("out-of-distribution" input) produces confident but unreliable output. Heavy sensor noise is often out-of-distribution for upscalers trained primarily on clean or lightly-degraded data.

**INTERPRETATION.** This is why "denoise first" is not merely tidier -- it moves the input *back into the model's competence region*. A denoised image looks, to the upscaler, like the clean-ish images it was trained to enlarge. A noisy image looks like something it was never properly taught to handle, and its response to the unfamiliar is to hallucinate confidently. You are not just cleaning the image; you are making the image legible to the model.

### The Optical Resolution Ceiling

There is a hard physical fact that no upscaler can honestly exceed, and it is the single most powerful tool for spotting fabrication: **an image cannot contain real detail finer than the capturing lens could resolve.** Every lens has a resolution limit (set by diffraction, aberrations, focus accuracy, and the anti-aliasing filter). Detail finer than that limit was never recorded, because the optics could not deliver it to the sensor. This is a first-principles consequence of the physics of the lens, not a property of the file.

**FACT.** If an upscaled image shows detail finer or crisper than the original lens could optically resolve, that detail is fabricated. There is no exception. The information was not in the light that reached the sensor, so it cannot have been recovered -- it can only have been invented.

This gives you an objective test. When you see, in a 4x upscale, eyelashes crisper than the lens delivered, individual hairs where the original showed a soft mass, or fabric weave sharper than the depth-of-field allowed at that focus distance, you are looking at model invention. The upscaler has drawn detail at a scale the photograph physically cannot contain. Real enlargement makes existing detail bigger and, at best, cleaner; it cannot make the photograph *out-resolve its own lens*.

---

## Photoshop Implementation

Photoshop is the hub, but for AI upscaling it is mostly a hub for *hand-offs*. Only Adobe Super Resolution runs inside the Adobe pipeline. Everything else is external. This section is honest about that architecture and shows how each tool actually integrates.

### Adobe Super Resolution (ACR Enhance): Detailed Walkthrough

**VERSION NOTE.** Interface details below reflect the general design of the Enhance feature in recent Camera Raw versions. Verify against your installation.

1. **Do all RAW-stage denoise first.** Open the RAW file in Camera Raw. Set white balance and exposure so the signal is correctly represented, then apply AI Denoise via the Enhance panel (see [Part 4](16D4_Camera_Raw_AI_Denoise_and_PS_Tools.md)). This produces a denoised DNG. This step is the whole game -- it ensures Super Resolution sees clean signal.
2. **Run Super Resolution on the clean file.** With the denoised DNG selected, invoke Enhance again (or, if your version allows, enable both Denoise and Super Resolution in a single Enhance dialog). Check Super Resolution. **FACT.** The output is a new DNG at roughly 2x linear dimensions (~4x pixel count).
3. **Evaluate at 100%.** Open the enhanced DNG. Zoom to 100% and inspect: smooth areas for injected texture (there should be little to none -- Adobe's model is conservative), edges for ringing/halos, and any faces for naturalness. Adobe Super Resolution rarely over-textures, but always verify.
4. **Develop and finish normally.** The enhanced DNG carries full RAW editing latitude. Complete tonal and color work, then apply output sharpening appropriate to the final size ([Chapter 22](22_Output_Delivery.md)).

**RECOMMENDATION.** For realistic photographic work where fidelity matters -- portraits, documentary, most fine-art landscape -- Adobe Super Resolution is the *default* AI upscaler precisely because it is restrained and RAW-integrated. Reach for the aggressive external tools only when you need a larger factor or a specific detail-enhancement aesthetic and you have confirmed the input is clean.

### ESRGAN-Family Tools (Real-ESRGAN, Remacri, 4x-UltraSharp): The Hand-Off

These models do not run in Photoshop. The honest workflow is a round-trip through a file:

1. **Prepare a clean master in Photoshop/ACR.** Denoise at the RAW stage. Do tonal and color work. Flatten to a single layer. **RECOMMENDATION.** Export a 16-bit TIFF in a wide working space (ProPhoto or Adobe RGB per your pipeline). Sixteen-bit matters: the upscaler will boost contrast and you want the tonal headroom. Do *not* export a compressed JPEG for upscaling -- JPEG artifacts are exactly the kind of degradation the model will happily solidify.
2. **Process externally.** Run the model in its standalone tool or front-end. Real-ESRGAN is commonly a command-line binary; the community models (Remacri, 4x-UltraSharp) are loaded as weights into an ESRGAN-compatible front-end.
3. **Return to Photoshop for finishing.** Open the upscaled TIFF, inspect at 100% against the source, correct any artifacts (paint out over-textured smooth regions by blending back a bicubic enlargement, tame oversharpened edges), and apply output sharpening for the final medium.

**Illustrative Real-ESRGAN command line (exact flags, model names, and options are version-dependent -- this is a starting point, not a universal recipe):**

```
realesrgan-ncnn-vulkan -i input_clean_16bit.tif -o output_4x.png -n realesrgan-x4plus -s 4
```

**UNCERTAINTY.** The binary name, available `-n` model names, whether TIFF output is supported (many builds output PNG), the meaning and range of `-s`, and the presence of any denoise-strength flag all depend on the specific build you have installed. Verify with the tool's own `--help`. Treat the line above purely as an illustration of the shape of the invocation, not as authoritative syntax.

**FACT.** ESRGAN-family tools operate on rendered pixels (8- or 16-bit images), never on RAW data. They have none of the linear-data advantages of the RAW stage. This is another reason to denoise upstream in ACR: the external tool cannot do a RAW-quality denoise even if it tries.

### Topaz Photo AI / Gigapixel: Standalone or Plug-In

Topaz offers two integration paths:

1. **Standalone (recommended for RAW work).** Denoise in ACR, export a clean 16-bit TIFF, open it in Topaz, choose the upscale model and factor, dial the strength down from the default if the result looks over-processed, and export. Return the result to Photoshop for finishing.
2. **Photoshop plug-in.** With a layer active, invoke the Topaz plug-in from the Filter menu; it round-trips the layer through the Topaz engine and returns the processed result. **RECOMMENDATION.** Run it on a duplicate layer or a Smart Object so the operation is recoverable and you can mask the effect.

**RECOMMENDATION.** In Topaz, resist the temptation to let a single pass both denoise and upscale a rendered file. If you have a RAW original, denoise it in ACR first and let Topaz do only the upscale (reduce or disable Topaz's own noise/sharpen where possible). If you have only a rendered file with no RAW, then Topaz's integrated denoise is a reasonable option -- but supervise it closely, because you have lost the RAW-stage advantage and the model is now denoising and upscaling in an order you do not fully control.

**UNCERTAINTY.** Whether a given Topaz version lets you fully separate denoise from upscale, and the exact model names, depend on the release. Verify in your installation.

### Where Upscaling Sits in the Full Pipeline

Combining this with the noise-and-sharpening ordering from [Chapter 16](16_Noise_Reduction.md) and [Part 5](16D5_Frequency_Masking_and_Sharpening.md):

| Step | Operation | Stage | Tool |
|------|-----------|-------|------|
| 1 | White balance, exposure, basic tonal | RAW | Camera Raw |
| 2 | Primary noise reduction (the critical precondition) | RAW | ACR AI Denoise |
| 3 | Capture sharpening (modest) | RAW | ACR Detail panel |
| 4 | Upscale | RAW (Adobe SR) or external (ESRGAN/Topaz) | ACR Enhance, or hand-off |
| 5 | Tonal/color finishing, retouching | Photoshop | Adjustment layers |
| 6 | Artifact correction on the upscale (mask out over-texture, tame halos) | Photoshop | Masked layers |
| 7 | Creative sharpening (if needed) | Photoshop | High Pass / Smart Sharpen, masked |
| 8 | Output sharpening for final size/medium | Photoshop (on output copy) | Smart Sharpen / USM ([Chapter 22](22_Output_Delivery.md)) |

**RECOMMENDATION.** Note that noise reduction (step 2) precedes upscaling (step 4), which precedes any further sharpening (steps 7-8). This is the ordering that keeps noise from ever reaching the upscaler and keeps sharpening from re-amplifying anything the upscaler left behind. [Part 7](16D7_Workflows_and_E520_Project.md) walks a complete real-file example (the Olympus E-520 project) through this pipeline.

---

## Professional Workflow

### Choosing a Tool by Subject and Purpose

There is no single best upscaler. The right choice depends on the subject, the source quality, and the fidelity requirement.

| Scenario | Recommended approach | Rationale |
|----------|---------------------|-----------|
| Portrait, realistic output, RAW available | ACR AI Denoise -> Adobe Super Resolution; or -> Topaz with a face-aware model at reduced strength | Skin punishes over-texturing; conservative tools and face-aware handling preserve naturalness. Avoid aggressive GAN models on skin. |
| Landscape, fine natural detail, RAW available | ACR AI Denoise -> Adobe Super Resolution for fidelity; Topaz or a restrained ESRGAN model if a larger factor is needed | Foliage and rock are where over-texturing and manufactured texture appear most; verify at 100%. |
| Documentary / journalism / competition | Interpolation or Adobe Super Resolution only; disclose any AI use per rules | Truth-value is paramount; minimize fabrication and follow the applicable disclosure rules ([Chapter 20](20_AI_Tools.md)). |
| Fine-art print requiring a large factor from a strong clean file | ACR AI Denoise -> external upscaler (Topaz or 4x-UltraSharp), finish and artifact-correct in Photoshop | Strong clean input tolerates aggressive upscaling; you get the size with manageable fabrication risk, at the artist's discretion. |
| Illustration, game art, stylized work | Remacri or an art-tuned model | The digital-art aesthetic is appropriate here; over-texturing is a feature, not a bug. |
| Only a noisy JPEG exists, no RAW | Denoise conservatively first ([Part 3](16D3_Denoising_Mindset_RAW_vs_JPEG.md) on JPEG limits), then upscale modestly; manage expectations | Worst case: no RAW latitude, baked-in artifacts. Small factors and heavy supervision; accept a limited result. |

### Choosing a Scale Factor

**RECOMMENDATION.** Do not upscale further than the output actually requires. Every additional factor of enlargement gives the model more gap to fill and therefore more opportunity to fabricate. Compute the pixels you need for the final print size and viewing distance ([Chapter 22](22_Output_Delivery.md)), and choose the smallest factor that reaches it. A 2x that you need beats a 4x you do not: the 4x invents twice as much. If Adobe Super Resolution's roughly-2x-linear result is enough, prefer it to a 4x GAN pass.

**INTERPRETATION.** There is a temptation to upscale to the maximum "because you can" and downsize later, on the theory that more pixels can't hurt. They can: the extra fabricated detail may survive the downsize as false micro-texture, and you have spent processing and introduced risk for pixels you discard. Upscale to the target, not beyond.

### The Supervision Discipline

**RECOMMENDATION.** Treat every AI upscale as a draft to be audited, never as a finished result. The discipline:

1. Always keep the original and a plain bicubic enlargement of the same target size. The bicubic version is your honesty reference: it contains only real (if soft) information. Any detail in the AI version that is absent from the bicubic version is *added* -- your job is to judge whether it is plausibly reconstructed or fabricated.
2. Inspect at 100% in the regions most prone to failure: smooth areas (skin, sky, water, defocused background), high-detail natural surfaces (foliage, rock, hair), edges, and any text or faces.
3. Where the upscale fabricated, blend the bicubic version back in through a mask. This is the most important practical technique in this whole chapter: you are not forced to accept the AI result globally. Mask the AI upscale onto the regions where it genuinely reconstructed real detail, and let the honest-but-soft bicubic show through where the AI invented. A slightly soft sky is always better than a hallucinated one.

---

## The False-Detail Problem

This is the heart of the chapter. Everything above leads here: given an AI upscale, how do you *tell* when the model has stopped restoring your photograph and started inventing a new one? The answer is a set of concrete, learnable tells. Once you can see them, you cannot un-see them, and your upscaling judgment becomes reliable.

The single organizing question is: **is this detail consistent with what the camera and lens could have captured, and with how real surfaces actually behave?** Fabrication betrays itself by violating one of those two consistencies.

### Recognizing Hallucinated Detail

Hallucinated detail is structure that corresponds to nothing in the scene. The tells:

- **Detail finer than the lens could resolve.** As established in the Theory section, this is the strongest objective test. Crisp eyelashes, individual hair strands, sharp fabric weave, or knife-edge textures in an image whose lens, focus, and depth-of-field could not have delivered them are fabricated. Compare against the source: if the source shows a soft mass and the upscale shows discrete strands, the strands were invented.
- **Detail in out-of-focus regions.** Real depth-of-field means the background is softer than the subject. An upscaler biased toward crispness may render the defocused background with the same detail as the subject. Sharp "detail" in what should be bokeh is fabrication -- the scene's own optics prove it.
- **Detail that disagrees with the source at 100%.** Overlay or flip between the source (bicubically enlarged to match) and the AI upscale. Real reconstruction tracks the source's structure and merely sharpens/refines it. Fabrication introduces structure the source does not even hint at.

### Recognizing Texture Synthesis and Over-Texturing

- **Texture on surfaces that had none.** Synthesized pores on a soft-focus cheek, weave on a smooth wall, stippling on a clear sky, or micro-texture on calm water. Real smooth surfaces stay smooth; injected texture is a fabrication tell.
- **Uniform texture where real texture is irregular.** This is the deepest tell and deserves emphasis: **real surfaces have non-uniform, non-repeating micro-structure; synthesized texture tends toward uniformity and repetition.** Real sand has clumps, variation in grain size, shadows, and irregular density. Synthesized sand tends toward an even, regular stipple. Real skin has varied pore size and distribution, blemishes, and directional structure; synthesized skin texture is often an even, uniform grain. When a texture looks *too consistent* -- the same everywhere, no clumping, no variation -- it is manufactured.

### Recognizing Oversharpening, Edge Ringing, and Halos

These are the classic sharpening artifacts from [Chapter 15](15_Sharpening.md) and [Part 5](16D5_Frequency_Masking_and_Sharpening.md), and aggressive upscalers produce them freely because they sharpen as they enlarge.

- **Oversharpening:** excessive local contrast, gritty edges, a general harshness. Everything looks "etched."
- **Edge ringing:** oscillating light/dark bands running parallel to a high-contrast edge -- the overshoot/undershoot of an aggressive edge model. Look along branch-against-sky edges, roof-lines, and horizons.
- **Halos:** bright (or dark) outlines hugging high-contrast edges. A pale glow around dark tree limbs against a bright sky is the textbook example. Halos are especially common where the upscaler enhanced an edge that the source rendered gently.

**INTERPRETATION.** Ringing and halos are *optically impossible*. No lens produces a bright band tracing the outside of every dark edge. Their presence is therefore a certain sign of processing, not capture -- another consistency violation you can rely on.

### Recognizing Plastic/Waxy Skin

- Skin that has lost pore structure and gradation, becoming smooth and slightly shiny -- the "wax figure" effect familiar from over-aggressive noise reduction ([Chapter 16](16_Noise_Reduction.md)), now produced by an upscaler that either smoothed the skin (conservative model on a small face) or, worse, synthesized a uniform fake pore texture over it (aggressive model). Either failure reads as inhuman.
- **Face substitution / the "different person" effect.** When the input face is too small or too noisy to constrain the model, a face-aware upscaler may reconstruct a face that is plausible but *not this person* -- subtly altered eye shape, symmetry that the real face lacked, teeth or iris detail that was never resolved. This is the most ethically fraught fabrication of all, and it is common when upscaling small faces in group shots or distant subjects. **RECOMMENDATION.** For any portrait where identity matters, compare facial features against the source meticulously, and disable or reduce face-recovery models if the input face is too small to trust.

### Recognizing Plastic Foliage and Manufactured Rock Texture

Foliage and rock are the two subjects where fabrication is most common and most visible, because both have complex, irregular natural structure that models tend to regularize.

- **Plastic foliage:** leaves rendered with uniform, repeating edge texture; a mass of distant foliage turned into a suspiciously regular pattern of near-identical leaf-shapes; a synthetic "sameness" where real foliage has endless irregular variation. Distant tree canopies are a prime offender -- the model, unable to resolve real leaves, invents a repeating leaf-texture.
- **Manufactured rock texture:** rock and stone rendered with a uniform, even granularity and repeating crack/fracture patterns. Real rock has irregular grain, non-repeating cracks, varied surface, and geologically-consistent structure. Manufactured rock texture has an even stipple and, tellingly, *repeating* crack motifs -- the same little fracture shape appearing multiple times across the surface. That repetition is impossible in a real weathered rock and is a definitive fabrication tell.

### The Statistical-Impossibility Tells

Some fabrications betray themselves by producing something that *cannot occur* in a real photograph:

- **Tiling / repeating textures.** The same small texture patch recurring at regular intervals. Real surfaces never tile perfectly. Repetition means synthesis.
- **Invented lettering and symbols.** A distant sign, license plate, book spine, or logo that the source rendered as an illegible blur may be "sharpened" by the model into crisp but *nonsensical* lettering -- text-like shapes that spell nothing, or spell something the scene never contained. Legible text that was not legible in the source is fabricated, full stop; the information to read it was never captured.
- **Invented faces.** In crowds or backgrounds, the model may synthesize face-like detail onto blurred figures -- eyes, mouths, expressions that were never resolved. Distant crowd faces that are suddenly crisp are invented.
- **Edge patterns inconsistent with the optics.** Detail whose orientation, scale, or contrast does not match the lens's behavior elsewhere in the frame -- crisp detail in a corner where the lens is known to be soft, or micro-detail at a scale finer than the frame's best-focused region achieves. When the "detail" contradicts the optics that produced the rest of the image, it is added.

### A Recognition Checklist

**RECOMMENDATION.** Run this quick audit on any AI upscale before delivery ([Part 8](16D8_Quality_Control_and_Cheat_Sheet.md) expands it into the full QC checklist):

| Tell | Where to look | What it means |
|------|---------------|---------------|
| Detail finer than the lens can resolve | Hair, lashes, fabric, distant detail | Fabricated -- optically impossible |
| Sharp detail in out-of-focus areas | Background bokeh, defocused foreground | Fabricated -- violates depth-of-field |
| Texture on smooth surfaces | Skin, sky, water, walls | Synthesized -- surface had none |
| Uniform / repeating texture | Sand, skin, rock, foliage | Synthesized -- real texture is irregular |
| Edge ringing / halos | High-contrast edges, horizons | Oversharpening artifact -- optically impossible |
| Waxy or fake-pore skin | Faces | Over-smoothed or over-textured -- inhuman |
| "Different person" face | Small/distant faces | Face model fabricated identity |
| Repeating crack/fracture motifs | Rock, stone, bark | Manufactured texture -- real cracks don't repeat |
| Legible-but-nonsense text | Signs, plates, spines | Invented lettering -- was never resolved |
| Crisp distant crowd faces | Backgrounds, crowds | Invented faces |

### The Authenticity and Ethics Line

The false-detail problem is not only a quality issue; it is an authenticity issue, and it connects directly to the ethical framework in [Chapter 20](20_AI_Tools.md).

**FACT.** Upscaled detail that was synthesized is *generated content*, not *recovered content*, in exactly the sense [Chapter 20](20_AI_Tools.md) defines. An AI upscaler that invents leaf texture, rock granularity, or facial features has added information that originated in a model's training data, not in the light the sensor recorded. The fact that it is added at the *detail* level rather than the *object* level does not change its category. A hallucinated rock texture is as generated as a Generative-Fill sky.

The context-specific standards therefore transfer directly:

- **Documentary, photojournalism, forensic.** Fabricated detail is disqualifying. Use interpolation or, at most, the most conservative RAW-integrated upscaling, and be prepared to defend that the output contains no invented structure. When in doubt, do not AI-upscale at all.
- **Nature and wildlife competition.** Most rules that prohibit adding or removing elements also, in spirit, prohibit fabricating detail. Manufactured feather texture or invented foliage can violate competition rules even if no object was added. Read the specific rules; when they are silent, assume conservatively. Disclose.
- **Stock photography.** Most agencies now require disclosure of AI-generated content, and heavily AI-upscaled images may be reclassified as illustration rather than photograph, or rejected. Fabricated detail counts. Check the agency's current policy ([Chapter 20](20_AI_Tools.md)).
- **Fine art.** The artist's discretion. There is no external truth-value requirement, but the *contextualization* of the work is affected: a piece that leans on synthesized detail is closer to digital art than to photography, and honest photographers describe it accordingly. [Part 8](16D8_Quality_Control_and_Cheat_Sheet.md) treats this as part of quality control -- knowing and being able to state what is real in your own image.
- **Commercial.** Generally acceptable; fabricated detail serves the message. Advertising-standards disclosure rules in some jurisdictions may still apply.

**RECOMMENDATION.** Whatever the context, *know what your own image contains.* The defining property of hallucinated detail is that no viewer can detect it -- so the ethical safeguard is entirely on the photographer's side. Keep the source and the bicubic reference; be able to point to which detail is reconstructed and which is invented; disclose per the applicable standard. This is the same principle as [Chapter 20](20_AI_Tools.md)'s central rule, applied at the level of texture: never present generated detail as captured detail.

---

## Common Mistakes

**Upscaling before denoising.** The cardinal error of this chapter. Feeding noisy input to an upscaler converts removable random noise into permanent, structured false detail. Always denoise at the RAW stage first. If the tool you are using offers only upscaling, add a denoise step upstream rather than accepting the order.

**Treating the upscaler's degradation-awareness as a denoiser.** Real-ESRGAN and similar models have some built-in noise tolerance, and Topaz can denoise as it upscales. None of this replaces a proper RAW-stage denoise. Relying on the upscaler to handle noise gives you solidified noise.

**Choosing the sharpest-looking model.** Sharpest is not most faithful. The most aggressive community models (and diffusion-based restorers) look most impressive at a glance and fabricate most. For realistic photography, prefer the conservative tools (Adobe Super Resolution, restrained Topaz settings) and reserve the aggressive models for stylized work or confirmed-clean strong input.

**Upscaling further than the output needs.** Every extra factor of enlargement is extra opportunity for fabrication. Compute the required pixels and upscale to that, not to the maximum the tool allows.

**Accepting the AI result globally instead of masking.** The upscale is a draft. It reconstructs some regions faithfully and fabricates others. Blend a bicubic enlargement back in through a mask wherever the AI invented (smooth skies, defocused backgrounds, over-textured surfaces). A soft-but-honest region beats a crisp-but-fake one.

**Not inspecting at 100% against the source.** AI upscales look convincing at fit-to-screen zoom. The fabrication only reveals itself at 100%, and only in comparison with the source or a bicubic reference. Skipping this step means shipping hallucinations you never saw.

**Trusting face-recovery models on small faces.** When the input face is too small or noisy to constrain the model, the "recovered" face may be a different person. For any image where identity matters, compare facial features meticulously and reduce or disable face models on under-resolved faces.

**Feeding JPEGs to upscalers.** JPEG compression artifacts are exactly the kind of structured degradation an upscaler will solidify, and 8-bit JPEG has no tonal headroom for the contrast boost the upscaler applies. Export a clean 16-bit TIFF from a RAW-denoised master whenever possible. When only a JPEG exists, denoise conservatively first and keep the factor modest.

**Presenting synthesized detail as captured detail.** In any truth-value-sensitive context (documentary, competition, stock, forensic), fabricated upscale detail is generated content and must be treated as such -- avoided or disclosed per the rules. The invisibility of the fabrication is exactly why the responsibility falls on you.

**Assuming "denoise-then-upscale" and "upscale-then-denoise" are interchangeable.** They are not. The operations are nonlinear and do not commute. The order determines what each model sees, and therefore the result. Denoise first, always.

---

## Summary

AI upscaling is not a better enlarger; it is a trained model that predicts a plausible high-resolution version of whatever you show it. This makes it categorically different from interpolation, which only redistributes existing information and can never fabricate. Interpolation's failure mode is softness; super-resolution's failure mode is confident invention. The entire discipline of AI upscaling is managing that difference.

The tools occupy a spectrum of aggressiveness and fidelity. **Adobe Super Resolution (ACR Enhance)** is the conservative, RAW-integrated default: roughly 2x linear (~4x pixels), output as a DNG, restrained and natural, and the safest choice for realistic work. The **ESRGAN family** -- Real-ESRGAN and community models like **Remacri** and **4x-UltraSharp** -- are standalone, more aggressive, offer larger factors and swappable aesthetics, and carry higher fabrication risk; many were trained partly on non-photographic sources and lean toward the "digital-art" look. **Topaz Photo AI / Gigapixel** add subject-aware and face-aware models with user-adjustable strength and an integrated but supervision-requiring denoise-plus-upscale pipeline. Newer transformer and diffusion approaches (**SwinIR**, **SUPIR**) can be spectacular and, because of their powerful generative priors, carry the highest hallucination risk of all. Specifics of the community and proprietary models are not authoritatively documented and were flagged throughout as uncertain -- test the actual version you have.

Upscaling a noisy image is dangerous because the model contains no noise detector: it interprets random noise grains as the low-resolution appearance of fine texture and synthesizes confident, permanent, high-resolution structure from them. Noise -- which was random, formless, and trivially removable a moment earlier -- is thereby "solidified" into false detail that no subsequent denoise can remove without also destroying real detail at the same scale. This is why the two orders are not equivalent: **RAW -> Denoise -> Upscale** shows the model only clean signal, while **RAW -> Upscale -> Denoise** solidifies the noise first and leaves the denoiser an impossible task. The ideal is specifically to denoise at the RAW stage with ACR AI Denoise -- the most effective denoise available -- and then upscale, so the upscaler receives the cleanest, most detail-rich, most in-distribution input possible.

The false-detail problem is the central skill of the chapter: learning to see when an upscale has stopped restoring and started inventing. The tells are concrete and learnable. Some rest on optical impossibility -- detail finer than the lens can resolve, sharpness in out-of-focus areas, edge ringing and halos that no lens produces. Others rest on the unnatural regularity of synthesis -- uniform and repeating textures, plastic foliage, manufactured rock with repeating crack motifs, waxy or fake-pore skin. Others are outright statistical impossibilities -- tiling textures, invented nonsense lettering, and fabricated faces on under-resolved subjects. The practical defenses are procedural: keep the source and a bicubic reference, inspect at 100%, upscale no further than the output requires, and mask the honest bicubic back in wherever the AI fabricated.

Finally, synthesized upscale detail is *generated content* in the exact sense of [Chapter 20](20_AI_Tools.md), and the authenticity line transfers directly: fabricated detail is disqualifying for documentary, journalism, and forensic work; likely prohibited and disclosable in nature competitions and stock; the artist's disclosed discretion in fine art; and generally acceptable in commercial work. Because hallucinated detail is by definition undetectable to the viewer, the responsibility rests entirely with the photographer to know what the image contains and to represent it honestly.

---

## Exercises

### Exercise 1: Interpolation vs Super-Resolution -- Seeing the Difference

Take a clean, sharp, moderate-resolution photograph (a low-ISO landscape or still life).

1. Enlarge it 2x with bicubic (Image > Image Size, Bicubic Smoother). Note the uniform softness.
2. Run Adobe Super Resolution on the RAW (or a suitable AI upscaler) to a similar size.
3. Compare at 100%. Identify: where has the AI version genuinely reconstructed structure that the bicubic version merely blurred? Is there anywhere the AI version shows detail the bicubic version gives no hint of?

**Success criterion:** You can articulate the difference between redistributed information (bicubic) and predicted information (AI), and you can point to specific regions where the AI added structure.

### Exercise 2: Demonstrating Noise Solidification

Take a RAW file and create deliberate noise by underexposing 3-4 stops and recovering in ACR (or use a genuine high-ISO file).

1. **Version A:** With no denoise (Luminance 0, Color 0), upscale the noisy image 2-4x with an aggressive tool (a community ESRGAN model, or Topaz with denoise disabled).
2. **Version B:** Apply ACR AI Denoise first, then upscale the clean version identically.
3. Compare at 100%. In Version A, find the noise grains that have been solidified into structure. Confirm they correspond to nothing in the scene.

**Success criterion:** You can see, in Version A, random noise rendered as confident false texture, and you understand why Version B avoids it.

### Exercise 3: The Two Orders

Using the same noisy file:

1. **Order A:** Denoise (ACR AI Denoise) then upscale.
2. **Order B:** Upscale the noisy file, then attempt to denoise the result in ACR or Photoshop.
3. Compare. In Order B, note that the denoiser cannot remove the solidified false structure without smearing real detail. Note also that any residual noise in Order B is coarser (it was enlarged before removal).

**Success criterion:** You can demonstrate that the orders are not equivalent and explain why, in terms of what each model receives as input.

### Exercise 4: Building the Fabrication Eye

Collect three AI upscales -- your own or examples -- of a portrait, a foliage-heavy landscape, and an image containing distant text or faces.

1. For each, run the recognition checklist from the False-Detail section.
2. Identify at least one concrete tell per image: an optical-impossibility tell (detail finer than the lens, halo, ringing, sharp bokeh) or a synthesis tell (uniform/repeating texture, plastic foliage, waxy skin, invented text/face, repeating crack motif).

**Success criterion:** You can name the specific tell and explain why it proves the detail is fabricated rather than captured.

---

## Advanced Exercises

### Advanced Exercise 1: The Masked-Hybrid Upscale

Take a portrait with a defocused background, shot at moderate-to-high ISO with RAW available.

1. ACR AI Denoise, then upscale to 2x (Adobe Super Resolution or Topaz).
2. Separately, bicubic-enlarge the denoised file to the identical dimensions.
3. In Photoshop, stack the AI upscale over the bicubic version.
4. Add a mask to the AI layer. Reveal the AI upscale only where it genuinely reconstructed real detail (in-focus subject features), and let the honest bicubic show through in the defocused background and any smooth skin the AI over-textured.
5. Compare the hybrid to the pure AI upscale.

**Success criterion:** You can produce a result that has the AI's benefit where it helped and none of its fabrication where it hurt, using masking to arbitrate region by region.

### Advanced Exercise 2: Tool Comparison on a Controlled Subject

Take one clean, detailed RAW file with a natural texture surface (weathered rock, tree bark, a foliage mass).

1. Upscale it 4x with three different approaches: Adobe Super Resolution (running twice if needed to reach 4x), a restrained ESRGAN model (e.g., 4x-UltraSharp), and an aggressive one (e.g., Remacri) -- or the closest tools you have.
2. At 100%, evaluate each for: over-texturing, uniformity/repetition, edge ringing/halos, and overall photographic realism.
3. Rank them for this specific subject and justify the ranking with concrete observations.

**Success criterion:** You can characterize each tool's aesthetic bias from direct observation rather than reputation, and match tool to subject deliberately.

### Advanced Exercise 3: The Optical-Ceiling Audit

Take an image where you know the capture conditions (lens, aperture, focus, subject distance).

1. Upscale it 4x with an aggressive tool.
2. Identify the finest real detail the lens could plausibly have resolved at that aperture and focus.
3. Hunt the upscale for detail finer than that ceiling. Document each instance.

**Success criterion:** You can use first-principles reasoning about the optics to identify fabricated detail objectively, independent of subjective "does it look fake" judgment.

---

## Blackbelt Challenge

You are given two upscaling assignments and must design a complete strategy for each -- tool, order, scale factor, artifact management, and an authenticity assessment -- and predict the failure modes of the wrong approaches, without touching the software.

**Assignment A:** A 2008-era 10-megapixel APS-C RAW file (an Olympus E-520-class file -- the subject of the [Part 7](16D7_Workflows_and_E520_Project.md) project), shot at ISO 1600 of an autumn forest scene: a sunlit foreground of fallen leaves with fine detail, a mid-ground of trees with dense foliage, deep shaded areas between trunks with visible luminance and chrominance noise, and a small hiker figure in the mid-ground whose face is perhaps 40 pixels tall. The client wants a 24x36 inch fine-art gallery print. The image will be exhibited and sold as a *photograph*.

**Assignment B:** A tightly cropped, well-exposed ISO 200 studio product shot of a matte ceramic vase against a smooth seamless background. The crop leaves the vase at only 1800 pixels tall, but the client needs a 2x enlargement for a web hero banner. The surface is deliberately smooth and matte; the background is a clean gradient.

Design and defend, for each assignment:

1. **Noise assessment.** For Assignment A, describe the noise distribution across the four zones (sunlit leaves, foliage mid-ground, shaded trunks, hiker figure) and explain specifically why upscaling *before* denoising would be catastrophic in the shaded zones and in the foliage. For Assignment B, explain why noise is nearly a non-issue and what that implies for tool choice.

2. **Order of operations.** State the exact pipeline for each (RAW-stage steps, upscale step, Photoshop finishing). Justify the denoise-then-upscale order for A by reference to what the upscaler would do to the shaded-zone noise. For B, justify whether a denoise step is even warranted.

3. **Tool and scale-factor choice.** For A, choose between Adobe Super Resolution and an external ESRGAN/Topaz path, justify the factor needed to reach 24x36 at an appropriate print resolution and viewing distance ([Chapter 22](22_Output_Delivery.md)), and address the hiker's 40-pixel face specifically -- would you allow a face-recovery model near it, and why or why not? For B, justify why a conservative 2x is almost certainly the right and only call.

4. **Fabrication risk map.** For Assignment A, predict exactly where each class of false detail would appear if you used an aggressive tool: which zone would show manufactured foliage, which would show manufactured rock/bark texture, where edge ringing and halos would appear (trunk-against-sky edges), and what would happen to the hiker's face. For Assignment B, predict what an aggressive over-texturing model would do to the matte vase surface and the smooth gradient background, and why those are the worst possible subjects for texture synthesis.

5. **Artifact management.** Describe the masked-hybrid strategy you would use for A to keep the AI benefit in the genuinely detailed sunlit foreground while protecting the shaded zones, the distant foliage, and the hiker's face from fabrication. For B, describe how you would verify the smooth surfaces stayed smooth.

6. **Authenticity assessment.** Assignment A is sold as a photograph and exhibited. Given that, decide: how much AI-synthesized detail is acceptable, and where is the line? If the aggressive tool produces beautiful but fabricated foliage in the distant trees, is the print still honestly a "photograph"? Cross-reference the framework in [Chapter 20](20_AI_Tools.md) and the QC standards in [Part 8](16D8_Quality_Control_and_Cheat_Sheet.md). For B (a commercial web banner), explain why the authenticity bar is different and what, if anything, still constrains you.

7. **Predict the failure modes.** For each assignment, describe in concrete visual terms what the final result would look like if you: (a) upscaled the noisy/small file directly with an aggressive GAN model and no denoise; (b) denoised correctly but then upscaled 4x when you only needed 2x; (c) used a face-recovery model on the 40-pixel hiker (Assignment A only). Be specific about the artifacts, where they would appear, and why they would occur.

This challenge requires you to synthesize noise physics, the noise-as-texture solidification mechanism, the non-commuting order of operations, the optical-resolution ceiling, tool-aesthetic biases, the full false-detail taxonomy, output-medium requirements, and the recovery-versus-generation ethics into two coherent, defensible upscaling strategies -- and to predict both the desired outcomes and the failure modes before opening a single application.
