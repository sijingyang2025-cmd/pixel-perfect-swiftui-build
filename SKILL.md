---
name: pixel-perfect-swiftui-build
description: Use when implementing iOS SwiftUI screens from PNG/JPG/WebP UI screenshots, image mockups, or full image-only design sets with high visual fidelity. Guides image-only source normalization, visual measurement, color sampling, OCR/copy preservation, asset-vs-code decisions, SwiftUI component implementation, screenshot overlay QA, iterative correction, and simulator/device validation.
---

# Pixel-Perfect SwiftUI Build

## Overview

Use this skill to turn image-only UI mockups into a consistent, high-fidelity SwiftUI frontend. The workflow optimizes for visual accuracy first, then component reuse, then business wiring.

Do not start by writing screens. First understand the whole design system, decide what must be an image asset, and define the smallest project-specific SwiftUI design system that can reproduce the screens consistently.

## Core Rule

### Image-Only Source Mode

When the input is only PNG/JPG/WebP screenshots or pasted UI images, the image is the only source of truth. Do not assume hidden design tokens, layers, auto layout rules, font names, export slices, or component metadata. All dimensions, colors, spacing, typography, radii, shadows, and asset boundaries must be inferred from visual observation, image measurement, local sampling, OCR, and screenshot iteration.

Mark uncertain values as inferred. Do not present guessed tokens as if they were measured or provided by a design file.

### Asset-vs-Code Rule

If the design contains polished raster visuals such as full-page backgrounds, 3D icons, product illustrations, decorative constellations, glass objects, painterly mountains, or heavily rendered hero art, implement those as image assets. Do not approximate them with SF Symbols, gradients, Canvas drawings, or generic SwiftUI shapes unless the design itself is code-native.

SwiftUI should own layout, text, interaction, card structure, navigation, state, accessibility, and responsive behavior. Image assets should own non-trivial illustration and icon rendering.

Do not use a full-screen screenshot as the final implementation. Full-image placement with invisible hotspots is allowed only as a temporary prototype or alignment aid. Final work should be SwiftUI structure plus extracted or recreated assets.

## Workflow

### 1. Inventory the Full Design Set

Inspect all available screens before implementing any one screen.

Create a short design inventory:

- Screen list: home, list, detail, form, settings, modal, empty state, error state, loading state.
- Shared surfaces: backgrounds, tab bars, navigation bars, cards, chips, buttons, rows, inputs.
- Repeated visual motifs: icons, ornaments, dividers, badges, selected states, decorative art.
- Page families: content feed, dashboard, form, chat, profile, chart/detail, picker.
- Missing materials: fonts, cut images, icon exports, 3D assets, color tokens, motion specs.

If only one screen is provided, still identify likely reusable patterns and call out what cannot be inferred across the product.

### 2. Normalize the Reference Image

Before visual analysis or code, record a reference normalization note for every supplied image:

- Source file path and image dimensions in pixels, e.g. `1290x2796`.
- Likely device and scale, e.g. `430pt wide @3x`, `393pt wide @3x`, or unknown.
- Point conversion used for implementation, e.g. `px / 3 = pt`.
- Whether status bar, Dynamic Island/notch, home indicator, tab bar, and safe areas are included in the image.
- Whether the image is a full screenshot, cropped screen, design canvas, exported frame, or composite mockup.
- Any aspect-ratio mismatch with the target simulator/device.

If device mapping is uncertain, say so and choose the nearest iPhone viewport for the first pass. Do not silently mix pixel and point measurements.

### 3. Measure Before Code

Create a compact visual measurement table before implementation. Include measured or inferred values for:

| Item | Value | Confidence |
| --- | --- | --- |
| Reference width/height | px and derived pt | measured/inferred |
| Page horizontal margin | pt | measured/inferred |
| Top content start | pt from safe area or image top | measured/inferred |
| Major card width/height | pt | measured/inferred |
| Card radius | pt | inferred unless measured |
| Main vertical gaps | pt | inferred |
| H1/title size and weight | pt/weight | inferred |
| Body/caption size and line height | pt | inferred |
| Button/chip/input height | pt | inferred |
| Icon/illustration size | pt | measured/inferred |
| Tab bar height and bottom offset | pt | measured/inferred |

Use this table as the first SwiftUI sizing pass. If later screenshots drift, update the table instead of randomly adjusting constants.

### 4. Sample Colors Before Code

Do not describe colors only by feel. Extract or estimate approximate HEX values:

- Page background top/middle/bottom.
- Main card fill.
- Card border/stroke.
- Primary accent.
- Secondary accent.
- Main text.
- Secondary text.
- Disabled/placeholder text.
- Shadow/glow colors.

