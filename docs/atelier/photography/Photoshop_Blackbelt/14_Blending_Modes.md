---
description: Blending modes in Photoshop — mathematical foundations, per-pixel compositing formulas, mode categories, Opacity vs Fill distinction, and photographic applications for contrast, exposure, dodge/burn, and color control.
---

# Chapter 14: Blending Modes — Mathematical Foundations and Photographic Applications

## Learning Objectives

After completing this chapter, you will be able to:

1. Explain what a blending mode is at the mathematical level: a per-pixel formula that combines two layers into a single result.
2. Define the three operands in every blending mode calculation — Base, Blend, and Result — and identify which layer is which in a Photoshop layer stack.
3. Write the actual formulas for Multiply, Screen, and Overlay, and predict their output for given input values without opening Photoshop.
4. Explain why Multiply always darkens, Screen always lightens, and Overlay increases contrast — from the mathematics, not from memorized descriptions.
5. Distinguish between Overlay and Soft Light at the formula level, and choose the correct mode for a 50% gray dodge/burn layer based on the degree of contrast desired.
6. Explain the relationship between Overlay and Hard Light, and predict the result of swapping layers between the two modes.
7. Describe the Luminosity and Color blend modes in terms of HSL component separation, and explain why they are essential for preventing unwanted color shifts or tonal shifts from adjustment layers.
8. Explain the distinction between Opacity and Fill for the "Special 8" blend modes, and predict which slider produces the more usable result for a given technique.
9. Select the correct blending mode for common photographic tasks — non-destructive dodge/burn, local contrast enhancement, tonal adjustment isolation, color grading — based on understanding of what the mode does mathematically.

---

## Conceptual Foundation

Chapter 04 introduced blending modes as "a mathematical formula that determines how a layer's pixel values are combined with the composite of layers below it." That description was deliberately brief. This chapter delivers the full treatment.

A blending mode is not a filter. It does not analyze neighborhoods of pixels, detect edges, or operate on spatial relationships. A blending mode is a pure per-pixel arithmetic operation. For every pixel position on the canvas, the blending mode takes two input values — one from the layer below (the composite rendered so far) and one from the current layer — and produces one output value through a defined formula. That formula is the same for every pixel; only the input values change.

This per-pixel independence is both the strength and the limitation of blending modes. The strength: the math is predictable, deterministic, and fast. Once you know the formula, you can calculate the result for any pair of input values. The limitation: blending modes know nothing about context. Multiply does not know whether a pixel is part of a sky or a face. It applies the same formula to every pixel position, blindly.

Understanding the formulas eliminates trial and error. Instead of cycling through 27 blend modes hoping to find one that looks acceptable, you diagnose what the image needs, choose the mode whose formula produces that effect, and predict the result before you apply it. This is the difference between using blending modes as creative slot machines and using them as precision tools.

### Why the Math Matters

Consider two common photographic problems:

**Problem 1:** You have a Curves adjustment layer that increases midtone contrast. It produces a good tonal result, but the increased contrast has shifted the color saturation in a way you do not want. How do you keep the tonal effect while discarding the color shift?

**Problem 2:** You have a 50% gray layer for dodging and burning. You need to choose between Soft Light and Overlay mode. Both produce the dodge/burn effect. Which one is correct for this image?

Without understanding the math, these questions require experimentation — try both, compare, hope you notice the difference. With understanding, the answers are immediate: Problem 1 is solved by setting the Curves layer to Luminosity mode (applies tonal changes only, discards color changes). Problem 2 is answered by knowing that Overlay's formula produces a stronger contrast effect than Soft Light's, so Soft Light is the correct choice when you want gentler, more controllable dodge/burn strokes.

Every "trick" involving blending modes is a direct consequence of the underlying formula. Learn the formulas, and the tricks become obvious.

### Normalized Values

Blending mode formulas are most clearly expressed using normalized values — pixel values scaled from the 0-255 range (8-bit) to the 0.0-1.0 range. In this notation:

- 0 (black) = 0.0
- 128 (50% gray) = approximately 0.5
- 255 (white) = 1.0

To convert from 8-bit to normalized: divide by 255. To convert back: multiply by 255.

This chapter presents formulas in both forms. The normalized form reveals the mathematical structure more clearly. The 8-bit form is what you encounter when reading pixel values in Photoshop's Info panel.

---

## Terminology

### Blending Mode

**Definition:** A per-pixel mathematical formula that determines how the pixel values of a layer (the Blend layer) are combined with the pixel values of the composite below it (the Base) to produce a single output value (the Result). Each blending mode defines a different formula. Photoshop provides 27 blending modes for layers, organized into six functional groups.

**Meaning in photographic practice:** Blending modes are the mechanism through which layers interact. Without a blending mode, the only option would be Normal — each layer simply covers the one below it, modulated by opacity. Blending modes allow layers to darken, lighten, increase contrast, transfer only color or only luminance, and perform many other operations that are fundamental to photographic editing. They are used for dodge/burn layers, contrast enhancement, color grading, exposure blending, luminosity isolation of adjustments, and texture overlays.

**Photoshop implementation:** The blending mode is selected from the dropdown menu at the top-left of the Layers panel. The default mode for new layers is Normal. Blending modes can be applied to pixel layers, adjustment layers, Smart Objects, fill layers, and groups. They can also be applied to painting tools (Brush, Clone Stamp, etc.), where the formula governs how paint strokes interact with existing pixels. The keyboard shortcut Shift+Plus cycles forward through modes; Shift+Minus cycles backward. Photoshop groups the 27 modes into six sections separated by horizontal lines in the dropdown, corresponding to functional categories (covered in the Implementation section).

**Related concepts:** Opacity, Fill, Base/Blend/Result, layer compositing order (Chapter 04), layer mask (Chapter 05).

**Common misconception:** "Blending modes are visual effects to experiment with." Each blending mode is a defined, deterministic mathematical function. The output for any pair of input values is exactly predictable. Treating them as random effects to audition wastes time and produces inconsistent results. Understanding the formulas lets you select the correct mode on the first attempt.

---

### Base Layer / Blend Layer / Result

**Definition:** The three operands in every blending mode calculation. The **Base** (also called the underlying layer or lower layer) is the composite of all visible layers below the current layer — the image as rendered up to this point in the compositing stack. The **Blend** (also called the upper layer or active layer) is the layer whose blending mode is being applied. The **Result** is the computed output of the blending mode formula applied to the Base and Blend values at each pixel position.

**Meaning in photographic practice:** When you set a layer to Multiply mode, the Base is what is below that layer and the Blend is the layer itself. The distinction matters because some blending modes are not commutative — swapping Base and Blend changes the result. Overlay tests the Base value to determine which sub-formula to apply; Hard Light tests the Blend value. This means the same two layers in the same two modes will produce different results depending on which layer is on top.

**Photoshop implementation:** In the Layers panel, the Base is always the rendered composite below the selected layer. The Blend is always the selected layer. The Result is what you see on screen (for that pixel position). When compositing continues upward through additional layers, the Result of one blending operation becomes the Base for the next layer above.

**Related concepts:** Compositing order (Chapter 04), Opacity, blending mode formula.

**Common misconception:** "The background layer is always the Base." The Base is the composite of everything visible below the current layer, not necessarily the Background layer alone. If you have a Background, a retouching layer above it, and a Curves adjustment layer above that, the Base for the Curves layer is the composite of the Background and the retouching layer together — not the Background alone.

---

### Normal Mode

**Definition:** The default blending mode, in which the Blend layer's pixel values completely replace the Base values. The formula is simply: Result = Blend. When Opacity is less than 100%, the formula becomes a linear interpolation: Result = Blend x Opacity + Base x (1 - Opacity).

