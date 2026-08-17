---
description: The physics of digital noise — signal-to-noise ratio as the unifying concept, shot noise and the Poisson square-root law, read noise, thermal and dark-current noise, fixed-pattern noise and banding, hot and stuck pixels, quantization noise, JPEG and demosaicing artifacts, the causal chain from ISO/exposure/aperture/sensor size to noise, and why "high ISO causes noise" is an oversimplification.
---

# Noise Deep Dive Part 1: The Physics of Digital Noise

This is the first part of an eight-part deep dive that expands [Chapter 16: Noise Reduction](16_Noise_Reduction.md) from an introductory treatment into a rigorous study of noise, denoising, sharpening interaction, AI tools, and production workflow. Chapter 16 established the working vocabulary — noise vs grain, luminance vs chrominance noise, signal-to-noise ratio, read noise, and shot noise — at the level needed to operate Camera Raw. This part goes underneath that vocabulary to the physics: what the sensor actually measures, what corrupts the measurement, and why every downstream decision you make in Camera Raw and Photoshop is constrained by events that occurred at the photosite before the shutter closed.

You cannot denoise well if you do not know what noise is. The tools in [Part 4](16D4_Camera_Raw_AI_Denoise_and_PS_Tools.md) are powerful, but they are inversion algorithms trying to undo a physical process. The better you understand the forward process — how a clean scene becomes a noisy file — the better you can judge whether a denoiser has recovered the signal or invented a plausible fiction. That distinction becomes acute in [Part 6](16D6_AI_Upscaling_and_Hallucinated_Detail.md), where "detail" can be hallucinated outright.

---

## Learning Objectives

After completing this part, you will be able to:

1. Define signal-to-noise ratio (SNR) precisely as a ratio of signal magnitude to the standard deviation of noise, and compute SNR for a photosite from a stated photon count using the Poisson square-root law.
2. Derive, from the Poisson nature of light, why shot noise equals the square root of the signal, and explain why this makes SNR *improve* with more light and *degrade* in shadows — quantitatively, not just qualitatively.
3. Distinguish the major noise sources by their physical origin and their signal-dependence: shot (photon) noise, read noise, thermal (dark-current) noise, fixed-pattern noise, banding (pattern read noise), and quantization noise.
4. Distinguish hot pixels from stuck pixels from dead pixels by cause and by behavior across exposures, and predict when each becomes visible.
5. Explain the physical origin of chrominance noise as distinct from luminance noise, and connect it to the Bayer color filter array and the demosaicing interpolation.
6. Identify demosaicing artifacts (maze/labyrinth patterns, color moire) and JPEG compression artifacts (blocking, ringing, chroma subsampling) as noise-like degradations that are *not* photonic noise and therefore respond differently to noise reduction.
7. Build the full causal chain from ISO, exposure time, aperture, and photosite area to the absolute number of photons captured, and from there to SNR — placing ISO in its correct role as post-capture amplification rather than a cause of noise.
8. Explain, emphatically and correctly, why "high ISO causes noise" is an oversimplification, and why underexposing at low ISO and lifting shadows later can produce noise equal to or worse than a correctly exposed high-ISO frame.
9. State the concept of ISO-invariance qualitatively, identify what it does and does not claim, and flag the exact per-camera behavior as something to be tested rather than assumed.
10. Observe and coarsely measure these noise types in Photoshop using the Info panel, channel inspection, and 100%/200% zoom, distinguishing signal-dependent from signal-independent noise by inspection.

---

## Conceptual Foundation

A digital photograph is a measurement. Each photosite on the sensor is a small bucket that, during the exposure, counts photons — or more precisely, counts the electrons that photons liberate when they strike the silicon. At the end of the exposure the accumulated charge in each bucket is read out, amplified, and converted to a number. The image is the array of those numbers. Everything you later call "detail," "tone," and "color" is reconstructed from this array of counts.

Noise is the discrepancy between the number you measured and the number that a perfect, noiseless measurement of the same scene would have produced. Chapter 16 defined noise as "unwanted random variation in pixel values." That is true, but it invites a misconception: that noise is a defect added by a bad sensor, something a better sensor could remove entirely. It cannot. The single largest source of noise in a well-designed camera is not the sensor at all — it is the light. Light arrives as discrete, randomly-timed photons, and counting random arrivals carries an irreducible uncertainty. A perfect sensor photographing a perfectly uniform gray wall would still record a slightly different count in every photosite, purely because photons do not arrive on a schedule.

This reframes the entire subject. Noise is not primarily an electronics problem to be engineered away; it is a *statistics* problem set by how much light you collected. The mental model to carry through all eight parts is this: **the sensor's job is to collect photons, and noise is the uncertainty in the count. The only fundamental way to reduce that uncertainty is to count more photons.** Everything else — better electronics, lower read noise, cooling, clever algorithms — either removes secondary noise sources that sit *on top of* the photon count, or estimates the true count from the noisy one after the fact. None of it beats simply having collected more light.

From this single idea the rest of the physics follows. Shadows are noisy not because shadows generate noise but because shadows contain few photons, and few photons means a large *relative* uncertainty. High ISO does not create noise; it amplifies a signal that was already too weak because too little light was collected. Small sensors are noisier per pixel not because their silicon is worse but because each small photosite intercepts fewer photons. A long exposure at night gathers photon signal but also gathers thermal electrons that masquerade as signal. Understanding these as one coherent story — signal is photons, noise is the uncertainty in and the corruption of the count — is the entire purpose of this part.

Two more framing points before the terminology.

First, **noise sources add in quadrature, not linearly.** When independent random processes combine, their standard deviations do not add directly; their variances (the squares of the standard deviations) add, and you take the square root of the sum. This is why one dominant noise source tends to swamp the others, and why reducing a secondary source barely moves the total until it approaches the dominant one. We will use this repeatedly.

Second, **not everything that looks like noise is noise in the physical sense.** Random photon-and-electron noise behaves one way; JPEG blocking, demosaicing mazes, banding, and hot pixels behave differently. They are structured, not random. A denoiser tuned for random noise handles them poorly, and treating them as ordinary noise is a common failure mode addressed in [Part 3](16D3_Denoising_Mindset_RAW_vs_JPEG.md) and [Part 8](16D8_Quality_Control_and_Cheat_Sheet.md). Part of the skill this part builds is telling them apart by inspection.

---

## Terminology

### Signal

**Definition:** The signal is the quantity you are trying to measure: the number of photoelectrons a photosite would accumulate from scene light during the exposure, in the absence of any noise. In a linear sensor, signal is proportional to scene luminance multiplied by exposure time multiplied by the effective collecting area and quantum efficiency. It is measured, at the physical level, in electrons (e-).

**Meaning in photographic practice:** Signal strength is set entirely at capture, before any ISO amplification. Two exposures of the same scene at the same aperture and shutter speed collect the same signal whether ISO is 100 or 12800 — ISO changes how the signal is *scaled* on the way to a number, not how much signal was collected. Because every noise consequence flows from signal strength, the phrase "collect more signal" is the most important instruction in low-light photography: open the aperture, lengthen the exposure, add light, or use a larger sensor.

**Related concepts:** Photon count, quantum efficiency, full-well capacity, exposure, SNR.

**Common misconception:** "Raising ISO increases the signal." It does not. Raising ISO amplifies whatever signal was captured, along with the noise riding on it. The signal — the photon count — is fixed the moment the shutter closes at a given aperture and shutter speed. See the extended treatment in the Theory section on why "high ISO causes noise" is an oversimplification.

---

### Signal-to-Noise Ratio (SNR)

**Definition:** SNR is the ratio of the signal magnitude to the magnitude of the noise, where "magnitude of the noise" means the standard deviation of the random fluctuation in the measurement. If a photosite's true signal is S electrons and the total noise has standard deviation sigma electrons, then SNR = S / sigma. It is dimensionless. It is often quoted in decibels: SNR(dB) = 20 log10(S / sigma).

**Meaning in photographic practice:** SNR, not the absolute noise amount, determines whether an image *looks* noisy. A bright region and a dark region in the same frame carry the same read noise, but the bright region has vastly more signal, so its SNR is high and it looks clean, while the dark region's SNR is low and it looks gritty. This is why Chapter 16 emphasizes that noise is "a shadow problem." Chapter 16 introduced SNR qualitatively; here we treat it numerically. Every action a photographer takes to "reduce noise" is, physically, an action to raise SNR — usually by raising S (collect more light), occasionally by lowering sigma (cooling, lower-read-noise mode, averaging frames).

