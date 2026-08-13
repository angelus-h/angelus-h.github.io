---
description: HSL and Selective Color control in Photoshop — Hue/Saturation adjustment, Selective Color adjustment, targeted color adjustments, saturation vs vibrance, Channel Mixer, and the HSL color model.
---

# Chapter 11: HSL and Selective Color Control

## Learning Objectives

After completing this chapter, you will be able to:

1. Explain the HSL color model and how it differs from HSB, and describe why this distinction matters when predicting the behavior of Photoshop's Hue/Saturation adjustment.
2. Use the Hue/Saturation Adjustment Layer to shift hue, saturation, and lightness globally and within targeted color ranges, and predict the result before applying.
3. Explain how the six preset color ranges in Hue/Saturation are defined, how the feathering zone between adjacent ranges works, and how to modify range boundaries manually.
4. Use the targeted adjustment tool (on-image eyedropper) in Hue/Saturation to isolate a specific color from the image and adjust it directly.
5. Explain how the Selective Color Adjustment Layer works — its CMYK-like component model, the difference between Relative and Absolute modes — and choose appropriately between it and Hue/Saturation.
6. Distinguish between Saturation and Vibrance at the algorithmic level, and predict which tool will produce superior results for images containing skin tones or mixed-saturation content.
7. Explain why the Lightness slider in Hue/Saturation typically produces inferior results compared to luminosity adjustments via Curves, and choose the correct tool for darkening or lightening a color range.
8. Use the Channel Mixer Adjustment Layer to redefine channel contributions, and understand its role in both color correction and black-and-white conversion.
9. Construct a multi-layer targeted color adjustment workflow that combines Hue/Saturation, Selective Color, and Curves to refine individual colors independently within a single image.

---

## Conceptual Foundation

Chapters 09 and 10 addressed color correction and color grading — operations that shift the overall color balance or establish a creative color mood across the entire image. Those techniques treat color globally or by tonal region (shadows, midtones, highlights). This chapter addresses a different problem: adjusting specific colors independently while leaving others untouched.

Consider an autumn landscape. The reds in the maple leaves need to be richer without shifting the yellows in the birch trees. The greens in the remaining foliage need to be darker and slightly cooler without affecting the warm sky. The blue of the sky needs to remain exactly as it is. Each of these is a color-specific adjustment — you are targeting a hue range, not a tonal range.

This kind of targeted color control requires tools that understand color identity. Curves can shift color, but it operates by channel and by tonal position — it cannot distinguish "red objects" from "blue objects" at the same luminance level. The tools in this chapter — Hue/Saturation, Selective Color, Vibrance, and Channel Mixer — provide mechanisms to isolate and modify specific colors based on their hue, allowing you to reshape the color palette of an image with precision.

The key conceptual distinction: Chapters 09-10 worked by adjusting channel values based on *where they sit in the tonal range*. This chapter works by adjusting colors based on *what hue they are*.

---

## Terminology

### Hue

**Definition:** Hue is the attribute of color that distinguishes red from blue from green — the position on the color wheel. In the HSL and HSB color models, hue is expressed as an angle from 0 to 360 degrees, where 0/360 degrees is red, 120 degrees is green, and 240 degrees is blue. Hue is the "name" of the color, independent of how bright or how pure it is.

**Meaning in photographic practice:** When you say "the sky is blue" or "the leaves are orange," you are identifying the hue. Hue is what changes when autumn leaves transition from green to yellow to orange to red — the saturation and lightness may stay roughly constant, but the hue rotates around the color wheel. In photographic editing, shifting hue means changing one color into another: turning a blue sky slightly more cyan, or pushing orange skin tones slightly toward yellow.

**Photoshop implementation:** Hue appears in several places in Photoshop. The Color Picker displays it as the H field (0-360 degrees). The Hue/Saturation Adjustment Layer provides a Hue slider (-180 to +180, representing a rotation around the 360-degree wheel). In Camera Raw and Lightroom, the HSL panel provides per-color Hue sliders. **FACT:** The Hue slider in Hue/Saturation rotates the hue of affected pixels by the specified number of degrees. A shift of +30 turns reds into oranges, oranges into yellows, yellows into yellow-greens, and so on around the wheel. The color bar at the bottom of the Hue/Saturation dialog shows the original hues (top bar) and the resulting hues (bottom bar) for reference.

**Related concepts:** Saturation, Lightness, HSL color model, HSB color model, color wheel.

**Common misconception:** "Hue and color are the same thing." Hue is only one dimension of color. A dark, desaturated red and a bright, vivid red share the same hue (both are "red") but differ dramatically in saturation and lightness. Changing the hue without changing saturation or lightness replaces one color name with another while preserving the relative purity and brightness.

---

### Saturation

**Definition:** Saturation describes the purity or intensity of a color — how far it is from a neutral gray of equal lightness. A fully saturated color contains no gray component; it is the purest version of that hue at that lightness. A completely desaturated color is a neutral gray. In the HSL model, saturation ranges from 0% (neutral gray) to 100% (maximum purity for that hue and lightness).

**Meaning in photographic practice:** Saturation is what makes a red fire-engine vivid versus a muted brick wall. High saturation produces bold, punchy colors. Low saturation produces muted, pastel, or earthy tones. In photographic editing, increasing saturation pushes colors toward their purest form, while decreasing it pulls them toward gray. Oversaturation is one of the most common editing mistakes — it produces colors that look artificial, cause gamut clipping on output, and fatigue the viewer. Undersaturation can make an image feel flat and lifeless.

**Photoshop implementation:** The Saturation slider in Hue/Saturation adjusts the saturation of the selected range (or all colors in Master mode). It ranges from -100 (complete desaturation to gray) to +100 (maximum saturation increase). **FACT:** At -100 on the Master channel, the Saturation slider produces a grayscale image, but not a good one — it uses a simple desaturation that does not weight the channels perceptually. For quality grayscale conversion, use the Black & White Adjustment Layer or Channel Mixer (Chapter 18). The Saturation slider in Camera Raw/Lightroom operates similarly but within the ACR processing pipeline.

**Related concepts:** Hue, Lightness, Vibrance, desaturation, gamut clipping.

**Common misconception:** "Saturation and Vibrance are the same thing with different names." They are fundamentally different algorithms. Saturation applies a uniform boost to all colors equally, regardless of their current saturation level. Vibrance applies a non-linear, selective boost that increases saturation more for low-saturation colors and less for already-saturated colors. See the Vibrance entry below for the full distinction.

---

### Lightness (in HSL Context)

**Definition:** In the HSL (Hue, Saturation, Lightness) color model, Lightness represents the range from black to white through the pure hue. At Lightness 0%, any hue at any saturation is black. At Lightness 100%, any hue at any saturation is white. At Lightness 50%, the fully saturated, pure version of the hue is displayed. Lightness differs from Brightness in the HSB model: HSB Brightness at 100% gives the purest, most vivid color, while HSL Lightness at 100% gives white.

**Meaning in photographic practice:** The Lightness slider in Photoshop's Hue/Saturation adjustment is often misunderstood and misused. Increasing Lightness adds white to the color (washing it out). Decreasing Lightness adds black (muddying it). Neither operation behaves like adjusting the luminosity of that color region through a Curves or Levels adjustment. Moving the Lightness slider to +50 does not make reds brighter in a useful photographic way — it makes them pink. Moving it to -50 does not make reds deeper — it makes them a dark, muddy brown.

