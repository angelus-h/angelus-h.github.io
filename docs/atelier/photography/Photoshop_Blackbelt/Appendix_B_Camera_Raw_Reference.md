---
title: "Appendix B: Camera Raw Quick Reference"
description: "Panel-by-panel quick reference for Adobe Camera Raw"
---

# Appendix B: Camera Raw Quick Reference

!!! warning "Version Note"
    Adobe Camera Raw panel names, slider ranges, and layout change between versions.
    This reference reflects ACR as of the **2024-2025 era** (ACR 16.x / Photoshop 2024-2025).
    If your interface looks different, check your ACR version under **Help > About Plug-In**.
    Adobe may rename, merge, or reorganize panels in future updates.

This appendix is a **desk reference companion** to Chapter 03 (Camera Raw). It lists panels, sliders, and their behaviors in a compact format for quick lookup during editing. For the underlying concepts, theory, and worked examples, see Ch 03.

---

## Basic Panel

The primary tonal and color adjustment panel. Process all global exposure and color balance work here before moving to other panels.

### White Balance

| Control | Range / Options | What It Does |
|---|---|---|
| WB Preset | As Shot, Auto, Daylight, Cloudy, Shade, Tungsten, Fluorescent, Flash, Custom | Applies a predefined white balance correction |
| Temperature | 2000 - 50000 (Kelvin scale) | Shifts color along the blue-yellow axis |
| Tint | -150 to +150 | Shifts color along the green-magenta axis |

**Tip:** For raw files, Temperature is in actual Kelvin. For JPEGs/TIFFs, it is a relative adjustment and the numeric scale differs.

### Tone

| Slider | Range | What It Does |
|---|---|---|
| Exposure | -5.00 to +5.00 | Overall brightness. Roughly equivalent to f-stops. Primarily affects midtones. |
| Contrast | -100 to +100 | Expands or compresses the tonal range around the midpoint. Positive values darken shadows and brighten highlights simultaneously. |
| Highlights | -100 to +100 | Recovers or pushes the upper tonal range (roughly the top 1-2 stops). Negative values recover blown highlight detail. |
| Shadows | -100 to +100 | Opens up or crushes the lower tonal range (roughly the bottom 1-2 stops). Positive values lift shadow detail. |
| Whites | -100 to +100 | Sets the white clipping point. Adjusts the very top of the histogram. |
| Blacks | -100 to +100 | Sets the black clipping point. Adjusts the very bottom of the histogram. |

**Tip:** Hold Alt/Option while dragging Whites or Blacks to see a clipping preview overlay. Areas that turn colored are clipping in that channel.

**Tip:** The order of operations matters. Set Exposure first (overall brightness), then Highlights/Shadows (recover extremes), then Whites/Blacks (set clipping points), then Contrast last.

### Presence

| Slider | Range | What It Does |
|---|---|---|
| Texture | -100 to +100 | Enhances or smooths medium-frequency detail (skin texture, foliage, fabric). Less aggressive than Clarity. Positive sharpens texture; negative smooths it. |
| Clarity | -100 to +100 | Midtone contrast enhancement. Increases local contrast at edges. Positive values add punch and structure; negative creates a soft glow. |
| Dehaze | -100 to +100 | Removes or adds atmospheric haze. Positive values cut through haze and fog; negative adds a haze effect. Affects both contrast and saturation. |
| Vibrance | -100 to +100 | Saturation boost that preferentially targets less-saturated colors and protects skin tones from over-saturation. |
| Saturation | -100 to +100 | Linear, equal saturation adjustment across all colors. -100 produces a grayscale image. |

**Tip:** Texture targets a narrower frequency band than Clarity. For portraits, try negative Texture (smooths skin) with positive Clarity (keeps structure in eyes, hair). For landscapes, stack both positive for maximum detail.

**Tip:** Dehaze can shift color balance. Re-check Temperature/Tint after heavy Dehaze adjustments.

---

## Tone Curve Panel

Provides two curve modes for precise tonal control beyond the Basic panel sliders.

### Parametric Curve

A guided curve with four region sliders. Cannot create arbitrary points.

| Slider | Range | What It Adjusts |
|---|---|---|
| Highlights | -100 to +100 | Upper quarter of tonal range |
| Lights | -100 to +100 | Upper-mid tonal range |
| Darks | -100 to +100 | Lower-mid tonal range |
| Shadows | -100 to +100 | Lower quarter of tonal range |

