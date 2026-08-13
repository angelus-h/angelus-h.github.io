---
title: "Chapter 13: Advanced Masking"
subtitle: "Luminosity Masks, Channel-Based Selections, and Edge Refinement"
chapter: 13
tags:
  - photoshop
  - masking
  - luminosity-masks
  - channels
  - edge-refinement
---

# Chapter 13: Advanced Masking

## Learning Objectives

By the end of this chapter, you will be able to:

1. Construct luminosity masks manually from the composite channel and understand the mathematics behind each mask level.
2. Derive narrowed Lights, Darks, and Midtones masks through self-intersection.
3. Use Apply Image and Calculations to combine channels into precision masks.
4. Select the maximum-contrast channel for channel-based masking of complex subjects.
5. Refine mask edges for hair, fur, and semi-transparent subjects using Select and Mask.
6. Apply Decontaminate Colors to eliminate colour fringing on extracted subjects.
7. Build a professional luminosity masking workflow for landscape tonal control.

---

## Conceptual Foundation

Chapter 05 established that masks are grayscale images controlling visibility: white reveals, black conceals, grey partially reveals. Chapter 12 covered selection tools that produce binary or anti-aliased boundaries. Both chapters dealt primarily with *spatial* masking --- defining *where* an effect applies based on edges, shapes, or painted regions.

This chapter crosses into a fundamentally different territory: **tonal masking** and **channel-derived masking**, where the image's own pixel data generates the mask. The distinction is critical.

A painted mask says: "Apply the effect inside this boundary I drew." A luminosity mask says: "Apply the effect proportionally to how bright each pixel already is." The mask is not drawn --- it is *derived* from the image. Every pixel's mask value is determined by its own luminance, producing graduated transitions that are inherently seamless because they follow the tonal structure the image already contains.

Channel-based masking extends this principle. Instead of using overall luminance, you examine individual colour channels to find where a subject naturally separates from its background. A blonde subject against a blue sky may be nearly invisible in the Red channel but sharply defined in the Blue channel. The image contains its own best mask --- you just need to find it.

These techniques represent the highest tier of masking precision. They are not more complex versions of the Quick Selection tool. They are a different paradigm: the image masks itself.

---

## Terminology

### Luminosity Mask

**Definition:** A grayscale mask derived from the image's own tonal values, where each pixel's mask opacity equals its luminance. Unlike a painted or selection-based mask, a luminosity mask contains no hard edges --- every transition is graduated, mirroring the tonal transitions present in the image itself.

**How it works:** Loading the RGB composite channel as a selection (Ctrl/Cmd-click the RGB thumbnail in the Channels panel) produces the base Lights mask. The selection is not binary: a pixel with a luminance of 200/255 is 78% selected, a pixel at 128/255 is 50% selected, and a pixel at 30/255 is 12% selected. When saved as an alpha channel, this creates a smooth grayscale mask where brighter areas are whiter (more selected) and darker areas are blacker (less selected).

**Why it matters:** Luminosity masks produce adjustments that look natural because the mask intensity follows the image's own tonal structure. An adjustment applied through a luminosity mask affects bright areas strongly and dark areas weakly (or vice versa) with transitions that exactly match the existing luminosity gradient. No manual feathering is needed because the feathering is inherent.

**Common misconception:** "A luminosity mask selects the bright parts of the image." This is misleading. A luminosity mask does not "select" in the binary sense. It creates a graduated opacity map. A pixel at mid-brightness is partially masked, not excluded. The mask is continuous, not thresholded.

**Photoshop location:** Channels panel (Ctrl/Cmd-click RGB composite to load as selection) > Save Selection as alpha channel. Narrower masks are built by intersecting this base selection with itself.

---

### Lights / Darks / Midtones Masks

**Definition:** The three fundamental families of luminosity masks, each targeting a different tonal range. Lights masks target highlights with graduated falloff into midtones. Darks masks target shadows with graduated falloff into midtones. Midtones masks target the central tonal range, falling off toward both highlights and shadows.

**How it works:**

| Mask | Derivation | What it targets |
|------|-----------|-----------------|
| Lights (L1) | Ctrl/Cmd-click RGB composite | Highlights strongest, graduated falloff |
| Lights 2 (L2) | Ctrl/Cmd-Shift-Alt-click RGB with L1 active | Narrower highlight range |
| Lights 3 (L3) | Repeat intersection with RGB | Even narrower --- near-white only |
| Darks (D1) | Invert L1 (Ctrl/Cmd-I on the alpha channel) | Shadows strongest, graduated falloff |
| Darks 2 (D2) | Intersect D1 with itself | Narrower shadow range |
| Midtones (M1) | Subtract L1 from D1 (or intersect full white minus L1 minus D1) | Central tones, peaks around 50% |

Each successive level (L2, L3, D2, D3) produces a narrower, more targeted mask. There is no fixed number of levels --- you create as many as needed.

**Why it matters:** Different parts of a landscape image need different treatments. Highlights may need recovery, shadows may need opening, and midtones may need contrast enhancement. Luminosity mask families let you apply adjustments to each tonal range independently, with perfect graduated transitions between them.

**Common misconception:** "Lights 2 targets brighter highlights than Lights 1." Not exactly. L2 does not shift the target range upward --- it *narrows* it. L2 still peaks at pure white, but it falls off more steeply, so midtone pixels that had partial selection in L1 have much less selection in L2. The peak does not move; the transition steepens.

**Photoshop location:** Built manually in the Channels panel using the intersection loading technique (Ctrl/Cmd-Shift-Alt-click), or via Apply Image operations on alpha channels.

