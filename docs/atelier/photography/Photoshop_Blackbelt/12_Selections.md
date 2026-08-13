---
description: Selection tools and techniques — from geometric marquees to AI-driven subject isolation
---

# Chapter 12: Selection Tools and Techniques

## 12.1 Learning Objectives

After completing this chapter you will be able to:

- Explain what a selection is in terms of underlying grayscale data and why marching ants are a limited representation of it.
- Classify selection tools into four families (geometric, edge-based, tone/color-based, AI-based) and choose the right one for a given task.
- Use Marquee, Lasso, Magic Wand, Quick Selection, Object Selection, and Select Subject with confident awareness of each tool's strengths and failure modes.
- Combine selections using Add, Subtract, and Intersect modes to build complex isolation masks.
- Refine a selection in the Select and Mask workspace, controlling edge detection, feathering, contrast, and decontamination.
- Articulate when to select first versus painting a mask directly, and convert fluently between the two.
- Complete a professional subject-isolation workflow from initial selection through final output.

---

## 12.2 Conceptual Foundation

A selection is a temporary mask that defines an area of effect.

Every editing operation in Photoshop — brushstroke, adjustment, filter, transform — can be constrained to act on only part of an image. The mechanism that defines "which part" is the selection. Strip away every visual cue Photoshop provides — the marching ants, the tool overlays, the property panels — and what remains is a grayscale map where white means "fully affected," black means "fully protected," and every gray value in between means "partially affected."

If you completed Chapter 05 (Channels and Masks), this should sound familiar. A selection and a layer mask encode the same information: a per-pixel opacity value between 0 and 255. The difference is persistence. A mask is saved with the document. A selection is transient — it exists only while active, and it vanishes when you deselect (Ctrl+D / Cmd+D) unless you explicitly save it as an alpha channel or convert it to a mask.

This chapter focuses on the tools that create selections. Think of these tools as different strategies for answering the same question: "Which pixels do I want to affect?" Some tools answer geometrically ("a rectangle this size"), some answer by tracing edges ("follow this boundary"), some answer by analyzing tone or color ("every pixel that looks like this"), and some answer by machine-learning inference ("that is a person — select them"). Mastering selections means knowing which strategy to deploy for which problem.

---

## 12.3 Terminology

### Selection

| Aspect | Detail |
|---|---|
| **Definition** | A temporary, per-pixel opacity map that constrains the area of effect for subsequent editing operations. |
| **Meaning in photographic practice** | The photographer's way of saying "I want to adjust only this region." Every local adjustment — dodging a face, saturating a sky, sharpening an eye — begins with defining where the adjustment should land. The selection is that definition. |
| **Photoshop implementation** | Stored internally as a grayscale channel (visible via Quick Mask mode, Q). Created by selection tools, menu commands (Select menu), or by Ctrl/Cmd-clicking a channel or mask thumbnail. Persists only while active; saving requires Select > Save Selection or conversion to a layer mask or alpha channel. |
| **Related concepts** | Mask (Ch 05), alpha channel (Ch 05), Quick Mask mode, marching ants, area of effect. |
| **Common misconception** | "A selection is binary — pixels are either selected or not." False. Selections are grayscale. A pixel can be 47% selected, meaning it receives 47% of whatever operation follows. The marching ants boundary only shows the 50% threshold, hiding the gradient nature of most useful selections. |

### Marching Ants

| Aspect | Detail |
|---|---|
| **Definition** | The animated dashed border Photoshop displays to indicate the boundary of an active selection, officially called the "selection border" or "selection edges." |
| **Meaning in photographic practice** | A visual confirmation that a selection is active. Photographers use marching ants as a rough guide to where their adjustment will land, but must understand that the ants show only one contour line of a potentially complex gradient. |
| **Photoshop implementation** | Displayed automatically when a selection is active. The ants trace the contour where selection opacity equals exactly 50%. Toggle visibility with Ctrl+H / Cmd+H (View > Extras). Hiding the ants does not deactivate the selection. |
| **Related concepts** | Selection, Quick Mask mode, feathering, anti-aliasing. |
| **Common misconception** | "The marching ants show the exact boundary of my selection." They do not. If you feather a selection by 40 pixels, the actual transition zone extends well beyond the ants in both directions. The ants mark only where the selection crosses 50%. To see the true selection shape, enter Quick Mask mode (Q) or use Select > Save Selection to view it as a channel. |

### Feathering

| Aspect | Detail |
|---|---|
| **Definition** | A gradual transition at the edge of a selection, where opacity falls from fully selected to fully unselected over a specified distance (measured in pixels). |
| **Meaning in photographic practice** | Feathering prevents hard-edged adjustments from looking artificial. When dodging a face or warming a sky, a feathered selection creates the smooth, invisible transition that separates professional work from obvious manipulation. The appropriate feather radius depends on image resolution and the nature of the boundary. |
| **Photoshop implementation** | Applied in two ways: (1) Before creating the selection — set the Feather value in the Options Bar for Marquee and Lasso tools. (2) After creating the selection — use Select > Modify > Feather (Shift+F6). Feathering applies a Gaussian blur to the selection's grayscale data. A 10-pixel feather on a 300 PPI image spans roughly 0.85 mm of print space. |
| **Related concepts** | Anti-aliasing, Gaussian blur, Refine Edge, Select and Mask workspace. |
| **Common misconception** | "Feathering and anti-aliasing are the same thing." They are not. Anti-aliasing smooths only the immediate pixel boundary (typically 1-2 pixels) to prevent jagged stair-stepping. Feathering creates a much broader transition zone — potentially dozens or hundreds of pixels wide. They serve different purposes and can be applied independently. |

### Anti-aliasing

| Aspect | Detail |
|---|---|
| **Definition** | A technique that smooths the jagged, stair-stepped edges (aliasing artifacts) of a selection by partially selecting pixels along curved or diagonal boundaries. |
| **Meaning in photographic practice** | When cutting out a subject or compositing elements, anti-aliased edges blend naturally against any background. Without anti-aliasing, curved edges show visible pixel staircases — a hallmark of amateur compositing work. |
| **Photoshop implementation** | Enabled via a checkbox in the Options Bar for Lasso, Magic Wand, and other selection tools. When active, pixels at the selection boundary receive intermediate opacity values (partial selection) instead of a hard 0-or-255 assignment. Unlike feathering, anti-aliasing cannot be applied after the selection is made — it must be set before creating the selection. |
| **Related concepts** | Feathering, aliasing, stair-stepping, sub-pixel rendering. |
| **Common misconception** | "Anti-aliasing makes edges blurry." It does not create visible softness. The transition zone is only 1-2 pixels wide — imperceptible at print resolution. Disabling anti-aliasing does not produce "sharper" selections; it produces jagged ones. |

