---
description: Pixels, RGB channels, bit depth, dynamic range, histograms, gamma encoding, and tonal range — the foundational concepts underlying every Photoshop operation.
---

# Chapter 01: Digital Image Fundamentals

## Learning Objectives

After completing this chapter, you will be able to:

1. Explain what a pixel stores and how a grid of pixels becomes an image.
2. Describe how the RGB color model represents color through additive mixing and how Photoshop organizes this data into channels.
3. Choose an appropriate bit depth for a Photoshop document and articulate the trade-offs involved.
4. Distinguish between dynamic range (a property of a scene or capture device) and tonal range (a property of an image file).
5. Read a histogram diagnostically — identifying clipping, low contrast, overexposure, and posterization before choosing a correction tool.
6. Explain why images are gamma-encoded rather than stored as linear light data, and predict how this encoding affects tonal edits.
7. Recognize the visible symptoms of posterization and clipping, and explain the data-level causes behind each.

## Conceptual Foundation

Every operation you perform in Photoshop — every Curves adjustment, every mask, every blend mode calculation — is an operation on numbers. The image you see on screen is a reconstruction from those numbers, mediated by color profiles, gamma encoding, and display hardware. If you do not understand what the numbers represent and how they are organized, you are adjusting images by trial and error.

This chapter establishes the vocabulary and mental model you need to understand what Photoshop is actually doing when you edit an image. These are not abstract concepts — they are the direct explanation for why your shadows posterize, why your highlights clip, why 16-bit files survive aggressive edits that destroy 8-bit files, and why the same RGB values produce different colors in different color spaces.

The goal is not to turn you into a computer scientist. The goal is to give you enough understanding of the underlying data that you can predict the consequences of your edits before you make them.

## Terminology

### Pixel

**Definition:** A pixel (from "picture element") is the smallest addressable unit of a raster image. Each pixel stores a set of numerical values that describe color at a single spatial location. A pixel has no inherent physical size.

**Meaning in photographic practice:** When you photograph a scene, your sensor samples the incoming light at millions of discrete locations. Each sample becomes one pixel. The image is a rectangular grid of these samples. The pixel count determines how much spatial detail the image can represent — but pixel count alone says nothing about image quality, sharpness, or print size. Those depend on lens quality, focus accuracy, sensor size, diffraction, and the output medium.

**Photoshop implementation:** In Photoshop, each pixel stores one value per channel. In an RGB image, that means three values per pixel (Red, Green, Blue). Photoshop displays pixel dimensions under Image > Image Size and allows you to inspect individual pixel values using the Info panel (Window > Info) and the Eyedropper tool.

**Related concepts:** Channel, bit depth, resolution, pixel dimensions.

**Common misconception:** "More pixels means better quality." Pixel count determines spatial resolution — the ability to represent fine detail. But image quality also depends on the quality of those samples: noise level, lens resolution, accurate focus, appropriate exposure. A 100-megapixel image from a poor lens may contain less usable detail than a 24-megapixel image from an excellent one.

### RGB Color Model

**Definition:** RGB is an additive color model in which red, green, and blue light are combined in varying intensities to produce a range of colors. When all three are at maximum intensity, the result is white. When all three are at zero, the result is black. The model is called "additive" because adding light increases brightness, unlike subtractive models (such as CMYK) where adding pigment absorbs light and decreases brightness.

**Meaning in photographic practice:** Your camera sensor captures light through a color filter array (typically a Bayer pattern of red, green, and blue filters). Your monitor displays images by emitting red, green, and blue light from sub-pixels. RGB is therefore the native color model of both your capture and your display pipeline. When you edit in RGB, you are working with data in the same domain as your sensor and your screen.

**Photoshop implementation:** RGB is Photoshop's default and most-used color mode (Image > Mode > RGB Color). In RGB mode, each pixel has three channel values. Photoshop also supports CMYK, Lab, Grayscale, and other modes, but photographic editing is overwhelmingly done in RGB. The specific meaning of RGB values depends on the color space assigned to the document (sRGB, Adobe RGB, ProPhoto RGB, etc.) — the same numerical triplet (180, 120, 90) describes a different actual color in each space. Color spaces are covered in Chapter 02.

**Related concepts:** Channel, additive color, color space (Chapter 02), CMYK.

**Common misconception:** "RGB values define an absolute color." They do not. RGB values are meaningless without a color space to interpret them. The triplet (200, 100, 50) is a different color in sRGB than it is in ProPhoto RGB. The color space provides the mapping from numbers to actual colors.

### Channel (in Photoshop Context)

**Definition:** A channel is a single grayscale component of an image. In an RGB image, there are three color channels — Red, Green, and Blue — each storing an intensity value for that color component at every pixel. Photoshop also supports alpha channels (used for selections and masks) and spot color channels (used in print production).

**Meaning in photographic practice:** Understanding channels lets you analyze and manipulate color with precision. When you see a color cast in the shadows, that is typically an imbalance in one or two channels. When you adjust Curves on a per-channel basis, you are directly modifying one channel's values while leaving the others unchanged. When you build a luminosity mask from a channel, you are using the tonal information in that single channel as a selection source.

