---
description: Tonal adjustments in Photoshop — histogram reading for diagnostic tonal evaluation, Levels, Curves, tonal mapping, clipping, contrast curves, and master vs per-channel curve control.
---

# Chapter 07: Tonal Adjustments — Levels and Curves

## Learning Objectives

After completing this chapter, you will be able to:

1. Read a histogram diagnostically and decide what tonal correction, if any, an image requires before choosing a tool.
2. Explain what a Levels adjustment does at the data level: how the black point, white point, and gamma slider remap input values to output values.
3. Explain what a Curves adjustment does at the data level: how it defines a transfer function that remaps every input value to an output value.
4. Describe the mathematical relationship underlying Curves: a 45-degree line means no change, points above the line lighten, points below the line darken.
5. Construct an S-curve to increase midtone contrast and explain why it simultaneously compresses highlight and shadow contrast.
6. Distinguish between the master (composite) curve and per-channel curves, and predict the tonal and color effects of each.
7. Choose appropriately between Levels, Curves, Exposure, and Brightness/Contrast for a given tonal problem, based on what each tool actually does to the data.
8. Apply Levels and Curves as Adjustment Layers within a non-destructive workflow.

---

## Conceptual Foundation

Every photograph has a tonal character — the distribution of light and dark across the frame. Sometimes that character matches your intent. Often it does not: the scene was flat, the exposure was conservative, the RAW development left the midtones lifeless.

Tonal adjustment is the process of changing the relationship between the values in your file and the tones you see on screen and in print. You are remapping numbers: taking an input value (what the pixel is now) and producing an output value (what you want it to become). Every tonal tool in Photoshop — Levels, Curves, Brightness/Contrast, Exposure — performs some variant of this remapping. They differ in how much control they give you over the shape of the remapping function.

Understanding tonal adjustment means understanding three things:

1. **What the image data looks like now** — read the histogram diagnostically (covered in Chapter 01, applied here).
2. **What you want it to look like** — a judgment call that depends on the photograph, the subject, and your creative intent.
3. **What the tool actually does to the numbers** — the transfer function it applies, and the consequences for contrast, clipping, and color.

This chapter teaches Levels and Curves as the two primary tonal adjustment tools in Photoshop. Levels is simple and fast. Curves is the most powerful and precise tonal tool in Photoshop — and arguably the single most important adjustment tool in the application. Once you understand Curves, every other tonal tool becomes a special case of what Curves can do.

---

## Terminology

### Levels Adjustment

**Definition:** A tonal adjustment that remaps pixel values using five control points: input black point, input white point, midtone (gamma) slider, output black level, and output white level. Levels performs a linear stretch (or compression) of the tonal range, with a gamma correction applied to the midtones.

**Meaning in photographic practice:** Levels is the simplest effective tool for correcting images that do not use the full tonal range. A flat photograph from a hazy day — where the histogram shows values bunched in the center with nothing at the extremes — can be corrected quickly with Levels by moving the black point to where the data actually begins and the white point to where it ends. This stretches the occupied range to fill the full tonal scale, restoring contrast.

**Photoshop implementation:** Available as an Adjustment Layer (Layer > New Adjustment Layer > Levels, or the Levels icon in the Adjustments panel) or as a direct adjustment (Image > Adjustments > Levels). The Levels dialog displays the histogram with five sliders: three below the histogram (input black point, midtone gamma, input white point) and two below a gradient bar (output black, output white). A channel dropdown lets you apply Levels to the master (composite RGB) or to individual channels. **RECOMMENDATION:** Always use the Adjustment Layer form for non-destructive editing, as established in Chapter 06.

**Related concepts:** Curves, histogram, black point, white point, gamma, Adjustment Layer (Chapter 06).

**Common misconception:** "Levels and Curves are equivalent tools — use whichever you prefer." They are not equivalent. Levels is a simplified subset of Curves. Everything Levels can do, Curves can do. But Curves can do far more: it allows arbitrary remapping of any tonal value to any other, with independent control over every region of the tonal range. Levels is limited to endpoint adjustment and a single midtone (gamma) control.

---

### Black Point / White Point / Midtone (Gamma) Slider

**Definition:** The three input sliders in the Levels dialog.

- The **black point** (left triangle, default position 0) sets which input value will become pure black (output 0) in the adjusted image. All input values at or below this point are clipped to black.
- The **white point** (right triangle, default position 255 in 8-bit) sets which input value will become pure white (output 255). All input values at or above this point are clipped to white.
- The **midtone slider** (center triangle, also called the gamma slider, default position 1.00) controls the distribution of midtones between the new black and white points. Values greater than 1.00 lighten midtones; values less than 1.00 darken them. The slider applies a gamma (power function) correction to the remapped values.

**Meaning in photographic practice:** The black and white point sliders set the tonal endpoints — they define what "black" and "white" are in the final image. The midtone slider adjusts overall brightness without affecting the endpoints: it redistributes the tones between black and white, making the image lighter or darker in the midtones without clipping more highlights or shadows. This separation of endpoint control from midtone redistribution is what makes Levels useful as a quick first-pass correction.

**Photoshop implementation:** In the Levels Properties panel (when using an Adjustment Layer), the input sliders appear beneath the histogram. Drag the black point to the right to darken shadows and increase contrast. Drag the white point to the left to brighten highlights and increase contrast. The gamma slider moves automatically as you adjust the endpoints, maintaining its relative position. You can also type exact values in the numeric fields. **FACT:** Holding Alt/Option while dragging the black or white point slider activates the clipping preview — Photoshop shows which pixels are clipping and in which channels. This is the same clipping preview described in Chapter 01, and it is the correct way to set endpoints precisely.

**Related concepts:** Clipping (Chapter 01), histogram, Levels adjustment, Output Levels.

**Common misconception:** "Set the black point to where the histogram starts and the white point to where it ends." This is often reasonable as a starting point, but it is not a universal rule. Some images should not have a full-range histogram. A photograph of fog should not have a pure black point. A high-key portrait may not need deep blacks. Always make the endpoint decision based on the image content, not on a histogram shape rule.

---

### Curves Adjustment

**Definition:** A tonal adjustment that defines a transfer function — a mapping from input values to output values — as a freeform curve drawn on a graph. The horizontal axis represents input values (the current pixel values), and the vertical axis represents output values (the values they will be remapped to). You control the shape of this mapping by placing and moving anchor points on the curve.

**Meaning in photographic practice:** Curves is the most precise and flexible tonal control in Photoshop. Where Levels gives you three points of control (black, white, midtone), Curves gives you as many as you need, placed wherever you need them. You can independently adjust highlights, midtones, and shadows. You can increase contrast in one tonal region while decreasing it in another. You can create subtle, targeted tonal shifts that no other single tool can achieve.

**Photoshop implementation:** Available as an Adjustment Layer (Layer > New Adjustment Layer > Curves) or as a direct adjustment (Image > Adjustments > Curves). The Curves dialog displays a graph with the histogram as a background reference. A channel dropdown lets you work on the master (RGB) curve or on individual Red, Green, and Blue curves. Points are placed by clicking on the curve; they are moved by dragging. The on-image adjustment tool (the hand icon with up/down arrows) lets you click on the image itself, identify the tonal value at that location on the curve, and drag to adjust it directly.

