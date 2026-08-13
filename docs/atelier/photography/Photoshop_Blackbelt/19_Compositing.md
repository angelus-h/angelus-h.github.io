---
title: "Chapter 19: Compositing Fundamentals"
description: "Layer alignment, perspective matching, lighting consistency, edge blending, and photographic compositing techniques in Adobe Photoshop."
---

# Chapter 19: Compositing Fundamentals

## Learning Objectives

After completing this chapter, you will be able to:

1. Distinguish between reconstructive compositing (panorama, focus stack, HDR) and creative compositing (montage, sky replacement, element insertion).
2. Evaluate composite candidates for perspective compatibility, light direction consistency, and color harmony before beginning work.
3. Use Photoshop's alignment, transform, and warping tools to integrate elements from different sources into a spatially coherent image.
4. Build edge transitions that are invisible at print resolution using feathered masks, Refine Edge, and manual brush refinement.
5. Execute a complete sky replacement using both manual compositing and Photoshop's automated Sky Replacement tool, and articulate when each approach is appropriate.
6. Produce panoramas, focus stacks, and HDR merges from multi-capture source files.
7. Apply a systematic compositing workflow that addresses perspective, lighting, color, edges, and final integration in the correct order.
8. Recognize the ethical boundaries of compositing in documentary, editorial, and commercial photography contexts.

---

## Conceptual Foundation

Compositing is the process of combining visual elements from different sources into a single unified image. Every photographer composites, whether they recognize it or not. A panorama stitched from three frames is a composite. A focus stack from twelve captures is a composite. An HDR merge from bracketed exposures is a composite. These are all acts of reconstruction: assembling a result that represents what the scene looked like to the human eye, compensating for the camera's limitations in field of view, depth of field, or dynamic range.

The second category of compositing is creation: placing elements into a scene that were never there. Replacing a flat white sky with a dramatic sunset. Inserting a product into an environment it was never photographed in. Combining a portrait subject with a background shot on a different continent. This is the domain of advertising, concept art, and visual storytelling.

Both categories demand the same technical skills. A panorama that shows visible seams fails just as completely as a fantasy composite with mismatched lighting. The physics of light, perspective, and color do not care whether your intent is documentary or fictional. The difference is ethical, not technical, and we address that distinction in Section 9.

**FACT.** The human visual system is extraordinarily sensitive to inconsistencies in lighting direction, perspective geometry, color temperature, and edge quality. A composite that violates any of these will register as "wrong" to the viewer even if they cannot articulate why.

**INTERPRETATION.** This sensitivity is both the challenge and the quality standard. If the viewer notices the compositing, the compositing has failed. The goal is seamless integration, regardless of whether the composite reconstructs reality or constructs fiction.

---

## Terminology

### Compositing

**Definition.** The process of combining two or more image elements from different sources or captures into a single unified image.

**Context.** Compositing is the umbrella term covering everything from automated panorama stitching to hand-built photomontage. In Photoshop, compositing always involves multiple layers, and the quality of the result depends on how well you match perspective, lighting, color, and edge transitions between those layers.

**How it works.** At its simplest, compositing is placing one image on top of another and masking the transition. At its most complex, it requires geometric transformation, light simulation, color science, and pixel-level edge work. The complexity scales with how different the source images are from each other. Two frames shot seconds apart from a tripod (panorama) require minimal matching. A studio portrait placed into an outdoor landscape requires matching on every axis.

**Photoshop implementation.** Every compositing operation in Photoshop uses layers, masks, and some combination of transform tools. The specific tools vary by task: Photomerge for panoramas, Auto-Align and Auto-Blend for focus stacking, Merge to HDR Pro for exposure merging, and manual layer stacking with masks for creative compositing.

**Common pitfalls.** Beginning compositors focus on selection quality and ignore perspective, lighting, and color matching. A perfectly masked element placed into a scene with the wrong perspective or light direction will never look convincing regardless of edge quality.

---

### Layer Alignment / Auto-Align

**Definition.** The process of positioning multiple layers so that their shared content registers precisely, either manually or using Photoshop's Auto-Align Layers command.

**Context.** When combining multiple captures of the same scene (focus stack, exposure bracket, panorama), the frames rarely align perfectly due to camera movement, lens distortion, or parallax. Alignment is the first technical step before any blending can occur.

**How it works.** Auto-Align Layers analyzes overlapping content between selected layers and applies geometric transformations (translation, rotation, scale, perspective correction, lens distortion compensation) to bring them into registration. It uses feature detection algorithms to identify corresponding points across layers, then computes the transformation matrix that minimizes positional error.

**Photoshop implementation.** Select the layers to align, then Edit > Auto-Align Layers. The dialog offers projection modes:

| Projection Mode | Use Case | Transformation Applied |
|---|---|---|
| Auto | General purpose, lets Photoshop decide | Variable |
| Perspective | Panoramas with strong perspective | Perspective + distortion |
| Collage | Overlapping images, rotation/scale only | Affine (no perspective) |
| Cylindrical | Wide panoramas (>120 degrees) | Cylindrical projection |
| Spherical | 360-degree or very wide panoramas | Spherical projection |
| Reposition | Tripod shots with minor shift | Translation only |

**Common pitfalls.** Auto-Align requires sufficient overlapping detail between layers. Featureless areas (clear sky, smooth walls) do not provide enough matching points. For handheld focus stacks, shoot with at least 30% content overlap between focal planes. Auto-Align can also fail when the scene contains significant moving elements between frames.

---

### Perspective Matching

**Definition.** The process of adjusting the geometric perspective of a composited element so that its vanishing points, horizon line, and foreshortening are consistent with the target scene.

