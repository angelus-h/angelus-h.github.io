---
description: Complete glossary of Photoshop Blackbelt terminology.
---

# Appendix A: Glossary

This glossary serves as a quick-reference companion to the detailed terminology sections found throughout the Photoshop Blackbelt manual. Each definition includes a chapter reference in parentheses pointing to where the concept is discussed in depth. For full context, worked examples, and practical guidance, consult the referenced chapter.

---

### A

**Adjustment Layer** — A non-destructive layer that applies tonal or color corrections to all layers beneath it without permanently altering pixel data. Adjustment layers can be masked, re-edited, and stacked. (Ch 06, Ch 07)

**Alpha Channel** — A grayscale channel stored alongside the RGB channels that represents a saved selection or mask. White areas are fully selected, black areas are unselected, and gray values represent partial selection. (Ch 05)

**Anti-aliasing** — A technique that smooths the jagged edges (stair-stepping) of selections and type by introducing semi-transparent transitional pixels along boundaries. (Ch 12)

**Apply Image** — A command that blends one image's channel data with another (or the same image) using blending modes and masking. Frequently used to build luminosity masks or perform channel-based corrections. (Ch 05, Ch 13)

### B

**Bit Depth** — The number of bits used to represent each color channel per pixel. Common values are 8-bit (256 tones per channel), 16-bit (65,536 tones), and 32-bit (floating point HDR). Higher bit depth preserves more tonal information and reduces banding during heavy edits. (Ch 01)

**Black Point** — The darkest value in an image or the point on a tonal curve that maps to pure black (0). Setting the black point correctly ensures full tonal range without clipping shadow detail. (Ch 07)

**Blending Mode** — A mathematical formula that determines how a layer's pixels interact with the pixels on layers below it. Photoshop provides over 25 blending modes grouped by behavior: darken, lighten, contrast, comparative, and component. (Ch 14)

**Burning** — Selectively darkening areas of an image to add depth, direct the viewer's eye, or recover highlight detail. Named after the traditional darkroom technique of increasing exposure through an enlarger. (Ch 08)

### C

**Camera Raw** — Adobe's RAW processing engine, available both as a standalone filter (Camera Raw Filter) and as the default editor for RAW files. It provides non-destructive controls for white balance, exposure, tone curves, detail, and lens corrections before pixel-level editing in Photoshop. (Ch 03)

**Channel** — A grayscale representation of color or selection information. An RGB image has three color channels (Red, Green, Blue) plus a composite. Additional alpha channels can store masks and selections. (Ch 05)

**Chrominance Noise** — Color noise that appears as random colored specks or blotches, most visible in shadow areas of high-ISO images. It is generally easier to remove than luminance noise because it carries less meaningful image detail. (Ch 16)

**Clipping (tonal)** — The loss of detail that occurs when pixel values are pushed beyond the boundaries of the tonal range (pure black at 0 or pure white at 255 in 8-bit). Clipped tones contain no recoverable detail. (Ch 07)

**Clipping Mask** — A layer arrangement where the content of one layer is visible only through the non-transparent areas of the layer directly beneath it. The lower layer acts as a shape mask for the upper layer. (Ch 04, Ch 05)

**Clone Stamp** — A retouching tool that copies pixels from a sampled source point to the brush location, creating an exact duplicate. Unlike the Healing Brush, it does not blend tone and color with the surrounding area. (Ch 17)

**Color Cast** — An overall unwanted tint affecting an image, typically caused by incorrect white balance, mixed lighting, or lens characteristics. Color casts are most visible in neutral tones (grays, whites, skin tones). (Ch 09)

**Color Correction** — The process of adjusting an image's colors to achieve accurate, neutral reproduction that faithfully represents the original scene. Distinct from color grading, which applies a deliberate creative bias. (Ch 09)

**Color Grading** — The intentional manipulation of color for creative or narrative effect, applied after color correction. Techniques include split-toning, LUT application, and selective hue shifts. (Ch 10)

**Color Management** — The system of ICC profiles, rendering intents, and calibrated devices that ensures consistent color reproduction from capture through editing to output (screen, print, web). (Ch 02)

**Color Space** — A defined range (gamut) of colors that can be represented, along with a mathematical model for encoding them. Common working spaces include sRGB, Adobe RGB, and ProPhoto RGB, each with different gamut sizes. (Ch 02)

**Compositing** — The process of combining multiple images or elements into a single cohesive scene. Requires matching perspective, lighting, color, noise, and sharpness across all source elements. (Ch 19)

