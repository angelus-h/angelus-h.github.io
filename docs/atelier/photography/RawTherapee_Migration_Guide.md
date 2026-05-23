# RawTherapee Migration Guide - From Adobe Camera RAW

**Version:** 1.0  
**Created:** 2026-05-05  
**Objective:** Seamless transition from Adobe Camera RAW to RawTherapee  
**Platform:** RawTherapee 5.9+ (Linux, Windows, macOS)

---

##  Introduction

**RawTherapee** is an open-source, professional-grade RAW photo editor that serves as an excellent alternative to Adobe Camera RAW. It's completely free, highly powerful, and requires no subscription.

### Why Choose RawTherapee?

 **Free and Open Source** - No subscription, no license fees  
 **Professional Tools** - Advanced color management, local corrections, HDR  
 **Complete RAW Processing** - Supports all major camera RAW formats  
 **Non-destructive Workflow** - Original files remain untouched  
 **Batch Processing** - Mass processing with sidecar files  
 **Cross-platform** - Linux, Windows, macOS  
 **No Vendor Lock-in** - Open formats, exportable settings

---

## 1. Installation and Initial Setup

### Linux (Fedora/RHEL)

```bash
# DNF installation
sudo dnf install rawtherapee

# Flatpak installation (newer version)
flatpak install flathub com.rawtherapee.RawTherapee

# Launch
rawtherapee
```

### Windows

```
1. Download: https://www.rawtherapee.com/downloads
2. Run installer (rawtherapee-5.9-win64.exe)
3. Select installation directory
4. Create Start Menu shortcut
```

### macOS

```bash
# Homebrew installation
brew install --cask rawtherapee

# Or download: https://www.rawtherapee.com/downloads
# Install DMG file
```

### First Launch - Preferences

**Preferences (Ctrl+P or Cmd+P)**

#### 1. General Tab
```
- Language: English
- Working Color Space: ProPhoto RGB (recommended for RAW)
- Theme: RawTherapee 5 (Dark) or GTK+
```

#### 2. Color Management Tab
```
- Monitor Profile: 
  - If calibrated: Select ICC profile
  - If not: sRGB IEC61966-2.1 (default)

- Output Profile (for export):
  - Web: sRGB
  - Print: Adobe RGB (1998)
  - Professional: ProPhoto RGB
```

#### 3. Image Processing Tab
```
- Default Processing Profile: Neutral (or custom)
- Auto-save: Enable (automatic save)
```

#### 4. File Browser Tab
```
- Thumbnail size: 200-250 px (preference)
- Show only supported formats: Enable
- Filmstrip: Enable (quick navigation)
```

---

## 2. Interface Overview - Camera RAW vs RawTherapee

### Interface Comparison

| Interface Area | Adobe Camera RAW | RawTherapee |
|----------------|------------------|-------------|
| **Top** | Histogram + Tools Panel | Menu Bar (File, Edit, Image, Batch, Tools, Help) |
| **Left Panel** | - (not present) | File Browser + Thumbnails |
| **Center** | Main Image Preview | Main Image Preview + Histogram |
| **Right Panel** | - (tools embedded top) | Tool Tabs: Exposure, Detail, Color, Transform, Raw, Metadata |
| **Bottom** | Toolbar (Basic/Details/etc.) | - (not present) |

**Key Differences:**
- **Left Panel:** File Browser - absent in Camera RAW
- **Center Area:** Large image preview + Histogram + Tools
- **Right Panel:** Editing tools organized by tabs

---

## 3. Basic Workflow - Step by Step

### Workflow 1: Simple RAW Processing

#### Step 1: Opening RAW Files

```
1. File Browser (left side) → Navigate to RAW files directory
2. Click thumbnail → Image loads
3. Or: File → Open Image (Ctrl+O)
```

**TIP:** Use filmstrip (bottom) for quick switching between images.

#### Step 2: Basic Exposure Correction

**Camera RAW Mapping:**

| Adobe Camera RAW | RawTherapee | Tab |
|------------------|-------------|-----|
| Exposure | Exposure Compensation | Exposure |
| Contrast | Contrast | Exposure |
| Highlights | Highlights | Exposure → Tone Mapping |
| Shadows | Shadows | Exposure → Shadows/Highlights |
| Whites | - | - |
| Blacks | Black | Exposure |