**Photoshop implementation:** The Lightness slider in Hue/Saturation ranges from -100 to +100. **FACT:** Internally, the Lightness slider adds white (positive values) or black (negative values) to the affected colors. This is a different operation from changing luminosity — adding white desaturates the color while brightening it; adding black desaturates while darkening. **RECOMMENDATION:** Avoid the Lightness slider in Hue/Saturation for most photographic work. If you need to make a color range lighter or darker, use a Curves Adjustment Layer with a mask or with the Blend If sliders (Chapter 14) to target the desired range, or use the Luminance slider in Camera Raw's HSL panel, which operates differently and produces superior results.

**Related concepts:** HSL color model, HSB color model, Brightness (HSB), Luminance (Camera Raw), Curves.

**Common misconception:** "The Lightness slider in Hue/Saturation works like the Luminance slider in Lightroom." It does not. The Lightroom/Camera Raw Luminance slider adjusts the luminance of a color range without adding white or black — it changes how bright or dark that hue appears while preserving its saturation characteristics more naturally. The Photoshop Hue/Saturation Lightness slider literally mixes in white or black, which desaturates the color as a side effect. They look similar in the interface but produce substantially different results.

---

### Vibrance (vs Saturation)

**Definition:** Vibrance is a non-linear saturation adjustment that applies a stronger boost to less-saturated colors and a weaker boost to already-saturated colors. It also reduces its effect on skin-tone hues (the orange-red range) to prevent skin from becoming unnaturally vivid. Vibrance was introduced in Camera Raw and Lightroom and later added to Photoshop as a dedicated Adjustment Layer.

**Meaning in photographic practice:** The Vibrance/Saturation distinction is one of the most important practical differences in color adjustment. Consider a portrait against a blue sky. The skin is moderately saturated; the sky is already quite saturated. If you increase Saturation by +30 on the Master channel in Hue/Saturation, both the skin and the sky receive the same +30 boost. The sky may clip into an impossible, neon blue. The skin turns an unnatural, sunburned orange. Vibrance at +30 would boost the skin only slightly (because Vibrance protects skin-tone hues and already moderately-saturated colors) and boost the sky even less (because it is already highly saturated). The result is a more balanced, natural-looking saturation increase.

**Photoshop implementation:** Available as an Adjustment Layer (Layer > New Adjustment Layer > Vibrance) with two sliders: Vibrance and Saturation. The Vibrance slider applies the non-linear, skin-protecting algorithm. The Saturation slider in the Vibrance Adjustment Layer is identical in behavior to the Master Saturation slider in Hue/Saturation — a uniform linear boost. Having both sliders in one panel lets you combine them: use Vibrance for the primary boost, then fine-tune with a small Saturation adjustment if needed.

**Related concepts:** Saturation, Hue/Saturation, skin tones, gamut clipping.

**Common misconception:** "Vibrance is just a weaker version of Saturation." Vibrance is not a lower-intensity version of the same operation. It is a fundamentally different algorithm. Saturation applies a uniform multiplier to all colors. Vibrance applies a variable multiplier that depends on the current saturation of each pixel and its hue. The two produce qualitatively different results, not just quantitatively different ones. On a landscape with no skin tones, Vibrance still behaves differently from Saturation because of the non-linear response to existing saturation levels.

---

### Hue/Saturation Adjustment Layer

**Definition:** A Photoshop Adjustment Layer that provides control over hue, saturation, and lightness of the image, either globally (Master mode) or within six preset color ranges (Reds, Yellows, Greens, Cyans, Blues, Magentas). Each range can be independently adjusted with its own Hue, Saturation, and Lightness sliders. The adjustment also includes a Colorize mode that converts the image to a monochromatic tint.

**Meaning in photographic practice:** Hue/Saturation is the primary tool for targeted color modification in Photoshop. A landscape photographer uses it to boost the saturation of autumnal reds without affecting the sky. A portrait photographer uses it to shift a background color that clashes with the subject's clothing. A product photographer uses it to match a specific brand color. The ability to isolate individual hue ranges and adjust them independently makes this tool essential for fine color control that goes beyond what Curves or Color Balance can offer.

**Photoshop implementation:** Available as an Adjustment Layer (Layer > New Adjustment Layer > Hue/Saturation, or the Hue/Saturation icon in the Adjustments panel). The Properties panel displays a channel dropdown (Master plus the six color ranges), three sliders (Hue, Saturation, Lightness), and two color bars at the bottom. The Colorize checkbox switches to a monochromatic tinting mode. **FACT:** The on-image adjustment tool (the hand icon with left/right arrows, also called the targeted adjustment tool or scrubby slider) allows you to click directly on a color in the image and drag left/right to decrease/increase its saturation. Ctrl+drag / Cmd+drag changes hue instead. This tool also automatically selects and refines the color range to match the clicked area.

**Related concepts:** Targeted adjustment tool, color range, Selective Color, Vibrance, Curves per-channel.

**Common misconception:** "Hue/Saturation is outdated — use Camera Raw's HSL panel instead." Both tools serve the same purpose but in different processing stages. Camera Raw's HSL panel operates during RAW development and is optimal for global color tuning at the RAW stage. Hue/Saturation in Photoshop operates as an Adjustment Layer, which means it can be masked, stacked, grouped, and combined with other Photoshop-specific techniques. They are complementary, not competing tools.

---

### Selective Color Adjustment Layer

**Definition:** A Photoshop Adjustment Layer that adjusts colors by modifying the CMYK component values (Cyan, Magenta, Yellow, Black) within nine color categories: Reds, Yellows, Greens, Cyans, Blues, Magentas, Whites, Neutrals, and Blacks. Unlike Hue/Saturation, which works in the HSL model, Selective Color works in a CMYK-like component model — it adds or removes ink-like components from specific color ranges.

**Meaning in photographic practice:** Selective Color offers a different kind of control than Hue/Saturation. Instead of shifting hue along a wheel, it lets you modify the component makeup of a color. For example, you can select Reds and add Cyan to them — this pushes reds toward a cooler, more neutral tone without rotating the hue the way Hue/Saturation would. You can select Yellows and add Magenta — this warms the yellows toward orange. The CMYK-based approach is intuitive for photographers who think in terms of print color mixing: "I want less yellow in the greens" is a natural Selective Color operation.

**Photoshop implementation:** Available as an Adjustment Layer (Layer > New Adjustment Layer > Selective Color). The Properties panel shows a Colors dropdown (Reds, Yellows, Greens, Cyans, Blues, Magentas, Whites, Neutrals, Blacks) and four sliders: Cyan, Magenta, Yellow, and Black. Each slider ranges from -100% to +100%. A Method radio button offers Relative (adjusts existing values proportionally — a -50% Cyan adjustment on a color that has 40% Cyan would reduce it by 50% of 40, yielding 20%) or Absolute (adjusts by the exact percentage specified — a -50% Cyan adjustment on any color reduces Cyan by 50 percentage points). **FACT:** Despite using CMYK terminology, Selective Color works on RGB images without converting them to CMYK. The adjustment interprets RGB values in terms of their CMY component equivalents and modifies them accordingly.

**Related concepts:** Hue/Saturation, CMYK color model, color ranges, Whites/Neutrals/Blacks categories.

**Common misconception:** "Selective Color converts the image to CMYK." It does not. Selective Color uses CMYK component logic as a method of adjustment, but the image remains in whatever color mode it was in (typically RGB). No mode conversion occurs. The CMYK sliders describe how to modify the color components, not the color mode of the file.

