---
description: Color grading in Photoshop — creative color manipulation, split toning, color harmonies, Color Balance, Photo Filter, LUTs, Gradient Map, Solid Color fill layers, and the distinction between color correction and color grading.
---

# Chapter 10: Color Grading — Creative Color Manipulation

## Learning Objectives

After completing this chapter, you will be able to:

1. Articulate the distinction between color correction (technical, objective) and color grading (creative, intentional), and explain why correction must precede grading in a professional workflow.
2. Define split toning and explain how applying different color shifts to shadows and highlights creates depth, mood, and visual separation.
3. Identify the three primary color harmony types — complementary, analogous, and triadic — and apply them as deliberate color grading strategies rather than arbitrary color shifts.
4. Use Color Balance adjustment layers to shift color independently in shadows, midtones, and highlights, and explain what the tool does at the data level.
5. Use Photo Filter adjustment layers to apply uniform color washes that simulate optical lens filters, and distinguish this from Color Balance's tonal-range targeting.
6. Explain what a LUT (Look-Up Table) is at the data level, use Color Lookup adjustment layers to apply LUTs, and articulate the fundamental limitation of LUTs as fixed, non-adjustable mappings.
7. Use Gradient Map adjustment layers to map tonal values to arbitrary colors, creating split-tone effects, duotones, and creative color treatments.
8. Use Solid Color fill layers with blend modes and reduced opacity as a controlled method for applying uniform color washes.
9. Choose the appropriate grading tool for a given creative intent, based on what each tool actually does to the data and how much control it offers.
10. Apply color grading as Adjustment Layers within a non-destructive workflow, positioned correctly in the layer stack above color correction.

---

## Conceptual Foundation

Color correction and color grading are different operations with different goals. Color correction, covered in Chapter 09, is technical: you remove unwanted color casts, establish accurate white balance, and ensure that neutral tones are actually neutral. The goal is fidelity — making the image accurately represent the scene, or at least establishing a technically clean starting point. Color correction asks: "What should the colors be?"

Color grading asks a different question: "What do I want the colors to feel like?"

Color grading is the deliberate, creative manipulation of color to establish mood, atmosphere, and visual coherence. It is what makes a film scene feel cold and clinical, or warm and nostalgic. It is why a landscape photograph can evoke the heavy golden light of late afternoon, or the brittle blue of winter twilight. The colors in a graded image are not "correct" in any technical sense — they are intentional.

This distinction matters because the two operations must happen in the right order. If you grade before correcting, you are building creative decisions on top of technical errors. A blue color cast in the shadows is not the same as a deliberate cool-shadow grade — the cast is uncontrolled and affects the entire shadow range indiscriminately, while the grade is a measured shift applied where you intend it. Correct first, then grade. The correction establishes the neutral foundation; the grading departs from it with purpose.

Photoshop provides several tools for color grading, each with different strengths and different relationships to the image data. Some operate on tonal ranges (Color Balance). Some simulate physical phenomena (Photo Filter). Some apply fixed mappings (LUTs). Some remap the entire tonal-to-color relationship (Gradient Map). Understanding what each tool does — not just what it looks like — is what allows you to choose the right tool for a given creative intent and to combine tools effectively.

---

## Terminology

### Color Grading (vs Color Correction)

**Definition:** Color grading is the intentional, creative manipulation of an image's color to establish mood, atmosphere, or visual style. It is distinct from color correction, which is the technical process of removing unwanted color casts and establishing accurate color reproduction. Color correction is objective (there is a measurably correct result); color grading is subjective (the result depends on creative intent).

**Meaning in photographic practice:** In a professional workflow, color correction and color grading are sequential steps. Color correction (Chapter 09) comes first: you neutralize color casts, set accurate white balance, and ensure that grays are gray. Once the image is technically clean, color grading introduces deliberate color shifts to serve the image's expressive purpose. A portrait might be graded with warm highlights and cool shadows to create a filmic quality. A landscape might be graded to push the warm tones of golden hour further than reality. A moody urban scene might be graded toward desaturated teal shadows with warm orange highlights — a widely used complementary color grade.

The distinction is not about which tools you use. You can perform both correction and grading with Curves. The distinction is about intent: correction removes what should not be there; grading adds what you want to be there.

**Photoshop implementation:** Photoshop does not enforce the separation between correction and grading — there is no "grading mode." The separation is a workflow discipline. In practice, you maintain correction Adjustment Layers (typically per-channel Curves, as covered in Chapter 09) and grading Adjustment Layers (Color Balance, Photo Filter, Gradient Map, Solid Color fill layers, Color Lookup, or additional Curves layers) as separate layers or layer groups in the stack. Grading layers sit above correction layers.

**Related concepts:** Color correction (Chapter 09), per-channel Curves (Chapter 07), white balance, neutral reference, Adjustment Layer (Chapter 06), layer stack order.

**Common misconception:** "Color grading is just more extreme color correction." It is not a matter of degree. A small color correction is still correction — it corrects an error. A subtle color grade is still grading — it introduces a deliberate departure from neutral. An image can require significant correction (removing a heavy tungsten cast) and then receive only gentle grading (a slight warmth in the highlights). Or it can need minimal correction (the white balance was accurate) and then undergo dramatic grading (pushing the entire image toward a specific palette). The operations are independent in both direction and magnitude.

---

### Split Toning

**Definition:** Split toning is a color grading technique in which different colors are applied to different tonal ranges of the image — typically one color to the shadows and a different color to the highlights. The term originates from darkroom printing processes (such as sepia toning and selenium toning applied selectively) where chemical toners affected shadows and highlights differently.

**Meaning in photographic practice:** Split toning creates visual depth and separation by giving shadows and highlights distinct color identities. The most common approach uses complementary or near-complementary colors: warm highlights with cool shadows, or cool highlights with warm shadows. This produces a natural-looking separation because warm and cool tones create a perceptual sense of depth — warm advances, cool recedes. But any color combination can be used depending on creative intent.

Split toning is not the same as a uniform color wash. A uniform wash (such as a Photo Filter) shifts the entire image toward one color. Split toning creates a gradient of color through the tonal range — one color dominates the darks, a different color dominates the lights, and the midtones transition between them. This is what gives split-toned images their characteristic depth.

**Photoshop implementation:** Split toning can be achieved through several tools in Photoshop:

- **Per-channel Curves** (Chapter 07): Pull the lower portion of the Blue curve down (warming shadows toward yellow) while raising the upper portion (cooling highlights toward blue). This is the most precise method.
- **Color Balance**: Set different color shifts for Shadows, Midtones, and Highlights using the three tone selectors.
- **Gradient Map**: Map the tonal range to a gradient that transitions from one color in the shadows to another in the highlights.
- **Camera Raw / Lightroom**: The Color Grading panel (formerly Split Toning) provides direct shadow/midtone/highlight color wheels.

**Related concepts:** Color grading, complementary colors, Color Balance, Gradient Map, per-channel Curves (Chapter 07), warm-cool contrast.

**Common misconception:** "Split toning only works with two colors — one for shadows and one for highlights." Two colors is the classic approach, but modern implementations (Camera Raw's Color Grading panel, or Photoshop's Color Balance with three tone selectors) allow independent color shifts in shadows, midtones, and highlights — effectively three-way split toning. You can also stack multiple Gradient Maps or Curves layers to create more complex color transitions through the tonal range.

---

### Color Harmony (Complementary, Analogous, Triadic)

**Definition:** Color harmony refers to combinations of colors that produce a visually coherent, aesthetically effective relationship. The three most commonly applied harmony types in color grading are:

- **Complementary:** Two colors opposite each other on the color wheel (e.g., orange and blue, red and cyan, yellow and violet). Complementary pairs produce maximum contrast between the two colors while maintaining a balanced visual relationship.
- **Analogous:** Colors adjacent to each other on the color wheel (e.g., yellow, yellow-orange, and orange). Analogous harmonies produce a unified, cohesive feel with low color contrast.
- **Triadic:** Three colors evenly spaced around the color wheel (e.g., red, green, and blue; or orange, green, and violet). Triadic harmonies offer variety while maintaining balance, though they are less common in photographic grading than complementary and analogous schemes.

**Meaning in photographic practice:** Color harmonies are not arbitrary aesthetic rules — they describe relationships that the human visual system perceives as coherent. In color grading, harmonies guide the selection of colors for split toning and overall palette control. The teal-and-orange grade used extensively in cinema is a complementary harmony: skin tones (warm orange) contrast with the graded shadows and backgrounds (cool teal). This works because complementary colors create maximum separation while remaining visually balanced. An autumn landscape graded in analogous warm tones (yellow, orange, red-orange) creates cohesion and intensity.