### Marquee Tools

| Aspect | Detail |
|---|---|
| **Definition** | A family of selection tools that create selections based on geometric shapes: Rectangular Marquee (M), Elliptical Marquee, Single Row Marquee, and Single Column Marquee. |
| **Meaning in photographic practice** | Used when the region of interest aligns with a geometric shape — selecting a rectangular crop area, creating a circular vignette, or isolating a single row of pixels for panoramic distortion analysis. Marquee tools are rarely the final answer for subject isolation, but they are precise, predictable, and fast for geometric tasks. |
| **Photoshop implementation** | Access via M (press Shift+M to cycle between Rectangular and Elliptical). Hold Shift while dragging to constrain to square or circle. Hold Alt/Option to draw from center. The Options Bar offers Style settings: Normal (freeform), Fixed Ratio, and Fixed Size. Feather and anti-aliasing (Elliptical only) are set before drawing. |
| **Related concepts** | Crop tool (geometric but destructive), Transform Selection (Select > Transform Selection). |
| **Common misconception** | "The Rectangular Marquee always starts from a corner." It does by default, but holding Alt/Option starts it from the center — essential when selecting around a centered subject. |

### Lasso Tools

| Aspect | Detail |
|---|---|
| **Definition** | A family of freeform selection tools: Lasso (L, freehand drawing), Polygonal Lasso (straight-line segments between clicks), and Magnetic Lasso (snaps to contrast edges while drawing). |
| **Meaning in photographic practice** | The Lasso family handles irregular shapes where geometric tools fail. The freehand Lasso suits rough isolation for local adjustments where precision is unnecessary. The Polygonal Lasso excels at architectural edges and product shots with straight boundaries. The Magnetic Lasso attempts to automate edge-following against high-contrast boundaries. |
| **Photoshop implementation** | Access via L (Shift+L to cycle). Lasso: click-drag freehand, release to close. Polygonal Lasso: click vertices, double-click or return to start to close. Magnetic Lasso: click once to start, move along edge (it places anchor points), click to force an anchor, double-click to close. Magnetic Lasso Options Bar controls: Width (detection zone), Contrast (edge sensitivity), Frequency (anchor point density). |
| **Related concepts** | Pen tool (for precise paths that can become selections), Quick Selection, edge detection. |
| **Common misconception** | "The Magnetic Lasso is unreliable and should be avoided." It is context-dependent. Against clean, high-contrast edges (a dark product on a white background), the Magnetic Lasso works well when Width and Contrast are tuned appropriately. It fails against low-contrast or busy edges — knowing when to use it matters more than dismissing it entirely. |

### Magic Wand

| Aspect | Detail |
|---|---|
| **Definition** | A selection tool (W) that selects contiguous or non-contiguous pixels based on tonal and color similarity to the clicked sample point, controlled by a Tolerance value. |
| **Meaning in photographic practice** | Best for selecting regions of relatively uniform color — a solid-colored background, a clear sky, or a product shot on a seamless backdrop. The Magic Wand answers the question "select everything that looks like this color." It is a legacy tool that has been largely superseded by Quick Selection and Select Subject for complex work, but remains efficient for simple, uniform-area tasks. |
| **Photoshop implementation** | Click a pixel; Photoshop evaluates surrounding pixels and selects those within the Tolerance range (0-255, where 0 selects only the exact color and 255 selects everything). Options Bar controls: Tolerance, Anti-alias checkbox, Contiguous checkbox (when off, selects matching pixels across the entire image), Sample All Layers checkbox. |
| **Related concepts** | Color Range (Select > Color Range), Quick Selection, tolerance, contiguous vs non-contiguous. |
| **Common misconception** | "Higher Tolerance always means a better selection." Tolerance is not a quality dial. Too low selects too little, requiring tedious clicking. Too high bleeds into unwanted areas. The correct Tolerance depends on the tonal variation within the target region. Start around 20-32 and adjust based on results. |

### Quick Selection Tool

| Aspect | Detail |
|---|---|
| **Definition** | A brush-based selection tool (W) that analyzes edges and textures to automatically expand a selection as you paint across the area you want to select. |
| **Meaning in photographic practice** | The workhorse selection tool for most photographic work since its introduction. Paint broadly across a subject and the tool finds edges, expanding the selection to fill regions bounded by contrast changes. It replaces the tedious click-by-click workflow of the Magic Wand with a fluid, painterly interaction. |
| **Photoshop implementation** | Access via W (Shift+W to cycle with Magic Wand). Paint across the area to select; the tool auto-detects edges and expands. Brush size matters — use a brush slightly smaller than the region to avoid spilling over edges. The Options Bar provides Add, Subtract, and New selection modes, plus Auto-Enhance (smoother edges at the cost of processing time) and Sample All Layers. |
| **Related concepts** | Magic Wand, Object Selection tool, Select Subject, Select and Mask workspace. |
| **Common misconception** | "Quick Selection just selects whatever you paint over." It does not work like a brush painting a mask. The tool uses edge detection to expand intelligently from your brush strokes. Painting over an edge does not necessarily cross it — the tool tries to respect boundaries. However, low-contrast edges will confuse it, requiring manual correction. |

### Object Selection Tool

| Aspect | Detail |
|---|---|
| **Definition** | An AI-driven selection tool (W) that detects and selects discrete objects within a drawn rectangle or lasso region. |
| **Meaning in photographic practice** | When a scene contains multiple distinct objects, this tool lets you draw a loose rectangle or lasso around any one of them and Photoshop's machine learning isolates just that object. It is particularly effective for product photography, group portraits (selecting one person), and scenes with clearly separated subjects. |
| **Photoshop implementation** | Access via W (Shift+W to cycle). Two modes in the Options Bar: Rectangle and Lasso. Draw around the target object; the AI analyzes the region and selects the most prominent object within it. Since Photoshop 2022 (v23), hovering over the image with this tool active highlights detected objects before you click. The Options Bar includes Sample All Layers and an Object Finder toggle for the hover-to-highlight behavior. |
| **Related concepts** | Select Subject, Quick Selection, machine learning, Adobe Sensei. |
| **Common misconception** | "Object Selection and Select Subject are the same thing." They solve different problems. Select Subject analyzes the entire image and selects what it considers the primary subject. Object Selection lets you target a specific object by drawing around it. In a group photo, Select Subject might select all people; Object Selection lets you isolate one person. |

