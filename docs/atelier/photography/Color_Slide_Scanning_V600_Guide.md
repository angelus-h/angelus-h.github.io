---
description: Evidence-based technical guide for scanning color transparency (E-6 slide) film with the Epson Perfection V600 Photo, digital processing in Photoshop, and Fine Art print preparation.
---

# Color Slide Film Scanning with the Epson Perfection V600 Photo

**Version:** 1.0
**Created:** 2026-08-13
**Objective:** Establish a reproducible, evidence-based digital-darkroom workflow for producing archival scans and Fine Art prints from color transparency (reversal) film using the Epson Perfection V600 Photo.

---

## 1. Scope and Intent

This guide covers the complete workflow from color transparency to Fine Art print:

**Color transparency** &#8594; **Scanner** &#8594; **Archival master** &#8594; **Photoshop** &#8594; **Upscaling (if needed)** &#8594; **Color management** &#8594; **Soft proof** &#8594; **Output sharpening** &#8594; **Fine Art print**

It addresses E-6 process color transparency films, primarily:

- Fujifilm Velvia 50
- Fujifilm Provia 100F
- Kodak Ektachrome E100
- Other historically relevant E-6 emulsions where useful

Every recommendation in this guide is sourced and graded. Where claims could not be verified for the V600 specifically, this is stated explicitly. Accuracy takes priority over completeness.

---

## 2. Transparency vs. Negative: The Fundamental Difference

A color negative film records the scene in inverted tones and complementary colors, with an integral orange mask designed to compensate for imperfect dye spectral absorption. The scanner must invert the image and remove the mask -- a complex transformation that depends on film-specific profiles (SilverFast NegaFix, Epson Scan's built-in curves).

A color transparency (reversal / slide) film already contains the final positive image. The physical film IS the reference:

- No inversion is required
- No orange mask exists to remove
- Colors and density are already encoded as the photographer intended them
- The transparency on a calibrated light box is the definitive version of the image

**Implications for scanning:**

| Aspect | Color Negative | Color Transparency |
|--------|---------------|-------------------|
| Scanner mode | Color Negative Film | Positive Film |
| Inversion | Required (software applies film profile) | Not required |
| Orange mask removal | Required | Not applicable |
| Color reference | No physical reference exists | The transparency itself |
| NegaFix (SilverFast) | Essential for accurate inversion | Not used -- counterproductive |
| Primary archival goal | Accurate inversion + mask removal | Faithful reproduction of the transparency |
| Scanning difficulty | Mask removal is the hard problem | Dmax / shadow penetration is the hard problem |
| Histogram interpretation | Inverted -- density and image brightness move in opposite directions | Direct -- what you see is what you get |

**The scanning philosophy differs accordingly.** With negatives, the scanner software must make interpretive decisions during inversion. With transparencies, the scanner's job is simpler in principle -- capture the positive image faithfully -- but the physical challenge is harder because the densest areas of the transparency (deep shadows) test the scanner's dynamic range limits.

---

## 3. Hardware Profile: Epson Perfection V600 Photo

### Verified Specifications

| Parameter | Value | Source |
|-----------|-------|--------|
| Sensor type | MatrixCCD, 3-line (12-line with sub-arrays), single lens | Epson official specs |
| Claimed optical resolution | 6400 x 9600 DPI | Epson official specs |
| Measured effective resolution | ~1,560 DPI (USAF 1951 target) | filmscanner.info independent test |
| Maximum interpolated resolution | 12,800 x 12,800 DPI | Epson official specs |
| Color bit depth (input/output) | 48-bit (16 bits/channel) | Epson official specs |
| Claimed Dmax | 3.4 | Epson official specs |
| Practical Dmax | ~3.0-3.1 (estimated) | Practitioner consensus; no published lab measurement found |
| Light source | White LED + IR LED (ReadyScan LED) | Epson official specs |
| Connection | USB 2.0 | Epson official specs |
| Transparency unit area | 2.7" x 9.5" (68.6 x 241.3 mm) | Epson official specs |
| Film holders included | 35mm strip/slide combo holder + 120/220 holder | Epson official specs |
| Digital ICE | Supported (Epson Scan only; NOT in Epson Scan 2) | Epson support documentation |

### Transparency Scanning Capacity Per Batch

| Format | Capacity |
|--------|----------|
| 35mm mounted slides | 4 slides |
| 35mm strips | 2 strips x 6 frames (12 frames total) |
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

**EVIDENCE:** Independent testing by filmscanner.info using a USAF 1951 resolution target measured approximately 1,560 DPI effective resolution. Scanning at 3200 DPI and 6400 DPI produced identical measured resolution.

**INTERPRETATION:** The nominal resolution reflects the CCD sensor's sampling density, not the optical system's resolving power. The single-lens optical path limits actual detail capture to roughly one-quarter of the claimed figure.

**RECOMMENDATION:** Treat the V600 as a scanner with approximately 1,500-2,000 DPI of genuine resolving power. This is significantly lower than the V700/V800/V850 series (~2,300-2,400 DPI measured) and dedicated film scanners like the Plustek 8200i (~3,250 DPI measured) or Nikon Coolscan series (~3,900 DPI measured).

### V600 Dmax and Transparency Film

**FACT:** Epson's own documentation states: "Dmax only applies when scanning slides -- the higher the Dmax, the greater the image detail in shadow and highlight areas."

**EVIDENCE:** The V600's claimed Dmax of 3.4 means it can theoretically distinguish density values up to 3.4. In practice, the usable Dmax is estimated at ~3.0-3.1 before noise degrades shadow detail. E-6 transparency films can reach Dmax values of 3.5-4.0 (Velvia 50 approaches 3.8 per Fujifilm characteristic curves).

**INTERPRETATION:** The V600 cannot fully penetrate the densest shadow areas of high-contrast transparencies, particularly Velvia 50. Shadow detail in the densest regions may be lost to scanner noise. This is the primary technical limitation of the V600 for transparency scanning.

**RECOMMENDATION:** For transparencies with extremely dense shadows, SilverFast Multi-Exposure (SE Plus and above) can extend effective dynamic range by combining two scans at different exposures. The V850's higher practical Dmax (~3.3-3.5) provides better shadow penetration.

---

## 4. E-6 Process and Film Classification

### What Is E-6 Processing

E-6 is a chromogenic process for developing color reversal (transparency) film, introduced by Kodak in 1976 as a successor to the E-4 process.

The commercial process uses six baths at 37.8°C (100.0°F):

1. **First Developer** -- creates a black-and-white negative silver image from exposed crystals
2. **Reversal Bath** -- chemically fogs the remaining unexposed silver halide
3. **Color Developer** -- oxidized developing agents couple with dye couplers built into each emulsion layer to form the positive color dye image
4. **Pre-Bleach / Conditioner** -- prepares for bleach
5. **Bleach** -- removes all metallic silver
6. **Fixer** -- removes unexposed silver halide

The final result is a positive dye image with no metallic silver remaining. This is critical for scanning: the dyes are transparent to infrared, enabling Digital ICE / iSRD dust removal.

Simplified home kits (Tetenal, CineStill, Adox) combine steps into a three-bath process.

### E-6 vs. K-14 (Kodachrome)

| Property | E-6 | K-14 (Kodachrome) |
|----------|-----|-------------------|
| Dye couplers | Built into emulsion layers | Added during processing |
| Processing labs | Any E-6 lab | Only Kodak's own labs (last: Dwayne's Photo, closed Dec 30, 2010) |
| Silver in final image | None (fully bleached) | None (fully bleached) |
| Cyan dye IR transparency | Transparent to IR | Absorbs IR |
| Digital ICE compatibility | Fully compatible | Incompatible (standard ICE) |
| Current availability | Three films in production | No processing available |

**Why Kodachrome breaks infrared dust removal:** Kodachrome's cyan dye has a different molecular structure from E-6 cyan dyes, with absorption extending into the near-infrared region. The IR channel sees this opaque layer as a defect, causing the algorithm to attempt to "repair" valid image content. Additionally, Kodachrome has a pronounced relief image that further affects IR transmission.

### Scanning Implications of E-6 Chemistry

Because E-6 processing removes all metallic silver, the final transparency consists entirely of organic dyes in three layers (cyan, magenta, yellow). These dyes:

- Are transparent to infrared radiation -- Digital ICE and iSRD work correctly
- Have known spectral absorption characteristics -- can be accurately profiled with IT8 targets on E-6 film
- Are subject to dye fading over decades -- Color Restoration may be relevant for older slides
- Produce no IR-blocking image component -- unlike silver-based B&W films

---

## 5. Film Characteristics

### Currently Available E-6 Films

Only three professional E-6 films remain in regular production (as of 2026):

1. **Fujifilm Velvia 50** (35mm, 120)
2. **Fujifilm Provia 100F** (35mm, 120, sheet)
3. **Kodak Ektachrome E100** (35mm, 120, 4x5, Super 8)

### Technical Comparison

| Specification | Velvia 50 (RVP50) | Provia 100F (RDPIII) | Ektachrome E100 |
|---------------|-------------------|---------------------|-----------------|
| ISO Speed | 50/18 | 100/21 | 100/21 |
| Process | E-6 | E-6 | E-6 |
| RMS Granularity | 9 | 8 | 8 |
| Resolving Power (1000:1) | 160 lines/mm | 140 lines/mm | ~125 lines/mm (unverified for E100; inferred from E100G) |
| Film Base | Cellulose triacetate | Cellulose triacetate | Acetate safety base |
| Dmax | ~3.8 (red channel) | Not verified | ~4.0 (from characteristic curves) |
| Exposure Latitude | -0.5 to +0.5 stops | -0.5 to +1 stop | -0.5 to +1 stop |
| Push/Pull | Not recommended | -0.5 to +2 stops | -0.5 to +2 stops (good) |
| Reciprocity (no compensation) | Severe after 4 sec | Up to 2 minutes | Up to 10 sec |
| Saturation | Extreme | Moderate (neutral) | Moderate (slightly warm) |
| Contrast | High | Medium | Low (extended tone scale) |
| Color Balance | Warm | Neutral | Neutral (slightly warm) |

Sources: Fujifilm datasheets AF3-0221E2 (Velvia 50), RDPIII datasheet (Provia 100F); Kodak Publication E-4000 (Ektachrome E100).

