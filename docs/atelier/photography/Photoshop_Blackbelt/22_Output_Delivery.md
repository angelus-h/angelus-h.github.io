---
title: "Chapter 22: Resizing, Output Sharpening, and Delivery"
subtitle: "Preparing Images for Their Final Destination"
---

# Chapter 22: Resizing, Output Sharpening, and Delivery

## Learning Objectives

After completing this chapter, you will be able to:

- Distinguish between resizing (changing print dimensions without altering pixel count) and resampling (changing pixel count), and choose the correct operation for each output scenario.
- Evaluate and select the appropriate interpolation algorithm for a given upsampling or downsampling task.
- Apply output sharpening as a separate, final stage — distinct from the capture sharpening and creative sharpening covered in Chapter 15.
- Calculate the PPI needed for a given print size and viewing distance.
- Prepare a master file for multiple outputs — print, web gallery, social media — without degrading the master.
- Choose the correct file format, bit depth, and color space for each delivery target.
- Use Photoshop's Export As, Save a Copy, and Save for Web (Legacy) dialogs with full awareness of their differences.

---

## Conceptual Foundation

Every image you edit has a destination. That destination might be a 60-inch gallery print on cotton rag, a 1200-pixel-wide web portfolio image, an Instagram post, a newspaper page, or a client's retouching proof. Each destination imposes different requirements on pixel count, sharpness, color space, bit depth, and file format.

Output preparation is the final stage of the imaging pipeline. It sits after all creative work is done. The sequence is always:

1. **Master file** — full resolution, full bit depth, working color space, all layers intact.
2. **Output derivative** — a purpose-built copy with the correct pixel dimensions, sharpening, color space, bit depth, and format for the target output.

This chapter treats the master file as sacred. You never alter it for output. You always produce derivatives. The master is your negative; the output file is the print.

The three core operations in output preparation are:

- **Resampling** — changing the pixel count to match the output's needs.
- **Output sharpening** — compensating for the softening that occurs during resampling and during the output process itself (screen rendering, halftone printing, inkjet dot patterns).
- **Format conversion** — choosing the file format, compression, color space, and bit depth that the output requires.

These three operations are interdependent. Output sharpening must happen after resampling (because resampling changes the pixel structure). Color space conversion should happen before final sharpening if the conversion alters luminosity values (which it can, in saturated colors). Bit depth reduction should happen last, after all tonal operations are complete.

---

## Terminology

### Resampling

**Definition:** Resampling is the process of changing the total number of pixels in an image. Upsampling adds pixels; downsampling removes pixels.

**Distinction from resizing:** Resizing changes the print dimensions (by altering the PPI value) without changing pixel count. Resampling changes pixel count. Photoshop's Image Size dialog can do either, controlled by the "Resample" checkbox.

**How it works:** When you upsample, Photoshop must invent pixel values that did not exist in the original capture. When you downsample, Photoshop must combine multiple pixel values into fewer pixels. Both operations use an interpolation algorithm to calculate the new values. Neither operation adds genuine captured detail.

**Where it appears in Photoshop:** Image > Image Size (with Resample checked), Crop tool (with specific pixel dimensions), Canvas Size (adds pixels at edges, not interpolation), and Content-Aware Scale.

**Why it matters:** Every resampling operation degrades image quality to some degree. Upsampling creates pixels that look plausible but contain no new information. Downsampling discards information permanently. Understanding this lets you minimize resampling operations and choose the right algorithm when resampling is necessary.

---

### Interpolation

**Definition:** Interpolation is the mathematical method Photoshop uses to calculate new pixel values when resampling an image. Different algorithms examine different numbers of neighboring pixels and apply different weighting functions to compute the result.

**Available algorithms in Photoshop:**

| Algorithm | Pixel Neighborhood | Best For | Characteristics |
|---|---|---|---|
| Nearest Neighbor | 1 pixel | Pixel art, hard-edged graphics, screenshots | No blending; preserves hard edges; produces jagged artifacts on photographic content |
| Bilinear | 4 pixels (2x2) | Fast previews; moderate quality needs | Linear averaging; smoother than Nearest Neighbor but softer than Bicubic |
| Bicubic | 16 pixels (4x4) | General purpose (legacy) | Cubic weighting function; good balance of sharpness and smoothness |
| Bicubic Smoother | 16 pixels (4x4) | Upsampling (enlargement) | Modified weighting to reduce artifacts when adding pixels; produces smoother results |
| Bicubic Sharper | 16 pixels (4x4) | Downsampling (reduction) | Modified weighting that preserves edge contrast when removing pixels |
| Preserve Details | Larger adaptive neighborhood | Upsampling with noise control | Analyzes image content to preserve edges; includes Noise Reduction slider |
| Preserve Details 2.0 | Larger adaptive neighborhood, AI-assisted | Upsampling (best quality) | Uses machine learning; produces best results for photographic enlargement |

**VERSION NOTE:** Preserve Details 2.0 was introduced in Photoshop CC 2018 (v19.1) and must be enabled via Preferences > Technology Previews in some versions. In current versions (2024+), it is available by default in the Image Size dialog.

**How it works mechanically:** Consider upsampling by 2x. Each original pixel must become four pixels. Nearest Neighbor simply copies the original value to all four. Bilinear averages the four nearest original pixels with linear weighting. Bicubic uses a cubic polynomial to weight the 16 nearest original pixels, producing smoother gradients and sharper edges than Bilinear. Preserve Details 2.0 uses a neural network trained on photographic content to produce results that mimic genuine detail.

**Why it matters:** The algorithm choice directly affects whether your output looks sharp, soft, or artifacted. Using Nearest Neighbor on a photograph produces visible stair-stepping. Using Bicubic Smoother for downsampling throws away sharpness unnecessarily. Matching algorithm to task is a fundamental skill.

---

### Output Sharpening

**Definition:** Output sharpening is the third and final stage of the three-stage sharpening model (capture sharpening, creative sharpening, output sharpening). It compensates for the specific softening characteristics of the output medium — screen pixels, inkjet dots, halftone cells, or offset lithography.

**The three-stage sharpening model (review from Chapter 15):**

| Stage | Purpose | When Applied | Stays in Master? |
|---|---|---|---|
| Capture sharpening | Compensate for anti-aliasing filter and lens softness | During raw conversion or on the base layer | Yes |
| Creative sharpening | Draw attention, enhance texture, stylistic choice | During editing, often via Smart Filters | Yes |
| Output sharpening | Compensate for output medium softness | After resampling, on a flattened output copy | No — output copy only |

**How it differs from capture/creative sharpening:** Output sharpening is applied to a flattened, resampled copy — never to the master. Its settings depend entirely on the output: print on glossy paper needs less output sharpening than print on matte paper (matte paper absorbs more ink, spreading dots, which softens the image). Screen output needs sharpening calibrated to the viewing resolution.