**Photoshop implementation:** Photoshop does not display SNR. You can estimate it crudely: place a color sampler (Info panel) over a region that should be uniform, read the mean level, then use the histogram of a tight rectangular selection over that same region to gauge the spread (standard deviation). A wide spread over a region that should be flat indicates low SNR. See the Photoshop Implementation section for the procedure.

**Related concepts:** Shot noise, read noise, standard deviation, decibels, dynamic range, ETTR (see Chapter 16).

**Common misconception:** "Higher-megapixel cameras have worse SNR." Not at the image level. Per pixel, smaller photosites collect fewer photons and have lower per-pixel SNR, but when you view or print at a fixed size you average many small pixels together, and the averaging recovers most of the SNR. SNR must always be compared at a fixed spatial scale (per pixel, or per unit print area) or the comparison is meaningless. This "per-pixel vs per-image" distinction recurs in [Part 2](16D2_Old_Sensors_and_Visual_Diagnosis.md).

---

### Shot Noise (Photon Noise)

**Definition:** Shot noise is the random variation in the number of photons (and hence photoelectrons) collected by a photosite, arising from the fact that photon arrival is a Poisson process. For a Poisson process, the standard deviation equals the square root of the mean. Therefore if a photosite collects, on average, N photoelectrons, the shot noise is sqrt(N) electrons, and the shot-noise-limited SNR is N / sqrt(N) = sqrt(N).

**Meaning in photographic practice:** Shot noise is the fundamental noise floor of photography. It is a property of light, not of the camera. Because SNR from shot noise alone equals sqrt(N), collecting four times as many photons doubles SNR (one stop of SNR improvement per doubling would be sqrt(2) ≈ 1.41×; a full 2× SNR needs 4× the light, i.e. two stops). This square-root relationship is the single most important quantitative fact in the physics of noise, and it explains why the returns on "just add more light" are real but sublinear. In any well-exposed image at base ISO, shot noise is the dominant noise source in the midtones and highlights; read noise only takes over deep in the shadows.

**Photoshop implementation:** Shot noise is signal-dependent — it is larger in brighter regions in *absolute* terms but smaller *relative* to the signal. You can see this directly: a bright uniform patch has a wider raw histogram spread than a dark patch in absolute levels, yet looks cleaner because the spread is small relative to its mean. This is the opposite of what beginners expect and is worth confirming by measurement (see Photoshop Implementation).

**Related concepts:** Poisson distribution, SNR, full-well capacity, quantum efficiency, dynamic range.

**Common misconception:** "Shot noise means my sensor is faulty." Shot noise is present in every measurement of light ever made, by any detector, including the human eye (which is why very dim scenes look grainy to us too). It cannot be engineered away. It can only be reduced by collecting more photons.

---

### Read Noise

**Definition:** Read noise is the random error introduced by the process of converting the accumulated charge in a photosite into a digital number: amplifier thermal noise, reset noise, and analog-to-digital conversion noise, among others. Crucially, read noise is (to first order) *independent of the signal* — it is roughly the same number of electrons whether the photosite collected many photons or none. It is quoted in electrons RMS (root-mean-square).

**Meaning in photographic practice:** Read noise sets the floor of the shadows. Where the signal is large, read noise is negligible next to shot noise; where the signal approaches zero (deep shadow, or a black frame), read noise is all that remains and it defines how far you can lift shadows before noise overwhelms the recovered detail. Modern sensors have dramatically reduced read noise compared to sensors of a decade or two ago, which is precisely why aggressive shadow recovery from a modern RAW file looks cleaner — the topic of [Part 2](16D2_Old_Sensors_and_Visual_Diagnosis.md) and the E-520 project in [Part 7](16D7_Workflows_and_E520_Project.md). **UNCERTAINTY:** Specific read-noise figures (in electrons) are camera- and ISO-specific and are measured by independent testers; do not assume a value for any given body without consulting measured data.

**Photoshop implementation:** Read noise is what you are fighting when you lift a near-black region several stops and see it dissolve into grain rather than resolve into detail. It is signal-independent, so unlike shot noise it does not shrink relative to itself as you brighten — brightening a read-noise-limited shadow amplifies signal and read noise together with no SNR gain from the brightening itself.

**Related concepts:** Shot noise, SNR, dynamic range, dark frame, ISO amplification, ISO-invariance.

**Common misconception:** "Read noise and shot noise are the same thing seen two ways." They are physically distinct. Shot noise scales as sqrt(signal) and comes from the light; read noise is roughly constant and comes from the electronics. They add in quadrature: total noise = sqrt(shot^2 + read^2). This formula, and its behavior across tones, is the backbone of the Theory section.

---

### Thermal Noise (Dark-Current Noise)

**Definition:** Thermal noise, or dark-current noise, arises from electrons that are liberated in the silicon by heat rather than by light. Even in total darkness, a warm sensor accumulates charge over time — the dark current. Because that accumulation is itself a random (Poisson) process, it carries its own shot-noise-like uncertainty equal to the square root of the number of thermally generated electrons. Dark current increases with exposure time and, strongly, with sensor temperature.

**Meaning in photographic practice:** Thermal noise is negligible for ordinary short exposures but becomes significant in long exposures — night photography, astrophotography, multi-second and multi-minute exposures — and in cameras that have been running hot (video, live view, warm ambient conditions). It appears as a rising noise floor and, characteristically, as hot pixels (below) that grow more numerous and brighter with exposure time and temperature. **FACT:** Dark current roughly doubles for every modest rise in sensor temperature; the exact doubling interval is device-specific. This is why cooled sensors are used in astronomy and why in-camera long-exposure noise reduction exists.

**Photoshop implementation:** Thermal noise and hot pixels are best addressed at capture (dark-frame subtraction, in-camera long-exposure NR) rather than in Photoshop, but you will encounter their residue as bright speckles in long-exposure night shots. Distinguishing them from stars in astro work is a real problem — a hot pixel sits at the same coordinate frame to frame, a star moves relative to the sensor if the mount tracks or the framing changes. See Photoshop Implementation for identification.

**Related concepts:** Hot pixels, dark frame, long-exposure noise reduction, shot noise, sensor temperature.

**Common misconception:** "Dark-current noise only matters for astrophotographers." It is a factor in any long exposure and in warm operating conditions; it simply hides beneath shot and read noise in normal daylight shooting.

---

### Fixed-Pattern Noise (FPN)

**Definition:** Fixed-pattern noise is spatial variation that is *not random from frame to frame* but instead fixed to particular photosites or readout structures. It comes in two flavors: dark FPN (each photosite has a slightly different dark offset and dark-current rate) and photo-response non-uniformity (PRNU, each photosite has a slightly different sensitivity/gain). Because FPN is fixed, it looks like a faint texture or pattern that stays put when the scene changes.

**Meaning in photographic practice:** Modern cameras remove most FPN internally — offset calibration and per-column correction handle much of it — so it is usually invisible in a single normal frame. It reasserts itself when you push extremely hard (heavy shadow lifting at high ISO) or when you average many frames to kill random noise: random noise averages toward zero, but fixed-pattern noise does *not* average away because it is the same every frame. Astro and long-exposure stackers meet FPN for exactly this reason and remove it with calibration frames (darks, flats, bias).

**Photoshop implementation:** If you stack frames to reduce noise and are left with a faint, repeatable texture or grid, you are looking at fixed-pattern noise (and possibly banding, below), not random noise — no amount of additional stacking removes it. It must be subtracted with calibration frames captured under matching conditions.

**Related concepts:** Banding, dark frame, flat frame, PRNU, frame averaging.

**Common misconception:** "Averaging more frames always makes it cleaner." Averaging removes *random* noise as 1/sqrt(number of frames); it does nothing to fixed-pattern noise, which is correlated across frames.

---

### Banding (Pattern Read Noise)

**Definition:** Banding is structured read noise that appears as faint horizontal or vertical stripes, arising from the sensor's readout architecture — row/column amplifiers, ADC channels, and power-supply interference that imprint a per-row or per-column offset. Unlike random read noise, banding is correlated along a line, so the eye — highly sensitive to straight lines — detects it at far lower amplitude than it would detect random speckle of the same magnitude.