For gradients, record start/end colors and direction. For compressed images or painterly backgrounds, sample the visual center of representative regions and average nearby pixels. Avoid default iOS system blue unless the reference visibly uses it.

### 5. Extract Copy With OCR

Before implementing the screen, extract visible text and preserve it exactly:

- Page titles and subtitles.
- Card titles and captions.
- Buttons, chips, tabs, placeholders.
- Dates, numbers, amounts, counters, labels.
- Empty/error/loading state text.

If OCR is uncertain, list the uncertain strings for user confirmation. Do not rewrite, shorten, translate, or invent copy while doing visual reconstruction.

### 6. Produce Visual Analysis Before Code

Before editing files, write a compact visual breakdown:

- Overall mood and audience fit.
- Color system: background, primary/accent, text, border, shadow, disabled.
- Typography: brand/title/body/caption/numeric labels, weight, tracking, line height.
- Layout: safe area behavior, horizontal margins, vertical rhythm, card spacing, alignment.
- Components: reusable components versus page-only compositions.
- Asset strategy: which visuals must be exported/generated as images.
- Implementation risks: hard-to-reproduce effects, missing fonts, oversized assets, dynamic type issues.
- Fidelity priority: must be 1:1, acceptable approximation, intentionally deferred.

Do not invent new design language at this stage.

### 7. Establish a Project-Specific Design System

Create a small SwiftUI design system before building multiple screens. Keep it domain-specific, not a generic component library.

Recommended structure:

- `Colors`: named semantic colors, no default system blue unless present in the design.
- `Typography`: exact font sizes, weights, line spacing, tracking, numeric styles.
- `Spacing`: screen margins, card gaps, section gaps, control heights, radii, hairlines.
- `CardStyle`: reusable card fill, border, shadow, material behavior.
- `ButtonStyle`: primary, secondary, icon, chip, selected states.
- `ImageAssetView`: predictable sizing for rendered icons and illustrations.
- `PageBackground`: image-backed when the mockup uses illustration.
- `SectionTitle`, `InputRow`, `ListCard`, `EmptyState`, `TabItem`.

Add previews or preview screens showing component states before assembling full screens.

### 8. Decide Asset vs Code

Classify visual material into three groups:

| Group | Examples | Implementation |
| --- | --- | --- |
| Direct cut assets | backgrounds, logos, complex illustrations, complex rendered icons | crop/export from source when quality is sufficient |
| Recreated assets | style-specific 3D icons, decorative objects, painterly motifs | generate or redraw as independent raster assets |
| SwiftUI/SF Symbols | simple buttons, rows, cards, basic glyphs, native controls | implement in code |

Then use this decision table:

| Element | Prefer Image Asset | Prefer SwiftUI |
| --- | --- | --- |
| 3D icons, glass icons, painterly objects | yes | no |
| Full-page illustrated backgrounds | yes | no |
| Complex decorative lines/constellations in the art | yes | no |
| Simple cards, rows, pills, buttons | no | yes |
| Text, data, labels, timestamps | no | yes |
| Repeated layout grids | no | yes |
| SF Symbols shown in the design | only if explicitly matching | yes |
| Product/logo art | yes | no |

Avoid mixed-style icons. If one section uses rendered 3D icons, use image assets for all icons in that section.

### 9. Build a Static Reference Screen First

Pick the screen with the richest shared patterns as the benchmark. Implement it statically before connecting real data.

Rules:

- Match the design copy exactly.
- Match visual hierarchy and spacing before interaction.
- Use the new design system instead of one-off styles.
- Use stable dimensions for cards, icon containers, tab bars, grids, and chips.
- Do not introduce new features, explanatory text, or unrelated states.
- Do not use placeholder SF Symbols where the mockup has custom art.

Only after the benchmark screen is visually close should you generalize components and build the remaining screens.

### 10. Implement Cross-Screen Consistency

For each new screen:

- Reuse existing tokens and components first.
- Add a token only when a design pattern repeats or the UI explicitly requires it.
- If a screen seems to need a new style, compare against the full design set before adding it.
- Keep page-specific compositions local, but shared controls in `Views/Components` or the project’s equivalent.
- Preserve navigation, tab, and background behavior across pages.

If a design set has several screens with the same card or icon family, implement the family once and pass content/assets as parameters.

### 11. Generate and Prepare Image Assets

For generated or manually supplied raster assets:

- Save final project assets inside the app workspace, not only in a temp/generated folder.
- For transparent icons, generate on a flat chroma-key background, remove the key locally, then crop transparent padding.
- Keep source names semantic: `HomeBackground`, `QuestionWorkIcon`, `CompatibilityHeartIcon`.
- Use asset catalogs (`.xcassets`) for iOS projects.
- Validate icon scale in screenshots; generated images often contain too much transparent padding.
- Prefer replacing the asset under the same name when the layout is correct but the art is wrong.

