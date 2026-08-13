---
description: Local tonal control in Photoshop — dodging and burning techniques, local contrast enhancement, luminosity-based local adjustments, guiding the viewer's eye through selective tonal manipulation.
---

# Chapter 08: Local Tonal Control — Dodging, Burning, and Local Contrast

## Learning Objectives

After completing this chapter, you will be able to:

1. Explain the distinction between global tonal adjustments (Chapter 07) and local tonal adjustments, and describe why both are necessary for complete tonal control.
2. Describe the photographic darkroom origins of dodging and burning, and explain how the digital equivalents preserve the concept while removing the limitations.
3. Evaluate the three primary digital dodging and burning methods — the Dodge/Burn tools, the 50% gray layer technique, and the Curves-with-mask technique — and choose the appropriate method based on the editing context.
4. Construct a non-destructive dodging and burning setup using a 50% gray layer in Soft Light or Overlay blend mode, and explain why neutral gray is invisible in these blend modes.
5. Construct a non-destructive dodging and burning setup using paired Curves Adjustment Layers with painted masks, and explain why this approach offers the most control.
6. Distinguish between local contrast enhancement and sharpening, and explain how both relate to spatial frequency but operate at different scales.
7. Apply local contrast enhancement using the High Pass filter, Clarity-style techniques, and Curves-based approaches.
8. Explain the concept of luminosity masks at an introductory level and describe how they enable tonal-range-targeted local adjustments.
9. Use the Blend If sliders to restrict a local adjustment to a specific tonal range without painting a mask.
10. Apply dodging and burning deliberately to guide the viewer's eye through a photograph.

---

## Conceptual Foundation

Chapter 07 established the tools for global tonal control: Levels, Curves, Brightness/Contrast. These tools apply the same transfer function to every pixel in the image (or to every pixel within a selection). A Curves S-curve that increases midtone contrast does so uniformly — a midtone pixel in the sky receives exactly the same remapping as a midtone pixel in the foreground.

Global adjustments set the overall tonal character of the image. They are necessary but insufficient. A photograph is not a uniform field — it has spatial structure, areas of different importance, regions that need different treatment. The sky may need darkening while the foreground needs brightening. A face may need subtle lightening to draw attention, while the background needs to recede into shadow. A rock face may need local contrast to reveal texture, while the smooth water beside it should remain soft.

This is the domain of local tonal control: adjustments that vary across the image, affecting different areas differently. Local adjustments are the bridge between the technical correction (getting the tones right) and the creative interpretation (making the photograph say what you intend).

The concept is not new. In the photographic darkroom, every master printer practiced local tonal control through dodging (holding back light to lighten an area) and burning (adding extra exposure to darken an area). Ansel Adams famously spent hours on a single print, shaping light and shadow with his hands, cardboard cutouts, and wire-mounted shapes to guide the viewer's eye through the landscape. The global exposure set the foundation; the local manipulation created the art.

Photoshop offers the same creative control with incomparably greater precision. You can dodge and burn with pixel-level accuracy, with fully reversible edits, with the ability to modify any adjustment at any time. But the creative principle is the same: you are shaping light and shadow to control how the viewer perceives the photograph.

This chapter covers three categories of local tonal control:

1. **Dodging and burning** — selectively lightening and darkening areas to correct exposure variations, guide the viewer's eye, and create three-dimensionality.
2. **Local contrast enhancement** — increasing tonal variation within localized regions to enhance perceived detail, texture, and depth.
3. **Luminosity-based local adjustments** — using the image's own tonal structure to target adjustments to specific brightness ranges.

---

## Terminology

### Dodging (Lighten Locally)

**Definition:** Selectively lightening a specific area of an image while leaving surrounding areas unchanged. The term derives from the darkroom practice of "dodging" — holding an opaque object (a card, a hand, a wire-mounted disk) between the enlarger lens and the paper during exposure to block light from reaching a specific area, resulting in less exposure and therefore a lighter tone in that region.

**Meaning in photographic practice:** Dodging is used to brighten areas that are too dark — shadow regions that have lost detail, faces that are underlit, foreground elements that need more visual weight. Beyond correction, dodging is a compositional tool: lightening an area draws the viewer's eye toward it, because human vision is attracted to brighter regions. A subtle dodge on a subject's face against a darker background creates natural visual emphasis without any obvious manipulation.

**Photoshop implementation:** Three primary methods exist, each with different characteristics (detailed in the Implementation section):

1. **Dodge Tool (O):** A brush-based tool that directly lightens pixels on the active layer. Operates destructively on pixel data.
2. **50% gray layer technique:** A new layer filled with 50% gray, set to Soft Light or Overlay blend mode. Painting with white (or light gray) on this layer lightens the composite. Non-destructive — the gray layer can be edited at any time.
3. **Curves-based dodging:** A Curves Adjustment Layer with a brightening curve, paired with a black (hide all) layer mask. Painting white on the mask reveals the brightening effect locally. Fully non-destructive with independent control over the amount and location of the dodge.

**Related concepts:** Burning (the inverse operation), exposure, blend modes (Soft Light, Overlay — Chapter 14), layer mask (Chapter 05), Curves Adjustment Layer (Chapter 07).

**Common misconception:** "Dodging just means making an area brighter." Dodging lightens, but the purpose is usually not brightness itself — it is the relative brightness between the dodged area and its surroundings. Dodging increases local tonal contrast between the lightened area and its neighbors. The perceptual effect depends on context: dodging a face surrounded by shadow creates emphasis; dodging an already-bright sky area may just clip highlights. Effective dodging is always about relationships, not absolute values.

---

### Burning (Darken Locally)

**Definition:** Selectively darkening a specific area of an image while leaving surrounding areas unchanged. The term derives from the darkroom practice of "burning in" — using a card with a hole cut in it (or cupped hands) to give additional exposure to a specific area, resulting in a darker tone.

**Meaning in photographic practice:** Burning darkens areas that are too bright or that need to recede visually. Burning the edges of a frame (vignetting) draws the eye toward the center. Burning a bright distraction at the edge of the composition reduces its visual pull. Burning a sky adds drama and density. Like dodging, burning is both a corrective tool (fixing exposure problems) and a creative tool (directing attention).

**Photoshop implementation:** The same three methods used for dodging, but in reverse:

1. **Burn Tool (O, Shift+O to cycle):** A brush-based tool that directly darkens pixels. Destructive.
2. **50% gray layer technique:** Painting with black (or dark gray) on the 50% gray Soft Light/Overlay layer darkens the composite.
3. **Curves-based burning:** A Curves Adjustment Layer with a darkening curve, paired with a black mask. Painting white on the mask reveals the darkening effect locally.

**Related concepts:** Dodging (the inverse operation), vignette, edge burning, blend modes, layer mask.

**Common misconception:** "Burning is the same as reducing the Exposure slider." Reducing global exposure darkens everything uniformly. Burning is spatially selective — it darkens only where you apply it. This selectivity is the entire point: you darken the edges while keeping the center bright, or darken the sky while keeping the foreground luminous. A global exposure reduction cannot achieve this.

---

### Local Contrast / Micro-Contrast

**Definition:** The tonal differences between adjacent or nearby pixels within a localized region of the image, as distinct from global contrast (the overall tonal range from the darkest to the brightest values in the entire image). Local contrast is sometimes called micro-contrast or detail contrast, though "micro-contrast" is also used in lens optics with a slightly different meaning.

**Meaning in photographic practice:** An image can have full global contrast (histogram spanning 0 to 255) but appear flat and lifeless because the tonal variation within localized regions is compressed. A stone wall may have values ranging from 100 to 140 — technically within the midtone range, but with weak differentiation between the stone texture details. Increasing local contrast means increasing the tonal separation within that wall, making the texture visible and three-dimensional, without necessarily changing the overall position of those tones in the global histogram.

Local contrast is what makes an image look "detailed," "crisp," or "three-dimensional." It is the quality that the Clarity slider in Camera Raw/Lightroom targets. It is distinct from both global contrast (which sets the overall tonal range) and sharpening (which enhances the finest pixel-to-pixel transitions — Chapter 15).

