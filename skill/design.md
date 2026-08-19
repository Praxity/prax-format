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
| `colorBackgroundLight` | string | — | Six-digit hex or opaque OKLCH light-mode background |
| `colorBackgroundDark` | string | — | Six-digit hex or opaque OKLCH dark-mode background |
| `colorText` | string | — | Six-digit hex or opaque OKLCH body text |
| `colorAccent` | string | — | Six-digit hex or opaque OKLCH accent/interactive colour |
| `colorButtonBackground` | string | — | Six-digit hex or opaque OKLCH button background |
| `colorButtonText` | string | — | Six-digit hex or opaque OKLCH button label |
| `colorSuccessOnLight` / `colorSuccessOnDark` | string | — | Success state for each background mode; adjusted in OKLCH to at least 4.5:1 |
| `colorErrorOnLight` / `colorErrorOnDark` | string | — | Error state for each background mode; adjusted in OKLCH to at least 4.5:1 |
| `colorWarningOnLight` / `colorWarningOnDark` | string | — | Warning state for each background mode; adjusted in OKLCH to at least 4.5:1 |
| `colorSuccess` / `colorError` / `colorWarning` | string | — | Legacy fallback when a mode-specific value is absent |
| `colorSecondary` | string or null | — | Optional secondary accent color |
| `colorTertiary` | string or null | — | Optional tertiary accent color |

Note: `colorBackground` is deprecated. Use `colorBackgroundLight` and `colorBackgroundDark` instead.

OKLCH syntax is `oklch(lightness chroma hue)`. Lightness accepts `0`–`1` or
`0%`–`100%`; chroma accepts `0`–`0.5`; hue is degrees with an optional `deg` suffix.
Design colours are opaque, so alpha syntax is rejected. Malformed values fall back to
the active palette. Export writes an sRGB hex fallback before each equivalent OKLCH
declaration for older LMS engines. Use `colorAccent: oklch(...)` to author lightness,
chroma, and hue together; separate `accentLightness`/`accentChroma` keys do not exist.

## Typography

| Parameter | Type | Default | Description |
|---|---|---|---|
| `typography.fontDisplay` | enum | `swap` | `auto`, `block`, `swap`, `fallback`, or `optional`; applies to packaged font faces |
| `typography.body` | font role | preset | Body `fontFamily`, `fontWeight`, and `fontStyle` |
| `typography.headings` | font role | preset | Heading defaults plus optional `h1`–`h6` overrides |
| `fontFamily` | string array | preset | Ordered primary and fallback catalogue families; maximum four |
| `fontWeight` | enum | role-specific | `400`, `500`, `600`, `700`, or `800` |
| `fontStyle` | enum | `normal` | `normal` or `italic` |
| `headingSize` | number | — | Raises the body-relative heading scale; heading floors prevent inversion |
| `lineHeight` | number | — | Prose line-height multiplier (e.g. 1.55) |

`typography.headings` is inherited by every heading level. An `h1`–`h6` object may
override any role field. Valid generic family fallbacks are `serif`, `sans-serif`,
`monospace`, and `system-ui`; other values must name an existing Praxity font.

## Spacing and layout

| Parameter | Type | Valid values | Default | Description |
|---|---|---|---|---|
| `density` | enum | `compact`, `comfortable`, `spacious` | `comfortable` | Global spacing density |
| `blockSpacing` | enum | `compact`, `default`, `spacious` | `default` | Legacy value; published adjacency uses the reading rhythm |
| `sectionGap` | number | — | — | Multiplier for alternate/manual section separation; pixel-era values are normalized |
| `contentMaxWidth` | number | — | — | Maximum content width in pixels |
| `borderRadius` | number | — | — | Border radius in pixels |
| `sectionRhythmMode` | enum | `uniform`, `alternate`, `manual` | `uniform` | Controls section spacing rhythm |

Published type ratios are h1 `max(1.5, headingSize)`, h2
`max(1.3, headingSize × 0.75)`, h3 `max(1.15, headingSize × 0.6)`, h4
`max(1.1, headingSize × 0.5)`, h5 `max(1.05, headingSize × 0.42)`, and h6
`max(1, headingSize × 0.36)`. They remain constant at every learner text-size step and
also apply inside container blocks.

Published block gaps use `tight` (0.5 line), `close` (0.75 line), `normal` (1 line),
`section` (1.5 lines), and `boundary` (2 lines), expressed in `rem`. Headings bind to
the content below them; text pairs use `close`; notes, quotes, and heavy blocks receive
larger separation; unmatched pairs use `normal`. `density` affects component internals,
not this reading rhythm or minimum target sizes.

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

