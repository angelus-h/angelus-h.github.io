---
description: Evidence-based technical guide for black-and-white film scanning with the Epson Perfection V600 Photo, digital processing in Photoshop, and Fine Art print preparation.
---

# Black & White Film Scanning with the Epson Perfection V600 Photo

**Version:** 1.0
**Created:** 2026-08-13
**Objective:** Establish a reproducible, evidence-based digital-darkroom workflow for producing archival scans and Fine Art prints from black-and-white negatives using the Epson Perfection V600 Photo.

---

## 1. Scope and Intent

This guide covers the complete workflow from film negative to Fine Art print:

**Film negative** &#8594; **Scanner** &#8594; **Archival master** &#8594; **Photoshop** &#8594; **Upscaling (if needed)** &#8594; **Color management** &#8594; **Soft proof** &#8594; **Output sharpening** &#8594; **Fine Art print**

It addresses three categories of black-and-white film:

- Traditional silver-based B&W films (Ilford HP5, Kodak Tri-X, Foma, etc.)
- Chromogenic B&W films (Ilford XP2 Super)
- Infrared-sensitive B&W films (Rollei Infrared 400, Ilford SFX 200)

Every recommendation in this guide is sourced and graded. Where claims could not be verified for the V600 specifically, this is stated explicitly. Accuracy takes priority over completeness.

---

## 2. Hardware Profile: Epson Perfection V600 Photo

### Verified Specifications

| Parameter | Value | Source |
|-----------|-------|--------|
| Sensor type | MatrixCCD, 3-line (12-line with sub-arrays), single lens | Epson official specs |
| Claimed optical resolution | 6400 x 9600 DPI | Epson official specs |
| Measured effective resolution | ~1,560 DPI (USAF 1951 target) | filmscanner.info independent test |
| Maximum interpolated resolution | 12,800 x 12,800 DPI | Epson official specs |
| Color bit depth (input/output) | 48-bit (16 bits/channel) | Epson official specs |
| Grayscale bit depth (input/output) | 16-bit | Epson official specs |
| Claimed Dmax | 3.4 | Epson official specs |
| Practical Dmax | ~3.0-3.1 (estimated) | Practitioner consensus; no published lab measurement found |
| Light source | White LED + IR LED (ReadyScan LED) | Epson official specs |
| Connection | USB 2.0 | Epson official specs |
| Transparency unit area | 2.7" x 9.5" (68.6 x 241.3 mm) | Epson official specs |
| Film holders included | 35mm strip/slide combo holder + 120/220 holder | Epson official specs |
| Digital ICE | Supported (Epson Scan only; NOT in Epson Scan 2) | Epson support documentation |

### Film Scanning Capacity Per Batch

| Format | Capacity |
|--------|----------|
| 35mm strips | 2 strips x 6 frames (12 frames total) |
| 35mm mounted slides | 4 slides |
| 120/220 medium format | Strips up to 6 x 22 cm |

Medium format frame capacity (approximate):

| Frame format | Frames per batch |
|-------------|-----------------|
| 6x4.5 | 4 |
| 6x6 | 3 |
| 6x7 | 2-3 |
| 6x9 | 2 |

### Claimed vs. Practical Performance

**FACT:** The V600 claims 6400 DPI optical resolution.

**EVIDENCE:** Independent testing by filmscanner.info using a USAF 1951 resolution target measured approximately 1,560 DPI effective resolution. DPReview corroborates this with approximately 1,575 DPI. Scanning at 3200 DPI and 6400 DPI produced identical measured resolution.

**INTERPRETATION:** The nominal resolution reflects the CCD sensor's sampling density, not the optical system's resolving power. The single-lens optical path limits actual detail capture to roughly one-quarter of the claimed figure.

**RECOMMENDATION:** Treat the V600 as a scanner with approximately 1,500-2,000 DPI of genuine resolving power. This is significantly lower than the V700/V800/V850 series (~2,300-2,400 DPI measured) and dedicated film scanners like the Plustek 8200i (~3,250 DPI measured) or Nikon Coolscan series (~3,900 DPI measured).

### Known Limitations Compared to V700/V800/V850

| Limitation | V600 | V850 Pro |
|-----------|------|----------|
| Lens system | Single lens | Dual-lens (auto-selects for media type) |
| Dmax (claimed / practical) | 3.4 / ~3.0 | 4.0 / ~3.3-3.5 |
| Effective resolution (measured) | ~1,560 DPI | ~2,300-2,400 DPI |
| Film holders | Plastic, no height adjustment, no ANR glass | ANR glass inserts, height adjustment |
| 35mm quality | ~3.6 effective megapixels | ~8 effective megapixels |

The V600 is widely regarded as adequate for medium format hobbyist-to-serious work and for 35mm web/small print use. For serious 35mm scanning, the resolution gap to dedicated film scanners is substantial.

---

## 3. Film Emulsion Classification

### Traditional Silver-Based B&W Film

The developed image consists of metallic silver nanoparticle clusters precipitated from silver halide crystals. These particles are **spectrally neutral** -- they attenuate visible light approximately equally across all wavelengths. They are also **opaque to infrared radiation**.

Examples: Ilford HP5 Plus, FP4 Plus, Pan F Plus, Delta 100/400/3200; Kodak Tri-X 400, T-Max 100/400; Fomapan 100/200/400; Rollei RPX 25/100/400.

**Scanning implications:**

- RGB channels record essentially identical density information
- Digital ICE / infrared dust removal does NOT work (silver blocks IR)
- Scanner-side or Photoshop dust removal required

### Chromogenic B&W Film

Processed in C-41 chemistry. The silver is bleached away during processing; the image is formed by **neutral-colored dyes**, identical in principle to color negative film but producing only neutral/black dyes.

Currently available: **Ilford XP2 Super** (the only remaining chromogenic B&W film in production).

Discontinued: Kodak BW400CN, Fuji Neopan 400CN, Kodak T400CN.

**Scanning implications:**

- Dyes are **transparent to infrared** -- Digital ICE / iSRD works correctly
- May have non-neutral film base (XP2 has grey/pink base; BW400CN had orange base like color negative film)
- RGB channels may capture meaningfully different information due to non-neutral dye response
- Should be scanned in **color negative mode** (not B&W negative mode) to properly handle base color
- 48-bit RGB scan recommended

### Infrared-Sensitive B&W Film

Traditional silver-halide emulsions with extended infrared sensitivity.

| Film | IR Sensitivity Range | Process |
|------|---------------------|---------|
| Rollei Infrared 400 | ~820 nm | B&W standard |
| Ilford SFX 200 | ~740 nm | B&W standard |

**Scanning implications:**

- Silver-halide image: infrared dust removal absolutely cannot be used
- Extended IR sensitivity compounds the problem -- the emulsion actively responds to the scanner's IR cleaning wavelengths
- Physical cleaning + Photoshop retouching are the only dust removal options
- Unique tonal rendering requires careful negative inversion and contrast management

---

## 4. The Scanner as Capture Device

### Capture vs. Interpretation

A fundamental distinction governs the scanning workflow:

**Scanner capture** = recording the maximum amount of information from the negative with minimal processing.

**Photographic interpretation** = transforming that information into a final image with desired tonal characteristics.

These are different operations. The scanner's job is to capture; Photoshop's job is to interpret.

### When to Process During Scanning

Some operations are better performed during scanning:

- **Negative inversion:** The scanner software applies film-specific density curves (SilverFast NegaFix, Epson Scan B&W Negative mode) that account for the film's characteristic response. These can produce a better starting point than raw inversion in Photoshop.
- **Infrared dust detection:** The IR pass happens during scanning and cannot be replicated later.
- **Multi-Exposure scanning (SilverFast):** Combines multiple exposure passes for increased dynamic range. Only possible during scanning.

### When to Postpone to Photoshop

- **Sharpening:** Scanner sharpening is destructive and non-adjustable. Photoshop provides superior control.
- **Contrast curves:** Photoshop Curves as adjustment layers are non-destructive and infinitely adjustable.
- **Dust removal (for silver B&W):** Photoshop's Healing Brush, Clone Stamp, and Remove Tool provide precision that software-based scanner filters cannot match.
- **Dodge and burn:** Cannot be done during scanning.
- **Local contrast:** Cannot be done during scanning.

### What "Flat Scanning" Means

"Scan flat" means: minimize scanner-side tonal processing to preserve the maximum information for Photoshop.

In **Epson Scan**: Set the Tone Correction curve to **Linear**. Leave Brightness/Contrast at defaults. Set Histogram output to 0-255 with input black/white points at the data edges. Disable Unsharp Mask and Grain Reduction.

In **SilverFast**: Use HDR/Archive mode (Archive Suite) for true raw scanning. In standard mode, minimize AACO and keep the gradation curve linear.

**Caution:** A completely flat scan of a negative will look unappealing -- dark, low contrast, possibly still inverted. This is expected. The visual quality should emerge during Photoshop processing, not during scanning.

---

## 5. Negative Density and Tonal Theory

### How Negatives Work

A photographic negative records light inversely: bright scene areas produce high silver density (dark on the negative); shadow areas produce low density (transparent on the negative).

| Term | Definition |
|------|-----------|
| Dmin | Minimum density -- the film base plus fog. The lightest area on a processed negative. |
| Dmax | Maximum density -- the darkest area a negative can achieve. |
| Film base | The physical support (acetate or polyester). Not perfectly clear -- may have slight color. |
| Film fog | Minimal development of unexposed silver halide. Adds to base density. |
| Density range | Dmax - Dmin. Determines how much tonal information the negative contains. |

### Scanner Dynamic Range

The V600's claimed Dmax of 3.4 means it can theoretically distinguish density values from ~0.1 (film base) to 3.4. In practice, the usable range is estimated at ~3.0-3.1 before noise degrades shadow detail.

