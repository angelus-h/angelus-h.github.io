---
description: Color correction in Photoshop — white balance, neutral reference points, color cast identification, per-channel Curves correction, Info panel color analysis, and systematic color correction workflow.
---

# Chapter 09: Color Correction

## Learning Objectives

After completing this chapter, you will be able to:

1. Distinguish between color correction and color grading, and explain why the distinction matters for a professional workflow.
2. Identify a color cast by visual inspection and confirm it numerically using the Info panel and Color Sampler tool.
3. Explain what a neutral reference point is, why equal R=G=B values indicate neutrality in a device-independent working space, and how to use neutral references to diagnose color casts.
4. Use per-channel Curves to remove a color cast from specific tonal regions while preserving correct color in other regions.
5. Use the Levels eyedroppers (black point, gray point, white point) to perform a rapid color correction based on known reference tones.
6. Use the Color Balance adjustment layer to make targeted warm/cool, green/magenta, and blue/yellow shifts in shadows, midtones, and highlights independently.
7. Apply the Info panel and Color Sampler tool as diagnostic instruments throughout the correction process, reading before/after values to verify adjustments numerically rather than relying solely on visual assessment.
8. Describe how white balance correction in Photoshop differs from white balance in Adobe Camera Raw, and choose the appropriate stage for the correction.
9. Execute a systematic color correction sequence: identify the cast, locate neutral references, correct per-channel, verify numerically.

---

## Conceptual Foundation

Chapter 07 established tonal correction: remapping input values to output values to achieve the intended brightness and contrast. That chapter focused on the master (RGB) curve, treating all three channels as a unit. This chapter separates the channels.

Color correction is the process of removing unwanted color shifts from an image so that the colors are accurate — or at least neutral where they should be neutral. It is a diagnostic, corrective activity. You are not choosing what color an image should be; you are identifying what went wrong and fixing it. The creative decision about what color an image should be — that is color grading, and it belongs in Chapter 10.

This distinction is not pedantic. It determines the order of operations in your workflow and the criteria you use to evaluate success. Color correction succeeds when a white shirt looks white, a gray card looks gray, and skin tones fall within a plausible range for the subject's complexion. Color grading succeeds when the emotional character of the image matches your creative intent. You cannot grade accurately until the correction is done, because grading on top of a color cast produces unpredictable results — you are shifting colors from an unknown starting point.

The tools for color correction are not new. Per-channel Curves (introduced conceptually in Chapter 07), Levels eyedroppers, and Color Balance are the same adjustment layers you already know. What changes in this chapter is the intent and the method: you are using these tools to diagnose and remove specific color problems, guided by numerical data from the Info panel rather than visual guesswork on an uncalibrated monitor.

Understanding color correction requires understanding complementary colors in the RGB model — the paired relationships between the three primary channels and their opposites. It requires understanding what "neutral" means in digital data. And it requires trusting the numbers over your eyes, at least as a diagnostic starting point, because your eyes adapt to color casts and your monitor may not be accurate.

---

## Terminology

### Color Correction (vs Color Grading)

**Definition:** Color correction is the process of adjusting the color values in an image to remove unwanted color casts, achieve accurate color reproduction, and establish neutral tones where neutrality is expected. Its goal is accuracy or at least plausible fidelity to the original scene. Color grading is a separate, subsequent process: the deliberate introduction of color shifts for aesthetic, emotional, or narrative effect.

**Meaning in photographic practice:** In a professional workflow, color correction comes first and color grading comes second. Color correction answers the question: "Are the colors in this image accurate?" Color grading answers: "What mood do I want the colors to convey?" A portrait shot under tungsten light has a warm orange color cast — correction removes that cast so skin tones are accurate. Afterward, you might grade the image with a cool blue tone in the shadows for a cinematic look. The correction establishes a known baseline; the grading departs from it intentionally.

**Photoshop implementation:** Both processes use the same tools — Curves, Color Balance, Hue/Saturation, Photo Filter. The tools do not enforce the distinction; the photographer does. In practice, color correction is typically handled with per-channel Curves or Levels eyedroppers, while grading uses Color Balance, Photo Filter, or per-channel Curves targeted to specific tonal regions. This chapter covers correction; Chapter 10 covers grading.

**Related concepts:** Color grading (Chapter 10), color cast, white balance, per-channel Curves, neutral reference.

**Common misconception:** "Color correction and color grading are the same thing — both involve changing colors." They serve opposite purposes. Correction removes color shifts you did not intend. Grading adds color shifts you deliberately choose. Confusing the two leads to images where you try to grade away a cast (compensating rather than removing it) or correct away an intentional grade. The workflow must separate them: correct first, grade second.

---

### Color Cast

**Definition:** A color cast is an overall or regional shift in color that causes the image to appear tinted — too warm, too cool, too green, too magenta. A cast affects tones that should be neutral (whites, grays, blacks) and shifts them toward a specific hue. Color casts originate from light source characteristics (tungsten, fluorescent, mixed lighting), incorrect white balance in camera or RAW processing, lens coatings, reflective surfaces in the scene (colored walls bouncing light), or monitor inaccuracy during previous editing.

**Meaning in photographic practice:** Color casts are the most common color problem in photography. A photograph taken under fluorescent lighting without white balance correction will have a green cast — whites appear greenish, skin tones look sickly. A photograph taken in open shade (blue skylight) will have a cool blue cast. Mixed lighting (tungsten in the foreground, daylight through a window) produces casts that vary across the image — warm in some areas, cool in others. Identifying the cast is the first step in color correction.

**Photoshop implementation:** A color cast is visible in the data as an imbalance between channels. In a neutral gray area (a tone that should have equal R, G, and B values), a cast shows as one or two channels being higher or lower than the others. For example, a warm cast might show R=145, G=130, B=110 in what should be a neutral midtone — the Red channel is too high and the Blue channel is too low relative to Green. The Info panel and Color Sampler tool are the instruments for detecting this imbalance.

**Related concepts:** White balance, neutral reference, complementary colors, per-channel Curves, Info panel.

**Common misconception:** "A color cast affects the entire image uniformly." Many casts do affect the entire tonal range, but some affect only specific regions. A tungsten-lit scene with daylight from a window produces a warm cast in the shadows (tungsten-dominated) and a cooler cast in the highlights (daylight-dominated). Mixed-lighting casts vary across the tonal range and require per-channel Curves targeted to specific tonal regions — a single global correction cannot fix them.

---

### Neutral Reference / Gray Point

**Definition:** A neutral reference is any element in the scene that should be a neutral gray — meaning it should reflect all wavelengths of visible light approximately equally. In RGB data, a true neutral at any brightness level has equal Red, Green, and Blue values: R=G=B. A neutral reference can be any tone from near-black to near-white, as long as it is known to be achromatic (without color) in the original scene.

**Meaning in photographic practice:** Neutral references are the diagnostic anchors of color correction. If you can identify something in the image that should be neutral — a concrete sidewalk, a white shirt, a gray card placed in the scene, an asphalt road, a white wall in open shade — you can measure its RGB values and determine whether a cast exists and in which direction. If the gray card reads R=128, G=128, B=140, you know the image has a blue cast in the midtones (the Blue channel is too high by approximately 12 levels). This gives you a precise, numerical target for correction.

**Photoshop implementation:** Use the Color Sampler tool (nested under the Eyedropper in the toolbar) to place persistent sample points on areas you know should be neutral. The Info panel displays the RGB values at each sampler. When a Curves or Levels adjustment layer is active, the Info panel shows both the before value (left of the slash) and the after value (right of the slash), allowing you to verify that your correction is bringing the channels into alignment. Place samplers on neutral references at different brightness levels — a dark neutral, a midtone neutral, and a bright neutral — to check whether the cast is uniform across the tonal range or varies.

**Related concepts:** Color cast, gray card, Info panel, Color Sampler, per-channel Curves, white balance.