### Fujifilm Velvia 50 (RVP50)

The landscape photographer's film. Introduced in 1990 (original RVP); the current RVP50 is a 2007 reformulation replicating the original's characteristics.

**Color palette:** Extremely vivid greens, deep blues, warm reds and oranges. The saturation is not neutral -- it actively enhances colors, particularly in nature subjects. Fujifilm describes it as having "the highest maximum density to date for more profound shadows and deeper blacks."

**Scanning challenges:** Very high Dmax (~3.8) demands a scanner with true Dmax of at least 3.5. The V600's practical Dmax of ~3.0-3.1 will clip the deepest shadows. High contrast and saturation can clip both highlight and shadow channels if auto-exposure is left enabled. The extreme saturation means scanner channels (especially reds and greens) may clip if scanned with a generic profile.

**Velvia generations:**

| Version | Code | Introduced | Status |
|---------|------|------------|--------|
| Velvia (original) | RVP | 1990 | Discontinued 2005 |
| Velvia 100F | RVP100F | 2002 | Discontinued 2012 |
| Velvia 100 | RVP100 | 2005 | Discontinued 2021 (US) |
| Velvia 50 | RVP50 | 2007 | In production |

### Fujifilm Provia 100F (RDPIII)

Fujifilm's general-purpose professional slide film. Vivid but faithful color reproduction -- less saturated than Velvia, more saturated than the discontinued Astia. Neutral gray balance, well-controlled gradation.

**Scanning characteristics:** The most "scanner-friendly" of the Fuji slide films. Lower contrast than Velvia means less risk of clipping. Excellent reciprocity performance. RMS 8 grain is extremely fine and scans cleanly.

### Kodak Ektachrome E100

Kodak's 2018 reintroduction of slide film. Explicitly designed with scanning in mind -- the T-GRAIN emulsion micro-structure was specifically tuned for scanner use. Low contrast tone scale optimized for digital workflow. Low D-min produces "whiter, brighter whites."

**Scanning characteristics:** Natural skin tones. Good shadow and highlight detail retention due to low contrast. The smooth T-GRAIN structure scans with less visible grain pattern than comparable cubic-grain films. Most comparable to the discontinued E100G.

### Historical E-6 Films

| Film | ISO | RMS | Saturation | Contrast | Status |
|------|-----|-----|------------|----------|--------|
| Velvia 100 (RVP100) | 100 | 8 | Very high | High | Discontinued (US, 2021) |
| Velvia 100F (RVP100F) | 100 | 8 | High (accurate) | Moderate-high | Discontinued 2012 |
| Provia 400X (RXP) | 400 | 11 | Moderate | Moderate | Discontinued 2013 |
| Astia 100F (RAP100F) | 100 | 7 | Low-moderate | Low | Discontinued 2012 |
| Ektachrome E100G | 100 | 8 | Moderate | Low | Discontinued |
| Ektachrome E100GX | 100 | 8 | Moderate (warm) | Low | Discontinued |
| Ektachrome E100VS | 100 | 11 | Extreme (vivid) | Moderate | Discontinued |
| Agfa CT Precisa 100 | 100 | N/A | Natural | Moderate | Discontinued ~2005 |

Sources: Fujifilm datasheets (Velvia 50, Velvia 100, Velvia 100F, Provia 100F, Provia 400X, Astia 100F); Kodak Publications E-4000 (E100), E-4024 (E100G/GX); 125px archived datasheets (E100VS).

---

## 6. Archival Philosophy

### Capture vs. Interpretation

The primary objective of the archival master scan is to preserve the information contained in the original transparency. This is a capture operation, not an interpretation operation.

| Operation | Scanner | Photoshop |
|-----------|---------|-----------|
| Record density and color | Yes | No |
| Set final contrast | No | Yes |
| Correct scanner color cast | No | Yes |
| Apply artistic color grading | No | Yes |
| Remove dust (IR) | Yes (only during scan) | Yes (manual retouching) |
| Apply sharpening | No | Yes |
| Set final saturation | No | Yes |

### Why Aggressive Scanner-Side Correction Is Undesirable

Scanner-side corrections are destructive -- they are baked into the scan data and cannot be reversed. Photoshop corrections applied as adjustment layers are non-destructive and infinitely adjustable.

**Exposure:** Scanner auto-exposure normalizes the histogram, which can clip highlights or crush shadows on contrasty slides. The V600's Auto Exposure is described by practitioners as problematic: it sets black/white points "without regard for the characteristics of the scanned image."

**Sharpening:** Scanner Unsharp Mask creates contrast halos around edges. These halos are then amplified by any subsequent sharpening in Photoshop. Film grain is exaggerated.

**Contrast:** Scanner contrast adjustments compress the tonal range. Information at the extremes is permanently discarded.

**Saturation:** Scanner saturation adjustments can push already-vivid slide colors (especially Velvia) into clipping.

### The Exception: Infrared Dust Removal

Digital ICE / iSRD dust removal is the one scanner-side operation that SHOULD be enabled for E-6 transparencies. The infrared pass happens during scanning and cannot be replicated later. The slight softening is a worthwhile trade-off against hours of manual dust retouching.

### What "Scan Flat" Means for Transparencies

For transparencies, "flat scanning" means: disable all scanner-side tonal and color processing. The resulting scan should closely match the transparency viewed on a light box -- perhaps slightly flat, perhaps with a slight scanner color cast. This is correct. The visual refinement happens in Photoshop.

Unlike negative scanning, a flat transparency scan should already look like a recognizable positive image. It does not require inversion or dramatic tonal transformation.

---

## 7. 48-Bit RGB

### Why 48-Bit for Transparencies

| Property | 24-bit (8-bit/channel) | 48-bit (16-bit/channel) |
|----------|----------------------|------------------------|
| Tonal levels per channel | 256 | 65,536 |
| Quantization steps | Visible after aggressive editing | Invisible even after heavy editing |
| Posterization risk | High after curves/levels | Negligible |
| File size (35mm at 3200 DPI) | ~39 MB | ~78 MB |
| Shadow recovery | Limited before banding | Extensive without artifacts |

**FACT:** The V600 captures 48-bit color (16 bits per channel) both internally and externally. No internal data is discarded on output.

**EVIDENCE:** 16-bit per channel provides 65,536 tonal levels vs. 256 in 8-bit. When applying aggressive Curves adjustments -- common when correcting scanner color casts or expanding shadow detail in dense transparencies -- an 8-bit file's histogram develops visible "combing" (gaps between values), while a 16-bit file maintains a smooth distribution.

**INTERPRETATION:** For transparency scanning, 48-bit capture is particularly important because:

1. The scanner may need significant color correction (V600 has a documented blue/cyan cast tendency)
2. Dense shadow areas contain subtle tonal differences that are lost in 8-bit
3. Velvia's extreme saturation and contrast demand maximum headroom for Curves work
4. Archival masters should preserve maximum downstream flexibility

**RECOMMENDATION:** Always scan transparencies as 48-bit Color. The 2x file size is a trivial cost for archival work. Convert to 8-bit only at final output (print or web export).

### 48-Bit Does Not Create Additional Optical Information

48-bit scanning provides additional numerical precision for the same optical information. It does not:

- Increase scanner resolution
- Improve scanner dynamic range
- Add optical detail
- Capture colors the sensor cannot see

The benefit is entirely in tonal precision during downstream processing.

---

## 8. 3200 DPI: Resolution Investigation

### Is 3200 DPI Rational for the V600?

**EVIDENCE (convergent from multiple independent sources):**

1. **filmscanner.info (measured):** Maximum effective resolution (~1,560 DPI) achieved at 3200 DPI scan setting. Scanning at 6400 DPI yielded identical effective resolution.
2. **Sebastian Schlueter (measured, V700):** Resolution peaked at 3200 SPI; 4800 and 6400 showed no improvement. The V600 is expected to perform slightly worse than the V700.
3. **Flickr V600 user group (measured):** USAF 1951 target tests showed "exactly the same" resolution at 3200 and 6400 DPI, with artifacts introduced at 6400.

### Resolution at Each DPI Setting

| Scan Setting | What Happens | Verdict |
|-------------|-------------|---------|
| 2400 DPI | Captures all or nearly all real detail | Good practical choice |
| 3200 DPI | Slightly oversamples. Captures maximum detail | Maximum useful setting |
| 4800 DPI | No additional real detail. Larger files | Not recommended |
| 6400 DPI | No additional detail. Artifacts. Massive files | Not recommended |

### Print Size Implications

#### 35mm Slides at 3200 DPI

| Parameter | Value |
|-----------|-------|
| Pixel dimensions | ~3,024 x 4,536 px (~13.7 MP nominal) |
| Genuine detail (at ~1,560 effective DPI) | ~1,474 x 2,211 px (~3.3 MP) |
| Native print at 300 PPI (from nominal pixels) | ~10 x 15 inches |
| Sharp print at 300 PPI (from genuine detail) | ~5 x 7 inches |

#### 120 Medium Format at 3200 DPI

| Format | Nominal Pixels | Native Print at 300 PPI | Sharp Print at 300 PPI |
|--------|---------------|------------------------|----------------------|
| 6x4.5 | ~4,200 x 5,300 | 14 x 17.6" | 9 x 11.3" |
| 6x6 | ~7,040 x 7,040 | 23.5 x 23.5" | 11.3 x 11.3" |
| 6x7 | ~7,040 x 8,832 | 23.5 x 29.4" | 11.4 x 14.3" |
| 6x9 | ~7,040 x 10,240 | 23.5 x 34.1" | 11.4 x 16.6" |

**RECOMMENDATION:** 3200 DPI is a sensible, well-supported resolution for the V600. It captures everything the scanner's optics can resolve, provides mild oversampling headroom, and avoids the artifacts and file bloat of higher settings.

---

## 9. Histogram, Density, and the Transparency Scanning Problem

### Transparency Density

| Term | Definition |
|------|-----------|
| Dmin | Minimum density -- the clear film base. The brightest area on a processed transparency. |
| Dmax | Maximum density -- the darkest area a transparency can achieve. |
| Density range | Dmax - Dmin. Determines how much tonal information the transparency contains. |

### How Transparencies Differ from Negatives in the Scanner

With positive/slide film, the relationship between density and image content is direct:

- **Low density (clear/bright film)** = highlights in the image
- **High density (dark/opaque film)** = shadows in the image

This is the SAME direction as the final image -- no inversion is needed. The histogram represents the actual scene tones directly.

**The hard problem is at the shadow end.** The densest shadow areas of the transparency produce very little transmitted light through the scanner. The scanner must amplify this weak signal, introducing noise. The V600's practical Dmax of ~3.0-3.1 means the very densest shadow areas of a well-exposed Velvia 50 transparency (Dmax ~3.8) may not be fully resolved.

For negatives, the hard problem is at the highlight end (which corresponds to the densest film). For transparencies, the problem inverts: highlights are easy (near-clear film) and shadows are hard (dense film).

### Scanner Dynamic Range and Film Demands

| Film | Approximate Dmax | V600 Captures Fully? |
|------|-----------------|---------------------|
| Ektachrome E100 | ~4.0 | No -- deepest shadows lost |
| Provia 100F | Not verified | Likely partial shadow loss |
| Velvia 50 | ~3.8 | No -- deepest shadows lost |
| Velvia 100 | Not verified | Likely partial shadow loss |

**FACT:** Epson states: "Dmax only applies when scanning slides -- the higher the Dmax, the greater the image detail in shadow and highlight areas."

### Histogram Interpretation

A well-exposed transparency should produce a histogram with data distributed across the full range. For transparencies:

- The left side (shadows) represents the densest film -- this is where the V600's dynamic range limitation bites
- The right side (highlights) represents near-clear film -- easy for the scanner
- Auto Exposure is dangerous because it may aggressively clip the shadow end, which represents the information most at risk

### Why the Preview Should Not Be Judged by Appearance Alone

The objective is to preserve recoverable information. A scan that looks slightly flat or dark may contain more useful shadow and highlight detail than a "better-looking" scan where auto-exposure has clipped the endpoints.

---

## 10. Color Management

### The Color Management Chain

```
Film transparency
    |
    v
Scanner (device-dependent RGB)
    |  <-- Scanner ICC profile (input profile)
    v
Profile Connection Space (CIELAB or XYZ)
    |  <-- Rendering intent
    v
Working color space (ProPhoto RGB or Adobe RGB)
    |
    v
Editing in Photoshop
    |
    v
Output:
    |-- Monitor: Display ICC profile (OS-managed)
    |-- Print: Printer+paper ICC profile
    |-- Web: Convert to sRGB
```

### Color Spaces for Transparency Scanning

| Color Space | Gamut Coverage | 16-Bit Required? | Best For |
|------------|---------------|-------------------|----------|
| sRGB | ~35% of visible spectrum | No | Web, screen display |
| Adobe RGB (1998) | ~50% of visible spectrum | Recommended | Print workflows, good compromise |
| ProPhoto RGB | ~90% of visible spectrum | Mandatory | Archival scanning, maximum preservation |

**Why ProPhoto RGB is appropriate for archival transparency scans:**

E-6 slide films (particularly Velvia) produce extremely saturated colors in yellows, reds, and cyans that can fall outside the Adobe RGB gamut. Scanning into sRGB or Adobe RGB permanently clips these colors at the point of capture. ProPhoto RGB preserves essentially all capturable color information.

**CAUTION:** ProPhoto RGB's vast gamut requires 16-bit per channel. With 8-bit, the gaps between adjacent representable colors become large enough to produce visible banding during editing. This is another reason 48-bit scanning is essential.

**Practical recommendation:**

- **Scan/archive:** ProPhoto RGB, 16-bit, TIFF
- **Edit/process:** ProPhoto RGB, 16-bit
- **Print output:** Convert to printer ICC profile or Adobe RGB
- **Web output:** Convert to sRGB, 8-bit, JPEG

### ICC Profiles

| Profile Type | Purpose | Source |
|-------------|---------|--------|
| Scanner profile | Translates scanner's RGB to standardized color | IT8 calibration (SilverFast Ai Studio) |
| Monitor profile | Ensures accurate on-screen color | Hardware calibration (X-Rite, Datacolor) |
| Printer+paper profile | Maps colors to ink on specific paper | Paper manufacturer downloads or custom profiling |

### Working Space vs. Output Space

The working color space (ProPhoto RGB or Adobe RGB) is where editing happens. It is device-independent and standardized. The output space (sRGB for web, printer ICC for print) is device-dependent and describes a specific output device's capabilities.

Never assign a printer ICC profile to your image file. Printer profiles are used only for soft proofing and in File > Print.

---

## 11. IT8 Calibration

### What IT8 Calibration Does

An IT8 target is a standardized color reference chart (ANSI/ISO 12641) consisting of 24 grey fields and 264 color fields. Profiling software scans the target, compares the scanner's RGB values against the known reference values, and creates an ICC profile that describes the scanner's specific color response.

### IT8 for Transparencies

| Standard | Medium | Use Case |
|----------|--------|----------|
| IT8.7/1 (ISO 12641-1) | Color transparency film | Scanning film and slides |
| IT8.7/2 (ISO 12641-2) | Photographic paper | Scanning prints and documents |

Transparency and reflective modes must be calibrated separately -- different light paths produce different color characteristics. For scanning transparency film, you need an IT8.7/1 target. A reflective IT8.7/2 target will not produce a valid profile for film scanning.

### Available Transparency IT8 Targets

| Supplier | Film | Formats | Notes |
|----------|------|---------|-------|
| LaserSoft Imaging (SilverFast) | Fuji Provia 100F | 35mm, 6x7 cm, 4x5" | Barcode for auto-calibration in SilverFast. Fogra-certified. ~$100/set |
| Wolf Faust | Various | Various | ISO-compliant. Cheaper. No barcode. Availability inconsistent |
| Kodak Q-60 | Ektachrome | 4x5" | Production limited/discontinued |

### IT8 with the V600 and SilverFast

**FACT:** SilverFast supports IT8 calibration for the V600. SilverFast's product page states: "The V600 plays to its strength when operated with SilverFast Ai + IT8 target."

**Edition requirement:** IT8 calibration is **Ai Studio and Archive Suite ONLY**. SE and SE Plus cannot create scanner ICC profiles.

**Auto IT8 calibration process:**

1. Place IT8 target in scanner
2. Click Calibration button in SilverFast Ai Studio
3. SilverFast automatically locates the target and reads the barcode
4. Reference data downloaded from LaserSoft's server
5. ICC profile generated (naming: `SF_T_EpsonV600.icc` for transparency)
6. Delta-E displayed (typical: 0.8-1.8; should be below 3)
7. Profile automatically activated

**Recalibration:** Every 1-3 months, or after moving the scanner. Target replacement every 3-4 years (film dyes shift over time).

**Does Epson Scan support IT8?** No. Epson Scan cannot create scanner ICC profiles. However, it can use externally created profiles via ICM mode in Configuration > Color Settings. Limitation: Epson Scan only supports ICC Profile Version 2.

### Why IT8 Matters More for Transparencies Than Negatives

For negatives, the NegaFix film profile dominates the color conversion -- it handles the orange mask removal and inversion that define the output color. For transparencies, there is no NegaFix profile. Color accuracy depends directly on the scanner's raw color response, making calibration accuracy more impactful.

### Metameric Limitation

An IT8 target made on Fuji Provia 100F will not perfectly correct colors when scanning film with different dye chemistry. Delta-E errors as large as 10 have been observed between different film chemistries scanned with a single profile. This is a fundamental physics limitation, not a V600-specific issue.

---

## 12. Epson Scan Professional Mode Workflow

### Software Version Note

**Epson Scan** (original, 32-bit) provides the most complete feature set for the V600, including Digital ICE and Professional Mode.

**Epson Scan 2** (64-bit) is required for macOS Catalina+ and current Windows. It supports transparency scanning but lacks Digital ICE. The Professional Mode interface is restructured.

### Complete Transparency Scanning Procedure

#### Scanner and Film Preparation

1. Remove the white document mat from the scanner lid to expose the transparency unit
2. Clean the scanner glass with a microfiber cloth
3. Clean slides with anti-static brush and blower
4. Insert slides into the holder with **shiny (base) side down**, emulsion (dull) side up
5. Align tab "C" on the slide holder to the "C" marker on the scanner bed

#### Software Settings

| Setting | Value | Rationale |
|---------|-------|-----------|
| Document Type | Film | Activates the transparency unit |
| Film Type | **Positive Film** | No inversion applied -- transparency is already positive |
| Image Type | **48-bit Color** | Maximum tonal depth; 16 bits per channel |
| Resolution | 3200 DPI | Maximum useful setting for V600 |
| Unsharp Mask | **OFF** | Destructive; apply in Photoshop instead |
| DIGITAL ICE Technology | **ON (Quality)** | Works correctly with E-6 film; removes dust via IR |
| Dust Removal | **OFF** | Cannot be used simultaneously with Digital ICE |
| Grain Reduction | **OFF** | Applies blur; destroys fine detail |
| Color Restoration | **OFF** | For faded slides only; can over-correct |
| Backlight Correction | **OFF** | For reflective scans only |
| Descreening | **OFF** | For printed halftone material only |

**In Configuration > Color tab:** Uncheck "Continuous auto exposure." Select ICM/ColorSync mode with Adobe RGB or ProPhoto RGB as target if using a custom scanner profile. Select "No Color Correction" for maximum Photoshop flexibility (but note: this may prevent Digital ICE from functioning -- this could not be fully verified for the V600).

#### Preview and Crop

1. Click **Preview**
2. Switch to **Normal** preview if Thumbnail mode does not frame correctly
3. Draw a marquee around the slide image, excluding the mount edges
4. **Do NOT rely on Auto Exposure** -- disable it and set histogram manually

#### Histogram and Tone

1. Open the **Histogram**
2. Set Output to 0 and 255 (full range)
3. Move Input black point rightward to the edge of the histogram data -- but set it slightly conservative (a short distance from the darkest tones)
4. Move Input white point leftward to the edge of the histogram data -- again slightly conservative
5. Leave gamma at 1.00 (linear) for maximum Photoshop flexibility
6. Set **Tone Correction** to **Linear**

For transparencies, the histogram directly represents the image: left = shadows, right = highlights. Clipping the left side clips shadow detail; clipping the right side clips highlights.

#### Output