The `slides` preset keeps previous/next controls beside its progress dots and leaves
`navEdgeArrows` off by default. Set `navEdgeArrows: true` to opt into the additional decorative
edge arrows; swipe and keyboard paging remain independently configurable.

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
| `navBottomArrows` | boolean | true / false | — | Show arrows beside bottom progress dots |
| `navTopBar` | boolean | true / false | — | Show the header bar |
| `navReadingSupportPlacement` | enum | `topBar`, `bottomBar`, `sidebar`, `none` | — | Place or hide Reading tools |
| `navFloatingToc` | boolean | true / false | — | Show floating table of contents |
| `navScrollProgress` | boolean | true / false | — | Show scroll-progress indicator |
| `navEdgeArrows` | boolean | true / false | — | Show edge arrow navigation |
| `navSlideCounter` | boolean | true / false | — | Show slide N-of-M counter |
| `navBreadcrumbs` | boolean | true / false | — | Show breadcrumb trail |
| `navCourseTitle` | boolean | true / false | — | Show course title in nav |
| `navLessonTitles` | boolean | true / false | — | Show lesson titles in nav |
| `navLessonTitleAsHeading` | boolean | true / false | — | Legacy compatibility setting; course and lesson titles remain in navigation, while page H1s come only from authored content |
| `navReadingTime` | boolean | true / false | — | Show estimated reading time |
| `navSwipeGestures` | boolean | true / false | — | Enable swipe navigation on touch |
| `navKeyboardArrows` | boolean | true / false | — | Enable arrow-key navigation |

> **Arrow keys cannot be the last one off.** If a page ends up with no visible way to
> change page — no outline, prev/next, edge arrows, hamburger or floating ToC — then
> `navKeyboardArrows: false` is ignored and arrow navigation stays on. Swipe is the only
> thing left otherwise, which no keyboard user and no mouse user can perform (2.1.1
> Keyboard; 2.5.7 Dragging Movements). Every other toggle is honoured as written, and the
> repair adds nothing visible.


Legacy field (deprecated):

| Parameter | Type | Valid values | Description |
|---|---|---|---|
| `navLayout` | enum | `sidebar`, `bottomBar`, `none` | Superseded by `navArchetype` |

## Image effects

`imageEffects` is a composable map — add only the effects you want. Each key's presence enables that effect; omit to disable.

| Effect key | Parameters | Description |
|---|---|---|
| `grayscale` | `intensity: 0–100%` | Desaturates the image |
| `colorWash` | `intensity: 0–100%`, `color: CSS hex` | Applies a color tint; color defaults to `#4269d0` |
| `accentLighting` | `intensity: 0–100%` | Adds accent color lighting |
| `progressiveBlur` | `intensity: 0–24px` | Blur that increases toward edges |
| `accentBlur` | `intensity: 0–16px` | Blurs non-accent areas |
| `motionBlur` | `intensity: 0–30px`, `direction: 0/45/90/135` | Directional motion blur |
| `grain` | `intensity: 0–100%` | Adds film grain |
| `halftone` | `intensity: 2–16px` | Applies halftone pattern |
| `dithering` | `intensity: 0–100%` | Applies dithering effect |

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

## Reading settings and branding pill

| Parameter | Type | Default | Description |
|---|---|---|---|
| `showA11yPill` | boolean | — | Show Reading settings in published output |
| `glossaryPage` | boolean | true | Publish the glossary page aggregated from inline tooltips |

Published Reading settings contain Larger text and a Reading guide. Larger text is a convenience
for environments where browser zoom does not persist and can be reset from the same control. The
Reading guide dims top-level content blocks, keeps compound blocks such as columns and captioned
images atomic, and groups a heading with the content it introduces. It is a reading aid, not an
accessibility remediation or efficacy claim. Contrast follows the learner's platform through
`prefers-contrast` and `forced-colors`; there is no course-level contrast toggle.

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
  typography:
    fontDisplay: swap
    body:
      fontFamily: [Inter, "DM Sans", sans-serif]
      fontWeight: 400
      fontStyle: normal
    headings:
      fontFamily: [Inter, "DM Sans", sans-serif]
      fontWeight: 600
      fontStyle: normal
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
  colorAccent: "oklch(48.8% 0.217 264.4)"
  colorSuccess: "#15803d"
  colorWarning: "#b45309"
  colorError: "#b91c1c"
  imageEffects:
    grayscale:
      intensity: 30
    grain:
      intensity: 20
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