---

### Channel-Based Selection

**Definition:** A masking technique that uses a single colour channel (Red, Green, or Blue) or a derived channel as the basis for a selection, exploiting natural contrast differences between channels to isolate subjects that would be difficult to mask by other means.

**How it works:** Every RGB image contains three grayscale channels. In each channel, the brightness of a pixel represents the intensity of that colour component. A red flower against green foliage will appear bright in the Red channel and dark in the Green channel. The background foliage will show the opposite pattern. By choosing the channel with maximum subject-background contrast, duplicating it, and pushing its contrast further with Levels or Curves, you can produce a near-binary mask from image data that would be extremely difficult to select spatially.

**Why it matters:** Channel-based selection is often the only effective approach for complex subjects: hair against varied backgrounds, tree branches against sky, translucent fabric, smoke, or any subject where spatial selection tools fail because the boundary is too intricate or semi-transparent.

**Common misconception:** "Always use the Blue channel for sky masking." This is not universally true. While the Blue channel often provides good sky contrast, the best channel depends on the specific image. A sunset sky might have more useful contrast in the Red channel. Always examine all three channels before choosing.

**Photoshop location:** Channels panel > click individual channel thumbnails to view them as grayscale > duplicate the best channel > adjust contrast with Levels (Ctrl/Cmd-L) > load as selection (Ctrl/Cmd-click).

---

### Apply Image

**Definition:** A command that blends a source image, layer, or channel into the current target using a selectable blending mode. It operates on the entire image or selection at once and can combine channels from different layers, documents, or alpha channels into a single result.

**How it works:** Apply Image takes a source (a channel from any open document of identical pixel dimensions) and blends it into the current target channel or layer using a chosen blending mode (Multiply, Screen, Add, Subtract, and others). It can also invert the source before blending. The operation executes in a single pass across every pixel.

For luminosity mask creation, Apply Image is used to intersect masks: applying a Lights mask to itself using Multiply darkens the mid-values (50% x 50% = 25%), narrowing the selected range. This is mathematically equivalent to the Ctrl/Cmd-Shift-Alt-click intersection method but is more controllable and undoable.

**Why it matters:** Apply Image is the precision tool for combining channels and masks. It gives explicit control over the blending mode, opacity, and source/target pairing that the keyboard-shortcut intersection method does not. For complex mask derivations --- especially Midtones masks --- Apply Image is more reliable than manual channel arithmetic.

**Common misconception:** "Apply Image and Calculations do the same thing." They are related but different. Apply Image blends a source *into* the current target, modifying it in place. Calculations blends two sources and outputs the result to a *new* channel, selection, or document, leaving the sources unchanged.

**Photoshop location:** Image > Apply Image. The dialog shows Source (document, layer, channel, Invert checkbox), Target (current active channel/layer), Blending mode, and Opacity.

---

### Calculations

**Definition:** A command that blends two source channels using a selectable blending mode and outputs the result as a new channel, selection, or new document. Unlike Apply Image, Calculations is non-destructive to both sources --- it always produces a new output.

**How it works:** Calculations takes two source inputs (Source 1 and Source 2), each specifiable as any channel from any open document of matching pixel dimensions. It blends them using a chosen blending mode and sends the result to a new alpha channel, a new selection, or a new document. Both sources can optionally be inverted before blending.

**Why it matters:** Calculations is ideal for generating masks from channel combinations without modifying any existing channels. You can blend the Red channel with an inverted Blue channel using Multiply to create a mask that isolates warm tones against cool backgrounds --- all without touching the original image data.

**Common misconception:** "Calculations is the old way to do things --- Select and Mask replaced it." These tools serve entirely different purposes. Calculations creates masks from channel mathematics. Select and Mask refines the edges of existing selections. They are complementary, not competing.

**Photoshop location:** Image > Calculations. Dialog shows Source 1 (document, layer, channel, Invert), Source 2 (same options), Blending mode, and Result (New Channel / Selection / New Document).

---

### Edge Refinement

**Definition:** The process of improving the boundary quality of a mask or selection, particularly along complex edges where hair, fur, foliage, or semi-transparent materials create boundaries that are not clean lines but zones of partial transparency. In Photoshop, the primary tool is Select and Mask (previously Refine Edge).

**How it works:** Select and Mask analyses the boundary zone of a selection and attempts to determine which pixels belong to the subject and which belong to the background. It uses radius-based sampling: within the specified edge detection radius, it evaluates pixel colour and contrast to rebuild the mask with per-pixel accuracy. The Smart Radius option varies the detection width automatically --- narrow along hard edges, wide along soft or intricate edges.

**Why it matters:** No initial selection method --- whether spatial, tonal, or channel-based --- produces perfect edges on complex subjects. Edge refinement is almost always the final step. Without it, hair extractions show hard cut-lines, colour fringes persist, and fine strands are lost.

**Common misconception:** "Higher Radius values give better results." Excessive radius creates halos and bleeds background colour into the subject. The radius should be just wide enough to encompass the transition zone --- typically 1-5 px for hard edges, 10-30 px for moderately complex hair, higher only for very wispy subjects. The Refine Edge Brush (within Select and Mask) lets you paint the radius selectively where needed.

**Photoshop location:** Select > Select and Mask (or click "Select and Mask" in the Options bar with any selection tool active). VERSION NOTE: In versions before CC 2015.5, this was Select > Refine Edge. The underlying algorithm is similar but the workspace interface differs significantly.