**Photoshop implementation:** The Channels panel (Window > Channels) displays each channel as a grayscale image. Brighter areas in the Red channel mean more red light at that location. You can view individual channels by clicking on them in the Channels panel, or use the keyboard shortcuts Ctrl/Cmd+3 (Red), Ctrl/Cmd+4 (Green), Ctrl/Cmd+5 (Blue) to view them individually, and Ctrl/Cmd+2 to return to the composite RGB view. Alpha channels appear below the color channels in the same panel.

**Related concepts:** RGB color model, alpha channel, masks (Chapter 05), per-channel Curves (Chapter 07).

**Common misconception:** "The Red channel shows what's red in the image." More precisely, the Red channel shows where the image has high red channel values. A bright area in the Red channel means that location has a strong red component, but the perceived color also depends on what the Green and Blue channels contain at that location. Pure white, for instance, has maximum values in all three channels — so white areas appear bright in the Red channel, even though they do not look "red."

### Bit Depth

**Definition:** Bit depth (also called color depth or precision) specifies how many bits are used to store each channel value per pixel. Higher bit depth means more possible values per channel, which means finer gradations between tones.

| Bit Depth | Values per Channel | Total Possible Colors (RGB) | Photoshop Mode Name |
|-----------|-------------------|----------------------------|---------------------|
| 8-bit | 256 (0-255) | ~16.7 million | 8 Bits/Channel |
| 16-bit | 65,536 (0-65535) | ~281 trillion | 16 Bits/Channel |
| 32-bit | Floating point | Effectively unlimited | 32 Bits/Channel |

**Meaning in photographic practice:** Bit depth determines how much editing headroom your image has. When you stretch or compress tonal values — which is what contrast adjustments, Curves, and Levels do — you are remapping values. In an 8-bit image, there are only 256 possible values per channel. Aggressive tonal edits can reduce the number of distinct values actually used, creating gaps in the histogram that manifest as visible posterization (banding in smooth gradients). In a 16-bit image, the same edit operates on 65,536 values per channel, and the gaps created by remapping remain invisible because adjacent values are still close enough to appear smooth.

**Photoshop implementation:** Set via Image > Mode > 8 Bits/Channel, 16 Bits/Channel, or 32 Bits/Channel. Photoshop's 16-bit mode internally uses 15 bits plus one value (range 0-32768), not the full 0-65535 range. This is a Photoshop-specific implementation detail that rarely matters in practice but occasionally appears in technical discussions. 32-bit mode uses floating point values and is primarily used for HDR imaging and compositing; many Photoshop features have limited or altered behavior in 32-bit mode.

**Related concepts:** Posterization, dynamic range, RAW file bit depth, sensor bit depth.

**Common misconception:** "My camera shoots 14-bit RAW, so I should always work in 16-bit in Photoshop." The premise is sound — you should generally convert from RAW to 16-bit to preserve the tonal precision captured by the sensor. But the file's bit depth, the sensor's bit depth, and the display's bit depth are three different things:

- **Sensor bit depth** (typically 12-bit or 14-bit): determines how finely the analog-to-digital converter in the camera samples the signal. This sets the maximum tonal precision of your raw data.
- **File bit depth** (8, 16, or 32-bit in Photoshop): determines the precision of values stored in the working document. A 16-bit file can hold all the precision from a 14-bit sensor, with room to spare for editing.
- **Display bit depth** (typically 8-bit, sometimes 10-bit): determines how many distinct tones your monitor can actually display. Even if your file is 16-bit, most monitors show only 256 levels per channel. The extra precision exists to survive editing, not for display.

### Dynamic Range vs Tonal Range

**Definition:** These two terms are related but describe different things.

**Dynamic range** is the ratio between the brightest and darkest luminance levels that a device can capture or reproduce, typically measured in stops (each stop representing a doubling of light). A scene with both deep shadow and direct sunlight might span 20+ stops. A camera sensor might capture 12-14 stops. A monitor might display 8-10 stops (more for HDR displays).

**Tonal range** refers to the span of tonal values actually present in an image file, from the darkest pixel value to the brightest. An image with values spanning from 0 to 255 (in 8-bit) uses the full tonal range. An image with values only between 80 and 200 uses a limited tonal range — it will appear low in contrast, and its histogram will show gaps at both ends.

**Meaning in photographic practice:** When you expose a scene, you are mapping the scene's dynamic range into the sensor's dynamic range, and then into the file's tonal range. If the scene's dynamic range exceeds the sensor's capability, you lose information at the highlights (clipping to white), the shadows (clipping to black), or both. Understanding this chain — scene dynamic range to sensor capability to file tonal range — is fundamental to exposure decisions and to knowing what data you have to work with in post-processing.

**Photoshop implementation:** Photoshop does not directly display dynamic range as a measurement. You evaluate tonal range by reading the histogram (Window > Histogram). The histogram shows the distribution of values across the available range. Clipped highlights appear as a spike at the right edge; clipped shadows appear as a spike at the left edge. Photoshop's 32-bit mode can store scene-referred data with values above 1.0 (superwhite) for HDR workflows, but standard 8-bit and 16-bit modes represent a fixed tonal range from black (0) to white (maximum value).