VERSION NOTE: The Object Selection tool uses Adobe's AI models, which are updated with each Photoshop release. Accuracy, edge quality, and the types of objects it can detect improve over time. Behavior described here reflects general functionality; specific results vary by version.

### Select Subject

| Aspect | Detail |
|---|---|
| **Definition** | A one-click, AI-powered command (Select > Subject, or via the Options Bar button when a selection tool is active) that analyzes the entire image and automatically selects the most prominent subject. |
| **Meaning in photographic practice** | The fastest path from "open image" to "subject isolated." For portraits, wildlife, and product shots with a clear subject-background relationship, Select Subject often produces a usable starting selection in under a second. It is a starting point, not a finished selection — refinement in Select and Mask is almost always necessary, particularly for hair, fur, and transparent materials. |
| **Photoshop implementation** | Accessed via Select > Subject or the "Select Subject" button in the Options Bar (visible when Quick Selection or Object Selection tool is active). Since Photoshop 2021 (v22), a cloud-based processing option provides improved results for complex edges. The command analyzes the full image and returns a selection. No parameters to set — it is fully automatic. |
| **Related concepts** | Object Selection tool, Select and Mask workspace, Remove Background (one-click layer mask). |
| **Common misconception** | "Select Subject replaces learning manual selection techniques." It does not. Select Subject fails or produces poor edges in many scenarios: low subject-background contrast, complex overlapping objects, unusual subjects the AI was not trained on. Manual techniques remain essential for refinement and for the cases where AI falls short. |

VERSION NOTE: Select Subject's accuracy depends heavily on Photoshop version and whether cloud processing is enabled. Results from Photoshop 2024+ are substantially better than earlier versions, particularly for hair and fur. Always evaluate the result at 100% zoom rather than trusting the marching ants.

### Select and Mask Workspace

| Aspect | Detail |
|---|---|
| **Definition** | A dedicated workspace (Select > Select and Mask, or click "Select and Mask" in the Options Bar with any selection tool active) for refining selection edges, with specialized tools and real-time preview against various backgrounds. |
| **Meaning in photographic practice** | Where initial selections become print-ready masks. Every professional subject isolation passes through edge refinement — adjusting the boundary where subject meets background. Select and Mask provides the controls for handling hair, fur, semi-transparent fabric, and every other edge type that raw selection tools cannot capture cleanly. |
| **Photoshop implementation** | Opens a full-screen workspace with its own toolbar (Quick Selection, Refine Edge Brush, Brush, Object Selection, Lasso, Hand, Zoom) and a Properties panel with Global Refinements (Smooth, Feather, Contrast, Shift Edge) and Output Settings (Decontaminate Colors, Output To). View modes (V to cycle): Onion Skin, Marching Ants, Overlay, On Black, On White, Black & White, On Layers. The Refine Edge Brush is the critical tool here — paint it over hair/fur edges and it separates fine detail from background. |
| **Related concepts** | Refine Edge (legacy), layer masks, alpha channels, compositing. |
| **Common misconception** | "Select and Mask is just for hair." While it excels at hair and fur, the Global Refinements (Smooth, Feather, Contrast, Shift Edge) are valuable for any selection edge. Even a simple product cutout benefits from a slight Shift Edge inward to eliminate fringe pixels. |

### Color Range

| Aspect | Detail |
|---|---|
| **Definition** | A selection command (Select > Color Range) that creates a selection based on sampled colors or predefined color/tonal ranges, with a Fuzziness control that functions like the Magic Wand's Tolerance but with real-time visual feedback. |
| **Meaning in photographic practice** | The precision tool for selecting by color across an entire image — isolating a red dress, selecting all the blue sky, targeting skin tones for correction. Unlike the Magic Wand, Color Range shows a grayscale preview of the resulting selection, making it far easier to tune for accurate results. |
| **Photoshop implementation** | Select > Color Range opens a dialog with an eyedropper (click in the image or the preview), Add/Subtract eyedroppers, a Fuzziness slider (0-200), and preset ranges (Reds, Yellows, Greens, Cyans, Blues, Magentas, Highlights, Midtones, Shadows, Skin Tones, Out of Gamut). The Selection Preview dropdown shows the selection overlaid on the image in various modes. Localized Color Clusters checkbox (when available) restricts the selection to a spatial region. |
| **Related concepts** | Magic Wand, Hue/Saturation targeting, Selective Color, channel-based selections. |
| **Common misconception** | "Color Range selects only exact color matches." With Fuzziness at 0, yes. But Fuzziness functions as a tolerance for color similarity, and practical use typically requires values of 20-80. The grayscale preview reveals exactly which pixels will be selected and at what opacity — use it instead of guessing. |

### Refine Edge

| Aspect | Detail |
|---|---|
| **Definition** | A legacy edge-refinement feature that has been incorporated into the Select and Mask workspace. In current Photoshop versions, "Refine Edge" refers specifically to the Refine Edge Brush tool within Select and Mask. |
| **Meaning in photographic practice** | The specialized tool for separating fine, wispy details — hair, fur, foliage — from their background. Without edge refinement, even the best initial selection produces a hard boundary that looks cut out rather than naturally isolated. |
| **Photoshop implementation** | Historically accessed via Select > Refine Edge (a standalone dialog, available through Photoshop CC 2015.5). In current versions, this functionality lives inside Select and Mask as the Refine Edge Brush tool (R within the workspace). Paint this brush over transition zones where fine detail meets background; the algorithm separates foreground from background at a sub-pixel level. Edge Detection Radius (in the Properties panel) controls how far from the selection edge the algorithm looks for refinable detail. |
| **Related concepts** | Select and Mask workspace, Decontaminate Colors, hair masking, compositing. |
| **Common misconception** | "Refine Edge was removed from Photoshop." It was not removed — it was absorbed into Select and Mask, which is a more capable replacement. The old Refine Edge dialog can still be accessed by holding Shift while clicking Select and Mask in the Options Bar, though Adobe may remove this access in future versions. |

---

## 12.4 Theory

### Selections as Grayscale Data

FACT: Every selection in Photoshop is stored internally as a grayscale channel — a single 8-bit plane where each pixel holds a value from 0 (unselected) to 255 (fully selected).

This is the same data structure used by layer masks (Ch 05). The parallel is exact:

| Selection state | Grayscale value | Layer mask equivalent | Effect on editing |
|---|---|---|---|
| Fully selected | 255 (white) | White | Operation applies at 100% |
| Fully unselected | 0 (black) | Black | Operation does not apply |
| Partially selected | 1-254 (gray) | Gray | Operation applies proportionally |

INTERPRETATION: Understanding this grayscale nature transforms how you think about selections. A selection is not a fence around an area — it is a continuous field of influence. A feathered selection is a gradient from white to black. An anti-aliased edge contains gray pixels. Even the "hard" edge of an unfeathered Rectangular Marquee is a step function in the grayscale channel — 255 inside, 0 outside, no intermediate values.

You can see this directly by pressing Q to enter Quick Mask mode. The selection becomes a visible red overlay (by default), where the overlay density represents unselected areas. Or save the selection as a channel (Select > Save Selection) and inspect it in the Channels panel — it is a standard grayscale image.

### The Marching Ants Limitation

FACT: Marching ants display only the contour line where selection opacity equals 50% (grayscale value 128). They show nothing about the gradient structure of the selection on either side of that line.

INTERPRETATION: This means:

- A heavily feathered selection shows ants at the 50% point, but the actual transition zone may extend 100+ pixels beyond the ants in both directions. The ants lie about the selection's true extent.
- A selection where no pixel reaches 50% opacity shows no ants at all. Photoshop displays a warning: "No pixels are more than 50% selected. The selection edges will not be visible." The selection still exists and still works — it is simply too subtle for ants to represent.
- Two selections can look identical (same ants) but produce different results if their internal gradient structures differ (e.g., different feather radii applied to the same boundary).

RECOMMENDATION: Never trust marching ants as the definitive view of a selection. Use Quick Mask mode (Q) or the grayscale preview in Select and Mask to see the true selection shape. This habit separates photographers who "select and hope" from those who select with precision.

### Feathering in Detail

FACT: Feathering applies a Gaussian blur to the selection channel. A feather radius of N pixels creates a transition zone approximately 2N pixels wide (the blur extends N pixels in each direction from the original edge).

The visual effect is a smooth fade from fully selected to fully unselected. The mathematical effect is identical to duplicating the selection as a channel, applying Filter > Blur > Gaussian Blur with a radius equal to the feather value, and loading the result back as a selection.

INTERPRETATION: Choosing the right feather radius is resolution-dependent. A 10-pixel feather on a 72 PPI web image creates a visible, soft transition. The same 10-pixel feather on a 300 PPI print file is barely perceptible. Think in physical dimensions, not pixels:

| Print resolution | Feather for subtle transition (~1 mm) | Feather for visible transition (~5 mm) |
|---|---|---|
| 72 PPI (screen) | 3 px | 14 px |
| 150 PPI (magazine) | 6 px | 30 px |
| 300 PPI (fine print) | 12 px | 59 px |

RECOMMENDATION: For photographic local adjustments (dodging, burning, color shifts), err on the side of larger feather radii. Visible adjustment boundaries are the most common hallmark of overworked images. When in doubt, enter Quick Mask mode and visually check that the transition is gradual enough for the intended output.

### Anti-aliasing in Detail

FACT: Anti-aliasing assigns intermediate opacity values to pixels at the boundary of a selection that crosses pixel boundaries at an angle or curve. Without anti-aliasing, every boundary pixel is either fully selected (255) or fully unselected (0), producing a stair-stepped edge.

INTERPRETATION: Anti-aliasing is almost always desirable. The only scenario where you would intentionally disable it is when selecting areas that must align exactly to pixel boundaries — such as selecting a region for pixel-precise web graphics or game sprites. For photographic work, leave anti-aliasing enabled.

Note that anti-aliasing is a property of the selection at creation time. Unlike feathering, it cannot be added after the fact. If you create a selection with anti-aliasing disabled, you must recreate it to get anti-aliased edges.

---

## 12.5 Photoshop Implementation

### Selection Tool Families

Selection tools in Photoshop fall into four families, each answering the question "which pixels?" through a different logic:

| Family | Logic | Tools | Best for |
|---|---|---|---|
| **Geometric** | User-defined shape | Rectangular Marquee, Elliptical Marquee, Single Row/Column Marquee | Cropping regions, vignettes, pixel-aligned selections, uniform areas |
| **Edge-based** | Tracing boundaries | Lasso, Polygonal Lasso, Magnetic Lasso, Pen tool (to selection) | Irregular but well-defined subject boundaries |
| **Tone/color-based** | Pixel value similarity | Magic Wand, Color Range, Select > Color Range | Uniform backgrounds, sky selection, color-specific targeting |
| **AI-based** | Machine learning inference | Quick Selection, Object Selection, Select Subject | General subject isolation, complex scenes, speed |

RECOMMENDATION: Begin with the family that best matches your problem. A clear blue sky calls for tone/color-based tools. A person against a busy background calls for AI-based tools. An architectural element with straight lines calls for edge-based tools. Combining families is normal — start with the tool that gets you closest, then refine.

### Geometric Tools: Marquee

**Rectangular Marquee (M)**

| Attribute | Detail |
|---|---|
| Strengths | Pixel-perfect geometric selections. Precise fixed-ratio or fixed-size options. Zero ambiguity. |
| Weaknesses | Useless for organic shapes. No edge detection. |
| When to use | Selecting rectangular regions for cropping or compositing. Creating vignettes (with feathering). Selecting areas for filter application. |

**Elliptical Marquee (Shift+M to cycle)**

| Attribute | Detail |
|---|---|
| Strengths | Clean oval and circular selections. Anti-aliasing available. |
| Weaknesses | Only produces ellipses — no other curves. |
| When to use | Circular vignettes. Selecting round objects (lenses, buttons, eyes for approximate isolation). |

Key techniques:

- Hold **Shift** while dragging to constrain to square (Rectangular) or circle (Elliptical).
- Hold **Alt/Option** while dragging to draw from center instead of corner.
- Hold **Shift+Alt/Option** to combine both constraints.
- Use **Select > Transform Selection** after drawing to reposition or resize the marquee without affecting pixels.
- Set **Style** to Fixed Ratio or Fixed Size in the Options Bar for precise aspect ratios.

### Edge-based Tools: Lasso Family

**Lasso (L)**

| Attribute | Detail |
|---|---|
| Strengths | Completely freeform. Fast for rough selections. No settings to configure. |
| Weaknesses | Precision depends entirely on hand steadiness. Poor for detailed work with a mouse (better with a tablet). |
| When to use | Quick, rough selections for local adjustments where precision is not critical. |