---

### Decontaminate Colors

**Definition:** A feature within Select and Mask that replaces colour fringe pixels along a mask edge with colours sampled from the subject interior, eliminating the halo of background colour that typically remains after masking complex edges.

**How it works:** When a subject is photographed against a coloured background, light from the background contaminates the edge pixels of the subject. A dark-haired person against a bright blue sky will have blue-tinted edge pixels. Masking can make these pixels partially transparent, but they still carry the background colour. Decontaminate Colors identifies these fringe pixels and shifts their hue/saturation toward the subject's interior colours, making the extraction look natural against any new background.

**Why it matters:** Colour contamination is the telltale sign of a composite. Even a perfect mask shape fails if edge pixels carry the wrong colour. Decontaminate Colors is often the difference between a convincing extraction and an obviously cut-out subject.

**Common misconception:** "Decontaminate Colors is non-destructive." It is not. This option forces output to a new layer with a layer mask (it cannot modify the original layer non-destructively because it alters pixel colour data). Always keep your original layer intact below.

**Photoshop location:** Select and Mask workspace > Output Settings section > Decontaminate Colors checkbox + Amount slider. Only available when Output To is set to "New Layer with Layer Mask" or "New Document."

---

### Mask Intersection

**Definition:** The operation of combining two or more masks so that only the area where all masks overlap remains active. Mathematically, intersection multiplies the mask values: if Mask A has a pixel at 80% and Mask B has the same pixel at 60%, the intersection produces 48% (0.80 x 0.60 = 0.48).

**How it works:** In Photoshop, intersection is performed by loading one mask as a selection and then intersecting it with another using Ctrl/Cmd-Shift-Alt-click on the second mask's channel thumbnail. The result is a selection where each pixel's "selectedness" is the product of both masks' values at that pixel. This is the core mechanism for narrowing luminosity masks: intersecting L1 with itself produces L2 because each pixel's value is squared (a pixel at 80% becomes 64%, a pixel at 50% becomes 25%).

**Why it matters:** Intersection is the primary operation for building targeted masks. Every narrower luminosity mask is an intersection. Combining a luminosity mask with a spatial mask (painted area) creates a mask that applies an adjustment only within a specific region *and* proportionally to luminance within that region. This combination of spatial and tonal control is the hallmark of professional retouching.

**Common misconception:** "Intersecting a mask with itself just produces the same mask." It does not. Self-intersection squares every value: 80% becomes 64%, 50% becomes 25%, 20% becomes 4%. The mask contracts toward pure white, effectively narrowing the selected range.

**Photoshop location:** Channels panel > Ctrl/Cmd-click first alpha channel to load > Ctrl/Cmd-Shift-Alt-click second alpha channel (or the same channel again for self-intersection). Also achievable via Apply Image with Multiply blending mode.

---

### Tonal Selection vs Spatial Selection

**Definition:** Two fundamentally different paradigms for defining where an adjustment applies. Spatial selection defines a *location* in the image (a region, boundary, or painted area). Tonal selection defines a *brightness range* across the entire image. A spatial selection says "this area." A tonal selection says "this brightness level, wherever it appears."

**How it works:** Spatial selections are created with the Marquee, Lasso, Pen, Quick Selection, or Object Selection tools, or by painting a mask. They define geography. Tonal selections are created by loading channel data as selections (luminosity masks) or using colour/tonal range selectors. They define a brightness or colour criterion that applies everywhere simultaneously.

| Characteristic | Spatial Selection | Tonal Selection |
|---|---|---|
| Defines | Location / region | Brightness / colour range |
| Boundary type | Edge-based | Graduated / continuous |
| Created by | Drawing, painting, AI detection | Channel loading, Apply Image, Select > Color Range |
| Typical use | Isolating objects | Tonal adjustments, exposure blending |
| Transition quality | Depends on feathering | Inherently seamless |

**Why it matters:** The most powerful masking workflows combine both paradigms. A luminosity mask controls *how much* an adjustment applies based on tone. A painted mask or selection constrains *where* it applies spatially. Together, they provide simultaneous tonal and geographic control over every adjustment.

**Common misconception:** "Luminosity masks replace the need for painted masks." They do not. A luminosity mask for highlight recovery affects highlights *everywhere* in the image. If you need recovery only in the sky, you combine the luminosity mask (tonal) with a gradient or painted mask (spatial) to restrict the adjustment's geography.

**Photoshop location:** Spatial: any selection or painting tool + layer mask. Tonal: Channels panel selection loading, Select > Color Range, blend-if sliders (Layer Style).

---

## Theory

### How Ctrl/Cmd-Click RGB Produces a Lights Mask

FACT: When you Ctrl/Cmd-click the RGB composite thumbnail in the Channels panel, Photoshop loads a selection where each pixel's selection percentage equals its luminance value scaled to the 0--255 range.

The mathematics are straightforward. Photoshop calculates a composite luminance value for each pixel. For an sRGB image, this follows the standard luminance coefficients:

```
L = (0.2126 x R) + (0.7152 x G) + (0.0722 x B)
```

VERSION NOTE: The exact coefficients may vary depending on the working colour space profile. The formula above applies to sRGB/Rec. 709. Adobe RGB and ProPhoto RGB use the same coefficient set for luminance derivation within Photoshop's internal processing.

A pixel with L = 255 is 100% selected. A pixel with L = 0 is 0% selected. A pixel with L = 128 is approximately 50% selected. This is the base Lights mask (L1).

