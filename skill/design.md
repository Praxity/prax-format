# Design Options — .prax Sub-Skill

## Frontmatter structure

Design values live in YAML frontmatter under `design:`. All design keys are optional — omit any key to use the default.

```prax
---
title: Safety Basics
lang: en
design:
  palette: standard
  colorMode: light
  accentHue: 220
---
```

## Palette

The `palette` key sets the overall visual preset. Public names map to internal theme aliases at parse time.

| Public name | Description |
|---|---|
| `standard` | Clean, neutral default |
| `minimal` | Bauhaus-inspired, low decoration |
| `universal` | High-contrast, accessibility-first |
| `editorial` | Warm tones, editorial feel |
| `bold` | High contrast, strong typography |
| `cinematic` | Dark background, dramatic |
| `ocean` | Cool blues, structured |
| `warm` | Warm earth tones |
| `dark` | Dark-mode default |
| `nature` | Greens and naturals |
| `pastel` | Soft, low-saturation |
| `corporate` | Professional, muted blues |
| `playful` | Vivid, energetic |

### Palette and color parameters

| Parameter | Type | Default | Description |
|---|---|---|---|
| `palette` | string | `standard` | Preset palette name |
| `colorMode` | enum | `light` | `light`, `dark`, or `auto` |
| `accentHue` | number | 220 | Hue angle (0–360) for accent color |
| `accentHueCustomized` | boolean | false | Marks accent as manually overridden |
| `colorBackgroundLight` | string | — | Hex color for light-mode background |
| `colorBackgroundDark` | string | — | Hex color for dark-mode background |
| `colorText` | string | — | Hex color for body text |
| `colorAccent` | string | — | Hex color for accent/interactive elements |
| `colorButtonBackground` | string | — | Hex color for button backgrounds |
| `colorButtonText` | string | — | Hex color for button labels |
| `colorSuccess` | string | — | Hex color for success states |
| `colorError` | string | — | Hex color for error states |
| `colorWarning` | string | — | Hex color for warning states |
| `colorSecondary` | string or null | — | Optional secondary accent color |
| `colorTertiary` | string or null | — | Optional tertiary accent color |

Note: `colorBackground` is deprecated. Use `colorBackgroundLight` and `colorBackgroundDark` instead.

## Typography

| Parameter | Type | Default | Description |
|---|---|---|---|
| `bodyFont` | string | — | Font family name for body text |
| `headingFont` | string | — | Font family name for headings |
| `headingSize` | number | — | Heading size multiplier (e.g. 1.1) |
| `lineHeight` | number | — | Line height multiplier (e.g. 1.55) |

## Spacing and layout

| Parameter | Type | Valid values | Default | Description |
|---|---|---|---|---|
| `density` | enum | `compact`, `comfortable`, `spacious` | `comfortable` | Global spacing density |
| `sectionGap` | number | — | — | Gap between page sections (multiplier) |
| `contentMaxWidth` | number | — | — | Maximum content width in pixels |
| `borderRadius` | number | — | — | Border radius in pixels |
| `sectionRhythmMode` | enum | `uniform`, `alternate`, `manual` | `uniform` | Controls section spacing rhythm |

## Section dividers

| Parameter | Type | Valid values | Default | Description |
|---|---|---|---|---|
| `dividerStyle` | enum | `none`, `thin`, `gradient`, `ornamental`, `wave`, `angle`, `curve`, `squiggle`, `blend` | `none` | Style of divider between sections |
| `sectionDarkBackground` | string | — | — | Hex color for dark-background sections |
| `sectionAccentBackground` | string | — | — | Hex color for accent-background sections |
| `accentOnDark` | string | — | — | Hex accent color used on dark sections |

Note: `sectionTransition` is deprecated — use `dividerStyle` instead.

## Visual effects

| Parameter | Type | Valid values | Default | Description |
|---|---|---|---|---|
| `shadowIntensity` | enum | `flat`, `subtle`, `elevated` | `subtle` | Depth of shadows on elements |
| `backgroundTexture` | enum | `none`, `grain`, `paper`, `noise` | `none` | Texture applied to backgrounds |
| `highlightStyle` | enum | `none`, `marker`, `gradient`, `scribble` | `none` | Style for inline text highlights |

## Navigation

The navigation system has two layers: an archetype shortcut that configures a preset nav pattern, and individual toggles for fine control.

### navArchetype

`navArchetype` selects a pre-configured navigation layout. It supersedes the older `navLayout` field.

| Value | Description |
|---|---|
| `sidebar` | Persistent sidebar with lesson outline |
| `bottomBar` | Fixed bar at the bottom of the viewport |
| `slides` | Slide-deck navigation (prev/next, no outline) |
| `scroll` | Long-scroll with floating TOC and progress bar |
| `minimal` | Minimal chrome, content-first |
| `embedded` | Embeds without visible chrome |
| `none` | No navigation elements |
| `custom` | Fully manual — individual toggles control everything |

### Individual navigation parameters

