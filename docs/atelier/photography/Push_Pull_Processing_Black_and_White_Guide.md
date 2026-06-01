# Push and Pull Processing for Black & White Film

*A comprehensive technical guide to modified development for fine art photography*

---

## Overview

Push and pull processing are **controlled modifications of film development** that alter the relationship between exposure and final negative density. These techniques allow photographers to:

- **Compensate for deliberate underexposure or overexposure**
- **Modify contrast and tonal rendering**
- **Create specific aesthetic effects**
- **Adapt to challenging lighting conditions**
- **Control grain structure and sharpness**

This guide covers the **physical, chemical, and artistic aspects** of push/pull processing with emphasis on:

- **Fine art black & white photography**
- **Medium format workflow** (120 film)
- **Darkroom fiber-based printing**
- **Atmospheric, melancholic imagery** (fog, winter, quiet contemplation)
- **Zone System integration**
- **Understanding WHY, not just HOW**

**Information sources:** All development times, technical specifications, and recommendations are verified from:

- Manufacturer datasheets (Ilford, Kodak, Foma, Rollei)
- Massive Dev Chart (community-verified development database)
- Ansel Adams *The Negative* and *The Print*
- Photrio and APUG darkroom community archives
- Sensitometric research (Ilford technical publications, BTZS system)

---

## 1. What is Push and Pull Processing? (The Physics)

### Physical Basis: What Happens to the Film

Black & white film consists of **silver halide crystals** (typically silver bromide with small amounts of silver iodide) suspended in a gelatin emulsion. When light strikes these crystals during exposure, it creates a **latent image** - invisible structural changes in the crystals that makes them developable.

**Development** is a chemical reduction process:

```
Silver Halide (AgBr) + Developer → Metallic Silver (Ag) + Bromide (Br-)
```

The developer **selectively reduces** exposed silver halide crystals to metallic black silver, creating the visible image.

#### Normal Development

**Standard process:**

1. **Exposure:** Light creates latent image in silver halide crystals
2. **Development:** Developer converts exposed crystals to metallic silver
3. **Density:** Amount of metallic silver determines negative density (darkness)
4. **Relationship:** More exposure → more latent image → more developed silver → higher density

**Characteristic curve (H&D curve, D-log H curve):**

The relationship between exposure (log H, horizontal axis) and resulting density (D, vertical axis) forms a characteristic S-shaped curve:

```
Density
  │
  │         ┌──────────  Shoulder (highlights, Dmax)
  │        ╱
  │       ╱
  │      ╱   ← Straight-line portion (gamma, contrast)
  │     ╱
  │    ╱
  │   ╱
  │  ╱
  │ ╱______   Toe (shadows, Dmin + fog)
  │
  └──────────────────────────> Log Exposure (H)
    Shadow  Midtone  Highlight
```

**Key regions:**

- **Toe:** Shadow detail - curved, lower contrast
- **Straight-line portion:** Midtones to highlights - linear relationship, slope = gamma (contrast)
- **Shoulder:** Extreme highlights - curved, compressing toward Dmax
- **Gamma (γ):** Slope of straight-line portion = contrast

#### Push Processing: What Happens Physically

**Push processing = Extended development time** (typically 20-50% longer than normal)

**Physical effects:**

1. **Increased development energy:**
   - Developer continues working after normal development would stop
   - More silver halide crystals are reduced to metallic silver
   - **Highlights:** Already-developed areas gain more density
   - **Shadows:** Under-exposed areas have fewer latent image sites - limited additional density gain

2. **Characteristic curve changes:**
   ```
   Density
     │
     │    ┌─────  Pushed curve (steeper, higher gamma)
     │   ╱╱
     │  ╱╱  Normal curve
     │ ╱╱
     │╱╱
     └──────────> Log Exposure
   ```
   - **Increased gamma:** Steeper slope = higher contrast
   - **Shoulder shifts:** Highlights gain density faster than shadows
   - **Toe remains similar:** Shadow density barely increases

3. **Why shadows don't "push":**
   - **Latent image density is fixed at exposure time**
   - Under-exposed shadows have few developed silver crystals
   - Extended development can't create silver from nothing
   - **You cannot develop detail that was never exposed**

4. **Result:**
   - **Increased overall density** (highlights become denser)
   - **Increased contrast** (midtones to highlights separate more)
   - **Minimal shadow gain** (shadows remain thin)
   - **Effective speed increase** (can rate film at higher ISO)

**Critical understanding:** Push processing does NOT increase film sensitivity. It **compensates for underexposure** by increasing development to bring midtone and highlight density to printable levels, at the cost of increased contrast and grain.

#### Pull Processing: What Happens Physically

**Pull processing = Reduced development time** (typically 15-30% shorter than normal)

**Physical effects:**

1. **Reduced development energy:**
   - Development stops before all exposed silver halide is reduced
   - **Highlights:** Heavily exposed areas don't reach maximum density
   - **Shadows:** Lightly exposed areas develop more gently

2. **Characteristic curve changes:**
   ```
   Density
     │
     │  ┌─────  Normal curve
     │ ╱
     │╱  Pulled curve (flatter, lower gamma)
     │╱
     └──────────> Log Exposure
   ```
   - **Decreased gamma:** Flatter slope = lower contrast
   - **Shoulder compression:** Highlights don't reach Dmax
   - **Toe expansion:** Shadow detail rendered more gently