---

### Targeted Adjustment Tool (On-Image Tool in Hue/Saturation)

**Definition:** An interactive tool within the Hue/Saturation (and Curves) Adjustment Layer that allows you to click directly on the image to identify a color, automatically select the corresponding color range, and adjust it by dragging. In Hue/Saturation, the tool is represented by a hand icon with left/right arrows in the Properties panel.

**Meaning in photographic practice:** The targeted adjustment tool removes guesswork from color targeting. Instead of estimating whether the autumn leaves are in the Reds or Yellows range, you click on a leaf and the tool selects the correct range automatically. It also narrows the range to better match the specific color you clicked, refining the default range boundaries. This makes precise color targeting fast and intuitive — particularly valuable when colors do not fall neatly into one of the six preset categories.

**Photoshop implementation:** Activate the tool by clicking its icon in the Hue/Saturation Properties panel (or press Ctrl+Alt+Shift+H / Cmd+Option+Shift+H to open Hue/Saturation and then click the tool). With the tool active: drag left/right to decrease/increase saturation of the identified color. Hold Ctrl/Cmd while dragging to shift hue instead. The color range at the bottom of the panel updates to show exactly which range of hues is being affected, including the feathering zones. You can click on additional colors while holding Shift to expand the targeted range.

**Related concepts:** Hue/Saturation Adjustment Layer, color range, feathering, on-image adjustment.

**Common misconception:** "The targeted adjustment tool locks you into the default color ranges." It does not. When you click on a color, the tool selects the nearest preset range but then adjusts the range boundaries (visible on the color bar at the bottom of the panel) to better match the specific color you targeted. You can further modify these boundaries manually by dragging the sliders on the color bar.

---

### Color Range (the Six Preset Ranges in Hue/Saturation)

**Definition:** The Hue/Saturation adjustment divides the 360-degree hue wheel into six preset color ranges: Reds, Yellows, Greens, Cyans, Blues, and Magentas. Each range covers approximately 60 degrees of the hue wheel as its core, with feathering zones on each side where the adjustment transitions smoothly to zero effect. These ranges can be individually selected from the dropdown, and each has independent Hue, Saturation, and Lightness sliders.

**Meaning in photographic practice:** The six ranges provide a structured way to target color adjustments. A landscape photographer might select Yellows to boost the vibrancy of golden grass, then switch to Blues to deepen the sky, then select Greens to shift foliage toward a cooler tone — each adjustment independent of the others. The ranges are a starting point: they cover the most commonly needed color divisions, but they are not fixed. You can expand, narrow, or shift the range boundaries to match the specific colors in your image.

**Photoshop implementation:** When you select a color range (e.g., Reds) from the dropdown, the color bar at the bottom of the Hue/Saturation panel displays four markers that define the range:

| Marker | Role |
|--------|------|
| Two inner markers (light gray bars) | Define the core range — hues within this span receive the full adjustment |
| Two outer markers (dark gray bars) | Define the feathering boundaries — hues between the outer and inner markers receive a gradually decreasing proportion of the adjustment |

**FACT:** You can drag these markers to redefine the range. Dragging the area between the inner markers moves the entire range. Dragging an outer marker extends or contracts the feathering zone. Dragging an inner marker widens or narrows the core range. This manual control lets you fine-tune which colors are affected and how sharply the adjustment transitions at the boundaries.

**Related concepts:** Hue/Saturation, feathering, targeted adjustment tool, hue wheel.

**Common misconception:** "The six color ranges are rigid and non-overlapping." The ranges overlap in their feathering zones. A hue at the boundary between Reds and Yellows may be partially affected by adjustments to both ranges. This is by design — it prevents hard color transitions — but it means you need to be aware of feathering when making large adjustments to adjacent ranges. If you shift Reds by +20 hue and Yellows by -20, the overlapping feathering zone will receive conflicting instructions, potentially producing unexpected results.

---

### Channel Mixer Adjustment Layer

**Definition:** A Photoshop Adjustment Layer that redefines the contribution of each source channel (Red, Green, Blue) to each output channel. For each output channel, you specify what percentage of the Red, Green, and Blue input channels should be mixed together, plus a constant offset. This is a matrix operation on the channel data.

**Meaning in photographic practice:** The Channel Mixer is primarily used for two purposes: precision black-and-white conversion (Chapter 18) and targeted color correction that requires redefining channel relationships. For color work, Channel Mixer allows operations that Hue/Saturation and Selective Color cannot: you can reduce the Green channel's contribution to the Red output while increasing the Blue channel's contribution, creating color shifts that do not map neatly to hue rotation or CMYK component adjustment.

**Photoshop implementation:** Available as an Adjustment Layer (Layer > New Adjustment Layer > Channel Mixer). The Properties panel shows an Output Channel dropdown (Red, Green, Blue) and three source channel sliders (Red, Green, Blue, each -200% to +200%) plus a Constant slider (-200% to +200%). A Monochrome checkbox enables grayscale mixing mode. **RECOMMENDATION:** When using Channel Mixer for color adjustment, keep the total of the three source sliders for each output channel near 100% to maintain consistent brightness. For example, if you reduce Red's contribution to the Red output from 100% to 80%, consider increasing Green or Blue contribution by the corresponding 20% to compensate. Significant deviation from a 100% total will darken or brighten the output.

**Related concepts:** RGB channels (Chapter 01), black-and-white conversion (Chapter 18), Hue/Saturation, per-channel Curves.

**Common misconception:** "Channel Mixer is only for black-and-white conversion." While black-and-white conversion is its most common use (and the Monochrome checkbox is designed for that purpose), Channel Mixer is a powerful color tool in its own right. Cross-channel mixing — feeding some of the Blue channel into the Red output, for example — creates color effects that no other single Adjustment Layer can replicate. It is an advanced tool, but it is not a monochrome-only tool.

---

## Theory

### The HSL Color Model

The HSL (Hue, Saturation, Lightness) color model describes color in three dimensions:

- **Hue:** The angular position on the color wheel (0-360 degrees).
- **Saturation:** The purity of the color (0% = gray, 100% = fully saturated).
- **Lightness:** The position between black and white (0% = black, 50% = pure color, 100% = white).

HSL can be visualized as a double cone or bicone: the equator is the color wheel at maximum saturation and Lightness 50%. The top apex is pure white (Lightness 100%). The bottom apex is pure black (Lightness 0%). Moving from the equator toward the center axis reduces saturation (colors become grayer). Moving up from the equator adds white. Moving down adds black.

### HSL vs HSB: A Critical Distinction

Photoshop's Color Picker and several interface elements use the HSB (Hue, Saturation, Brightness) model, not HSL. These models share the Hue dimension but define saturation and the third dimension differently:

| Property | HSL | HSB (also called HSV) |
|----------|-----|----------------------|
| Third dimension name | Lightness | Brightness (or Value) |
| At maximum third dimension | White (all hues become white at L=100%) | Pure, fully vivid color (at B=100%, the color is at its brightest and most saturated form) |
| At minimum third dimension | Black (L=0%) | Black (B=0%) |
| Pure hue location | L=50%, S=100% | B=100%, S=100% |
| Shape metaphor | Double cone (bicone) | Single cone or cylinder |

**FACT:** Photoshop's Hue/Saturation adjustment operates in the HSL model. The Lightness slider produces results consistent with HSL, not HSB. This is why the Lightness slider adds white when increased — in HSL, moving Lightness above 50% toward 100% blends the color with white. If it operated in HSB, increasing Brightness would make the color more vivid, not whiter.

