---
description: RGB channels, alpha channels, layer masks, vector masks, clipping masks, Quick Mask mode — how Photoshop stores color data and controls visibility through grayscale maps.
---

# Chapter 05: Channels, Masks, and Alpha Channels

## Learning Objectives

After completing this chapter, you will be able to:

1. Explain what a channel is in Photoshop, and distinguish between color channels and alpha channels.
2. Describe how the RGB channel structure stores color data and how individual channels relate to the full-color composite.
3. Explain how a mask works mathematically — grayscale values as opacity multipliers — and predict the visual result of any mask value.
4. Create, edit, and refine layer masks, and use them to apply adjustments selectively to specific image areas.
5. Distinguish between layer masks, vector masks, and clipping masks, and choose the appropriate type for a given task.
6. Use Quick Mask mode to visualize and refine selections as painted masks.
7. Explain the relationship between selections, masks, and alpha channels — how they are different representations of the same underlying concept.
8. Use the Density and Feather controls in the Properties panel to modify mask behavior non-destructively.
9. Use Select and Mask (Refine Edge) to produce accurate masks for complex edges such as hair and foliage.


## Conceptual Foundation

### The Two Roles of Channels

The word "channel" in Photoshop refers to two fundamentally different things, and this dual meaning causes persistent confusion.

The first role: **color channels**. An RGB image is composed of three grayscale images — one for red, one for green, one for blue. Each of these grayscale images is a channel. Together, they define the color and brightness of every pixel. You saw this in Chapter 01: each pixel stores three values (R, G, B), and each value lives in its own channel.

The second role: **alpha channels**. An alpha channel is an additional grayscale image stored alongside the color channels. It does not represent color. It represents a selection, a mask, or a transparency map — a grayscale image where each pixel's value indicates how visible or selected something is at that position.

Both types of channels are grayscale images with values from 0 to 255 (in 8-bit). Both are stored in the same Channels panel. But they serve completely different purposes: color channels define what the image looks like; alpha channels define what is visible, selected, or masked.

**FACT:** An RGB document always contains exactly three color channels (Red, Green, Blue) plus a composite view (RGB). Alpha channels are additional channels you create. A CMYK document contains four color channels. A Grayscale document contains one. The Channels panel shows all of them.

### Why Masks Matter for Photography

As a photographer, you already understand local adjustments. In the darkroom, you held back light with your hands or cardboard cutouts to dodge an area, or gave extra exposure to burn it down. The shape of your hand determined where the effect applied. The precision was limited.

A mask in Photoshop is the digital equivalent — but with pixel-level precision, complete reversibility, and the ability to control not just where an effect applies but how strongly it applies at every point. A white area on the mask is like fully exposing the paper. A black area is like completely blocking the light. A gray area is partial — and you control the exact percentage.

Every selective adjustment in Photoshop — every time you brighten only the foreground, saturate only the sky, sharpen only the subject — depends on masks. If Chapter 04 established that layers are the foundation of non-destructive editing, this chapter establishes that masks are the foundation of selective editing. Together, they form the two pillars of professional Photoshop work.

### The Core Insight: Masks Are Grayscale Images

A mask is not a special or mysterious construct. It is a grayscale image, the same dimensions as your document, where each pixel's brightness value controls a single thing: how much of the associated layer is visible at that position.

That is the entire concept. Everything else — layer masks, vector masks, Quick Mask, alpha channels, Select and Mask — is a variation on this one idea: a grayscale image controlling visibility.


## Terminology

### Channel (Color Channel)

**Definition:** A single grayscale image that represents one color component of the full image. In an RGB document, the three color channels are Red, Green, and Blue.

**Meaning in photographic practice:** Each color channel is effectively a black-and-white photograph of the scene as seen through a filter of that color. The Red channel is bright where the scene contains red light and dark where it does not. The Green channel shows the distribution of green light. The Blue channel shows blue. When all three are combined by the display, you see the full-color image.

For photographers, individual channels are diagnostically valuable. The Blue channel in a digital photograph typically contains the most noise (because camera sensors are least sensitive to blue light). The Red channel often has the best tonal separation in skin tones. Examining channels individually reveals information that is invisible in the composite view.

**Photoshop implementation:** Viewed in the Channels panel (Window > Channels). Clicking a single channel (Red, Green, or Blue) displays that channel as a grayscale image and hides the others. Clicking the composite (RGB) at the top restores the full-color view. Each channel can be selected for targeted editing — for example, applying Curves to only the Red channel.

Keyboard shortcuts for channel visibility: Ctrl+~ (Cmd+~) for the composite, Ctrl+3 (Cmd+3) for Red, Ctrl+4 (Cmd+4) for Green, Ctrl+5 (Cmd+5) for Blue.

**VERSION NOTE:** The exact keyboard shortcut for the composite channel varies across Photoshop versions and operating systems. On some versions it is Ctrl+2 (Cmd+2). Verify in your current version via the Channels panel fly-out menu.

**Related concepts:** Composite (the combined RGB view), alpha channel (a non-color channel stored alongside the color channels), histogram (can display per-channel or composite data).

**Common misconception:** "The Red channel shows everything that is red in the image." The Red channel shows the intensity of the red component at every pixel, not just areas that appear red. A white area in the image has high values in all three channels, including Red. A bright yellow area has high Red and high Green but low Blue. The Red channel is not a mask of "red things" — it is a record of how much red light is present everywhere.

---

### Alpha Channel

**Definition:** An additional grayscale channel stored in a document alongside the color channels. Alpha channels represent selections, masks, or transparency information — not color data.

**Meaning in photographic practice:** Alpha channels are the storage mechanism for selections and masks. When you save a selection (Select > Save Selection), it is stored as an alpha channel. When you create a layer mask, Photoshop stores it as an alpha channel attached to that layer. Alpha channels allow you to preserve complex selections — a carefully refined mask around hair, a luminosity selection, a hand-painted mask — and reuse them later.

For photographers who build complex masks (luminosity masks, refined hair masks, sky selections), alpha channels are the filing system that makes this work possible. Without them, every selection would be temporary and lost the moment you deselect.

**Photoshop implementation:** Visible in the Channels panel below the color channels. Created via Select > Save Selection, or by clicking the "Save selection as channel" button at the bottom of the Channels panel, or automatically when you add a layer mask. Alpha channels appear as additional entries in the Channels panel, named "Alpha 1," "Alpha 2," etc. (renamable by double-clicking).