**Meaning in photographic practice:** Normal mode means the layer sits on top and covers what is below, modulated only by opacity and mask. This is the correct mode for pixel layers containing image content (retouching layers, compositing elements, stamped visible layers) and for adjustment layers where you want the full, unmodified effect of the adjustment.

**Photoshop implementation:** Normal is the default blending mode for all new layers. It is the first entry in the blending mode dropdown. For adjustment layers, Normal mode means the adjustment's full tonal and color effect is applied to the composite below. Changing an adjustment layer's mode to something other than Normal (such as Luminosity or Color) changes which components of the adjustment's effect are applied — this is one of the most important practical uses of blending modes in photography.

**Related concepts:** Opacity, Fill, Dissolve (a related mode that uses dithering instead of transparency — rarely useful in photography).

**Common misconception:** "Normal mode is the absence of a blending mode." Normal is itself a blending mode — it applies the formula Result = Blend. Understanding this helps clarify that every layer always has an active blending mode; there is no "off" state.

---

### Multiply

**Definition:** A blending mode that multiplies the Base and Blend values and divides by the maximum value, producing a result that is always equal to or darker than either input.

The formula in 8-bit values:

Result = (Base x Blend) / 255

The formula in normalized values (0.0 to 1.0):

result = base x blend

**Meaning in photographic practice:** Multiply simulates stacking two transparencies (slides) on a light table. Light must pass through both, so the result is always darker. Photographers use Multiply for darkening operations: duplicating a layer and setting it to Multiply darkens the entire image (equivalent to approximately one stop of underexposure for typical images). A black brush on a Multiply layer produces pure black. A white brush on a Multiply layer has no effect. Multiply is used for adding density to thin or overexposed areas, combining texture overlays, and creating vignettes.

**Photoshop implementation:** Selected from the blending mode dropdown. Multiply is in the second group (Darken modes). It can be applied to any layer type. When used with adjustment layers, the adjustment's rendered output is multiplied with the composite below, which produces different results than applying the adjustment in Normal mode.

**FACT:** The formula guarantees three properties:

| Blend Value | Result | Why |
|---|---|---|
| White (255 / 1.0) | Base unchanged | Base x 255 / 255 = Base. Multiplying by 1.0 is the identity operation. |
| Black (0 / 0.0) | Pure black (0) | Base x 0 / 255 = 0. Multiplying by zero always produces zero. |
| Any value | Always <= min(Base, Blend) | Both normalized values are <= 1.0, so their product is <= either factor. |

This is why Multiply darkens: multiplying any value by a fraction produces a smaller number. The only exception is when one input is exactly 1.0 (white), which preserves the other value unchanged.

**Related concepts:** Screen (the mathematical inverse), Linear Burn (adds values and subtracts 255 — a more aggressive darkener), Color Burn (a contrast-increasing darkener).

**Common misconception:** "Multiply makes things darker" is correct as a description, but it obscures the mechanism. Multiply does not subtract a fixed amount from each value. It scales each value by a factor proportional to the Blend value. Dark Blend values produce heavy darkening; light Blend values produce minimal darkening. This proportional behavior is why Multiply preserves relative tonal relationships — it darkens while maintaining contrast structure, unlike Linear Burn which shifts values uniformly and clips aggressively.

---

### Screen

**Definition:** A blending mode that inverts both inputs, multiplies them, and inverts the result — producing an output that is always equal to or lighter than either input. Screen is the mathematical complement of Multiply.

The formula in 8-bit values:

Result = 255 - ((255 - Base) x (255 - Blend) / 255)

The formula in normalized values:

result = 1 - (1 - base) x (1 - blend)

**Meaning in photographic practice:** Screen simulates projecting two slides onto the same screen from two projectors. Both images contribute light, so the result is always brighter. Photographers use Screen for lightening operations: duplicating a layer and setting it to Screen brightens the entire image (roughly one stop of additional exposure for typical images). Screen is used to open up underexposed shadows, lighten dark areas selectively, and create glow effects.

**Photoshop implementation:** Selected from the blending mode dropdown. Screen is in the third group (Lighten modes). Like Multiply, it can be applied to any layer type.

**FACT:** The formula guarantees three properties — the mirror image of Multiply's properties:

| Blend Value | Result | Why |
|---|---|---|
| Black (0 / 0.0) | Base unchanged | 1 - (1 - base) x 1 = 1 - (1 - base) = base. Black in Screen has no effect. |
| White (255 / 1.0) | Pure white (255) | 1 - (1 - base) x 0 = 1 - 0 = 1. Screening with white produces white. |
| Any value | Always >= max(Base, Blend) | The formula always produces a value >= both inputs. |

This is why Screen lightens: it is "Multiply on the inverses," and multiplying inverted values then inverting back always increases the result.

**Related concepts:** Multiply (the mathematical inverse — Multiply the inverses to understand Screen, or Screen the inverses to understand Multiply), Linear Dodge/Add (adds values directly — a more aggressive lightener), Color Dodge (a contrast-increasing lightener).

**Common misconception:** "Screen is just the opposite of Multiply — it adds light." The concept is correct, but "adds light" is imprecise. Screen does not add a fixed amount of brightness. It operates proportionally, like Multiply. Dark areas receive more lightening than bright areas (as a proportion of the remaining headroom to white). This proportional behavior is why Screen preserves highlight detail better than Linear Dodge, which adds values directly and clips highlights aggressively.

---

### Overlay

**Definition:** A blending mode that combines Multiply and Screen, switching between them based on the Base layer value. Where the Base is dark (below 50% gray), Overlay applies a Multiply-like formula. Where the Base is light (above 50% gray), Overlay applies a Screen-like formula. The result is increased contrast centered around the midpoint of the Base.

The formula in 8-bit values:

If Base <= 128:  Result = (2 x Base x Blend) / 255

If Base > 128:   Result = 255 - (2 x (255 - Base) x (255 - Blend) / 255)

The formula in normalized values:

If base <= 0.5:  result = 2 x base x blend

If base > 0.5:   result = 1 - 2 x (1 - base) x (1 - blend)

**Meaning in photographic practice:** Overlay increases contrast. Dark areas of the Base become darker (Multiply-like behavior), and light areas become brighter (Screen-like behavior). The pivot point is 50% gray — values at exactly 50% gray are minimally affected. This makes Overlay useful for contrast enhancement and for the 50% gray dodge/burn technique: a 50% gray layer set to Overlay is invisible (neutral gray has no effect), and painting with white lightens (dodges) while painting with black darkens (burns).

**Photoshop implementation:** Selected from the blending mode dropdown. Overlay is in the fourth group (Contrast modes). The contrast modes are the most commonly used group in photographic editing.

**FACT:** The critical detail in Overlay's formula is that the conditional tests the **Base** value, not the Blend value. This means Overlay's behavior is determined by the underlying image — dark areas of the image get Multiplied, light areas get Screened — regardless of what is on the Blend layer. This is why Overlay "respects the image" — it enhances the existing contrast structure rather than imposing a new one.

**FACT:** When the Blend value is 50% gray (128 / 0.5), the result approximately equals the Base value — no visible change. This is the mathematical reason why a 50% gray fill layer set to Overlay is invisible: every pixel's Blend value is 0.5, so the formula produces approximately the original Base value everywhere.

Verification: If blend = 0.5 and base <= 0.5: result = 2 x base x 0.5 = base. If blend = 0.5 and base > 0.5: result = 1 - 2 x (1-base) x 0.5 = 1 - (1-base) = base. Both halves of the formula return the Base value unchanged.

**Related concepts:** Soft Light (same concept, gentler formula), Hard Light (same formula but tests Blend instead of Base), 50% gray dodge/burn (Chapter 08).