Understanding color harmonies prevents a common problem in grading: applying color shifts that feel random or discordant. When you choose your grading colors based on a harmony relationship, the result reads as intentional even to viewers who cannot articulate why.

**Photoshop implementation:** Photoshop does not include a color harmony tool or color wheel selector in its grading Adjustment Layers. You apply harmony principles by choosing your grading colors deliberately: when setting the color for a Photo Filter, the hue for a Color Balance shift, or the gradient stops for a Gradient Map, you select colors that form a coherent harmony relationship. External tools such as Adobe Color (color.adobe.com) can help visualize harmony relationships and generate palettes. The knowledge is in your head; the tool executes your choice.

**Related concepts:** Color wheel, complementary colors, split toning, color grading, Gradient Map, warm-cool contrast.

**Common misconception:** "Complementary colors always look good together." Complementary harmonies produce maximum color contrast, which can be either effective or garish depending on saturation and proportion. A split tone of desaturated warm highlights and desaturated cool shadows looks cinematic. The same complementary pair at full saturation — vivid orange and vivid blue — looks like a sports team logo. Harmony describes a relationship between hues; the effectiveness depends equally on saturation, lightness, and the proportion each color occupies in the image.

---

### Color Balance Adjustment Layer

**Definition:** A Photoshop Adjustment Layer that shifts the color balance of the image by adjusting the relative strength of complementary color pairs (Cyan-Red, Magenta-Green, Yellow-Blue) independently within three tonal ranges: Shadows, Midtones, and Highlights. Each slider moves between a color and its complement along a single axis.

**Meaning in photographic practice:** Color Balance is the most intuitive grading tool in Photoshop for split toning. Its three tone selectors (Shadows, Midtones, Highlights) let you apply different color shifts to different parts of the tonal range — the fundamental operation of split toning. Its slider axes are arranged as complementary pairs, which naturally encourages harmonious color choices. Moving one slider toward Red simultaneously moves away from Cyan; moving toward Blue simultaneously moves away from Yellow. This paired structure prevents the accidental introduction of unrelated color shifts.

Color Balance is simpler than per-channel Curves but less precise: you cannot target a specific tonal value (e.g., "only the quarter-tones"), only the broad categories of Shadows, Midtones, and Highlights. For many grading tasks, this level of control is sufficient.

**Photoshop implementation:** Available as an Adjustment Layer (Layer > New Adjustment Layer > Color Balance, or the Color Balance icon in the Adjustments panel). The Properties panel displays three sliders (Cyan/Red, Magenta/Green, Yellow/Blue) and a Tone selector with three radio buttons (Shadows, Midtones, Highlights). A "Preserve Luminosity" checkbox, when checked, prevents the color shift from also shifting the brightness of the affected tonal range. **RECOMMENDATION:** Keep "Preserve Luminosity" checked for grading work. Without it, shifting toward lighter colors (Yellow, Cyan) also brightens the image, and shifting toward darker colors (Blue, Red) also darkens it — mixing tonal and color changes in a single control.

**Related concepts:** Split toning, complementary colors, per-channel Curves (Chapter 07), Selective Color (Chapter 11), Preserve Luminosity.

**Common misconception:** "Color Balance is just a simplified version of Curves." The operations are related but not identical. Color Balance works in complementary color pairs and operates on three broad tonal ranges. Per-channel Curves operates on individual channels with arbitrary precision across the tonal range. Color Balance's "Preserve Luminosity" option has no direct equivalent in Curves — when you shift a channel in Curves, you are changing both the color balance and the luminosity of the affected tones simultaneously. Color Balance is a different tool with a different relationship to the data, not simply a dumbed-down Curves.

---

### Photo Filter Adjustment Layer

**Definition:** A Photoshop Adjustment Layer that simulates the effect of placing a colored optical filter in front of the camera lens. It applies a uniform color tint across the entire image, with a Density slider controlling the strength of the effect. Unlike Color Balance, Photo Filter does not distinguish between tonal ranges — it applies the same color shift to shadows, midtones, and highlights equally.

**Meaning in photographic practice:** Photo Filter is useful for global color washes — warming an entire image, cooling it, or shifting it toward a specific hue. Traditional photographic warming filters (81-series) and cooling filters (82-series) are available as presets, along with named color presets (Deep Yellow, Sepia, Violet, etc.). The key characteristic of Photo Filter is its uniformity: it tints the entire tonal range with the same color. This makes it useful for overall mood shifts but unsuitable for split toning, which requires different colors in different tonal ranges.

**Photoshop implementation:** Available as an Adjustment Layer (Layer > New Adjustment Layer > Photo Filter). The Properties panel offers a dropdown of preset filters (Warming Filter 85, Cooling Filter 80, etc.) or a custom color picker. The **Density** slider (1-100%) controls how strong the filter effect is. A **Preserve Luminosity** checkbox, when checked, prevents the filter from darkening the image (colored filters reduce light transmission; Preserve Luminosity compensates for this). **RECOMMENDATION:** Leave "Preserve Luminosity" checked unless you specifically want the darkening effect of a physical filter.

**FACT:** The preset filter names (Warming 85, Cooling 80, etc.) reference Kodak Wratten filter numbers. The "85" warming filter converts daylight-balanced film for use under tungsten lighting; the "80" cooling filter does the reverse. In digital photography, these physical filters are largely obsolete — white balance correction handles the same task. But the filter presets in Photoshop's Photo Filter provide a convenient starting point for color grading.

**Related concepts:** Color Balance, Solid Color fill layer, color wash, warming/cooling, optical filter.

**Common misconception:** "Photo Filter and Color Balance are interchangeable." They operate differently. Photo Filter applies a uniform color wash across the entire image — shadows, midtones, and highlights all receive the same tint. Color Balance lets you apply different color shifts to shadows, midtones, and highlights independently. Use Photo Filter for a global color wash. Use Color Balance for split toning or tonal-range-specific grading.

---

### Color Lookup (LUT) Adjustment Layer

**Definition:** A Photoshop Adjustment Layer that applies a Look-Up Table (LUT) to the image. The LUT defines a complete mapping from input RGB values to output RGB values — for every possible input color, the LUT specifies what the output color should be. The Color Lookup Adjustment Layer loads LUT files and applies them to the image data.

**Meaning in photographic practice:** Color Lookup layers provide instant, one-click color grades. They are widely used in video production and film color grading, where LUTs encode the "look" of specific film stocks, cinematic styles, or custom color treatments. In still photography, they offer a fast way to apply complex, pre-built color grades that would be time-consuming to construct manually with individual adjustment tools.

The fundamental limitation of a LUT is that it is a fixed mapping. Unlike Curves or Color Balance, where you can adjust sliders and control points to modify the effect, a LUT is a black box: it maps input colors to output colors according to a predetermined table, and you cannot modify the mapping itself. Your only control is the Adjustment Layer's opacity, which blends the LUT's output with the original image. You cannot, for example, increase the LUT's effect in the shadows while decreasing it in the highlights — not without masking.

**Photoshop implementation:** Available as an Adjustment Layer (Layer > New Adjustment Layer > Color Lookup). The Properties panel provides three dropdown menus for loading LUTs in different formats: **3DLUT File** (.cube, .3dl, .look), **Abstract** (Photoshop abstract profiles), and **Device Link** (ICC device link profiles). Photoshop ships with a small set of built-in LUTs; additional LUTs can be downloaded or purchased from third parties and loaded via the "Load 3D LUT" option. The most common format for distributing LUTs is the .cube file, which is widely supported across applications.

**Related concepts:** LUT (Look-Up Table), color grading, Adjustment Layer opacity, masking (Chapter 05).

**Common misconception:** "LUTs are adjustable — you can fine-tune them to your image." A LUT is a fixed mapping table. You cannot open it up and adjust its internal values within Photoshop. What you can do is: reduce the Adjustment Layer's opacity to lessen the overall effect, use a layer mask to restrict where the LUT applies, or stack additional Adjustment Layers (Curves, Color Balance, Hue/Saturation) above or below the LUT layer to modify the result. But the LUT itself is a preset — it does the same thing to every image it is applied to, which is precisely why a LUT that looks excellent on one image may look wrong on another.

---

### LUT (Look-Up Table)

**Definition:** A Look-Up Table (LUT) is a data structure that defines a mapping from input values to output values. In color grading, a 3D LUT maps every possible combination of input RGB values to a corresponding set of output RGB values. Rather than applying a mathematical formula, a LUT stores the answer for every possible input (or, more precisely, for a grid of sample points with interpolation between them). A 3D LUT with 33 grid points per axis contains 33 x 33 x 33 = 35,937 sample points, each specifying the output RGB for that input RGB position.

