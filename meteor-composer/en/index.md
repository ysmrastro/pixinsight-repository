---
layout: single
title: "Meteor Composer"
---

[日本語](../) \| [GitHub Repository](https://github.com/ysmr3104/meteor-composer)

A PixInsight script that detects meteors across a night of registered frames, lets you screen the candidates by eye, and composites the accepted ones onto a master light. Written entirely in PJSR — no Python, no external processes.

Finding the few dozen frames that caught a meteor among several hundred is what the detector takes over. **The accept/reject decision always stays with you.** Detection stops at proposing candidates; nothing is discarded automatically.

## Example

![Meteor shower composite]({{ site.baseurl }}/assets/images/meteor-composer/composite.jpg)

The Perseids on 12 August 2026. 31 meteors accepted out of 654 frames shot with an ILCE-7M3 at 13-second exposures.

## Screenshots

### Screening window

![Screening window]({{ site.baseurl }}/assets/images/meteor-composer/screening.jpg)

The candidate list is on the left, a full-frame preview in the centre, and the selected candidate at 1:1 on the right. Each candidate carries the length, angle, elongation, track length and score measured during detection, and the list can be sorted by score or by capture order. The purple region at the lower left is the exclusion mask — here a band tilted 37 degrees off the left edge, keeping the landscape out of detection.

The two preview panes answer different questions, and neither of them asks you to hunt for the candidate.

**The right-hand pane is always centred on the selected candidate.** Around it sits about 2.5 times the candidate's own size of context, adjustable with the **+** and **-** buttons above the pane, and a thin rectangle marks the candidate itself so you can tell the detection from its surroundings. For a candidate near the edge of the frame the crop shifts rather than shrinks, so the magnification stays the same instead of jumping scale.

**The centre pane keeps its own zoom, and while zoomed in it scrolls to follow the selection** — but only when the candidate is not already on screen. Stepping through candidates that sit close together therefore does not shake the view. At Fit it never moves, since the whole frame is already visible.

Verdicts are single keystrokes — `M` (meteor), `N` (not a meteor), `U` (uncertain) — and **every verdict is saved as you make it.**

## Four stages

| Stage | What it does |
|---|---|
| 1. Detection | Scan every frame and build a list of meteor candidates |
| 2. Screening | Show the candidates with previews so you can accept or reject each one |
| 3. Mask generation | Build a trail mask for each accepted candidate from its light distribution |
| 4. Composition | Composite the meteors alone onto a master light |

## Features

- **Whole-night batch detection**: scans every frame and enumerates candidates, at roughly 0.8 seconds per frame
- **1:1 previews for screening**: a downscaled view cannot separate a meteor from a satellite, so the selected candidate is always shown at full resolution
- **Verdicts saved as you go**: written out on every keystroke, so nothing is lost by forgetting to save
- **Satellite and aircraft suppression**: objects that travel along the same path across several frames are matched up and can be hidden as a group
- **Sorting and cutoffs**: candidates are scored from length, elongation, trail colour and more. The default `Loose` hides nothing
- **Exclusion regions**: keep the landscape or a streetlight out of detection, either as per-edge depth and tilt, or as a painted image
- **Fragment merging**: when one trail is detected in pieces, collinear fragments are merged back into a single candidate
- **Trail masks from measured light**: the mask follows the actual spread of the trail's light, so the composite leaves no seam in the background
- **Interruptible**: detection can be stopped at any point and keeps what it has found

### Exclusion regions

Landscape and streetlights can be kept out of detection. The excluded region is drawn over the preview, and **the measured excluded area is always shown.**

Note that **the area outside the registered frame — what the alignment pushed off the edge — is excluded automatically**, so there is no need to paint over it. Painting a wide margin by hand costs you any meteor that fell inside it.

## Input data

The input is registered images, aligned and debayered by WBPP.

- **Tracked exposures on an equatorial mount**
- **Fixed-tripod exposures** that follow the NPF rule, with stars still recorded as points

Fixed exposures long enough to trail the stars are out of scope, since StarAlignment will not solve them. A fixed tripod also drifts across the sky over the night, which puts a focal-length-dependent ceiling on total shooting time.

## Measured results

Measured over 654 frames from 12 August 2026 (ILCE-7M3, 13-second exposures, 6024x4024).

| | |
|---|---|
| Candidates detected | 360 |
| Visually confirmed meteors recovered | **9 / 9** |
| Detection time | about 9 minutes (810 ms per frame) |
| Pixels changed outside the mask during composition | **0** |

## Requirements

- **PixInsight 1.9.4 or later (V8 engine only)**
- No external dependencies — no Python, no third-party processes

PixInsight 1.9.3 and earlier (SpiderMonkey engine) are not supported.

## Installation

### From the repository (recommended)

1. Open PixInsight
2. Open **Resources > Updates > Manage Repositories**
3. Add this repository URL:
   ```
   https://ysmrastro.github.io/pixinsight-scripts/
   ```
4. Run **Resources > Updates > Check for Updates**
5. Install MeteorComposer and restart PixInsight

### Manual installation

1. Clone or download the repository:
   ```bash
   git clone https://github.com/ysmr3104/meteor-composer.git
   ```
2. Copy every file in `javascript/` into a `MeteorComposer/` folder in your PixInsight scripts directory
3. Restart PixInsight

Once installed, launch it from **Script > Image Analysis > MeteorComposer** or **Script > ysmrastro > MeteorComposer**.

## Usage

1. Point **Frames** at the directory of registered frames. The first one appears in the preview
2. Set **Output**. Detection results, verdicts and the composite all go here — **nothing is ever written to the input directory**
3. Use **Mask** to exclude the landscape if you need to
4. Run **Run detection**
5. Judge the candidates one at a time (`M` meteor / `N` not a meteor / `U` uncertain)
6. Use **Compose...** to pick a master light and build the composite

You can stop part way through screening and pick up again with **Load session...**.

## Troubleshooting

- **Too many candidates**: tick `Hide satellites and aircraft` and raise `Cutoff` to `Standard` or `Strict`. Tightening the cutoff does raise the chance of missing a meteor, so it is worth going through `Loose` once first
- **A meteor never appears as a candidate**: check that the exclusion mask is not too wide. The excluded area is reported to the console when `Run detection` starts
- **The area around a composited meteor looks wrong**: trail masks are built from the light distribution, so a meteor that spanned an exposure boundary (recorded across two consecutive frames) can leave a seam
- **Detection is slow**: I/O dominates. Keep the registered frames on an SSD

## License

[MIT License](https://github.com/ysmr3104/meteor-composer/blob/main/LICENSE)

## Support

These scripts are free to use. If you would like to support their continued development, you can do so via [GitHub Sponsors](https://github.com/sponsors/ysmr3104).