**Common misconception:** "Overlay is just a contrast boost." Overlay is a contrast boost applied through a specific per-pixel formula. The degree of the effect depends on the Blend layer's content. A 50% gray Blend produces no change. A copy of the image as the Blend layer produces a strong contrast increase. A texture overlay as the Blend layer embeds the texture into the image's tonal structure. Understanding the formula lets you predict all of these applications rather than discovering them by accident.

---

### Soft Light

**Definition:** A blending mode that produces a contrast effect similar to Overlay but with a gentler, more gradual formula. Where Overlay uses a hard switch between Multiply and Screen at the 50% gray boundary, Soft Light uses a smooth, continuous function that produces a subtler result. Like Overlay, 50% gray in the Blend layer is neutral (no visible effect).

**UNCERTAINTY:** The exact formula Photoshop uses for Soft Light is not published in Adobe's official documentation, and multiple formulas have been proposed by reverse-engineering researchers. The most widely cited formulation (sometimes called the "Pegtop" variant) is:

In normalized values:

result = (1 - 2 x blend) x base^2 + 2 x blend x base

This can be rewritten as:

result = base x (base + 2 x blend x (1 - base))

Verification of neutral gray behavior: When blend = 0.5: result = (1 - 1) x base^2 + 1 x base = 0 + base = base. Correct — 50% gray produces no change.

An alternative formula appears in the W3C Compositing specification, which uses a piecewise function with a square root term for blend values above 0.5. The visual difference between the two formulations is subtle. Both produce the key properties described below. If precise formula verification matters for your work, test against Photoshop's actual output with known input values.

**Meaning in photographic practice:** Soft Light is the preferred blending mode for the 50% gray dodge/burn technique in most photographic workflows. Its gentler response curve means that brush strokes on the gray layer produce more gradual, more controllable lightening and darkening than Overlay mode. Where Overlay can produce harsh transitions and excessive contrast, Soft Light builds up gradually, making it easier to paint subtle tonal shaping.

**Photoshop implementation:** Selected from the blending mode dropdown, in the Contrast modes group directly below Overlay. Soft Light is the default recommendation in Chapter 08 for non-destructive dodge/burn layers, and it is the gentler option for the High Pass local contrast technique.

**FACT:** Regardless of which exact formula Photoshop uses internally, Soft Light's behavior can be characterized by three verifiable properties:

| Property | Behavior |
|---|---|
| 50% gray Blend | Neutral — no visible change to the Base |
| Blend lighter than 50% gray | Lightens the Base (dodge effect) |
| Blend darker than 50% gray | Darkens the Base (burn effect) |
| Overall intensity | Gentler than Overlay for the same Blend values |

**Related concepts:** Overlay (same concept, more aggressive), Hard Light (Overlay with layers conceptually swapped), 50% gray dodge/burn (Chapter 08).

**Common misconception:** "Soft Light and Overlay do the same thing — Soft Light is just weaker." The effect is qualitatively similar (both increase contrast, both have 50% gray as neutral), but the formulas are different in kind, not merely in degree. Soft Light's response curve is smooth and continuous. Overlay's formula has a hard conditional break at 50% gray Base values. Reducing the Opacity of an Overlay layer does not produce the same result as Soft Light at full opacity — the tonal distribution of the effect differs. They are related but distinct tools.

---

### Hard Light

**Definition:** A blending mode that uses the same mathematical formula as Overlay but tests the **Blend** layer value (instead of the Base value) to determine which sub-formula applies. Where Overlay asks "is the Base dark or light?" and applies Multiply or Screen accordingly, Hard Light asks "is the Blend dark or light?"

The formula in normalized values:

If blend <= 0.5:  result = 2 x base x blend

If blend > 0.5:   result = 1 - 2 x (1 - base) x (1 - blend)

**Meaning in photographic practice:** Hard Light and Overlay are symmetric partners. If you have two layers, A and B, and you place B on top set to Overlay, the result is the same as placing A on top set to Hard Light (with the appropriate layer arrangement). In practice, this symmetry means that Overlay "protects" the Base layer's tonal structure (the conditional depends on the Base), while Hard Light "imposes" the Blend layer's tonal structure (the conditional depends on the Blend). Photographers encounter Hard Light less frequently than Overlay, but it is useful for texture and pattern overlays where the overlay content should drive the contrast behavior.

**Photoshop implementation:** Located in the Contrast modes group, below Overlay and Soft Light in the dropdown. Like Overlay, 50% gray in the Blend layer is neutral.

**Related concepts:** Overlay (same formula, tests Base instead of Blend), Soft Light (gentler variant of Overlay), Vivid Light (a more aggressive variant based on Color Burn/Dodge).

**Common misconception:** "Hard Light is just a stronger version of Overlay." It is not stronger — it uses the same formulas with the same magnitude. The difference is which layer controls the conditional branching. This distinction matters when the Base and Blend have different tonal distributions, because the conditional determines where Multiply vs Screen behavior applies.

---

### Luminosity Mode

**Definition:** A blending mode that applies only the luminance (brightness) of the Blend layer to the Result, while preserving the hue and saturation of the Base layer. The Result has the tonal values of the Blend and the color of the Base.

**Meaning in photographic practice:** Luminosity mode is essential for preventing color shifts from tonal adjustments. When you apply a Curves S-curve in Normal mode, the increased contrast also increases apparent color saturation — because stretching the difference between channel values in RGB inherently increases saturation. Setting the Curves adjustment layer to Luminosity mode applies the tonal effect (the contrast change) while discarding the color side-effect (the saturation increase). The image gets the contrast you wanted without the color shift you did not want.

This is one of the most important practical applications of blending modes in photographic editing.

**Photoshop implementation:** Located at the bottom of the blending mode dropdown, in the Component modes group (the sixth and final group). Luminosity mode uses the HSL (Hue, Saturation, Luminosity) color model to decompose the Blend and Base into components, then recombines them: Luminosity from the Blend, Hue and Saturation from the Base.

**FACT:** The exact luminance calculation Photoshop uses for the Luminosity blend mode is based on HSL decomposition, not a simple weighted sum of RGB channels. The luminosity value is derived from the relationship between the maximum and minimum RGB channel values at each pixel: L = (max(R,G,B) + min(R,G,B)) / 2. This differs from the "perceived luminance" formula (0.299R + 0.587G + 0.114B) used elsewhere in image processing. The distinction rarely matters in practice, but explains why Luminosity mode results can occasionally differ from what you might predict using the weighted-sum formula.

**Related concepts:** Color mode (the complement — applies color from Blend, luminance from Base), HSL color model (Chapter 02), Normal mode, adjustment layer blend modes.

**Common misconception:** "Luminosity mode changes nothing visible." If the Blend layer has the same luminance distribution as the Base, then yes, Luminosity mode produces no visible change. But if the Blend layer has different luminance (as when an adjustment layer changes tonal values), Luminosity mode applies those tonal changes while filtering out any color side-effects. The tonal change is visible; the color change is not.

---

### Color Mode

**Definition:** A blending mode that applies the hue and saturation of the Blend layer to the Result, while preserving the luminance of the Base layer. The Result has the color of the Blend and the tonal values of the Base.

**Meaning in photographic practice:** Color mode is the complement of Luminosity mode. It is used when you want to change color without affecting tones. Setting a Hue/Saturation or Color Balance adjustment layer to Color mode ensures that the color shift does not alter the image's brightness distribution. More importantly, painting on a layer set to Color mode lets you change colors in the image (for example, recoloring an object) without disturbing the underlying luminance detail — the shading, texture, and form of the object are preserved because only the color changes.