To load an alpha channel as a selection: Ctrl-click (Cmd-click) its thumbnail in the Channels panel, or use Select > Load Selection.

**Related concepts:** Layer mask (an alpha channel attached to a specific layer), selection (a temporary marching-ants boundary that can be saved as an alpha channel), Quick Mask (a temporary visualization of a selection as an alpha channel).

**Common misconception:** "Alpha channels increase file size enormously." An alpha channel adds exactly one additional grayscale image at the document's dimensions and bit depth. For an 8-bit, 6000x4000 pixel image, one alpha channel adds approximately 24 MB of uncompressed data. This is non-trivial but far less than many photographers assume, and file formats like PSD and TIFF compress alpha channel data effectively. A document with five alpha channels is entirely manageable.

---

### Layer Mask

**Definition:** A grayscale image (an alpha channel) attached to a specific layer that controls the transparency of that layer on a pixel-by-pixel basis. White areas of the mask reveal the layer content; black areas hide it; gray values produce proportional partial transparency.

**Meaning in photographic practice:** Layer masks are the primary mechanism for selective editing. If a Curves adjustment layer brightens the entire image but you only want to brighten the foreground, you paint black on the Curves layer's mask over the sky. The adjustment's parameters remain unchanged everywhere — the mask controls where the adjustment is visible.

This is the single most important concept for photographic post-processing in Photoshop. Virtually every professional photographic edit uses layer masks to control where adjustments apply.

**Photoshop implementation:** Added by selecting a layer and clicking the "Add layer mask" button (rectangle-with-circle icon) at the bottom of the Layers panel. By default, the mask is white (fully revealing). Alt-clicking (Option-clicking) the button creates a black (fully hiding) mask. Adjustment layers are created with a white layer mask automatically.

To edit the mask: click the mask thumbnail in the Layers panel (a border appears around the mask thumbnail to confirm it is selected), then paint with black (to hide), white (to reveal), or gray (for partial transparency). Press D to set default colors (white foreground, black background when a mask is selected), press X to swap foreground and background.

To view the mask as a grayscale overlay: Alt-click (Option-click) the mask thumbnail. The canvas shows the mask as a black-and-white image. Alt-click again to return to normal view. To view the mask as a red overlay (rubylith): press \ (backslash) while the mask is selected, or Shift+Alt-click (Shift+Option-click) the mask thumbnail.

To disable the mask temporarily: Shift-click the mask thumbnail. A red X appears over the mask, and the layer displays as if the mask did not exist. Shift-click again to re-enable.

**Related concepts:** Alpha channel (a layer mask is stored as an alpha channel), Properties panel (displays Density and Feather controls when a mask is selected), vector mask (a resolution-independent alternative), clipping mask (a different mechanism based on layer transparency).

**Common misconception:** "Layer masks delete pixels." Layer masks hide pixels; they do not delete them. The underlying pixel data is completely intact. Painting white on the mask reveals it again. The only destructive operation is applying the mask (right-click the mask thumbnail > Apply Layer Mask), which permanently deletes the hidden pixels and removes the mask.

---

### Vector Mask

**Definition:** A mask defined by a vector path (Bezier curves) rather than by raster pixels. The path defines a sharp-edged boundary: everything inside the path is visible, everything outside is hidden.

**Meaning in photographic practice:** Vector masks are less common in photographic editing than layer masks, but they are essential when you need a geometrically precise, resolution-independent edge. Product photography cutouts, architectural selections with straight lines, or any situation requiring a mathematically clean edge benefits from a vector mask. Because the mask is defined by a path rather than pixels, it remains perfectly sharp at any zoom level and any output resolution.

**Photoshop implementation:** Created via Layer > Vector Mask > Reveal All (or Hide All), or by drawing a path with the Pen Tool and then adding it as a vector mask via Layer > Vector Mask > Current Path. In the Layers panel, the vector mask appears as a second mask thumbnail to the right of the layer mask thumbnail (a layer can have both a layer mask and a vector mask simultaneously).

Vector masks are edited with the path selection tools (Path Selection Tool, Direct Selection Tool) and the Pen Tool. They cannot be painted on with a brush — they are paths, not pixels.

**Related concepts:** Layer mask (a raster mask that can be painted with soft edges and gradients), Pen Tool (the primary tool for creating and editing vector paths), clipping path (a vector path used to define a cutout for print production — related but distinct from a vector mask).

**Common misconception:** "Vector masks can do everything layer masks can do, but better." Vector masks produce only hard-edged boundaries (inside or outside the path). They cannot represent gradual transitions, soft edges, or partial transparency. For photographic tasks like blending a sky replacement with a soft horizon, or fading an effect across a gradient, a layer mask is necessary. Vector masks and layer masks solve different problems.

---

### Clipping Mask

**Definition:** A relationship between adjacent layers where the upper layer's visibility is restricted to the non-transparent areas of the layer immediately below it (the base layer). The base layer's pixel content acts as the mask.

**Meaning in photographic practice:** Clipping masks are most commonly used to restrict an adjustment layer so it affects only one specific layer below it, rather than the entire stack. For example, if you have an empty retouching layer with clone stamp work and you want to apply a Curves adjustment to only that retouching layer, you clip the Curves adjustment layer to it.

Clipping masks are also used in compositing: if you place a texture inside a text layer's shape, you clip the texture layer to the type layer. The texture is visible only where the text exists.

**Photoshop implementation:** Created by Alt-clicking (Option-clicking) the line between two layers in the Layers panel, or via Layer > Create Clipping Mask (Alt+Ctrl+G / Option+Cmd+G). Clipped layers display an indented thumbnail with a small downward-pointing arrow.

Multiple layers can be clipped to the same base layer. All clipped layers are constrained to the base layer's transparency.

**Related concepts:** Layer mask (controls visibility based on a painted grayscale map — fundamentally different from a clipping mask), base layer (the bottom layer of the clipping group, whose transparency defines the visible area).

**Common misconception:** "Clipping masks and layer masks are interchangeable." They solve fundamentally different problems. A layer mask is a grayscale image you paint to define where a layer is visible. A clipping mask uses another layer's existing transparency as the visibility boundary. A layer mask is self-contained on one layer; a clipping mask is a relationship between layers. They can be used simultaneously on the same layer — a clipped layer can also have its own layer mask, and both constraints apply.

---

### Quick Mask Mode