**Meaning in photographic practice:** Banding is one of the most objectionable low-light artifacts precisely because it is structured. It typically appears when shadows are lifted aggressively, especially at high ISO, and it is strongly camera-dependent. Horizontal banding usually originates in row readout; vertical banding in column amplifiers or ADCs. **UNCERTAINTY:** Which cameras band, in which direction, and at which ISOs is device-specific and best determined from measured tests rather than assumed.

**Photoshop implementation:** Banding often lives predominantly in one channel (frequently the blue or red channel) and along one axis. It resists ordinary noise reduction — a bilateral or wavelet denoiser sees a low-amplitude, low-frequency structure and leaves it. Targeted removal uses the directionality: sampling a line, blurring along the band direction, or dedicated de-banding tools. This is covered practically in [Part 4](16D4_Camera_Raw_AI_Denoise_and_PS_Tools.md) and [Part 5](16D5_Frequency_Masking_and_Sharpening.md).

**Related concepts:** Read noise, fixed-pattern noise, channel inspection, shadow recovery, ISO.

**Common misconception:** "Banding is a compression artifact." Banding from readout is a capture-stage phenomenon in the RAW data. It can be *worsened* by 8-bit posterization and compression, but its origin is the sensor's readout electronics, not the file format. (Posterization from insufficient bit depth is a separate issue covered under quantization noise.)

---

### Hot Pixels, Stuck Pixels, and Dead Pixels

**Definition:** These are three distinct defects, often conflated:

- A **hot pixel** is a photosite with abnormally high dark current. It reads darker-than-normal in short exposures but grows progressively brighter with longer exposure time and higher temperature, eventually appearing as a bright (often colored) speck. Hot pixels are *conditional* — they emerge with exposure time, ISO, and heat.
- A **stuck pixel** reads a constant, abnormal value (often fully on — bright white/colored, or a fixed high value) regardless of exposure. It is "stuck" at one output.
- A **dead pixel** produces no signal at all — it reads black regardless of light. It is a non-responsive photosite.

**Meaning in photographic practice:** Hot pixels are the ones most photographers actually meet, and they multiply with exposure time and sensor age. Stuck and dead pixels are individual manufacturing/aging defects. All three are *fixed in location* — the same coordinate every frame — which is both how you identify them and how software removes them: a defect map or median-of-neighbors interpolation replaces the bad photosite with an estimate from its neighbors. Many are handled automatically during RAW demosaicing. **FACT:** Because these defects are single-photosite and fixed-location, they are trivially distinguished from random noise, which never repeats in the same place.

**Photoshop implementation:** A lone bright speck that persists at the same pixel coordinate across multiple frames of different scenes is a hot or stuck pixel, not noise. In Photoshop the Dust & Scratches filter, the Spot Healing Brush, or a small median on a targeted selection removes it. In long-exposure and astro work, dark-frame subtraction removes hot pixels wholesale. Do not attack them with global noise reduction — that degrades the whole image to remove a few specks.

**Related concepts:** Thermal noise, dark frame, defect map, median filter, dust removal.

**Common misconception:** "A bright colored dot in my night shot is chroma noise." A single, sharp, always-in-the-same-place dot is a hot or stuck pixel. Chroma noise is a *field* of random colored speckles that changes every frame. The remedy differs completely.

---

### Chrominance Noise vs Luminance Noise (Physical Origin)

**Definition:** Luminance noise is fluctuation in brightness; chrominance noise is fluctuation in color (hue/saturation) in regions that should be chromatically uniform. Chapter 16 defined both and their visual impact. Here the point is their *physical origin*: both begin as per-photosite noise on a color-filtered sensor, but chrominance noise is largely *created by the demosaicing step*. Each photosite sits under one color of the Bayer filter and measures only red, green, or blue. When demosaicing interpolates a full RGB triple for every pixel, independent noise in the separate color-filtered samples becomes correlated color error: a noisy-high red sample next to a noisy-low blue sample produces a pixel that is spuriously magenta.

**Meaning in photographic practice:** This origin explains why chrominance noise is spatially coarser and blotchier than luminance noise (the interpolation spreads a single-photosite error across a neighborhood), why it lives disproportionately in the channel with the least light (often blue), and why it can be suppressed aggressively with little loss of real detail — genuine scene color varies slowly across many pixels, while chroma noise varies pixel to pixel and can be smoothed in the color channels while leaving luminance sharp. The luminance/chroma separation that denoisers exploit is not arbitrary; it mirrors this physical structure.