**Typical starting points (Unsharp Mask):**

| Output | Amount | Radius | Threshold |
|---|---|---|---|
| Screen (web/social, 72-150 PPI) | 50-80% | 0.3-0.5 px | 0 |
| Inkjet glossy (240-360 PPI) | 80-120% | 0.5-0.8 px | 0 |
| Inkjet matte/fine art (240-360 PPI) | 120-180% | 0.8-1.2 px | 0 |
| Offset lithography (300 PPI) | 100-150% | 0.8-1.0 px | 0 |

**INTERPRETATION:** These are starting points. Every image, paper, and printer combination is different. The correct output sharpening is the one that looks right on the final output, verified by test prints or careful screen evaluation at 100% view.

**Why it matters:** Without output sharpening, prints look soft. With too much, they look harsh and artifacted. Because output sharpening is destructive and medium-specific, it must never be baked into the master file.

---

### PPI vs DPI

**Definition — PPI (Pixels Per Inch):** The number of image pixels that will be placed along one linear inch of the output. PPI is a property of the image file and its relationship to a specific print size. It is set in the Image Size dialog.

**Definition — DPI (Dots Per Inch):** The number of ink dots (or toner dots, or laser spots) that a printer can place along one linear inch of paper. DPI is a property of the printer hardware and driver settings. It is set in the print driver, not in Photoshop.

**The critical distinction:** PPI and DPI are not the same thing. A 300 PPI image printed on a 1440 DPI inkjet printer means that each image pixel is rendered using approximately 4.8 x 4.8 printer dots (1440/300 = 4.8). Those multiple printer dots are used to simulate the color and tone of that single pixel through dithering patterns.

**FACT:** Photoshop's Image Size dialog and its Print dialog both work in PPI. The term "DPI" does not appear in Photoshop's image handling. When Photoshop's Print dialog shows "resolution," it means PPI.

**Common PPI values:**

| Output Target | Typical PPI | Rationale |
|---|---|---|
| Inkjet photo printing | 240-360 | Matches the optical resolution of most inkjet printers (native input resolution) |
| Offset lithography (magazines, books) | 300 | Convention: 2x the typical 150 LPI halftone screen |
| Newspaper | 150-200 | Lower halftone screen frequency; uncoated paper absorbs ink |
| Large format (banners, trade show) | 100-150 | Viewed from distance; lower PPI is acceptable |
| Gallery fine art print (close viewing) | 300-360 | Close viewing distance demands full resolution |
| Screen display (web, social) | 72-96 (nominal) | PPI is irrelevant for screen; only pixel dimensions matter |

**RECOMMENDATION:** For screen output, ignore PPI entirely. Only pixel dimensions matter. A 1200x800 image displays identically whether marked as 72 PPI or 300 PPI — the browser or screen application uses the pixel dimensions, not the PPI metadata.

---

### File Formats for Photographic Output

#### PSD (Photoshop Document)

**Definition:** Adobe's native layered file format for Photoshop. Supports all Photoshop features: layers, masks, channels, Smart Objects, adjustment layers, paths, and color management.

**Technical limits:** Maximum file size 2 GB. Maximum dimensions 30,000 x 30,000 pixels. Supports 1, 8, 16, and 32 bits per channel.

**Use case:** Working master file for images within the size limits. Not a delivery format.

**Compression:** RLE (lossless) compression by default. Optional: no compression.

#### PSB (Large Document Format)

**Definition:** Extended version of PSD that removes the 2 GB and 30,000-pixel limits. Maximum dimensions 300,000 x 300,000 pixels. Maximum file size approximately 4 exabytes (practically limited by available storage).

**Use case:** Master file for high-resolution composites, panoramas, or heavily layered files that exceed PSD limits.

**Compatibility:** Supported in Photoshop CS and later, and in some Adobe applications. Not widely supported outside the Adobe ecosystem.

#### TIFF (Tagged Image File Format)

**Definition:** A flexible, well-documented raster image format that supports multiple compression options, bit depths, and color spaces. Widely used as an archival and print-delivery format.

**Compression options in Photoshop:**

| Compression | Type | Notes |
|---|---|---|
| None | Uncompressed | Largest file; maximum compatibility |
| LZW | Lossless | Good compression for images with large uniform areas; widely compatible |
| ZIP | Lossless | Better compression ratio than LZW for photographic content; slightly less compatible with older software |
| JPEG | Lossy | Smaller files but introduces compression artifacts; rarely appropriate for a TIFF workflow |

**FACT:** Photoshop can save layers in TIFF files (the "Save Layers" checkbox). This embeds a flattened composite for compatibility while preserving layers for Photoshop. This significantly increases file size — the file contains both the flattened image and all layer data.

**Bit depth:** Supports 8, 16, and 32 bits per channel. 16-bit TIFF is the standard hand-off format for high-quality print workflows.

**RECOMMENDATION:** For print delivery, use TIFF with ZIP compression, 16-bit if the print provider accepts it, 8-bit otherwise. Always flatten before saving a delivery TIFF — do not send layered TIFFs to print providers unless specifically requested.

#### JPEG (Joint Photographic Experts Group)

**Definition:** A lossy compressed format that achieves small file sizes by discarding high-frequency image data. The degree of loss is controlled by a quality setting.

**How JPEG compression works (simplified):** The image is divided into 8x8 pixel blocks. Each block is transformed from spatial data into frequency data using a Discrete Cosine Transform (DCT). High-frequency coefficients (fine detail) are quantized — rounded to zero or reduced in precision. The degree of quantization is controlled by the quality setting. Lower quality = more aggressive quantization = smaller file = more visible artifacts.

**Photoshop quality scale:** 0-12 in the Save dialog (0 = lowest quality, 12 = maximum quality). In Save for Web and Export As, the scale is 0-100%.

| Photoshop Quality (Save) | Approximate % | Typical Use |
|---|---|---|
| 10-12 | 80-100% | High-quality output; minimal visible artifacts |
| 7-9 | 55-75% | Good quality; small artifacts in detailed areas |
| 4-6 | 30-50% | Moderate quality; visible artifacts in gradients and edges |
| 0-3 | 0-25% | Low quality; severe blocking and color shift; avoid for photography |

**FACT:** JPEG does not support transparency, layers, or 16-bit depth. Every save-close-reopen-save cycle introduces additional quality loss (generational loss). Never use JPEG as a working format.

**RECOMMENDATION:** For web delivery, save JPEG at quality 8-10 (Save dialog) or 70-85% (Export As / Save for Web). Below quality 7 (approximately 60%), compression artifacts become objectionable in photographic images — banding in gradients, mosquito noise around high-contrast edges, and smearing of fine texture.