INTERPRETATION: The resulting selection is not a threshold --- it is a continuous gradient. There are no hard edges anywhere in the mask. Every pixel is selected proportionally to its brightness. This is why luminosity mask adjustments blend seamlessly: the mask literally *is* the tonal structure of the image.

### Narrowing by Self-Intersection

FACT: When you load L1 as a selection and then Ctrl/Cmd-Shift-Alt-click the RGB composite again, Photoshop intersects the existing selection with a new Lights load. Each pixel's selection value is the product of both operations.

Mathematically, if a pixel's luminance produces a selection value of *v* (where *v* is between 0 and 1):

| Mask | Operation | Pixel value formula | Value at L=200 (v=0.784) | Value at L=128 (v=0.502) | Value at L=50 (v=0.196) |
|------|-----------|-------------------|--------------------------|--------------------------|-------------------------|
| L1 | Load RGB | v | 0.784 | 0.502 | 0.196 |
| L2 | L1 intersect RGB | v^2 | 0.615 | 0.252 | 0.038 |
| L3 | L2 intersect RGB | v^3 | 0.482 | 0.126 | 0.008 |
| L4 | L3 intersect RGB | v^4 | 0.378 | 0.063 | 0.001 |

INTERPRETATION: Each intersection squares the separation between bright and mid-tone pixels. A pixel at luminance 128 drops from 50% selected (L1) to 25% (L2) to 12.5% (L3). A pixel at luminance 200 drops from 78% to 61% to 48%. The bright pixels retain selection much more effectively than mid-tone pixels, so each successive mask targets an increasingly narrow highlight range.

The mask does not "move" toward brighter values --- it *contracts*. The peak remains at pure white (value 1.0 raised to any power is still 1.0). But the falloff steepens dramatically. This is exponential decay applied to masking.

### Deriving Darks and Midtones Masks

FACT: A Darks mask is the inverse of its corresponding Lights mask. D1 is the inverse of L1. If L1 has value *v* at a pixel, D1 has value *(1 - v)* at that pixel.

Narrower Darks masks are built by intersecting D1 with itself:

| Mask | Formula | Value at L=50 (dark pixel) | Value at L=128 (midtone) | Value at L=200 (highlight) |
|------|---------|---------------------------|--------------------------|---------------------------|
| D1 | 1 - v | 0.804 | 0.498 | 0.216 |
| D2 | (1 - v)^2 | 0.646 | 0.248 | 0.047 |
| D3 | (1 - v)^3 | 0.519 | 0.123 | 0.010 |

A Midtones mask targets the central tonal range. The most common derivation method:

1. Load L1 as a selection.
2. Invert the selection (Ctrl/Cmd-Shift-I) --- this is not the same as D1; this creates the *complement* of the L1 selection.
3. Intersect the result with L1 (Ctrl/Cmd-Shift-Alt-click RGB).
4. The result peaks at the midtones and falls off toward both highlights and shadows.

FACT: The Midtones 1 mask formula is: M1 = v x (1 - v). This peaks at v = 0.5 (midtone pixel = 25% selected) and falls to zero at both v = 0 and v = 1. The alternative method of subtracting L1 and D1 from a full-white mask yields a similar but not identical result.

INTERPRETATION: Note that M1 peaks at only 25% selection, not 100%. Midtones masks are inherently weaker than their Lights and Darks counterparts because the multiplication of two opposing gradients produces low maximum values. This is normal. You compensate by applying stronger adjustments through the mask or by using Levels on the mask channel to expand its range.

### Why Luminosity Masks Produce Seamless Transitions

INTERPRETATION: The seamlessness of luminosity mask adjustments is not a feature --- it is a mathematical inevitability. Because the mask is derived from the image's own luminance values, every tonal transition in the mask exactly mirrors a tonal transition in the image. There can be no mismatch between mask edge and image content because they are the same data.

Compare this to a painted mask with feathered edges: the feather is a fixed-radius gradient that approximates a transition but does not follow the image structure. Where the image has a sharp tonal boundary, the feather is too gradual. Where the image has a gentle gradient, the feather is too abrupt. Luminosity masks have no such approximation error.

RECOMMENDATION: When an adjustment needs to follow tonal boundaries rather than spatial boundaries --- exposure blending, highlight recovery, shadow opening, midtone contrast --- luminosity masks should be the default approach, not painted masks with feathering.

### Channel-Based Masking: Finding the Maximum Contrast Channel

FACT: In an RGB image, each colour channel is a grayscale representation of that colour component's intensity. A subject that is chromatically distinct from its background will show different contrast patterns in each channel.

The procedure for channel-based masking:

1. **Examine each channel individually** (click R, G, B thumbnails in the Channels panel). Look for the channel where the subject-background contrast is greatest.
2. **Duplicate the best channel** (drag it to the New Channel icon).
3. **Push contrast** using Levels (Ctrl/Cmd-L) or Curves. Push the subject toward white and the background toward black (or vice versa).
4. **Refine** with a soft brush where the automated contrast push was insufficient.
5. **Load as selection** (Ctrl/Cmd-click the channel thumbnail).

FACT: Common channel contrast patterns for natural subjects:

| Subject/Background combination | Typically best channel | Reason |
|------|------|------|
| Subject against blue sky | Blue or Red (examine both) | Sky is bright in Blue, dark in Red |
| Green foliage against earth/bark | Green | Foliage bright in Green, bark is not |
| Blonde hair against dark background | Red | Hair carries warm tones, bright in Red |
| Dark hair against light background | Blue | Hair is darkest in Blue channel |
| Red object against green | Red or Green | Maximum chromatic opposition |