**Photoshop implementation:** Located in the Component modes group, directly above Luminosity in the dropdown. Like Luminosity mode, Color mode uses HSL decomposition to separate the color components (Hue and Saturation) from luminance.

**Related concepts:** Luminosity mode (the complement), Hue mode (applies only hue, preserving both saturation and luminance — more restrictive than Color), Saturation mode (applies only saturation).

**Common misconception:** "Color mode is for color grading." Color mode can be used for color grading, but its primary value is isolation — ensuring that color adjustments do not produce unintended tonal shifts. Many photographers set color adjustment layers (Hue/Saturation, Color Balance, Selective Color) to Color mode as a safety measure, ensuring that the adjustment changes only color, never brightness.

---

### Opacity vs Fill — The "Special 8" Blend Modes

**Definition:** Opacity and Fill are both transparency controls for layers, but they interact differently with certain blending modes. For most blending modes (including Normal, Multiply, Screen, Overlay, Soft Light, Luminosity, and Color), reducing Opacity and reducing Fill by the same amount produce identical visual results. However, for eight specific blend modes — known as the "Special 8" — reducing Fill produces a visibly different result than reducing Opacity by the same amount.

The "Special 8" blend modes are:

| Mode | Group |
|---|---|
| Color Burn | Darken modes |
| Linear Burn | Darken modes |
| Color Dodge | Lighten modes |
| Linear Dodge (Add) | Lighten modes |
| Vivid Light | Contrast modes |
| Linear Light | Contrast modes |
| Hard Mix | Contrast modes |
| Difference | Difference modes |

**Meaning in photographic practice:** The distinction matters because for these eight modes, reducing Fill produces a more subtle, more usable photographic effect than reducing Opacity by the same percentage. When Opacity is reduced, Photoshop calculates the full blend mode result at 100% and then linearly interpolates between that result and the unmodified Base — effectively fading the entire effect uniformly. When Fill is reduced for the Special 8, the internal calculation itself changes — the blend mode formula receives a modulated input, producing a qualitatively different interaction with the Base values.

**INTERPRETATION:** The practical consequence is that for techniques using the Special 8 modes (most commonly Vivid Light, Linear Light, or Hard Mix for advanced dodge/burn or contrast effects), the Fill slider provides a smoother, more photographic-looking gradation than the Opacity slider. Many advanced practitioners use Fill exclusively when working with these modes.

**Photoshop implementation:** Opacity is controlled by the Opacity field at the top-right of the Layers panel. Fill is directly below it. For layers with Normal, Multiply, Screen, Overlay, Soft Light, Hard Light, Luminosity, or Color blending modes, the two sliders produce identical results. For the Special 8, test both and observe the difference — it is substantial.

**Related concepts:** Opacity (Chapter 04), Fill (Chapter 04), layer styles (Fill does not affect layer style transparency, while Opacity does — relevant for graphic design but rarely for photography).

**Common misconception:** "Opacity and Fill are always interchangeable, and Fill only matters for layer styles." The layer style distinction (Fill reduces pixel transparency without affecting Drop Shadow, Stroke, etc.) is one difference, but the Special 8 behavior is a separate, independent distinction that matters for photographic blending mode work regardless of whether layer styles are present.

---

## Theory: The Mathematics of Blending Modes

### The General Compositing Formula

Every blending mode operation in Photoshop follows the same overall compositing structure:

1. The blend mode formula is applied to the Base and Blend values, producing a raw blended result.
2. The layer mask (if any) determines the effective transparency of the Blend layer at each pixel.
3. The Opacity value scales the contribution of the blended result vs the original Base.

In normalized notation:

final = blended_result x effective_opacity + base x (1 - effective_opacity)

Where effective_opacity = layer_opacity x mask_value (both in the 0.0-1.0 range).

At 100% opacity with no mask, the final result is purely the blended_result. As opacity decreases, the final result transitions smoothly toward the unmodified Base.

**FACT:** This means Opacity always acts as a linear fade between the fully blended result and the original Base, regardless of the blend mode. Understanding this helps predict the effect of reducing opacity on any blend mode: at 50% opacity, you see a 50/50 mix of the full blend effect and the original image.

### Multiply: Detailed Analysis

Normalized formula: result = base x blend

This is ordinary multiplication. Consider what happens to specific values:

| Base | Blend | Result | Interpretation |
|---|---|---|---|
| 0.5 (mid gray) | 0.5 (mid gray) | 0.25 (dark gray) | Two mid grays multiplied produce a substantially darker result |
| 1.0 (white) | 0.7 | 0.7 | White Blend preserves the Base exactly |
| 0.7 | 1.0 (white) | 0.7 | White Base preserves the Blend exactly |
| 0.0 (black) | any value | 0.0 (black) | Black in either layer produces black |
| 0.8 | 0.9 | 0.72 | Two light values produce a moderately darker result |
| 0.2 | 0.3 | 0.06 | Two dark values produce a much darker result |

The darkening effect is proportional. Light values are darkened gently; dark values are darkened aggressively. This is why Multiply preserves the relative tonal structure of the image — it is a multiplicative (proportional) scaling, not a subtractive (uniform) shift.

**Physical analogy:** Multiply is optically correct for combining two transparent media. If a red filter transmits 80% of light and a blue filter transmits 60%, stacking them transmits 0.8 x 0.6 = 48%. This is exactly what Multiply computes. The darkroom analogy of sandwiching two slides is the same physics.

### Screen: Detailed Analysis

Normalized formula: result = 1 - (1 - base) x (1 - blend)

This can be understood as "Multiply the inverses, then invert the result." Algebraically equivalent to: result = base + blend - base x blend.

| Base | Blend | Result | Interpretation |
|---|---|---|---|
| 0.5 (mid gray) | 0.5 (mid gray) | 0.75 (light gray) | Two mid grays screened produce a substantially lighter result |
| 0.0 (black) | 0.3 | 0.3 | Black Blend preserves the Base exactly |
| 0.3 | 0.0 (black) | 0.3 | Black Base preserves the Blend exactly |
| 1.0 (white) | any value | 1.0 (white) | White in either layer produces white |
| 0.2 | 0.3 | 0.44 | Two dark values produce a moderately lighter result |
| 0.8 | 0.9 | 0.98 | Two light values produce a result close to white |

The lightening is proportional — dark values are lightened significantly; light values have less headroom and approach white asymptotically. Screen preserves the relative tonal structure in the same way Multiply does, but in the light direction.

**Physical analogy:** Screen models two projectors aimed at the same screen. Each projector contributes light independently. The combined image is always brighter than either individual projection.

### The Multiply-Screen Duality

Multiply and Screen are mathematical complements:

- Multiply(base, blend) = base x blend
- Screen(base, blend) = 1 - (1 - base) x (1 - blend)

Screen is "Multiply applied to the inverted values, then inverted back." Every property of Screen can be derived by inverting the corresponding property of Multiply:

| Multiply | Screen |
|---|---|
| Always darkens | Always lightens |
| White Blend is neutral | Black Blend is neutral |
| Black Blend produces black | White Blend produces white |
| Darkens dark areas aggressively | Lightens light areas aggressively |

Understanding this duality means you only need to learn one formula. The other follows by inversion.

### Overlay: Detailed Analysis

Normalized formula:

If base <= 0.5:  result = 2 x base x blend

If base > 0.5:   result = 1 - 2 x (1 - base) x (1 - blend)

The first branch is "double Multiply" — twice the darkening of standard Multiply. The second branch is "double Screen" — twice the lightening of standard Screen. The factor of 2 compensates for the fact that each branch operates on only half the tonal range.