**INTERPRETATION:** The HSL/HSB distinction matters whenever you predict the outcome of a Lightness adjustment. If you expect "making the lightness higher makes the color brighter and more vivid," you are thinking in HSB. In HSL, higher Lightness makes the color lighter by adding white, which simultaneously reduces saturation. Understanding which model the tool uses prevents surprises.

### How Targeted Adjustments Isolate Colors

When you select a color range in Hue/Saturation (e.g., Reds), Photoshop examines each pixel's hue value and determines how much of the adjustment to apply based on where that hue falls relative to the selected range:

1. **Inside the core range** (between the two inner markers): The adjustment applies at full strength.
2. **Inside the feathering zone** (between an inner and outer marker): The adjustment applies at a strength proportional to the pixel's position within the zone — full strength at the inner marker, zero at the outer marker. This creates a smooth, gradual transition.
3. **Outside the outer markers**: No adjustment is applied.

This mechanism is a hue-based mask. The image data is not physically separated into "red pixels" and "non-red pixels" — instead, each pixel's hue is evaluated against the range, and the adjustment strength is modulated accordingly. Pixels with ambiguous hues (near the boundary between two ranges) receive partial adjustment from both ranges if both are modified.

**FACT:** The feathering between ranges is linear. A pixel exactly halfway between the inner and outer marker receives 50% of the adjustment. A pixel at one-quarter of the feathering distance from the inner marker receives 75% of the adjustment. This linear falloff is usually smooth enough for photographic work, but very large hue shifts on narrow ranges can sometimes reveal the transition boundary.

### How Selective Color Works: CMYK Component Logic on RGB Data

Selective Color operates on a fundamentally different principle than Hue/Saturation. Instead of working in the HSL model, it analyzes colors in terms of their CMYK component equivalents and lets you add or remove those components.

For each pixel, Photoshop determines which of the nine color categories it falls into:

| Category | How pixels are classified |
|----------|--------------------------|
| Reds | Pixels where Red is the dominant RGB channel and the color is recognizably red |
| Yellows | Pixels where Red and Green are both high relative to Blue |
| Greens | Pixels where Green is the dominant channel |
| Cyans | Pixels where Green and Blue are both high relative to Red |
| Blues | Pixels where Blue is the dominant channel |
| Magentas | Pixels where Red and Blue are both high relative to Green |
| Whites | Pixels where all three channels are high (near white) |
| Neutrals | Pixels where all three channels are similar (near gray) |
| Blacks | Pixels where all three channels are low (near black) |

Once a pixel is classified, the CMYK sliders modify its color by adding or removing the specified component:

- Adding **Cyan** to Reds reduces the Red channel value (Cyan is the complement of Red).
- Adding **Magenta** to Greens reduces the Green channel value.
- Adding **Yellow** to Blues reduces the Blue channel value.
- The **Black** slider darkens or lightens the selected range uniformly.

**FACT:** The Relative vs Absolute method setting in Selective Color significantly changes the result:

| Method | Behavior | Example: -50% Cyan on a pixel with 80% Cyan |
|--------|----------|----------------------------------------------|
| Relative | Adjusts proportionally to the existing amount of that component | Reduces by 50% of 80% = removes 40 percentage points, resulting in 40% Cyan |
| Absolute | Adjusts by the exact percentage specified | Reduces by 50 percentage points, resulting in 30% Cyan |

**INTERPRETATION:** Relative mode produces more subtle, proportional results — colors that already have little of a component are barely affected. Absolute mode produces a fixed, uniform shift regardless of existing values. For most photographic work, Relative mode is the safer starting point because it respects the existing color relationships. Absolute mode is useful when you need a definitive, consistent change across a range.

### Saturation vs Vibrance: The Algorithmic Difference

The distinction between Saturation and Vibrance is not a matter of intensity — it is a difference in algorithm.

**Saturation (uniform/linear):** Multiplies the distance of each pixel's color from the neutral axis by a constant factor. Every pixel receives the same proportional boost, regardless of its current saturation. A pixel that is already 90% saturated and a pixel at 20% saturated both receive the same percentage increase. This means already-saturated colors clip first, skin tones become unnatural first, and the overall result can look garish on mixed-saturation images.

**Vibrance (non-linear/adaptive):** Applies a variable multiplier based on the pixel's current saturation. Low-saturation pixels receive a stronger boost. High-saturation pixels receive a weaker boost or none at all. Additionally, the algorithm reduces its effect on hues in the skin-tone range (approximately the orange-red portion of the hue wheel), preventing skin from becoming oversaturated even when other colors are boosted significantly.

| Characteristic | Saturation | Vibrance |
|---------------|------------|----------|
| Boost to low-saturation colors | Moderate | Strong |
| Boost to mid-saturation colors | Moderate | Moderate |
| Boost to high-saturation colors | Moderate (same as all others) | Weak or none |
| Effect on skin tones | Full boost (no protection) | Reduced boost (skin-tone hues protected) |
| Risk of gamut clipping | High at positive values | Low |
| Useful for | Uniform saturation changes, artistic effect, targeted range with Hue/Sat | Global saturation enhancement, images with skin, mixed-saturation scenes |

**RECOMMENDATION:** For general saturation enhancement on photographs, start with Vibrance. Switch to the Saturation slider only when you need uniform control, when working within a specific color range in Hue/Saturation, or when the non-linear behavior of Vibrance is not producing the result you want.

### Why the Lightness Slider Is Usually Wrong

The Lightness slider in Hue/Saturation is the most misleading control in the panel. Its name suggests "make this color lighter or darker," but its implementation — adding white or black — produces results that rarely match photographic intent.

Consider wanting to darken blue skies. The intuitive approach: select Blues, reduce Lightness. The result: the sky becomes a darker, muddier, less saturated blue — as if you mixed black paint into blue paint. The blue loses vibrancy. The effect looks heavy and artificial.

The alternative: use a Curves Adjustment Layer to darken the blue range. This reduces the luminance of blue pixels without mixing in black, preserving the color's saturation character. The sky becomes a deeper, richer blue — darker but still vivid. The difference is significant.

| Method | Effect on Blue Sky |
|--------|-------------------|
| Hue/Saturation Lightness -30 | Darker, muddier, desaturated — black mixed in |
| Curves darkening (masked to blues) | Darker, richer, saturation preserved — luminance reduced |
| Camera Raw Luminance slider (Blues -30) | Similar to Curves — luminance reduced without desaturation |

**FACT:** The Lightness slider in Hue/Saturation operates by adding white or black to the color values. This is not the same as adjusting luminosity. A luminosity adjustment changes how bright the color appears while preserving its chromatic character. Adding white or black changes the color itself — white desaturates toward a tint, black desaturates toward a shade.

---

## Photoshop Implementation

### Hue/Saturation: Detailed Walkthrough

**Opening as an Adjustment Layer:** Click the Hue/Saturation icon in the Adjustments panel, or go to Layer > New Adjustment Layer > Hue/Saturation. The Properties panel displays the Hue/Saturation controls.

**The channel dropdown:** At the top of the panel, a dropdown defaults to Master. Selecting Master affects all hues equally. Selecting one of the six color ranges (Reds, Yellows, Greens, Cyans, Blues, Magentas) restricts the adjustment to that range.

**The three sliders:**