RECOMMENDATION: Never assume which channel will work best --- always examine all three. Images with complex lighting, mixed backgrounds, or subjects that share hues with the background may surprise you. Some images may benefit from combining two channels via Calculations.

### The Mathematics of Apply Image Blending Modes for Masking

FACT: When using Apply Image to combine channels or masks, the blending mode determines the pixel-level arithmetic:

| Blending mode | Formula (per pixel, normalised 0-1) | Masking application |
|---|---|---|
| Multiply | A x B | Intersection --- narrows both masks |
| Screen | 1 - (1-A) x (1-B) | Union --- expands both masks |
| Add | (A + B) / scale + offset | Combine with brightness control |
| Subtract | (A - B) / scale + offset | Derive Midtones from Lights/Darks |

Multiply is the most frequently used mode for mask intersection. Screen is used to expand a mask (equivalent to a union operation). Subtract is used for deriving Midtones masks: Subtract L1 from white to approximate D1, or subtract both L1 and D1 from white to isolate midtones.

---

## Photoshop Implementation

### Creating a Full Luminosity Mask Set Manually

The following procedure creates L1-L3, D1-D3, and M1 as alpha channels. These can then be loaded as selections for any adjustment layer.

**Lights masks:**

1. Open the Channels panel (Window > Channels).
2. Ctrl/Cmd-click the RGB composite thumbnail. Marching ants appear (but remember: many pixels are partially selected, not just the ones inside the ants).
3. Click the Save Selection as Channel button (bottom of Channels panel). An Alpha 1 channel appears. Double-click it and rename to "L1."
4. With L1 active, Ctrl/Cmd-click the RGB composite again while holding Shift+Alt/Opt. This intersects the current selection with a new RGB load. Save as "L2."
5. Repeat the intersection once more and save as "L3."

**Darks masks:**

1. Click the L1 channel thumbnail to view it.
2. Select All (Ctrl/Cmd-A), then Ctrl/Cmd-C to copy.
3. Create a new alpha channel. Paste (Ctrl/Cmd-V). Invert (Ctrl/Cmd-I). Rename to "D1."
4. Ctrl/Cmd-click D1 to load as selection. Ctrl/Cmd-Shift-Alt-click D1 again (self-intersection). Save as "D2."
5. Repeat for "D3."

**Midtones mask:**

1. Select All (Ctrl/Cmd-A). This creates a 100% selection (all white).
2. Ctrl/Cmd-Alt-click the L1 channel. This *subtracts* L1 from the selection.
3. Ctrl/Cmd-Alt-click the D1 channel. This subtracts D1 from the remaining selection.
4. Save the result as "M1."

VERSION NOTE: The Ctrl/Cmd-Alt-click shortcut for subtracting from a selection requires that a selection is already active. If the marching ants disappear (Photoshop may deselect if the remaining selection values are all below 50%), the midtones mask is still valid --- save it regardless.

### Creating Luminosity Masks with Apply Image

Apply Image provides more control than the keyboard shortcut method and avoids potential issues with selections dropping below the visibility threshold.

1. Create L1 as above (Ctrl/Cmd-click RGB, save as channel).
2. Click the L1 channel to make it the active target.
3. Go to Image > Apply Image.
4. Set Source to the same document, Channel: L1, Blending: Multiply, Opacity: 100%.
5. Click OK. The L1 channel now contains L2 (it has been multiplied by itself).
6. Rename to "L2" (or duplicate L1 first and apply to the duplicate to preserve L1).

RECOMMENDATION: Always duplicate the source channel before using Apply Image if you want to preserve the original. Apply Image modifies the target in place.

### Channel-Based Hair Masking

1. Open the image and examine each channel (click R, G, B in the Channels panel).
2. Identify the channel with the greatest contrast between hair and background.
3. Duplicate that channel (right-click > Duplicate Channel).
4. With the duplicate active, open Levels (Ctrl/Cmd-L).
5. Drag the black point slider right to darken the background. Drag the white point slider left to brighten the subject.
6. Use a soft white brush to paint the subject interior pure white (do not paint near the hair edges).
7. Use a soft black brush to paint the background pure black (do not paint near the hair edges).
8. The edge zone retains its natural graduated transition from the channel data.
9. Ctrl/Cmd-click the channel to load as selection.
10. Return to RGB view. Use the selection as a layer mask.

### Select and Mask Refinement

1. Make a rough selection using any method (Quick Selection, channel-based, Object Selection).
2. Open Select > Select and Mask.
3. Set View Mode to "On Black" or "On White" to evaluate edge quality.
4. Adjust Edge Detection Radius. Start low (2-5 px) and increase only where needed.
5. Enable Smart Radius for subjects with both hard edges (clothing) and soft edges (hair).
6. Use the Refine Edge Brush tool (second tool in the left toolbar) to paint over hair/fur edges. This tells Photoshop to analyse that zone in detail.
7. In Global Refinements: use Smooth sparingly (it rounds corners), Feather minimally, Contrast to tighten loose edges, and Shift Edge to contract or expand the mask boundary.
8. In Output Settings: enable Decontaminate Colors if replacing the background. Set Amount to 50-70% as a starting point.
9. Output To: "New Layer with Layer Mask" (required if Decontaminate Colors is enabled).

---

## Professional Workflow

### Luminosity Masking for Landscape Processing

A professional landscape luminosity workflow follows this sequence:

**Phase 1: Assessment.** Open the image and evaluate which tonal ranges need separate treatment. Common needs: highlight recovery in sky, shadow detail in foreground, midtone contrast in subject.

