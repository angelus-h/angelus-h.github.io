---
description: Color models, color spaces, ICC profiles, gamut, rendering intents, working space selection, and the critical distinction between Assign Profile and Convert to Profile.
---

# Chapter 02: Color Science for Photographers

## Learning Objectives

After completing this chapter, you will be able to:

1. Distinguish between a color model and a color space, and explain why RGB values are meaningless without a color space.
2. Describe the purpose of the CIE chromaticity diagram and how it provides a reference frame for comparing color spaces.
3. Compare sRGB, Adobe RGB (1998), and ProPhoto RGB in terms of gamut, gamma, and appropriate use cases.
4. Explain what an ICC profile does, the difference between a device profile and a working space profile, and why both are necessary for color management.
5. Define gamut and out-of-gamut, and predict what happens when colors fall outside a destination gamut.
6. Describe the four rendering intents and choose the appropriate one for a given photographic output scenario.
7. Explain the critical difference between Assign Profile and Convert to Profile, and predict the visual result of each operation.
8. Configure Photoshop's Color Settings dialog to establish a consistent, photographer-appropriate color management environment.
9. Understand the purpose of soft proofing and when it becomes essential in a print workflow.

## Conceptual Foundation

Chapter 01 established that RGB values are numbers, and that the same numbers mean different colors in different color spaces. This chapter explains how that mapping works, why it matters, and how Photoshop manages the translation between different devices and color spaces.

Color management exists because every device in the imaging chain — your camera, your monitor, your printer — reproduces a different subset of visible colors. Your camera captures colors your monitor cannot display. Your monitor displays colors your printer cannot reproduce. Without a system to manage these translations, the color you see on screen is an accident of hardware, not a faithful representation of your image data.

The system that manages these translations is built on ICC profiles and rendering intents. Understanding this system is not optional for serious photographic work. If you do not understand it, you are making critical decisions — working space, export format, print settings — without knowing what those decisions do to your colors. The result is unpredictable color from screen to print, mysterious color shifts when opening files from other sources, and damaged images from incorrect profile operations.

This chapter gives you the conceptual framework. The practical output workflow — soft proofing for specific printers, preparing files for specific output conditions — is covered in detail in Chapter 21.

## Terminology

### Color Model vs Color Space

**Definition:** A color model is an abstract mathematical system for describing color using a set of components. A color space is a specific implementation of a color model: it takes the abstract component axes and defines exactly what each combination of values means in terms of actual, measurable color. A color model is a coordinate system; a color space is a map drawn on that coordinate system.

**Meaning in photographic practice:** RGB is a color model. It says: "Describe color using three components — red, green, and blue — each ranging from zero to a maximum." But it does not say which red, which green, which blue, or what the maximum represents. sRGB is a color space: it specifies exactly which red, green, and blue primaries to use, defines the white point (D65), specifies the transfer function (close to gamma 2.2), and thereby assigns a precise, measurable color to every possible combination of R, G, B values. Adobe RGB (1998) is a different color space using the same RGB model but with different primaries, yielding a larger gamut. The same RGB triplet — say (0, 180, 0) — refers to a different actual green in sRGB than in Adobe RGB.

**Photoshop implementation:** Every Photoshop document has an associated color space (visible in the document title bar and under Edit > Assign Profile or Edit > Convert to Profile). When you create a new document, it inherits the working space specified in Edit > Color Settings. When you open a file, Photoshop checks whether the embedded profile matches the working space and offers options (depending on your Color Settings policy) if there is a mismatch.

**Related concepts:** Color space, ICC profile, working space, gamut.

**Common misconception:** "RGB is a color space." RGB is a color model. Saying "this image is in RGB" tells you nothing about which colors the values represent. You need to know which RGB color space — sRGB, Adobe RGB, ProPhoto RGB, or another — to interpret the values. An image with no embedded profile is ambiguous: Photoshop will treat the values according to your working space setting, but it cannot know what the values were intended to mean.

### CIE Chromaticity Diagram

**Definition:** The CIE chromaticity diagram is a two-dimensional representation of all colors visible to the average human observer, derived from the CIE 1931 color matching experiments. It plots hue and saturation on x and y axes, with the horseshoe-shaped boundary representing the spectral locus (pure spectral colors). Any color space can be plotted on this diagram as a triangle (for RGB spaces) or other shape, showing which portion of visible color it can represent.

**Meaning in photographic practice:** The CIE diagram serves as a device-independent reference frame. When you compare sRGB and Adobe RGB, you can plot both triangles on the CIE diagram and visually see that Adobe RGB's triangle is larger — it encompasses more of the visible color range, particularly in the cyan-green region. The diagram makes gamut comparisons concrete. You do not need to perform calculations on the diagram; you need to understand what it represents so that gamut comparisons and profile visualizations make sense.