| Slider | Range | Effect |
|--------|-------|--------|
| Hue | -180 to +180 | Rotates the hue of affected pixels by the specified angle on the 360-degree color wheel |
| Saturation | -100 to +100 | Increases (+) or decreases (-) the saturation of affected pixels. At -100, affected pixels become grayscale |
| Lightness | -100 to +100 | Adds white (+) or black (-) to affected pixels. See Theory section for why this is usually undesirable |

**The color bars:** Two horizontal color bars at the bottom of the panel show the full spectrum. When Master is selected, both bars show the same spectrum. When a specific range is selected and the Hue slider is moved, the top bar shows the original hue mapping and the bottom bar shows the result — giving a visual preview of the hue rotation.

**The range markers:** When a color range is selected, four small markers appear between the color bars:

- The area between the two inner (wider) markers is the core range — full-strength adjustment.
- The areas between inner and outer markers are the feathering zones.
- Drag the inner markers to widen/narrow the core. Drag the outer markers to extend/contract the feathering. Drag between the inner markers to shift the entire range.

**The targeted adjustment tool:** Click the hand icon in the Properties panel. Click on any color in the image. The tool automatically selects the nearest color range and refines the range boundaries to match the clicked color. Drag left to decrease saturation; drag right to increase it. Hold Ctrl/Cmd while dragging to shift hue instead.

**The Colorize checkbox:** When checked, the entire image is converted to a monochromatic tint. All hues are replaced with a single hue specified by the Hue slider. The Saturation slider controls the intensity of the tint. The Lightness slider adjusts brightness. This is useful for sepia-tone effects or duotone simulations, but not for targeted color adjustment.

**The eyedropper tools:** Below the sliders, three eyedropper icons let you sample colors to define the range:

| Eyedropper | Function |
|------------|----------|
| Plain eyedropper | Click on the image to set the color range center |
| Plus (+) eyedropper | Click to add to the current range |
| Minus (-) eyedropper | Click to subtract from the current range |

### Selective Color: Detailed Walkthrough

**Opening as an Adjustment Layer:** Click the Selective Color icon in the Adjustments panel, or go to Layer > New Adjustment Layer > Selective Color.

**The Colors dropdown:** Nine categories: Reds, Yellows, Greens, Cyans, Blues, Magentas, Whites, Neutrals, Blacks. Select the category of colors you want to adjust.

**The four sliders:**

| Slider | Range | Adding (+) | Removing (-) |
|--------|-------|------------|-------------|
| Cyan | -100% to +100% | Adds Cyan (reduces Red) | Removes Cyan (increases Red) |
| Magenta | -100% to +100% | Adds Magenta (reduces Green) | Removes Magenta (increases Green) |
| Yellow | -100% to +100% | Adds Yellow (reduces Blue) | Removes Yellow (increases Blue) |
| Black | -100% to +100% | Darkens the selected range | Lightens the selected range |

**The Method radio buttons:**

| Method | When to Use |
|--------|-------------|
| Relative | Default. Produces proportional, subtle adjustments. Respects existing color balance. Use for most photographic corrections. |
| Absolute | Produces fixed-percentage shifts. More aggressive. Use when you need a definitive change regardless of existing values. |

**The Whites, Neutrals, and Blacks categories:** These are unique to Selective Color and provide control that Hue/Saturation does not offer. They let you adjust the color cast in the lightest, mid-neutral, and darkest portions of the image. For example, selecting Whites and adding Cyan introduces a slight blue-cyan cast into the highlights — a subtle form of split toning. Selecting Blacks and adding Yellow warms the deepest shadows.

**INTERPRETATION:** The Whites/Neutrals/Blacks categories in Selective Color overlap functionally with what per-channel Curves can do (Chapter 10). The advantage of Selective Color for this purpose is that it uses more intuitive CMYK terminology ("add warmth to shadows" = "add Yellow to Blacks") rather than requiring you to think in terms of which channel curve to lower.

### Vibrance Adjustment Layer

**Opening as an Adjustment Layer:** Layer > New Adjustment Layer > Vibrance, or the Vibrance icon in the Adjustments panel.

**Two sliders:**

| Slider | Algorithm | Behavior |
|--------|-----------|----------|
| Vibrance | Non-linear, adaptive, skin-protecting | Boosts low-saturation colors more than high-saturation colors. Protects skin tones. |
| Saturation | Linear, uniform | Identical to Master Saturation in Hue/Saturation. Boosts all colors equally. |

**RECOMMENDATION:** For a general saturation increase, set Vibrance to a positive value first, then evaluate. If specific colors still need more boost, add a small positive Saturation value. If skin tones become too vivid even with Vibrance, mask the Vibrance layer to exclude skin areas, or use Hue/Saturation on a targeted range to boost only the non-skin colors.

### Channel Mixer: Detailed Walkthrough

**Opening as an Adjustment Layer:** Layer > New Adjustment Layer > Channel Mixer.

**Output Channel dropdown:** Select which output channel you are defining (Red, Green, or Blue).

**Source channel sliders:** For the selected output channel, three sliders control how much of each input channel contributes:

| Slider | Default for Red output | Default for Green output | Default for Blue output |
|--------|----------------------|------------------------|----------------------|
| Red | +100% | 0% | 0% |
| Green | 0% | +100% | 0% |
| Blue | 0% | 0% | +100% |

**Constant slider:** Adds a fixed offset to the output channel (-200% to +200%).

**Monochrome checkbox:** Converts output to grayscale by mixing all three source channels into a single output. See Chapter 18 for detailed grayscale conversion workflow.

**FACT:** The default Channel Mixer settings (100% of each channel to itself, 0% cross-contribution) produce no change — the identity matrix. Any deviation from these defaults is a color modification.

---

## Professional Workflow

### Where Targeted Color Adjustments Fit in the Layer Stack

Building on the workflow established in Chapters 06, 07, 09, and 10:

1. **Smart Object** (base photograph) — bottom of stack.
2. **Retouching layers** (sensor spots, blemishes).
3. **Tonal adjustments** (Levels, Curves — Chapter 07).
4. **Color corrections** (neutral balance, cast removal — Chapter 09).
5. **Color grading** (creative mood, split toning — Chapter 10).
6. **Targeted color adjustments** (Hue/Saturation, Selective Color, Vibrance — this chapter) — above color grading.
7. **Local adjustments** (dodging, burning — Chapter 08).

**INTERPRETATION:** Targeted color adjustments typically come after global color grading because they are refinements — you establish the overall color character first, then fine-tune individual hues. However, this order is not rigid. If a specific color is so problematic that it interferes with your judgment during grading (e.g., a distracting oversaturated sign in the background), correct it earlier in the stack so it does not mislead your grading decisions.

**RECOMMENDATION:** Group your targeted color Adjustment Layers into a layer group named "Color Target" or "HSL." This separates them from global color corrections and grading layers, making the stack easier to navigate and toggle.

### Choosing Between Hue/Saturation and Selective Color

Both tools target specific colors, but they offer different types of control:

| Criterion | Hue/Saturation | Selective Color |
|-----------|---------------|----------------|
| Shift hue (change one color into another) | Yes — Hue slider rotates hue | No — Selective Color cannot rotate hue |
| Adjust saturation of a specific hue range | Yes — Saturation slider | Indirectly — adding/removing components changes saturation as a side effect |
| Adjust component makeup (e.g., add Cyan to Reds) | No — works in HSL, not CMYK components | Yes — designed for this purpose |
| Adjust Whites, Neutrals, Blacks specifically | No — only six hue-based ranges | Yes — has Whites, Neutrals, Blacks categories |
| Fine-tune range boundaries | Yes — adjustable range markers and feathering | No — fixed categories, no boundary adjustment |
| On-image targeting tool | Yes — click to identify and adjust | No |