**Definition:** A temporary editing mode that displays the current selection as an editable red overlay (rubylith) on the image. In Quick Mask mode, you paint with brushes to modify the selection as if it were a mask.

**Meaning in photographic practice:** Quick Mask mode bridges the gap between selections and masks. Instead of using selection tools (which show marching ants and offer limited visual feedback), you switch to Quick Mask mode and paint the selection with brushes. This gives you the precision and visual feedback of mask painting with the convenience of converting the result back to a selection when you exit the mode.

For photographers, this is particularly useful for refining selections that are difficult to achieve with automated selection tools — for example, cleaning up a Quick Selection that missed part of a subject, or creating a soft-edged selection that fades gradually.

**Photoshop implementation:** Activated by pressing Q, or by clicking the Quick Mask button at the bottom of the Tools panel (a rectangle with a circle). By default, the masked (unselected) areas appear as a semi-transparent red overlay. Paint with black to add to the masked area (remove from selection), paint with white to remove from the masked area (add to selection). Press Q again to exit Quick Mask mode — the painted mask converts back to a marching-ants selection.

The overlay color and opacity can be changed by double-clicking the Quick Mask button in the Tools panel. You can also toggle whether the red overlay indicates masked areas or selected areas.

**FACT:** Quick Mask mode creates a temporary alpha channel named "Quick Mask" in the Channels panel. This channel is automatically deleted when you exit Quick Mask mode. If you want to preserve the work, save the selection (Select > Save Selection) before exiting.

**Related concepts:** Selection (Quick Mask is a visualization of a selection as a mask), layer mask (a permanent mask attached to a layer — Quick Mask is temporary), alpha channel (Quick Mask creates a temporary one).

**Common misconception:** "Quick Mask mode modifies the image." Quick Mask mode does not change any pixel data in the image. It only modifies the selection. The red overlay is a visualization aid, not a paint operation on the photograph. When you exit Quick Mask mode, the result is a selection, not a color change.

---

### Selection vs. Mask

This is not a single tool but a critical conceptual distinction.

**Definition:** A **selection** is a temporary boundary (displayed as marching ants) that defines which pixels are affected by subsequent operations. A **mask** is a persistent grayscale image stored in the document (as an alpha channel or layer mask) that controls visibility or effect strength on a per-pixel basis.

**Meaning in photographic practice:** A selection is what you make on the fly — you drag a marquee, run Select Subject, paint in Quick Mask mode. The moment you click elsewhere or deselect (Ctrl+D / Cmd+D), the selection is gone. A mask is what you store — it persists in the file, can be edited at any time, and survives saving, closing, and reopening the document.

Professional photographic editing relies on masks, not selections. You may start with a selection (using Quick Selection, Select Subject, or Color Range), but you immediately convert it to a layer mask or save it as an alpha channel. The selection was the means; the mask is the result.

**Photoshop implementation:** Selections are created by selection tools and the Select menu. They display as marching ants (which only show the 50% boundary — they do not represent partial selections or feathered edges). A selection is converted to a layer mask by selecting a layer and clicking the "Add layer mask" button while the selection is active. A selection is saved as an alpha channel via Select > Save Selection.

Conversely, a mask can be loaded as a selection: Ctrl-click (Cmd-click) the mask or alpha channel thumbnail.

**Related concepts:** Marching ants (the visual representation of a selection), feather (applies to both selections and masks), alpha channel (the storage format for saved selections and masks).

**Common misconception:** "The marching ants show exactly what is selected." Marching ants display only the 50% selection boundary. If you feather a selection, the marching ants show where the selection is 50% or more, but the gradual falloff below 50% is invisible. This is why masks are superior for precise work — you can see the actual grayscale values, not just a binary boundary.

---

### Mask vs. Alpha Channel

Another critical conceptual distinction that clarifies the architecture.

**Definition:** An **alpha channel** is a grayscale image stored in the Channels panel. A **layer mask** is an alpha channel that is attached to a specific layer, controlling that layer's transparency. Every layer mask is an alpha channel, but not every alpha channel is a layer mask.

**Meaning in photographic practice:** When you add a layer mask to a Curves adjustment layer, Photoshop creates an alpha channel and associates it with that layer. If you look in the Channels panel, you will see it listed (though it appears only when the layer is selected, in current Photoshop versions). When you save a selection via Select > Save Selection, Photoshop creates a standalone alpha channel that is not attached to any layer — it is simply stored in the document for future use.

The practical distinction: a layer mask directly controls the associated layer's visibility in real time. A standalone alpha channel just sits in the Channels panel doing nothing until you load it as a selection (Ctrl-click / Cmd-click its thumbnail) or apply it as a mask.

**Photoshop implementation:** Layer masks appear as thumbnails next to layers in the Layers panel and as channel entries in the Channels panel when that layer is selected. Standalone alpha channels appear at the bottom of the Channels panel, always visible regardless of which layer is selected.

**Related concepts:** Layer mask, selection, Quick Mask (creates a temporary alpha channel).

**Common misconception:** "Alpha channels and layer masks are completely different things." They are the same data structure (a grayscale image stored as a channel). The only difference is whether it is attached to a layer (layer mask) or free-standing (standalone alpha channel). You can duplicate a layer mask to create a standalone alpha channel, and you can load a standalone alpha channel as a layer mask.

---

### Density (Mask Property)

**Definition:** A property in the Properties panel that controls the maximum opacity of a mask. At 100% density, the mask operates normally (black = fully hidden). Reducing density makes the black areas of the mask less opaque — at 50% density, areas painted black on the mask are only 50% hidden rather than fully hidden.

**Meaning in photographic practice:** Density is a non-destructive way to reduce the strength of a mask without repanting it. If you spent time carefully painting a mask to hide an adjustment from the sky, but then decide the adjustment should still apply to the sky at reduced strength, you can lower the mask density instead of repainting the black areas with gray. The mask painting remains intact; the overall strength is simply scaled.

**Photoshop implementation:** Displayed in the Properties panel when a layer mask is selected. The slider goes from 0% to 100%. At 100% (default), the mask functions normally. Reducing density uniformly reduces the hiding power of all mask values. In effect, it remaps the mask range: at 50% density, a mask value of 0 (black) behaves as if it were 128 (middle gray).

**Related concepts:** Feather (the other mask property in the Properties panel), Opacity (layer-level opacity is a different control), mask painting (the alternative — painting with gray values directly on the mask).