**Content-Aware Fill** — An automated fill method that analyzes surrounding pixels and synthesizes replacement content to seamlessly remove or replace selected areas. Results depend heavily on the complexity and regularity of the surrounding content. (Ch 17)

**Contrast** — The difference in luminance or color between elements in an image. Global contrast affects the overall tonal range; local contrast (midtone contrast, clarity) affects the perception of detail and dimensionality. (Ch 07, Ch 08)

**Convolution** — A mathematical operation where a kernel (small matrix of numbers) is applied to each pixel and its neighbors to produce effects such as blur, sharpen, emboss, or edge detection. All Photoshop filter operations are built on convolutions. (Ch 15)

**Curves** — The most versatile tonal adjustment tool, representing the relationship between input and output values as an editable spline. Individual channel curves enable simultaneous tonal and color adjustments. (Ch 07)

### D

**Demosaicing** — The interpolation process that reconstructs a full-color image from the mosaic of single-color values captured by a camera sensor's Bayer (or similar) filter array. Demosaicing algorithms directly affect resolution, color accuracy, and artifact levels. (Ch 01, Ch 03)

**Destructive Editing** — Any operation that permanently alters the original pixel data, making changes difficult or impossible to undo beyond the current session's history states. Contrast with non-destructive editing. (Ch 06)

**DNG** — Digital Negative. Adobe's open-standard RAW format that encapsulates RAW sensor data along with metadata and XMP settings in a single file. Some cameras shoot DNG natively; others require conversion from proprietary RAW formats. (Ch 03)

**Dodging** — Selectively lightening areas of an image to open shadows, reveal detail, or guide viewer attention. Named after the traditional darkroom technique of blocking light during printing. (Ch 08)

**Dynamic Range** — The ratio between the brightest and darkest values a sensor can capture or an image can represent. Higher dynamic range preserves more detail in both highlights and shadows simultaneously. (Ch 01, Ch 03)

### E

**Embedded Smart Object** — A Smart Object whose source data is stored within the PSD file itself. Edits to the embedded source do not affect any external file, and the PSD is self-contained. Compare with Linked Smart Object. (Ch 06)

**Exposure** — In digital imaging, the total amount of light captured by the sensor, determined by aperture, shutter speed, and ISO. In Photoshop and Camera Raw, the Exposure slider adjusts overall image brightness centered primarily on the midtones. (Ch 03, Ch 07)

### F

**Feathering** — Softening the edge of a selection or mask by creating a gradual transition from fully selected to fully unselected over a specified pixel radius. Prevents hard, visible edges when making localized adjustments or composites. (Ch 12, Ch 13)

**Fill (layer property)** — A layer opacity control that affects only the layer's own painted or placed content, without affecting layer styles (drop shadows, strokes, etc.). Differs from Opacity, which affects everything on the layer including styles. (Ch 04, Ch 14)

**Flatten** — Merging all visible layers into a single Background layer, discarding hidden layers and transparency. A destructive operation that reduces file size but eliminates all layer-based editability. (Ch 04)

**Frequency Separation** — A retouching technique that splits an image into a low-frequency layer (color and tone) and a high-frequency layer (texture and detail), allowing independent editing of each. Commonly used for skin retouching. (Ch 17)

### G

**Gamma** — A value describing the nonlinear relationship between the encoded pixel values and the actual luminance displayed on screen. Standard gamma for most displays and working spaces is approximately 2.2. (Ch 01, Ch 02)

**Gamut** — The complete range of colors that a color space, device, or medium can reproduce. Colors outside a device's gamut must be mapped to the nearest reproducible color during conversion. (Ch 02)

**Gaussian Blur** — A blur filter that applies a bell-curve (Gaussian) weighted average to pixels, producing a smooth, natural-looking softness. The radius parameter controls the extent of the blur. Foundational to many sharpening, noise reduction, and compositing techniques. (Ch 15, Ch 16)

**Generative Fill** — An AI-powered content synthesis tool (introduced in Photoshop v24.5, 2023) that generates contextually appropriate pixels within a selection based on a text prompt or surrounding content analysis. Results are placed on a new generative layer. Requires an internet connection and Adobe cloud processing. (Ch 17)

**Gradient Map** — An adjustment layer that maps an image's tonal range to a user-defined color gradient. Shadow tones receive the left color, highlights receive the right color, and midtones receive intermediate colors. Widely used for color grading and black-and-white toning. (Ch 10, Ch 18)

**Grayscale** — An image mode containing only luminance information with no color channels. A true grayscale image has a single channel. Converting to grayscale permanently discards color data unless done via a non-destructive method such as a Black & White adjustment layer. (Ch 18)

### H

