---
title: "Chapter 21: Color Management for Output"
subtitle: "Soft Proofing, Rendering Intents, and Print Preparation"
topic: Color Management for Output
tags:
  - photoshop
  - color-management
  - soft-proofing
  - printing
  - ICC-profiles
  - rendering-intents
---

# Chapter 21: Color Management for Output

## Learning Objectives

After completing this chapter, you will be able to:

- Explain why screen colors differ from print colors and how ICC-based color management bridges the gap.
- Configure Proof Setup for any target output device and evaluate images using soft proofing.
- Distinguish the four rendering intents and select the appropriate one for a given image and output condition.
- Identify out-of-gamut colors using gamut warnings and make informed decisions about how to handle them.
- Configure the Photoshop Print dialog correctly, choosing between "Let Photoshop Manage Colors" and "Let Printer Manage Colors" without double-profiling.
- Execute a complete print preparation workflow from working space to final output.
- Prepare a file for fine art inkjet printing with confidence in color accuracy.

---

## Conceptual Foundation

In Chapter 02, you learned that color spaces are mathematical models describing ranges of color. In Chapters 07-11, you manipulated color with precision inside your working space. But there is a fundamental problem that no amount of editing skill can solve by itself: **the colors you see on your monitor are not the colors that will appear on paper.**

Your monitor produces color by emitting light through RGB phosphors or LEDs. Paper produces color by absorbing light through ink or pigment on a reflective surface. These are physically different phenomena. A backlit display can produce vivid cyans and electric blues that no inkjet paper can reproduce. Conversely, certain deep magentas and rich earth tones achievable in pigment have no exact counterpart on a typical display. The ranges of reproducible color --- the gamuts --- are different shapes, and neither fully contains the other.

Color management does not eliminate this gap. No technology can make paper glow like a screen. What color management does is **predict** and **control** the translation. It tells you, before you print, what the result will look like. It gives you the tools to decide how colors outside the printable range should be handled. And when you do print, it ensures the translation is executed with maximum accuracy.

This chapter teaches the output side of the color management pipeline: from the moment you finish editing to the moment ink meets paper.

---

## Terminology

### Soft Proofing

**Definition.** Soft proofing is the process of simulating the appearance of a printed output on a calibrated monitor. Photoshop applies the output profile (printer + paper combination) to the display, showing you an approximation of how colors will shift, compress, or clip when reproduced on the target medium.

**Context.** Soft proofing exists because test prints are expensive, time-consuming, and wasteful. Before color management, photographers made multiple test prints, adjusting by trial and error. Soft proofing replaces most of that iteration with an on-screen preview. It is not perfect --- a monitor cannot fully simulate the tactile quality of paper, the influence of viewing light, or the metamerism of certain ink sets --- but it is accurate enough to prevent costly surprises.

**How Photoshop uses it.** View > Proof Colors (Ctrl+Y / Cmd+Y) toggles soft proofing on and off. When active, Photoshop applies the profile selected in View > Proof Setup > Custom to the display pipeline. The image on screen shifts to simulate the output. The title bar appends the proof profile name. All editing operations continue to work on the actual document data, not the proofed preview.

**Relationship to other concepts.** Soft proofing depends on three profiles working together: the document profile (your working space), the output profile (the printer/paper ICC profile), and the monitor profile (set at the OS level by your calibration device). If any of these is inaccurate, the soft proof is unreliable. Soft proofing is the on-screen counterpart to hard proofing (making a physical test print).

**Practical significance.** A calibrated monitor is a prerequisite, not a luxury. Soft proofing on an uncalibrated display is worse than useless --- it gives you false confidence. With a properly calibrated system, soft proofing lets you evaluate and adjust images for a specific output before committing to paper.

---

### Rendering Intent

**Definition.** A rendering intent is a strategy for mapping colors from one gamut to another when the source gamut contains colors that the destination gamut cannot reproduce. The ICC specification defines four rendering intents: Perceptual, Relative Colorimetric, Absolute Colorimetric, and Saturation.

**Context.** The problem is straightforward: your working space (e.g., Adobe RGB) contains colors that your printer cannot print. Something must happen to those unprintable colors. The rendering intent determines *what* happens. Different intents make different trade-offs between accuracy and visual pleasantness.

**How Photoshop uses it.** Rendering intent appears in three locations: the Proof Setup dialog (for soft proofing), the Convert to Profile dialog (Edit > Convert to Profile), and the Print dialog. In each case, you select one of the four intents from a dropdown. The choice affects how the color management engine (CMM) maps source colors to destination colors.

**The four intents explained:**

| Rendering Intent | Strategy | Best For |
|---|---|---|
| **Perceptual** | Compresses the entire source gamut to fit within the destination gamut. All colors shift, but the *relationships* between colors are preserved. If Color A was slightly more saturated than Color B in the source, it remains slightly more saturated in the output. | Images with significant out-of-gamut content --- vivid sunsets, neon signs, saturated landscapes. The compression keeps transitions smooth. |
| **Relative Colorimetric** | Colors that exist in both gamuts are reproduced exactly. Colors outside the destination gamut are clipped to the nearest reproducible color. The source white point is mapped to the destination white point. | Images where most colors are in-gamut and you want maximum accuracy for those colors. Portraits, neutral tones, subtly colored subjects. This is the default in most Photoshop operations. |
| **Absolute Colorimetric** | Same as Relative Colorimetric, but the source white point is *not* mapped to the destination white point. If the source white is D50 and the paper white is slightly warm, the output preserves the D50 appearance by leaving un-inked areas to represent a white that may differ from the paper's native white. | Proofing: simulating one printer/paper combination on another. Rarely used for final output. |
| **Saturation** | Maximizes saturation, sacrificing accuracy. Maps the most saturated colors in the source to the most saturated colors in the destination. | Business graphics, charts, presentation slides. Almost never appropriate for photography. |