**Meaning in photographic practice:** A LUT encodes a complete color transformation — it can simultaneously adjust brightness, contrast, saturation, hue shifts, and split toning across the entire color and tonal range. A single LUT can reproduce the combined effect of dozens of individual adjustments. This is why LUTs are popular in film production: a colorist builds a complex grade using multiple tools, then exports the result as a LUT that can be applied consistently to every frame.

For photographers, the key insight is that a LUT is a snapshot of a specific transformation applied to a specific type of input. A LUT designed for a properly exposed, color-corrected image will produce poor results on an underexposed image with a color cast. A LUT designed for log-encoded video footage will produce extreme results on a standard gamma-encoded photograph. LUTs are not magic — they are fixed transformations that assume specific input characteristics.

**Photoshop implementation:** Photoshop applies LUTs through the Color Lookup Adjustment Layer. The most common LUT format is **.cube** (a plain-text file listing input-output RGB triplets on a 3D grid). Photoshop also supports .3dl, .look, and ICC abstract/device link profiles as LUT sources. Third-party LUT files are placed in the appropriate system directory or loaded directly via the file browser in the Color Lookup Properties panel.

**Related concepts:** Color Lookup Adjustment Layer, color grading, 3D color space, interpolation, film emulation.

**Common misconception:** "A LUT is the same as a preset or filter." A preset (in Camera Raw or Lightroom) is a set of slider values — you can see and modify every individual adjustment. A LUT is a baked mapping table — you cannot decompose it into individual adjustments. A preset is a recipe; a LUT is the finished dish. You can modify a recipe ingredient by ingredient; you cannot un-mix the dish. This is why LUTs offer less creative control than building a grade from individual adjustment layers.

---

### Gradient Map Adjustment Layer

**Definition:** A Photoshop Adjustment Layer that replaces the image's color information with colors sampled from a gradient, based on the luminosity of each pixel. The darkest pixels receive the color at the left end of the gradient, the brightest pixels receive the color at the right end, and intermediate tones receive colors from the corresponding positions along the gradient. The result maps the image's tonal range onto an arbitrary color range.

**Meaning in photographic practice:** Gradient Map is one of the most powerful and least understood grading tools in Photoshop. At its simplest, a two-stop gradient from dark blue to warm orange creates a split tone: shadows become blue, highlights become orange, and midtones transition between the two. But the gradient can have any number of color stops at any positions, creating complex multi-point color mappings through the tonal range.

Gradient Map is fundamentally different from other grading tools because it replaces color entirely based on luminosity. Color Balance and Photo Filter shift existing colors; Gradient Map overwrites them. A red object and a green object at the same luminosity level will receive the same gradient color. This total replacement is the tool's strength (for dramatic recoloring effects and precise tonal-to-color mapping) and its limitation (it destroys all original color information at full opacity). At reduced opacity or in specific blend modes (Color, Soft Light), Gradient Map becomes a nuanced grading tool that blends the mapped colors with the original image.

**Photoshop implementation:** Available as an Adjustment Layer (Layer > New Adjustment Layer > Gradient Map). The Properties panel displays the current gradient; clicking on it opens the Gradient Editor, where you can add, remove, and reposition color stops and adjust their opacity. The "Dither" checkbox adds noise to reduce banding in smooth gradient transitions. The "Reverse" checkbox flips the gradient direction.

**RECOMMENDATION:** For color grading (rather than complete recoloring), use the Gradient Map Adjustment Layer at reduced opacity (10-30%) or set the layer's blend mode to Color or Soft Light. At 100% opacity in Normal mode, the Gradient Map completely replaces the image's color with the gradient colors, which is useful for duotones and stylized effects but too extreme for most photographic grading.

**Related concepts:** Split toning, duotone, luminosity, blend modes (Chapter 14), tonal range, Gradient Editor, layer opacity.

**Common misconception:** "Gradient Map is only useful for black-and-white toning effects." Gradient Map is often demonstrated as a sepia or duotone tool, but it is far more versatile. A multi-stop gradient with subtle, desaturated colors applied at low opacity can create sophisticated split toning. A gradient transitioning from a deep teal through neutral gray to a pale gold, applied in Soft Light mode, produces a cinematic complementary grade. The tool's potential scales with the complexity of your gradient and the blend mode you apply it through.

---

### Solid Color Fill Layer (with Blend Mode)

**Definition:** A fill layer (Layer > New Fill Layer > Solid Color) that fills the entire layer with a single, uniform color. When combined with a blend mode other than Normal and reduced opacity, the Solid Color fill layer applies the chosen color as a controlled tint or color wash over the layers beneath it. The blend mode determines how the fill color interacts with the underlying image data.

**Meaning in photographic practice:** A Solid Color fill layer in a blend mode such as Soft Light, Overlay, or Color is one of the simplest and most controllable methods for applying a color wash to an image. The color is chosen once in the Color Picker. The intensity is controlled by the layer opacity. The interaction with the underlying image is determined by the blend mode. This gives you three independent controls — hue, intensity, and blending behavior — in a single, easily adjustable layer.

The technique is commonly used to unify the color palette of an image (a desaturated warm tone in Color mode at 10-15% opacity brings disparate colors into a cohesive warm family) or to add a color accent (a deep blue in Soft Light at 8-10% opacity adds a subtle cool undertone to the entire image).

**Photoshop implementation:** Create via Layer > New Fill Layer > Solid Color (or click the "New Fill or Adjustment Layer" button at the bottom of the Layers panel and select Solid Color). The Color Picker opens immediately; select the desired color. Then change the layer's blend mode from Normal to the desired mode (Soft Light, Color, and Overlay are the most common for grading) and reduce opacity to taste. Double-click the layer thumbnail at any time to reopen the Color Picker and change the color.

**Related concepts:** Blend modes (Chapter 14), Photo Filter, Color mode, Soft Light mode, Overlay mode, layer opacity.

**Common misconception:** "A Solid Color fill layer in Normal mode at low opacity is the same as Photo Filter." They are similar in effect but differ in implementation. Photo Filter applies a tint while optionally preserving luminosity. A Solid Color fill layer in Normal mode at low opacity mixes the solid color with the underlying image, which shifts both the color and the luminosity (the color fill brightens or darkens the image depending on whether the fill color is lighter or darker than the underlying pixels). To approximate Photo Filter's behavior, use the Solid Color fill layer in Color blend mode, which affects only the color (hue and saturation) without changing the luminosity.

---

### Selective Color Adjustment Layer

**Definition:** A Photoshop Adjustment Layer that allows targeted adjustment of the CMYK ink components within specific color ranges. You select a target color range (Reds, Yellows, Greens, Cyans, Blues, Magentas, Whites, Neutrals, or Blacks) and then adjust the Cyan, Magenta, Yellow, and Black component sliders within that range. The adjustment affects only pixels that fall within the selected color range.

**Meaning in photographic practice:** Selective Color is a precision tool for targeted color refinement. Where Color Balance shifts the entire shadow, midtone, or highlight range, Selective Color targets specific color families — you can shift only the reds, or only the blues, while leaving all other colors untouched. This makes it valuable for refining individual color populations within a graded image: warming only the skin tones, shifting only the sky color, or adjusting only the greens in foliage.

Selective Color is introduced here because it bridges the gap between grading (this chapter) and targeted color manipulation (Chapter 11). It is a grading tool when used to refine the overall color palette, and a targeted color tool when used to adjust specific color populations. Chapter 11 covers Selective Color's full capabilities in detail.

**Photoshop implementation:** Available as an Adjustment Layer (Layer > New Adjustment Layer > Selective Color). The Properties panel displays a dropdown for selecting the target color range and four sliders: Cyan, Magenta, Yellow, and Black. A "Relative" / "Absolute" toggle at the bottom controls how the slider percentages are applied: Relative adjusts the existing amount proportionally; Absolute adds or subtracts a fixed amount.

**Related concepts:** Chapter 11 (HSL and Selective Color Control), CMYK ink model, targeted color adjustment, color families.

**Common misconception:** "Selective Color only works in CMYK mode." Despite using CMYK slider labels, Selective Color works in RGB mode. The CMYK labels describe the direction of the color shift, not the color mode of the document. Increasing Cyan in the Reds range shifts red pixels toward cyan (cooler, less orange). You do not need to convert your document to CMYK to use Selective Color.

---

### Color Temperature (Warm/Cool in Grading Context)

**Definition:** In the context of color grading, color temperature refers to the perceived warmth or coolness of the image's color palette. Warm colors (red, orange, yellow) are associated with higher color temperatures in terms of visual perception (sunlight, firelight, warmth), while cool colors (blue, cyan, violet) are associated with lower perceived warmth (shade, twilight, cold). This is the perceptual meaning of warm and cool in grading, which is related to but not identical with the Kelvin-scale color temperature of light sources.