**Phase 2: Mask creation.** Build the luminosity mask set (L1-L3, D1-D3, M1 minimum). Examine each mask by Alt/Opt-clicking its thumbnail in the Channels panel to view it in the document window. Identify which mask best isolates the tonal range you need.

**Phase 3: Targeted adjustments.** For each adjustment:

1. Click the target alpha channel in the Channels panel.
2. Ctrl/Cmd-click to load it as a selection.
3. Return to the Layers panel.
4. Create an adjustment layer (Curves, Levels, etc.). The active selection automatically becomes the layer's mask.
5. Apply the adjustment. It now affects only the targeted tonal range, with graduated transitions.

**Phase 4: Spatial restriction.** If an adjustment should apply to only part of the image (e.g., sky highlight recovery should not affect reflections in water), paint black on the adjustment layer's mask in the areas where you want to remove the effect. This combines the luminosity mask (tonal control) with a painted mask (spatial control).

**Phase 5: Review.** Toggle adjustment layers on/off to verify that each adjustment is seamless. Check for tonal banding or unnatural transitions, especially in sky gradients.

### Channel-Based Extraction Workflow

**Phase 1: Channel evaluation.** Examine R, G, B individually. Choose the highest-contrast channel.

**Phase 2: Channel mask creation.** Duplicate and contrast-push the chosen channel.

**Phase 3: Refinement.** Paint subject interior white, background black, leave edges untouched.

**Phase 4: Edge refinement.** Load channel mask as selection, enter Select and Mask, refine edges.

**Phase 5: Decontamination.** Enable Decontaminate Colors if the subject will be placed on a new background.

**Phase 6: Compositing.** Place the masked subject on the new background. Evaluate edge quality at 100% zoom.

---

## Step-by-Step Example: Luminosity Masks for Landscape Tonal Control

**Scenario:** A landscape photograph of mountains with a bright sky. The sky highlights are slightly blown, the mountain midtones lack contrast, and the foreground shadows are too dark.

### Step 1: Build the Luminosity Mask Set

1. Open the image in Photoshop.
2. Open the Channels panel (Window > Channels).
3. **Create L1:** Ctrl/Cmd-click the RGB composite thumbnail. Save the selection as a new channel. Rename to "L1."
4. **Create L2:** With L1 still loaded as a selection, Ctrl/Cmd-Shift-Alt-click the RGB composite. Save as "L2."
5. **Create L3:** Repeat. Ctrl/Cmd-Shift-Alt-click the RGB composite. Save as "L3."
6. **Create D1:** Duplicate L1 (right-click > Duplicate Channel). Rename to "D1." Invert it (Ctrl/Cmd-I).
7. **Create D2:** Ctrl/Cmd-click D1 to load. Ctrl/Cmd-Shift-Alt-click D1 to self-intersect. Save as "D2."
8. **Create M1:** Select All (Ctrl/Cmd-A). Ctrl/Cmd-Alt-click L1 (subtract). Ctrl/Cmd-Alt-click D1 (subtract). Save as "M1."

### Step 2: Recover Sky Highlights

1. Examine L2 by Alt/Opt-clicking its thumbnail. The sky should appear bright (well-selected), mountains mid-grey, foreground dark. If the sky is not sufficiently isolated, try L3.
2. Ctrl/Cmd-click L2 to load as selection.
3. Switch to Layers panel. Create a Curves adjustment layer. The L2 mask automatically applies.
4. In Curves, pull the highlights down gently. The sky darkens and reveals colour/detail. The mountains and foreground are barely affected because their mask values are low.

### Step 3: Open Foreground Shadows

1. In the Channels panel, examine D2. The foreground should appear bright (well-selected), sky should be near-black.
2. Ctrl/Cmd-click D2 to load as selection.
3. Create a Curves adjustment layer.
4. In Curves, lift the shadow region. The foreground opens up. Sky and bright areas are protected by the mask.

### Step 4: Add Midtone Contrast to Mountains

1. Ctrl/Cmd-click M1 to load as selection.
2. Create a Curves adjustment layer.
3. Apply an S-curve to add contrast. This affects primarily the mountain midtones. Highlights and shadows are protected.

### Step 5: Spatial Restriction

1. The sky highlight recovery (Step 2) may have slightly affected bright rocks in the foreground. Click the Curves layer mask from Step 2.
2. Select a soft black brush at low opacity (20-30%).
3. Paint over the foreground rocks to remove the darkening effect from that area.
4. This combines spatial masking (painted black = "not here") with the luminosity mask (L2 = "proportional to highlight brightness").

### Step 6: Final Evaluation

1. Toggle each adjustment layer on/off (click the eye icon) to verify the effect.
2. Zoom to 100% on tonal transition zones (mountain ridgeline against sky, shadow-midtone transitions in foreground).
3. Verify no banding, halos, or unnatural transitions are visible.
4. If any adjustment is too strong, reduce the adjustment layer's opacity or refine the Curves settings.

---

## Common Mistakes

### 1. Ignoring the Marching Ants Warning

**The mistake:** When loading a luminosity mask as a selection, Photoshop displays the warning "No pixels are more than 50% selected. The selection edges will not be visible." The user clicks OK and assumes nothing is selected.

**Why it happens:** Photoshop only shows marching ants around pixels that are more than 50% selected. Many luminosity masks (especially Midtones and narrower Darks/Lights masks) have no pixels above 50%. The selection is valid and functional --- it is just not visible as marching ants.