**RawTherapee Exposure Tab:**

```
Exposure → Exposure
├─ Exposure Compensation: -2.0 to +5.0 EV (brightness)
├─ Contrast: -100 to +100 (contrast)
├─ Saturation: -100 to +100 (saturation)
├─ Black: 0 to 65535 (black point)
└─ Highlight Compression: 0-500 (save overexposed)

Exposure → Tone Mapping (Advanced)
├─ Highlights: -100 to +100
├─ Shadows: -100 to +100
├─ Local Contrast: 0 to 100
└─ Tone Curve: Linear, Film-like, etc.
```

**Exercise 3.1: Fixing Underexposed Image**

```
1. Open a dark RAW image
2. Exposure Tab → Exposure Compensation: +1.5 EV
3. Shadows/Highlights → Shadows: +50 (recover details)
4. Highlights: -20 (save sky)
5. Contrast: +10 (increase dynamics)
```

#### Step 3: White Balance

**Camera RAW Mapping:**

| Adobe Camera RAW | RawTherapee | Tab |
|------------------|-------------|-----|
| Temperature | Temperature | Color → White Balance |
| Tint | Tint (Green/Magenta) | Color → White Balance |
| WB Presets (Daylight, etc.) | Method dropdown | Color → White Balance |

**RawTherapee White Balance:**

```
Color Tab → White Balance
├─ Method: 
│   ├─ Camera (camera setting)
│   ├─ Auto (automatic)
│   ├─ Daylight (5500K)
│   ├─ Cloudy (6500K)
│   ├─ Shade (7500K)
│   ├─ Tungsten (2850K)
│   ├─ Fluorescent (4000K)
│   └─ Custom
│
├─ Temperature: 2000K - 12000K (warm/cool)
└─ Tint (Green <-> Magenta): -150 to +150
```

**Exercise 3.2: Warm Sunset Mood**

```
1. Color → White Balance
2. Method: Custom
3. Temperature: 6500K (slightly warm)
4. Tint: -10 (toward magenta)
5. Saturation: +15 (enhance colors)
```

**Eyedropper Tool:**

```
1. White Balance → Click Eyedropper icon
2. Click neutral gray area in image
3. RawTherapee auto-adjusts WB
```

#### Step 4: Sharpening and Detail

**Camera RAW Mapping:**

| Adobe Camera RAW | RawTherapee | Tab |
|------------------|-------------|-----|
| Sharpening Amount | Amount | Detail → Sharpening |
| Radius | Radius | Detail → Sharpening |
| Detail | - | - |
| Masking | - | (Edge detection instead) |
| Noise Reduction Luminance | Luminance | Detail → Noise Reduction |
| Noise Reduction Color | Chrominance | Detail → Noise Reduction |

**RawTherapee Sharpening:**

```
Detail Tab → Sharpening
├─ Sharpening Method:
│   ├─ Unsharp Mask (USM) - classic
│   ├─ RL Deconvolution - professional
│   └─ Contrast by Detail Levels - detail-rich
│
├─ Amount: 0-300 (strength)
├─ Radius: 0.5-2.5 (scope)
├─ Threshold: 0-2000 (edge detection)
└─ Only Edges: Enable (sharpen edges only)
```

**Exercise 3.3: Portrait Sharpening**

```
1. Detail → Sharpening
2. Method: Unsharp Mask
3. Amount: 80
4. Radius: 0.8
5. Threshold: 20 (smooth skin)
6. Only Edges: Disable
```

**Exercise 3.4: Landscape Sharpening**

```
1. Detail → Sharpening
2. Method: RL Deconvolution
3. Iterations: 30
4. Damping: 0
5. Detail → Microcontrast: +20 (enhance details)
```

#### Step 5: Noise Reduction

```
Detail Tab → Noise Reduction
├─ Luminance (brightness noise):
│   ├─ Luminance: 0-100 (strength)
│   ├─ Detail Recovery: 0-100 (preserve details)
│   └─ Luminance Detail: 0-100
│
└─ Chrominance (color noise):
    ├─ Chrominance Master: 0-100
    ├─ Red-Green: 0-100
    └─ Blue-Yellow: 0-100
```