| Base | Blend | Result | Which Branch | Effect |
|---|---|---|---|---|
| 0.25 (dark) | 0.5 (mid gray) | 0.25 | Multiply branch | No change (50% gray Blend is neutral) |
| 0.75 (light) | 0.5 (mid gray) | 0.75 | Screen branch | No change (50% gray Blend is neutral) |
| 0.25 (dark) | 0.75 (light) | 0.375 | Multiply branch | Darkened, but less than if Blend were also dark |
| 0.75 (light) | 0.75 (light) | 0.9375 | Screen branch | Lightened significantly |
| 0.25 (dark) | 0.25 (dark) | 0.125 | Multiply branch | Darkened significantly |
| 0.75 (light) | 0.25 (dark) | 0.625 | Screen branch | Darkened from 0.75 toward midtone |

**FACT:** The key insight is the conditional on the **Base** value. Overlay preserves the Base's tonal structure: areas that are already dark get darker (Multiply branch), areas that are already light get lighter (Screen branch). The Blend layer controls the intensity but not the direction of the change. This is why Overlay is described as "increasing contrast" — it pushes dark and light values further apart, pivoting around the midpoint.

### Soft Light vs Overlay: Quantitative Comparison

Using the commonly cited Soft Light formula: result = (1 - 2 x blend) x base^2 + 2 x blend x base

Compare the two modes for identical Base and Blend values:

| Base | Blend | Overlay Result | Soft Light Result | Difference |
|---|---|---|---|---|
| 0.25 | 0.75 | 0.375 | 0.344 | Overlay is more aggressive |
| 0.75 | 0.75 | 0.938 | 0.891 | Overlay is more aggressive |
| 0.25 | 0.25 | 0.125 | 0.156 | Overlay darkens more |
| 0.75 | 0.25 | 0.625 | 0.609 | Overlay darkens more |
| 0.50 | 0.50 | 0.500 | 0.500 | Both neutral at 50% gray |
| 0.50 | 0.75 | 0.750 | 0.625 | Overlay shifts more from neutral |
| 0.50 | 0.25 | 0.250 | 0.375 | Overlay shifts more from neutral |

The pattern is clear: Overlay produces larger deviations from the Base value than Soft Light for the same inputs. Both are neutral at 50% gray Blend. Both darken dark areas and lighten light areas. But Overlay does so more aggressively.

**RECOMMENDATION:** For 50% gray dodge/burn layers, Soft Light is the better default choice in most photographic workflows. Its gentler response means you can paint with higher brush opacity while maintaining smooth, controllable results. Use Overlay when you deliberately want a stronger effect — for example, for dramatic local contrast or heavy-handed dodging on severely underexposed areas.

### Hard Light: The Symmetric Partner of Overlay

Hard Light uses the same formula as Overlay but tests the **Blend** value instead of the **Base** value:

If blend <= 0.5:  result = 2 x base x blend

If blend > 0.5:   result = 1 - 2 x (1 - base) x (1 - blend)

This symmetry has a specific consequence: placing Layer A above Layer B with Overlay produces the same result as placing Layer B above Layer A with Hard Light (assuming no other layers interact).

| Mode | Conditional tests | Effect is driven by |
|---|---|---|
| Overlay | Base value | The underlying image's existing tonal structure |
| Hard Light | Blend value | The applied layer's tonal content |

**INTERPRETATION:** Overlay says: "I respect the image below — its darks stay dark, its lights stay light." Hard Light says: "I impose my own tonal structure — my darks darken, my lights lighten." For photographic editing, Overlay is far more commonly used because you typically want the blend effect to follow the image's existing structure. Hard Light is useful when the blend layer's content should dictate the contrast behavior — for example, when applying a texture or pattern that should maintain its own light-dark distribution.

### Luminosity and Color: Component Separation

The Luminosity and Color blend modes decompose the Base and Blend into HSL components and selectively recombine them:

| Mode | Takes from Blend | Takes from Base | Result |
|---|---|---|---|
| Luminosity | Luminosity (L) | Hue (H), Saturation (S) | Tones of the Blend, colors of the Base |
| Color | Hue (H), Saturation (S) | Luminosity (L) | Colors of the Blend, tones of the Base |

These two modes are complements. Together, they decompose the blending operation into two independent components: tonal and chromatic.

**FACT:** When a Curves adjustment layer increases midtone contrast in Normal mode, it produces two effects simultaneously: (1) tonal contrast increase and (2) apparent saturation increase. The saturation increase occurs because stretching the differences between channel values in RGB inherently increases the chromatic separation between channels. Setting the Curves layer to Luminosity mode isolates effect (1) — the tonal contrast — and discards effect (2) — the saturation shift.

**FACT:** When a Hue/Saturation adjustment layer shifts the hue of a color range, it can produce unintended luminance shifts — because different hues have different inherent perceptual luminance (yellow is inherently lighter than blue at the same saturation). Setting the adjustment layer to Color mode prevents the hue shift from altering the image's luminance distribution.

---

## Photoshop Implementation

### The Six Mode Groups

Photoshop organizes the 27 blending modes into six groups, separated by horizontal dividers in the dropdown. Each group shares a functional characteristic:

| Group | Modes | Common Characteristic |
|---|---|---|
| 1. Normal | Normal, Dissolve | Layer replaces Base (Dissolve uses dithering) |
| 2. Darken | Darken, Multiply, Color Burn, Linear Burn, Darker Color | Result is always darker than or equal to the Base |
| 3. Lighten | Lighten, Screen, Color Dodge, Linear Dodge (Add), Lighter Color | Result is always lighter than or equal to the Base |
| 4. Contrast | Overlay, Soft Light, Hard Light, Vivid Light, Linear Light, Pin Light, Hard Mix | Darken and lighten simultaneously; 50% gray Blend is neutral |
| 5. Inversion | Difference, Exclusion, Subtract, Divide | Produce difference or comparative results |
| 6. Component | Hue, Saturation, Color, Luminosity | Operate on HSL components independently |

**RECOMMENDATION:** You do not need to memorize all 27 modes. For photographic editing, the following subset covers the vast majority of needs:

| Mode | Primary Photographic Use |
|---|---|
| Normal | Default for pixel layers, adjustment layers with full effect |
| Multiply | Darkening, burning, vignettes |
| Screen | Lightening, dodging, opening shadows |
| Overlay | Contrast enhancement, texture overlay, dodge/burn (aggressive) |
| Soft Light | Dodge/burn (controlled), subtle contrast, High Pass local contrast |
| Luminosity | Isolating tonal effect of adjustment layers |
| Color | Isolating color effect of adjustment layers, recoloring |

The remaining 20 modes have legitimate uses in specialized contexts, but these seven modes handle the large majority of photographic blending work.

### Photographic Application: Multiply for Darkening

**Technique:** Duplicate the Background (or stamp visible), set the copy to Multiply mode. The image darkens by approximately one stop. Reduce Opacity to moderate the effect.

**When to use:** Quick exposure correction for moderately overexposed images. Also useful for adding density to thin or washed-out scans of film negatives.

**RECOMMENDATION:** For most photographic darkening needs, a Curves adjustment layer provides superior control. The Multiply-duplicate technique is a quick approximation, not a precision tool. Its advantage is speed; its disadvantage is that it darkens the entire tonal range proportionally, with no ability to target specific tones.

### Photographic Application: Screen for Lightening

**Technique:** Duplicate the Background (or stamp visible), set the copy to Screen mode. The image lightens by approximately one stop. Reduce Opacity to moderate.

**When to use:** Quick exposure correction for moderately underexposed images. Screen lightens shadows more effectively than simply raising Curves, because its proportional behavior concentrates the lightening effect in the darker values.

**RECOMMENDATION:** Like the Multiply technique, this is a quick approximation. A Curves adjustment layer offers more control for most situations. Screen is most useful as a fast one-step correction or as a component of a more complex blending workflow.