| Parameter | Type | Valid values | Default | Description |
|---|---|---|---|---|
| `navArchetype` | enum | see table above | — | Navigation archetype shortcut |
| `navArchetypeBase` | enum | see table above | — | Base archetype before overrides |
| `navOutline` | boolean | true / false | — | Show lesson outline |
| `navOutlinePosition` | enum | `left`, `right` | — | Outline panel position |
| `navPrevNext` | boolean | true / false | — | Show previous/next navigation |
| `navPrevNextPosition` | enum | `bottom`, `top` | `bottom` | Position of prev/next buttons |
| `navProgressStyle` | enum | `bar`, `dots`, `segments`, `none` | — | Progress indicator style |
| `navFloatingToc` | boolean | true / false | — | Show floating table of contents |
| `navScrollProgress` | boolean | true / false | — | Show scroll-progress indicator |
| `navEdgeArrows` | boolean | true / false | — | Show edge arrow navigation |
| `navSlideCounter` | boolean | true / false | — | Show slide N-of-M counter |
| `navBreadcrumbs` | boolean | true / false | — | Show breadcrumb trail |
| `navCourseTitle` | boolean | true / false | — | Show course title in nav |
| `navLessonTitles` | boolean | true / false | — | Show lesson titles in nav |
| `navLessonTitleAsHeading` | boolean | true / false | — | Render lesson title as heading |
| `navReadingTime` | boolean | true / false | — | Show estimated reading time |
| `navSwipeGestures` | boolean | true / false | — | Enable swipe navigation on touch |
| `navKeyboardArrows` | boolean | true / false | — | Enable arrow-key navigation |

Legacy field (deprecated):

| Parameter | Type | Valid values | Description |
|---|---|---|---|
| `navLayout` | enum | `sidebar`, `bottomBar`, `none` | Superseded by `navArchetype` |

## Image effects

`imageEffects` is a composable map — add only the effects you want. Each key's presence enables that effect; omit to disable.

| Effect key | Parameters | Description |
|---|---|---|
| `grayscale` | `intensity: 0–1` | Desaturates the image |
| `colorWash` | `intensity: 0–1`, `color: primary/secondary/tertiary` | Applies a color tint |
| `accentLighting` | `intensity: 0–1` | Adds accent color lighting |
| `progressiveBlur` | `intensity: 0–1` | Blur that increases toward edges |
| `accentBlur` | `intensity: 0–1` | Blurs non-accent areas |
| `motionBlur` | `intensity: 0–1`, `direction: 0/45/90/135` | Directional motion blur |
| `grain` | `intensity: 0–1` | Adds film grain |
| `halftone` | `intensity: 0–1` | Applies halftone pattern |
| `dithering` | `intensity: 0–1` | Applies dithering effect |

Auto-calibration:

| Parameter | Type | Default | Description |
|---|---|---|---|
| `imageEffectsAuto` | boolean | false | Auto-calibrate effect intensities per image using perceptual metadata |

Note: `imageStyle` is deprecated — use `imageEffects` instead.

Image hover behavior:

| Parameter | Type | Valid values | Default | Description |
|---|---|---|---|---|
| `imageHoverEffect` | enum | `none`, `lift`, `tilt`, `glow` | `none` | Hover animation on images |

## Animation

| Parameter | Type | Valid values | Default | Description |
|---|---|---|---|---|
| `motionEntrance` | enum | `none`, `fade`, `slide`, `scale` | `none` | Entrance animation for blocks |
| `motionStagger` | enum | `none`, `sequential` | `none` | Stagger timing for entrance animations |

## Branding

Brand settings are nested under `design.brand`:

| Parameter | Type | Valid values | Default | Description |
|---|---|---|---|---|
| `logoUrl` | string | URL | — | Logo image URL (light mode) |
| `logoDarkUrl` | string | URL | — | Logo image URL (dark mode) |
| `logoPlacement` | enum | `left`, `center`, `hidden` | — | Logo position in nav bar |
| `logoScope` | enum | `all-pages`, `title-only` | — | Pages where logo appears |

## Accessibility and branding pill

| Parameter | Type | Default | Description |
|---|---|---|---|
| `showA11yPill` | boolean | — | Show accessibility badge in published output |
| `showBrandingPill` | boolean | — | Show "Made with Praxity" badge |

## Component defaults and custom CSS

| Parameter | Type | Description |
|---|---|---|
| `componentDefaults` | object | Block-level default overrides (key = block type, value = param object) |
| `customCss` | string | Raw CSS appended to the published output |

## Complete example

```prax
---
title: Safe Lab Operations
lang: en
design:
  palette: standard
  colorMode: light
  accentHue: 210
  bodyFont: Inter
  headingFont: Inter
  headingSize: 1.1
  lineHeight: 1.55
  density: comfortable
  sectionGap: 1.1
  contentMaxWidth: 860
  borderRadius: 12
  shadowIntensity: subtle
  dividerStyle: thin
  backgroundTexture: grain
  highlightStyle: marker
  motionEntrance: fade
  motionStagger: sequential
  navArchetype: sidebar
  navOutline: true
  navPrevNext: true
  navProgressStyle: segments
  navReadingTime: true
  navKeyboardArrows: true
  colorBackgroundLight: "#f8fafc"
  colorText: "#0f172a"
  colorAccent: "#1d4ed8"
  colorSuccess: "#15803d"
  colorWarning: "#b45309"
  colorError: "#b91c1c"
  imageEffects:
    grayscale:
      intensity: 0.3
    grain:
      intensity: 0.2
  imageEffectsAuto: true
  brand:
    logoUrl: https://example.com/logo-light.svg
    logoDarkUrl: https://example.com/logo-dark.svg
    logoPlacement: left
    logoScope: all-pages
  componentDefaults:
    note:
      style: filled
---
```