**Photoshop implementation:** Photoshop does not display the CIE diagram directly within its interface. However, the diagram appears frequently in documentation, profile inspection tools (such as Apple's ColorSync Utility or third-party profile editors), and monitor calibration software. Understanding the diagram helps you interpret color management information you encounter outside Photoshop.

**Related concepts:** Gamut, color space, spectral locus, color matching functions.

**Common misconception:** "The CIE diagram shows all colors accurately on screen." It cannot. Your monitor can display only a fraction of the colors represented in the diagram. The regions of the diagram outside your monitor's gamut are approximated — the diagram is useful as a map, not as a faithful reproduction of every color it plots.

### sRGB

**Definition:** sRGB (standard Red Green Blue) is a color space defined by HP and Microsoft in 1996 as a standard for monitors, printers, and the internet. It uses specific red, green, and blue primaries, a D65 white point, and a piecewise transfer function close to gamma 2.2. Its gamut is relatively small compared to other photographic working spaces — it was designed to represent the typical color range of consumer CRT monitors of the mid-1990s.

**Meaning in photographic practice:** sRGB is the lowest common denominator of color spaces. Virtually every consumer device — monitors, phones, web browsers, consumer printers — assumes sRGB if no profile is specified. It is the correct choice for images destined for the web, social media, email, or any context where you cannot control the viewer's color management. However, sRGB clips colors that cameras routinely capture, particularly saturated cyans, greens, and some blues. Using sRGB as a working space during editing means you are discarding those colors before you even begin post-processing.

**Photoshop implementation:** sRGB is one of the working space options in Edit > Color Settings. It is typically selected via the "North America General Purpose 2" or "Monitor Color" presets. For photographic work, sRGB is appropriate as a final export space (Edit > Convert to Profile before saving for web) but is generally too small as an editing working space.

**Related concepts:** Adobe RGB, ProPhoto RGB, gamut, working space, web output.

**Common misconception:** "sRGB is fine for everything because my monitor is sRGB." Even if your monitor displays only the sRGB gamut, your camera captures colors outside that gamut. Working in a larger space preserves those colors through your editing pipeline. They may be visible on a wide-gamut monitor, reproducible by a printer on certain papers, or relevant to future output media. Converting to sRGB at the end of your pipeline preserves options; starting in sRGB discards them immediately.

### Adobe RGB (1998)

**Definition:** Adobe RGB (1998) is a color space developed by Adobe Systems. It uses a D65 white point, a pure gamma of 2.2 (unlike sRGB's piecewise function), and wider-gamut primaries than sRGB. Its gamut is approximately 50% larger than sRGB's when measured as a volume in CIE Lab space, with the most significant expansion in the cyan-green region.

**Meaning in photographic practice:** Adobe RGB is the standard working space for photographic editing when output includes print. It captures most of the colors that CMYK printing, inkjet photo printing, and high-quality monitors can reproduce, while being small enough that out-of-gamut issues are uncommon during editing. Landscape photographers benefit particularly from the expanded greens and cyans. Most professional photography workflows use Adobe RGB as the working space.

**Photoshop implementation:** Adobe RGB (1998) is available as a working space in Edit > Color Settings. The "North America Prepress 2" preset selects it as the RGB working space. Many cameras offer an Adobe RGB color space option (which affects only JPEG output and the embedded preview of RAW files — the RAW data itself is not in any specific RGB color space until conversion).

**Related concepts:** sRGB, ProPhoto RGB, gamut, working space, CMYK gamut.

**Common misconception:** "Shooting in Adobe RGB in-camera gives me better RAW files." The camera's color space setting affects only the JPEG rendering and the embedded preview/thumbnail of the RAW file. The RAW data itself is sensor-native and gets converted to a color space only during RAW development. The color space setting in your RAW converter is what matters for the resulting file.

### ProPhoto RGB

**Definition:** ProPhoto RGB (also known as ROMM RGB, Reference Output Medium Metric) is a very wide gamut color space developed by Kodak. It uses a D50 white point (unlike sRGB and Adobe RGB which use D65) and a gamma of 1.8. Its gamut encompasses nearly all colors achievable by photographic output devices, and extends well beyond the range of visible color in some regions — meaning it includes "colors" that do not exist as perceptible hues.

**Meaning in photographic practice:** ProPhoto RGB is the largest commonly used photographic working space. It can contain virtually all colors captured by digital camera sensors, preserving the maximum color data through the editing pipeline. However, this comes with significant trade-offs:

- Because the gamut is so large relative to the number of available code values, the "distance" between adjacent code values represents a larger perceptual difference. In 8-bit mode, this can cause posterization in smooth gradients. ProPhoto RGB should be used only in 16-bit or higher.
- It is trivially easy to push colors into regions that no device can reproduce and that do not correspond to visible colors. Without soft proofing discipline, you can create images that look dramatically different on output.
- The D50 white point differs from sRGB and Adobe RGB (D65), which means converting between ProPhoto RGB and D65-based spaces involves a chromatic adaptation transformation — a detail handled by the color management system but occasionally relevant in troubleshooting.

**Photoshop implementation:** ProPhoto RGB is available as a working space in Edit > Color Settings. Adobe Camera Raw and Lightroom use a variant of ProPhoto RGB (called "Melissa RGB" internally in some versions) as their native editing space. When you open a file from Camera Raw into Photoshop, the output profile you select in Camera Raw's workflow options determines the color space of the resulting Photoshop document.

**Related concepts:** Adobe RGB, gamut, bit depth, 16-bit editing, working space, D50 vs D65 white point.

**Common misconception:** "ProPhoto RGB is always better because it is bigger." Bigger is not inherently better. ProPhoto RGB preserves more captured color data, but it demands 16-bit precision (8-bit is inadequate for the gamut size), requires more careful monitoring of out-of-gamut conditions, and offers no advantage for images whose colors fall entirely within Adobe RGB's gamut. For many photographs — particularly indoor portraits, product photography, and low-saturation subjects — Adobe RGB is equally effective and easier to manage.

### ICC Profile

**Definition:** An ICC profile is a standardized data file (conforming to the International Color Consortium specification) that describes the color characteristics of a device or a color space. It contains the mathematical transformations needed to convert color values between a device-specific or space-specific representation and a device-independent reference (the Profile Connection Space, typically CIE Lab or CIE XYZ).

**Meaning in photographic practice:** ICC profiles are the mechanism that makes color management work. Every link in the imaging chain — your monitor, your printer, your working space, the output file — has (or should have) an associated ICC profile. When Photoshop converts from your working space to your printer's space, it uses the working space profile to translate your document's values into the Profile Connection Space, then uses the printer profile to translate from the Profile Connection Space into the values the printer needs. Without accurate profiles, this translation is impossible.

There are two broad categories:

- **Device profiles** describe how a specific device (a particular monitor, a specific printer with a specific paper and ink set) reproduces color. Device profiles are created by measuring the device's output — typically using a spectrophotometer and calibration software.
- **Working space profiles** (also called abstract or editing profiles) describe idealized, mathematically defined color spaces like sRGB, Adobe RGB, or ProPhoto RGB. These are not tied to any physical device. They provide a well-behaved, uniform editing environment.

**Photoshop implementation:** Photoshop manages ICC profiles through several mechanisms:

- Edit > Color Settings: sets the default working space profile and color management policies.
- Edit > Assign Profile: attaches a different profile to the document without changing pixel values.
- Edit > Convert to Profile: transforms pixel values from the current profile to a destination profile.
- View > Proof Setup: uses profiles to simulate how the image will look on a specific output device (soft proofing — see below).

**Related concepts:** Working space, device profile, color management, Profile Connection Space, Assign Profile, Convert to Profile.

**Common misconception:** "ICC profiles change the colors in my image." A profile by itself does not change pixel values. It provides the interpretation of those values. Assigning a different profile changes the interpretation (and therefore the appearance), but the numbers in the file remain the same. Converting to a different profile does change the pixel values — it recalculates them so that the appearance is preserved under the new interpretation.

### Gamut

**Definition:** The gamut of a color space, device, or profile is the complete range of colors it can represent or reproduce. It is a three-dimensional volume in color space (commonly visualized as a 2D projection on the CIE chromaticity diagram, but the full gamut includes the lightness dimension as well). No device or color space can represent all visible colors, and every translation between spaces involves a comparison of gamuts.

**Meaning in photographic practice:** Gamut determines what colors survive each step of your pipeline. Your camera sensor has a gamut (the colors it can distinguish). Your working space has a gamut (the colors its numerical range can encode). Your monitor has a gamut (the colors it can physically display). Your printer, with a specific paper and ink combination, has a gamut (the colors it can put on paper). At every handoff — sensor to file, file to screen, file to print — colors outside the destination gamut must be handled somehow, either by compressing the color range or by clipping individual colors to the nearest reproducible value.

**Photoshop implementation:** Photoshop provides gamut warnings via View > Gamut Warning, which overlays a color (default gray) on areas of the image that fall outside the gamut of the current proof profile (set via View > Proof Setup). This is a binary indicator — in or out — and does not show how far out of gamut a color is or how it will be remapped. More nuanced evaluation requires soft proofing (Chapter 21).

**Related concepts:** Out-of-gamut, color space, rendering intent, soft proofing, gamut mapping.

**Common misconception:** "If a color is in the gamut of my working space, my printer can reproduce it." The working space gamut and the printer gamut are different volumes. Adobe RGB contains highly saturated cyans and greens that many printers cannot reproduce. Conversely, some printers can reproduce certain dark, saturated colors (particularly in the yellow-orange region) that fall outside sRGB. Working space gamut and output device gamut overlap but are not identical.

### Out-of-Gamut

**Definition:** A color is out-of-gamut when it falls outside the range of colors that a given device, color space, or profile can represent or reproduce. An out-of-gamut color must be remapped to a reproducible color during conversion — a process controlled by the rendering intent.

**Meaning in photographic practice:** Out-of-gamut colors are inevitable whenever you move from a larger space to a smaller one: from ProPhoto RGB to Adobe RGB, from Adobe RGB to sRGB, from your working space to your printer's gamut. The consequence depends on how many colors are out of gamut, by how much, and which rendering intent is used. A photograph of a sunset in ProPhoto RGB may have saturated oranges and reds that fall well within even sRGB's gamut, while a photograph of a tropical forest may have greens that exceed Adobe RGB's gamut. Out-of-gamut is always relative to a specific destination.

**Photoshop implementation:** View > Gamut Warning highlights out-of-gamut areas relative to the profile configured in View > Proof Setup. The default proof setup is your current working CMYK space, which may not be relevant; you must set the proof profile to your actual output profile (printer + paper) for the warning to be useful. The warning shows only whether pixels are out of gamut, not by how much. Some pixels may be barely outside the gamut boundary (and will be remapped with negligible visible change), while others may be far outside (and will shift noticeably).

**Related concepts:** Gamut, rendering intent, soft proofing, gamut compression, clipping.

**Common misconception:** "Out-of-gamut colors are lost." They are not necessarily lost — they are remapped. The rendering intent determines how. Perceptual rendering compresses the entire color range to fit, preserving relationships between colors. Relative Colorimetric clips individual out-of-gamut colors to the nearest reproducible color. The result depends on the rendering intent and the severity of the gamut mismatch, not on a simple binary of "preserved or lost."

### Rendering Intent

**Definition:** A rendering intent is a strategy for handling colors when converting from one color space or profile to another, particularly when the source gamut exceeds the destination gamut. The ICC specification defines four rendering intents: Perceptual, Relative Colorimetric, Absolute Colorimetric, and Saturation.

**Meaning in photographic practice:** The choice of rendering intent directly affects how out-of-gamut colors are handled during conversion:

- **Perceptual:** Compresses the entire source gamut to fit within the destination gamut, adjusting all colors (not just out-of-gamut ones) to preserve the perceived relationships between colors. This maintains smooth gradations and relative color differences, but shifts even in-gamut colors slightly. Best for images with many out-of-gamut colors (saturated landscapes, vivid florals) where preserving the overall color relationships matters more than absolute accuracy of any single color.

- **Relative Colorimetric:** Leaves in-gamut colors unchanged (or nearly so), and clips out-of-gamut colors to the nearest reproducible color on the gamut boundary. The source white point is mapped to the destination white point. Best for images with few or no out-of-gamut colors, or where accuracy of reproducible colors is more important than preserving relationships among saturated extremes. This is the most commonly used rendering intent for photographic proofing and output.

- **Absolute Colorimetric:** Similar to Relative Colorimetric, but does not remap the white point. It reproduces colors exactly as specified, including simulating the white point of the source on the destination medium. Used primarily for proofing — simulating how a print will look on a specific paper, including the paper's white tint.

- **Saturation:** Prioritizes maintaining vivid, saturated colors over accuracy or perceptual relationships. Rarely used in photography; intended for business graphics (charts, diagrams) where vivid color differentiation matters more than color accuracy.

**Photoshop implementation:** You select a rendering intent whenever you perform a color conversion:

- Edit > Convert to Profile: the "Intent" dropdown.
- View > Proof Setup > Custom: the "Rendering Intent" dropdown for soft proofing.
- File > Print: the rendering intent in the Color Management section (when Photoshop manages colors).

The "Use Black Point Compensation" checkbox (available alongside the rendering intent) maps the darkest value in the source to the darkest value the destination can reproduce. For photographic work, this is almost always left checked — it prevents shadow detail from being crushed in the conversion.

**Related concepts:** Gamut, out-of-gamut, ICC profile, soft proofing (Chapter 21), Convert to Profile, black point compensation.

**Common misconception:** "Perceptual is always best for photographs." Perceptual rendering shifts all colors, not just out-of-gamut ones. For an image where most colors are within the destination gamut, Relative Colorimetric will produce a more accurate result because it preserves in-gamut colors. The best choice depends on the specific image and the specific conversion. Soft proofing both options (Chapter 21) is the professional approach to making this decision.

### Color Management

**Definition:** Color management is the system of profiles, conversions, and policies that ensures consistent and predictable color reproduction across devices with different color characteristics. It relies on ICC profiles to describe each device's color behavior, a Profile Connection Space (PCS) as a device-independent translation hub, and rendering intents to handle gamut differences.

**Meaning in photographic practice:** Without color management, the meaning of your RGB values depends entirely on whatever device happens to be interpreting them. The same file could look correct on your calibrated monitor, oversaturated on your client's uncalibrated laptop, and dull on your print. Color management does not make all devices display the same colors (that is physically impossible, given different gamuts). What it does is ensure that each device reproduces the closest possible approximation to the intended color, given its capabilities, and that translations between devices are predictable and controlled.

A color-managed workflow requires three things: accurate profiles for each device, a consistent working space for editing, and correct conversion settings for each output.

**Photoshop implementation:** Photoshop's color management is configured primarily through Edit > Color Settings. This dialog controls:

- The default working space for each color mode (RGB, CMYK, Gray, Spot).
- Color management policies: what Photoshop does when it encounters a file whose embedded profile does not match the working space (preserve, convert, or discard).
- Profile mismatch warnings: whether Photoshop alerts you when opening or pasting content with mismatched profiles.

Additionally, the operating system's display profile (set via display calibration) tells Photoshop how to translate document colors for your specific monitor. If your monitor profile is inaccurate, your on-screen colors will be wrong regardless of how correctly you configure Photoshop.

**Related concepts:** ICC profile, working space, rendering intent, soft proofing, monitor calibration.

**Common misconception:** "Color management makes everything look the same everywhere." It cannot. A printer with a small gamut cannot reproduce the saturated greens that a wide-gamut monitor can display. Color management ensures that the translation is as accurate as the destination device allows, and that you can predict the result before committing to output (via soft proofing). It does not overcome physical limitations.

### Working Space

**Definition:** A working space (also called an editing space) is an idealized, device-independent color space used as the environment for editing. Unlike device profiles, which describe the idiosyncrasies of a specific monitor or printer, a working space profile defines a mathematically regular, perceptually useful space designed for editing. The three standard photographic working spaces are sRGB, Adobe RGB (1998), and ProPhoto RGB.

**Meaning in photographic practice:** Your working space determines the maximum range of colors your editing pipeline can handle and the numerical characteristics of your editing environment. The choice involves trade-offs:

| Working Space | Gamut | Gamma | 8-bit Safe | Primary Use |
|---------------|-------|-------|------------|-------------|
| sRGB | Small | ~2.2 (piecewise) | Yes | Web-only output |
| Adobe RGB (1998) | Medium | 2.2 (pure) | Yes | General photography, print |
| ProPhoto RGB | Very large | 1.8 | No (use 16-bit) | Maximum data preservation |

The working space does not control what your monitor displays (that is the monitor profile's job) or what your printer outputs (that is the printer profile's job). It controls the space in which your editing math operates.

**Photoshop implementation:** The working space is set in Edit > Color Settings under the "Working Spaces" section. The RGB dropdown lists available working spaces. When you create a new document (File > New), it is created in the current working space. When you open a file whose embedded profile matches the working space, no conversion is needed. When profiles differ, Photoshop follows the policy you set in Color Settings (convert, preserve, or ask).

**Related concepts:** Color space, ICC profile, device profile, Color Settings, gamut.

**Common misconception:** "My working space should match my monitor." No. Your working space is a device-independent editing environment. Your monitor profile describes your monitor's actual behavior and is used by the operating system and Photoshop to translate document colors for accurate on-screen display. These are two separate roles. Your working space might be Adobe RGB while your monitor displays only sRGB — Photoshop uses both profiles to show you an accurate representation of your document colors within the monitor's capabilities.

### Device Profile vs Working Space Profile

**Definition:** A device profile describes the color behavior of a specific physical device — a particular monitor unit, a specific printer with specific inks on a specific paper. It is created by measuring the device's output. A working space profile describes an idealized, mathematically defined color space intended for editing. It is not tied to any device and is identical everywhere.

**Meaning in photographic practice:** This distinction matters because the two types of profiles serve different purposes in the pipeline:

- Your **monitor's device profile** tells Photoshop (via the operating system) how to translate document colors into the signals that will produce the correct colors on your screen. An inaccurate monitor profile means your screen display is wrong, even if everything else is configured correctly.
- Your **printer's device profile** (or more precisely, the profile for your printer + paper + ink combination) tells Photoshop how to translate document colors into the values that will produce the best possible colors on that specific output. It is used during Convert to Profile (for permanent conversion) or soft proofing (for preview).
- Your **working space profile** defines the editing environment. It affects what your RGB numbers mean during editing, and determines the gamut boundary of your working file.

All three are ICC profiles in the same file format, but they encode different kinds of information and serve different roles.

**Photoshop implementation:** Device profiles are managed by the operating system (for monitors) or installed in the system's profile directory (for printers). Working space profiles are built into Photoshop and appear in the Color Settings dialog. The distinction becomes operationally critical in Edit > Convert to Profile, where you might convert from a working space profile (source) to a device profile (destination) when preparing a file for a specific output device.

**Related concepts:** ICC profile, working space, monitor calibration, soft proofing, Convert to Profile.

**Common misconception:** "I should set my Photoshop working space to my monitor's profile." This is a common and damaging mistake. If you set your working space to your monitor profile, you are editing in a space that describes your monitor's quirks — its non-uniformities, its gamut limitations, its specific white point. Your documents become device-dependent. If you open them on a different computer, the colors will be wrong. Working spaces are device-independent for a reason: they provide a consistent, portable editing environment.

### Soft Proofing

**Definition:** Soft proofing is the process of using Photoshop's display engine to simulate on screen how an image will appear when reproduced on a specific output device (typically a printer with a specific paper). It uses the output device's ICC profile and the selected rendering intent to preview the color transformations that will occur during the conversion, without actually altering the document's pixel data.

**Meaning in photographic practice:** Soft proofing is the photographer's tool for previewing the gap between what the image looks like in the working space and what it will look like in print. A highly saturated green that looks vivid on a wide-gamut monitor may be beyond the printer's gamut. Soft proofing reveals this before you print, allowing you to make targeted adjustments (reduce saturation in that area, shift the hue, or accept the compromise) rather than discovering the problem on paper.

Soft proofing is introduced here as a concept; the full practical workflow — setting up proof conditions, comparing rendering intents, making proof-aware adjustments — is covered in Chapter 21.

**Photoshop implementation:** View > Proof Setup > Custom opens the soft proof configuration dialog, where you select the output profile (the ICC profile of your printer + paper combination), the rendering intent, and options such as black point compensation and paper color simulation. View > Proof Colors (Ctrl/Cmd+Y) toggles the soft proof preview on and off. When soft proofing is active, the title bar indicates the proof profile. The display simulates the output, but the document data remains in the working space.

**Related concepts:** ICC profile, rendering intent, gamut, out-of-gamut, View > Gamut Warning, Chapter 21.

**Common misconception:** "Soft proofing shows exactly what the print will look like." Soft proofing is an approximation. Its accuracy depends on your monitor's calibration accuracy, the accuracy of the printer profile, the quality of the profile's gamut mapping tables, and the ambient lighting conditions under which you view the proof. It is a valuable prediction tool — far better than printing blind — but it is not a pixel-perfect preview of the final print.

### Assign Profile vs Convert to Profile

**Definition:** These are two fundamentally different operations in Photoshop that both involve ICC profiles but do entirely different things to a document:

- **Assign Profile** (Edit > Assign Profile) changes the ICC profile associated with the document without altering any pixel values. The numbers in the file remain identical. Because a different profile interprets the same numbers differently, the appearance of the image changes (often dramatically).

- **Convert to Profile** (Edit > Convert to Profile) transforms the pixel values in the document so that the image appears the same (or as close as possible) under the new profile. The numbers change; the appearance is preserved (within the limits of the destination gamut and rendering intent).

**Meaning in photographic practice:** This is one of the most critical distinctions in color management:

- **Assign Profile** answers: "These numbers were meant to be interpreted under this profile." Use it when a file has the wrong profile attached (or no profile), and you know what profile the values were originally created for. For example: you receive a JPEG with no embedded profile that you know was created in Adobe RGB. Assigning Adobe RGB correctly interprets the values.
- **Convert to Profile** answers: "I want this image to look the same, but live in a different color space." Use it when you need to move an image from one working space to another, or when preparing output (converting from your working space to a printer profile or to sRGB for web delivery).

Confusing the two is one of the most common and most destructive color management errors. Assigning a new profile when you should have converted changes the appearance of every color in the image. Converting when you should have assigned performs an unnecessary transformation on values that were already correct under the new profile.

**Photoshop implementation:**

- Edit > Assign Profile presents the options: "Don't Color Manage This Document," "Working RGB" (assigns the current working space), or "Profile" (lets you select any installed profile). Preview checkbox shows the effect before committing. The pixel values in the file do not change.
- Edit > Convert to Profile presents the source profile (current document profile), destination profile, conversion engine, and rendering intent. "Use Black Point Compensation" and "Use Dither" are available as options. The pixel values in the file are recalculated.

**Related concepts:** ICC profile, working space, color management, rendering intent.

**Common misconception:** "Assign Profile and Convert to Profile do the same thing — they both change the profile." They produce opposite outcomes. Assign Profile changes interpretation, preserving numbers but changing appearance. Convert to Profile changes numbers, preserving appearance. Applying Assign Profile when you meant Convert to Profile (or vice versa) produces a visibly wrong result. If you are uncertain which to use, ask yourself: "Should the image look the same after this operation, or should the numbers stay the same?" If the image should look the same: Convert. If the numbers should stay the same: Assign.

## Theory

### How Color Spaces Map Numbers to Colors

A color space defines a precise relationship between numerical triplets and measurable colors. This relationship has three components:

1. **Primaries:** The three specific colors (red, green, blue in an RGB space) that form the vertices of the space's gamut triangle. The primaries determine which colors can be produced by mixing. sRGB's primaries are less saturated than Adobe RGB's, which are less saturated than ProPhoto RGB's — hence the increasing gamut sizes.

2. **White point:** The color of "white" in the space — the color produced when all three channels are at maximum. sRGB and Adobe RGB use D65 (a standard daylight illuminant at approximately 6500K). ProPhoto RGB uses D50 (approximately 5000K, often used as a reference in printing and graphic arts).

3. **Transfer function (gamma):** The mathematical relationship between encoded values and light intensity. sRGB uses a piecewise function (linear near zero, power curve with exponent near 2.4 for the rest, producing an end-to-end response close to gamma 2.2). Adobe RGB uses a pure gamma of 2.2. ProPhoto RGB uses a gamma of 1.8.

These three components together create a complete mapping: for any RGB triplet, the color space defines exactly which CIE XYZ color it corresponds to. Conversely, for any visible color (within the gamut), the color space defines the RGB values that represent it.

**FACT:** The RGB values (0, 255, 0) mean "maximum green, no red, no blue" in any RGB space. But the actual green produced is different in each space: sRGB green is a moderately saturated green; Adobe RGB green is more saturated; ProPhoto RGB green is extremely saturated — more saturated than any real surface color, reaching into the territory of spectral green.

### Why the Same RGB Values Mean Different Colors

Consider the value (100, 200, 100) in three different spaces:

In sRGB, this represents a medium-brightness, moderately saturated green — the kind of green you might see in well-lit grass.

In Adobe RGB, the same numbers (100, 200, 100) represent a more saturated green, because Adobe RGB's green primary is more saturated than sRGB's. The "distance" each unit covers in terms of color saturation is larger.

In ProPhoto RGB, the same numbers represent an even more saturated green — possibly more saturated than any physical surface. ProPhoto RGB's vast gamut means that each numerical step corresponds to a larger change in actual color.

This is why profile information is essential. Without knowing the profile, the numbers (100, 200, 100) are ambiguous — they could represent any of these greens, or others in different spaces.

**INTERPRETATION:** Think of it like coordinates on a map. The numbers (40, 50) mean completely different locations depending on whether you are using UTM Zone 33N or WGS84 latitude/longitude. The coordinates are meaningless without the coordinate system. RGB values are meaningless without the color space.

### Gamut Comparison

The three standard photographic working spaces form a rough hierarchy of gamut size:

sRGB < Adobe RGB (1998) < ProPhoto RGB

This is an approximation. The spaces are not strict subsets in every color region — the gamut boundaries have different shapes and the comparison depends on lightness level. But the general relationship holds:

- **sRGB** encompasses approximately the gamut of a typical consumer monitor. It is the smallest of the three.
- **Adobe RGB (1998)** is approximately 50% larger than sRGB (as measured in CIE Lab volume). The expansion is primarily in the cyan-green region, with some expansion in the yellows.
- **ProPhoto RGB** is approximately 90% larger than Adobe RGB. It extends well beyond the range of visible color in some regions — meaning it can encode "colors" that no human can perceive.

**FACT:** The CIE chromaticity diagram represents these spaces as triangles. sRGB's triangle fits inside Adobe RGB's in most regions. Adobe RGB's fits inside ProPhoto RGB's. The spectral locus (the horseshoe boundary of all visible colors) falls between Adobe RGB's triangle and ProPhoto RGB's in many regions — meaning ProPhoto RGB extends beyond visible color.

**RECOMMENDATION:** For a practical workflow decision:

- If your output is exclusively web/screen: edit in Adobe RGB, convert to sRGB for delivery.
- If your output includes print: edit in Adobe RGB. This covers the gamut of most inkjet papers.
- If you want maximum data preservation and are disciplined about 16-bit workflow and soft proofing: edit in ProPhoto RGB.
- Do not edit in ProPhoto RGB at 8-bit. The gamut is too large for 256 values per channel to represent smoothly.

### When to Use Which Working Space

The choice of working space is a trade-off between gamut (preserving color data) and practicality (bit depth requirements, risk of out-of-gamut accidents).

**sRGB as working space:**

- Appropriate when the final output is exclusively web or screen, and no print output is anticipated.
- Advantage: simple, universally understood, no gamut surprises.
- Disadvantage: clips colors your camera captured. Once clipped, they cannot be recovered.

**Adobe RGB (1998) as working space:**

- Appropriate for the majority of photographic workflows, including print.
- Advantage: captures nearly all colors that inkjet printers reproduce. Safe at 8-bit. Widely supported.
- Disadvantage: clips the most extremely saturated colors some sensors capture, particularly in the deep greens and violets.

**ProPhoto RGB as working space:**

- Appropriate when maximum color preservation is the priority, for archival masters, or for output to the widest-gamut devices.
- Advantage: preserves virtually all captured color data. Future-proofs against wider-gamut output devices.
- Disadvantage: requires 16-bit. Easy to create imaginary colors. Demands soft proofing discipline for every output.

**RECOMMENDATION:** There is no single "correct" answer. The choice depends on your output chain, your discipline, and your needs. Adobe RGB is the pragmatic default for most photographers. If you choose ProPhoto RGB, commit to 16-bit editing and regular soft proofing. Never use ProPhoto RGB at 8-bit.

### The Color Settings Dialog

Edit > Color Settings is the central configuration point for Photoshop's color management. Understanding its sections is essential.

**Working Spaces:**

The RGB dropdown sets the default working space for new documents and for conversions that target the "Working RGB." The most relevant options for photographers are sRGB, Adobe RGB (1998), and ProPhoto RGB.

**Color Management Policies:**

These three dropdowns (RGB, CMYK, Gray) control what Photoshop does when it encounters a document whose embedded profile differs from the working space. The options for each are:

- **Off:** Photoshop ignores embedded profiles. This effectively disables color management for incoming files and is almost never appropriate for photographic work.
- **Preserve Embedded Profiles:** Photoshop keeps the document in its embedded space without converting. The document retains its original profile. This is the safest default — it respects the intent of whoever created the file.
- **Convert to Working RGB:** Photoshop automatically converts incoming documents to the working space. This ensures all documents end up in the same space but alters pixel values during the conversion.

**RECOMMENDATION:** For photographic work, set RGB policy to "Preserve Embedded Profiles" and check all three mismatch/missing profile warning checkboxes ("Ask When Opening," "Ask When Pasting," and "Missing Profiles: Ask When Opening"). This ensures Photoshop alerts you when a decision is needed, rather than silently applying a default.

**Profile Mismatches — Ask When Opening:**

When this is checked and you open a file whose profile differs from the working space, Photoshop presents a dialog with three options:

- Use the embedded profile (open the document in its original space).
- Convert the document's colors to the working space.
- Discard the embedded profile (treat the values as if they are in the working space — equivalent to assigning the working space profile).

The correct choice depends on context. If you trust that the file was created with the correct embedded profile, preserve it. If you need the document in your working space for consistency, convert. Do not discard profiles unless you have a specific reason and understand the consequences.

**Conversion Options (under "More Options"):**

- **Engine:** The color management module (CMM) used for conversions. Adobe (ACE) is the standard for Photoshop work.
- **Intent:** The default rendering intent for conversions. Relative Colorimetric is the typical default.
- **Use Black Point Compensation:** Maps the source black point to the destination black point. Leave this checked for photographic work.
- **Use Dither:** Adds a small amount of dithering during conversions to reduce banding. Leave this checked.

**FACT:** The "Blend RGB Colors Using Gamma" option (available under "More Options" in some versions) affects how Photoshop blends colors in compositing operations. Setting this to 1.00 forces linear-light blending. This is relevant for compositing and was introduced in Chapter 01.

## Photoshop Implementation

### Setting Up Color Settings

The recommended configuration for photographic editing:

1. Open Edit > Color Settings (Ctrl/Cmd+Shift+K).
2. Under "Settings," the dropdown may show presets. For manual configuration, any preset serves as a starting point.
3. Set **Working Spaces > RGB** to Adobe RGB (1998) (or ProPhoto RGB if you have committed to a 16-bit workflow).
4. Set **Color Management Policies > RGB** to "Preserve Embedded Profiles."
5. Check all three warning checkboxes: "Profile Mismatches: Ask When Opening," "Profile Mismatches: Ask When Pasting," "Missing Profiles: Ask When Opening."
6. Under "Conversion Options" (click "More Options" if this section is not visible): Engine = Adobe (ACE), Intent = Relative Colorimetric, check "Use Black Point Compensation," check "Use Dither."
7. Click OK.

**FACT:** These settings persist across sessions until you change them. They apply to new documents and influence how Photoshop handles profile mismatches, but they do not retroactively change existing open documents.

### Checking a Document's Color Space

The current document's color profile is displayed in the title bar (by default) and in the status bar at the bottom of the document window. You can also check it via Edit > Assign Profile (which shows the current profile) or Image > Mode (which shows the color mode and bit depth but not the specific profile).

To configure the document status bar: click the arrow at the bottom-left of the document window and select "Document Profile" to display the profile name.

### Converting Between Color Spaces

When you need to move a document from one color space to another — for example, converting from Adobe RGB to sRGB for web delivery:

1. Open Edit > Convert to Profile.
2. The "Source Space" shows the current profile.
3. Under "Destination Space," select the target profile (e.g., sRGB IEC61966-2.1).
4. Select the rendering intent (Relative Colorimetric for most conversions; Perceptual if many colors are out of gamut in the destination).
5. Ensure "Use Black Point Compensation" is checked.
6. Ensure "Use Dither" is checked.
7. Use the Preview checkbox to compare before and after.
8. Click OK.

**FACT:** This operation recalculates every pixel value in the document. It is destructive in the sense that the original values are replaced. Work on a copy if you need to preserve the original-space version, or convert only at the final output stage.

### Handling Profile Mismatches on Open

When you open a file whose embedded profile does not match your working space (and you have the mismatch warnings enabled), Photoshop presents the Embedded Profile Mismatch dialog. The three options:

- **Use the embedded profile (instead of the working space):** The document opens in its original space. No pixel values are changed. This is the safest choice when you do not know the file's history.
- **Convert document's colors to the working space:** Photoshop recalculates the values to preserve the appearance in your working space. Appropriate when you want all documents in the same space for consistent editing.
- **Discard the embedded profile (don't color manage):** The document opens with no profile, and Photoshop interprets the values using the working space. This is equivalent to Assign Profile with the working space. Use only when you know the embedded profile is wrong and the values should be interpreted in your working space.

### Handling Missing Profiles

When you open a file with no embedded profile (and "Missing Profiles: Ask When Opening" is checked), Photoshop presents three options:

- **Leave as is (don't color manage):** Photoshop treats the values according to the working space for display purposes but does not assign a profile. The document remains unmanaged.
- **Assign working RGB:** Assigns the working space profile to the document. The values are interpreted as if they were created in your working space.
- **Assign profile:** Lets you select any installed profile. Use this when you know the original intended space.

**RECOMMENDATION:** For files with missing profiles, try assigning the most likely profile (sRGB for web-sourced images, Adobe RGB for images from photography-oriented sources) and use the Preview checkbox to see whether the colors look correct. If the image looks correct with a given assignment, that profile is likely the one under which the values were created.

### The Assign Profile Dialog

Edit > Assign Profile presents:

- **Don't Color Manage This Document:** Removes any profile association.
- **Working RGB:** Assigns the current working space profile.
- **Profile:** Select from any installed profile.

Preview the effect with the Preview checkbox. Remember: no pixel values change. Only the interpretation changes. If the image suddenly looks too saturated or desaturated, the assigned profile is probably not the correct one.

### The Convert to Profile Dialog

Edit > Convert to Profile presents:

- **Source Space:** The current profile (read-only).
- **Destination Space:** The target profile (select from the dropdown).
- **Conversion Options:** Engine, Intent, Black Point Compensation, Use Dither, Flatten Image to Preserve Appearance.

The "Flatten Image to Preserve Appearance" option is relevant when working with layers and converting between profiles whose gamut mapping might affect layer blending calculations differently. For most photographic work, leaving this unchecked preserves your layer structure.

## Professional Workflow

### Working Space Selection as a Pipeline Decision

Your working space should be chosen once and used consistently throughout your editing pipeline. Switching working spaces mid-project forces conversions that degrade data. The decision depends on your output:

1. **Web-only photographer (social media, client galleries):** Adobe RGB as working space, convert to sRGB at export. This preserves more color data during editing while ensuring correct web delivery.

2. **Print photographer (fine art, exhibition, client prints):** Adobe RGB as working space. This covers the gamut of most inkjet papers. Soft proof against your specific printer + paper profile before printing (Chapter 21).

3. **Archival / maximum-quality workflow:** ProPhoto RGB at 16-bit as working space. Convert to the appropriate output space (sRGB for web, printer profile for print) at the final stage. This demands discipline: always 16-bit, always soft proof, always convert at the end.

### The Export Conversion Step

Regardless of your working space, final output almost always requires a conversion:

- For web delivery: Convert to Profile > sRGB. This ensures correct display on uncalibrated devices and in non-color-managed applications (most web browsers handle embedded sRGB profiles correctly, but many ignore non-sRGB profiles).
- For print: the conversion happens either explicitly (Convert to Profile with the printer profile as destination) or implicitly (when Photoshop or the printer driver manages the conversion at print time). The better practice is to let Photoshop manage colors and use soft proofing to preview the result — this is covered in Chapter 21.

**RECOMMENDATION:** Maintain your master files in your working space (Adobe RGB or ProPhoto RGB). Create output copies for each destination. Never convert your master file to sRGB and save over the original — you cannot recover the clipped gamut.

### Receiving Files from External Sources

When you receive files from clients, agencies, or other photographers, profile handling is your first diagnostic step:

1. **File has an embedded profile that matches your working space:** No action needed. Open normally.
2. **File has an embedded profile that does not match:** Choose between preserving the embedded profile (if you trust it) or converting to your working space (if you need consistency). Do not discard the profile.
3. **File has no embedded profile:** Investigate. Web-sourced images are most likely sRGB. Images from professional sources may be Adobe RGB. Try assigning likely profiles and check the result visually. Assign — do not convert — until you have established the correct interpretation.

## Common Mistakes

**Setting the working space to the monitor profile.** This makes your editing device-dependent. Your documents will look wrong on any other monitor. Working spaces exist specifically to avoid this problem. Your monitor profile is used by the display system, not as an editing space.

**Confusing Assign Profile with Convert to Profile.** Assigning a new profile when you should have converted produces an image where every color is wrong. Converting when you should have assigned introduces an unnecessary (and incorrect) transformation. The rule: if the appearance should stay the same, Convert. If the numbers should stay the same, Assign.

**Working in ProPhoto RGB at 8-bit.** ProPhoto RGB's vast gamut spread across only 256 values per channel results in large perceptual steps between adjacent values. Smooth gradients posterize. Always use 16-bit with ProPhoto RGB.

**Ignoring profile mismatch warnings.** When Photoshop asks about a profile mismatch, it is asking because the answer matters. Clicking "Don't show again" or blindly accepting the default can result in images being silently converted or misinterpreted. Read the dialog. Understand the options. Choose deliberately.

**Assuming sRGB is adequate because "my images look fine on screen."** Your monitor may display sRGB or close to it. But if your camera captured colors outside sRGB and you converted to sRGB early in your pipeline, those colors are gone. You may not notice on your sRGB monitor, but the data loss becomes visible on a wide-gamut display or in print on high-quality papers.

**Using Gamut Warning without setting the correct proof profile.** The default proof profile in View > Proof Setup may not be your actual output device. Gamut Warning against the wrong profile gives you meaningless information. Always configure Proof Setup to your intended output before checking gamut.

**Converting to a printer profile and then printing with the printer driver also managing colors.** This applies the color transformation twice ("double profiling"), producing wrong colors. Either Photoshop manages colors (and the printer driver is set to "no color management") or the printer driver manages colors (and Photoshop sends the data without conversion). Never both. This is detailed in Chapter 21.

## Summary

Color management is the system that gives meaning to RGB numbers. Without it, the triplet (180, 120, 90) is ambiguous — it represents a different color in every color space. With it, the numbers are anchored to measurable, reproducible colors through ICC profiles.

A color model (RGB) defines the structure — three channels, additive mixing. A color space (sRGB, Adobe RGB, ProPhoto RGB) defines the specific mapping from values to colors, via its primaries, white point, and transfer function. Each space has a gamut — the range of colors it can encode. sRGB is small, Adobe RGB is medium, ProPhoto RGB is very large. Colors outside a space's gamut are out-of-gamut and must be handled through rendering intents during conversion.

The four rendering intents — Perceptual, Relative Colorimetric, Absolute Colorimetric, and Saturation — define different strategies for handling gamut mismatches. Perceptual compresses everything to preserve relationships. Relative Colorimetric preserves in-gamut colors and clips the rest. For photographic work, Relative Colorimetric is the default; Perceptual is the alternative for highly saturated images. The choice depends on the specific image and conversion.

Assign Profile reinterprets existing values under a new profile (numbers stay, appearance changes). Convert to Profile recalculates values to preserve appearance under a new profile (numbers change, appearance stays). Confusing these two operations is one of the most damaging errors in color management.

Your working space — configured in Edit > Color Settings — determines the editing environment for your pipeline. Adobe RGB is the pragmatic choice for most photographers. ProPhoto RGB preserves maximum data but demands 16-bit precision and disciplined soft proofing. sRGB is adequate only for web-exclusive workflows.

Soft proofing (covered in depth in Chapter 21) allows you to preview how your image will look on a specific output device before committing to the conversion. It is the bridge between your working space and your final output.

These concepts are not academic. They determine whether your prints match your screen, whether your web exports look correct on clients' devices, whether files from external sources open with correct colors, and whether your editing pipeline preserves or discards the colors your camera captured.

## Exercises

### Exercise 1: Profile Awareness

Open three different image files — one from a web download, one from your own camera (converted from RAW), and one from a colleague or stock library. For each:

1. What profile is embedded? (Check the document title bar or Edit > Assign Profile.)
2. Is the profile the same as your working space? If not, what did Photoshop do (or offer to do) when you opened it?
3. If the file has no embedded profile, which profile is Photoshop assuming? How do you determine whether that assumption is correct?

### Exercise 2: Assign Profile vs Convert to Profile

Open a photograph in Adobe RGB. Duplicate it (Image > Duplicate). Now perform two operations:

- On the original: Edit > Assign Profile and select sRGB. Observe the change.
- On the duplicate: Edit > Convert to Profile and select sRGB as the destination. Observe the change.

1. Which operation changed the appearance? Which preserved it?
2. Which operation changed the pixel values? Verify by sampling the same pixel location with the Info panel in both documents.
3. In which document are the colors now "correct" for sRGB output? Explain why.
4. Undo the Assign on the original (Ctrl/Cmd+Z). Now, under what circumstances would Assign Profile be the correct operation to perform?

### Exercise 3: Gamut Warning Exploration

Open a photograph with vivid, saturated colors (a sunset, autumn foliage, or tropical flowers work well). Configure View > Proof Setup > Custom to use sRGB as the proof profile with Relative Colorimetric intent.

1. Toggle View > Gamut Warning (Ctrl/Cmd+Shift+Y). Which areas are out of gamut?
2. Now change the proof profile to Adobe RGB. How does the out-of-gamut area change?
3. For the sRGB proof, switch the rendering intent to Perceptual. Does the gamut warning change? (Consider: why or why not — gamut warning is about the gamut boundary, not the rendering strategy.)
4. Toggle View > Proof Colors (Ctrl/Cmd+Y) to see the soft proof. Compare with the unproofed view. Can you see the color shifts that the gamut warning was indicating?

### Exercise 4: Color Settings Configuration

Open Edit > Color Settings. Document your current configuration:

1. What is your current RGB working space?
2. What is the color management policy for RGB?
3. Are profile mismatch warnings enabled?
4. Under "More Options" (if visible): what engine, intent, and black point compensation settings are configured?
5. Based on what you learned in this chapter, would you change any of these settings? What would you change, and why?

## Advanced Exercises

### Advanced Exercise 1: Numerical Impact of Color Space

Open a colorful photograph in Adobe RGB (16-bit). Place four color samplers (Color Sampler tool, nested under the Eyedropper) on: a saturated green area, a saturated blue area, a neutral midtone (gray), and a skin tone.

1. Record the RGB values at all four sampler points.
2. Convert to Profile: sRGB. Record the new RGB values.
3. Which sampler changed the most? Which changed the least? Explain why, based on where sRGB's gamut boundary differs most from Adobe RGB's.
4. Undo the conversion. Now Convert to Profile: ProPhoto RGB. Record the values. Which direction did the values shift compared to Adobe RGB, and why? (Hint: think about what happens to the same perceptual color when the gamut boundaries expand — the values must move toward the center of the numerical range.)

### Advanced Exercise 2: Rendering Intent Comparison

Open an image with significant out-of-gamut content (relative to sRGB). Set up soft proofing (View > Proof Setup > Custom) with sRGB as the destination. Perform two conversions:

1. Duplicate the document. Convert one copy to sRGB using Relative Colorimetric. Convert the other to sRGB using Perceptual.
2. Place both documents side by side. Where do they differ? Where do they look the same?
3. Sample matching pixel locations with the Info panel. In the in-gamut areas, which conversion preserved the original values more accurately? In the previously out-of-gamut areas, which conversion produced more natural-looking results?
4. Which conversion would you choose for this specific image, and why? Is there a different image where you would make the opposite choice?

### Advanced Exercise 3: Diagnosing an Unknown File

A colleague sends you a TIFF with no embedded profile. The image looks desaturated and slightly warm when you open it (Photoshop interprets it in your working space).

1. Without changing any pixel values, try assigning different profiles: sRGB, Adobe RGB, ProPhoto RGB. Under which assignment does the image look most natural?
2. Explain what is happening in each case. Why does assigning ProPhoto RGB to a file created in sRGB make it look desaturated? Why does assigning sRGB to a file created in Adobe RGB make it look oversaturated?
3. Once you have identified the correct profile (by visual assessment), what is the correct sequence of operations to bring this file into your working space while preserving its intended appearance?

## Blackbelt Challenge

You are preparing a series of landscape photographs for two simultaneous outputs: a gallery exhibition (large-format inkjet prints on a matte fine-art paper) and a web portfolio (sRGB, displayed on consumer devices).

Your working files are in ProPhoto RGB, 16-bit. The landscape images include highly saturated autumn foliage (deep reds and oranges), a twilight sky (deep blues and purples), and a tropical coastline (vivid turquoise water).

Without opening Photoshop, answer the following:

1. Which of the three images is most likely to have out-of-gamut problems when converting to sRGB for web? Which is most likely to have problems when converting to the printer's gamut? Are the problem areas the same for both conversions? Explain your reasoning.

2. You need to choose a rendering intent for the sRGB web conversion and separately for the print output. Would you use the same intent for both? Under what circumstances would you choose differently for web vs print, and why?

3. Your print lab sends you their ICC profile for your specific paper. When you soft-proof the autumn foliage image, the deep reds look noticeably less saturated in the proof. You have three options: (a) increase saturation in the master file to compensate, (b) create a separate adjustment layer visible only when printing, (c) accept the limitation. Analyze the trade-offs of each approach. Which approach (or combination) represents best practice, and why?

4. A client downloads one of your web portfolio images (sRGB JPEG) and opens it in their Photoshop, which is configured with ProPhoto RGB as the working space and the policy set to "Convert to Working RGB." What happens to the image? Are the colors preserved, damaged, or unchanged? What would happen differently if their policy were "Preserve Embedded Profiles"?

This challenge has no single correct answer set. The quality of your response depends on your ability to trace the consequences of color management decisions through the full pipeline from working space to output.