The region split points (the three sliders along the bottom of the curve) can be dragged to redefine where each region begins and ends.

### Point Curve

A freeform curve where you click to add and drag control points. Supports per-channel editing.

| Feature | Description |
|---|---|
| Channel selector | All (RGB composite), Red, Green, or Blue individually |
| Preset dropdown | Linear, Medium Contrast, Strong Contrast (starting points) |
| Point editing | Click on curve to add a point, drag to adjust. Right-click a point to delete it. |
| Input/Output fields | Numeric readout of selected point's position (0-255 scale) |

**Tip:** The Point Curve with Linear preset is a flat, neutral starting point. The Parametric curve and Point Curve are additive -- they stack on top of each other.

**Tip:** For per-channel color grading in the Point Curve, work with the individual R, G, B channels. Lifting the Blue channel in shadows while lowering it in highlights creates the classic teal-and-orange split.

---

## Detail Panel

Sharpening and noise reduction. Always evaluate at 100% zoom or higher.

### Sharpening

| Slider | Range | Default | What It Does |
|---|---|---|---|
| Amount | 0 - 150 | 40 | Strength of sharpening. 0 = no sharpening. |
| Radius | 0.5 - 3.0 | 1.0 | Size of the sharpening halo in pixels. Lower for fine detail, higher for coarser subjects. |
| Detail | 0 - 100 | 25 | Controls how much fine detail is sharpened. Higher values sharpen fine textures (and potentially noise). Lower values sharpen only prominent edges. |
| Masking | 0 - 100 | 0 | Restricts sharpening to edges. 0 = sharpens everything uniformly. 100 = sharpens only the strongest edges. |

**Tip:** Hold Alt/Option while dragging Masking to see a grayscale edge mask. White areas will be sharpened; black areas will not. This is essential for avoiding noise amplification in smooth areas like skies.

**Tip:** Hold Alt/Option while dragging Amount, Radius, or Detail to see a grayscale preview of the sharpening effect.

### Noise Reduction

#### Luminance Noise

| Slider | Range | Default | What It Does |
|---|---|---|---|
| Luminance | 0 - 100 | 0 | Strength of luminance noise reduction. Higher values = smoother but may lose detail. |
| Detail | 0 - 100 | 50 | Controls the luminance detail threshold. Higher preserves more detail but may leave some noise. Lower gives smoother result. |
| Contrast | 0 - 100 | 0 | Preserves luminance contrast. Higher values preserve contrast and texture but may produce mottled results. |

#### Color Noise

| Slider | Range | Default | What It Does |
|---|---|---|---|
| Color | 0 - 100 | 25 | Strength of color noise reduction. Removes colored speckles. |
| Detail | 0 - 100 | 50 | Controls color detail threshold. Lower values remove more color noise but may bleed color at edges. |
| Smoothness | 0 - 100 | 50 | Smooths out large-scale color mottling artifacts. |

**Tip:** Color noise reduction at 25 is the default for raw files and is usually sufficient. Push Luminance noise reduction only as far as needed -- it trades detail for smoothness.

**Tip:** If you see color blotches or mottling in smooth areas after Luminance NR, increase Smoothness.

---

## Color Mixer / HSL Panel

Per-color adjustment of Hue, Saturation, and Luminance. Targets eight color ranges independently.

### Color Ranges

Reds, Oranges, Yellows, Greens, Aquas, Blues, Purples, Magentas

### Adjustment Axes

| Axis | Range | What It Does |
|---|---|---|
| Hue | -100 to +100 | Shifts the color toward its neighboring hues on the color wheel |
| Saturation | -100 to +100 | Increases or decreases saturation of that specific color range |
| Luminance | -100 to +100 | Brightens or darkens that specific color range |

**Tip:** The Targeted Adjustment Tool (TAT) allows you to click on a color in the image and drag up/down to adjust it directly. This often affects multiple color ranges simultaneously because real-world colors span multiple sliders.

**Tip:** Desaturating specific color ranges (e.g., reducing Oranges saturation in portraits) can be more nuanced than global Vibrance/Saturation adjustments.

**Tip:** The Luminance axis is powerful for B&W conversion control (see Ch 18). Shifting luminance per color is equivalent to using colored filters in traditional B&W photography.

