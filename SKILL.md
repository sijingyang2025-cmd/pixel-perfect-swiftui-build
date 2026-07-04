---
name: pixel-perfect-swiftui-build
description: Use when implementing iOS SwiftUI screens from Figma files, image mockups, or full UI design sets with high visual fidelity. Guides design analysis, cross-screen design-system extraction, asset-vs-code decisions, SwiftUI component implementation, screenshot-based visual QA, iterative correction, and simulator/device validation.
---

# Pixel-Perfect SwiftUI Build

## Overview

Use this skill to turn a UI design set into a consistent, high-fidelity SwiftUI frontend. The workflow optimizes for visual accuracy first, then component reuse, then business wiring.

Do not start by writing screens. First understand the whole design system, decide what must be an image asset, and define the smallest project-specific SwiftUI design system that can reproduce the screens consistently.

## Core Rule

If the design contains polished raster visuals such as full-page backgrounds, 3D icons, product illustrations, decorative constellations, glass objects, painterly mountains, or heavily rendered hero art, implement those as image assets. Do not approximate them with SF Symbols, gradients, Canvas drawings, or generic SwiftUI shapes unless the design itself is code-native.

SwiftUI should own layout, text, interaction, card structure, navigation, state, accessibility, and responsive behavior. Image assets should own non-trivial illustration and icon rendering.

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

### 2. Produce Visual Analysis Before Code

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

### 3. Establish a Project-Specific Design System

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

### 4. Decide Asset vs Code

Use this decision table:

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

### 5. Build a Static Reference Screen First

Pick the screen with the richest shared patterns as the benchmark. Implement it statically before connecting real data.

Rules:

- Match the design copy exactly.
- Match visual hierarchy and spacing before interaction.
- Use the new design system instead of one-off styles.
- Use stable dimensions for cards, icon containers, tab bars, grids, and chips.
- Do not introduce new features, explanatory text, or unrelated states.
- Do not use placeholder SF Symbols where the mockup has custom art.

Only after the benchmark screen is visually close should you generalize components and build the remaining screens.

### 6. Implement Cross-Screen Consistency

For each new screen:

- Reuse existing tokens and components first.
- Add a token only when a design pattern repeats or the UI explicitly requires it.
- If a screen seems to need a new style, compare against the full design set before adding it.
- Keep page-specific compositions local, but shared controls in `Views/Components` or the project’s equivalent.
- Preserve navigation, tab, and background behavior across pages.

If a design set has several screens with the same card or icon family, implement the family once and pass content/assets as parameters.

### 7. Generate and Prepare Image Assets

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

### 8. Screenshot-Based QA Loop

Every implementation cycle must include a rendered screenshot.

Recommended loop:

1. Build the app.
2. Launch on simulator.
3. Wait long enough to avoid splash/blank startup frames.
4. Capture a screenshot.
5. Compare against the design.
6. List only the largest 3-5 visual differences.
7. Fix those differences.
8. Repeat.

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

### 9. Device Validation

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

## Pitfalls to Avoid

- Starting with a generic component library before understanding the design set.
- Building business logic before the static benchmark screen is visually close.
- Mixing SF Symbols with rendered 3D icons in the same visual family.
- Drawing complex decorative backgrounds in SwiftUI when the design is image-like.
- Leaving both image background art and old code-drawn decoration active.
- Ignoring transparent padding in generated assets.
- Letting text truncate inside chips because icons consume too much width.
- Treating the first simulator screenshot as final when it captured a startup blank frame.
- Optimizing only one page while breaking cross-screen design consistency.
- Recreating the whole app when the task is targeted visual correction.

## Completion Checklist

Before reporting completion:

- Full design set or available screen set has been inventoried.
- Design system tokens/components exist or were intentionally not needed.
- Custom art is implemented as image assets.
- Static benchmark screen has been screenshot-tested.
- Largest visible differences have been iterated.
- New screens reuse shared components.
- Simulator build passes.
- Real-device build/install is attempted when available.
- Remaining mismatches and asset limitations are stated plainly.