**The fix:** Ignore the warning and proceed. Save the selection as an alpha channel and view it there (Alt/Opt-click the channel thumbnail) to see the actual mask values.

### 2. Painting Near Edges in Channel-Based Masks

**The mistake:** When cleaning up a duplicated channel with brushwork, the user paints white or black too close to the subject edge, destroying the natural graduated transition that makes channel masks effective.

**Why it happens:** The temptation to "clean up" the mask fully. The edge zone looks messy compared to the solid black background and solid white subject.

**The fix:** Use a hard-edged brush for the subject interior and background, but stop well short of the edge (20-50 pixels away). The edge zone should retain its original channel data. If further refinement is needed, use Select and Mask rather than manual painting.

### 3. Using Overly Aggressive Levels on Channel Masks

**The mistake:** Pushing the black and white points in Levels too aggressively when preparing a channel-based mask, causing posterisation in the transition zone and loss of fine detail (hair wisps, semi-transparent areas).

**The fix:** Apply Levels in multiple passes. Push moderately, examine the edges, push again only if needed. The goal is maximum contrast in the subject/background areas while preserving the transition zone's gradient.

### 4. Forgetting to Return to RGB Before Applying Adjustments

**The mistake:** After examining alpha channels, the user creates an adjustment layer while an individual channel is still the active view. The adjustment applies to one channel only, producing a colour shift.

**The fix:** Always click the RGB composite thumbnail in the Channels panel (or press Ctrl/Cmd-~ or Ctrl/Cmd-2) to return to the full colour view before creating adjustment layers.

### 5. Applying Decontaminate Colors When Not Needed

**The mistake:** Enabling Decontaminate Colors for every extraction, even when there is no colour fringe. This alters edge pixel colours unnecessarily and forces output to a new layer.

**Why it happens:** Treating Decontaminate Colors as a default "quality improvement."

**The fix:** Evaluate the extraction on a contrasting background first. Only enable Decontaminate Colors if visible colour fringing exists. For subjects against neutral backgrounds, fringing may be minimal or absent.

### 6. Creating Too Many Luminosity Mask Levels

**The mistake:** Routinely generating L1 through L6, D1 through D6, and multiple Midtones variants "just in case," cluttering the Channels panel with masks that will never be used.

**The fix:** Start with L1-L3, D1-D3, and M1. Examine each against your specific image needs. Create additional levels only when the existing set does not isolate your target range narrowly enough. Delete unused channels before saving to reduce file size.

---

## Alternative Approaches

### Blend-If Sliders (Layer Style)

The blend-if sliders in the Layer Style dialog (double-click a layer) provide real-time luminosity-based blending without creating explicit masks. Dragging the "This Layer" or "Underlying Layer" sliders controls which tonal values are visible based on their brightness. Holding Alt/Opt while dragging splits the slider for a graduated transition.

**Advantage:** Instant, non-destructive, adjustable at any time. No alpha channels needed.

**Limitation:** The blend is based on the full composite luminance or a single channel. You cannot achieve the precision of intersected luminosity masks. The transitions are linear, not image-structure-derived. There is no visible mask to refine or combine with other masks.

RECOMMENDATION: Use blend-if for quick tonal targeting during exploration. Switch to explicit luminosity masks when you need precise control, combinability with spatial masks, or the ability to see and edit the mask directly.

### Select > Color Range

Color Range (Select > Color Range) creates selections based on sampled colour or preset tonal ranges (Highlights, Midtones, Shadows). The Fuzziness slider controls how broadly the selection extends from the sampled point.

**Advantage:** Quick, visual, and capable of targeting specific hues in addition to tonal ranges. The Highlights/Midtones/Shadows presets approximate luminosity masks without manual construction.

**Limitation:** The preset tonal ranges use a different algorithm than true luminosity masks. They are threshold-based with feathering, not continuous graduated selections derived from actual pixel luminance values. The results are usable but less precise than manually constructed luminosity masks.

VERSION NOTE: The Range slider for Highlights/Midtones/Shadows presets was added in Photoshop CC 2014. Earlier versions offer fixed ranges only.

### Luminosity Masking Panels and Extensions

Third-party panels (such as TKActions, ADP LumiFlow, Lumenzia, and others) automate luminosity mask creation and management. They generate the full mask set with one click and provide a visual interface for selecting and combining masks.

**Advantage:** Significant time savings. Visual mask preview. Some panels offer zone-based masking (inspired by the Zone System) that divides the tonal range into discrete numbered zones.

**Limitation:** Masks generated by panels use the same underlying channel mathematics as manual creation. The panels add convenience, not capability. Understanding the manual process (as covered in this chapter) is essential for troubleshooting, customising masks beyond the panel's presets, and working when panels are unavailable.

RECOMMENDATION: Learn the manual process first. Use panels for production speed once you understand what they are doing internally.

### AI-Based Selection (Object Selection, Select Subject)

Photoshop's AI-powered selection tools (Select Subject, Object Selection) can produce initial selections of complex subjects, including hair, with increasing accuracy in recent versions.

**Advantage:** Fast starting point. Improving rapidly with each Photoshop release.

**Limitation:** AI selections still require refinement for professional-quality results. They produce spatial selections, not tonal selections, so they do not replace luminosity masks for tonal work. Edge quality on fine hair/fur is improving but often still needs Select and Mask refinement.

VERSION NOTE: Select Subject was introduced in Photoshop CC 2018 and has been significantly improved in each subsequent release. Object Selection was added in Photoshop CC 2020. The algorithms are cloud-assisted in newer versions.