---

## Color Grading Panel

Split-toning with three-way color wheels for shadows, midtones, and highlights, plus a global wheel. Replaced the older Split Toning panel.

### Color Wheels

| Wheel | What It Targets |
|---|---|
| Shadows | Tones in the lower tonal range |
| Midtones | Tones in the middle tonal range |
| Highlights | Tones in the upper tonal range |
| Global | Applies a uniform color cast across the entire tonal range |

Each wheel has:

| Control | Range | What It Does |
|---|---|---|
| Hue | 0 - 360 (degrees) | The color to apply, selected by angle on the wheel |
| Saturation | 0 - 100 | Intensity of the color grading. Controlled by distance from center of the wheel. |
| Luminance | -100 to +100 | Brightens or darkens the tones in that range (slider below each wheel) |

### Blending and Balance

| Slider | Range | What It Does |
|---|---|---|
| Blending | 0 - 100 | Controls how much the shadow and highlight toning overlap in the midtones. 0 = hard split, 100 = smooth overlap. |
| Balance | -100 to +100 | Shifts the boundary between shadow and highlight toning. Negative = more shadow toning, Positive = more highlight toning. |

**Tip:** Blending at 50 (default) produces a natural transition. Set to 0 for a harder, more graphic split-tone effect.

**Tip:** Color Grading works best with subtle adjustments. High saturation values on the wheels produce unnatural results quickly.

---

## Optics Panel

Lens correction for chromatic aberration, distortion, and vignetting.

### Automated Corrections

| Control | What It Does |
|---|---|
| Remove Chromatic Aberration (checkbox) | Automatically corrects purple/green fringing at high-contrast edges. Works well in most cases. |
| Use Lens Profile (checkbox) | Applies a lens-specific distortion and vignetting correction based on EXIF metadata. |

### Lens Profile

When Use Lens Profile is enabled:

| Control | What It Does |
|---|---|
| Make / Model / Profile | Auto-detected from EXIF or manually selectable |
| Distortion | 0 - 200 (slider, default 100 = full correction) | Adjusts how much of the profile's distortion correction is applied |
| Vignetting | 0 - 200 (slider, default 100 = full correction) | Adjusts how much of the profile's vignetting correction is applied |

### Manual Chromatic Aberration (Defringe)

| Slider | Range | What It Does |
|---|---|---|
| Purple Amount | 0 - 20 | Strength of purple fringe removal |
| Purple Hue | Range selector (two handles) | Defines which hues count as "purple fringe" |
| Green Amount | 0 - 20 | Strength of green fringe removal |
| Green Hue | Range selector (two handles) | Defines which hues count as "green fringe" |

**Tip:** Enable Remove Chromatic Aberration on virtually every image. There is almost no downside.

**Tip:** If the automatic defringe misses stubborn fringing, use the manual Defringe sliders to target the specific hue range.

---

## Geometry Panel

Perspective and geometric correction.

### Upright Modes

| Mode | What It Does |
|---|---|
| Auto | Balanced correction of both vertical and horizontal perspective |
| Level | Corrects horizon tilt only |
| Vertical | Corrects vertical converging lines (keystoning) |
| Full | Aggressive correction of both vertical and horizontal perspective plus rotation |
| Guided | Manual: draw up to four guide lines on the image; ACR aligns to them |

### Transform Sliders

| Slider | Range | What It Does |
|---|---|---|
| Vertical | -100 to +100 | Corrects vertical perspective (tilting forward/backward) |
| Horizontal | -100 to +100 | Corrects horizontal perspective (tilting left/right) |
| Rotate | -10.00 to +10.00 | Rotates the image |
| Aspect | -100 to +100 | Corrects aspect ratio distortion introduced by perspective correction |
| Scale | 1 - 200 | Scales the corrected image to fill or reduce the frame |
| X Offset | -100 to +100 | Shifts the image horizontally within the frame |
| Y Offset | -100 to +100 | Shifts the image vertically within the frame |

**Tip:** Constrain Crop (checkbox) automatically crops to remove empty edges after correction.

**Tip:** Guided mode is the most precise for architecture. Draw two vertical guide lines along building edges and two horizontal guide lines along floors or ceilings.

---

## Effects Panel

Creative effects applied after cropping.

### Post-Crop Vignetting