**Meaning in photographic practice:** Color temperature in grading is a creative tool, not a white balance correction. In Chapter 09, color temperature correction adjusts white balance to neutralize a color cast — removing unwanted warmth or coolness. In color grading, you deliberately introduce warmth or coolness to serve the image's mood. The same photographer who corrects a tungsten cast in Chapter 09 might then add back a measured amount of warmth in the grade, because the warm light was part of the scene's character — the correction removes the uncontrolled cast; the grade reintroduces warmth as a deliberate choice.

Warm-cool contrast is one of the most powerful tools in color grading. Warm tones perceptually advance (appear closer to the viewer); cool tones perceptually recede (appear farther away). This creates depth. A landscape graded with warm foreground tones and cool background tones enhances the sense of spatial recession. A portrait with warm skin tones against a cool background separates the subject from the environment.

**Photoshop implementation:** There is no single "color temperature" slider in Photoshop's Adjustment Layers (unlike Camera Raw, which has a Temperature slider). In Photoshop, you control perceived color temperature through the tools covered in this chapter: Color Balance (shift toward Yellow/Red for warmth, toward Blue/Cyan for coolness), Photo Filter (select a warming or cooling filter), Curves (raise the Red curve and lower the Blue curve for warmth, or the reverse for coolness), or Gradient Map (use warm and cool colors at appropriate tonal positions).

**Related concepts:** Color temperature (Kelvin), white balance (Chapter 09), warm-cool contrast, split toning, Color Balance.

**Common misconception:** "Color temperature in grading is the same as white balance adjustment." White balance correction (Chapter 09) targets technical accuracy — making whites white and grays gray under the scene's illumination. Color temperature in grading is a creative departure from accuracy. You might correct a white balance error of 500K and then grade the image 200K warmer than neutral, because the warmth serves the mood. The correction and the grade may move in the same direction, but they are different operations with different goals, and they should exist as separate Adjustment Layers in the stack.

---

## Theory

### Color Harmonies and Their Application in Grading

Color harmonies describe relationships between hues on the color wheel. In color grading, they provide a framework for choosing colors that work together rather than against each other.

**The Color Wheel for Grading Purposes**

The relevant color wheel for grading is the artist's color wheel (red, orange, yellow, green, blue, violet), not the RGB additive wheel (red, green, blue) or the CMYK subtractive wheel (cyan, magenta, yellow). The artist's wheel is more perceptually uniform — colors that appear similar are near each other, and colors that appear maximally different are opposite each other. When choosing grading colors, think in terms of the artist's wheel.

**Complementary Harmonies in Practice**

Complementary pairs sit opposite each other on the color wheel:

| Complementary Pair | Grading Application |
|--------------------|-------------------|
| Orange and Blue (Teal) | The most widely used cinematic split tone. Warm skin tones against cool backgrounds. Effective because skin tones fall naturally in the orange range. |
| Red and Cyan | High-contrast, dramatic. Common in action and thriller grading. More aggressive than orange/blue. |
| Yellow and Violet | Warm but unusual. Creates a distinctive, slightly surreal quality. Less common in photography than in painting. |
| Green and Magenta | Rarely used as a primary grade because green tones in skin are unflattering. More common as a secondary accent. |

**INTERPRETATION:** Complementary harmonies dominate film and photographic grading because they create maximum color contrast while maintaining balance. The orange/teal combination is so prevalent in cinema that it has become a cliche — but it persists because it works. The human visual system responds strongly to complementary contrast, and the fact that skin tones occupy the warm side of the orange/blue pair means that grading the non-skin areas toward teal automatically separates the subject from the background.

**Analogous Harmonies in Practice**

Analogous colors are neighbors on the color wheel. In grading, an analogous palette produces cohesion and unity:

| Analogous Group | Grading Application |
|----------------|-------------------|
| Yellow, Orange, Red | Autumnal warmth. Golden hour enhancement. Nostalgic, romantic mood. |
| Blue, Cyan, Green | Cold, clinical, or aquatic mood. Winter scenes. Introspective or somber tone. |
| Red, Magenta, Violet | Twilight, theatrical, dramatic. Uncommon but distinctive. |

Analogous harmonies work by restricting the color palette to a narrow arc of the wheel. The result is an image that feels unified — everything belongs to the same color family. The trade-off is reduced color contrast: without opposing colors, the image can feel monochromatic or flat if not supported by strong tonal contrast.

**Triadic Harmonies in Practice**

Triadic harmonies use three colors evenly spaced around the wheel (120 degrees apart). They are less common in photographic grading because managing three distinct color populations requires careful control. In practice, triadic grades usually emphasize one color and use the other two as accents.

**RECOMMENDATION:** Begin with complementary harmonies. They are the most forgiving to implement, the most immediately effective, and the easiest to control with Photoshop's split-toning tools (Color Balance, Gradient Map). Once you are comfortable with complementary splits, explore analogous harmonies for images where cohesion matters more than contrast. Triadic grades require more experience and are best reserved for stylized or editorial work.

### Warm-Cool Contrast and Perceptual Depth

Warm and cool colors create a perceptual depth effect that is independent of perspective, scale, or atmospheric haze. The human visual system interprets warm colors as advancing (closer) and cool colors as receding (farther). This is why warm-cool split toning enhances the sense of three-dimensionality in photographs:

- In a landscape, warm foreground tones and cool background tones reinforce the spatial depth already present in the scene.
- In a portrait, warm skin tones against a cool background push the subject forward, increasing visual separation.
- In an interior, warm light from windows or lamps against cool ambient shadow creates a sense of light direction and space.

**FACT:** The warm-cool depth effect is a perceptual phenomenon rooted in the chromatic aberration of the human eye. Short-wavelength light (blue) focuses at a different distance on the retina than long-wavelength light (red), creating a depth cue. This is not a strong enough cue to determine distance precisely, but it contributes to the overall perception of spatial relationships, especially in flat media like photographs and paintings.

### Emotional Associations of Color

Color associations are culturally influenced and context-dependent, but some broad tendencies are consistent enough to be useful in grading:

| Color Direction | Common Associations | Grading Effect |
|----------------|--------------------|--------------------|
| Warm (orange, yellow, amber) | Sunlight, warmth, comfort, nostalgia, intimacy | Creates an inviting, golden, nostalgic quality. Common in lifestyle, portrait, and golden-hour landscape grading. |
| Cool (blue, cyan, teal) | Shade, distance, solitude, melancholy, calm | Creates a detached, contemplative, or somber mood. Common in moody landscapes, urban photography, and editorial work. |
| Desaturated warm | Faded memories, age, vintage, film | Produces a muted, analog quality. Common in vintage-style and documentary grading. |
| Desaturated cool | Overcast, industrial, bleakness, sterility | Produces a flat, muted coldness. Common in dramatic, documentary, and photojournalistic grading. |
| High saturation | Vibrancy, energy, artificiality | Produces intensity and impact. Risk of looking garish if not controlled. |
| Low saturation | Subtlety, restraint, realism, solemnity | Produces a muted, understated quality. Risk of looking flat without strong tonal contrast to compensate. |

**INTERPRETATION:** These associations are generalizations, not rules. Context matters: warm tones can feel oppressive in a desert scene; cool tones can feel refreshing in a summer shade scene. The associations are useful as starting points for grading decisions, not as formulas to apply mechanically. The best color grades arise from observing the emotional quality the image already suggests and enhancing it through deliberate color choices.

### The Relationship Between Saturation and Grading Effectiveness

A common mistake in color grading is applying color shifts at too high a saturation. Effective grading is almost always more desaturated than beginners expect. There are two reasons for this:

1. **Perceptual sensitivity:** The human visual system is sensitive to subtle color shifts. A 5-10% color bias is enough to establish a mood. A 30% bias reads as an obvious filter effect rather than an inherent quality of the light.

2. **Interaction with existing colors:** At high saturation, a grade overwhelms the image's original colors, flattening the color diversity that gives the photograph its visual richness. At low saturation, the grade tints the existing colors without replacing them — the original palette remains visible through the grade, creating a layered, complex color quality.

**RECOMMENDATION:** When applying any grading tool, start with its effect far lower than you think necessary. Reduce the Adjustment Layer opacity to 10-15% and evaluate. Increase only if the effect is genuinely too subtle. Professional color grades are measured in single-digit percentages of opacity, not double digits.

---

## Photoshop Implementation

### Color Balance: Detailed Walkthrough

**Creating the Adjustment Layer:** Click the Color Balance icon in the Adjustments panel, or go to Layer > New Adjustment Layer > Color Balance. Name it descriptively (e.g., "Grade - Split Tone").

**The three sliders:**