1. Click **File Save Settings**
2. Format: **TIFF**, compression: **None** (or LZW)
3. Check **Embed ICC Profile**
4. Set output folder and file naming prefix
5. Click **Scan**

### Settings That Auto-Reset

**Warning:** The Unsharp Mask checkbox and Auto Exposure can re-enable themselves after clicking Preview. Always verify all checkboxes before the final scan.

### Digital ICE and Positive Film

**FACT:** Epson's official documentation confirms Digital ICE supports "Color positive film/slides" with the exception of Kodachrome.

| Film Type | Digital ICE Support |
|-----------|-------------------|
| E-6 color slides (Velvia, Provia, Ektachrome) | Yes -- works well |
| Kodachrome | No -- cyan dye absorbs IR |
| B&W silver slides | No -- silver blocks IR |

**Performance:** Scanning time approximately triples with ICE enabled (practitioner report: 1m30s vs 4m30s per frame at 6400 DPI). Slight image softening occurs from the inpainting algorithm. Preview does NOT show ICE results -- they appear only in the final scan. ICE cannot be used simultaneously with software-based Dust Removal.

### Epson Scan 2 Differences

| Feature | Epson Scan (original) | Epson Scan 2 |
|---------|----------------------|--------------|
| Digital ICE | Yes | **No** |
| Positive Film mode | Yes (Film Type) | Yes (Document Type: Color Positive) |
| 48-bit Color | Yes | Yes |
| Professional Mode | Yes | Restructured interface |
| macOS Catalina+ | No (32-bit only) | Yes (64-bit) |

For macOS users who need Digital ICE, SilverFast with iSRD is the alternative.

---

## 13. SilverFast Workflow

### Editions for the V600

| Edition | Price (USD) | Key Features for Transparencies |
|---------|-----------|-------------------------------|
| SilverFast SE 9 | $49 (free bundled) | Positive mode, WorkflowPilot, SRDx (basic) |
| SilverFast SE Plus 9 | $99 | + Multi-Exposure, AACO, iSRD |
| SilverFast Ai Studio 9 | $249 | + IT8 calibration, 48-bit processed output, JobManager |
| SilverFast Archive Suite 9 | $399 | Ai Studio + HDR Studio (two-stage archival workflow) |

### Critical Edition Limitations

**SE and SE Plus processed output is limited to 24-bit color (8 bits per channel).** They can scan to 48-bit HDR raw files (unprocessed), but applying corrections (curves, color, iSRD) reduces output to 8 bits per channel. **Only Ai Studio can output processed 48-bit TIFF files.**

**Multi-Exposure requires SE Plus or above.** This is a major reason to upgrade from the free SE for transparency scanning.

**IT8 calibration requires Ai Studio.** This is the single biggest feature differentiator.

### Configuring for Positive Transparency

Two settings are required:

1. **Source type:** Select **Transparent** (not Reflective)
2. **Film type:** Select **Positive** (not Negative, not Kodachrome)

For Kodachrome slides, select the third option: **Kodachrome** (loads a special color profile and activates Kodachrome-optimized iSRD).

**NegaFix is NOT used for positive transparencies.** NegaFix is exclusively for negative inversion. When "Positive" is selected, NegaFix is not activated and should not be engaged.

### Color Depth Options

| Setting | Output | Corrections Applied |
|---------|--------|-------------------|
| 48 &#8594; 24 Bit | 24-bit TIFF | Yes (all tools applied) |
| 48 Bit HDR | 48-bit TIFF | No -- scanner-native raw data |
| 64 Bit HDRi | 48-bit RGB + 16-bit IR | No -- raw data + infrared channel |

For archival purposes, **64 Bit HDRi** captures the maximum data including the infrared channel for later iSRD processing in HDR Studio.

### WorkflowPilot

The WorkflowPilot guides through tools in the correct order for positive transparencies:

1. Select Transparent / Positive
2. Set color depth
3. Set resolution
4. Run prescan/preview
5. Activate Multi-Exposure (if available)
6. Activate iSRD
7. Frame detection
8. Auto image optimization
9. Scan

Available in all editions.

### Multi-Exposure

**Confirmed supported on the V600** (SE Plus and above). Performs two scans at different CCD exposure durations and combines them to expand dynamic range.

- First scan: normal exposure (captures highlights)
- Second scan: increased exposure (captures shadow detail)
- Particularly beneficial for transparencies because their density range often exceeds the V600's native capability

**Edition availability:**

| Edition | Multi-Exposure |
|---------|---------------|
| SE | No |
| SE Plus | Yes |
| Ai Studio | Yes |

### AACO (Auto Adaptive Contrast Optimization)

Lightens shadow areas without affecting midtones and highlights. Particularly useful for:

- Underexposed slides
- Velvia with its inherently high contrast (recovers shadow detail without blowing highlights)
- Forest scenes and interiors with deep shadow areas

Available in SE Plus (basic) and Ai Studio (expert sliders: Shadow Width, Saturation, Radius Level).

### iSRD (Infrared Dust Removal)

iSRD uses the same infrared hardware as Digital ICE but with SilverFast's own algorithm. Works excellently with E-6 transparencies.

| Feature | SE | SE Plus | Ai Studio |
|---------|-----|---------|-----------|
| Automatic scratch removal | Yes | Yes | Yes |
| Detection sensitivity control | No | Yes | Yes |
| Selective correction mask | No | No | Yes |
| Defect size control | No | No | Yes |

### Archive Mode (Archive Suite)

Two-stage archival workflow:

1. **Scan to 64-bit HDRi RAW** (48-bit image + 16-bit IR channel). No processing applied.
2. **Process later** in SilverFast HDR Studio non-destructively.

This separates capture from processing, allowing unlimited reprocessing with different settings. The RAW file is never modified.

### SilverFast Transparency Scanning Procedure