**RECOMMENDATION:** Use Hue/Saturation when you need to shift hue, when you need fine control over which hues are affected, or when you need the on-image targeting tool. Use Selective Color when you need to adjust the component makeup of a color (add or remove cyan, magenta, yellow, or black), or when you need to target Whites, Neutrals, or Blacks specifically.

### The Vibrance-First Principle

**RECOMMENDATION:** When the goal is a general saturation increase across the image, apply Vibrance before Hue/Saturation.

1. **First:** Create a Vibrance Adjustment Layer. Increase Vibrance to a moderate value (+15 to +35 is a typical range for photographic images). Evaluate the result.
2. **Then:** If specific color ranges need additional adjustment, create a Hue/Saturation Adjustment Layer and work within individual ranges. This two-step approach lets Vibrance handle the broad, safe, non-linear boost, while Hue/Saturation addresses specific color refinements.

This order matters because Vibrance's adaptive algorithm produces the best results on unmodified saturation relationships. If you boost specific ranges with Hue/Saturation first, those ranges may already be at high saturation when Vibrance is applied, and Vibrance will then leave them largely untouched — defeating the purpose of the sequence.

### Combining Hue/Saturation with Curves for Darkening Colors

When you want to make a color range darker (e.g., deepen a blue sky), avoid the Lightness slider in Hue/Saturation. Instead:

1. Create a Curves Adjustment Layer.
2. Pull the midtones or upper range of the curve downward to darken.
3. Restrict the Curves layer to the desired hue range using one of these methods:
    - Apply a Hue/Saturation Adjustment Layer below the Curves layer that selects the hue range and reduces its lightness — then discard this approach (it adds black).
    - Use the Blend If sliders (Layer Style dialog — Chapter 14) to restrict the Curves layer to specific underlying color values.
    - Create a mask from a color range selection (Select > Color Range, sample the target color, adjust Fuzziness) and apply it as the Curves layer mask.
    - In Camera Raw (if working from a Smart Object), use the HSL panel's Luminance sliders instead.

**INTERPRETATION:** This workflow is more complex than a single Lightness slider, but it produces photographically superior results. The Lightness slider exists for convenience; Curves with targeting produces quality.

---

## Step-by-Step Example: Enhancing Autumn Foliage

This example walks through targeted color adjustment of an autumn landscape containing reds, oranges, yellows, and greens in the foliage, with a blue sky and neutral gray rocks.

**Starting condition:** An autumn landscape where the foliage colors are present but lack the vibrancy and separation the scene had in person. The reds and oranges are somewhat muted. The yellows are slightly greenish. The remaining green foliage is too vivid relative to the warm tones. The sky and rocks should remain unchanged.

### Step 1: Global Vibrance Boost

1. Create a Vibrance Adjustment Layer. Name it "Vibrance - Global."
2. Increase Vibrance to +25. This moderately boosts the less-saturated colors (the muted reds and yellows) while barely affecting the already-vivid greens and sky blue.
3. Leave the Saturation slider at 0.
4. Evaluate: the foliage should appear slightly more vivid without any color becoming garish. If skin tones are present in the image (hikers, for example), verify they remain natural.

### Step 2: Enhance Reds Independently

1. Create a Hue/Saturation Adjustment Layer above the Vibrance layer. Name it "H/S - Reds."
2. Select **Reds** from the channel dropdown.
3. Increase Saturation to +15. The red maple leaves become richer.
4. Shift Hue to -5 (a slight shift toward magenta-red, deepening the reds away from orange). Evaluate: the reds should appear more vivid and slightly deeper, without turning magenta.
5. Leave Lightness at 0. Do not use the Lightness slider.
6. Check the range markers at the bottom. The default Reds range covers approximately 315 to 45 degrees with feathering. If some orange leaves are being affected and you want to isolate only the truest reds, narrow the range by dragging the inner markers closer together.

### Step 3: Warm the Oranges

1. On the same Hue/Saturation Adjustment Layer (or create a separate one for cleaner control), select **Reds** (oranges often fall in the Reds range) or use the targeted adjustment tool.
2. Activate the targeted adjustment tool (hand icon). Click on an orange leaf in the image. The tool may select Reds or Yellows depending on the specific hue — orange sits at the boundary.
3. Examine the range markers. Adjust them so the core range covers the orange hues specifically (roughly 15-45 degrees).
4. Increase Saturation by +10 to +20.
5. Optionally shift Hue by +5 to push the oranges slightly more toward warm orange (away from red).

### Step 4: Correct the Yellows

1. Create a new Hue/Saturation Adjustment Layer. Name it "H/S - Yellows."
2. Select **Yellows** from the dropdown.
3. The yellows appear slightly greenish. Shift Hue to -8 (rotating toward warmer yellow, away from green-yellow).
4. Increase Saturation to +10.
5. Leave Lightness at 0.
6. Evaluate: the birch and aspen leaves should now read as a cleaner, warmer yellow.

### Step 5: Subdue the Greens

1. On the Yellows Adjustment Layer or a new one, select **Greens**.
2. The remaining green foliage is too vivid compared to the warm tones. Decrease Saturation to -15.
3. Optionally shift Hue by +5 (pushing greens slightly toward cyan-green, which creates better visual separation from the warm yellows).
4. Leave Lightness at 0.

### Step 6: Refine with Selective Color

1. Create a Selective Color Adjustment Layer. Name it "Sel Color - Refinement."
2. Select **Reds.** Add Magenta +8, Yellow +5 (Relative method). This enriches the reds by adding color depth without rotating the hue — a different effect than the Hue/Saturation adjustment. The reds gain warmth and body.
3. Select **Yellows.** Remove Cyan -5 (which adds warmth to yellows). Add Yellow +10 (which makes yellows more vivid).
4. Select **Neutrals.** This affects the gray rocks and tree bark. If they have an unwanted color cast, correct it here. For example, if the rocks appear slightly blue from sky reflection, add Yellow +5 to Neutrals to warm them.

### Step 7: Evaluate the Composite Result

1. Group all the targeted color layers into a layer group named "Foliage Color."
2. Toggle the group on/off to compare before/after. The foliage should now have clear separation between reds, oranges, yellows, and greens, each enhanced independently.
3. Check the sky — it should be unaffected because none of the adjustments targeted Blues or Cyans.
4. Check the rocks — the Selective Color Neutrals adjustment should have cleaned any minor cast without introducing a new one.
5. If any adjustment is too strong, reduce the opacity of that specific Adjustment Layer rather than re-adjusting the sliders — this preserves the color character while reducing intensity.

---

## Common Mistakes

**Using the Lightness slider to darken or lighten colors.** The Lightness slider in Hue/Saturation adds black or white, which desaturates the color as a side effect. Darkening a blue sky with Lightness -30 produces a muddy, washed-out blue. Use Curves with targeting for darkening or lightening specific color ranges, or use the Luminance slider in Camera Raw's HSL panel.

**Boosting Master Saturation instead of Vibrance.** Increasing Saturation on the Master channel applies the same boost to every pixel regardless of its current saturation or hue. Already-saturated colors clip, skin tones turn orange, and the result looks synthetic. Use Vibrance for global saturation enhancement; reserve the Saturation slider for targeted, per-range adjustments in Hue/Saturation.

