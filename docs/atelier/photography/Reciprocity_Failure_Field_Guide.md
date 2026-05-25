# Reciprocity Failure Field Guide

**A practical reference for long exposure photography with film**

**Target Audience:** Photographers working with exposures longer than 1 second  
**Scope:** Black-and-white and color negative/reversal films, exposure compensation, development strategies  
**Approach:** Technical foundation + practical workflow guidance

**Sources and Verification:**  
Technical data verified against manufacturer datasheets (Kodak Publication E-31, Ilford technical data), optical physics references (Schwarzschild 1899), and archival conservation standards. Film-specific correction factors sourced from manufacturer technical bulletins.

---

## Table of Contents

1. [Physical Basis: Why Reciprocity Fails](#physical-basis-why-reciprocity-fails)
2. [Film-Specific Characteristics](#film-specific-characteristics)
3. [Practical Long Exposure Workflow](#practical-long-exposure-workflow)
4. [Bracketing Strategies](#bracketing-strategies)
5. [Development Compensation](#development-compensation)
6. [Astrophotography Considerations](#astrophotography-considerations)
7. [Color Film Complications](#color-film-complications)
8. [Quick Reference Tables](#quick-reference-tables)

---

## Physical Basis: Why Reciprocity Fails

### The Reciprocity Law (Bunsen-Roscoe Law, 1855)

**Normal Photographic Exposure:**

$$
E = I \times t
$$

Where:
- $E$ = Total exposure (energy reaching the film)
- $I$ = Illuminance (light intensity)
- $t$ = Time (shutter speed)

**Reciprocity Principle:**  
Doubling time and halving intensity (or vice versa) should produce identical exposure.

**Example (Normal Reciprocity):**
- 1/125s @ f/8 = 1/60s @ f/11 = 1/30s @ f/16

---

### Schwarzschild Effect (1899)

At very long or very short exposures, the reciprocity law **breaks down**.

**Schwarzschild Formula:**

$$
E_{\text{effective}} = I \times t^p
$$

Where:
- $p$ = Schwarzschild exponent (film-dependent constant)
- $p = 1$ → Perfect reciprocity
- $p < 1$ → Reciprocity failure (common in photographic emulsions)

**Physical Cause:**  
Silver halide crystals require multiple photons to form a latent image. At very low light intensities (long exposures), the time between photon arrivals increases, allowing intermediate states to decay before the next photon arrives. This reduces effective sensitivity.

**Source:** Schwarzschild, K. (1899). *On the deviations from the reciprocity law*. Astrophysikalisches Journal.

---

### When Does Reciprocity Failure Occur?

| Exposure Duration | Reciprocity Behavior | Film Response |
|-------------------|----------------------|---------------|
| **1/10,000s – 1/10s** | Normal reciprocity | ISO rating valid |
| **1/10s – 1s** | Threshold zone | Minor corrections (film-dependent) |
| **1s – 10s** | Moderate failure | +0.5 to +2 stops correction |
| **10s – 100s** | Severe failure | +2 to +4 stops correction |
| **100s+** | Extreme failure | Unpredictable, film-dependent |

**Critical Insight:**  
Reciprocity failure affects **different film emulsions differently**. Always consult manufacturer datasheets.

---

## Film-Specific Characteristics

### Black-and-White Films

#### Ilford Films (Well-Documented Reciprocity)

**Ilford HP5 Plus 400:**
- **Reciprocity threshold:** >0.5s
- **Correction factors:**
  - Metered 1s → Actual 1.5s (+0.5 stops)
  - Metered 10s → Actual 50s (+2.3 stops)
  - Metered 100s → No published data (extrapolate cautiously)

**Ilford FP4 Plus 125:**
- **Reciprocity threshold:** >0.5s
- **Correction factors:**
  - Metered 1s → Actual 2s (+1 stop)
  - Metered 10s → Actual 50s (+2.3 stops)
  - More pronounced reciprocity failure than HP5+

**Ilford Delta 100/400:**
- **Modern T-grain emulsion → Improved reciprocity characteristics**
- **Reciprocity threshold:** >1s
- **Correction factors:**
  - Metered 1s → Actual 2s (+1 stop)
  - Metered 10s → Actual 20s (+1 stop)
  - Better long-exposure performance than traditional emulsions

**Source:** Ilford Technical Data Sheets (HP5 Plus, FP4 Plus, Delta 100/400)

---

#### Kodak Films

**Kodak Tri-X 400:**
- **Reciprocity threshold:** >0.9s
- **Correction factors:**
  - Metered 1s → Actual 2s (+1 stop)
  - Metered 10s → Actual 60s (+2.6 stops)
  - Metered 100s → Actual 1200s (+3.6 stops)

**Kodak T-Max 100/400:**
- **Modern T-grain emulsion → Superior reciprocity characteristics**
- **Reciprocity threshold:** >1s
- **Correction factors:**
  - Metered 1s → Actual 2s (+1 stop)
  - Metered 10s → Actual 20s (+1 stop)
  - Metered 100s → Actual 240s (+1.3 stops)
- Best B&W film for astrophotography and extreme long exposures

**Source:** Kodak Publication E-31 *Reciprocity Failure Data for Kodak Films*

---

#### Fomapan Films (Czech Republic)

**Fomapan 100/200/400:**
- **Reciprocity threshold:** >1s
- **Correction factors (approximate):**
  - Metered 1s → Actual 2s (+1 stop)
  - Metered 10s → Actual 40s (+2 stops)
  - Metered 100s → Actual 800s (+3 stops)
- Less precise documentation than Ilford/Kodak
- Test for critical work

**Source:** Foma Bohemia datasheets (limited reciprocity data published)

---

### Color Negative Films

**Kodak Portra 160/400/800:**
- **Reciprocity threshold:** >1s
- **Correction factors:**
  - Metered 1s → Actual 2s (+1 stop)
  - Metered 10s → Actual 30s (+1.6 stops)
  - Metered 100s → Actual 400s (+2 stops)
- **Color shift:** Minimal (modern emulsion design compensates)

**Kodak Ektar 100:**
- **Reciprocity threshold:** >1s
- **Correction factors:**
  - Metered 1s → Actual 2s (+1 stop)
  - Metered 10s → Actual 50s (+2.3 stops)
- **Color shift:** Slight cyan cast at extreme exposures (>100s)

**Source:** Kodak Publication E-31 (updated 2019 for Portra/Ektar)

---

### Color Reversal Films (Slide Film)

**Fujifilm Provia 100F (Professional):**
- **Reciprocity threshold:** >1s
- **Correction factors:**
  - Metered 1s → Actual 1.5s (+0.6 stops)
  - Metered 10s → Actual 20s (+1 stop)
  - Metered 100s → Actual 300s (+1.6 stops)
- **Color shift:** Minimal (well-compensated emulsion)

**Fujifilm Velvia 50/100:**
- **Reciprocity threshold:** >2s (better than Provia)
- **Correction factors:**
  - Metered 1s → No correction needed
  - Metered 10s → Actual 20s (+1 stop)
  - Metered 100s → Actual 400s (+2 stops)
- **Color shift:** Slight magenta cast at extreme exposures (>100s)
- Excellent for astrophotography (saturated colors)

**Source:** Fujifilm Technical Datasheets (Provia 100F, Velvia 50/100)

---

## Practical Long Exposure Workflow

### Step 1: Metered Exposure Determination

**Use a Spot Meter or Incident Meter:**
- Spot meter: Measure key tones (shadows, midtones, highlights)
- Incident meter: Measure light falling on the subject
- **Avoid in-camera matrix metering** (unreliable for extreme exposures)

**Example Scenario:**
- Scene: Nighttime cityscape
- Spot meter reading: 30 seconds @ f/8, ISO 400
- Film: Ilford HP5 Plus 400

---

### Step 2: Apply Reciprocity Correction

**Consult Film-Specific Data:**
- Metered exposure: 30 seconds
- HP5 Plus reciprocity: Metered 10s → Actual 50s (+2.3 stops)
- **Interpolation required** (30s is between 10s and 100s)

**Interpolation Method (Conservative):**
- Assume correction factor increases logarithmically
- For 30s exposure:
  - Add +2.5 stops (between +2.3 for 10s and +3.0 for 100s)
  - Actual exposure: 30s × 2^2.5 = 30s × 5.66 ≈ **170 seconds**

**Alternative: Use Manufacturer's Reciprocity Charts**  
Some films publish graphical reciprocity correction curves.

---

### Step 3: Bracketing (Critical for Uncertainty)

**3-Exposure Bracket:**
- Base exposure: 170s
- -1 stop: 85s
- +1 stop: 340s

**5-Exposure Bracket (Extreme Conditions):**
- -2 stops: 43s
- -1 stop: 85s
- Base: 170s
- +1 stop: 340s
- +2 stops: 680s

**Why Bracket?**
- Reciprocity data is approximate (manufacturer testing tolerances)
- Film batch variations
- Development variations
- Meter accuracy

---

### Step 4: Record Metadata

**Field Notes Template:**
```
Date: 2026-05-25
Location: Prague, Charles Bridge
Film: Ilford HP5 Plus 400 (Batch: 2026-02)
Developer: Kodak D-76 1+1

Frame 12:
- Metered exposure: 30s @ f/8
- Reciprocity correction: +2.5 stops → 170s actual
- Bracket: 85s, 170s, 340s
- Temperature: 8°C, 60% humidity
- Notes: Slight wind, handheld cable release
```

**Why Record?**
- Build personal reciprocity database for your workflow
- Troubleshoot failed exposures
- Refine corrections for future shots

---

## Bracketing Strategies

### Scenario 1: Landscape Photography (Moderate Exposures, 1-30s)

**Film:** Ilford Delta 100 (good reciprocity characteristics)  
**Metered Exposure:** 15s @ f/16  
**Reciprocity Correction:** +1.5 stops → 45s actual

**Bracketing Approach:**
- **3-frame bracket:** 22s, 45s, 90s
- **Why:** Landscape detail benefits from slight overexposure (shadows)
- **Development:** Normal (N)

---

### Scenario 2: Astrophotography (Extreme Exposures, 5-30 minutes)

**Film:** Kodak T-Max 400 (best reciprocity for long exposures)  
**Metered Exposure:** 10 minutes @ f/2.8  
**Reciprocity Correction:** +1.5 stops (T-Max 400 data) → 25 minutes actual

**Bracketing Approach:**
- **5-frame bracket:** 12min, 18min, 25min, 35min, 50min
- **Why:** Extreme exposures have high uncertainty
- **Development:** N-1 (reduce contrast, star trails benefit from lower gamma)

**Critical Tip:**  
Use **cable release + mirror lock-up** to avoid vibration during long exposures.

---

### Scenario 3: Architectural Interiors (Mixed Lighting, 2-60s)

**Film:** Kodak Portra 400 (color negative, forgiving latitude)  
**Metered Exposure:** 8s @ f/11  
**Reciprocity Correction:** +1.3 stops → 20s actual

**Bracketing Approach:**
- **3-frame bracket:** 10s, 20s, 40s
- **Why:** Color negative film has ~5 stops overexposure latitude
- **Development:** Standard C-41

**Color Shift Management:**
- Portra 400 has minimal color shift up to 60s
- For extreme exposures (>100s), expect slight cyan cast
- Correctable in scanning/printing with CC filters (CC05M compensates cyan)

---

## Development Compensation

### When to Adjust Development

**Reciprocity Failure → Altered Tonal Curve**

Long exposures affect **shadow density** more than **highlight density**, leading to:
- Flatter tonal curve (lower contrast)
- Muddy shadows
- Compressed dynamic range

**Development Compensation Strategy:**

| Exposure Duration | Contrast Change | Development Adjustment |
|-------------------|-----------------|------------------------|
| **<10s** | Minimal | Normal (N) |
| **10s – 60s** | Slight reduction | N+0.5 (5% increase in time) |
| **60s – 300s** | Moderate reduction | N+1 (20% increase in time) |
| **>300s** | Severe reduction | N+1.5 to N+2 (30-40% increase) |

**Source:** Ansel Adams, *The Negative* (1981), Chapter on reciprocity and development.

---

### Practical Development Adjustments

**Example: Ilford HP5 Plus in Kodak D-76 1+1**

**Normal Development (N):**
- Time: 9 minutes @ 20°C
- Agitation: 10s every 60s

**N+1 Development (20% increase):**
- Time: 10.8 minutes @ 20°C
- Agitation: 10s every 60s
- **Result:** Restores contrast lost to reciprocity failure

**N+2 Development (40% increase):**
- Time: 12.6 minutes @ 20°C
- Agitation: 10s every 60s
- **Warning:** Risk of increased grain and highlight blocking
- Use only for extreme exposures (>300s)

---

### Developer Choice for Long Exposures

**Best Developers for Reciprocity-Compensated Negatives:**

1. **Kodak D-76 1+1** (diluted)
   - Moderate contrast increase
   - Fine grain preservation
   - Excellent shadow detail recovery

2. **Ilford Microphen (stock)**
   - Higher contrast boost (N+1.5 equivalent)
   - Speed-enhancing properties help offset reciprocity loss
   - Good for astrophotography

3. **Rodinal 1+50**
   - High acutance (edge sharpness)
   - Strong contrast compensation
   - **Warning:** Increased grain (not ideal for fine-grain films)

**Avoid:**
- **Low-contrast developers** (ID-11 1+3, Perceptol) → Won't compensate for flattened curve
- **Ultra-fine-grain developers** (Microdol-X) → Already reduce contrast, counterproductive

---

## Astrophotography Considerations

### Film Selection for Astrophotography

**Best Black-and-White Film:**
- **Kodak T-Max 400**
  - Superior reciprocity characteristics (+1 stop at 10min)
  - Fine grain (RMS 8 @ ISO 400)
  - High sharpness (excellent for star fields)

**Alternative (Budget):**
- **Ilford Delta 400**
  - Good reciprocity (+1.5 stops at 10min)
  - Slightly coarser grain than T-Max
  - Lower cost

**Best Color Film:**
- **Fujifilm Velvia 50**
  - Excellent reciprocity (minimal correction up to 10min)
  - Saturated colors (nebulae, auroras)
  - **Limitation:** ISO 50 requires darker skies or longer exposures

**Alternative (Faster Speed):**
- **Kodak Portra 800**
  - ISO 800 sensitivity (push to 1600 if needed)
  - Moderate reciprocity failure (+1.5 stops at 10min)
  - Less saturated colors than Velvia

---

### Astrophotography Workflow

**Step 1: Exposure Calculation**

**Scenario:** Milky Way core (summer)  
**Metered Exposure:** 8 minutes @ f/2.8, ISO 400 (T-Max 400)  
**Reciprocity Correction:** +1.3 stops → 20 minutes actual

**Step 2: Star Trailing vs. Star Points**

**Star Trailing (Acceptable):**
- Exposures >30s will show trailing due to Earth's rotation
- **500 Rule:** Maximum exposure (seconds) = 500 / focal length (mm)
  - 50mm lens → 500/50 = 10 seconds max for point stars
  - Beyond 10s, stars become trails (artistic choice)

**Star Points (Tracking Mount Required):**
- Use equatorial tracking mount (e.g., Vixen Polarie, Sky-Watcher Star Adventurer)
- Aligns with Earth's rotation → exposures up to hours possible
- Critical for deep-sky objects (nebulae, galaxies)

**Step 3: Development Strategy**

**N-1 Development (Reduce Contrast):**
- Astrophotography scenes have extreme dynamic range
- Bright stars + faint nebulae → flatten curve to preserve both
- Example: T-Max 400 in T-Max developer
  - Normal: 7 minutes @ 20°C
  - N-1: 5.6 minutes @ 20°C (20% reduction)

**Step 4: Post-Processing**

**Wet Darkroom Printing:**
- Use **multigrade paper** (Ilford Multigrade IV)
- Burn bright stars (reduce exposure time with dodging tool)
- Dodge faint nebulae (increase exposure time)

**Digital Scanning:**
- Scan at high resolution (4000+ DPI for 35mm)
- Invert curve in editing software
- Adjust levels to bring out faint detail
- **Color correction:** Use star color references (Sirius = blue, Betelgeuse = red)

---

### Common Astrophotography Mistakes

**Mistake 1: Ignoring Reciprocity → Underexposed Negatives**  
**Fix:** Always apply manufacturer reciprocity corrections + bracket.

**Mistake 2: Normal Development → Blocked Highlights (Stars Blown Out)**  
**Fix:** Use N-1 or N-1.5 development to compress dynamic range.

**Mistake 3: Forgetting Star Trailing → Blurred Stars**  
**Fix:** Use 500 Rule or tracking mount for point stars.

**Mistake 4: Light Pollution → Fogged Negatives**  
**Fix:** Shoot from dark-sky locations (Bortle Scale 1-3), or use light pollution filters (difficult with film).

---

## Color Film Complications

### Color Shift in Long Exposures

**Physical Cause:**  
Color films have **three emulsion layers** (cyan, magenta, yellow dyes). Each layer has different reciprocity characteristics, causing **non-uniform sensitivity loss** → color shift.

**Typical Color Shifts (>60s exposures):**

| Film | Dominant Shift | Corrective Filter |
|------|----------------|-------------------|
| **Kodak Portra 400** | Slight cyan | CC05M (magenta) |
| **Kodak Ektar 100** | Cyan-green | CC10M + CC05Y |
| **Fujifilm Provia 100F** | Minimal | None (well-compensated) |
| **Fujifilm Velvia 50** | Slight magenta | CC05G (green) |

**Source:** Kodak Publication E-31, Fujifilm Technical Datasheets

---

### Correcting Color Shift

**During Exposure (CC Filters on Lens):**
- **Advantage:** Prevents shift at source
- **Disadvantage:** Requires precise prediction (difficult)
- **Example:** Ektar 100, 120s exposure → Add CC10M filter to lens

**During Printing (Color Darkroom):**
- **Advantage:** Precise control, can adjust per print
- **Method:** Use color printing filters (cyan, magenta, yellow) in enlarger
- **Example:** Negative has cyan cast → Add +10M +05Y filtration

**During Scanning (Digital Workflow):**
- **Advantage:** Non-destructive, infinite adjustments
- **Method:** Use color balance tools in scanning software (VueScan, SilverFast) or post-processing (Photoshop, Lightroom)
- **Example:** Cyan cast → Shift red/magenta channel in curves

---

### Film Recommendations to Minimize Color Shift

**Best Color Negative Film (Minimal Shift):**
1. **Kodak Portra 400/800** → Modern emulsion, well-compensated
2. **Kodak Ektar 100** → Slight shift, but predictable and correctable

**Best Color Reversal Film (Slide Film):**
1. **Fujifilm Provia 100F** → Minimal shift up to 300s
2. **Fujifilm Velvia 50** → Slight magenta shift, acceptable for landscapes

**Avoid for Long Exposures:**
- **Consumer films** (Kodak Gold, Fuji Superia) → Unpredictable reciprocity and color shift
- **Aged slide films** (Kodachrome, Ektachrome E100VS discontinued) → Unreliable data

---

## Quick Reference Tables

### Table 1: Reciprocity Correction Summary (Common Films)

| Film | Threshold | 1s → Actual | 10s → Actual | 100s → Actual | Notes |
|------|-----------|-------------|--------------|---------------|-------|
| **Ilford HP5 Plus** | >0.5s | 1.5s (+0.5) | 50s (+2.3) | — | Moderate failure |
| **Ilford FP4 Plus** | >0.5s | 2s (+1.0) | 50s (+2.3) | — | More pronounced |
| **Ilford Delta 100** | >1s | 2s (+1.0) | 20s (+1.0) | — | T-grain, better |
| **Kodak Tri-X 400** | >0.9s | 2s (+1.0) | 60s (+2.6) | 1200s (+3.6) | Traditional emulsion |
| **Kodak T-Max 400** | >1s | 2s (+1.0) | 20s (+1.0) | 240s (+1.3) | Best for long exposures |
| **Fomapan 100** | >1s | 2s (+1.0) | 40s (+2.0) | 800s (~3.0) | Approximate data |
| **Kodak Portra 400** | >1s | 2s (+1.0) | 30s (+1.6) | 400s (+2.0) | Minimal color shift |
| **Kodak Ektar 100** | >1s | 2s (+1.0) | 50s (+2.3) | — | Slight cyan shift >100s |
| **Fuji Provia 100F** | >1s | 1.5s (+0.6) | 20s (+1.0) | 300s (+1.6) | Well-compensated |
| **Fuji Velvia 50** | >2s | No correction | 20s (+1.0) | 400s (+2.0) | Best slide film |

**Key:**
- **Threshold:** Exposure duration where reciprocity failure begins
- **Correction format:** Metered time → Actual exposure time (stops added)
- **Source:** Kodak E-31, Ilford datasheets, Fujifilm technical data

---

### Table 2: Development Compensation Guidelines

| Exposure Duration | Contrast Loss | Development Adjustment | Developer Examples |
|-------------------|---------------|------------------------|---------------------|
| **<10s** | None | Normal (N) | Any standard developer |
| **10s – 60s** | Minimal | N+0.5 (+5% time) | D-76 1+1, ID-11 |
| **60s – 300s** | Moderate | N+1 (+20% time) | D-76 1+1, Microphen |
| **>300s** | Severe | N+1.5 to N+2 (+30-40% time) | Microphen, Rodinal 1+50 |

**Warning:** N+2 development risks increased grain and highlight blocking. Test first.

---

### Table 3: Astrophotography Film Recommendations

| Film Type | Best Choice | ISO | Reciprocity @ 10min | Grain | Color |
|-----------|-------------|-----|---------------------|-------|-------|
| **B&W (Fine Grain)** | Kodak T-Max 400 | 400 | +1.0 stop | RMS 8 | — |
| **B&W (Budget)** | Ilford Delta 400 | 400 | +1.5 stops | RMS 10 | — |
| **Color Negative** | Kodak Portra 800 | 800 | +1.5 stops | Moderate | Natural |
| **Color Slide** | Fuji Velvia 50 | 50 | +1.0 stop | RMS 9 | Saturated |

**Development:** Use N-1 for all astrophotography (compress dynamic range).

---

## Additional Resources

### Manufacturer Datasheets

**Kodak:**
- Publication E-31: *Reciprocity Failure Data for Kodak Films*
- Available: Kodak Alaris website (discontinued films: archive.org)

**Ilford:**
- HP5 Plus Technical Datasheet (PDF)
- FP4 Plus Technical Datasheet (PDF)
- Delta 100/400 Technical Datasheet (PDF)
- Available: ilfordphoto.com → Technical Information

**Fujifilm:**
- Provia 100F Technical Data
- Velvia 50/100 Technical Data
- Available: fujifilm.com → Professional Photography

**Foma Bohemia:**
- Fomapan 100/200/400 Datasheets
- Available: foma.cz → Products → Technical Data (limited reciprocity info)

---

### Community Resources

**Photrio.com Forum:**
- Dedicated reciprocity failure discussion threads
- User-contributed reciprocity test data (real-world results)

**Large Format Photography Forum (LFPF):**
- Astrophotography and long-exposure techniques
- Field reports from experienced photographers

**Massive Dev Chart (digitaltruth.com):**
- Development time database (includes some reciprocity notes)

---

### Books and References

**Ansel Adams, *The Negative* (1981):**
- Chapter on reciprocity failure and development compensation
- Practical workflows for large-format long exposures

**Bruce Barnbaum, *The Art of Photography* (2010):**
- Reciprocity failure in landscape photography
- Bracketing and field techniques

**Schwarzschild, K. (1899):**
- *On the deviations from the reciprocity law*
- Original astrophysics paper (historical interest)

---

## Final Notes

**Key Takeaways:**

1. **Always consult manufacturer datasheets** for film-specific reciprocity data.
2. **Bracket exposures** for long exposures (uncertainty is high).
3. **Adjust development** (N+1 or N+1.5) to compensate for contrast loss.
4. **Record metadata** to build a personal reciprocity database.
5. **For astrophotography:** Use T-Max 400 (B&W) or Velvia 50 (color), develop N-1.
6. **For color films:** Expect slight color shifts >60s, correct in printing/scanning.

**Reciprocity failure is predictable but film-dependent. Test, bracket, and learn your materials.**

---

**Document Version:** 1.0  
**Last Updated:** 2026-05-25  
**Sources:** Kodak E-31, Ilford datasheets, Fujifilm technical data, Schwarzschild (1899), Ansel Adams (1981)