**Photoshop implementation:** Inspect individual channels (Channels panel) to see that noise amplitude differs by channel; convert to a luminance/color representation (e.g. Lab mode, or Camera Raw's separate Luminance vs Color sliders) to treat them independently. The green channel, doubly sampled in the Bayer array, is usually cleanest and carries most luminance detail — see the per-channel discussion in Chapter 16 and [Part 5](16D5_Frequency_Masking_and_Sharpening.md).

**Related concepts:** Bayer color filter array, demosaicing, Lab color, per-channel noise, blue-channel noise.

**Common misconception:** "Color noise is real color from the scene." It is almost never scene color; it is interpolation-amplified per-channel noise. This is why aggressive color noise reduction is safe (Chapter 16).

---

### Quantization Noise

**Definition:** Quantization noise is the error introduced by representing a continuous analog charge as a finite set of discrete integer levels during analog-to-digital conversion, and again whenever bit depth is reduced (e.g. 14-bit RAW to 8-bit JPEG). Rounding each true value to the nearest available level introduces an error of up to half a level; over many samples this behaves like a small added noise of standard deviation approximately one level divided by sqrt(12).

**Meaning in photographic practice:** In a well-exposed RAW file with adequate ADC bit depth, quantization noise is far below shot and read noise and is irrelevant. It becomes visible in two situations: extreme tonal stretching of low-bit-depth data (lifting shadows in an 8-bit JPEG, where too few levels remain to describe a smooth gradient, producing *posterization/banding* in gradients), and editing in 8 bits when heavy adjustments demand more precision than 256 levels provide. **FACT:** This is the core reason to shoot RAW and edit in 16-bit — not because 8 bits looks bad initially, but because aggressive edits redistribute levels and expose the coarseness of the quantization. See [Chapter 01: Digital Image Fundamentals](01_Digital_Image_Fundamentals.md) for bit depth and gamma, and [Part 3](16D3_Denoising_Mindset_RAW_vs_JPEG.md) for why RAW gives the denoiser more to work with.

**Photoshop implementation:** Posterization from quantization appears as *banded steps* in what should be a smooth gradient (a sky), visible as distinct plateaus in the histogram (a comb of spikes with gaps). This is different from readout banding (stripes) and different from random noise (speckle). Working in 16-bit and, where possible, from RAW prevents it; adding a tiny amount of noise/dither can mask residual posterization (covered in [Part 5](16D5_Frequency_Masking_and_Sharpening.md)).

**Related concepts:** Bit depth, posterization, dithering, ADC, gamma encoding (Chapter 01), 8-bit vs 16-bit editing.

**Common misconception:** "Quantization noise and posterization are unrelated." Posterization *is* the visible symptom of quantization error when too few levels remain to describe a gradient smoothly.

---

### JPEG Compression Artifacts

**Definition:** JPEG artifacts are structured degradations introduced by lossy JPEG compression: 8×8 block boundaries ("blocking"), ringing/halos near high-contrast edges (from truncating high-frequency DCT coefficients — the "Gibbs" phenomenon), and color smearing from chroma subsampling (typically 4:2:0, storing color at half resolution in each axis). They are *deterministic consequences of the compression*, not random noise.

**Meaning in photographic practice:** JPEG artifacts look superficially like noise but are structured and edge-aligned, and they interact badly with subsequent editing and sharpening: sharpening amplifies ringing and block edges into visible grids and halos. Because the artifacts are correlated (blocks, rings), random-noise denoisers handle them poorly and dedicated JPEG-artifact removal is needed. This is a central reason RAW is preferable when noise is a concern — the JPEG engine has already discarded information and imposed structure. See [Part 3](16D3_Denoising_Mindset_RAW_vs_JPEG.md) for the RAW-vs-JPEG mindset in full.

**Photoshop implementation:** Zoom to 200-400% on a JPEG near a high-contrast edge and on a smooth area: the 8×8 blocking grid and edge ringing become visible. Chroma subsampling shows as color that "leaks" across luminance edges (a red sign bleeding onto its background). Photoshop's "Reduce Noise" filter has a "Remove JPEG Artifact" option aimed at this; it is a blunt instrument. Prevention (shoot RAW, or save JPEG at maximum quality only once) beats cure.

**Related concepts:** DCT, chroma subsampling, blocking, ringing, generation loss, RAW vs JPEG.

**Common misconception:** "Re-saving a JPEG at high quality restores it." Every JPEG save is lossy; artifacts accumulate with each generation (generation loss). Quality settings limit new damage but never undo prior damage.

---

### Demosaicing Artifacts

**Definition:** Demosaicing artifacts are errors introduced when the demosaicing algorithm interpolates a full-color image from the single-color-per-photosite Bayer data. The two signature artifacts are *maze/labyrinth* patterns (a wormy, zippering texture in fine detail where the interpolation guesses edge direction wrongly) and *color moire* (false rainbow color in fine repetitive patterns — fabric weave, distant railings, feathers — where the pattern frequency beats against the color filter array sampling grid).

**Meaning in photographic practice:** These are reconstruction errors, not photon noise, and they scale with how hard the demosaicer is working — worst in fine, high-contrast, near-Nyquist detail. They also interact with noise: noise perturbs the demosaicer's edge-direction estimates, so a noisy file demosaics with more maze artifacts, and denoising *before* or *during* demosaic (as Camera Raw does on RAW data) produces cleaner reconstruction than denoising rendered pixels afterward — a further argument for the RAW-stage processing emphasized in Chapter 16 and [Chapter 03: Camera Raw](03_Camera_Raw.md).

**Photoshop implementation:** Maze and moire are visible at 200-400% in fine detail and are best mitigated at the RAW stage (better demosaic algorithm, Camera Raw's color-moire and detail controls) rather than in Photoshop. An optical low-pass (anti-aliasing) filter on the sensor trades a little sharpness to suppress moire at capture; many modern high-resolution bodies omit it, trading moire risk for resolution.

**Related concepts:** Bayer array, Nyquist frequency, anti-aliasing filter, color moire, chrominance noise, RAW development (Chapter 03).

**Common misconception:** "Moire is noise I can denoise away." Moire is a structured aliasing artifact; a noise reducer will not remove it. It requires demosaic-stage handling or targeted color/frequency work ([Part 5](16D5_Frequency_Masking_and_Sharpening.md)).

---

### ISO (Amplification, not Sensitivity)

**Definition:** ISO in a digital camera is a scaling applied to the captured signal on its way to a digital number — historically an analog gain applied before or at the ADC, and/or a digital multiplication. It sets how a given quantity of collected photons maps onto the output brightness scale. It does *not* change how many photons the sensor collects, nor (in general) the sensor's fundamental sensitivity to light.

**Meaning in photographic practice:** ISO's real job is to place the captured signal at a usable brightness given the exposure you chose. Raising ISO for a fixed aperture and shutter speed does not add signal; it amplifies the weak signal (and its noise) so the image is not black. The reason high-ISO images *look* noisy is that you reach for high ISO precisely when little light is available — the noise is a symptom of the low light, and ISO is the messenger. On many cameras, higher analog ISO actually *reduces* the impact of downstream read noise (the signal is amplified above the noise added later in the chain), which is the seed of ISO-invariance, below.

**Photoshop implementation:** None directly, but the correct mental model changes your capture decisions and your shadow-recovery expectations. See the extended Theory treatment "Why 'High ISO Causes Noise' Is an Oversimplification."

**Related concepts:** Exposure, read noise, ISO-invariance, ETTR, gain.

**Common misconception:** The central one this part exists to correct: "High ISO causes noise." High ISO reveals and amplifies noise that low light made unavoidable; the same low light captured at low ISO and then brightened in software is generally as noisy or noisier. Full treatment below.

---

### ISO-Invariance

**Definition:** A camera is described as (approximately) ISO-invariant over some range if brightening a low-ISO underexposed file in software gives nearly the same noise result as having shot at the correspondingly higher ISO in-camera. This holds when read noise added *after* the ISO amplification stage is negligible, so amplifying in-camera versus amplifying in software makes little difference.

**Meaning in photographic practice:** On a strongly ISO-invariant body you can protect highlights by shooting at a lower ISO and lift the shadows later with little penalty; on a less invariant body, raising ISO in-camera meaningfully cleans the shadows because it lifts the signal above downstream read noise before it is added. This directly affects the "expose to protect highlights, lift later" strategy. **UNCERTAINTY:** The degree of ISO-invariance is strongly camera-dependent and even ISO-range-dependent within one body (many cameras have a "dual conversion gain" step where behavior changes at a particular ISO). Treat any specific claim as something to *test* with your own body, not to assume. Measured data from independent testers is the reliable source.

**Photoshop implementation:** Test it yourself: shoot a static scene at base ISO underexposed by N stops and again at the ISO N stops higher correctly exposed; develop both to matching brightness; compare shadow noise at 100%. This experiment is set out as an exercise below.

**Related concepts:** Read noise, ISO amplification, dual conversion gain, ETTR, dynamic range.

**Common misconception:** "ISO-invariance means ISO doesn't matter." It means *where* the amplification happens matters less on some cameras. It never means light doesn't matter — the amount of light collected still sets SNR entirely.

---

## Theory

### SNR and the Poisson Square-Root Law

Everything numeric in noise physics rests on one fact about light: photon arrivals follow a Poisson distribution, and for a Poisson distribution the standard deviation equals the square root of the mean. If a photosite collects, on average, N photoelectrons of signal, then the random frame-to-frame (and photosite-to-photosite) variation in that count has standard deviation sqrt(N).

The shot-noise-limited SNR is therefore:

SNR = signal / noise = N / sqrt(N) = sqrt(N)

Work the canonical examples:

| Photoelectrons collected (N) | Shot noise (sqrt N) | Relative noise (sqrt N / N) | Shot-limited SNR (sqrt N) |
|------------------------------|---------------------|-----------------------------|---------------------------|
| 1,000,000 | 1,000 | 0.1% | 1,000 |
| 100,000 | ~316 | ~0.32% | ~316 |
| 10,000 | 100 | 1% | 100 |
| 1,000 | ~31.6 | ~3.2% | ~31.6 |
| 100 | 10 | 10% | 10 |
| 25 | 5 | 20% | 5 |
| 9 | 3 | ~33% | 3 |

Read the table as a story about tones within one image. A bright highlight photosite near full well might hold on the order of tens of thousands of electrons and enjoy an SNR in the hundreds — visually flawless. A deep-shadow photosite holding a hundred electrons has a relative noise of 10% — visibly gritty. A near-black photosite holding a handful of electrons is dominated by its own counting uncertainty and, once read noise is added, is essentially unrecoverable. Same frame, same instant, same sensor: the difference is purely how many photons landed.

Two consequences deserve emphasis:

**More light helps, but sublinearly.** Because SNR scales as sqrt(N), you must *quadruple* the collected light to *double* the SNR (a 2× SNR gain = two stops). One stop more light (2× the photons) improves SNR by only sqrt(2) ≈ 1.41×. This is why "just expose brighter" is genuinely effective yet has diminishing returns, and why the jump from a tiny sensor to a large one (which can gather several stops more total light) produces a visible, but not unlimited, cleanliness improvement.

**Noise is always worst where signal is weakest.** This is the quantitative form of Chapter 16's "noise is a shadow problem." It is not that shadows generate extra noise; it is that shadows carry little signal, and sqrt(N)/N grows as N shrinks.

### Adding Noise Sources in Quadrature

Shot noise is not the only contributor. The total noise seen at a photosite is the quadrature sum of independent sources — most importantly shot noise and read noise:

total_noise = sqrt( shot^2 + read^2 ) = sqrt( N + read_noise^2 )

(using shot^2 = N electrons^2, since variance of a Poisson variable equals its mean). Suppose, illustratively, a read noise of 4 electrons — **UNCERTAINTY: this is a round number for arithmetic, not a spec for any camera; real values are ISO- and body-specific and must be looked up in measured data.** Then:

| Signal N (e-) | Shot noise sqrt(N) | Read noise (assumed 4 e-) | Total noise sqrt(N + 16) | Dominant source | SNR = N / total |
|---------------|--------------------|---------------------------|--------------------------|-----------------|-----------------|
| 10,000 | 100 | 4 | ~100.08 | Shot (read negligible) | ~99.9 |
| 1,000 | ~31.6 | 4 | ~31.9 | Shot | ~31.4 |
| 100 | 10 | 4 | ~10.8 | Shot, read now noticeable | ~9.3 |
| 16 | 4 | 4 | ~5.66 | Shot ≈ read (crossover) | ~2.8 |
| 4 | 2 | 4 | ~4.47 | Read dominates | ~0.9 |
| 0 (black) | 0 | 4 | 4 | Read only | 0 |

The table makes the quadrature behavior concrete. In bright and midtone regions the total is essentially the shot noise; the read noise is buried and irrelevant. Only as the signal falls toward the read-noise level (here, around 16 electrons where shot noise sqrt(16)=4 equals the assumed read noise) does read noise start to matter, and below that it dominates. The *crossover point* — the signal level where shot and read noise are equal — is one useful way to characterize a sensor's shadow behavior: below it, you are read-noise-limited and stuck; above it, you are shot-noise-limited and can only improve by collecting more light.

This quadrature structure also explains why chasing secondary noise sources rarely pays until the dominant one is handled. If shot noise is 100 and read noise is 4, halving the read noise to 2 changes the total from 100.08 to 100.02 — invisible. Engineering effort (and denoising effort) is best spent on whichever source dominates the tones you care about.

### The Dynamic Range Connection

Dynamic range, at the photosite level, is the ratio of the largest signal it can hold (full-well capacity, where the bucket overflows and highlights clip) to the smallest signal it can distinguish (set by the read-noise floor). Roughly:

DR (stops) ≈ log2( full_well_capacity / read_noise )

This ties noise physics directly to what a file can survive in post. A large full well and a low read noise mean a wide DR: highlights hold detail and shadows stay above the noise floor, so aggressive tonal moves (the Curves and Levels work of [Chapter 07](07_Tonal_Adjustments.md)) do not immediately expose noise. This is the physical substrate under "expose to the right": pushing exposure up fills the well further, raising N everywhere and thus SNR everywhere, at the cost of highlight headroom. **UNCERTAINTY:** Full-well and read-noise values, and hence DR, are body- and ISO-specific; use measured data, and note that raising ISO generally *reduces* full-well headroom and thus usable DR.

### The Causal Chain: From Camera Settings to SNR

It is worth assembling the whole chain explicitly, because every "noise" lever a photographer touches acts through the number of photoelectrons N:

1. **Scene luminance** — brighter subject or added light means more photons per second toward the lens. Directly raises N.
2. **Aperture** — a wider aperture (smaller f-number) increases the light-collecting area of the lens, so more photons per second reach the sensor. Each full stop doubles N. Directly raises N and thus SNR (by sqrt(2) per stop).
3. **Shutter speed / exposure time** — longer exposure integrates photons for longer, raising N linearly with time. Doubling exposure time doubles N (until motion blur or highlight clipping intervenes). Raises SNR; but very long exposures also raise thermal noise.
4. **Photosite area (sensor size and pixel pitch)** — a larger photosite intercepts more of the image-forming light, collecting more photons for the same scene and exposure. This is why, per pixel, larger photosites (bigger sensor, or fewer/larger pixels) have higher SNR. At the *whole-image* level the comparison is subtler — see below.
5. **Quantum efficiency** — the fraction of incident photons actually converted to collected electrons. Higher QE means more N from the same light. This is a genuine sensor-technology difference between generations.
6. **ISO** — after N is fixed by all of the above, ISO scales the result to a usable brightness. It does *not* change N. It changes where amplification happens relative to read noise (see ISO-invariance) but cannot add signal.

Only steps 1-5 change the physical SNR of the capture. ISO (step 6) is bookkeeping applied afterward. This ordering is the antidote to the "ISO causes noise" confusion: the noise was set by how much light steps 1-5 delivered; ISO merely made it visible at normal brightness.

### Sensor Size and the Per-Pixel vs Per-Image Trap

A recurring confusion deserves its own treatment. Take two sensors of the same physical size, one with 12 MP (large photosites) and one with 48 MP (small photosites), same generation and QE. Per pixel, the 12 MP sensor's larger photosites collect ~4× the photons and thus have ~2× (one stop) better SNR per pixel. It is tempting to conclude the low-resolution sensor is "cleaner." But at a fixed output size — same print, same screen dimensions — you downsample the 48 MP file, averaging groups of ~4 pixels together, and averaging four independent noisy samples improves their SNR by sqrt(4) = 2×, exactly recovering the difference. **INTERPRETATION:** To first order, for the same sensor area, generation, and output size, total-light collection — not pixel count — sets image-level noise. More pixels give more resolution, not inherently more noise, provided you compare at a fixed viewing scale. The larger *sensor* is cleaner than the smaller *sensor* because it collects more total light; the higher-*megapixel* sensor of the same size is not inherently noisier at fixed output size. Confusing "smaller pixels" with "smaller sensor" is the root of endless megapixel-vs-noise arguments. This distinction is applied to real old-sensor diagnosis in [Part 2](16D2_Old_Sensors_and_Visual_Diagnosis.md).

### Why "High ISO Causes Noise" Is an Oversimplification

This is the conceptual centrepiece of Part 1, so it gets an emphatic, standalone treatment.

**The claim, stated fairly:** In everyday use, "high ISO causes noise" is a serviceable rule of thumb — high-ISO photos *do* look noisier. The reason it is an oversimplification is that it points at the wrong cause and therefore recommends the wrong fixes.

**What actually happens.** Noise level is set by N, the number of photoelectrons collected, which is fixed by scene light, aperture, and shutter time. You typically raise ISO in exactly the situations where N is small: dim light, or a shutter speed kept short to freeze motion, or an aperture kept narrow for depth of field. The high ISO is a *response* to low light, and the low light is what produced the low SNR. The ISO amplification then scales that low-SNR signal up to normal brightness, making the pre-existing noise visible. Correlation, not causation: high ISO co-occurs with noise because both are consequences of insufficient light.

**The decisive thought experiment.** Photograph a dim scene two ways, at the *same aperture and shutter speed* (so N is identical in both):

- Frame A: shot at high ISO in-camera, correctly bright.
- Frame B: shot at base ISO, coming out very dark, then brightened by the same number of stops in software.

Both collected the same photons. Both therefore have the same shot noise. The only possible difference is read noise and where amplification occurred. On a strongly ISO-invariant camera, A and B look nearly identical — proof that the high ISO in A did not "add" noise; the noise was in the light budget, not the ISO dial. On a less invariant camera, A may actually be *cleaner* than B, because raising ISO in-camera amplified the signal above the read noise added later in the chain — the opposite of "ISO causes noise."

**The corollary that trips people up: deliberate underexposure is not a free lunch.** A photographer who believes "high ISO causes noise" may "protect image quality" by shooting at low ISO in low light, accepting a very dark frame, and lifting it in post. Physically this collects the same few photons as a high-ISO shot and then, in the lift, amplifies the same shot noise *plus* whatever read noise (and banding, and quantization if the file is not RAW/16-bit) sits below. The result is noise equal to a correctly exposed high-ISO frame, and frequently *worse* — because in-camera high ISO on many bodies suppresses downstream read noise, and because lifting a low-bit-depth or JPEG file adds quantization and posterization the high-ISO JPEG would not have. **RECOMMENDATION:** In low light, expose to collect as much light as the scene and your motion/DOF constraints allow (open up, slow down, add light), then choose ISO to place that exposure at a usable brightness. Do not chronically underexpose at low ISO believing you are avoiding noise; you are usually adding it. The exception is deliberately protecting highlights on an ISO-invariant body — a controlled underexposure of the *highlights* with a plan to lift, which is a different maneuver from simply shooting dark.

**What high ISO genuinely does cost.** Raising ISO reduces highlight headroom (the amplified signal clips sooner), so usable dynamic range shrinks at high ISO. That is a real cost of high ISO — but it is a dynamic-range cost, not a "creates noise" cost. Keeping the two straight is the mark of understanding the physics rather than the folklore.

**Summary of the correction:** Noise comes from too few photons. ISO is how you brighten a photon-starved capture; it reveals the noise, it does not create it. The fix for noise is more light, not a lower ISO number at the same exposure — indeed lowering ISO at the same exposure and lifting later usually makes things worse.

### Distinguishing Random Noise from Structured Degradations

A final theoretical point that pays off constantly in practice. Group the degradations by their statistical structure, because the structure dictates the remedy:

| Degradation | Random per frame? | Fixed location? | Structured/correlated? | Scales with signal? | Right remedy |
|-------------|-------------------|-----------------|------------------------|---------------------|--------------|
| Shot noise | Yes | No | No | Yes (as sqrt N) | More light; denoise |
| Read noise | Yes | No | No | No (constant) | More light above floor; denoise; lower-read-noise ISO |
| Thermal / dark current | Yes (its shot part) | Hot pixels yes | Partly | Grows with time/heat | Shorter/cooler exposure; dark-frame subtraction |
| Fixed-pattern noise | No (repeats) | Yes | Yes | Varies | Calibration-frame subtraction |
| Banding | No (repeats) | Along lines | Yes (directional) | No | Directional/de-band tools |
| Hot/stuck/dead pixels | No (repeat) | Yes (single px) | Point defects | Hot: with time/heat | Defect map; median; dark frame |
| Quantization / posterization | Deterministic | No | Stepped | No | 16-bit; RAW; dither |
| JPEG artifacts | Deterministic | Block grid | Yes (blocks/rings) | No | Shoot RAW; artifact removal |
| Demosaic maze / moire | Deterministic | Detail-dependent | Yes (aliasing) | No | RAW demosaic controls; OLPF |

The single most useful diagnostic question is: **does it change when I take another frame of the same scene, and is it in the same place?** Random noise (shot, read, thermal's random part) reshuffles every frame and sits nowhere in particular. Fixed-pattern noise, banding, hot pixels, JPEG blocking, and moire are correlated — repeatable, located, structured — and they laugh at random-noise denoisers. Getting this classification right before you reach for a tool is what separates deliberate correction from flailing. It is the theme of the diagnostic workflow in [Part 8](16D8_Quality_Control_and_Cheat_Sheet.md).

---

## Photoshop Implementation

Photoshop is not a scientific instrument, and none of the following yields calibrated electron counts. But you can *observe* every noise type discussed above and *coarsely measure* the ones that matter, which is enough to drive good decisions. The tools are the Info panel, the Channels panel, the Histogram panel on a tight selection, and disciplined use of 100%/200%/400% zoom.

### Rule Zero: Evaluate Noise Only at 100% or Higher

Noise is a per-pixel phenomenon. Below 100% zoom, the screen is *downsampling* the image — averaging pixels — which suppresses noise exactly as downsizing for the web does, and hides what you are trying to judge. **RECOMMENDATION:** Assess noise at 100% for a realistic per-pixel view, and at 200-400% to identify *type* (blocking grid, banding lines, maze, single hot pixels). Always judge in a smooth region (for noise visibility) and a detailed region (for detail cost) — the paired-inspection habit from Chapter 16.

### Measuring SNR Coarsely with the Info Panel and Histogram

To estimate SNR in a region that *should* be uniform (a patch of clear sky, a gray card, a smooth wall):

1. Open the Info panel (Window > Info) and the Histogram panel (Window > Histogram, set to a single channel or Luminosity, Expanded View so it shows Mean and Std Dev).
2. Make a tight rectangular Marquee selection entirely inside the uniform region — no edges, no texture.
3. Read **Mean** (this stands in for signal S) and **Std Dev** (this stands in for noise sigma) from the Histogram panel for that selection.
4. Estimate SNR ≈ Mean / Std Dev for that patch and channel.

Do this on a bright uniform patch and a dark uniform patch in the same image. **FACT:** The bright patch will show a *larger* absolute Std Dev but a *much larger* Mean, giving a high SNR; the dark patch shows a smaller absolute Std Dev but a tiny Mean, giving a low SNR. This directly demonstrates the sqrt(N) story: absolute noise rises with brightness while *relative* noise (and thus visible grittiness) falls. Watching the two numbers move confirms the physics on your own files.

Caveats: Photoshop's values are on the gamma-encoded 0-255 (or 0-32768 in 16-bit) scale, not linear electrons, so this is qualitative. Gamma encoding compresses highlights and stretches shadows (see [Chapter 01](01_Digital_Image_Fundamentals.md)), exaggerating the shadow Std Dev — which is itself a useful reminder of why shadow noise is so visible after rendering.

### Separating Shot-Type from Read-Type Behavior by Inspection

You cannot label a photosite's noise as "shot" or "read" from one file, but you can see their signatures:

- Compare a well-exposed frame and a heavily-lifted underexposed frame of the same scene. Noise that *grows disproportionately in the deepest shadows* as you lift is the read-noise floor becoming visible — its relative size explodes because the signal there is near zero.
- Noise that is present but *proportionally modest across midtones and mild in highlights* is shot-noise-dominated behavior.

### Channel Inspection: Seeing Chrominance Noise's Origin

Open the Channels panel (Window > Channels) and click Red, Green, Blue in turn to view each as grayscale at 100-200% over a shadow region:

- The **green** channel is typically cleanest (double-sampled in the Bayer array) and carries most luminance detail.
- The **blue** channel is often noisiest (least light in many scenes/illuminants), and frequently the home of banding.
- Because the three channels carry *independent* noise, their disagreement at each pixel is what the eye reads as *color* speckle after they are combined — this is chrominance noise made visible. Toggling channels demonstrates why treating color noise separately (Chapter 16; Camera Raw's Color slider) is safe and effective.

To see luminance vs chroma cleanly, convert a copy to Lab (Image > Mode > Lab Color) and inspect the L channel (luminance noise) versus the a and b channels (chroma noise) separately. The a/b channels reveal the coarse, blotchy character of chroma noise that the interpolation produced.

### Identifying the Structured Degradations

- **Banding:** At 200-400% in a lifted shadow, look for faint horizontal or vertical stripes; confirm by checking individual channels (banding often lives in one channel/axis). If it survives ordinary noise reduction, it is banding, not speckle.
- **Hot/stuck pixels:** Lone bright (often colored) specks that sit at the *same coordinate* across different frames. Confirm by opening two unrelated long exposures from the same body and comparing coordinates. Remove locally (Spot Healing Brush, Dust & Scratches, or a targeted median), never with global NR.
- **Posterization (quantization):** Look at a smooth gradient (sky) for stepped plateaus, and at the Histogram for a comb of spikes with gaps — the fingerprint of too few remaining levels. More likely in 8-bit/JPEG files after heavy edits.
- **JPEG artifacts:** At 300-400% near a high-contrast edge, look for the 8×8 blocking grid, edge ringing/halos, and color bleeding across luminance edges (chroma subsampling). Present in JPEGs, absent in freshly-developed RAW.
- **Demosaic maze/moire:** At 200-400% in fine, high-contrast repetitive detail (fabric, distant railings, fine foliage), look for wormy zippering (maze) and false rainbow color (moire). These are RAW-development-stage problems; note them for correction in [Part 4](16D4_Camera_Raw_AI_Denoise_and_PS_Tools.md).

### A Quick Diagnostic Command for Structured vs Random Noise

If you have two exposures of the same static scene from the same body and want to see what is *fixed* (pattern noise, hot pixels, banding) versus *random* (shot/read), difference them: stack the two as layers, set the top layer to Difference blend mode, and flatten. What remains bright in the difference is the *random* component (it did not cancel); what cancels toward black was identical in both frames (the scene *and* any perfectly fixed pattern). For isolating fixed-pattern noise instead, subtract a dark frame (a same-settings exposure with the lens cap on) from the light frame — what remains of the dark frame *is* the fixed pattern plus hot pixels. If you prefer the command line for the difference of two exports, ImageMagick does it in one line:

```
magick frameA.tif frameB.tif -compose difference -composite -auto-level diff_random_noise.tif
```

**INTERPRETATION:** This difference-of-frames technique is the single most convincing way to *see*, on your own gear, that shot/read noise is random (never cancels) while hot pixels and fixed-pattern noise are fixed (cancel against a matching frame). It is the empirical version of the classification table in the Theory section.

---

## Professional Workflow

The physics in this part precedes any Photoshop step; the workflow payoff is that it changes decisions at capture and at ingest, before denoising even begins.

**At capture — maximize N, then set ISO.** The governing move is to collect light. In priority order for a given scene: add or wait for more light; open the aperture as far as depth of field allows; lengthen the shutter as far as motion (subject and camera) allows; only then raise ISO to place the exposure at usable brightness. This ordering falls directly out of the causal chain — steps 1-4 raise N and thus SNR; ISO does not. On a tripod with a static subject, a longer exposure at lower ISO genuinely collects more light and is cleaner; handheld or with a moving subject, raising ISO to keep the shutter fast is correct, because a blurred low-noise frame is worthless. **RECOMMENDATION:** Decide your motion and DOF constraints first; they cap shutter and aperture; then light and ISO are the free variables, and you push light before ISO.

**Protect highlights deliberately, not accidentally.** Expose-to-the-right (Chapter 16) fills the well and raises SNR everywhere, but only up to highlight clipping. On a body you have tested for ISO-invariance, you may intentionally hold ISO down to protect highlights and plan to lift shadows — a controlled decision. This is entirely different from chronic underexposure "to avoid noise," which the physics shows is counterproductive.

**Shoot RAW when noise is in play.** RAW keeps the data linear and high-bit-depth, so denoising and demosaicing happen with maximum information and before gamma stretches the shadow noise (Chapter 16; [Chapter 03](03_Camera_Raw.md)). It also avoids the JPEG engine's in-camera noise reduction, sharpening, and compression artifacts, which are baked and irreversible. The full RAW-vs-JPEG argument, including when JPEG is acceptable, is [Part 3](16D3_Denoising_Mindset_RAW_vs_JPEG.md).

**Manage thermal noise for long work.** For multi-second and multi-minute exposures, or long video/live-view sessions, expect thermal noise and hot pixels to rise. In-camera long-exposure NR (which shoots and subtracts a dark frame) or manual dark-frame subtraction is the correct capture-stage remedy; do not try to fix hot pixels with global denoising later.

**Classify before you correct.** At ingest, spend the seconds to classify what you are actually seeing — random speckle, chroma blotches, banding, hot pixels, JPEG blocks, moire — because each routes to a different tool. This classification, built on the Theory table above, is the entry point to the practical denoising decisions in [Part 4](16D4_Camera_Raw_AI_Denoise_and_PS_Tools.md) and the QC pass in [Part 8](16D8_Quality_Control_and_Cheat_Sheet.md).

---

## Common Mistakes

**Believing the ISO dial creates noise, and underexposing at low ISO to avoid it.** The most common and most damaging error. Underexposing at low ISO and lifting later collects the same photons as a high-ISO shot and adds read-noise, quantization, and banding on the lift — usually worse than simply using appropriate ISO. Push light, not the ISO number, and only underexpose deliberately to protect highlights on a tested ISO-invariant body.

**Comparing noise at less than 100% zoom.** Fit-to-screen downsampling hides noise. Any judgment of noise (or of a denoiser's result) made below 100% is unreliable. Judge at 100%, diagnose type at 200-400%.

**Confusing "small pixels" with "small sensor."** More megapixels on the same sensor area do not mean a noisier image at fixed output size — downsampling recovers the per-pixel SNR difference. It is total light collection (largely sensor area and exposure), not pixel count, that sets image-level noise.

**Attacking structured degradations with a random-noise denoiser.** Banding, hot pixels, JPEG blocking, fixed-pattern noise, and moire are correlated, not random. A luminance/chroma denoiser tuned for speckle will not remove them and may smear detail trying. Diagnose the structure first, then use the matching remedy (directional de-band, defect map/median, dark-frame subtraction, RAW demosaic controls).

**Treating chroma speckle as real color.** Chrominance noise is interpolation-amplified per-channel noise, not scene color; suppress it freely. Conversely, treating a single fixed colored dot as chroma noise wastes a global operation on what is a hot pixel — remove it locally.

**Editing heavily in 8-bit and blaming the sensor for posterization.** Banded gradients after strong tonal moves on an 8-bit/JPEG file are quantization/posterization, a bit-depth problem, not sensor noise. Work in 16-bit from RAW.

**Ignoring thermal noise in long exposures.** Hot pixels and a rising floor in night and astro work are dark-current effects tied to time and temperature; they are a capture-stage problem solved with dark frames, not a post-processing denoise problem.

**Chasing a secondary noise source while a dominant one remains.** Because noise adds in quadrature, halving a read noise that is already tiny next to shot noise does nothing visible. Identify the dominant source for the tones you care about and address that.

---

## Summary

A digital photograph is a count of photoelectrons, and noise is the uncertainty in and the corruption of that count. The unifying quantity is signal-to-noise ratio, SNR = signal / noise-standard-deviation, and the foundational fact is that light is Poisson-distributed, so shot noise equals sqrt(N) and the shot-limited SNR equals sqrt(N). Consequently more light raises SNR but only as its square root (four times the light for twice the SNR), and noise is always worst where signal is weakest — the quantitative core of "noise is a shadow problem."

Shot noise (from light, scales as sqrt of signal) and read noise (from readout electronics, roughly constant) add in quadrature; shot noise dominates midtones and highlights, read noise dominates the deepest shadows, and their crossover characterizes a sensor's shadow behavior. Thermal (dark-current) noise rises with exposure time and temperature and spawns hot pixels. Fixed-pattern noise and banding are *structured* read-side degradations that repeat frame to frame and defeat random-noise denoisers; they require calibration-frame subtraction or directional tools. Hot, stuck, and dead pixels are distinct fixed-location point defects. Quantization noise (bit-depth/ADC) surfaces as posterization when too few levels remain after heavy edits. JPEG artifacts (blocking, ringing, chroma subsampling) and demosaicing artifacts (maze, moire) are deterministic, structured degradations — not photon noise — and respond to different remedies. Chrominance noise, specifically, is largely manufactured by demosaicing turning independent per-channel noise into correlated color error, which is why it is coarse, channel-biased, and safe to suppress hard.

The causal chain runs: scene light, aperture, shutter time, photosite area, and quantum efficiency together fix N (and thus SNR); ISO then merely scales the captured signal to a usable brightness. Therefore "high ISO causes noise" is an oversimplification that names the wrong cause: high ISO co-occurs with noise because both follow from too little light, and amplifying a photon-starved capture merely reveals the noise. Critically, underexposing at low ISO and lifting in post collects the same few photons and adds read noise, quantization, and banding on the lift — producing noise equal to, or worse than, a correctly exposed high-ISO frame. ISO-invariance describes how little this matters on some bodies, but it is strongly camera-dependent and must be tested, not assumed; the real, non-negotiable cost of high ISO is reduced dynamic range from lost highlight headroom, not "created" noise.

The practical throughline: collect more light before touching ISO, shoot RAW and edit in 16-bit when noise matters, and classify each degradation by structure — random vs fixed, signal-dependent vs signal-independent — before choosing a tool. That classification carries directly into the diagnosis of old sensors in [Part 2](16D2_Old_Sensors_and_Visual_Diagnosis.md), the RAW-vs-JPEG mindset in [Part 3](16D3_Denoising_Mindset_RAW_vs_JPEG.md), and every tool decision from [Part 4](16D4_Camera_Raw_AI_Denoise_and_PS_Tools.md) onward.

---

## Exercises

### Exercise 1: The Poisson Table by Hand

Without a computer, fill in a table for photoelectron counts N = 10,000, 2,500, 400, 64, and 16. For each, compute the shot noise sqrt(N), the relative noise sqrt(N)/N as a percentage, and the shot-limited SNR (which equals sqrt(N)). Then answer: how many stops of extra light (each stop = 2× N) are needed to improve SNR by a factor of two? Confirm your answer against two rows of the table.

**Success criterion:** You can produce the sqrt(N) relationship from memory and correctly state that doubling SNR requires quadrupling light (two stops), because SNR scales as the square root of N.

### Exercise 2: Measuring SNR in Your Own File

Open a well-exposed daylight RAW file and develop it with all noise reduction set to zero. In Photoshop, use the Histogram panel (Expanded View, Mean and Std Dev) on tight Marquee selections placed entirely inside (a) a bright smooth patch (clear sky or a lit gray surface) and (b) a dark smooth patch (a shadow). Record Mean and Std Dev for each, in the Luminosity channel and then in the Blue channel.

1. Compute Mean/Std Dev (your coarse SNR) for each patch.
2. Which patch has the larger *absolute* Std Dev? Which has the larger *SNR*?
3. Is the Blue channel noisier than Luminosity in the dark patch? Why would that be expected?

**Success criterion:** You observe that the bright patch has higher SNR despite (often) a larger absolute Std Dev, confirming the sqrt(N) relationship, and you can explain the blue channel's typically worse SNR.

### Exercise 3: Seeing Chrominance Noise's Origin in the Channels

Take a high-ISO or heavily-lifted file. In the Channels panel, inspect Red, Green, and Blue individually at 200% over a shadow region and rank them by noise. Then convert a copy to Lab and inspect L versus a and b over the same region.

1. Which RGB channel is cleanest, and does that match the expected Bayer explanation?
2. In Lab, is the coarse, blotchy speckle concentrated in a/b (chroma) or L (luminance)?
3. Explain, in one or two sentences, why independent per-channel noise becomes visible *color* speckle after the channels combine.

**Success criterion:** You can connect the per-channel noise you see to the demosaicing origin of chrominance noise and justify why color noise reduction is safe.

### Exercise 4: Classify the Degradation

Assemble four crops at 300% zoom: a smooth gradient sky from a heavily-edited JPEG; a lifted shadow from a high-ISO RAW; a fine repetitive texture (distant fabric or railings); and a long night exposure. For each, name the dominant degradation you see (posterization, random speckle/banding, moire/maze, or hot pixels/thermal) and state one diagnostic feature that identifies it and one correct remedy.

**Success criterion:** You correctly classify each degradation by its structural signature and pair it with the appropriate remedy rather than defaulting to generic noise reduction.

---

## Advanced Exercises

### Advanced Exercise 1: Random vs Fixed — The Difference-Frame Proof

With a tripod and a static scene (or lens cap on for a pure dark frame), shoot two identical exposures from the same body at the same settings. Bring both into Photoshop as layers, set the top layer to Difference blend mode, and add an aggressive Levels/auto-level to amplify what remains.

1. What survives the difference (does not cancel)? What does that tell you about its statistical nature?
2. Now subtract a matching dark frame from a light frame instead. What survives now, and how does it differ from the result above?
3. From these two results, identify which noise sources are random (reshuffle every frame) and which are fixed (repeat at the same location).

**Success criterion:** You empirically demonstrate that shot/read noise is random (never cancels between two frames) while hot pixels and fixed-pattern noise are fixed (cancel against a matching frame), and you can map each result to the classification table in the Theory section.

### Advanced Exercise 2: Testing Your Camera's ISO-Invariance

On a tripod, photograph a static scene containing a full range of tones. Shoot Frame A correctly exposed at, say, ISO 3200. Shoot Frame B at base ISO with the *same aperture and shutter speed* (so it is severely underexposed, by the corresponding number of stops). In Camera Raw, lift Frame B by exactly that many stops to match Frame A's brightness, matching white balance and tone. Compare shadow noise at 100%.

1. How closely do the shadows of A and B match? Is A cleaner, or are they nearly identical?
2. What does the result tell you about where your camera's read noise is added relative to ISO amplification?
3. Repeat the test straddling a suspected dual-conversion-gain ISO if your body has one, and note whether the behavior changes across that boundary.

**Success criterion:** You produce your own evidence for how ISO-invariant your specific body is, understand that the answer is body- and ISO-range-specific (an UNCERTAINTY not to be assumed), and can articulate the highlight-protection strategy that follows.

### Advanced Exercise 3: The Underexposure Penalty

Take one static scene and produce three developed results at matched final brightness: (A) correctly exposed at a moderate ISO; (B) underexposed by three stops at base ISO, then lifted three stops in Camera Raw from the RAW; (C) the same three-stop underexposure exported to JPEG first, then lifted three stops in Photoshop in 8-bit. Compare shadow noise and gradient smoothness at 100%.

1. Rank A, B, C by shadow cleanliness and by gradient smoothness.
2. Attribute C's extra degradation to specific sources (quantization/posterization, JPEG artifacts) beyond the shot/read noise shared with B.
3. State the workflow lesson for low-light capture and file format.

**Success criterion:** You demonstrate that underexposure-plus-lift is not free, that RAW/16-bit limits the damage relative to JPEG/8-bit, and that neither beats collecting adequate light in the first place.

---

## Blackbelt Challenge

You are handed three files and their capture metadata and must reason from physics alone — no denoising yet — to a complete diagnosis and a defensible capture-and-ingest plan for each. Do not open a single noise-reduction slider; this challenge is about understanding the forward process well enough to predict and prescribe.

**File A — Concert, handheld.** ISO 12800, 1/250 s, f/2.8, modern full-frame body, JPEG straight from camera (the photographer had no time to shoot RAW). Deep shadows behind the performer show colored blotches; a smooth spotlight wash on the back curtain shows faint stepping; and at 300% there are 8×8 grids near the stage-light edges.

**File B — Coastal long exposure.** ISO 100, 120 s, f/11, tripod, RAW, warm summer evening, no in-camera long-exposure NR. The sky and water are clean, but scattered bright red and white specks dot the frame, several of them at identical coordinates in a second 120 s frame taken minutes later.

**File C — Deliberately underexposed portrait.** ISO 200, 1/60 s, f/4, RAW, indoor tungsten, exposed three stops dark "to protect the file from ISO noise," then lifted three stops in Camera Raw. The shadows on the subject's jacket show heavy grain and faint horizontal stripes in the blue channel.

Address the following for all three:

1. **Photon budget and SNR reasoning.** For each file, reason qualitatively about how much light was collected (N) relative to what a clean result needs, and where in the tonal range SNR is adequate versus inadequate. For File A, was the high ISO the *cause* of the shadow noise, or a response to the light budget set by the fixed 1/250 s and f/2.8? Justify with the causal chain.

2. **Degradation classification.** For each file, list every distinct degradation present and classify each as random vs fixed, signal-dependent vs signal-independent, and photonic vs structured. Name the specific mechanism (shot noise, read noise, thermal/hot pixels, banding, quantization/posterization, JPEG blocking, chroma-from-demosaic).

3. **Diagnostic procedure in Photoshop.** Describe, concretely, how you would confirm each classification using only the Info/Histogram panels, channel inspection, zoom, and (for File B) a difference or dark-frame comparison with the second frame. State exactly what you expect to see for each.

4. **Root-cause and capture remedy.** For each file, state what should have been done differently at capture to raise SNR or avoid the structured degradation — being precise about which lever (light, aperture, shutter, ISO placement, file format, in-camera long-exposure NR) and why it acts on the physics. For File C specifically, explain why "protect the file from ISO noise" by underexposing at ISO 200 was a mistake, and what the photographer should have done instead, distinguishing this from a legitimate highlight-protection underexposure.

5. **The ISO-invariance question.** For File C, describe the experiment that would tell you whether shooting at ISO 1600 in-camera (three stops up) would have been cleaner than the ISO 200 underexpose-and-lift approach on *this* body, and state honestly why you cannot know the answer without that test.

6. **Predict the denoising difficulty.** For each file, and *before* any tool is chosen, predict which degradations a conventional random-noise denoiser will handle well, which it will fail on (and why), and which must be addressed by a non-denoise remedy (defect map/median, dark-frame subtraction, directional de-band, artifact removal, RAW demosaic controls). Rank the three files by how recoverable they are and defend the ranking from the physics.

This challenge requires you to synthesize the Poisson SNR law, the quadrature combination of shot and read noise, the thermal and structured degradations, the demosaic origin of chroma noise, the JPEG/quantization degradations, the full ISO causal chain, and the ISO-invariance concept into three coherent diagnoses — predicting outcomes and prescribing capture remedies without touching a single slider. Your answers set up the tool choices you will make in [Part 4](16D4_Camera_Raw_AI_Denoise_and_PS_Tools.md) and the quality-control discipline of [Part 8](16D8_Quality_Control_and_Cheat_Sheet.md).
