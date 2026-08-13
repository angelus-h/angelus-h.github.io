---
description: Pixel layers, adjustment layers, Smart Objects, fill layers, layer groups, and the architectural principles behind a professional photographic layer stack.
---

# Chapter 04: The Layer System

## Learning Objectives

After completing this chapter, you will be able to:

- Explain what layers are and how Photoshop composites them into a visible image
- Distinguish between pixel layers, adjustment layers, Smart Objects, and fill layers, and choose the right type for a given task
- Explain the rendering order of layers and the difference between Opacity and Fill
- Describe the role of blending modes and masks at a conceptual level (detailed treatment in later chapters)
- Design a layer architecture for a photographic edit that remains flexible, non-destructive, and organized
- Identify when to merge, flatten, or stamp visible, and understand the irreversible consequences of each
- Build a professional-quality layer stack for a landscape or portrait edit


## Conceptual Foundation

### What Problem Do Layers Solve?

Before layers existed in image editors, every edit was applied directly to the image data. Darkening a sky meant permanently changing those pixel values. If the result was wrong, you could undo the last step, but you could not revisit a decision made ten steps ago without losing everything done since.

Layers solve this problem by separating different elements and adjustments onto independent planes. Each layer can be modified, reordered, hidden, or deleted without touching any other layer. The image you see on screen is not any single layer; it is a computed composite of all visible layers, rendered on the fly.

**FACT:** Photoshop's layer system is a compositing engine. The visible image is calculated from bottom to top, combining each layer's content, opacity, blending mode, and masks. The original pixel data of each layer is preserved independently until you explicitly flatten or merge.

This is not merely a convenience feature. It is the foundation of non-destructive editing. Without layers, there is no practical way to maintain editability across a complex photographic workflow.

### The Compositing Model

Think of layers as a vertical stack of transparent sheets. Each sheet may contain pixels (paint, a photograph, text), or it may contain instructions (make the image brighter, shift the color, add a gradient). Photoshop renders the final image by starting at the bottom layer and compositing each successive layer on top, taking into account that layer's content, opacity, blending mode, and mask.

**FACT:** The compositing order is strictly bottom-to-top within the Layers panel. The layer at the bottom of the stack is rendered first, and each layer above it is composited on top of the result below. The Layers panel displays this visually: the bottom layer is at the bottom, the top layer is at the top.

This bottom-to-top rendering means that layer order matters. An adjustment layer placed above a pixel layer affects that pixel layer. The same adjustment layer placed below it does not. This is not merely organizational — it changes the computed output.

### Layers Are Not Files

A common mental model is to think of each layer as a separate image file. This is partially correct but misleading in important ways:

- A pixel layer does contain its own pixel grid, but that grid may be smaller or larger than the canvas. Pixels that extend beyond the canvas edges are hidden but not deleted (unless you use **Image > Trim** or **Canvas Size** to crop them).
- An adjustment layer contains no pixel data at all. It contains parameters for a mathematical operation (for example, a Curves adjustment). Those parameters are applied to the composite of everything below during rendering.
- A Smart Object contains an embedded (or linked) file — potentially an entire layered PSD, a RAW file, or a vector file — and displays a rendered preview of that file on the canvas.

Understanding what each layer type *contains* is essential to understanding what operations are possible on it and what happens when you transform, resize, or merge it.


## Terminology

### Layer (Pixel Layer)

**Definition:** A layer that contains raster pixel data — a grid of colored pixels at the document's resolution and bit depth.

**Meaning in photographic practice:** Pixel layers hold actual image content. When you open a photograph, it arrives as a pixel layer. When you paint, clone, or heal, you are writing pixels onto a pixel layer. Retouching work (clone stamp, healing brush) is typically done on separate empty pixel layers so the original remains untouched.

**Photoshop implementation:** Created via **Layer > New > Layer**, or by duplicating an existing layer, pasting content, or placing a rasterized file. In the Layers panel, pixel layers show a thumbnail of their contents. An empty pixel layer shows a checkerboard pattern (representing transparency).

**Related concepts:** Background layer (a special pixel layer), Smart Object (a non-rasterized container), adjustment layer (contains parameters, not pixels).

**Common misconception:** "All layers contain pixels." Adjustment layers, fill layers, and type layers do not contain raster pixel data in the conventional sense. Treating them as pixel layers (for example, attempting to paint on an adjustment layer) leads to confusion.

---

### Adjustment Layer

**Definition:** A layer that contains the parameters of an image adjustment (Curves, Levels, Hue/Saturation, etc.) and applies that adjustment non-destructively to all visible layers below it.

**Meaning in photographic practice:** Adjustment layers are the primary mechanism for non-destructive tonal and color editing. Instead of applying Curves directly to your pixel data (which permanently changes those values), you add a Curves adjustment layer. The parameters are stored in the layer; the underlying pixels are never modified. You can change the curve, reduce its opacity, add a mask, or delete it entirely at any point in the future.

**Photoshop implementation:** Created via **Layer > New Adjustment Layer**, or by clicking the half-filled circle icon at the bottom of the Layers panel. Each adjustment layer has an associated layer mask (white by default, meaning the adjustment applies everywhere) and its parameters appear in the Properties panel when selected.

**Related concepts:** Layer mask (controls where the adjustment applies), clipping mask (restricts the adjustment to a single layer below), fill layer (similar structure but contains a fill pattern rather than an adjustment).

**Common misconception:** "Adjustment layers affect only the layer directly below them." By default, an adjustment layer affects the entire composite of all visible layers below it in the stack, not just the immediately adjacent layer. To limit an adjustment layer to affect only a single layer, you must use a clipping mask.

---

### Smart Object

**Definition:** A layer that contains an embedded (or linked) source file, displayed on the canvas as a rendered preview. The source file is preserved intact inside the PSD, and transformations applied to the Smart Object are recalculated from the source rather than applied destructively to pixels.

**Meaning in photographic practice:** Smart Objects are essential for non-destructive scaling, rotation, and filtering. If you scale down a regular pixel layer, Photoshop discards the excess pixels. If you later scale it back up, the quality is permanently degraded. A Smart Object, by contrast, always recalculates from the original embedded data, so scaling down and back up produces no quality loss.

For photographers, Smart Objects are most commonly used for: embedding a Camera Raw file so you can re-edit the RAW development settings at any time; applying filters non-destructively as Smart Filters (the filter can be re-edited or removed later); and preserving quality when resizing or transforming compositing elements.

