# Photography Filters - Exposure Reference Guide

**Purpose:** Quick reference for photographic filter light loss and exposure compensation  
**Film Formats:** 35mm and 120 Medium Format  
**Updated:** 2026-05-23

---

## What is a Filter and Why Use It?

**Filter:** Optical glass or resin element mounted in front of the lens that modifies the light passing through.

**Three main categories:**
1. **Protective filters** - Optical protection (UV, Skylight)
2. **Creative filters** - Visual modification (Polarizer, Graduated ND)
3. **Technical filters** - Exposure/color control (ND, Color filters)

**Light Loss (Stop Loss):** Every filter blocks a portion of light. Exposure must be compensated accordingly.

---

## Stop vs Filter Factor vs Density

**Three measurement systems for the same phenomenon:**

| Stop Loss | Filter Factor | ND Density | Light Transmission |
|-----------|---------------|------------|-------------------|
| 1 stop | 2× | 0.3 | 50% |
| 2 stop | 4× | 0.6 | 25% |
| 3 stop | 8× | 0.9 | 12.5% |
| 4 stop | 16× | 1.2 | 6.25% |
| 5 stop | 32× | 1.5 | 3.125% |
| 6 stop | 64× | 1.8 | 1.56% |
| 10 stop | 1024× | 3.0 | 0.098% |

**Calculation formulas:**
```
Stop loss = log₂(Filter factor)
Filter factor = 2^(Stop loss)
ND density = Stop loss × 0.3
```

---

## ND (Neutral Density) Filters

**Purpose:** Reduce light without color shift, enabling longer shutter speeds or wider apertures.

### Standard ND Filter Table

| Filter Name | ND Value | Density | Stop Loss | Filter Factor | Light Transmission | ISO Compensation Example |
|------------|----------|---------|-----------|---------------|-------------------|-------------------------|
| **ND2** | 0.3 | ND 0.3 | 1 stop | 2× | 50% | ISO 100 - meter at ISO 200 |
| **ND4** | 0.6 | ND 0.6 | 2 stop | 4× | 25% | ISO 100 - meter at ISO 400 |
| **ND8** | 0.9 | ND 0.9 | 3 stop | 8× | 12.5% | ISO 100 - meter at ISO 800 |
| **ND16** | 1.2 | ND 1.2 | 4 stop | 16× | 6.25% | ISO 100 - meter at ISO 1600 |
| **ND32** | 1.5 | ND 1.5 | 5 stop | 32× | 3.125% | ISO 100 - meter at ISO 3200 |
| **ND64** | 1.8 | ND 1.8 | 6 stop | 64× | 1.56% | ISO 100 - meter at ISO 6400 |
| **ND100** | 2.0 | ND 2.0 | 6.67 stop | 100× | 1% | ISO 100 - meter at ISO 6400 |
| **ND400** | 2.6 | ND 2.6 | 8.67 stop | 400× | 0.25% | ISO 100 - meter at ISO 25600 |
| **ND1000** | 3.0 | ND 3.0 | 10 stop | 1024× | 0.098% | ISO 100 - meter at ISO 102400 |

### Practical Applications

**ND8 (3 stop) - Landscape, waterfall smoothing:**
- In sunlight: 1/500s becomes 1/60s
- Creates "silk" effect on moving water in daylight

**ND64 (6 stop) - Midday long exposure:**
- In sunlight: 1/250s becomes 1/4s
- Removes people from crowded locations

**ND1000 (10 stop) - Extreme long exposure:**
- In sunlight: 1/125s becomes 8 seconds
- Cloud streaking, mirror-smooth water

---

## Polarizing Filter (CPL - Circular Polarizer)

**Purpose:**
- Reduce reflections (water, glass surfaces)
- Deepen blue sky
- Increase contrast

**Stop loss:** 1-2 stop (average 1.5 stop)  
**Filter factor:** ~3×  
**ISO compensation:** ISO 100 - meter at ISO 400

**Usage:**
- Rotatable mount, maximum effect at 90° to light source
- May cause uneven sky with wide-angle lenses (below 24mm)

**Note:** Not compatible with autofocus cameras using linear polarizer (old type). Always use Circular Polarizer (CPL).

---

## Color Filters for Black & White Film

**Purpose:** Modify contrast by altering tonal separation between colors.

**Basic principle:** Filter lightens its own color, darkens its complement.

### Color Filter Table (B&W Film)