| Slider | Left End | Right End | Effect of Moving Right |
|--------|----------|-----------|----------------------|
| Cyan/Red | Cyan | Red | Shifts toward Red (warmer). Moving left shifts toward Cyan (cooler). |
| Magenta/Green | Magenta | Green | Shifts toward Green. Moving left shifts toward Magenta. |
| Yellow/Blue | Yellow | Blue | Shifts toward Blue (cooler). Moving left shifts toward Yellow (warmer). |

**The Tone selector:** Three radio buttons — Shadows, Midtones, Highlights — determine which tonal range the sliders affect. You must select one range at a time, set its sliders, then switch to the next range. The settings for all three ranges are active simultaneously.

**Building a basic split tone with Color Balance:**

1. Select **Shadows**. Move the Cyan/Red slider slightly toward Red (+5 to +10). Move the Yellow/Blue slider toward Blue (+5 to +15). This gives shadows a cool-warm tint.
2. Select **Highlights**. Move the Cyan/Red slider toward Red (+5 to +10). Move the Yellow/Blue slider toward Yellow (-5 to -10). This warms the highlights.
3. Select **Midtones**. Leave near neutral, or apply a very gentle shift to bridge the shadow and highlight tones.

**Preserve Luminosity:** Check this box. Without it, the color shifts also change the brightness of the affected tones — adding Blue darkens shadows further, adding Yellow brightens highlights. With it checked, the luminosity is held constant and only the color changes.

**Strengths:** Intuitive complementary-pair sliders. Direct shadow/midtone/highlight targeting. Easy to build split tones quickly.

**Weaknesses:** Only three tonal ranges — no ability to target a specific tonal value. The transitions between Shadows, Midtones, and Highlights are fixed and cannot be adjusted. Less precise than per-channel Curves for tonal targeting.

### Photo Filter: Detailed Walkthrough

**Creating the Adjustment Layer:** Click the Photo Filter icon in the Adjustments panel, or go to Layer > New Adjustment Layer > Photo Filter.

**Presets vs Custom color:**

The dropdown offers preset filters:

| Preset Category | Examples | Effect |
|----------------|----------|--------|
| Warming Filters | Warming Filter (85), Warming Filter (LBA), Warming Filter (81) | Shifts toward warm amber/orange. The 85 and LBA are stronger; 81 is more subtle. |
| Cooling Filters | Cooling Filter (80), Cooling Filter (LBB), Cooling Filter (82) | Shifts toward cool blue. The 80 and LBB are stronger; 82 is more subtle. |
| Named Colors | Deep Yellow, Deep Blue, Violet, Sepia, and others | Apply a tint of the named color at the specified density. |

Alternatively, select the **Color** radio button and click the color swatch to open the Color Picker for any arbitrary color.

**The Density slider:** Controls the strength of the filter from 1% (barely perceptible) to 100% (full tint). For grading purposes, densities between 5% and 25% are typical. Values above 40% produce heavy, obvious tinting.

**Preserve Luminosity:** Same function as in Color Balance — check it to prevent the filter from darkening or brightening the image.

**Strengths:** Simple, fast, intuitive. Preset naming references real optical filters, which is useful if you have experience with physical filtration. Single-control intensity (Density slider).

**Weaknesses:** Uniform application — no tonal range targeting. Cannot create split tones with a single Photo Filter layer (you would need two Photo Filter layers with complementary masks). Less versatile than Color Balance or Gradient Map for grading.

**RECOMMENDATION:** Use Photo Filter for simple, uniform color washes — warming an entire image, adding a global cool tone, or simulating the effect of a filter you would have used in-camera. For split toning or tonal-range-specific grading, use Color Balance or Gradient Map instead.

### Color Lookup (LUT): Detailed Walkthrough

**Creating the Adjustment Layer:** Click the Color Lookup icon in the Adjustments panel, or go to Layer > New Adjustment Layer > Color Lookup.

**Loading a LUT:**

The Properties panel offers three dropdown menus:

| Dropdown | Format | Description |
|----------|--------|-------------|
| 3DLUT File | .cube, .3dl, .look | Standard 3D LUT format. Most third-party LUTs use .cube. |
| Abstract | Photoshop abstract profiles | Adobe's proprietary format for color transformations. |
| Device Link | ICC device link profiles | ICC profiles that encode a direct device-to-device transformation. |

Select a dropdown and choose from the built-in options, or use "Load 3D LUT" (in the 3DLUT File dropdown) to load a .cube file from disk.

**Photoshop's built-in LUTs:** Photoshop ships with several built-in LUTs accessible through the 3DLUT File dropdown (names vary by version but typically include options like Crisp_Warm, EdgyAmber, FallColors, FoggyNight, and others). These are starting points for exploration, not necessarily production-quality grades.

**Controlling intensity:** A LUT Adjustment Layer has no internal intensity control. You control the strength of the effect by reducing the Adjustment Layer's **opacity** in the Layers panel. A LUT at 30-50% opacity often produces more usable results than the same LUT at 100%.

**RECOMMENDATION:** When evaluating a LUT, always try it at several opacity levels. A LUT that looks excessive at 100% may produce a pleasing, subtle grade at 25%. Also, apply LUTs to properly corrected images — LUTs assume a specific input state, and applying them to images with uncorrected color casts produces unpredictable results.

**Strengths:** Instant, complex color grades. Consistent application across a series of images (apply the same LUT to all images for a unified look). Can encode transformations too complex to build manually.

**Weaknesses:** Fixed mapping — no internal adjustability. Image-dependent (a LUT designed for one exposure/color profile may look wrong on another). Black box — you cannot see or modify what the LUT is doing to individual colors or tonal ranges.

### Gradient Map: Detailed Walkthrough

**Creating the Adjustment Layer:** Click the Gradient Map icon in the Adjustments panel, or go to Layer > New Adjustment Layer > Gradient Map.

**How it works:** The Gradient Map reads each pixel's luminosity value and replaces its color with the color at the corresponding position in the gradient. The darkest pixels (luminosity 0) receive the leftmost color in the gradient. The brightest pixels (luminosity 255 in 8-bit) receive the rightmost color. Everything in between receives the color at the proportional position along the gradient.

**Editing the gradient:** Click on the gradient preview in the Properties panel to open the Gradient Editor.

| Element | Function |
|---------|----------|
| Color stops (bottom edge of the gradient bar) | Define colors at specific positions. Click below the gradient bar to add a stop. Drag a stop to reposition it. Select a stop and click the Color swatch to change its color. Drag a stop off the gradient bar to delete it. |
| Opacity stops (top edge of the gradient bar) | Control the transparency of the gradient at specific positions. Typically left at 100% for grading. |
| Midpoint diamonds (between color stops) | Control where the transition between two adjacent colors reaches the 50% blend point. Drag to shift the transition. |
| Smoothness | Controls the interpolation smoothness. 100% is standard. |

**Building a cinematic split tone with Gradient Map:**

1. Create a Gradient Map Adjustment Layer.
2. Click the gradient to open the Gradient Editor.
3. Set the leftmost color stop to a deep teal (e.g., R: 30, G: 70, B: 80).
4. Set the rightmost color stop to a warm amber (e.g., R: 240, G: 195, B: 130).
5. Optionally add a middle stop at approximately 50% position with a neutral or slightly warm gray (e.g., R: 140, G: 130, B: 120) to control the midtone transition.
6. Click OK.
7. In the Layers panel, change the blend mode from Normal to **Soft Light** or **Color**.
8. Reduce opacity to **10-25%**.

At 100% opacity in Normal mode, the image becomes a duotone. In Soft Light or Color mode at low opacity, the gradient colors tint the image subtly — shadows take on the teal character, highlights take on the amber character, and midtones transition between them.

**Strengths:** Most precise tonal-to-color mapping available. Any number of color stops at any position. Produces smooth, continuous color transitions through the tonal range. Excellent for split toning when combined with blend modes and reduced opacity.

**Weaknesses:** At full opacity in Normal mode, destroys all original color information. Requires understanding of blend modes to use as a subtle grading tool. Less intuitive than Color Balance for simple split tones.

### Solid Color Fill Layer: Detailed Walkthrough

**Creating the layer:** Click the "New Fill or Adjustment Layer" button at the bottom of the Layers panel and select Solid Color, or go to Layer > New Fill Layer > Solid Color.

**Choosing the color:** The Color Picker opens immediately. Select the color you want for your wash. For grading, consider:

- Warm washes: amber (R: 255, G: 180, B: 100), golden yellow (R: 255, G: 220, B: 140)
- Cool washes: steel blue (R: 100, G: 140, B: 200), teal (R: 60, G: 160, B: 170)
- Neutral washes: warm gray (R: 180, G: 170, B: 150), cool gray (R: 150, G: 160, B: 180)

**Setting the blend mode and opacity:**