#### PNG (Portable Network Graphics)

**Definition:** A lossless compressed format that supports transparency (alpha channel). Uses DEFLATE compression (similar to ZIP).

**Key properties:** Lossless — no quality degradation. Supports 8-bit and 16-bit per channel. Supports full alpha transparency (unlike JPEG). Does not support CMYK.

**Use case:** Web images that require transparency (logos, product cutouts on transparent backgrounds). Screenshots. Images where JPEG artifacts are unacceptable (text overlays, graphics with sharp edges).

**File size:** Significantly larger than JPEG for photographic content (typically 3-10x). Not appropriate for photographic web delivery where transparency is not needed.

**PNG-8 vs PNG-24:** PNG-8 uses a palette of up to 256 colors (like GIF); PNG-24 uses full 24-bit color. Photoshop's Export As and Save for Web offer both options.

#### JPEG 2000

**Definition:** A wavelet-based image compression format offering both lossy and lossless compression modes. Technically superior to JPEG — better compression ratios, no 8x8 blocking artifacts, support for transparency and 16-bit.

**FACT:** JPEG 2000 has never achieved widespread adoption for web delivery. Browser support is limited (Safari only, as of 2024). Its primary use is in digital cinema (DCP), medical imaging (DICOM), and archival workflows.

**In Photoshop:** Available as a plugin/export option, but not a default Save format. Rarely used in photography workflows.

#### WebP

**Definition:** A modern image format developed by Google, offering both lossy and lossless compression. Lossy WebP typically achieves 25-35% smaller file sizes than JPEG at equivalent visual quality.

**VERSION NOTE:** Photoshop gained native WebP support (open and save) in version 23.2 (February 2022). Earlier versions require a plugin.

**Key properties:** Supports transparency (alpha channel) in both lossy and lossless modes. Supports animation. Maximum dimensions 16,383 x 16,383 pixels. 8-bit only.

**Use case:** Web delivery where browser support is confirmed. All modern browsers (Chrome, Firefox, Safari 14+, Edge) support WebP.

**RECOMMENDATION:** WebP is an excellent choice for web delivery when you control the platform and know the audience uses modern browsers. For maximum compatibility (email, legacy systems), JPEG remains safer.

#### HEIF / HEIC (High Efficiency Image Format)

**Definition:** A container format using HEVC (H.265) compression. Apple adopted HEIF/HEIC as the default camera format on iOS devices starting with iOS 11 (2017). Offers approximately 50% file size reduction compared to JPEG at similar quality.

**VERSION NOTE:** Photoshop has supported opening HEIF/HEIC files since CC 2020 (v21.0). Saving to HEIF requires the HEIF plugin and appropriate licensing. Support varies by operating system — full support on macOS, limited on Windows (requires HEVC codec from Microsoft Store).

**Use case:** Primarily a capture format from Apple devices. Rarely used as a deliberate output format in photography workflows due to licensing complexity and limited cross-platform support.

---

### JPEG Compression (Detailed)

**Definition:** The lossy compression algorithm used in JPEG files that achieves small file sizes by selectively discarding image information that is less perceptible to human vision.

**Technical process:**

1. **Color space conversion:** RGB is converted to YCbCr. The Y (luminance) channel retains full resolution; Cb and Cr (chrominance) channels are typically downsampled by 2x (chroma subsampling 4:2:0 or 4:2:2), exploiting the eye's lower sensitivity to color detail.
2. **Block division:** The image is divided into 8x8 pixel blocks.
3. **DCT transform:** Each block is transformed from spatial domain to frequency domain.
4. **Quantization:** Frequency coefficients are divided by values from a quantization table (which is controlled by the quality setting) and rounded. Higher quality = smaller divisors = less rounding = more data preserved.
5. **Entropy coding:** The quantized coefficients are losslessly compressed using Huffman coding or arithmetic coding.

**Visible artifacts at low quality:** Blocking (grid of 8x8 squares visible in smooth areas), mosquito noise (ringing around high-contrast edges), color bleeding (chroma subsampling artifacts), banding in gradients, and texture smearing.

**FACT:** JPEG compression is most destructive in areas of smooth, subtle gradation (skies, skin tones, studio backgrounds). It is least visible in areas of high-frequency, high-contrast detail (foliage, gravel, fabric texture). An image of a sunset gradient over smooth water will show artifacts at a higher quality setting than an image of a dense forest.

**Why it matters:** Understanding where JPEG compression fails lets you choose quality settings intelligently — and lets you decide when JPEG is the wrong format entirely (images with text, graphics with sharp edges, images requiring transparency).

---

### Export As / Save a Copy / Save for Web (Legacy)

**Definition:** Photoshop provides three distinct mechanisms for producing output files, each with different capabilities and intended use cases.

**Save a Copy (File > Save a Copy):**

- Saves a copy without changing the current working document.
- Supports all Photoshop-native formats (PSD, PSB, TIFF, JPEG, PNG, PDF).
- Preserves the current state of the working file — your open document remains unchanged after the save.
- Does not offer web-optimization options (no preview of file size, no side-by-side quality comparison).
- **VERSION NOTE:** "Save a Copy" replaced the old "Save As" behavior starting in Photoshop v23.0 (October 2021). "Save As" now only saves in the current format. "Save a Copy" is where you access format conversion.

**Export As (File > Export > Export As):**

- Designed for web and screen output.
- Supports PNG, JPEG, GIF, SVG, and WebP.
- Provides a live preview with file size display.
- Allows setting exact pixel dimensions, canvas size, and metadata options.
- Can export multiple sizes simultaneously (1x, 2x, 3x for responsive design).
- Offers color space conversion to sRGB.
- Does not support TIFF, PSD, or 16-bit output.

**Save for Web (Legacy) (File > Export > Save for Web (Legacy)):**

- The original web export dialog, present since Photoshop 5.5.
- Marked as "Legacy" since CC 2015 but still present and functional.
- Offers 2-up and 4-up preview comparisons — you can compare different quality settings side by side.
- Supports JPEG, PNG-8, PNG-24, GIF, and WBMP.
- Provides detailed file size and download time estimates.
- Offers more granular control over JPEG settings (Progressive, ICC Profile embedding, blur).
- Includes "Convert to sRGB" checkbox.
- Does not support WebP.

**RECOMMENDATION:** Use Save for Web (Legacy) when you need side-by-side quality comparison or fine control over JPEG settings. Use Export As for WebP output, for batch-exporting multiple sizes, or for a cleaner workflow. Use Save a Copy for TIFF and PSD output copies.

---

### Bit Depth Conversion for Output