1. Set source to **Transparent**, film type to **Positive**
2. Set color depth (48 Bit HDR for archive, 48 &#8594; 24 for quick work)
3. Set resolution to **3200 DPI**
4. Run prescan/preview
5. Enable **Multi-Exposure** (SE Plus/Ai Studio)
6. Enable **iSRD** for dust removal (SE Plus/Ai Studio)
7. Apply **AACO** for shadow optimization if needed (SE Plus/Ai Studio)
8. Set histogram: white and black points at data edges, conservative
9. Adjust per-frame settings via **JobManager** (Ai Studio)
10. Scan to TIFF

### Output Formats by Edition

| Format | SE | SE Plus | Ai Studio |
|--------|-----|---------|-----------|
| TIFF 8-bit processed | Yes | Yes | Yes |
| TIFF 16-bit processed | **No** | **No** | Yes |
| TIFF 48-bit HDR raw | Yes | Yes | Yes |
| TIFF 64-bit HDRi raw | Yes | Yes | Yes |
| DNG (HDR raw) | Yes | Yes | Yes |

---

## 14. Epson Scan vs. SilverFast Comparison

| Aspect | Epson Scan | SilverFast |
|--------|-----------|------------|
| **Cost** | Free (bundled) | Free SE; paid upgrades |
| **Positive film mode** | Film Type: Positive Film | Transparent + Positive |
| **Histogram** | Basic | Advanced (Ai Studio: 3-part, 16-bit, densitometer) |
| **Curves** | Basic Tone Correction | Gradation with channel control |
| **IR dust removal** | Digital ICE (Epson Scan only) | iSRD (SE Plus/Ai Studio) |
| **Multi-Exposure** | Not available | Available (SE Plus/Ai Studio) |
| **IT8 calibration** | Not available | Available (Ai Studio) |
| **48-bit processed output** | Yes (all modes) | **Only Ai Studio** |
| **Archival RAW** | Not available | 64-bit HDRi (Archive Suite) |
| **Batch scanning** | Multiple marquees | JobManager (Ai Studio) |
| **Ease of use** | Simple, familiar | Steeper learning curve |
| **macOS Catalina+** | Epson Scan 2 (no ICE) | Full feature set |

### Which Workflow for Which Situation?

| Situation | Recommendation |
|-----------|---------------|
| Quick scanning of a few slides | Epson Scan -- simpler interface, Digital ICE |
| Archival quality, maximum shadow detail | SilverFast Ai Studio -- Multi-Exposure, IT8, 48-bit |
| Dense Velvia 50 slides | SilverFast SE Plus/Ai Studio -- Multi-Exposure essential |
| Batch scanning large slide collections | SilverFast Ai Studio -- JobManager |
| macOS Catalina or later | SilverFast -- Epson Scan 2 lacks Digital ICE |
| Budget-conscious | Epson Scan + Photoshop -- do color work in Photoshop |
| Maximum color accuracy | SilverFast Ai Studio with IT8 transparency target |

---

## 15. Digital ICE and Color Transparency

### How Infrared Dust Detection Works with E-6 Film

E-6 process films use chromogenic dyes (cyan, magenta, yellow) that absorb primarily in the visible spectrum (400-700 nm). Their molecular structure does not have significant absorption bands in the near-infrared region (~700-1000 nm), making them largely transparent to IR light.

During an IR-equipped scan:

1. **RGB pass:** Visible-light scan captures the image
2. **IR pass:** Infrared scan at the same position and resolution
3. Because E-6 dyes are IR-transparent, the IR image appears nearly uniform (the dye image is invisible to infrared)
4. Surface defects (dust, scratches, fingerprints) block or scatter IR light and appear as dark marks
5. The software generates a spatial defect map and inpaints affected areas using surrounding pixel data

### Film Compatibility

| Film Type | Digital ICE / iSRD | Why |
|-----------|-------------------|-----|
| E-6 color slides (Velvia, Provia, Ektachrome) | Works correctly | Dyes transparent to IR |
| Kodachrome (K-14) | Incompatible (standard ICE) | Cyan dye absorbs IR |
| Chromogenic B&W (XP2 Super) | Works correctly | Dye-based image |
| Traditional silver B&W | **NEVER USE** | Silver opaque to IR |

### Digital ICE on the V600

| Feature | Epson Scan | Epson Scan 2 | SilverFast |
|---------|-----------|--------------|------------|
| IR dust removal | Digital ICE (Quality / Lite) | Not available | iSRD (SE Plus/Ai Studio) |
| Control level | Binary on/off | N/A | Manual sensitivity, masks, eraser |
| Kodachrome support | No | N/A | Yes (Kodachrome-specific engine + manual masking) |

### Epson Digital ICE vs. SilverFast iSRD

| Aspect | Digital ICE | SilverFast iSRD |
|--------|-------------|-----------------|
| Technology | Kodak/ASF IR algorithm | LaserSoft proprietary algorithm |
| User control | On/off only | Full manual adjustment per region |
| Kodachrome | Incompatible | Supported with Kodachrome mode + masking |
| Masking tools | None | Polygon/path masks, marker, eraser |
| Integration | Built into Epson Scan driver | Runs in SilverFast application |

### Possible Artifacts

- **Overall image softening** -- the inpainting algorithm averages surrounding pixels, which can blur fine textures and grain
- **Edge artifacts** around high-contrast boundaries
- **False positives** -- image features incorrectly identified as defects (rare with E-6)
- **Embedded emulsion scratches** (not surface scratches) are NOT reliably detected

### Dense Shadow Areas

Very dense shadow areas of any slide film reduce IR transmission. The denser the film, the less IR light passes through, making it harder to distinguish defects. This is generally minor for standard E-6 films but can affect extremely dense shadow areas of Velvia 50. SilverFast's manual masking tools in iSRD address this by allowing exclusion of problematic dense regions.

### Recommendation

For standard E-6 transparencies on the V600: enable Digital ICE (Epson Scan) or iSRD (SilverFast). The slight softening is a worthwhile trade-off against manual dust retouching. For high-value frames destined for large prints, combine IR cleaning with targeted Photoshop retouching.

---

## 16. Grain, Noise, and Detail

### Distinguishing Film Character from Scanner Artifacts

| Artifact | Origin | Character | Desired? |
|----------|--------|-----------|----------|
| Film grain | Dye clouds from E-6 development | Organic, fine, film-stock-dependent | Yes -- part of photographic identity |
| Scanner noise | CCD sensor electronics | Regular patterns, color speckles, worse in shadows | No -- technical artifact |
| Chroma noise | CCD channel imbalance | Color speckles in uniform areas | No |
| Sharpening artifacts | USM applied too aggressively | Halos around high-contrast edges | No |
| Digital ICE softening | IR inpainting algorithm | Slight loss of micro-detail | Acceptable trade-off |

### Film Grain by Stock

| Film | RMS Granularity | Grain Character |
|------|----------------|-----------------|
| Velvia 50 | 9 | Very fine, visible at high magnification but organic |
| Provia 100F | 8 | Extremely fine, scans very cleanly |
| Ektachrome E100 | 8 | Smooth T-GRAIN structure, designed for scanning |
| Provia 400X | 11 | Fine for ISO 400; more visible |
| Ektachrome E100VS | 11 | Coarser grain (trade-off for extreme saturation) |
| Astia 100F | 7 | Finest grain of any ISO 100 slide film ever made |

### Grain Preservation Strategy

- **Never apply global noise reduction to a well-exposed E-6 film scan.** It destroys the film character.
- E-6 transparency grain is extremely fine (RMS 7-9 for ISO 100 stocks) and should be nearly invisible in normal prints
- Scanner noise is more visible in the dense shadow areas where the V600's dynamic range is stressed
- Reduce scanner noise selectively: target shadow areas only using luminosity masks
- **Order of operations:** Noise reduction BEFORE sharpening

---

## 17. Sharpening

### Scanner Sharpening vs. Photoshop Sharpening

| Property | Scanner Sharpening | Photoshop Sharpening |
|----------|-------------------|---------------------|
| Control | 3 presets (Low/Medium/High) | Full Amount/Radius/Threshold/Masking |
| Destructive | Yes (baked into scan) | No (adjustment layers, Smart Filters) |
| Grain impact | Exaggerates grain | Controllable via masking |
| Reversible | No | Yes |
| Output-dependent | No (one size fits all) | Yes (different for print, web, paper type) |

### Three-Stage Sharpening Framework (Bruce Fraser / Jeff Schewe)

| Stage | Purpose | When |
|-------|---------|------|
| Capture sharpening | Compensate for scanner optical softness | After scanning, early in workflow |
| Creative sharpening | Artistic emphasis on specific areas | During editing, with masks |
| Output sharpening | Compensate for ink dot gain / paper absorption | Last step, after resizing |

### Capture Sharpening for V600 Transparency Scans

**Smart Sharpen (recommended):**

| Parameter | Setting | Rationale |
|-----------|---------|-----------|
| Remove | Lens Blur | Detects edges, thinner halos than Gaussian Blur |
| Amount | 100-150% | Restores edge definition without over-sharpening grain |
| Radius | 1.0-2.0 px | Matches scale of V600 flatbed optical softness |
| Reduce Noise | 5-10% | Prevents grain amplification |

After applying: Edit > Fade > blend mode Luminosity, opacity 70-80%. Prevents color fringing.

### Creative Sharpening (High Pass Filter)

1. Stamp visible (Ctrl+Alt+Shift+E)
2. Set blend mode to Overlay or Soft Light
3. Filter > Other > High Pass. Radius 1.0-2.0px for 35mm, 2.0-4.0px for medium format
4. Add black layer mask, paint white only where emphasis is desired
5. Reduce opacity to 40-70%

### Output Sharpening

Applied LAST, after resizing to final print dimensions. See Section 23.

---

## 18. Photoshop Workflow

### Color Correction vs. Color Grading

This distinction is critical for transparency film:

**Color correction** restores accuracy. The goal: the scan on a calibrated monitor matches the transparency on a calibrated light box. Scanner color casts are removed. Tonal balance is restored. This is a technical operation.

**Color grading** is a deliberate artistic departure from accuracy. The photographer shifts colors away from neutrality to establish mood or atmosphere. This is a creative operation.

**Why the order matters:** The original transparency is a physical color reference. If you grade before correcting, you cannot distinguish a scanner-introduced cast from a deliberate artistic shift. Always correct first (clean baseline matching the slide), then grade on top.

### Two Explicit Workflows

#### Workflow A: Faithful Archival Reproduction

The goal: reproduce the transparency as accurately as practical on screen and in print.

Operations appropriate:
- Scanner color cast removal
- Accurate black/white point setting
- Neutral point correction (gray eyedropper on known neutral)
- Minimal contrast adjustment to match transparency appearance
- Dust and scratch removal
- Capture sharpening only

Operations NOT appropriate:
- Saturation boost or reduction
- Color grading / split toning
- Creative contrast enhancement
- Heavy dodge and burn
- Selective color manipulation beyond cast correction

#### Workflow B: Fine Art Interpretation

The goal: use the transparency as source material for a deliberate artistic interpretation.

All operations are appropriate, including:
- Creative color grading
- Split toning
- Contrast enhancement beyond the transparency's characteristics
- Selective saturation adjustments
- Creative dodge and burn
- Local contrast enhancement
- LUT application

The transparency informs but does not constrain the final result.

### Recommended Layer Stack (Bottom to Top)

```
[TOP]
  Output Sharpening (stamp visible, High Pass, Overlay) -- print copy only
  Dodge & Burn (50% gray layer, Overlay mode)
  Color Grading (Curves / Color Balance / Gradient Map) -- Workflow B only
  Selective Color (targeted hue refinements)
  Curves: S-curve contrast (blend mode: Luminosity)
  Curves: Per-channel color correction
  Levels: Black/white point
  Capture Sharpening (Smart Sharpen as Smart Filter)
  Dust retouching layer (empty layer, "Sample Current & Below")
  Background: Original 48-bit TIFF scan (convert to Smart Object)
[BOTTOM]
```

### Step 1: Preserve Original Scan

Open the 48-bit TIFF. Verify Image > Mode shows RGB Color and 16 Bits/Channel. Save as PSD immediately. Convert Background to Smart Object to protect original data.

### Step 2: Dust and Scratch Removal

Create an empty layer above the Background. Set retouching tools to "Sample: Current & Below."

**On transparencies, dust appears as DARK spots** (dust blocks transmitted light). Set Spot Healing Brush blending mode to **Lighten** to affect only the dark dust particles without touching correctly-exposed pixels.

| Tool | Best For | Notes |
|------|----------|-------|
| Spot Healing Brush (J) | Isolated dust spots, smooth areas | Set to Content-Aware, Lighten mode |
| Healing Brush (J) | Smooth gradients (sky, water) | Blends color/luminosity from source |
| Clone Stamp (S) | Edges, scratches, complex textures | Preserves grain character from source |
| Remove Tool (PS 2024+) | Complex areas | Standard mode preserves grain better than Generative AI mode |

**Systematic dust removal:**

1. **Phase 1 -- Dust & Scratches filter + History Brush (fastest for heavy dust):**
   - Duplicate Background
   - Filter > Noise > Dust & Scratches (Radius 3-5px, Threshold ~20)
   - Take History Snapshot of filtered state
   - Undo filter (Ctrl+Z)
   - Select History Brush, set source to filtered snapshot, mode Lighten
   - Paint over dust spots -- only dark particles affected

2. **Phase 2 -- Manual cleanup at 100% zoom:**
   - Work in serpentine grid pattern across entire image
   - Click remaining specks with Spot Healing Brush (Lighten mode)
   - Switch to Clone Stamp near edges

3. **Phase 3 -- Quality check:**
   - Add temporary extreme S-curve Curves layer to exaggerate contrast (makes subtle dust visible)
   - Delete the Curves layer when done

### Step 3: Capture Sharpening

Apply Smart Sharpen as described in Section 17. Convert to Smart Filter for non-destructive application.

### Step 4: Set Black and White Points

**Threshold technique:**

1. Add Threshold adjustment layer
2. Drag slider left (white), then slowly right -- first meaningful black patch = darkest point. Place Color Sampler marker #1.
3. Drag slider right (black), then slowly left -- first meaningful white patch = brightest point. Place marker #2.
4. Delete Threshold layer

**Apply Levels:**

1. Add Levels adjustment layer
2. Double-click Set Black Point eyedropper: set R:10, G:10, B:10 (not 0,0,0 -- preserves shadow detail in prints)
3. Double-click Set White Point eyedropper: set R:245, G:245, B:245 (not 255,255,255 -- prevents blown highlights)
4. Click Black Point eyedropper on marker #1
5. Click White Point eyedropper on marker #2

**Alternative:** Hold Alt/Option while dragging black/white input sliders to see clipping preview.

### Step 5: Color Cast Correction (Curves Per-Channel)

Add Curves adjustment layer. The V600 has a documented blue/cyan cast tendency.

**Channel relationships:**
- Red: raise = add red; lower = add cyan
- Green: raise = add green; lower = add magenta
- Blue: raise = add blue; lower = add yellow

**Workflow:**

1. Open Info panel. Hover over areas that should be neutral; RGB values reveal the cast direction
2. If a gray area reads R:128, G:135, B:140 -- there is a blue/cyan cast
3. Select the Blue channel in Curves, lower the midtone/highlight area slightly
4. Color casts from the V600 often vary with luminance -- shadows and highlights may need different corrections. Place separate control points.

**Gray eyedropper method:**

1. In Levels or Curves, select Set Gray Point eyedropper
2. Click on any area that should be neutral (concrete, asphalt, a gray card)
3. Set Sample Size to 5x5 Average or 11x11 Average

**V600-specific correction notes:**

| Film Stock | V600 Tendency | Correction |
|-----------|---------------|------------|
| Velvia 50/100 | May exaggerate warm cast | Pull back Red channel slightly in midtones |
| Provia 100F | Blue/cyan cast most apparent (Provia is supposed to be neutral) | Gray eyedropper works well |
| Ektachrome E100 | Blue cast compounds E100's natural cool tendency | May need more Blue channel reduction |

**Save corrections as Curves presets** for batch consistency across a roll.

### Step 6: Contrast (Curves S-Curve)

Add Curves adjustment layer. Set blend mode to **Luminosity** (adds contrast without affecting saturation).

- Shadows: pull down slightly at ~25% point
- Highlights: pull up slightly at ~75% point
- Adjust opacity to taste
- For Velvia (already high contrast): gentle S-curve or skip entirely
- For Ektachrome E100 (low contrast tone scale): more pronounced S-curve may be appropriate

### Step 7: Selective Color

Add Selective Color adjustment layer for targeted hue refinements.

| Subject | Target Color | Adjustments |
|---------|-------------|-------------|
| Greens in landscapes (Velvia) | Greens + Yellows | Greens: Cyan +5 to +15, Yellow +slight |
| Skin tones (Provia/Ektachrome) | Reds + Yellows | Reds: Cyan -slight, Magenta -slight, Yellow +slight |
| Skies | Cyans + Blues | Increase Cyan and Magenta slightly |

Use Relative mode for subtle corrections, Absolute for stronger shifts.

### Step 8: Luminosity Masks

Luminosity masks select pixels based on brightness, creating self-feathering selections that precisely target tonal ranges.

**Why they are critical for slide scan processing:** E-6 film can have different color casts in different tonal zones. A global Curves correction that fixes midtones may make shadows or highlights worse. Luminosity masks allow zone-specific correction.

**Creating basic luminosity masks:**

1. Ctrl+click the RGB channel thumbnail -- loads a Lights selection
2. Save as new channel (Select > Save Selection)
3. To narrow: Ctrl+Shift+click the same channel to intersect ("Light Lights")
4. To create Darks: Invert the Lights selection (Ctrl+Shift+I)

**Practical application:** Load Darks 2-3 > add Curves > correct shadow color cast per-channel. Set Curves blend mode to Color to avoid tonal shifts.

**16-bit luminosity masks via Image > Calculations:** Image > Calculations with both sources set to RGB channel, Blending = Multiply, Result = New Channel. This avoids the 8-bit selection bottleneck of Ctrl+click.

**Panels:** TK Actions (Tony Kuyper), Lumenzia (Greg Benz) automate luminosity mask creation.

### Step 9: Dodge and Burn

**50% Gray Layer method:**

1. Alt+click New Layer icon. Name "Dodge & Burn", Mode: Overlay, fill with 50% gray
2. Paint with white to dodge (lighten), black to burn (darken)
3. Brush: Soft round, Flow 5-10%, Opacity 100%
4. Build up gradually. Paint 50% gray (#808080) to erase any stroke.

### Step 10: Local Contrast Enhancement

**High Pass + Overlay method:**

1. Stamp visible (Ctrl+Alt+Shift+E)
2. Set blend mode to Overlay or Soft Light
3. Filter > Other > High Pass. Radius: 30-80 pixels (local contrast, NOT detail sharpening)
4. Reduce opacity to taste
5. Double-click layer > Blending Options > Blend If: exclude pure blacks and whites

### Step 11: Color Grading (Workflow B Only)

**Camera Raw Filter (non-destructive):**

1. Convert layer to Smart Object
2. Filter > Camera Raw Filter (Ctrl+Shift+A)
3. Key tools: Color Grading wheels (Shadows/Midtones/Highlights), Color Mixer/HSL, Point Color

**Film-specific grading guidance:**

- **Velvia:** Do NOT push Master Saturation -- Velvia's channel-specific saturation produces garish results with global boost. Enhance midtone contrast instead. Preserve the designed warmth.
- **Provia:** Neutral baseline accepts grading gracefully. Light split toning works well.
- **Ektachrome E100:** Embrace the cooler character for appropriate subjects. Warm with Photo Filter (Warming 81, density 10-15%) when needed.

**Color Lookup Tables (LUTs):**

1. Build grade using only adjustment layers
2. File > Export > Color Lookup Tables, select CUBE format
3. Apply to other scans from the same roll
4. Load via Layer > New Adjustment Layer > Color Lookup

---

## 19. Upscaling and Large Prints

### When Upscaling Is Needed

| Scenario | Upscaling Needed? |
|---------|-------------------|
| 35mm V600 scan, print 8x10 | Borderline -- depends on viewing distance |
| 35mm V600 scan, print 16x20 | Yes, definitely |
| 6x7 V600 scan, print 16x20 | No -- native resolution sufficient |
| 6x7 V600 scan, print 24x30 | Borderline |
| 6x7 V600 scan, print 40x60 | Yes, but viewing distance forgives |

### Viewing Distance and Required Resolution

| Print Size | Typical Viewing Distance | Minimum PPI |
|-----------|------------------------|-------------|
| 8x10 in | ~19 in (close) | 300 PPI |
| 16x20 in | ~38 in (~1m) | 200 PPI |
| 24x36 in | ~65 in (~1.7m) | 150 PPI |
| 40x60 in | ~108 in (~2.7m) | 100 PPI |

### Upscaling Methods

#### Camera Raw Super Resolution

- Doubles linear resolution (4x total pixels) using machine learning
- Open TIFF via Bridge > "Open in Camera Raw" > right-click > Enhance > Super Resolution
- Fixed 2x only. Cannot be applied twice.
- 48-bit TIFF supported
- Best from high-quality, low-noise sources -- fine-grained E-6 scans are ideal

#### Photoshop Preserve Details 2.0

- Image > Image Size > Resample: Preserve Details 2.0
- Must manually select (Automatic does NOT choose it)
- Reduce Noise slider: ~20% for clean E-6 scans
- Practical limits: 160-200% reliable; 400% noticeably degraded; beyond 4x severe diminishing returns

#### Third-Party AI Upscaling (Topaz Gigapixel)

- More aggressive detail reconstruction than Photoshop
- Can produce sharper results but with more artifacts
- May impose digital-looking artifacts on film grain
- For slide scans, Photoshop's conservative approach may better preserve organic film character

### Three Categories of "Detail"

| Type | What It Is | Visual Character |
|------|-----------|-----------------|
| Genuine detail | Information captured by scanner optics | Sharp at native resolution; softens when enlarged |
| Interpolated detail | New pixels calculated from neighbors | Smooth, soft, no new edges |
| AI-hallucinated detail | Synthesized by neural network | Can look plausible but may contain invented features |

**AI-generated detail is NOT recovered original information.** It is statistically plausible reconstruction. For Fine Art photography where authenticity matters, this distinction is important.

---

## 20. Fine Art Print Workflow

### Print Preparation Chain

**Working PSD** &#8594; **Resize to print dimensions** &#8594; **Soft proof** &#8594; **Adjust for print** &#8594; **Convert to profile** &#8594; **Output sharpen** &#8594; **Save print master** &#8594; **Print**

### Soft Proofing

View > Proof Setup > Custom:

| Setting | Value |
|---------|-------|
| Device to Simulate | ICC profile for your printer/paper combination |
| Preserve RGB Numbers | Unchecked |
| Rendering Intent | Start with Relative Colorimetric; compare with Perceptual |
| Black Point Compensation | Checked |
| Simulate Paper Color | Recommended for realistic preview |

Toggle soft proof: Ctrl+Y. Toggle gamut warning: Ctrl+Shift+Y.

### Rendering Intent

| Intent | Best For | Effect |
|--------|----------|--------|
| Perceptual (+ BPC) | Images with many saturated out-of-gamut colors; Velvia slides | Compresses entire range proportionally |
| Relative Colorimetric (+ BPC) | Images where most colors are in-gamut; Provia, Ektachrome | Preserves in-gamut colors exactly; clips OOG |

For Velvia's extreme saturation: **Perceptual** is generally preferred because it gracefully compresses the wide gamut rather than hard-clipping out-of-gamut colors.

### Black Point Compensation

Always enable. Maps source black to destination black, preventing crushed shadows.

### Tonal Adjustment for Paper

Monitor contrast ratio: ~1000:1. Glossy print: ~200:1. Matte print: ~100:1.

On a duplicate/proof copy:
1. Lift deepest shadow point from 0 to ~10-15 for matte (prevents blocked shadows)
2. Increase midtone contrast with gentle S-curve (compensates for overall flattening)
3. Targeted dodging/burning for critical shadow areas

### Output Sharpening

Applied LAST, after resizing to final print dimensions.

| Paper Type | USM Amount | USM Radius | Notes |
|-----------|-----------|-----------|-------|
| Glossy / Baryta | 100-150% | 0.8-1.2 px | Less sharpening needed; halos visible |
| Matte / Cotton Rag | 150-250% | 1.0-2.0 px | Ink absorption dulls perceived sharpness |
| Fine Art Textured | 200-300% | 1.0-1.5 px | Paper texture masks some detail |

**High Pass method:** Stamp visible, blend mode Overlay, High Pass radius 1.0-3.0 px, reduce opacity to taste.

**The output-sharpened file should look slightly oversharpened on screen.** If it looks right on screen, it will look soft in print.

### Paper Selection for Color Transparency Prints

| Paper | Base | Key Characteristics | Best For |
|-------|------|---------------------|----------|
| Hahnemuhle Photo Rag Baryta 315 | 100% cotton + baryta, OBA-free | Deep blacks, warm tone, high Dmax | Velvia landscape prints |
| Hahnemuhle Fine Art Baryta 325 | Alpha-cellulose + baryta | High gloss, darkroom fiber look | Maximum color vibrancy |
| Hahnemuhle Photo Rag 308 | 100% cotton, OBA-free | Fine art standard, matte | Contemplative, subdued rendering |
| Canson Infinity Platine Fibre Rag | 100% cotton + platine, OBA-free | Museum-grade, slight sheen | Archival exhibition prints |

For maximum fidelity to slide film characteristics (high saturation, vivid colors), luster or baryta papers are the best match. Baryta papers offer Dmax up to 2.99 -- the closest inkjet can approach to the depth of the original transparency.

Matte papers produce a more subdued rendering. Deep shadows render as dark charcoal, not true black.

### Print Driver Settings

**Photoshop Manages Colors:** File > Print > Color Handling: Photoshop Manages Colors. Select paper ICC profile. Same rendering intent as soft proofing. BPC ON.

**CRITICAL:** Disable color management in printer driver ("Off - No Color Adjustment" for Epson) to prevent double profiling. Double profiling produces unpredictable, usually oversaturated results.

---

## 21. Archival Master Scan

### What Constitutes an Archival Master

| Property | Specification | Rationale |
|----------|--------------|-----------|
| Format | TIFF, uncompressed | Universal, lossless, guaranteed readable for decades |
| Bit depth | 48-bit Color (16 bits/channel) | Maximum tonal information |
| Resolution | 3200 DPI | Maximum useful for V600 |
| Compression | None (LZW acceptable) | Zero risk of data loss |
| ICC profile | Embedded (Adobe RGB or ProPhoto RGB) | Ensures correct interpretation |
| Scanner adjustments | Minimal -- linear tone, no sharpening, no grain reduction | Preserves raw information |
| Dust removal | Digital ICE / iSRD ON for E-6 | Cannot be replicated later |

### File Hierarchy

| Tier | Format | Purpose | Example |
|------|--------|---------|---------|
| Archival master | 48-bit TIFF, uncompressed | Untouched scan. Never edited. | `_master.tif` |
| Working master | Photoshop PSD with layers | All editing happens here | `_work.psd` |
| Print master | Flattened TIFF, output-sharpened | One per paper/size combination | `_print_Baryta315_16x20.tif` |
| Web derivative | sRGB JPEG, long edge ~2048px | Portfolio, social media | `_web.jpg` |

### Naming Convention

Format: `YYYY-MM_RollNNN_FNN_FilmStock_master.tif`

Example: `2026-03_R045_F12_Velvia50_master.tif`

### Checksum Verification

```bash
sha256sum *.tif > checksums.sha256
```

Verify after any file transfer or backup:

```bash
sha256sum -c checksums.sha256
```

### Storage: 3-2-1 Rule

Three copies, on two different media types, one off-site. The physical transparencies remain the ultimate master -- store in acid-free archival sleeves, cool and dry conditions. Kodak specifies 80+ years dark storage stability for Ektachrome E100 at 10°C / 50°F, 15-20% RH.

---

## 22. Controlled Test Matrix

### Purpose

Validate the scanning workflow through systematic comparison using the same transparency.

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
| B1 | 48-bit Color | Maximum post-processing flexibility |
| B2 | 24-bit Color | Adequate for simple corrections; banding risk with heavy edits |

#### Software Comparison

| Scan | Software | Key Difference |
|------|----------|---------------|
| S1 | Epson Scan Professional Mode | Digital ICE, simpler interface |
| S2 | SilverFast (with Multi-Exposure) | Extended dynamic range, IT8 |

#### Dust Removal Comparison

| Scan | Method | Notes |
|------|--------|-------|
| D1 | No correction | Baseline |
| D2 | Digital ICE (Epson Scan) | Hardware IR cleaning |
| D3 | SilverFast iSRD | Hardware IR cleaning (SilverFast algorithm) |
| D4 | Photoshop retouching (on D1) | Manual post-scan |

#### Color Management Comparison (if Ai Studio available)

| Scan | Profile | Notes |
|------|---------|-------|
| C1 | No custom profile | Generic scanner response |
| C2 | IT8-calibrated transparency profile | Scanner-specific ICC profile |

### Evaluation Grid

| Criterion | Method |
|-----------|--------|
| Shadow detail | 100% view of deep shadow areas (dense transparency) |
| Highlight detail | 100% view of brightest textured areas (near-clear film) |
| Color accuracy | Compare to transparency on calibrated light box |
| Tonal smoothness | Examine smooth gradients for posterization/banding |
| Grain character | 100% view of mid-density uniform areas |
| Sharpness | Edge definition of fine detail at 100% |
| Artifacts | Halos, banding, edge artifacts, ICE/iSRD artifacts |
| Dust/scratches | Compare visibility and removal effectiveness |
| File size | Measure and record |
| Processing flexibility | Perform aggressive Curves manipulation; compare banding |

### Test Transparencies

**Minimum set:**

1. Velvia 50 (high contrast, high saturation, high Dmax) -- 35mm
2. Provia 100F (neutral, moderate contrast) -- 35mm or 120
3. Ektachrome E100 (low contrast, scanner-optimized) -- if available

Each test transparency should contain: deep shadows with detail, bright highlights with texture, smooth gradients, saturated colors, fine detail, visible grain at 100%.

---

## 23. Troubleshooting: Failure Modes

### Muddy Shadows

**Symptom:** Deep shadows lack separation; dark tones merge.

**Likely causes:**
- Black point set too aggressively (clipping shadow data)
- Dense transparency exceeding V600's practical Dmax (~3.0-3.1)
- Scanner noise in dense shadow areas

**Diagnosis:** Check histogram -- if shadow end is clipped, the scan lost data. If data is there but compressed, the problem is in Photoshop curves.

**Corrective actions:** Re-scan with conservative black point. Use Multi-Exposure (SilverFast SE Plus/Ai Studio). Use luminosity masks (Darks 2-3) to selectively lift shadows. Accept that the V600 has Dmax limitations for very dense Velvia slides.

**Do NOT:** Apply global Brightness increase (shifts entire range, may clip highlights).

### Clipped Highlights

**Symptom:** Brightest areas show no texture; pure white with no detail.

**Likely causes:** White point set too aggressively. Auto Exposure enabled.

**Corrective actions:** Re-scan with conservative white point. Disable Auto Exposure. In Photoshop, pull highlight Curves endpoint down slightly.

### Flat Colors / Low Saturation

**Symptom:** Colors appear washed out compared to the transparency on a light box.

**Likely causes:** This may be expected for an uncorrected scan (scanner color response differs from visual perception). "No Color Correction" mode produces deliberately unsaturated output.

**Corrective actions:** Apply IT8 profile. Adjust Curves per-channel. Use Vibrance (not Saturation) for gentle boost. For Velvia, a slight Vibrance increase may be appropriate; for Provia, correction of gray balance often restores perceived saturation.

**Do NOT:** Push Master Saturation slider aggressively -- especially with Velvia, where channel-specific saturation produces garish results from global boost.

### Oversaturated Colors

**Symptom:** Colors appear more vivid than the original transparency, especially reds and greens.

**Likely causes:** Scanner color response differs from E-6 dye spectral characteristics. Auto Color Restoration enabled. Saturation slider pushed during scanning.

**Corrective actions:** Disable Color Restoration and all auto adjustments. IT8 calibration addresses systematic color response errors. In Photoshop, use Selective Color to target specific problematic hues rather than global desaturation.

### Color Casts

**Symptom:** Overall color shift not present in the original transparency.

**Likely causes:** V600's documented blue/cyan cast tendency. Incorrect color management settings. No scanner ICC profile.

**Corrective actions:** Gray eyedropper on known neutral point. Per-channel Curves correction. IT8 calibration for systematic fix. Save Curves preset for batch correction.

### Excessive Softness

**Symptom:** Image lacks sharpness beyond expected V600 limitations.

**Likely causes:** Film not at optimal focal distance. Digital ICE softening. V600's inherent optical limitation (~1,560 DPI effective).

**Corrective actions:** Ensure film is flat in holder. Try aftermarket holders with height adjustment. Apply capture sharpening in Photoshop. If using Digital ICE, compare with and without -- slight softening is expected.

### Digital ICE Artifacts

**Symptom:** Unusual blotching, smearing, or detail loss in specific areas.

**Likely causes:** Kodachrome scanned with ICE enabled (cyan dye blocks IR). Very dense shadow areas where IR cannot penetrate. Film damage misidentified.

**Corrective actions:** For Kodachrome: disable ICE entirely. For dense shadows: use SilverFast iSRD with manual masking to exclude problem areas. Combine IR cleaning with manual Photoshop retouching.

### Newton Rings

**Symptom:** Concentric rainbow-like rings visible in the scan.

**Likely causes:** Film base in optical contact with scanner glass or holder surface.

**Corrective actions:** Ensure film does not touch glass directly. Anti-Newton Ring (ANR) glass holders (not included with V600, available as aftermarket upgrade).

### Inconsistent Color Between Scans

**Symptom:** Same slide produces different color renderings on different scan sessions.

**Likely causes:** Scanner LED warm-up variation. Auto Exposure re-engaging. Different software settings.

**Corrective actions:** Allow scanner to warm up (~5 minutes). Verify Auto Exposure is disabled. IT8 calibration provides a consistent baseline. Save and reuse settings presets.

### Banding / Posterization

**Symptom:** Visible steps in smooth gradients.

**Likely causes:** Working in 8-bit mode. Excessive tonal manipulation. JPEG compression.

**Corrective actions:** Always work in 16-bit mode until final output. Save masters as 16-bit TIFF. Convert to 8-bit only for final JPEG/print output.

---

## 24. Evidence Table

| Claim | Source | Source Type | Confidence | V600-Specific? |
|-------|--------|------------|-----------|---------------|
| V600 optical resolution 6400 x 9600 DPI | Epson official specs | Manufacturer | High | Yes |
| V600 effective resolution ~1,560 DPI | filmscanner.info USAF test | Independent test | High | Yes |
| 3200 and 6400 DPI yield identical effective resolution | filmscanner.info, Flickr V600 group | Independent tests | High | Yes |
| V600 practical Dmax ~3.0-3.1 | Practitioner estimates | Forum consensus | Medium | Yes (estimated) |
| V600 claimed Dmax 3.4 | Epson official specs | Manufacturer | High | Yes |
| Velvia 50 RMS granularity 9 | Fujifilm datasheet AF3-0221E2 | Manufacturer | High | N/A (film) |
| Velvia 50 Dmax ~3.8 | Fujifilm characteristic curves, kenrockwell.com | Mixed | Medium-High | N/A (film) |
| Provia 100F RMS granularity 8 | Fujifilm datasheet RDPIII | Manufacturer | High | N/A (film) |
| Ektachrome E100 RMS granularity 8 | Kodak Publication E-4000 | Manufacturer | High | N/A (film) |
| Ektachrome E100 designed for scanning | Kodak Publication E-4000 | Manufacturer | High | N/A (film) |
| E-6 dyes transparent to infrared | Physics of chromogenic dyes | Established science | High | N/A (physics) |
| Kodachrome cyan dye absorbs IR | Multiple documented sources | Established knowledge | High | N/A (film) |
| Digital ICE not available in Epson Scan 2 | Epson support, practitioner confirmation | Manufacturer + users | High | Yes |
| Digital ICE works with E-6 slides | Epson official documentation | Manufacturer | High | Yes |
| SilverFast Multi-Exposure supported on V600 | SilverFast V600 product page | Manufacturer | High | Yes |
| SilverFast IT8 supported on V600 (Ai Studio) | SilverFast V600 product page | Manufacturer | High | Yes |
| SE/SE Plus processed output limited to 8-bit | SilverFast comparison table | Manufacturer | High | Yes |
| V600 blue/cyan cast tendency | Flickr V600 group, practitioner reports | Forum consensus | Medium | Yes |
| SilverFast IT8 typical deltaE 0.8-1.8 | SilverFast calibration documentation | Manufacturer | Medium | Not V600-specific |
| ProPhoto RGB preserves E-6 saturated colors | Multiple color management sources | Established knowledge | High | N/A |
| iSRD works with E-6 transparencies | SilverFast iSRD documentation | Manufacturer | High | Yes |
| NegaFix not used for positive film | SilverFast NegaFix documentation | Manufacturer | High | Yes |
| 3200 DPI is maximum useful V600 setting | Multiple independent tests | Convergent evidence | High | Yes |

---

## 25. Validated Workflows

### Workflow A: Archival Master (Maximum Preservation)

For: Every transparency worth preserving.

```
Epson V600
  Film Type: Positive Film
  Image Type: 48-bit Color
  Resolution: 3200 DPI
  Digital ICE: ON (Quality) for E-6; OFF for Kodachrome
  All other adjustments: OFF (Linear tone, no sharpening, no grain reduction)
  Histogram: Conservative endpoints, gamma 1.0
  Output: Uncompressed TIFF, ICC profile embedded (Adobe RGB or ProPhoto RGB)

-> Archival master (never modify)
-> SHA-256 checksum
-> 3-2-1 backup

-> Photoshop:
   Open as Smart Object (16-bit RGB)
   Dust retouching on empty layer (Spot Healing, Lighten mode)
   Capture Sharpening (Smart Sharpen as Smart Filter)
   Levels: Black/white point (eyedropper method)
   Curves: Per-channel color cast correction
   Curves: S-curve contrast (Luminosity blend mode)
   Selective Color: Targeted hue refinements
   Luminosity masks: Zone-specific corrections
   Dodge & Burn: 50% gray layer

-> Working master PSD (16-bit)

-> Print master:
   Resize to final dimensions
   Soft proof with paper ICC profile
   Output sharpening (USM or High Pass)
   Save as flattened TIFF
```

### Workflow B: Archival Master (SilverFast Maximum Quality)

For: Dense slides (Velvia), maximum shadow detail, IT8-calibrated color.

```
SilverFast Ai Studio
  Source: Transparent
  Film Type: Positive
  Color Depth: 48 Bit HDR (or 64 Bit HDRi for Archive Suite)
  Resolution: 3200 DPI
  Multi-Exposure: ON
  iSRD: ON
  AACO: ON (for dense shadows)
  IT8 profile: Applied
  Output: 48-bit TIFF (Ai Studio processed) or HDRi raw

-> Archival master
-> Photoshop workflow as above
```

### Workflow C: Faithful Reproduction

For: Reproducing the transparency as accurately as practical.

```
Scanner workflow: As Workflow A or B
Photoshop:
  Correct scanner color cast (gray eyedropper, per-channel Curves)
  Set accurate black/white points
  Minimal contrast adjustment to match transparency appearance
  Dust removal
  Capture sharpening only
  NO color grading, NO saturation adjustment, NO creative contrast
  Soft proof for print
```

### Workflow D: Fine Art Print Interpretation

For: Using the transparency as source material for deliberate artistic expression.

```
Scanner workflow: As Workflow A or B
Photoshop:
  Full correction workflow (Steps 2-7)
  Color grading: Camera Raw Filter, split toning, LUTs
  Creative contrast enhancement
  Dodge & Burn for compositional emphasis
  Local contrast (High Pass Overlay)
  Selective saturation adjustments
  Upscale if needed (Super Resolution + Preserve Details 2.0)
  Soft proof
  Output sharpening for specific paper
```

### Film-Specific Notes

| Film | Scanner Notes | Photoshop Notes |
|------|--------------|-----------------|
| Velvia 50 | Multi-Exposure essential (high Dmax). ICE works. Auto Exposure dangerous. | Avoid global Saturation boost. Shadow cast may need luminosity-masked correction. Perceptual rendering intent for print. |
| Provia 100F | Most scanner-friendly. Lower contrast, less clipping risk. | Blue/cyan scanner cast correction primary task. Gray eyedropper effective. Relative Colorimetric usually fine. |
| Ektachrome E100 | Low contrast suits V600 well. Designed for scanning. | V600 blue cast compounds E100's cool tendency. May need S-curve for more punch. |
| Kodachrome | Digital ICE / iSRD OFF. SilverFast Kodachrome mode for iSRD with masking. | Manual dust retouching essential. May have strong blue cast without Kodachrome-specific profile. |

---

## 26. Final Quality Control Notes

### Verification Checklist

- [x] V600 specifications confirmed from Epson official documentation
- [x] Effective resolution confirmed from independent filmscanner.info testing
- [x] Digital ICE compatibility with E-6 confirmed from Epson official documentation
- [x] Kodachrome IR incompatibility confirmed from multiple sources
- [x] SilverFast edition features confirmed from official comparison table and V600 model page
- [x] NegaFix confirmed as NOT used for positive transparencies
- [x] Multi-Exposure confirmed supported on V600
- [x] IT8 calibration confirmed for V600 (Ai Studio only)
- [x] Film specifications confirmed from manufacturer datasheets (Fujifilm, Kodak)
- [x] Photoshop techniques confirmed from Adobe documentation and established practitioners
- [x] E-6 process chemistry confirmed from multiple technical sources

### Known Uncertainties

| Item | Uncertainty |
|------|-----------|
| V600 practical Dmax | No published independent lab measurement. The ~3.0-3.1 figure is estimated from practitioner reports. |
| Ektachrome E100 resolving power | The E-4000 datasheet was not fully accessible. The ~125 lines/mm figure is inferred from the E100G family. |
| Ektachrome E100 Dmax | The ~4.0 figure from characteristic curves could not be independently verified from the primary datasheet. |
| Velvia 50 exact Dmax | Ken Rockwell reports "almost 4.0D"; Fujifilm curves show ~3.8 for red channel. Official published figure not confirmed. |
| "No Color Correction" + Digital ICE | One source states ICE may not function with No Color Correction mode. Not confirmed across multiple V600-specific sources. |
| SilverFast free SE iSRD | Conflicting reports on whether the free Epson-bundled SE version includes iSRD. |
| ProPhoto RGB vs Adobe RGB for E-6 gamut | The claim that E-6 films exceed Adobe RGB is widely stated but spectrophotometric data quantifying this for specific emulsions was not found. |
| V600 formal deltaE measurements | No independent lab deltaE measurements for the V600 before/after IT8 calibration were found. |

### What This Guide Does NOT Claim

- It does not claim that 48-bit scanning captures additional optical information (it captures additional numerical precision for the same optical data)
- It does not claim that nominal DPI equals genuine captured detail (6400 DPI nominal yields ~1,560 DPI resolved)
- It does not claim that AI upscaling recovers original photographic information (it generates statistically plausible reconstructions)
- It does not claim that the V600 can fully capture the dynamic range of high-Dmax transparencies like Velvia 50
- It does not claim that IT8 calibration on one film stock perfectly corrects all E-6 emulsions (metameric failure exists)

### Lehetseges torzitasok

- This guide relies partially on practitioner consensus from photography forums. While consistent across multiple sources, these are not controlled studies.
- Resolution measurements (filmscanner.info) are from a specific V600 unit with specific film holder positioning. Individual scanner variation exists.
- The V600 has been in production since 2009. Hardware revisions may have occurred without public documentation.
- SilverFast pricing and feature allocation may change. Information is current as of the research date.
- Film specifications are from manufacturer datasheets. Real-world performance depends on processing quality, storage conditions, and age.
- Paper recommendations are based on manufacturer claims and reviewer experience. Paper/ink/printer interactions are complex and printer-specific.
- V600 color cast reports (blue/cyan tendency) come from practitioner forums, not controlled measurement. Individual scanner units may vary.
- IT8 deltaE figures come from SilverFast's own calibration readout, not from independent lab testing.

---

*This guide was constructed from official manufacturer documentation (Epson, Fujifilm, Kodak, SilverFast, Adobe), independent measurements (filmscanner.info), established practitioner workflows, and peer-reviewed research on photographic dye chemistry. Claims are graded by source type and confidence level. Where evidence was insufficient, uncertainty is explicitly stated. The objective is a verified, reproducible color-transparency digital-darkroom workflow, not a collection of generic film-scanning folklore.*