**Halos** — Visible bright or dark outlines along high-contrast edges, typically caused by excessive sharpening, clarity adjustments, or HDR tone mapping. A common artifact that degrades perceived image quality. (Ch 15)

**Healing Brush** — A retouching tool that samples texture from a source area and blends it with the tone and color of the destination, producing more seamless repairs than the Clone Stamp. (Ch 17)

**Histogram** — A graph showing the distribution of pixel values across the tonal range, from shadows (left) to highlights (right). Available per-channel or as a composite luminosity display. Essential for evaluating exposure, contrast, and clipping. (Ch 01, Ch 07)

**HSL** — Hue, Saturation, Lightness. A color model that separates color into three intuitive components. HSL-based adjustments in Camera Raw and Photoshop allow targeted control of individual color ranges. (Ch 11)

**Hue** — The attribute of color that distinguishes it as red, blue, yellow, etc. Represented as an angular position (0-360 degrees) on the color wheel. (Ch 02, Ch 11)

### I

**ICC Profile** — A standardized data file (International Color Consortium specification) that describes the color characteristics of a device or color space. Profiles enable the color management system to convert colors accurately between devices. (Ch 02)

**Interpolation** — The mathematical method used to calculate new pixel values when an image is resampled (resized, rotated, or transformed). Photoshop offers several algorithms including Nearest Neighbor, Bilinear, Bicubic, and Preserve Details. (Ch 01)

### L

**Layer** — The fundamental organizational unit in Photoshop. Each layer is an independent plane that can contain pixels, adjustments, text, shapes, or smart objects. Layers stack in a defined order and can be individually controlled for visibility, opacity, blending, and masking. (Ch 04)

**Layer Mask** — A grayscale mask attached to a layer that controls the visibility of that layer's content on a pixel-by-pixel basis. White reveals, black conceals, and gray partially reveals. Entirely non-destructive and re-editable. (Ch 05)

**Levels** — A tonal adjustment that controls the black point, white point, and midtone gamma of an image. Simpler than Curves but effective for basic tonal range correction and per-channel color adjustment. (Ch 07)

**Lightness** — The perceptual brightness of a color, independent of its hue and saturation. In the Lab color model, the L channel represents lightness on a scale from 0 (black) to 100 (white). (Ch 02, Ch 11)

**Linked Smart Object** — A Smart Object whose source data resides in an external file. Changes to the external file propagate to all PSD files that reference it. Useful for multi-document workflows but creates a file dependency. (Ch 06)

**Luminance** — The measurable intensity of light from a surface. In digital imaging, often used to describe the brightness component of a pixel independent of its color information. (Ch 01, Ch 16)

**Luminosity Mask** — A selection or mask derived from the image's own luminance values, creating self-feathering selections that precisely target specific tonal ranges (shadows, midtones, highlights). Built using channel operations or dedicated panels. (Ch 13)

**LUT (Look-Up Table)** — A mathematical table that maps input color values to output color values, used to apply complex color transformations in a single operation. LUTs are commonly used for color grading and color space conversions. (Ch 10)

### M

**Mask** — Any grayscale image used to control the visibility, selection, or application area of an effect. Includes layer masks, vector masks, channel masks, clipping masks, and filter masks. (Ch 05, Ch 13)

**Midtones** — The range of tonal values between shadows and highlights, typically representing the middle portion of the histogram. Most image detail and color information resides in the midtones. (Ch 07)

**Multiply (blend mode)** — A darkening blending mode that multiplies the base color values by the blend color values and divides by 255. The result is always equal to or darker than the original. White is neutral (has no effect). Analogous to stacking two transparencies. (Ch 14)

### N

**Neural Filters** — A set of AI-powered filters in Photoshop (introduced in v22.0, 2020) that perform complex image manipulations such as skin smoothing, style transfer, colorization, and expression adjustment using trained neural networks. Some require cloud processing; availability varies by version. (Ch 17)

**Noise** — Random variation in pixel brightness or color that degrades image quality. Luminance noise appears as monochromatic grain; chrominance noise appears as colored specks. Primarily caused by high ISO settings, long exposures, and sensor heat. (Ch 16)

**Non-Destructive Editing** — A workflow philosophy that preserves the original image data by using adjustment layers, smart objects, smart filters, and masks instead of directly modifying pixels. Allows unlimited revision without quality loss. (Ch 06)

### O

**Opacity** — A layer property (0-100%) that controls the overall transparency of the layer, including its content and any applied layer styles. At 0%, the layer is completely invisible. (Ch 04, Ch 14)