**Context.** Every photograph encodes a specific camera position (height, distance, angle) through its perspective geometry. When you place an element from one photograph into another, the perspective of that element must match the perspective of the destination scene. Mismatched perspective is the single most common cause of unconvincing composites.

**How it works.** Perspective is defined by the camera's position relative to the subject. Key indicators include: the horizon line position (which encodes camera height relative to the scene), the degree of foreshortening on vertical and horizontal surfaces, the convergence angle of parallel lines, and the apparent size relationships between near and far objects. Matching perspective means transforming the inserted element so that all of these indicators are consistent with the destination scene.

**Photoshop implementation.** Use Edit > Free Transform (Ctrl/Cmd+T), then right-click for Perspective, Distort, or Warp options. For precise control, use Edit > Perspective Warp, which allows you to define planes in the image and then adjust their perspective independently. The Vanishing Point filter (Filter > Vanishing Point) lets you define perspective planes and paste/clone content that automatically conforms to those planes.

**Common pitfalls.** Photographers often match scale but forget perspective. An element can be the right size but still look wrong because it was photographed from a different height or angle. Before compositing, identify the horizon line in both source and destination images. If the horizon line is at a different relative position on the subject in each image, the perspective will never match without significant transformation, and sometimes the mismatch is too severe to correct.

---

### Light Direction Matching

**Definition.** Ensuring that the direction, quality, and intensity of illumination on a composited element is consistent with the lighting in the destination scene.

**Context.** Light direction is encoded in every photograph through highlights, shadows, and the gradation between them. The human eye is highly sensitive to lighting inconsistencies. If a subject's shadows fall to the left but the scene's shadows fall to the right, the composite will immediately read as false.

**How it works.** Light direction analysis requires examining four properties: (1) the direction of cast shadows, (2) the position of specular highlights on reflective surfaces, (3) the bright-to-dark gradation across matte surfaces, and (4) the color temperature of highlights versus shadows. All four must be consistent between the composited element and the destination scene.

**Photoshop implementation.** When the source element's lighting does not match the destination, you have limited corrective options. You can flip the element horizontally (which reverses the light direction) if the element has no text or other orientation-dependent features. You can paint additional shadows and highlights on a separate layer set to Multiply (shadows) or Screen/Soft Light (highlights) to simulate a different light angle. For subtle adjustments, Curves adjustment layers with luminosity masks can shift the tonal distribution to suggest a different light position.

**Common pitfalls.** Light direction is the hardest compositing variable to fix in post-production. It is far easier to select source images that already have compatible lighting than to attempt to re-light an element in Photoshop. When shooting elements for planned composites, photograph them under lighting conditions that match your intended destination scene.

---

### Color Matching (Between Composite Elements)

**Definition.** The process of adjusting the color characteristics (white balance, saturation, tonal range, color cast) of a composited element so that it appears to exist in the same light environment as the destination scene.

**Context.** Two photographs taken in different locations, at different times, or under different lighting will have different color characteristics even after white balance correction. In a composite, these color differences immediately reveal that the elements do not belong together. Color matching is the process of harmonizing these differences.

**How it works.** Color matching operates on three axes: (1) overall color temperature and tint (warm vs cool, green vs magenta), (2) the color of shadows versus highlights (which encodes the ambient vs direct light color), and (3) the saturation and vibrancy relationships. A sunset scene has warm highlights and cool-blue shadows; an element composited into that scene must exhibit the same color relationships or it will appear foreign.

**Photoshop implementation.** Photoshop offers several approaches. The Match Color command (Image > Adjustments > Match Color) attempts automated color matching between a source and target layer, but results are often heavy-handed. More reliable: use Curves adjustment layers clipped to the composited element, adjusting the Red, Green, and Blue channels independently to match the color relationships visible in the destination scene. Sample shadow, midtone, and highlight colors from the destination scene using the eyedropper, then shift the element's corresponding tones toward those values.

**Common pitfalls.** Matching overall color temperature while ignoring the shadow-to-highlight color shift is the most common error. In natural outdoor light, shadows are illuminated by blue sky (cool) while highlights receive direct sunlight (warm). Studio-lit subjects may have neutral shadows. Placing a neutral-shadow studio shot into a natural outdoor scene requires adding blue into the shadow tones of the composited element.

---

### Edge Blending (Feathered Masks, Refine Edge)

**Definition.** The techniques used to create natural, invisible transitions at the boundary between a composited element and its surrounding scene.

**Context.** Edges are where composites succeed or fail at the pixel level. A hard, aliased edge around a composited element instantly reveals the manipulation. Natural edges in photographs are never perfectly sharp: they exhibit subtle softness from lens optics, motion, depth of field, and atmospheric diffusion. Composite edges must replicate these characteristics.

**How it works.** Edge blending involves three concerns: (1) the hardness/softness of the mask transition, (2) the presence of fringing or halo artifacts from the original background, and (3) the interaction of the element's edge with the new background at sub-pixel level. Hair, fur, and semi-transparent edges (glass, smoke, fabric) require specialized treatment because they contain the colors of both the subject and the original background.