| Blend Mode | Effect on Grading | Typical Opacity Range |
|------------|-------------------|----------------------|
| Soft Light | Subtle tinting with mild contrast enhancement. Warm colors warm; cool colors cool. The effect scales gently with lightness. | 5-20% |
| Overlay | Similar to Soft Light but more aggressive. Increases contrast more noticeably. | 5-15% |
| Color | Changes hue and saturation without affecting luminosity. Produces a pure color tint without contrast change. | 3-15% |
| Multiply | Darkens and tints simultaneously. Useful for moody, shadow-heavy grades. | 3-10% |
| Screen | Lightens and tints simultaneously. Useful for airy, high-key grades. | 3-10% |

**RECOMMENDATION:** For most photographic grading, Soft Light and Color are the most useful blend modes for Solid Color fill layers. Soft Light produces a natural-looking tint that interacts with the image's existing tonal structure. Color produces a pure color wash without affecting luminosity or contrast.

**Strengths:** Maximum simplicity — one color, one blend mode, one opacity slider. Easy to adjust (double-click the layer thumbnail to change the color). Extremely predictable.

**Weaknesses:** Uniform application (no tonal range targeting). Requires blend mode understanding to use effectively. Cannot create split tones from a single layer (use two layers with masks, or use Gradient Map instead).

### Selective Color: Brief Overview

Selective Color is covered in detail in Chapter 11. Its role in grading is to refine specific color populations after the overall grade is established. For example, after applying a warm split tone with Color Balance, you might use Selective Color to:

- Shift the Reds slightly toward Orange (reducing Cyan in Reds) to warm skin tones specifically.
- Add Cyan to the Blues to push the sky further toward teal, reinforcing the cool side of the grade.
- Reduce Yellow in the Greens to prevent foliage from turning yellow-green under the warm grade.

Selective Color's CMYK sliders within per-color selections give you surgical control over individual color families — a level of specificity that Color Balance and Photo Filter cannot offer. See Chapter 11 for the full treatment.

### Per-Channel Curves for Grading (Review)

Per-channel Curves, covered in Chapter 07 and applied to color correction in Chapter 09, is also a powerful grading tool. Its grading application follows the same mechanics as its correction application — you shift individual channel curves to introduce color shifts — but with creative intent rather than corrective intent.

For grading, the key technique is shaping the channel curves to create different color shifts at different tonal positions:

| Channel Curve Adjustment | Grading Effect |
|--------------------------|----------------|
| Lower the Blue curve in the shadows (reduce Blue in darks) | Warm (yellow) shadows |
| Raise the Blue curve in the highlights (increase Blue in lights) | Cool (blue) highlights |
| Raise the Red curve in the highlights | Warm (red-amber) highlights |
| Lower the Red curve in the shadows | Cool (cyan) shadows |
| Raise the Green curve in the midtones | Shift midtones toward green |
| Lower the Green curve in the midtones | Shift midtones toward magenta |

**FACT:** Per-channel Curves is the most precise grading tool in Photoshop because you control the exact tonal position and magnitude of every color shift. Color Balance gives you three tonal zones; per-channel Curves gives you arbitrary positioning of anchor points anywhere in the tonal range. The trade-off is complexity: building a split tone with three independent channel curves and multiple anchor points per curve requires practice and a clear mental model of what each adjustment does.

---

## Professional Workflow

### Where Color Grading Fits in the Layer Stack

In the non-destructive workflow established in Chapter 06, color grading occupies a specific position:

1. **Smart Object** (base photograph) — bottom of stack.
2. **Retouching layers** (sensor spots, blemishes) — above the base.
3. **Tonal adjustments** (Levels, Curves for contrast) — above retouching. (Chapter 07)
4. **Color corrections** (per-channel Curves for cast removal, white balance refinement) — above tonal adjustments. (Chapter 09)
5. **Color grading** (Color Balance, Photo Filter, Gradient Map, Solid Color fills, LUTs) — above color correction. (This chapter)
6. **Local adjustments** (dodging, burning, local contrast) — above or interleaved with grading as needed. (Chapter 08)
7. **Targeted color adjustments** (Selective Color, Hue/Saturation) — typically above grading. (Chapter 11)

**RECOMMENDATION:** Group your grading layers into a layer group named "Grade" or "Color Grade." This separates grading from correction in the stack, makes it easy to toggle the entire grade on/off for before/after comparison, and prevents accidental confusion between correction layers and grading layers.

### The Grading Sequence

Within the grading group, a practical ordering is:

1. **Primary grade** (overall mood): A single Color Balance layer or Gradient Map establishing the main split tone or color direction.
2. **Secondary refinement** (color-specific adjustments): Selective Color or per-channel Curves refining specific color populations to work within the grade.
3. **Accent or wash** (optional): A Photo Filter or Solid Color fill layer adding a subtle, unified tint across the image.
4. **LUT** (optional): A Color Lookup layer applied at reduced opacity if using a LUT-based grade.

This sequence moves from broad to specific: establish the overall color direction first, then refine individual colors, then add finishing touches. Reversing this order — starting with a LUT and then trying to correct its effects with Color Balance — works against you because the LUT's fixed mapping has already moved every color, and subsequent adjustments must compensate for changes you cannot see or control individually.

### Evaluating the Grade

**Toggle the grading group on/off.** This is the single most important evaluation technique. If the grade looks better than the ungraded image, it is working. If it looks worse, or if you cannot tell the difference, reconsider.

**Check the grade at different scales.** Zoom to fit the screen and evaluate the overall mood. Then zoom to 100% and check whether the grade has introduced unwanted artifacts in critical areas — banding in skies, unnatural skin tones, posterization in smooth gradients.

**Check neutral areas.** If your image contains areas that should read as neutral (concrete, white walls, overcast sky), check whether the grade has shifted them in a way that looks intentional or accidental. A good grade shifts neutrals subtly enough that they still read as neutral-ish — they take on the ambient color of the grade without looking like they have been painted with a filter.

**Check skin tones.** If the image contains people, skin tones are the most demanding test of any grade. The human visual system is extraordinarily sensitive to skin color. A grade that shifts skin tones too far from their natural range — too green, too magenta, too orange, too gray — will look wrong even if every other element in the image looks fine. Always evaluate the grade's effect on skin.

**RECOMMENDATION:** Evaluate your grade after stepping away from the screen for several minutes. Fresh eyes are the best calibration tool for creative decisions. A grade that looks perfect after twenty minutes of tweaking may look obviously heavy after a break.

---

## Step-by-Step Example: Grading a Landscape for Golden Hour Warmth

This example walks through grading a landscape photograph to enhance the golden hour atmosphere. The image has already been tonal-adjusted (Chapter 07) and color-corrected (Chapter 09). It is technically clean, with neutral whites, accurate colors, and good tonal range. The goal is to enhance the warm, golden quality of the late-afternoon light — making the image feel the way the scene felt, which is warmer, deeper, and more atmospheric than the technically correct rendering.

**Starting condition:** A landscape at golden hour. The foreground (grass, rocks, warm earth) catches direct golden light. The background (distant hills, sky) is cooler — soft blues and muted purples. The image is correctly exposed and color-corrected, but it looks clinical. The warmth of the light is present but understated.

### Step 1: Establish the Split Tone with Color Balance

1. Create a Color Balance Adjustment Layer above the correction layers. Name it "Grade - Split Tone."
2. Select **Shadows**. Move the Yellow/Blue slider toward Blue (+8). Move the Cyan/Red slider slightly toward Cyan (-3). This pushes the shadows subtly cooler, creating depth and separation from the warm foreground.
3. Select **Highlights**. Move the Yellow/Blue slider toward Yellow (-12). Move the Cyan/Red slider toward Red (+6). This intensifies the golden warmth in the sunlit areas.
4. Select **Midtones**. Move the Yellow/Blue slider toward Yellow (-4). Move the Cyan/Red slider toward Red (+3). This adds a gentle overall warmth without overpowering the cool shadows.
5. Ensure **Preserve Luminosity** is checked.
6. Evaluate: The image should now have a warmer overall feel, with the sunlit foreground reading as distinctly warm and the shadows reading as subtly cool. The warm-cool contrast enhances the sense of depth.

### Step 2: Enhance the Golden Tones with a Photo Filter

1. Create a Photo Filter Adjustment Layer above the Color Balance layer. Name it "Grade - Golden Wash."
2. Select the **Color** radio button and click the color swatch. Choose a warm amber color (approximately R: 255, G: 195, B: 75).
3. Set **Density** to 8-12%.
4. Ensure **Preserve Luminosity** is checked.
5. Evaluate: The Photo Filter adds a uniform golden tint across the entire image, pushing the overall palette further into the warm range. Because Preserve Luminosity is checked, the image does not darken.

### Step 3: Add Depth with a Gradient Map