**Out-of-Gamut** — Colors that exist in one color space but cannot be accurately reproduced in another. For example, highly saturated cyans in ProPhoto RGB may be out-of-gamut for sRGB. Photoshop can display gamut warnings via View > Gamut Warning. (Ch 02)

**Output Sharpening** — Sharpening applied as a final step, tailored to the specific output medium (screen, inkjet print, offset print) and viewing size. Distinct from capture sharpening and creative sharpening applied earlier in the workflow. (Ch 15)

**Overlay (blend mode)** — A contrast-increasing blending mode that applies Multiply to values darker than 50% gray and Screen to values lighter than 50% gray. Neutral gray (128) has no effect. Commonly used for dodging and burning, local contrast enhancement, and texture application. (Ch 14)

### P

**Pixel** — The smallest addressable element of a raster image, representing a single point of color defined by its channel values. Short for "picture element." (Ch 01)

**Posterization** — A visible reduction in the number of tonal steps in a gradient or smooth area, creating abrupt bands of flat color instead of smooth transitions. Typically caused by aggressive tonal manipulation in low bit-depth images. (Ch 01, Ch 07)

**ProPhoto RGB** — A very wide-gamut color space that encompasses nearly all visible colors. Recommended as a working space for high-end photography workflows to preserve the full range of camera-captured color, but requires 16-bit depth to avoid posterization. (Ch 02)

**PPI (Pixels Per Inch)** — A measure of image resolution that describes pixel density in a printed or displayed image. Higher PPI means smaller pixels and finer detail. Standard print resolution is 300 PPI; screen display varies by device. (Ch 01)

### Q

**Quick Mask** — A temporary overlay mode (activated by pressing Q) that displays a selection as a translucent colored overlay, allowing direct painting to refine the selection with brush tools. Red overlay indicates unselected areas by default. (Ch 12)

**Quick Selection** — A selection tool that uses edge detection to automatically expand a selection as the user paints, analyzing color and texture to find object boundaries. Often used as a starting point before refinement with Select and Mask. (Ch 12)

### R

**Rasterize** — Converting vector, type, or smart object data into a pixel-based (raster) layer. A destructive operation that fixes the content at the current resolution and removes the ability to re-edit the source data. (Ch 06)

**RAW File** — A minimally processed file containing the uninterpolated sensor data from a digital camera, along with metadata. RAW files preserve maximum dynamic range and color information, allowing extensive non-destructive adjustment before conversion. (Ch 03)

**Rendering Intent** — The method used by the color management system to handle out-of-gamut colors during profile conversion. The four standard intents are Perceptual, Relative Colorimetric, Absolute Colorimetric, and Saturation. Relative Colorimetric and Perceptual are most common for photographic work. (Ch 02)

**Resampling** — Changing the pixel count of an image by adding pixels (upsampling) or removing pixels (downsampling). The quality of the result depends on the interpolation algorithm used. (Ch 01)

**Resolution** — The amount of detail an image holds, expressed as pixel dimensions (e.g., 6000 x 4000) for digital display or as PPI for print output. Resolution is a fixed property of the capture; resampling changes pixel count but does not add genuine detail. (Ch 01)

**RGB** — Red, Green, Blue. An additive color model where colors are produced by combining varying intensities of red, green, and blue light. The standard color model for digital imaging and display. (Ch 01, Ch 02)

### S

**Saturation** — The intensity or purity of a color. Fully saturated colors contain no gray; desaturated colors approach neutral gray. Excessive saturation increases can push colors out of gamut and create unnatural results. (Ch 02, Ch 11)

**Screen (blend mode)** — A lightening blending mode that inverts both layers, multiplies them, and inverts the result. The effect is always equal to or lighter than the original. Black is neutral (has no effect). Analogous to projecting two slides onto the same screen. (Ch 14)

**Selection** — A defined region of pixels within an image, indicated by marching ants. Only selected pixels are affected by edits. Selections can be saved as alpha channels and converted to and from masks. (Ch 12)

**Selective Color** — An adjustment that allows targeted modification of individual color components (Cyan, Magenta, Yellow, Black) within specific color ranges (Reds, Yellows, Greens, Cyans, Blues, Magentas, Whites, Neutrals, Blacks). Based on CMYK color separation principles. (Ch 11)

**Sharpening** — The process of enhancing edge contrast to increase the perception of detail and clarity. Sharpening does not add real detail; it increases acutance (edge contrast) at tonal boundaries. Typically applied in stages: capture, creative, and output. (Ch 15)

**Smart Filter** — A filter applied to a Smart Object layer that remains non-destructive and re-editable. Smart Filters automatically include an editable filter mask and can be reordered, hidden, or deleted at any time. (Ch 06, Ch 15)