**Photoshop implementation:** Local contrast can be enhanced through several techniques: the High Pass filter combined with Soft Light or Overlay blend mode, Unsharp Mask at large radius with low amount, Curves Adjustment Layers restricted to localized regions via masks, and the Camera Raw filter's Clarity and Texture sliders when applied through a Smart Object. Each technique operates at a different spatial scale and produces different visual characteristics (detailed in the Theory and Implementation sections).

**Related concepts:** Global contrast (Chapter 07), sharpening (Chapter 15), spatial frequency, Clarity, Texture, High Pass filter, Unsharp Mask.

**Common misconception:** "Local contrast and sharpening are the same thing." They are related — both involve increasing tonal differences between neighboring pixels — but they operate at different spatial scales. Sharpening targets the finest detail (pixel-to-pixel transitions, edge halos at 1-3 pixel widths). Local contrast enhancement targets broader structures (tonal variation at scales of 20-100+ pixels). A sharpened image has crisp edges. A locally contrast-enhanced image has three-dimensional, textured surfaces. An image can be well-sharpened but lack local contrast, or have excellent local contrast but soft edges. The two operations complement each other and are applied at different stages of the workflow.

---

### Global Adjustment vs Local Adjustment

**Definition:** A **global adjustment** applies the same operation uniformly to every pixel in the image (or every pixel in a selected channel). A **local adjustment** applies different amounts of change to different spatial locations — the adjustment varies across the image area.

**Meaning in photographic practice:** The distinction is fundamental to photographic editing. Global adjustments set the overall tonal and color character: the white balance, the overall contrast, the general brightness. Local adjustments refine the image spatially: brighten this face, darken that sky, add contrast to this texture, soften that background. A professional photographic edit typically involves both: global corrections first (Chapter 07), followed by local refinements (this chapter).

**Photoshop implementation:** A Curves Adjustment Layer without a mask (or with a fully white mask) is a global adjustment — every pixel receives the same remapping. The same Curves Adjustment Layer with a painted mask becomes a local adjustment — the mask controls where and how strongly the adjustment applies. A Curves layer with a gradient mask transitions smoothly from global effect to no effect across the image. Any adjustment layer becomes local when its mask is not uniformly white.

**Related concepts:** Curves (Chapter 07), layer mask (Chapter 05), dodging and burning, luminosity mask (Chapter 13).

**Common misconception:** "A Curves S-curve adds local contrast." A Curves S-curve on the master curve is a global adjustment. It increases midtone contrast for every midtone pixel identically, regardless of what surrounds that pixel. Two pixels with the same value — one in a detailed rock face, one in a smooth sky — receive the same remapping. Local contrast enhancement, by definition, adjusts pixels differently based on their spatial context. The distinction was introduced in Chapter 07 and is developed fully in this chapter.

---

### 50% Gray Layer Technique

**Definition:** A non-destructive dodging and burning method that uses a layer filled with 50% gray (RGB 128, 128, 128) set to Soft Light or Overlay blend mode. Because both Soft Light and Overlay render 50% gray as invisible (neutral), the layer has no visual effect in its default state. Painting lighter values (white or light gray) on this layer lightens the composite; painting darker values (black or dark gray) darkens it.

**Meaning in photographic practice:** The 50% gray layer technique is the most widely used non-destructive dodging and burning method. It provides a visual record of all dodging and burning work — viewing the gray layer in Normal blend mode reveals exactly what was painted and where. The work is fully editable: individual brush strokes can be erased, the layer opacity can be reduced to weaken the entire effect, and the layer can be duplicated or deleted without affecting the original image data.

**Photoshop implementation:** Create a new layer (Layer > New Layer). In the New Layer dialog, set the blend mode to Soft Light (or Overlay), and check "Fill with Soft-Light-neutral color (50% gray)." Alternatively, create the layer, fill it with 50% gray (Edit > Fill > 50% Gray), and set the blend mode manually.

To dodge: paint with a soft white brush at low opacity (5-15%).
To burn: paint with a soft black brush at low opacity (5-15%).
Build up the effect gradually with multiple strokes rather than using high opacity.

**FACT:** 50% gray is neutral in both Soft Light and Overlay blend modes. The mathematical reason: in Soft Light mode, a blend value of 128 (50%) produces an output equal to the base value — no change. In Overlay mode, 128 is the threshold between the Screen and Multiply operations, and at exactly 128, neither operation has any effect. Any value other than 128 on the blend layer produces either lightening (above 128) or darkening (below 128).

**Related concepts:** Soft Light blend mode (Chapter 14), Overlay blend mode (Chapter 14), Curves-based dodging and burning, layer opacity.

**Common misconception:** "Soft Light and Overlay produce the same result for dodging and burning." They do not. Overlay produces a stronger, more contrasty effect than Soft Light for the same paint value. Overlay is a harder blend mode — it drives values toward the extremes more aggressively. Soft Light is gentler and tends to produce more natural-looking results for subtle photographic dodging and burning. For most photographic work, Soft Light is the better choice. Overlay is useful when you want a more pronounced effect or are working on local contrast enhancement.

---

### Curves-Based Dodging and Burning

**Definition:** A dodging and burning technique that uses paired Curves Adjustment Layers — one set to brighten (dodge), one set to darken (burn) — each with a black (hide all) layer mask. The dodging or burning effect is revealed locally by painting white on the respective mask.

**Meaning in photographic practice:** This technique provides the most control of any dodging and burning method. The Curves adjustment controls the character of the lightening or darkening (you can target specific tonal regions, control the curve shape, adjust the amount at any time). The mask controls the spatial application (where the effect appears). And because the Curves layer and the mask are independent, you can change either without affecting the other — adjust the dodge intensity without repainting, or refine the spatial application without changing the tonal character.

**Photoshop implementation:** Create two Curves Adjustment Layers:

1. **Dodge layer:** Name it "Dodge." Raise the midpoint of the curve (approximately input 128 to output 160-170) to create a brightening effect. Invert the mask to black (Ctrl+I / Cmd+I on the mask, or Alt-click the mask button when creating the layer). The entire brightening effect is now hidden.
2. **Burn layer:** Name it "Burn." Lower the midpoint of the curve (approximately input 128 to output 90-100) to create a darkening effect. Invert the mask to black. The entire darkening effect is hidden.

To dodge an area: select the Dodge layer's mask, paint with a soft white brush at low opacity (10-20%). The brightening effect is revealed where you paint.
To burn an area: select the Burn layer's mask, paint with a soft white brush at low opacity. The darkening effect is revealed where you paint.

**Related concepts:** Curves Adjustment Layer (Chapter 07), layer mask (Chapter 05), 50% gray layer technique, luminosity mask (Chapter 13).

**Common misconception:** "Curves-based dodging and burning is just a complicated version of the gray layer technique." The visual results can appear similar, but the underlying mechanisms differ significantly. The gray layer technique works through blend mode math (Soft Light or Overlay), which applies a fixed mathematical relationship between the blend layer value and the base layer value. Curves-based dodging applies a transfer function that you define with complete control over the curve shape. With Curves, you can dodge the shadows without blowing the highlights (by anchoring the upper portion of the curve). You can dodge only the midtones (by constraining the curve shape). You can change the amount of dodge after painting by adjusting the curve — something impossible with the gray layer technique without repainting.

---

### Luminosity Mask

**Definition:** A mask derived from the image's own luminosity (brightness) data, which restricts an adjustment to specific tonal ranges. A highlights luminosity mask is bright where the image is bright and dark where the image is dark, so an adjustment applied through this mask affects highlights strongly and shadows minimally (or not at all). Shadow and midtone luminosity masks provide corresponding targeting.

**Meaning in photographic practice:** Luminosity masks allow you to target adjustments by tonal range without manual painting. If you want to dodge only the shadow areas in a landscape — brightening the dark foreground while leaving the bright sky untouched — a shadows luminosity mask achieves this automatically, with transitions that follow the image's own tonal structure. The resulting adjustment blends naturally because the mask is derived from the photograph itself.

Luminosity masks are among the most powerful tools in photographic post-processing. They are introduced conceptually here because they intersect with local tonal control. The full theory and construction methods are covered in Chapter 13.

**Photoshop implementation:** The simplest luminosity mask is created by Ctrl-clicking (Cmd-clicking) the RGB composite thumbnail in the Channels panel. This loads the image's luminosity as a selection — bright areas are strongly selected, dark areas are weakly selected. Adding a Curves Adjustment Layer while this selection is active creates a Curves layer whose mask is the luminosity map: adjustments affect highlights fully and shadows minimally.