**Exercise 3.5: High ISO Noise Handling**

```
1. Detail → Noise Reduction
2. Luminance: 40
3. Detail Recovery: 50 (avoid plastic look)
4. Chrominance Master: 60
5. Check: 100% zoom (Ctrl+1)
```

---

## 4. Advanced Color Management

### Vibrance and Saturation

**Camera RAW Mapping:**

| Adobe Camera RAW | RawTherapee | Tab |
|------------------|-------------|-----|
| Vibrance | - | (Saturation + Lab Adjustments) |
| Saturation | Saturation | Exposure Tab |

**RawTherapee Approach:**

```
Exposure Tab → Saturation: -100 to +100 (global saturation)

Color Tab → Vibrance
├─ Pastels Saturation: -100 to +100 (pale colors)
├─ Saturated Saturation: -100 to +100 (saturated colors)
└─ Threshold: 0-100 (boundary)
```

**Exercise 4.1: Natural Color Enhancement**

```
1. Exposure → Saturation: +10
2. Color → Vibrance → Pastels Saturation: +30
3. Saturated Saturation: -10 (avoid over-saturation)
```

### HSV / HSL Color Curve (Hue/Saturation/Value)

**Camera RAW Mapping:**

| Adobe Camera RAW | RawTherapee | Tab |
|------------------|-------------|-----|
| HSL Panel (Hue/Sat/Lum) | HSV Equalizer | Color → HSV Equalizer |

**RawTherapee HSV Equalizer:**

```
Color Tab → HSV Equalizer
├─ Hue: 
│   - Red, Orange, Yellow, Green, Cyan, Blue, Magenta, Purple
│   - Shift hue: -180 to +180°
│
├─ Saturation:
│   - Per-channel saturation: -100 to +100
│
└─ Value (Brightness):
    - Per-channel brightness: -100 to +100
```

**Exercise 4.2: Blue Sky Enhancement**

```
1. Color → HSV Equalizer
2. H (Hue) → Blue channel: +5 (deeper blue)
3. S (Saturation) → Blue channel: +20
4. V (Value) → Blue channel: -10 (darker)
```

**Exercise 4.3: Skin Tone Correction (Portrait)**

```
1. Color → HSV Equalizer
2. H → Orange channel: -5 (more natural skin)
3. S → Orange channel: -10 (reduce over-saturation)
4. V → Orange channel: +5 (brighter skin)
```

### Lab Adjustments (Professional Color Control)

**RawTherapee unique feature** - no direct Camera RAW equivalent.

```
Color Tab → Lab Adjustments
├─ Lightness (L): -100 to +100 (brightness)
├─ a (Green-Red): -100 to +100
├─ b (Blue-Yellow): -100 to +100
├─ Chromaticity: -100 to +100 (color saturation)
└─ Lightness Curve (tone curve for brightness)
```

**Exercise 4.4: Cinematic Color Grading**

```
1. Color → Lab Adjustments
2. a (Green-Red): +10 (warmer tint)
3. b (Blue-Yellow): -5 (slight blue shift)
4. Lightness Curve: Draw S-curve (contrast)
5. Exposure → Saturation: -10 (film look)
```

---

## 5. Local Corrections

**Camera RAW Mapping:**

| Adobe Camera RAW | RawTherapee | Tab |
|------------------|-------------|-----|
| Adjustment Brush | - | (No native support) |
| Graduated Filter | Graduated Filter | Transform → Graduated Filter |
| Radial Filter | - | (Workaround: GIMP combo) |

### RawTherapee Graduated Filter

```
Transform Tab → Graduated Filter
├─ Strength: -2.0 to +2.0 EV (brightness change)
├─ Feather: 0-100 (transition smoothing)
├─ Angle: 0-360° (filter direction)
└─ Center X/Y: 0-100% (position)
```

**Exercise 5.1: Darken Sky**

```
1. Transform → Graduated Filter
2. Strength: -0.8 EV
3. Angle: 0° (top to bottom)
4. Feather: 60 (soft transition)
5. Center Y: 70% (cover sky)
```

### Workaround: GIMP + RawTherapee Workflow

**For local corrections:**