**Related concepts:** Histogram, clipping, bit depth, exposure, HDR.

**Common misconception:** "Dynamic range and tonal range are the same thing." They are not. Dynamic range is a property of a scene, sensor, or display — it describes the ratio between the brightest and darkest reproducible levels. Tonal range is a property of the image data — it describes what portion of the available value range is actually occupied. You can have a high-dynamic-range scene captured into a limited tonal range (underexposed, or with aggressive tone mapping), or a low-dynamic-range scene spread across the full tonal range of the file.

### Histogram

**Definition:** A histogram is a graph showing the distribution of pixel values in an image. The horizontal axis represents tonal values from black (left) to white (right). The vertical axis represents the number of pixels at each value. A histogram is a statistical summary of the image data — it tells you about tonal distribution but nothing about spatial arrangement.

**Meaning in photographic practice:** The histogram is your primary diagnostic tool for evaluating exposure and tonal distribution. Before you reach for Levels or Curves, read the histogram. It tells you:

- Whether highlights or shadows are clipped (data piled against the edges)
- Whether the image uses the full tonal range or only a portion of it
- Whether the tonal distribution is biased toward highlights (high-key), shadows (low-key), or centered
- Whether there are gaps that indicate posterization or previous aggressive editing

A histogram is descriptive, not prescriptive. There is no single "correct" histogram shape. A high-key photograph of snow should be right-biased. A low-key portrait should be left-biased. The histogram helps you verify that the tonal distribution matches your photographic intent.

**Photoshop implementation:** Photoshop provides histograms in multiple locations:

- **Histogram panel** (Window > Histogram): dedicated panel showing the histogram of the current state of the image. Can display individual channels, luminosity, or colors. The panel has an "Uncached" refresh button — if you see a yellow triangle warning icon, the displayed histogram is based on a cached (potentially inaccurate) version. Click to refresh.
- **Levels dialog** (Image > Adjustments > Levels, or as an adjustment layer): displays the histogram and lets you set black point, white point, and midtone (gamma) directly.
- **Curves dialog**: displays the histogram as a background reference behind the curve.
- **Camera Raw**: displays a histogram for the RAW data, updated in real time as you adjust sliders.

**Related concepts:** Tonal range, clipping, Levels (Chapter 07), Curves (Chapter 07).

**Common misconception:** "A good histogram should look like a bell curve centered in the middle." This is incorrect as a universal rule. The "correct" histogram shape depends entirely on the subject matter and the photographer's intent. What the histogram reliably tells you is whether you have data where you need it, and whether you have lost data to clipping where you did not intend to.

### Gamma / Gamma Correction / Transfer Function

**Definition:** Gamma refers to the nonlinear relationship between encoded pixel values and the light intensity they represent. A transfer function (sometimes called a tone response curve or gamma curve) defines this mapping mathematically. "Gamma correction" is the process of applying or compensating for this nonlinearity.

The core concept: human vision is more sensitive to differences in dark tones than in bright tones. If you were to store light intensity values linearly (where value 200 represents twice the light of value 100), you would waste most of your available code values on bright tones that look similar to each other, while providing too few values in the shadows where your eyes can actually perceive the differences. Gamma encoding solves this by allocating more of the available code values to darker tones, roughly matching the distribution of values to human perceptual sensitivity.

**Meaning in photographic practice:** When you open a JPEG or a TIFF that has been rendered from RAW, the values in that file are gamma-encoded, not linear. This has practical consequences:

- A pixel value of 128 in an 8-bit gamma-encoded file does not represent 50% of the light intensity. In the sRGB encoding (which uses a transfer function approximately equivalent to gamma 2.2), a value of 128 represents roughly 21.6% of maximum light intensity. Perceptually, however, it looks like a "middle" tone — which is exactly the point of the encoding.
- When Photoshop performs calculations (blending modes, filters), some operations work on the encoded values directly. This means the mathematical result differs from what would happen if the calculation were performed in linear light. This distinction matters for operations like blurring, resizing, and certain blend modes.
- Your camera's RAW file contains linear data from the sensor. The conversion to a rendered image (JPEG, TIFF, or when you open the file in Photoshop from Camera Raw) applies a transfer function as part of the rendering. This is one of the many things that happens during RAW development.

**Photoshop implementation:** Photoshop's standard 8-bit and 16-bit working modes operate on gamma-encoded values. The specific transfer function depends on the color space: sRGB uses a piecewise function close to gamma 2.2; Adobe RGB (1998) uses a pure gamma of 2.2; ProPhoto RGB uses a gamma of 1.8. These differences are generally handled by the color management system and are covered in Chapter 02. Photoshop's 32-bit mode can work with linear data. The "Blend RGB Colors Using Gamma" option (found in Edit > Color Settings under "More Options") affects how certain blending operations treat gamma, with a value of 1.00 corresponding to linear-light blending.

**Related concepts:** Linear vs gamma-encoded data, transfer function, color space (Chapter 02), perceptual encoding, 32-bit mode.

**Common misconception:** "Gamma is just something monitors do." Historically, gamma correction was introduced partly to compensate for the nonlinear voltage-to-luminance response of CRT monitors. But the concept persists in modern imaging because gamma encoding is genuinely useful: it efficiently allocates code values to match human perception. Modern flat-panel displays, image file formats, and color spaces all use transfer functions, even though the original CRT reason is obsolete. Gamma is a property of the encoding, not just the display.