**Definition:** Reducing the bit depth of an image from a higher precision (16 or 32 bits per channel) to a lower precision (8 bits per channel) for output to formats that require it or for file size reduction.

**When conversion is required:** JPEG only supports 8-bit. PNG supports 8 and 16-bit. WebP supports 8-bit only. Web browsers render 8-bit images. Most print providers accept 8-bit; some accept 16-bit TIFF.

**How to convert:** Image > Mode > 8 Bits/Channel. In Save for Web and Export As, conversion to 8-bit happens automatically.

**What is lost:** In a 16-bit file, each channel has 65,536 tonal levels. Converting to 8-bit reduces this to 256 levels. If you have applied heavy tonal corrections, the 16-bit file may have smooth gradations where the 8-bit version would show banding. The conversion itself, however, applies high-quality dithering to minimize visible banding.

**RECOMMENDATION:** Perform all tonal corrections in 16-bit. Convert to 8-bit as the last step before output, after all adjustments and sharpening are finalized. If a print provider accepts 16-bit TIFF, deliver in 16-bit.

---

### Color Space Conversion for Output

**Definition:** Converting an image from the working color space (typically Adobe RGB or ProPhoto RGB) to the color space required by the output target — usually sRGB for web and screen output, or the printer's profile for print output.

**Why sRGB for web:** FACT: The vast majority of web browsers, mobile devices, and consumer monitors operate in or assume sRGB. An image tagged as Adobe RGB and displayed in a non-color-managed browser will appear desaturated — the wider-gamut values are interpreted as sRGB values, resulting in muted colors.

**How to convert:** Edit > Convert to Profile. Choose the destination profile (sRGB IEC61966-2.1 for web). Use Relative Colorimetric intent with Black Point Compensation for most photographic images.

**INTERPRETATION:** Converting from a wide gamut (ProPhoto RGB or Adobe RGB) to sRGB is a one-way, lossy operation. Colors outside the sRGB gamut are compressed or clipped. This is why you never convert your master file — you convert a copy.

**For print output:** If you are providing files to a print lab or press operator, ask them what profile and color space they want. Many fine art print labs accept Adobe RGB TIFF files and handle the conversion to their printer profile internally. Some want sRGB. Do not guess — ask.

**RECOMMENDATION:** The conversion sequence for web output is: (1) flatten the image, (2) resample to output dimensions, (3) convert to sRGB, (4) convert to 8-bit, (5) apply output sharpening, (6) save in the output format. This order ensures that sharpening is calibrated to the final color values the viewer will see.

---

## Theory

### Why Output Sharpening Is Separate

Chapter 15 introduced the three-stage sharpening model. Output sharpening is not a repetition of capture sharpening at a different strength — it addresses a fundamentally different problem.

Capture sharpening compensates for the inherent softness of the digital capture process (anti-aliasing filter, lens diffraction, demosaicing). It restores the image to what the lens "saw."

Creative sharpening emphasizes specific textures or areas for artistic effect. It enhances what the photographer wants the viewer to notice.

Output sharpening compensates for softening that has not yet occurred — softening that will occur during the output process itself:

- **Screen rendering:** When an image is displayed at less than 100% zoom, the display must map multiple image pixels to single screen pixels. The rendering algorithm introduces slight softening. Even at 100%, sub-pixel rendering on LCD screens introduces slight edge effects.
- **Inkjet printing:** Each image pixel is rendered as a cluster of tiny ink droplets on paper. The dots spread into the paper fibers (dot gain). The ink-paper interaction always produces a softer result than the digital file. Matte and fine art papers exhibit more dot gain than glossy papers.
- **Offset lithography:** The halftone screening process converts continuous tones into patterns of dots. This inherently reduces apparent sharpness.

Because different output processes introduce different amounts of softening, output sharpening must be tailored to each output. A file sharpened for screen display will look oversharpened on a glossy print and undersharpened on a matte print. A file sharpened for matte paper will look harsh on screen.

### Interpolation Algorithms in Depth

All interpolation algorithms face the same fundamental problem: they must assign values to pixel locations where no measurement was taken. The differences lie in how they weight the contributions of surrounding known pixels.

**Nearest Neighbor** assigns the value of the single closest original pixel. This produces zero blending between pixels, which preserves hard edges perfectly but creates visible stair-step patterns (aliasing) on diagonal and curved edges in photographic content. It is the only appropriate choice for pixel art, screenshots, and images where the pixel grid itself carries meaning.

**Bilinear** interpolation computes a weighted average of the four nearest original pixels (a 2x2 neighborhood). The weights are proportional to the distance from each original pixel to the new pixel location. This produces smooth results but slightly soft images, because the linear averaging acts as a low-pass filter.

**Bicubic** interpolation uses a 4x4 neighborhood (16 pixels) and applies cubic polynomial weighting. The cubic function has the property of producing smoother curves than linear averaging — it can maintain sharper edges while still producing smooth gradients. Standard Bicubic is a balanced default.

**Bicubic Smoother** modifies the cubic weighting to produce less edge contrast. When upsampling, standard Bicubic can produce slight ringing artifacts at high-contrast edges (tiny bright-dark overshoots). Bicubic Smoother reduces these at the cost of slightly less edge definition. INTERPRETATION: For moderate upsampling (up to 150-200%), the difference is subtle. For large enlargements, Bicubic Smoother produces fewer artifacts.

**Bicubic Sharper** modifies the cubic weighting to emphasize edge contrast. When downsampling, standard Bicubic can produce slightly soft results because it averages pixels together. Bicubic Sharper compensates for this with a sharpening component in the weighting function.

**Preserve Details 2.0** uses a fundamentally different approach. Instead of a fixed mathematical weighting function, it uses a neural network trained on large datasets of photographic images to predict what high-resolution detail should look like given low-resolution input. FACT: It produces visibly better results than any Bicubic variant for enlargements greater than 200%, with better edge definition and fewer artifacts in textured areas.

### File Format Internals

Understanding what each format actually stores helps you make informed choices.

**TIFF internals:** TIFF is a tag-based format — the file consists of a header followed by one or more Image File Directories (IFDs), each containing tags that describe the image data (dimensions, bit depth, color space, compression method) followed by the image data itself. This flexible structure is why TIFF can support so many variations. The same flexibility is why "TIFF" alone does not tell you enough — you need to know the compression, bit depth, and color space to fully characterize the file.

**JPEG internals:** A JPEG file contains a header (with metadata including EXIF and ICC profile), quantization tables (which define the compression quality), Huffman tables (for entropy coding), and the compressed image data as a stream of 8x8 DCT blocks. The quantization tables are the quality setting made permanent — they cannot be reversed. This is why you cannot "restore" a heavily compressed JPEG.