**Making large hue shifts on wide ranges.** A Hue shift of +40 on the default Reds range (which includes some oranges due to feathering) can produce bizarre color transitions at the range boundaries, where reds shift dramatically but adjacent oranges shift partially, creating an unnatural color gradient. Keep hue shifts small (under 15 degrees) unless you have narrowed the range precisely. Large hue shifts are a special-effect technique, not a correction tool.

**Ignoring the feathering zones when adjusting adjacent ranges.** If you boost Reds saturation and reduce Yellows saturation, colors in the overlapping feathering zone between Reds and Yellows receive conflicting adjustments. The result may be a visible band of inconsistent saturation at the red-yellow boundary. Check the range markers to ensure feathering zones are appropriate, or use the targeted adjustment tool to auto-refine the ranges.

**Using Selective Color in Absolute mode without understanding the difference.** Absolute mode applies a fixed percentage-point shift regardless of the existing component level. On colors with little of the adjusted component, this can produce dramatic, unintended shifts. Use Relative mode as the default; switch to Absolute only when you understand and want the non-proportional behavior.

**Stacking too many Hue/Saturation Adjustment Layers without organization.** Multiple Hue/Saturation layers with overlapping range selections can interact in confusing ways. Name each layer descriptively (e.g., "H/S - Blue Sky Desat"), group them logically, and toggle each on/off independently to verify its contribution.

**Applying Hue/Saturation to desaturate for black-and-white conversion.** Setting Master Saturation to -100 removes all color, but the resulting grayscale uses a simplistic formula that does not weight channels perceptually. A blue sky and a red barn of similar luminance may become nearly the same gray, losing all tonal separation. Use the Black & White Adjustment Layer or Channel Mixer for quality grayscale conversion (Chapter 18).

**Confusing the Lightness slider in Hue/Saturation with the Luminance sliders in Camera Raw.** Despite similar names, these operate differently. Camera Raw's Luminance sliders adjust the luminance of color ranges without mixing in white or black, preserving saturation character. Hue/Saturation's Lightness slider mixes in white or black, changing the color character. Do not assume one behaves like the other.

---

## Alternative Approaches

### Camera Raw HSL Panel