### Clipping (Tonal)

**Definition:** Tonal clipping occurs when pixel values are pushed to the minimum (0, pure black) or maximum (255 in 8-bit, 32768 in Photoshop's 16-bit) and can go no further. All scene detail at or beyond that value is lost — compressed into a single value. Clipped highlights appear as featureless white; clipped shadows appear as featureless black.

**Meaning in photographic practice:** Clipping is permanent data loss. Once values are clipped in a rendered file (JPEG, TIFF), no amount of post-processing can recover the lost detail — the information is gone. This is why exposure is critical: overexposing until highlights clip, or underexposing until shadows clip, destroys information at capture. RAW files provide more headroom than rendered files because they store a wider range of data and the clipping point has not yet been set by rendering.

Clipping is not always a problem. Specular highlights (reflections of light sources, sun glints on water) are expected to clip — they are supposed to be featureless white. The question is whether clipping occurs in areas where you need detail.

**Photoshop implementation:** Photoshop shows clipping in several ways:

- **Histogram panel:** values stacked against the left or right edge.
- **Levels and Curves:** hold Alt/Option while dragging the black or white point slider. The display changes to show which pixels are clipping and in which channels. Pixels that clip in all three channels appear white (highlight clipping) or black (shadow clipping). Pixels clipping in one or two channels appear in the complementary color.
- **Info panel:** values reading 0 or 255 (in 8-bit).

**Related concepts:** Histogram, dynamic range, exposure, highlight recovery (Chapter 03), Levels (Chapter 07).

**Common misconception:** "I can always recover clipped highlights in post." In a RAW file, you may have 1-2 stops of highlight data above the point where the JPEG preview looks clipped, depending on the camera. But once the sensor has genuinely clipped (the photosite is saturated), that data does not exist. And in a rendered file (JPEG, TIFF), clipped values are already at maximum — there is nothing to recover.

### Posterization / Banding

**Definition:** Posterization (also called banding or quantization artifacts) is the visible staircase effect in what should be a smooth tonal gradient. Instead of a seamless transition from one tone to another, you see distinct bands of uniform color. Posterization occurs when there are too few distinct tonal values to represent a smooth gradient over a given spatial area.

**Meaning in photographic practice:** Posterization most commonly appears in:

- Smooth gradients (clear skies, studio backdrops, skin tones in soft light)
- Images that have undergone aggressive tonal adjustments in 8-bit mode
- Images that were captured at low bit depth or heavily compressed

When you apply a strong Curves or Levels adjustment to an 8-bit image, you are remapping 256 input values to a smaller number of output values. The values that are no longer used create gaps in the histogram. If those gaps become large enough, the eye perceives them as distinct steps rather than a smooth gradient.

**Photoshop implementation:** You can detect posterization by examining the histogram after editing. Gaps (a "comb" pattern) indicate that some values are no longer present in the image. Whether these gaps produce visible banding depends on the image content — gradients reveal posterization that textured areas hide. Photoshop's Image > Adjustments > Posterize is a deliberate effect that reduces the number of tonal levels; do not confuse the deliberate creative effect with the unintentional artifact.

**Related concepts:** Bit depth, histogram, 8-bit vs 16-bit editing, dithering.

**Common misconception:** "I can fix posterization by adding noise." Adding a small amount of noise (or using Photoshop's dithering options) can mask the visual appearance of banding by breaking up the hard boundaries between bands. But it does not restore the lost tonal values. Prevention (working in 16-bit for heavy edits) is more effective than attempting to conceal the artifact after the fact.

### Linear vs Gamma-Encoded Data

**Definition:** Linear data represents light intensity proportionally: a value twice as large represents twice the light. Gamma-encoded data applies a nonlinear transfer function that compresses highlights and expands shadows relative to linear, allocating more code values to darker tones to match human perceptual sensitivity.

**Meaning in photographic practice:** This distinction matters because it determines what "half the value" means:

- In **linear** data: halving a pixel value halves the light intensity it represents. This corresponds to one stop less exposure.
- In **gamma-encoded** data (such as a standard 8-bit TIFF or JPEG): halving a pixel value produces a much darker result than one stop down, because the encoding is nonlinear.

Your camera sensor captures light linearly. RAW files contain linear (or near-linear) data. When you render the image (develop the RAW file into a TIFF, PSD, or JPEG), the RAW converter applies a transfer function. From that point on, the pixel values in the file are gamma-encoded.

**Photoshop implementation:** Standard Photoshop documents (8-bit and 16-bit) contain gamma-encoded data. The specific encoding depends on the working color space. Photoshop performs most operations on the encoded values directly. This generally produces acceptable results because the encoding was designed with editing in mind. However, operations that are technically correct only in linear light (such as Gaussian blur, image resampling, and certain compositing operations) will produce slightly different results on gamma-encoded data. The practical impact ranges from negligible (most tonal edits) to occasionally visible (compositing edges, certain resizing scenarios). Photoshop's 32-bit float mode typically works with linear data, and the "Blend RGB Colors Using Gamma 1.0" setting in Color Settings can force linear-light blending in lower bit depth modes for compositing work.

**Related concepts:** Gamma, transfer function, RAW development (Chapter 03), color space (Chapter 02), blending modes (Chapter 14).

**Common misconception:** "It does not matter whether data is linear or gamma-encoded because the result looks the same on screen." The results are different. When Photoshop blurs a gamma-encoded image, it is averaging encoded values, which overweights darker tones. In linear data, the same blur averages actual light intensities. The difference is subtle in most photographic editing but becomes visible in compositing and resizing, particularly around high-contrast edges.

## Theory

### How a Digital Image Represents a Scene

A digital photograph is a spatial sampling of light. The sensor divides the image plane into a grid, and each cell (photosite) accumulates photons during the exposure. The analog charge is converted to a digital number by the camera's analog-to-digital converter (ADC). This number is proportional to the light intensity at that location — the data is linear at this stage.

The RAW file stores these linear values (after some camera-internal processing such as black-level subtraction and possibly some compression). When you develop the RAW file — whether in Adobe Camera Raw, Lightroom, or another converter — several transformations occur:

1. **Demosaicing:** The color filter array (Bayer pattern) means each photosite captures only one color. The converter interpolates the missing color information to produce full RGB values at each pixel.
2. **White balance:** The converter adjusts the relative strength of the channels to neutralize the color of the illuminant.
3. **Tone mapping / transfer function:** The linear data is mapped through a tone curve that applies gamma encoding, adjusts contrast, and maps the scene's dynamic range into the file's tonal range.
4. **Color space assignment:** The values are mapped into a specific color space (sRGB, Adobe RGB, ProPhoto RGB).

The result is a gamma-encoded RGB image — the kind of file you open in Photoshop. Every operation you perform in Photoshop operates on this already-processed data.

### Why Gamma Encoding Exists

Consider an 8-bit channel with 256 possible values. If these values represented light intensity linearly, value 128 would represent 50% of the light that value 255 represents. The problem: human vision is roughly logarithmic in its response to light. You perceive a much larger difference between 1% and 2% light intensity than between 50% and 51%, even though the absolute difference is far larger in the second case.

In a linear encoding, approximately half of the 256 available values would be allocated to the brightest stop of the image (the range from 50% to 100% intensity), because that range spans half the numerical range. The darkest stop would receive very few values. This wastes precision where you do not need it and starves precision where you do.

Gamma encoding applies a power function (approximately) that compresses highlights and stretches shadows:

```
Encoded value = Linear value ^ (1/gamma)
```

For a gamma of 2.2, this means:

| Linear Light Intensity | Gamma-Encoded Value (approximate, 8-bit) |
|------------------------|-------------------------------------------|
| 100% (maximum white) | 255 |
| 50% | 186 |
| 25% | 144 |
| 18% (photographic middle gray) | 118 |
| 10% | 90 |
| 1% | 28 |

This encoding allocates far more code values to the shadows and midtones, where human vision is most discriminating. The decoding is performed by the display system, which applies the inverse function to convert back to light intensity.

**FACT:** The sRGB standard does not use a pure power function. It uses a piecewise function: a linear segment near black and a power curve (with an exponent near 2.4 combined with scaling, producing an end-to-end behavior close to gamma 2.2) for the rest of the range. For practical editing purposes, the distinction between "pure gamma 2.2" and "sRGB transfer function" rarely matters, but be aware that they are technically different.

### Bit Depth and Editing Headroom

The fundamental trade-off of bit depth is precision vs file size and compatibility.

Consider what happens when you increase the contrast of an image using Levels. You set a new black point at value 20 and a new white point at value 230 in an 8-bit image. Photoshop must remap the original range of 20-230 (211 values) to fill the full range of 0-255 (256 values). Some output values will have no corresponding input value — they become gaps. The histogram develops a comb pattern.

In an 8-bit image, you started with 256 possible values. After this adjustment, you might have only 211 distinct values remaining, with 45 gaps distributed through the range.

Now perform the same operation on a 16-bit image. You started with 32,769 possible values (in Photoshop's 16-bit implementation). The remapping from the equivalent input range to the full output range still creates proportionally the same number of gaps, but you have so many values that the remaining distinct values are far more than 256. Even after aggressive editing, the distribution remains dense enough to appear perfectly smooth. If you later convert to 8-bit for output, there are more than enough surviving values to fill all 256 output levels without visible gaps.

**RECOMMENDATION:** Convert from RAW to 16-bit for any image that will receive significant tonal adjustment. Convert to 8-bit only at the end of your workflow, when you are preparing the final output file. The file size penalty of 16-bit (roughly double) is trivial compared to the editing headroom gained.

### The Histogram as a Diagnostic Tool

Reading a histogram is a skill of pattern recognition. Here are the key patterns and what they indicate:

**Histogram touching neither edge, concentrated in the center:** The image is not using the full tonal range. It will appear flat and low-contrast. A Levels or Curves adjustment to expand the range will improve contrast, but check whether the flat appearance is intentional (fog, low-contrast subject) before "correcting" it.

**Spike at the right edge:** Highlight clipping. Pixels have been pushed to maximum white. If this is in a specular highlight, it may be acceptable. If it extends across a significant area of sky, skin, or important detail, information has been lost.

**Spike at the left edge:** Shadow clipping. The darkest tones have been crushed to pure black. In some high-contrast scenes this is inevitable; the question is whether important shadow detail is affected.

**Comb pattern (evenly spaced gaps):** Posterization from tonal editing. The image has been stretched or compressed enough that some values are no longer present. This is a sign that the edit may have been performed at insufficient bit depth, or that the edit was very aggressive.

**Narrow spike (single value dominates):** A large area of the image is a single uniform tone. Common in images with large areas of sky, studio backgrounds, or blown-out areas.

**Bimodal (two humps):** The image has two dominant tonal regions — common in high-contrast scenes with distinct light and dark areas (backlit subjects, for example).

**INTERPRETATION:** A histogram is descriptive. It tells you what the data looks like. Your photographic judgment tells you whether that is right for the image. The histogram does not know what the image is supposed to look like.

### Resolution vs Pixel Dimensions

These terms are frequently confused. They refer to different things.

**Pixel dimensions** are the width and height of the image in pixels (e.g., 6000 x 4000 pixels). This is a fixed property of the image data.

**Resolution** (in the DPI/PPI sense) is the density at which pixels are mapped to a physical output medium: pixels per inch (PPI) for screens, dots per inch (DPI) for print. Resolution is a scaling instruction — it tells the output device how large each pixel should be.

The same 6000 x 4000 pixel image:

- At 300 PPI, prints at 20 x 13.3 inches
- At 72 PPI, displays at 83.3 x 55.6 inches (notionally — in practice, screen display is controlled by the operating system and application, not the PPI tag)

**FACT:** Changing the resolution value in Image > Image Size (with "Resample" unchecked) changes only the PPI metadata tag. It does not add, remove, or modify any pixels. The image data is identical before and after.

**FACT:** Changing the resolution with "Resample" checked does change the pixel data — Photoshop adds pixels (upsampling) or removes pixels (downsampling) using an interpolation algorithm. This physically alters the image.

### Luminance vs Brightness: A Brief Introduction

These terms are used loosely in photography, but they have distinct technical meanings that become important in later chapters.

**Luminance** is a measurable, physical quantity: the intensity of light per unit area, measured in candelas per square meter (cd/m2). It is objective and device-independent.

**Brightness** is a perceptual quality — how intense a light or surface appears to a human observer. Brightness is subjective and depends on adaptation, surrounding context, and viewing conditions.

In everyday photographic conversation, these terms are often used interchangeably. In this manual, "luminance" refers to measurable light intensity, and "brightness" refers to the perceived sensation. The distinction becomes important when discussing color science (Chapter 02), tone curves (Chapter 07), and luminosity masks (Chapter 13).

## Photoshop Implementation

### Document Color Mode and Bit Depth

When you create or open an image in Photoshop, the document has a color mode (typically RGB) and a bit depth (8, 16, or 32 bits per channel). These are set under Image > Mode.

| Setting | Storage | Typical Use |
|---------|---------|-------------|
| 8 Bits/Channel, RGB | 3 bytes per pixel | Final output, web delivery, images with minimal editing |
| 16 Bits/Channel, RGB | 6 bytes per pixel | Working files from RAW, heavy tonal editing, archival masters |
| 32 Bits/Channel, RGB | 12 bytes per pixel (float) | HDR imaging, compositing with scene-referred data, linear workflows |

**VERSION NOTE:** Feature availability varies by bit depth. Historically, some filters and features have been unavailable in 16-bit or 32-bit mode. Adobe has expanded 16-bit support substantially over the years. Check your current version if a specific feature is unavailable — it may require 8-bit mode, or it may have been updated since.

### The Info Panel

The Info panel (Window > Info) is your real-time data inspector. As you move the cursor over the image, it displays the pixel values at that location. By default it shows RGB values, but you can configure it to show additional readouts (CMYK equivalents, Lab values, etc.) via the panel options.

Key uses:

- **Verifying values:** Check whether a supposedly neutral gray is actually neutral (equal R, G, B values in an appropriate color space).
- **Monitoring clipping:** Values at 0 or 255 (8-bit) indicate clipped data.
- **Before/after comparison:** When using an adjustment layer, the Info panel shows both the original value and the adjusted value.

The Eyedropper tool samples pixel values and displays them in the Info panel. You can place up to four color samplers (using the Color Sampler tool, nested under the Eyedropper) to persistently monitor specific pixel locations as you edit.

### The Histogram Panel

The Histogram panel (Window > Histogram) provides detailed statistical information about the tonal distribution.

Panel display options (accessible from the panel menu):

- **Compact View:** Small histogram, no statistics.
- **Expanded View:** Larger histogram with a channel dropdown and statistics.
- **All Channels View:** Displays individual R, G, B channel histograms alongside the composite.

The statistics display (in Expanded View) shows:

| Statistic | Meaning |
|-----------|---------|
| Mean | Average tonal value |
| Std Dev | How spread out the values are |
| Median | Middle value (half the pixels are above, half below) |
| Pixels | Total pixel count |
| Level | Value at the cursor position |
| Count | Number of pixels at that value |
| Percentile | Percentage of pixels at or below the cursor value |
| Cache Level | The zoom level used for computation (1 = full resolution) |

**RECOMMENDATION:** When the Histogram panel shows a yellow triangle with an exclamation mark, it is displaying a cached (approximate) histogram, typically computed from a lower-resolution proxy. Click the triangle or the refresh icon to compute the histogram from the full-resolution data. For critical evaluation, always use the uncached histogram.

### Image Size Dialog

Image > Image Size provides a comprehensive view of the document's pixel dimensions, resolution, and physical size.

Key fields:

- **Dimensions** (at the top): Pixel dimensions and file size.
- **Width / Height:** Can be set in pixels, inches, centimeters, and other units. The lock icon links width and height to maintain aspect ratio.
- **Resolution:** PPI value. Meaningful only for physical output (print). Irrelevant for screen display in most contexts.
- **Resample:** When checked, changing dimensions or resolution alters the pixel data. When unchecked, changing resolution only adjusts the PPI metadata — pixels remain untouched.

## Professional Workflow

### Implications for Your Editing Pipeline

The concepts in this chapter directly shape professional workflow decisions:

**1. Always export from RAW to 16-bit when you intend to edit in Photoshop.**

Your RAW file contains 12-bit or 14-bit data. Exporting to 8-bit immediately discards precision you may need. Export to 16-bit, perform your tonal and color edits, and convert to 8-bit only when creating the final deliverable.

**2. Read the histogram before reaching for any tonal tool.**

The histogram tells you what the image data looks like. Diagnose first, then choose the tool. If the image is flat, decide whether it needs a Levels expansion, a Curves S-curve, or whether the flatness is intentional. If highlights are clipping, decide whether you need to address this globally or locally. The diagnostic step prevents you from applying a default action that may not match the specific situation.

**3. Understand what the numbers mean at every stage of your pipeline.**

When you see RGB values of (128, 128, 128) in an sRGB document, you should know that this represents approximately 21.6% of maximum light intensity — perceptual middle gray, roughly equivalent to a photographic 18% gray card. When you see a histogram with a comb pattern, you should know that values have been lost to quantization and that further aggressive editing will make it worse.

**4. Monitor clipping with the Info panel and histogram, not just by looking at the screen.**

Your monitor's brightness, contrast setting, ambient lighting, and calibration all affect what you see. An area that looks like it has detail on your screen may actually contain clipped values. Trust the numbers over your eyes for technical evaluation.

**5. Know when to use 32-bit mode.**

For standard photographic editing, 16-bit is sufficient. Consider 32-bit mode for:

- HDR merge and tone mapping workflows
- Compositing where you need to preserve super-white or super-black values
- Linear-light editing pipelines

32-bit mode is not automatically "better." It increases file size, and some Photoshop features behave differently or are unavailable.

## Common Mistakes

**Editing in 8-bit from start to finish.** This is the most common cause of posterization in processed images. The solution is simple: work in 16-bit for the editing phase and convert to 8-bit only for final output.

**Confusing resolution (PPI) with image quality.** Changing PPI without resampling changes nothing about the image data. An image does not become "higher quality" because you type 300 into the resolution field. Quality is determined by the pixel data itself — the number of pixels, the precision of their values, the quality of the original capture.

**Ignoring the histogram and editing by screen appearance.** Your monitor's display characteristics (brightness, contrast, color temperature, ambient light) introduce bias into your perception. The histogram and Info panel provide objective data. Use them, especially when evaluating shadow detail and highlight clipping.

**Assuming clipped data can be recovered in post.** In a rendered file (JPEG, TIFF, PSD), clipped values are at the minimum or maximum. There is no hidden data beyond the clipping point. RAW files offer some recovery headroom, but even RAW data has hard limits — and those limits depend on the specific camera's sensor and ADC.

**Treating all histograms as needing "correction."** Not every image needs a full-range histogram. A photograph of fog should have a narrow histogram concentrated in the upper midtones. A dark low-key portrait should have most values in the left third. A silhouette against a sunset will have a bimodal histogram with clipped shadows. The histogram serves your creative intent, not the other way around.

**Confusing pixel dimensions with resolution.** Cropping an image reduces pixel dimensions. Changing resolution (without resampling) does not. These are different operations with different effects, and conflating them leads to confusion about print size and output quality.

## Summary

A digital image is a grid of pixels, each storing numerical values that represent color. In RGB mode, each pixel has three values — one per channel — describing the red, green, and blue components. The bit depth determines how many distinct values are available per channel: 256 for 8-bit, 32,769 for Photoshop's 16-bit, and effectively unlimited for 32-bit floating point.

The pixel values in a standard Photoshop document are gamma-encoded: they follow a nonlinear mapping designed to allocate more code values to darker tones, matching human perceptual sensitivity. This encoding means that numerical operations on pixel values do not correspond directly to operations on light intensity — a distinction that matters for understanding blend modes, compositing, and filter behavior.

Dynamic range is the ratio of brightest to darkest values a device can capture or reproduce — a property of scenes, sensors, and displays. Tonal range is the span of values actually present in an image file. The histogram visualizes tonal distribution and is the primary diagnostic tool for evaluating clipping, contrast, and the effects of tonal edits.

Clipping (loss of data at the extremes) is permanent in rendered files. Posterization (visible banding from insufficient tonal values) is largely preventable by working in 16-bit during the editing phase. Both are detectable through histogram analysis and the Info panel before they become visible problems.

These concepts are not academic — they are the foundation of every tonal and color decision you will make in subsequent chapters.

## Exercises

### Exercise 1: Histogram Diagnosis

Open three photographs of different character: a high-key image (bright, airy), a low-key image (dark, dramatic), and a "normal" exposure. For each, open the Histogram panel in All Channels View. Before making any adjustments:

1. Describe the histogram shape in words. Where is the mass of the data? Is it continuous or does it have gaps?
2. Is there clipping in any channel? Use the Info panel to verify by sampling the brightest and darkest areas.
3. Decide: does this image need tonal adjustment, or does the histogram match the photographic intent? Write down your reasoning before touching any controls.

The goal is to practice reading histograms diagnostically, not to "fix" anything.

### Exercise 2: Bit Depth and Posterization

Open a 16-bit image with a smooth gradient area (a sky, or a studio backdrop). Duplicate it and convert the duplicate to 8-bit (Image > Mode > 8 Bits/Channel). Now apply the same aggressive Curves adjustment to both — create an S-curve that significantly increases contrast. Compare the histograms of both results.

1. How do the histograms differ?
2. Can you see visible posterization in the 8-bit version? Where?
3. Is there posterization in the 16-bit version? If so, is it visible?

### Exercise 3: Resolution vs Pixel Dimensions

Open an image and note its pixel dimensions and resolution in Image > Image Size.

1. Uncheck "Resample." Change the resolution from 300 PPI to 72 PPI. What happened to the pixel dimensions? What happened to the print size? Was any image data changed?
2. Undo. Now check "Resample." Change the resolution from 300 PPI to 72 PPI. What happened to the pixel dimensions? Was image data changed?
3. Explain in your own words why these two operations produce different results.

### Exercise 4: Channel Analysis

Open a color photograph and examine the individual channels (Channels panel, or Ctrl/Cmd+3, 4, 5).

1. Which channel has the most contrast? Why might that be, given the color content of the scene?
2. Which channel is noisiest? (Hint: this is typically the Blue channel. Why?)
3. Find an area in the image that appears bright in one channel but dark in another. What color is that area in the composite image? Does this match your understanding of how RGB channels work?

## Advanced Exercises

### Advanced Exercise 1: Gamma Visualization

Create a new 16-bit document (1000 x 200 pixels). Use the Gradient tool to draw a smooth black-to-white gradient across the entire width. This gradient is created in gamma-encoded space (because your document is in a standard RGB working space).

1. Use the Info panel to sample values at the exact horizontal center (500 pixels in). What is the RGB value? Is it 128 (the mathematical midpoint) or something different?
2. Now reason: the center of a smooth gradient in gamma-encoded space should be the perceptual midpoint between black and white. But does the center of this gradient represent 50% of the light intensity? Use the gamma relationship to estimate what percentage of linear light this midpoint value represents.
3. Consider: if you wanted a gradient that represents linearly increasing light intensity (each position emitting proportionally more light), what would that gradient look like on screen? Would it appear to brighten evenly, or would it appear to brighten rapidly in the highlights?

### Advanced Exercise 2: Clipping Investigation

Open a photograph with both highlight and shadow detail at risk. Use Levels (as an adjustment layer) and hold Alt/Option while dragging the white-point slider and then the black-point slider.

1. Document exactly where clipping begins in each direction. Does it start in all channels simultaneously or in one channel first? Which channel clips first in the highlights? In the shadows?
2. Why does single-channel clipping matter differently from all-channel clipping? What happens to color in areas where one or two channels are clipped but not all three?
3. Set conservative clipping points (just before visually important detail begins to clip). Remove the adjustment layer. Now apply a different approach: use Curves to achieve a similar tonal range expansion but with a smoother rolloff at the endpoints. Compare the results. Which approach preserved more tonal smoothness in the near-white and near-black areas?

## Blackbelt Challenge

You receive a photograph from a client for large-format printing. The file is an 8-bit JPEG at 72 PPI. The histogram shows a moderate comb pattern, highlight clipping in the Red and Green channels (but not Blue) in the sky area, and the overall tonal range occupies roughly 60% of the available range.

Without opening Photoshop, answer the following:

1. What has already happened to this image, based on the evidence? Explain the likely history (capture, processing, export choices) that produced this particular combination of symptoms.
2. What can and cannot be recovered? Be specific about each problem.
3. What is the correct sequence of operations to prepare this file for large-format output? Include bit depth conversion, tonal correction, color correction, resizing, resolution setting, and output sharpening — and explain your reasoning for the order.
4. The client asks: "Can you make the sky bluer and recover the detail in the clouds?" What is your honest technical assessment, and what is the best you can actually do given the data you have?

This exercise has no single correct answer. The quality of your response depends on how well you apply the concepts from this chapter to reason about a real-world scenario.