**Relationship to other concepts.** The rendering intent interacts with the output profile. A high-quality ICC profile contains tables for at least the Perceptual and Relative Colorimetric intents. Some profiles (particularly those from paper manufacturers) have well-tuned Perceptual tables; others have minimal Perceptual support that may produce flat results. When in doubt, compare both intents using soft proofing before committing.

**Practical significance.** RECOMMENDATION: For most photographic printing, start with Relative Colorimetric. Switch to Perceptual only when the image contains significant out-of-gamut areas where smooth gradations matter more than exact color matching. Use the soft proof toggle (Ctrl+Y / Cmd+Y) to compare. Never use Saturation for photography.

---

### Gamut Warning / Gamut Mapping

**Definition.** A gamut warning is a visual overlay that highlights pixels whose colors fall outside the reproducible range of the target output profile. Gamut mapping is the broader process by which the color management engine remaps those out-of-gamut colors to printable equivalents according to the selected rendering intent.

**Context.** Every output device has a fixed gamut boundary. Colors beyond that boundary simply cannot be reproduced. The gamut warning shows you *where* those problem areas are. Gamut mapping is *what happens* to those colors during conversion --- they are either compressed (Perceptual) or clipped (Relative Colorimetric).

**How Photoshop uses it.** View > Gamut Warning (Ctrl+Shift+Y / Cmd+Shift+Y) paints a solid overlay color (default: neutral gray) over any pixel that cannot be reproduced by the currently selected proof profile. The warning color is configurable in Preferences > Transparency & Gamut. The gamut warning works in conjunction with soft proofing --- you should have a proof profile selected (View > Proof Setup) for the warning to be meaningful.

**Relationship to other concepts.** The gamut warning tells you *where* clipping will occur; the rendering intent determines *how* clipping is handled. The warning uses the proof profile, not the document profile. If no proof profile is configured, the warning defaults to the CMYK working space from Color Settings, which is unlikely to match your actual output.

**Practical significance.** INTERPRETATION: The gamut warning is a diagnostic tool, not a panic button. Large areas of warning in a sunset sky mean you should compare Perceptual vs. Relative Colorimetric soft proofs carefully. Small specular highlights showing as out-of-gamut are rarely a concern. Use the warning to identify *where* to look, then evaluate *how much* the soft proof deviates from your working view.

---

### Proof Colors (View > Proof Colors)

**Definition.** Proof Colors is the toggle command (View > Proof Colors, shortcut Ctrl+Y / Cmd+Y) that activates or deactivates the soft proof simulation on the current document. When enabled, the display pipeline applies the profile and rendering intent specified in the current Proof Setup configuration.

**Context.** Proof Colors is the switch; Proof Setup (see below) is the configuration. Toggling Proof Colors does not alter the document data. It changes only the display rendering. This distinction is essential: you are still editing in your working space. The soft proof overlay shows you a prediction of the output, not the output itself.

**How Photoshop uses it.** When Proof Colors is active, the document title bar shows the proof profile name in parentheses. The image appearance shifts --- typically becoming slightly less saturated, with compressed shadows and a warmer or cooler cast depending on the simulated paper white. You can edit while Proof Colors is active: adjustments you make affect the underlying data, and the soft proof updates in real time.

**Relationship to other concepts.** Proof Colors depends on Proof Setup for its configuration. It interacts with Gamut Warning (both can be active simultaneously). It requires an accurate monitor profile to produce meaningful results.

**Practical significance.** RECOMMENDATION: Develop the habit of toggling Proof Colors on and off while evaluating an image for print. The toggle shows you the *difference* between your working space appearance and the predicted output. That difference is what you need to understand and, if necessary, compensate for.

---

### Proof Setup (View > Proof Setup > Custom)

**Definition.** Proof Setup is the configuration dialog that specifies which output profile, rendering intent, and simulation options to use when Proof Colors is active. Accessed via View > Proof Setup > Custom, it defines the target output condition for soft proofing.

**Context.** Photoshop ships with several preset proof setups (Working CMYK, various plate simulations), but for photographic printing, you will almost always use Custom. The Custom dialog lets you specify the exact ICC profile for your printer and paper combination.

**How Photoshop uses it.** The Custom Proof Condition dialog contains the following controls:

| Control | Function |
|---|---|
| **Device to Simulate** | Select the ICC profile for the target printer+paper. |
| **Preserve RGB Numbers / Preserve CMYK Numbers** | When checked, bypasses conversion and shows what would happen if the raw numbers were sent to the output device. Useful for diagnosing no-conversion scenarios. Usually left unchecked for photography. |
| **Rendering Intent** | Selects the rendering intent for the soft proof simulation. |
| **Black Point Compensation** | Maps the source black point to the destination black point, preserving shadow detail. |
| **Simulate Paper Color** | Simulates the white point of the paper (see dedicated entry below). |
| **Simulate Black Ink** | Simulates the darkest black the printer can produce (see dedicated entry below). |