**Common misconception:** "A white object in the scene is always a reliable neutral reference." White objects are only reliable if they are illuminated by the main light source and are not reflecting colored light from nearby surfaces. A white shirt next to a red wall will pick up reflected red light and will not read as neutral. A white sheet of paper under mixed lighting may be warm on one side and cool on the other. The ideal neutral reference is a known-neutral object (a photographic gray card) photographed in the same light as the subject. Absent that, use objects likely to be truly achromatic: concrete, asphalt, unpainted metal, white paper under uniform lighting.

---

### White Balance (in Photoshop Context vs Camera/ACR Context)

**Definition:** White balance is the adjustment that compensates for the color temperature of the light source so that objects that appear white to the eye also appear white in the photograph. In digital photography, white balance can be applied at two stages: in the camera or RAW processor (adjusting the raw sensor data before rendering), or in Photoshop (adjusting the rendered RGB data after the file has been converted to a standard color space).

**Meaning in photographic practice:** White balance in Camera Raw (ACR) or Lightroom operates on the raw, linear sensor data. It has maximum headroom: the color temperature and tint sliders adjust the channel multipliers applied during demosaicing, before gamma encoding, before gamut clipping. This produces the cleanest results because the adjustment is integrated into the rendering pipeline. White balance in Photoshop operates on rendered, gamma-encoded RGB data — the image has already been processed into a color space. Adjusting white balance at this stage is mathematically less clean: you are modifying values that have already been through gamma encoding, which means the channel relationships are nonlinear. It works, and often works well, but the results are not identical to adjusting the same white balance in ACR.

**Photoshop implementation:** Photoshop has no dedicated "White Balance" tool in the way Camera Raw has a Temperature/Tint slider and a white balance eyedropper. White balance correction in Photoshop is achieved through per-channel Curves, per-channel Levels, the Levels gray point eyedropper, or Color Balance. The most precise method is per-channel Curves, guided by the Info panel.

**Related concepts:** Camera Raw (Chapter 03), color temperature, per-channel Curves, Levels eyedroppers, color cast.

**Common misconception:** "White balance in Photoshop is the same as white balance in Camera Raw." It is not. ACR white balance adjusts channel multipliers on raw linear data before rendering. Photoshop white balance adjusts rendered, gamma-encoded values after rendering. ACR white balance has more headroom and produces smoother results, especially for large corrections. If you have access to the RAW file (especially as a Smart Object, per Chapter 06), always perform the primary white balance correction in ACR. Use Photoshop's tools for refinement, for regional corrections (different casts in different areas), or when the RAW file is not available.

---

### Complementary Colors (in RGB Context)

**Definition:** In the RGB color model, each primary color has a complementary color formed by the combination of the other two primaries. The three complementary pairs are: Red and Cyan (Cyan = Green + Blue), Green and Magenta (Magenta = Red + Blue), Blue and Yellow (Yellow = Red + Green). Reducing one primary is visually equivalent to increasing its complement, and vice versa.

**Meaning in photographic practice:** Understanding complementary pairs is essential for color correction because every per-channel adjustment has a predictable, bidirectional color effect. If you lower the Blue channel, the image shifts toward Yellow. If you raise the Blue channel, it shifts toward Blue. There are only three independent channels, so there are only six color directions you can move in: toward any of the three primaries or toward any of their three complements. Every color cast can be described in terms of these six directions, and every correction is a movement in the opposite direction.

**Photoshop implementation:** The complementary relationships are directly visible in per-channel Curves:

| Channel Action | Color Shift |
|---------------|-------------|
| Raise Red | Toward Red |
| Lower Red | Toward Cyan |
| Raise Green | Toward Green |
| Lower Green | Toward Magenta |
| Raise Blue | Toward Blue |
| Lower Blue | Toward Yellow |

These are the same relationships presented in Chapter 07's per-channel Curves discussion. In this chapter, they become the operational vocabulary for color correction.

**Related concepts:** RGB color model, per-channel Curves, color cast, color wheel, additive color mixing.

**Common misconception:** "Complementary colors in RGB are the same as complementary colors in painting." They are not identical systems. In painting (subtractive mixing), complementary pairs are defined differently depending on the color model used (traditional, CMY, Munsell, etc.). In RGB (additive mixing), the complements are precisely defined by the channel structure: R-C, G-M, B-Y. This chapter uses the RGB definitions exclusively, because Photoshop's per-channel tools operate in RGB.

---

### Per-Channel Curves

**Definition:** Per-channel Curves refers to using the Curves adjustment on individual color channels (Red, Green, or Blue) rather than on the master RGB composite. Each channel's curve independently remaps that channel's values, producing color shifts rather than (primarily) tonal shifts. Per-channel Curves is the most precise color correction tool in Photoshop.

**Meaning in photographic practice:** Per-channel Curves allows you to correct color casts with tonal specificity. If a fluorescent light has added a green cast to the midtones but the highlights are relatively clean, you can lower the Green curve only in the midtone region — leaving the highlights untouched. If tungsten light has made the shadows warm while the highlights (lit by window daylight) are cool, you can lower the Red and raise the Blue in the shadows only, without affecting the highlights. No other color correction tool in Photoshop offers this degree of regional color control.

**Photoshop implementation:** In the Curves adjustment layer, the channel dropdown at the top switches between RGB (master), Red, Green, and Blue. Each channel has its own independent curve. All four curves are active simultaneously within a single Curves adjustment layer. The on-image adjustment tool (hand icon, introduced in Chapter 07) works per-channel: switch to the Blue channel, click on a midtone area in the image, and Photoshop places a point on the Blue curve at that tonal value. Drag down to reduce Blue (shift toward Yellow) or up to increase Blue.

**Related concepts:** Curves adjustment (Chapter 07), complementary colors, master curve vs per-channel curves (Chapter 07), color cast, tonal targeting.

**Common misconception:** "Per-channel Curves is for advanced users; Color Balance is simpler and does the same thing." Color Balance divides the tonal range into three broad zones (Shadows, Midtones, Highlights) and offers slider control within each zone. Per-channel Curves lets you place anchor points at any position along the tonal range and control the curve shape with arbitrary precision. Color Balance is a blunt approximation of what per-channel Curves can do. For simple, global corrections, Color Balance may suffice. For complex or tonal-region-specific corrections, per-channel Curves is the only adequate tool.

---

### Color Balance Adjustment

**Definition:** The Color Balance adjustment layer provides three slider pairs (Cyan/Red, Magenta/Green, Yellow/Blue) that shift the color balance in three independently addressable tonal zones: Shadows, Midtones, and Highlights. A "Preserve Luminosity" checkbox prevents the color shift from also changing the overall brightness.

**Meaning in photographic practice:** Color Balance is the most intuitive color adjustment tool in Photoshop. The slider labels directly name the color directions: drag the Cyan/Red slider toward Cyan to add Cyan (cool) or toward Red to add Red (warm). The three tonal zones let you, for example, warm the shadows while cooling the highlights — a common aesthetic adjustment. Because the sliders are labeled with color names rather than channel names, Color Balance is easier to use intuitively than per-channel Curves, but it offers less precision.

**Photoshop implementation:** Available as an Adjustment Layer (Layer > New Adjustment Layer > Color Balance) or as a direct adjustment. The Properties panel shows three sliders and a radio button selector for Shadows, Midtones, or Highlights. The "Preserve Luminosity" checkbox is checked by default. When checked, the adjustment shifts color without changing the perceived brightness of the affected tones. When unchecked, adding Red also brightens slightly (because Red contributes to luminosity), and adding Cyan darkens slightly.

**Related concepts:** Per-channel Curves, complementary colors, color correction, color grading (Chapter 10).