**PNG internals:** PNG stores image data as a series of filtered scanlines compressed with DEFLATE. The filtering step (sub, up, average, Paeth) predicts each pixel value from its neighbors and stores only the difference, which compresses better than raw values. This is why PNG compression is lossless — the full original data can be perfectly reconstructed.

### Viewing Distance and Required PPI

The human eye has a resolving power of approximately 1 arcminute (1/60 of a degree) under ideal conditions. This translates to:

| Viewing Distance | Maximum Resolvable Detail | Required PPI |
|---|---|---|
| 10 inches (25 cm) — handheld print | ~0.004 inches | ~250 PPI |
| 18 inches (45 cm) — desktop viewing | ~0.007 inches | ~150 PPI |
| 3 feet (1 m) — gallery wall | ~0.015 inches | ~70 PPI |
| 6 feet (2 m) — large print | ~0.029 inches | ~35 PPI |
| 10 feet (3 m) — banner | ~0.048 inches | ~21 PPI |

**INTERPRETATION:** These are theoretical minimums based on visual acuity. In practice, higher PPI is preferred because: (a) viewers may approach closer than the "intended" distance; (b) the output process (inkjet, lithography) introduces its own softening; (c) psychologically, a print that remains sharp at close inspection conveys quality. The 300 PPI convention for print delivery provides ample headroom for most viewing scenarios.

**FACT:** For large-format prints viewed from distance (trade show banners, building wraps), 100-150 PPI is standard practice and produces excellent results. Sending a 300 PPI file for a 6-foot banner wastes processing time and storage without visible benefit.

---

## Photoshop Implementation

### Image Size Dialog (Image > Image Size)

The Image Size dialog is the primary interface for both resizing and resampling.

**Controls:**

- **Dimensions display (top):** Shows current file size, pixel dimensions, and document size.
- **Fit To dropdown:** Presets for common output sizes.
- **Width / Height:** Set target dimensions in pixels, inches, centimeters, or other units.
- **Resolution:** The PPI value. When Resample is off, changing Resolution changes Width/Height proportionally (and vice versa) without changing pixel count. When Resample is on, changing Resolution while Width/Height are locked in physical units changes the pixel count.
- **Resample checkbox:** Controls whether the pixel count changes.
- **Resample method dropdown:** Selects the interpolation algorithm. "Automatic" lets Photoshop choose (Bicubic Smoother for enlargement, Bicubic Sharper for reduction).

**RECOMMENDATION:** Do not trust "Automatic" blindly. For critical enlargements, manually select Preserve Details 2.0 and evaluate the result. For critical downsampling, Bicubic Sharper is usually correct, but verify at 100% zoom.

### Applying Output Sharpening

Output sharpening should be applied to a flattened copy of the image, after resampling to the final output dimensions.

**Method 1: Unsharp Mask (Filter > Sharpen > Unsharp Mask)**

The most controllable method. Set Amount, Radius, and Threshold based on output medium (see the table in the Output Sharpening terminology section above).

**Method 2: Smart Sharpen (Filter > Sharpen > Smart Sharpen)**

Offers lens blur and motion blur removal in addition to standard sharpening. More sophisticated edge detection than Unsharp Mask. The "Reduce Noise" slider (in newer versions) helps control artifact amplification.

**Method 3: High Pass sharpening on a merged visible layer**

Create a merged visible layer (Ctrl/Cmd+Shift+Alt/Opt+E), apply Filter > Other > High Pass with a radius matching your output sharpening needs, set the blend mode to Overlay, Soft Light, or Hard Light. Adjust layer opacity to control strength.

**RECOMMENDATION:** For output sharpening specifically, Unsharp Mask provides the most predictable control because its parameters map directly to the output characteristics you are compensating for. Use it with Amount and Radius values from the table above, then evaluate at 100% view (for screen output) or via test print (for print output).

### Export As Dialog (File > Export > Export As)

**Settings:**

- **Format:** PNG, JPEG, GIF, SVG, WebP.
- **Quality (JPEG/WebP):** 0-100% slider with live preview and file size display.
- **Image Size:** Set pixel dimensions directly. Maintains aspect ratio by default.
- **Canvas Size:** Add padding around the image.
- **Metadata:** Copyright, Contact Info, or None.
- **Color Space:** Checkbox to "Convert to sRGB." Leave checked for web output. ICC profile embedding is automatic when checked.
- **Scale All:** Export at 1x, 2x, 3x simultaneously (useful for responsive/retina web assets).

### Save for Web (Legacy) Dialog

**Accessed via:** File > Export > Save for Web (Legacy), or keyboard shortcut Ctrl/Cmd+Shift+Alt/Opt+S.

**Unique capabilities not found in Export As:**

- **2-up and 4-up preview:** Compare original vs. compressed, or compare four different compression settings side by side.
- **Progressive JPEG:** Encodes the image in multiple passes — the browser displays a low-quality version first, then refines. Slightly larger file size but better perceived loading speed.
- **Optimized:** Uses optimized Huffman tables for slightly smaller JPEG file size.
- **Embed Color Profile:** Checkbox to include or exclude the ICC profile. For sRGB files destined for web, embedding the profile adds ~3-4 KB but ensures correct rendering in color-managed browsers.
- **Blur:** Applies a slight blur before compression to reduce JPEG artifacts. Rarely useful for photography.
- **Image Size section:** Resize within the dialog without going to Image Size first.
- **Metadata:** Dropdown to strip or preserve EXIF, XMP, and copyright data.

**INTERPRETATION:** Save for Web (Legacy) remains the best tool for iterative JPEG quality optimization because of the side-by-side preview. You can visually compare Quality 70 vs. 80 vs. 85 and see the file size impact in real time. Export As shows only one setting at a time.

### Save a Copy (File > Save a Copy)

**Accessed via:** File > Save a Copy, or keyboard shortcut Ctrl/Cmd+Shift+Alt/Opt+S (note: the shortcut may conflict with Save for Web depending on version and keyboard shortcut settings).

**Use case:** Saving a TIFF or PSD copy for print delivery. Provides access to format-specific options:

- **TIFF options:** Compression (None, LZW, ZIP, JPEG), byte order, layer flattening, transparency.
- **JPEG options:** Quality slider 0-12, format options (Baseline Standard, Baseline Optimized, Progressive).
- **PSD/PSB options:** Maximize compatibility.

---

## Professional Workflow

### Complete Output Preparation for Print

The following workflow assumes you have a finished master file (PSD or PSB) at full resolution, 16-bit, in your working color space (ProPhoto RGB or Adobe RGB), with all layers intact.

**Step 1: Create a working copy.**
Duplicate the master file (Image > Duplicate) or use Save a Copy to create a TIFF copy. Work on the copy from this point forward. The master is not touched.