**Common misconception:** "Density is the same as layer opacity." Density affects the mask; layer opacity affects the entire layer. Reducing layer opacity makes the layer uniformly more transparent everywhere. Reducing mask density makes the masked (hidden) areas less hidden while leaving the white (revealed) areas unchanged at full visibility.

---

### Feather (Mask Property)

**Definition:** A property in the Properties panel that applies a non-destructive blur to the mask edge, creating a gradual transition between hidden and revealed areas. Higher feather values produce softer, more gradual transitions.

**Meaning in photographic practice:** Hard mask edges are often visible in photographic edits as abrupt transitions between adjusted and unadjusted areas. Feathering softens these transitions to produce natural-looking blends. For example, a mask separating sky from foreground benefits from some feathering at the horizon line to avoid an obvious halo or hard line.

The Properties panel feather is non-destructive — you can increase, decrease, or remove it at any time without altering the underlying mask painting. This is a significant advantage over applying Gaussian Blur directly to the mask (which is destructive and permanent).

**Photoshop implementation:** Displayed in the Properties panel when a layer mask is selected. The slider goes from 0 px to 1000 px. The feathering is applied as a visual effect on the mask — the actual mask pixel data is not modified. This means the feather value can be adjusted back and forth without any quality loss.

**FACT:** The Properties panel feather is mathematically equivalent to a Gaussian blur applied to the mask, but it is computed non-destructively. Applying Filter > Blur > Gaussian Blur directly to the mask produces a similar visual result but permanently modifies the mask pixel data.

**Related concepts:** Gaussian blur (the destructive equivalent), Density (the other mask property), Select and Mask (provides feather controls during selection refinement), Refine Edge (predecessor to Select and Mask).

**Common misconception:** "Feather and blur are different effects." In the context of masks, feather IS a blur. The Properties panel Feather slider applies a Gaussian blur to the mask non-destructively. The term "feather" is inherited from the selection feathering feature (Select > Modify > Feather), which applies the blur at the moment of selection creation (destructively). The Properties panel version does the same thing non-destructively.

---

### Select and Mask (Refine Edge)

**Definition:** A dedicated workspace for refining mask and selection edges, particularly for complex subjects with fine detail such as hair, fur, foliage, and semi-transparent edges. Provides edge detection algorithms, brush-based refinement tools, and multiple output options.

**Meaning in photographic practice:** Accurate masking of complex edges is one of the most demanding tasks in photographic editing. Cutting a person from a background requires handling individual hair strands that no polygon-based selection tool can capture. Select and Mask provides edge-detection algorithms specifically designed for this problem — analyzing color transitions, frequency detail, and contrast to compute mask values for individual hair strands.

For compositing, sky replacement, background swaps, and subject isolation, Select and Mask is typically the final refinement step after an initial selection.

**Photoshop implementation:** Accessed via Select > Select and Mask, or by clicking the "Select and Mask" button in the Options Bar when a selection tool is active, or by selecting a layer mask and opening the workspace from the Properties panel. The workspace provides:

- **Refine Edge Brush:** Paints over areas with complex edges (hair, fur), telling the algorithm where to analyze edge detail.
- **Edge Detection Radius:** Controls how far from the initial edge the algorithm searches for detail.
- **Smart Radius:** Automatically varies the detection radius based on edge complexity.
- **Smooth, Feather, Contrast, Shift Edge:** Global edge refinement controls.
- **Decontaminate Colors:** Attempts to remove background color that has bled into semi-transparent edge pixels.
- **Output To:** Selection, Layer Mask, New Layer, New Layer with Layer Mask, New Document, New Document with Layer Mask.

**VERSION NOTE:** Select and Mask replaced the older Refine Edge dialog in Photoshop CC 2015.5. The core algorithms are similar, but the workspace interface is substantially different. Older tutorials referencing "Refine Edge" are describing the predecessor feature.

**Related concepts:** Refine Edge Brush (the key tool within the workspace), layer mask (the most common output target), alpha channel, Color Range (an alternative selection method for color-based masking).

**Common misconception:** "Select and Mask works well with a single click." Select and Mask is a refinement tool, not a one-click solution. It requires a reasonable initial selection to refine. The quality of the output depends heavily on the quality of the input selection, the care with which you use the Refine Edge Brush, and the characteristics of the image (high-contrast edges against a uniform background produce far better results than low-contrast edges against a busy background).


## Theory: How Masks Work Mathematically

### Masks as Opacity Multipliers

A mask is a grayscale image. Each pixel in the mask has a value from 0 to 255 (in 8-bit). This value acts as a multiplier that determines how much of the associated layer's content appears in the final composite at that pixel position.

**FACT:** The mask calculation for each pixel is:

```
output_opacity = layer_content_opacity * (mask_value / 255)
```

At each pixel position:

| Mask value | Fraction | Effect |
|---|---|---|
| 255 (white) | 255/255 = 1.0 | Layer fully visible at that pixel |
| 0 (black) | 0/255 = 0.0 | Layer fully hidden at that pixel |
| 128 (middle gray) | 128/255 = 0.502 | Layer approximately 50% visible |
| 191 (light gray) | 191/255 = 0.749 | Layer approximately 75% visible |
| 64 (dark gray) | 64/255 = 0.251 | Layer approximately 25% visible |

This is a linear multiplication in the mask's value space. The mask value is divided by 255 (normalizing to a 0.0-1.0 range) and then multiplied by the layer's pixel value or opacity.

### The Multiplication in Practice

Consider a Curves adjustment layer that brightens the image by one stop. Without a mask, the brightening applies to every pixel. With a layer mask:

- Where the mask is white (255): the full brightening applies.
- Where the mask is black (0): zero brightening applies; the image is unchanged.
- Where the mask is 128 (middle gray): approximately 50% of the brightening applies.

**FACT:** When a mask is applied to a pixel layer (rather than an adjustment layer), the mask directly controls pixel opacity. The per-pixel calculation is:

```
visible_pixel_alpha = pixel_alpha * (mask_value / 255)
```

Where `pixel_alpha` is the pixel's own transparency (fully opaque for most photographic content). The result is that pixels under black mask areas become transparent, revealing whatever is below them in the layer stack.

For adjustment layers, the mask does not make pixels transparent — instead, it controls the strength of the adjustment effect at each position. The adjustment is fully applied where the mask is white, not applied where the mask is black, and partially applied where the mask is gray.

### Feathering Is Gaussian Blur