**Relationship to other concepts.** Proof Setup feeds into Proof Colors and Gamut Warning. The profile you select here should match the profile you will use in the Print dialog or Convert to Profile dialog. Inconsistency between these produces misleading previews.

**Practical significance.** RECOMMENDATION: Save custom proof setups for your most-used printer/paper combinations. After configuring, the setup appears at the bottom of the View > Proof Setup submenu for quick access. Name them clearly: "Epson P900 - Hot Press Bright - Relative" is better than "Custom 1."

---

### "Simulate Paper Color" / "Simulate Black Ink"

**Definition.** These are two checkboxes in the Proof Setup > Custom dialog that refine the soft proof simulation. "Simulate Paper Color" adjusts the display to show the paper's actual white point instead of the monitor's native white. "Simulate Black Ink" adjusts the display to show the darkest black the printer/paper combination can achieve instead of the monitor's native black.

**Context.** Paper is not pure white. Even premium fine art paper has a warm or cool cast and is significantly less bright than a backlit display. Similarly, the darkest black achievable on paper is far less dense than the black a monitor can display. These two checkboxes attempt to simulate those physical realities on screen.

**How Photoshop uses it.** Checking "Simulate Paper Color" automatically enables "Simulate Black Ink" (you cannot simulate the paper without also simulating the ink). The display shifts dramatically: the image appears duller, lower in contrast, and often with a visible color cast. FACT: This shift is jarring the first time you see it. The image looks "worse" than expected. This is normal. The simulation is *more accurate*, not less --- it is showing you what the paper will actually do to your image.

**Relationship to other concepts.** These options only affect the soft proof display. They do not alter document data. They work in conjunction with the output profile selected in Device to Simulate. Their accuracy depends on the quality of the ICC profile (some profiles have better media white point data than others).

**Practical significance.** INTERPRETATION: "Simulate Paper Color" provides the most honest preview of printed output. However, many photographers find it psychologically difficult to edit while it is active because the screen looks flat and lifeless. A practical approach: do your primary editing with "Simulate Paper Color" off, then toggle it on at the end to evaluate overall impression and make final adjustments. If the paper simulation looks unacceptably flat, the issue is usually the paper choice, not the image.

---

### "Let Photoshop Manage Colors" vs "Let Printer Manage Colors"

**Definition.** These are the two primary options in the Color Management section of Photoshop's Print dialog. They determine *who* performs the conversion from the document's color space to the printer's color space.

**Context.** Color conversion from working space to printer space must happen exactly once. If it happens zero times, colors are wrong. If it happens twice (double profiling), colors are dramatically wrong --- typically oversaturated, with heavy color casts. These two options control where the single conversion occurs.

**How Photoshop uses it.**

| Option | What Photoshop Does | What the Printer Driver Must Do |
|---|---|---|
| **Let Photoshop Manage Colors** | Photoshop converts the image from the document profile to the selected printer profile using the specified rendering intent. It sends already-converted data to the printer driver. | The printer driver must have its color management **turned off** (often labeled "No Color Adjustment," "Off (No Color Management)," or "Application Managed Colors" depending on the driver). |
| **Let Printer Manage Colors** | Photoshop sends the image data in its working space (e.g., Adobe RGB or sRGB) with the profile tag embedded. No conversion is performed by Photoshop. | The printer driver performs the conversion using its built-in profiles and algorithms. |

**Relationship to other concepts.** "Let Photoshop Manage Colors" gives you full control: you choose the profile and rendering intent. "Let Printer Manage Colors" relies on the driver's built-in logic, which may or may not use the optimal intent or profile. The critical rule is: conversion must happen in exactly one place.

**Practical significance.** RECOMMENDATION: For fine art and photographic printing, use "Let Photoshop Manage Colors." It gives you explicit control over the profile and rendering intent, and it matches the soft proof you already evaluated. Use "Let Printer Manage Colors" only when you lack a proper ICC profile for your paper or when using a commercial lab that requires unmanaged files. FACT: Double profiling (both Photoshop and the driver converting) is the single most common cause of poor color in photographic prints.

---

### Output Profile (Printer + Paper ICC Profile)

**Definition.** An output profile is an ICC profile that describes the color behavior of a specific printer, ink set, and paper combination under specific printing conditions (resolution, ink limit, etc.). It maps the device's reproducible colors into the Profile Connection Space (PCS) and back.

**Context.** Unlike monitor profiles (which describe a single device) or working space profiles (which are idealized mathematical constructs), output profiles describe a *system*: the interaction of printer, ink, and substrate. Changing any one component --- switching from Photo Black to Matte Black, changing paper, even changing print resolution --- invalidates the profile.

**How Photoshop uses it.** Output profiles appear in three places: the Proof Setup dialog (Device to Simulate), the Print dialog (Printer Profile), and the Convert to Profile dialog (Destination Space). In each case, Photoshop uses the profile's lookup tables to transform color values between the document space and the output space.

**Relationship to other concepts.** Output profiles are distinct from monitor profiles (which describe your display) and document profiles (which describe your editing space). The quality of an output profile directly determines the accuracy of soft proofing and the fidelity of printed output. Paper manufacturers (Hahnemuhle, Canson, Ilford, Epson) provide ICC profiles for their papers on specific printers. Custom profiles, made with a spectrophotometer and profiling software, are more accurate.