**Polygonal Lasso (Shift+L to cycle)**

| Attribute | Detail |
|---|---|
| Strengths | Pixel-precise straight segments. Excellent for architectural subjects and products with flat faces. |
| Weaknesses | Cannot draw curves — only straight segments between click points. Tedious for organic shapes. |
| When to use | Buildings, furniture, packaging, any subject with predominantly straight edges. |

**Magnetic Lasso (Shift+L to cycle)**

| Attribute | Detail |
|---|---|
| Strengths | Automates edge-following against high-contrast boundaries. Faster than manual tracing for suitable subjects. |
| Weaknesses | Fails at low-contrast edges. Requires parameter tuning. Can be frustrating when it snaps to the wrong edge. |
| When to use | Subjects with clear, high-contrast edges against a uniform background. Dark product on white seamless. Light building against blue sky. |

Magnetic Lasso parameters:

| Parameter | What it controls | Typical starting value |
|---|---|---|
| Width | How far from the cursor the tool searches for edges (in pixels) | 10 px for precise edges, 20-40 px for obvious boundaries |
| Contrast | Minimum contrast required to detect an edge (1%-100%) | 10% for low-contrast edges, 50%+ for high-contrast |
| Frequency | How often anchor points are placed automatically (0-100) | 50-70 for moderate detail, higher for complex contours |

Tip: Press **Caps Lock** to switch the Magnetic Lasso cursor from the default circle to a crosshair — this removes visual clutter and helps with precision.

### Tone/Color-based Tools

**Magic Wand (W)**

| Attribute | Detail |
|---|---|
| Strengths | Simple, fast, predictable. Tolerance is a single numeric control. Good for uniform areas. |
| Weaknesses | Requires relatively uniform target areas. Tolerance affects all channels simultaneously. No preview — trial and error. |
| When to use | Selecting solid-color backgrounds, uniformly lit seamless backdrops, areas of consistent tone. |

Key settings:

| Setting | Effect |
|---|---|
| Tolerance (0-255) | Range of tonal/color similarity accepted. Higher = more inclusive. |
| Contiguous (checkbox) | When on, selects only connected matching pixels. When off, selects all matching pixels in the image. |
| Sample All Layers (checkbox) | When on, samples color data from all visible layers. When off, samples only the active layer. |
| Anti-alias (checkbox) | Smooths the selection boundary. Leave on for photographic work. |

RECOMMENDATION: Start with Tolerance 20-32 and adjust. Use the Contiguous option intentionally: off for "select all the sky including patches between tree branches," on for "select only this connected region of sky."

**Color Range (Select > Color Range)**

| Attribute | Detail |
|---|---|
| Strengths | Real-time grayscale preview of the selection. Fuzziness slider with visual feedback. Add/Subtract eyedroppers for multi-sample targeting. Preset ranges for common selections. |
| Weaknesses | No spatial intelligence — selects by color globally. Can select unwanted regions that share the target color. |
| When to use | Selecting all instances of a color across the image. Targeting skin tones. Selecting highlights, midtones, or shadows. Any time the Magic Wand's lack of preview is frustrating. |

Color Range workflow:

1. Open Select > Color Range.
2. Click on the target color in the image or the preview.
3. Use the Add Eyedropper (+) to include additional color variations (Shift+click is the shortcut while the dialog is open).
4. Adjust Fuzziness to broaden or narrow the selection range.
5. Watch the grayscale preview — white is selected, black is not.
6. Use Selection Preview to see the selection overlaid on your image.
7. Click OK to create the selection.

INTERPRETATION: Color Range is the Magic Wand's more capable sibling. The real-time preview alone makes it the superior choice for any tonal or color-based selection task. The Magic Wand's only advantage is speed for trivially simple selections.

### AI-based Tools

**Quick Selection (W)**

| Attribute | Detail |
|---|---|
| Strengths | Fast, intuitive brush-based interaction. Good edge detection. Auto-detects boundaries as you paint. |
| Weaknesses | Can struggle with low-contrast edges. Sometimes over-selects or under-selects despite edge detection. Brush size matters but the relationship is not always predictable. |
| When to use | First-pass subject isolation in most scenarios. Fast selection when you need a quick mask. |

Workflow tips:

- Start with a brush slightly smaller than the subject area.
- Paint in broad strokes across the interior of the subject, not along its edges.
- When the selection spills over, switch to Subtract mode (hold Alt/Option) and paint back.
- Enable Auto-Enhance in the Options Bar for smoother edges (slower processing).

**Object Selection (W)**

| Attribute | Detail |
|---|---|
| Strengths | One-object targeting by drawing a rough rectangle or lasso. Hover-to-highlight preview in recent versions. Better than Quick Selection at isolating single objects in complex scenes. |
| Weaknesses | AI-dependent — cannot select objects the model does not recognize. Sometimes includes unwanted elements within the drawn region. |
| When to use | Selecting one specific object from a scene with multiple objects. Product shots. Targeting individual people in group photos. |

VERSION NOTE: Object Selection's hover-to-highlight (Object Finder) feature requires Photoshop 2022 (v23) or later. Its accuracy and the range of objects it recognizes improve with each release.

Workflow tips:

- Use Rectangle mode for blocky objects, Lasso mode for irregular shapes.
- Draw the selection boundary loosely — do not try to trace the object precisely. The AI handles the edge finding.
- If the wrong object is selected, undo and redraw with a tighter boundary around the desired object.

**Select Subject (Select > Subject)**

| Attribute | Detail |
|---|---|
| Strengths | One-click operation. No parameters to set. Cloud processing option for improved results. Remarkably effective for portraits and clearly defined subjects. |
| Weaknesses | No control over what it considers "the subject." May select too much or too little. Edge quality varies — almost always requires refinement. |
| When to use | As a first step when the image has a clear primary subject. Portraits, wildlife, product shots, sports photography. Follow with Select and Mask for refinement. |

VERSION NOTE: Select Subject's quality has improved dramatically across Photoshop versions. Cloud-based processing (available in recent versions with internet connectivity) produces noticeably better results than device-only processing, especially for hair and fur.

### Selection Combination Modes

All selection tools support combination modes, accessible via the Options Bar or keyboard modifiers:

| Mode | Options Bar icon | Keyboard modifier | Effect |
|---|---|---|---|
| New Selection | Single square | (none) | Replaces any existing selection |
| Add to Selection | Overlapping squares with + | Hold **Shift** | Adds new selection area to existing |
| Subtract from Selection | Overlapping squares with - | Hold **Alt/Option** | Removes new selection area from existing |
| Intersect with Selection | Overlapping squares with X | Hold **Shift+Alt/Option** | Keeps only the overlap between new and existing selection |

INTERPRETATION: Building complex selections through Add and Subtract is fundamental technique. Start with the tool that captures most of the target area, then add missed regions and subtract excess. This iterative approach is faster and more accurate than trying to create a perfect selection in one pass.

### Select and Mask Workspace — In Depth

Access: Select > Select and Mask, or click "Select and Mask" in the Options Bar with any selection tool active. Keyboard shortcut: Ctrl+Alt+R / Cmd+Option+R.

**Workspace Tools:**

| Tool | Shortcut (within workspace) | Purpose |
|---|---|---|
| Quick Selection | W | Add or subtract from the selection |
| Refine Edge Brush | R | Separate fine detail (hair, fur) from background |
| Brush | B | Manually paint the selection (add or subtract) |
| Object Selection | W (cycle) | Target specific objects |
| Lasso / Polygonal Lasso | L | Freeform add/subtract |
| Hand | H | Pan the image |
| Zoom | Z | Zoom in/out |

**View Modes (V to cycle):**

| Mode | What it shows | When to use |
|---|---|---|
| Onion Skin | Subject over transparent/faded background | General refinement, gauging edge quality |
| Marching Ants | Standard marching ants | Quick reality check |
| Overlay | Red (or custom color) overlay on unselected areas | Similar to Quick Mask; good for seeing missed areas |
| On Black | Subject on pure black | Checking for light fringe/halo on light subjects |
| On White | Subject on pure white | Checking for dark fringe on dark subjects |
| Black & White | The selection as a grayscale mask | Most accurate view of the actual selection data |
| On Layers | Subject over existing layers | Checking composite result |

RECOMMENDATION: Check your selection against multiple view modes. A selection that looks clean On White may show obvious fringe On Black. The Black & White view shows the raw truth — use it to verify edge quality.

**Global Refinements:**

| Control | Effect | Typical use |
|---|---|---|
| Smooth | Reduces irregularities in the selection edge | Cleaning up jagged, noisy edges |
| Feather | Softens the entire edge uniformly | Creating subtle transitions |
| Contrast | Sharpens soft edges, making them more defined | Tightening an over-feathered edge |
| Shift Edge | Expands (+) or contracts (-) the entire selection boundary | Eliminating fringe — negative values pull the edge inward away from background color contamination |

**Output Settings:**

| Setting | Effect |
|---|---|
| Decontaminate Colors | Replaces color fringe at semi-transparent edges with colors sampled from the subject. Destructive on the original layer — forces output to a new layer. |
| Output To | Selection, Layer Mask (most common), New Layer, New Layer with Layer Mask, New Document, New Document with Layer Mask |

RECOMMENDATION: Output to Layer Mask in almost all cases. This preserves the original pixels and lets you refine further. If using Decontaminate Colors, output to New Layer with Layer Mask to preserve the original.

**Refine Edge Brush — Critical Technique:**

The Refine Edge Brush (R) is the most important tool in this workspace. It handles the edges that no other selection tool can get right — hair against a busy background, fine fur, wispy fabric.

How it works: Paint over the transition zone where fine foreground detail mixes with background. The algorithm analyzes pixel colors in the painted region, determines which pixels are foreground and which are background, and assigns appropriate opacity values. It is color-aware: it separates dark hair from a light sky by recognizing the color populations in the sample region.

Guidelines:

- Paint along the edge, not over it. The brush should straddle the boundary between subject and background.
- Use a brush size that covers the transition zone — typically the width of the wispy hair/fur region.
- If the result is poor, the background and foreground colors may be too similar for the algorithm. In such cases, manual mask painting (Ch 05 techniques) may be necessary.
- Edge Detection Radius in the Properties panel controls how far the algorithm searches. Increase it for wider transition zones (e.g., long flowing hair); keep it small for tight edges.

---

## 12.6 Professional Workflow: Choosing the Right Selection Method

The selection method should match the problem. This decision tree guides the choice:

**Step 1: Characterize the boundary.**

| Boundary type | Example | Best starting tool |
|---|---|---|
| Geometric (straight lines, circles) | Architecture, product boxes, frames | Marquee or Polygonal Lasso |
| Organic, high-contrast edge | Subject on white/black backdrop | Quick Selection or Object Selection |
| Organic, low-contrast edge | Subject against similar-toned background | Pen tool (to selection), careful manual work |
| Color-defined region | Blue sky, red dress, skin tones | Color Range |
| Fine detail (hair, fur, foliage) | Portrait hair against sky | Select Subject + Select and Mask (Refine Edge Brush) |
| AI-recognizable subject | People, animals, vehicles, common objects | Select Subject (one-click start) |

**Step 2: Create the initial selection using the chosen tool.**

Get close, not perfect. The goal of the first pass is to capture approximately 80-90% of the target area.

**Step 3: Refine using combination modes.**

Switch to Add (Shift) and Subtract (Alt/Option) to clean up missed areas and excess selection. You can switch tools freely — start with Quick Selection, subtract with the Lasso, add with the Polygonal Lasso.

**Step 4: Enter Select and Mask for edge refinement.**

Use Global Refinements for overall edge quality. Use the Refine Edge Brush for problem edges (hair, fur). Check against multiple view modes.

**Step 5: Output as a Layer Mask.**

This converts your temporary selection into a persistent, editable mask — the bridge between this chapter and Ch 05.

### Selection vs Mask: When to Choose Which

| Scenario | Approach | Rationale |
|---|---|---|
| Subject isolation for compositing | Selection tools, then convert to mask | Selection tools are optimized for finding edges; masks are optimized for storing the result |
| Local tonal adjustment with soft boundary | Paint mask directly on adjustment layer | Faster than creating a selection when the boundary does not follow any image edge |
| Precise color-based targeting | Color Range selection, then refine | Color Range provides visual feedback that mask painting cannot match |
| Adjustment following an existing contour | Selection tools, convert to mask | The tools detect edges; your brush does not |
| Creative effect with arbitrary shape | Paint mask directly | No image edge to detect — the shape comes from your intent |

INTERPRETATION: Selections and masks are two views of the same data. The question is not "selection or mask?" but "which creation method produces the best result for this boundary?" Use selection tools when edges are detectable. Paint masks when they are not.