**Related concepts:** Transfer function, anchor point, S-curve, master curve, per-channel curves, Levels, histogram.

**Common misconception:** "Curves is too complicated — Levels does the same thing more simply." Levels does a fraction of what Curves does. Levels can set endpoints and adjust the midtone gamma. Curves can do that and also: increase midtone contrast while protecting shadow detail, create a film-like rolloff in highlights, independently brighten quarter-tones while darkening three-quarter-tones, correct per-channel color casts with surgical precision. The learning curve for Curves is steeper, but the tool is not optional for serious photographic editing.

---

### Input vs Output (in Curves)

**Definition:** In the Curves graph, the horizontal axis represents **input values** (the pixel's current value before the adjustment) and the vertical axis represents **output values** (the value the pixel will be mapped to after the adjustment). A point on the curve at coordinates (100, 150) means: "every pixel that currently has a value of 100 will become 150."

**Meaning in photographic practice:** This input-to-output mapping is the fundamental concept underlying Curves. When you drag a point on the curve upward, you are telling Photoshop: "make pixels at this input value brighter (higher output)." When you drag downward: "make them darker (lower output)." Understanding this relationship means you can predict exactly what a curve shape will do before you apply it.

**Photoshop implementation:** As you hover over the curve, the Info panel (if open) or the curve dialog itself displays the input and output values at the cursor position. When you place an anchor point and drag it, both values are shown. The bottom-left corner of the graph is (0, 0) — black maps to black. The top-right corner is (255, 255) in 8-bit — white maps to white. The default state is a straight diagonal line from (0, 0) to (255, 255): every input value maps to the same output value. No change.

**Related concepts:** Transfer function, anchor point, Curves adjustment, tonal mapping.

**Common misconception:** "The horizontal axis is shadows and the vertical axis is highlights." This is incorrect. Both axes represent the full range from black to white. The horizontal axis is the input (before) and the vertical axis is the output (after). A point in the lower-left region of the graph affects dark tones; a point in the upper-right region affects bright tones — but this is because of where those tones fall along the input axis, not because the axes represent shadows and highlights respectively.

---

### Anchor Point (on a Curve)

**Definition:** A control point placed on the Curves graph that pins the curve at a specific input-output coordinate. The curve passes through all anchor points and interpolates smoothly between them.

**Meaning in photographic practice:** Anchor points let you target specific tonal regions for adjustment while leaving other regions unchanged. When you want to brighten the midtones without affecting the highlights, you place an anchor point in the highlights to lock them in place, then drag the midtone region of the curve upward. Without the anchor, moving the midtones would shift the highlights as well because the curve interpolates continuously.

**Photoshop implementation:** Click on the curve to add an anchor point. Drag it to adjust. Select a point and press Delete/Backspace to remove it. Ctrl+click / Cmd+click on the image (or use the on-image adjustment tool) to place a point at the tonal value of the pixel under the cursor — this is the most reliable way to target a specific tone, because it tells you exactly where on the curve that tone lives. You can place up to 14 anchor points on a single curve in Photoshop, though more than 4-5 rarely produces useful results.

**Related concepts:** Curves adjustment, input vs output, S-curve, interpolation.

**Common misconception:** "More anchor points give more control." Technically true, but practically counterproductive. Each additional point constrains the curve further, making it harder to achieve a smooth shape. Most professional tonal adjustments use 2-4 anchor points. An adjustment requiring more than 5 points is usually a sign that you should split the work into multiple Curves Adjustment Layers, each targeting a different tonal problem.

---

### S-Curve (Contrast Curve)

**Definition:** A curve shape that brightens highlights (pulling the upper portion above the diagonal) and darkens shadows (pulling the lower portion below the diagonal), forming an S-shape. The result is increased contrast in the midtones — the tonal region where the curve is steepest — at the cost of reduced contrast in the extreme highlights and shadows, where the curve is flattened.

**Meaning in photographic practice:** The S-curve is the most common Curves adjustment in photographic editing. It adds "punch" to images by stretching the midtone range and compressing the extremes. The visual effect is increased perceived contrast and saturation (because contrast enhancement in RGB also increases the apparent difference between color channels). The degree of the S determines the intensity of the effect — a gentle S produces a subtle refinement; an aggressive S can create harsh contrast and clipped extremes.

**Photoshop implementation:** To create a basic S-curve: place an anchor point in the lower-midtone region (approximately input 64 in 8-bit) and drag it slightly downward. Place another in the upper-midtone region (approximately input 192) and drag it slightly upward. The resulting S-shape steepens the curve through the midtones while flattening it at the extremes. The center of the curve (input 128) typically remains near its original position, serving as a pivot.

**Related concepts:** Midtone contrast, contrast, anchor point, Curves adjustment.

**Common misconception:** "An S-curve increases contrast everywhere." It does not. An S-curve increases contrast only in the midtones (where the curve is steeper than the 45-degree diagonal). It simultaneously decreases contrast in the highlights and shadows (where the curve is flatter). This is why aggressive S-curves can make highlights look flat and blocked and shadows look crushed — you have compressed those regions to make room for the expanded midtone range.

---

### Master Curve vs Per-Channel Curves

**Definition:** The **master curve** (also called the composite or RGB curve) applies the same tonal remapping to all three color channels simultaneously. **Per-channel curves** apply independent remapping to individual channels — the Red curve affects only the Red channel, the Green curve only the Green channel, and the Blue curve only the Blue channel.

**Meaning in photographic practice:** The master curve is primarily a luminosity control — it changes brightness and contrast. However, because it operates on gamma-encoded RGB data, it also shifts color: brightening midtones on the master curve can produce a very slight desaturation effect, and an aggressive master S-curve increases apparent saturation because it exaggerates the differences between channels. Per-channel curves are color adjustment tools: they shift the color balance of specific tonal regions. A landscape photographer might use the Blue channel curve to add warmth to the shadows (pulling the lower Blue curve down, shifting shadows toward yellow) while keeping the sky blue (leaving the upper Blue curve untouched).

**Photoshop implementation:** In the Curves Properties panel, a dropdown at the top lets you select RGB (master), Red, Green, or Blue. Each channel has its own independent curve. All four curves (master + three channels) are active simultaneously. **VERSION NOTE:** In Photoshop CC and later, the Curves Adjustment Layer properties panel shows a small color swatch next to the channel name, making it easier to identify which channel you are editing.

**Related concepts:** Curves adjustment, RGB channels (Chapter 01), color correction (Chapter 09), color cast.

**Common misconception:** "The master RGB curve is the same as adjusting each channel curve by the same amount." It is not precisely the same. The master curve operates on the composite — Photoshop applies the same remapping function to all three channel values at each pixel. If you manually place identical adjustments on each of the three channel curves independently, you get a similar but not always identical result, because the master curve application is a single pass while three independent channel adjustments are computed sequentially within the same Adjustment Layer. In practice, the difference is usually negligible, but the conceptual distinction matters: the master curve treats luminosity as the primary concern, while channel curves treat color as the primary concern.

---

### Contrast

**Definition:** Contrast is the difference in luminance (or tonal value) between different parts of an image. An image with high contrast has a wide range of tones, with strong separation between light and dark areas. An image with low contrast has a narrow range of tones, with muted differences between light and dark.

**Meaning in photographic practice:** Contrast is not a single number — it is a characteristic of the tonal distribution. A photograph can have high overall contrast (deep blacks, bright whites) but flat midtones. It can have compressed endpoints but vivid midtone separation. Understanding contrast means understanding where in the tonal range the separation occurs and whether that matches your intent.

**Photoshop implementation:** Contrast is not a single control in Photoshop but the result of how tonal values are distributed. Every tonal tool affects contrast: Levels adjusts endpoint contrast. Curves can target contrast in any specific tonal region. The Brightness/Contrast adjustment provides a simplified global control. Clarity and Texture (in Camera Raw) affect local contrast at different spatial scales.

**Related concepts:** Midtone contrast, local contrast (Chapter 08), S-curve, tonal range, histogram.

**Common misconception:** "Contrast means making blacks blacker and whites whiter." That describes only one type of contrast — endpoint or global contrast. Midtone contrast (the separation between tones in the middle range) is often more visually important than endpoint contrast. An image can have full-range endpoints but still look flat if the midtones are compressed.

---

### Local Contrast (vs Global Contrast)

**Definition:** **Global contrast** describes the overall tonal range of the image — the difference between the darkest and brightest values. **Local contrast** (also called micro-contrast or detail contrast) describes the tonal differences between adjacent or nearby pixels — the tonal variation within small regions of the image.

**Meaning in photographic practice:** An image can have good global contrast (full range from black to white) but poor local contrast (fine details within midtone regions appear mushy and indistinct). Increasing local contrast enhances the perception of texture, detail, and three-dimensionality without necessarily changing the overall tonal range. This is what the Clarity slider in Camera Raw/Lightroom does, and it is the subject of Chapter 08.

**Photoshop implementation:** Global contrast is controlled by Levels, Curves, and Brightness/Contrast. Local contrast enhancement involves techniques that increase tonal variation at a spatial scale — affecting neighboring pixels differently based on their surroundings. This is covered in detail in Chapter 08, which addresses dodging and burning, Clarity-style techniques, and luminosity-based local adjustments. The distinction is introduced here because global tonal adjustments (this chapter) and local tonal adjustments (Chapter 08) solve different problems and should not be confused.

**Related concepts:** Contrast, midtone contrast, Clarity (Chapter 08), Unsharp Mask at large radius (Chapter 08), dodging and burning (Chapter 08).

**Common misconception:** "Increasing the Curves S-curve adds local contrast." A Curves S-curve on the master curve is a global adjustment — it applies the same remapping function to every pixel regardless of its spatial surroundings. It increases midtone contrast globally, not locally. Two pixels with the same input value will get the same output value regardless of whether they are in a flat sky or a textured rock face. Local contrast techniques, by contrast, adjust pixels differently based on what surrounds them.

---

### Midtone Contrast

**Definition:** The degree of tonal separation specifically within the midtone range — roughly the central third to half of the tonal scale. Midtone contrast is distinct from overall (global) contrast, which measures the full range from black to white.

**Meaning in photographic practice:** Midtone contrast is often the most visually impactful form of contrast in a photograph. Most of the important subject detail — skin tones, foliage, architectural surfaces, the subtle gradations in clouds — falls in the midtone range. An image with compressed midtones looks flat and lifeless even if it has deep blacks and bright whites. Increasing midtone contrast makes the image appear more vivid, more three-dimensional, and more detailed.

**Photoshop implementation:** Midtone contrast is most effectively controlled with Curves by creating an S-curve that steepens the midtone portion of the curve. The steeper the midtone segment, the greater the separation between values in that range. You can also target midtone contrast with the midtone slider in Levels (the gamma slider), though with less precision. The Brightness/Contrast adjustment (with "Use Legacy" unchecked) applies a roughly midtone-weighted contrast adjustment that produces acceptable results for simple corrections.

**Related concepts:** S-curve, Curves, contrast, local contrast (Chapter 08), Clarity.

**Common misconception:** "Midtone contrast is the same as the Clarity slider." Clarity operates on local contrast at a specific spatial frequency — it enhances tonal differences between neighboring regions, not the global midtone range. A Curves S-curve affects midtone contrast globally (every pixel in the midtone range is remapped the same way regardless of spatial context). Clarity affects midtone contrast locally (pixels are adjusted based on their surroundings). Both target midtones, but through fundamentally different mechanisms.

---

### Exposure Adjustment (Photoshop Adjustment Layer)

**Definition:** A Photoshop Adjustment Layer that simulates photographic exposure change. It operates primarily in linear light (internally converting values, applying the adjustment, and converting back), which means it affects highlights more dramatically than shadows — mimicking the behavior of changing exposure in-camera.

**Meaning in photographic practice:** The Exposure adjustment is designed for situations where you want to simulate a different exposure — for example, recovering an image that was underexposed by half a stop. Because it operates in a linear-light manner, increasing Exposure brightens highlights significantly more than shadows, just as adding exposure in-camera would. This behavior differs from Curves, which operates on gamma-encoded values, and from Brightness, which uses a perceptually weighted algorithm.

**Photoshop implementation:** Available as an Adjustment Layer (Layer > New Adjustment Layer > Exposure) or as a direct adjustment. Three sliders: **Exposure** (shifts the entire tonal range, primarily affecting highlights, measured in stops), **Offset** (shifts the entire tonal range uniformly, primarily affecting shadows and midtones), **Gamma Correction** (applies a power function to adjust midtone brightness). **INTERPRETATION:** Despite its name suggesting camera-like behavior, the Exposure adjustment was designed primarily for 32-bit HDR workflows. In 8-bit and 16-bit modes, it works but can produce harsh results because it is applying linear-light math to gamma-encoded data. For most photographic tonal correction in 8-bit or 16-bit files, Curves produces smoother, more controllable results.

**Related concepts:** Curves, Brightness/Contrast, linear vs gamma-encoded data (Chapter 01), HDR workflow.

**Common misconception:** "The Exposure adjustment is the best way to fix underexposure in Photoshop." For rendered files (JPEG, TIFF, PSD) in 8-bit or 16-bit mode, Curves typically provides superior control. The Exposure adjustment's linear-light behavior can push highlights toward clipping aggressively. For RAW files, the Exposure slider in Camera Raw is the appropriate place to correct exposure, because it operates on the raw, linear sensor data before rendering. The Photoshop Exposure adjustment is not the same operation as the Camera Raw Exposure slider.

---

### Brightness/Contrast Adjustment (Photoshop Adjustment Layer)

**Definition:** A simplified tonal adjustment with two sliders: Brightness (shifts midtone values lighter or darker) and Contrast (expands or compresses the tonal range around the midpoint). In its modern (non-legacy) form, it uses a perceptually weighted algorithm that reduces the risk of clipping compared to a simple linear shift.

**Meaning in photographic practice:** Brightness/Contrast is the simplest tonal tool in Photoshop. It is suitable for minor, quick corrections where precise tonal control is not required. The modern algorithm (with "Use Legacy" unchecked) produces reasonable results for gentle adjustments — it is not the blunt destructive instrument it was in older versions of Photoshop.

**Photoshop implementation:** Available as an Adjustment Layer or direct adjustment. Two sliders: Brightness (-150 to +150) and Contrast (-50 to +100). A "Use Legacy" checkbox switches between the modern algorithm and the pre-CS3 behavior.

**FACT:** With "Use Legacy" **unchecked** (the default in modern Photoshop), the Brightness slider applies a non-linear adjustment that is weighted toward the midtones and rolls off at the extremes, reducing clipping. The Contrast slider expands or compresses the tonal range in a perceptually reasonable way.

**FACT:** With "Use Legacy" **checked**, the Brightness slider applies a simple linear shift to all values (adding or subtracting the same number from every pixel), which easily clips highlights or shadows. The Contrast slider applies a linear scaling around the midpoint, which also clips aggressively. The legacy mode is the behavior from Photoshop CS2 and earlier, and it is almost always inferior to the modern mode.

**Related concepts:** Levels, Curves, Exposure adjustment, midtone contrast.

**Common misconception:** "Brightness/Contrast is a beginner tool that professionals never use." The legacy version was genuinely poor — a blunt linear shift that clipped easily. The modern version (default since CS3) is a reasonable tool for minor corrections. Professional photographers typically prefer Curves for the precision it offers, but the modern Brightness/Contrast is not destructive in the way the legacy version was, and it has legitimate uses for quick, gentle adjustments.

---

## Theory

### What Curves Actually Does: Remapping via Transfer Function

A Curves adjustment defines a **transfer function** — a mathematical mapping from input values to output values. For every possible pixel value in the image, the curve specifies what that value should become.

Visualize the Curves graph:

- The **horizontal axis** represents input values (0 at left, 255 at right in 8-bit).
- The **vertical axis** represents output values (0 at bottom, 255 at top).
- The **curve** is the function that maps each input to its output.

**The default state** is a straight line from the bottom-left corner (0, 0) to the top-right corner (255, 255), running at 45 degrees. This line is the identity function: every input value maps to the same output value. No change.

**FACT:** The 45-degree diagonal represents no change. Any deviation from this line is a tonal modification.

Every other curve shape can be understood in terms of deviation from this diagonal:

| Curve Position | Effect | Why |
|----------------|--------|-----|
| Point **above** the diagonal | Lightening | Output value is higher than input value — pixel becomes brighter |
| Point **below** the diagonal | Darkening | Output value is lower than input value — pixel becomes darker |
| Curve **steeper** than 45 degrees | Increased contrast in that tonal region | A steeper slope means small input differences produce larger output differences — tones are spread further apart |
| Curve **flatter** than 45 degrees | Decreased contrast in that tonal region | A flatter slope means input differences are compressed — tones are pushed closer together |
| Horizontal segment (slope = 0) | Complete tonal compression | All input values in that range map to the same output value — all detail in that range is destroyed |
| Vertical segment or reversal | Inversion / solarization | Values are reversed — lighter inputs produce darker outputs. Rarely intentional in photographic editing |

### The S-Curve: Why It Increases Midtone Contrast

The S-curve is not a mysterious aesthetic effect. It is a direct consequence of the mathematics of the transfer function.

An S-curve has three regions:

1. **Lower portion** (shadows): the curve is pulled below the diagonal, making shadows darker. The slope in this region is **flatter** than 45 degrees, meaning shadow contrast is **reduced**.
2. **Middle portion** (midtones): the curve transitions from below the diagonal to above it, passing through the center. The slope in this region is **steeper** than 45 degrees, meaning midtone contrast is **increased**.
3. **Upper portion** (highlights): the curve is pulled above the diagonal, making highlights brighter. The slope in this region is **flatter** than 45 degrees, meaning highlight contrast is **reduced**.

The total "amount" of contrast is redistributed. Contrast does not appear from nowhere — the S-curve takes contrast away from the shadows and highlights and concentrates it in the midtones. This is why an aggressive S-curve makes shadows look crushed and highlights look flat: you have compressed those regions to pay for the midtone expansion.

**INTERPRETATION:** The S-curve is popular because the midtones typically contain the most photographically important detail. Sacrificing some extremity detail for midtone presence is usually a favorable trade. But this is a creative decision, not a universal improvement.

### Levels as a Three-Point Simplification of Curves

Levels is not a separate mathematical operation from Curves. It is a simplified interface to a specific, constrained type of Curves adjustment.

A Levels adjustment with black point set to 20 and white point set to 230 (gamma = 1.00) is mathematically equivalent to a Curves adjustment with two anchor points: one at input 20 mapping to output 0, and one at input 230 mapping to output 255, with a straight line between them.

Moving the midtone (gamma) slider away from 1.00 bends this line into a curve — specifically, it applies a power function (gamma correction) that bows the midtone region. Gamma > 1.00 bows the curve upward (lightening midtones). Gamma < 1.00 bows it downward (darkening midtones).

This is why everything Levels can do, Curves can also do:

| Levels Operation | Curves Equivalent |
|------------------|-------------------|
| Move black point to 20 | Place anchor at (20, 0) |
| Move white point to 230 | Place anchor at (230, 255) |
| Gamma slider > 1.00 (lighten midtones) | Bow the midtone region upward above the straight line |
| Gamma slider < 1.00 (darken midtones) | Bow the midtone region downward below the straight line |
| Output Levels: set output black to 20 | Move the (0, 0) endpoint up to (0, 20) — shadow floor |
| Output Levels: set output white to 230 | Move the (255, 255) endpoint down to (255, 230) — highlight ceiling |

What Levels cannot do that Curves can: place a point specifically at input 75 and raise it, while leaving input 50 and input 100 unchanged. Target a specific tonal region with independent control on each side. Create an S-shape. Manipulate the curve freely at any position along the tonal range.

### Per-Channel Curves and Color

When you adjust the master (RGB) curve, the same remapping applies to all three channels. The primary visual effect is a change in brightness and contrast, with secondary color shifts.

When you adjust a per-channel curve, you change values in one channel while the other two remain unchanged at the input position. This directly shifts color:

| Channel Curve Action | Color Effect |
|---------------------|--------------|
| Raise the Red curve (increase Red output) | Shift toward Red |
| Lower the Red curve (decrease Red output) | Shift toward Cyan (Red's complement) |
| Raise the Green curve | Shift toward Green |
| Lower the Green curve | Shift toward Magenta (Green's complement) |
| Raise the Blue curve | Shift toward Blue |
| Lower the Blue curve | Shift toward Yellow (Blue's complement) |

These shifts are complementary pairs because reducing one RGB channel is equivalent to increasing the relative strength of the other two — which is how subtractive color mixing works.

**FACT:** Per-channel curves can target color shifts to specific tonal ranges. Pulling down the lower portion of the Blue curve (reducing Blue in the shadows) warms only the shadows, shifting them toward yellow, while leaving the highlights untouched. This is the technical mechanism behind "split toning" and selective color grading in Curves, covered in detail in Chapter 10.

### Why Curves Is the Most Important Tonal Tool in Photoshop

Curves is not just one tool among many. It is the general-purpose transfer function editor. Every other tonal adjustment in Photoshop — Levels, Brightness/Contrast, Exposure — can be expressed as a specific Curves shape. Curves can express all of them and infinitely more.

This does not mean you should always use Curves. Levels is faster for simple endpoint corrections. Brightness/Contrast is acceptable for quick global tweaks. But when you need precision, when you need to target a specific tonal region, when you need to adjust color through tonal remapping — Curves is the tool, and there is no substitute.

---

## Photoshop Implementation

### Levels: Detailed Walkthrough

**Opening Levels as an Adjustment Layer:** Click the Levels icon in the Adjustments panel, or go to Layer > New Adjustment Layer > Levels. The Properties panel displays the Levels controls.

**The histogram display:** The histogram in Levels shows the distribution of tonal values in the layer(s) below the adjustment. It is the same histogram as the Histogram panel but displayed within the Levels interface for immediate reference.

**Input sliders (below the histogram):**

| Slider | Position | Effect |
|--------|----------|--------|
| Black point (left triangle) | Default: 0 | Drag right to clip shadows and increase contrast. All input values at or below this point become output 0 (black). |
| Midtone / Gamma (center triangle) | Default: 1.00 | Drag left to lighten midtones (gamma > 1). Drag right to darken midtones (gamma < 1). Does not affect the endpoints. |
| White point (right triangle) | Default: 255 | Drag left to clip highlights and increase contrast. All input values at or above this point become output 255 (white). |

**Output sliders (below the output gradient):**

| Slider | Position | Effect |
|--------|----------|--------|
| Output black (left) | Default: 0 | Raise to set a shadow floor — the darkest output value will be this number instead of 0. Reduces contrast; useful for preventing ink-clogging blacks in print output. |
| Output white (right) | Default: 255 | Lower to set a highlight ceiling — the brightest output value will be this number instead of 255. Reduces contrast. |

**Clipping preview:** Hold Alt/Option while dragging the black point or white point slider. The image view changes to show which pixels are clipping:

- When dragging the **black point**: the image starts white. As you drag right, colored pixels appear — these are the pixels that are being clipped. Pixels appearing in color (red, green, blue, cyan, magenta, yellow) indicate clipping in individual channels. Pixels appearing in black indicate clipping in all channels (complete shadow loss).
- When dragging the **white point**: the image starts black. As you drag left, colored pixels appear — these are clipping. Pixels appearing in white indicate clipping in all channels (complete highlight loss).

**Channel operation:** The dropdown at the top of the Levels panel lets you select RGB (all channels together), or Red, Green, or Blue individually. Per-channel Levels is an effective way to remove a simple color cast: if the image has a blue color cast, switch to the Blue channel and move the midtone slider slightly to the right (reducing Blue in the midtones). However, per-channel Curves offers more control and is covered in Chapter 09.

**The Auto button:** Levels includes an Auto button that applies an automatic tonal correction. The algorithm can be configured by Alt/Option-clicking the Auto button, which opens the Auto Color Correction Options dialog. Options include Enhance Monochromatic Contrast, Enhance Per Channel Contrast, and Find Dark & Light Colors, each with configurable target values and clipping percentages.

**INTERPRETATION:** The Auto correction options can be useful as a starting point, particularly the "Find Dark & Light Colors" algorithm with conservative clipping percentages (0.1% or less). However, they make assumptions about what the image should look like, and they cannot know your creative intent. Use them as a starting point if useful, not as a final answer.

### Curves: Detailed Walkthrough

**Opening Curves as an Adjustment Layer:** Click the Curves icon in the Adjustments panel, or go to Layer > New Adjustment Layer > Curves. The Properties panel displays the Curves graph.

**The graph:** The Curves graph is a square coordinate system.

- **Horizontal axis (bottom):** Input values, from 0 (left) to 255 (right) in 8-bit.
- **Vertical axis (left):** Output values, from 0 (bottom) to 255 (top) in 8-bit.
- **Background:** A faint histogram showing the tonal distribution of the underlying layers.
- **Default curve:** A straight 45-degree diagonal from (0, 0) to (255, 255).

**FACT:** The orientation of the Curves graph (blacks at bottom-left vs top-right) can be toggled by clicking the small toggle arrow at the bottom of the curve grid area. By default in Photoshop, black is at the bottom-left. Some older versions or printing-oriented workflows used the inverted orientation (ink percentage: 0% at left, 100% at right). This manual assumes the default Photoshop orientation (light values at top-right).

**Placing anchor points:** Click anywhere on the curve to add a point. The point pins the curve at that location — tones at that input value will map to the output value you set by the point's vertical position.

**Moving anchor points:** Drag a placed point to change its output value. Use arrow keys for precise 1-level nudges. Hold Shift+arrow for 10-level jumps.

**Removing anchor points:** Select a point and press Delete or Backspace. Alternatively, drag the point off the graph area.

**The on-image adjustment tool:** Click the hand icon with arrows (or press Ctrl+Alt+Shift+click / Cmd+Option+Shift+click on the image — check your current version for the exact modifier keys). With this tool active, click on any area of the image. Photoshop identifies the tonal value at that location, shows you where it falls on the curve, and lets you drag up or down to adjust that region. This is the fastest way to target a specific tone: click on the area you want to change, then drag.

**RECOMMENDATION:** The on-image adjustment tool eliminates guesswork. Instead of estimating which part of the curve corresponds to the skin tone, the cloud, or the shadow you want to adjust, click directly on it. The tool places a point at the exact tonal value.

**Channel selection:** The dropdown at the top of the Curves panel lets you select RGB (master), Red, Green, or Blue. When you switch channels, the curve you draw affects only that channel. All four curves (master + three channels) are applied simultaneously.

**Preset curves:** The Curves panel includes a Preset dropdown with common curve shapes (Darker, Lighter, Increase Contrast, Linear Contrast, Medium Contrast, Strong Contrast, and others). These can be useful for learning what standard curve shapes look like, but they are generic starting points — not tuned to any specific image.

**The pencil tool:** A pencil icon at the top of the graph lets you draw a freeform curve rather than placing points. This is rarely useful for photographic editing — it produces jaggy, discontinuous curves that cause posterization. It exists primarily for special effects and technical applications.

**RECOMMENDATION:** Use the point-based curve (the default), not the pencil tool, for all photographic tonal adjustments.

### Levels vs Curves: Choosing the Right Tool

| Situation | Recommended Tool | Why |
|-----------|-----------------|-----|
| Image does not use full tonal range — histogram shows gaps at both ends | Levels | Simple endpoint correction is all that is needed. Fast and direct. |
| Flat midtones after setting endpoints | Curves | Need to steepen the midtone region without moving the endpoints. Levels cannot do this — its gamma slider adjusts midtone brightness, not midtone contrast. |
| Need to brighten only the quarter-tones without affecting midtones or highlights | Curves | Only Curves lets you target a specific tonal region with anchored adjacent regions. |
| Color cast removal | Curves (per-channel) | Per-channel Curves lets you target the cast in specific tonal ranges. Chapter 09 covers this in detail. |
| Quick global brightness adjustment with minimal risk of clipping | Brightness/Contrast or Curves | Brightness/Contrast is faster. Curves gives more control if the simple adjustment is not adequate. |
| Quick endpoint correction as the first step before more refined Curves work | Levels | Many photographers use Levels for endpoints, then Curves for midtone shaping. This two-step approach separates concerns. |

### Exposure vs Brightness/Contrast vs Curves: Different Operations

These three adjustments are not interchangeable. They use different algorithms and produce different results, even when attempting to achieve the same apparent effect.

| Adjustment | Algorithm | Primary Effect | Best Used For |
|------------|-----------|----------------|---------------|
| Exposure | Linear-light shift (internally converts to linear, applies adjustment, converts back) | Primarily affects highlights; simulates photographic exposure change | 32-bit HDR workflows; simulating exposure compensation |
| Brightness/Contrast (modern) | Perceptually weighted nonlinear adjustment | Weighted toward midtones; rolls off at extremes to reduce clipping | Quick, simple corrections where precise control is not needed |
| Brightness/Contrast (legacy) | Linear additive shift (brightness) and linear scaling (contrast) | Uniform shift across all values; clips aggressively | Almost never — the legacy mode exists for backward compatibility |
| Curves | User-defined transfer function | Whatever you draw — full control over every region | Precision tonal correction, contrast targeting, color correction, creative grading |

**FACT:** Increasing Exposure by +1.00 in the Exposure adjustment does not produce the same result as raising the Curves midpoint. The Exposure adjustment simulates adding one stop of light, which in linear space doubles all values. After the linear-to-gamma round trip, highlights are affected dramatically while shadows shift only modestly. A Curves adjustment raising the midpoint affects gamma-encoded values directly, producing a more evenly distributed brightening across the tonal range.

### The "Use Legacy" Checkbox in Brightness/Contrast

**FACT:** The Brightness/Contrast adjustment includes a "Use Legacy" checkbox. When checked, it reverts to the pre-CS3 algorithm.

| Mode | Brightness Operation | Contrast Operation | Quality |
|------|--------------------|--------------------|---------|
| Modern (Use Legacy unchecked) | Nonlinear, midtone-weighted, clips less | Nonlinear, weighted, clips less | Acceptable for simple adjustments |
| Legacy (Use Legacy checked) | Adds a constant to every pixel value | Linear scaling around midpoint | Clips aggressively; inferior to every alternative |

**RECOMMENDATION:** Never check "Use Legacy" unless you are deliberately reproducing a pre-CS3 effect for compatibility or artistic reasons. The legacy brightness is a destructive linear shift that easily clips highlights (when brightening) or shadows (when darkening). The modern algorithm is superior in every practical respect.

---

## Professional Workflow

### Where Tonal Adjustments Fit in the Layer Stack

In a non-destructive workflow (Chapter 06), tonal adjustments are Adjustment Layers placed in a specific position in the layer stack:

1. **Smart Object** (base photograph) — bottom of stack.
2. **Retouching layers** (sensor spots, blemishes) — above the base.
3. **Tonal adjustments** — above the retouching layers. Typically a Levels Adjustment Layer for endpoints, followed by one or more Curves Adjustment Layers for contrast and midtone control.
4. **Color corrections** — above the tonal adjustments (Chapter 09).
5. **Color grading** — above color correction (Chapter 10).
6. **Local adjustments** — dodging, burning, local contrast (Chapter 08).

**RECOMMENDATION:** Group your tonal Adjustment Layers into a layer group named "Tonal" or "Tone." This keeps the layer stack organized and lets you toggle all tonal adjustments on/off with a single click for before/after comparison.

### The Diagnostic Workflow: Histogram First, Tool Second

Before applying any tonal adjustment, read the histogram. This is the diagnostic step that prevents unnecessary or counterproductive adjustments.

1. **Open the Histogram panel** (Window > Histogram) in All Channels View.
2. **Refresh the histogram** if the cache warning (yellow triangle) is showing.
3. **Diagnose the tonal distribution:**
    - Does the image use the full tonal range, or is it bunched in a portion of the range?
    - Is there clipping at either end? In which channels?
    - Is the midtone region compressed or expanded?
    - Does the histogram match the photographic intent (e.g., a high-key image should be right-heavy)?
4. **Decide what correction, if any, is needed.** Not every image needs tonal correction. A deliberately flat image (fog, haze) or a deliberately dark image (low-key portrait) may be exactly right as-is.
5. **Choose the tool based on the diagnosis:**
    - Endpoints only: Levels.
    - Midtone contrast or targeted tonal shaping: Curves.
    - Quick global shift: Brightness/Contrast.
    - All of the above: Levels for endpoints, then Curves for everything else.

### Using the Info Panel During Tonal Adjustments

**RECOMMENDATION:** Keep the Info panel (Window > Info) open while making tonal adjustments. When a Curves or Levels Adjustment Layer is selected, the Info panel shows two values for each pixel as you hover: the value before the adjustment and the value after. This lets you verify exactly what the adjustment is doing to specific tones.

Place Color Sampler points (using the Color Sampler tool, nested under the Eyedropper) on critical areas — a skin tone highlight, a shadow with important detail, a neutral gray reference — and monitor how their values change as you adjust the curve.

### Combining Levels and Curves

A common professional approach is to use both tools, each for its strength:

1. **Levels first:** Set the black point and white point to establish the tonal endpoints. Use the clipping preview (Alt/Option+drag) to set them precisely without destroying important detail.
2. **Curves second:** With the endpoints established, use Curves to shape the tonal distribution within that range. Add midtone contrast with a gentle S-curve. Brighten specific tonal regions. Darken others.

This two-step approach separates endpoint correction (Levels) from tonal shaping (Curves), making each adjustment simpler and more targeted.

---

## Step-by-Step Example: Correcting a Flat Landscape

This example walks through a common real-world correction: a landscape photograph that appears flat and lifeless due to haze, conservative exposure, or flat RAW development.

**Starting condition:** A landscape image where the histogram shows values bunched in the center of the range, with no data near the black or white endpoints. The image looks washed out, with muted contrast and dull midtones.

### Step 1: Diagnose with the Histogram

Open the Histogram panel (Window > Histogram) in Expanded View or All Channels View. Observe:

- The left edge of the histogram (shadow end) has empty space — the darkest pixels are nowhere near black.
- The right edge (highlight end) has empty space — the brightest pixels are nowhere near white.
- The data is concentrated in the middle, with a roughly even distribution.
- No clipping is present at either end.

**Diagnosis:** The image does not use the full tonal range. The correct first step is endpoint correction — establishing a proper black point and white point.

### Step 2: Set Endpoints with Levels

1. Create a Levels Adjustment Layer (Adjustments panel > Levels icon). Name it "Levels - Endpoints."
2. Hold Alt/Option and drag the **black point** slider (left triangle) to the right. The image goes white. As you drag, colored pixels begin to appear — these are the first pixels to clip. Stop just before important shadow detail begins to clip. Note the input value (for example, input level 15).
3. Release Alt/Option. The shadow end of the histogram now starts at a value that will map to black.
4. Hold Alt/Option and drag the **white point** slider (left triangle under the histogram's right side) to the left. The image goes black. As you drag, bright pixels appear. Stop just before important highlight detail clips. Note the input value (for example, input level 240).
5. Evaluate the midtone brightness. If the image now looks too dark or too light overall, adjust the **gamma (midtone) slider**. Drag left to lighten midtones, right to darken.

**Result after Levels:** The histogram now spans the full range from near-black to near-white. The image has regained overall contrast, but the midtone separation may still be flat.

### Step 3: Shape Midtones with Curves

1. Create a Curves Adjustment Layer above the Levels layer. Name it "Curves - Contrast."
2. Ensure the channel dropdown is set to **RGB** (master curve).
3. Identify the tonal values you want to affect. Use the on-image adjustment tool (hand icon with arrows) to click on a midtone shadow area (for example, a shaded hillside). Photoshop places a point on the curve at that value — perhaps around input 60.
4. Click on a midtone highlight area (for example, a sunlit meadow). Photoshop places a second point — perhaps around input 190.
5. Drag the lower point (input ~60) slightly **downward** — darkening the lower midtones. A shift of 5-10 output levels is often sufficient.
6. Drag the upper point (input ~190) slightly **upward** — brightening the upper midtones. Again, 5-10 levels.
7. The curve now has a gentle S-shape through the midtones, with the endpoints anchored at their original positions.

**Result after Curves:** The midtones now have increased separation. The landscape looks more vivid and three-dimensional. Shadows have depth. Highlights have clarity. The overall contrast set by Levels is preserved; the Curves adjustment refined the contrast distribution.

### Step 4: Evaluate and Refine

1. Toggle the Levels Adjustment Layer visibility (eye icon) to compare before/after for the endpoint correction.
2. Toggle the Curves Adjustment Layer visibility to compare before/after for the midtone shaping.
3. Check the histogram again. It should now span the full range with a smooth distribution. A slight comb pattern may appear — this is normal after tonal stretching in 8-bit, and another reason to work in 16-bit.
4. Check the Info panel on critical areas: are skin tones reasonable? Are shadow areas still above zero (not crushed)? Are highlight areas still below 255 (not blown)?
5. If the S-curve is too aggressive (shadows look crushed, highlights look flat at the extremes), reduce the displacement of the anchor points or add additional points to soften the curve's transition.

**RECOMMENDATION:** Subtlety is almost always correct. A gentle S-curve that adds 5-10 levels of displacement produces a natural-looking result. An aggressive curve with 30+ levels of displacement produces obvious, heavy-handed contrast that fatigues the viewer. When in doubt, do less.

---

## Common Mistakes

**Applying tonal adjustments destructively instead of as Adjustment Layers.** Using Image > Adjustments > Curves directly bakes the adjustment into the pixel data. If you need to change it later, you cannot. Always use Adjustment Layers (Chapter 06).

**Setting black and white points by histogram shape instead of by image content.** The histogram shows data distribution, but the decision of where to clip should be made based on what is in the image, not where the histogram starts. A foggy scene should not have a full-range histogram. A backlit silhouette should have clipped shadows. Use the clipping preview (Alt/Option+drag) to see what you are actually clipping, and decide based on the image content.

**Over-applying the S-curve.** An aggressive S-curve produces dramatic contrast but destroys shadow and highlight detail. The midtone expansion comes at the cost of compressing the extremes. If you find yourself dragging anchor points more than 15-20 levels in an 8-bit image, you are probably overdoing it. Step back and evaluate the result at 100% view on a calibrated monitor.

**Ignoring the per-channel histogram when setting master Levels.** Setting the master black and white points clips all three channels equally. But channels often have different distributions. If the Red channel extends further into the shadows than the Green and Blue, a master black point set to the Red channel's edge clips important data in the other channels. Check individual channel histograms for images with strong color bias.

**Using the Exposure adjustment for standard tonal correction.** The Exposure adjustment was designed for 32-bit HDR workflows. In 8-bit and 16-bit files, it applies linear-light math to gamma-encoded data, which disproportionately affects highlights and can cause harsh clipping. Use Curves instead for standard photographic tonal correction.

**Confusing the Brightness slider with Curves midtone brightening.** The Brightness slider in Brightness/Contrast applies a specific weighted algorithm. A Curves adjustment that lifts the midpoint applies a different function. They produce different results, especially in the shadows and highlights. They are not interchangeable operations even though both "make the image brighter."

**Making tonal adjustments in 8-bit mode for images that will undergo heavy editing.** Every tonal adjustment in 8-bit mode redistributes 256 values and creates gaps. Multiple stacked adjustments compound the loss. Convert to 16-bit before heavy tonal editing and convert back to 8-bit only for final output.

**Adjusting per-channel curves on the master curve by mistake.** It is easy to forget which channel is selected in the Curves dropdown. Adjusting the Red channel when you think you are on RGB produces an unexpected color shift. Always verify the channel dropdown before dragging a curve, and check the color overlay on the curve line (Red, Green, or Blue tint) to confirm which channel is active.

---

## Alternative Approaches

### Camera Raw / Lightroom as the Primary Tonal Tool

For images coming from RAW files, the most effective tonal adjustments are often made in Adobe Camera Raw (ACR) or Lightroom, before the image enters Photoshop. The Exposure, Contrast, Highlights, Shadows, Whites, and Blacks sliders in ACR operate on the raw sensor data (or near-raw data) and produce smoother results than equivalent Photoshop adjustments on rendered files. The Tone Curve panel in ACR provides Curves-like control.

**INTERPRETATION:** The general principle is: make global tonal corrections in ACR/Lightroom, and use Photoshop's Levels/Curves for refinements, local adjustments, and corrections that require masks or layer-based control. If you opened the RAW file as a Smart Object (Chapter 06), you can return to ACR at any time by double-clicking the Smart Object thumbnail.

### Curves Adjustment Layers with Masks for Targeted Tonal Control

Instead of applying a single Curves adjustment to the entire image, you can use multiple Curves Adjustment Layers, each with a layer mask that restricts its effect to a specific area. For example: one Curves layer for the sky (masked to the sky), another for the foreground. This is a form of local tonal control and is the bridge between this chapter's global adjustments and Chapter 08's local techniques.

### Blend Modes for Contrast

Duplicating a layer and setting the blend mode to Soft Light or Overlay increases contrast — Soft Light produces a gentler effect, Overlay produces a stronger one. Reducing the layer opacity moderates the effect. This technique is simple but imprecise: you cannot control which tonal region is affected. It is included here for completeness but Curves provides superior control.

### The "Blend If" Sliders for Tonal Targeting

The Layer Style dialog (double-click to the right of the layer name) includes "Blend If" sliders that let you control which tonal values an adjustment affects. By splitting the "Underlying Layer" slider (Alt/Option+drag to split), you can make a Curves Adjustment Layer affect only the midtones while leaving shadows and highlights untouched — without a mask. This technique is explored further in Chapter 08 and Chapter 14.

---

## Summary

Tonal adjustment is the remapping of input pixel values to output pixel values through a transfer function. Every tonal tool in Photoshop — Levels, Curves, Brightness/Contrast, Exposure — performs a version of this operation. They differ in the shape of the transfer function and the degree of control you have over it.

**Levels** provides three points of control: black point, white point, and a midtone gamma slider. It is efficient for setting tonal endpoints and quick midtone brightness adjustments. It is a simplified subset of Curves.

**Curves** provides arbitrary control over the entire transfer function. You place anchor points wherever you need them and shape the mapping curve to achieve any desired tonal redistribution. The 45-degree diagonal means no change. Above the line means lighter. Below the line means darker. Steeper slopes mean more contrast in that region; flatter slopes mean less. The S-curve — the most common photographic Curves adjustment — increases midtone contrast at the expense of highlight and shadow contrast.

**Master curves** affect all channels equally and primarily change brightness and contrast. **Per-channel curves** affect individual channels and shift color: raising a channel adds that color, lowering it adds the complement.

**Brightness/Contrast** provides a simplified two-slider interface. The modern (non-legacy) algorithm is acceptable for minor corrections. The legacy algorithm is a destructive linear operation that clips aggressively and should be avoided.

**Exposure** simulates photographic exposure change using linear-light math. It is designed for 32-bit HDR workflows and is not the optimal choice for standard 8-bit or 16-bit tonal correction.

The professional workflow is diagnostic: read the histogram first, diagnose the tonal problem, then choose the appropriate tool. Use Levels for endpoints, Curves for midtone shaping and precise tonal control. Apply all adjustments as Adjustment Layers for non-destructive editing. Work in 16-bit when heavy tonal editing is required.

Curves is the most important tonal tool in Photoshop. Invest the time to understand it thoroughly — it is the foundation of tonal control, color correction (Chapter 09), and creative color grading (Chapter 10).

---

## Exercises

### Exercise 1: Reading the Curves Graph

Open a Curves Adjustment Layer on any photograph. Before placing any points, answer these questions:

1. What does the 45-degree diagonal represent?
2. If you place a point at input 128 and drag it up to output 160, what happens to pixels that are currently at value 128? What about pixels at value 64? At value 192?
3. If you place a point at input 128 and drag it down to output 100, what happens to the same tones?
4. Now place the point and verify your predictions using the Info panel. Hover over midtone, shadow, and highlight areas and compare the before/after values shown.

**Success criterion:** You can predict the effect of a curve modification before applying it, based on the input/output relationship.

### Exercise 2: Levels Endpoint Correction

Open a flat, low-contrast photograph (or deliberately flatten one by reducing contrast in Camera Raw).

1. Create a Levels Adjustment Layer.
2. Use the Alt/Option+drag clipping preview to set the black point just before important shadow detail clips.
3. Use the clipping preview to set the white point just before important highlight detail clips.
4. Compare the histogram before and after. How has the tonal range changed?
5. Toggle the Levels layer on/off to see the before/after difference.

Now undo and try achieving the same result with a Curves Adjustment Layer — place two points to match the Levels endpoints. Compare the results.

**Success criterion:** You understand that Levels endpoint correction is a subset of what Curves can do, and you can achieve the same result with either tool.

### Exercise 3: The S-Curve

Open a photograph with adequate tonal range but flat midtones.

1. Create a Curves Adjustment Layer.
2. Place an anchor point in the lower midtones (around input 64) and drag it down by 8 levels.
3. Place an anchor point in the upper midtones (around input 192) and drag it up by 8 levels.
4. Observe the effect on the image. Then examine the histogram — what has changed in the distribution?
5. Now increase the S-curve displacement to 20 levels on each point. What happens to the shadows and highlights at the extremes? Do you see evidence of compression?

**Success criterion:** You can create an S-curve of controlled intensity and predict its effect on midtone contrast, shadow detail, and highlight detail.

### Exercise 4: Master Curve vs Per-Channel Curve

Open a photograph with visible color variety (a landscape with green foliage, blue sky, and warm earth tones).

1. Create a Curves Adjustment Layer. On the **RGB master** curve, create a moderate S-curve.
2. Observe: does the color balance shift? Does saturation change? Note your observations.
3. Reset the master curve to default (right-click > Reset Channel or delete your points).
4. Switch to the **Blue** channel. Pull the lower portion of the Blue curve down (reducing Blue in the shadows). What happens to the shadow color?
5. Switch to the **Red** channel. Pull the upper portion of the Red curve up (increasing Red in the highlights). What happens to the highlight color?
6. Reset and experiment with other channel combinations.

**Success criterion:** You understand that the master curve primarily affects luminosity and contrast, while per-channel curves primarily affect color — and you can predict the color direction of per-channel adjustments.

---

## Advanced Exercises

### Advanced Exercise 1: Comparing Tonal Tools

Open the same photograph in four separate windows (or duplicate the image four times). Apply the following adjustments, each producing approximately the same amount of overall brightening:

1. **Curves:** Lift the midpoint (input 128 to approximately output 160).
2. **Levels:** Move the gamma slider to approximately 1.4.
3. **Brightness/Contrast:** Increase Brightness to approximately +30 (Use Legacy unchecked).
4. **Exposure:** Increase Exposure to approximately +0.5.

Now compare:

1. How do the highlights respond in each? Which tool clips highlights most aggressively?
2. How do the shadows respond? Which tool shifts shadows least?
3. Examine the histogram of each result. How do the distributions differ?
4. Which tool gives the most natural-looking result for this particular image? Why?

**Success criterion:** You can articulate the different algorithms behind each tonal tool and predict which situations favor which tool based on the image content.

### Advanced Exercise 2: Endpoint Correction Without Clipping

Open a photograph that has mild clipping in one channel (check the histogram in All Channels View — look for a channel that extends further than the others at one end).

1. Create a Levels Adjustment Layer. Set the master black and white points to the edges of the composite histogram.
2. Now check the individual channel histograms. Is any channel being clipped by the master adjustment?
3. Undo. This time, use per-channel Levels: switch to each channel individually and set its endpoints to match that channel's actual data range.
4. Compare the two approaches. How do they differ in terms of color shift and clipping?

**Success criterion:** You understand why per-channel endpoint correction can produce superior results to master endpoint correction, and you can identify when the difference matters.

### Advanced Exercise 3: Stacked Curves for Separate Concerns

Process a single image using three separate Curves Adjustment Layers, each with a single, clear purpose:

1. **Curves 1 — Endpoints:** Set the black and white points only. No midtone adjustment.
2. **Curves 2 — Midtone Contrast:** Create a gentle S-curve. Do not touch the endpoints.
3. **Curves 3 — Shadow Lift:** Raise the deepest shadows slightly (lift the very bottom of the curve above zero) to create a film-like faded black effect.

Toggle each layer on and off independently. Observe how each adjustment affects the image in isolation and in combination.

**Success criterion:** You understand the value of separating tonal concerns into individual Adjustment Layers, and you can build a modular tonal correction stack.

---

## Blackbelt Challenge

You receive two versions of the same photograph:

**Version A:** A properly exposed landscape with good tonal range, shot in golden hour light. The image is technically correct but visually flat — it lacks the dramatic quality the scene had in person. The midtones are compressed, the warm light lacks intensity, and the shadows lack depth.

**Version B:** The same scene, but overexposed by approximately one stop. The sky is partially clipped in the Red and Green channels (but not Blue). The foreground is well-exposed.

For each version, without opening Photoshop:

1. **Describe the histogram** you expect to see. Be specific about the shape, the location of the data mass, and the clipping state.
2. **Design a complete tonal correction strategy.** Specify every adjustment layer, its type (Levels or Curves), its purpose, and the approximate curve shape or slider positions you would use. Explain why each step is necessary and why you chose that tool for that step.
3. **For Version B:** Explain what can and cannot be recovered in the clipped sky. What happens when only two of three channels are clipped? What does the resulting color look like in the clipped region, and why? What is the best realistic outcome you can achieve?
4. **Predict the color side-effects** of your tonal corrections on the master curve. If you apply an S-curve to Version A, what will happen to the apparent color saturation, and why does this occur?
5. **Design a per-channel curve correction** that adds warmth to the shadows of Version A without shifting the highlights. Specify which channel(s) you would adjust, in which direction, and in which portion of the tonal range. Explain the color theory behind your choice.

This exercise requires you to synthesize the concepts of histograms (Chapter 01), channels (Chapter 01), non-destructive workflow (Chapter 06), Levels, Curves, master vs per-channel curves, and clipping behavior into a coherent professional workflow — without touching the software.