Inverting this mask (Ctrl+I / Cmd+I) produces a shadows mask. Intersecting masks (loading one selection, then Ctrl+Alt+Shift-clicking / Cmd+Option+Shift-clicking another) produces narrower tonal ranges — midtones, narrow highlights, narrow shadows. Chapter 13 covers these operations in detail.

**Related concepts:** Alpha channel (Chapter 05), layer mask (Chapter 05), Curves Adjustment Layer (Chapter 07), channel-based selection (Chapter 13), Blend If sliders.

**Common misconception:** "Luminosity masks require third-party plugins." While plugins and panel scripts (such as Tony Kuyper's TK Actions or Lumenzia) automate luminosity mask creation and make it more efficient, the underlying technique uses only native Photoshop features: loading channels as selections, intersecting selections, and applying them as masks. No plugin is required. Plugins add convenience and speed but not capability.

---

### Blend If Sliders

**Definition:** Controls in the Layer Style dialog (accessed by double-clicking to the right of the layer name in the Layers panel) that restrict a layer's visibility based on the tonal values of the current layer ("This Layer" slider) or the underlying composite ("Underlying Layer" slider). By adjusting these sliders, you can make a layer or adjustment affect only specific tonal ranges without painting a mask.

**Meaning in photographic practice:** Blend If sliders provide a fast, non-destructive way to restrict any adjustment to a tonal range. If you create a Curves Adjustment Layer to brighten the image but want the brightening to apply only to the shadows, you can use the "Underlying Layer" slider to exclude highlights and midtones. The transition between affected and unaffected areas follows the tonal structure of the image automatically.

The key advantage of Blend If over luminosity masks is speed — it requires no mask construction. The key limitation is that it provides only tonal targeting, not spatial targeting. Blend If affects all pixels in a tonal range regardless of where they are in the image. For purely tonal targeting, Blend If is faster. For spatial control (dodge this shadow area but not that one), you still need a painted mask.

**Photoshop implementation:** Double-click to the right of the layer name (not on the name itself) to open the Layer Style dialog. The "Blend If" section at the bottom contains two gradient bars:

- **This Layer:** Restricts visibility based on the values on the current layer.
- **Underlying Layer:** Restricts visibility based on the values of the composite below.

Drag the sliders to exclude tonal ranges. **FACT:** Holding Alt/Option while dragging splits the slider into two halves, creating a smooth transition between affected and unaffected areas. Without splitting, the transition is abrupt and produces visible artifacts. Always split the sliders for photographic work.

The "Blend If" dropdown lets you apply the restriction to the Gray (luminosity) composite or to individual channels (Red, Green, Blue).

**Related concepts:** Luminosity mask (Chapter 13), layer mask (Chapter 05), Layer Style dialog, blend modes (Chapter 14).

**Common misconception:** "Blend If is just a simpler version of luminosity masks." Blend If and luminosity masks both target tonal ranges, but they work differently. Blend If is a display-time calculation — it does not create a mask in the traditional sense, and it responds dynamically if the underlying image changes (useful in some workflows, problematic in others). Luminosity masks are static — they capture the tonal state at the time of creation and do not change if the image changes. Blend If cannot be combined with spatial painting (you cannot say "Blend If shadows, but only in the left half of the image" without adding a separate layer mask). Luminosity masks, being actual masks, can be painted, feathered, and combined freely.

---

### Dodge Tool / Burn Tool (Photoshop Tools)

**Definition:** Built-in Photoshop painting tools that lighten (Dodge Tool) or darken (Burn Tool) pixels directly on the active layer. Accessible via the keyboard shortcut O (press Shift+O to cycle between Dodge, Burn, and Sponge tools). The Options Bar provides Range (Shadows, Midtones, Highlights) and Exposure (strength) controls.

**Meaning in photographic practice:** The Dodge and Burn tools are the most direct digital equivalents of darkroom dodging and burning. You paint on the image, and the area lightens or darkens. The Range setting targets the effect to a specific tonal region (a darkroom printer's hands could not do this). The Exposure setting controls the strength of each stroke.

Despite being intuitive and direct, these tools have significant limitations that make them unsuitable for professional photographic workflow.

**Photoshop implementation:** Select the Dodge Tool (O) or Burn Tool (Shift+O). In the Options Bar:

- **Range:** Shadows, Midtones, or Highlights — restricts the effect to the selected tonal range.
- **Exposure:** 1-100% — controls the strength of each brush stroke.
- **Protect Tones:** When checked, attempts to prevent hue shifts and tonal clipping during dodging/burning.

**FACT:** The Dodge and Burn tools operate destructively — they modify pixel data directly on the active layer. There is no adjustment layer, no mask, no non-destructive parameter to change later. Once applied and the history state is lost, the modification is permanent. The "Protect Tones" option (available since Photoshop CS4) reduces but does not eliminate color shifts and clipping artifacts.

**Related concepts:** 50% gray layer technique (non-destructive alternative), Curves-based dodging and burning (non-destructive alternative with more control), Sponge Tool (saturate/desaturate — grouped with Dodge and Burn).

**Common misconception:** "The Dodge and Burn tools are the standard way to dodge and burn in Photoshop." They are the most obvious way, but not the professional standard. The destructive nature of these tools — no undo beyond history, no ability to modify the effect after application, no separation of the adjustment from the image data — makes them incompatible with a non-destructive workflow. The 50% gray layer technique and the Curves-based technique provide equivalent or superior results with full reversibility. Professional photographic editors almost universally use one of the non-destructive methods.

---

## Theory

### How Local Tonal Control Guides the Viewer's Eye

Human visual perception is drawn to contrast, brightness, and detail. Within a photograph, the viewer's eye naturally gravitates toward the brightest area, the area of highest contrast, and the area of sharpest detail. This is not a cultural convention — it is a physiological response rooted in how the visual cortex processes spatial information.

This perceptual tendency is the foundation of local tonal control as a compositional tool:

- **Brightening an area (dodging) draws the eye toward it.** A face lightened slightly against a darker background becomes the natural focal point.
- **Darkening an area (burning) pushes the eye away from it.** Burned edges guide the viewer toward the center of the frame.
- **Increasing local contrast in an area emphasizes it.** A textured surface with enhanced local contrast appears more detailed and commands more visual attention than a smooth, low-contrast region.
- **Decreasing local contrast reduces visual importance.** A background with reduced local contrast recedes, keeping the viewer's focus on the subject.

**INTERPRETATION:** Ansel Adams's printing practice was fundamentally about this principle. His famous "Moonrise, Hernandez, New Mexico" involved extensive burning of the sky and foreground to direct attention to the luminous crosses and buildings. The negative captured the scene; the printing shaped the viewer's experience. Digital dodging and burning serves the same purpose with greater precision.

### The Darkroom Heritage

Understanding the darkroom origins clarifies the terminology and the intent:

**Dodging in the darkroom:** During exposure under the enlarger, the printer holds an opaque object (typically a piece of cardboard on a wire, or a hand) between the lens and the paper. The blocked area receives less light and therefore prints lighter. The printer moves the dodging tool constantly during the exposure to avoid a visible edge — this constant motion creates a soft transition between the dodged area and its surroundings.

**Burning in the darkroom:** After the base exposure, the printer gives additional exposure to specific areas using a card with a hole in it, or cupped hands. The burned area receives more light and prints darker. Again, the tool is kept in constant motion to feather the edges.

**FACT:** The naming convention is counterintuitive until you understand the print process. Dodging means "holding back light from the paper," which means less exposure, which means a lighter print. Burning means "giving more light to the paper," which means more exposure, which means a darker print. The terms describe what happens to the light, not what happens to the tone.

In the digital domain, the principle is identical but the mechanism is different. There is no enlarger, no paper, no physical blocking of light. Instead, you modify pixel values or adjustment parameters selectively across the image area. The creative intent — shaping the tonal distribution to guide the viewer — is unchanged.

### 50% Gray + Soft Light/Overlay: Why It Works

The 50% gray layer technique depends on a specific mathematical property of the Soft Light and Overlay blend modes: both render 50% gray (value 128 in 8-bit) as completely neutral — no change to the underlying image.

**FACT:** The Overlay blend mode is defined as:

- If the base value (underlying pixel) is less than 128: `result = 2 * base * blend / 255`
- If the base value is 128 or greater: `result = 255 - 2 * (255 - base) * (255 - blend) / 255`

When the blend value is exactly 128 (50% gray):

- For dark bases: `result = 2 * base * 128 / 255 = base * 256 / 255`, which rounds to the base value.
- For bright bases: the calculation similarly produces a result approximately equal to the base value.

In both cases, 50% gray produces no visible change. Values below 128 on the blend layer darken the result (like Multiply). Values above 128 lighten the result (like Screen). The further from 128, the stronger the effect.

**FACT:** Soft Light uses a different formula that produces a gentler curve. The precise formula varies between implementations, but the key property is the same: a blend value of 128 produces no change. Values above 128 lighten; values below 128 darken. The effect is softer (lower contrast) than Overlay at all equivalent blend values.

This is why the technique works for dodging and burning:

1. Fill a layer with 50% gray: invisible in Soft Light or Overlay.
2. Paint white (255): maximum lightening — a dodge.
3. Paint light gray (e.g., 170): moderate lightening — a subtle dodge.
4. Paint black (0): maximum darkening — a burn.
5. Paint dark gray (e.g., 80): moderate darkening — a subtle burn.
6. Paint 50% gray (128): erase the dodge/burn — return to no effect.

The practical advantage: you can see all your dodging and burning work by viewing the gray layer in Normal blend mode. White and light areas are dodges. Black and dark areas are burns. The gray baseline is untouched image.

### Curves-Based Approach: Maximum Control

The Curves-based dodging and burning technique separates three concerns:

1. **The character of the tonal change** — defined by the Curves adjustment (the shape of the transfer function).
2. **The location of the change** — defined by the layer mask (where you paint white).
3. **The overall intensity** — controlled by layer opacity or by adjusting the curve.

This separation gives you control that the gray layer technique cannot match:

| Control | 50% Gray Layer | Curves-Based |
|---------|---------------|--------------|
| Amount of lightening/darkening | Determined by paint value (how far from 128). Fixed once painted. | Determined by the curve shape. Adjustable at any time by moving the curve. |
| Tonal character | Determined by blend mode math (Soft Light or Overlay). Fixed. | Determined by the curve shape. You can brighten only shadows, or only midtones, or only highlights. |
| Spatial application | Painted directly on the gray layer. | Painted on the mask — white reveals, black hides. |
| Modifying the amount after painting | Requires repainting or adjusting layer opacity (which affects all dodging uniformly). | Adjust the Curves anchor points. All painted areas update simultaneously while preserving relative differences. |
| Tonal targeting | Blend mode math applies a fixed relationship. Cannot target a specific tonal region independently. | Curve shape can target any tonal region. Anchor highlights while dodging shadows. |

**RECOMMENDATION:** For quick, simple dodging and burning (subtle face brightening, edge darkening), the 50% gray layer technique is efficient and adequate. For precise, complex, or revisable work (landscape luminosity, portrait sculpting, commercial retouching), the Curves-based technique provides superior control. Many professionals use both in the same document: a gray layer for broad gestural work, Curves layers for targeted refinements.

### Local Contrast: Spatial Frequency and Perceived Detail

To understand local contrast enhancement, you need the concept of spatial frequency — which will be developed fully in Chapter 15 (Sharpening) but is introduced here because local contrast operates in this domain.

**Spatial frequency** describes how rapidly tonal values change across the image area:

- **Low spatial frequency:** Large areas of relatively uniform tone — a clear sky, a smooth wall, a blurred background. Tonal values change slowly across the surface.
- **High spatial frequency:** Fine detail with rapid tonal changes — individual hair strands, fabric texture, text edges. Tonal values change rapidly from one pixel to the next.
- **Medium spatial frequency:** The intermediate scale — the grain of a rock face, the folds in fabric, the undulations of a hillside. Tonal values change over regions of tens to hundreds of pixels.

Each category of spatial frequency corresponds to a different type of tonal manipulation:

| Spatial Frequency | Scale | Manipulation | Chapter |
|-------------------|-------|-------------- |---------|
| Low | Entire image or large regions | Global tonal adjustments (Levels, Curves) | 07 |
| Medium | Local regions (20-200+ pixels) | Local contrast enhancement, dodging/burning | 08 (this chapter) |
| High | Pixel-level detail (1-5 pixels) | Sharpening | 15 |

**FACT:** Local contrast enhancement increases tonal variation at medium spatial frequencies — the scale of surface texture, material detail, and three-dimensional form. It works by increasing the difference between adjacent regions without necessarily changing the overall tonal range. The visual effect is enhanced perceived detail, texture, and depth.

**INTERPRETATION:** The relationship between local contrast and sharpening is one of scale, not kind. Both increase tonal differences between neighboring areas. Sharpening operates at the pixel-to-pixel level (high spatial frequency). Local contrast enhancement operates at a broader scale (medium spatial frequency). An image can benefit from both: local contrast to reveal the three-dimensional structure of surfaces, and sharpening to crisp the finest edges. They are complementary, not competing.

### Why Local Contrast Enhancement Is Not the Same as Global Contrast

A global contrast increase (Curves S-curve) applies the same transfer function to every pixel. Two pixels with the same input value receive the same output value regardless of their spatial location or surroundings.

Local contrast enhancement adjusts pixels differently based on their spatial context. A pixel at value 128 surrounded by darker pixels (a local bright spot) is treated differently from a pixel at value 128 surrounded by brighter pixels (a local dark spot). The first might be brightened further (increasing its difference from its dark surroundings); the second might be darkened (increasing its difference from its bright surroundings).

This context-dependent behavior is what makes local contrast enhancement a spatial operation. It requires information about the pixel's neighborhood, not just its own value. This is fundamentally different from Curves, which processes each pixel independently.

---

## Photoshop Implementation

### Method 1: The Dodge and Burn Tools (Destructive)

**Setup:** Select the Dodge Tool (O) or Burn Tool (Shift+O). Set Range and Exposure in the Options Bar.

| Setting | Options | Recommended |
|---------|---------|-------------|
| Range | Shadows, Midtones, Highlights | Midtones for general work. Shadows or Highlights for targeted corrections. |
| Exposure | 1-100% | 5-15% for photographic work. Build up gradually. |
| Protect Tones | On/Off | On. Reduces hue shifts and clipping. |

**Pros:** Immediate, intuitive. No layer setup required.

**Cons:** Destructive — modifies pixel data permanently. Cannot be modified after application (beyond undo history). Exposure setting is relative to each stroke — inconsistent buildup. Can produce color shifts, especially in saturated areas. Incompatible with non-destructive workflow principles (Chapter 06).

**RECOMMENDATION:** Avoid the Dodge and Burn tools for photographic editing. Use them only for quick, disposable work on flattened copies where non-destructive workflow is not a concern.

### Method 2: 50% Gray Layer (Non-Destructive)

**Setup:**

1. Create a new layer: Layer > New Layer.
2. In the New Layer dialog: set Mode to Soft Light (or Overlay). Check "Fill with Soft-Light-neutral color (50% gray)." Name the layer "Dodge & Burn."
3. Click OK. The layer appears in the stack with a gray thumbnail but no visible change to the image.

Alternatively: create a new layer, Edit > Fill > 50% Gray, then set the blend mode to Soft Light in the Layers panel.

**Usage:**

| Action | Tool | Color | Opacity |
|--------|------|-------|---------|
| Dodge (lighten) | Brush (B) | White | 5-15% |
| Burn (darken) | Brush (B) | Black | 5-15% |
| Erase dodge/burn | Brush (B) | 50% gray (#808080) | 100% |
| Reduce all dodging/burning | Lower layer opacity | n/a | Adjust to taste |

**RECOMMENDATION:** Use a soft brush (0% hardness) at very low opacity (5-10%) and build up the effect with multiple overlapping strokes. This produces smooth, natural-looking gradations. A single high-opacity stroke creates a visible hot spot. Press D for default colors (black and white foreground/background), then X to swap between them.

**Viewing the work:** Set the gray layer to Normal blend mode temporarily. The dodging (white paint) and burning (black paint) become visible against the neutral gray background. This view is useful for evaluating the uniformity and extent of your work. Return to Soft Light when finished inspecting.

**Pros:** Non-destructive (the original pixels are untouched). Easy to set up. Visual record of all work (visible in Normal blend mode). Layer opacity controls overall strength. Can be duplicated, masked, or deleted.

**Cons:** Cannot independently control the character of the lightening/darkening (fixed by blend mode math). Cannot target specific tonal ranges (shadows, highlights) independently. Difficult to modify the intensity of specific painted areas without repainting.

### Method 3: Curves-Based Dodging and Burning (Non-Destructive, Maximum Control)

**Setup:**

1. Create a Curves Adjustment Layer. Name it "Dodge."
2. Raise the midpoint of the curve to create a visible brightening effect. The exact amount is adjustable later — start with approximately input 128, output 160.
3. Invert the layer mask: select the mask thumbnail, then Ctrl+I (Cmd+I). The mask turns black, hiding the brightening effect entirely.
4. Create a second Curves Adjustment Layer. Name it "Burn."
5. Lower the midpoint of the curve to create a visible darkening effect. Start with approximately input 128, output 95.
6. Invert this layer mask to black as well.

**Usage:**

| Action | Select | Tool | Color | Opacity |
|--------|--------|------|-------|---------|
| Dodge an area | Click the Dodge layer's mask | Brush (B) | White | 10-20% |
| Remove a dodge | Click the Dodge layer's mask | Brush (B) | Black | 100% |
| Burn an area | Click the Burn layer's mask | Brush (B) | White | 10-20% |
| Remove a burn | Click the Burn layer's mask | Brush (B) | Black | 100% |
| Increase overall dodge strength | Adjust the Dodge Curves anchor points upward | n/a | n/a |
| Decrease overall dodge strength | Adjust the Dodge Curves anchor points toward the diagonal | n/a | n/a |

**FACT:** In this technique, you always paint with white (to reveal) or black (to hide) on the mask. You do not paint with gray — the degree of the effect at each location is controlled by the opacity of your white brush strokes as they build up on the mask. Multiple low-opacity white strokes create a mask value between 0 and 255, producing partial application of the Curves effect.

**Advanced: Tonal Targeting with Curves Shape**

The Curves-based method allows something the gray layer technique cannot: restricting the dodge or burn to a specific tonal range by shaping the curve.

- **Dodge shadows only:** On the Dodge Curves layer, raise only the lower portion of the curve (shadows region) while anchoring the midtones and highlights. Even when you paint white on the mask everywhere, only the shadow areas brighten.
- **Burn highlights only:** On the Burn Curves layer, lower only the upper portion of the curve while anchoring the midtones and shadows. Only highlight areas darken.
- **Protect highlight clipping during dodge:** On the Dodge Curves layer, anchor the curve at the upper end (place a point near input 240, output 240). The dodge effect applies to midtones and shadows but the highlights cannot be pushed above the anchor point, preventing clipping.

### Method Comparison

| Criterion | Dodge/Burn Tools | 50% Gray Layer | Curves-Based |
|-----------|-----------------|----------------|--------------|
| Destructive? | Yes | No | No |
| Editable after application? | No (beyond undo) | Partially (erase, adjust opacity) | Fully (adjust curve, adjust mask, adjust opacity) |
| Tonal targeting? | Range selector (Shadows/Midtones/Highlights) | None (blend mode math determines response) | Full (curve shape determines tonal response) |
| Spatial targeting? | Brush stroke location | Brush stroke location | Mask painting |
| Independent intensity control? | No | Layer opacity only (uniform) | Curve adjustment (affects all masked areas proportionally) |
| Visual record of work? | None | View gray layer in Normal mode | View mask (Alt-click mask thumbnail) |
| Setup complexity | None | One layer | Two layers (dodge + burn) |
| Best for | Quick disposable work | General photographic dodging/burning | Precise, revisable, complex dodging/burning |

### Local Contrast Enhancement: High Pass Method

The High Pass filter extracts detail at a specified spatial frequency. Combined with Soft Light or Overlay blend mode, it increases local contrast.

**Setup:**

1. Select all visible layers: Ctrl+Alt+Shift+E (Cmd+Option+Shift+E) to create a merged stamp of the current image on a new layer. Name it "Local Contrast."
2. Convert this layer to a Smart Object (right-click > Convert to Smart Object) for non-destructive filter application.
3. Apply Filter > Other > High Pass. Set the Radius to control the spatial scale:
    - **2-5 px:** Targets fine detail (approaches sharpening — Chapter 15).
    - **10-30 px:** Targets medium detail — surface texture, fabric, stone.
    - **40-100 px:** Targets broad structures — the sense of depth, large-scale form.
4. The image becomes a flat gray field with visible detail at the selected scale.
5. Set the layer blend mode to Soft Light (subtle) or Overlay (stronger).
6. Adjust layer opacity to control the overall intensity.

**FACT:** The High Pass filter works by subtracting a blurred version of the image from the original. The Radius value controls the blur: a small radius subtracts a slightly blurred version (leaving fine detail), while a large radius subtracts a heavily blurred version (leaving broader structures). The result is recentered around 128 (50% gray), which is why it appears as a gray image with detail visible as lighter and darker variations from gray.

**INTERPRETATION:** When this High Pass result is blended through Soft Light or Overlay, the values above 128 lighten the image and the values below 128 darken it — at the spatial scale determined by the High Pass radius. This increases the tonal difference between adjacent regions at that scale, which is precisely what local contrast enhancement means.

**Pros:** Simple to set up. Effective. Non-destructive when applied as a Smart Filter on a Smart Object. The radius provides intuitive control over the spatial scale.

**Cons:** Affects the entire image uniformly unless masked. Can produce halos at high radii if overdone. Cannot target specific tonal ranges without additional techniques (Blend If or luminosity masks).

### Local Contrast Enhancement: Unsharp Mask at Large Radius

**FACT:** The Unsharp Mask filter (Filter > Sharpen > Unsharp Mask) can be used for local contrast enhancement by setting a large Radius (20-100 px) and a low Amount (15-40%). At these settings, the filter does not sharpen fine edges — it increases contrast between broader adjacent regions. This is sometimes called "clarity" or "mid-frequency contrast."

| Unsharp Mask Parameter | Sharpening Use (Ch 15) | Local Contrast Use (This Chapter) |
|------------------------|------------------------|-----------------------------------|
| Amount | 100-300% | 15-40% |
| Radius | 0.5-3 px | 20-100 px |
| Threshold | 0-10 | 0 |

At large radius and low amount, the filter detects tonal transitions at a broad spatial scale and increases the difference across them by a small percentage. The visual effect is enhanced three-dimensionality and surface detail without the edge halos associated with sharpening.

**RECOMMENDATION:** Apply this technique as a Smart Filter on a Smart Object for non-destructive control. The settings are sensitive — small changes in Amount produce significant visible differences. Start conservatively (Amount 20%, Radius 40 px) and increase gradually.

### Local Contrast Enhancement: Camera Raw Filter

If the image is opened as a Smart Object, the Clarity and Texture sliders in Adobe Camera Raw provide sophisticated local contrast enhancement:

- **Clarity:** Enhances contrast at medium spatial frequencies. Produces a broad, three-dimensional look. Can introduce halos at extreme settings.
- **Texture:** Enhances contrast at finer spatial frequencies than Clarity. Targets surface texture specifically. Less prone to halos.

**FACT:** Both Clarity and Texture operate on specific frequency bands within the image. Their exact algorithms are proprietary to Adobe, but their visual behavior is consistent with spatially aware contrast enhancement at different scales. Texture operates at a finer scale than Clarity.

To apply selectively: use the Camera Raw filter on a Smart Object (Filter > Camera Raw Filter), then add a layer mask to the Smart Object or use the filter mask (available as a Smart Filter mask) to restrict the effect to specific areas.

### Using Blend If for Tonal Restriction

Any of the above techniques can be restricted to a specific tonal range using Blend If sliders, without painting a mask.

**Example: Local contrast enhancement applied only to midtones**

1. Apply the High Pass local contrast technique as described above.
2. Double-click to the right of the layer name to open Layer Style.
3. In the "Underlying Layer" Blend If bar, hold Alt/Option and drag the left half of the black slider to approximately 50. Hold Alt/Option and drag the right half of the white slider to approximately 200.
4. The local contrast enhancement now affects only the midtone range (approximately values 50-200). Shadows and highlights are excluded with smooth transitions.

**RECOMMENDATION:** Always split the Blend If sliders (Alt/Option+drag) to create a gradual transition. Non-split sliders create an abrupt cutoff that produces visible banding in photographic images.

---

## Professional Workflow

### Layer Stack Position for Local Tonal Adjustments

In the non-destructive layer stack established in Chapter 06, local tonal adjustments sit above global adjustments:

1. **Smart Object** (base photograph) — bottom.
2. **Retouching layers** (sensor spots, blemishes).
3. **Global tonal adjustments** (Levels, Curves — Chapter 07).
4. **Local tonal adjustments** (dodging, burning, local contrast — this chapter).
5. **Color corrections** (Chapter 09).
6. **Color grading** (Chapter 10).

**RECOMMENDATION:** Group all dodging and burning layers into a layer group named "D&B" or "Dodge & Burn." Group local contrast layers into a group named "Local Contrast." This organization allows toggling all local adjustments on/off with a single click for before/after comparison. It also prevents confusion between global Curves layers (Chapter 07) and local Curves layers (this chapter).

### The Diagnostic Step: Where Does the Eye Go?

Before dodging and burning, answer one question: **where does the viewer's eye go in this photograph, and where do you want it to go?**

1. Squint at the image or view it as a small thumbnail. Your eye will be drawn to the brightest area, the highest-contrast area, or the area of most detail.
2. Is that where you want the viewer to look? If the subject is a person's face but the brightest area is a sunlit patch on the ground behind them, the composition fights against your intent.
3. Plan your dodging and burning to align the tonal emphasis with the compositional intent. Dodge the subject to draw the eye. Burn competing bright areas to suppress them. Darken edges to contain the viewer's gaze.

**INTERPRETATION:** This diagnostic step is more important than any specific technique. A technically perfect dodge-and-burn on the wrong areas makes the photograph worse. Spending 30 seconds analyzing where the eye goes before touching a brush saves minutes of undoing misguided work.

### Brush Settings for Dodging and Burning

The brush settings for dodging and burning are as important as the technique itself:

| Parameter | Recommended Setting | Why |
|-----------|-------------------|-----|
| Hardness | 0% | Soft edges blend naturally. Hard edges create visible boundaries. |
| Opacity | 5-15% | Low opacity forces gradual buildup, producing smooth gradations. High opacity creates blotchy hot spots. |
| Flow | 100% | Keep Flow at 100% and control intensity via Opacity. Reducing both creates unpredictable buildup behavior. |
| Brush size | Large relative to the area | A brush too small for the area produces visible individual strokes. A brush approximately the size of the area being dodged/burned creates a smooth wash. |

**RECOMMENDATION:** Press [ and ] to decrease and increase brush size rapidly. Adjust brush size constantly as you work — large for broad areas, smaller for targeted regions. Pen tablet pressure sensitivity (if available) is valuable for dodging and burning because it provides natural variation in stroke intensity.

### Dodging and Burning: Correction vs Creative Emphasis

Dodging and burning serves two distinct purposes, and recognizing which purpose you are serving affects how you approach the work:

**Corrective dodging and burning:** Fixing exposure problems that exist in the photograph. The subject's face is underexposed because the camera metered for the background. The foreground is too dark because the sky dominated the metering. Corrective work aims to bring the image closer to how the scene appeared to the eye.

**Creative dodging and burning:** Shaping the tonal distribution to serve your artistic intent, even when the exposure is technically correct. Darkening edges to create a natural vignette. Brightening the subject to create separation from the background. Adding depth to clouds by burning their bases and dodging their edges. Creative work goes beyond correction into interpretation.

**INTERPRETATION:** Most professional dodging and burning is a blend of both purposes. The distinction matters because it affects how far you go: corrective work has a natural endpoint (the image looks like the scene), while creative work is a judgment call (the image looks like your vision of the scene). Knowing which mode you are in prevents both under-correction (leaving fixable problems) and over-manipulation (turning a photograph into an illustration).

---

## Step-by-Step Example: Portrait Dodging and Burning

This example walks through a common professional scenario: a portrait photograph where the subject's face competes with bright background elements for the viewer's attention.

**Starting condition:** An environmental portrait. The subject is positioned against a scene with bright highlights — a sunlit wall, a window, or a reflective surface. The face is correctly exposed but does not dominate the frame because the background bright spots draw the eye.

### Step 1: Diagnostic Assessment

1. View the image as a small thumbnail (zoom out, or use Navigator panel). Note where your eye is drawn. If it goes to the background before the face, corrective dodging and burning is needed.
2. Identify the specific areas:
    - Areas to lighten (dodge): the face, possibly the hands or key compositional elements.
    - Areas to darken (burn): the bright background distractions, the edges of the frame.

### Step 2: Create the Dodging and Burning Structure

1. Create a layer group named "D&B" above the global tonal adjustments.
2. Inside the group, create a new layer. In the New Layer dialog: set Mode to Soft Light, check "Fill with Soft-Light-neutral color," name it "D&B - Broad." This layer is for large-area adjustments — edge burning, general face brightening.
3. Create a second new layer with the same settings, named "D&B - Detail." This layer is for smaller, more targeted adjustments — under-eye brightening, shadow sculpting.

**INTERPRETATION:** Separating broad and detail dodging/burning onto different layers gives you independent control. You might want to reduce the broad edge-burning effect while keeping the detailed face work — layer opacity on each layer lets you do this independently.

### Step 3: Broad Dodging and Burning

1. Select the "D&B - Broad" layer.
2. Select a soft brush (0% hardness). Set opacity to 8-10%.
3. Press D to set default colors (black and white), then X to swap between them as needed.
4. **Burn the edges:** With black foreground, paint around the edges of the frame using a large brush (roughly 1/3 to 1/2 the image width). Build up gradually with overlapping strokes. The goal is a subtle, natural darkening — not a visible vignette.
5. **Burn the bright distractions:** Still with black, paint over the bright background elements that compete with the face. Use a smaller brush, still at low opacity.
6. **Dodge the face:** Switch to white (press X). With a brush roughly the size of the face, paint gently over the face and neck. Build up with 3-5 overlapping strokes. The face should brighten subtly relative to the background.

### Step 4: Detail Dodging and Burning

1. Select the "D&B - Detail" layer.
2. Zoom in to 100% on the face.
3. Set brush opacity to 5-8% and brush size to roughly the width of the nose or eyes.
4. **Dodge highlights on the face:** With white, paint over the natural highlights — the forehead, the bridge of the nose, the upper cheeks, the chin. Follow the natural light direction. You are enhancing the existing light, not creating new light sources.
5. **Burn shadows on the face:** With black, paint into the natural shadow areas — beneath the cheekbones, along the jaw line, beside the nose. This enhances the facial structure and dimensionality.
6. **Lighten the eyes:** With white at 3-5% opacity, paint over the irises to brighten them subtly. Overdo this and the result looks artificial — restraint is essential.

### Step 5: Evaluate and Refine

1. Toggle the "D&B" group visibility to compare before/after.
2. If the overall effect is too strong, reduce the opacity of the individual D&B layers.
3. View each gray layer in Normal blend mode to inspect the paint pattern. Look for:
    - Uneven patches that will show as blotchy adjustments.
    - Missing areas that should have been dodged or burned.
    - Over-painted areas that will look heavy-handed.
4. Correct any issues by painting 50% gray over problem areas (erasing the dodge/burn) and re-applying as needed.

**RECOMMENDATION:** The result should be invisible as a technique. The viewer should feel that the portrait subject "just looks good" — the face is luminous, the background supports without competing. If anyone can identify the dodging and burning without toggling the layer, it is overdone.

---

## Common Mistakes

**Using high brush opacity for dodging and burning.** Painting at 30-50% opacity creates immediately visible hot spots and blotchy, uneven results. The fundamental principle of dodging and burning is gradual buildup. Use 5-15% opacity and build up with multiple overlapping strokes. If you need a strong effect, make more passes — do not increase the opacity.

**Dodging and burning on the wrong layer.** With multiple layers in the stack, it is easy to select the wrong layer and paint on image data, a mask, or a different D&B layer than intended. Always verify the selected layer in the Layers panel before painting. The layer name and blend mode in the Layers panel should confirm you are on the correct D&B layer.

**Creating visible edges between dodged/burned and untreated areas.** Using a brush that is too small relative to the area, or a brush that is too hard, creates visible transition lines. Use a large, soft brush and keep it moving — do not park the brush in one spot. The darkroom printer's constant hand motion had the same purpose: feathering the transitions.

**Over-dodging highlights or over-burning shadows.** Dodging an already-bright area pushes it toward clipping. Burning an already-dark area crushes it into black. Check the histogram or Info panel after significant dodging/burning to verify you have not introduced clipping. The Curves-based technique can prevent this by anchoring the curve at the extremes.

**Dodging and burning for the wrong purpose.** Dodging a dark shadow to "fix" it when the shadow serves the composition. Burning a highlight that provides important visual weight. Before every dodge or burn, ask: "Am I making the photograph more effective, or am I reflexively evening out the tones?" Not every shadow needs brightening. Not every highlight needs taming.

**Forgetting to dodge and burn symmetrically on faces.** In portrait retouching, asymmetric dodging/burning creates an unnaturally uneven look. The human face has approximate bilateral symmetry in its light/shadow pattern (given a single light source). If you brighten the left cheekbone, evaluate whether the right side needs the same treatment for consistency.

**Applying local contrast enhancement too aggressively.** Over-applied High Pass or Clarity creates visible halos around tonal transitions — bright rims around dark objects, dark edges around bright objects. These halos are the most common artifact of excessive local contrast enhancement. Reduce the intensity until the halos disappear. If you need to zoom to 100% to see the enhancement, you are approximately at the right strength.

**Confusing local contrast enhancement with sharpening.** Using High Pass at 2 px radius in Overlay mode is sharpening, not local contrast. For local contrast, use a radius of 20 px or higher. For sharpening, use 0.5-3 px. Applying both at the wrong scale wastes effort and produces artifacts. Chapter 15 covers sharpening as a separate operation.

---

## Alternative Approaches

### Camera Raw / Lightroom as the Primary Local Adjustment Tool

Adobe Camera Raw and Lightroom provide built-in local adjustment tools — the Masking panel with Brush, Linear Gradient, Radial Gradient, and AI-based masks (Select Subject, Select Sky). These tools provide local Exposure, Contrast, Highlights, Shadows, Clarity, and other adjustments that apply selectively to masked regions.

**INTERPRETATION:** For many photographers, ACR/Lightroom local adjustments are sufficient for basic dodging and burning. A gradient mask that darkens the sky, a radial mask that brightens the subject, and a brush mask that adds clarity to a textured area can be executed entirely within ACR. The advantages are simplicity and speed. The limitations are precision (ACR brushes are less precise than Photoshop brushes at fine scales) and complexity (multiple overlapping local adjustments become difficult to manage in ACR).

When you need pixel-level precision, multiple overlapping adjustments, or the ability to see and edit the mask as a grayscale image, Photoshop's layer-based approach is superior. Many photographers use ACR for broad local adjustments (sky, foreground, subject) and Photoshop for detailed work (face sculpting, precise dodging).

### Gradient Maps and Luminosity for Tonal Shaping

A Gradient Map adjustment layer (Layer > New Adjustment Layer > Gradient Map) maps tonal values to colors (or to grayscale tones) along a gradient. When mapped to a grayscale gradient, it provides another way to remap tones — functioning similarly to Curves but with a different visual interface. Combined with a mask, it can serve as a local tonal adjustment tool.

This is an advanced technique primarily useful for stylized or black-and-white work. For most photographic dodging and burning, Curves-based methods provide a more intuitive workflow.

### Separate Lightening and Darkening Layers with Blend Modes

Some photographers create two empty layers — one set to Screen blend mode (lightens everything) and one set to Multiply blend mode (darkens everything) — and paint on them with very low opacity black. Screen mode with black paint produces no effect; with gray or white paint, it lightens. Multiply mode works inversely.

**INTERPRETATION:** This approach works but offers no practical advantage over the 50% gray layer technique or the Curves-based technique. The mathematical behavior is less intuitive, and it provides no additional control. It is mentioned here for completeness because it appears in some older tutorials.

### Frequency Separation for Localized Tonal Work

Frequency separation (covered in Chapter 17) splits the image into a low-frequency layer (color and broad tonal transitions) and a high-frequency layer (fine detail and texture). Dodging and burning applied to the low-frequency layer affects only the broad tonal transitions, leaving texture intact. This is primarily a portrait retouching technique for smoothing skin tones without destroying pore detail.

---

## Summary

Local tonal control is the process of applying different tonal adjustments to different areas of a photograph. Where Chapter 07's global adjustments set the overall tonal character, local adjustments shape the distribution of light and shadow within the frame to guide the viewer's eye, correct localized exposure problems, and enhance the three-dimensional quality of the image.

**Dodging** (lightening locally) and **burning** (darkening locally) are the foundational local tonal control techniques, inherited from the photographic darkroom. Three digital methods exist:

- **The Dodge and Burn tools** are destructive and provide no means of later modification. They are unsuitable for professional non-destructive workflow.
- **The 50% gray layer technique** (a layer filled with 50% gray, set to Soft Light or Overlay) is non-destructive and effective for general photographic dodging and burning. Paint white to lighten, black to darken. The neutral gray is invisible because of the mathematical properties of the Soft Light and Overlay blend modes.
- **Curves-based dodging and burning** (paired Curves Adjustment Layers with painted masks) provides the most control. The curve shape determines the character of the lightening or darkening. The mask determines where it applies. Both are independently adjustable at any time.

**Local contrast enhancement** increases tonal variation at medium spatial frequencies — the scale of surface texture and three-dimensional form. It is distinct from global contrast (which applies uniform remapping) and from sharpening (which targets fine pixel-level edges). Techniques include the High Pass filter in Soft Light/Overlay blend mode, Unsharp Mask at large radius and low amount, and the Clarity/Texture sliders in Camera Raw.

**Luminosity masks** restrict adjustments to specific tonal ranges using masks derived from the image's own brightness data. They are introduced here conceptually and covered in full in Chapter 13.

**Blend If sliders** provide a fast, non-destructive way to restrict any layer or adjustment to a tonal range without painting a mask. They offer tonal targeting but not spatial targeting.

The professional workflow is diagnostic: before dodging and burning, identify where the viewer's eye goes and where you want it to go. Use broad, low-opacity brush strokes to build up effects gradually. Separate broad adjustments from detail adjustments on different layers. Group all local tonal layers for easy before/after comparison. Aim for invisible technique — the viewer should see a well-lit photograph, not evidence of manipulation.

---

## Exercises

### Exercise 1: The 50% Gray Layer — Building the Muscle Memory

Open any photograph.

1. Create a new layer set to Soft Light, filled with 50% gray (use the New Layer dialog options).
2. Select a large, soft brush at 8% opacity. Paint white over the center of the image with 5-6 overlapping strokes. Observe the lightening effect.
3. Switch to black (press X). Paint over the edges of the image with 5-6 overlapping strokes. Observe the darkening effect.
4. Set the gray layer to Normal blend mode. Examine the paint pattern — white in the center, black at the edges, gray baseline elsewhere.
5. Return to Soft Light mode. Toggle the layer visibility to compare before/after.
6. Reduce the layer opacity to 50%. The entire effect is halved. Return to 100%.
7. Paint with 50% gray (#808080) over an area you previously dodged. The dodge disappears — you have erased it by restoring the neutral gray.

**Success criterion:** You understand the relationship between paint color on the gray layer, blend mode behavior, and the resulting lightening/darkening effect. You can set up a 50% gray layer without referring to instructions.

### Exercise 2: Curves-Based Dodge and Burn Setup

Open any photograph.

1. Create a Curves Adjustment Layer. Name it "Dodge." Raise the midpoint to approximately input 128, output 165. The entire image brightens.
2. Select the mask thumbnail. Press Ctrl+I (Cmd+I) to invert the mask to black. The brightening disappears — it is hidden everywhere.
3. Select a soft white brush at 10% opacity. Paint on the mask over a small area of the image. The brightening appears only where you paint.
4. Alt-click (Option-click) the mask thumbnail to view it. You should see white paint against a black field — the white areas are where the dodge applies.
5. Return to composite view (Alt-click the mask again). Now adjust the Curves: raise the midpoint further. Observe that all dodged areas brighten proportionally.
6. Lower the midpoint toward the diagonal. The dodge effect weakens. You are controlling the intensity of all previously painted areas by adjusting a single curve — without repainting.
7. Create a second Curves layer named "Burn." Lower the midpoint, invert the mask, and practice burning selected areas.

**Success criterion:** You can set up paired dodge/burn Curves layers, understand that the curve controls intensity while the mask controls location, and recognize the advantage of independent control over both.

### Exercise 3: Comparing Soft Light and Overlay

Create two 50% gray layers on the same photograph — one set to Soft Light, one set to Overlay. Paint identical white strokes at 15% opacity on both layers (same areas, same brush size, same number of strokes).

1. Toggle each layer independently and compare the effect.
2. Which blend mode produces a stronger lightening effect?
3. Which produces a more natural-looking result at the same paint intensity?
4. Now paint black strokes at 15% opacity on both layers. Compare the darkening effect.
5. Evaluate: which blend mode would you choose for subtle portrait dodging? Which for dramatic landscape contrast?

**Success criterion:** You can articulate the visual difference between Soft Light and Overlay blend modes in the context of dodging and burning, and make a deliberate choice based on the image and intent.

### Exercise 4: Local Contrast with High Pass

Open a photograph with significant texture — a stone building, a rocky landscape, or a close-up of weathered wood.

1. Create a merged stamp layer (Ctrl+Alt+Shift+E / Cmd+Option+Shift+E). Convert it to a Smart Object.
2. Apply Filter > Other > High Pass. Set Radius to 5 px. Observe the result — mostly fine detail.
3. Set the layer to Soft Light. Observe the sharpening-like effect.
4. Double-click the High Pass entry in the Layers panel (Smart Filter) to re-open it. Change the Radius to 40 px. Observe the difference — broader structures are now enhanced.
5. Change the Radius to 100 px. Observe how the effect shifts to very broad tonal structures.
6. Set the Radius to a value that enhances surface texture and three-dimensionality without creating halos (typically 20-50 px for most images).
7. Adjust the layer opacity to a level where the enhancement is visible but not obvious. Toggle the layer to confirm.

**Success criterion:** You understand how the High Pass radius controls the spatial scale of the local contrast enhancement, and you can distinguish the visual difference between low-radius (sharpening) and high-radius (local contrast) settings.

---

## Advanced Exercises

### Advanced Exercise 1: Tonal Targeting with Curves-Based Dodge

Open a landscape photograph with both bright sky and dark foreground.

1. Create a Curves Adjustment Layer named "Dodge Shadows." Shape the curve so that only the lower portion is raised (shadows brighten) while the midtones and highlights remain unchanged — place anchor points at the midtone and highlight positions to lock them.
2. Invert the mask to black. Paint white on the mask over the entire image.
3. Observe: only the shadow areas brighten, even though the mask reveals the effect everywhere. The curve shape constrains which tones are affected.
4. Now create a second Curves layer named "Burn Highlights." Shape the curve so that only the upper portion is lowered (highlights darken) while midtones and shadows are anchored.
5. Invert the mask, paint white over the sky. Only the bright areas of the sky darken.
6. Compare this result to what you would achieve with the 50% gray layer technique — can the gray layer produce this kind of tonal targeting?

**Success criterion:** You understand how Curves shape provides tonal targeting in addition to the spatial targeting provided by the mask, and you recognize why the Curves-based method offers control that the gray layer method cannot match.

### Advanced Exercise 2: Blend If for Tonal Restriction

Open a photograph with visible texture in the midtones (landscape or architectural subject).

1. Apply the High Pass local contrast technique (merged stamp, Smart Object, High Pass at 30 px radius, Soft Light blend mode).
2. Observe the effect — it should enhance texture but may introduce halos in the highlights or noise in the shadows.
3. Double-click to the right of the layer name to open Layer Style.
4. In the "Underlying Layer" section, Alt/Option-drag the black slider's right half to approximately 40. This excludes the deepest shadows from the effect.
5. Alt/Option-drag the white slider's left half to approximately 210. This excludes the brightest highlights.
6. Click OK. Observe: the local contrast enhancement now affects only the midtone range. Shadow noise and highlight halos are eliminated.
7. Experiment with the slider positions to find the optimal tonal range for the enhancement.

**Success criterion:** You can use Blend If sliders to restrict any layer effect to a specific tonal range, and you understand the importance of splitting the sliders for smooth transitions.

### Advanced Exercise 3: Combined Dodging, Burning, and Local Contrast

Process a single landscape photograph using the complete local tonal control workflow:

1. **Global tonal adjustment first** (Chapter 07): Levels for endpoints, Curves for overall midtone contrast.
2. **Broad dodging and burning:** 50% gray layer in Soft Light. Dodge the foreground subject. Burn the edges. Darken the sky slightly.
3. **Targeted dodging and burning:** Curves-based dodge layer. Shape the curve to brighten only shadows. Paint on the mask to brighten shadow areas in the foreground without blowing highlights.
4. **Local contrast enhancement:** High Pass at 30 px radius in Soft Light, restricted to midtones with Blend If sliders.
5. Group all local layers and toggle the group to evaluate the before/after.
6. Adjust layer opacities until the overall effect enhances the photograph without looking processed.

**Success criterion:** You can build a complete tonal workflow that integrates global and local adjustments, using the appropriate technique for each purpose. The result looks natural — enhanced, not manipulated.

---

## Blackbelt Challenge

You receive a landscape photograph with the following characteristics:

- The foreground contains detailed rock formations with rich texture, correctly exposed but visually flat — they lack three-dimensionality.
- The sky is dramatic — large clouds with bright edges and dark bases — but the tonal difference between cloud highlights and shadows is insufficient. The sky looks promising but unfinished.
- The middle ground contains a lake reflecting the sky. The reflection is about one stop brighter than ideal, pulling the eye away from the rock foreground.
- The overall exposure is correct (histogram spans the full range with no clipping), and the global contrast has been set with a Curves S-curve (Chapter 07).

Without opening Photoshop, design a complete local tonal control plan:

1. **Diagnostic assessment:** Describe where the viewer's eye goes in the current state of the image, and where you want it to go. Explain the mismatch between the current tonal hierarchy and your compositional intent.

2. **Dodging and burning plan:** Specify every dodge and burn operation you would apply. For each, state:
    - What area is being adjusted.
    - Whether it is a dodge or burn.
    - Which technique you would use (gray layer or Curves-based) and why.
    - What brush opacity and approximate size you would use.
    - Whether the purpose is corrective or creative.

3. **Local contrast plan:** Design a local contrast enhancement strategy for the rock foreground that enhances texture without affecting the smooth lake reflection or the cloud edges. Specify:
    - Which technique you would use (High Pass, Unsharp Mask, or another method).
    - What radius or settings you would choose and why.
    - How you would restrict the effect to the rocks only — describe the masking or Blend If approach.

4. **Cloud sculpting:** The cloud bases need darkening and the cloud edges need brightening to create dramatic depth. Describe how you would approach this:
    - Would you use a single D&B layer or separate dodge and burn Curves layers?
    - How would you handle the transition between cloud base (darken) and cloud edge (lighten) without creating visible artifacts?
    - Would you use any tonal targeting (Curves shape, Blend If) to protect tones that should not change?

5. **Layer organization:** Describe your complete layer stack for this edit, from bottom to top. Name every layer and group, specify blend modes and any Blend If settings.

6. **Self-check:** After completing the plan, identify one area where you might overdo the effect and describe what the visual artifact would look like. Then describe how you would detect and correct it.

This challenge requires you to integrate the diagnostic approach (where does the eye go?), the technique selection (which method for which task?), the tonal analysis (which tones need which treatment?), and the workflow organization (how to structure the layers for maximum control and revisability) — all without touching the software. The ability to plan a complete local tonal strategy before opening the application is the mark of a photographer who has internalized these concepts.