**Common misconception:** "Color Balance is just a simplified Curves." Color Balance and per-channel Curves produce different results even when targeting the same color shift in the same tonal region. Color Balance divides the tonal range into three fixed zones with soft transitions between them. Per-channel Curves lets you define the boundaries and transition shapes. Color Balance's "Preserve Luminosity" option has no direct equivalent in Curves (though you can approximate it with blending mode changes). They are related tools but not mathematical equivalents.

---

### Levels Per-Channel (Eyedroppers: Black Point, Gray Point, White Point)

**Definition:** The Levels adjustment includes three eyedropper tools — the black point eyedropper (sets the sample point as the darkest value), the white point eyedropper (sets it as the brightest value), and the gray point eyedropper (neutralizes the sample point, making it achromatic). The black and white eyedroppers perform per-channel endpoint corrections. The gray point eyedropper performs a per-channel midtone correction that forces the sampled pixel's R, G, and B values to become equal — effectively removing the color cast at that tonal level and propagating the correction across the tonal range.

**Meaning in photographic practice:** The gray point eyedropper is the fastest color correction tool in Photoshop. If you have a known neutral reference in the image — a gray card, a concrete surface, a white wall — clicking on it with the gray point eyedropper immediately corrects the color balance across the entire image. The correction assumes that the sampled area should be neutral, adjusts each channel's gamma independently to make R=G=B at that point, and applies the resulting per-channel gamma adjustment to the full tonal range. For images with a simple, uniform color cast and a visible neutral reference, this single click can be a complete correction.

**Photoshop implementation:** In the Levels Properties panel (Adjustment Layer), the three eyedroppers appear in the lower-left area. From left to right: black point eyedropper (sets the clicked pixel as black in each channel independently), gray point eyedropper (neutralizes the clicked pixel), white point eyedropper (sets the clicked pixel as white in each channel independently). Double-clicking an eyedropper opens the Color Picker, letting you set the target value — for example, setting the white point eyedropper target to R=245, G=245, B=245 instead of pure white to prevent highlight clipping.

**FACT:** The same eyedropper tools are available in the Curves Properties panel. They function identically whether accessed from Levels or Curves.

**Related concepts:** Neutral reference, gray point, color cast, Levels adjustment (Chapter 07), per-channel Levels.

**Common misconception:** "The gray point eyedropper works perfectly on any gray-looking area." The eyedropper forces the clicked pixel to become neutral. If the area you click is not actually a neutral tone — if it is a colored surface that merely looks grayish — the correction will be wrong. The tool cannot distinguish "this is gray with a cast" from "this is inherently a colored surface." You must be confident that the reference area is genuinely neutral in the original scene before clicking. An incorrect reference produces an incorrect correction across the entire image.

---

### Info Panel Color Readout

**Definition:** The Info panel (Window > Info) displays the color values of the pixel directly under the cursor, in real time, as the cursor moves over the image. When an adjustment layer is active, the Info panel shows two sets of values separated by a slash: the value before the adjustment (left) and the value after (right). The readout mode (RGB, CMYK, Lab, HSB, etc.) is configurable independently for the first and second color readout.

**Meaning in photographic practice:** The Info panel is the primary diagnostic instrument for color correction. It transforms color correction from a subjective visual judgment ("that looks about right") into a measurable, verifiable process ("the neutral reference reads R=128, G=128, B=132 — there is a 4-level blue cast in the midtones"). For critical work, especially on uncalibrated or unknown monitors, the Info panel is more reliable than your eyes. Your eyes adapt to color casts within seconds — a warm image starts to look normal after you stare at it. The Info panel does not adapt.

