---
description: Comprehensive image upscaling reference for print production — AI-generated art, scanned artwork, digital photography. Tool selection, model comparison, scanning challenges, POD optimization.
---

# Image Upscaling & Print Production Guide

## Overview

This guide covers the complete workflow of upscaling images to print-ready resolution. It addresses three primary source types — AI-generated art, scanned physical artwork, and digital photography — with specific recommendations for each.

**Goal:** Produce the highest quality, most natural-looking enlarged image for fine art printing and Print on Demand (POD) production.

**Philosophy:** Upscaling is not magic. It works best when the source image is already high quality. The upscaler's job is to enlarge without introducing visible artifacts — not to invent detail that was never there.

---

## Resolution Fundamentals

### DPI, Pixels, and Print Size

**DPI (Dots Per Inch)** describes how many pixels fit into one inch of printed output. The same pixel count prints larger at lower DPI and smaller at higher DPI.

**Formula:** `Required Pixels = Print Size (inches) x DPI`

### Minimum Resolution by Print Type

| Print Type | Minimum DPI | Notes |
|---|---|---|
| Fine art paper print | 300 | Viewed up close — maximum detail needed |
| Canvas gallery wrap | 150–200 | Canvas texture hides minor imperfections |
| Large format poster (> A2) | 150–225 | Viewed from distance |
| Metal / sublimation print | 300 | High-contrast medium, detail matters |
| DTG garment (t-shirt) | 150–300 | Depends on platform and design complexity |
| Billboard / banner | 20–72 | Viewed from several meters |

### Common Print Size Pixel Requirements (300 DPI)

| Print Size (inches) | Print Size (cm) | Pixels Required |
|---|---|---|
| 8 x 10 | 20 x 25 | 2 400 x 3 000 |
| 11 x 14 | 28 x 36 | 3 300 x 4 200 |
| 16 x 20 | 41 x 51 | 4 800 x 6 000 |
| 18 x 24 | 46 x 61 | 5 400 x 7 200 |
| 24 x 36 | 61 x 91 | 7 200 x 10 800 |
| 30 x 40 | 76 x 102 | 9 000 x 12 000 |

### POD Platform Minimum Requirements

| Platform | Min. Resolution | Aspect Ratio | Format |
|---|---|---|---|
| Fine Art America | 6 000 px (long edge recommended) | Flexible | JPEG, PNG |
| Displate | 2 900 x 4 060 px | 1:1.4 | JPG |
| Redbubble | 4 500 x 5 400 px (poster) | Varies by product | PNG recommended |
| INPRNT | 4 000 px (short edge recommended) | Flexible | JPEG, PNG, TIFF |
| Printful (poster) | 300 DPI at print size | Varies | PNG recommended |

> **Note:** Platform requirements change. Always verify current specs on the platform's upload page before preparing files.

---

## Source Types and Their Characteristics

### AI-Generated Images

**Typical native resolution:**

| Tool | Native Output |
|---|---|
| DALL-E 3 (ChatGPT) | 1 024 x 1 024 or 1 024 x 1 792 |
| Stable Diffusion (SD 1.5) | 512 x 512 (optimal) |
| Stable Diffusion XL (SDXL) | 1 024 x 1 024 (optimal) |
| Midjourney v6+ | Up to 2 048 x 2 048 |

**Upscaling need:** Almost always required for print. A 1 024 x 1 024 image at 300 DPI prints only 3.4 x 3.4 inches (8.7 x 8.7 cm) — too small for most products.

**Advantages for upscaling:**

- Clean digital source — no film grain, sensor noise, or scanning artifacts
- Colors stay within sRGB gamut — no out-of-gamut surprises
- No specular reflection or physical texture issues
- Consistent quality across the image

**Watch for:**

- AI artifacts in hands, text, fine geometric patterns
- Inconsistent fine detail that upscaling may amplify
- Some AI generators apply JPEG compression — save/download in highest quality

### Scanned Physical Artwork

**Typical resolution:** 600–1 200 DPI from a flatbed scanner (e.g., Epson V600)