| Filter | Color | Stop Loss | Filter Factor | ISO Compensation | Effect |
|--------|-------|-----------|---------------|-----------------|--------|
| **Yellow (Y/K2)** | Yellow | 1 stop | 2× | ISO 100 - ISO 200 | Sky slightly darker, clouds enhanced |
| **Orange (O/G)** | Orange | 1.5-2 stop | 3-4× | ISO 100 - ISO 400 | Sky darker, clouds more dramatic, smooth skin tones |
| **Red (R/25)** | Red | 2-3 stop | 8× | ISO 100 - ISO 800 | Black sky, dramatic contrast, darkens green |
| **Green (X1/G)** | Green | 1-2 stop | 4× | ISO 100 - ISO 400 | Natural skin tones, enhances foliage |
| **Blue (C5)** | Blue | 2 stop | 4× | ISO 100 - ISO 400 | Enhances haze, emphasizes distance (rarely used) |

### Practical Examples (B&W)

**Landscape with sharp clouds:**
- **Orange filter (O)** - 2 stop loss
- Base exposure: 1/125s, f/11, ISO 100
- Compensated: 1/30s, f/11, ISO 100 OR 1/125s, f/11, meter at ISO 400

**Portrait with soft skin tones:**
- **Green filter (X1)** - 1.5 stop loss
- Base exposure: 1/250s, f/5.6, ISO 100
- Compensated: 1/125s, f/5.6, ISO 100 OR 1/250s, f/5.6, meter at ISO 320

---

## Graduated ND Filters

**Purpose:** Darken sky without affecting foreground (landscapes).

**Types:**
- **Soft-edge (soft transition)** - Natural horizons, hills
- **Hard-edge (sharp transition)** - Straight horizons (ocean, plains)
- **Reverse (reversed)** - Sunrise/sunset (horizon brighter)

**Gradations:**
- **0.3 (1 stop)** - Slight contrast difference
- **0.6 (2 stop)** - Medium contrast difference
- **0.9 (3 stop)** - Strong contrast difference (sunset)

**Stop loss:** Variable (0 stop on lower half, 1-3 stop on upper half)  
**ISO compensation:** Meter for foreground, sky compensates automatically

**Note:** In field, use spot metering separately for foreground and sky. Choose grad ND based on the difference between the two values.

---

## Protective Filters (UV, Skylight)

**Purpose:** Protect lens from dust, water, fingerprints.

| Filter | Stop Loss | Filter Factor | Effect |
|--------|-----------|---------------|--------|
| **UV (Ultraviolet)** | 0 stop | 1× | UV light filtration, reduce haze in mountain scenes |
| **Skylight (1A/1B)** | 0 stop | 1× | Slight warming, UV filter + blue tone reduction |

**ISO compensation:** None (0 stop loss)

**Debate:** Many photographers don't use protective filters because:
- Modern lenses have built-in UV protection
- Extra glass layer can reduce sharpness and increase flare risk

**Use when:**
- Working in extreme environments (coastal wind, sandstorm, rain)
- Using valuable vintage lenses

---

## Warming and Cooling Filters (81/82 Series)

**Purpose:** Modify color temperature (warming/cooling).

**Warming (81 series - orange/amber):**

| Filter | Stop Loss | Effect |
|--------|-----------|--------|
| **81A** | 0.3 stop | Slight warming (shaded sunlight) |
| **81B** | 0.3 stop | Medium warming |
| **81C** | 0.3 stop | Strong warming (cloudy day) |

**Cooling (82 series - blue):**

| Filter | Stop Loss | Effect |
|--------|-----------|--------|
| **82A** | 0.3 stop | Slight cooling (tungsten correction) |
| **82B** | 0.5 stop | Medium cooling |
| **82C** | 0.5 stop | Strong cooling (reduce sunset warmth) |

**ISO compensation (average):** ISO 100 - meter at ISO 125-160 (minimal)

**Note:** In digital photography, white balance adjustment replaces this, but useful for color temperature shift in film.

---

## Combined Filters (Stacking)

**When using multiple filters together, stop losses add.**

### Example: CPL + ND8

| Filter | Stop Loss |
|--------|-----------|
| CPL | 1.5 stop |
| ND8 | 3 stop |
| **Total** | **4.5 stop** |

**Original exposure:** 1/500s, f/8, ISO 100  
**Compensated exposure:** 1/30s, f/8, ISO 100  
**OR ISO method:** 1/500s, f/8, meter at ISO 1600-2000