### Photographic Application: Soft Light with 50% Gray for Non-Destructive Dodge/Burn

This technique was introduced in Chapter 08. Here is the mathematical explanation for why it works.

**Setup:**

1. Create a new pixel layer. Fill it with 50% gray (Edit > Fill > 50% Gray).
2. Set the layer's blending mode to Soft Light.
3. The image appears unchanged — 50% gray is neutral in Soft Light mode.
4. Paint with white to dodge (lighten). Paint with black to burn (darken).

**Why it works:** In Soft Light mode, a Blend value of 0.5 (50% gray) produces no change to the Base (as demonstrated in the formula verification above). Values above 0.5 (lighter than mid gray, toward white) lighten the Base. Values below 0.5 (darker than mid gray, toward black) darken the Base. By filling the layer with neutral gray, you create a "blank canvas" where every deviation from gray becomes a dodge or burn instruction.

**Why Soft Light instead of Overlay:** Chapter 08 recommended Soft Light for most dodge/burn work. The quantitative comparison in the Theory section above demonstrates why: Soft Light produces gentler, more controllable shifts for the same brush input. This means you can use higher brush opacity (15-20%) with Soft Light and still achieve smooth, buildable results. With Overlay, the same brush opacity produces a more aggressive shift that is harder to blend seamlessly.

**FACT:** The dodge/burn strokes on the gray layer are visible when you Alt-click the layer thumbnail in the Layers panel (which displays the layer in isolation). This provides a "map" of all your dodge/burn work — white areas where you dodged, black areas where you burned, gray areas you left unchanged. This visual feedback is unique to the gray layer technique and is one of its practical advantages over the Curves-with-mask alternative described in Chapter 08.

### Photographic Application: Luminosity Mode on Adjustment Layers

**Problem:** You apply a Curves S-curve to increase midtone contrast. The contrast improvement is exactly what you wanted, but the image also becomes more saturated than you intended. The increased contrast has amplified the color channel differences.

**Solution:** Set the Curves adjustment layer's blending mode to **Luminosity**.

**Effect:** The tonal remapping (contrast increase) is applied. The color side-effect (saturation increase) is discarded. The image receives the tonal change only, preserving the original color relationships.

**RECOMMENDATION:** Consider setting every Curves and Levels adjustment layer to Luminosity mode by default, unless you specifically want the color shift that accompanies the tonal change. The saturation increase from a Normal-mode Curves contrast boost is often subtle, but in images with vivid colors (autumn foliage, saturated skies, product photography), the unwanted color shift can be significant. Luminosity mode eliminates it at no cost.

**FACT:** The same principle works in reverse. If you have a Hue/Saturation adjustment layer that shifts a color range, and the shift produces an unwanted luminance change (because different hues have different inherent perceptual brightness), you can set the Hue/Saturation layer to **Color** mode. This preserves the color shift while discarding the luminance side-effect.

### Photographic Application: Color Mode on Adjustment Layers

**Problem:** You use a Hue/Saturation adjustment to desaturate the sky's blue slightly. The desaturation is correct, but the sky also appears slightly darker — the change in saturation has affected the perceived brightness.

**Solution:** Set the Hue/Saturation adjustment layer's blending mode to **Color**.

**Effect:** The hue and saturation changes are applied. The luminance distribution of the Base is preserved exactly. The sky's color changes as intended, but its brightness does not shift.

**INTERPRETATION:** The luminance shift from Hue/Saturation adjustments is often small enough to go unnoticed. But in critical work — skin retouching, product photography, any context where luminance accuracy matters — setting color adjustment layers to Color mode provides a safety guarantee that tonal values will not shift as a side-effect of color manipulation.

### Photographic Application: Overlay/Soft Light for Local Contrast (High Pass Technique)

**Technique:**

1. Stamp visible (Ctrl+Alt+Shift+E / Cmd+Option+Shift+E) or duplicate the target layer.
2. Apply **Filter > Other > High Pass** with a radius of 2-10 pixels (depending on image resolution and desired effect scale).
3. Set the resulting layer's blending mode to **Overlay** or **Soft Light**.

**Why it works:** The High Pass filter removes all tonal information except the local contrast detail — it outputs a 50% gray image with deviations from gray only where local transitions (edges and texture) exist. When this layer is set to Overlay or Soft Light, the 50% gray is neutral (invisible) and only the deviations affect the image — lightening the bright side of edges and darkening the dark side. This enhances local contrast without changing the overall tonal distribution.

**Overlay vs Soft Light for High Pass:** Overlay produces stronger local contrast enhancement. Soft Light produces a subtler effect. Both are valid; the choice depends on how much enhancement the image needs. Reducing the layer's Opacity provides fine control over the intensity.

**RECOMMENDATION:** Convert the stamped layer to a Smart Object before applying High Pass. This makes the High Pass filter a Smart Filter, allowing you to re-edit the radius at any time. Set the Smart Object layer's blend mode to Overlay or Soft Light.

---

## Professional Workflow

### Choosing Blend Modes by Diagnosis

The professional approach to blending modes is diagnostic, not experimental. Before selecting a mode, identify what you need the layer interaction to achieve, then select the mode whose formula produces that effect.

| Diagnosis | Required Interaction | Correct Mode |
|---|---|---|
| "I need to darken the image proportionally" | Multiplicative darkening | Multiply |
| "I need to lighten the image proportionally" | Inverse multiplicative lightening | Screen |
| "I need to increase contrast around the midpoint" | Darken darks + lighten lights | Overlay (strong) or Soft Light (gentle) |
| "I need a non-destructive dodge/burn layer" | 50% gray neutral, lighter = dodge, darker = burn | Soft Light (default) or Overlay (aggressive) |
| "I need to apply a tonal adjustment without color shift" | Luminance-only transfer | Luminosity |
| "I need to apply a color adjustment without tonal shift" | Color-only transfer | Color |
| "I need to add local contrast via High Pass" | 50% gray neutral, edge deviations affect contrast | Overlay or Soft Light |
| "I need the layer to simply sit on top" | Direct replacement, modulated by opacity | Normal |

### Blend Mode Stacking in the Layer Architecture

Building on the layer architecture from Chapter 04, here is how blending modes fit into the professional stack:

**Tonal adjustment layers (Curves, Levels):** Set to **Luminosity** when you want pure tonal control without color side-effects. Keep at **Normal** when you deliberately want both the tonal and color effects of the adjustment.

**Color adjustment layers (Hue/Saturation, Color Balance, Selective Color):** Set to **Color** when you want pure color control without luminance side-effects. Keep at **Normal** when both effects are acceptable.

**Dodge/burn layers:** Set to **Soft Light** (default) or **Overlay** (for stronger effect). The layer content is 50% gray fill with painted deviations.

**Local contrast layers (High Pass):** Set to **Overlay** (standard) or **Soft Light** (gentle). The layer content is a High Pass filtered composite.

**Texture overlays:** Set to **Soft Light** or **Overlay** depending on how aggressively the texture should interact with the image.

**Quick exposure corrections:** Multiply for darkening, Screen for lightening. Use as temporary corrections, not permanent workflow elements — Curves provides better control.

### When to Use Fill Instead of Opacity

For the seven common photographic modes (Normal, Multiply, Screen, Overlay, Soft Light, Luminosity, Color), Fill and Opacity are interchangeable. Use whichever is more convenient.

When working with the Special 8 modes (Color Burn, Linear Burn, Color Dodge, Linear Dodge, Vivid Light, Linear Light, Hard Mix, Difference), always test the Fill slider before settling for Opacity. In most cases, reducing Fill produces a more refined, more photographic-looking result — the effect integrates with the image more naturally, with fewer harsh transitions and less clipping at the extremes.