**Step 2: Flatten.**
Layer > Flatten Image. This merges all layers, discards hidden layers, and applies all adjustment layers. The result is a single Background layer.

**Step 3: Determine required pixel dimensions.**
Calculate the pixel dimensions needed for the target print size at the target PPI.

Formula: pixels = print dimension (inches) x PPI

Example: 16 x 24 inch print at 300 PPI = 4800 x 7200 pixels.

If the master has sufficient or excess pixels, resample down. If the master has fewer pixels than needed, you must decide whether to upsample (with inevitable quality loss) or reduce the print size to match available pixels.

**Step 4: Resample to output dimensions.**
Image > Image Size. Check "Resample." Set the target dimensions. Choose the appropriate interpolation method.

- Downsampling: Bicubic Sharper or Automatic.
- Upsampling: Preserve Details 2.0 for photographic content.

**Step 5: Verify color space.**
Check Edit > Assign Profile / Convert to Profile. The file should be in the color space the print provider expects. Most fine art labs expect Adobe RGB or sRGB. Ask the provider.

If conversion is needed: Edit > Convert to Profile. Destination: the target profile. Intent: Relative Colorimetric. Check Black Point Compensation.

**Step 6: Bit depth.**
If the print provider requires 8-bit: Image > Mode > 8 Bits/Channel. If they accept 16-bit, leave it.

**Step 7: Apply output sharpening.**
Filter > Sharpen > Unsharp Mask. Use settings appropriate for the paper type (see the table in the Output Sharpening terminology section). Evaluate at 100% view. For critical work, make a small test print of a representative area.

**Step 8: Save the output file.**
File > Save a Copy. Choose TIFF with ZIP compression for print delivery. Uncheck "Layers" (the file should already be flat). Ensure the ICC profile is embedded.

**Step 9: Verify.**
Open the saved TIFF and inspect it. Confirm dimensions, resolution, color space (Edit > Assign Profile should show the correct profile), bit depth, and visual quality.

### Complete Output Preparation for Web

**Step 1: Create a working copy.**
Image > Duplicate. Work on the duplicate.

**Step 2: Flatten.**
Layer > Flatten Image.

**Step 3: Convert to sRGB.**
Edit > Convert to Profile. Destination: sRGB IEC61966-2.1. Intent: Relative Colorimetric. Black Point Compensation: checked.

**Step 4: Convert to 8-bit.**
Image > Mode > 8 Bits/Channel.

**Step 5: Resample to output pixel dimensions.**
Image > Image Size. Check "Resample." Set the longest edge to the desired pixel dimension (common values: 1200-2048 pixels for portfolio/gallery, 800-1200 for blog, check platform-specific recommendations for social media). Use Bicubic Sharper for downsampling.

**Step 6: Apply output sharpening for screen.**
Filter > Sharpen > Unsharp Mask. Starting point: Amount 50-80%, Radius 0.3-0.5 px, Threshold 0. Evaluate at 100% zoom — this is how the viewer will see it.

**Step 7: Save the output file.**
File > Export > Save for Web (Legacy) or File > Export > Export As.

For JPEG: Quality 75-85% (Save for Web) or 8-10 (Save a Copy). Embed the sRGB profile. Set metadata preferences (strip EXIF for privacy, or keep copyright info).

For WebP: Quality 75-85%. Smaller file size than JPEG at equivalent visual quality.

For PNG: Use only when transparency is needed or when the image contains sharp text/graphics. Photographic PNG files are very large.

---

## Step-by-Step Example: One Image, Two Outputs

### Scenario

You have a landscape photograph — a lake scene with detailed foreground rocks, smooth water, and a gradient sky. The master file is:

- 7360 x 4912 pixels (36 megapixels, from a full-frame camera)
- 16-bit, ProPhoto RGB
- PSD with 14 layers (adjustments, retouching, creative sharpening)
- File size: 1.2 GB

You need to produce:

1. **Fine art print:** 20 x 30 inches on matte cotton rag paper, for gallery exhibition.
2. **Web gallery:** 1600 pixels on the longest edge, JPEG, for your portfolio website.

### Output 1: Fine Art Print (20 x 30 inches, matte paper)

**1. Duplicate the master.**
Image > Duplicate. Name it "Lake_30x20_matte_output."

**2. Flatten.**
Layer > Flatten Image. The 14-layer file becomes a single Background layer. File size drops to approximately 207 MB (16-bit, 3 channels, 7360 x 4912).

**3. Calculate required pixels.**
30 inches x 300 PPI = 9000 pixels (long edge).
20 inches x 300 PPI = 6000 pixels (short edge).
The master is 7360 x 4912. The required output is 9000 x 6000. You need to upsample.

**4. Resample.**
Image > Image Size. Check "Resample." Set Width to 9000 pixels (Height auto-calculates to 6000 based on aspect ratio). Resample method: Preserve Details 2.0. Adjust the Noise Reduction slider if needed — start at 10-20% and evaluate.

**5. Verify color space.**
The print lab accepts Adobe RGB TIFF files. Convert: Edit > Convert to Profile. Destination: Adobe RGB (1998). Intent: Relative Colorimetric. Black Point Compensation: checked.

INTERPRETATION: Converting from ProPhoto RGB to Adobe RGB will clip some colors that exist in ProPhoto but not in Adobe RGB. For most landscape photographs, the impact is minimal. If the image contains extremely saturated cyans or greens, use Perceptual intent instead to compress rather than clip the out-of-gamut colors.

**6. Bit depth.**
The lab accepts 16-bit TIFF. Leave it at 16-bit.

**7. Output sharpening for matte paper.**
Filter > Sharpen > Unsharp Mask.
Amount: 150%. Radius: 1.0 px. Threshold: 0.

Evaluate at 100% zoom. The sharpening should look slightly oversharpened on screen — matte paper will absorb the excess, and the final print will appear correctly sharpened. This is the counterintuitive part of print output sharpening: it should look a bit too strong on your monitor.

Check the sky gradient area carefully — oversharpening can amplify noise in smooth gradients. If needed, undo and reapply after masking the sky with a selection (this is a case where using a Smart Object for the sharpening layer gives you more control, though for a final output copy, direct application is standard).

**8. Save.**
File > Save a Copy. Format: TIFF. Compression: ZIP. Layers: unchecked. Embed ICC Profile: checked (Adobe RGB). Pixel Order: Interleaved. Byte Order: IBM PC (for widest compatibility).

Result: A flattened, 9000 x 6000, 16-bit, Adobe RGB TIFF file, output-sharpened for matte paper. Approximately 310 MB with ZIP compression.

### Output 2: Web Gallery (1600 px longest edge, JPEG)

