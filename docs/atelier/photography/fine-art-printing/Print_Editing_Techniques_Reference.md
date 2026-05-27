---
description: Print-specific editing workflows - two-pass strategy, dodge and burn techniques, local adjustments, and creating dimensional three-dimensional fine art prints.
---

# Print Editing Techniques Reference

## Overview

This reference covers print-specific editing workflows: adjusting images for reflective media, two-pass editing strategy, and dodge & burn techniques for creating dimensional, three-dimensional prints.

**Core Principle:** Editing for screen and editing for print require different approaches. Screen-optimized images often fail on paper.

---

## Why Screen Edits Fail on Paper

### The Screen Advantage

**Screens Are Forgiving:**
- Backlit (self-illuminating) → brightness compensates for weak tones
- High contrast tolerance → harsh edits look acceptable
- Instant eye adaptation → pupils adjust to screen brightness
- Close viewing (12-24") → detail visible even in compressed tones

### The Print Reality

**Prints Are Revealing:**
- Reflective (ambient light dependent) → weak tones disappear
- Lower contrast tolerance → harsh edits feel aggressive
- Fixed luminance → can't adapt, static appearance
- Distance viewing (3-10 feet) → compressed tones go solid

**Specific Failures:**

| Screen Edit | Print Result | Why |
|-------------|--------------|-----|
| **Punchy contrast (+30)** | Harsh, aggressive | Reflective media amplifies contrast feel |
| **Deep blacks (0,0,0)** | Blocked shadows, no detail | Paper has limited shadow range |
| **Bright highlights (100%)** | Blown white, lifeless | No backlight to create "glow" |
| **High saturation** | Overly vibrant or clipped | Paper gamut narrower than screen |
| **Cool white balance** | Looks even cooler on paper | Paper base color shifts perception |

**Rule:** If an edit looks "perfect" on screen, it's likely too extreme for print.

---

## Print-First Adjustments

### The Print Compensation Strategy

**Key Adjustments for Reflective Media:**

**1. Lift Midtones Slightly (+5-10% exposure in middle grays)**
- Why: Prints feel darker than screens
- How: Curves midpoint up, or Exposure +0.15 to +0.30

**2. Soften Highlights (avoid hard clip at 100% white)**
- Why: Backlight-free paper needs gentle rolloff
- How: Compress highlights in curves (see Tonal Control Reference)

**3. Open Shadows (more separation than screen requires)**
- Why: Shadow detail less visible on reflective media
- How: Lift shadows +10 to +20, preserve blacks anchor

**4. Reduce Global Contrast (10-15% less than screen optimal)**
- Why: Screen tolerates high contrast, paper feels harsh
- How: Contrast slider -10 to -15, or flatten curve slightly

**5. Add Warmth (+2-5 Kelvin)**
- Why: Prints feel cooler than screen (no backlight warmth)
- How: Temperature +200 to +500K

**6. Reduce Microcontrast (Clarity -5 to +10 instead of +20+)**
- Why: Analog feel, avoid harsh digital look
- How: Clarity slider 0 to +10 (not screen-optimal +20 to +40)

---

## Two-Pass Editing Strategy

### Concept

**Pass 1:** Edit for screen (normal workflow, satisfying on monitor) 
**Pass 2:** Adjust for print (virtual copy/snapshot, print-specific tweaks)

**Why Two Passes?**
- Separates concerns: screen vs. print requirements
- Preserves screen-optimized version (for web, social media)
- Builds print-specific expertise without destroying original edit
- Teaches your eye to pre-visualize print needs

### Two-Pass Process

**Pass 1: Screen-Optimized Edit**

1. Edit image as you normally would (satisfying on monitor)
2. Typical screen edit:
- Punchy contrast (+20 to +30)
- Vibrant colors (Vibrance +15 to +25, Saturation +5 to +10)
- Microcontrast high (Clarity +20 to +40)
- Deep blacks (Blacks -20 to -40)
- Bright highlights (Whites +10 to +20)
3. Export for web/screen use

**Pass 2: Print-Optimized Edit**

1. Create virtual copy (Lightroom) or snapshot (Photoshop)
2. Apply print adjustments:
- **Lift midtones:** Curves midpoint up 5-10%
- **Soften highlights:** Compress highlights in curves
- **Open shadows:** Lift shadow endpoint 3-5%
- **Reduce contrast:** Contrast slider -10 or flatten curve slightly
- **Add warmth:** Temperature +200-300K
- **Lower Clarity:** Reduce Clarity by -10 to -20 from Pass 1 value
- **Adjust Saturation:** Vibrance +5 (compensate for print saturation loss)
3. Soft-proof (verify paper gamut)
4. Print test

### Two-Pass Comparison Exercise

**Goal:** Train your eye to see print-specific needs.

**Process:**

1. Select 5 images you consider "finished" for screen

2. For each image:
- **Pass 1:** Export as-is (screen-optimized)
- **Pass 2:** Create virtual copy, apply print adjustments (see above)

3. Print both versions (8x10")

4. Compare side-by-side in natural light:
- Which version looks better on paper?
- Is Pass 1 too harsh/contrasty?
- Is Pass 2 more natural/organic?
- Document findings in notebook

**Expected Result:** Pass 2 (print-optimized) feels more natural, dimensional, and cohesive on paper.

---

## Building a Print Preset

### Goal

Create a reusable starting point for print adjustments—apply as base, then fine-tune per image.

### Preset Creation (Lightroom)

**Process:**

1. Start with neutral image (not already edited)

2. Apply print-friendly adjustments:
- **Tone Curve:**
- Slight S-curve with lifted blacks (black point to 3-5%)
- Gentle highlight compression (see Tonal Control Reference)
- **Basic Panel:**
- Exposure: +0.15 to +0.30 (compensate for print darkness)
- Contrast: -10 to -15 (reduce harshness)
- Highlights: -5 to -10 (prevent clipping)
- Shadows: +10 to +15 (open shadow detail)
- Whites: -5 (preserve highlight rolloff)
- Blacks: +5 (prevent blocked shadows)
- Clarity: 0 to +10 (organic, not harsh)
- Vibrance: +5 (compensate for print saturation loss)
- Saturation: 0 (or slight reduction -5)
- **Color:**
- Temperature: +2 to +5 (add warmth, print pulls cool)
- Tint: 0 (adjust per image if needed)

3. Test on 10 diverse images

4. Print all 10 (8x10")

5. Refine preset based on results:
- Too bright? Reduce Exposure
- Too flat? Increase Contrast slightly
- Too warm? Reduce Temperature

6. Save final version as **"My Print Preset"**

### Using the Preset

**Application:**
1. Load image in Lightroom
2. Apply "My Print Preset"
3. Fine-tune per image (subject-specific adjustments)
4. Soft-proof
5. Print test

**Benefit:** Consistent starting point, less guesswork, faster print-ready edits.

---

## Iterative Print Refinement

### The Iteration Mindset

**Reality:** First print is almost never perfect. 
**Goal:** Refine through 2-4 iterations, learning print behavior.

### Iteration Process

**Iteration 1 (Baseline Print):**

1. Apply screen-optimized edit OR print preset
2. Print (8x10")
3. View in target lighting (wait 10-15 min for inks to settle)
4. Take detailed notes:
- **Brightness:** Too dark? Too bright? (rate 1-10, 5 = perfect)
- **Highlights:** Blown? OK? Too soft?
- **Shadows:** Blocked? Visible? Too open?
- **Contrast:** Harsh? Flat? Balanced?
- **Colors:** Accurate? Too saturated? Too dull? Shifts?
- **Paper match:** Does surface suit image?

**Iteration 2 (Correction):**

1. Address biggest issue first (usually brightness or contrast)
2. Make small changes:
- Too dark → +0.15 to +0.30 Exposure
- Too bright → -0.10 to -0.20 Exposure
- Too harsh → Contrast -10, flatten curve
- Too flat → Contrast +10, add S-curve
- Highlights blown → Compress with curves
- Shadows blocked → Shadows +15, lift curve endpoint
3. Print again (8x10")
4. Compare to Iteration 1
5. Document progress

**Iteration 3 (Fine-Tuning):**

1. Address remaining minor issues
2. Smaller adjustments:
- Brightness tweak +/- 0.10 Exposure
- Highlight/shadow fine-tune +/- 5
- Color shifts +/- 200K Temperature, +/- 2 Tint
3. Print (8x10")
4. Evaluate: Satisfied?

**If Satisfied:** Proceed to final size 
**If Not Satisfied:** Iteration 4 (rare beyond this)

**Beyond Iteration 4-5:**
- May be wrong paper choice
- May be image not suited for print (extreme contrast, saturated colors)
- Consider different paper or accept print limitations

### Training Your Eye

**Goal:** By iteration 3-4, pre-visualize print needs without testing.

**Process:**
- Document all iterations with notes
- Build mental model: "This type of edit → this print result"
- Over time, fewer iterations needed (pre-visualize adjustments)

**Example Learning:**
- "High-key images need +0.3 exposure for print" (learned via iteration)
- "Baryta paper needs -10 Clarity or feels harsh" (learned via iteration)
- "Warm white paper needs -300K Temperature adjustment" (learned via iteration)

---

## Dodge & Burn for Dimensionality

### What is Dodge & Burn?

**Darkroom Technique:**
- **Dodge:** Selectively lighten areas (less exposure)
- **Burn:** Selectively darken areas (more exposure)
- Ansel Adams: "Dodging and burning are steps to take care of mistakes God made in establishing tonal relationships."

**Digital Advantage:**
- Non-destructive (layers, adjustment brushes)
- Infinitely adjustable
- Precise masking

### Why Dodge & Burn Matters for Prints

**The Dimensional Problem:**
- Prints lack screen luminosity → dimensionality must be "built in"
- Subtle tonal shaping guides the viewer's eye
- Creates depth cues:
- Lighter = closer, more important
- Darker = receding, supporting

**Screen vs. Print:**
- Effective dodge & burn is **invisible on screen**
- Becomes **apparent in print** (enhances three-dimensionality)
- Aim for 3-8% tonal shifts (subtle, not dramatic)

### Dodge & Burn Principles

**Dodge (Lighten) Areas:**
- Highlights on rounded forms (cheekbones, shoulders, curves)
- Areas you want to emphasize (subject's face, foreground interest)
- Catchlights and specular highlights (adds sparkle)
- Leading lines (paths, rivers—guides eye)

**Burn (Darken) Areas:**
- Edges and corners (vignetting, subtle framing)
- Backgrounds (pushes them back, creates depth)
- Shadows on forms (enhances volume, shape)
- Distracting elements (de-emphasize without removing)

**Subtlety is Key:**
- Aim for 3-8% tonal shifts (not 20-50%)
- Should feel natural, not manipulated
- Test: Turn effect on/off—should be "oh, nice" not "wow, obvious"

---

## Portrait Dodge & Burn

### Goal

Enhance facial structure, create three-dimensional feel, flatter skin.

### Process

**Setup (Photoshop Method):**

1. Create new layer
2. Fill with 50% gray (Edit > Fill > 50% Gray)
3. Set blend mode to **Soft Light** (or Overlay for stronger effect)
4. Select brush tool:
- White brush = dodge (lighten)
- Black brush = burn (darken)
- Opacity: 5-10% (subtle, buildable)
- Soft round brush, appropriate size

**Dodge Areas (White Brush, 5-10% Opacity):**

- **Face:**
- Forehead center (convex form)
- Bridge of nose (highlight)
- Cheekbones (prominence)
- Chin (if prominent)
- Catchlights in eyes (enhance existing, don't fabricate)
- **Body:**
- Shoulders/collarbone (structure)
- Arms (cylindrical form, top lit)

**Burn Areas (Black Brush, 5-10% Opacity):**

- **Face:**
- Temples (concave, recedes)
- Sides of nose (shadow, defines nose)
- Under cheekbones (contour, adds definition)
- Jawline (sharpens, defines edge)
- Neck sides (narrows, creates shadow)
- **Background:**
- Around hair (separates subject from background)
- Edges of frame (subtle vignette, contains composition)

**Technique:**
- Build up effect with multiple low-opacity strokes
- Zoom out frequently to check overall effect
- Toggle layer on/off to verify subtlety

### Portrait Exercise

**Process:**

1. Select 2 portrait images (one studio, one natural light)

2. Apply dodge & burn (as described above)

3. Print before/after (11x14" or larger to see effect)

4. Evaluate:
- Does face feel more three-dimensional?
- Is lighting more sculpted?
- Does subject "pop" from background?

**Expected Result:**
- Before: Flat, two-dimensional
- After: Sculpted, form revealed, dimensional

---

## Landscape Depth Enhancement

### Goal

Create depth via tonal separation—foreground "comes forward," background "recedes."

### Process

**Depth Cues via Dodge & Burn:**

**Dodge (Lighten):**
- **Foreground elements** (rocks, flowers, trees—draws eye, "closer")
- **Sky focal points** (sunlit clouds—creates interest)
- **Leading lines** (paths, rivers—guides viewer in)

**Burn (Darken):**
- **Background** (mountains, distant hills—pushes back, atmospheric)
- **Sky corners** (natural vignette—frames, contains)
- **Less important midground** (de-emphasize, support foreground)
- **Frame edges** (vignette—contains composition, focuses)

**Selective Application:**
- Use adjustment brush or graduated filters
- Foreground: +0.3 to +0.5 Exposure (dodge)
- Background: -0.2 to -0.4 Exposure (burn)
- Smooth transitions (feathering)

### Landscape Exercise

**Process:**

1. Select 2 landscape images (with clear foreground, midground, background)

2. Apply dodge & burn depth cues (as described)

3. Print before/after (16x20" if possible—depth effect scales with size)

4. View from 3-6 feet (typical gallery viewing distance)

5. Evaluate:
- Does image "pull you in" more?
- Does foreground feel closer?
- Does background recede naturally?

**Expected Result:**
- Before: Flat, all elements same depth plane
- After: Layered, dimensional, clear depth progression

---

## Selective Attention with Burning

### Goal

Isolate subject in busy composition via selective darkening (burn down distractions).

### Process

**Busy Scene Strategy:**

1. Identify main subject

2. Burn down everything except subject area:
- **Backgrounds:** -10 to -15% Exposure (significant darkening)
- **Corners:** -20 to -30% Exposure (strong vignette)
- **Competing elements:** -5 to -10% Exposure (de-emphasize)
- **Leave subject at normal or slightly dodged** (0 to +5% Exposure)

3. Use radial/graduated filters for broad areas

4. Use adjustment brush for precise areas

5. Feather heavily (smooth transitions, avoid hard edges)

### Exercise

**Process:**

1. Take one busy composition (street scene, market, cluttered interior)

2. Burn down distractions (as described)

3. Print (8x10")

4. Evaluate:
- Does eye go straight to subject?
- Are distractions subdued?
- Does composition feel cleaner?

**Expected Result:**
- Before: Eye wanders, no clear focal point
- After: Subject immediately clear, supporting elements recede

---

## Tools & Techniques

### Lightroom Dodge & Burn

**Tools:**
- Adjustment Brush (K key)
- Radial Filter (Shift+M)
- Graduated Filter (M key)

**Process:**
1. Select tool
2. Set Exposure +0.05 to +0.30 (dodge) or -0.05 to -0.30 (burn)
3. Paint over areas
4. Adjust feathering (smooth transitions)
5. Fine-tune with sliders (Highlights, Shadows, Clarity)

**Advantage:** Fast, non-destructive, per-image adjustments

**Limitation:** Less precise than Photoshop layers

### Photoshop Dodge & Burn

**Method 1: 50% Gray Layer (Recommended)**

1. Create new layer, fill with 50% gray
2. Set blend mode to Soft Light or Overlay
3. Paint with white (dodge) or black (burn) at 5-10% opacity
4. Build up effect with multiple strokes

**Advantage:** Fully non-destructive, painterly control

**Method 2: Dodge & Burn Tools**

1. Select Dodge Tool (O key) or Burn Tool
2. Set Range: Highlights, Midtones, or Shadows
3. Set Exposure: 5-10%
4. Paint over areas

**Advantage:** Direct, immediate

**Limitation:** Destructive (affects pixels), harder to undo

**Method 3: Curves Adjustment Layer + Mask**

1. Create Curves layer, adjust (brighten or darken)
2. Invert mask (black)
3. Paint white on mask to reveal effect selectively

**Advantage:** Powerful, surgical precision

**Limitation:** More complex workflow

### Wacom Tablet (Optional but Recommended)

**Benefit:** Superior control over mouse for dodge & burn
- Pressure sensitivity (light touch = subtle, hard press = stronger)
- Natural hand motion (like painting)
- Faster, more intuitive workflow

**Entry-Level:** Wacom Intuos Small (~$70-100)

---

## Dodge & Burn Best Practices

**1. Subtlety Over Drama**
- Aim for 3-8% shifts, not 20-50%
- Should enhance, not dominate
- Test: Turn effect off—if you miss it, good. If you shout "wow it's gone!", too strong.

**2. Follow Existing Light**
- Don't invent light direction (dodge left, burn right → confusion)
- Enhance natural light and shadow patterns
- Study light behavior on forms (cylinders, spheres, planes)

**3. Build Gradually**
- Multiple low-opacity strokes > single high-opacity stroke
- Easier to control, more natural result

**4. Zoom Out Frequently**
- Close-up work can create over-localized effects
- Check overall balance from viewing distance

**5. Toggle On/Off**
- Verify effect is subtle
- If too obvious, reduce opacity or erase over-done areas

**6. Print to Verify**
- Dodge & burn often invisible on screen, revealed in print
- What looks "barely there" on screen may be perfect on paper

---

## Key Takeaways

1. **Screen edits fail on paper** → Prints need lifted midtones, softer highlights, reduced contrast.
2. **Two-pass editing** → Screen version vs. print version, separate workflows.
3. **Print preset accelerates workflow** → Consistent starting point, less guesswork.
4. **Iterate 2-4 times** → First print rarely perfect, refine through testing.
5. **Dodge & burn creates dimension** → Essential for print "pop," invisible on screen.
6. **Subtlety is key** → Aim for 3-8% tonal shifts, not dramatic changes.
7. **Follow existing light** → Enhance natural patterns, don't invent.
8. **Build gradually** → Multiple low-opacity strokes, not single high stroke.
9. **Print to verify** → Dodge & burn reveals itself on reflective media.
10. **Train your eye** → Document iterations, learn to pre-visualize print needs.

---

## Related References

- **[Print_Foundations_Reference.md](Print_Foundations_Reference.md)** - Tonal basics, screen vs. print differences
- **[Tonal_Control_Reference.md](Tonal_Control_Reference.md)** - Highlight/shadow control, microcontrast
- **[Color_Management_Workflow_Reference.md](../Color_Management_Workflow_Reference.md)** - Iterative testing workflow
- **[Fine_Art_Print_Philosophy_Reference.md](Fine_Art_Print_Philosophy_Reference.md)** - Print-first mindset

---

**Updated:** 2026-05-23 
**Author:** Documentation Team 
**Use Case:** Print-specific editing, Two-pass workflow, Dodge & burn for dimensionality