When you feather a mask — whether via the Properties panel Feather slider, Select > Modify > Feather on a selection, or Filter > Blur > Gaussian Blur applied directly to the mask — you are applying a Gaussian blur to the grayscale mask image.

**FACT:** A Gaussian blur replaces each pixel's value with a weighted average of surrounding pixels, where the weights follow a Gaussian (bell curve) distribution. The radius parameter controls the spread of the bell curve: larger radius = wider blur = softer transition.

The visual effect on the mask: hard black-to-white transitions become gradual gradients. A mask with a sharp edge between black and white becomes a mask with a smooth gradient from black through gray to white. The width of this gradient is controlled by the blur radius (or feather value).

This is why feathered masks produce smooth blends — the transition zone contains gray values that produce partial visibility, creating a gradual crossfade between masked and unmasked areas.

### Density as Mask Compression

The Density control in the Properties panel compresses the mask's value range from the black end. At 100% density, the mask uses its full 0-255 range. At 50% density, the effective mask range is compressed to 128-255: a mask pixel painted at 0 (black) is remapped to behave as 128 (middle gray).

**FACT:** The density adjustment can be expressed as:

```
effective_mask_value = mask_value + (255 - mask_value) * (1 - density / 100)
```

At density 100%: `effective = mask_value` (no change).
At density 50%: a mask pixel of 0 (black) becomes `0 + (255 - 0) * 0.5 = 127.5`, approximately middle gray.
At density 0%: every mask pixel becomes 255 (white) — the mask has no effect.

The practical consequence: reducing density makes the mask "weaker." Black areas become gray, hiding less. White areas remain white — they are unaffected. This is useful for globally reducing the strength of a mask without repainting it.

### The Black/White/Gray Convention

**FACT:** Photoshop's mask convention is:

- **Black (0) = Hidden.** The layer is not visible at this position.
- **White (255) = Visible.** The layer is fully visible at this position.
- **Gray (1-254) = Partially visible.** The layer has proportional transparency.

This convention is consistent across layer masks, alpha channels (when loaded as masks), and Quick Mask mode. Memorizing "black hides, white reveals" is essential — every mask operation in Photoshop follows this rule.

**INTERPRETATION:** A common mnemonic is "black conceals, white reveals." This is accurate for layer masks. Note that when viewing an alpha channel as a saved selection (not as a layer mask), the convention can be inverted depending on Photoshop's display settings — white represents the selected area. The underlying math is the same; the display interpretation differs.

### Channel as Color Component vs. Channel as Stored Selection

The Channels panel contains two categories of channels, and understanding the distinction prevents confusion:

| Property | Color channel (R, G, B) | Alpha channel |
|---|---|---|
| Purpose | Stores color intensity data | Stores selection/mask/transparency data |
| Number per document | Fixed (3 for RGB, 4 for CMYK, 1 for Grayscale) | Variable (0 or more, user-created) |
| Effect on image appearance | Directly defines pixel color | No effect unless loaded as selection or attached as mask |
| Can be deleted? | No (removing a color channel changes the color mode) | Yes |
| Value meaning | Intensity of that color component | Selection/visibility level (255 = selected/visible) |

**FACT:** Both types of channels are grayscale images stored in the same data structure. The difference is semantic — how Photoshop interprets and uses the values. A color channel's value of 255 in the Red channel means maximum red intensity. An alpha channel's value of 255 means fully selected or fully visible.


## Photoshop Implementation

### The Channels Panel

The Channels panel (Window > Channels) displays all channels in the document:

| Panel element | Description |
|---|---|
| RGB (composite) | Not a real channel — displays the combined result of R+G+B. Clicking it shows the full-color image. |
| Red | The red color channel. |
| Green | The green color channel. |
| Blue | The blue color channel. |
| Alpha channels | Listed below the color channels. Named "Alpha 1" by default (renamable). |
| Layer mask channels | Appear when a layer with a mask is selected. Named by the layer name. |

**Viewing individual channels:** Click a single channel name to view it as a grayscale image. The other channels are hidden. Click the composite (RGB) to return to full color. You can view multiple channels simultaneously by Shift-clicking additional channel names — they display as tinted overlays in their respective colors.

**FACT:** When viewing a single channel, Photoshop displays it as a grayscale image by default. You can change this to display each channel in its actual color (red channel in red, etc.) via Edit > Preferences > Interface > Show Channels in Color. The grayscale view is more useful for evaluating tonal values; the color view is more intuitive but harder to judge tonally.

### Creating and Managing Alpha Channels

**Save a selection as an alpha channel:** With an active selection, choose Select > Save Selection. In the dialog, choose the destination document and channel (New or an existing alpha channel). Alternatively, click the "Save selection as channel" button at the bottom of the Channels panel.

**Load an alpha channel as a selection:** Ctrl-click (Cmd-click) the alpha channel's thumbnail in the Channels panel, or use Select > Load Selection and choose the channel.

**Edit an alpha channel directly:** Click the alpha channel in the Channels panel to select it for editing. Paint with white, black, or gray directly on the channel. This is useful for creating masks from scratch or refining saved selections.

**Delete an alpha channel:** Drag it to the trash icon in the Channels panel, or right-click > Delete Channel.

### Creating and Editing Layer Masks

**Add a white (reveal all) mask:** Select the layer, click the "Add layer mask" button at the bottom of the Layers panel.

**Add a black (hide all) mask:** Alt-click (Option-click) the "Add layer mask" button.

**Add a mask from a selection:** Make a selection first, then click the "Add layer mask" button. The selection becomes a white area on the mask; everything outside the selection becomes black.

**Edit the mask:** Click the mask thumbnail in the Layers panel to select it. A border appears around the thumbnail. Paint with black, white, or gray. Use soft brushes for gradual transitions, hard brushes for precise edges.

**View the mask as an overlay:** Press \ (backslash) to toggle the rubylith overlay, or Shift+Alt-click (Shift+Option-click) the mask thumbnail.

**View the mask as a grayscale image:** Alt-click (Option-click) the mask thumbnail. The canvas shows only the mask. Alt-click again to return to normal view.

**Disable/enable the mask:** Shift-click the mask thumbnail. A red X appears when disabled.

**Apply the mask (destructive):** Right-click the mask thumbnail > Apply Layer Mask. This permanently deletes the hidden pixels and removes the mask. **This is destructive and cannot be undone after saving.**

**Delete the mask without applying:** Right-click the mask thumbnail > Delete Layer Mask. The mask is removed, and the layer returns to full visibility.

