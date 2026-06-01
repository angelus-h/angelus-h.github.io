# Ilford SFX 200 Field Guide

**Extended Red Sensitivity Film for Infrared-Effect Photography**

*Last Updated: 2026-06-01*

---

## Quick Reference Card

| **Specification** | **Value** |
|-------------------|-----------|
| **Film Type** | Extended red sensitivity panchromatic B&W negative |
| **Base ISO** | 200 (no filter) |
| **Effective ISO with R72** | 3-6 (6-7 stop compensation) |
| **Effective ISO with 25A Red** | 25 (3 stop compensation) |
| **Spectral Sensitivity** | 400-740nm (peak red sensitivity 720nm)[^1] |
| **Grain** | Medium |
| **Formats** | 35mm, 120 |
| **Reciprocity Failure Factor** | P = 1.43 (kicks in after 1 second)[^2] |
| **Exposure Latitude** | ±2 stops (without filter), ±1 stop (with IR filter) |

[^1]: [Ilford Photo - USING ILFORD SFX 200 PART 1](https://www.ilfordphoto.com/using-ilford-sfx-part-1/)
[^2]: [Ilford Photo - Reciprocity Failure Compensation](https://www.ilfordphoto.com/wp/wp-content/uploads/2024/05/Reciprocity-Failure-Compensation-v2.pdf)

---

## What Makes SFX 200 Special?

### Not True Infrared Film

**CRITICAL DISTINCTION:** Ilford SFX 200 is **not** infrared film. It has **extended red sensitivity** up to 740nm, with peak red sensitivity at 720nm.[^3] True infrared films (like discontinued Kodak HIE) extended beyond 900nm.

[^3]: [Ilford Photo - SFX 200 Technical Information](https://www.ilfordphoto.com/amfile/file/download/file/1907/product/701/)

**What this means in practice:**
- **With no filter:** Acts as normal panchromatic B&W film (ISO 200)
- **With deep red filter (25A, 29):** Enhanced red sensitivity creates infrared-like effects
- **With R72 (720nm) filter:** Maximum infrared effect - foliage glows white, skies go black
- **With R72 + longer exposure:** Captures light between 650-750nm invisible to human eye

### Visual Character

**Without Filter:**
- Normal B&W tonality
- Slightly higher contrast than HP5 Plus
- Medium grain structure
- Full panchromatic sensitivity

**With Deep Red Filter (25A):**
- Darkened blue skies
- Enhanced cloud contrast
- Lighter foliage (but not glowing white)
- Strong contrast in landscape photography

**With R72 Infrared Filter:**
- Glowing white foliage (Wood Effect)
- Near-black skies
- Ethereal, dreamlike landscapes
- Strong halation around light sources (sun, bright objects)
- Penetrates atmospheric haze

---

## Filter Recommendations

### For Infrared Effect

**R72 (720nm) Filter - Recommended:**
- B+W 092 (very dark red, extends slightly into IR)[^4]
- Hoya R72 (720nm cutoff)
- **Exposure compensation:** +6 to +7 stops
- **Metering:** Set ISO to 3-6 on handheld meter

[^4]: [Photrio - Ilford SFX 200 with IR or red filter discussion](https://www.photrio.com/forum/threads/ilford-sfx-200-with-ir-or-red-filter.114734/)

**Important:** SFX 200's sensitivity doesn't extend far enough into IR spectrum to benefit from 820nm or higher filters. R72 is the practical maximum.

### For Enhanced Contrast (Less Extreme)

**25A Red Filter:**
- Traditional red filter
- **Exposure compensation:** +3 stops
- **Metering:** Set ISO to 25
- Produces strong sky contrast without full IR effect

**29 Deep Red Filter:**
- Slightly darker than 25A
- **Exposure compensation:** +3 to +4 stops
- Intermediate effect between 25A and R72

### Without Filter

Use as normal ISO 200 panchromatic film:
- General purpose B&W photography
- Portraits, street photography
- Situations where full panchromatic sensitivity is desired

---

## Exposure and Metering

### With R72 Filter

**Method 1: In-Camera Meter (if TTL works through filter)**
- Set camera ISO to **6** (some sources say 3-6, test your camera)
- Meter through the R72 filter
- Shoot at indicated exposure

**Method 2: Handheld Meter (Recommended)**
- Meter scene without filter at ISO 200
- Add **+6 to +7 stops** compensation manually
- Example: Metered 1/125s f/11 → Actual 1/2s f/11 (6 stops slower)

**Method 3: Sunny 16 Equivalent**
- R72 filter: Start with **f/16 @ 1/6s** in bright sun
- Bracket ±1 stop

### With 25A Red Filter

- Set handheld meter to **ISO 25**
- Meter without filter, then add **+3 stops**
- Or meter through filter if TTL works

### Reciprocity Failure with R72 Filter

**CRITICAL FOR LONG EXPOSURES:** SFX 200 has significant reciprocity failure for exposures longer than 1 second.[^5]

[^5]: [Lucas.dev - Reciprocity for Ilford SFX 200](https://lucas.dev/reciprocity/ilford-sfx-200)

**Formula:** Tc = Tm^1.43

Where:
- Tm = Metered exposure time
- Tc = Corrected exposure time
- P = 1.43 (Ilford's published reciprocity factor)

**Practical Examples:**

| Metered Time | Corrected Time (apply reciprocity) |
|--------------|--------------------------------------|
| 1 second | 1.4 seconds |
| 10 seconds | 25 seconds |
| 30 seconds | 95 seconds (~1.5 minutes) |
| 60 seconds | 348 seconds (~6 minutes) |
| 120 seconds | 1004 seconds (~17 minutes) |

**Real-World Note:** When using R72 filter, exposures commonly range from 5-30 seconds metered, requiring 15 seconds to 2 minutes actual exposure after reciprocity correction.

**Testing Confirmation:** Photographer tests with 720nm IR filter confirmed Ilford's 1.43 power factor is accurate.[^6]

[^6]: [EMULSIVE - Long exposure film tests part two: ILFORD SFX 200](https://emulsive.org/reviews/long-exposure-film-tests-part-two-ilford-hp5-ilford-delta-professional-400-ilford-delta-professional-3200-ilford-sfx-200-by-toni-skokovic)

### Bracketing Strategy with R72 Filter

Given reciprocity uncertainty and variable IR reflectance in scenes:

1. **Meter and calculate** corrected exposure (Tm^1.43)
2. **Shoot corrected exposure**
3. **Bracket:** -1 stop and +1 stop from corrected exposure
4. **Total:** 3 frames per composition

Example:
- Metered: 30 seconds → Corrected: 95 seconds
- Shoot: 48s, 95s, 190s (bracket around calculated exposure)

---

## Development Recommendations

### Standard Development (No Filter or Red Filter)

**Ilford Ilfosol 3:**
- **1+9 dilution:** 6 minutes @ 20°C[^7]
- **1+14 dilution:** 9 minutes @ 20°C

**Ilford Ilfotec DD-X:**
- **1+4 dilution:** 10 minutes @ 20°C

**ID-11/D-76 (Stock):**
- 8.5 minutes @ 20°C
- Normal contrast

**Rodinal 1+50:**
- 10.5 minutes @ 20°C
- Sharpness emphasis, increased grain

[^7]: [Ilford Photo - SFX 200 Technical Information](https://www.ilfordphoto.com/amfile/file/download/file/1907/product/701/)

### Development with R72 Filter

**IMPORTANT:** Development times **do not change** based on filtration. The filter only affects exposure, not development.

Use standard development times as above. The infrared effect comes from:
1. The filter blocking visible light during exposure
2. The film's extended red sensitivity capturing near-IR wavelengths

**However, some practitioners adjust development for contrast control:**

**For Lower Contrast (Infrared Effect Can Be Very Contrasty):**
- Reduce development time by 10-15%
- Use Ilfotec DD-X 1+4 for 8.5 minutes instead of 10 minutes
- Or use D-76/ID-11 1+1 dilution for softer tonality

**For Maximum IR Effect (Higher Contrast):**
- Use Rodinal 1+25 or 1+50 for maximum sharpness
- Standard development time or +10%

### Push/Pull Processing

**Push to ISO 400 (with no filter):**
- ID-11/D-76 stock: +1.5 minutes (10 minutes total)
- Ilfotec DD-X 1+4: +2 minutes (12 minutes total)
- Increased grain, higher contrast

**Pull to ISO 100 (with no filter):**
- ID-11/D-76 stock: -1.5 minutes (7 minutes total)
- Lower contrast, finer grain

**Note:** Push/pull adjustments apply to unfiltered shooting. With R72 filter, expose at EI 3-6 and develop normally.

---

## Shooting Workflow with R72 Filter

### Step-by-Step Field Procedure

**1. Focus First (CRITICAL):**
- Focus **without** the R72 filter on camera
- Infrared light focuses at slightly different plane than visible light
- Some lenses have IR focus mark (red line) - shift focus slightly toward infinity after focusing visually
- **For landscape work:** Stop down to f/11 or f/16 to ensure depth of field covers IR focus shift

**2. Compose:**
- Attach R72 filter to lens
- Viewfinder will be nearly black - compose before attaching filter or use Live View (if mirrorless)

**3. Meter:**
- Handheld meter (incident or reflected) at ISO 200, no filter
- Calculate exposure compensation: +6 to +7 stops
- Example: f/11 @ 1/125s → f/11 @ 1/2s (6 stops)

**4. Apply Reciprocity Correction:**
- If metered exposure > 1 second, use formula: Tc = Tm^1.43
- Example: 10s metered → 25s actual exposure

**5. Bracket:**
- Shoot calculated exposure
- Shoot -1 stop and +1 stop bracket

**6. Use Tripod:**
- With R72 filter, exposures range from 1 second to several minutes
- Sturdy tripod mandatory
- Cable release or self-timer to avoid camera shake

### Best Subjects for SFX 200 + R72

**Landscapes with Foliage:**
- Trees and grass glow bright white
- Blue sky goes near-black
- Dramatic cloud contrast
- Ethereal, otherworldly atmosphere

**Architectural Details:**
- Brick and stone textures enhanced
- Strong shadow/highlight separation
- Haze penetration for distant structures

**Portraits (Experimental):**
- Skin tones lighten (can be unflattering)
- Veins and blemishes may be emphasized
- Use with caution - test first

**Water and Reflections:**
- Water surface can appear darker or reflective depending on angle
- Cloud reflections in water are dramatic

### Conditions That Work Best

**Bright Sunlight:**
- Maximum IR reflection from foliage
- Strongest Wood Effect
- Darkest skies

**Partly Cloudy Days:**
- Dramatic cloud formations against black sky
- Good subject contrast

**Avoid:**
- Overcast/flat light - reduces IR effect
- Shade/open shade - minimal IR reflection from foliage

---

## Technical Characteristics

### Grain Structure

**Medium grain** - finer than HP5 Plus, coarser than FP4 Plus.

With R72 filter and long exposures, grain may appear slightly more pronounced due to:
- Reciprocity failure effects
- Longer exposure times
- Development characteristics

### Sharpness and Acutance

**Good acutance** when developed in Rodinal or Ilfotec DD-X.

**IR focus shift consideration:** Sharpness can be affected by infrared focus plane difference. Stop down to f/11-f/16 for critical sharpness.

### Contrast

**Without filter:** Medium-high contrast (slightly higher than HP5 Plus)

**With R72 filter:** Very high contrast
- Foliage (bright white) vs sky (near-black) creates extreme tonal separation
- May require reduced development time to control highlights

### Halation and Glow

SFX 200 has **moderate halation** around bright light sources (sun, reflections) when shot with R72 filter. This contributes to the ethereal, glowing quality of infrared-effect photography.

**To minimize halation:** Avoid shooting directly into sun, or use lens hood

**To maximize halation:** Shoot backlit subjects, include sun in frame

---

## Practical Tips and Techniques

### Focusing for Infrared

**Problem:** Infrared light focuses at slightly different plane than visible light.

**Solutions:**

1. **Stop down to f/11 or f/16** - Depth of field covers IR focus shift
2. **Use lens IR focus mark** (if available) - Red dot or line on focus ring
   - Focus visually, then shift focus slightly toward infinity to IR mark
3. **Hyperfocal distance** - Set focus to hyperfocal for f/11 or f/16
4. **Test your lenses** - Some modern lenses have minimal IR focus shift

### Handling Long Exposures

**With R72 filter, exposures commonly range from 5 seconds to 5+ minutes.**

**Workflow:**
- Use sturdy tripod
- Cable release or 10-second self-timer
- Mirror lock-up (if SLR)
- Shield lens from wind vibration
- Wait for wind to calm when shooting foliage (long exposures blur moving leaves)

### Scanning and Printing

**Scanning:**
- Scan as black & white negative (not color negative)
- SFX 200 negatives may appear unusually dense with R72 filter - this is normal
- Adjust scanner settings for contrast control

**Darkroom Printing:**
- SFX 200 prints well on standard variable contrast paper
- With R72 filter shots, negatives may be contrasty - use grade 1-2 paper or reduce print contrast
- Dodging skies and burning foliage may be necessary

### Film Handling and Storage

**No special handling required** - Load and unload film as normal B&W film.

Unlike true infrared films (Kodak HIE), SFX 200 does not require loading in complete darkness or IR-safe film cassettes.

**Storage:**
- Store unexposed film in cool, dry conditions
- Normal film refrigeration extends shelf life
- Process exposed film promptly (standard practice for all films)

---

## Common Issues and Solutions

### Issue: Negatives Too Thin (Underexposed)

**Cause:** Insufficient exposure compensation with R72 filter

**Solution:**
- Verify +6 to +7 stop compensation applied
- Check reciprocity failure calculation for long exposures
- Bracket exposures

### Issue: Negatives Too Dense (Overexposed)

**Cause:** Excessive exposure or over-development

**Solution:**
- Reduce exposure by 1 stop
- Reduce development time by 10-15%

### Issue: Minimal Infrared Effect

**Cause:**
- Wrong filter used (not R72)
- Shooting in shade or overcast conditions (minimal IR reflection)
- Underexposure

**Solution:**
- Confirm R72 (720nm) filter in use
- Shoot in bright sunlight for maximum IR effect
- Increase exposure by 1 stop

### Issue: Extreme Contrast (Blown Highlights, Blocked Shadows)

**Cause:** IR effect creates very high contrast scenes

**Solution:**
- Reduce development time by 10-15%
- Use softer working developer (D-76 1+1 instead of Rodinal)
- Expose for highlights, accept shadow detail loss
- Use graduated ND filter to balance sky and foreground

### Issue: Soft Focus / Lack of Sharpness

**Cause:** IR focus shift not corrected

**Solution:**
- Stop down to f/11 or f/16
- Use lens IR focus mark (if available)
- Focus slightly past infinity after visual focus

---

## Comparison with Other Infrared-Capable Films

### SFX 200 vs Rollei Infrared 400

| Aspect | SFX 200 | Rollei IR 400 |
|--------|---------|---------------|
| **Spectral Sensitivity** | Up to 740nm | Up to 820nm (true near-IR) |
| **ISO (no filter)** | 200 | 400 |
| **ISO (with R72)** | 3-6 | 6-12 |
| **Infrared Effect** | Moderate (extended red) | Strong (true near-IR) |
| **Halation** | Moderate | Minimal (anti-halation layer) |
| **Grain** | Medium | Medium-coarse |
| **Availability** | Widely available | Limited availability |
| **Cost** | Moderate | Higher |

**When to choose SFX 200:**
- More widely available
- Lower cost
- Easier to work with (less extreme IR effect)
- Good for learning IR-effect photography

**When to choose Rollei IR 400:**
- Maximum IR sensitivity (up to 820nm)
- Stronger Wood Effect (glowing foliage)
- Higher base speed (ISO 400 vs 200)

### SFX 200 vs Standard Panchromatic Films with Red Filter

**SFX 200 + R72 filter** produces a stronger infrared-like effect than **standard films (HP5, Tri-X) + red filter** because:

1. Extended red sensitivity (720-740nm) captures wavelengths standard films cannot
2. R72 filter blocks all visible light, isolating extended red/near-IR response
3. Creates the characteristic "Wood Effect" glowing foliage

**Standard films with red filter** produce:
- Darkened skies
- Enhanced cloud contrast
- Lighter foliage (but not glowing white)
- Traditional red-filtered B&W aesthetic (not infrared effect)

---

## Recommended Uses and Aesthetic

### Best Applications

**Landscape Photography:**
- Dramatic skies with near-black tonality
- Glowing white foliage (Wood Effect)
- Strong cloud contrast
- Atmospheric, ethereal quality

**Experimental Fine Art:**
- Surreal, dreamlike imagery
- Alternative vision of familiar scenes
- High-contrast abstract compositions

**Architectural Photography:**
- Haze penetration for distant structures
- Strong texture emphasis (brick, stone)
- Dramatic shadow/highlight separation

### Atmospheric Character

SFX 200 + R72 filter produces:
- **Otherworldly, dreamlike quality**
- **High contrast, graphic compositions**
- **Ethereal glow** around foliage and bright objects
- **Melancholic, atmospheric mood** (when processed for lower contrast)

**Visual Signature:**
- White trees against black skies
- Penetrating haze and atmospheric effects
- Glowing, luminous foliage
- Strong separation between sky and earth

### Fine Art and Limited Edition Prints

**Market Appeal:**
- Infrared-effect photography has strong collector interest
- Unique aesthetic not achievable in normal B&W
- Limited edition prints of IR landscapes command premium pricing

**Print Presentation:**
- Emphasize film-based IR process (vs digital IR conversion)
- Describe extended red sensitivity and R72 filtration
- Highlight handmade darkroom printing or fine art scanning
- Limited series (10-25 prints) of signature IR landscapes

---

## Development Time Reference Table

| Developer | Dilution | Time @ 20°C | Notes |
|-----------|----------|-------------|-------|
| **Ilfosol 3** | 1+9 | 6 min | Recommended by Ilford |
| **Ilfosol 3** | 1+14 | 9 min | Finer grain |
| **Ilfotec DD-X** | 1+4 | 10 min | Fine grain, good contrast control |
| **ID-11/D-76** | Stock | 8.5 min | Normal contrast |
| **ID-11/D-76** | 1+1 | 11 min | Lower contrast, finer grain |
| **Rodinal** | 1+50 | 10.5 min | Sharp, accentuated grain |
| **Rodinal** | 1+25 | 7.5 min | Higher contrast, maximum sharpness |
| **Perceptol** | Stock | 13 min | Finest grain (for non-IR shooting) |

**For R72 filtered exposures:** Development times remain the same. Adjust for contrast control:
- **Lower contrast:** Reduce time by 10-15% or use DD-X/D-76 1+1
- **Higher contrast:** Use Rodinal or increase time by 10%

---

## Filter Exposure Compensation Quick Reference

| Filter Type | Filter Examples | Exposure Compensation | Effective ISO |
|-------------|----------------|----------------------|---------------|
| **None** | - | 0 stops | 200 |
| **Yellow** | K2, 8 | +1 stop | 100 |
| **Orange** | 21, G | +2 stops | 50 |
| **Red** | 25A, 29 | +3 stops | 25 |
| **Deep Red** | 29 | +4 stops | 12 |
| **R72 (720nm)** | B+W 092, Hoya R72 | +6 to +7 stops | 3-6 |

---

## Sources and Further Reading

**Official Ilford Resources:**
- [Ilford Photo - USING ILFORD SFX 200 PART 1](https://www.ilfordphoto.com/using-ilford-sfx-part-1/)
- [Ilford Photo - SFX 200 Technical Data Sheet](https://www.ilfordphoto.com/amfile/file/download/file/1907/product/701/)
- [Ilford Photo - Reciprocity Failure Compensation](https://www.ilfordphoto.com/wp/wp-content/uploads/2024/05/Reciprocity-Failure-Compensation-v2.pdf)

**Practical Guides and Reviews:**
- [J. Bradford Photo - Ilford SFX 200 Review and Guide](https://jbradfordphoto.com/photography/ilford-sfx-200-review-and-guide/)
- [Blue Moon Camera Codex - Film Friday Ilford SFX 200 Film Review](https://bluemooncameracodex.com/film-fridays/ffsfx)
- [Will Gudgeon - Shooting and developing Ilford SFX 200](https://www.willgudgeon.com/2017/01/shooting-and-developing-ilford-sfx-200.html)

**Technical Discussions:**
- [Photrio - Ilford SFX 200 with IR or red filter discussion](https://www.photrio.com/forum/threads/ilford-sfx-200-with-ir-or-red-filter.114734/)
- [Photrio - Ilford SFX 200 Reciprocity discussion](https://www.photrio.com/forum/threads/ilford-sfx-200-reciprocity.139837/)
- [EMULSIVE - Long exposure film tests: ILFORD SFX 200](https://emulsive.org/reviews/long-exposure-film-tests-part-two-ilford-hp5-ilford-delta-professional-400-ilford-delta-professional-3200-ilford-sfx-200-by-toni-skokovic)

**Reciprocity Calculators:**
- [Lucas.dev - Reciprocity for Ilford SFX 200](https://lucas.dev/reciprocity/ilford-sfx-200)

---

**End of Field Guide**

*This guide prioritizes verified technical information from Ilford official documentation, published testing by practitioners, and community consensus. Where information varies (e.g., exact R72 compensation - some sources say +6, others +7 stops), multiple perspectives are presented with recommendation to bracket exposures.*