**Upscaling need:** Usually minimal if scanned at 600+ DPI. A 600 DPI scan of an A4 original (8.3 x 11.7 inches) produces a 4 980 x 7 020 pixel image — sufficient for fine art prints up to approximately 16 x 23 inches at 300 DPI without upscaling.

**Challenges:**

- Scanner light direction creates directional artifacts on textured surfaces
- Specular reflection from glossy/metallic media (gel pens, metallic inks)
- Dust, hair, and surface debris appear as bright spots on dark media
- Auto-exposure may struggle with predominantly dark artwork

See the dedicated [Scanning Artwork on Dark Surfaces](#scanning-artwork-on-dark-surfaces) section below.

### Digital Photographs

**Typical resolution:** 12–60+ megapixels depending on camera

**Upscaling need:** Rarely needed for the full image — more commonly needed when cropping significantly reduces usable resolution.

**Watch for:**

- Sensor noise (especially high ISO) — upscaling amplifies noise
- Lens softness at edges — upscaling cannot recover true detail
- JPEG compression artifacts from in-camera processing
- Motion blur — upscaling sharpens edges but cannot undo motion blur

### Phone Photos of Physical Artwork

**Typical resolution:** 12–200 MP (modern smartphones)

**This is the worst source type for reproduction.** Problems include:

- Uneven lighting and shadows
- Perspective distortion (keystoning)
- Color temperature inconsistency
- Lens distortion at edges
- Reflection from glossy surfaces
- Compression artifacts

**Recommendation:** Always use a flatbed scanner for flat artwork. Reserve phone photography only for initial concept documentation or when the artwork exceeds the scanner bed.

---

## Upscaling Tools

### Upscayl (Recommended Primary Tool)

**What:** Free, open-source, GPU-accelerated image upscaler using Real-ESRGAN and related models.

**Why recommended:**

- Completely free and open-source
- Runs locally — nothing leaves the machine
- GPU-accelerated via Vulkan (works with NVIDIA, AMD, Intel GPUs)
- Multiple AI models included
- Simple interface for quick processing
- PNG output (lossless) by default

**System Requirements:**

| Component | Minimum | Recommended |
|---|---|---|
| GPU | Vulkan-compatible (GTX 1060 / RX 580) | GTX 1070+ / RTX series |
| VRAM | 4 GB | 6–8 GB |
| RAM | 8 GB | 16 GB |
| Storage | ~500 MB for app + models | SSD recommended |

**Key Settings:**

| Setting | Recommendation | Notes |
|---|---|---|
| Output Format | PNG | Lossless — always use for intermediate files |
| Compression | 0 | Default; lossless PNG compression |
| Scale Factor | 4x | Maximum useful single-pass factor |
| Tile Size | 512 (default) | Reduce to 256 or 128 if VRAM errors occur |
| Double Upscayl | Use cautiously | Chains two 4x passes (= 16x total); inspect at 100% zoom |

**Practical Limits:**

- 4x is the sweet spot for most upscaling tasks
- Beyond 4x (using Double Upscayl), the AI begins inventing detail — results become speculative
- Always inspect the output at 100% zoom before committing to production
- Large source images (8 000+ px) may cause VRAM overflow — reduce tile size or process in sections

### ComfyUI Integrated Upscaling

For users already working in ComfyUI for image generation, upscaling can be integrated directly into the generation workflow.

**Core Nodes:**

| Node | Purpose |
|---|---|
| **UpscaleModelLoader** | Loads an ESRGAN model from `models/upscale_models/` |
| **ImageUpscaleWithModel** | Applies the loaded model to upscale an image |
| **Ultimate SD Upscale** (custom node) | Tile-based upscaling with optional img2img refinement |

**Model Installation:** Place `.pth` model files in `ComfyUI/models/upscale_models/`. Models are available from [OpenModelDB](https://openmodeldb.info/) and [Civitai](https://civitai.com/).

**Two Approaches:**

**1. Simple Model Upscale (non-generative):**

- Load model → feed image → get upscaled result
- Fast, deterministic, same result every time
- No KSampler needed
- Best for: final production upscaling of a finished image

**2. Generative Tiled Upscale (img2img refinement):**

- Upscales with ESRGAN, then runs a tiled img2img pass to add AI-generated detail
- Uses KSampler + ControlNet Tile for fidelity
- Slower, non-deterministic (varies between runs)
- **Denoise:** 0.3–0.5 (lower = more faithful to original; higher = more creative freedom)
- Best for: adding fine detail to images that lack texture at higher resolution

> **Caution:** Generative upscaling changes the image. New detail is invented by the model, not recovered from the source. Always compare with a non-generative upscale to verify the result is acceptable.

### Other Notable Tools

| Tool | Type | Cost | Notes |
|---|---|---|---|
| **Topaz Gigapixel AI** | Commercial, local | ~$199 (one-time) | Widely regarded as high quality; proprietary models |
| **Let's Enhance** | Cloud-based | From $9/month | Convenient; images leave your machine |
| **GIMP + plugins** | Open-source | Free | Limited AI upscaling capabilities |
| **ImageMagick** | CLI | Free | Non-AI; bicubic/Lanczos only — useful for precise resizing, not AI upscaling |
| **Waifu2x** | Open-source | Free | Anime/illustration focused; predecessor to modern ESRGAN models |

> **Note on cloud tools:** Any cloud-based upscaling service processes the image on their servers. For unpublished or sensitive artwork, local processing (Upscayl, ComfyUI) is preferable.

---

## Model Selection

### Upscayl Models — Detailed Comparison

| Model | Best For | Characteristics | Watch For |
|---|---|---|---|
| **Real-ESRGAN (General Photo)** | Photographs, mixed real-world images | Balanced; trained on realistic degradation patterns | May smooth fine artistic textures |
| **Real-ESRGAN (Fast)** | Quick previews, batch processing | Faster but lower quality than standard | Not for final production |
| **4x-UltraSharp** | AI-generated art, illustrations, text | Aggressive detail reconstruction; sharpest edges | Can over-sharpen; may create artifacts on organic textures (grass, skin) |
| **Remacri** | Best all-rounder; mixed content | Balanced detail vs. artifact avoidance; good text/screenshot handling | Slightly less sharp than UltraSharp on clean sources |
| **Ultramix Balanced** | Urban scenes, architecture | Good structural detail preservation | Significantly slower (5–6x) than other models |
| **x4plus-anime** | Anime, manga, cel-shaded illustration | Clean line handling, flat color region preservation | Will smear photographic skin texture |

### Model Selection Decision Tree

**Start here → What is the source image?**

1. **AI-generated illustration / digital art** → Try **4x-UltraSharp** first. If over-sharpened → **Remacri**
2. **Photograph (digital camera)** → **Real-ESRGAN** or **Remacri**
3. **Scanned line art / ink drawing** → **4x-UltraSharp**
4. **Scanned painting / textured artwork** → **Remacri** (preserves texture better)
5. **Anime / manga / flat-color illustration** → **x4plus-anime**
6. **Urban / architectural photo** → **Ultramix Balanced** (if time permits)
7. **Mixed content / unsure** → **Remacri** (safest default)

### Advanced: Model Chaining

Some workflows chain two different models for potentially better results:

- **Real-ESRGAN 2x → then UltraSharp 2x** = combines ESRGAN's natural texture with UltraSharp's edge definition
- **Any model 4x → then Lanczos downscale to target size** = upscale higher than needed, then downsample for extra sharpness

> **Uncertain:** Model chaining results are subjective and content-dependent. Always A/B test against a single-pass 4x upscale before adopting a chained workflow. The quality improvement is not guaranteed and adds processing time.

---

## Scanning Artwork on Dark Surfaces

This section addresses the specific challenge of scanning artwork created on **black cardboard or paper** using **gel pens, metallic inks, colored pencils, or markers** — common in certain illustration styles.

### Why Dark-Surface Artwork Is Difficult to Scan

**1. Specular Reflection**

Gel pens and metallic inks have glossy, reflective surfaces. A flatbed scanner's light source hits the artwork at a fixed angle. Where the ink's surface angle aligns with the light-sensor path, the scanner captures a **bright specular highlight** instead of the actual color. This appears as white or washed-out spots in the scan.

**2. Auto-Exposure Confusion**

Most scanner software auto-exposure algorithms assume a predominantly light image. A mostly-black artwork causes the software to dramatically increase exposure, blowing out the highlights (the actual artwork) and lifting the black background to noisy grey.

**3. Scanner Lid Reflection**

The standard white scanner lid reflects light back through the artwork. On dark paper, this is usually not a problem (unlike with film scanning), but on semi-transparent or thin dark paper, it can contaminate the black with a milky appearance.

### Scanner Settings for Dark Artwork

**Use Professional / Manual Mode** — never Auto mode.

| Setting | Value | Reason |
|---|---|---|
| Document Type | Reflective | Standard for opaque artwork |
| Image Type | 48-bit Color | Maximum color depth for post-processing latitude |
| Resolution | 600 DPI | Sufficient for most reproduction; 1 200 DPI only if final print will be significantly larger than the original |
| Color Restoration | OFF | Designed for faded photos — will distort intentional color choices |
| Dust Removal (ICE) | OFF | May remove intentional fine marks; clean manually in post |
| Unsharp Mask | OFF | Apply sharpening in post-processing where you have more control |
| Auto Exposure | OFF | Set manually to prevent black level corruption |
| Backlight Correction | OFF | Not applicable to opaque artwork |

**Manual Exposure Adjustment:**

- Set the black point so that the cardboard background is deep black but not clipped to pure #000000 — you want to preserve the paper's subtle texture
- Set the white point / highlight so that the brightest gel pen strokes are captured without clipping

### Techniques for Reducing Specular Reflection

**1. Cork Buffer Method (Most Effective for Flatbed)**

Place thin cork strips (1–2 mm) at the corners between the artwork and the scanner glass. This creates a small air gap that changes the angle of light interaction, reducing direct specular reflection from glossy media.

> **Source:** [Muddy Colors — A Quick Trick for Scanning Textured Surfaces](https://muddycolors.com/2022/01/a-quick-trick-for-scanning-textured-surfaces/)

**2. Black Backing Instead of White Lid**

Replace or cover the scanner's white lid with matte black paper or card. This eliminates reflected light bouncing back through the glass from the lid, reducing overall light contamination on dark artwork.

**3. Multi-Pass Scanning (180° Rotation)**

1. Scan the artwork in its normal orientation
2. Rotate the artwork 180° on the scanner bed
3. Scan again with identical settings
4. In Photoshop: layer both scans, rotate one 180° to align, then blend (use lighter/darker blending or manual masking) to average out directional lighting artifacts

> **Source:** [Muddy Colors — How I Scan and Assemble My Work](https://muddycolors.com/2021/03/how-i-scan-and-assemble-my-work/)

**4. Multi-Pass for Metallic Media**

For artwork with both matte and metallic/glossy elements:

1. Scan once with standard exposure (optimized for matte areas)
2. Scan again with reduced exposure (optimized for metallic highlights)
3. Combine in Photoshop using layer masks — matte areas from scan 1, metallic areas from scan 2

**5. Photography Alternative (For Highly Reflective Media)**

When scanning cannot capture metallic effects:

- Set up two lights at 45° angles to the artwork
- Use polarizing filters on both lights AND the camera lens (cross-polarization)
- This eliminates specular highlights while preserving color
- Requires careful color calibration against a reference target

> **Note:** Cross-polarization photography requires specialized equipment (polarizing gels for lights, CPL filter for camera) and is more complex than scanning. Only necessary when flatbed scanning consistently fails to capture metallic effects.

### Physical Preparation Before Scanning

1. Allow ink/paint to fully cure (gel pens: 24+ hours for metallic varieties)
2. Clean scanner glass with lint-free microfiber cloth and electronics-safe glass cleaner
3. Wipe artwork gently with anti-static cloth to remove dust and fibers
4. On black paper/card, every speck of dust appears as a bright white dot — thorough cleaning is critical
5. Handle artwork by edges only — fingerprints show on both the glass and glossy ink surfaces

### Post-Scan Processing

| Step | Tool | Notes |
|---|---|---|
| Dust/debris removal | Photoshop — Healing Brush, Clone Stamp | Manual removal; auto dust removal may damage fine lines |
| Black level adjustment | Photoshop — Levels / Curves | Bring background to deep black while preserving paper texture |
| Color correction | Photoshop — Curves per channel | Correct any color cast from scanner light source |
| Sharpening | Photoshop — Unsharp Mask or Smart Sharpen | Apply at final output resolution, not before upscaling |
| Save | TIFF 16-bit or PNG | Lossless format for archival master file |

---

## Print-Specific Optimization

### Color Space Considerations

| Workflow Stage | Color Space | Notes |
|---|---|---|
| AI image generation | sRGB | All major AI generators output sRGB |
| Scanning (archival) | Adobe RGB (if scanner supports) | Wider gamut preserves more color data |
| Editing / post-processing | Adobe RGB or ProPhoto RGB | Edit in the widest space practical |
| Upload to POD platform | **sRGB** | Most POD platforms expect sRGB; they handle CMYK conversion internally |
| Upload to professional print lab | Check lab requirements | Some accept Adobe RGB or ICC-profiled TIFF |

> **Important:** Do NOT manually convert to CMYK for POD platforms. Their RIP (Raster Image Processor) software uses custom ICC profiles calibrated to their specific printers and papers. Your home CMYK conversion will be less accurate than theirs.

### Gamut Limitations — What Cannot Be Printed

Certain colors visible on screen or in physical media **cannot be reproduced in print:**

| Medium | Gamut Issue |
|---|---|
| Neon / fluorescent gel pens | Colors exceed sRGB and CMYK gamut — will appear duller in print |
| Metallic inks (gold, silver) | Reflective quality cannot be reproduced with standard CMYK ink on paper |
| Holographic / iridescent media | Angle-dependent color impossible in static print |
| Extreme saturated colors (electric blue, hot pink) | sRGB can display these; CMYK often cannot match |

**Compensation strategies:**

- Increase Vibrance +5–10% in post-processing to compensate for print dullness
- Consider metallic paper or metal substrate prints for artwork originally created with metallic media
- Accept that some physical media effects are inherently non-reproducible — communicate this clearly in product descriptions if necessary

### Optimization by Print Medium

#### Paper Prints (Giclée, Fine Art)

- **Paper choice matters:** Matte / rag paper absorbs light → deep, bársonyos fekete effect
- **Avoid glossy paper for predominantly dark images:** High ink coverage on glossy paper causes visible print head banding, fingerprint collection, and distracting reflections
- **Heavy paper (250+ g/m²):** Required for high ink coverage — prevents warping/cockling
- Recommended paper types for dark artwork: Somerset Velvet, Hahnemühle Photo Rag, Canson Infinity Rag Photographique

> **Note:** Paper choice is only relevant when printing yourself or using a custom print lab. POD platforms select their own paper stock; your control is limited to choosing between "matte" and "glossy" options (always choose matte for dark backgrounds).

#### Metal Prints (Sublimation)

Metal prints are **ideal for dark-background artwork** because:

- Sublimation dye is infused into the metal coating under heat — no ink sitting on the surface
- The metal substrate itself provides depth and luminosity
- No ink saturation / warping problems
- The reflective metal surface naturally reproduces the "glow" effect of bright colors on dark backgrounds
- Durable, scratch-resistant, waterproof

**Technical requirements:**

- sRGB color space
- 300 DPI at target print size
- JPEG or PNG (check platform requirements)

#### Canvas Prints

- Canvas texture naturally hides minor upscaling artifacts and printer imperfections
- 150–200 DPI is generally acceptable for canvas (lower than paper)
- Gallery wrap requires extra image area for the edges (typically 1.5–2 inches per side)
- Dark backgrounds on canvas can appear slightly less deep than on paper or metal

#### Garments (DTG — Direct to Garment)

For dark-background artwork on garments:

- **Use transparent PNG** — remove the black background entirely
- The garment fabric provides the "black background"
- This avoids the printer applying a thick ink layer for the background → no rubbery/stiff feel
- If the background contains intentional texture/pattern (not solid black), leave it — but the garment should be black

**Caution:** If a solid digital black (#000000) background is left in the file and printed on a black garment, the printer will still lay down black ink on black fabric. The result is a visible, slightly different-shade rectangle with a stiffer texture that cracks after washing.

---

## Quality Verification Checklist

After upscaling and before uploading to any platform:

| Check | How | Pass Criteria |
|---|---|---|
| **100% zoom inspection** | View at actual pixels in Photoshop | No visible artifacts, halos, or "plastic" smoothing |
| **Edge quality** | Zoom into sharp edges, text, fine lines | Edges are clean without ringing or over-sharpening |
| **Texture preservation** | Compare with source at similar zoom | Paper grain, brush strokes, pencil texture maintained (not smoothed away) |
| **Background uniformity** | Inspect dark/solid areas at 100% | No introduced noise, pattern artifacts, or color banding |
| **Color accuracy** | Compare with source side by side | No color shift introduced by upscaling model |
| **Resolution** | Check pixel dimensions and DPI | Meets target platform's minimum requirements |
| **File format** | Verify output format | PNG for intermediate; platform-specific format for upload |
| **File size** | Check file size is reasonable | Unusually small may indicate compression; unusually large may cause upload issues |

### Test Print Protocol

**Always order a proof before listing a product for sale.**

1. Order the smallest/cheapest size available on the platform
2. Choose the same paper/material type as the production listing
3. Evaluate in person: check blacks, color accuracy, sharpness, texture
4. View at intended hanging/viewing distance
5. If unsatisfactory, adjust and re-proof before listing

---

## Common Pitfalls

| Mistake | Why It Happens | Solution |
|---|---|---|
| Upscaling a JPEG multiple times | Each save adds compression artifacts that compound | Always work from the original source; upscale once from the best version |
| Using the wrong model | Applying a photo model to illustrations (or vice versa) | Match model to content type — see Model Selection section |
| Upscaling too aggressively | 16x upscale on a low-res source | Maximum 4x in a single pass; beyond that, quality degrades rapidly |
| Ignoring 100% zoom check | Thumbnail looks fine but actual pixels show artifacts | Always inspect at 100% before committing |
| Not ordering a test print | Screen and print differ — always | Budget for test prints as part of the production cost |
| Manual CMYK conversion for POD | Trying to control what the printer does | Upload sRGB; let the platform handle conversion |
| Leaving solid black BG on garments | Assumes the printer "knows" the garment is black | Export transparent PNG for dark garments |
| Over-sharpening after upscaling | Stacking upscaler sharpening with post-process sharpening | Apply sharpening only once, at the final step, at output resolution |
| Scanning at maximum DPI "just in case" | 2 400+ DPI scans are enormous with no benefit for reflective media | 600 DPI is sufficient for most artwork; 1 200 DPI maximum for paper originals |

---

## Quick Reference: Complete Workflow

### AI-Generated Art → Print

| Step | Tool | Details |
|---|---|---|
| 1. Generate image | Dall-E / ComfyUI + SDXL | Request highest available resolution; specify desired textures in prompt |
| 2. Quality check | Photoshop | Fix AI artifacts (hands, text, geometric errors) |
| 3. Upscale | Upscayl — **4x-UltraSharp** or **Remacri** | 4x factor; inspect at 100% |
| 4. Color adjust | Photoshop — Vibrance +5-10% | Compensate for print dullness (optional, image-dependent) |
| 5. Prepare variants | Photoshop | Full background PNG (wall art) + transparent PNG (garments) |
| 6. Export | PNG, sRGB | Verify pixel dimensions meet platform requirements |
| 7. Upload + test print | Platform | Always proof before listing |

### Scanned Artwork → Print

| Step | Tool | Details |
|---|---|---|
| 1. Prepare & scan | Flatbed scanner, Professional Mode | 600 DPI, 48-bit color, all auto corrections OFF |
| 2. Clean | Photoshop — Healing Brush | Remove dust, debris; preserve intentional texture |
| 3. Levels/Curves | Photoshop | Set black and white points; correct color cast |
| 4. Upscale (if needed) | Upscayl — **Remacri** or **UltraSharp** | Only if print size significantly exceeds scan resolution |
| 5. Sharpen | Photoshop — Unsharp Mask | Apply once at final resolution |
| 6. Export | TIFF 16-bit (archival) + PNG (upload) | sRGB for POD; Adobe RGB for custom lab |
| 7. Upload + test print | Platform | Always proof before listing |

### Digital Photo → Print

| Step | Tool | Details |
|---|---|---|
| 1. RAW develop | RawTherapee / Lightroom | Export at maximum resolution, 16-bit TIFF |
| 2. Retouch | Photoshop | Standard post-processing |
| 3. Upscale (if needed) | Upscayl — **Real-ESRGAN** or **Remacri** | Only if heavy crop reduced resolution below target |
| 4. Sharpen | Photoshop | Output sharpening for print |
| 5. Export | TIFF (archival) + JPEG/PNG (upload) | sRGB for POD |
| 6. Upload + test print | Platform | Always proof before listing |

---

## Appendix: Pixel Dimension Calculator

To calculate required pixel dimensions for any print size:

**Pixels = inches x DPI**

| Target | At 150 DPI | At 300 DPI |
|---|---|---|
| 5 x 7" | 750 x 1 050 | 1 500 x 2 100 |
| 8 x 10" | 1 200 x 1 500 | 2 400 x 3 000 |
| 11 x 14" | 1 650 x 2 100 | 3 300 x 4 200 |
| 16 x 20" | 2 400 x 3 000 | 4 800 x 6 000 |
| 18 x 24" | 2 700 x 3 600 | 5 400 x 7 200 |
| 20 x 30" | 3 000 x 4 500 | 6 000 x 9 000 |
| 24 x 36" | 3 600 x 5 400 | 7 200 x 10 800 |
| 30 x 40" | 4 500 x 6 000 | 9 000 x 12 000 |
| 40 x 60" | 6 000 x 9 000 | 12 000 x 18 000 |

**For metric paper sizes at 300 DPI:**

| Paper Size | Dimensions (mm) | Pixels at 300 DPI |
|---|---|---|
| A5 | 148 x 210 | 1 748 x 2 480 |
| A4 | 210 x 297 | 2 480 x 3 508 |
| A3 | 297 x 420 | 3 508 x 4 961 |
| A2 | 420 x 594 | 4 961 x 7 016 |
| A1 | 594 x 841 | 7 016 x 9 933 |
| A0 | 841 x 1 189 | 9 933 x 14 043 |

---

## Sources and Further Reading

- [Upscayl — GitHub](https://github.com/upscayl/upscayl)
- [Real-ESRGAN — GitHub](https://github.com/xinntao/Real-ESRGAN)
- [OpenModelDB — Upscale Models](https://openmodeldb.info/)
- [ComfyUI Upscale Tutorial](https://docs.comfy.org/tutorials/basic/upscale)
- [Muddy Colors — Scanning Textured Surfaces](https://muddycolors.com/2022/01/a-quick-trick-for-scanning-textured-surfaces/)
- [Charlie Kirkham — Documenting Metallic Artworks](https://charliekirkham.blog/2024/02/19/documenting-metallic-artworks-how-to-scan-and-photograph-to-minimise-reflections/)
- [Canada.ca — How to Scan Reflective Objects](https://www.canada.ca/en/heritage-information-network/services/digitization/scan-reflective-objects.html)

---

*Last updated: 2026-08-11*