### Mask Properties in the Properties Panel

When a layer mask is selected, the Properties panel displays two controls:

| Control | Range | Effect |
|---|---|---|
| Density | 0% - 100% | Reduces the maximum hiding strength of the mask. See Theory section. |
| Feather | 0 px - 1000 px | Applies non-destructive Gaussian blur to the mask edges. |

Additionally, the Properties panel provides buttons for:

- **Select and Mask:** Opens the Select and Mask workspace for edge refinement.
- **Color Range:** Opens the Color Range dialog to create a mask based on color similarity.
- **Invert:** Inverts the mask (swaps black and white).

**RECOMMENDATION:** Use the Properties panel Density and Feather controls rather than destructive editing (Levels on the mask, Gaussian Blur on the mask) whenever possible. They are non-destructive — you can adjust them at any time without degrading the mask.

### Creating and Using Vector Masks

**Add a vector mask with Reveal All:** Select the layer, then Layer > Vector Mask > Reveal All. The vector mask appears as a second thumbnail in the Layers panel.

**Add a vector mask from a path:** Draw a path with the Pen Tool, then Layer > Vector Mask > Current Path. The path becomes the vector mask boundary.

**Edit the vector mask:** Use the Path Selection Tool (A) or Direct Selection Tool (A, press Shift+A to toggle) to select and modify anchor points. Use the Pen Tool to add or remove anchor points.

**A layer can have both a layer mask and a vector mask simultaneously.** Both constraints apply — the layer is visible only where both masks allow visibility. This is useful for combining a precise geometric edge (vector mask) with a soft-edged or painted transition (layer mask).

### Creating Clipping Masks

**Create a clipping mask:** Alt-click (Option-click) the line between two layers in the Layers panel. The upper layer becomes clipped to the lower layer. Alternatively, select the upper layer and use Layer > Create Clipping Mask (Alt+Ctrl+G / Option+Cmd+G).

**Release a clipping mask:** Alt-click the line again, or Layer > Release Clipping Mask.

**Multiple clipped layers:** You can clip multiple consecutive layers to the same base layer. All clipped layers are constrained to the base layer's transparency.

### Quick Mask Mode

**Enter Quick Mask mode:** Press Q, or click the Quick Mask button in the Tools panel.

**Paint the mask:** Use any painting tool. By default, painting with black adds to the masked (unselected) area (red overlay appears), painting with white removes from the masked area.

**Exit Quick Mask mode:** Press Q again. The painted mask converts to a marching-ants selection.

**Change overlay settings:** Double-click the Quick Mask button in the Tools panel. You can change the overlay color, opacity, and whether the overlay indicates masked areas or selected areas.

**RECOMMENDATION:** Before entering Quick Mask mode, set your starting selection with an automated tool (Quick Selection, Select Subject, Magic Wand). Then enter Quick Mask mode to clean up edges and refine areas the automated tool missed. This combines the speed of automated selection with the precision of manual painting.

### Using Select and Mask

The typical workflow for complex edge refinement:

1. Make an initial selection (Quick Selection, Select Subject, or another method).
2. Open Select and Mask (Select > Select and Mask, or Options Bar button).
3. In the workspace, use the View mode dropdown to choose a preview mode (Onion Skin, Marching Ants, Overlay, On Black, On White, Black & White, On Layers). "On Black" and "On White" are useful for spotting edge problems.
4. Set the Edge Detection Radius. Enable Smart Radius for subjects with mixed edge types (hard body edges, soft hair edges).
5. Use the Refine Edge Brush to paint over areas with fine detail (hair, fur, foliage). The algorithm analyzes the painted area and computes refined mask values.
6. Adjust Global Refinements (Smooth, Feather, Contrast, Shift Edge) as needed.
7. Enable Decontaminate Colors if background color is bleeding into edge pixels.
8. Set Output To (typically "Layer Mask" or "New Layer with Layer Mask").
9. Click OK.

**FACT:** The Decontaminate Colors option changes pixel data — it replaces the color of edge pixels. This is a destructive operation. When enabled, the output options that preserve the original layer (plain Selection, Layer Mask) become unavailable. Photoshop forces output to a new layer to preserve the original.


## Professional Workflow

### Mask Strategy: When to Use Which Mask Type

| Situation | Recommended mask type | Rationale |
|---|---|---|
| Selective adjustment (brighten foreground, darken sky) | Layer mask | Paint or gradient to control where the adjustment applies |
| Restrict adjustment to one layer only | Clipping mask | Limits scope to the base layer without complex mask painting |
| Product cutout with clean geometric edges | Vector mask | Resolution-independent, mathematically precise edges |
| Complex edge (hair, fur, foliage) | Layer mask + Select and Mask | Raster mask required for soft/semi-transparent edges |
| Temporary selection refinement | Quick Mask mode | Paint-based refinement, converts back to selection |
| Preserve a complex selection for later reuse | Alpha channel (Save Selection) | Stored in the document, loadable at any time |
| Combine hard edges with soft transitions | Vector mask + layer mask on the same layer | Vector mask defines the hard boundary, layer mask adds gradients |

### Building Masks from Channels

One of the most powerful masking techniques for photography is deriving masks from the image's own channel data. Because individual channels are grayscale images with tonal variation, they can serve as starting points for masks.

**INTERPRETATION:** This technique is the foundation of luminosity masking and channel-based selection, covered in depth in Chapter 13. The principle here is simple: the image's own tonal data is often the best starting point for a mask. A sky is typically brighter than the foreground. The Blue channel often has the strongest sky/foreground separation. By loading the Blue channel as a selection and converting it to a mask, you get a natural-looking transition that follows the image's own tonal structure.

Basic workflow:

1. Inspect each channel (Red, Green, Blue) to find the one with the best tonal separation for your subject.
2. Ctrl-click (Cmd-click) the channel thumbnail to load it as a selection.
3. Add an adjustment layer — the selection automatically becomes the layer mask.
4. Refine the mask with Levels (Image > Adjustments > Levels, with the mask selected) to increase contrast between the areas you want masked and revealed.

### Mask Quality Indicators

A well-made mask has:

- **Clean interior areas:** Fully white where the effect should apply, fully black where it should not. Gray values only in the transition zone.
- **Appropriate edge softness:** Hard enough to avoid visible bleeding from the adjustment, soft enough to avoid a visible halo or hard line.
- **No artifacts:** No stray white or black specks in areas that should be uniform.