**Photoshop implementation:** Open the Info panel via Window > Info (or F8). Click the small eyedropper icon within the panel to change the readout mode. For color correction work, the first readout should be set to "Actual Color" (which shows values in the document's color space, typically RGB). The second readout can be set to a different mode — CMYK is useful if you are preparing for print, and Lab is useful for evaluating perceptual lightness independently of color. When a Curves or Levels adjustment layer is active and selected, hovering over any pixel shows the original value and the adjusted value side by side.

**Related concepts:** Color Sampler, per-channel Curves, color cast, neutral reference.

**Common misconception:** "The Info panel shows what the color looks like." The Info panel shows what the numbers are. The numbers are the objective truth of the file. What the color "looks like" depends on your monitor's calibration, your ambient lighting, and your visual adaptation state. For color correction, trust the numbers. If the Info panel says a neutral reference reads R=G=B after your correction, the correction is numerically accurate regardless of whether your eyes agree on an uncalibrated screen.

---

### Color Sampler

**Definition:** The Color Sampler tool (nested under the Eyedropper in the toolbar, keyboard shortcut I, cycle through tools with Shift+I) places persistent measurement points on the image. Up to ten Color Sampler points can be placed simultaneously. Each sampler's values are displayed in the Info panel and update in real time as adjustments are made. Unlike the normal cursor readout, Color Sampler points remain fixed regardless of where the cursor moves.

**Meaning in photographic practice:** Color Samplers are the fixed reference points of a color correction session. You place them on critical areas at the start of the correction — a neutral reference, a skin tone, a known-color object, a shadow with detail — and then monitor their values throughout every adjustment. They turn the correction process from "adjust and hope" into "adjust and verify." If you place a sampler on a gray card, you can watch its R, G, and B values converge as you adjust per-channel Curves, and stop precisely when they are equal.

**Photoshop implementation:** Select the Color Sampler tool from the toolbar (nested under the Eyedropper). Click on the image to place a sampler point — a small numbered crosshair appears. Each sampler's values are shown in the Info panel below the cursor readout. To remove a sampler, Alt/Option+click on it, or drag it off the canvas. To move a sampler, click and drag it while the Color Sampler tool is active. The sample size (point sample vs 3x3 average vs 5x5 average, etc.) is set in the Options bar. **RECOMMENDATION:** Use a 3x3 or 5x5 average rather than Point Sample. A point sample reads a single pixel, which may be noisy or atypical. An averaged sample reads the neighborhood, giving a more reliable diagnostic value.

**Related concepts:** Info panel, Eyedropper tool, neutral reference, per-channel Curves, color correction workflow.

**Common misconception:** "Color Samplers are the same as the Eyedropper tool." The Eyedropper sets the foreground or background color. The Color Sampler places a persistent diagnostic point. They are different tools with different purposes, though they share a toolbar slot. The Eyedropper is for choosing a color. The Color Sampler is for monitoring a location.

---

## Theory

### What Does "Correct" Color Mean?

Before you can correct color, you must define what "correct" means. There are two valid definitions, and they serve different purposes.

**Technical neutrality:** A technically correct image has neutral tones that measure as neutral (R=G=B in the working space) and colors that are an accurate reproduction of the scene. A gray card under the light source reads equal R, G, B. White objects are white. Skin tones fall within their expected range. This definition is objective, measurable, and independent of creative intent.

**FACT:** In a properly profiled RGB working space (sRGB, Adobe RGB, ProPhoto RGB), a pixel with R=G=B at any brightness level is, by definition, neutral — it represents a shade of gray with no chroma. This is a mathematical property of the color space definition. If a known-neutral object does not measure as R=G=B, the image has a color cast.

**Creative intent:** A creatively correct image has the color character the photographer intended. A golden-hour landscape should look warm. A moonlit scene should look cool. These are deliberate departures from technical neutrality. Creative intent does not contradict technical correction — it builds on it. You correct first (establish the accurate baseline), then grade (depart intentionally).

**INTERPRETATION:** In practice, "correct" color often means "plausible" rather than "measured against a reference standard." Most photographs do not include a gray card. The goal is colors that look natural and unbiased — whites that look white, grays that look gray, skin that looks like skin — even if you cannot verify them against an absolute reference. The Info panel and neutral references get you as close to objective as the image allows; after that, informed judgment takes over.

### Complementary Colors and Per-Channel Relationships

The RGB color model defines three independent channels. The relationship between these channels determines the color at every pixel. Color correction works by adjusting these channel relationships.

The six color directions in RGB:

| Direction | Achieved By | Complementary Direction |
|-----------|------------|------------------------|
| Toward Red | Raise Red channel | Toward Cyan |
| Toward Cyan | Lower Red channel | Toward Red |
| Toward Green | Raise Green channel | Toward Magenta |
| Toward Magenta | Lower Green channel | Toward Green |
| Toward Blue | Raise Blue channel | Toward Yellow |
| Toward Yellow | Lower Blue channel | Toward Blue |

**FACT:** These are not six independent adjustments. There are only three channels, each with two directions (raise or lower). The six color directions come from three independent controls. Shifting toward Cyan is the same operation as shifting away from Red — they are the same channel adjustment viewed from opposite ends.

### How Color Casts Appear in the Data

A color cast manifests as a systematic imbalance between channel values in areas that should be neutral. The nature of the cast determines which channels are out of balance:

| Cast Color | Channel Imbalance at Neutral Points | Correction Direction |
|-----------|--------------------------------------|---------------------|
| Warm (orange/yellow) | R too high, B too low (G variable) | Lower R, raise B |
| Cool (blue) | B too high, R too low | Raise R, lower B |
| Green | G too high relative to R and B | Lower G |
| Magenta | G too low relative to R and B | Raise G |
| Cyan | R too low, G and B too high | Raise R |
| Yellow-green (fluorescent) | G too high, B too low | Lower G, raise B |

**INTERPRETATION:** Most real-world color casts involve two channels, not one. A tungsten cast is not just "too much Red" — it is "too much Red and too little Blue," often with Green falling between the two. Correcting only one channel may fix one aspect of the cast while introducing a new imbalance. This is why the Info panel is essential: it tells you exactly which channels are off and by how much.

### Per-Channel Curves for Color Correction: The Mechanism

When you adjust the master (RGB) Curves, the same remapping applies to all three channels — this primarily changes brightness and contrast, as covered in Chapter 07. When you adjust a per-channel curve, you change values in that single channel while the others remain unchanged at that input position.

The key insight for color correction: you can target color shifts to specific tonal regions by placing anchor points strategically on the per-channel curve.

**Example:** An image shot under tungsten light has a strong warm cast in the midtones and shadows, but the highlights (a window showing daylight) are approximately correct.

- On the Blue channel curve: the lower and middle portions need to be raised (adding Blue to counteract the Yellow cast in shadows and midtones). The upper portion (highlights) should remain near the default diagonal.
- On the Red channel curve: the lower and middle portions may need to be lowered slightly (reducing excessive Red in the warm regions). The highlights stay anchored.

This produces a correction that fixes the tungsten-lit areas without shifting the daylight areas. No global correction tool — Color Balance, Levels eyedroppers, or Photo Filter — can achieve this specificity.

**FACT:** When you raise or lower a single channel, you change both the color and the brightness of the affected pixels. Raising the Blue channel adds Blue but also slightly increases the overall luminosity of affected pixels (because Blue contributes to the composite brightness). For most color correction adjustments, this brightness shift is small enough to be negligible. For large adjustments, you may need a follow-up tonal correction on the master curve. Alternatively, setting the Curves adjustment layer's blending mode to "Color" applies only the color shift without the luminosity change — though this alters how the curve values translate to visible effect.

### The Info Panel as a Color Correction Instrument

The Info panel is not a convenience tool for color correction — it is the primary instrument. Here is the diagnostic workflow:

1. **Place Color Samplers** on known or suspected neutral areas at different brightness levels (a dark neutral, a midtone neutral, a highlight neutral). If no neutral references exist in the image, place samplers on areas where you have the strongest expectation of neutrality.

2. **Read the channel values.** A neutral midtone reading R=142, G=138, B=124 tells you: Red is slightly high (2-4 levels above the mean of the three), Blue is significantly low (14-18 levels below the mean). This is a warm cast, weighted toward Yellow (low Blue) with a hint of excess Red.

3. **Apply per-channel Curves.** Raise the Blue curve in the midtone region. Check the Info panel: the sampler should now show Blue rising toward the mean. Lower the Red curve slightly. Check again.

4. **Verify convergence.** The correction is complete when the neutral reference reads R=G=B (within 1-2 levels — perfect equality is not always achievable due to rounding and interpolation). The before/after values displayed in the Info panel (left of slash vs right of slash) confirm the change numerically.

**RECOMMENDATION:** Do not rely on visual assessment alone, especially on uncalibrated monitors or in rooms with colored ambient light. The Info panel gives you objective, numerical feedback. Use it. An image that "looks right" on your screen may look wrong on a calibrated display. An image where the numbers are right will be right everywhere.

---

## Photoshop Implementation

### Per-Channel Curves for Color Correction: Detailed Walkthrough

**Setup:** Create a Curves Adjustment Layer (Adjustments panel > Curves icon, or Layer > New Adjustment Layer > Curves). Open the Info panel (Window > Info or F8). Select the Color Sampler tool and place samplers on 2-4 areas you expect to be neutral.

**Step 1: Diagnose the cast.** Read the sampler values in the Info panel. Identify which channel(s) are too high or too low relative to the others.

**Step 2: Select the channel to correct.** In the Curves Properties panel, use the channel dropdown to switch from RGB to the channel that needs adjustment. If the Blue channel is too low (warm/yellow cast), select the Blue channel.

**Step 3: Target the tonal region.** Use the on-image adjustment tool (hand icon with arrows in the Curves panel) to click on a midtone area of the image while the appropriate channel is selected. This places a point on the channel curve at the exact tonal value of that area.

**Step 4: Adjust the point.** Drag the point upward (to increase that channel's contribution) or downward (to decrease it). Watch the Info panel — the sampler values update in real time. Drag until the channels at the neutral reference approach equality.

**Step 5: Anchor unaffected regions.** If only part of the tonal range needs correction (common in mixed lighting), place anchor points in the regions that should not change. For example, if the highlights are correct but the midtones are cast, place a point in the highlight region of the channel curve and leave it on the diagonal. Then adjust the midtone point. The anchor prevents the highlight correction from drifting.

**Step 6: Repeat for other channels if needed.** Switch to the next channel that needs adjustment and repeat. Most corrections involve two channels — for example, raising Blue and lowering Red for a tungsten cast.

**Step 7: Verify.** Check all sampler points. Neutral references should now read approximately R=G=B. Non-neutral areas (skin, foliage, sky) should not look obviously wrong. Toggle the adjustment layer on/off (eye icon) for a before/after comparison.

### Color Balance: Detailed Walkthrough

**When to use:** Color Balance is appropriate for simple, broad color corrections where per-channel Curves' precision is not required. It is also useful as a quick preliminary correction before refining with Curves, or for corrections where you think in terms of color names (warmer/cooler) rather than channel numbers.

**Setup:** Create a Color Balance Adjustment Layer (Adjustments panel > Color Balance icon, or Layer > New Adjustment Layer > Color Balance).

**Controls:**

| Control | Range | Effect |
|---------|-------|--------|
| Cyan/Red slider | -100 to +100 | Negative = toward Cyan; Positive = toward Red |
| Magenta/Green slider | -100 to +100 | Negative = toward Magenta; Positive = toward Green |
| Yellow/Blue slider | -100 to +100 | Negative = toward Yellow; Positive = toward Blue |
| Tone selector | Shadows / Midtones / Highlights | Targets the correction to one tonal zone |
| Preserve Luminosity | Checkbox (default on) | Prevents color shifts from changing brightness |

**Workflow:** Select the tonal zone to correct (start with Midtones). Drag the sliders in the direction opposite to the cast. If the image is too warm (yellowish), drag Yellow/Blue toward Blue and Cyan/Red toward Cyan. Adjust each tonal zone independently if the cast varies between shadows, midtones, and highlights.

**RECOMMENDATION:** Leave "Preserve Luminosity" checked for color correction. This prevents the color shift from also changing the tonal balance you established in Chapter 07. Uncheck it only when you intentionally want the brightness to follow the color shift (more common in creative grading than in correction).

### Levels Per-Channel: Eyedropper Correction

**Setup:** Create a Levels Adjustment Layer. Ensure the Info panel is open and Color Samplers are placed on neutral references.

**Using the gray point eyedropper:**

1. Select the gray point eyedropper (the middle eyedropper icon in the Levels or Curves Properties panel).
2. Click on an area of the image that you know should be neutral gray — not pure white, not pure black, but a midtone gray.
3. Photoshop recalculates the per-channel gamma values to make the clicked pixel read as neutral (R=G=B). This correction propagates across the tonal range.
4. Check the result. If the correction looks wrong, the reference was not truly neutral. Ctrl+Z / Cmd+Z to undo and try a different reference point.

**FACT:** The gray point eyedropper performs a per-channel gamma adjustment — it independently adjusts the midtone (gamma) slider of each channel to force the clicked pixel to neutrality. This is equivalent to adjusting three gamma sliders manually in per-channel Levels. The eyedropper does it in one click but does not allow you to target specific tonal regions — it applies a global gamma correction per channel.

**Using the black and white point eyedroppers for color correction:**

The black point eyedropper sets the clicked pixel as the black point independently in each channel. If the darkest area of the image has a color cast (for example, the shadow reads R=5, G=8, B=15 — too blue in the deep shadows), clicking with the black point eyedropper clips each channel to its own darkest point, removing the shadow cast. Similarly, the white point eyedropper sets independent per-channel white points.

**RECOMMENDATION:** Double-click the black point eyedropper and set the target to R=5, G=5, B=5 rather than pure black (R=0, G=0, B=0). Double-click the white point eyedropper and set the target to R=245, G=245, B=245 rather than pure white. This prevents hard clipping at the extremes while still establishing neutral endpoints. These target values persist across sessions until you change them.

### Info Panel Configuration for Color Correction

**Configuring the readout:**

1. Open the Info panel (Window > Info).
2. Click the small eyedropper icon next to the first readout to set the mode. For color correction, "Actual Color" (the document's native color mode, typically RGB) is the most useful first readout.
3. Click the second readout's eyedropper icon. Set it to a secondary mode useful for verification. Options:
   - **Lab Color:** Useful because the a* and b* channels represent color independently of lightness. A neutral tone has a=0, b=0 in Lab. Any deviation indicates a cast, regardless of the tone's brightness.
   - **CMYK:** Useful if your output is print — you can monitor CMYK values while correcting in RGB.
   - **HSB:** Shows Hue, Saturation, Brightness. A neutral tone has Saturation = 0%. Any saturation in a known-neutral area indicates a cast.

**RECOMMENDATION:** For color correction, set the second readout to Lab. A neutral reference should show a* and b* both at or near 0. If b* is negative, there is a blue cast; if positive, yellow. If a* is positive, there is a magenta cast (in skin tones, some positive a* is normal); if negative, green. Lab separates lightness from color, making cast diagnosis simpler in some situations.

---

## Professional Workflow

### The Systematic Color Correction Sequence

Color correction is most reliable when performed as a systematic sequence rather than an ad hoc visual adjustment. This workflow minimizes guesswork and produces consistent, verifiable results.

**Step 1: Complete tonal corrections first.**

Before correcting color, complete the tonal corrections from Chapter 07 — set the black and white points, establish midtone contrast. Tonal corrections on the master curve affect all three channels equally and do not introduce color shifts (or introduce only minor ones). If you correct color before setting the tonal range, your color correction may shift when you subsequently adjust the tonal endpoints.

**Step 2: Identify neutral references.**

Scan the image for areas that should be neutral: gray cards, white surfaces, concrete, asphalt, metal, shadows in neutral-colored objects. Place Color Sampler points on 2-4 neutral references at different brightness levels — ideally one in the shadows, one in the midtones, and one in the highlights.

**Step 3: Diagnose the cast numerically.**

Read the Color Sampler values in the Info panel. For each neutral reference, note which channel(s) are higher or lower than the others and by how much. Determine whether the cast is uniform (same imbalance at all brightness levels) or variable (different casts in shadows vs highlights, indicating mixed lighting).

**Step 4: Choose the correction tool.**

| Situation | Recommended Tool |
|-----------|-----------------|
| Simple, uniform cast with a good neutral reference | Levels gray point eyedropper (one click) |
| Simple, uniform cast without a clear neutral reference | Color Balance (intuitive slider adjustment) |
| Cast that varies across the tonal range (mixed lighting) | Per-channel Curves (targeted anchor points) |
| Cast concentrated in shadows or highlights only | Per-channel Curves (adjust one end, anchor the other) |
| Minor refinement after primary correction | Color Balance or small per-channel Curves tweak |

**Step 5: Apply the correction.**

For per-channel Curves: adjust the relevant channel(s) in the tonal region(s) where the cast appears, anchoring regions that are already correct. Monitor the Info panel continuously.

For the Levels gray point eyedropper: click on the most reliable midtone neutral reference.

For Color Balance: select the tonal zone, shift the sliders opposite to the cast direction.

**Step 6: Verify numerically.**

After the correction, check all Color Sampler points:

- Neutral references should now read R=G=B within 1-2 levels.
- Skin tones should fall within plausible ranges (see Step-by-Step Example below).
- No new cast should have been introduced in regions that were previously correct.

**Step 7: Verify visually.**

Toggle the correction layer on/off for a before/after comparison. Look for:

- Neutral areas that now appear neutral.
- Skin tones that look natural.
- No overcorrection (a cast replaced by its complement is equally wrong).
- No regional artifacts (one area corrected, another made worse).

**Step 8: Name the layer.**

Name the Curves or Color Balance adjustment layer descriptively: "CC - Remove Tungsten Cast" or "CC - Neutralize Midtones." This documents the intent for future reference.

### Where Color Correction Fits in the Layer Stack

Following the layer stack order established in Chapter 07:

1. Smart Object (base photograph)
2. Retouching layers (sensor spots, blemishes)
3. Tonal adjustments (Levels for endpoints, Curves for contrast)
4. **Color correction** (per-channel Curves, Color Balance, Levels eyedroppers)
5. Color grading (Chapter 10)
6. Local adjustments (dodging, burning — Chapter 08)

**RECOMMENDATION:** Group your color correction adjustment layers into a layer group named "Color Correction" or "CC." Keep them separate from tonal adjustment layers (which are in a "Tonal" group) and from color grading layers (Chapter 10). This three-group separation — Tonal, Color Correction, Color Grading — makes the workflow auditable and each group independently toggleable.

### Mixed Lighting: The Hard Problem

Most simple scenes have a single dominant light source with a single color temperature. The cast is uniform across the tonal range, and a single global correction fixes it.

Mixed lighting — two or more light sources with different color temperatures illuminating different areas of the same scene — produces casts that vary spatially or tonally. A room lit by tungsten lamps with daylight coming through a window has warm shadows (tungsten-dominated) and cool highlights (daylight-dominated). A subject under fluorescent overhead lighting with a tungsten fill has a green cast from above and a warm cast from below.

**Correcting mixed lighting requires one of two approaches:**

1. **Per-channel Curves with tonal targeting:** If the different light sources affect different tonal ranges (shadows vs highlights), per-channel Curves can correct each range independently. This works when the tonal separation corresponds to the lighting separation — which is common in practice, because the weaker light source dominates the shadows while the stronger one dominates the highlights.

2. **Masked, region-specific corrections:** If the different light sources affect different spatial regions (left side vs right side, subject vs background), you need multiple correction layers, each with a mask limiting its effect to the appropriate region. This combines the masking techniques from Chapter 05 with the color correction techniques from this chapter.

**INTERPRETATION:** Mixed lighting is the most difficult color correction problem in photography. Perfect correction of a mixed-lighting image often requires multiple correction passes — a global correction for the dominant cast, followed by masked local corrections for regions dominated by the secondary light source. In many cases, the most effective correction happens in Camera Raw, using local adjustments with the white balance brush, before the image reaches Photoshop. If you have the RAW file as a Smart Object (Chapter 06), consider returning to ACR for the primary white balance work and using Photoshop only for refinement.

---

## Step-by-Step Example: Correcting a Mixed-Lighting Portrait

This example walks through correcting a portrait photograph taken in a room with tungsten ceiling lights and a large window providing daylight fill. The subject's face is lit primarily by the window (cooler light) while the background and the shadow side of the face are dominated by the tungsten lights (warmer light). The image was shot in RAW and developed with the Camera Raw white balance set to "As Shot" (which was close to daylight, leaving the tungsten-lit areas warm).

### Starting Condition

The image has been through tonal correction (Chapter 07): endpoints are set, midtone contrast is appropriate. But the color is not right:

- The shadow side of the subject's face is noticeably warm/orange.
- The background wall (painted white) appears yellowish.
- The highlight side of the face (daylight-lit) appears approximately correct, perhaps very slightly cool.
- Overall, the image reads as "too warm" but the warmth is not uniform — it is concentrated in the shadows and midtones.

### Step 1: Place Color Samplers

Select the Color Sampler tool. Set the sample size to 5x5 Average in the Options bar. Place four samplers:

- **Sampler #1:** On the white background wall in a tungsten-lit area (this should be neutral).
- **Sampler #2:** On the subject's forehead on the highlight (daylight) side.
- **Sampler #3:** On the subject's cheek on the shadow (tungsten) side.
- **Sampler #4:** On the subject's white shirt collar (another neutral reference).

### Step 2: Read the Info Panel

Open the Info panel. Note the readings (example values):

| Sampler | Location | R | G | B | Diagnosis |
|---------|----------|---|---|---|-----------|
| #1 | White wall (tungsten-lit) | 195 | 182 | 155 | R high, B low = warm cast |
| #2 | Forehead, highlight side | 218 | 190 | 170 | Normal skin — not neutral |
| #3 | Cheek, shadow side | 175 | 145 | 108 | Warm cast over skin tone |
| #4 | White shirt collar | 210 | 200 | 178 | R high, B low = warm cast |

**Diagnosis:** Samplers #1 and #4 are neutral references. Both show the same pattern: Red is the highest channel, Blue is the lowest, with a spread of approximately 35-40 levels. This is a significant warm/yellow cast. The cast is consistent between the midtone neutral (#1: approximate midtone brightness) and the highlight neutral (#4: brighter). Sampler #2 (skin on the daylight side) shows relatively normal skin tone values — some excess of Red over Green over Blue is expected in skin.

### Step 3: Create a Curves Adjustment Layer

Create a Curves adjustment layer above the tonal correction layers. Name it "CC - Remove Tungsten Cast."

### Step 4: Correct the Blue Channel

Switch to the Blue channel in the Curves dropdown. The Blue channel is the most deficient (the warm cast is primarily a Blue deficiency / Yellow excess).

1. Use the on-image adjustment tool. Click on the white wall (Sampler #1 area). Photoshop places a point on the Blue channel curve at approximately the midtone position.
2. Drag upward. Watch the Info panel — Sampler #1's Blue value rises. Drag until the Blue value at Sampler #1 approaches the Green value (approximately 182). Stop. The reading should now be approximately R=195, G=182, B=182.
3. Check Sampler #4 (shirt collar). The Blue value there should also have risen, though perhaps not to full equality. If the highlight region needs more Blue, place a second point higher on the Blue curve and raise it slightly.
4. Place an anchor point in the deep shadow region of the Blue curve (near the bottom-left) to prevent the correction from adding excessive Blue to the very deepest shadows, where noise in the Blue channel could become visible.

### Step 5: Correct the Red Channel

Sampler #1 now reads approximately R=195, G=182, B=182. The Red channel is still too high by approximately 13 levels.

Switch to the Red channel. Use the on-image adjustment tool to click on the white wall. Place a point on the Red curve. Drag downward slightly until the Red value at Sampler #1 drops to approximately 182-184. The reading should now be approximately R=183, G=182, B=182 — effectively neutral.

Check Sampler #4 (shirt collar). It should now also be near-neutral. Verify.

### Step 6: Evaluate Skin Tones

Sampler #2 (highlight forehead) and Sampler #3 (shadow cheek) are not neutral references — they should not read R=G=B. But they should read as plausible skin tones after the correction.

**FACT:** Skin tone values vary widely depending on the subject's complexion, the light source, and the color space. There is no single "correct" skin tone value. However, some general patterns hold in RGB working spaces for a medium-complexion Caucasian subject under neutral light:

| Skin Tone Region | Typical R:G:B Relationship |
|-----------------|---------------------------|
| Highlight skin (well-lit) | R highest, G in the middle, B lowest. Approximate ratio R > G by 10-20%, G > B by 10-15%. |
| Shadow skin | Same general order (R > G > B), but with smaller absolute differences and lower overall values. |
| Dark complexion | Lower overall values, R > G > B relationship still typically holds but with narrower spread. |
| Light/fair complexion | Higher overall values, may have narrower R-G-B spread. |

**INTERPRETATION:** These are guidelines, not rules. The correct evaluation of skin tones requires judgment based on the subject and the lighting. After removing the cast from neutral references, skin tones should look plausible. If they look obviously wrong after a correction that made neutrals neutral, there may be a localized color issue (reflected color from clothing or surroundings) that requires a masked correction.

### Step 7: Final Verification

Toggle the "CC - Remove Tungsten Cast" layer on and off.

- **Off (before):** The wall looks yellowish, the shirt collar looks warm, the shadow side of the face is orange-tinted.
- **On (after):** The wall looks white/gray. The shirt collar looks white. The shadow side of the face has a natural skin color without excessive warmth. The highlight side is unchanged or very slightly warmer (which is acceptable — daylight has a slight warm quality compared to pure achromatic).

Check all sampler values one final time. Neutral references should be within 2-3 levels of R=G=B. Skin tones should show a plausible R > G > B ordering without extreme channel imbalances.

---

## Common Mistakes

**Correcting color before completing tonal adjustments.** Tonal corrections (Levels/Curves on the master channel) affect all three channels simultaneously. If you set your white point after color correction, the endpoint adjustment may shift the channel balance you just corrected. Complete tonal work first, then color correct.

**Clicking the gray point eyedropper on a non-neutral area.** The gray point eyedropper forces the clicked pixel to R=G=B. If you click on a colored surface — a beige wall, a bluish concrete, a shirt that is close to white but actually pale blue — the entire image shifts to compensate for a "cast" that is actually correct color. Always verify that your neutral reference is genuinely achromatic before using the eyedropper.

**Trusting your eyes instead of the Info panel.** Human vision adapts to color casts within seconds. After staring at a warm image for thirty seconds, you stop perceiving the warmth. After correcting the warmth, the image may temporarily look too cool to your adapted eyes. The Info panel does not adapt. It shows the numbers. Trust the numbers for diagnostic purposes, then evaluate visually after a brief break from the image.

**Overcorrecting: replacing one cast with its complement.** If you raise the Blue channel too far, a warm cast becomes a cool cast. The goal is neutrality, not the opposite extreme. Monitor the Info panel and stop when the channels converge. A 2-level residual imbalance is invisible; a 20-level overcorrection in the opposite direction is worse than the original cast.

**Applying a global correction to a mixed-lighting image.** A single Curves or Color Balance adjustment that corrects the tungsten-lit areas will overcorrect the daylight-lit areas (and vice versa). Mixed lighting requires either per-channel Curves with tonal targeting (correcting different tonal ranges differently) or masked regional corrections. A single global correction cannot fix both light sources simultaneously.

**Using Color Balance for precision work.** Color Balance divides the tonal range into three broad, overlapping zones. If the cast is confined to a narrow tonal band (for example, only the upper midtones), Color Balance cannot target it precisely — it will shift the entire midtone zone. Use per-channel Curves for targeted corrections.

**Ignoring the sample size setting on the Color Sampler.** Point Sample reads a single pixel, which may be noise, a hot pixel, or otherwise atypical. Set the sample size to 3x3 Average or 5x5 Average for diagnostic work. The averaged reading is more representative of the actual tone at that location.

**Correcting a creative warm tone that was intentional.** Not every warm image has a color cast. Golden-hour light is warm. Candlelight is warm. If the photographer (or you) chose a warm white balance in Camera Raw deliberately, "correcting" it removes the intended atmosphere. Before correcting, determine whether the color is an error or a choice. If unsure, check whether neutral references exist in the image. If they read neutral despite the overall warmth, the warmth is part of the scene, not a cast.

---

## Alternative Approaches

### Camera Raw as the Primary Color Correction Tool

For images from RAW files — especially those opened as Smart Objects (Chapter 06) — the most effective white balance and color correction happens in Adobe Camera Raw (ACR) or Lightroom. The Temperature and Tint sliders in ACR operate on the raw sensor data before gamma encoding, producing smoother and more accurate results than equivalent corrections in Photoshop.

ACR also provides a white balance eyedropper that works identically to the Levels gray point eyedropper but operates on raw data. The local adjustment tools in ACR (Graduated Filter, Radial Filter, Adjustment Brush) allow regional white balance shifts for mixed-lighting situations without leaving ACR.

**RECOMMENDATION:** If you have access to the RAW file: perform the primary white balance and global color correction in ACR. Use Photoshop's per-channel Curves for refinement, regional corrections that require precise masking, and corrections that must interact with other Photoshop-specific adjustments.

### Lab Color Mode for Color Correction

Color correction can be performed in Lab color mode (Image > Mode > Lab Color), where the L channel represents lightness and the a* and b* channels represent color independently. A color cast appears as an offset in the a* channel (green-magenta axis) or the b* channel (blue-yellow axis). Correcting the cast in Lab means centering the a* and/or b* curves through the midpoint, without any effect on luminosity.

**INTERPRETATION:** Lab correction is conceptually elegant — it separates luminosity from color completely. However, converting to Lab and back to RGB introduces rounding errors (two color space conversions). For most photographic work, per-channel Curves in RGB produces excellent results without the conversion overhead. Lab correction is more commonly used in prepress and commercial retouching where the luminosity/chrominance separation is essential.

### Auto Color Correction Options

Both Levels and Curves include an Auto button. Alt/Option-clicking the Auto button in the Levels or Curves dialog opens the Auto Color Correction Options, which provides four algorithms:

| Algorithm | Behavior |
|-----------|----------|
| Enhance Monochromatic Contrast | Clips each channel by the same amount — adjusts contrast without shifting color balance. |
| Enhance Per Channel Contrast | Clips each channel independently — adjusts contrast and can correct simple casts. |
| Find Dark & Light Colors | Identifies the darkest and lightest pixels and uses them to set the black and white points per channel. |
| Enhance Brightness and Contrast | Uses a perceptually weighted algorithm for gentle brightness and contrast adjustment. |

The "Snap Neutral Midtones" checkbox (available with some algorithms) attempts to neutralize the midtone average — a rough automatic cast correction.

**INTERPRETATION:** The Auto options can produce surprisingly good results on images with simple, uniform casts and a reasonable tonal distribution. They are useful as a starting point — apply Auto, evaluate the result, then refine manually. They are not reliable for mixed lighting, subtle casts, or images where creative warmth or coolness should be preserved. Always evaluate the Auto result critically before accepting it.

### Photo Filter Adjustment Layer

The Photo Filter adjustment layer simulates placing a colored glass filter in front of the lens. It applies a uniform color tint with a density control. It can be used for simple color corrections — an "85 Warming Filter" to correct a cool cast, an "80 Cooling Filter" to correct a warm cast.

**INTERPRETATION:** Photo Filter is more of a grading tool than a correction tool. It applies a uniform tint across the entire tonal range and cannot target specific regions. For correction, per-channel Curves or Color Balance provide more control. Photo Filter is mentioned here because it is sometimes used for quick warming/cooling corrections, but it is covered more fully in Chapter 10 as a creative grading tool.

---

## Summary

Color correction is the diagnostic process of removing unwanted color casts to achieve accurate or plausibly neutral color in a photograph. It is distinct from color grading (creative color shifts) and must precede grading in the workflow to establish a known, neutral baseline.

A color cast is a systematic channel imbalance: one or two RGB channels are higher or lower than expected at tones that should be neutral. The six color directions in RGB — Red/Cyan, Green/Magenta, Blue/Yellow — are complementary pairs determined by the three-channel structure. Every cast can be described in terms of these directions, and every correction is a movement in the opposite direction.

**Neutral references** — areas that should have equal R, G, and B values — are the diagnostic anchors. Gray cards, white surfaces, concrete, unpainted metal: any known-achromatic surface in the scene tells you whether a cast exists and in which channels.

**Per-channel Curves** is the most precise correction tool. It allows you to target color shifts in specific tonal regions — correcting a warm cast in the shadows while leaving correct highlights untouched. This is essential for mixed-lighting situations where the cast varies across the tonal range.

**The Levels gray point eyedropper** is the fastest correction tool. One click on a known neutral reference applies a per-channel gamma correction that neutralizes the midtones. It is effective for simple, uniform casts but cannot handle mixed lighting or region-specific problems.

**Color Balance** provides intuitive slider control in three tonal zones. It is less precise than per-channel Curves but easier to use for simple corrections and for photographers who think in color-name terms rather than channel numbers.

**The Info panel and Color Sampler tool** are the instruments that make color correction objective. They show you the channel values at critical locations, display before/after values during adjustments, and let you verify that neutral references actually read as neutral. Without them, color correction is visual guesswork on a monitor of unknown accuracy.

The professional workflow is systematic: complete tonal corrections first, place Color Samplers on neutral references, diagnose the cast numerically, choose the appropriate tool, apply the correction while monitoring the Info panel, verify numerically and visually. White balance correction should be performed in Camera Raw when possible (operating on raw data), with Photoshop used for refinement and regional corrections.

Color correction establishes the accurate starting point. Color grading (Chapter 10) departs from it creatively. The two must remain separate in your workflow and in your thinking.

---

## Exercises

### Exercise 1: Finding and Reading Neutral References

Open a photograph of an indoor scene that includes objects you believe to be neutral — white walls, gray floors, metal surfaces, white clothing. Place Color Sampler points on four candidate neutral references at different brightness levels.

1. Read the R, G, B values at each sampler. Do any of them show equal R, G, B? If not, which channel is highest and which is lowest at each?
2. Calculate the maximum channel spread at each sampler (highest channel value minus lowest channel value). A spread of 0-3 levels is effectively neutral. A spread of 5-10 suggests a mild cast. A spread of 15+ suggests a significant cast.
3. Is the cast direction consistent across all four samplers (same channel highest at each)? Or does it vary? What does consistency or inconsistency tell you about the lighting?
4. Based on your readings, describe the cast in words: "The image has a [warm/cool/green/magenta] cast of approximately [X] levels in the [shadows/midtones/highlights/all tones]."

**Success criterion:** You can diagnose a color cast numerically from Info panel data, without relying on visual assessment.

### Exercise 2: Gray Point Eyedropper Correction

Open a photograph with a visible color cast and at least one reliable neutral reference.

1. Place a Color Sampler on the neutral reference. Record the R, G, B values.
2. Create a Levels Adjustment Layer. Select the gray point eyedropper (middle eyedropper).
3. Click on the neutral reference. Observe the immediate change in the image.
4. Check the Info panel: does the sampler now read R=G=B (or within 2 levels)?
5. Evaluate the rest of the image. Do other areas look correct? Do skin tones look natural?
6. Now undo (Ctrl+Z / Cmd+Z) and try clicking the gray point eyedropper on a different area — one that is not truly neutral (a slightly colored surface). What happens to the image? Why is the result wrong?

**Success criterion:** You understand that the gray point eyedropper's accuracy depends entirely on clicking a genuinely neutral area, and you can verify the result numerically.

### Exercise 3: Per-Channel Curves Correction

Open a photograph with a warm (tungsten) color cast. Place Color Samplers on neutral references.

1. Create a Curves Adjustment Layer. Switch to the Blue channel.
2. Place a point on the Blue curve at the midtone position. Drag it upward, watching the Info panel.
3. Stop when the Blue value at your neutral reference approaches the Green value.
4. Switch to the Red channel. If the Red value at the reference is still higher than Green and Blue, place a point and drag it down until the three channels converge.
5. Check the final reading. Is the neutral reference now within 2-3 levels of R=G=B?
6. Toggle the correction layer on/off. Evaluate the before/after visually.

Now try the same correction using Color Balance instead of Curves. Can you achieve the same result? Where does Color Balance fall short in precision compared to Curves?

**Success criterion:** You can correct a color cast using per-channel Curves, guided by the Info panel, and you understand the precision advantage of Curves over Color Balance.

### Exercise 4: Identifying the Cast Without Neutral References

Open a portrait photograph that has no obvious neutral references — no gray card, no white surface, no metal or concrete visible.

1. Examine the skin tones with the Color Sampler. Do the channels follow the expected R > G > B pattern? How wide is the spread?
2. Look at catch lights in the eyes (if visible). These often reflect the light source and can serve as near-neutral highlights. Sample them.
3. Look at the whites of the eyes. These are not perfectly white (they have blood vessels and are slightly warm), but they can serve as a rough neutral reference if no better option exists.
4. Based on whatever diagnostic information you can gather, make your best assessment of the cast direction and magnitude. Apply a correction using per-channel Curves. Verify with the Info panel on whatever reference points you have.

**Success criterion:** You can perform a reasonable color correction even when ideal neutral references are absent, using alternative reference strategies and informed judgment.

---

## Advanced Exercises

### Advanced Exercise 1: Mixed-Lighting Correction

Open a photograph taken in a room with two different light sources — for example, tungsten ceiling lights and daylight from a window. The image should show a visible difference in color between the areas dominated by each light source.

1. Place Color Samplers in neutral areas under each light source (one in the tungsten-lit zone, one in the daylight-lit zone).
2. Read both samplers. Confirm that they show different cast directions — one warm, one cool (or one green, one neutral, etc.).
3. Create a Curves Adjustment Layer. Attempt to correct the tungsten cast using per-channel Curves, targeting only the midtone/shadow region of the channel curves (where the tungsten light dominates). Anchor the highlight region to protect the daylight-lit areas.
4. Evaluate: did the per-channel Curves approach successfully correct the tungsten areas without overcorrecting the daylight areas? If not, what additional step is needed (masked regional correction)?
5. If needed, create a second Curves Adjustment Layer with a layer mask that limits the correction to the daylight-lit area. Apply a separate correction there.

**Success criterion:** You can handle mixed lighting using either tonal targeting on per-channel Curves or masked regional corrections, and you can identify which approach is appropriate for a given situation.

### Advanced Exercise 2: Correction Accuracy — Numbers vs Eyes

Correct a color cast using the Info panel exclusively — place Color Samplers on neutrals, adjust per-channel Curves until all neutral references read R=G=B, without looking at the image itself (minimize the canvas or look away). Then reveal the corrected image.

1. Does the numerically corrected image look correct to your eyes?
2. Now try correcting a copy of the same image by visual assessment only — adjust until it "looks right" without consulting the Info panel.
3. Compare the two corrections side by side. Do they differ? Check the neutral reference values on the visual-only correction. How close did your eye get to numerical accuracy?
4. Which correction would you trust for a client delivery on a monitor you have not calibrated?

**Success criterion:** You understand the value and limitations of both numerical and visual correction methods, and you can articulate when each is appropriate.

### Advanced Exercise 3: Correction vs Grading — Separating the Steps

Open a photograph taken in golden-hour light. The image is warm — everything is tinted amber. This is both a cast (the light source has a low color temperature) and a creative quality (golden-hour warmth is usually desirable).

1. Correct the image to technical neutrality. Remove the warm cast entirely using per-channel Curves, guided by the Info panel. Make neutral references read R=G=B.
2. Evaluate: does the technically neutral version look "right"? Or does it look flat and lifeless — like a photograph that has lost its atmosphere?
3. Now, on top of the correction, add a Color Balance or per-channel Curves adjustment layer as a grading layer. Reintroduce warmth deliberately — add it to the shadows and midtones, leaving the highlights cleaner. Name this layer "Grade - Golden Hour Warmth."
4. Compare: (a) the original uncorrected image, (b) the corrected-only image, (c) the corrected-then-graded image. How does the corrected-then-graded version differ from the original? Is it better? Why?

**Success criterion:** You understand the difference between correction and grading as separate workflow steps, and you can articulate why correcting first and grading second produces a more controlled result than leaving the original cast and hoping it reads as creative intent.

---

## Blackbelt Challenge

You receive a portrait photograph with the following characteristics:

- The subject was photographed in a restaurant with warm tungsten overhead lighting, green-tinted fluorescent lights in the background bar area, and cool daylight from a street-facing window on the subject's left side.
- No gray card was placed in the scene.
- The subject is wearing a white shirt and is seated at a table with a white tablecloth.
- The walls of the restaurant are painted a warm beige (not neutral).
- The RAW file was developed with the Camera Raw white balance set to "Daylight" (5500K), which was not the dominant light source.

Without opening Photoshop, answer the following:

1. **Predict the channel imbalance** you expect to see at each of these sample points: (a) the subject's white shirt on the side facing the window, (b) the white shirt on the side facing the interior, (c) the white tablecloth under the overhead tungsten light, (d) the bar area in the background under the fluorescent lights. For each, state which channel you expect to be highest and lowest, and explain why.

2. **Design a complete correction strategy.** Specify every adjustment layer (Levels, Curves, Color Balance, masked or unmasked), its purpose, and the approximate channel adjustments. Explain whether you would use a single correction or multiple corrections, and why. Address the three-way lighting challenge: how do you handle three different light sources with three different color temperatures in one image?

3. **Evaluate the neutral references.** The subject's white shirt, the tablecloth, and the beige walls are all candidate reference points. Which would you trust and which would you reject? What is the risk of using the beige wall as a neutral reference? If the beige wall reads R=180, G=165, B=140, is that a cast on a white wall or the actual color of a beige wall? How would you decide?

4. **Predict the skin tone interaction.** After correcting the tungsten cast on the interior-lit side of the face, what risk exists for the skin tone on the window-lit side? If the window side was approximately correct before the correction, what happens to it when you apply a global correction designed for the tungsten areas? How would you prevent this?

5. **Propose a Camera Raw alternative.** If you could go back to the RAW development stage (the file is a Smart Object), describe the ACR adjustments you would make instead of correcting in Photoshop. Would you use a global white balance change, local adjustments, or both? What advantage does the ACR approach have over the Photoshop approach for this specific image?

This challenge requires you to integrate knowledge of color casts, complementary colors, per-channel Curves, neutral reference evaluation, mixed-lighting diagnostics, masked corrections, the Info panel, and the relationship between Camera Raw and Photoshop color correction — all applied analytically to a realistic scenario before touching the software.