**Practical significance.** RECOMMENDATION: Download and install the manufacturer's ICC profiles for your printer/paper combinations. They are free and specific to your equipment. For critical work, invest in custom profiles. Always verify that you are using the correct profile for the exact paper, ink configuration (Photo Black vs. Matte Black), and print settings (quality level, resolution) you intend to use.

---

### Black Point Compensation

**Definition.** Black Point Compensation (BPC) is an option in Photoshop's color conversion engine that maps the darkest black of the source profile to the darkest black of the destination profile, rather than clipping shadow values. It stretches the tonal range to use the full dynamic range of the output device.

**Context.** Different devices have different black points. A monitor can display a much denser black than paper can reflect. Without BPC, shadow values darker than the paper's maximum black are clipped to a single value, crushing shadow detail. With BPC, those values are smoothly compressed to fit the output's available range.

**How Photoshop uses it.** BPC appears as a checkbox in the Proof Setup dialog, the Convert to Profile dialog, and the Print dialog. FACT: BPC is only relevant when the rendering intent is Relative Colorimetric or Absolute Colorimetric. The Perceptual intent has its own built-in gamut compression that typically handles the black point mapping internally.

**Relationship to other concepts.** BPC interacts with rendering intent. When using Relative Colorimetric (the default for most conversions), BPC should almost always be enabled. Without it, shadow detail is lost to clipping. BPC is conceptually similar to what the Perceptual intent does for the entire gamut, but applied only to the black end of the tonal range.

**Practical significance.** RECOMMENDATION: Leave Black Point Compensation enabled for all Relative Colorimetric conversions to print. The only scenario where you might disable it is Absolute Colorimetric proofing, where you want to simulate the exact output of another device including its black density.

---

### Convert to Profile (Edit > Convert to Profile)

**Definition.** Convert to Profile is a Photoshop command (Edit > Convert to Profile) that transforms the document's pixel data from the current color space to a different color space using ICC profiles and a specified rendering intent. Unlike Assign Profile (which reinterprets the numbers without changing them), Convert to Profile recalculates every pixel value to preserve color appearance.

**Context.** In a print preparation workflow, Convert to Profile can be used to permanently transform an image into the output color space before saving or handing off to a print service. This is distinct from the Print dialog's on-the-fly conversion, which converts at print time without altering the document.

**How Photoshop uses it.** The dialog provides: Source Space (current document profile, read-only), Destination Space (the target profile), Engine (the CMM --- Adobe ACE is the default and recommended), Intent (the rendering intent), Black Point Compensation, Use Dither (reduces banding in gradients during conversion), and Flatten Image to Preserve Appearance (merges layers if blending mode interactions would change meaning in the new space).

**Relationship to other concepts.** Convert to Profile is the permanent version of what the Print dialog does temporarily. If you print via "Let Photoshop Manage Colors," the conversion happens on the fly and your document remains in its working space. If you use Convert to Profile, the document itself changes to the output space. The latter approach is useful when saving files for a print lab that expects pre-converted data.

**Practical significance.** RECOMMENDATION: For personal printing on your own printer, let the Print dialog handle conversion ("Let Photoshop Manage Colors") --- it is simpler and leaves your master file in its working space. Use Convert to Profile when preparing files for commercial labs, offset press, or archival deliverables where the recipient needs a file already in the output space. Always work on a copy --- never convert your master file's working space permanently.

---

## Theory

### The Profile Connection Space

All ICC-based color management relies on a concept introduced in Chapter 02: the **Profile Connection Space (PCS)**. The PCS is a device-independent color space (CIE Lab or CIE XYZ) that acts as a universal translator. Every ICC profile contains tables that map its device-specific values to and from the PCS. When Photoshop converts color from your document space to an output profile, the pipeline is:

```
Document Space  -->  PCS (Lab/XYZ)  -->  Output Space
```

FACT: Photoshop never converts directly from, say, Adobe RGB to an Epson printer profile. It always passes through the PCS. This two-step conversion is what allows any profile to work with any other profile --- they share a common language.

### Rendering Intents in Depth

The four rendering intents define different strategies for handling the conversion through the PCS. Understanding them requires understanding what happens at the gamut boundary.

**Perceptual.** The Perceptual intent treats the entire source gamut as a unit. It compresses the source gamut to fit inside the destination gamut while preserving the *relative* relationships between all colors. This means:

- In-gamut colors shift slightly (they are compressed along with everything else).
- Out-of-gamut colors are brought inside the gamut smoothly.
- Gradations are preserved --- no hard clipping edges.
- The overall image may look slightly less saturated than a Relative Colorimetric rendering.

INTERPRETATION: Perceptual is the "safe" choice for images with large out-of-gamut regions. It sacrifices absolute accuracy for visual smoothness. The quality of the Perceptual rendering depends heavily on the profile's Perceptual tables. Premium profiles (from paper manufacturers or custom profiling services) tend to have well-crafted Perceptual tables. Generic or auto-generated profiles may have poor Perceptual mappings.

**Relative Colorimetric.** The Relative Colorimetric intent preserves colors that exist in both gamuts and clips out-of-gamut colors to the nearest reproducible equivalent. It maps the source white point to the destination white point (so paper white is treated as "white"). This means:

- In-gamut colors are reproduced with high accuracy.
- Out-of-gamut colors are clipped to the gamut boundary, which can produce visible "flat" areas where distinct source colors collapse to a single output value.
- Transitions near the gamut boundary may show abrupt changes.

INTERPRETATION: Relative Colorimetric is the "accurate" choice for images that are mostly in-gamut. For a portrait shot in controlled lighting with moderate saturation, Relative Colorimetric will produce more faithful colors than Perceptual. The risk is clipping in saturated regions.

**Absolute Colorimetric.** Identical to Relative Colorimetric except that the white point is not remapped. The source white is reproduced as-is, which may leave un-inked paper areas showing the paper's native tint rather than the simulated source white. This intent is used almost exclusively for **proofing** --- simulating one output device on another (e.g., previewing an offset press result on an inkjet proofer).

**Saturation.** Maps the most saturated colors in the source to the most saturated colors in the destination, maximizing vividness at the expense of accuracy. Designed for business graphics where impact matters more than fidelity. Not relevant to photographic work.

### Gamut Mapping Visualized

Imagine the source and destination gamuts as two differently-shaped blobs in 3D color space. The source blob (e.g., Adobe RGB) is larger in some directions (vivid cyans, deep greens). The destination blob (e.g., a matte fine art paper) is smaller but may extend slightly further in certain earth tones.

- **Perceptual** shrinks the entire source blob to fit inside the destination blob, maintaining proportions.
- **Relative Colorimetric** keeps the overlapping region identical and shaves off anything that protrudes beyond the destination boundary.
- The colors that get "shaved off" in Relative Colorimetric are exactly the pixels highlighted by the Gamut Warning overlay.

### The Role of Black Point Compensation

The black point story is often overlooked. Your monitor can display a black with a luminance near 0.1 cd/m2. Matte paper on a typical inkjet printer might have a maximum black density equivalent to 2-3 cd/m2 under normal viewing conditions. That is a significant difference.

Without BPC, values darker than the paper's achievable black are clipped. Detail in deep shadows vanishes. With BPC, the shadow values are smoothly remapped: the source black maps to the destination black, and intermediate shadow values are spread across the available range. The result is preserved shadow separation at the cost of slightly lifted blacks.

RECOMMENDATION: Always enable BPC when using Relative Colorimetric for photographic output. The only exception is Absolute Colorimetric proofing, where you deliberately want to see the destination's actual black level.

---

## Photoshop Implementation

### Soft Proofing Setup

**Step 1: Install the output profile.** Download the ICC profile for your printer/paper combination from the paper or printer manufacturer's website. On macOS, copy the profile to `/Library/ColorSync/Profiles/` or `~/Library/ColorSync/Profiles/`. On Windows, right-click the .icc file and select "Install Profile." Restart Photoshop if it was open during installation.

**Step 2: Configure Proof Setup.** Go to View > Proof Setup > Custom. In the Custom Proof Condition dialog:

| Setting | Value |
|---|---|
| Device to Simulate | Select your printer/paper ICC profile |
| Preserve RGB Numbers | Unchecked |
| Rendering Intent | Start with Relative Colorimetric |
| Black Point Compensation | Checked |
| Simulate Paper Color | Unchecked initially (enable later for evaluation) |
| Simulate Black Ink | Unchecked initially (auto-enables with Simulate Paper Color) |

Click OK. Optionally, check "Save" to store this configuration for future use.

**Step 3: Toggle Proof Colors.** Press Ctrl+Y (Cmd+Y) to activate soft proofing. The title bar shows the proof profile name. Evaluate the image. Press Ctrl+Y again to toggle off and compare.

**Step 4: Check Gamut Warning.** Press Ctrl+Shift+Y (Cmd+Shift+Y) to overlay the gamut warning. Areas painted in the warning color contain unprintable values under the current proof configuration.

**Step 5: Compare rendering intents.** Return to View > Proof Setup > Custom and switch the rendering intent to Perceptual. Toggle Proof Colors on and compare. Decide which intent produces the better result for this specific image.

### The Print Dialog

**File > Print** opens the Photoshop Print dialog. The Color Management panel is where critical decisions are made.

VERSION NOTE: The layout described here reflects Photoshop CC 2024 and later. Earlier versions may have slightly different labeling, but the concepts are identical.

| Section | Setting | Guidance |
|---|---|---|
| Color Handling | Let Photoshop Manage Colors | Select this for maximum control. |
| Printer Profile | [Your printer/paper ICC profile] | Must match the profile you used for soft proofing. |
| Rendering Intent | Relative Colorimetric or Perceptual | Must match your soft proofing evaluation. |
| Black Point Compensation | Checked | Preserve shadow detail. |

After clicking Print (or Print Settings), you are taken to the OS or printer driver dialog. **You must disable the printer driver's color management.** The exact steps vary by manufacturer:

| Printer Brand | Where to Disable Driver Color Management |
|---|---|
| Epson | Printer Settings > Color Management > Off (No Color Management) |
| Canon | Quality & Media > Color/Intensity > Manual > Matching > None |
| HP | Color tab > Application Managed Colors |

FACT: If you cannot find the option to disable driver color management, consult the printer manual. The terminology varies, but every modern photo printer has this option. Failing to disable it results in double profiling.

### "Let Photoshop Manage Colors" vs "Let Printer Manage Colors" --- Decision Flow