To evaluate mask quality, Alt-click (Option-click) the mask thumbnail to view it as a grayscale image. Noisy, patchy, or blotchy masks produce noisy, patchy results.

**RECOMMENDATION:** Always view your mask in grayscale at least once before considering it finished. The rubylith overlay is useful for positioning but inadequate for evaluating mask quality. The grayscale view reveals noise, artifacts, and insufficient edge refinement that are invisible in the overlay.

### Naming and Organizing Masks

Unlike layers, masks cannot be independently named in the Layers panel. The mask is identified by its association with its layer. This makes layer naming doubly important when masks are involved:

- "Curves - brighten foreground" tells you what the adjustment does and implies the mask hides the sky.
- "Curves 1" tells you nothing about the mask.

When storing standalone alpha channels, name them descriptively: "Sky selection - feathered," "Hair mask - refined," "Luminosity highlights."


## Common Mistakes

### 1. Painting on the Layer Instead of the Mask

**The mistake:** Intending to edit the mask but accidentally painting on the layer content because the wrong thumbnail is selected in the Layers panel.

**Why it matters:** You paint black onto your photograph instead of onto the mask. The pixel data is now damaged. If you do not notice immediately, the damage may not be apparent until you view the image at full resolution.

**The fix:** Always check which thumbnail has the selection border in the Layers panel before painting. The mask thumbnail shows a border around it when selected. Pressing \ to toggle the rubylith overlay confirms you are editing the mask. Build the habit of checking before every paint stroke on a mask.

### 2. Forgetting That Marching Ants Hide Partial Selections

**The mistake:** Making a feathered selection, observing the marching ants, and assuming the selection boundary is sharp because the ants show a crisp line.

**Why it matters:** Marching ants only display the 50% threshold. A feathered selection may extend significantly beyond the ants (at less than 50% selection strength) and significantly inside them (at less than 100% selection strength). Operations applied "inside" the selection will actually affect a broader area with feathered intensity.

**The fix:** When precision matters, work in Quick Mask mode or convert the selection to a layer mask and view the mask in grayscale. These show the actual selection values, not just the 50% contour.

### 3. Using a Hard Brush for Mask Painting on Photographic Content

**The mistake:** Painting a mask with a hard-edged (100% hardness) brush to hide an adjustment from a natural area (sky, landscape, skin).

**Why it matters:** Hard mask edges create visible transitions in the image — a sharp line between adjusted and unadjusted areas that looks unnatural. In a photograph, almost no natural boundary is perfectly sharp.

**The fix:** Use a soft brush (0-30% hardness) for most photographic mask painting. Reduce brush opacity (15-40%) for gradual buildup. Use hard brushes only for masks on geometric subjects with genuinely sharp edges (architecture, product photography).

### 4. Over-Relying on Select and Mask

**The mistake:** Attempting to produce a perfect mask in a single pass through Select and Mask, then accepting the result without manual cleanup.

**Why it matters:** Select and Mask is an excellent tool but not infallible. Complex backgrounds, similar colors between subject and background, and motion blur all degrade its accuracy. Accepting the output uncritically results in masks with visible artifacts: halos, missing hair strands, or fringing.

**The fix:** Treat Select and Mask as a starting point. After outputting to a layer mask, inspect the mask in grayscale (Alt-click the mask thumbnail). Clean up problem areas manually with a small brush. View the result against multiple backgrounds (the "On Black," "On White," and "On Layers" views in Select and Mask help with this).

### 5. Applying the Mask When It Could Remain Editable

**The mistake:** Right-clicking a layer mask and choosing "Apply Layer Mask" before the edit is finalized.

**Why it matters:** Applying a mask permanently deletes the hidden pixels. The mask is gone. If you later need to reveal more of the layer or adjust the mask edge, you must start the masking work over from scratch.

**The fix:** Leave masks unapplied in your working PSD. There is no performance or quality benefit to applying a mask prematurely. Apply only when explicitly required (certain operations demand it) and only after confirming the mask is finalized.

### 6. Confusing Clipping Masks with Layer Masks