**INTERPRETATION:** Some advanced dodge/burn practitioners use Vivid Light or Linear Light instead of Soft Light for their 50% gray layers, specifically because reducing Fill on these modes produces a unique quality of tonal shaping that is not achievable with Soft Light or Overlay at any Opacity setting. This is a legitimate advanced technique, but Soft Light remains the correct starting point for most photographers.

---

## Common Mistakes

### 1. Cycling Through Blend Modes Randomly

**The mistake:** Selecting a layer and pressing Shift+Plus to cycle through all 27 blend modes, looking for "something that looks good."

**Why it matters:** This approach treats blending modes as random effects rather than deterministic tools. It produces inconsistent results, wastes time, and prevents learning. You cannot explain or reproduce your result if you found it by accident.

**The fix:** Diagnose what you need (darkening, lightening, contrast, luminance isolation, color isolation) and select the mode that mathematically produces that effect. The diagnostic table in the Professional Workflow section above is a starting reference.

### 2. Using Overlay Instead of Soft Light for Dodge/Burn

**The mistake:** Defaulting to Overlay for 50% gray dodge/burn layers, producing overly aggressive results that are difficult to blend smoothly.

**Why it matters:** Overlay's stronger formula makes each brush stroke more impactful, which sounds desirable but in practice makes it harder to build up subtle, smooth tonal shaping. The result often looks heavy-handed, with visible transitions between dodged and undodged areas.

**The fix:** Use Soft Light as the default for dodge/burn layers. Switch to Overlay only when you specifically need the stronger effect and understand why.

### 3. Forgetting Luminosity Mode on Tonal Adjustments

**The mistake:** Applying Curves or Levels adjustments in Normal mode and not noticing the unwanted saturation shift.

**Why it matters:** In images with vivid colors, a Normal-mode Curves contrast boost can produce noticeable, unwanted color saturation changes. The photographer may then try to "fix" the color with additional adjustments, adding complexity to solve a problem that should have been prevented.

**The fix:** Set tonal adjustment layers (Curves, Levels, Brightness/Contrast) to Luminosity mode when you want the tonal effect only. Make this a default practice and deviate from it only when you specifically want the color side-effect.

### 4. Confusing Overlay and Hard Light

**The mistake:** Assuming Overlay and Hard Light are interchangeable because they use the same formula.

**Why it matters:** The formula is the same, but the conditional test is different. Overlay tests the Base (the underlying image) to decide which branch to apply. Hard Light tests the Blend (the applied layer). With different source images above and below, the two modes produce visibly different results. Using the wrong one produces an effect driven by the wrong layer's tonal structure.

**The fix:** Remember: Overlay follows the image below. Hard Light follows the layer above. For most photographic work, Overlay is what you want — it respects the existing image structure.

### 5. Using Opacity When Fill Would Be Better for Special 8 Modes

**The mistake:** Reducing Opacity on a layer using Color Burn, Vivid Light, or another Special 8 mode, and getting a harsh, unsatisfying result. The photographer concludes the mode is "unusable" and reverts to a simpler mode.

**Why it matters:** The Special 8 modes produce dramatically different results with the Fill slider than with the Opacity slider. Reducing Fill often transforms an apparently harsh, clipping-prone effect into a smooth, usable one.

**The fix:** When using any of the Special 8 modes, always try reducing Fill before adjusting Opacity. Compare the two. In most photographic applications, Fill produces the preferred result.

### 6. Not Understanding Why 50% Gray Is Neutral

**The mistake:** Creating a dodge/burn layer without understanding why it works. Filling with gray because a tutorial said to, without understanding that 50% gray is neutral specifically for the Contrast mode group's formulas. Then attempting to use the same technique with a mode where 50% gray is not neutral (such as Multiply or Screen), and getting unexpected results.

**Why it matters:** In Multiply, 50% gray is not neutral — it darkens the image by 50%. In Screen, 50% gray lightens by a significant amount. Only in the Contrast group (Overlay, Soft Light, Hard Light, and related modes) is 50% gray mathematically neutral. Using the wrong mode for the gray layer technique produces an immediate, unwanted tonal shift across the entire image.

**The fix:** Understand that 50% gray neutrality is a property of specific formulas, not a universal truth. The Contrast modes (Overlay, Soft Light, Hard Light, Vivid Light, Linear Light, Pin Light, Hard Mix) all share this neutral point. Other mode groups do not.

### 7. Applying Blend Modes to Groups Without Understanding Pass Through

**The mistake:** Placing dodge/burn layers or adjustment layers inside a group set to Pass Through (the default) and then changing the group's blend mode to Overlay or Multiply, not realizing this changes the compositing behavior of every layer inside the group.

**Why it matters:** As covered in Chapter 04, a group set to Pass Through is compositing-transparent — its layers interact with the full stack as if the group did not exist. Changing the group's mode to anything else (including Normal) creates an isolated compositing context: the layers inside the group composite among themselves first, and then the group's result composites with layers below using the group's mode. This can dramatically change the visual output in ways that are confusing if you do not understand the mechanism.

**The fix:** Be deliberate about group blend modes. Pass Through is usually correct for organizational groups. Change it only when you specifically want the group to be composited as a unit.

---

## Summary

- A **blending mode** is a per-pixel mathematical formula that combines two values — **Base** (the composite below) and **Blend** (the current layer) — to produce a **Result**. The formula is applied independently at every pixel position.

- **Multiply** (result = base x blend) always darkens. White Blend is neutral. Black Blend produces black. The darkening is proportional — it preserves tonal structure.

- **Screen** (result = 1 - (1-base)(1-blend)) always lightens. Black Blend is neutral. White Blend produces white. Screen is the mathematical complement of Multiply.

- **Overlay** applies Multiply to dark Base areas and Screen to light Base areas, increasing contrast around the midpoint. The conditional tests the **Base** value. 50% gray Blend is neutral.

- **Soft Light** produces a similar contrast effect to Overlay but with a smoother, gentler formula. It is the preferred mode for 50% gray dodge/burn layers because of its more controllable response.

- **Hard Light** uses the same formula as Overlay but tests the **Blend** value. It is the symmetric partner of Overlay — placing layers in Hard Light is equivalent to swapping their positions and using Overlay.

- **Luminosity** mode applies only the tonal values (luminance) of the Blend layer, preserving the Base layer's color. Set tonal adjustment layers to Luminosity to prevent unwanted saturation shifts from contrast changes.

- **Color** mode applies only the color (hue and saturation) of the Blend layer, preserving the Base layer's luminance. Set color adjustment layers to Color to prevent unwanted brightness shifts from color changes.

- **Opacity** and **Fill** produce identical results for most blending modes. For the "**Special 8**" modes (Color Burn, Linear Burn, Color Dodge, Linear Dodge, Vivid Light, Linear Light, Hard Mix, Difference), Fill produces a qualitatively different and often more usable result than Opacity at the same percentage.

- The professional approach to blending modes is diagnostic: identify the required interaction, select the mode whose formula produces it, and predict the result before applying. This replaces trial-and-error cycling with deliberate, repeatable technique.

---

## Exercises

### Exercise 1: Predicting Multiply and Screen Results

Without opening Photoshop, calculate the results for these blend operations. Use the normalized formula (values 0.0-1.0). Then verify your answers in Photoshop by creating two layers with the specified values (using Edit > Fill > Solid Color or painting with a specific brightness) and observing the Info panel.

**Multiply:**

1. Base = 0.8, Blend = 0.5. What is the Result?
2. Base = 1.0, Blend = 0.3. What is the Result?
3. Base = 0.5, Blend = 0.0. What is the Result?