```
1. RawTherapee: RAW basic processing
2. Export → TIFF 16-bit (File → Save Image)
3. GIMP: Local brush/mask corrections
4. Export → Final JPEG/PNG
```

---

## 6. RAW Processing Settings

### Demosaicing (RAW Interpolation)

**RawTherapee unique feature** - Camera RAW doesn't expose this.

```
Raw Tab → Demosaicing
├─ Method:
│   ├─ AMaZE (best quality, slow)
│   ├─ DCB (balanced)
│   ├─ RCD (fast, good quality)
│   └─ IGV (green channel optimized)
│
├─ False Color Suppression: 0-100 (suppress false colors)
└─ Iterations: 1-5 (refinement steps)
```

**Recommendation:**
- **Landscapes, detail-rich:** AMaZE or RCD
- **Portrait:** DCB
- **Fast preview:** Fast (preprocessing)

### Chromatic Aberration Correction

```
Raw Tab → Chromatic Aberration
├─ Auto Correction: Enable (automatic)
├─ Red: -3.0 to +3.0 (red shift)
└─ Blue: -3.0 to +3.0 (blue shift)
```

**Exercise 6.1: Lens Error Correction**

```
1. Raw → Chromatic Aberration
2. Auto Correction: Enable
3. If visible purple/green fringe: manual adjustment
   - 100% zoom (Ctrl+1) at edges
   - Red/Blue slider fine-tuning
```

### Vignetting Correction

```
Transform Tab → Vignetting Filter
├─ Amount: -100 to +100 (darken/lighten)
├─ Radius: 0-100 (distance from center)
└─ Strength: 1-100 (effect strength)
```

**Exercise 6.2: Creative Vignetting**

```
1. Transform → Vignetting Filter
2. Amount: -40 (darken)
3. Radius: 60 (wide circle)
4. Strength: 50 (soft effect)
5. Goal: Direct attention to center
```

---

## 7. Export and Output Settings

### Export Formats

```
File → Save Image (Ctrl+S)

├─ JPEG (Quality 1-100)
│   - Web: 85-90 Quality
│   - Print: 95-100 Quality
│   - Chroma Subsampling: Best quality (4:4:4)
│
├─ TIFF (8-bit or 16-bit)
│   - Uncompressed (highest quality)
│   - LZW Compression (lossless)
│   - ZIP Compression (smaller file size)
│
├─ PNG (8-bit or 16-bit)
│   - Lossless compression
│   - Good for web with transparency
│
└─ PPM/PGM (rare use)
```

**Recommended Workflow:**

```
1. RAW → RawTherapee editing
2. Export TIFF 16-bit (if further GIMP editing needed)
3. Export JPEG 95% (if finished)
```

### Batch Processing

**Difference from Camera RAW:** RawTherapee uses .pp3 sidecar files.

#### Batch Workflow

```
1. Edit one image
2. File → Save Current Profile (Ctrl+S) → Saves .pp3 file
3. File Browser → Select multiple images (Ctrl+Click)
4. Batch Queue (Ctrl+B)
5. Add images → Queue Tab
6. Select Output Directory
7. Start Batch Processing
```

**Exercise 7.1: Batch Processing 50 Wedding Photos**

```
1. Edit first image:
   - Exposure: +0.5 EV
   - WB: Daylight
   - Saturation: +10
   - Sharpening: Amount 100
   
2. Save Profile as "Wedding_Preset.pp3"

3. File Browser → Select all 50 RAW files (Ctrl+A)

4. Batch → Add to Queue

5. Output:
   - Format: JPEG
   - Quality: 95
   - Directory: /home/user/processed/

6. Start Processing
```

---

## 8. Presets and Processing Profiles

### Built-in Profiles

```
Exposure Tab → Processing Profile Dropdown
├─ Neutral (default, neutral)
├─ Standard (mild contrast)
├─ Landscape (saturated colors)
├─ Portrait (soft skin)
├─ Sunset (warm tones)
├─ Night (noise reduction)
└─ Custom (saved profiles)
```

### Saving Custom Processing Profile

```
1. Complete editing
2. Processing Profile Dropdown → Save Current Profile
3. Filename: "MyPortrait.pp3"
4. Save location: ~/.config/RawTherapee/profiles/ (Linux)
   or C:\Users\<User>\AppData\Local\RawTherapee\profiles\ (Windows)
```