3. **Result:**
   - **Decreased overall density** (entire curve shifts down)
   - **Decreased contrast** (gentler tonal gradations)
   - **Retained highlight detail** (overexposed highlights don't block)
   - **Smooth, delicate tonality** (ideal for atmospheric work)

**Critical understanding:** Pull processing compensates for **overexposure** by reducing development to prevent highlight blocking and excessive contrast, creating smooth, low-contrast negatives ideal for fine art printing.

### What Push/Pull Does NOT Do

**Common misconceptions:**

1. **"Push processing increases film speed"**
   - **FALSE:** Film sensitivity is fixed by emulsion chemistry
   - **TRUE:** Push processing compensates for underexposure, allowing you to *rate* the film at higher ISO
   - **Reality:** You're accepting thin shadows in exchange for printable midtone/highlight density

2. **"Pull processing decreases film speed"**
   - **FALSE:** Film sensitivity hasn't changed
   - **TRUE:** You intentionally overexpose and reduce development to control contrast
   - **Reality:** You're trading excess highlight density for smoother tonality

3. **"Push/pull changes the film's ISO rating"**
   - **FALSE:** ISO is a manufacturer specification based on standardized sensitometry
   - **TRUE:** You are choosing a personal **Exposure Index (EI)** that differs from box ISO
   - **Reality:** The film's inherent sensitivity hasn't changed - you've changed your exposure and development strategy

### Sensitometry Basics

**Key terms:**

- **Density (D):** Logarithmic measure of negative opacity
  - D = log₁₀(incident light / transmitted light)
  - D = 0.3 means 50% transmission (one stop of light blocked)
  - D = 0.6 means 25% transmission (two stops blocked)
  - D = 1.0 means 10% transmission
  - D = 2.0 means 1% transmission (very dense negative)

- **Gamma (γ):** Slope of characteristic curve's straight-line portion
  - γ = ΔDensity / ΔLog Exposure
  - Higher gamma = higher contrast
  - Normal development: γ ≈ 0.55-0.65 (varies by film/developer)
  - Pushed development: γ ≈ 0.7-0.9
  - Pulled development: γ ≈ 0.4-0.5

- **Base + Fog (Dmin):** Minimum density of unexposed, developed film
  - Caused by film base tint + chemical fog
  - Typical values: 0.08-0.15
  - Push processing increases fog (Dmin rises to 0.15-0.25)

- **Maximum Density (Dmax):** Maximum achievable density
  - Limited by emulsion thickness and silver content
  - Typical values: 1.8-2.5 for normal film
  - Pushed film can exceed 2.5 in extreme highlights

### Contrast and Gamma Explained

**Gamma determines printing contrast:**

- **Low gamma (0.4-0.5):** Low-contrast negative
  - Requires high-contrast printing (Grade 3-4)
  - Smooth, gentle tonal rendering
  - Ideal for atmospheric fine art work

- **Normal gamma (0.55-0.65):** Standard negative
  - Prints on Grade 2-3 paper
  - Balanced tonal range

- **High gamma (0.7-0.9):** High-contrast negative
  - Requires low-contrast printing (Grade 1-2)
  - Punchy, graphic tonality
  - Can be difficult to print if gamma is too high (>0.9)

**Push/pull and gamma:**

- **Push +1 stop:** Gamma increases ~15-25% (e.g., 0.6 → 0.72)
- **Push +2 stops:** Gamma increases ~30-50% (e.g., 0.6 → 0.85)
- **Pull -1 stop:** Gamma decreases ~15-25% (e.g., 0.6 → 0.48)

**Zone System relationship:**

- **N (Normal) development:** Standard gamma for average scenes (SBR ~7 stops)
- **N+1 (Expansion):** Increased gamma for low-contrast scenes (SBR ~5 stops)
- **N-1 (Contraction):** Decreased gamma for high-contrast scenes (SBR ~9 stops)

Push processing is **similar to N+1/N+2 expansion**, but motivated by underexposure rather than scene contrast.

---

## 2. Exposure Index vs. ISO: Critical Distinction

### ISO: Manufacturer Specification

**ISO rating** (International Organization for Standardization) is a **standardized measurement** of film sensitivity based on:

1. **Sensitometric testing** under controlled conditions
2. **Specific density threshold** for "correct" exposure
3. **Standardized development** (manufacturer-specified time/temperature)

**Example:** Ilford Delta 100 ISO 100 means:

- Under standardized testing conditions
- Developed in specific developer for specific time
- Achieves target density for Zone V (middle gray)
- **This is a laboratory specification, not necessarily your working speed**

### Exposure Index (EI): Your Working Speed

**Exposure Index (EI)** is the **ISO value you set on your meter** based on:

1. **Your personal workflow** (developer, development time, agitation)
2. **Your desired tonal rendering** (shadow placement, highlight retention)
3. **Your printing or scanning workflow**
4. **Your aesthetic goals**

**Example:** Rating Delta 100 at EI 64 means:

- You intentionally overexpose by 2/3 stop vs box ISO
- Your meter calculates exposure as if film were ISO 64
- You develop normally (or pull slightly)
- **Result:** Richer shadow detail, gentler highlight rolloff

**EI is personal and situational** - there is no "wrong" EI if it produces the results you want.

### Why Photographers Use Different EI than Box ISO

**Common personal EI adjustments:**

1. **EI lower than box ISO (overexposure):**
   - **Reason:** Prioritize shadow detail (Zone System "expose for shadows")
   - **Example:** Delta 100 rated at EI 64-80
   - **Result:** Rich shadows, full tonal range, slight overexposure
   - **Development:** Normal or pulled to control contrast

2. **EI higher than box ISO (underexposure):**
   - **Reason:** Shooting in low light without tripod
   - **Example:** Delta 100 rated at EI 200-400 (push processing required)
   - **Result:** Thin shadows, increased contrast, increased grain
   - **Development:** Pushed to compensate for underexposure

3. **EI matching box ISO:**
   - **Reason:** Trust manufacturer rating
   - **Example:** Delta 100 rated at EI 100
   - **Result:** Balanced exposure, predictable results
   - **Development:** Normal

### Shadow Placement and EI

**Zone System principle:** "Expose for the shadows, develop for the highlights"

**Shadow placement determines EI:**

1. **Identify important shadow detail** (darkest area you want visible detail in)
2. **Meter shadow:** Spot meter or incident meter reading
3. **Place shadow at Zone III:**
   - **Zone III = 2 stops below middle gray (Zone V)**
   - Meter reading gives you Zone V
   - Open aperture 2 stops (or slow shutter 2 stops) to place reading at Zone III
4. **Calculate EI:**
   - If you consistently place shadows at Zone III
   - Your effective EI is ~2/3 to 1 stop lower than box ISO
   - **Example:** ISO 100 film, metered shadow at 1/60 f/8 (Zone V)
   - Open to 1/60 f/4 (Zone III placement) = 2 stops overexposure
   - **Effective EI ≈ 64-80** (between 1 and 2 stops overexposure)

**Community consensus:**

- **Fine art photographers often rate ISO 100 films at EI 64-80** for rich shadows
- **Photojournalists often rate ISO 400 films at EI 250-320** to protect highlights
- **Commercial photographers typically use box ISO** for predictable lab results

### Personal EI Calibration

**How to find your personal EI:**

1. **Test exposure series:**
   - Meter static scene (gray card or average scene)
   - Shoot bracketed exposures: -1, -2/3, -1/3, box ISO, +1/3, +2/3, +1 stop
   - Develop all frames identically

2. **Evaluate negatives:**
   - **Examine shadow detail:** Which exposure gives desired Zone III density?
   - **Examine highlights:** Which exposure retains desired highlight detail?
   - **Print or scan:** Which negative produces best print/scan?

3. **Determine personal EI:**
   - If "+1/3 stop overexposure" looks best: Personal EI = box ISO ÷ 1.25
   - If "+2/3 stop overexposure" looks best: Personal EI = box ISO ÷ 1.5
   - If "+1 stop overexposure" looks best: Personal EI = box ISO ÷ 2

**Example calculation:**

- Film: Ilford Delta 100 (box ISO 100)
- Test result: "+2/3 stop overexposure" produces best negatives
- Personal EI = 100 ÷ 1.5 = **EI 64**
- **Set your meter to ISO 64 for this film with your workflow**

**Variables affecting personal EI:**

- **Developer choice:** High-acutance developers (Rodinal) may need lower EI
- **Development time:** Reduced development may need lower EI (more initial exposure)
- **Enlarger type:** Condenser enlargers benefit from slightly denser negatives (lower EI)
- **Printing paper:** High-contrast papers work better with low-contrast negatives (may need higher EI + pull development)

---

## 3. Push Processing: Detailed Analysis

### How Push Processing Works

**Process:**

1. **Expose film at higher EI than box ISO:**
   - Example: ISO 100 film shot at EI 400 = +2 stops underexposure

2. **Increase development time:**
   - +1 stop push: Increase development ~20-30%
   - +2 stops push: Increase development ~40-60%
   - +3 stops push: Increase development ~60-100%
   - (Exact times vary by film and developer - see section 6)

3. **Result:**
   - Midtone and highlight density increased to printable levels
   - Shadows remain thin (latent image was insufficient)
   - Overall contrast increased

### What You Gain

**1. Ability to shoot in lower light:**

- Rate ISO 100 film at EI 400: **+2 stops** of effective light sensitivity
- Example: Metered exposure 1/15 @ f/2.8 (too slow for handheld)
- Pushed exposure: 1/60 @ f/2.8 (hand-holdable)
- **Allows handheld shooting in dim conditions**

**2. Increased contrast (when desired):**

- Flat, overcast scenes benefit from push development
- **N+1 expansion** in Zone System terms
- Adds "punch" to low-contrast subjects

**3. Enhanced grain character:**

- Push processing increases grain size and visibility
- **Intentional aesthetic choice** for gritty, photojournalistic look
- Grain adds texture and "film feel"

**4. Higher acutance:**

- Extended development in high-acutance developers (Rodinal, Pyro) produces very sharp edges
- **Maximum edge definition** for graphic work

### What You Lose

**1. Shadow detail (CRITICAL LIMITATION):**

- Underexposed shadows have insufficient latent image
- Extended development cannot create detail that wasn't exposed
- **Shadows go black** (Zones 0-II are lost)
- **You cannot push shadow detail into existence**

**2. Increased grain:**

- Extended development causes grain clumping (silver grains fuse)
- Grain becomes more visible and coarse
- **Fine-grain aesthetic is lost**

**3. Increased contrast:**

- May be too contrasty for some subjects
- High-contrast negatives are difficult to print
- **Requires low-contrast printing paper** (Grade 0-1)

**4. Increased base fog:**

- Extended development produces chemical fog (non-image silver)
- Base + fog density increases (Dmin rises)
- **Slight veiling in shadow areas**

**5. Loss of highlight latitude:**

- Pushed highlights can block (reach Dmax)
- Overexposed areas go completely opaque
- **Reduced "safety margin" for exposure errors**

### When to Push Process

**Good reasons:**

1. **Insufficient light for box ISO:**
   - Indoor events, concerts, low-light street photography
   - No tripod available
   - Subject movement requires fast shutter speed

2. **Intentional high-contrast aesthetic:**
   - Gritty photojournalism look
   - Film noir cinematography-inspired imagery
   - Graphic, high-contrast prints

3. **Low-contrast scenes needing enhancement:**
   - Flat overcast landscapes (push to add contrast)
   - Foggy scenes where you want more separation
   - **N+1 expansion** in Zone System workflow

4. **Grain as creative element:**
   - Embracing "film look" with visible grain
   - Texture and atmosphere through grain structure

**Poor reasons:**

1. **"I want to use faster shutter speeds always":**
   - Use faster film (ISO 400) instead of pushing ISO 100
   - Push processing is not a substitute for appropriate film choice

2. **"I accidentally underexposed":**
   - Push can salvage underexposed frames, but quality will suffer
   - Prevention (correct exposure) is better than correction (push)

3. **"I want fine-grain AND high effective speed":**
   - These are contradictory goals
   - Choose: Fine grain (ISO 100 at box speed) OR high speed (ISO 400+ film)

### Push Levels and Their Effects

#### Push +1 Stop (EI 2× box ISO)

**Example:** ISO 100 film rated at EI 200

**Development increase:** +20-30% time (varies by developer)

**Effects:**

- **Contrast:** Moderate increase (~15-20% gamma increase)
- **Grain:** Slight increase (barely noticeable in fine-grain films)
- **Shadow loss:** Minimal (Zones 0-I lost, Zone II thin)
- **Highlight behavior:** Slightly denser, still printable
- **Printability:** Excellent - negatives still print on Grade 2-3 paper
- **Fine art suitability:** Very good - acceptable trade-offs for most work

**Community consensus:** **+1 stop push is very usable** for most films - grain increase is minimal, contrast is manageable, results are high-quality.

**Recommended for:**

- Low-light fine art work where tripod isn't practical
- Slight contrast boost for flat scenes
- Emergency underexposure recovery

#### Push +2 Stops (EI 4× box ISO)

**Example:** ISO 100 film rated at EI 400

**Development increase:** +40-60% time

**Effects:**

- **Contrast:** Significant increase (~30-40% gamma increase)
- **Grain:** Noticeable increase (grain becomes visible even in T-grain films)
- **Shadow loss:** Substantial (Zones 0-II lost, Zone III thin)
- **Highlight behavior:** Dense, approaching or reaching Dmax
- **Printability:** Challenging - requires Grade 0-1 paper or significant print manipulation
- **Fine art suitability:** Moderate - grain and contrast may conflict with subtle atmospheric goals

**Community consensus:** **+2 stops push is acceptable but compromised** - grain is visible, contrast is high, shadow detail is lost. Suitable for photojournalism or intentional gritty aesthetic, less ideal for fine art.

**Recommended for:**

- Low-light shooting where no alternative exists
- Intentional high-contrast, grainy aesthetic
- Subjects where shadow detail is unimportant (backlit silhouettes, high-key work)

#### Push +3 Stops (EI 8× box ISO)

**Example:** ISO 100 film rated at EI 800

**Development increase:** +60-100% time (often double normal time)

**Effects:**

- **Contrast:** Extreme increase (~50-70% gamma increase)
- **Grain:** Very coarse, prominent grain structure
- **Shadow loss:** Severe (Zones 0-III lost, Zone IV thin)
- **Highlight behavior:** Blocked (Dmax reached or exceeded)
- **Printability:** Difficult - requires Grade 0 or split-grade printing
- **Fine art suitability:** Poor for subtle work - grain and contrast dominate
- **Base fog:** Noticeable gray veil in unexposed areas

**Community consensus:** **+3 stops push is emergency/special-effect territory** - image quality is significantly degraded. Only use when absolutely necessary or for intentional extreme aesthetic.

**Recommended for:**

- Desperation shooting (cannot capture image otherwise)
- Extreme gritty aesthetic (intentional degradation as artistic choice)
- **NOT recommended for fine art atmospheric work**

#### Push Beyond +3 Stops

**Generally not recommended:**

- Image quality deteriorates rapidly beyond +3 stops
- Grain becomes objectionable
- Contrast becomes unmanageable
- Shadow detail is completely lost
- Base fog becomes significant

**Exception:** Some modern T-grain films (Ilford Delta 3200, Kodak T-Max P3200Z) are designed for extreme pushing and can be pushed to EI 12,800-25,000 with specialized developers. However, these are NOT fine art films - they are photojournalism/surveillance films.

---

## 4. Pull Processing: Detailed Analysis

### How Pull Processing Works

**Process:**

1. **Expose film at lower EI than box ISO:**
   - Example: ISO 100 film shot at EI 50 = +1 stop overexposure

2. **Decrease development time:**
   - -1 stop pull: Reduce development ~15-25%
   - -2 stops pull: Reduce development ~25-40%

3. **Result:**
   - Overall density reduced
   - Contrast decreased
   - Smooth, gentle tonality
   - Overexposed highlights prevented from blocking

### Why Use Pull Processing

**Primary motivations:**

1. **Contrast control:**
   - High-contrast scenes (bright sun + deep shadow)
   - **N-1 contraction** in Zone System terms
   - Compress extreme tonal range into printable scale

2. **Aesthetic gentleness:**
   - Soft, dreamy, ethereal tonality
   - Low-contrast atmospheric photography
   - Melancholic, contemplative mood

3. **Highlight protection:**
   - Overexposed film (accidental or intentional)
   - Prevent highlight blocking
   - Retain detail in bright areas

4. **Fine-grain enhancement:**
   - Reduced development = finer grain
   - Smooth, grainless aesthetic

### What You Gain

**1. Smooth, gentle tonality:**

- Reduced gamma = lower contrast
- Delicate tonal gradations
- **Perfect for atmospheric fine art work**

**2. Protected highlights:**

- Overexposed highlights don't block
- Detail retained in bright areas
- **Increased highlight latitude**

**3. Finer grain:**

- Reduced development = less grain clumping
- Smoother grain structure
- **Ultra-fine grain aesthetic**

**4. Expanded tonal scale:**

- High-contrast scenes compressed into printable range
- **Zones I-IX captured** instead of losing Zones I or IX to blocking

### What You Lose

**1. Contrast (may require compensation in printing):**

- Low-contrast negatives require high-contrast printing paper
- **Typically requires Grade 3-4 paper** or higher multigrade filtration
- May need longer exposure times due to low negative density

**2. Overall density:**

- Negatives are thinner (less dense) overall
- **Longer printing exposures** required
- May approach minimum printable density

**3. Shadow separation (if pulled too much):**

- Extreme pulls (-2 stops or more) can cause shadow detail to merge
- **Zones II-III may compress together**

### When to Pull Process

**Excellent reasons:**

1. **High-contrast scenes:**
   - Bright sunlight with deep shadows (SBR 9-10 stops)
   - Backlit subjects
   - **N-1 or N-2 contraction** to compress tonal range

2. **Atmospheric fine art photography:**
   - Fog, mist, overcast scenes (already low contrast)
   - Intentional soft, dreamy aesthetic
   - **"Window to another dimension" melancholic imagery**

3. **Overexposed film (accidental or intentional):**
   - Salvage accidentally overexposed frames
   - Intentional overexposure for maximum shadow detail + pull to control highlights

4. **Fine-grain priority:**
   - Maximum enlargement from 35mm or 120
   - Grainless, smooth tonality
   - **"Large format look" from smaller formats**

**Poor reasons:**

1. **"I want more speed":**
   - Pull processing reduces effective speed (you're overexposing)
   - Use faster film instead if you need speed

2. **All situations:**
   - Pull is not a universal improvement
   - Normal development is ideal for average-contrast scenes
   - Pull is a **specialized technique for specific situations**

### Pull Levels and Their Effects

#### Pull -1 Stop (EI 1/2 box ISO)

**Example:** ISO 100 film rated at EI 50

**Development reduction:** -15-25% time

**Effects:**

- **Contrast:** Moderate decrease (~15-20% gamma reduction)
- **Grain:** Slight reduction (finer grain)
- **Tonality:** Smooth, gentle gradations
- **Highlight behavior:** Protected from blocking
- **Printability:** Very good - requires Grade 3-4 paper
- **Fine art suitability:** Excellent - smooth, delicate tonality ideal for atmospheric work

**Community consensus:** **-1 stop pull is very usable and beautiful** for fine art work - produces smooth, low-contrast negatives perfect for matte fiber printing.

**Recommended for:**

- High-contrast scenes (SBR ~9 stops)
- Atmospheric fog/mist photography
- Intentional dreamy, soft aesthetic
- Fine-grain priority

#### Pull -2 Stops (EI 1/4 box ISO)

**Example:** ISO 100 film rated at EI 25

**Development reduction:** -25-40% time

**Effects:**

- **Contrast:** Significant decrease (~30-40% gamma reduction)
- **Grain:** Very fine (near-grainless)
- **Tonality:** Extremely smooth, almost flat
- **Highlight behavior:** Well-protected, gentle rolloff
- **Printability:** Challenging - requires Grade 4-5 paper or heavy multigrade filtration
- **Fine art suitability:** Moderate - very low contrast may lack "presence"

**Community consensus:** **-2 stops pull is specialized** - produces very low contrast negatives suitable for extreme high-contrast scenes or ultra-smooth aesthetic. May be too flat for general use.

**Recommended for:**

- Extreme high-contrast scenes (SBR 10+ stops, harsh sunlight)
- Intentional ultra-smooth, ethereal aesthetic
- **Rarely needed** - most scenes don't require -2 stop pull

#### Pull Beyond -2 Stops

**Rarely used:**

- Contrast becomes so low that printing becomes difficult
- Shadow detail may merge (Zones II-III indistinguishable)
- Negatives approach fog level (minimal density difference)

**Exception:** Some photographers pull -3 stops for **ultra-high-contrast scenes** (snow in bright sun, architecture with extreme bright/dark areas). Requires specialized printing technique.

---

## 5. Film-Specific Push/Pull Behavior

Different films respond very differently to push and pull processing. Modern T-grain films handle pushing better than traditional cubic-grain films, while traditional films often pull more gracefully.

### Fomapan 100 (Traditional Cubic Grain)

**Push characteristics:**

- **+1 stop:** Very good - grain increases moderately, contrast manageable
- **+2 stops:** Good - noticeable grain, high contrast, but usable
- **+3 stops:** Acceptable - coarse grain, very high contrast, emergency use only

**Why Fomapan pushes well:**

- Traditional emulsion has natural latitude
- Cubic grain structure tolerates extended development
- **Community favorite for push processing** due to low cost + good results

**Pull characteristics:**

- **-1 stop:** Excellent - smooth, gentle tonality, fine grain
- **-2 stops:** Very good - ultra-smooth, may be too flat for some scenes

**Recommended push/pull workflows:**

- **Push +1 (EI 200):** HC-110 Dilution B, +25% time
- **Pull -1 (EI 50):** XTOL 1:1, -20% time (ultra-smooth atmospheric work)

**Fine art use:** Fomapan 100 pulled to EI 50 in XTOL produces **beautiful low-contrast negatives** perfect for melancholic atmospheric printing.

### Ilford Delta 100 (Modern T-Grain)

**Push characteristics:**

- **+1 stop:** Excellent - minimal grain increase, good contrast control
- **+2 stops:** Very good - grain visible but fine, contrast manageable
- **+3 stops:** Good - grain increased but still finer than pushed cubic-grain films
- **+4 stops (EI 1600):** Acceptable - emergency use

**Why Delta pushes exceptionally well:**

- T-grain structure resists grain clumping
- Modern emulsion design optimized for pushing
- **Best-pushing ISO 100 film available**

**Community experience:** Delta 100 pushed +2 stops produces **finer grain than many ISO 400 films at box speed** - remarkable performance.

**Pull characteristics:**

- **-1 stop:** Excellent - ultra-fine grain, smooth tonality
- **-2 stops:** Very good - near-grainless, very low contrast

**Recommended push/pull workflows:**

- **Push +1 (EI 200):** DD-X stock, +25% time (maintains fine grain)
- **Push +2 (EI 400):** DD-X stock or Microphen, +50% time
- **Pull -1 (EI 50):** DD-X 1:4 or Perceptol, -20% time (ultra-fine grain)

**Fine art use:** Delta 100 is **versatile** - pushes well for low-light work, pulls beautifully for atmospheric scenes. Best choice if you need both capabilities.

### Ilford SFX 200 (Extended Red Sensitivity)

**Push characteristics:**

- **+1 stop:** Good - grain increases moderately
- **+2 stops:** Acceptable - noticeable grain, high contrast
- **Beyond +2:** Not recommended - grain becomes coarse

**Special consideration:** SFX 200 is often used with deep red or IR filters (requiring 2-3 stops exposure compensation). **Avoid combining IR filter + push processing** - grain and contrast become excessive.

**Pull characteristics:**

- **-1 stop:** Very good - smooth tonality, controlled grain
- **-2 stops:** Good - low contrast suitable for IR work

**Recommended workflow:**

- **Standard IR use:** Rate at box ISO 200, use deep red filter (+3 stops compensation), develop normally
- **Pull for smooth IR aesthetic:** Rate at EI 100, use orange/red filter, pull -1 stop
- **NOT recommended:** Push processing with IR filters (too grainy/contrasty)

**Fine art use:** SFX 200 pulled slightly produces **smooth, dreamlike IR-simulation images** without excessive contrast.

### Rollei Infrared 400 (True Infrared Sensitivity)

**Push characteristics:**

- **+1 stop:** Acceptable - grain increases significantly
- **+2 stops:** Poor - grain becomes very coarse, contrast extreme
- **Not recommended for push beyond +1 stop**

**Why Rollei IR doesn't push well:**

- Already high base ISO (400)
- Infrared-sensitive emulsion is inherently grainy
- Extended development produces unmanageable grain

**Pull characteristics:**

- **-1 stop:** Good - reduces grain slightly, smooths tonality
- **-2 stops:** Acceptable - very smooth but may lack IR "punch"

**Recommended workflow:**

- **Standard IR:** Rate at box ISO 400, use IR filter (720nm or 850nm), develop normally
- **Smooth IR aesthetic:** Rate at EI 200 (-1 stop), use 720nm filter, pull -1 stop development
- **Avoid pushing** - grain becomes too coarse

**Fine art use:** Rollei IR at box speed or pulled produces **distinctive infrared aesthetic**. Pushing degrades image quality unacceptably.

### Ilford Pan F Plus 50 (Ultra-Fine Grain)

**Push characteristics:**

- **+1 stop:** Good - grain increases but remains fine
- **+2 stops:** Acceptable - grain visible, contrast high
- **Beyond +2:** Not recommended - defeats film's fine-grain purpose

**Why Pan F Plus shouldn't be pushed heavily:**

- Film's primary virtue is **ultra-fine grain**
- Pushing negates this advantage
- **Use faster film** (Delta 100, HP5) if you need higher speed

**Pull characteristics:**

- **-1 stop:** Excellent - ultra-fine grain, smooth tonality
- **-2 stops:** Very good - near-grainless, very low contrast
- **-3 stops:** Acceptable for extreme smooth aesthetic

**Recommended workflow:**

- **Fine-grain priority:** Rate at box ISO 50, develop in Perceptol or DD-X 1:4
- **Ultra-smooth atmospheric:** Rate at EI 25-32 (-1 to -2 stops), pull in Perceptol or XTOL 1:2
- **Avoid pushing beyond +1 stop** - defeats film's purpose

**Fine art use:** Pan F Plus at EI 25-32, pulled in Perceptol, produces **grainless, ethereal negatives** perfect for large enlargements and atmospheric fine art work.

### Summary Table: Film Push/Pull Suitability

| Film | Best Push | Acceptable Push | Best Pull | Notes |
|------|-----------|----------------|-----------|-------|
| **Fomapan 100** | +1 to +2 stops | +3 stops | -1 stop | Good all-around push/pull capability |
| **Ilford Delta 100** | +1 to +3 stops | +4 stops | -1 to -2 stops | Best-pushing ISO 100 film |
| **Ilford SFX 200** | +1 stop | +2 stops (without IR filter) | -1 stop | Avoid push with IR filters |
| **Rollei IR 400** | Avoid | +1 stop (no IR filter) | -1 stop | Already grainy, don't push |
| **Pan F Plus 50** | Avoid | +1 stop (defeats purpose) | -1 to -2 stops | Pull for ultra-fine grain |

---

## 6. Developer-Specific Push/Pull Behavior

Developer choice dramatically affects push/pull results. High-acutance developers (Rodinal, Pyro) enhance grain and sharpness when pushing. Fine-grain developers (XTOL, Perceptol) minimize grain increase. Compensating developers (Rodinal at high dilution, Pyro) protect highlights during push processing.

### Rodinal (High Acutance, Compensating)

**Push characteristics:**

- **Grain:** Increases significantly when pushing (sharp-edged grain)
- **Acutance:** Excellent - maximum edge sharpness even when pushed
- **Contrast:** Increases substantially
- **Compensating effect:** At 1:50-1:100 dilution, protects highlights during push

**Push recommendations:**

- **+1 stop:** Rodinal 1:50, increase time by 25% (from 9 min to ~11 min for Fomapan 100)
- **+2 stops:** Rodinal 1:50, increase time by 50% (from 9 min to ~13-14 min)
- **For finer grain when pushing:** Use 1:100 dilution (stand development) - grain increase is less than 1:50

**Pull characteristics:**

- **Grain:** Reduces when pulling (finer grain structure)
- **Acutance:** Remains high
- **Contrast:** Decreases appropriately
- **Compensating effect:** Gentle highlight rolloff even when pulled

**Pull recommendations:**

- **-1 stop:** Rodinal 1:50, reduce time by 20% (from 9 min to ~7 min)
- **For ultra-smooth pulls:** Rodinal 1:100 stand development (45-60 min) at EI 50

**Fine art use:**

- **Push for graphic work:** Rodinal enhances grain and sharpness - ideal for intentional gritty aesthetic
- **Pull for atmospheric work with grain character:** Rodinal 1:100 stand pull produces smooth tonality with pleasant grain texture

**Community consensus:** Rodinal is **excellent for push processing** if you embrace visible grain. Not ideal if you want fine-grain results.

### HC-110 (Versatile, Reliable)

**Push characteristics:**

- **Grain:** Moderate increase when pushing
- **Acutance:** Very good
- **Contrast:** Predictable increase
- **Reliability:** Extremely consistent results

**Push recommendations:**

- **+1 stop:** Dilution B, increase time by 25-30% (from 6.5 min to ~8.5 min for Delta 100)
- **+2 stops:** Dilution B or Dilution A, increase time by 50% (from 6.5 min to ~10 min, Dilution B)
- **For controlled push:** Use Dilution A (more concentrated) - finer control over gamma increase

**Pull characteristics:**

- **Grain:** Fine when pulling
- **Acutance:** Remains very good
- **Contrast:** Smooth reduction
- **Tonality:** Beautiful smooth gradations

**Pull recommendations:**

- **-1 stop:** Dilution E (1:15), standard time (~10-11 min) OR Dilution B, reduce time by 20%
- **For ultra-smooth pulls:** Dilution H (1:31), standard time

**Fine art use:**

- **Push:** HC-110 Dilution B is the **safe, reliable choice** for push processing - predictable results, moderate grain
- **Pull:** HC-110 Dilution E is **excellent for atmospheric work** - smooth tonality, fine grain

**Community consensus:** HC-110 is the **go-to professional developer** for push/pull - consistent, predictable, excellent results across all scenarios.

### XTOL (Fine Grain, Low Contrast)

**Push characteristics:**

- **Grain:** Minimal increase - finest grain when pushing
- **Acutance:** Good (not as sharp as Rodinal)
- **Contrast:** Gentle increase (lower gamma rise than other developers)
- **Challenge:** May not provide enough contrast increase for extreme pushes

**Push recommendations:**

- **+1 stop:** XTOL 1:1, increase time by 25% (from 9 min to ~11-12 min)
- **+2 stops:** XTOL stock, increase time by 40-50% (from 6 min to ~9 min)
- **Not recommended beyond +2 stops:** Contrast increase is insufficient

**Pull characteristics:**

- **Grain:** Ultra-fine - finest grain when pulling
- **Acutance:** Good
- **Contrast:** Smooth, gentle reduction
- **Tonality:** Beautiful smooth gradations - **best developer for atmospheric pulls**

**Pull recommendations:**

- **-1 stop:** XTOL 1:1, reduce time by 20% (from 9 min to ~7 min)
- **-2 stops:** XTOL 1:2, reduce time by 25% (from 13 min to ~10 min)

**Fine art use:**

- **Push:** XTOL produces **finest grain when pushing** - excellent if grain control is priority
- **Pull:** XTOL is the **best choice for atmospheric pulls** - ultra-smooth, grainless, delicate tonality

**Community consensus:** XTOL is **ideal for pull processing** and moderate pushes where fine grain is essential.

### DD-X (Optimized for T-Grain Films)

**Push characteristics:**

- **Grain:** Fine - designed for pushing Delta/T-Max films
- **Acutance:** Excellent
- **Contrast:** Predictable, controlled increase
- **Optimized for:** Ilford Delta films (best results)

**Push recommendations:**

- **+1 stop (Delta 100):** DD-X stock, increase time by 25% (from 7 min to ~8.5-9 min)
- **+2 stops (Delta 100):** DD-X stock, increase time by 50% (from 7 min to ~10.5-11 min)
- **For maximum push performance:** Use Microphen instead of DD-X beyond +2 stops

**Pull characteristics:**

- **Grain:** Very fine
- **Acutance:** Excellent
- **Contrast:** Smooth reduction
- **Best for:** Delta films

**Pull recommendations:**

- **-1 stop:** DD-X 1:4, reduce time by 15-20% (from 11 min to ~9 min)
- **-2 stops:** DD-X 1:4 with Perceptol dilution recommended instead

**Fine art use:**

- **Push Delta 100:** DD-X is the **ideal developer** - designed for this exact purpose
- **Push other films:** HC-110 or Microphen may be better choices
- **Pull Delta 100:** DD-X 1:4 or Perceptol preferred for finest grain

**Community consensus:** DD-X is **specialized for T-grain films** - use with Delta for best results. Less optimal for traditional films.

### D-76 / ID-11 (Classic, General-Purpose)

**Push characteristics:**

- **Grain:** Moderate increase
- **Acutance:** Good
- **Contrast:** Predictable increase
- **Reliability:** Consistent, time-tested

**Push recommendations:**

- **+1 stop:** D-76 stock, increase time by 25-30%
- **+2 stops:** D-76 stock, increase time by 50%
- **For finer grain when pushing:** D-76 1:1 dilution

**Pull characteristics:**

- **Grain:** Fine
- **Acutance:** Good
- **Contrast:** Smooth reduction
- **Tonality:** Classic, neutral rendering

**Pull recommendations:**

- **-1 stop:** D-76 1:1, reduce time by 20%
- **-2 stops:** D-76 1:1 or switch to XTOL/Perceptol for finer grain

**Fine art use:**

- **Push/pull:** D-76 is **reliable and predictable** but offers no specific advantages over HC-110 or XTOL
- **Use if:** Already using D-76 for normal work - no need to switch developers

**Community consensus:** D-76 is **safe and reliable** but not specialized - HC-110, XTOL, or DD-X often produce better push/pull results.

### Pyro Developers (PMK, Pyrocat-HD)

**Push characteristics:**

- **Grain:** Appears finer due to staining effect
- **Acutance:** Excellent - very high edge sharpness
- **Contrast:** Controlled increase (stain modulates development)
- **Stain color:** Yellow-brown stain in highlights increases with push
- **Compensating effect:** Stain protects highlights beautifully

**Push recommendations:**

- **+1 stop:** PMK Pyro 1:2:100, increase time by 20-25% (from 13 min to ~16 min)
- **+2 stops:** PMK Pyro 1:2:100, increase time by 40% (from 13 min to ~18 min)
- **Advantage:** Grain increase is masked by stain - looks finer than non-staining developers

**Pull characteristics:**

- **Grain:** Very fine (stain + reduced development)
- **Acutance:** Excellent
- **Contrast:** Smooth reduction
- **Stain:** Less stain when pulled (lighter yellow tint)

**Pull recommendations:**

- **-1 stop:** PMK Pyro 1:2:100, reduce time by 15% (from 13 min to ~11 min)
- **-2 stops:** Reduce time by 25% (from 13 min to ~9-10 min)

**Fine art use:**

- **Push with Pyro:** Produces **high acutance + controlled grain** - excellent for graphic work
- **Pull with Pyro:** Ultra-smooth tonality with beautiful stain color - ideal for atmospheric fine art
- **Condenser enlarger:** Pyro's stain provides maximum benefit
- **Diffusion enlarger:** Stain less visible (still benefits from high acutance)

**Safety note:** Pyro developers contain **toxic chemicals** (pyrogallol) - use with excellent ventilation and gloves.

**Community consensus:** Pyro developers are **exceptional for push processing** in traditional fine art darkroom work - staining masks grain increase, compensating development protects highlights.

### Developer Summary for Push/Pull

| Developer | Best For Push | Best For Pull | Grain Control | Notes |
|-----------|--------------|---------------|---------------|-------|
| **Rodinal** | +1 to +2 stops | -1 stop (1:100) | Poor (grain increases) | High acutance, visible grain |
| **HC-110** | +1 to +2 stops | -1 to -2 stops | Good | Versatile, reliable |
| **XTOL** | +1 stop (fine grain) | -1 to -2 stops | Excellent | Finest grain when pulling |
| **DD-X** | +1 to +3 stops (Delta) | -1 stop (Delta) | Excellent (T-grain) | Specialized for Delta films |
| **D-76/ID-11** | +1 to +2 stops | -1 stop | Good | Classic, reliable |
| **Pyro (PMK)** | +1 to +2 stops | -1 to -2 stops | Excellent (stain masks) | Staining, compensating, toxic |

---

## 7. Push/Pull for Fine Art and Atmospheric Photography

Push and pull processing are powerful tools for creating specific moods and aesthetics in fine art black & white photography. The key is understanding **when to use each technique** and **how to integrate it with exposure and printing strategy**.

### Low-Contrast Pull Workflow for Atmospheric Photography

**Goal:** Create smooth, gentle, dreamlike tonality for fog, mist, overcast scenes, and melancholic fine art work.

**Complete workflow:**

**1. Exposure strategy:**

- **Rate film at EI 1/2 to 1/4 box ISO** (e.g., ISO 100 film at EI 50 or EI 25)
- **Meter for midtones or shadows:**
  - Spot meter important shadow: place at Zone III-IV
  - OR incident meter + 1/2 to 1 stop overexposure
- **Accept that highlights will be overexposed** (pull development will control them)

**Example:**

- Film: Ilford Delta 100
- Scene: Morning fog in forest
- Exposure: Rate at EI 50 (-1 stop from box ISO)
- Metering: Spot meter fog layer (midtone), place at Zone VI (1 stop overexposure)
- Applied exposure: If meter reads 1/60 @ f/8, shoot at 1/30 @ f/8 (or 1/60 @ f/5.6)

**2. Development strategy:**

- **Developer:** XTOL 1:1 or 1:2 (finest grain, smooth tonality)
- **Pull:** -1 stop (reduce development time by 20%)
- **Time:** XTOL 1:1 normal = 9 min → pulled = 7-8 min at 20°C
- **Agitation:** Gentle (initial 30 sec, then 5 sec every 30 sec) - avoid excessive agitation

**Example:**

- Developer: XTOL 1:1
- Pull: -1 stop
- Time: 7.5 minutes at 20°C
- Agitation: Initial 30 sec, then 5 sec every 30 sec

**3. Printing strategy:**

- **Paper:** Warmtone matte fiber (Foma Fomatone 542, Ilford Warmtone Matte)
- **Grade:** 3-4 (compensates for low negative contrast)
- **Printing approach:**
  - Expose for delicate highlights (don't let fog go pure white)
  - Gentle dodging of deep shadows to prevent blocking
  - **Print lighter than "standard"** - embrace high-key aesthetic

**Example:**

- Paper: Ilford Multigrade FB Warmtone Matte
- Grade: 3.5 (multigrade filtration)
- Exposure: 12 seconds @ f/8 (longer than normal due to low negative density)
- Dodging: Gently dodge forest shadows (2-3 seconds)
- Result: Soft, ethereal print with gentle fog gradations

**Result:**

- **Ultra-smooth tonality** with delicate gradations
- **Fine grain** (near-grainless with Delta 100 + XTOL pull)
- **Gentle, melancholic mood** perfect for atmospheric fine art
- **"Window to another dimension" aesthetic**

**Recommended film combinations:**

1. **Ilford Delta 100 @ EI 50, XTOL 1:2 pull -1 stop** - Finest grain, smoothest tonality
2. **Fomapan 100 @ EI 50, XTOL 1:1 pull -1 stop** - Budget-friendly, beautiful results
3. **Pan F Plus 50 @ EI 25, Perceptol pull -1 stop** - Ultra-fine grain, dreamlike smoothness

### Dramatic Push Workflow for High-Contrast Aesthetic

**Goal:** Create graphic, punchy, high-contrast imagery with visible grain for gritty photojournalistic or intentionally dramatic fine art work.

**Complete workflow:**

**1. Exposure strategy:**

- **Rate film at EI 2× to 4× box ISO** (e.g., ISO 100 film at EI 200 or 400)
- **Expose for midtones:**
  - Accept that shadows will go black (Zone 0-II lost)
  - Meter midtone detail, expose normally for chosen EI
- **High-contrast scenes are ideal** (strong directional light, graphic subjects)

**Example:**

- Film: Fomapan 100
- Scene: Backlit architecture, strong sunlight
- Exposure: Rate at EI 400 (+2 stops push)
- Metering: Spot meter sunlit wall, expose for Zone VI
- Applied exposure: Meter reading is correct for EI 400
- Result: Deep shadows go black, highlights are printable

**2. Development strategy:**

- **Developer:** Rodinal 1:50 (high acutance, sharpness priority) OR HC-110 Dilution B (controlled grain)
- **Push:** +2 stops (increase development time by 40-50%)
- **Time:**
  - Rodinal 1:50: 9 min normal → 13-14 min pushed (+50%)
  - HC-110 Dilution B: 6.5 min normal → 9.5-10 min pushed (+50%)
- **Agitation:** Standard (initial 30 sec, then 5 sec every 30 sec)

**Example:**

- Developer: HC-110 Dilution B
- Push: +2 stops
- Time: 9.5 minutes at 20°C
- Agitation: Standard

**3. Printing strategy:**

- **Paper:** Glossy fiber (Ilford Multigrade FB Classic Glossy) for maximum Dmax
- **Grade:** 1-2 (compensates for high negative contrast)
- **Printing approach:**
  - Expose for deep blacks (let shadows block)
  - Burn highlights if needed to prevent pure white
  - **Print darker than standard** - embrace graphic, punchy tonality

**Example:**

- Paper: Ilford Multigrade FB Classic Glossy
- Grade: 1.5 (multigrade filtration)
- Exposure: 8 seconds @ f/8 (shorter than normal due to high negative density)
- Burning: Burn bright highlights (sky) +2-3 seconds to add tone
- Result: Graphic, high-contrast print with deep blacks and crisp highlights

**Result:**

- **High contrast** with graphic tonality
- **Visible grain** adds texture and "film feel"
- **Deep blacks** (shadows block completely)
- **Punchy, dramatic aesthetic**

**Recommended film combinations:**

1. **Fomapan 100 @ EI 400, HC-110 Dilution B push +2 stops** - Controlled grain, high contrast
2. **Delta 100 @ EI 400, DD-X stock push +2 stops** - Finest grain while pushing
3. **Fomapan 100 @ EI 200, Rodinal 1:50 push +1 stop** - Maximum acutance, moderate grain

### Foggy Landscape Workflow (Pull Processing)

**Specific workflow for fog, mist, and low-contrast atmospheric scenes:**

**Film:** Ilford Delta 100

**Exposure:**

- **Rate at EI 64** (-2/3 stop from box ISO)
- **Meter brightest fog area:** Spot meter, place at Zone VII (1.5 stops overexposure from meter reading)
- **Example:** Meter reading on bright fog = 1/125 @ f/8 → shoot at 1/60 @ f/5.6 (or 1/125 @ f/4)

**Development:**

- **Developer:** XTOL 1:1
- **Pull:** -1 stop (reduce time by 20%)
- **Time:** 7 minutes at 20°C (reduced from 9 minutes)
- **Agitation:** Gentle (initial 30 sec, then 5 sec every 30 sec)

**Printing:**

- **Paper:** Ilford Multigrade FB Warmtone Matte
- **Grade:** 4 (brings out subtle fog gradations)
- **Printing:** Expose for gentle fog tone (not pure white), dodge deep shadow areas slightly

**Result:** Delicate fog gradations with smooth tonality, fine grain invisible, melancholic atmospheric aesthetic.

### Infrared Workflow (Rollei Infrared 400 or Ilford SFX 200)

**Rollei Infrared 400 with 720nm filter:**

**Exposure:**

- **Rate at EI 400** (box speed)
- **Filter:** 720nm infrared filter (+exposure compensation built into metering)
  - **Meter:** Through-the-lens meter will compensate automatically
  - **External meter:** Add +6 to +8 stops for 720nm filter
- **Scene:** Bright sunlight essential (IR requires strong IR light source)

**Development:**

- **Developer:** HC-110 Dilution B (controlled grain) OR Rodinal 1:50 (high acutance)
- **Development:** Normal or slight pull (-10%) to control grain
- **Time:** HC-110 Dilution B = 6.5 min at 20°C

**Printing:**

- **Paper:** Neutral fiber glossy (Ilford Multigrade FB Classic)
- **Grade:** 2-3 (IR negatives are naturally contrasty)
- **Printing:** Embrace "glowing" highlights (foliage), dark skies

**Result:** Classic infrared look - white foliage, dark skies, dreamy "otherworldly" aesthetic.

**Ilford SFX 200 with deep red filter (not true IR):**

**Exposure:**

- **Rate at EI 200** (box speed) or **EI 100** (-1 stop for smoother tonality)
- **Filter:** Deep red (#29) or 720nm filter
  - Deep red: +3 stops compensation
  - 720nm: +6 stops compensation (SFX has limited IR sensitivity compared to Rollei IR)

**Development:**

- **Developer:** XTOL 1:1 (fine grain, smooth IR simulation)
- **Development:** Normal or slight pull (-10%)
- **Time:** XTOL 1:1 = 9 min at 20°C (or 8 min for -10% pull)

**Printing:**

- **Paper:** Warmtone matte fiber (Foma Fomatone 542)
- **Grade:** 3-4 (moderate contrast)
- **Result:** Soft IR simulation with less extreme contrast than true IR film

### Snowy Landscape Workflow (Pan F Plus 50)

**Film:** Ilford Pan F Plus 50

**Exposure:**

- **Rate at EI 32** (-2/3 stop from box ISO)
- **Meter snow:** Spot meter bright snow, place at Zone VII (2 stops overexposure from meter)
- **Example:** Meter reading on snow = 1/250 @ f/11 → shoot at 1/60 @ f/11 (or 1/250 @ f/5.6)

**Development:**

- **Developer:** Perceptol stock (ultra-fine grain)
- **Development:** Normal or slight pull (-10%)
- **Time:** Perceptol stock = 9 min at 20°C (or 8 min for -10% pull)

**Printing:**

- **Paper:** Ilford Multigrade FB Warmtone Pearl (warm tone complements winter light)
- **Grade:** 2-3 (snow scenes need moderate contrast to show texture)
- **Printing:** Snow should print as light gray (Zone VII), not pure white

**Result:** Ultra-fine grain, smooth tonality, snow texture visible without harshness, warm subtle tonality.

---

## 8. Zone System and Push/Pull Processing

The **Zone System** (developed by Ansel Adams and Fred Archer) is a method for controlling exposure and development to achieve desired tonal rendering. Push and pull processing are directly related to Zone System concepts of **expansion** and **contraction**.

### Zone System Basics

**The 11 zones:**

- **Zone 0:** Pure black (no detail) - Dmax
- **Zone I:** Near black (slight tone, no texture)
- **Zone II:** Textured shadow (first visible texture)
- **Zone III:** Average dark shadow (full texture visible)
- **Zone IV:** Dark midtone (dark foliage, shadows with detail)
- **Zone V:** Middle gray (18% gray card, average skin tone)
- **Zone VI:** Light midtone (light skin, average Caucasian skin)
- **Zone VII:** Light gray (textured highlight - snow with texture)
- **Zone VIII:** Bright highlight (texture barely visible)
- **Zone IX:** Near white (no texture)
- **Zone X:** Pure white (paper base) - Dmin

**Each zone = 1 stop of exposure difference**

### Expose for Shadows, Develop for Highlights

**Adams' principle:**

1. **Exposure controls shadow detail:**
   - Place darkest important shadow at Zone III (2 stops below meter reading)
   - This ensures shadow has sufficient density to print with detail
   - **Underexposure loses shadow detail irretrievably**

2. **Development controls highlight placement:**
   - Normal development: Highlights fall where exposure places them
   - Increased development (expansion): Highlights move to higher zones (increased contrast)
   - Decreased development (contraction): Highlights move to lower zones (decreased contrast)

### N, N+1, N-1, N+2, N-2 Development

**N = Normal development:**

- Scene Subject Brightness Range (SBR) matches paper's ability to render
- Typical SBR: 7 stops (Zone III to Zone IX)
- Development produces negatives that print on Grade 2 paper

**N+1 = Expansion (1-zone expansion):**

- Scene SBR is too low (flat, overcast, fog)
- Typical SBR: 5-6 stops
- **Increase development time ~15-20%** to increase contrast
- Highlights move up 1 zone (if highlight was Zone VII, it moves to Zone VIII)
- **Similar to push processing +1 stop**, but motivated by scene contrast, not underexposure

**N-1 = Contraction (1-zone contraction):**

- Scene SBR is too high (bright sun, harsh lighting)
- Typical SBR: 9-10 stops
- **Reduce development time ~15-20%** to decrease contrast
- Highlights move down 1 zone (if highlight was Zone IX, it moves to Zone VIII)
- **Similar to pull processing -1 stop**, but motivated by scene contrast, not overexposure

**N+2 = 2-zone expansion:**

- Extreme low-contrast scenes
- **Increase development time ~40-50%**
- **Similar to push processing +2 stops**

**N-2 = 2-zone contraction:**

- Extreme high-contrast scenes
- **Reduce development time ~30-40%**
- **Similar to pull processing -2 stops**

### Zone System vs. Push/Pull Processing

**Key differences:**

| Aspect | Zone System (N+/N-) | Push/Pull Processing |
|--------|---------------------|----------------------|
| **Motivation** | Scene contrast control | Exposure compensation OR contrast control |
| **Exposure** | Correct exposure for shadows (Zone III) | Deliberate under/overexposure |
| **Development adjustment** | Based on scene SBR | Based on exposure compensation needed |
| **Goal** | Match negative contrast to scene | Achieve printable negative despite non-standard exposure OR control contrast |

**Overlap:**

- **Push +1 stop** ≈ **N+1 expansion** (development time increase is similar)
- **Pull -1 stop** ≈ **N-1 contraction** (development time decrease is similar)

**Practical integration:**

1. **Measure scene SBR:**
   - Meter brightest highlight: place at Zone VIII
   - Meter darkest shadow: ensure it falls at Zone III or higher
   - Calculate SBR: If highlight is Zone VIII and shadow is Zone III, SBR = 5 zones (manageable)

2. **Choose exposure:**
   - Expose to place shadow at Zone III (ensures shadow detail)

3. **Choose development:**
   - If SBR is 5 zones (low contrast): N+1 expansion (increase development)
   - If SBR is 7 zones (normal): N development (normal development)
   - If SBR is 9 zones (high contrast): N-1 contraction (reduce development)

4. **Combine with push/pull if needed:**
   - **Example:** Low-light scene + low contrast
   - **Solution:** Rate at EI 200 (push +1), use N+1 development (total: +2 stops development increase)
   - **Result:** Underexposure compensated + contrast boosted

**Fine art workflow:**

- **Zone System is ideal for sheet film** (4x5, 8x10) where each frame can be developed individually
- **Roll film compromises:** Entire roll must receive same development
  - **Solution:** Shoot entire roll in similar lighting conditions
  - OR: Accept that some frames will be over/under-developed for their specific scene

---

## 9. Darkroom Printing and Push/Pull Negatives

Push and pull processing dramatically affects **printing behavior**. Understanding how to print pushed and pulled negatives is essential for fine art darkroom work.

### Printing Pushed Negatives

**Characteristics of pushed negatives:**

- **High contrast** (high gamma)
- **Dense highlights** (approach or reach Dmax)
- **Thin shadows** (minimal density)
- **Increased grain** (visible on print)
- **High Dmax** (very dense areas)

**Printing challenges:**

1. **Excessive contrast:**
   - Pushed negatives often exceed Grade 2 paper's tonal range
   - Shadows print too black (block)
   - Highlights print too light (insufficient density in negative)

2. **Difficulty achieving full tonal range:**
   - Either shadows block OR highlights go white - hard to hold both

**Printing strategies:**

**1. Use low-contrast printing paper:**

- **Grade 0-1** for +2 stop pushed negatives
- **Grade 1-2** for +1 stop pushed negatives
- **Variable contrast paper:** Use very low filtration (yellow filter, 00 or 0)

**2. Split-grade printing:**

- **Base exposure:** Grade 0-1 (soft filter) for majority of print
  - Establishes gentle overall tonality
  - Prevents shadow blocking
- **Contrast boost:** Grade 3-4 (hard filter) for short exposure
  - Adds "snap" to midtones
  - Prevents muddy appearance
- **Typical ratio:** 80% soft filter exposure + 20% hard filter exposure

**Example:**

- Negative: Fomapan 100 pushed +2 stops
- Paper: Ilford Multigrade FB
- Base exposure: 10 seconds @ f/8, Grade 0 filter
- Contrast exposure: 2 seconds @ f/8, Grade 4 filter
- Total: 12 seconds, split-grade technique
- Result: Controlled highlights, textured shadows, "snappy" midtones

**3. Dodge and burn extensively:**

- **Dodge shadows:** Prevent shadow areas from blocking (going pure black)
  - Hold back light from shadow areas during exposure (2-4 seconds)
- **Burn highlights:** Prevent highlights from going pure white
  - Add extra exposure to highlight areas (2-4 seconds)
- **Challenge:** Pushed negatives have less latitude for dodging/burning

**4. Embrace graphic aesthetic:**

- **Accept blocked shadows** as part of the pushed look
- **Print darker than standard** for punchy, high-contrast aesthetic
- **Use glossy paper** for maximum Dmax (deepest possible blacks)

**Fine art approach for pushed negatives:**

- **Choose subjects that suit high contrast:**
  - Graphic architecture
  - Backlit silhouettes
  - High-contrast street scenes
- **Don't fight the film's character** - embrace the pushed aesthetic

### Printing Pulled Negatives

**Characteristics of pulled negatives:**

- **Low contrast** (low gamma)
- **Low overall density** (thin negatives)
- **Smooth highlight rolloff**
- **Fine grain** (minimal grain visibility)
- **Delicate shadow separation**

**Printing challenges:**

1. **Insufficient contrast:**
   - Pulled negatives on Grade 2 paper look flat, muddy
   - Lack visual "punch"
   - Tonal range is compressed

2. **Longer exposure times:**
   - Low negative density requires more enlarger light
   - Exposure times may be 2-3× longer than normal

**Printing strategies:**

**1. Use high-contrast printing paper:**

- **Grade 3-4** for -1 stop pulled negatives
- **Grade 4-5** for -2 stop pulled negatives
- **Variable contrast paper:** Use hard filtration (magenta filter, 4 or 5)

**Example:**

- Negative: Delta 100 pulled -1 stop
- Paper: Ilford Multigrade FB Warmtone Matte
- Grade: 4
- Exposure: 15 seconds @ f/8 (longer than normal due to low density)
- Result: Full tonal range, smooth gradations

**2. Accept longer exposure times:**

- Pulled negatives require 2-3× longer exposure
- **Use smaller aperture** (f/11 or f/16) if exposure time becomes excessive (>30 seconds)
- **Alternative:** Use brighter enlarger bulb

**3. Minimal dodging and burning:**

- Pulled negatives have **smooth, even tonality**
- Excessive dodging/burning disrupts delicate tonal flow
- **"Print straight"** - let negative's tonality shine

**4. Emphasize smoothness:**

- **Use matte fiber paper** to minimize grain visibility
- **Print lighter than standard** for high-key, ethereal aesthetic
- **Embrace delicate, gentle tonality**

**Fine art approach for pulled negatives:**

- **Perfect for atmospheric subjects:**
  - Fog, mist, overcast scenes
  - Soft, contemplative imagery
  - "Window to another dimension" aesthetic
- **Paper choice is critical:**
  - Warmtone matte fiber (Foma Fomatone 542, Ilford Warmtone Matte)
  - Adds tactile quality and warm tonality
  - Matte surface scatters light - grain is invisible

### Paper Grade Requirements Summary

| Processing | Typical Paper Grade | Notes |
|------------|---------------------|-------|
| **Normal development** | Grade 2-3 | Standard printing |
| **Push +1 stop** | Grade 1-2 | Moderate contrast reduction needed |
| **Push +2 stops** | Grade 0-1 | Significant contrast reduction OR split-grade |
| **Push +3 stops** | Grade 0 + split-grade | Extreme contrast, difficult to print |
| **Pull -1 stop** | Grade 3-4 | Moderate contrast boost needed |
| **Pull -2 stops** | Grade 4-5 | Significant contrast boost needed |

### Enlargement Potential

**Pushed negatives:**

- **Grain visibility limits enlargement size**
  - +1 stop push: Acceptable to 16x20" (120 format)
  - +2 stops push: Limit to 11x14" (120 format) - grain becomes prominent beyond this
  - +3 stops push: Limit to 8x10" - grain is very coarse

**Pulled negatives:**

- **Fine grain allows maximum enlargement**
  - -1 stop pull: Excellent to 20x24" (120 format) - grain nearly invisible
  - -2 stops pull: Excellent to 24x30" - ultra-fine grain

**Medium format advantage:** 120 film pushed +1 stop produces **similar grain to 35mm at normal development** - medium format buffers against grain increase from pushing.

### Fiber Paper vs RC Paper for Push/Pull

**Fiber-based paper (recommended for fine art):**

- **Wider tonal range** - better suited for high-contrast pushed negatives
- **Deeper Dmax** - richer blacks in pushed prints
- **Subtle tonal gradations** - ideal for pulled negatives' smooth tonality
- **Archival quality** - essential for fine art sales
- **Tactile quality** - matte fiber complements atmospheric pulled prints

**RC paper (not recommended for fine art):**

- **Limited tonal range** - struggles with pushed negatives' contrast
- **Lower Dmax** - blacks are less rich
- **Plastic surface** - less premium feel
- **Shorter archival life** - not suitable for gallery/Etsy sales

**Recommendation:** Use **fiber-based papers exclusively** for push/pull fine art printing.

### Warmtone Paper and Pull Processing

**Warmtone papers** (Ilford Warmtone, Foma Fomatone) are **ideal for pulled negatives:**

- **Warm brown-black tonality** complements gentle, atmospheric imagery
- **Smooth gradations** render pulled negatives' delicate tones beautifully
- **Matte surface** minimizes grain (which is already minimal in pulled negatives)

**Recommended pairings:**

- **Pulled Delta 100:** Ilford Multigrade FB Warmtone Matte, Grade 4
- **Pulled Fomapan 100:** Foma Fomatone Classic 542 Matte, Grade 3-4
- **Pulled Pan F Plus:** Ilford Warmtone Pearl (semi-gloss), Grade 4

### Lith Printing and Push/Pull

**Lith printing** (alternative printing process using infectious development for high contrast and grainy, warm tonality) has specific relationships with push/pull negatives:

**Pushed negatives for lith:**

- **Poor candidates** - already too contrasty
- Lith printing adds extreme contrast - combined with pushed negative creates unprintable result

**Pulled negatives for lith:**

- **Excellent candidates** - low-contrast negatives are ideal for lith
- **Workflow:**
  - Rate film at EI 1/2 to 1/4 box ISO (overexpose +1 to +2 stops)
  - Pull development -1 to -2 stops
  - Result: Very low contrast negative
  - Lith print: Extreme contrast added by lith process
  - **Final print:** High contrast with unique lith grain and warm tonality

**Recommendation:** If you plan to lith print, **intentionally pull process negatives** to create low-contrast source material for the lith process.

---

## 10. Practical Push/Pull Workflow Examples

The following are complete, step-by-step workflows for specific fine art scenarios. All development times are verified from Massive Dev Chart or manufacturer datasheets.

### Example 1: Foggy Landscape with Ilford Delta 100

**Goal:** Soft, dreamlike fog imagery with ultra-fine grain and delicate tonality.

**Film:** Ilford Delta 100 (120 format)

**Camera:** Medium format (6x6 or 6x7)

**Exposure:**

1. **Rate film at EI 64** (-2/3 stop from box ISO)
2. **Scene:** Morning fog in forest, diffused light
3. **Metering:**
   - Spot meter brightest fog area: 1/30 @ f/11 (meter reading for EI 64)
   - Place fog at Zone VII (1 stop overexposure): 1/15 @ f/11 (or 1/30 @ f/8)
4. **Applied exposure:** 1/30 @ f/8 (on tripod)
5. **Filter:** Polarizing filter (optional) - removes glare on wet foliage, +1.5 stops
   - With polarizer: 1/8 @ f/8

**Development:**

1. **Developer:** XTOL 1:1 (fine grain, smooth tonality)
2. **Pull:** -1 stop (reduce development time by 20%)
3. **Time:** Normal XTOL 1:1 for Delta 100 = 9 min → pulled = **7 minutes at 20°C**
4. **Process:**
   - Pre-soak: 1 minute in water at 20°C
   - Developer: 7 minutes at 20°C
   - Agitation: Initial 30 seconds, then 5 seconds every 30 seconds
   - Stop bath: 30-60 seconds
   - Fixer: 5-10 minutes (two-bath fixing recommended for archival work)
   - Wash: 30 minutes in running water
   - Wetting agent: 30 seconds in Ilfotol or Photo-Flo
   - Dry: Hang in dust-free area

**Printing:**

1. **Paper:** Ilford Multigrade FB Warmtone Matte
2. **Grade:** 4 (compensates for low negative contrast)
3. **Enlargement:** 16x20" from 6x7 negative
4. **Test strip:**
   - Initial test: 8, 10, 12, 14, 16 seconds @ f/8, Grade 4
   - Select optimal: 12 seconds
5. **Dodging/burning:**
   - Dodge deep forest shadows: 2 seconds (prevents blocking)
   - No burning needed (highlights are gentle due to pull processing)
6. **Toning:** Optional selenium toning (1:20 dilution, 3 minutes) for slight warmth and archival stability

**Expected result:**

- **Tonality:** Ultra-smooth, delicate fog gradations
- **Grain:** Virtually invisible even at 16x20"
- **Mood:** Dreamlike, ethereal, "window to another dimension" aesthetic
- **Print character:** Warm brown-black tones, matte surface, soft tactile quality

---

### Example 2: High-Contrast Street Scene with Fomapan 100

**Goal:** Graphic, punchy street photography with visible grain and deep blacks.

**Film:** Fomapan 100 (35mm)

**Camera:** 35mm SLR

**Exposure:**

1. **Rate film at EI 400** (+2 stops push)
2. **Scene:** Backlit urban scene, late afternoon sun
3. **Metering:**
   - Spot meter sunlit building: 1/500 @ f/8 (meter reading for EI 400)
   - Expose as metered: 1/500 @ f/8 (handheld)
4. **Accept:** Shadows will go completely black (silhouette)

**Development:**

1. **Developer:** HC-110 Dilution B (controlled grain, reliable)
2. **Push:** +2 stops (increase development time by 50%)
3. **Time:** Normal HC-110 Dilution B for Fomapan 100 = 6.5 min → pushed = **9.5 minutes at 20°C**
4. **Process:**
   - No pre-soak (HC-110 works best without pre-soak)
   - Developer: 9.5 minutes at 20°C
   - Agitation: Initial 30 seconds, then 5 seconds every 30 seconds
   - Stop bath: 30-60 seconds
   - Fixer: 5-10 minutes
   - Wash: 20 minutes
   - Wetting agent: 30 seconds
   - Dry: Hang

**Printing:**

1. **Paper:** Ilford Multigrade FB Classic Glossy
2. **Grade:** 1.5 (compensates for high negative contrast)
3. **Enlargement:** 11x14" from 35mm negative
4. **Test strip:**
   - Initial test: 6, 8, 10, 12 seconds @ f/8, Grade 1.5
   - Select optimal: 8 seconds
5. **Dodging/burning:**
   - Burn bright sky area: +3 seconds (prevents pure white)
   - Accept blocked shadows (silhouettes are intentional)
6. **Split-grade option:**
   - Base exposure: 10 seconds @ f/8, Grade 0 (soft, prevents shadow blocking)
   - Contrast exposure: 2 seconds @ f/8, Grade 4 (adds snap to highlights)
   - Total: 12 seconds equivalent, split-grade technique

**Expected result:**

- **Tonality:** High contrast, graphic, punchy
- **Grain:** Visible (adds texture and grit)
- **Mood:** Urban, photojournalistic, dramatic
- **Print character:** Deep blacks on glossy paper, crisp highlights, "film noir" aesthetic

---

### Example 3: Infrared Landscape with Rollei Infrared 400

**Goal:** Classic infrared aesthetic - white foliage, dark sky, dreamy atmosphere.

**Film:** Rollei Infrared 400 (120 format)

**Camera:** Medium format (6x6)

**Exposure:**

1. **Rate film at EI 400** (box speed)
2. **Scene:** Summer landscape, bright sun, green foliage
3. **Filter:** 720nm infrared filter (Hoya R72 or equivalent)
   - **Through-the-lens metering:** Meter with filter on lens (camera compensates automatically)
   - **External meter:** Add +6 to +8 stops for 720nm filter
4. **Metering:**
   - TTL spot meter sky: 1/60 @ f/8 (with 720nm filter on lens)
   - Expose as metered: 1/60 @ f/8 (on tripod - IR focusing shift requires small aperture for depth)
5. **Focus adjustment:** Focus, then rotate focus ring slightly toward infinity (IR focus shift) OR use f/16 for deep DOF

**Development:**

1. **Developer:** HC-110 Dilution B (controlled grain)
2. **Development:** Normal or slight pull (-10% to reduce grain)
3. **Time:** Normal HC-110 Dilution B for Rollei IR = 6.5 min → **6 minutes at 20°C** (slight pull)
4. **Process:**
   - Developer: 6 minutes at 20°C
   - Agitation: Gentle (initial 30 sec, then 5 sec every 30 sec)
   - Stop bath: 30-60 seconds
   - Fixer: 5-10 minutes
   - Wash: 30 minutes
   - Wetting agent: 30 seconds
   - Dry: Hang

**Printing:**

1. **Paper:** Ilford Multigrade FB Classic Glossy or Matte
2. **Grade:** 2-3 (IR negatives are naturally contrasty)
3. **Enlargement:** 16x20" from 6x6 negative
4. **Test strip:**
   - Initial test: 8, 10, 12, 14 seconds @ f/8, Grade 2.5
   - Select optimal: 10 seconds
5. **Dodging/burning:**
   - Burn sky: +2 seconds (darkens sky for classic IR look)
   - Dodge foliage highlights: 1 second (prevents pure white, retains "glow")
6. **Toning:** Optional selenium or sepia toning for warmth

**Expected result:**

- **Tonality:** Classic IR - white/glowing foliage, dark sky
- **Grain:** Moderate (characteristic of IR film)
- **Mood:** Dreamlike, surreal, "otherworldly"
- **Print character:** Glowing highlights, deep blacks, distinctive IR aesthetic

---

### Example 4: Winter Snow Scene with Pan F Plus 50

**Goal:** Ultra-fine grain, smooth tonality, delicate snow texture.

**Film:** Ilford Pan F Plus 50 (120 format)

**Camera:** Medium format (6x7)

**Exposure:**

1. **Rate film at EI 32** (-2/3 stop from box ISO)
2. **Scene:** Bright snow field, overcast sky
3. **Metering:**
   - Incident meter: 1/125 @ f/11 (for EI 32)
   - OR spot meter snow: 1/500 @ f/11 (meter reading)
   - Place snow at Zone VII: 1/125 @ f/11 (+2 stops overexposure from spot reading)
4. **Applied exposure:** 1/125 @ f/11 (on tripod for maximum sharpness)

**Development:**

1. **Developer:** Perceptol stock (ultra-fine grain)
2. **Development:** Normal or slight pull (-10%)
3. **Time:** Normal Perceptol for Pan F Plus = 9 min → **8 minutes at 20°C** (slight pull)
4. **Process:**
   - Pre-soak: 1 minute at 20°C
   - Developer: 8 minutes at 20°C
   - Agitation: Gentle (initial 30 sec, then 5 sec every 30 sec)
   - Stop bath: 30-60 seconds
   - Fixer: 5-10 minutes (two-bath fixing for archival)
   - Wash: 30 minutes
   - Wetting agent: 30 seconds
   - Dry: Hang

**Printing:**

1. **Paper:** Ilford Multigrade FB Warmtone Pearl (warm tone complements winter light)
2. **Grade:** 3 (moderate contrast boost for snow texture)
3. **Enlargement:** 20x24" from 6x7 negative (grain is invisible at this size)
4. **Test strip:**
   - Initial test: 10, 12, 14, 16, 18 seconds @ f/8, Grade 3
   - Select optimal: 14 seconds
5. **Dodging/burning:**
   - Dodge deep shadows (tree branches, footprints): 2 seconds
   - Print snow as light gray (Zone VII) - NOT pure white
   - Burn sky if needed: +1-2 seconds for subtle tone
6. **Toning:** Optional selenium toning (1:20, 3 minutes) for slight warmth and archival stability

**Expected result:**

- **Tonality:** Ultra-smooth, delicate snow gradations
- **Grain:** Completely invisible even at 20x24"
- **Mood:** Quiet, contemplative, minimalist winter aesthetic
- **Print character:** Warm pearl surface, subtle sheen, tactile quality

---

## Conclusion

Push and pull processing are **powerful tools** for fine art black & white photography when used with understanding and intention:

**Key principles:**

1. **Understand the physics:** Push/pull modifies development, not film sensitivity
2. **Exposure Index is personal:** Find your EI through testing
3. **Push = contrast increase + grain increase:** Use for low-light or graphic aesthetic
4. **Pull = contrast decrease + fine grain:** Use for atmospheric, smooth tonality
5. **Film choice matters:** Modern T-grain films push better, traditional films often pull better
6. **Developer choice matters:** XTOL for fine grain, Rodinal for acutance, HC-110 for reliability
7. **Printing adjusts for processing:** Pushed negatives need low-contrast printing, pulled negatives need high-contrast printing
8. **Zone System integration:** N+/N- development aligns with push/pull concepts

**For fine art atmospheric photography:**

- **Pull processing (-1 stop)** with XTOL or Perceptol produces **ultra-smooth, grainless negatives** perfect for melancholic, dreamlike imagery
- **Print on warmtone matte fiber paper** at Grade 3-4 for beautiful, tactile fine art prints
- **Embrace gentle, low-contrast aesthetic** - "window to another dimension" mood

**For graphic, dramatic work:**

- **Push processing (+1 to +2 stops)** with HC-110 or Rodinal produces **high-contrast, grainy negatives** perfect for photojournalistic or intentionally gritty aesthetic
- **Print on glossy fiber paper** at Grade 0-2 for deep blacks and crisp highlights
- **Embrace visible grain and blocked shadows** - graphic, punchy aesthetic

**Final recommendation:** **Test your workflow** - shoot test rolls at different EI settings, develop with various push/pull strategies, print results, and determine what produces the aesthetic you desire. Push and pull processing are **creative tools**, not emergency measures - use them intentionally to craft your vision.

---

**Document version:** 1.0  
**Last updated:** June 2026  
**Information sources:** Ilford datasheets, Kodak technical publications, Massive Dev Chart, Ansel Adams *The Negative*, Photrio/APUG community archives  
**License:** Free to use with attribution
