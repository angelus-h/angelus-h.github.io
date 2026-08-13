---
title: "Chapter 20: AI-Assisted Editing"
description: "AI tools in Photoshop -- Generative Fill, Generative Expand, Remove Tool, Neural Filters, Select Subject, AI Denoise. Capabilities, limitations, artifacts, and the critical distinction between recovering photographic information and generating new visual content."
---

# Chapter 20: AI-Assisted Editing

## Learning Objectives

After completing this chapter, you will be able to:

1. Distinguish between AI tools that recover photographic information (AI Denoise, Select Subject) and AI tools that generate new visual content (Generative Fill, Generative Expand), and articulate why this distinction determines the truth-value of the resulting image.
2. Explain how generative AI (neural network trained on external data, producing novel content) differs from traditional algorithmic processing (pattern analysis operating on the image's own pixel data), and why this difference is not merely technical but epistemological.
3. Evaluate when Photoshop's AI tools produce results that are derived from the photograph being edited versus results that are derived from a model's training data, and apply this evaluation to determine appropriate use in each professional context.
4. Use Generative Fill with and without text prompts, understanding the non-deterministic nature of its output, the generative layer system, and the practical limitations of resolution, coherence, and structural accuracy.
5. Use Generative Expand to extend image canvas, understanding that the extended content is generated, not recovered, and assessing common failure modes at structural boundaries.
6. Describe the function and limitations of Neural Filters, distinguishing between filters that analyze and transform existing pixel data (Style Transfer, Smart Portrait) and understanding the variability of these features across Photoshop versions.
7. Explain how Select Subject and Object Selection use machine learning to produce selections, and why their output is a selection boundary (derived from the photograph) rather than generated content.
8. Describe how AI Denoise differs from traditional noise reduction algorithms, what "hallucinated detail" means in the context of AI-based image processing, and why AI Denoise output is a prediction rather than a recovery.
9. Apply a systematic decision framework for choosing between traditional tools and AI tools based on the retouching task, the required truth-value of the result, and the professional context.
10. Articulate a clear ethical framework that accounts for AI-generated content across documentary, fine art, stock, and commercial photography, including the current state of metadata tagging for AI-assisted edits.

---

## Conceptual Foundation

Chapters 17 and 16 introduced several AI-powered tools in context: Generative Fill and the Remove Tool as retouching instruments (Chapter 17), and AI Denoise as a noise reduction method (Chapter 16). This chapter examines all of Photoshop's AI tools as a category, focusing on a question those chapters raised but did not fully resolve: what is the difference between processing photographic data and generating new visual data, and why does that difference matter?

Traditional Photoshop tools operate on the image's own pixel data. A Curves adjustment remaps tonal values that the sensor captured. Unsharp Mask increases contrast at edges that exist in the photograph. The Clone Stamp copies pixels from one part of the image to another. Content-Aware Fill analyzes patterns in the surrounding image and assembles them into a fill region. In every case, the output is derived from the photograph. The tools transform, rearrange, or blend what the camera recorded.

AI-powered tools introduce a second source of information: a neural network trained on external data. When Generative Fill creates a sky, that sky is not derived from the photograph being edited. It is produced by a model that has learned what skies look like from its training dataset. When AI Denoise removes noise from a shadow, it does not merely smooth the pixel values -- it predicts what the noise-free image should look like, based on patterns learned from millions of image pairs. The output in both cases incorporates information that originated outside the photograph.

This is the central distinction of this chapter: **recovering information that exists in the photograph** versus **generating information that does not exist in the photograph**. These are different operations with different truth-value implications, and they must be understood separately even when they are performed by the same software.

**FACT.** Some AI tools in Photoshop operate primarily on the image's own data. Select Subject uses machine learning to identify subject boundaries in the photograph, but its output is a selection -- a mask boundary derived from the image's content. It does not create new visual content. AI Denoise uses a trained model to predict what clean pixel values should be, producing output that is a prediction informed by external training data but applied to the photograph's own signal. Generative Fill creates entirely new visual content using a model that draws on external training data, producing pixels that have no derivation from the photograph at all.

**INTERPRETATION.** These three examples represent a spectrum of how much external information the AI tool contributes to the result. Select Subject: minimal external contribution (the model identifies boundaries, but the boundaries are in the photograph). AI Denoise: moderate external contribution (the model's prediction shapes the output, but the input is the photograph's noisy signal). Generative Fill: maximal external contribution (the output is produced by the model, not derived from the photograph). Understanding where each tool falls on this spectrum is essential for making informed decisions about when to use them and how to characterize the result.

**RECOMMENDATION.** Before using any AI tool, ask two questions. First: is the output derived from my photograph, or is it generated by a model? Second: does the answer to the first question matter for the purpose this image serves? A landscape photographer submitting to a documentary competition and a commercial retoucher preparing an advertisement will answer the second question differently, but both must answer the first question accurately.

---

## Terminology

### Generative Fill

**VERSION NOTE:** Generative Fill was introduced in Photoshop (Beta) in May 2023 and moved to the release version in Photoshop 25.0 (September 2023). It requires an internet connection for cloud-based processing, though Adobe has introduced on-device generation for certain operations in subsequent updates. The feature requires a Creative Cloud subscription with Generative Credits. The underlying Firefly model, the resolution of generated output, and the available controls have changed across versions. Verify the current capabilities against your specific Photoshop version.

**Definition:** Generative Fill is an AI-powered content creation tool that uses Adobe's Firefly generative model to produce new image content within a selected area. The model receives the surrounding image context and, optionally, a text prompt, and generates pixels that are visually plausible for that context. The generated content is created by the neural network based on patterns learned during training on external image data. It is not derived from the photograph being edited.

Chapter 17 introduced Generative Fill in the context of retouching. This chapter examines it as the defining example of generative AI in photographic editing: a tool whose output is fabricated by a model rather than extracted from the image.

**Meaning in photographic practice:** Generative Fill answers the question "what content would plausibly exist here?" rather than "what content exists nearby that can fill this gap?" (which is Content-Aware Fill's question). This distinction is not semantic; it is structural. Content-Aware Fill's output fails in predictable ways -- when the surrounding area lacks matching patterns. Generative Fill's output fails in fundamentally different ways: it can produce content that is photorealistic but physically impossible, structurally incoherent, or factually incorrect, because the model generates what looks plausible, not what is true.

Generative Fill is powerful for creative and commercial work: extending backgrounds, replacing skies, filling areas where no photographic data exists, inserting elements described by text prompts. It is categorically inappropriate for any context where the image is expected to represent photographic reality.

**Photoshop implementation:** Make a selection, then click the Generative Fill button in the contextual taskbar (or Edit > Generative Fill). The interface provides a text prompt field (optional) and generates multiple variations (typically three) for each request. Results are placed on a non-destructive Generative Layer that preserves the original pixel data beneath.

| Feature | Behavior |
|---------|----------|
| Prompted generation | Enter a text description of the desired content. The model generates content matching the description within the image context. |
| Unprompted generation | Leave the prompt empty. The model generates content based solely on the surrounding image context. Used for extending backgrounds, filling gaps, and object removal. |
| Variations | Each generation produces multiple options. Browse and select the best result. |
| Generative Layer | Non-destructive layer preserving the original data. Deletable at any time. |
| Iterative refinement | Regenerate with modified prompts or adjusted selections for different results. |

**VERSION NOTE:** The resolution and quality of Generative Fill output have improved across Firefly model versions. Earlier versions produced content at a lower internal resolution that was upscaled, resulting in visible softness compared to the surrounding photograph. Later versions have reduced but not eliminated this resolution gap. Always inspect generated content at 100% zoom alongside the original image content.

**Related concepts:** Content-Aware Fill (Chapter 17), Generative Expand, Adobe Firefly, generative AI, Generative Layer, photographic ethics.

**Common misconception:** "Generative Fill recovers what was in the scene." It does not. Generative Fill creates what a model calculates would be plausible in the scene. If you remove a person from a park bench and fill the area with Generative Fill, the generated bench surface, grass, or shadow is not a reconstruction of what was actually behind that person. It is a fabrication -- a statistically likely visual pattern that the model produces. The distinction between fabrication and reconstruction is invisible in the pixels but fundamental in the photograph's relationship to reality.

---

### Remove Tool

**VERSION NOTE:** The Remove Tool was introduced in Photoshop version 24.5 (2023). Its underlying algorithm has been updated in subsequent releases. In its initial implementation, the Remove Tool used primarily pattern-analysis-based synthesis (similar to Content-Aware Fill). Subsequent versions have incorporated AI-based generation for more complex removals. The exact behavior -- whether the tool uses pattern synthesis, AI generation, or a hybrid approach -- depends on the Photoshop version and may not be transparent to the user. The description below reflects the general design as of 2025.

**Definition:** The Remove Tool is a brush-based retouching tool that removes objects by painting over them. Photoshop analyzes the surrounding content and synthesizes or generates replacement pixels to fill the painted area. The tool occupies a transitional space between traditional algorithmic processing and generative AI: depending on the complexity of the removal and the Photoshop version, it may use pattern synthesis from the surrounding image (like Content-Aware Fill), AI-powered generation (like Generative Fill), or a combination of both.

**Meaning in photographic practice:** The Remove Tool is the most accessible object removal tool in Photoshop -- paint over the unwanted element, and the tool handles the rest. Its simplicity is also its limitation: you do not control whether the replacement content is synthesized from surrounding pixels (derived from your photograph) or generated by a model (derived from training data). For professional work where the distinction between photographed and generated content matters, this ambiguity is significant.

Chapter 17 covered the Remove Tool as a retouching instrument positioned between the Spot Healing Brush and Content-Aware Fill. The AI perspective adds a critical question: are the replacement pixels derived from my photograph, or are they generated?

**Photoshop implementation:** Accessed from the toolbar (shortcut: J, cycle with Shift+J). Paint over the element to remove. Key settings:

| Setting | Function |
|---------|----------|
| Sample All Layers | Enable for non-destructive work on an empty layer. |
| Remove after each stroke | When enabled, processing occurs immediately after each stroke. When disabled, multiple strokes define the area before confirmation. |

**VERSION NOTE:** Adobe has progressively integrated AI capabilities into the Remove Tool across versions. The degree of AI involvement may differ between simple removals (where surrounding patterns suffice) and complex removals (where the tool may invoke generative processing). Adobe's documentation does not always specify which processing method is used for a given removal. When the truth-value of the result matters, verify by comparing the removal result against what can be inferred from the surrounding image content.

**Related concepts:** Content-Aware Fill (Chapter 17), Generative Fill, Spot Healing Brush (Chapter 17), retouching tool hierarchy.

**Common misconception:** "The Remove Tool uses AI, so it generates content." The Remove Tool's behavior is version-dependent and task-dependent. For simple removals in areas with abundant surrounding pattern information, it may operate primarily through pattern synthesis (algorithmically, not generatively). For complex removals, it may invoke generative processing. The tool does not disclose which method it is using, which means you cannot assume the result is either purely pattern-derived or purely generated without inspecting the output critically.

---

### Content-Aware Fill (Traditional, Non-AI)

**Definition:** Content-Aware Fill is a pattern-analysis-based retouching tool that removes selected content and replaces it with pixels synthesized from the surrounding image area. It uses a patch-matching algorithm to identify visual patterns in the designated sampling region and assembles those patterns to fill the selected area. Content-Aware Fill does not use neural networks or external training data. Its output is derived entirely from the photograph being edited.

This term is included here as a reference point. Content-Aware Fill was covered in depth in Chapter 17. Its significance in this chapter is as the comparison baseline: the traditional, non-AI approach to content replacement that Generative Fill, the Remove Tool, and other AI tools are often compared against.

**Meaning in photographic practice:** Content-Aware Fill answers the question "what patterns exist nearby that can fill this gap?" It cannot generate content that does not resemble the surrounding area. It cannot respond to text prompts. It cannot create objects, people, or scenes. These limitations are also its integrity: every pixel in Content-Aware Fill's output is derived from the photograph's own data, rearranged and blended. The output is constructed from the photograph. It is not fabricated by a model.

**FACT.** Content-Aware Fill's patch-matching algorithm scans the sampling area for small rectangular patches that match the patterns along the edge of the fill region, then assembles and blends those patches to construct the fill content. The result is a mosaic of real image data from the surrounding area, not a generation from a model.

**Photoshop implementation:** Edit > Content-Aware Fill (with a selection active). The dedicated workspace provides sampling area control (green overlay), color adaptation, rotation adaptation, scale, mirror, and output options. Always output to a New Layer for non-destructive work.

**Related concepts:** Generative Fill, Remove Tool, Patch Tool (Chapter 17), pattern synthesis, patch matching.

**Common misconception:** "Content-Aware Fill is obsolete now that Generative Fill exists." Content-Aware Fill and Generative Fill solve different problems and have different truth-value implications. Content-Aware Fill is the correct tool when you need replacement content that is verifiably derived from the photograph -- when the image's integrity as a photographic record matters. Generative Fill is the correct tool when you need content that does not exist in the image and the generative nature of the result is acceptable for the image's purpose. The choice is not about which tool is "better" but about what the replacement pixels need to represent.

---

### Neural Filters

**VERSION NOTE:** Neural Filters were introduced in Photoshop 22.0 (October 2020) as a collection of AI-powered image manipulation tools. The available filters, their quality, and their status (Featured, Beta, or removed) have changed significantly across versions. Some filters present at launch have been removed. Others have been added. The specific filters described below may not be available in your version of Photoshop. Treat any specific filter name or capability as version-dependent and verify against your current installation.

**Definition:** Neural Filters are a collection of AI-powered image transformation tools in Photoshop, each implemented as a neural network trained for a specific image manipulation task. Unlike Generative Fill (which creates new content from a text prompt and image context), Neural Filters apply trained transformations to existing image content: changing apparent age, modifying facial expression, transferring artistic style, adjusting gaze direction, smoothing skin, colorizing black-and-white images, and other operations that transform rather than create.

**Meaning in photographic practice:** Neural Filters occupy an ambiguous position in the recovery-versus-generation spectrum. A Skin Smoothing neural filter applies a transformation to existing skin pixels -- it modifies what the camera captured rather than creating new content. A Colorize filter assigns colors to a black-and-white image based on the model's training data -- it generates color information that was never captured. A Style Transfer filter transforms the image's visual style based on a reference -- it reshapes existing content to match an external aesthetic. In each case, the nature of the operation (recovery, transformation, or generation) is different, and the truth-value of the result is different.

**Photoshop implementation:** Filter > Neural Filters opens the Neural Filters workspace. Filters are organized by status:

| Status | Meaning |
|--------|---------|
| Featured | Considered stable and production-ready by Adobe. |
| Beta | Functional but subject to quality limitations and interface changes. |
| Waitlist / Removed | Previously available but deprecated or pending redesign. |

**VERSION NOTE:** The following list reflects filters that have existed across various versions. Not all are available in any single version:

| Filter | Operation | Content Status |
|--------|-----------|---------------|
| Skin Smoothing | Reduces skin texture while preserving structure | Transforms existing content |
| Style Transfer | Applies the visual style of a reference image | Transforms existing content using external reference |
| Colorize | Assigns color to black-and-white images | Generates color information not present in the image |
| Super Resolution | Upscales image resolution | Generates detail beyond what the sensor captured |
| Smart Portrait (Beta) | Modifies facial features: expression, age, gaze, hair | Generates modified facial features |
| Landscape Mixer (Beta) | Blends landscape characteristics with a reference | Transforms/generates landscape features |
| Harmonization | Matches the color and tone of a composited element to its background | Transforms existing content |
| Depth Blur | Applies depth-based blur | Generates depth map, transforms existing content |
| JPEG Artifacts Removal | Reduces JPEG compression artifacts | Attempts to recover pre-compression data |

**FACT.** Neural Filters that modify facial features (Smart Portrait, expression changes, age changes) generate content. A smile that was not on the subject's face is not recovered from the photograph -- it is produced by a model that has learned what smiles look like. The result may be photorealistic, but it is fabricated. The subject did not make that expression.

**Related concepts:** Generative Fill, AI Denoise, machine learning, image transformation, content generation, facial manipulation.

**Common misconception:** "Neural Filters are just enhanced traditional filters." Traditional Photoshop filters (Gaussian Blur, Unsharp Mask, High Pass) apply deterministic mathematical operations to pixel values. The same input always produces the same output, and the operation can be described mathematically. Neural Filters apply learned transformations through neural networks trained on external datasets. The same input may produce slightly different output across model versions. The operation cannot be described by a simple mathematical formula -- it is defined by the network's trained weights, which encode patterns from the training data. This is a fundamentally different category of image processing.

---

### Select Subject / Object Selection

**VERSION NOTE:** Select Subject was introduced in Photoshop CC 2018 (v19.1). Object Selection Tool was introduced in Photoshop 21.0 (November 2019). Both tools have been improved in subsequent releases with updated machine learning models. The quality of selections produced by these tools varies across versions. Specific capabilities described below may differ from your current version.

**Definition:** Select Subject and Object Selection are AI-powered selection tools that use machine learning to identify and select the primary subject or specific objects within a photograph. Select Subject analyzes the entire image and generates a selection of the detected main subject. Object Selection allows the user to draw a rough rectangle or lasso around an object, and the AI refines the selection to the object's boundaries.

**Meaning in photographic practice:** These tools are significant in the AI-tools discussion for what they do not do: they do not generate visual content. Their output is a selection boundary -- a mask that identifies which pixels belong to the subject and which belong to the background. The selection is derived entirely from the photograph's own pixel data. The AI model's contribution is pattern recognition (identifying subject boundaries), not content generation.

This makes Select Subject and Object Selection fundamentally different from Generative Fill or Neural Filters. They use machine learning to analyze the photograph, but their output does not alter, replace, or generate any pixel content. The photograph remains unchanged. The tool produces a selection that the photographer then uses with other tools.

**Photoshop implementation:** Select Subject: Select > Subject, or the "Select Subject" button in the options bar when a selection tool is active. Object Selection Tool: accessed from the toolbar (shortcut: W, cycle with Shift+W), draw a rectangle or lasso around the target object.

| Tool | Interaction | Output |
|------|------------|--------|
| Select Subject | One-click, no user guidance | Selection of detected primary subject |
| Object Selection | User draws rough boundary around target | Refined selection snapped to object edges |

Both tools produce standard Photoshop selections (marching ants) that can be refined in Select and Mask, saved as channels, or applied as layer masks using the same workflows covered in Chapters 12 and 13.

**VERSION NOTE:** Adobe has updated the underlying machine learning models for these tools across versions, significantly improving edge accuracy, hair detection, and the ability to handle complex backgrounds. If you find the selection quality inadequate, check whether a Photoshop update is available -- the difference between model generations can be substantial.

**Related concepts:** Select and Mask (Chapters 12-13), Quick Selection Tool, Color Range, channel-based selections, layer masks.

**Common misconception:** "Select Subject uses AI, so the selection is AI-generated content." The selection is a boundary identification, not content generation. The AI identifies where the subject's edges are in the photograph. It does not create, modify, or generate any pixel data. The distinction matters because using Select Subject on a documentary photograph does not introduce any AI-generated content into the image. It is an analysis tool, not a generation tool.

---

### AI Denoise

**VERSION NOTE:** AI Denoise was introduced in Adobe Camera Raw in 2023. Its capabilities, interface, processing speed, and availability have changed across updates. The feature creates a new DNG file as output. On-device versus cloud-based processing behavior may vary by version. Verify the current implementation against your Camera Raw version.

**Definition:** AI Denoise is a machine-learning-based noise reduction tool in Adobe Camera Raw that uses a neural network trained on large datasets of noisy and clean image pairs to predict what the noise-free version of the input image should look like. Unlike traditional algorithmic noise reduction (which applies mathematical smoothing based on local pixel statistics), AI Denoise produces output that is shaped by the model's learned understanding of what clean images look like.

Chapter 16 covered AI Denoise as a noise reduction technique and compared it to traditional algorithmic approaches. This chapter examines a question that Chapter 16 raised: is AI Denoise recovering information or generating it?

**Meaning in photographic practice:** AI Denoise exists in the middle of the recovery-generation spectrum. It is not generating content in the way Generative Fill does -- it is not creating objects, scenes, or elements that were never in front of the camera. But it is also not performing a purely mathematical operation on the existing pixel data the way a Gaussian blur does. It is making a prediction: given this noisy input, what does the clean version look like? That prediction is informed by the model's training data, not solely by the photograph's pixel values.

This means AI Denoise can produce "hallucinated detail" -- texture, structure, or patterns that appear photorealistic but were not present in the original signal. At moderate noise levels, this effect is negligible because the underlying signal is strong enough to guide the prediction. At extreme noise levels (very high ISO, severe underexposure), the signal may be so weak that the model's prediction is more influenced by its training data than by the actual image content. In those cases, the output may contain plausible-looking detail that was generated, not recovered.

**FACT.** Traditional noise reduction operates by smoothing: it reduces the amplitude of pixel-to-pixel variation, suppressing noise at the cost of some detail. The trade-off is fixed and predictable. AI Denoise operates by prediction: it replaces noisy pixel values with predicted clean values, potentially preserving or even enhancing the appearance of detail. The trade-off is less predictable because the model may predict detail that was not in the original signal.

**Photoshop implementation:** AI Denoise is accessed through the Detail panel in Camera Raw or via the Camera Raw filter in Photoshop. It processes the entire image, creates a new DNG file as output, and offers an Amount slider to control the strength of the AI-based noise reduction.

| Characteristic | AI Denoise | Traditional NR (Luminance slider) |
|----------------|-----------|-----------------------------------|
| Processing method | Neural network prediction | Mathematical smoothing |
| Detail preservation | High (can exceed original) | Limited (detail lost with noise) |
| Predictability | Variable (model-dependent) | High (deterministic) |
| Artifacts | Hallucinated detail, unusual textures | Smoothing, waxiness, loss of fine structure |
| Processing time | High (seconds to minutes) | Low (real-time slider) |
| Output | New DNG file | Non-destructive slider adjustment |
| Best input | RAW files | RAW or rendered files |

**Related concepts:** Noise (Chapter 16), signal-to-noise ratio (Chapter 16), luminance noise, chrominance noise, noise reduction workflow, Camera Raw Detail panel.

**Common misconception:** "AI Denoise recovers the original detail that noise was hiding." This is the single most important misconception in AI-assisted photography. AI Denoise predicts what the clean image should look like. At high signal-to-noise ratios, the prediction closely matches the original signal because the signal is strong enough to guide the model. At low signal-to-noise ratios, the model has less signal to work with and fills in more from its learned patterns. The "recovered detail" in an extremely noisy image may be partially or entirely generated by the model. It looks like photographic detail, but it is a prediction, not a recovery. This distinction rarely matters for creative or commercial purposes, but it matters fundamentally for scientific, forensic, or documentary imaging where the pixel values must represent what the sensor actually recorded.

---

### Generative Expand

**VERSION NOTE:** Generative Expand (also called Expand Image, and accessed through Generative Fill applied to canvas extensions) was introduced alongside Generative Fill capabilities. The specific feature name, access method, and quality of output have varied across Photoshop versions. In some versions, Generative Expand is a dedicated feature; in others, the same result is achieved by extending the canvas with the Crop tool and applying Generative Fill to the empty area. Verify the current access method in your version.

**Definition:** Generative Expand is the application of Generative Fill to extend the image beyond its original boundaries. The user extends the canvas (via the Crop tool, Canvas Size, or a dedicated Generative Expand interface), and the AI model generates content for the new area based on the visual context at the image edges.

**Meaning in photographic practice:** Generative Expand illustrates the recovery-versus-generation distinction with particular clarity. When you extend a photograph's canvas, there is no photographic data beyond the original frame -- the camera captured nothing outside its field of view. Every pixel in the extended area is generated by the model. The result is a photograph at the center surrounded by AI-generated content at the edges.

This is useful for practical purposes: adjusting aspect ratios, creating space for text in commercial layouts, or expanding a tight crop. It is important to understand that the expanded area has no photographic authority. The model generates plausible content -- a continuation of the sky, ground, or architecture visible at the edges -- but that content was never in front of the camera.

**Photoshop implementation:** Multiple access methods depending on version:

| Method | Steps |
|--------|-------|
| Crop tool | Select the Crop tool, drag handles beyond the image boundary to extend the canvas, then apply Generative Fill to the empty area. |
| Generative Expand (dedicated) | In versions that offer this feature, select the Crop tool and look for a "Generative Expand" or "Fill" option in the contextual taskbar. |
| Canvas Size + Generative Fill | Image > Canvas Size to add empty space, then select the empty area and apply Generative Fill. |

**FACT.** Generative Expand produces content at the edges that blends with the existing image content at the boundary. The blending is typically seamless at normal viewing distances but may show resolution differences, tonal discontinuities, or structural inconsistencies at 100% zoom or in print at large sizes.

**Related concepts:** Generative Fill, canvas size, Crop tool, aspect ratio, image extension.

**Common misconception:** "Generative Expand just extends what is already in the photo." It generates what might plausibly be beyond the frame based on what is visible at the edges. It does not recover what was actually outside the frame -- it has no access to that information. If the edge of your photograph shows the corner of a building, Generative Expand will generate a plausible continuation of that building, but the generated architecture has no relationship to the actual building. The generated content is fiction that looks like a continuation of fact.

---

### Generated Content vs Recovered Content

**Definition:** Generated content is visual information created by an AI model based on patterns learned from external training data. It is not derived from the photograph being edited. Recovered content is visual information that was present in the photograph's captured data and has been extracted, enhanced, or made visible through processing. The distinction is between information that originates in the photograph (recovered) and information that originates in the model (generated).

**Meaning in photographic practice:** This distinction is the central concept of this chapter and, arguably, the most important concept in modern digital photography ethics. Every previous chapter in this manual has dealt with operations that process the photograph's own data. Tonal adjustments, color correction, sharpening, noise reduction (traditional), selections, masks, compositing from multiple captures of the same scene -- all of these operate on or combine data that the camera recorded. Even Content-Aware Fill, which synthesizes replacement content, derives that content from the photograph's own pixels.

Generative AI breaks this model. Generative Fill, Generative Expand, and certain Neural Filters introduce content that was produced by a model trained on millions of external images. The generated content may be photorealistic. It may blend seamlessly with the surrounding photograph. But it did not come from the camera that captured this image, and it does not represent what was in front of that camera.

**FACT.** The distinction between generated and recovered content is not visible in the pixel data. There is no pixel-level marker that separates generated content from photographic content within the same image file. A viewer examining the final output cannot reliably determine which regions were photographed and which were generated. This invisibility is what makes the distinction ethically significant: the photographer is the only person who knows.

**INTERPRETATION.** The following operations produce results at different positions on the recovery-generation spectrum:

| Operation | Source of Output | Classification |
|-----------|-----------------|----------------|
| Curves adjustment | Image data, mathematically remapped | Recovery (tonal information was captured) |
| Unsharp Mask | Image data, edge contrast enhanced | Recovery (edges were captured) |
| Traditional noise reduction | Image data, smoothed | Recovery (signal was captured, noise was suppressed) |
| AI Denoise | Image data, predicted by model | Mostly recovery, partially generation (model shapes the prediction) |
| Content-Aware Fill | Image data, rearranged from surrounding area | Reconstruction (original patches, new arrangement) |
| Select Subject | Image data, analyzed for boundaries | Analysis (no content change) |
| Remove Tool | Image data and/or model, depending on version and task | Ambiguous (may be reconstruction or generation) |
| Generative Fill | Model, informed by image context | Generation (content originates in the model) |
| Generative Expand | Model, informed by edge context | Generation (content originates in the model) |
| Neural Filters (facial modification) | Model, applied to image data | Generation (modified features were not photographed) |

**Related concepts:** Photographic ethics, documentary photography, metadata, AI disclosure, Content Credentials.

**Common misconception:** "If the AI-generated content looks photorealistic, it is functionally equivalent to photographic content." Functional equivalence depends on the purpose. For a commercial advertisement, a generated sky may be functionally equivalent to a photographed sky -- both serve the image's visual purpose. For a documentary photograph, a news image, a forensic record, or a competition entry in a nature category, generated content is not functionally equivalent to photographic content regardless of its visual quality. The question is not "does it look real?" but "is it real?" and the answer for generated content is always no.

---

## Theory

### How Generative AI Differs from Traditional Algorithmic Processing

Traditional image processing in Photoshop is deterministic and mathematically describable. A Gaussian Blur applies a convolution kernel with known values. Unsharp Mask subtracts a blurred copy from the original at a specified radius and amount. Content-Aware Fill uses a patch-matching algorithm that searches for similar texture blocks in the sampling area. In every case, the same input produces the same output, and the operation can be described by a mathematical formula or a reproducible algorithm.

**FACT.** Generative AI tools use neural networks -- layered mathematical structures whose parameters (weights and biases) have been set through training on large datasets. The network's behavior is defined not by a formula that a human designed but by the statistical patterns the network learned from its training data. The network produces output that is statistically consistent with its training data, not output that is mathematically derived from the input image.

**FACT.** Generative AI output is non-deterministic. The same input (same selection, same prompt, same surrounding context) can produce different output on different runs due to randomness in the generation process. This non-determinism is inherent to the architecture, not a defect. Photoshop acknowledges it by presenting multiple variations for each generation.

**INTERPRETATION.** The non-determinism of generative output reveals something fundamental about its nature. When a tool can produce multiple different "correct" answers for the same input, none of those answers can claim to represent the ground truth. Each is a plausible possibility, not a recovery of fact. Traditional processing tools produce one answer because the operation is deterministic: there is one correct result of applying a Gaussian Blur with radius 5.0 to a given set of pixels. Generative tools produce multiple answers because they are sampling from a probability distribution, not computing a unique result.

### The Training Data Question

**FACT.** Adobe Firefly, the model underlying Generative Fill and Generative Expand, was trained on Adobe Stock images, openly licensed content, and public domain content. Adobe has stated that Firefly is designed to be commercially safe for use. The training methodology and the specific composition of the training dataset are determined by Adobe and are subject to change.

**INTERPRETATION.** The training data matters for two reasons. First, the model can only generate content that is consistent with patterns in its training data. If a visual style, subject, or scene type is underrepresented in the training data, the model will produce lower-quality or inappropriate results for those inputs. Second, the model's output carries implicit biases from the training data -- the aesthetic choices, demographic representations, and visual conventions that were common in the training set will be reflected in the generated output. The photographer using Generative Fill does not control or necessarily know what these biases are.

### What "Hallucinated Detail" Means

**FACT.** In AI and machine learning terminology, "hallucination" refers to output that the model generates with apparent confidence but that does not correspond to the input data. In image processing, hallucinated detail is visual information (texture, structure, patterns) that appears in the AI-processed output but was not present in the original photograph's signal.

**INTERPRETATION.** Hallucinated detail is not always a problem. When AI Denoise produces fine texture in a noise-reduced image that the original noisy signal did not resolve, that texture may be more visually pleasing than the smooth, featureless result that traditional noise reduction would produce. For creative and commercial purposes, this is often an improvement. For scientific, forensic, or documentary purposes, hallucinated detail is a false positive -- it looks like captured information but is actually generated information. The photographer must understand that any AI-processed image may contain detail that was predicted, not recorded.

**RECOMMENDATION.** When pixel-level accuracy matters (scientific imaging, forensic analysis, archival photography), use traditional algorithmic tools rather than AI tools. Traditional tools may produce less visually appealing results (smoother noise reduction, less impressive detail), but their output is verifiably derived from the image's own data. When visual quality matters more than pixel-level accuracy (commercial, creative, fine art), AI tools offer significant advantages, and hallucinated detail is an acceptable trade-off.

---

## Photoshop Implementation

### Generative Fill: Capabilities and Limitations

**Capabilities:**

- Object removal by selecting the object and generating a replacement background (unprompted).
- Object addition by selecting an empty area and providing a text prompt describing the desired content.
- Background replacement by selecting the background and generating a new one (prompted or unprompted).
- Scene extension by selecting areas beyond the image boundary and generating continuation content.
- Style-consistent generation that matches the lighting, color, and perspective of the surrounding image context (when the model succeeds).

**Limitations and artifacts:**

| Limitation | Description |
|------------|-------------|
| Resolution mismatch | Generated content may be softer or at a different effective resolution than surrounding photographic content. Inspect at 100% zoom. |
| Structural incoherence | The model may generate architecturally impossible structures, misaligned perspective lines, or physically implausible objects. |
| Anatomical errors | Human figures, hands, and faces in generated content frequently contain anatomical errors (incorrect finger count, asymmetric features, inconsistent proportions). |
| Text generation | The model may generate text-like elements that are nonsensical or misspelled. |
| Repetition patterns | Generated textures may contain subtle repeating patterns that do not exist in natural surfaces. |
| Boundary artifacts | Visible transitions between generated content and original photographic content, especially in areas with complex texture. |
| Inconsistent lighting | The model may generate content with lighting that does not perfectly match the surrounding scene. |
| Non-determinism | Multiple generations from the same input produce different results. There is no guarantee of convergence toward a single "correct" output. |

**VERSION NOTE:** The severity and frequency of these artifacts have decreased across Firefly model versions. They have not been eliminated. Always evaluate generated content critically at 100% zoom and in the context of the full image.

### Generative Expand: Capabilities and Limitations

Generative Expand shares all of Generative Fill's capabilities and limitations, with additional concerns specific to canvas extension:

| Concern | Description |
|---------|-------------|
| Edge continuity | The model must seamlessly continue textures, structures, and tonal gradients from the original image edge into the generated area. Success varies with content complexity. |
| Perspective continuation | Vanishing lines and perspective cues must be geometrically consistent between the original and generated content. The model approximates but does not guarantee geometric accuracy. |
| Scale asymmetry | Large extensions (doubling or tripling the canvas) produce lower-quality results than small extensions (adding 10-20% to one side). |
| Directional bias | Extension quality may vary by direction (extending sky upward may succeed where extending ground downward fails, or vice versa). |

### Neural Filters: Capabilities and Limitations

**VERSION NOTE:** The Neural Filters landscape changes across Photoshop versions. The following describes the general characteristics of Neural Filters as a category. Verify which specific filters are available and at what quality level in your version.

**Capabilities:**

- Skin Smoothing: Reduces skin texture while preserving facial structure. Operates on existing image content.
- Colorize: Assigns plausible color to black-and-white images. Generates color information.
- Style Transfer: Applies the visual aesthetic of a reference image. Transforms existing content.
- Super Resolution (in Camera Raw): Upscales resolution using AI prediction.
- Smart Portrait adjustments: Modifies expression, age, gaze, and other facial attributes.

**Limitations:**

| Limitation | Description |
|------------|-------------|
| Version instability | Filters are added, modified, and removed between versions. A workflow built on a specific Neural Filter may break with an update. |
| Quality inconsistency | Results vary significantly across different images. A filter that produces excellent results on one portrait may fail on another. |
| Artifacts near complex features | Filters that modify facial features often produce artifacts near hair, glasses, teeth, and ears. |
| Processing requirements | Some filters require significant processing time or a GPU that meets minimum specifications. |
| Beta status | Many Neural Filters carry a "Beta" designation, indicating that Adobe considers them experimental. |

### Select Subject and Object Selection: Capabilities and Limitations

**Capabilities:**

- Rapid selection of clearly defined subjects against contrasting backgrounds.
- Effective hair and fur edge detection (in recent versions).
- Object isolation within complex scenes (Object Selection Tool with user-guided boundary).

**Limitations:**

| Limitation | Description |
|------------|-------------|
| Low-contrast boundaries | When the subject and background share similar tones and colors, the AI may fail to identify the boundary. |
| Multiple subjects | Select Subject selects what the model considers the "primary" subject. In group photos or multi-subject scenes, it may select only one person or an unexpected element. |
| Edge refinement still required | AI-generated selections typically require refinement in Select and Mask for production-quality masking, especially for hair, transparency, and motion blur. |
| Model bias | The model's training data influences what it considers a "subject." Standard photographic subjects (people, animals, vehicles) are detected well. Unusual subjects may not be recognized. |

### AI Denoise: Capabilities and Limitations

Chapter 16 provided the detailed walkthrough. The key points from an AI-tools perspective:

**Capabilities:**

- Dramatically better noise-to-detail trade-off than traditional algorithmic noise reduction.
- Preservation of fine texture (pores, fabric weave, foliage detail) that traditional noise reduction would destroy.
- Recovery of usable images from extremely high ISO or severely underexposed captures.

**Limitations:**

| Limitation | Description |
|------------|-------------|
| Hallucinated detail | At extreme noise levels, the model may generate texture that was not in the original signal. This looks like recovered detail but is a prediction. |
| Processing overhead | AI Denoise creates a new DNG file and requires significant processing time. Not suitable for high-volume batch processing where speed matters. |
| Unusual texture handling | Subjects with unusual textures (highly technical, microscopic, or non-photographic imagery) may receive inappropriate treatment because they differ from the model's training data. |
| Non-reversible output | The DNG output file has the AI processing baked in. Unlike traditional slider-based noise reduction, you cannot later adjust the AI Denoise parameters without reprocessing. |

---

## Professional Workflow

### Decision Framework: When to Use AI Tools vs Traditional Tools

The choice between AI and traditional tools depends on two factors: (1) the technical capabilities of each option for the specific task, and (2) the truth-value requirements of the professional context.

**Step 1: Identify the task.**

| Task Category | Examples |
|---------------|---------|
| Selection / Masking | Isolating a subject, selecting an object for adjustment | 
| Noise reduction | Cleaning up high-ISO images, shadow noise |
| Object removal | Removing distractions, people, signs |
| Content creation | Extending canvas, replacing backgrounds, adding elements |
| Image transformation | Changing expression, aging, colorizing, style transfer |

**Step 2: Evaluate truth-value requirements.**

| Context | Truth-Value Requirement | AI Generation Acceptable? |
|---------|------------------------|---------------------------|
| Documentary / Journalistic | Maximum: pixels must represent what the sensor recorded | No. AI analysis tools (Select Subject) acceptable. AI generation tools (Generative Fill) not acceptable. |
| Nature / Wildlife competition | High: images must represent the natural scene as captured | Generally no for generated content. AI selection and denoise may be acceptable depending on competition rules. |
| Stock photography | Moderate to high: disclosure of AI-generated content is required by most agencies | Depends on agency policy. Must be disclosed. May be categorized as illustration rather than photograph. |
| Fine art | Photographer's discretion | Yes, at the artist's judgment. No external truth-value requirement, though disclosure affects how the work is received. |
| Commercial / Advertising | Low: the image serves the message | Yes. Standard practice. Client expectations and advertising standards apply. |
| Personal / Social media | Photographer's discretion | Yes. No external truth-value requirement for personal expression. |

**Step 3: Select the tool.**

| Task | Traditional Tool | AI Tool | When to Choose Traditional | When to Choose AI |
|------|-----------------|---------|---------------------------|-------------------|
| Selection | Quick Selection, Color Range, Channels, Pen tool | Select Subject, Object Selection | When AI selection does not capture the boundary accurately; complex multi-subject scenes; when you need manual control | When the subject is clearly defined and the AI produces an accurate boundary; when speed matters |
| Noise reduction | Camera Raw Luminance/Color sliders, Reduce Noise filter | AI Denoise | When the noise level is moderate and traditional sliders produce an acceptable result; when processing speed matters; when pixel-level accuracy is required | When the noise level is severe enough that traditional sliders cannot preserve acceptable detail; when the noise-to-detail trade-off is more important than pixel-level accuracy |
| Object removal | Clone Stamp, Healing Brush, Content-Aware Fill | Remove Tool, Generative Fill | When the surrounding area provides adequate source material; when the replacement content must be verifiably derived from the photograph; when truth-value matters | When the removal is complex and Content-Aware Fill cannot construct adequate replacement content; when truth-value requirements permit generated content |
| Content creation | Not available (compositing from separate photographs is the traditional approach) | Generative Fill, Generative Expand | When the content must be photographic; when you have source material to composite | When no photographic source material exists and generated content is acceptable for the purpose |
| Image transformation | Dodge and burn, Curves, Hue/Saturation, manual retouching | Neural Filters | When the transformation must remain within the bounds of the original data; when predictability matters | When the desired transformation is beyond what manual tools can achieve and the generative nature of the result is acceptable |

### Workflow Integration

**RECOMMENDATION.** Apply AI tools at the correct position in the editing workflow:

| Operation | Workflow Position | Rationale |
|-----------|-------------------|-----------|
| AI Denoise | Early: during or immediately after RAW development | Operates on raw data for best results. Must precede sharpening. |
| Select Subject / Object Selection | When you need a selection, at any point | Does not modify pixel data; produces a selection for use with other tools. |
| Remove Tool | After basic tonal and color adjustments | The tool analyzes surrounding content, which should be properly exposed and color-corrected first. |
| Generative Fill | After basic adjustments, before final color grading | Generated content should integrate with the properly adjusted image. Final grading applied globally afterward. |
| Neural Filters | Depends on the specific filter. Skin Smoothing: late in the retouching workflow. Colorize: early, before tonal adjustments. | Varies by operation. |

### Combining AI and Traditional Tools

**INTERPRETATION.** The most effective professional workflows combine AI and traditional tools rather than relying exclusively on either. AI tools provide rapid, effective results for tasks they handle well (selection, noise reduction, object removal in accommodating backgrounds). Traditional tools provide control, predictability, and verifiable output for tasks that require them (edge refinement, precise retouching near complex structures, work where truth-value matters).

A practical hybrid approach:

1. Use Select Subject for the initial selection, then refine in Select and Mask with traditional tools (Refine Edge Brush, manual mask painting).
2. Use AI Denoise for severe noise, then fine-tune with traditional Luminance and Color sliders in Camera Raw.
3. Use the Remove Tool for an initial object removal, then refine with the Clone Stamp or Healing Brush where the result is imperfect.
4. Use Generative Fill for background extension, then paint over artifacts with traditional retouching tools.

---

## Photographic Ethics

This is the most important section of this chapter. The AI tools described above give photographers the ability to produce images that are indistinguishable from unaltered photographs but that contain content created by a machine learning model. This capability demands a clear ethical framework.

### The Central Principle

**FACT.** A photograph is a record of light captured by a sensor at a specific moment in a specific place. AI-generated content is not that. It is a statistically plausible visual pattern produced by a neural network. These are different categories of visual information. No amount of photorealism in the generated content changes its origin.

**RECOMMENDATION.** Never present AI-generated content as original photographic content. This is not a suggestion -- it is a factual-accuracy standard. An image containing AI-generated content is not a photograph in the documentary sense. It may be a photograph that has been augmented, manipulated, or partially constructed, but the generated regions are not photographic data.

### Context-Specific Standards

| Context | AI Analysis Tools (Select Subject, AI Denoise) | AI Generation Tools (Generative Fill, Neural Filters) | Disclosure Requirements |
|---------|-----------------------------------------------|-------------------------------------------------------|------------------------|
| Documentary / Photojournalism | Generally acceptable (analysis aids workflow without altering content) | Not acceptable. Any generated content violates the documentary function. | Wire services (AP, Reuters) have explicit policies. Violations result in professional sanction. |
| Nature / Wildlife competitions | Acceptable in most competition rules. Verify specific rules. | Typically prohibited in nature categories. May be allowed in "open" or "creative" categories. | Competition rules specify disclosure requirements. |
| Fine art photography | Acceptable | Acceptable. The artist's creative decision. | No mandatory disclosure, but disclosure affects how the work is contextualized (as photography vs digital art). |
| Stock photography | Acceptable | Agency-specific policies apply. Most agencies require disclosure of AI-generated content. Some categorize AI-augmented images as illustrations rather than photographs. | Check the specific agency's current policies. These are evolving. |
| Commercial / Advertising | Acceptable | Acceptable. Standard practice in many commercial contexts. | Advertising standards vary by jurisdiction. Some jurisdictions require disclosure of digitally altered images, particularly in fashion and beauty advertising. |
| Forensic / Scientific | Acceptable with documentation | Not acceptable. Pixel values must represent sensor data. | Complete processing documentation is required. |

### The Metadata Question

**VERSION NOTE:** Adobe has developed Content Credentials (based on the C2PA standard) as a system for documenting the provenance and editing history of digital content, including the use of AI tools. The implementation, availability, and detail level of Content Credentials tagging have changed across Photoshop versions and are subject to ongoing development.

**FACT.** As of 2025, Photoshop can attach Content Credentials to files that document the use of AI-powered tools. When this feature is enabled, the metadata records that generative AI was used in the editing process. The completeness and reliability of this metadata depend on the specific Photoshop version, whether the feature is enabled, and whether the metadata survives export, format conversion, and distribution through various platforms.

**INTERPRETATION.** Content Credentials are a step toward transparency, but they are not currently a reliable guarantee. They can be stripped during export or conversion. Not all platforms preserve them. Not all Photoshop versions implement them. A photographer cannot rely on Content Credentials as a substitute for personal disclosure practices.

**RECOMMENDATION.** Regardless of what metadata systems are available, maintain your own documentation of AI-generated content. Note which regions of an image contain generated content and what tools were used. When licensing or publishing images, disclose AI-generated content according to the applicable standards for the context.

### Five Operations with Different Truth-Value Implications

The user instructions for this chapter identified five operations that are often conflated but have fundamentally different implications. Each is addressed here.

**1. Removing an object.** Removing a distracting element and replacing the vacated area with content derived from the surrounding image (Content-Aware Fill, Clone Stamp, Healing Brush). The replacement pixels are derived from the photograph. The image is altered (the object was there and is now gone), but the replacement content is photographic. Truth-value: the image no longer shows what was in the scene, but what it does show (the replacement area) is derived from the photograph.

**2. Reconstructing missing information.** Repairing a damaged or missing area based on what can be inferred from the photograph itself (Content-Aware Fill repairing a torn photograph, reconstructing a damaged scan). The replacement content is synthesized from the image's own patterns. Truth-value: the reconstructed area is an educated guess based on the image's own data. It is an inference, not a record.

**3. Extending an image.** Adding content beyond the original frame (Generative Expand). The generated content has no photographic source -- there was nothing beyond the frame to recover. Truth-value: the extended area is entirely generated. It has no relationship to what was actually outside the camera's field of view.

**4. Generating new content.** Adding an element that was never in the scene using Generative Fill with a text prompt. Truth-value: the added content is fabricated. It was created by a model and represents nothing that existed in front of the camera.

**5. Replacing an element.** Selecting an existing element and using Generative Fill to replace it with something different (replacing a sky, changing a background). Truth-value: the replacement content is generated. The original element that was photographed has been removed and replaced with model output.

**INTERPRETATION.** Operations 1 and 2 work with the photograph's own data and produce results that are derived from the image (though they alter its factual content). Operations 3, 4, and 5 introduce content from a generative model. The ethical implications are different, and a photographer who understands these distinctions can make deliberate, defensible decisions about when each operation is appropriate.

---

## Common Mistakes

| Mistake | Why It Happens | How to Avoid |
|---------|----------------|--------------|
| Calling AI-generated content "recovered detail" | AI Denoise and Generative Fill produce photorealistic results that feel like they reveal hidden information | Understand the difference between prediction and recovery. AI Denoise predicts what clean pixels would look like. Generative Fill creates content. Neither recovers data the sensor did not capture. |
| Using Generative Fill in documentary contexts | The results are photorealistic and the workflow feels natural | Before using any generative tool, ask: does this image need to represent what the camera recorded? If yes, generative tools are inappropriate. |
| Not inspecting AI results at 100% zoom | AI output looks convincing at screen-fitting zoom levels | Always inspect at 100% zoom. Look for resolution mismatches, anatomical errors, structural impossibilities, repetition artifacts, and boundary transitions. |
| Trusting a single generation | The first Generative Fill result looks acceptable | Generate multiple variations. Compare each for structural accuracy, lighting consistency, and artifact presence. The first result is rarely the best. |
| Over-relying on Select Subject | The one-click selection feels complete | Select Subject provides a starting point, not a finished selection. Always refine in Select and Mask. Inspect edges at 100% zoom, particularly around hair, transparency, and low-contrast boundaries. |
| Applying AI Denoise to every image | AI Denoise exists, so it must be better than nothing | AI Denoise is justified when traditional noise reduction cannot achieve an acceptable result. For low-noise images, the processing overhead and hallucination risk are not warranted. |
| Ignoring version dependencies | Assuming all AI features work the same across all Photoshop versions | AI features change between versions. The quality, availability, and behavior of every AI tool described in this chapter is version-dependent. Verify capabilities against your current installation. |
| Using Neural Filters for facial modification without considering implications | The slider interface makes expression and age changes feel like simple adjustments | A smile that was never on the subject's face is not a "correction." It is a generated expression. Modifying facial features without the subject's knowledge or consent raises ethical questions regardless of technical ease. |
| Mixing generated and photographic content without documentation | The blend is seamless and no one will notice | Your awareness of the distinction is the ethical safeguard. Document which areas contain generated content. The invisibility of the blend is the reason documentation matters. |
| Assuming AI tools eliminate the need for traditional skills | AI handles selections, noise reduction, and object removal automatically | AI tools produce starting points and first-pass results. Professional-quality output still requires understanding masking, tonal control, retouching technique, and manual refinement. AI extends what you can do; it does not replace knowing how to do it. |

---

## Summary

AI tools in Photoshop fall into two fundamentally different categories: tools that analyze the photograph's own data to assist the photographer (Select Subject, Object Selection, AI Denoise) and tools that generate new visual content using neural networks trained on external data (Generative Fill, Generative Expand, certain Neural Filters). This distinction -- recovering versus generating -- is the organizing principle of this chapter and the essential concept for any photographer using AI-assisted editing.

Traditional algorithmic processing (Content-Aware Fill, Healing Brush, Curves, Unsharp Mask) is deterministic, mathematically describable, and operates on the image's own pixel data. Its output is derived from the photograph. Generative AI processing is non-deterministic, driven by trained neural network weights, and produces output that originates in the model's training data rather than in the photograph. Its output is generated, not derived.

AI Denoise occupies a middle position: it uses a trained model to predict what the clean version of a noisy image should look like. At high signal-to-noise ratios, its output closely follows the photograph's actual signal. At low signal-to-noise ratios, the model contributes more of its own prediction, potentially introducing hallucinated detail that looks like captured information but is not.

The ethical framework is context-dependent. Documentary and journalistic photography require that pixel content represent what the sensor recorded, making AI generation tools inappropriate. Fine art and commercial photography operate under different standards where generated content may be fully acceptable. Stock photography requires disclosure under most agency policies. In all contexts, the photographer bears responsibility for knowing whether the image contains generated content and disclosing that information where appropriate.

AI features in Photoshop change rapidly between versions. Every capability, limitation, and behavioral characteristic described in this chapter is version-dependent. The conceptual framework -- the distinction between recovering photographic information and generating new visual information -- is stable. The specific tools that implement those concepts will continue to evolve.

---

## Exercises

### Foundation Exercises

1. **Identify the category.** Open five images you have edited with AI tools (or create them now). For each operation you performed, classify it as: analysis (no content change), recovery (processing the image's own data), reconstruction (rearranging the image's own data), or generation (creating new content from a model). Be specific: if you used the Remove Tool, evaluate whether the replacement content appears to be pattern-synthesized or generated.

2. **Select Subject evaluation.** Open five photographs with different subject types: a person against a clean background, a person against a complex background, an animal, an object on a table, and a group of people. Run Select Subject on each. Evaluate the selection quality at 100% zoom: where does the AI succeed, where does it fail, and what characteristics of the image predict success or failure? Refine each selection in Select and Mask and note how much manual work is required beyond the AI's initial output.

3. **Generative Fill vs Content-Aware Fill comparison.** Open an image containing a medium-complexity object to remove (a person in a park, a sign on a wall). Remove the object twice on separate layers: once with Content-Aware Fill (workspace, with manual sampling control) and once with Generative Fill (unprompted). Compare at 100% zoom. Identify: which produces more realistic texture? Which maintains better structural continuity? Can you distinguish which result was generated and which was synthesized from surrounding patterns?

4. **AI Denoise vs traditional noise reduction.** Open a high-ISO RAW file (ISO 3200 or higher). Process three versions: (a) Camera Raw Luminance slider at the minimum value needed to make the image usable, (b) AI Denoise at default Amount, (c) AI Denoise at maximum Amount. Compare all three at 100% zoom in a shadow area and a detailed area (foliage, fabric, or skin). Identify where AI Denoise preserves detail better than the traditional slider. Then look for hallucinated detail in the AI Denoise versions: areas where the processed image shows texture or structure that the noisy original does not contain, even accounting for the noise.

### Advanced Exercises

5. **Generative Expand evaluation.** Open a well-composed photograph. Extend the canvas by approximately 30% on one side using Generative Expand (or Generative Fill applied to the extended canvas). Inspect the boundary between original and generated content at 100% zoom. Note: resolution differences, tonal discontinuities, structural breaks (lines that do not continue accurately), and texture inconsistencies. Then extend a second photograph by 30% on all four sides (approximately doubling the canvas area). Compare the quality of the larger extension to the smaller one.

6. **Neural Filters critical assessment.** Open a portrait and apply two Neural Filters: Skin Smoothing and one facial modification filter (expression change, age change, or gaze direction -- whichever is available in your version). For each: (a) evaluate the visual quality of the result, (b) classify the output as transformation of existing data or generation of new data, (c) describe the artifacts you observe at 100% zoom, and (d) articulate the ethical implications of the modification if this portrait were used in a documentary context, a commercial context, and a personal context.

7. **Hybrid workflow exercise.** Open a landscape photograph that needs three operations: a distracting object removed, noise reduced in the shadow areas, and the composition adjusted by extending the canvas. Perform each operation twice: once using only AI tools (Remove Tool or Generative Fill for removal, AI Denoise for noise, Generative Expand for extension) and once using only traditional tools (Content-Aware Fill for removal, Camera Raw Luminance slider for noise, no extension possible without AI -- document this limitation). Compare the results. Where does the AI approach produce superior results? Where does it introduce artifacts the traditional approach avoids?

8. **Remove Tool provenance analysis.** Open three images with objects to remove: one with a simple, uniform background behind the object (clear sky, smooth wall), one with a textured but repeatable background (grass, pavement, brick), and one with a complex, non-repeating background (a crowd, a cluttered room). Remove the object in each using the Remove Tool. For each result, evaluate: does the replacement content appear to be synthesized from surrounding patterns (you can find matching source material in the surrounding image), or does it appear to be generated (the content is plausible but does not match any specific region of the surrounding image)?

### Blackbelt Challenge

**AI tools: full critical evaluation.** This challenge requires you to produce a single image using multiple AI tools and then rigorously audit the result.

**Part 1: Create.** Start with a photograph that requires significant work. Perform the following operations, each on a separate layer or as a separate documented step:

- Use Select Subject to isolate the primary subject.
- Use AI Denoise to clean up noise in the RAW file.
- Use the Remove Tool to remove at least two distracting elements.
- Use Generative Fill to replace one significant area of the image (a background region, a sky, or an element).
- Use Generative Expand to extend the canvas by at least 20% on one side.
- Apply one Neural Filter (any that is available and applicable).

**Part 2: Audit.** Produce a written audit of the final image that answers the following questions:

1. What percentage of the final image's pixel area contains original photographic data, and what percentage contains AI-generated content? Measure or estimate this from your layer structure.
2. For each AI operation: classify it as analysis, recovery, reconstruction, or generation. Justify each classification.
3. Inspect every AI-processed area at 100% zoom. Document every artifact you find: structural errors, resolution mismatches, hallucinated detail, boundary transitions, anatomical errors, pattern repetition.
4. For what professional contexts would this image be acceptable as delivered? For what contexts would it need additional disclosure? For what contexts would it be entirely inappropriate? Justify each answer by reference to the specific AI-generated content in the image.
5. If you removed the generative layers and reverted to only the photographic data plus traditional processing, what would the image look like? What would be lost? What would be gained in terms of photographic integrity?
6. Could a viewer, examining the final flattened output without knowledge of your editing process, identify which regions are photographic and which are generated? If not, what does that imply about the photographer's disclosure responsibility?