**Photoshop implementation:** Created via **Layer > Smart Objects > Convert to Smart Object**, or by using **File > Place Embedded** / **File > Place Linked** to bring in an external file. Smart Object layers display a small icon (a page with a folded corner) in the lower-right of their thumbnail in the Layers panel. Double-clicking the thumbnail opens the embedded source for editing in a separate window.

The distinction between embedded and linked Smart Objects matters:

| Property | Embedded Smart Object | Linked Smart Object |
|---|---|---|
| Source location | Stored inside the PSD file | References an external file on disk |
| File size impact | Increases PSD file size | Minimal impact on PSD size |
| Portability | Self-contained, works on any machine | Breaks if linked file is moved or deleted |
| Use case | General-purpose non-destructive editing | Shared assets across multiple documents |

**Related concepts:** Smart Filters (filters applied to a Smart Object, stored non-destructively), rasterize (converting a Smart Object back to a regular pixel layer, discarding the embedded source).

**Common misconception:** "Smart Objects are always better." Smart Objects have real costs: they increase file size (the embedded source is stored in addition to the rendered preview), they cannot be directly painted on or edited with pixel-level tools (you must open the embedded source or rasterize), and they add complexity. For a simple retouching layer where you need to paint with the Clone Stamp, a regular empty pixel layer is the correct choice.

---

### Fill Layer

**Definition:** A layer that generates a solid color, gradient, or pattern fill, applied non-destructively and with an associated layer mask.

**Meaning in photographic practice:** Fill layers are used for effects that require a uniform fill — a solid color overlay for color grading, a gradient for simulating a graduated neutral density filter, or a pattern fill for texture overlays. Because they are non-destructive, you can change the color, gradient angle, or pattern at any time.

**Photoshop implementation:** Created via **Layer > New Fill Layer** with options for Solid Color, Gradient, or Pattern. Like adjustment layers, fill layers include a layer mask. The fill parameters are editable by double-clicking the layer thumbnail.

**Related concepts:** Adjustment layer (similar panel structure but modifies layers below rather than generating new content), blending mode (often used with fill layers to control how the fill interacts with the image below).

**Common misconception:** "Fill layers are the same as painting a color onto a pixel layer." A fill layer is parametric — you can change the color value at any time without repainting. A painted pixel layer contains fixed pixel data. Additionally, a Gradient fill layer recomputes the gradient at the current canvas size; a gradient painted onto a pixel layer is fixed and will not adapt if the canvas is resized.

---

### Layer Group

**Definition:** A folder-like container that holds multiple layers, allowing them to be organized, moved, and transformed as a unit.

**Meaning in photographic practice:** Layer groups are essential for organizing complex edits. A typical photographic edit may contain 15-30 layers. Without groups, the Layers panel becomes unmanageable. Groups allow you to collapse related layers (for example, all skin retouching layers, or all color grading adjustments), apply a mask or blending mode to the entire group, and toggle the group's visibility to see its cumulative effect.

**Photoshop implementation:** Created via **Layer > New > Group**, or by selecting multiple layers and pressing Ctrl+G (Cmd+G on macOS). Groups can be nested inside other groups. A group can have its own blending mode (default: Pass Through) and its own layer mask.

The group blending mode has a critical subtlety: **Pass Through** means each layer inside the group interacts with layers below the group as if the group did not exist. Any other blending mode (Normal, Multiply, etc.) causes the group contents to be composited among themselves first, and then the flattened result is composited with the layers below using the specified blending mode. This distinction matters when layers inside the group use blending modes that interact with layers outside the group.

**Related concepts:** Layer mask (can be applied to a group to mask all layers within it simultaneously), Pass Through blending mode (unique to groups).

**Common misconception:** "Groups are just for tidiness." Groups are a compositing tool. Changing a group's blending mode from Pass Through to Normal changes the rendered output. Adding a mask to a group affects every layer inside it. These are functional, not cosmetic, operations.

---

### Layer Opacity

**Definition:** A percentage value (0-100%) that controls the overall transparency of a layer's contribution to the composite. At 100%, the layer is fully opaque. At 0%, the layer is completely invisible.

**Meaning in photographic practice:** Opacity is used to control the strength of an effect. If a Curves adjustment layer creates the correct tonal shape but the effect is too strong, reducing its opacity from 100% to 60% blends 60% of the adjusted result with 40% of the unadjusted result. This is simpler and often more intuitive than modifying the adjustment parameters directly.

**Photoshop implementation:** Controlled by the Opacity field at the top of the Layers panel, or via keyboard shortcuts (pressing a digit key while a layer is selected — 5 for 50%, 3 then 5 quickly for 35%, 0 for 100%).

**Related concepts:** Fill (a separate transparency control that behaves differently from Opacity for certain blending modes), blending mode (determines how the layer composites before opacity is applied).

**Common misconception:** "Opacity and Fill are the same thing." They produce identical results when the blending mode is Normal, but they differ for blending modes that have "special" behavior — most notably the modes in Photoshop's "Special 8" group. See the Fill entry below.

---

### Layer Fill

**Definition:** A percentage value (0-100%) that controls the transparency of a layer's pixel content without affecting the transparency of layer styles (such as Drop Shadow, Stroke, or Bevel and Emboss) applied to that layer.

**Meaning in photographic practice:** For photographic editing (where layer styles are less common), the critical distinction between Fill and Opacity arises with certain blending modes. When using blending modes such as Color Burn, Linear Burn, Color Dodge, Linear Dodge (Add), Vivid Light, Linear Light, Hard Mix, and Difference, reducing Fill produces a different visual result than reducing Opacity by the same amount. Reducing Fill in these modes tends to produce a subtler, more usable effect that photographers often prefer for techniques like non-destructive dodging and burning or contrast enhancement.

**INTERPRETATION:** Many advanced Photoshop practitioners deliberately use Fill instead of Opacity when working with blending modes like Vivid Light or Hard Light on 50% gray layers for dodging and burning, because the Fill slider produces a smoother, more photographic-looking falloff.

**Photoshop implementation:** Located directly below the Opacity field in the Layers panel. There is no single-key shortcut for Fill; you must click the field or use Shift + digit keys when a painting tool is not selected (this shortcut behavior depends on which tool is active and can be unintuitive — verify in your version).