For images opened as Smart Objects from Camera Raw, the HSL panel in ACR provides Hue, Saturation, and Luminance sliders for eight color ranges (Reds, Oranges, Yellows, Greens, Aquas, Blues, Purples, Magentas — a finer division than Hue/Saturation's six). The ACR Luminance slider adjusts brightness without adding white or black, producing superior results to Hue/Saturation's Lightness slider for darkening or lightening specific colors.

**RECOMMENDATION:** Perform initial HSL adjustments in Camera Raw when working from RAW files. Use Photoshop's Hue/Saturation and Selective Color for refinements, masked adjustments, and corrections that require layer-based control.

### Color Range Selections with Curves

Instead of Hue/Saturation for color-specific luminosity adjustments, use Select > Color Range to create a selection based on a sampled color, convert the selection to a layer mask on a Curves Adjustment Layer, and adjust the Curves to darken, lighten, or shift the color. This provides full Curves control — including per-channel adjustments — targeted to a specific color. The Fuzziness slider in Color Range controls how broadly the selection extends into similar colors, functioning like the feathering zone in Hue/Saturation.

### Blend If Sliders for Color Targeting

The Layer Style dialog (double-click to the right of a layer name) includes Blend If sliders that can restrict any Adjustment Layer's effect to specific underlying color values. By switching the Blend If dropdown from Gray to a specific channel (Red, Green, Blue) and adjusting the "Underlying Layer" sliders, you can make a Curves or other Adjustment Layer affect only pixels with high Blue values (blues), or only pixels with high Red and low Blue (warm tones). This technique does not use the HSL model at all — it targets by channel value directly.

### Lab Color Mode for Saturation Control

Converting the image to Lab color mode (Image > Mode > Lab Color) provides access to the a* and b* channels, which encode color independently of luminosity. Increasing the contrast of the a* and b* channels (via Curves) boosts saturation without affecting brightness. This technique is mentioned for completeness — it requires a mode conversion (which should be done on a flattened copy), and the Vibrance Adjustment Layer has largely replaced it for most photographic saturation work.

### Replace Color Command

Edit > (or Image > Adjustments >) Replace Color combines a Color Range-style selection with a Hue/Saturation adjustment in a single dialog. It is convenient for quick one-off color replacements but is destructive (not available as an Adjustment Layer). **RECOMMENDATION:** For non-destructive work, replicate the same operation by combining a Color Range selection (as a mask) with a Hue/Saturation Adjustment Layer.

---

## Summary

Targeted color control — adjusting specific hues without affecting others — is the subject of this chapter. Where Chapters 09 and 10 treated color globally or by tonal region, the tools here isolate colors by hue identity.

**Hue/Saturation** is the primary tool for hue-based adjustments. It operates in the HSL color model, dividing the hue wheel into six adjustable ranges (Reds, Yellows, Greens, Cyans, Blues, Magentas), each with independent Hue, Saturation, and Lightness sliders. The targeted adjustment tool enables on-image color identification and direct manipulation. The feathering between ranges ensures smooth transitions. However, the Lightness slider should generally be avoided because it adds white or black rather than adjusting luminance — use Curves or Camera Raw's Luminance sliders instead.

**Selective Color** offers a complementary approach, adjusting colors by their CMYK component makeup rather than by HSL attributes. It cannot rotate hue, but it can modify the component balance of a color — adding Cyan to Reds, Yellow to Greens, and so on. It uniquely provides Whites, Neutrals, and Blacks categories for tonal-range color adjustments. Relative mode adjusts proportionally; Absolute mode adjusts by fixed percentage.

**Vibrance** provides non-linear, adaptive saturation enhancement that boosts low-saturation colors more than high-saturation colors and protects skin tones. It should be the first tool for global saturation increases, with Hue/Saturation's Saturation slider reserved for per-range refinements.

**Channel Mixer** redefines channel contributions through matrix operations, enabling color shifts that neither Hue/Saturation nor Selective Color can produce. It is also the basis for precision black-and-white conversion (Chapter 18).

The critical distinctions: Saturation applies uniformly; Vibrance adapts. Hue/Saturation works in HSL; Selective Color works in CMYK components. The Lightness slider adds white/black; Curves adjusts luminosity. HSL and HSB share the Hue axis but differ in their treatment of lightness/brightness. Understanding these distinctions prevents misapplication and produces superior results.

The professional workflow places targeted color adjustments after global grading and before local adjustments. Use Vibrance first for general enhancement, then Hue/Saturation for per-range refinement, then Selective Color for component-level fine-tuning.

---

## Exercises

### Exercise 1: Saturation vs Vibrance Comparison

Open a photograph that contains both muted and vivid colors — a portrait with a colorful background, or a landscape with both saturated sky and desaturated earth tones.

1. Create a Vibrance Adjustment Layer. Set Vibrance to +40. Leave Saturation at 0. Note the effect on the muted colors, the vivid colors, and any skin tones.
2. Toggle the Vibrance layer off. Create a Hue/Saturation Adjustment Layer. Set Master Saturation to +40. Note the effect on the same areas.
3. Compare: which colors changed more with each tool? Which adjustment looks more natural? Did skin tones become unnatural with Saturation? Did already-vivid colors clip?
4. Reset both layers. Now set Vibrance to +40 and Saturation (in the Vibrance panel) to +10. Evaluate the combined result.

**Success criterion:** You can predict and explain why Vibrance and Saturation produce different results on the same image, and you choose Vibrance as the default for global enhancement.

### Exercise 2: Targeted Hue Shift with Range Markers

Open a photograph with at least two clearly different colors (e.g., a red object against a green background).

1. Create a Hue/Saturation Adjustment Layer.
2. Select Reds. Shift the Hue slider to +30. Observe: does only the red object change? Or are other colors partially affected?
3. Examine the range markers at the bottom of the panel. Note the feathering zones.
4. Narrow the core range by dragging the inner markers closer together. Widen the feathering by dragging the outer markers. How does this change which pixels are affected?
5. Use the targeted adjustment tool: click on the red object. How does the tool refine the range compared to the default Reds setting?

**Success criterion:** You can manually adjust range markers to precisely target a specific hue, and you understand how feathering controls the transition at range boundaries.

### Exercise 3: Selective Color Component Adjustment

Open a landscape photograph with a blue sky.

1. Create a Selective Color Adjustment Layer (Relative method).
2. Select **Cyans** (sky is often more cyan than pure blue). Add Cyan +20. What happens? The sky becomes deeper, more saturated cyan.
3. Now add Black +10 to Cyans. The sky darkens. Compare this to what would happen with the Lightness slider in Hue/Saturation — is the result similar or different?
4. Switch to **Greens.** Remove Yellow -15 (which adds blue to the greens, cooling them). Add Cyan +10 (deepening the greens). Evaluate: does the foliage look cooler and more vivid?
5. Switch between Relative and Absolute methods and repeat step 2. Note the difference in intensity.

**Success criterion:** You understand how CMYK component adjustments affect RGB colors, and you can use Selective Color to refine colors in ways that Hue/Saturation cannot.

### Exercise 4: Lightness Slider vs Curves for Darkening

Open a photograph with a prominent blue sky.

1. Create a Hue/Saturation Adjustment Layer. Select Blues. Set Lightness to -30. Observe the sky: is it darker? Is it muddier? Does the saturation appear to decrease?
2. Toggle the Hue/Saturation layer off. Create a Curves Adjustment Layer.
3. Use Select > Color Range to sample the blue sky (set Fuzziness to about 40). Click OK.
4. With the selection active, click the mask icon on the Curves Adjustment Layer — the selection becomes the layer mask.
5. On the Curves layer, pull the midtones down to darken the blue sky by a comparable amount.
6. Compare the two results side by side: which sky looks deeper and richer? Which looks muddier and more washed out?

**Success criterion:** You understand why the Lightness slider in Hue/Saturation produces inferior darkening compared to Curves, and you choose Curves for color-range luminosity adjustments.

---

## Advanced Exercises

### Advanced Exercise 1: Multi-Layer Color Refinement

Open a complex photograph with at least five distinct colors (e.g., a market scene, a garden, a city street).

1. Create a Vibrance layer for global enhancement.
2. Create separate Hue/Saturation layers for at least three different color ranges, each with a descriptive name.
3. Create a Selective Color layer to fine-tune the component makeup of at least two colors.
4. Group all layers. Toggle the group on/off for before/after.
5. Reduce the opacity of each individual Adjustment Layer until the effect is subtle and natural — no single color should draw disproportionate attention.

**Success criterion:** You can construct a multi-layer targeted color workflow, with each layer serving a specific, identifiable purpose, and the composite result looking natural and intentional.

### Advanced Exercise 2: Isolating a Color for Emphasis

Open a photograph where one color element could be made to stand out more against its surroundings (e.g., a red door in a gray stone wall, a yellow taxi in a muted urban scene).

1. Desaturate the entire image slightly using a Vibrance layer (Vibrance -20 or Saturation -15).
2. Create a Hue/Saturation layer that targets only the accent color. Boost its Saturation by +20 to +30.
3. Use the range markers or targeted adjustment tool to ensure only the desired color is affected.
4. Optionally, use a layer mask to further restrict the saturation boost to the specific object, not just the color range globally.
5. Evaluate: does the accent color draw the eye? Is the effect subtle enough to look intentional, or does it look like a filter?

**Success criterion:** You can use targeted color adjustment to create visual hierarchy through selective saturation, combining global desaturation with local saturation emphasis.

### Advanced Exercise 3: Channel Mixer for Creative Color Shifting

Open a landscape or architectural photograph.

1. Create a Channel Mixer Adjustment Layer.
2. Select the Blue output channel. Reduce Blue from 100% to 70%. Increase Red to 30% (keeping the total at 100%). The blues in the image now have a warm component — skies may shift slightly toward purple or warm blue.
3. Select the Red output channel. Reduce Red from 100% to 85%. Increase Green to 15%. The reds become warmer with a slight yellow-green component.
4. Evaluate the result. Use the layer opacity to moderate the effect.
5. Compare this to what Hue/Saturation or Selective Color would produce. Can you replicate the exact same color shift with those tools?

**Success criterion:** You understand that Channel Mixer creates cross-channel color shifts that other targeted color tools cannot replicate, and you can use it for creative color effects.

---

## Blackbelt Challenge

You receive a photograph of an autumn forest with the following characteristics:

- The canopy contains maples (red-orange), birches (yellow), and oaks (brown-green, not yet fully turned).
- A stream with blue-gray reflections runs through the foreground.
- Exposed rock faces in neutral gray are visible along the stream bank.
- A hiker in a bright red jacket stands on a rock — the jacket is nearly the same hue as the reddest maple leaves.
- The overall image is correctly exposed and white-balanced, but the colors lack the separation and vibrancy the scene had in person.

Without opening Photoshop:

1. **Design a complete targeted color adjustment strategy.** Specify every Adjustment Layer (type, name, settings, order in the stack) you would use to:
    - Enhance the red-orange maples without affecting the hiker's jacket.
    - Warm and vivify the yellow birch leaves without shifting them toward green or orange.
    - Subdue the green oaks slightly so they do not compete with the warm foliage.
    - Preserve the blue-gray stream reflections exactly as they are.
    - Clean any slight color cast from the gray rocks.
    - Maintain natural skin tones if the hiker's face is visible.

2. **Explain how you would isolate the maple leaves from the hiker's red jacket** given that both are the same hue. What tool or technique separates them when hue-based targeting cannot? Describe your masking strategy.

3. **Predict what happens if you boost Master Saturation by +30 instead of using your targeted approach.** Describe the specific problems that would appear in each element of the scene: the maples, the birches, the oaks, the stream, the rocks, the hiker's jacket, and any skin tones.

4. **Explain the Hue/Saturation vs Selective Color decision for each color adjustment.** For each adjustment in your strategy, state whether you chose Hue/Saturation or Selective Color and why. What can one tool do for that specific adjustment that the other cannot?

5. **Design a fallback approach using only Curves and masks** (no Hue/Saturation, no Selective Color, no Vibrance). Describe how you would achieve similar results using per-channel Curves with layer masks or Blend If sliders. What are the advantages and disadvantages compared to your HSL/Selective Color approach?

This challenge requires you to synthesize hue-based targeting, CMYK component adjustment, the Saturation/Vibrance distinction, the Lightness slider problem, spatial masking for same-hue objects, and the relationships between all the color adjustment tools covered in this chapter and the preceding ones.