---

## 12.7 Step-by-Step Example: Isolating a Subject from Background

**Scenario:** A portrait shot against a moderately busy outdoor background. The subject has dark hair, wearing a patterned shirt. Goal: isolate the subject onto a transparent layer for compositing.

**Step 1: Duplicate the background layer (Ctrl+J / Cmd+J).**

Non-destructive practice. Work on the copy; preserve the original.

**Step 2: Use Select Subject for the initial selection.**

With the duplicate layer active, go to Select > Subject (or click "Select Subject" in the Options Bar with the Quick Selection tool active). Photoshop analyzes the image and returns a selection around the subject.

Evaluate: Zoom to 100% and scroll around the subject boundary. The body and clothing are likely well-selected. The hair edge is likely rough or has missed fine strands. This is expected.

**Step 3: Correct major selection errors with Quick Selection.**

If Select Subject missed an arm, part of the shirt, or included a background element:

- Switch to Quick Selection tool (W).
- Hold Shift and paint over missed subject areas to add them.
- Hold Alt/Option and paint over incorrectly included background areas to subtract them.

Do not spend time on the hair edge yet — that is handled in Step 4.

**Step 4: Enter Select and Mask (Ctrl+Alt+R / Cmd+Option+R).**

Set View Mode to On Black (or On White if the subject is dark) to see fringe clearly.

**4a: Global Refinements.**

- Set Smooth to 2-5 to reduce edge noise on clothing and skin boundaries.
- Leave Feather at 0 for now (feathering composited subjects is rarely desirable — edges should be sharp but clean).
- Set Shift Edge to -10% to -30% to contract the selection slightly inward, eliminating the thin fringe of background color that clings to subject edges.

**4b: Refine the hair edge.**

- Select the Refine Edge Brush (R).
- Set brush size to roughly the width of the hair transition zone (where you can see individual strands mixing with background).
- Paint along the hair boundary. The algorithm separates hair strands from background color.
- Check the result in Black & White view — hair strands should appear as gray values (partially selected), not hard white or hard black.

**4c: Handle color contamination.**

If background color bleeds into semi-transparent hair edges:

- Enable Decontaminate Colors in the Output Settings.
- Set the Amount slider (start at 50%, adjust as needed).
- This replaces fringe colors with colors sampled from the subject interior. Note: this modifies pixel colors and forces output to a new layer.

**Step 5: Output as Layer Mask.**

In Output Settings, set Output To: Layer Mask (or New Layer with Layer Mask if using Decontaminate Colors). Click OK.

**Step 6: Verify.**

Place a solid color layer beneath the subject layer. Check several high-contrast colors — the fringe that is invisible on white may be obvious on red or blue. Refine the mask with a soft black/white brush if needed (Ch 05 techniques).

**Result:** The subject is cleanly isolated on a layer with a mask. The mask is non-destructive and can be refined further at any time by returning to Select and Mask or by painting directly on it.

---

## 12.8 Common Mistakes

| Mistake | Why it happens | Correction |
|---|---|---|
| Trusting marching ants as the full selection | The ants look definitive, so photographers assume they show the whole truth | Press Q for Quick Mask view or check Black & White in Select and Mask. The ants show only the 50% contour. |
| Setting feather radius without considering resolution | Thinking "10 pixels" is a universal value | Calculate feather in physical units based on output resolution. A 10px feather is invisible at 300 PPI but heavy at 72 PPI. |
| Forgetting anti-aliasing on tools that do not default to it | Some tools default to anti-aliasing on, others off | Check the Options Bar before creating the selection. Anti-aliasing cannot be added after creation. |
| Using the Magic Wand when Color Range would be better | The Magic Wand is the first tool many photographers learned | Color Range provides a real-time grayscale preview. Use it whenever you need precision with tone/color-based selection. |
| Trying to perfect the initial selection before entering Select and Mask | Spending excessive time with Quick Selection trying to nail the hair edge | Get the body/clothing selection right, then use Select and Mask for edges. The Refine Edge Brush exists specifically for this. |
| Never using Subtract mode | Building selections only with Add (Shift), then starting over when they go too far | Alt/Option-click/paint to subtract. Iterative add-subtract is the normal workflow. |
| Applying Decontaminate Colors without understanding it is destructive | Enabling it without realizing it modifies pixel data | Decontaminate Colors forces output to a new layer for good reason — it changes pixel colors. Use it deliberately, not by default. |
| Creating hard-edged selections for photographic adjustments | Applying Curves or Hue/Saturation with no feathering on the selection | Unless you are compositing or cutting out, photographic adjustments almost always need feathered selections to avoid visible boundaries. |
| Selecting when a painted mask would be faster | Using selection tools to define an area that does not follow any image edge | If the adjustment boundary is creative (not edge-following), paint the mask directly on the adjustment layer. |

---

## 12.9 Alternative Approaches

### Channel-based Selections (Ch 05 Review)

FACT: Any grayscale channel can become a selection via Ctrl/Cmd-clicking its thumbnail in the Channels panel.

For images where the subject-background separation is already present in a single channel (e.g., a subject that is dark in the Blue channel but the background is light), duplicating and adjusting that channel with Levels or Curves, then loading it as a selection, can produce results that AI tools cannot match. This technique was the professional standard before AI-based tools existed and remains valuable for difficult cases.

### Pen Tool Paths

The Pen tool creates resolution-independent vector paths that can be converted to selections (right-click > Make Selection, or Ctrl+Enter / Cmd+Return). For product photography, packaging, and any subject with clean, defined edges, the Pen tool produces the most precise boundaries possible. The tradeoff is time — Pen tool work is slow and demands skill. It is covered in depth in a dedicated chapter.

### Luminosity Masks

Advanced technique: create selections based on luminosity ranges using the Channels panel or specialized actions/plugins. Ctrl/Cmd-click the RGB composite channel thumbnail to load a luminosity selection (highlights), then intersect or subtract to isolate midtones or shadows. Luminosity masks enable tonal adjustments that follow the image's own light structure rather than geometric or edge-based boundaries.

### Focus Area Selection

Select > Focus Area creates a selection based on in-focus regions of the image. Useful for isolating a sharply focused subject from a blurred (bokeh) background. The dialog provides an In-Focus Range slider and noise-tolerance controls. Results vary but can be a useful starting point for shallow-depth-of-field images.

---

## 12.10 Summary