**Related concepts:** Opacity (affects everything including layer styles), blending mode (the category of blending mode determines whether Fill and Opacity behave identically or differently).

**Common misconception:** "Fill is just a second opacity slider." For Normal blending mode with no layer styles, they are functionally identical. But for advanced blending mode work, the distinction is substantial and practically important. This is covered in depth in Chapter 14 (Blending Modes).

---

### Blending Mode

**Definition:** A mathematical formula that determines how a layer's pixel values are combined with the composite of layers below it. Each blending mode defines a different mathematical operation (multiply, screen, overlay, etc.).

**Meaning in photographic practice:** Blending modes are central to photographic editing. Multiply darkens (simulating the effect of sandwiching two slides), Screen lightens (simulating projecting two slides onto the same screen), Overlay increases contrast (darkening darks and lightening lights), Soft Light applies a subtle contrast and color shift, and Luminosity applies only the tonal values of the layer while preserving the colors below. Photographers use blending modes for contrast control, color grading, exposure blending, texture overlays, and many other tasks.

**Photoshop implementation:** Selected from the dropdown menu at the top-left of the Layers panel, or by using Shift+ and Shift+- to cycle through modes. Blending modes can be applied to layers, groups, and painting tools.

**Related concepts:** Opacity and Fill (modify the strength of the blending mode's effect), layer mask (controls where the blending mode applies spatially).

**Common misconception:** "Blending modes are just visual effects to experiment with randomly." Each blending mode is a defined mathematical operation. Understanding the underlying math (covered in Chapter 14) allows you to predict what a blending mode will do and choose the right one deliberately, rather than cycling through all of them hoping for a lucky result.

*Blending modes receive full treatment in Chapter 14.*

---

### Clipping Mask

**Definition:** A relationship between two or more adjacent layers where the visibility (and effect) of the upper layer(s) is restricted to the non-transparent pixels of the layer immediately below them (the base layer).

**Meaning in photographic practice:** Clipping masks are most commonly used to restrict an adjustment layer so it affects only one specific layer rather than the entire stack below it. For example, if you have a retouching layer with clone stamp work and you want to apply a Curves adjustment to only that retouching layer without affecting the Background, you clip the Curves adjustment layer to the retouching layer.

**Photoshop implementation:** Created by holding Alt (Option on macOS) and clicking on the line between two layers in the Layers panel, or via **Layer > Create Clipping Mask**, or with the shortcut Alt+Ctrl+G (Option+Cmd+G). Clipped layers display an indented thumbnail with a small downward-pointing arrow in the Layers panel.

**Related concepts:** Layer mask (controls where a layer's effect applies based on a grayscale map — fundamentally different from a clipping mask, which controls based on the transparency of another layer).

**Common misconception:** "Clipping masks and layer masks do the same thing." They solve different problems. A layer mask is a grayscale image attached to a layer that controls that layer's transparency on a pixel-by-pixel basis. A clipping mask is a relationship between layers where the base layer's transparency defines the visible area for all clipped layers above it. They can be used together on the same layer.

*Layer masks receive full treatment in Chapter 05.*

---

### Layer Mask

**Definition:** A grayscale image attached to a layer that controls the transparency of that layer on a pixel-by-pixel basis. White areas of the mask are fully visible, black areas are fully hidden, and gray values produce proportional transparency.

**Meaning in photographic practice:** Layer masks are the primary tool for applying adjustments selectively. If a Curves adjustment layer brightens the entire image but you only want to brighten the foreground, you paint black on the layer mask over the sky and background areas, hiding the adjustment there. The adjustment's parameters remain unchanged; the mask controls where it is visible.

For photographic editing, this is arguably the single most important feature in Photoshop. It allows any adjustment to be applied locally rather than globally, with precise control and complete reversibility.

**Photoshop implementation:** Added by selecting a layer and clicking the "Add layer mask" button (rectangle with circle) at the bottom of the Layers panel. By default, a new mask is white (fully revealing). Alt-clicking the button creates a black (fully hiding) mask. Adjustment layers are created with a white layer mask automatically. The mask is edited by clicking on its thumbnail in the Layers panel and then painting with black, white, or gray.

**Related concepts:** Alpha channel (a layer mask is stored as an alpha channel), clipping mask (restricts based on another layer's transparency rather than a painted mask), vector mask (a resolution-independent mask defined by a path).

**Common misconception:** "Layer masks delete pixels." Layer masks hide pixels; they do not delete them. Painting black on a mask makes those areas invisible, but the pixel data underneath is unchanged. Painting white again reveals them. This is non-destructive. (Applying the mask via right-click > Apply Layer Mask is destructive — it permanently deletes the hidden pixels.)

*Layer masks receive full treatment in Chapter 05.*

---

### Flatten Image / Merge Layers / Stamp Visible

These three operations are frequently confused, and the confusion has real consequences for your work.

**Flatten Image**

**Definition:** Combines all visible layers into a single Background layer and discards all hidden layers. The result is a single-layer document with no transparency.

**Photoshop implementation:** **Layer > Flatten Image**. Any transparent areas are filled with white.

**Consequence:** This is irreversible once saved. All adjustment layer parameters, masks, Smart Object source data, and layer structure are permanently discarded. The only remaining data is the final rendered pixel output.

---

**Merge Layers / Merge Down / Merge Visible**

**Definition:** Combines selected layers (Merge Layers), or a layer with the one below it (Merge Down), or all visible layers (Merge Visible) into a single pixel layer.

**Photoshop implementation:** **Layer > Merge Layers** (Ctrl+E / Cmd+E with multiple layers selected), **Layer > Merge Down** (Ctrl+E / Cmd+E with a single layer selected), **Layer > Merge Visible** (Ctrl+Shift+E / Cmd+Shift+E).

**Consequence:** The merged layers' individual editability is lost. Adjustment layers become baked into pixels. Smart Object source data is discarded. Masks are applied and removed. This is destructive for the merged layers, though layers not included in the merge are unaffected.

---

**Stamp Visible**

**Definition:** Creates a new pixel layer that contains the rendered composite of all currently visible layers, without affecting or removing any existing layers.

**Photoshop implementation:** Select the topmost layer, then press Ctrl+Alt+Shift+E (Cmd+Option+Shift+E on macOS). This creates a new pixel layer above the current selection containing the flattened visible result.

**Consequence:** This is the least destructive of the three operations because all original layers remain intact. However, the stamped layer is a static pixel snapshot — it does not update if you later change layers below it. It also increases file size.

---

| Operation | Layers preserved? | Destructive? | Hidden layers | Transparency |
|---|---|---|---|---|
| Flatten Image | No | Yes | Discarded | Filled with white |
| Merge Visible | Merged layers lost | Partially | Untouched | Preserved |
| Merge Down | Both layers lost | Yes (for those two) | Untouched | Preserved |
| Stamp Visible | All preserved | No (creates new layer) | Untouched | Preserved |

**Meaning in photographic practice:**

- **Flatten** is used only for final output delivery when you need a single-layer file (for example, a JPEG or TIFF for printing). Never flatten your working PSD.
- **Merge** is used occasionally when you need to combine several layers into one for a specific operation (for example, applying a filter that needs pixel data). Try to find a non-destructive alternative first (Smart Object, Stamp Visible).
- **Stamp Visible** is used when you need a pixel composite for further editing (for example, running a sharpening filter on the full composite) while preserving all original layers below.

**Common misconception:** "I need to flatten my image to apply a filter." You do not. You can use Stamp Visible to create a composite layer and apply the filter to that, or convert to a Smart Object and apply the filter as a Smart Filter.

---

### Background Layer

**Definition:** A special pixel layer that Photoshop creates by default when you open a flat image (JPEG, TIFF, etc.) or create a new document with the "Background Contents" set to White or Background Color. It has the following restrictions: it cannot have transparency (no alpha channel), it cannot be reordered (always at the bottom of the stack), and it is locked by default.

**Meaning in photographic practice:** The Background layer typically holds your original photograph. Many photographers immediately duplicate it or convert it to a regular layer. Others leave it locked as a safety net — the lock prevents accidental painting or transforming of the original.

**Photoshop implementation:** Identified by the name "Background" in italics in the Layers panel, with a lock icon. To convert it to a regular layer, double-click it and click OK (or rename it), or click the lock icon. The conversion allows transparency and reordering.

**RECOMMENDATION:** For photographic editing, keep the Background layer locked and untouched as your original reference. Build all edits on layers above it. If you need to transform or resize the image, convert the Background to a Smart Object first, so the original pixel data is preserved inside it.

**Related concepts:** Layer lock (several lock types exist in Photoshop — the Background layer uses a full lock by default).

**Common misconception:** "I need to unlock the Background to start editing." You do not need to unlock it. You can add adjustment layers, retouching layers, and Smart Objects above the locked Background without unlocking it. Unlocking is only necessary if you need to add transparency to the Background itself, reorder it, or apply certain operations directly to it.


## Theory: How Photoshop Composites Layers

### The Rendering Pipeline

Understanding how Photoshop renders the final image from a stack of layers is not an academic exercise. It determines the visual output and dictates how you must structure your layer stack.

**FACT:** Photoshop renders the visible image through the following process, evaluated for each pixel position on the canvas:

1. Start at the bottom-most visible layer.
2. Take its pixel value (or generated value, for fill/adjustment layers) at this position.
3. Move to the next visible layer above.
4. Apply that layer's mask (if any) to determine the layer's transparency at this position.
5. Apply the layer's blending mode to combine the layer's value with the composite below.
6. Apply the layer's opacity (and fill) to determine the final contribution.
7. The result becomes the new composite. Move to the next visible layer and repeat.
8. Continue until all visible layers have been composited.

This process happens for every pixel, for every channel, independently. The result is the image displayed on screen and the image that would be produced by flattening.

### Opacity and Blending in the Pipeline

Opacity and blending mode interact in a specific order:

1. The layer's raw pixel values and the composite below are fed into the **blending mode** formula, producing a "blended result."
2. The **opacity** value then interpolates between the blended result and the original composite below. At 100% opacity, you see the full blended result. At 50% opacity, you see a 50/50 mix of the blended result and the original composite.
3. **Fill** operates similarly to opacity but, for certain blending modes (the "Special 8"), it interacts differently with the blending formula's internal calculations. The details are covered in Chapter 14.

### Layer Order Matters

Because compositing is bottom-to-top, the order of layers changes the result. This is intuitive for pixel layers (a layer on top obscures a layer below), but less intuitive for adjustment layers.

Consider this stack:

```
Hue/Saturation (desaturate reds)
Curves (boost red channel)
Background
```

The Curves layer boosts reds in the Background. The Hue/Saturation layer then desaturates the boosted reds. Reversing the order of the two adjustment layers would produce a different result: first desaturating reds, then boosting what remains.

**FACT:** Adjustment layers operate on the composite of everything visible below them, not on the original image data. An adjustment layer at the top of the stack "sees" the cumulative effect of all adjustments below it.

### Groups and Compositing

As mentioned in the terminology section, groups with **Pass Through** blending mode are compositing-transparent — their layers interact with the rest of the stack as if the group were not there. Changing a group's blending mode to anything else (including Normal) creates an isolated compositing context:

1. The layers inside the group are composited among themselves.
2. The group's composite result is then blended with the layers below the group using the group's blending mode and opacity.

**INTERPRETATION:** This behavior is useful when you want a set of layers to interact only with each other and not with layers outside the group. For example, a dodge-and-burn group set to Normal ensures that the dodge/burn layers only affect the composite *as it exists below the group*, without interaction between dodge/burn layers and layers above the group in unexpected ways.

### Bit Depth and Layer Compositing

**FACT:** All layer compositing occurs at the document's working bit depth. If your document is 8-bit, the intermediate compositing results are quantized to 8 bits at each step, which can introduce rounding errors and banding in extreme adjustments. Working in 16-bit provides substantially more precision during compositing, at the cost of approximately doubling RAM usage and file size.

**RECOMMENDATION:** For photographic editing with multiple adjustment layers, work in 16-bit mode. Convert to 8-bit only at the final output stage if the delivery format requires it.


## Photoshop Implementation

### Layer Types Reference

| Layer type | Contains | Created via | Can paint on it? | Can apply filters? | Non-destructive? |
|---|---|---|---|---|---|
| Pixel layer | Raster pixels | New Layer, Duplicate, Paste | Yes | Yes (destructive) | No (edits modify pixels) |
| Adjustment layer | Adjustment parameters + mask | New Adjustment Layer, Adjustments panel | No (paint on its mask) | No | Yes |
| Fill layer | Color/gradient/pattern + mask | New Fill Layer | No (paint on its mask) | No | Yes |
| Smart Object | Embedded/linked source file | Convert to Smart Object, Place | No (must open source) | Yes (as Smart Filters) | Yes |
| Type layer | Vector text | Type tool | No (must rasterize first) | Limited (as Smart Filters if converted) | Yes (text remains editable) |
| Shape layer | Vector path + fill | Shape tools | No | Limited | Yes (path remains editable) |
| Layer group | Other layers | New Group, Ctrl+G / Cmd+G | No | No | N/A (organizational) |

### The Layers Panel

The Layers panel is the control center for all layer operations. Its key elements:

- **Layer list:** Shows all layers in compositing order (top = rendered last = visually on top).
- **Visibility eye:** Toggles layer visibility without deleting it. Alt-click to solo a layer (hide all others).
- **Blending mode dropdown:** Top-left of the panel.
- **Opacity and Fill fields:** Top-right of the panel, with scrubby sliders.
- **Lock buttons:** Lock transparency, pixels, position, or all. The lock icon on the Background layer is a full lock.
- **Link indicator:** Links selected layers so they move together.
- **Layer mask thumbnail:** Appears next to the layer thumbnail when a mask is present. Click to select the mask for editing; Shift-click to disable it temporarily.
- **Bottom buttons:** (left to right) Link layers, Layer styles, Add mask, New adjustment/fill layer, New group, New layer, Delete layer.

### The Properties Panel

**FACT:** When an adjustment layer is selected, the Properties panel displays that adjustment's parameters (the Curves graph, the Levels sliders, etc.). This is where you edit adjustment layer settings. When a mask is selected, the Properties panel shows mask properties (density, feather).

**VERSION NOTE:** The Properties panel was introduced in Photoshop CS6 (2012). In earlier versions, adjustment parameters appeared in the Adjustments panel. Current versions of Photoshop use the Properties panel.

### Creating and Managing Layers

**New empty pixel layer:** **Layer > New > Layer**, or click the New Layer button in the Layers panel, or Ctrl+Shift+N (Cmd+Shift+N). Creating via the button creates the layer with default settings immediately; using the menu or keyboard shortcut opens a dialog where you can set the name, blending mode, and color.

**Duplicate layer:** **Layer > Duplicate Layer**, or Ctrl+J (Cmd+J). If a selection is active, Ctrl+J creates a new layer containing only the selected pixels. If no selection is active, it duplicates the entire layer.

**Delete layer:** Select the layer and press the Delete/Backspace key, or drag to the trash icon, or **Layer > Delete > Layer**.

**Reorder layers:** Drag in the Layers panel. Ctrl+] (Cmd+]) moves a layer up; Ctrl+[ (Cmd+[) moves it down. Ctrl+Shift+] moves to the top; Ctrl+Shift+[ moves to the bottom.

**Select multiple layers:** Click to select one, then Ctrl-click (Cmd-click) to add individual layers, or Shift-click to select a contiguous range.

### Smart Object Operations

**Convert to Smart Object:** Select one or more layers, then **Layer > Smart Objects > Convert to Smart Object**, or right-click > Convert to Smart Object. Multiple selected layers are wrapped into a single Smart Object.

**Edit Smart Object contents:** Double-click the Smart Object thumbnail. The embedded source opens in a separate document window (or in Camera Raw, if the source is a RAW file). Edit, save, and close — the Smart Object updates.

**Rasterize:** Right-click the Smart Object > Rasterize Layer. This replaces the Smart Object with a regular pixel layer at the current rendered resolution. The embedded source is permanently discarded. **This is destructive and irreversible.**

**RECOMMENDATION:** Before rasterizing a Smart Object, ask yourself why. If you need to paint on it, consider opening the Smart Object's source and painting there. If you need to apply a filter, apply it as a Smart Filter (which requires no rasterization). Rasterize only when there is no alternative, and consider duplicating the Smart Object first as a backup.

**Replace Contents:** Right-click > Replace Contents. This swaps the embedded source file with a different file while maintaining all transformations, masks, and Smart Filters applied to the Smart Object. Useful for batch-processing similar images with the same adjustments.

### Layer Styles vs. Layer Effects

**FACT:** Layer styles (Drop Shadow, Inner Glow, Bevel and Emboss, Stroke, etc.) are effects attached to a layer that are computed dynamically based on the layer's content. They are non-destructive and update automatically when the layer's content changes.

**INTERPRETATION:** Layer styles are more relevant to graphic design than photography. However, Stroke and Color Overlay can occasionally be useful for photographic presentation (border effects, contact sheets). Understanding that they exist — and that they are affected differently by Opacity vs. Fill — is sufficient for photographic work.


## Professional Workflow: Layer Architecture for Photographic Editing

This section presents a recommended layer stack architecture for photographic editing. This is not the only valid approach, but it addresses the most common organizational problems and scales well from simple edits to complex composites.

### Why Architecture Matters

Without a deliberate layer structure, a complex edit devolves into chaos: unnamed layers, adjustments in random order, destructive edits applied directly to the photograph, and no ability to revisit earlier decisions. The cost of this chaos is not apparent on a 3-layer edit. It becomes crippling on a 20-layer edit, and it makes collaboration (or revisiting your own work months later) nearly impossible.

**RECOMMENDATION:** Establish a layer architecture before you begin editing. The time investment is minimal; the payoff is substantial.

### Recommended Layer Stack

The following architecture organizes a photographic edit into logical groups, ordered from bottom to top:

```
--- TOP OF STACK ---

OUTPUT PREPARATION (group)
    Sharpening (Stamp Visible, Smart Object, Smart Sharpen/USM)
    Noise reduction (if needed at this stage)
    Crop/resize (if final output requires it)

LOCAL ADJUSTMENTS (group)
    Dodge and burn layer(s) (50% gray, Soft Light/Overlay)
    Gradient masks for local exposure
    Targeted color/tone adjustments with painted masks

COLOR (group)
    Color Balance / Photo Filter
    Hue/Saturation
    Selective Color
    Color grading adjustment layers

TONE (group)
    Curves (contrast)
    Levels (black/white point)
    Exposure/Brightness adjustments

RETOUCHING (group)
    Healing layer (empty pixel layer, "Sample All Layers" enabled)
    Clone Stamp layer (empty pixel layer, "Sample All Layers" enabled)
    Spot removal layer

BASE
    Background (original photograph, locked)

--- BOTTOM OF STACK ---
```

### Rationale for This Order

**Background at the bottom, locked and untouched.** This is your original. Every edit above it is non-destructive relative to this base. If everything goes wrong, you can delete every layer above it and start over.

**Retouching above the Background.** Retouching (clone stamp, healing brush, spot removal) must be done on the actual image content before tonal and color adjustments are applied. If you apply heavy contrast first and then retouch, the retouching must match the adjusted tones, which is harder and more error-prone. By retouching first (on empty layers with "Sample All Layers"), you work on the natural image tones.

**INTERPRETATION:** Some photographers prefer to retouch after initial tone adjustments, arguing that you need to see the final contrast to judge what blemishes are visible. Both approaches are valid. The key principle is to use separate retouching layers, not to paint directly on the Background.

**Tonal adjustments above retouching.** Levels and Curves set the overall tonal range and contrast. These are global adjustments that affect the entire image.

**Color adjustments above tonal.** Color corrections and grading are applied after the tonal range is established. The reason: color perception is affected by luminosity. Adjusting color before establishing the correct tonal range means you may need to readjust color after every tonal change.

**Local adjustments above global.** Local adjustments (dodging, burning, selective corrections) refine specific areas. These are applied after global tone and color because they are fine-tuning operations that depend on the global adjustments being reasonably final.

**Output preparation at the top.** Sharpening, noise reduction, and resizing are the last operations. They are output-specific — different output media (screen, print, large print) may require different sharpening. Placing these at the top means you can have multiple output preparation groups (or swap them out) without affecting the underlying edit.

### Implementation Details

**Retouching on empty layers:** Create empty pixel layers for retouching, not duplicates of the Background. In the tool options bar for the Clone Stamp and Healing Brush, set the "Sample" dropdown to **Current & Below** or **All Layers**. This samples the visible composite below but deposits the corrected pixels onto the empty layer. The original Background remains untouched.

**FACT:** When using the Healing Brush or Clone Stamp on an empty layer with "Sample All Layers," the tool reads the composite below but writes only to the active empty layer. This allows non-destructive retouching. However, if you later change the layers below (for example, modify a tonal adjustment), the retouching pixels on the empty layer will not update — they are fixed. This can cause visible mismatches for strong tonal changes applied after retouching.

**Adjustment layers with masks for local corrections:** For local adjustments, create an adjustment layer and then fill its mask with black (Edit > Fill > Black, or Ctrl+I / Cmd+I to invert the white mask). Then paint white on the mask where you want the adjustment to appear. This gives you precise spatial control.

**Dodge and burn on a 50% gray layer:** Create a new pixel layer, fill it with 50% gray (Edit > Fill > 50% Gray), and set the blending mode to Soft Light or Overlay. Paint with white to dodge (lighten) and black to burn (darken). The 50% gray is neutral for these blending modes and renders invisible; only your painted deviations from 50% gray affect the image.

**INTERPRETATION:** The 50% gray dodge/burn technique is widely used but has alternatives. Some photographers prefer to use Curves adjustment layers with black masks, painting white on the mask to apply local brightening or darkening. The advantage of the Curves approach is that you can adjust the curve shape after painting. The advantage of the gray layer approach is visual simplicity and speed.

### Naming Conventions

**RECOMMENDATION:** Name every layer and group descriptively. "Curves 1" tells you nothing three months later. "Curves - midtone contrast" tells you exactly what it does. Photoshop allows renaming by double-clicking the layer name in the Layers panel.

Use consistent naming conventions. For example:

| Group | Naming pattern | Example layer names |
|---|---|---|
| Retouching | Tool + area | "Heal - skin", "Clone - background cleanup" |
| Tone | Adjustment + purpose | "Curves - contrast S", "Levels - set black point" |
| Color | Adjustment + purpose | "Hue/Sat - reduce sky cyan", "Color Balance - warm shadows" |
| Local | Effect + area | "Dodge - foreground rocks", "Burn - sky corners" |
| Output | Operation + spec | "Sharpen - web output", "Resize - 2048px" |

### Color Coding

**FACT:** Photoshop allows you to assign colors to layers and groups (right-click > layer color). This provides an additional visual cue in the Layers panel. The colors are: Red, Orange, Yellow, Green, Blue, Violet, Gray, and None.

**RECOMMENDATION:** Use a consistent color scheme across all your edits. For example: Red for retouching, Orange for tonal adjustments, Yellow for color adjustments, Green for local corrections, Blue for output. The specific colors matter less than consistency.


## Step-by-Step Example: Building a Layer Structure for a Landscape Edit

This example walks through building a complete layer architecture for a landscape photograph with the following characteristics: underexposed foreground, slightly oversaturated sky, a sensor dust spot, and output needed for both web and print.

### Step 1: Open and Assess

Open the image in Photoshop. It arrives as a locked Background layer.

Assess the image:

- Overall exposure: Foreground is underexposed by approximately 1 stop.
- Sky: Good exposure but slightly oversaturated in the blue channel.
- Dust spot: Upper-left corner, visible against the sky.
- Color: Slightly cool overall; shadows need warming.

Do not start editing yet. Plan your layer structure first.

### Step 2: Create the Group Structure

Create the following empty groups, from bottom to top:

1. Select the Background layer. **Layer > New > Group**, name it "RETOUCH." Set color to Red.
2. With the RETOUCH group selected, create a new group above: "TONE." Color: Orange.
3. Above TONE: "COLOR." Color: Yellow.
4. Above COLOR: "LOCAL." Color: Green.
5. Above LOCAL: "OUTPUT." Color: Blue.

The Layers panel now reads (top to bottom):

```
OUTPUT (Blue)
LOCAL (Green)
COLOR (Yellow)
TONE (Orange)
RETOUCH (Red)
Background (locked)
```

### Step 3: Retouching

1. Select the RETOUCH group. Create a new empty pixel layer inside it: "Heal - dust spots."
2. Select the Healing Brush. In the options bar, set Sample to **Current & Below**.
3. Heal the dust spot by sampling clean sky adjacent to it.

The dust spot is removed on a separate layer. The Background is untouched.

### Step 4: Tonal Adjustments

1. Select the TONE group. Add a **Levels** adjustment layer (name: "Levels - black/white points").
2. In the Properties panel, set the black point and white point using the histogram as a guide — drag the black point slider to where the histogram data begins, and similarly for the white point. This ensures full tonal range utilization.
3. Add a **Curves** adjustment layer above (name: "Curves - midtone contrast").
4. Add a gentle S-curve to increase midtone contrast: pull the shadows control point slightly down and the highlights control point slightly up.

### Step 5: Color Adjustments

1. Select the COLOR group. Add a **Color Balance** adjustment layer (name: "Color Balance - warm shadows").
2. In the Properties panel, select Shadows and shift the Cyan/Red slider slightly toward Red, and the Yellow/Blue slider slightly toward Yellow. This warms the shadow tones.
3. Add a **Hue/Saturation** adjustment layer (name: "Hue/Sat - reduce sky blue saturation").
4. In the Properties panel, select the Blues channel and reduce Saturation by 15-20 points.

### Step 6: Local Adjustments

1. Select the LOCAL group. Add a **Curves** adjustment layer (name: "Curves - brighten foreground").
2. Pull the midtone of the curve upward to brighten.
3. Invert the mask: with the mask selected, press Ctrl+I (Cmd+I). The mask turns black, hiding the adjustment entirely.
4. Select a soft white brush at 30-50% opacity. Paint on the mask over the foreground area. The brightening appears only where you paint.
5. Optionally, add a 50% gray layer set to Soft Light for final dodge/burn refinement (name: "Dodge/Burn - fine"). Use a soft brush at 10-15% opacity with white to dodge and black to burn.

### Step 7: Output Preparation

1. Select the OUTPUT group. Select the topmost layer in the document.
2. Press Ctrl+Alt+Shift+E (Cmd+Option+Shift+E) to Stamp Visible. A new pixel layer appears containing the full composite. Name it "Composite - web sharpen."
3. Convert this layer to a Smart Object (right-click > Convert to Smart Object).
4. Apply **Filter > Sharpen > Smart Sharpen** with settings appropriate for web output (for example, Amount 80%, Radius 0.5px, Reduce Noise 10%). Because the layer is a Smart Object, this appears as an editable Smart Filter.

### Result

The final stack contains approximately 10 layers organized into 5 groups. Every adjustment is non-destructive and independently editable. The Background is untouched. The retouching, tonal, color, and local adjustments are cleanly separated. The output sharpening can be re-edited or replaced for different output targets.


## Common Mistakes

### 1. Editing Directly on the Background Layer

**The mistake:** Applying clone stamp, healing, or painting directly to the Background pixel layer.

**Why it matters:** These edits permanently modify the original pixel data. If you need to undo retouching work later — even months later — you cannot, because the original pixels are overwritten.

**The fix:** Always create a separate empty pixel layer for retouching. Set the tool's Sample option to "Current & Below" or "All Layers."

### 2. Too Many Unnamed Layers

**The mistake:** Accumulating layers named "Layer 1," "Curves 2," "Layer 3 copy" without descriptive names.

**Why it matters:** You cannot efficiently navigate your edit. Returning to a file after a week (or handing it to a collaborator) requires guessing what each layer does by toggling visibility one at a time.

**The fix:** Name every layer immediately when you create it. Use the naming conventions described in the Professional Workflow section.

### 3. Flattening the Working File

**The mistake:** Flattening the PSD to reduce file size, then saving over the original.

**Why it matters:** All layer editability is permanently destroyed. This is the most common cause of "I wish I could change that adjustment, but I flattened the file."

**The fix:** Never flatten your working PSD. If you need a flat file for output, use **File > Save As** (or **File > Save a Copy**) to save a separate flattened TIFF or JPEG while keeping the layered PSD intact.

### 4. Using Merge When Stamp Visible Would Suffice

**The mistake:** Merging visible layers (Ctrl+Shift+E / Cmd+Shift+E) when you need a composite for filtering.

**Why it matters:** Merge Visible replaces all the merged layers with a single pixel layer, destroying their individual editability.

**The fix:** Use Stamp Visible (Ctrl+Alt+Shift+E / Cmd+Option+Shift+E) instead. This creates a new composite layer while preserving all layers below.

### 5. Ignoring Adjustment Layer Scope

**The mistake:** Adding an adjustment layer intending it to affect one specific layer, but not using a clipping mask. The adjustment affects everything below it.

**Why it matters:** Unintended changes to layers below the target layer, which may not be immediately visible.

**The fix:** Use a clipping mask (Alt-click / Option-click the line between the adjustment layer and its target) to restrict the adjustment to a single layer.

### 6. Rasterizing Smart Objects Without Backup

**The mistake:** Rasterizing a Smart Object to perform a pixel-level operation, without keeping a copy of the original Smart Object.

**Why it matters:** The embedded source data is permanently discarded. You cannot re-edit the RAW development settings, re-apply transformations losslessly, or modify Smart Filters.

**The fix:** Duplicate the Smart Object layer before rasterizing (Ctrl+J / Cmd+J). Hide the copy and keep it as a fallback. Better yet, find a non-destructive alternative to rasterizing.

### 7. Confusing Opacity and Fill

**The mistake:** Treating Opacity and Fill as interchangeable for all blending modes.

**Why it matters:** For blending modes such as Vivid Light, Hard Mix, or Color Burn, reducing Fill produces a substantially different visual result than reducing Opacity. Using the wrong slider gives you the wrong effect with no indication of why.

**The fix:** Understand the distinction (covered in Chapter 14) and test both sliders when working with non-Normal blending modes.


## Alternative Approaches

### Lightroom / Camera Raw Only Workflow

For photographers who do not need compositing, complex masking, or pixel-level retouching, Adobe Lightroom (or Camera Raw's editing tools) provides a layer-free non-destructive editing environment. All adjustments are stored as metadata (a list of instructions), and the original file is never modified.

**Trade-off:** Simpler workflow, no layer management. But no pixel-level control, limited masking, no compositing, and less precise tool behavior.

### Affinity Photo

Affinity Photo offers a similar layer architecture to Photoshop (pixel layers, adjustment layers, live filter layers, groups) at a one-time purchase price. Its core concepts are nearly identical, and skills transfer well.

**Trade-off:** Excellent value. But smaller ecosystem, fewer third-party plugins, and some Photoshop-specific features (certain blending mode behaviors, some Smart Object capabilities) differ in subtle ways.

### Flat-File Editing (One-Layer Workflow)

Some photographers deliberately work without layers, applying all adjustments directly and in sequence. This was the standard workflow before layers existed and is still used in some specialized contexts (astronomical imaging, scientific photography).

**Trade-off:** Simpler file structure, smaller file sizes, faster performance. But every edit is destructive, decisions cannot be revisited, and the approach does not scale to complex edits.


## Summary

- Photoshop's layer system is a compositing engine that renders the final image by combining all visible layers from bottom to top.
- **Pixel layers** contain raster image data. **Adjustment layers** contain non-destructive editing parameters. **Smart Objects** contain embedded source files that preserve quality through transformations. **Fill layers** generate parametric fills. **Layer groups** organize layers and can affect compositing.
- Layer compositing follows a strict bottom-to-top order. Layer order changes the rendered result.
- **Opacity** controls overall layer transparency. **Fill** controls pixel transparency but not layer style transparency, and behaves differently from Opacity for certain blending modes.
- **Layer masks** control where a layer is visible using a grayscale map. **Clipping masks** restrict a layer's visibility to the non-transparent areas of the layer below.
- **Flatten** destroys all layers. **Merge** destroys the merged layers. **Stamp Visible** creates a composite copy while preserving all layers. Know the difference.
- A deliberate layer architecture — with the original at the bottom, retouching above it, global tonal adjustments, color adjustments, local adjustments, and output preparation at the top — keeps edits organized, non-destructive, and revisitable.
- Name every layer. Color-code groups. Establish conventions and follow them consistently.


## Exercises

### Exercise 1: Layer Type Identification

Open any photograph in Photoshop. Build the following stack without performing any actual adjustments. The goal is to practice creating and organizing layer types:

1. Keep the Background locked.
2. Create an empty pixel layer named "Retouching."
3. Create a Curves adjustment layer named "Tone - Curves."
4. Create a Solid Color fill layer (any color), set to Multiply at 20% Opacity, named "Color Overlay."
5. Create a group named "Local Adjustments" and place a Levels adjustment layer inside it with a black (fully hiding) mask.
6. Convert a duplicate of the Background to a Smart Object, name it "Smart Object Test."

Verify that you can identify each layer type by its thumbnail appearance. Toggle each layer's visibility and observe the effect on the composite.

### Exercise 2: Merge, Flatten, and Stamp Visible Comparison

Using the stack from Exercise 1:

1. Save the file as "exercise_02_original.psd."
2. Use **Stamp Visible** (Ctrl+Alt+Shift+E / Cmd+Option+Shift+E). Observe that all original layers remain. Delete the stamped layer.
3. Use **Merge Visible** (Ctrl+Shift+E / Cmd+Shift+E). Observe that all visible layers are replaced by a single pixel layer. Undo immediately (Ctrl+Z / Cmd+Z).
4. Use **Flatten Image** (Layer > Flatten Image). Observe that the result is a single Background layer with no transparency. Undo immediately.

Document in your notes: What was lost in each operation? What was preserved?

### Exercise 3: Clipping Mask vs. Layer Mask

1. Open a photograph. Duplicate the Background layer (Ctrl+J / Cmd+J).
2. Add a Hue/Saturation adjustment layer above the duplicate. Increase saturation to an extreme value (+80).
3. Observe: the adjustment affects the entire image.
4. Clip the adjustment layer to the duplicate (Alt-click / Option-click the line between them). Observe: the adjustment still affects the entire visible image, because the duplicate has the same content as the Background.
5. Now add a layer mask to the duplicate layer. Paint black on the mask over half the image.
6. Observe: the clipped adjustment now only appears where the duplicate layer is visible (where the mask is white).

This exercise demonstrates that clipping masks restrict based on the base layer's visible content (including its mask), while layer masks restrict based on a painted grayscale pattern.

### Exercise 4: Opacity vs. Fill

1. Open a photograph. Duplicate the Background.
2. Set the duplicate's blending mode to **Vivid Light**.
3. First, set Opacity to 30% and Fill to 100%. Observe the result.
4. Then set Opacity to 100% and Fill to 30%. Observe the result.
5. The two results are visibly different.

Repeat with **Normal** blending mode. Observe that the two results are now identical.

---

### Advanced Exercises

### Advanced Exercise 1: Build a Complete Portrait Edit Stack

Open a portrait photograph and build a full editing layer stack following the architecture described in the Professional Workflow section. Include:

- A retouching group with separate layers for skin healing and background cleanup
- A tonal group with black/white point correction and a contrast curve
- A color group with shadow warming
- A local group with a dodge/burn layer (50% gray, Soft Light) to shape facial lighting
- An output group with a stamped and sharpened Smart Object

Do not aim for a perfect edit. The goal is to practice building the architecture. Keep each adjustment subtle and focus on organization.

### Advanced Exercise 2: Non-Destructive Redo

Open the portrait from Advanced Exercise 1 (your completed layered PSD). Now simulate a change of creative direction:

1. The client wants cooler shadows instead of warmer shadows. Change the Color Balance parameters.
2. The client wants less contrast. Modify the Curves layer, not a new one.
3. The client wants the dodge/burn removed from the chin area. Paint black on the dodge/burn layer (not on a mask — directly with black paint, which is neutral on Soft Light mode) or reduce the layer opacity.
4. The client wants output sharpened for print (higher Amount, larger Radius). Double-click the Smart Sharpen filter on the Smart Object in the output group and change the settings.

All four changes should be possible without creating new layers or undoing previous work. If any change requires undoing or flattening, your architecture has a structural weakness — identify it.

---

### Blackbelt Challenge

Open a landscape photograph you have never edited before. Do not plan your layer structure in advance.

1. Assess the image: identify what tonal, color, and local adjustments it needs.
2. Build a complete layer architecture from scratch, making deliberate choices about layer types, order, grouping, and naming.
3. Execute the edit using only non-destructive techniques: adjustment layers, masks, empty retouching layers, Smart Objects.
4. When finished, hide all layers except the Background and compare it with the full composite.
5. Then selectively disable individual adjustments to evaluate each one's contribution.

The test: Can you modify any single aspect of the edit — tone, color, local adjustment, sharpening — without affecting any other aspect? Can a colleague open this file and understand what every layer does without asking you? If both answers are yes, you have built a professional-quality layer architecture.