---

## Summary

Advanced masking divides into two paradigms. **Tonal masking** uses the image's own luminance values to create graduated masks that inherently match the tonal structure of the image. **Channel-based masking** uses individual colour channels where subject-background contrast is naturally strongest.

Luminosity masks are built from the RGB composite. Ctrl/Cmd-clicking the composite produces the base Lights mask (L1). Self-intersection narrows the range (L2, L3). Inversion produces Darks masks. Subtracting both Lights and Darks from full white produces Midtones masks. The mathematics are multiplicative: each intersection squares the mask values, steepening the falloff from the targeted range.

Channel-based masking starts with evaluating which colour channel provides the best subject-background contrast. Duplicating and contrast-pushing that channel creates a working mask. For complex edges (hair, fur), Select and Mask refines the boundary, and Decontaminate Colors removes background colour contamination from edge pixels.

The most powerful workflows combine tonal and spatial masking. A luminosity mask controls the intensity of an adjustment proportionally to brightness. A painted mask restricts it geographically. Together, they provide the precision control that distinguishes professional from amateur image processing.

Apply Image and Calculations are the precision instruments for mask arithmetic. Apply Image modifies a target channel in place. Calculations creates a new output from two sources. Both offer explicit blending mode control for intersection (Multiply), union (Screen), and derivation (Subtract/Add) operations.

---

## Exercises

### Exercise 1: Build a Complete Luminosity Mask Set

Open a landscape photograph with a full tonal range (highlights in the sky, dark shadows in the foreground). Manually create L1, L2, L3, D1, D2, D3, and M1 using the Ctrl/Cmd-click intersection method. View each mask individually (Alt/Opt-click the channel thumbnail). Document which mask best isolates: (a) the sky highlights, (b) the deep foreground shadows, (c) the midtone detail in the main subject.

### Exercise 2: Luminosity-Masked Highlight Recovery

Using the mask set from Exercise 1, apply a Curves adjustment through L2 (or L3 if needed) to darken blown sky highlights. Then apply a second Curves adjustment through D2 to open foreground shadows. Compare the result with the same adjustments applied using painted masks with 100px feather. Evaluate transition quality at 100% zoom.

### Exercise 3: Channel-Based Tree Masking

Open a photograph of a tree with fine branches against a blue sky. Examine R, G, B channels. Identify the channel with the best branch-sky contrast. Duplicate it, push contrast with Levels, clean up the interior and background with brush work (staying away from edges), and load as a selection. Apply the selection as a layer mask to isolate the tree.

### Exercise 4: Apply Image vs Keyboard Intersection

Create L1 and save it. Duplicate it. On the duplicate, use Image > Apply Image with the original L1 as source, Multiply mode. Compare the result with L2 created via the Ctrl/Cmd-Shift-Alt-click method. Are they identical? (They should be.) Repeat with Screen mode and observe how the mask expands rather than contracts.

### Exercise 5: Midtones Mask via Three Methods

Create a Midtones mask using three different methods: (a) Select All, subtract L1, subtract D1, save; (b) Use Apply Image with Multiply to combine L1 (inverted) with L1; (c) Use Calculations with Source 1 = L1 (inverted) and Source 2 = L1, Multiply mode, output to new channel. Compare all three results. Note the subtle differences and determine which method best isolates your image's midtone range.

---

## Advanced Exercises

### Advanced Exercise 1: Combined Tonal and Spatial Masking

Open a landscape with a bright sky reflected in water. Apply highlight recovery using an L2 mask. Notice that it affects both the sky and the reflection. Now combine the luminosity mask with a painted gradient mask to restrict the recovery to the sky only, leaving the reflection's brightness intact. Use a soft gradient on the layer mask (black at the bottom, fading to white at the top) multiplied with the luminosity mask.

### Advanced Exercise 2: Channel Mask for Complex Hair Extraction

Find a portrait with hair against a varied, non-uniform background. Build a channel-based mask using the highest-contrast channel. Refine edges with Select and Mask, using the Refine Edge Brush on the hair boundary. Apply Decontaminate Colors. Place the subject on three different backgrounds (dark, light, coloured) and evaluate edge quality on each. Identify remaining artefacts and address them.

### Advanced Exercise 3: Luminosity Mask Exposure Blending

Take two exposures of the same scene (one for highlights, one for shadows, tripod-mounted or aligned). Stack them as layers. Create an L2 mask from the bright exposure and apply it as a layer mask to the dark exposure layer. This reveals the dark exposure's preserved highlight detail in the bright areas while keeping the bright exposure's shadow detail in the dark areas. Compare with Photoshop's HDR merge and with manual painted blending.

---

## Blackbelt Challenge

**Multi-Technique Composite Masking**

Create a composite that requires all three masking paradigms covered in this chapter:

1. Extract a portrait subject with complex hair using channel-based masking and Select and Mask refinement.
2. Place the subject into a landscape scene.
3. Use luminosity masks to match the subject's tonal range to the new scene: darken highlights to match the scene's lighting, open shadows to match the scene's ambient level.
4. Use a Midtones mask to add contrast to the subject's midtones, matching the scene's contrast character.
5. Combine a luminosity mask with a painted mask to apply colour grading that affects only the sky highlights behind the subject.
6. All mask transitions must be invisible at 100% zoom. The composite must show no haloing, colour fringing, or tonal discontinuity at any edge.

Document each mask used, its derivation method, and why it was chosen over alternatives. Export at full resolution and evaluate critically.