**1. Return to the master file.** Or open the master again. Do not work from the print output — it has been sharpened for matte paper, which is wrong for screen.

**2. Duplicate the master.**
Image > Duplicate. Name it "Lake_web_1600."

**3. Flatten.**
Layer > Flatten Image.

**4. Convert to sRGB.**
Edit > Convert to Profile. Destination: sRGB IEC61966-2.1. Relative Colorimetric. Black Point Compensation: checked.

**5. Convert to 8-bit.**
Image > Mode > 8 Bits/Channel.

**6. Resample.**
Image > Image Size. Check "Resample." Set Width (longest edge) to 1600 pixels. Height auto-calculates to 1067 pixels. Resample method: Bicubic Sharper (or Automatic — both will use Bicubic Sharper for this downsampling operation).

Downsampling from 7360 to 1600 is a significant reduction (approximately 78%). The result will be sharp but will benefit from output sharpening.

**7. Output sharpening for screen.**
Filter > Sharpen > Unsharp Mask.
Amount: 60%. Radius: 0.4 px. Threshold: 0.

Evaluate at 100% zoom. This is exactly how the image will appear in a web browser at 1:1 display. The sharpening should look clean and natural — no visible halos around edges, no amplified noise in the sky.

**8. Save.**
File > Export > Save for Web (Legacy).
Format: JPEG. Quality: 80%. Progressive: checked. Embed Color Profile: checked (sRGB). Metadata: Copyright.

Use the 2-up preview to compare the original (left) with the compressed version (right). Examine the sky gradient for banding. Check the rock textures for smearing. If artifacts are visible at 80%, increase to 85%. If the file looks clean, try 75% to reduce file size.

Result: A 1600 x 1067, 8-bit, sRGB JPEG file, output-sharpened for screen viewing. Approximately 350-500 KB at quality 80%.

---

## Common Mistakes

### 1. Sharpening the master file for output

**The mistake:** Applying output sharpening directly to the layered master file, then saving.

**Why it is wrong:** Output sharpening is calibrated for a specific medium and resolution. A master sharpened for web is ruined for print (oversharpened at high resolution). A master sharpened for matte print will look aggressively oversharpened on screen and wrong on glossy paper.

**The fix:** Always duplicate the master before applying output sharpening. Output sharpening goes on the derivative, never the master.

### 2. Upsampling and expecting new detail

**The mistake:** Enlarging a 12-megapixel image to 60 megapixels and expecting the result to match a 60-megapixel capture.

**Why it is wrong:** Interpolation creates plausible pixel values based on mathematical prediction. It does not recover detail that was never captured. A 200% enlargement via Preserve Details 2.0 will look convincing but will not withstand the same close-up scrutiny as a native-resolution capture.

**The fix:** Set realistic expectations. For critical work, plan the capture resolution for the intended output size. Use upsampling when necessary but understand its limits.

### 3. Confusing PPI and DPI

**The mistake:** Saying "I need to set my image to 1440 DPI to match my printer" and setting 1440 in the PPI field.

**Why it is wrong:** 1440 DPI is the printer's output resolution. Setting the image to 1440 PPI would create an enormous file with no visible quality improvement over 300 PPI. The printer's driver handles the mapping from image PPI to printer DPI.

**The fix:** Set PPI based on the output viewing needs (240-360 for standard print, 300 as a safe default). Let the printer driver handle DPI.

### 4. Saving web images in Adobe RGB or ProPhoto RGB

**The mistake:** Exporting a JPEG for the web in Adobe RGB without converting to sRGB.

**Why it is wrong:** Non-color-managed applications (most web browsers historically, many social media platforms, email clients) assume sRGB. An Adobe RGB image displayed as if it were sRGB appears desaturated — colors look washed out and muted.

**The fix:** Always convert to sRGB before saving for web. Use the "Convert to sRGB" checkbox in Export As or Save for Web.

### 5. Multiple rounds of JPEG compression

**The mistake:** Opening a JPEG, making edits, saving as JPEG. Opening that JPEG, making more edits, saving as JPEG again. Each cycle introduces additional quality loss.

**Why it is wrong:** JPEG compression is lossy and cumulative. Each save cycle re-quantizes the DCT coefficients. After three or four generations, artifacts become clearly visible — especially in gradients and smooth tones.

**The fix:** Work on the master file (PSD/TIFF). Export to JPEG only as the final step. If you must edit a JPEG, save your work as PSD during editing and export a new JPEG from the PSD when done.

### 6. Using maximum JPEG quality for everything

**The mistake:** Always saving JPEG at Quality 12 (100%) for web, creating unnecessarily large files.

**Why it is wrong:** JPEG Quality 12 produces files 3-5x larger than Quality 10 with virtually no visible improvement. The quality difference between 95% and 100% is not perceptible in photographic images, but the file size difference is significant.

**The fix:** For web delivery, JPEG quality 8-10 (Save dialog) or 75-85% (Export As / Save for Web) provides the best balance of quality and file size. Save at maximum quality only when the JPEG will undergo further editing or processing.

### 7. Resampling in the wrong order

**The mistake:** Applying output sharpening and then resampling. Or converting to 8-bit and then making tonal adjustments.

**Why it is wrong:** Resampling after sharpening will soften the sharpening effect (if downsampling) or amplify sharpening artifacts (if upsampling). Converting to 8-bit before tonal adjustments increases the risk of posterization and banding.

**The fix:** Follow the correct order: flatten, color space conversion, bit depth reduction, resample, sharpen, save. (The exact order of color space and resample can be debated, but sharpening is always last before save.)

### 8. Stripping the ICC profile from output files

**The mistake:** Unchecking "Embed Color Profile" to save a few kilobytes.

**Why it is wrong:** Without an embedded profile, the receiving application must guess the color space. Most will assume sRGB — which is correct only if the image is actually sRGB. If it is Adobe RGB without a profile, the colors will be interpreted as sRGB and displayed incorrectly.

**The fix:** Always embed the ICC profile. The overhead is 3-4 KB for sRGB — negligible compared to the image data.

---

## Summary

Output preparation is the bridge between your creative work and the viewer's experience. It consists of three interdependent operations: resampling (matching pixel count to the output), output sharpening (compensating for the softening characteristics of the output medium), and format conversion (choosing the file format, color space, and bit depth that the output requires).

The master file is never modified for output. All output preparation happens on duplicated, flattened derivatives. This preserves the master for future output to any medium.

Key principles:

- **Interpolation creates pixels, not detail.** Upsampling produces plausible results but cannot substitute for capturing at the needed resolution.
- **PPI is a property of the image; DPI is a property of the printer.** Set PPI for the viewing conditions; let the printer handle DPI.
- **Output sharpening is medium-specific.** Matte paper needs more sharpening than glossy. Screen needs different settings than print. Apply it last, on the output copy.
- **sRGB for web, always.** Convert and embed the profile. No exceptions for standard web delivery.
- **JPEG quality has diminishing returns.** Quality 80-85% is the sweet spot for photographic web delivery. Going higher gains almost nothing visible; going lower degrades quickly.
- **The correct order matters.** Flatten, convert color space, reduce bit depth, resample, sharpen, save.

---

## Exercises

### Exercise 1: Resizing Without Resampling

Open a high-resolution image (4000+ pixels on the longest edge). Open Image > Image Size and uncheck "Resample." Change the Resolution from 300 PPI to 72 PPI. Observe how the Width and Height (in inches) change while the pixel dimensions remain constant. Then change the Resolution to 600 PPI. Again note the physical dimensions. Understand that you have changed nothing about the image data — only the metadata that describes how many pixels map to an inch.

### Exercise 2: Interpolation Comparison

Open a detailed photograph (landscape with fine texture). Crop a 500 x 500 pixel section that includes both fine detail and a smooth gradient area. Duplicate the cropped image four times. Resample each duplicate to 2000 x 2000 pixels using a different algorithm: Nearest Neighbor, Bilinear, Bicubic Smoother, and Preserve Details 2.0. Place all four results side by side at 100% zoom and compare: edge sharpness, texture plausibility, gradient smoothness, and artifact visibility. Document which algorithm you would choose and why.

### Exercise 3: JPEG Quality Ladder

Take a well-exposed photograph with both smooth gradients (sky) and fine texture (foliage). Export to JPEG at quality levels 100%, 85%, 70%, 55%, and 40% using Save for Web (Legacy). Compare the resulting file sizes. At 100% zoom, examine the sky gradient and foliage texture at each quality level. Identify the quality level at which artifacts first become visible, and the quality level at which artifacts become objectionable. Record the file sizes at each step to understand the quality-vs-size tradeoff.

### Exercise 4: Output Sharpening for Screen vs Print

Open a portrait or landscape photograph. Duplicate it twice. Resample both duplicates to 1600 pixels on the longest edge. Apply screen output sharpening to one (USM: Amount 60%, Radius 0.4 px, Threshold 0) and hypothetical matte print output sharpening to the other (USM: Amount 150%, Radius 1.0 px, Threshold 0). View both at 100% zoom. The print-sharpened version should look noticeably oversharpened on screen — this is correct and expected. If you have access to a printer, print both on matte paper and observe which looks correct in print.

### Exercise 5: Color Space Conversion Verification

Open an image in Adobe RGB or ProPhoto RGB that contains saturated colors (flowers, sunset, or neon signage). Duplicate the image. Convert the duplicate to sRGB (Edit > Convert to Profile). Toggle between the two images and identify areas where the conversion caused visible color shifts. Use Window > Arrange > 2-up Vertical to view them side by side. Understand that these shifts represent the gamut limitations of sRGB.

---

## Advanced Exercises

### Advanced Exercise 1: Multi-Output Batch Workflow

Take a set of five finished master images. Prepare each for three outputs: (a) 16 x 20 inch glossy print at 300 PPI as 8-bit Adobe RGB TIFF, (b) 2048 px longest edge web gallery JPEG at quality 80% in sRGB, and (c) 800 px longest edge social media JPEG at quality 75% in sRGB. Create a Photoshop Action that automates the web gallery export (steps: flatten, convert to sRGB, convert to 8-bit, resize to 2048 px longest edge with Bicubic Sharper, apply USM 60/0.4/0, save JPEG at 80%). Run the action on all five images via File > Automate > Batch.

### Advanced Exercise 2: Upsampling Stress Test

Take a 12-megapixel image (4000 x 3000 pixels) and upsample it to 300% using three different methods: (a) Bicubic Smoother, (b) Preserve Details 2.0, and (c) stepwise upsampling — three consecutive 150% enlargements using Bicubic Smoother. Compare the three results at 100% zoom. Which method preserves the most convincing texture? Which shows the most artifacts? Is stepwise upsampling still superior, or has Preserve Details 2.0 made it obsolete? Write down your conclusions.

### Advanced Exercise 3: Generational JPEG Loss Documentation

Start with a 16-bit TIFF of a smooth gradient (use the Gradient tool to create one if needed). Save it as JPEG at Quality 8 (out of 12). Open the JPEG. Save it as JPEG at Quality 8 again. Repeat for 10 generations. Compare the 1st, 3rd, 5th, and 10th generation files at 100% zoom. Document where artifacts first appear and how they compound. This exercise viscerally demonstrates why JPEG is never a working format.

---

## Blackbelt Challenge

### Challenge: Gallery Exhibition Output Suite

You are preparing a solo exhibition with these requirements:

1. **Six prints:** Three at 16 x 24 inches on matte cotton rag, three at 24 x 36 inches on glossy baryta. The lab requires 16-bit Adobe RGB TIFF files at 300 PPI with no output sharpening (they apply their own profiles and sharpening — this is common with high-end fine art labs).

2. **Exhibition website:** Each image appears in a gallery page at 2000 px longest edge (JPEG, sRGB, optimized for fast loading — target under 400 KB per image) and a detail view at 4000 px longest edge (JPEG, sRGB, quality 90% for maximum detail).

3. **Press kit:** Three selected images at 3000 px longest edge, CMYK (US Web Coated SWOP v2), 8-bit TIFF, for print publication.

4. **Social media announcement:** One image at 1080 x 1080 (square crop), JPEG, sRGB, under 200 KB.

From six master files, you will produce a total of 22 output files (6 print TIFFs, 6 web gallery JPEGs, 6 detail view JPEGs, 3 press TIFFs, 1 social media JPEG).

**Requirements:**

- Create a documented folder structure for the output files.
- Build Photoshop Actions to automate the repeatable steps.
- For the gallery JPEGs (2000 px), use Save for Web to compare quality settings and find the highest quality that meets the 400 KB target for each image.
- For the press kit CMYK conversion, use Relative Colorimetric intent and evaluate the conversion for each image — flag any images where the RGB-to-CMYK conversion causes unacceptable color shifts and note which colors are affected.
- Verify every output file: correct dimensions, correct color space, correct bit depth, embedded ICC profile, and acceptable visual quality.
- Document your sharpening decisions for each output (print TIFFs excluded — the lab handles sharpening). Explain why you chose the specific USM values for the 2000 px gallery images vs. the 4000 px detail images vs. the 1080 px social media crop.

This challenge tests your ability to manage a real-world output matrix, balancing quality, efficiency, and format-specific requirements across multiple delivery targets from a single set of master files.