**Exercise 8.1: Creating "Film Look" Profile**

```
1. Exposure:
   - Contrast: -10 (flatter)
   - Saturation: -15
   - Tone Curve: Film-like (built-in)

2. Color → Lab Adjustments:
   - a (Green-Red): +5
   - b (Blue-Yellow): -3

3. Detail → Sharpening:
   - Amount: 60 (mild)

4. Save Profile → "Film_Look.pp3"
```

### Sharing Profiles

```
# Linux
~/.config/RawTherapee/profiles/MyProfile.pp3

# Share:
1. Copy .pp3 file
2. On another machine: paste into profiles/ directory
3. Restart RawTherapee
4. Profile available in dropdown
```

---

## 9. Keyboard Shortcuts

**Boost Productivity** - Learn at least 10-15 shortcuts!

### Basic Navigation

| Function | Shortcut | Description |
|---------|----------|-------------|
| **Open Image** | Ctrl+O | Open RAW file |
| **Save Image** | Ctrl+S | Export (JPEG/TIFF) |
| **Preferences** | Ctrl+P | Settings |
| **Quit** | Ctrl+Q | Exit |
| **Undo** | Ctrl+Z | Undo |
| **Redo** | Ctrl+Y | Redo |

### Zoom and View

| Function | Shortcut | Description |
|---------|----------|-------------|
| **Fit to Screen** | Ctrl+F | Fit entire image |
| **100% Zoom** | Ctrl+1 | 1:1 size (details) |
| **200% Zoom** | Ctrl+2 | 200% magnification |
| **Zoom In** | Ctrl++ | Zoom in |
| **Zoom Out** | Ctrl+- | Zoom out |
| **Full Screen** | F11 | Full screen mode |

### Comparison (Before/After)

| Function | Shortcut | Description |
|---------|----------|-------------|
| **Toggle Before/After** | B | Original ↔ Edited |
| **Show Original** | Hold Spacebar | View original |

### Batch and Queue

| Function | Shortcut | Description |
|---------|----------|-------------|
| **Add to Queue** | Ctrl+B | Add to batch queue |
| **Next Image** | → or Page Down | Next image |
| **Previous Image** | ← or Page Up | Previous image |

---

## 10. Tips and Tricks

### Tip 1: Non-Destructive Workflow

```
 RawTherapee does NOT modify original RAW files!
 All settings saved to .pp3 sidecar files
 Can always return to original
```

**File Structure:**

```
photos/
├── IMG_0001.CR2          (Canon RAW - original)
├── IMG_0001.CR2.pp3      (RawTherapee settings)
├── IMG_0002.NEF          (Nikon RAW - original)
└── IMG_0002.NEF.pp3      (RawTherapee settings)
```

### Tip 2: Using Histogram

```
Histogram Panel (above image)
├─ Left edge (Black): Shadows clipping (too dark)
├─ Right edge (White): Highlights clipping (overexposed)
└─ Center: Midtones
```

**Clipping Warning:**

```
View → Show Clipped Highlights (Ctrl+H)
- Red: Overexposed areas
- Blue: Underexposed areas (Ctrl+Shift+H)
```

---

## 11. Workflow Examples - Different Genres

### 11.1 Portrait Photo Workflow

```
1. RAW Import

2. Exposure Tab
   - Exposure Compensation: +0.3 EV (brighter skin)
   - Contrast: -5 (softer)
   - Saturation: -5 (more natural)

3. Color Tab
   - White Balance: Auto or Daylight
   - HSV Equalizer → Orange (skin):
     * Hue: -3
     * Saturation: -10
     * Value: +5

4. Detail Tab
   - Sharpening: Amount 60, Radius 0.8
   - Noise Reduction: Luminance 20 (if high ISO)

5. Transform Tab
   - Vignetting: -15 (darken background)

6. Export
   - JPEG, Quality 95
```

### 11.2 Landscape Photo Workflow