**Photoshop implementation.** Layer masks provide the primary edge control. The Properties panel for a mask offers Feather (adds Gaussian blur to the mask edge) and Density (reduces the mask's maximum opacity). Select and Mask workspace (Select > Select and Mask) provides Refine Edge Brush for hair/fur, plus Global Refinements (Smooth, Feather, Contrast, Shift Edge). For manual refinement, paint on the layer mask with a soft brush at reduced opacity, building the transition gradually.

**Common pitfalls.** Uniform feathering across an entire mask edge looks artificial. Real edges vary: a person's silhouette has hard edges along clothing, soft edges along hair, and semi-transparent edges through loose fabric. Use different mask treatment for different edge regions. Also, never feather the mask of an element and then place it on a high-contrast background without checking for halo artifacts from the original background color bleeding through.

---

### Sky Replacement (Manual vs Automated)

**Definition.** The compositing technique of replacing the sky area in a photograph with a different sky, either through manual selection and masking or using Photoshop's dedicated Sky Replacement tool.

**Context.** Sky replacement is among the most common compositing tasks in landscape, architectural, and real estate photography. Overcast or featureless skies are frequently replaced with more dramatic alternatives. The challenge lies in creating a convincing transition at the horizon line and matching the lighting and color of the new sky to the foreground.

**How it works.** Manual sky replacement involves selecting the sky region (using Color Range, Quick Selection, or channel-based selections), creating a layer mask, placing the replacement sky beneath the foreground layer, then matching color temperature, brightness, and edge transitions. Automated sky replacement uses machine learning to detect the sky region and applies adjustments automatically.

**Photoshop implementation.** Manual: Use Select > Color Range or the Blue channel to create an initial sky selection, refine with Select and Mask, then mask the foreground layer. Place the new sky on a layer below. Use Curves and Hue/Saturation clipped to the foreground to match color temperature with the new sky. Automated: Edit > Sky Replacement provides a one-step tool with sky presets, edge refinement, brightness/temperature adjustments, and foreground adjustment controls.

!!! note "VERSION NOTE"
    The Sky Replacement tool (Edit > Sky Replacement) was introduced in Photoshop v22.0 (October 2020). Earlier versions require manual compositing for sky replacement.

**Common pitfalls.** Both manual and automated approaches struggle with fine detail at the sky-foreground boundary: tree branches, power lines, hair, and semi-transparent elements like thin clouds overlapping foreground structures. The automated tool handles simple horizon lines well but often produces visible artifacts around complex edges. Manual compositing gives full control but requires significantly more time.

---

### Panorama Stitching (Photomerge)

**Definition.** The process of combining multiple overlapping photographs of the same scene into a single wider (or taller) image that exceeds the field of view of any individual capture.

**Context.** Panorama stitching is reconstructive compositing: you are assembling a representation of a scene that existed but could not be captured in a single frame at the desired resolution and field of view. Unlike creative compositing, there is no ethical ambiguity. You are reconstructing what was there.

**How it works.** The stitching algorithm identifies corresponding features in overlapping regions between adjacent frames, computes the geometric transformation needed to align them, projects all frames into a common coordinate space (cylindrical, spherical, or perspective), and blends the overlapping regions to produce seamless transitions. Exposure and color variations between frames are compensated during the blending step.

**Photoshop implementation.** File > Automate > Photomerge. Select source images, choose a layout mode (Auto, Perspective, Cylindrical, Spherical, Collage, Reposition), and optionally enable Blend Images Together, Vignette Removal, and Geometric Distortion Correction. Photomerge creates a layered document with each source frame on a separate layer with a layer mask defining its contribution area. For raw files, open all frames in Camera Raw first, synchronize development settings, then send to Photomerge.

**Common pitfalls.** Shooting errors that cause stitching failures: insufficient overlap (need at least 25-30% between frames), changing focal length between frames, significant parallax (rotate around the lens's nodal point, not the camera body), and moving subjects that appear in the overlap zone (ghosting). After stitching, crop to remove irregular edges, and inspect the seam lines at 100% zoom for alignment artifacts.

---

### Focus Stacking

**Definition.** The compositing technique of combining multiple photographs of the same scene taken at different focus distances, extracting the sharpest regions from each, to produce a final image with greater depth of field than any single capture.

**Context.** Optical physics limits depth of field, especially in macro photography and at wide apertures. A macro shot at f/2.8 may have a depth of field measured in millimeters. Focus stacking overcomes this by capturing a sequence of images across the focus range and compositing the sharp regions from each.

**How it works.** The camera captures a series of frames, each focused at a slightly different distance. Software analyzes each pixel position across all frames, determines which frame has the sharpest content at that position (using contrast detection), and selects that frame's pixels for the final output. The result is an image with apparent depth of field that would be physically impossible from a single capture.

**Photoshop implementation.** Load all focus frames as layers in a single document (File > Scripts > Load Files into Stack, with "Attempt to Automatically Align Source Images" checked). Select all layers, then Edit > Auto-Blend Layers with "Stack Images" selected and "Seamless Tones and Colors" checked. Photoshop creates layer masks that reveal the sharpest region from each layer. Flatten and inspect at 100% for blending artifacts, particularly at the boundaries between focus zones.

**Common pitfalls.** Focus breathing (the slight change in magnification as focus distance changes) causes misalignment between frames. Auto-Align compensates for this, but severe breathing (common in older macro lenses) can overwhelm the alignment. Shoot more frames with smaller focus increments to minimize per-frame magnification change. Moving subjects between frames (wind-blown foliage, living insects) create ghosting artifacts that require manual mask correction.

---

### HDR Merge

**Definition.** The process of combining multiple exposures of the same scene, captured at different shutter speeds, to produce a single image with a dynamic range exceeding what any single exposure can capture.

**Context.** Camera sensors have a limited dynamic range, typically 12-14 stops. Scenes with bright highlights and deep shadows (interior with windows, sunset landscapes) may exceed this range, forcing the photographer to choose between blown highlights and blocked shadows. HDR merge overcomes this by combining the highlight detail from short exposures with the shadow detail from long exposures.

**How it works.** The merge algorithm maps each pixel position across all exposures, selecting well-exposed data from whichever frame provides it. Overexposed pixels in short exposures are replaced by properly exposed pixels from longer exposures, and vice versa. The result is a 32-bit floating-point image with a dynamic range that exceeds display capability and must be tone-mapped to a displayable range.

**FACT.** HDR merge and tone mapping are distinct operations. The merge creates the high-dynamic-range data. Tone mapping compresses that data into a displayable range (8-bit or 16-bit). The quality of the final image depends on both steps, but they serve different purposes.

**Photoshop implementation.** File > Automate > Merge to HDR Pro. Select bracketed source files, enable "Attempt to Automatically Align Source Images" for handheld brackets. The result is a 32-bit image. For tone mapping, use Image > Mode > 16 Bits/Channel with the HDR Toning dialog, or (recommended) open the 32-bit file in Camera Raw, which provides the full suite of tone mapping controls (Highlights, Shadows, Whites, Blacks) with higher-quality algorithms.

!!! note "VERSION NOTE"
    For Camera Raw HDR merge, use the Camera Raw filter or open bracketed raw files directly in Camera Raw and use the Merge to HDR command from Camera Raw's filmstrip. This approach (available from Camera Raw 9.0+, refined significantly in later versions) produces a DNG file with full HDR data, which many photographers prefer over Merge to HDR Pro.

**Common pitfalls.** Over-processed HDR (the "HDR look" with flat contrast, halos around high-contrast edges, and oversaturated colors) results from aggressive tone mapping, not from the merge itself. The merge is a legitimate reconstruction of scene dynamic range. The tone mapping step is where restraint is needed. A well-executed HDR image should be indistinguishable from a single well-exposed capture of a lower-contrast scene.

---

### Transform Tools (Free Transform, Warp, Perspective)

**Definition.** The set of Photoshop tools that alter the geometric properties (scale, rotation, skew, perspective, and non-linear distortion) of layers, selections, or paths.

**Context.** Transform tools are essential to compositing because source elements almost never arrive at the correct size, angle, or perspective for the destination scene. Every creative composite requires geometric transformation of at least one element.

**How it works.** Linear transforms (scale, rotate, skew) preserve straight lines. Perspective transform maps a rectangle to a trapezoid, simulating a change in viewing angle. Warp applies non-linear distortion using a Bezier grid, allowing curves and localized bending. All transforms resample pixels (except for smart objects, which store the original data and resample on output).

**Photoshop implementation.**

| Transform | Access | What It Does |
|---|---|---|
| Free Transform | Ctrl/Cmd+T | Scale, rotate, and skew in a single operation |
| Scale | Free Transform + drag handle | Resize proportionally (hold Shift for non-proportional in recent versions, reversed from legacy behavior) |
| Rotate | Free Transform + drag outside bounding box | Rotate around the reference point |
| Skew | Free Transform > right-click > Skew | Shift edges independently along one axis |
| Distort | Free Transform > right-click > Distort | Move each corner independently |
| Perspective | Free Transform > right-click > Perspective | Move corners symmetrically to simulate perspective change |
| Warp | Free Transform > right-click > Warp, or click Warp icon in options bar | Non-linear Bezier distortion grid |
| Perspective Warp | Edit > Perspective Warp | Define planes, then adjust perspective per plane |
| Puppet Warp | Edit > Puppet Warp | Pin-based mesh deformation for organic reshaping |
| Content-Aware Scale | Edit > Content-Aware Scale | Scale while protecting detected subjects |

**Common pitfalls.** Every transform that changes pixel dimensions causes resampling degradation. Convert layers to Smart Objects before transforming so the original pixel data is preserved and resampled only once at final output, regardless of how many times you adjust the transformation. Avoid multiple sequential rasterized transforms. Also note that Photoshop changed the default behavior of Shift-drag for proportional scaling in version 20.0 (CC 2019); verify your version's behavior.

---

## Theory

### Perspective Geometry in Composites

**FACT.** Every photograph is a central projection: straight lines in the scene project to straight lines in the image, and parallel lines that recede from the camera converge toward vanishing points. The position of the horizon line in the image corresponds to the camera's height relative to the scene.

**FACT.** Two photographs of different subjects will have compatible perspective only if the camera was at the same relative height and angle to the subject in both. A portrait shot from eye level and a street scene shot from a rooftop have fundamentally incompatible perspectives.

**INTERPRETATION.** Before attempting a composite, perform a perspective audit. In the destination scene, identify: (1) the horizon line, (2) the vanishing points of receding lines, and (3) the camera height relative to the ground plane. Then verify that the source element is consistent with all three. If it is not, determine whether geometric transformation can correct the mismatch. Small perspective corrections are feasible. Large ones (such as converting a bird's-eye view to eye-level) distort the subject beyond recognition.

**RECOMMENDATION.** When shooting elements for a planned composite, match the camera height and lens focal length to the destination scene as closely as possible. Perspective mismatches that are trivial to prevent during capture are expensive or impossible to fix in post.

### Light Direction Analysis

**FACT.** The direction of illumination in a photograph is encoded in cast shadows (direction and length), specular highlights (position on curved surfaces), and the bright-to-dark gradation across forms. A single dominant light source creates unambiguous directional indicators. Multiple light sources or heavy diffusion reduce directional clarity but do not eliminate it.

**INTERPRETATION.** Light direction analysis should be performed at the planning stage, before any compositing work begins. If the source element has light coming from the upper left and the destination scene has light from the upper right, the mismatch can sometimes be resolved by horizontally flipping the source element, provided the element has no text, recognizable landmarks, or other laterally-asymmetric features. If flipping is not viable, the element is not usable for that scene.

**RECOMMENDATION.** Build a habit of reading light direction in every photograph you evaluate as a potential composite element. Ask three questions: (1) Where are the cast shadows pointing? (2) Where are the specular highlights positioned? (3) Which side of three-dimensional forms is bright versus dark? If the answers are consistent with the destination scene, proceed. If not, find a different source.

### Color Harmony Between Elements

**FACT.** The color of light varies with time of day, weather, environment, and light source type. Golden hour light is approximately 3500K. Overcast daylight is approximately 6500K. Open shade under blue sky can exceed 8000K. These differences manifest as color casts that pervade every surface in the scene.

**FACT.** In natural outdoor light, highlight and shadow color temperatures differ. Direct sunlight (warm) illuminates highlights while blue skylight (cool) fills shadows. This warm-highlight/cool-shadow relationship is a strong perceptual cue for outdoor lighting. Its absence or reversal signals artificial light.

**INTERPRETATION.** Effective color matching requires matching not just the overall color temperature but the highlight-to-shadow color shift. An element photographed under neutral studio light and placed into a golden-hour landscape will appear foreign even if you warm its overall color temperature, because its shadows will remain neutral instead of blue-tinged.

**RECOMMENDATION.** Use separate Curves adjustments for the highlights and shadows of the composited element. Sample highlight and shadow colors from the destination scene using the eyedropper, then use Curves (per channel: Red, Green, Blue) to push the element's highlights and shadows toward those sampled values. Work with luminosity masks or Blend If sliders to isolate the adjustment to the appropriate tonal range.

### Edge Integration Theory

**FACT.** Edges in real photographs are never perfectly sharp binary transitions. They are softened by lens resolution limits, diffraction, depth of field, atmospheric haze, motion, and the Bayer demosaicing process. The degree of softness varies across the image (center vs edge of frame, in-focus vs out-of-focus regions).

**FACT.** An element composited into a scene must exhibit edge softness consistent with its position in the scene's depth of field. An object in the background should have softer edges than an object in the foreground (assuming the foreground is the plane of focus).

**INTERPRETATION.** Edge treatment is not a single setting applied uniformly. It is a spatially varying property that must match the depth and focus characteristics of the scene. A composited tree on a distant hillside needs softer edges than a composited object in the foreground. The mask feathering, and potentially an additional Gaussian blur on the layer itself, must be tuned to simulate the appropriate defocus for the element's apparent distance.

**RECOMMENDATION.** After compositing an element, examine the destination scene's actual depth of field by measuring the edge softness of objects at similar apparent distances. Match the composited element's edge characteristics to those reference objects. Use the Properties panel's Feather slider for the mask, and if necessary, apply a small Gaussian blur to the composited layer itself (on a Smart Object, so it remains adjustable).

---

## Photoshop Implementation

### Setting Up a Compositing Document

**RECOMMENDATION.** Work in 16-bit mode for compositing. The additional bit depth reduces banding artifacts when adjusting tones and colors of composited elements, which typically require more aggressive correction than single-image editing.

The compositing workspace benefits from a consistent layer organization:

| Layer Group | Contents | Purpose |
|---|---|---|
| Final Adjustments | Global curves, color grading | Top of stack; affects entire composite |
| Foreground Elements | Composited foreground objects + clipped adjustments | Elements in front of the scene |
| Scene Corrections | Retouching, local adjustments to the base image | Fixes to the original scene |
| Base Scene | The destination photograph | Bottom of stack; the foundation |
| Sky / Background Elements | Replacement sky or background | Below base scene (base scene masked to reveal) |

### Alignment and Registration

For multi-capture composites (panorama, focus stack, HDR), use the automated tools first and refine manually only where they fail.

**Auto-Align workflow:**

1. Place all source frames as separate layers in a single document (File > Scripts > Load Files into Stack).
2. Select all layers in the Layers panel.
3. Edit > Auto-Align Layers > choose the appropriate projection mode.
4. Inspect alignment at 100% zoom by toggling layer visibility. Look for registration errors at high-contrast edges.
5. For layers that failed to align, use Edit > Free Transform to manually nudge them into position, using Difference blend mode temporarily to visualize misalignment (perfectly aligned areas appear black in Difference mode).

### Transform Workflow for Creative Compositing

**RECOMMENDATION.** Always convert the composited element to a Smart Object before transforming. This preserves the original pixel data and allows non-destructive re-transformation.

1. Place the source element on its own layer.
2. Right-click the layer > Convert to Smart Object.
3. Edit > Free Transform (Ctrl/Cmd+T).
4. Scale and position the element approximately. Do not finalize yet.
5. Evaluate perspective match: do the element's vanishing points and horizon line align with the scene?
6. If perspective correction is needed, right-click within the transform boundary > Perspective or Distort.
7. Commit the transform (Enter/Return).
8. If further adjustment is needed, re-enter Free Transform. Because the layer is a Smart Object, this operates on the original data, not on previously resampled pixels.

### Edge Refinement Workflow

For complex edges (hair, fur, foliage):

1. Make an initial selection of the element using any appropriate selection method (chapter 12-13 techniques apply).
2. Open Select and Mask workspace (Select > Select and Mask).
3. Use the Refine Edge Brush (second tool in the left toolbar) to paint over hair/fur edges. This tool uses edge-detection algorithms optimized for fine detail.
4. Adjust Global Refinements: Smooth (reduces jagged edges), Feather (softens transition), Contrast (sharpens the mask boundary), Shift Edge (expands or contracts the mask boundary, useful for removing fringing).
5. Set Output To: Layer Mask.
6. After applying, inspect the mask at 100% against multiple background colors. Zoom to problem areas and paint the mask manually with a soft brush where the automated result is inadequate.

For simple edges (buildings, geometric objects):

1. Create a path with the Pen tool for the cleanest possible hard edge.
2. Convert to a selection with a feather radius appropriate to the element's position in the scene's depth of field (typically 0.5-2 pixels for in-focus elements).
3. Apply as a layer mask.

### Color Matching Workflow

1. Add a Curves adjustment layer clipped to the composited element (Alt/Opt-click between the layers).
2. Set the eyedropper sample size to 5x5 Average in the options bar.
3. In the destination scene, sample a highlight area. Note the RGB values.
4. In the Curves adjustment, switch to the Red channel. Click to add a point in the highlight region, then drag it until the element's highlights match the destination's highlight red value. Repeat for Green and Blue channels.
5. Repeat steps 3-4 for a shadow area and a midtone area.
6. Add a Hue/Saturation adjustment layer (clipped) to fine-tune the saturation relationship.
7. Evaluate the result by squinting at the image. Squinting reduces detail perception and amplifies color differences. If the composited element visually "pops out" when squinting, the color match needs further work.

---

## Professional Workflow

A systematic compositing sequence addresses each matching requirement in the correct order. Working out of order wastes effort because later adjustments can invalidate earlier ones.

### Compositing Sequence

| Step | Action | Purpose | If Skipped |
|---|---|---|---|
| 1 | Source evaluation | Assess perspective, light, color compatibility before starting | You may invest hours in a composite that can never work |
| 2 | Rough placement and scale | Position the element approximately in the scene | Cannot evaluate subsequent steps without spatial context |
| 3 | Perspective matching | Transform the element to match scene geometry | Element will appear to float or tilt relative to the scene |
| 4 | Light direction check | Verify highlight/shadow consistency; flip if needed | Element will appear lit from the wrong direction |
| 5 | Rough masking | Create an initial mask to isolate the element | Cannot properly evaluate color and integration with background visible |
| 6 | Color matching | Adjust temperature, tint, shadow color, highlight color | Element will appear to be from a different location/time |
| 7 | Edge refinement | Finalize mask edges with appropriate softness and detail | Visible hard edges or halos reveal the composite |
| 8 | Shadow creation | Paint cast shadows and contact shadows from the element | Element will appear to float above the surface |
| 9 | Atmospheric integration | Add haze, depth cues, environmental effects | Element will appear "pasted on" rather than embedded in the scene |
| 10 | Global adjustments | Apply overall color grading and tonal adjustments that tie all elements together | Elements remain visually disjoint |

**RECOMMENDATION.** Steps 1 through 4 should be completed before investing significant time in masking. If the element fails the perspective or lighting evaluation, no amount of edge refinement will make the composite convincing. The evaluation steps are cheap. The execution steps are expensive. Fail fast.

---

## Step-by-Step Example: Sky Replacement with Color Matching

This example demonstrates manual sky replacement, which provides complete control over every variable. The automated tool is discussed afterward for comparison.

### Scenario

A landscape photograph with a flat, overcast white sky. The foreground (mountains, trees) has good color and exposure but the sky contributes nothing to the image. A separate photograph of a dramatic sunset sky will replace it.

### Procedure

**Step 1: Evaluate Source Compatibility**

Open both images. Assess:

- Is the light in the sunset sky consistent with the foreground light direction? The foreground has diffuse light (overcast), which is non-directional. This is fortunate: non-directional foreground light is compatible with most sky directions. Directional foreground light (strong side-lighting) would require a sky with a sun position consistent with that light direction.
- Is the color plausible? A warm sunset sky above a foreground with blue-white overcast color requires color correction of the foreground to establish consistency.
- Is the perspective compatible? The horizon line in the sky image should be near the horizon of the landscape, and the sky should not contain perspective-dependent elements (such as buildings) that would reveal a different camera angle.

**Step 2: Prepare the Canvas**

1. Open the landscape image. Duplicate the background layer (Ctrl/Cmd+J). Name it "Foreground."
2. Place the sky image on a new layer below the Foreground layer. Name it "Sky." Scale and position the sky to cover the area that will be revealed when the landscape's sky is masked out.

**Step 3: Create the Sky Mask**

1. Select the Foreground layer.
2. Open the Channels panel. Examine each channel (Red, Green, Blue) to find the one with the greatest contrast between sky and foreground. For an overcast white sky against darker landscape, the Blue channel typically offers the best separation.
3. Duplicate the Blue channel (drag to the "Create New Channel" icon). This creates a working alpha channel.
4. Apply Levels (Ctrl/Cmd+L) to the alpha channel: drag the white point slider left and the black point slider right to push the sky toward pure white and the foreground toward pure black. Work gradually to avoid destroying edge detail.
5. Paint with a black brush to fill any remaining white areas in the foreground (areas that should be masked).
6. Paint with a white brush to fill any remaining dark areas in the sky region.
7. Ctrl/Cmd-click the alpha channel thumbnail to load it as a selection.
8. Return to the Layers panel. Select the Foreground layer. Click the "Add Layer Mask" button. This creates a mask that hides the original sky, revealing the new sky layer below.

**Step 4: Refine the Mask Edge**

1. Select the Foreground layer's mask. Open Select and Mask (Select > Select and Mask).
2. Use the Refine Edge Brush along the tree line / horizon boundary to capture fine detail (branches, foliage).
3. Apply a small Feather (0.5-1.5 pixels) to soften the transition.
4. Use Shift Edge (-10% to -30%) to contract the mask slightly, eliminating the bright fringe of original sky color that clings to edge pixels.
5. Output to Layer Mask (replace existing).

**Step 5: Color Match the Foreground to the New Sky**

This is the critical step that most beginners skip. The foreground was lit by an overcast sky (cool, neutral, flat). It now sits beneath a sunset sky (warm, directional, saturated). The foreground must be color-corrected to appear as if it was actually lit by sunset light.

1. Add a Curves adjustment layer above the Foreground layer, clipped to it (Alt/Opt-click between layers).
2. In the Red channel: raise the curve slightly in the highlights and midtones to add warmth.
3. In the Blue channel: lower the curve in the highlights and midtones to reduce the cool cast and add golden tone.
4. In the RGB (composite) channel: darken the overall curve slightly, because a sunset sky is dimmer than midday overcast, and the foreground should reflect this.
5. Add a Color Balance adjustment layer (clipped to Foreground): push Highlights toward Yellow and Red. Push Shadows toward Blue (to maintain the natural warm-highlight/cool-shadow relationship).
6. Add a Photo Filter adjustment layer (clipped): choose a Warming Filter (85) at 15-25% to unify the foreground color with the sky's warmth.

**Step 6: Add Atmospheric Integration**

1. Create a new layer between Sky and Foreground. Name it "Horizon Haze."
2. Sample a color from the base of the new sky where it meets the horizon (typically a pale warm tone in a sunset).
3. Using a large, soft brush at low opacity (5-10%), paint this color along the horizon line where sky meets land. This simulates atmospheric haze and prevents a hard color transition at the horizon.
4. Set this layer's blend mode to Screen or Normal depending on the desired intensity.

**Step 7: Final Evaluation**

1. Zoom to 100% and scroll along the entire sky-foreground boundary. Look for fringing, hard edges, or missed masking areas.
2. Zoom out to full image view. Squint. Does the foreground appear to belong under this sky? If the color match is off, it will be obvious when squinting.
3. Toggle the Sky layer's visibility off and on. The foreground should look naturally warm (with the sky), not artificially corrected.

### Comparison: Manual vs Automated Sky Replacement

| Aspect | Manual Compositing | Photoshop Sky Replacement Tool |
|---|---|---|
| Edge quality at fine detail | Full control; can use Refine Edge, manual painting, channel-based selection | Automated; good for simple horizons, weaker on trees/foliage |
| Color matching | Complete control over foreground color adaptation | Provides Brightness, Temperature, and Foreground Adjustments sliders |
| Sky positioning | Full control over scale, position, and rotation | Limited repositioning within the tool |
| Custom sky images | Any image you choose | Your own images or Photoshop's presets |
| Time investment | 20-60 minutes depending on edge complexity | 2-5 minutes |
| Appropriate use | Critical commercial work; complex edges; full creative control | Quick evaluations; simple horizons; social media output |
| Output | Non-destructive layers and masks | Creates a Sky layer group with masks and adjustment layers |

!!! note "VERSION NOTE"
    Photoshop's Sky Replacement tool (Edit > Sky Replacement) was introduced in v22.0 (October 2020). It provides a non-destructive output option that creates editable layers, allowing you to refine its automated result with manual techniques. For critical work, using the automated tool as a starting point and then refining manually can save time while maintaining quality.

---

## Common Mistakes

| Mistake | Why It Happens | How to Avoid |
|---|---|---|
| Ignoring perspective mismatch | Focusing on edge quality before evaluating geometry | Perform perspective audit before any masking work |
| Uniform mask feathering | Applying the same feather value to all edges | Vary feathering by edge region: hard edges on architecture, soft on hair, very soft on out-of-focus elements |
| No foreground color correction after sky replacement | Treating sky replacement as only a masking task | Always adjust foreground color to match the new sky's lighting character |
| Mismatched noise/grain | The composited element has different noise characteristics (smoother or grainier) than the scene | Add or reduce noise on the composited element to match the scene's grain structure |
| Missing cast shadows | Composited objects appear to float without contact shadows | Paint cast and contact shadows on a Multiply layer, sampling shadow color from the scene |
| Scale inconsistency | Placing an element at a size inconsistent with its apparent distance in the scene | Use known-size reference objects in the scene to calibrate the element's scale |
| Light direction contradiction | Shadows and highlights on the element conflict with the scene's light | Evaluate light direction before starting; reject incompatible source elements |
| Over-processing HDR | Aggressive tone mapping produces the characteristic "HDR look" | Use restrained tone mapping; the result should look like a well-exposed single capture |
| Visible seams in panoramas | Exposure or white balance differences between frames | Synchronize Camera Raw settings across all frames before stitching |
| Ignoring atmospheric perspective | Distant composited elements appear as sharp and saturated as foreground elements | Reduce contrast, shift toward blue, and reduce saturation for elements at increasing apparent distance |

---

## Photographic Ethics

Compositing changes the factual content of an image. This demands ethical consideration that purely tonal or color adjustments do not.

### The Reconstructive-Creative Distinction

**FACT.** Reconstructive compositing (panorama, focus stack, HDR merge) produces an image that represents what the scene looked like to the human eye. The camera's limitations (field of view, depth of field, dynamic range) are compensated. No element is added that was not present. The factual content of the image is preserved.

**FACT.** Creative compositing (sky replacement, element insertion, background replacement) produces an image that depicts something different from what existed at the time of capture. The factual content of the image is altered.

**INTERPRETATION.** The ethical implications of this distinction depend entirely on context and disclosure.

### Context-Dependent Ethics

| Context | Reconstructive Compositing | Creative Compositing |
|---|---|---|
| Photojournalism / Documentary | Generally accepted (panorama, HDR for scene reconstruction) | Prohibited. Any alteration of factual content violates the documentary function. |
| Editorial (magazine covers, features) | Accepted | Increasingly common but requires disclosure. Major publications have been embarrassed by undisclosed compositing. |
| Fine art | Accepted | The artist's creative decision. No ethical issue if the work is not presented as documentary. |
| Commercial / Advertising | Accepted | Standard practice. The image serves the message, not the truth. Disclosure requirements vary by jurisdiction and medium. |
| Real estate / Product photography | Accepted | Ethically and sometimes legally problematic. Replacing a gray sky with blue is common but misrepresents the property. Regulations vary. |
| Photo competitions | Usually accepted, varies by category | Typically restricted to "open" or "creative" categories. Entering a composite in a "nature" or "documentary" category violates competition rules and can result in disqualification and bans. |
| Social media / Personal | Accepted | No ethical issue for personal creative expression, provided you are not deceiving someone who relies on the image for factual information. |

**RECOMMENDATION.** The guiding principle is disclosure and context. If the viewer would reasonably expect the image to represent reality (news, documentary, nature competition, real estate listing), creative compositing is deceptive. If the context makes the creative nature obvious or expected (advertising, fine art, fantasy, personal expression), compositing is a legitimate creative tool. When in doubt, disclose.

---

## Summary

Compositing is the integration of multiple image elements into a unified whole. It ranges from the reconstructive (panorama, focus stack, HDR, all of which reconstruct what the scene actually looked like) to the creative (sky replacement, montage, element insertion, which construct scenes that never existed).

Successful compositing requires matching five properties between source and destination: perspective geometry, light direction, color harmony, edge quality, and atmospheric consistency. These must be evaluated and addressed in order, because later adjustments depend on earlier ones being correct. Perspective and lighting should be evaluated before any significant masking work begins, because these mismatches cannot be resolved with better edge refinement.

Photoshop provides automated tools for common compositing tasks (Photomerge for panoramas, Auto-Blend for focus stacks, Merge to HDR Pro for HDR, Sky Replacement for automated sky compositing) and manual tools for creative compositing (Free Transform, Perspective Warp, Warp, layer masks, Select and Mask, Curves for color matching). The automated tools handle reconstructive compositing well. Creative compositing, with its greater demands for cross-image matching, typically requires manual work.

Edge quality, while important, is not the primary determinant of composite success. Perspective, lighting, and color matching are. A perfectly masked element with mismatched perspective will always look wrong. A roughly masked element with perfect perspective and lighting can look convincing.

Ethics are context-dependent. Reconstructive compositing is almost universally accepted. Creative compositing is a legitimate artistic and commercial tool but becomes deceptive when presented as documentary truth. Disclose when the viewer would reasonably expect an unaltered image.

---

## Exercises

### Foundation Exercises

1. **Perspective analysis.** Open five photographs from different sources. For each, identify the horizon line, estimate the camera height, and locate any vanishing points. Rank them in order of perspective compatibility for compositing with each other.

2. **Light direction reading.** Collect ten photographs of people or objects with visible directional lighting. For each, determine the primary light direction using cast shadows, specular highlights, and form gradation. Sort them into groups that share compatible light direction.

3. **Color matching practice.** Take two photographs in different lighting conditions (e.g., indoor tungsten and outdoor daylight). Composite a small element from one into the other. Use Curves adjustment layers (per channel) to match the element's color to the destination scene. Evaluate by squinting.

4. **Focus stack.** Photograph a small object (coin, flower, pen) on a table using the widest aperture your lens allows. Take 8-10 frames, each focused at a slightly different distance across the object. Process in Photoshop using Auto-Align and Auto-Blend > Stack Images. Evaluate the blending at 100%.

5. **Panorama stitching.** Shoot a 3-frame panorama of a scene with good detail across the frame. Stitch using File > Automate > Photomerge. Inspect the seams at 100% zoom. Then intentionally shoot a 3-frame panorama with insufficient overlap (10%) and attempt to stitch. Observe the failure mode.

### Advanced Exercises

6. **Sky replacement (manual).** Starting from a landscape with an overcast sky, execute a full manual sky replacement following the step-by-step procedure in this chapter. Pay particular attention to the foreground color correction: the foreground must appear naturally lit by the new sky.

7. **Sky replacement (automated vs manual comparison).** Perform the same sky replacement using both the manual method and Photoshop's Sky Replacement tool. Compare the results at 100% zoom along the horizon boundary. Document where each method succeeds and where it fails.

8. **Object compositing with shadow creation.** Photograph an object on a neutral background. Cut it out and composite it onto a photograph of a desk or table. Match perspective and color, then create a realistic cast shadow and contact shadow using Multiply layers. The shadow direction and softness must match the scene's existing shadows.

9. **HDR processing comparison.** Shoot a 5-exposure bracket of a high-contrast scene. Process it three ways: (a) Merge to HDR Pro with default tone mapping, (b) Camera Raw HDR merge with restrained processing, (c) manual luminosity-mask blending of two exposures. Compare the results for naturalness and artifact severity.

10. **Atmospheric perspective simulation.** Composite a building or tree onto a landscape photograph at three apparent distances: foreground, middle distance, and far background. For each distance, adjust contrast, saturation, color shift (toward blue), and edge softness to simulate atmospheric perspective. The three copies should look like they were actually photographed at their apparent distances.

### Blackbelt Challenge

**Multi-element composite from scratch.** Create a composite landscape image containing at least four elements from four different source photographs: a foreground, a middle-ground subject, a background, and a sky. Each element must come from a different capture. The final image must satisfy all five matching criteria simultaneously:

1. Perspective: all elements share consistent vanishing points and horizon line.
2. Lighting: all elements are lit from the same direction with consistent shadow angles.
3. Color: all elements share consistent color temperature, shadow color, and highlight color.
4. Edges: all transitions are invisible at 100% zoom on a 300 PPI print-resolution file.
5. Atmosphere: depth cues (haze, contrast reduction, color shift) are consistent with each element's apparent distance.

Deliver the final composite as a layered PSD file with all adjustment layers, masks, and Smart Objects intact and editable. A viewer examining the final flattened output should not be able to identify it as a composite without being told.