```
Do you have an ICC profile for your exact printer + paper + ink configuration?
|
+-- YES --> Use "Let Photoshop Manage Colors"
|           Select the profile, rendering intent, and BPC
|           Disable color management in the printer driver
|
+-- NO  --> Do you trust the printer driver's built-in profiles?
            |
            +-- YES --> Use "Let Printer Manage Colors"
            |           Leave the printer driver's color management enabled
            |           (Photoshop sends unconverted working-space data)
            |
            +-- NO  --> Obtain or create a profile first
```

---

## Professional Workflow

### Complete Print Preparation Sequence

The following workflow assumes you have a calibrated monitor, installed ICC profiles for your output, and a master file in your working space (Adobe RGB or ProPhoto RGB).

**Phase 1: Evaluate**

1. Open the master file. Verify the document profile (shown at the bottom of the document window or in Edit > Assign Profile).
2. Configure Proof Setup (View > Proof Setup > Custom) for your target output.
3. Enable Proof Colors (Ctrl+Y / Cmd+Y). Evaluate the overall impression.
4. Enable Gamut Warning (Ctrl+Shift+Y / Cmd+Shift+Y). Note any large areas of out-of-gamut color.
5. Compare Perceptual vs. Relative Colorimetric rendering intents using Proof Setup. Decide which works better for this image.
6. Enable "Simulate Paper Color" briefly to assess the paper's impact on the image.

**Phase 2: Adjust (if needed)**

7. If the soft proof reveals unacceptable color shifts or clipping, create an adjustment layer to compensate. Work with Proof Colors active so you can see the effect on the simulated output.
8. Typical adjustments: slightly increase saturation in areas that compress too much, open shadows that block up, adjust neutrals that shift under the output profile.
9. RECOMMENDATION: Make these adjustments on a separate layer or in a separate adjustment layer group. Name it after the output condition (e.g., "Adjust for Epson HPB"). This way, your master file retains the original edit and the output-specific compensation as a toggleable layer.

**Phase 3: Size and Sharpen**

10. Flatten a copy of the file (Image > Duplicate, then Image > Flatten).
11. Resize to the target output dimensions at the target resolution (typically 300 PPI for inkjet, 360 PPI for Epson printers at their native resolution).
12. Apply output sharpening appropriate to the medium and size (see Chapter 15). Matte papers require stronger sharpening than glossy papers. Large prints viewed at distance require less sharpening than small prints viewed closely.

**Phase 4: Print**

13. File > Print. Select "Let Photoshop Manage Colors."
14. Select the correct output profile and rendering intent.
15. Enable Black Point Compensation.
16. Verify the print preview in the dialog.
17. Click Print Settings. Disable the printer driver's color management.
18. Set paper type, quality, and other driver settings to match the ICC profile's assumptions.
19. Print.

**Phase 5: Evaluate the print**

20. Allow the print to dry fully before evaluating (inkjet prints shift slightly during drying --- typically 15-30 minutes, up to 24 hours for some pigment inks on fine art paper).
21. Evaluate under controlled, neutral lighting (5000K or 6500K, depending on your standard). Avoid fluorescent or LED lighting with poor color rendering.
22. Compare the print to the soft proof. Note any discrepancies for future profile or workflow refinements.

---

## Step-by-Step Example: Soft Proofing and Preparing a File for Fine Art Inkjet Printing

**Scenario:** You have a landscape photograph edited in Adobe RGB on a calibrated display. You want to print it on Hahnemuhle Photo Rag 308 using an Epson P900 with matte black ink. The target print size is 13x19 inches.

### Step 1: Install the ICC Profile

Download the Hahnemuhle ICC profile for the Epson P900 from the Hahnemuhle website. The profile will be named something like `HFA_EpsonSCP900_PhotoRag308_MK_360.icc`. Install it in the appropriate OS location. Restart Photoshop.

### Step 2: Configure Proof Setup

1. Open your image in Photoshop.
2. Go to View > Proof Setup > Custom.
3. Set **Device to Simulate** to the Hahnemuhle Photo Rag 308 profile.
4. Set **Rendering Intent** to Relative Colorimetric.
5. Check **Black Point Compensation**.
6. Leave **Simulate Paper Color** and **Simulate Black Ink** unchecked for now.
7. Click OK.

### Step 3: Activate Soft Proofing

1. Press Ctrl+Y (Cmd+Y). The title bar now reads something like: `[Landscape.psd @ 66.7% (Adobe RGB / 8#) / HFA Photo Rag 308]`.
2. Evaluate the image. Notice how vivid blues in the sky have desaturated slightly. Bright greens in foliage may have shifted. This is the matte paper's narrower gamut at work.

### Step 4: Check Gamut Warnings

1. Press Ctrl+Shift+Y (Cmd+Shift+Y). Gray overlay appears on the most saturated sky areas and some bright foliage.
2. The gamut warning shows roughly 15% of the image is out of gamut, concentrated in the sky.

### Step 5: Compare Rendering Intents

1. Go to View > Proof Setup > Custom. Change Rendering Intent to Perceptual. Click OK.
2. Evaluate: the sky transitions are smoother, but overall saturation has decreased slightly compared to Relative Colorimetric.
3. Switch back to Relative Colorimetric. Evaluate: the in-gamut areas (rocks, water, earth tones) look more accurate, but the sky shows a subtle banding where saturated blues clip to the gamut boundary.
4. **Decision:** For this image with its large expanse of saturated sky, Perceptual preserves the sky gradation better. Select Perceptual.