1. Create a Gradient Map Adjustment Layer above the Photo Filter layer. Name it "Grade - Tonal Color."
2. Click the gradient to open the Gradient Editor.
3. Set the leftmost color stop to a deep slate blue (R: 40, G: 55, B: 75).
4. Set the rightmost color stop to a pale golden cream (R: 255, G: 235, B: 190).
5. Add a middle color stop at approximately 45% position with a warm neutral (R: 160, G: 140, B: 110).
6. Click OK.
7. Change the Gradient Map layer's blend mode to **Soft Light**.
8. Reduce opacity to **12-18%**.
9. Evaluate: The Gradient Map reinforces the split tone — cool in the shadows, warm in the highlights — with a smooth, continuous transition through the midtones. The Soft Light blend mode integrates the effect naturally with the image's existing tonal structure.

### Step 4: Evaluate and Refine

1. Group the three grading layers into a group named "Color Grade."
2. Toggle the group on/off to compare the graded image with the corrected-but-ungraded version.
3. If the effect is too strong, reduce the opacity of the Color Grade group (not the individual layers) to moderate the entire grade proportionally.
4. If the shadows are too blue, return to the Color Balance layer and reduce the Blue shift in Shadows.
5. If skin tones (if present) look unnatural, consider adding a Selective Color layer targeting Reds and Oranges to correct them within the grade.
6. Check the sky. If it has shifted too warm (losing the natural blue), consider masking the Photo Filter layer to exclude the sky area.

**Result:** The landscape now reads as a warm, atmospheric golden-hour scene. The foreground glows with amber light. The shadows have a subtle cool depth that creates spatial recession. The midtones transition smoothly between warm and cool. The overall palette is cohesive — warm and golden but not uniformly orange. The grade enhances the mood that was present in the scene without looking like a filter has been applied.

---

## Common Mistakes

**Grading before correcting.** If the image has a color cast, every grading decision you make is built on top of that cast. A blue cast in the shadows is not the same as a cool shadow grade — the cast is uncontrolled and may not respond to grading tools the way you expect. Remove casts first (Chapter 09), then grade on a neutral foundation.

**Applying grading at too high an intensity.** The most common beginner mistake. A Gradient Map at 100% opacity in Normal mode obliterates the image's original colors. A Color Balance shift of +30 on any slider produces an obviously tinted image. Professional grades are subtle — often imperceptible in isolation, visible only in comparison. Start at lower intensity than you think necessary and increase only if genuinely needed.

**Using a LUT as the entire grade without adjustment.** A LUT is designed for a generic input image, not your specific photograph. Applying a LUT at 100% and considering the grade "done" ignores the specific needs of your image. A LUT is a starting point: apply it at reduced opacity, evaluate the result, and layer additional corrections on top if needed.

**Ignoring skin tones.** A color grade that makes the landscape look atmospheric but makes the person in the frame look sick is not a successful grade. Always evaluate the grade's effect on skin tones. If the grade shifts skin tones too far from their natural range, use a layer mask to reduce the grade's effect on skin areas, or add a Selective Color adjustment targeting Reds and Oranges to compensate.

**Confusing grading tools and applying them redundantly.** Using Color Balance, Photo Filter, a Solid Color fill layer, and a Gradient Map all pushing in the same direction simultaneously creates an opaque stack of overlapping effects that is impossible to evaluate or adjust. Each grading layer should have a clear purpose. If two layers are doing essentially the same thing, remove one.

**Grading for the screen without considering output.** A grade that looks perfect on your calibrated wide-gamut monitor may push colors out of the sRGB gamut (for web delivery) or out of the printer's gamut (for print). Check the grade under soft proofing conditions (Chapter 21) for your intended output before finalizing. This is especially relevant for grades that push toward highly saturated warm or cool tones.

**Applying a uniform color wash when a split tone is needed.** A Photo Filter or Solid Color fill layer in Color mode applies the same tint everywhere. If the image's mood requires different colors in the shadows and highlights (which most atmospheric grades do), a uniform wash produces a flat, monochromatic tint instead of the rich depth that split toning creates. Choose the right tool for the intent: uniform wash for overall color unity, split tone for tonal-range-specific color contrast.

**Treating a LUT as adjustable.** When a LUT does not look right, the instinct is to "adjust" it. But a LUT has no adjustable parameters inside Photoshop — it is a fixed mapping. You can change the layer opacity, mask the layer, or stack additional adjustments on top of it, but you cannot modify what the LUT itself does to the data. If a LUT is not working for your image, the solution is often to use a different LUT or to build the grade manually from adjustable tools.

---

## Alternative Approaches

### Camera Raw / Lightroom Color Grading Panel

Adobe Camera Raw and Lightroom include a dedicated Color Grading panel (renamed from "Split Toning" in late 2020) with three color wheels — Shadows, Midtones, Highlights — and a Global wheel. Each wheel lets you select a hue and adjust saturation, with a luminance slider per range and a blending slider controlling the transition between ranges.

**INTERPRETATION:** For many photographers, the Camera Raw Color Grading panel is the most practical grading tool for single-image work. It combines the functionality of Color Balance (three tonal ranges) with the intuitive interface of a color wheel (hue + saturation selection). If your base image is a Smart Object opened from Camera Raw (Chapter 06), you can access this panel by double-clicking the Smart Object and re-entering Camera Raw. The advantage is that the grading is applied before the image enters Photoshop's pixel pipeline, which can produce smoother results.

### Per-Channel Curves as the Primary Grading Tool

Many professional retouchers use per-channel Curves as their only grading tool. A single Curves Adjustment Layer with shaped Red, Green, and Blue curves can produce any split tone achievable with Color Balance, and with more tonal precision. This approach keeps the layer stack minimal and concentrates all grading control in one layer.

The trade-off is complexity: a single Curves layer doing all the grading work is harder to evaluate and adjust than separate layers with clear individual purposes. Modifying the shadow color requires finding and adjusting the lower region of multiple channel curves simultaneously, whereas in Color Balance, you simply switch to the Shadows selector and adjust three sliders.

### Blend Modes for Creative Color Effects

Beyond Solid Color fill layers, blend modes can be applied to duplicated image layers or to blank layers painted with color for grading effects:

- **Duplicate the background layer and set to Soft Light.** This increases contrast and slightly saturates the image — a quick "punch" effect. Reduce opacity to moderate.
- **Create a blank layer, paint with warm color in the highlights and cool color in the shadows using a large soft brush, and set to Color or Soft Light.** This is a manual, painterly approach to split toning that offers spatial control — you can paint warmth exactly where you want it, not just based on tonal value.

These techniques are less precise and less repeatable than Adjustment Layers, but they offer spatial control that the other tools do not.

### Luminosity Masks for Graded Tonal Ranges

Combining grading layers with luminosity masks (Chapter 13) provides the most precise control over which tones receive which color. A Solid Color fill layer restricted by a luminosity mask that selects only the brightest highlights applies its color exclusively to those highlights, with a smooth feathered transition. This approach separates the grading color choice from the tonal targeting, giving you independent control over both.

---

## Summary

Color grading is the deliberate, creative manipulation of image color to establish mood, atmosphere, and visual style. It is distinct from color correction: correction removes what should not be there (color casts, white balance errors); grading adds what you want to be there (intentional color shifts, split tones, palette unification). Correction must precede grading — a grade built on top of an uncorrected color cast is built on a flawed foundation.

The primary color grading tools in Photoshop are:

| Tool | What It Does | Best For |
|------|-------------|----------|
| Color Balance | Shifts color in three tonal ranges (Shadows, Midtones, Highlights) using complementary-pair sliders | Split toning, tonal-range-specific grading |
| Photo Filter | Applies a uniform color tint with a density control, simulating an optical filter | Global color washes, overall warming or cooling |
| Gradient Map | Maps the image's tonal range to a gradient of colors | Precise tonal-to-color mapping, split tones, duotones |
| Solid Color Fill Layer | Fills a layer with one color; combined with blend mode and opacity for controlled washes | Simple color washes, palette unification |
| Color Lookup (LUT) | Applies a fixed, preset mapping from input to output colors | Instant, complex color grades; consistent look across a series |
| Per-Channel Curves | Shapes individual RGB channel curves to introduce color shifts at arbitrary tonal positions | Maximum precision grading; single-layer control |
| Selective Color | Adjusts CMYK components within specific color families | Refining individual color populations within a grade (detail in Chapter 11) |

Color harmonies — complementary, analogous, and triadic — provide a framework for choosing grading colors that work together. Complementary harmonies (opposite colors on the color wheel) create maximum color contrast and are the foundation of most cinematic grading (orange/teal, warm/cool splits). Analogous harmonies (adjacent colors) create unity and cohesion. Warm-cool contrast enhances perceptual depth: warm tones advance, cool tones recede.