| Slider | Range | What It Does |
|---|---|---|
| Amount | -100 to +100 | Negative = darken edges, Positive = lighten edges |
| Midpoint | 0 - 100 | How far the vignette extends toward the center. Lower = larger vignette. |
| Roundness | -100 to +100 | Shape of the vignette. Negative = rectangular, Positive = circular. |
| Feather | 0 - 100 | Softness of the transition between vignetted and non-vignetted areas. |
| Highlights | 0 - 100 | (Only for negative Amount) Protects highlights from being darkened. Higher = more highlight protection. |
| Style | Highlight Priority, Color Priority, Paint Overlay | Controls how the darkening interacts with colors. Highlight Priority preserves highlight contrast. Color Priority preserves color saturation. Paint Overlay is a simple overlay blend. |

### Grain

| Slider | Range | What It Does |
|---|---|---|
| Amount | 0 - 100 | Intensity of the grain effect. 0 = no grain. |
| Size | 0 - 100 | Grain particle size. Higher = coarser grain. |
| Roughness | 0 - 100 | Irregularity of the grain pattern. Higher = more variation in grain size. |

**Tip:** Grain can unify composite images that were shot at different ISOs. A consistent grain overlay masks the different noise signatures.

**Tip:** For the vignette, Highlight Priority style generally produces the most natural-looking result for darkening edges.

---

## Calibration Panel

Low-level color adjustments that operate below the profile level. Rarely needed for routine work.

### Process Version

| Control | What It Does |
|---|---|
| Process | Selects the Camera Raw processing engine version. Current default for new images: Version 5. Older images may use Version 1-4. Changing this affects how all sliders behave. |

**Tip:** Changing the Process Version reinterprets all slider values. Do not change this casually on an image you have already edited.

### Shadow Tint

| Slider | Range | What It Does |
|---|---|---|
| Shadow Tint | -100 to +100 | Corrects green or magenta tint specifically in shadow regions. Useful for fixing sensor-specific shadow color casts. |

### Primary Adjustments

Per-channel hue and saturation shifts at the sensor level.

| Slider | Range | What It Does |
|---|---|---|
| Red Primary Hue | -100 to +100 | Shifts the hue of the red channel response |
| Red Primary Saturation | -100 to +100 | Adjusts saturation of the red channel response |
| Green Primary Hue | -100 to +100 | Shifts the hue of the green channel response |
| Green Primary Saturation | -100 to +100 | Adjusts saturation of the green channel response |
| Blue Primary Hue | -100 to +100 | Shifts the hue of the blue channel response |
| Blue Primary Saturation | -100 to +100 | Adjusts saturation of the blue channel response |

**Tip:** The Calibration panel is not the same as the Color Mixer/HSL panel. Calibration adjusts the raw sensor channel interpretation, while HSL targets perceptual color ranges. Calibration changes are more fundamental and affect how all other adjustments are calculated.

**Tip:** Creative use: shifting Blue Primary Hue toward negative can produce a stylized warm-tone look that is difficult to replicate with other tools. This is a technique some photographers use for signature color styles.

---

## Panels Not Covered Here

The following ACR features are not covered in this appendix because they are interactive tools rather than slider panels, or are covered in depth in their respective chapters:

- **Masking tools** (Linear Gradient, Radial Gradient, Brush, Select Subject, Select Sky, etc.) -- See Ch 08 and Ch 13
- **Crop and Rotate** -- See Ch 03
- **Red Eye / Pet Eye Removal** -- Straightforward point-and-click tools
- **Snapshots and Presets** -- See Ch 06
- **HDR and Panorama Merge** -- See Ch 19

---

## Keyboard Shortcuts (Selected)

| Shortcut | Action |
|---|---|
| Ctrl/Cmd + Z | Undo last adjustment |
| P | Toggle preview (before/after) |
| Alt/Option + drag slider | Clipping preview (for applicable sliders) |
| Double-click slider | Reset slider to default |
| O | Highlight clipping warning toggle |
| U | Shadow clipping warning toggle |
| Ctrl/Cmd + Shift + A | Open ACR from Photoshop (selected layer or file) |

---

*Cross-reference: Ch 03 Camera Raw for worked examples and theory. Ch 15 Sharpening and Ch 16 Noise Reduction for the output-sharpening pipeline that follows ACR.*