### Step 6: Check Paper Simulation

1. Go to View > Proof Setup > Custom. Check **Simulate Paper Color**. Click OK.
2. The image appears noticeably duller and lower in contrast. The whites have a slight warm cast (Photo Rag has a warm-white base).
3. This is accurate. Photo Rag is a warm, matte paper. The print *will* look this way compared to the glowing backlit display.
4. Evaluate whether the warmth complements the image. For this landscape, it does. If it did not, consider a brighter white paper (e.g., Photo Rag Bright White).
5. Uncheck **Simulate Paper Color** for the editing phase.

### Step 7: Create Output-Specific Adjustments

1. With Proof Colors active (Ctrl+Y), create a Curves adjustment layer. Name it "Print Adjust - Photo Rag."
2. Add a slight S-curve to compensate for the contrast loss on matte paper.
3. Add a Hue/Saturation adjustment layer above it. Increase Saturation by +5 to +8 to offset the gamut compression.
4. Toggle Proof Colors off and on to verify the adjustments improve the proofed appearance without over-correcting.
5. Group these layers and name the group "Photo Rag Output Adjust."

### Step 8: Resize and Sharpen

1. Duplicate the file (Image > Duplicate). Name the duplicate `Landscape_PrintReady_PhotoRag_13x19.psd`.
2. Flatten (Image > Flatten).
3. Resize: Image > Image Size. Set Width to 13 inches, Height to 19 inches, Resolution to 360 PPI (Epson's native). Resample using Bicubic Sharper if downsampling, Preserve Details 2.0 if upsampling.
4. Apply output sharpening. For matte paper at 13x19, apply moderate sharpening. Filter > Sharpen > Unsharp Mask with Amount 80-100%, Radius 1.0-1.5, Threshold 0. Or use your preferred output sharpening method (Chapter 15).

### Step 9: Print

1. File > Print.
2. In the Color Management section:
    - Color Handling: **Let Photoshop Manage Colors**
    - Printer Profile: **HFA Photo Rag 308 (the same profile used for soft proofing)**
    - Rendering Intent: **Perceptual** (matching your soft proof evaluation)
    - Black Point Compensation: **Checked**
3. Click Print Settings. In the Epson driver dialog:
    - Media Type: Velvet Fine Art (or as specified in Hahnemuhle's profile instructions)
    - Print Quality: 2880 x 1440 dpi (or as specified)
    - Color Management: **Off (No Color Management)**
    - High Speed: Off (for maximum quality)
4. Print.

### Step 10: Evaluate

1. Allow the print to dry for at least 30 minutes. Matte papers dry faster than glossy, but pigment inks continue to settle.
2. Evaluate under 5000K or 6500K neutral lighting.
3. Compare to the soft proof. If the match is good, your workflow is validated. If there are discrepancies, note them for future reference --- they indicate areas where the profile or monitor calibration could be improved.

---

## Common Mistakes

### Double Profiling

**The mistake:** Setting "Let Photoshop Manage Colors" in the Print dialog but leaving the printer driver's color management enabled. The image is converted twice: once by Photoshop, once by the driver.

**The result:** Oversaturated or undersaturated prints with heavy color casts, often with a distinctive muddy or posterized appearance.

**The fix:** When using "Let Photoshop Manage Colors," always disable the printer driver's color management. When using "Let Printer Manage Colors," leave the driver's color management enabled. Never both. Never neither.

### Wrong Profile Selected

**The mistake:** Soft proofing with one profile but printing with another. Or using a profile for Paper A when printing on Paper B.

**The result:** The print does not match the soft proof. Colors may be wrong in subtle or dramatic ways, depending on how different the papers are.

**The fix:** Verify that the same ICC profile is used in Proof Setup, in the Print dialog, and that it matches the actual paper loaded in the printer. Profiles are specific to printer model, ink configuration (Photo Black vs. Matte Black), and paper.

### Editing the Master in the Output Space

**The mistake:** Converting the master file to the output profile (via Convert to Profile) and then editing in that compressed space.

**The result:** Loss of data. The output profile's gamut is smaller than your working space. Converting compresses or clips data, and that data cannot be recovered. Subsequent edits operate on a reduced dataset.

**The fix:** Always keep your master file in its working space (Adobe RGB or ProPhoto RGB). Make output-specific adjustments on separate layers. Create output-ready copies only when you are ready to print.

### Ignoring the Soft Proof

**The mistake:** Sending an image to print without soft proofing, assuming it will look the same as it does on screen.

**The result:** Surprise. Colors that looked vivid on screen appear muted on paper. Shadows that had detail on screen are blocked up on paper. The photographer blames the printer, the paper, or the profile, when the real issue is unmet expectations.

**The fix:** Soft proof every image before printing. The few minutes it takes can save hours of frustration and sheets of expensive paper.

### Soft Proofing on an Uncalibrated Monitor

**The mistake:** Relying on soft proofing without having calibrated the monitor with a hardware calibration device (colorimeter or spectrophotometer).

**The result:** The soft proof is inaccurate because the monitor's display is inaccurate. Corrections made based on the faulty soft proof make the print worse, not better.

**The fix:** Calibrate your monitor regularly (every 2-4 weeks) with a hardware device. Software-only calibration is insufficient for proofing.

### Sharpening Before Resizing

**The mistake:** Applying output sharpening at the master file's resolution, then resizing the image to the print dimensions.

**The result:** Sharpening artifacts are scaled and become visible in the print, or the sharpening effect is diluted by the resize.

**The fix:** Always resize first, then apply output sharpening. Output sharpening is resolution-specific, size-specific, and medium-specific. It must be the last step before printing.

### Panic-Editing Out-of-Gamut Colors

**The mistake:** Seeing the gamut warning overlay and aggressively desaturating every flagged area to bring it into gamut.

**The result:** An image that is unnecessarily desaturated. The rendering intent (especially Perceptual) handles the gamut mapping more gracefully than manual desaturation.

**The fix:** Use the gamut warning as a diagnostic tool, not a directive. Evaluate the soft proof to see how the rendering intent handles the out-of-gamut colors. Only intervene manually when the automatic mapping produces visually unacceptable results.

---

## Summary

Color management for output is the bridge between the luminous world of the monitor and the physical world of ink on paper. The core pipeline is straightforward: an ICC profile describes the output device's gamut, a rendering intent defines how out-of-gamut colors are handled, and soft proofing lets you preview the result before committing to paper.

The key principles:

- **Soft proof every image** using the correct ICC profile for your printer/paper combination on a calibrated monitor.
- **Choose the rendering intent** based on the image: Relative Colorimetric for mostly in-gamut images, Perceptual for images with significant out-of-gamut content.
- **Enable Black Point Compensation** with Relative Colorimetric to preserve shadow detail.
- **Color conversion must happen exactly once:** either Photoshop converts (and the driver does not) or the driver converts (and Photoshop does not). Never both.
- **Keep your master file in its working space.** Make output-specific adjustments on separate layers. Create flattened, resized, sharpened copies for printing.
- **Evaluate prints under controlled lighting** after drying, and compare to the soft proof.

Mastering this workflow means that when you press Print, you already know what the result will look like. The print becomes a confirmation, not a surprise.

---

## Exercises

### Foundation Exercises

1. **Proof Setup Configuration.** Install an ICC profile for a printer/paper combination you use regularly. Configure View > Proof Setup > Custom with that profile and Relative Colorimetric intent. Toggle Proof Colors on a well-saturated image and describe what changes you observe.

2. **Rendering Intent Comparison.** Open an image with known saturated colors (a sunset, neon sign, or color checker). Soft proof it with Relative Colorimetric, then Perceptual. For each intent, write down: which areas changed more, whether gradations remained smooth, and which intent you would choose for this image and why.

3. **Gamut Warning Exploration.** On the same saturated image, activate Gamut Warning. Estimate what percentage of the image is out of gamut. Now switch the proof profile to a different paper (e.g., from glossy to matte). How does the out-of-gamut area change?

4. **Simulate Paper Color.** Enable "Simulate Paper Color" in Proof Setup for a warm-toned matte paper. Describe the shift in white point and contrast. Now switch to a cool-white glossy paper profile. Compare the two simulations.

5. **Print Dialog Verification.** Open File > Print and configure "Let Photoshop Manage Colors" with your profile. Before printing, open the printer driver settings and locate the option to disable driver color management. Document the exact path (menu, tab, option name) for your printer.

### Advanced Exercises

6. **Output-Specific Adjustment Layers.** Take an image with vivid blues. Soft proof it for matte fine art paper. Create a set of adjustment layers (Curves, Hue/Saturation) that compensate for the gamut compression visible in the soft proof. Group these layers and name them after the output condition. Verify that the proofed appearance improves while the un-proofed (working space) appearance remains acceptable.

7. **Cross-Paper Comparison.** Soft proof the same image for three different papers on the same printer (e.g., glossy photo, semi-gloss, matte fine art). Document the differences in gamut (using Gamut Warning), contrast (using Simulate Paper Color), and overall color impression. Which paper best suits this image?

8. **Convert to Profile vs. Print Dialog.** Print the same image twice: once using "Let Photoshop Manage Colors" in the Print dialog (no pre-conversion), and once by first converting the file with Edit > Convert to Profile to the output profile and then printing with "Let Printer Manage Colors." Compare the results. They should be nearly identical if executed correctly.

9. **Black Point Compensation Test.** Print a grayscale step wedge (21 steps from white to black) twice: once with BPC enabled, once with BPC disabled, both at Relative Colorimetric. Compare the shadow steps. Document at which step the version without BPC loses visible separation.

### Blackbelt Challenge

10. **Complete Output Mastery.** Select a portfolio-quality image with challenging color --- a scene containing both vivid, out-of-gamut colors and subtle, neutral tones that demand accuracy. Prepare it for two different output conditions: a glossy inkjet print and a matte fine art print. For each output:

    - Soft proof with both Perceptual and Relative Colorimetric intents.
    - Document your rendering intent choice and rationale.
    - Create output-specific adjustment layers (in separate named groups).
    - Resize and sharpen appropriately for each medium.
    - Print both versions.
    - Evaluate the prints under controlled lighting.
    - Write a brief technical report comparing: how each paper handled the out-of-gamut colors, whether the soft proof accurately predicted the result, and which paper better serves the image.

    This exercise validates the entire pipeline from soft proof through final evaluation. If the prints match your soft proof predictions and your technical report demonstrates understanding of *why* each paper produced the result it did, you have achieved blackbelt-level mastery of color management for output.