```
1. RAW Import

2. Exposure Tab
   - Exposure Compensation: According to histogram
   - Contrast: +15 (dramatic sky)
   - Saturation: +20 (vivid colors)

3. Color Tab
   - Vibrance: Pastels +30, Saturated +10
   - HSV Equalizer:
     * Blue (sky): H +5, S +25, V -5
     * Green (foliage): H -5, S +15, V +5

4. Detail Tab
   - Sharpening Method: RL Deconvolution
   - Iterations: 40
   - Microcontrast: +25 (enhance details)

5. Transform Tab
   - Graduated Filter (darken sky):
     * Strength: -0.6 EV
     * Feather: 70
     * Angle: 0°

6. Raw Tab
   - Demosaicing: AMaZE (best quality)

7. Export
   - JPEG, Quality 98 (for print)
```

---

## 12. Camera RAW → RawTherapee Migration Checklist

### Week 1 - Master Basics

```
- [ ] Install and configure RawTherapee
- [ ] Learn interface (File Browser, Tool Tabs)
- [ ] Process 10 images: Exposure, WB, Saturation
- [ ] Practice export workflow (JPEG 90%)
- [ ] Shortcuts: Ctrl+O, Ctrl+S, Ctrl+1, B (before/after)
```

### Week 2 - Color Management and Detail

```
- [ ] Use HSV Equalizer (5 image practice)
- [ ] Sharpening techniques (Portrait vs Landscape)
- [ ] Noise Reduction (high ISO images)
- [ ] Vibrance vs Saturation differences
- [ ] Try Lab Adjustments
```

### Week 3 - Advanced RAW Settings

```
- [ ] Compare Demosaicing methods (AMaZE, DCB, RCD)
- [ ] Chromatic Aberration correction
- [ ] Creative Vignetting Filter
- [ ] Tone Mapping (HDR-like effects)
- [ ] Practice Graduated Filter
```

### Week 4 - Batch Processing and Automation

```
- [ ] Save first Processing Profile
- [ ] Create 3 custom profiles (Portrait, Landscape, Night)
- [ ] Batch Queue with 50+ images
- [ ] Speed up filmstrip navigation
- [ ] Setup .pp3 sidecar backup system
```

---

## 13. Resources and Further Learning

### Official Documentation

- **RawPedia:** https://rawpedia.rawtherapee.com/ (complete reference)
- **Official Manual:** https://rawtherapee.com/documentation
- **Forum:** https://discuss.pixls.us/c/software/rawtherapee

### YouTube Channels

- **Harry Durgin** - RawTherapee tutorials
- **The Photographer's Workflow** - RAW processing
- **GIMP & RawTherapee Combined** - Full workflow

### Communities

- **r/RawTherapee** (Reddit)
- **Pixls.us** (Open source photography community)
- **RawTherapee Facebook Group**

---

## Summary

**RawTherapee advantages over Adobe Camera RAW:**

| Aspect | Adobe Camera RAW | RawTherapee |
|--------|------------------|-------------|
| **Price** | $54.99/mo (LR+PS) | **Free** |
| **Platforms** | Windows, macOS | Linux, Windows, macOS |
| **RAW Support** | Extensive | Extensive (with DNG) |
| **Color Management** | Good | **Excellent** (Lab, HSV) |
| **Batch Processing** | Limited | **Strong** (.pp3 profiles) |
| **Local Corrections** | **Excellent** (Brush, Radial) | Limited (Grad Filter) |
| **Demosaicing Control** | None | **Full** (AMaZE, DCB, etc.) |
| **Learning Curve** | Easier | Steeper |

**Conclusion:**

RawTherapee is an excellent free alternative if:
-  You don't want Adobe subscription
-  You use Linux
-  You need batch processing
-  You focus on RAW development (vs local corrections)

**Recommended Combination:**
- **RawTherapee** - RAW development
- **GIMP** - Local corrections, retouching
- **Darktable** - If you need Lightroom-like workflow

---

**Created:** 2026-05-05  
**Author:** AI Assistant for portfolio demonstration  
**Purpose:** Adobe Camera RAW → RawTherapee migration  

**Happy Photo Editing! **

---

## License

This guide is released under the **MIT License**.

---

## Contributing

Contributions welcome! Found an error or want to add techniques:

1. Fork the repository
2. Create feature branch (`git checkout -b feature/new-technique`)
3. Commit changes (`git commit -m 'Add HSV color grading technique'`)
4. Push (`git push origin feature/new-technique`)
5. Open Pull Request