A selection is a temporary grayscale mask that defines which pixels an editing operation affects and by how much. Every selection maps to a channel where 255 is fully selected, 0 is fully unselected, and intermediate values are partially selected. Marching ants display only the 50% boundary and hide the selection's true gradient structure.

Four families of selection tools exist: geometric (Marquee), edge-based (Lasso family), tone/color-based (Magic Wand, Color Range), and AI-based (Quick Selection, Object Selection, Select Subject). Each answers "which pixels?" through a different logic, and professional work routinely combines tools from multiple families.

Feathering creates gradual transitions by blurring the selection channel; anti-aliasing smooths boundaries at the pixel level. Both prevent artificial-looking edges but serve different purposes and operate at different scales.

The Select and Mask workspace is the finishing environment for any selection. Its Refine Edge Brush handles hair, fur, and fine detail that no other tool can capture cleanly. Output to Layer Mask bridges the temporary selection into a persistent, editable mask.

The strategic decision is not "which tool?" but "which boundary type?" Classify the boundary (geometric, organic, color-defined, AI-recognizable, fine-detail), choose the matching tool family, create an approximate initial selection, refine through add/subtract operations, finalize in Select and Mask, and output as a layer mask. This workflow handles every subject isolation scenario from product shots to windblown hair.

---

## 12.11 Exercises

### Standard Exercises

**Exercise 12.1 — Selection Anatomy**
Open any photograph. Use the Rectangular Marquee to draw a selection. Press Q to enter Quick Mask mode and observe the red overlay. Select > Save Selection to store it as a channel. Inspect it in the Channels panel. Return to the selection (Select > Load Selection), apply Select > Modify > Feather with a 30-pixel radius. Enter Quick Mask mode again and compare the gradient falloff to the original hard edge. Save this feathered version as a second channel. View both channels side by side and note how feathering transforms the step function into a gradient.

**Exercise 12.2 — Marching Ants vs Reality**
Open a photograph. Use the Elliptical Marquee with 0 feather and draw a circle. Note the marching ants. Now undo (Ctrl+Z / Cmd+Z), set Feather to 60 pixels in the Options Bar, and draw the same circle in the same location. Compare the marching ants to the first version. They will appear in a different position — the ants moved inward because the 50% boundary shifted. Enter Quick Mask mode to see the actual falloff zone that the ants cannot represent.

**Exercise 12.3 — Tool Family Comparison**
Open a photograph containing a clear subject against a contrasting background. Select the subject using four different methods: (1) Lasso tool (freehand), (2) Magic Wand, (3) Quick Selection, (4) Select Subject. Save each selection as a separate channel (name them "Lasso," "Wand," "QuickSel," "Subject"). Compare the four channels at 100% zoom. Note where each tool succeeded and failed. Which produced the cleanest edges? Which was fastest? Which required the most correction?

**Exercise 12.4 — Color Range Precision**
Open a photograph with a prominent color (a red car, a blue sky, a yellow flower). Use Select > Color Range to select that color. Experiment with Fuzziness values from 0 to 100. Use the Add Eyedropper to click multiple points within the color region. Observe how the grayscale preview changes. Output the selection as a layer mask on a Hue/Saturation adjustment layer and shift the targeted color dramatically. Evaluate whether the selection was clean — look for fringing at the boundaries.

**Exercise 12.5 — Add/Subtract Combination Workflow**
Open a photograph with a complex subject. Start with Select Subject. Switch to the Quick Selection tool and practice adding (Shift) and subtracting (Alt/Option) to fix errors in the initial selection. Then switch to the Polygonal Lasso and use Add mode to include a geometric element that Quick Selection missed. Then use the Lasso in Subtract mode to remove a background region that was incorrectly included. This exercise trains you to switch tools freely while building a single selection.

### Advanced Exercises

**Exercise 12.6 — Select and Mask Mastery**
Find or take a portrait photograph where the subject has visible hair against a non-uniform background. Use Select Subject to create an initial selection, then enter Select and Mask. Practice the following sequence: (1) Set View Mode to On Black and evaluate fringe. (2) Set Shift Edge to -20% and observe how the boundary contracts. (3) Switch to the Refine Edge Brush, set an appropriate brush size, and paint along the hair boundary. (4) Switch between On Black, On White, and Black & White views to evaluate. (5) Enable Decontaminate Colors and observe its effect. (6) Output as New Layer with Layer Mask. Place a contrasting solid color layer behind the subject and evaluate the result.

**Exercise 12.7 — Channel-based Selection vs AI**
Open a high-contrast photograph (dark subject, bright background or vice versa). Create two versions of the same selection: (1) Using Select Subject + Select and Mask. (2) By duplicating the most contrasty channel, adjusting with Levels to push it toward pure black and white, then loading as a selection. Compare the two results. Note where each method excels and fails. For which parts of the boundary would you use each approach?

**Exercise 12.8 — Resolution-Aware Feathering**
Take a single high-resolution image (3000+ pixels on the long edge). Create an elliptical selection in the center. Feather it with a 5-pixel radius and fill the inverse with black to create a vignette effect. Undo. Repeat with 20, 50, and 100-pixel feather radii. Now resize the image to 800 pixels on the long edge and repeat all four feather values. Compare the visible results. At which feather radius does the transition become imperceptibly smooth at each resolution? Document your findings as a personal reference for future work.

### Blackbelt Challenge

**Challenge 12.1 — The Impossible Hair**
Find a photograph where a subject with fine, light-colored hair (blonde or gray) is photographed against a light, textured background (pale wall, overcast sky with clouds). This is the most difficult selection scenario — foreground and background colors are nearly identical, and AI tools struggle.

Isolate the subject using every technique at your disposal:

1. Start with Select Subject and evaluate where it fails.
2. Enter Select and Mask and work the Refine Edge Brush along the hair boundary. Note its limitations with similar foreground/background colors.
3. Exit Select and Mask. Go to the Channels panel and examine each channel individually. Find the one with the most separation between hair and background.
4. Duplicate that channel. Use Levels and Curves aggressively to push it toward a usable mask, while preserving hair strand detail.
5. Load the channel as a selection, combine it with the Select Subject result (using Intersect or Add as appropriate).
6. Return to Select and Mask for final refinement.
7. Output as a layer mask. Place the subject on three different colored backgrounds and evaluate at 100% zoom.

Document which technique contributed most to the final result and where you had to accept imperfection. There is no shame in acknowledging that some edges cannot be perfectly selected — the blackbelt skill is knowing how close you can get and which tradeoffs to accept.