For most B&W negatives:

- Well-exposed negatives: density range of approximately 1.0-2.5 -- well within the V600's capability
- Push-processed or high-contrast negatives: density range can exceed 3.0 -- approaching the V600's practical limits
- Zone System N+2 development: may exceed the scanner's ability to differentiate deep shadow detail

### Histogram and Tonal Placement

After scanning and inversion:

- **Black point:** The densest part of the negative becomes the lightest area of the positive image. Set the black point where shadow detail just begins.
- **White point:** The thinnest area of the negative (film base) becomes the darkest area of the positive. Set the white point at the brightest meaningful highlight.
- **Gamma/Midtone:** Controls the overall brightness distribution. For a flat archival scan, gamma 1.0 (linear) preserves the most information. For a working scan, gamma ~1.4-1.5 produces a more visually natural starting point.

### Why the Scan Need Not Look Good

The scanner's purpose is information capture, not visual presentation. A good archival scan may look flat, slightly dark, and lacking contrast. This is correct -- it means no highlight or shadow information has been clipped. The "look" of the final image is created in Photoshop through Curves, Levels, and dodge/burn.

---

## 6. 48-bit RGB vs. 16-bit Grayscale: Technical Investigation

This section investigates whether scanning B&W negatives as 48-bit RGB (the photographer's current workflow) is technically justified.

### How the V600 Handles Both Modes

**FACT:** The V600's CCD has a 12-line sensor architecture with separate R, G, B filtered rows.

**EVIDENCE:** The scan speed at 6400 DPI is identical for monochrome and color modes (21.00 msec/line, per Epson specifications). This indicates that grayscale mode still reads all three RGB-filtered rows and converts to grayscale mathematically in firmware/software. If a dedicated monochrome sensor row existed, grayscale scanning would be faster.

**INTERPRETATION:** In both RGB and Grayscale mode, the sensor captures through all three color-filtered rows. The difference is only in output: RGB outputs three 16-bit channels; Grayscale outputs a single 16-bit channel (weighted combination, likely approximately 0.30R + 0.59G + 0.11B).

### Do RGB Channels Carry Different Information from B&W Film?

#### Traditional Silver-Based Films

**EVIDENCE:** Research on the optical properties of metallic silver in photographic imaging shows that developed silver achieves broadband ("neutral") absorption across the visible spectrum through overlapping plasmon resonances. (Source: Journal of Imaging Science and Technology, peer-reviewed.)

**INTERPRETATION:** For traditional silver B&W films, the R, G, and B channels measure essentially the same density through different color filters. The channels are not independent measurements -- they are redundant representations of the same monochrome information. Any differences come from the film base color (slight pink or grey tint in acetate), not from the image itself.

**CONCLUSION:** RGB scanning of traditional silver B&W film does NOT capture additional image information. The three channels contain essentially the same data.

#### Chromogenic B&W Films (XP2 Super)

**EVIDENCE:** Chromogenic B&W films use dye-based images that are not perfectly neutral across wavelengths. XP2 Super has a grey/pink base. Practitioner testing (EarthSunFilm) found that scanning XP2 in 48-bit color produced "better results" with "more depth as well as less contrast" compared to 16-bit grayscale, and that frames which "failed to scan in B&W mode" were recoverable in color mode.

**CONCLUSION:** For chromogenic B&W films, 48-bit RGB scanning is recommended. The base color requires color data for proper handling, and the dye image may carry slightly different information across channels.

### Noise Considerations

**FACT:** If the noise in each RGB channel is uncorrelated (true for photon shot noise and random read noise), averaging three channels improves signal-to-noise ratio by sqrt(3) = approximately 1.73x (4.8 dB).

**CAVEAT:** If the scanner's internal grayscale conversion already averages all three channels (which the evidence suggests), then the noise-averaging benefit is already present in 16-bit grayscale mode. The advantage of RGB scanning would then be the ability to:

- Perform the averaging yourself with a potentially better algorithm
- Pick the single cleanest channel
- Weight channels differently based on the specific negative

### Post-Processing Advantages of RGB Scans

Even when the channels contain essentially redundant image information, 48-bit RGB provides practical flexibility:

| Advantage | Explanation |
|-----------|-------------|
| Channel selection | View R, G, B separately and select the cleanest one. The green channel often has the best sharpness on CCD sensors. |
| Channel Mixer | Create custom blends simulating colored contrast filters (red for dramatic skies, green for natural rendering). |
| Dust visibility | Dust and scratches may appear with different intensity in different channels. The blue channel typically shows most defects; the green channel often shows the least. |
| Simulated filtration | Adjust channel contributions after the fact to simulate shooting with yellow, orange, or red filters. |
| Archival flexibility | Future processing techniques may benefit from having all three channels available. |

**Source:** Peachpit / Katrin Eismann (recognized restoration specialist): "Scan black and white or monochromatic images in RGB mode, and then extract the cleanest and best channel to work on."

### File Size Impact

| Film Format | 48-bit RGB TIFF | 16-bit Grayscale TIFF | Ratio |
|------------|----------------|----------------------|-------|
| 35mm at 3200 DPI | ~78 MB | ~26 MB | 3:1 |
| 6x6 at 3200 DPI | ~284 MB | ~94 MB | 3:1 |
| 6x7 at 3200 DPI | ~356 MB | ~118 MB | 3:1 |
| Full 35mm roll (36 exp) | ~2.8 GB | ~0.9 GB | 3:1 |

### Summary: When to Use Which

| Film Type | Recommendation | Rationale |
|-----------|---------------|-----------|
| Traditional silver B&W | **16-bit Grayscale for routine work; 48-bit RGB for archival masters** | Channels are essentially redundant for image content. RGB provides post-processing flexibility (channel selection, simulated filtration) at 3x file size cost. |
| Chromogenic B&W (XP2) | **48-bit RGB recommended** | Non-neutral base and dye response benefit from color data. |
| Infrared B&W | **16-bit Grayscale or 48-bit RGB** | Same physics as traditional silver. RGB adds no image information but provides channel-selection flexibility. |

### The Photographer's Current Workflow: Assessment

The photographer currently scans all B&W negatives as 48-bit RGB. This is a defensible practice for archival work:

**Justified because:**
- Preserves maximum post-processing flexibility
- Allows channel selection and simulated filtration
- Correct approach for any chromogenic B&W film
- Provides channel-based dust analysis
- "Scan once, process many ways" philosophy

**Trade-off:**
- 3x file size with no additional captured image information for silver-based films
- For pure routine scanning of traditional B&W, 16-bit Grayscale produces equivalent final results at 1/3 the storage

**Verdict:** The 48-bit RGB workflow is technically justifiable as an archival strategy. It does not capture more optical information from silver B&W negatives, but it preserves more processing options. Whether the 3x storage cost is worth the flexibility is a workflow decision, not a quality decision.

---

## 7. 3200 DPI: Resolution Investigation

### Is 3200 DPI Rational for the V600?

**EVIDENCE (convergent from multiple independent sources):**

1. **filmscanner.info (measured):** Maximum effective resolution (~1,560 DPI) achieved at 3200 DPI scan setting. Scanning at 6400 DPI yielded identical effective resolution.
2. **Sebastian Schlueter (measured, V700):** Resolution peaked at 3200 SPI; 4800 and 6400 showed no improvement. The V600 is expected to perform slightly worse than the V700.
3. **Flickr V600 user group (measured):** USAF 1951 target tests showed "exactly the same" resolution at 3200 and 6400 DPI, with artifacts introduced at 6400.
4. **Multiple practitioner sources:** Converge on 3200 DPI as the maximum useful setting.

### Resolution at Each DPI Setting

| Scan Setting | What Happens | Verdict |
|-------------|-------------|---------|
| 2400 DPI | Captures all or nearly all real detail the optics resolve. Clean files, reasonable size. | Good practical choice |
| 3200 DPI | Slightly oversamples relative to optical limit. Captures maximum detail. May produce marginally smoother results when downsampled. | Maximum useful setting |
| 4800 DPI | No additional real detail over 3200 DPI. Larger files. May introduce slight artifacts. | Not recommended |
| 6400 DPI | No additional real detail. High-contrast edge artifacts. Massive files. Much longer scan times. | Not recommended |

### Print Size Implications

#### 35mm Film at 3200 DPI

| Parameter | Value |
|-----------|-------|
| Pixel dimensions | ~3,024 x 4,536 px (~13.7 MP nominal) |
| Genuine detail (at ~1,560 effective DPI) | ~1,474 x 2,211 px (~3.3 MP) |
| Native print at 300 PPI (from nominal pixels) | ~10 x 15 inches |
| Sharp print at 300 PPI (from genuine detail) | ~5 x 7 inches |

#### 120 Medium Format at 3200 DPI

| Format | Nominal Pixels | Genuine Detail | Native Print at 300 PPI | Sharp Print at 300 PPI |
|--------|---------------|----------------|------------------------|----------------------|
| 6x4.5 | ~4,200 x 5,300 | ~2,700 x 3,400 | 14 x 17.6" | 9 x 11.3" |
| 6x6 | ~7,040 x 7,040 | ~3,400 x 3,400 | 23.5 x 23.5" | 11.3 x 11.3" |
| 6x7 | ~7,040 x 8,832 | ~3,432 x 4,298 | 23.5 x 29.4" | 11.4 x 14.3" |

**INTERPRETATION:** The V600 captures enough genuine detail for excellent medium format prints up to ~11x14 inches and acceptable 35mm prints up to about 5x7 inches without upscaling. Beyond these sizes, the print contains interpolated pixels, which is acceptable when viewing distance increases proportionally.

### Oversampling

**The question:** Is there value in scanning above optical resolution and downsampling?

**EVIDENCE:** The V700 tests (Schlueter) showed no measurable oversampling benefit at 4800 or 6400 DPI. The V600 introduces artifacts at 6400 DPI that degrade rather than improve the image.

**RECOMMENDATION:** Scanning at 3200 DPI and downsampling to ~2400 DPI is defensible. Scanning above 3200 DPI for oversampling purposes is not justified on the V600.

### Verdict

**3200 DPI is a sensible, well-supported resolution for the V600.** It captures everything the scanner's optics can resolve, provides mild oversampling headroom, and avoids the artifacts and file bloat of higher settings. 2400 DPI is also defensible and produces smaller files with negligibly less detail.

### Aftermarket Film Holders

**EVIDENCE (practitioner reports):** The stock Epson holders are widely reported as "flimsy" and may not position film at the optimal focal distance. Aftermarket holders (Lomo Digitaliza, Better Scanning, DIY height-adjusted) can improve results meaningfully -- potentially pushing effective resolution from ~1,500 toward ~2,000 DPI. This is the single biggest variable within the user's control.

---

## 8. Digital ICE and Infrared Dust Removal

### How Infrared Dust Detection Works

The scanner has two light sources: a visible-spectrum LED and an infrared LED. During an IR-equipped scan:

1. The scanner performs one pass with visible light (capturing the image) and one pass with infrared light (capturing the defect map).
2. Color film dyes are largely **transparent to infrared light**, so the IR scan produces a nearly uniform clear image. Surface defects (dust, scratches) **block or scatter** IR light and appear as dark marks.
3. The software compares the RGB scan with the IR defect map. Where the IR channel shows defects, the algorithm inpaints the affected areas using surrounding pixel data.

### Film Type Compatibility

| Film Type | Digital ICE / iSRD | Why |
|-----------|-------------------|-----|
| Color negative (C-41) | Works correctly | Dyes transparent to IR |
| Color slide (E-6) | Works correctly | Dyes transparent to IR |
| Kodachrome | Problematic | Cyan dye layer absorbs IR |
| Chromogenic B&W (XP2 Super) | Works correctly | Dye-based image, transparent to IR |
| Traditional silver B&W | **NEVER USE** | Silver particles opaque to IR -- scanner cannot distinguish image from dust |
| Infrared B&W (Rollei IR, SFX) | **NEVER USE** | Silver + extended IR sensitivity -- worst case scenario |

### Why ICE Fails on Silver B&W Film

Metallic silver nanoparticles are opaque to infrared radiation. When the scanner performs its IR pass:

- Every silver grain in the image reflects IR light
- Dust particles on the surface also reflect IR light
- The scanner cannot distinguish between them
- The algorithm treats the entire image as one massive defect

**Result:** Severe image destruction -- blotching, smearing, detail obliteration, or near-blank output.

### Available Options by Software

#### Epson Scan (Original)

| Option | Type | Notes |
|--------|------|-------|
| Dust Removal | Software-based | Basic algorithmic detection |
| DIGITAL ICE Technology - Quality | Hardware (IR) | Full IR processing, slower |
| DIGITAL ICE Lite Technology - Speed | Hardware (IR) | Faster, less thorough |

Dust Removal and DIGITAL ICE cannot be used simultaneously.

**Critical:** Epson Scan 2 (required for macOS Catalina+) does NOT include Digital ICE. The 32-bit ICE code was never ported.

#### SilverFast

| Feature | Type | SE | SE Plus | Ai Studio |
|---------|------|-----|---------|-----------|
| SRDx | Software-only | Basic sliders | Full sliders | Full + mask/marker/eraser tools |
| iSRD | Infrared-based | Limited | Full | Full + advanced mask tools |

**SRDx** is SilverFast's software-only defect removal. It does not use IR and **works with B&W film**. SilverFast explicitly states SRDx was "developed especially for black and white film." It analyzes image data to find areas differing sharply from their surroundings, targeting elongated narrow shapes characteristic of scratches. However, practitioner experience is mixed -- SRDx can produce false positives (mistaking grain for defects), especially on high-grain films.

**iSRD** uses the same IR hardware as Digital ICE. Same film-type limitations apply.

### Recommended Dust/Scratch Workflow by Film Type

#### Traditional Silver B&W

1. **Pre-scan physical cleaning:**
   - Powder-free nitrile gloves at all times
   - Anti-static brush (Kinetronics) to dissipate static and remove loose particles
   - Rocket/bulb blower for remaining dust (avoid canned compressed air -- propellants can damage emulsion)
   - PEC-12 on PecPads only for stubborn contaminants (fingerprints, adhesive)
   - Clean scanner glass immediately before scanning

2. **During scan:** SilverFast SRDx at conservative settings (if available). Leave Epson Scan's Dust Removal and DIGITAL ICE OFF.

3. **Post-scan Photoshop retouching:** This is the primary dust removal method for silver B&W.

#### Chromogenic B&W (XP2 Super)

Digital ICE or SilverFast iSRD can be used safely. This is a significant practical advantage of XP2.

#### Infrared B&W

Identical to traditional silver B&W -- all IR-based cleaning must be completely disabled. Physical cleaning + Photoshop retouching only.

---

## 9. Epson Scan Professional Mode Workflow

### Software Version Note

**Epson Scan** (original) provides the most complete feature set for the V600, including Digital ICE and Professional Mode with its full control set.

**Epson Scan 2** is required for macOS Catalina+ and current Windows. It reorganizes the interface into a single unified view (no named modes), retains histogram/curves/adjustments, but lacks Digital ICE. For B&W silver negatives, this loss is irrelevant since ICE cannot be used with silver film anyway.

### Complete B&W Negative Scanning Procedure

#### Scanner and Film Preparation

1. Clean the scanner glass with a microfiber cloth
2. Clean the film holder slots with compressed air
3. Handle film by edges only (gloves recommended)
4. Remove dust from both sides of the film with anti-static brush and blower
5. Remove the white document mat from the scanner lid
6. Insert film into the holder with **shiny (base) side down**, emulsion (dull) side up. Text and frame numbers should appear backwards when looking at the top side
7. Place the holder on the scanner bed, aligning Tab A (35mm) or Tab B (120) to the corresponding area marker

#### Software Settings

| Setting | Value | Rationale |
|---------|-------|-----------|
| Document Type | Film | Required for transparency unit |
| Film Type | B&W Negative Film | Applies appropriate inversion and tonal mapping |
| Image Type | 16-bit Grayscale (or 48-bit Color for archival RGB) | Maximum tonal depth; see Section 6 for RGB vs Grayscale |
| Resolution | 3200 DPI | Maximum useful setting for V600; see Section 7 |
| Unsharp Mask | **OFF** | Destructive; apply in Photoshop instead |
| DIGITAL ICE | **OFF** | Does not work with silver B&W film |
| Dust Removal | **OFF** | Software-based; crude compared to Photoshop |
| Grain Reduction | **OFF** | Applies blur; destroys fine detail |
| Color Restoration | **OFF** | Irrelevant for B&W |
| Backlight Correction | **OFF** | For reflective scans only |
| Descreening | **OFF** | For printed halftone material only |

**In Configuration > Color tab:** Uncheck "Continuous auto exposure."

#### Preview and Crop

1. Click **Preview**
2. Switch to **Normal** preview if Thumbnail mode does not frame correctly (Normal gives more precise crop control)
3. Draw a marquee around the frame, excluding the film border
4. Optionally click **Auto Exposure** as a starting point

#### Histogram and Tone

1. Open the **Histogram**: Set Output to 0 and 255 (full range)
2. Move Input black point rightward to the edge of the histogram data
3. Move Input white point leftward to the edge of the histogram data
4. Adjust gamma (midpoint) until preview brightness is approximately correct. For maximum Photoshop flexibility, leave gamma at 1.00 (linear)
5. Open **Tone Correction**: Set to **Linear** from the dropdown for maximum post-processing flexibility

#### Output

1. Click **File Save Settings**
2. Format: **TIFF**, compression: **None** (or LZW for lossless compression)
3. Set output folder and file naming prefix
4. Check "Embed ICC Profile" if desired
5. Click **Scan**
6. Repeat for additional frames

### Alternative: Scan as Positive Film

Some practitioners scan B&W negatives as **Positive Film** in 48-bit Color and invert manually in Photoshop. The rationale: the scanner's built-in B&W negative inversion can crush blacks (shadow detail). Scanning as positive preserves the raw negative density data.

**This is a valid advanced workflow** but adds complexity. It is most useful for very dense or very thin negatives where the scanner's automatic inversion produces poor results.

### Settings That Auto-Reset

**Warning:** The Unsharp Mask checkbox and auto exposure settings can auto-enable themselves after clicking Preview. Always verify all checkboxes before the final scan.

---

## 10. SilverFast Workflow

### Editions for the V600

| Edition | Tier | Price | Key Features |
|---------|------|-------|-------------|
| SilverFast SE 9 | Basic | ~$49 (free from Epson for V600) | NegaFix (presets), SRDx (basic), WorkflowPilot |
| SilverFast SE Plus 9 | Plus | ~$99 | + Multi-Exposure, AACO, iSRD |
| SilverFast Ai Studio 9 | Premium | ~$249 | + IT8 calibration, NegaFix expert mode, JobManager, full histogram tools, 48-bit processed output |
| SilverFast Archive Suite 9 | Premium bundle | ~$399 | Ai Studio + HDR Studio (two-stage archival workflow) |

### Critical Edition Limitations

**SE and SE Plus processed output is limited to 24-bit color / 8-bit grayscale.** They can scan to 48-bit/16-bit HDR raw files (unprocessed), but baked-in adjustments reduce to 8 bits per channel. **Only Ai Studio can output processed 48-bit TIFF files.**

This is an important limitation for a 16-bit Photoshop workflow. With SE/SE Plus, you must either:

- Accept 8-bit processed output (not recommended for serious work), or
- Use HDR raw output (48-bit unprocessed) and process in HDR Studio or Photoshop

### NegaFix for B&W Negatives

NegaFix inverts negatives using film-specific density response curves.

**Available B&W film profiles (confirmed from official NegaFix profiles list):**

| Manufacturer | Films |
|-------------|-------|
| Ilford | HP5 Plus 400, FP4 Plus 125, Pan-F Plus 50, Delta 100, Delta 400, XP2 Super 400, SFX 200 |
| Kodak | Tri-X 400, Tri-X 320, T-Max 100, T-Max 400, Plus-X 125, Black & White + 400, Portra BW 400, T400CN |
| Fuji | Neopan 400, Neopan 1600 |
| Other | Konica Monochrome VX 400, Macophot UP-25, Generic "Monochrome" |

**Confirmed absent from the official list:**

- Ilford Delta 3200
- Any Foma films (Fomapan 100, 200, 400)
- Any Rollei films (RPX, Retro, Infrared)

For unlisted films, use the generic "Monochrome" profile or substitute a similar film's profile. Ai Studio allows creating and saving custom profiles.

**NegaFix edition differences:**

- **SE / SE Plus:** Preset profile selection and automatic CCR (Color Cast Removal). No curve editing.
- **Ai Studio:** Expert dialog with gradation curves, RGB/CMY histograms, custom profile creation/modification.

### Multi-Exposure

**Confirmed supported on the V600** (SE Plus and Ai Studio). The scanner performs two scans at different exposure levels and combines them to increase dynamic range and reduce noise. Particularly useful for dense B&W negatives with shadow detail.

### AACO (Auto Adaptive Contrast Optimization)

Available in SE Plus and Ai Studio. Reveals detail in dark, low-contrast areas without affecting highlights. The "Shadow width" parameter controls how deep into the midtones the optimization reaches. Useful for dense B&W negatives.

### SilverFast B&W Negative Scanning Procedure

1. Set scan type to **Negative**, select **Monochrome** or specific B&W film type
2. Select **NegaFix profile** -- choose exact film or generic "Monochrome"
3. Adjust exposure -- NegaFix allows simulating up to 3 stops compensation
4. Apply **SRDx** for dust removal (NOT iSRD for silver B&W)
5. Apply **AACO** (SE Plus/Ai Studio) for shadow optimization
6. In **Ai Studio:** access NegaFix expert dialog for gradation curve fine-tuning
7. Apply **Multi-Exposure** (SE Plus/Ai Studio) for dense negatives
8. Scan to TIFF

### Archive Mode (Archive Suite Only)

The two-stage archival workflow:

1. **Scan to 64-bit HDRi RAW** (48-bit image + 16-bit IR channel). No image processing applied during scanning.
2. **Process later** in SilverFast HDR Studio, non-destructively. The RAW file is never modified; processed versions are exported.

This separates capture from processing, allowing unlimited reprocessing with different settings.

### Output Formats by Edition

| Format | SE | SE Plus | Ai Studio |
|--------|-----|---------|-----------|
| TIFF 8-bit processed | Yes | Yes | Yes |
| TIFF 16-bit processed | **No** | **No** | Yes |
| TIFF 48-bit/16-bit HDR raw | Yes | Yes | Yes |
| DNG (HDR raw) | Yes | Yes | Yes |
| JPEG | Yes | Yes | Yes |
| PSD | Yes | Yes | Yes |

---

## 11. Epson Scan vs. SilverFast Comparison

| Aspect | Epson Scan | SilverFast |
|--------|-----------|------------|
| **Cost** | Free (bundled) | Free SE; paid upgrades |
| **Film profiles** | Generic B&W curve only | NegaFix with 100+ film-specific profiles |
| **Negative handling** | Basic inversion | Sophisticated inversion with adjustable film curves (Ai Studio) |
| **Histogram** | Basic with channel view | Advanced (Ai Studio: 3-part output, 16-bit, multi-point densitometer) |
| **Curves** | Basic tone correction | Gradation with channel control (Ai Studio) |
| **IR dust removal** | Digital ICE (Epson Scan only, not Scan 2) | iSRD (SE Plus/Ai Studio) |
| **Software dust removal** | Basic Dust Removal | SRDx with selective controls |
| **Sharpening** | 3 presets (Low/Medium/High) | Adjustable USM parameters |
| **Multi-Exposure** | Not available | Available (SE Plus/Ai Studio) |
| **IT8 calibration** | Not available | Available (Ai Studio) |
| **Batch scanning** | Multiple marquees | JobManager (Ai Studio) |
| **16-bit processed output** | Yes (all modes) | **Only Ai Studio** |
| **Archival RAW** | Not available | 64-bit HDRi (Archive Suite) |
| **Ease of use** | Simple, familiar | Steeper learning curve |
| **macOS Catalina+** | Epson Scan 2 (no ICE) | Full feature set |

### Which Workflow for Which Situation?

| Situation | Recommendation |
|-----------|---------------|
| Quick scanning of a few frames | Epson Scan -- simpler interface, adequate controls |
| Traditional silver B&W, archival quality | SilverFast Ai Studio -- NegaFix profiles, Multi-Exposure, 16-bit processed output |
| Chromogenic B&W (XP2) | Either -- both handle color negatives; SilverFast iSRD provides better dust control than ICE |
| Dense negatives needing maximum shadow detail | SilverFast SE Plus/Ai Studio -- Multi-Exposure and AACO |
| Batch scanning large archives | SilverFast Ai Studio -- JobManager |
| macOS Catalina or later | SilverFast -- Epson Scan 2 lacks Digital ICE (though irrelevant for silver B&W) |
| Budget-conscious | Epson Scan + Photoshop -- all serious tonal work in Photoshop anyway |

---

## 12. Archival Master Scan

### What Constitutes an Archival Master

The archival master is the scanner's output file, preserved exactly as scanned, never modified. It represents the best possible capture of the physical negative's information at a specific point in time.

| Property | Specification | Rationale |
|----------|--------------|-----------|
| Format | TIFF, uncompressed | Universal, lossless, guaranteed readable for decades |
| Bit depth | 16-bit Grayscale or 48-bit RGB | Maximum tonal information preserved |
| Resolution | 3200 DPI (V600 maximum useful) | Captures all available optical detail |
| Compression | None (LZW acceptable; technically lossless) | Zero risk of data loss |
| ICC profile | Embedded (Gray Gamma 2.2 for grayscale; Adobe RGB for color) | Ensures correct interpretation |
| Scanner adjustments | Minimal -- linear tone curve, no sharpening, no grain reduction | Preserves raw captured information |

### File Hierarchy

| Tier | Format | Purpose | Example |
|------|--------|---------|---------|
| Archival master | 16-bit TIFF, uncompressed | Untouched scan. Never edited. | `_master.tif` |
| Working master | Photoshop PSD with layers | All editing happens here | `_work.psd` |
| Print master | Flattened TIFF, output-sharpened, ICC-profiled | One per paper/size combination | `_print_PhotoRagBaryta_16x20.tif` |
| Web derivative | sRGB JPEG, 72-96 PPI, long edge ~2048px | Portfolio, social media | `_web.jpg` |

### Naming Convention

Format: `YYYY-MM_RollNNN_FNN_FilmStock_master.tif`

Example: `2026-03_R045_F12_TriX400_master.tif`

Keep filenames under 40 characters. Use only letters, numbers, underscores. No spaces or special characters. Date-first sorting ensures chronological order.

### Checksum Verification

Use SHA-256 for archival integrity:

```bash
sha256sum *.tif > checksums.sha256
```

Verify after any file transfer or backup:

```bash
sha256sum -c checksums.sha256
```

Re-verify periodically (quarterly or annually) to detect bit-rot. Filesystems like ZFS and Btrfs perform automatic checksumming.

### Storage: 3-2-1 Rule

Three copies, on two different media types, one off-site. The physical film negatives remain the ultimate master -- store in acid-free archival sleeves, cool and dry conditions.

---

## 13. Controlled RGB vs. Grayscale Experiment Design

### Purpose

Determine whether 48-bit RGB scanning produces measurably better results than 16-bit Grayscale for traditional silver B&W negatives on the V600.

### Protocol

**Test negative:** Select one well-exposed frame with:

- Deep shadows with detail
- Bright highlights with texture
- Smooth tonal gradations (sky, skin)
- Fine detail (text, edges, textures)
- Visible grain (shoot on a grainy film like Tri-X 400 or HP5)
- At least one area with dust or minor scratches

**Scan A:** 48-bit RGB TIFF at 3200 DPI

**Scan B:** 16-bit Grayscale TIFF at 3200 DPI

**Hold constant:** Same film holder, same film position (do not remove film between scans), same resolution, same sharpening (OFF), same dust removal (OFF), same Histogram settings (linear tone curve, output 0-255, input points at data edges).

### Processing

For both scans, perform equivalent negative inversion and tonal processing in Photoshop:

- Invert via Curves (drag endpoints)
- Set black and white points
- Apply identical S-curve contrast
- Apply identical Levels gamma

For the RGB scan, additionally:

- Extract green channel only, compare to the grayscale scan
- Create a Channel Mixer custom blend, compare
- Examine individual R, G, B channels for differences

### Evaluation Criteria

| Criterion | How to Evaluate |
|-----------|----------------|
| Shadow detail | Zoom to 100% on deep shadow areas. Compare visibility of detail before clipping. |
| Highlight detail | Examine brightest areas with texture. Compare clipping behavior. |
| Tonal smoothness | Look for posterization or banding in smooth gradients (sky, walls). |
| Grain character | Compare grain appearance at 100%. Note if grain looks different between RGB extraction and grayscale. |
| Apparent sharpness | Compare edge definition of fine detail at 100%. |
| Channel differences | In the RGB scan, compare R, G, B channels individually. Quantify: are they truly identical or do they differ? |
| Dust/artifact visibility | Compare visibility of known dust spots between channels and between the two scan modes. |
| Noise in shadows | Compare noise level and character in the deepest shadows. |
| Processing flexibility | After aggressive Curves manipulation, compare posterization and banding. |

### Expected Outcome

Based on the research, the expected outcome for traditional silver B&W film is:

- Channels will be nearly identical (silver is spectrally neutral)
- Small differences may exist from film base color
- Green channel may be marginally sharper
- Overall quality should be essentially equivalent
- RGB scan provides flexibility advantages but not quality advantages

If results differ from these expectations, the experiment has identified something worth documenting.

---

## 14. Photoshop B&W Negative Workflow

### Layer Stack (Recommended Order)

```
[TOP]
  Output Sharpening (stamp visible, High Pass, Overlay) -- only on print copy
  Dodge & Burn (50% gray layer, Overlay mode)
  Luminosity-masked Curves (targeted tonal adjustments)
  Local Contrast (High Pass ~30-80px, Overlay, reduced opacity)
  Curves: S-curve contrast
  Levels: Black/white point
  Levels: Midtone gamma
  Channel Mixer or B&W Adjustment (if converting from RGB)
  Curves: Inversion (endpoints dragged to invert)
  Dust retouching layer (empty layer, "Sample Current & Below")
  Background: Original scan (convert to Smart Object for protection)
[BOTTOM]
```

### Step 1: Preserve Original Scan

Open the TIFF scan. Immediately save as PSD. Convert the Background layer to a Smart Object (right-click > Convert to Smart Object) to protect the original data.

### Step 2: Negative Inversion

**Preferred method:** Add a Curves adjustment layer. Drag the bottom-left point to the top-left (output 255), and the top-right point to the bottom-right (output 0). This inverts while allowing simultaneous tonal adjustment.

**Why not Ctrl+I (Image > Invert)?**

- Destructive -- applied directly to pixels, cannot be modified later
- Produces flat, low-contrast result with no simultaneous tonal control
- Offers no per-channel adjustment

If working with an RGB scan of a B&W negative, invert each channel in the Curves dialog. Adjust per-channel curves to neutralize any color cast from the film base.

### Step 3: Tonal Normalization

#### Black and White Points

Add a **Levels adjustment layer**:

- Drag the black point slider rightward until the darkest meaningful shadow just touches pure black
- Drag the white point slider leftward until the brightest meaningful highlight just touches pure white
- Hold Alt/Option while dragging to see clipping preview

#### Midtone Gamma

The Levels midtone slider (center) adjusts overall brightness. A starting value of approximately 1.45 is often appropriate for inverted B&W negatives (practitioner recommendation from 125px).

### Step 4: Global Contrast (Curves)

Add a **Curves adjustment layer**. Apply a gentle **S-curve**: pull shadows slightly down, push highlights slightly up. This expands midtone contrast.

Adjust opacity with the number keys (1-0 for 10%-100%) to taste. For flat negatives, increase the S-curve steepness. For contrasty negatives, reduce opacity.

### Step 5: Local Contrast Enhancement

**High Pass + Overlay method:**

1. Stamp visible (Ctrl+Alt+Shift+E)
2. Set blend mode to **Overlay** (or Soft Light for subtler effect)
3. Filter > Other > High Pass. Radius: **30-80 pixels** (this is NOT detail sharpening -- the large radius creates a local contrast / clarity effect)
4. Reduce layer opacity to taste

To restrict to midtones only: double-click the layer to open Blending Options. In the "Blend If" section, Alt+drag the shadow and highlight sliders to exclude the effect from pure blacks and pure whites.

**Note:** Photoshop 2024+ includes a native Clarity adjustment as an adjustment layer, which is simpler.

### Step 6: Channel-Based Techniques (RGB Scans Only)

If working from a 48-bit RGB scan:

**Channel selection:**

1. Open the Channels panel
2. Click on R, G, B individually to preview each
3. The Green channel is typically sharpest on CCD sensors
4. The Blue channel typically shows most noise and dust artifacts

**Channel Mixer:**

1. Add a Channel Mixer adjustment layer
2. Check **Monochrome** at the bottom
3. Adjust R, G, B sliders. Default: 40R / 40G / 20B. Total should approximately equal 100%.

Channel mixer equivalents to traditional B&W filters:

| Simulated Filter | Red | Green | Blue | Effect |
|-----------------|-----|-------|------|--------|
| Red (#25) | 80 | 15 | 5 | Dramatic skies, high contrast |
| Orange (#21) | 60 | 30 | 10 | Moderate sky darkening |
| Yellow (#8) | 40 | 45 | 15 | Mild sky contrast |
| Green (#58) | 15 | 75 | 10 | Natural tonal rendering |

### Step 7: Dodge and Burn

**50% Gray Layer method (professional standard):**

1. Alt/Option+click the New Layer icon
2. Name: "Dodge & Burn", Mode: **Overlay**, Check "Fill with Overlay-neutral color (50% gray)"
3. Paint with **black to burn** (darken) and **white to dodge** (lighten)
4. Brush: Soft round, Flow at **5-10%**, Opacity 100%
5. Build up the effect gradually with multiple strokes
6. Press D for default colors (black/white), X to swap

### Step 8: Luminosity Masks

Luminosity masks select pixels based on brightness, creating self-feathering selections that precisely target tonal ranges.

**Creating basic luminosity masks:**

1. Ctrl+click the RGB channel thumbnail in the Channels panel -- loads a Lights selection
2. Save as a new channel (Select > Save Selection)
3. To narrow: Ctrl+Shift+click the same channel to intersect with itself ("Light Lights")
4. To create Darks: Invert the Lights selection (Ctrl+Shift+I)

**Application:** Create Curves or Levels adjustment layers with luminosity masks to affect only specific tonal ranges without affecting others. Multiple subtle adjustments, each targeting a narrow range, produce superior results to single aggressive adjustments.

**Panels:** TK Actions (Tony Kuyper), Lumenzia (Greg Benz), or Zone System Express (Blake Rudis) automate luminosity mask creation.

### Step 9: Dust and Scratch Removal

Create an empty layer above the Background, set retouching tools to "Sample: Current & Below."

| Tool | Best For | Notes |
|------|----------|-------|
| Healing Brush (J) | Isolated dust spots, smooth areas | May smudge grain in textured areas |
| Clone Stamp (S) | Edges, linear scratches, precise control | Preserves grain character from source |
| Remove Tool (PS 2024+) | Complex backgrounds, large spots | Content Aware mode preserves grain better than Generative AI mode |
| Content-Aware Fill | Large damaged areas | Via Edit > Content-Aware Fill workspace |

**Dust & Scratches Filter + History Brush technique (fastest for heavily dusted scans):**

1. Apply Filter > Noise > Dust & Scratches (Radius ~4-5px, Threshold as high as possible)
2. Image looks too soft -- expected
3. Edit > Undo (Ctrl+Z) to restore the original
4. In the History panel, set History Brush source to the filtered state
5. Paint with the History Brush only over dust spots to restore the filtered version selectively

**Pro tip:** Add a temporary extreme Levels adjustment layer to darken the image while spotting. This makes subtle dust much more visible. Delete the adjustment layer when done.

### Step 10: Sharpening

Three-stage framework (Bruce Fraser):

#### Capture Sharpening (compensating for scanner softness)

Apply early in the workflow. Recommended method:

- Camera Raw Filter (Filter > Camera Raw Filter): Amount ~40-60, Radius ~0.8-1.2, Detail ~25, Masking ~40-60
- OR Smart Sharpen (as Smart Filter): Lens Blur removal, Amount 100-200%, Radius 0.5-1.0px

#### Creative Sharpening (selective, image-dependent)

Apply to specific areas: eyes in portraits, key textures. Use a sharpening layer with a black mask, paint white where desired.

#### Output Sharpening (for print)

Applied LAST, after resizing to final print dimensions. See Section 19.

### Step 11: Final Tonal Interpretation

At this point, step back and evaluate the image as a whole. Toggle individual adjustment layers on/off. Adjust opacities. The final interpretation is artistic -- guided by the photographer's vision, not by technical rules.

---

## 15. Grain vs. Noise

| Artifact | Origin | Character | Desired? |
|----------|--------|-----------|----------|
| Film grain | Silver halide crystals in emulsion | Organic, irregular, increases with ISO, uniform across frame | Yes -- part of photographic identity |
| Scanner noise | CCD sensor electronics | Regular patterns, color speckles, banding, worse in shadows | No -- technical artifact |
| Luminance noise | CCD read noise | Random brightness variation | No |
| Chroma noise | CCD channel imbalance | Color speckles even in B&W scans | No |
| Grain aliasing | Scanner sampling vs grain frequency | Moire-like patterns | No |
| Sharpening artifacts | USM or High Pass applied too aggressively | Halos around high-contrast edges | No |
| Dust | Physical particles on film or glass | Dark spots on positive image | No |
| Scratches | Physical abrasion of film surface | Linear marks, usually along film travel direction | No |

### Distinguishing Grain from Scanner Noise

- Film grain is **uniform across the entire frame** (density-dependent but pattern-consistent)
- Scanner noise is often **more visible in shadows** and may show **color speckles** even in a B&W scan
- In an RGB scan: scanner noise often differs between channels; film grain should be similar across channels
- Viewing at 100%: grain looks organic and random; scanner noise may show banding or regular patterns

### Grain Preservation Strategy

- **Never apply global noise reduction to a well-exposed film scan.** It destroys grain character.
- Reduce scanner noise selectively: Camera Raw Luminance noise reduction at low values (10-20), or targeted noise reduction in shadow areas only
- **Order of operations:** Noise reduction BEFORE sharpening. Sharpening noise makes it more obvious.
- For films with distinctive grain (Tri-X, HP5): grain is a positive aesthetic quality. Preserve it.
- Tabular grain films (T-Max, Delta) were designed for scanning and produce cleaner results with less visible grain.

---

## 16. Dust and Scratch Workflow

### Prevention (Most Effective)

| Stage | Action | Impact |
|-------|--------|--------|
| Development | Final rinse in Photo-Flo + distilled water | Prevents water spots and most dust adhesion |
| Storage | Acid-free archival sleeves immediately after drying | Prevents dust accumulation and scratches |
| Scanning environment | HEPA air filter in scanning room | Reduces ambient dust |
| Handling | Powder-free nitrile gloves always | Prevents fingerprints and oil |

### During Scanning

| Method | Best For | Limitations |
|--------|----------|------------|
| Physical cleaning (brush + blower) | All film types | Cannot remove embedded particles |
| SilverFast SRDx (software-based) | Traditional silver B&W | May produce false positives on grain; crude compared to Photoshop |
| Digital ICE / SilverFast iSRD (infrared) | Chromogenic B&W (XP2), color film | Cannot be used on silver B&W or infrared films |
| Epson Scan Dust Removal (software-based) | General use | Less sophisticated than SRDx |

### In Photoshop

| Tool | Best For | Grain Impact |
|------|----------|-------------|
| Healing Brush (J) | General dust spots | Moderate -- can smudge grain |
| Clone Stamp (S) | Scratches, edges, precision work | Good -- samples grain from source |
| Remove Tool (PS 2024+) | Complex areas | Good in Content Aware mode; variable in Gen AI mode |
| Dust & Scratches Filter + History Brush | Heavily dusted scans (batch approach) | Selective -- only affects painted areas |
| Frequency Separation | Critical retouching without affecting grain | Best grain preservation |

**Recommended approach for silver B&W:** Physical cleaning + scan without dust correction + Photoshop retouching (Clone Stamp / Healing Brush). This provides the best quality control.

---

## 17. Infrared Film: Dedicated Section

### Films Covered

| Film | IR Sensitivity | ISO | Process | Status |
|------|---------------|-----|---------|--------|
| Rollei Infrared 400 | ~820 nm | 400 (25 with IR filter) | B&W standard | Available |
| Ilford SFX 200 | ~740 nm | 200 | B&W standard | Available |

### Scanning Challenges

1. **Traditional silver-halide emulsion:** All IR-based dust removal is impossible. The silver is opaque to IR (same as any B&W film).

2. **Extended IR sensitivity compounds the problem:** These emulsions were specifically designed to respond to infrared wavelengths -- the same wavelengths used by the scanner's IR cleaning system. Using iSRD/ICE would produce artifacts at least as severe as with standard B&W film, potentially worse.

3. **Unique tonal rendering:** Infrared photographs have distinctive tonal characteristics (bright foliage, dark skies, ethereal skin tones) that require careful negative inversion. Standard B&W scanner profiles may not produce optimal results.

### Recommended Scanning Workflow

1. **All IR-based cleaning OFF:** Digital ICE OFF, iSRD OFF. No exceptions.
2. **Physical cleaning is essential:** Anti-static brush, blower, PEC-12 if needed.
3. **SilverFast NegaFix:** Ilford SFX 200 has a profile in NegaFix. Rollei Infrared 400 does not -- use the generic "Monochrome" profile or experiment with other profiles.
4. **Scan as B&W Negative (16-bit Grayscale or 48-bit RGB)** at 3200 DPI.
5. **Epson Scan alternative:** Some users report better results scanning IR film as Positive Film and inverting in Photoshop, as the scanner's B&W Negative inversion may not handle the unusual tonal distribution well. This is a practitioner recommendation, not an officially documented workflow.

### Photoshop Processing

- Negative inversion via Curves (as per Section 14)
- Infrared photographs often have very high contrast -- be careful not to clip highlights (bright foliage) or shadows (dark sky)
- Local contrast enhancement can be particularly effective for the "IR glow" characteristic
- Grain is typically pronounced, especially with Rollei Infrared 400 at higher speeds
- For the classic "Wood Effect" (bright foliage), ensure the white point is set to preserve texture in the brightest foliage areas

### False-Color Workflows

If the negative was shot with a visible-light-blocking IR filter (R72, Hoya RM90) and scanned in 48-bit RGB, the channel data may support false-color effects:

- Swap channels in the Channel Mixer to create infrared false-color images
- The extent of useful false-color data depends on the filter used and the film's spectral response
- With a deep IR filter (R72 or deeper), most visible light is excluded and channel swapping produces subtle effects. With a lighter filter (R60, red filter), more visible-light data is captured and false-color effects are more dramatic.

**Note:** This is a creative technique. The results are inherently unpredictable and image-dependent.

---

## 18. Upscaling and Large Prints

### When Upscaling Is Needed

| Scenario | Upscaling Needed? |
|---------|-------------------|
| 35mm V600 scan, print 8x10 | Borderline -- depends on viewing distance |
| 35mm V600 scan, print 16x20 | Yes, definitely |
| 6x7 V600 scan, print 16x20 | No -- native resolution sufficient |
| 6x7 V600 scan, print 24x30 | Borderline -- viewing distance matters |
| 6x7 V600 scan, print 40x60 | Yes, but viewing distance forgives this |

### Viewing Distance and Required Resolution

| Print Size | Typical Viewing Distance | Minimum PPI |
|-----------|------------------------|-------------|
| 8x10 in | ~19 in (close) | 300 PPI |
| 16x20 in | ~38 in (~1m) | 200 PPI |
| 24x36 in | ~65 in (~1.7m) | 150 PPI |
| 40x60 in | ~108 in (~2.7m) | 100 PPI |

At gallery viewing distances, a 40x60 inch print at 100 PPI appears sharp. Resolution paranoia is based on examining prints at unrealistic distances.

### Upscaling Methods

#### Photoshop Image Size -- Preserve Details 2.0

Image > Image Size > Resample: Preserve Details 2.0

- Uses machine learning to upscale while reducing blur
- Conservative -- does not invent texture or hallucinate detail
- Includes noise reduction slider
- Good for moderate upscaling (up to 2x)
- Result is softer than AI upscalers but more honest

#### Camera Raw Super Resolution

- Doubles linear resolution (4x total pixels) using machine learning
- For film scans: **upscales grain along with detail**. Grain becomes larger and more prominent, not sharper.
- Cannot be combined with Denoise on the same image
- Workaround: noise reduction first, then Super Resolution, then optional synthetic grain
- Designed primarily for RAW digital files; results are mixed with film scans

#### Third-Party AI Upscaling (Topaz Gigapixel, etc.)

- Neural networks reconstruct textures and details
- Multiple models available; Standard or HQ recommended for film
- Generally outperforms Photoshop at higher enlargement factors (4x+)
- Can misinterpret film grain as noise and obliterate it

### Three Categories of "Detail"

| Type | What It Is | Visual Character |
|------|-----------|-----------------|
| Genuine detail | Information captured by scanner optics from the film | Sharp at native resolution; softens when enlarged |
| Interpolated detail | New pixels calculated from neighbors (bicubic, Lanczos) | Smooth, soft, no new edges. Honest but blurry. |
| AI-hallucinated detail | Synthesized by neural network from learned patterns | Can look sharp and plausible but may contain wrong features, false edges, unnatural micro-contrast |

**AI-generated detail is NOT recovered original information.** It is statistically plausible reconstruction that may or may not match the original scene. For Fine Art photography where authenticity matters, this distinction is important.

---

## 19. Fine Art Print Workflow

### Print Preparation Chain

**Working PSD** &#8594; **Resize to print dimensions** &#8594; **Soft proof** &#8594; **Adjust for print** &#8594; **Convert to profile** &#8594; **Output sharpen** &#8594; **Save print master** &#8594; **Print**

### Color Management for B&W Printing

#### Grayscale vs. RGB for Printing

Keep B&W images in **RGB mode** for printing. Reasons:

- RGB files work with all printer drivers and all printing software
- Toning (warm, cool, selenium-tone simulations) requires RGB
- Some printers achieve better B&W tonal range by mixing small amounts of color ink with monochrome inks -- RGB mode enables this
- Custom RGB ICC profiles yield more accurate tonal distribution

Exception: If using a printer's dedicated B&W mode (e.g., Epson Advanced Black & White / ABW), grayscale files in Gray Gamma 2.2 may work better because the driver bypasses color management.

#### ICC Profiles

Every printer + ink + paper combination requires its own ICC profile. Generic profiles from paper manufacturers are starting points; custom profiles made with a spectrophotometer are superior.

#### Soft Proofing

View > Proof Setup > Custom:

1. **Device to Simulate:** Select ICC profile for your printer/paper combination
2. **Rendering Intent:** See below
3. **Black Point Compensation:** Enable
4. **Simulate Paper Color:** Optional but recommended for accurate preview

Toggle soft proof: Ctrl/Cmd+Y

#### Rendering Intent

| Intent | Best For | Effect |
|--------|----------|--------|
| Perceptual (+ BPC) | Matte / cotton rag papers; B&W ICC profiles | Compresses entire tonal range to fit. Nothing clips, but everything shifts slightly. |
| Relative Colorimetric (+ BPC) | Glossy / baryta papers with good Dmax | Preserves in-gamut tones exactly. Out-of-gamut tones clipped to nearest value. |

For B&W on matte cotton rag: **Perceptual** is generally better because matte papers have smaller achievable tonal range.

For B&W on baryta or glossy: **Relative Colorimetric with Black Point Compensation** is typically preferred.

In practice: try both for each image and toggle between them. The difference is image-dependent.

#### Black Point Compensation

Always enable. Maps the source black point to the destination black point, preventing crushed shadows. Without BPC, multiple dark tones may collapse to solid black.

### Output Sharpening

Output sharpening is ALWAYS the last step. Applied AFTER resizing to final print dimensions.

| Paper Type | Sharpening Amount | Notes |
|-----------|------------------|-------|
| Glossy / Baryta | Lighter | High contrast; halos easily visible |
| Matte / Cotton Rag | Stronger | Ink absorption dulls perceived sharpness |
| Fine Art Textured | Moderate-Strong | Paper texture masks some detail |

**High Pass method for output sharpening:**

1. Stamp visible (Ctrl+Alt+Shift+E)
2. Set blend mode to **Overlay**
3. Filter > Other > High Pass. Radius: **1.0-3.0 pixels** (much smaller than local contrast radius)
4. Reduce opacity to taste. Soft Light produces subtler results than Overlay.

**The output-sharpened file should look oversharpened on screen.** If it looks right on screen, it will look soft in print. The only reliable evaluation method is to sharpen, print, and examine.

### Paper Selection for B&W Fine Art

| Paper | Base | Key Characteristics |
|-------|------|---------------------|
| Hahnemuhle Photo Rag 308 | 100% cotton, OBA-free | Industry standard for B&W fine art. More gray tones than hard B&W. |
| Hahnemuhle Photo Rag Baryta 315 | 100% cotton + baryta, OBA-free | Deep blacks, warm tone. Bridges rag archival quality with baryta depth. |
| Hahnemuhle Fine Art Baryta 325 | Alpha-cellulose + baryta | High gloss, traditional darkroom fiber paper look. Contains OBAs. |
| Canson Infinity Platine Fibre Rag | 100% cotton + platine, OBA-free | Museum-grade. |
| Moab Juniper Baryta Rag 305 | 100% cotton + baryta | Highest-rated for deep-black B&W prints. |

**OBA (Optical Brightening Agents) Warning:** OBAs make paper appear whiter by fluorescing under UV. They fade over time, causing yellowing. For archival fine art: choose OBA-free papers.

**Practical advice:** Buy sample packs before committing. Print the same image on 3-4 papers. Compare under intended display lighting.

### Metamerism

Pigment inkjet B&W prints exhibit different color casts under different lighting. A print that looks neutral under daylight may show green or magenta cast under tungsten or fluorescent light. Evaluate prints under the display lighting conditions.

---

## 20. Practical Test Matrix

### Purpose

Validate the scanning workflow through systematic comparison using representative negatives.

### Test Variables

#### Resolution Comparison

| Scan | DPI | Expected Outcome |
|------|-----|-----------------|
| R1 | 2400 | Near-maximum detail, smaller files |
| R2 | 3200 | Maximum useful detail |
| R3 | 4800 | No additional detail, larger files |

#### Bit Depth Comparison

| Scan | Mode | Expected Outcome |
|------|------|-----------------|
| B1 | 48-bit RGB | Post-processing flexibility |
| B2 | 16-bit Grayscale | Equivalent quality at 1/3 file size |

#### Software Comparison

| Scan | Software | Key Difference |
|------|----------|---------------|
| S1 | Epson Scan Professional Mode | Generic B&W negative curve |
| S2 | SilverFast (with NegaFix profile) | Film-specific inversion profile |

#### Dust Removal Comparison (for chromogenic B&W only)

| Scan | Method | Notes |
|------|--------|-------|
| D1 | No correction | Baseline |
| D2 | Digital ICE / iSRD | Infrared hardware cleaning (chromogenic only) |
| D3 | SilverFast SRDx | Software-only cleaning |
| D4 | Photoshop retouching (on D1) | Manual post-scan |

### Evaluation Grid

For each comparison, evaluate:

| Criterion | Method |
|-----------|--------|
| Shadow detail | 100% view of deep shadow areas |
| Highlight detail | 100% view of brightest textured areas |
| Tonal smoothness | Examine smooth gradients for posterization/banding |
| Grain character | 100% view of mid-density uniform areas |
| Sharpness | Edge definition of fine detail at 100% |
| Artifacts | Any halos, banding, edge artifacts |
| Dust/scratches | Compare visibility and removal effectiveness |
| File size | Measure and record |
| Processing flexibility | Perform aggressive Curves manipulation; compare posterization |

### Test Negatives

**Minimum set:**

1. Traditional silver B&W (e.g., Ilford HP5 Plus or Kodak Tri-X 400) -- 35mm
2. Traditional silver B&W -- 120/6x7
3. Chromogenic B&W (Ilford XP2 Super) -- 35mm (if available)

Each test negative should contain: deep shadows with detail, bright highlights with texture, smooth gradients, fine detail, visible grain.

---

## 21. Troubleshooting: Failure Modes

### Muddy Shadows

**Symptom:** Deep shadows lack separation; dark tones merge into undifferentiated dark mass.

**Likely causes:**
- Black point set too aggressively (clipping shadow data)
- Dense negative exceeding scanner's Dmax (~3.0 practical for V600)
- Digital ICE mistakenly enabled on B&W film (destroying shadow detail)
- Scanner Auto Exposure compressing tonal range

**Diagnosis:** Check the histogram -- if the shadow end is clipped (hard vertical line at left edge), the scan lost data. If the data is there but compressed, the problem is in Photoshop curves/levels.

**Corrective actions:** Re-scan with more conservative black point. Use Multi-Exposure (SilverFast) for dense negatives. Use Curves with precise shadow control. Use luminosity masks to selectively lift shadows.

**Do NOT:** Apply Brightness increase (shifts entire range, may clip highlights).

### Blocked Highlights

**Symptom:** Brightest areas show no texture; pure white with no detail.

**Likely causes:** White point set too aggressively. Over-development of the negative (excessive Dmax).

**Corrective actions:** Re-scan with more conservative white point. In Photoshop, pull the highlight curve endpoint down slightly (not at 255).

### Flat Scans

**Symptom:** Image lacks contrast; appears washed out and lifeless.

**Likely causes:** This is EXPECTED for a flat/archival scan with linear tone curve. The scan is capturing information, not creating the final look.

**Corrective actions:** Apply S-curve contrast in Photoshop. Set appropriate black and white points. This is normal -- the contrast is created in post-processing.

### Excessive Grain

**Symptom:** Image appears much grainier than expected for the film stock.

**Likely causes:** Scanner sharpening (Unsharp Mask) applied during scanning -- amplifies grain. Excessive Curves contrast in Photoshop amplifies grain in midtones. Film push-processed beyond normal range.

**Diagnosis:** Examine the archival master scan at 100%. If grain is already exaggerated, scanner-side sharpening is likely the cause.

**Corrective actions:** Re-scan with all sharpening OFF. Apply sharpening selectively in Photoshop (avoid sharpening flat mid-tone areas where grain is most visible). Use luminosity masks to sharpen only detail areas.

**Do NOT:** Apply global noise reduction -- it destroys the grain character.

### Excessive Softness

**Symptom:** Image lacks sharpness; fine detail is blurred or indistinct.

**Likely causes:** Film not at optimal focal distance in holder. V600's inherent optical limitation (~1,560 DPI effective resolution). Film curl moving emulsion out of focal plane.

**Corrective actions:** Ensure film is flat in the holder. Try aftermarket film holders with height adjustment. Apply capture sharpening in Photoshop (Smart Sharpen, Amount 100-200%, Radius 0.5-1.0px). Accept that the V600 has fundamental optical limitations for 35mm.

### Halos

**Symptom:** Bright outlines around high-contrast edges.

**Likely causes:** Excessive Unsharp Mask (too high Amount and/or too large Radius). Scanner-side sharpening. Local contrast enhancement applied too aggressively.

**Corrective actions:** Reduce USM Amount and Radius. For capture sharpening: Amount 100-200%, Radius 0.5-1.0px maximum. For output sharpening: High Pass at 1-3px, not higher.

### Banding

**Symptom:** Visible steps or bands in smooth gradients instead of smooth transitions.

**Likely causes:** Working in 8-bit mode instead of 16-bit. Excessive tonal manipulation in 8-bit. JPEG artifacts from lossy compression.

**Corrective actions:** Always work in 16-bit mode until the final output step. Save masters as 16-bit TIFF. Convert to 8-bit only for the final JPEG/print output.

### Unexpected Color in RGB Scans of B&W Film

**Symptom:** Color cast visible when scanning B&W film in color mode.

**Likely causes:** Film base color (acetate base has slight pink/grey tint). Film staining from developer (pyro-developed negatives have brown/green stain). This is normal and expected.

**Corrective actions:** This is not a problem -- it is the film base color. Neutralize with Channel Mixer or Black & White adjustment layer during conversion to grayscale. The colors carry no image information for traditional silver B&W.

### Newton Rings

**Symptom:** Concentric rainbow-like rings visible in the scan, especially on glossy film base.

**Likely causes:** Film base in optical contact with a smooth surface (scanner glass or holder surface). Interference patterns from thin air gap.

**Corrective actions:** Ensure film does not touch the scanner glass directly (use holders). Anti-Newton Ring (ANR) glass holders (not included with V600, available as aftermarket upgrade). Slightly raise film away from the glass surface.

### ICE Artifacts on B&W Film

**Symptom:** Severe blotching, smearing, detail obliteration when scanning B&W film.

**Cause:** Digital ICE or iSRD enabled on silver-halide B&W film. Silver blocks infrared light -- the algorithm treats the entire image as a defect.

**Corrective action:** DISABLE Digital ICE / iSRD immediately. Re-scan. This is the only solution.

### Scanner Flare

**Symptom:** Overall loss of contrast, especially with dense negatives. Shadows appear lighter than expected.

**Likely causes:** Internal reflections within the scanner. More pronounced with the V600's single-lens design than with dual-lens scanners.

**Corrective actions:** Clean the scanner glass thoroughly. Mask off unused areas of the scanning bed (cover with opaque material around the film frame). This is a known limitation of the V600 compared to the V850.

### Differences Between Epson Scan and SilverFast Results

**Symptom:** The same negative produces visibly different tonal rendering in each software.

**Cause:** Different negative inversion algorithms and tonal mapping curves. SilverFast NegaFix uses film-specific profiles; Epson Scan uses a generic B&W curve.

**This is expected.** Neither result is "wrong" -- they are different interpretations. For maximum control, scan with minimal processing in either software and perform the interpretation in Photoshop.

---

## 22. Evidence Table

| Claim | Source | Source Type | Confidence | V600-Specific? | Version-Specific? |
|-------|--------|------------|-----------|---------------|------------------|
| V600 optical resolution is 6400 x 9600 DPI | Epson official specs | Manufacturer documentation | High | Yes | No |
| V600 effective resolution is ~1,560 DPI | filmscanner.info USAF test | Independent lab test | High | Yes | No |
| Scanning at 3200 and 6400 DPI yields identical effective resolution | filmscanner.info, Flickr V600 group | Independent tests | High | Yes | No |
| V600 practical Dmax is ~3.0-3.1 | Practitioner estimates | Forum consensus | Medium | Yes (estimated) | No |
| V600 scan speed identical for color and grayscale at 6400 DPI (21 ms/line) | Epson specifications | Manufacturer documentation | High | Yes | Yes |
| Silver halide particles are spectrally neutral (broadband absorption) | Journal of Imaging Science and Technology | Peer-reviewed research | High | N/A (physics) | No |
| XP2 Super scans better in color mode than B&W mode | EarthSunFilm | Practitioner test with comparisons | Medium | No (general scanner) | No |
| Green channel typically sharpest on CCD sensors | Multiple practitioner sources | Practitioner consensus | Medium | Not V600-specific | No |
| Digital ICE not available in Epson Scan 2 | Epson support documentation | Manufacturer documentation | High | Yes (and other models) | Epson Scan 2 |
| SilverFast SE processed output limited to 8-bit/channel | SilverFast comparison table | Manufacturer documentation | High | Yes (V600 page) | SilverFast 9 |
| Multi-Exposure supported on V600 | SilverFast V600 model page | Manufacturer documentation | High | Yes | SilverFast 9 |
| NegaFix profiles for specific B&W films | SilverFast NegaFix profiles page | Manufacturer documentation | High | Profiles generated per-scanner | SilverFast 9 |
| Foma and Rollei films absent from NegaFix | SilverFast NegaFix profiles page | Manufacturer documentation | High | Yes | SilverFast 9 |
| S-curve at opacity ~100% plus Levels gamma ~1.45 as B&W starting point | 125px workflow guide | Practitioner recommendation | Medium | No (general Photoshop) | No |
| High Pass 30-80px Overlay for local contrast | Multiple Photoshop guides | Documented technique | High | N/A (Photoshop) | No |
| 50% gray Overlay dodge & burn | Multiple Photoshop guides | Documented technique | High | N/A (Photoshop) | No |
| Perceptual rendering intent better for matte papers | Multiple print workflow guides | Practitioner consensus | Medium-High | N/A (printing) | No |
| OBA-free papers for archival prints | Paper manufacturers | Manufacturer documentation | High | N/A (printing) | No |
| V600 film holders are flimsy, no ANR glass | filmscanner.info, multiple reviews | Independent reviews | High | Yes | No |
| Aftermarket holders can improve resolution from ~1,500 to ~2,000 DPI | Practitioner reports | Forum consensus | Medium | Yes | No |

---

## 23. Validated Workflows

### Workflow A: Archival Master (Maximum Flexibility)

For: Negatives worth preserving with maximum post-processing options.

```
Epson V600
  Film Type: B&W Negative Film
  Image Type: 48-bit Color (RGB)
  Resolution: 3200 DPI
  All adjustments: OFF (Linear tone curve, no sharpening, no dust removal)
  Output: Uncompressed TIFF
  
-> Archival master (never modify)
-> SHA-256 checksum
-> 3-2-1 backup

-> Photoshop:
   Open as Smart Object
   Curves: Invert (endpoints)
   Channel Mixer: Select best channel or blend
   Levels: Black/white points + gamma
   Curves: S-curve contrast
   Local contrast: High Pass Overlay
   Dodge & Burn: 50% gray layer
   Luminosity masks: Targeted adjustments
   Dust retouching: Clone Stamp / Healing Brush on separate layer
   
-> Working master PSD

-> Print master:
   Resize to final dimensions
   Soft proof with ICC profile
   Output sharpening (High Pass 1-3px)
   Convert to printer/paper ICC profile
   Save as flattened TIFF
```

### Workflow B: Efficient Production

For: Routine scanning when maximum archival flexibility is not needed.

```
Epson V600 (or SilverFast with NegaFix profile)
  Film Type: B&W Negative Film
  Image Type: 16-bit Grayscale
  Resolution: 3200 DPI
  Tone curve: Slight adjustment for usable starting point
  All other adjustments: OFF
  Output: TIFF (LZW compression acceptable)

-> Master scan
-> Photoshop: Levels + Curves + Dodge/Burn + Dust retouching
-> Print or web output
```

### Workflow C: Chromogenic B&W (XP2 Super)

```
Epson V600 (or SilverFast)
  Film Type: Color Negative Film (NOT B&W Negative)
  Image Type: 48-bit Color (RGB)
  Resolution: 3200 DPI
  Digital ICE or iSRD: ON (infrared cleaning works on dye-based film)
  Output: Uncompressed TIFF

-> Photoshop:
   Channel Mixer to Monochrome (or B&W adjustment layer)
   Standard tonal workflow (Levels, Curves, etc.)
```

### Workflow D: Infrared B&W (Rollei IR 400, Ilford SFX 200)

```
Epson V600
  Film Type: B&W Negative Film (or scan as Positive and invert in Photoshop)
  Image Type: 16-bit Grayscale or 48-bit RGB
  Resolution: 3200 DPI
  Digital ICE: OFF (MUST be disabled)
  iSRD: OFF (MUST be disabled)
  SRDx: OFF (optional -- may produce false positives)
  All sharpening: OFF
  Output: Uncompressed TIFF

-> Thorough physical cleaning is essential
-> Photoshop: Manual inversion if scanned as Positive
-> Extra care with highlight/shadow clipping (IR images are high contrast)
-> Manual dust retouching
```

---

## 24. Final Quality Control Notes

### Verification Checklist

Before trusting any claim in this guide, the following was verified during the research phase:

- [x] V600 specifications confirmed from Epson official documentation
- [x] Effective resolution confirmed from independent filmscanner.info testing
- [x] Digital ICE film compatibility confirmed from Epson support documentation and physics of silver/IR interaction
- [x] SilverFast edition features confirmed from official SilverFast comparison table and V600 model page
- [x] NegaFix film profiles confirmed from official profiles list
- [x] Photoshop techniques confirmed from Adobe documentation and established practitioner workflows
- [x] Paper recommendations confirmed from manufacturer specifications

### Known Uncertainties

| Item | Uncertainty |
|------|-----------|
| V600 practical Dmax | No published independent lab measurement found. The ~3.0-3.1 figure is estimated from practitioner reports and comparative reasoning. |
| Noise advantage of RGB averaging for grayscale | Theoretically sqrt(3) improvement, but unclear whether the scanner's internal grayscale conversion already performs this averaging. |
| Green channel sharpness advantage | Practitioner consensus, not formally measured for the V600 specifically. Consistent with CCD design principles. |
| NegaFix profile scanner specificity | SilverFast states profiles are "generated individually film for film for every scanner," but the full V600-specific profile list was not independently confirmed vs. the general list. |
| Epson Scan version number | A specific version number could not be verified from official sources. |

### What This Guide Does NOT Claim

- It does not claim that 48-bit RGB scanning captures additional optical information from silver B&W film (it does not -- the channels are essentially redundant for image content)
- It does not claim that nominal DPI equals genuine captured detail (it does not -- the V600's 6400 DPI nominal yields ~1,560 DPI of genuine resolved detail)
- It does not claim that AI upscaling recovers original photographic information (it does not -- it generates statistically plausible reconstructions)
- It does not claim that the V600 is adequate for all purposes (it has significant limitations for 35mm film compared to dedicated film scanners)

### Lehetseges torzitasok

- This guide relies partially on practitioner consensus from photography forums. While consistent across multiple sources, these are not controlled studies.
- Resolution measurements (filmscanner.info) are from a specific V600 unit with specific film holder positioning. Individual scanner variation exists.
- The V600 has been in production since 2009. Hardware revisions may have occurred without public documentation.
- SilverFast pricing and feature allocation may change. Information is current as of the research date.
- Paper recommendations are based on manufacturer claims and reviewer experience. Paper/ink/printer interactions are complex and printer-specific.

---

*This guide was constructed from documented specifications, independent measurements, peer-reviewed research, and practitioner evidence. Claims are graded by source type and confidence level. Where evidence was insufficient, uncertainty is explicitly stated. The objective is a verified, reproducible workflow -- not an impressive-sounding collection of scanning folklore.*