**Smart Object** — A layer that encapsulates image data (raster, vector, or RAW) in a non-destructive container. Transformations, filters, and warps applied to Smart Objects are recalculated from the original data, preventing cumulative quality loss. (Ch 06)

**Soft Light (blend mode)** — A subtler contrast blending mode similar to Overlay but with a less pronounced effect. Applies a combination of darkening and lightening based on the blend layer values. Often preferred over Overlay for gentle dodging, burning, and color grading. (Ch 14)

**Soft Proofing** — Simulating on screen how an image will appear when printed on a specific paper with a specific printer profile. Allows the photographer to identify and address out-of-gamut colors and tonal shifts before committing to print. (Ch 02)

**Spatial Frequency** — The rate of tonal change across an image. Low-frequency areas contain gradual tonal transitions (sky, skin); high-frequency areas contain rapid transitions (texture, edges, fine detail). This concept underpins frequency separation and many sharpening/noise reduction strategies. (Ch 15, Ch 17)

**sRGB** — Standard RGB. The default color space for the web and most consumer displays. It has a relatively small gamut compared to Adobe RGB or ProPhoto RGB, but ensures the widest compatibility across devices and browsers. (Ch 02)

**Stamp Visible** — A command (Ctrl/Cmd+Alt+Shift+E) that creates a new layer containing a merged copy of all currently visible layers, without flattening the layer stack. Useful for applying filters to a composite result while preserving the original layers below. (Ch 04, Ch 06)

### T

**Threshold** — An adjustment that converts an image to pure black and white based on a specified luminance value. Pixels brighter than the threshold become white; darker pixels become black. Useful as an analytical tool for finding the darkest and brightest points in an image. (Ch 07, Ch 18)

**Tonal Range** — The span of luminance values present in an image, from the darkest shadow to the brightest highlight. A well-exposed image typically uses the full tonal range without clipping at either end. (Ch 07)

**Toning** — Applying color to a grayscale or desaturated image, either uniformly or split across tonal ranges (e.g., warm shadows, cool highlights). Techniques include Gradient Map, Color Balance, and LUT application. (Ch 10, Ch 18)

**Transfer Function** — A mathematical function that describes the relationship between input and output values in a tonal adjustment. Curves adjustments are a visual representation of a transfer function. Also refers to the gamma encoding applied when writing image files. (Ch 01, Ch 07)

### U

**Unsharp Mask** — A sharpening filter that works by finding edges (areas of tonal contrast) and increasing the contrast across them. Named after the traditional darkroom technique of using a blurred (unsharp) film copy to create an edge mask. Controlled by three parameters: Amount, Radius, and Threshold. (Ch 15)

### V

**Vector Mask** — A resolution-independent mask defined by paths (Bezier curves) rather than pixels. Produces perfectly sharp, clean edges regardless of zoom level or output resolution. Ideal for geometric shapes and precise cutouts. (Ch 05, Ch 13)

**Vibrance** — An intelligent saturation control that increases the saturation of less-saturated colors more aggressively than already-saturated colors, while also protecting skin tones from over-saturation. Produces more natural results than a uniform Saturation increase. (Ch 03, Ch 11)

### W

**White Balance** — The adjustment that neutralizes color casts caused by the color temperature of the light source, ensuring that neutral objects appear truly neutral. Best set during RAW processing where it operates on unprocessed sensor data. (Ch 03, Ch 09)

**White Point** — The brightest value in an image or the point on a tonal curve that maps to pure white (255 in 8-bit). Also refers to the chromaticity of "white" in a color space or on a calibrated display (e.g., D65 = 6500K daylight). (Ch 02, Ch 07)

**Working Space** — The color space assigned to a document for editing, independent of any device profile. Common choices for photography are sRGB (web/consumer), Adobe RGB (advanced amateur/print), and ProPhoto RGB (high-end professional). Set in Edit > Color Settings. (Ch 02)

### X

**XMP Sidecar** — An XML-based metadata file (.xmp) stored alongside a RAW file, containing all Camera Raw or Lightroom adjustments, keywords, ratings, and other metadata. Allows non-destructive editing without modifying the original RAW file. DNG files can embed XMP data internally. (Ch 03)

### Z

**Zone System** — A systematic method of exposure and development control originated by Ansel Adams and Fred Archer, dividing the tonal range into eleven zones (0-X) from pure black to pure white. In digital photography, the Zone System informs deliberate exposure and tonal placement strategies using the histogram as a visual equivalent of the zone scale. (Ch 07, Ch 08, Ch 18)