**Caution:**
- More than two filters can cause vignetting (dark corners)
- Wide-angle lenses (below 28mm) should avoid stacking

---

## Exposure Compensation Methods

**Three methods for filter compensation:**

### 1. Shutter Speed Modification (Most Common for Film)

**Example: ND8 (3 stop) filter**
- Base exposure: 1/125s, f/11, ISO 100
- Compensated: 1/15s, f/11, ISO 100 (3 stop slower shutter)

**Shutter speed conversion (stop table):**
```
1 stop slower: 1/125s - 1/60s
2 stop slower: 1/125s - 1/30s
3 stop slower: 1/125s - 1/15s
4 stop slower: 1/125s - 1/8s
```

### 2. Aperture Modification

**Example: CPL (1.5 stop) filter**
- Base exposure: 1/250s, f/11, ISO 100
- Compensated: 1/250s, f/6.3, ISO 100 (1.5 stop wider aperture)

**Aperture conversion (stop table):**
```
1 stop wider: f/11 - f/8
2 stop wider: f/11 - f/5.6
3 stop wider: f/11 - f/4
```

### 3. ISO Modification on Meter (Recommended in Field)

**Example: Red filter (3 stop) for B&W film**
- Film ISO: 100
- Light meter setting: ISO 800 (3 stop higher)
- Value shown by meter is already compensated!

**ISO compensation table:**

| Stop Loss | Film ISO 100 | Film ISO 400 | Film ISO 3200 |
|-----------|--------------|--------------|---------------|
| 1 stop | Meter at ISO 200 | Meter at ISO 800 | Meter at ISO 6400 |
| 2 stop | Meter at ISO 400 | Meter at ISO 1600 | Meter at ISO 12800 |
| 3 stop | Meter at ISO 800 | Meter at ISO 3200 | Meter at ISO 25600 |
| 4 stop | Meter at ISO 1600 | Meter at ISO 6400 | - |
| 5 stop | Meter at ISO 3200 | Meter at ISO 12800 | - |
| 6 stop | Meter at ISO 6400 | Meter at ISO 25600 | - |

**Advantage:** No mental calculation, meter automatically gives correct value.  
**Disadvantage:** Don't forget to reset to actual ISO after removing filter!

---

## Practical Calculation Examples

### Example 1: Waterfall Smoothing in Daylight (ND8)

**Goal:** Silk effect in sunlight

**Given:**
- Exposure without filter: 1/500s, f/11, ISO 100
- Filter: ND8 (3 stop)

**Compensation (shutter speed method):**
```
1/500s + 3 stop = 1/60s
Final: 1/60s, f/11, ISO 100
```

**Compensation (ISO method):**
```
Film: ISO 100
Light meter: ISO 800
Meter shows: 1/60s, f/11
```

### Example 2: Landscape with Sharp Sky (CPL + Orange B&W)

**Given:**
- Exposure without filter: 1/250s, f/16, ISO 100
- Filters: CPL (1.5 stop) + Orange (2 stop) = 3.5 stop total

**Compensation (shutter speed method):**
```
1/250s + 3.5 stop ≈ 1/30s (rounded)
Final: 1/30s, f/16, ISO 100
```

**Compensation (ISO method):**
```
Film: ISO 100
Light meter: ISO 1000 (10× factor ≈ 3.3 stop, close to 3.5)
Meter shows: ~1/30s, f/16
```

### Example 3: Midday Long Exposure (ND1000)

**Goal:** 10 second exposure in sunlight

**Given:**
- Exposure without filter: 1/100s, f/16, ISO 100
- Filter: ND1000 (10 stop)

**Compensation (shutter speed method):**
```
1/100s + 10 stop = 10s (2^10 = 1024× slower)
1/100 × 1024 ≈ 10.24s
Final: 10s, f/16, ISO 100
```

**Compensation (ISO method):**
```
Film: ISO 100
Light meter: ISO 102400 (theoretical, often not available)
Alternative: Meter without filter, calculate manually
```

---

## Quick Reference Card (Memorize)

### ND Filters (Common in Field)

| Filter | Stop | Shutter Speed Conversion (from 1/125s) | ISO Compensation (ISO 100) |
|--------|------|----------------------------------------|---------------------------|
| ND8 | 3 | 1/15s | ISO 800 |
| ND64 | 6 | 1/2s | ISO 6400 |
| ND1000 | 10 | 8s | ISO 102400 (calculate!) |