Common transparent-icon sequence:

1. Generate the subject centered on a flat key color.
2. Remove the key to alpha.
3. Crop transparent whitespace with a small padding margin.
4. Import into `.xcassets`.
5. Render via `Image("AssetName").resizable().scaledToFit()`.

If a cropped asset has white matte, compression edges, or leftover background, clean alpha locally. If cleanup damages the asset, recreate it as an independent image rather than forcing code to hide the problem.

### 12. Screenshot Overlay QA Loop

Every implementation cycle must include a rendered screenshot. A screenshot is not enough by itself; compare it against the reference at a normalized size.

Recommended loop:

1. Build the app.
2. Launch on simulator.
3. Wait long enough to avoid splash/blank startup frames.
4. Capture a screenshot.
5. Resize the simulator screenshot and the reference to the same logical width.
6. Compare side-by-side and, when possible, with a semi-transparent overlay.
7. List only the largest 3 visual differences.
8. Fix those differences.
9. Repeat.

Do not change typography, spacing, colors, and assets all in the same correction pass unless the issue requires it. Keep each pass attributable: say whether the pass changed layout, typography, color, asset crop, or state.

Compare:

- Top/bottom safe area placement.
- Horizontal margins.
- Vertical rhythm.
- Font size and weight.
- Text truncation and line breaks.
- Card radius, border, shadow, opacity.
- Icon size and style consistency.
- Background position and brightness.
- Tab bar overlap and keyboard behavior.
- Whether visible elements are image-backed or accidentally duplicated by code.

Do not claim visual completion without a fresh screenshot.

### 13. Device Validation

After simulator QA, run on a real device when available.

Check:

- Device status is available/paired.
- App builds for iPhoneOS with the intended bundle id and signing team.
- Local network/API settings are valid for a physical phone if the screen uses backend data.
- Text is readable at physical size.
- Touch targets feel usable.
- Bottom navigation does not float incorrectly with keyboard.
- Scroll position reveals content that the simulator crop might hide.

If wireless install is unreliable, use a cable, keep the phone unlocked, and trust the Mac when prompted.

## Correction Rules

When the user reports a visual mismatch:

- Treat their reference as the source of truth.
- Identify whether the mismatch is asset, layout, typography, or state.
- Fix the smallest layer that can solve it.
- If the layout is correct but the art is wrong, replace only the asset.
- If the art is correct but cropped/scaled wrong, adjust frame, padding, or transparent crop.
- If a code-rendered decoration duplicates a background image, remove the code decoration.
- If a semantic icon is wrong, regenerate/replace the asset with the correct subject without changing unrelated layout.

Use confidence labels when discussing measurements:

- `measured`: obtained from image dimensions, pixel sampling, screenshot coordinates, or deterministic tooling.
- `inferred`: estimated from visual inspection.
- `user-confirmed`: explicitly confirmed by the user.

Do not let inferred values harden into permanent tokens without screenshot validation or user confirmation.

## Pitfalls to Avoid

- Starting with a generic component library before understanding the design set.
- Assuming Figma layers, design tokens, font names, or export slices when the input is only images.
- Building business logic before the static benchmark screen is visually close.
- Mixing SF Symbols with rendered 3D icons in the same visual family.
- Long-term use of full-screen screenshot backgrounds with invisible hotspots.
- Drawing complex decorative backgrounds in SwiftUI when the design is image-like.
- Leaving both image background art and old code-drawn decoration active.
- Ignoring transparent padding in generated assets.
- Treating pixel measurements as SwiftUI points without scale normalization.
- Rewriting OCR-visible text during visual reconstruction.
- Fixing too many mismatch categories in one pass, making regression causes unclear.
- Letting text truncate inside chips because icons consume too much width.
- Treating the first simulator screenshot as final when it captured a startup blank frame.
- Optimizing only one page while breaking cross-screen design consistency.
- Recreating the whole app when the task is targeted visual correction.

## Completion Checklist

Before reporting completion:

- Full design set or available screen set has been inventoried.
- Reference images were normalized to device scale and SwiftUI points.
- A measurement table was produced or intentionally deferred with a reason.
- Colors were sampled or approximated with stated confidence.
- OCR-visible copy was preserved exactly or uncertainties were listed.
- Design system tokens/components exist or were intentionally not needed.
- Custom art is implemented as image assets.
- Asset crops were checked for white matte, transparent padding, and scale.
- Static benchmark screen has been screenshot-tested.
- Largest visible differences have been iterated with normalized side-by-side or overlay comparison.
- New screens reuse shared components.
- Simulator build passes.
- Real-device build/install is attempted when available.
- Remaining mismatches and asset limitations are stated plainly.