The professional workflow places grading layers above correction layers in the stack, grouped separately. A practical grading sequence moves from broad to specific: establish the primary grade (Color Balance or Gradient Map), refine specific colors (Selective Color), then add finishing touches (Photo Filter or Solid Color fill). LUTs, when used, are applied at reduced opacity and treated as starting points.

Effective grading is almost always more subtle than beginners expect. Start with low intensity — 10-15% opacity, small slider values — and increase only if needed. Evaluate the grade by toggling the grading group on/off. Always check skin tones. Always consider output gamut.

The distinction between LUTs (fixed, preset mappings) and adjustable tools (Color Balance, Curves, Gradient Map) is fundamental: LUTs offer convenience and complexity but no internal adjustability; adjustable tools offer control and transparency but require understanding.

---

## Exercises

### Exercise 1: Color Balance Split Tone

Open a photograph with a full tonal range (deep shadows through bright highlights). Create a Color Balance Adjustment Layer and build a complementary split tone:

1. Warm the highlights (shift toward Yellow and Red).
2. Cool the shadows (shift toward Blue and Cyan).
3. Leave the midtones approximately neutral.
4. Check "Preserve Luminosity."
5. Toggle the layer on/off to evaluate the effect. Does the image feel more three-dimensional with the split tone applied?

Now reverse the split: cool the highlights and warm the shadows. How does the mood change? Which version works better for this particular image, and why?

**Success criterion:** You can build a split tone with Color Balance, predict the mood effect of warm vs cool placement in shadows and highlights, and explain why each version creates a different emotional quality.

### Exercise 2: Photo Filter vs Color Balance

Open the same photograph twice (or duplicate it). On one copy, apply a **Photo Filter** with Warming Filter (85) at 15% density. On the other, apply a **Color Balance** shift with equivalent warmth in all three tonal ranges (a small shift toward Red and Yellow in Shadows, Midtones, and Highlights).

1. Compare the two results side by side. Where do they differ? Are the shadows affected differently? The highlights?
2. Now modify the Color Balance version to create a split tone (warm highlights, cool shadows). Can the Photo Filter version achieve the same effect? Why or why not?
3. Reset both and apply a cooling effect to each. Compare again.

**Success criterion:** You can articulate the difference between a uniform color wash (Photo Filter) and a tonal-range-targeted color shift (Color Balance), and you can predict which tool to choose for a given creative intent.

### Exercise 3: Gradient Map Exploration

Open a photograph with good tonal range. Create a Gradient Map Adjustment Layer.

1. Set a two-stop gradient: dark blue (left) to warm orange (right). Observe the result in Normal mode at 100% opacity. What happened to the original colors?
2. Change the blend mode to **Soft Light**. How does the result change?
3. Change the blend mode to **Color**. How does this differ from Soft Light?
4. Reduce opacity to 15% in each blend mode. Which produces the most natural-looking split tone for this image?
5. Now edit the gradient: add a third color stop at the midpoint with a neutral warm gray. How does this change the midtone rendering?

**Success criterion:** You understand that Gradient Map replaces colors based on luminosity, and that blend modes and opacity are essential controls for using Gradient Map as a grading tool rather than a recoloring tool.

### Exercise 4: LUT Evaluation

Open a properly corrected photograph. Create a Color Lookup Adjustment Layer.

1. Try three different built-in LUTs from the 3DLUT File dropdown. For each, evaluate: what is the LUT doing to the shadows? The highlights? The saturation? The overall color direction?
2. For the LUT you find most appealing, reduce the layer opacity. At what opacity does the effect transition from "obviously filtered" to "subtly graded"?
3. Now evaluate: does this LUT work equally well on a different photograph (open a second image and apply the same LUT at the same opacity)? If not, why not?
4. Try to reproduce the approximate effect of one of the LUTs using Color Balance and/or Gradient Map. Can you get close? What does this exercise teach you about the difference between a fixed LUT and adjustable tools?

**Success criterion:** You can evaluate what a LUT does to an image, control its intensity with opacity, and understand why a LUT that works on one image may not work on another.

---

## Advanced Exercises

### Advanced Exercise 1: Building a Cinematic Orange/Teal Grade

Open a photograph that includes both warm elements (skin tones, sunlit surfaces, warm-toned objects) and neutral or cool elements (sky, shadows, cool-toned surfaces).

Build a cinematic orange/teal grade using only adjustable tools (no LUTs):

1. Start with a Color Balance Adjustment Layer for the primary split tone (warm highlights, cool shadows).
2. Add a Gradient Map Adjustment Layer in Soft Light mode at low opacity to reinforce the tonal color mapping.
3. Add a Selective Color Adjustment Layer to refine: push the Cyans and Blues further toward teal; warm the Reds and Oranges toward amber.
4. Evaluate skin tones. If they have shifted too far toward orange, use Selective Color's Reds range to correct.
5. Toggle the entire grade group on/off. Does the grade enhance the image's mood? Does it look intentional or like a filter?

**Success criterion:** You can construct a multi-layer grade that achieves a specific cinematic palette, with independent control over each element, and you can identify and correct skin tone issues within the grade.

### Advanced Exercise 2: Matching a Reference Image's Color Grade

Find a professionally graded photograph or film still whose color treatment you admire. Open your own photograph alongside it.

1. Analyze the reference image's color grade: What are the shadow colors? The highlight colors? Is it a complementary, analogous, or other harmony? What is the overall saturation level? How is the warm-cool balance distributed?
2. Using the tools from this chapter (Color Balance, Gradient Map, Photo Filter, Solid Color fill, per-channel Curves), attempt to reproduce a similar grade on your own image.
3. Evaluate: where does your grade match the reference? Where does it diverge? What tools would you need (or what adjustments would you need to make) to get closer?

**Success criterion:** You can analyze a color grade in terms of its component parts (shadow color, highlight color, harmony type, saturation level) and translate that analysis into specific Photoshop adjustments.

### Advanced Exercise 3: Comparing Grading Tools on the Same Image

Open a photograph and create four separate grading layer groups, each using a different primary tool to achieve the same creative intent (e.g., warm highlights, cool shadows, complementary split tone):

1. **Group A:** Color Balance only.
2. **Group B:** Per-channel Curves only.
3. **Group C:** Gradient Map (in Soft Light or Color mode, at reduced opacity) only.
4. **Group D:** Solid Color fill layers with blend modes (two layers — one warm in a highlight mask, one cool in a shadow mask) only.

Compare all four results:

1. Which produces the smoothest tonal transition?
2. Which gives you the most precise control over where the color shifts occur?
3. Which is the fastest to set up and adjust?
4. Which would you choose for your own workflow, and why?

**Success criterion:** You understand the practical differences between grading tools well enough to choose the right tool for a given situation based on the specific requirements of that situation, not based on habit.

---

## Blackbelt Challenge

You receive a set of five landscape photographs from the same location, shot over two hours spanning golden hour through blue hour. The images have been correctly exposed and color-corrected — whites are white, no color casts remain. The set is destined for a gallery exhibition as a five-image series. The client wants the series to have a unified visual identity while allowing each image's unique light quality to remain distinct.

Without opening Photoshop:

1. **Design a grading strategy for the series.** What color harmony will you use? What will be consistent across all five images (the unifying element)? What will vary (the differentiating element)? How will you ensure that the 5:00 PM golden-hour image and the 6:30 PM blue-hour image feel like they belong to the same series without looking like they received the same grade?

2. **Specify the exact Photoshop tool and settings for each grading layer** you would create. For each layer, state: the tool (Color Balance, Gradient Map, Photo Filter, etc.), the key settings (slider positions, gradient colors, blend mode, opacity), and the purpose of that layer in the overall grade.

3. **A client review reveals a problem:** the grade you designed works on four of the five images, but on the fifth (the blue-hour image), the cool-shadow component of your split tone is too strong, making the entire image look monotonously cold. You cannot change the grading layers themselves (the other four images depend on them). Describe three different approaches to solving this problem for the fifth image only, using techniques from this chapter and previous chapters. Evaluate the trade-offs of each approach.

4. **Predict the output impact.** The gallery prints will be on a warm-toned matte fine-art paper. How will the paper's warm base tone interact with the cool components of your grade? Which images in the series will be most affected? What adjustment, if any, would you make during soft proofing (Chapter 21) to compensate?

5. **A colleague suggests using a single LUT across all five images for consistency.** Evaluate this suggestion. Under what circumstances would a LUT-based approach work for a series like this? Under what circumstances would it fail? What is the fundamental limitation of a LUT that makes it problematic for a series of images shot under changing light conditions?

This challenge requires you to synthesize color harmony theory, grading tool selection, series consistency, output considerations, and the distinction between fixed mappings (LUTs) and adjustable tools into a coherent professional strategy.