**Screen:**

4. Base = 0.2, Blend = 0.5. What is the Result?
5. Base = 0.0, Blend = 0.7. What is the Result?
6. Base = 0.5, Blend = 0.5. What is the Result?

**Success criterion:** You can calculate the exact result for any Multiply or Screen operation by hand, and your predictions match the Photoshop output (allow for rounding differences in 8-bit conversion).

### Exercise 2: Overlay Neutral Point Verification

1. Open any photograph in Photoshop.
2. Create a new pixel layer above it. Fill with 50% gray (Edit > Fill > 50% Gray).
3. Set the layer's blend mode to Overlay. Observe: the image should appear unchanged.
4. Using the Info panel, hover over several areas and verify that the before/after values are identical or differ by no more than 1 level (8-bit rounding).
5. Now paint a broad stroke of white (100% opacity brush) on the gray layer. Observe the lightening effect.
6. Paint a broad stroke of black on the gray layer. Observe the darkening effect.
7. Repeat steps 3-6 with Soft Light. Compare the intensity of the white and black strokes between the two modes.

**Success criterion:** You can verify that 50% gray is neutral for both Overlay and Soft Light, and you can visually confirm that Overlay produces a stronger effect than Soft Light for the same paint values.

### Exercise 3: Luminosity Mode Isolation

1. Open a photograph with vivid colors (saturated landscape, colorful product shot, or similar).
2. Create a Curves adjustment layer. Apply a strong S-curve (more aggressive than you would normally use — this is for demonstration).
3. Toggle the Curves layer on/off and observe both the contrast change and the saturation increase.
4. Now change the Curves layer's blend mode from Normal to Luminosity.
5. Toggle on/off again. The contrast change should remain. The saturation change should be eliminated.
6. Compare the two states (Normal vs Luminosity) by switching the blend mode back and forth. Focus on the most saturated areas of the image — this is where the difference is most visible.

**Success criterion:** You can see and articulate the difference between a Normal-mode Curves adjustment (which shifts both contrast and color) and a Luminosity-mode Curves adjustment (which shifts contrast only).

### Exercise 4: Opacity vs Fill Comparison

1. Open a photograph. Duplicate the Background layer.
2. Set the duplicate's blend mode to **Vivid Light**.
3. Set Opacity to 25%, Fill to 100%. Observe the result. Take a screenshot or note the appearance.
4. Set Opacity to 100%, Fill to 25%. Observe the result.
5. Compare the two. The results should be visibly different — Opacity produces a harsh, contrasty effect, while Fill produces a smoother, more integrated effect.
6. Now change the blend mode to **Normal** and repeat steps 3-5. In Normal mode, the two results should be visually identical.

**Success criterion:** You can demonstrate the Opacity/Fill distinction for the Special 8 modes and verify that the distinction disappears for Normal mode.

---

### Advanced Exercises

### Advanced Exercise 1: Building a Complete Blend-Mode-Aware Layer Stack

Open a photograph that needs the following corrections: slightly underexposed, slightly flat midtones, and oversaturated colors.

Build a layer stack that addresses all three problems using blending modes deliberately:

1. A Curves adjustment layer for midtone contrast (S-curve), set to **Luminosity** mode to prevent saturation increase.
2. A Curves adjustment layer for exposure brightening (midpoint lift), set to **Luminosity** mode.
3. A Hue/Saturation adjustment layer for saturation reduction, set to **Color** mode to prevent any luminance shift.
4. A 50% gray layer set to **Soft Light** for targeted dodging of the foreground.

Toggle each layer on/off and verify that each adjustment affects only the component you intend — tonal layers do not shift color, the color layer does not shift tones, and the dodge/burn layer produces smooth, controllable results.

**Success criterion:** You have built a layer stack where blending modes provide surgical separation of concerns: tonal changes isolated from color changes, local adjustments isolated from global ones.

### Advanced Exercise 2: High Pass Local Contrast at Different Intensities

1. Open a photograph with rich texture (stone, wood, fabric, or landscape with rock and foliage).
2. Stamp visible. Convert the stamped layer to a Smart Object.
3. Apply **Filter > Other > High Pass**, radius 5 pixels.
4. Set the layer's blend mode to **Overlay**. Observe the local contrast enhancement.
5. Change the blend mode to **Soft Light**. Compare the intensity with Overlay.
6. Return to Overlay. Reduce the Opacity to 50%. Compare this with Soft Light at 100% Opacity. Note whether they look the same — they should not, because the formula differences are qualitative, not just quantitative.
7. Double-click the High Pass Smart Filter and change the radius to 2 pixels. Then to 15 pixels. Observe how the radius changes the spatial scale of the contrast enhancement.

**Success criterion:** You understand that Overlay vs Soft Light for High Pass is not simply a question of intensity (which could be controlled by Opacity), but of tonal distribution. You can select the appropriate mode and radius for different image content and enhancement goals.

### Advanced Exercise 3: Multiply and Screen for Exposure Blending

1. Open a photograph that is moderately underexposed (1-2 stops below optimal).
2. Duplicate the Background. Set the duplicate to **Screen**. Observe: the image lightens.
3. Duplicate the Screen layer. Now you have two Screen layers. Observe: the image lightens further. This is the equivalent of "double screening."
4. Adjust the opacity of each Screen layer independently to find a natural-looking brightness.
5. Now delete both Screen layers. Instead, create a Curves adjustment layer that produces a similar brightening effect. Compare the two approaches.
6. Repeat the entire exercise with an overexposed image, using **Multiply** layers for darkening.

**Success criterion:** You understand the Multiply/Screen exposure correction technique, including its proportional behavior, and you can articulate when Curves offers better control and when the blend mode approach is acceptable.

---

### Blackbelt Challenge

Without opening Photoshop, analyze the following scenario and design a complete solution.

You have a portrait photograph with the following properties:

- Overall exposure is correct, but the subject's face is slightly underlit (approximately 2/3 stop under).
- Midtone contrast is slightly flat across the entire image.
- The background contains vivid blue and orange elements that you do not want to shift during tonal correction.
- There is a subtle light leak in the upper-left corner that adds an unwanted warm/bright area.
- You want to add a subtle texture overlay (a film grain scan) that integrates with the image's tonal structure without looking pasted on.

Design a complete layer stack, specifying:

1. **Every layer** — its type (pixel, adjustment, fill), its content, its purpose, and its blending mode. Explain your blend mode choice for each layer by referencing the formula or behavior that makes it correct.

2. **Luminosity vs Normal mode** — for each adjustment layer, state whether you would use Luminosity mode, Color mode, or Normal mode, and explain why.

3. **The dodge/burn layer** — specify the fill value, blend mode, and why you chose Soft Light vs Overlay. Explain what brush color, opacity, and flow settings you would use for the facial dodge, and predict what happens to the 50% gray layer's pixel values as you paint.

4. **The texture overlay** — specify the blend mode for the grain scan layer. Explain why this mode integrates the grain with the image's existing tonal structure rather than simply sitting on top. Predict what happens to the grain's appearance in the shadow areas vs the highlight areas of the image, and explain why using the formula.

5. **The light leak correction** — describe how you would use a combination of a pixel layer, blending mode, and mask to neutralize the warm bright area without affecting the rest of the image. Consider whether painting black on a layer set to Multiply would achieve this, and explain mathematically why or why not.

6. **Opacity vs Fill decision** — for each layer, state whether you would adjust Opacity, Fill, or either (because they are interchangeable for that mode). For any layer using a Special 8 mode, explain the difference in result between the two sliders.

The test: Your design should demonstrate that every blend mode choice is the result of analysis, not experimentation. A colleague reading your design should be able to predict the visual output of each layer without opening the file.