### Color Filters for B&W Film

| Filter | Stop | ISO Compensation (ISO 100) | Use |
|--------|------|---------------------------|-----|
| Yellow | 1 | ISO 200 | Cloud enhancement |
| Orange | 2 | ISO 400 | Dramatic sky |
| Red | 3 | ISO 800 | Extreme contrast |

### Polarizer

| Filter | Stop | ISO Compensation (ISO 100) |
|--------|------|---------------------------|
| CPL | 1.5 | ISO 400 |

---

## Field Calculation Method

### Steps for Filter Compensation

**1. Meter without filter:**
- Set actual film ISO (e.g., ISO 100)
- Meter accurately (incident or spot metering)
- Record: e.g., 1/250s, f/11

**2. Determine filter stop loss:**
- ND8 = 3 stop
- CPL = 1.5 stop
- Combined = Add (e.g., ND8 + CPL = 4.5 stop)

**3. Compensate (choose one method):**

**A. Shutter speed method (Recommended for film):**
```
Each stop = 2× slower shutter speed
3 stop = 2 × 2 × 2 = 8× slower
1/250s - 1/30s (3 stop)
```

**B. ISO method (Faster in field):**
```
Set meter to higher ISO
3 stop = ISO 100 - ISO 800
Meter again, value is automatically compensated
```

---

## Field Notes Section

**Print and laminate for field use.**

### Your Filter Kit

Fill in your own filter data:

| Filter Type | Size | Stop Loss | Filter Factor | ISO Compensation (ISO 100 film) | Notes |
|------------|------|-----------|---------------|--------------------------------|-------|
| _______ | ___mm | ___ stop | ___× | ISO ___ | ________________ |
| _______ | ___mm | ___ stop | ___× | ISO ___ | ________________ |
| _______ | ___mm | ___ stop | ___× | ISO ___ | ________________ |
| _______ | ___mm | ___ stop | ___× | ISO ___ | ________________ |

### Quick Calculation Worksheet

**Given:**
- Exposure without filter: 1/___s, f/___, ISO ___
- Filter: _______ (___ stop loss)

**Compensated exposure (shutter speed method):**
```
Shutter: 1/___s + ___ stop = 1/___s
Final: 1/___s, f/___, ISO ___
```

**Compensated exposure (ISO method):**
```
Actual film ISO: ___
Light meter setting: ISO ___
Metered value: 1/___s, f/___
```

---

## Common Mistakes and Solutions

### Mistake 1: Underexposed Image After Filter Use

**Cause:** Forgot to compensate for filter stop loss.  
**Solution:** Always meter again with filter, OR use ISO compensation method.

### Mistake 2: Blurry Image After ND Filter

**Cause:** Camera movement during long shutter speed.  
**Solution:** Use tripod and cable release for shutter speeds below 1/30s.

### Mistake 3: Vignetting (Dark Corners)

**Cause:** Too many stacked filters.  
**Solution:** Use maximum 2 filters together, or thin-mount (slim) filters.

### Mistake 4: Color Shift with ND Filter

**Cause:** Cheap ND filters are not truly neutral.  
**Solution:** Use quality filters (B+W, Hoya Pro1D, Lee), or correct with color temperature.

### Mistake 5: Forgotten Filter ISO Reset

**Cause:** After filter removal, meter still at compensated ISO.  
**Solution:** Make field notes about filter changes, or use reminder marker (e.g., colored rubber band on meter).

---

## Online Calculators (For Verification)

**ND Filter Calculators:**
- NDCalc: https://www.ndcalc.com (ND exposure calculator)
- PhotoPills: https://www.photopills.com/calculators/exposure (complex exposure calculations)

**Best practice:** Calculate critical exposures in advance in field, record in this guide.

---

## Sources and Further Reading

**Books:**
- "The Negative" - Ansel Adams (1981) - Zone System and filters in B&W
- "Light and Film" - Time-Life Photography Series (1970) - Filter use basics
- "Long Exposure Photography" - Glenn Homann (2016) - ND filter techniques

**Manufacturer Specifications:**
- B+W Filters - Schneider Kreuznach catalog
- Lee Filters - Graduated ND Guide
- Hoya Filters - Technical Specifications

---

**Compiled:** 2026-05-23  
**Formats:** Universal (35mm, 120, 4×5 film)  
**For field use:** Print and laminate