**The mistake:** Using a clipping mask when a layer mask is needed, or vice versa. For example, attempting to clip an adjustment layer to a group (which clips to the group's composited transparency) when the intent was to mask the adjustment to a painted region.

**Why it matters:** The two mechanisms work differently. A clipping mask constrains to another layer's transparency; a layer mask constrains to a painted grayscale pattern. Choosing the wrong one produces unexpected results and wastes time troubleshooting.

**The fix:** Ask yourself: "Am I restricting based on another layer's shape/transparency?" If yes, use a clipping mask. "Am I restricting based on a spatial region I want to define?" If yes, use a layer mask.

### 7. Not Saving Complex Selections as Alpha Channels

**The mistake:** Spending significant time building a complex selection (hair masking, luminosity selection, color-range-based selection), then deselecting without saving it.

**Why it matters:** The selection is lost permanently. Recreating it requires repeating all the work.

**The fix:** Before deselecting any complex selection, save it: Select > Save Selection. The cost is one additional alpha channel. The benefit is permanent preservation of potentially hours of masking work.


## Summary

- **Channels** serve two roles: color channels (Red, Green, Blue) store the image's color data; alpha channels store selections, masks, and transparency information. Both are grayscale images in the same panel.
- A **mask** is a grayscale image that controls visibility. White reveals, black hides, gray partially reveals. This is the single convention underlying all masking in Photoshop.
- **Mathematically**, a mask multiplies the layer's opacity at each pixel: `output = content * (mask_value / 255)`. Feathering is Gaussian blur applied to the mask. Density compresses the mask's value range.
- A **layer mask** is a raster (pixel-based) mask attached to a layer. It is the primary tool for selective photographic adjustments.
- A **vector mask** is a path-based mask that produces resolution-independent, geometrically precise edges. It cannot represent soft transitions.
- A **clipping mask** is a relationship between layers where the base layer's transparency constrains the layers above it. It is fundamentally different from a layer mask.
- **Quick Mask mode** is a temporary visualization that lets you paint a selection as if it were a mask, then converts the result back to a selection.
- A **selection** is temporary (lost on deselect). A **mask** or **alpha channel** is persistent (stored in the document). Professional editing converts selections to masks.
- The **Properties panel** provides non-destructive Density and Feather controls for layer masks. Use these rather than destructive alternatives.
- **Select and Mask** refines complex edges (hair, fur, foliage) but is a starting point, not a final solution. Always inspect and clean up the result.
- Individual color channels can serve as starting points for masks, leveraging the image's own tonal structure for natural-looking selections.


## Exercises

### Exercise 1: Channel Inspection

Open a color photograph (landscape or portrait) in Photoshop.

1. Open the Channels panel (Window > Channels).
2. Click each color channel individually (Red, Green, Blue) and observe the grayscale representation.
3. For each channel, identify: Which areas of the image are brightest? Which are darkest? Which channel has the most contrast between sky and foreground?
4. Note which channel contains the most visible noise (typically Blue).
5. Return to the RGB composite view.

The goal: develop the habit of inspecting channels as diagnostic information, not just decorative data.

### Exercise 2: Layer Mask Fundamentals

Open a photograph with distinct sky and foreground.

1. Add a Curves adjustment layer. Pull the midtone down to darken the image noticeably.
2. The entire image is darker. Now select the mask thumbnail (click it in the Layers panel).
3. Select the Gradient Tool. Set it to black-to-white, linear gradient.
4. Drag a gradient from the top of the image to the horizon line. The sky darkens; the foreground brightens back to normal.
5. Alt-click the mask thumbnail to view the mask. You should see a smooth black-to-white gradient.
6. Experiment with the Properties panel: reduce Density to 50%. Observe how the darkening effect weakens. Return to 100%.
7. Increase Feather to 50px. Observe the softened transition. Return to 0px.

### Exercise 3: Selection to Mask to Alpha Channel

1. Open a photograph. Use the Quick Selection Tool (W) to select a subject.
2. Click the "Add layer mask" button to convert the selection to a layer mask on the current layer.
3. Alt-click the mask to view it as grayscale. Evaluate the edge quality.
4. Ctrl-click (Cmd-click) the mask thumbnail to reload it as a selection.
5. Save the selection: Select > Save Selection. Name it "Subject mask."
6. Open the Channels panel. Observe the new alpha channel at the bottom.
7. Deselect (Ctrl+D / Cmd+D). The selection is gone, but the alpha channel preserves it.
8. Ctrl-click the alpha channel thumbnail to reload the selection at any time.

### Exercise 4: Black Hides, White Reveals

1. Open a photograph. Duplicate the Background layer (Ctrl+J / Cmd+J).
2. Apply a strong filter to the duplicate (Filter > Blur > Gaussian Blur, Radius 20px) so the effect is visually obvious.
3. Add a black (hide all) layer mask: Alt-click the "Add layer mask" button. The blur disappears entirely — the mask hides everything.
4. Select a soft white brush at 100% opacity. Paint on the mask over the subject's face. The blur appears only where you paint.
5. Switch to black and paint over part of the revealed area. The blur disappears there.
6. Reduce brush opacity to 30% and paint with white on an unblurred area. The blur appears at reduced strength — partial transparency.
7. Alt-click the mask to view it. Correlate the white, black, and gray areas with what you see in the composite.

---

### Advanced Exercises

### Advanced Exercise 1: Combining Layer Mask and Vector Mask

1. Open a photograph of a building or architectural subject.
2. With the Pen Tool, draw a precise path around a window or door frame.
3. Add the path as a vector mask: Layer > Vector Mask > Current Path. Everything outside the path is hidden.
4. Now add a layer mask to the same layer (click the "Add layer mask" button). The layer now has both a vector mask and a layer mask.
5. On the layer mask, paint a black-to-white gradient. The layer is now clipped to the vector path AND faded by the gradient.
6. Observe: both masks constrain visibility simultaneously. The vector mask defines a hard geometric boundary; the layer mask adds a soft transition within that boundary.

### Advanced Exercise 2: Building a Mask from a Channel

1. Open a landscape photograph with a well-defined sky/foreground boundary.
2. In the Channels panel, identify which channel has the strongest contrast between sky and foreground (usually Blue or Red).
3. Duplicate that channel: drag it to the "Create new channel" button at the bottom of the Channels panel.
4. With the duplicated channel selected, use Image > Adjustments > Levels to increase contrast: drag the black point right and the white point left until the sky is nearly white and the foreground is nearly black (or vice versa).
5. Clean up remaining gray areas with a brush: paint pure white on the sky, pure black on the foreground.
6. Ctrl-click (Cmd-click) the channel to load it as a selection.
7. Click the RGB composite to return to the full-color view.
8. Add a Curves adjustment layer. The selection becomes the layer mask.
9. Adjust the curve to darken or color-shift only the sky. The mask follows the natural tonal boundary of the image.

### Advanced Exercise 3: Quick Mask Refinement Workflow

1. Open a portrait photograph. Use Select > Subject to create an initial selection.
2. Press Q to enter Quick Mask mode. Observe the red overlay on the unselected (masked) areas.
3. Zoom in to the edges. Use a small, hard brush with black to mask areas that should not be selected (background areas the automated tool included).
4. Switch to white to reveal areas that should be selected (parts of the subject the tool missed).
5. Along the hair line, use a soft brush at reduced opacity (30-50%) to create semi-transparent mask transitions.
6. Press Q to exit Quick Mask mode. The refined result becomes a selection.
7. Add an adjustment layer — the selection becomes the layer mask. Evaluate the mask quality by Alt-clicking its thumbnail.

---

### Blackbelt Challenge

Open a portrait photograph with complex hair against a non-uniform background. Your task: isolate the subject completely, replace the background, and apply a tonal adjustment to the subject only, with an invisible transition at the edges.

1. Assess the image. Examine the channels to identify which has the best edge contrast for the hair region.
2. Make an initial selection using the method you judge most appropriate.
3. Refine the selection using Select and Mask, paying particular attention to fine hair detail.
4. Output to a layer mask on the subject layer.
5. Inspect the mask in grayscale. Clean up any artifacts, halos, or missed areas manually.
6. Add a new background layer below the subject. The cutout should look convincing at 100% zoom.
7. Add a Curves adjustment layer clipped to the subject layer. Adjust the tonal range of the subject without affecting the new background.
8. View the composite at 100% zoom and evaluate: Is the hair edge natural? Is there visible fringing or halo? Does the tonal adjustment transition smoothly?

The test: zoom to 200% on the hair boundary. If you cannot identify where the mask edge is — if the transition looks photographic rather than digital — you have produced a professional-quality mask. If you see a visible edge, halo, or color fringe, identify the cause (mask too hard, mask too soft, color contamination, incorrect edge refinement) and correct it.
