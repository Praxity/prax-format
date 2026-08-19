# Frontmatter Design Options

## Course metadata

```yaml
title: Course Title
lang: en
```

## Palette and colors

```yaml
design:
  palette: standard
  colorMode: light
  accentHue: 220
```

Palette aliases are normalized internally. Public presets include:

- `standard`, `minimal`, `universal`, `editorial`, `bold`, `cinematic`
- `ocean`, `warm`, `dark`, `nature`, `pastel`, `corporate`, `playful`

Color tokens:

- `colorBackgroundLight`, `colorBackgroundDark`
- `colorText`, `colorAccent`
- `colorButtonBackground`, `colorButtonText`
- `colorSuccessOnLight`, `colorSuccessOnDark`
- `colorWarningOnLight`, `colorWarningOnDark`
- `colorErrorOnLight`, `colorErrorOnDark`
- `colorSuccess`, `colorWarning`, `colorError` (legacy fallbacks)
- `colorSecondary`, `colorTertiary`

Every color token accepts either a six-digit hex value or an opaque numeric OKLCH value:

```yaml
design:
  colorAccent: "oklch(57.7% 0.215 27.3)"
  colorBackgroundLight: "#ffffff"
```

OKLCH uses `oklch(lightness chroma hue)`: lightness is `0`–`1` or `0%`–`100%`,
chroma is `0`–`0.5`, and hue is a number in degrees (an optional `deg` suffix is
accepted). Alpha values are not accepted for design tokens. Invalid or out-of-range
values fall back to the active palette instead of producing invalid CSS.

Published output emits the gamut-mapped sRGB hex declaration first and the equivalent
OKLCH declaration second. Older LMS browsers therefore keep the hex colour; current
browsers use OKLCH. Hex remains fully backward compatible.

`accentHue` remains the preset-selection input. There are no separate
`accentLightness` or `accentChroma` keys: use `colorAccent: oklch(...)` when all three
axes must be authored, avoiding two competing sources for the accent colour.

### Semantic colour token ownership

Learner-facing component CSS consumes `--praxity-color-{role}` or
`--praxity-color-{role}-{state}` custom properties. Concrete colours belong in the
root/theme token layer; component rules must not introduce a new literal when a
semantic role exists. Hex fallbacks are allowed only at token boundaries or for
isolated rendering. Intrinsic colours whose identity is the content (for example a
colour-vision-safe chart series) stay with that asset or renderer.

## Typography

- `typography.fontDisplay`: `auto | block | swap | fallback | optional`
- `typography.body` (font role)
- `typography.headings` (font role plus optional `h1` through `h6` overrides)
- `fontFamily` (ordered array of up to four family names)
- `fontWeight`: `400 | 500 | 600 | 700 | 800`
- `fontStyle`: `normal | italic`
- `headingSize` (number)
- `lineHeight` (number)

```yaml
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
    h1:
      fontWeight: 700
    h2:
      fontFamily: [Merriweather, Inter, serif]
      fontWeight: 500
      fontStyle: italic
```

The `headings` role supplies defaults for all six levels. Each `h1` through `h6`
object can override any combination of family, weight, or style. Family names must
come from Praxity's existing font catalogue; `serif`, `sans-serif`, `monospace`, and
`system-ui` are also valid generic fallbacks. Praxity loads and packages every
catalogue family in the ordered list. `fontDisplay` is global because it controls
the packaged `@font-face` rules rather than an individual text element.
If a family does not ship the exact requested face, exports embed its nearest
available weight and style so the browser can synthesize the requested rendering.

Published headings use one body-relative scale. `headingSize` raises that scale but
cannot make a heading smaller than body text:

| Level | Published ratio |
|---|---|
| h1 | `max(1.5, headingSize)` |
| h2 | `max(1.3, headingSize × 0.75)` |
| h3 | `max(1.15, headingSize × 0.6)` |
| h4 | `max(1.1, headingSize × 0.5)` |
| h5 | `max(1.05, headingSize × 0.42)` |
| h6 | `max(1, headingSize × 0.36)` |

The ratios use `rem`, so they remain constant when the learner changes the viewer's
text size. The same scale applies inside columns, notes, accordions, tab panels, and
assessment cards. `lineHeight` controls leading within prose; it does not replace the
published adjacency scale.

## Spacing and layout

- `density`: `compact | comfortable | spacious`
- `blockSpacing`: `compact | default | spacious`
- `sectionGap` (number)
- `sectionRhythmMode`: `uniform | alternate | manual`
- `contentMaxWidth` (number)
- `borderRadius` (number)

Published content uses five body-relative rhythm tokens: `tight` (`0.825rem`), `close`
(`1.2375rem`), `normal` (`1.65rem`), `section` (`2.475rem`), and `boundary` (`3.3rem`).
Unknown block pairs use `normal`, so future block types have a stable fallback.

| Adjacency | Gap |
|---|---|
| page edge → first block/title; last block → page navigation | boundary |
| page title → first content | normal |
| heading → heading | tight |
| non-heading → h1 / h2 / h3 / h4–h6 | boundary / section / normal / close |
| h1 / h2 / h3–h6 → non-heading | normal / close / tight |
| text ↔ text or checklist | close |
| text ↔ note or quote | normal |
| text ↔ heavy block | section |
| consecutive note/quote or heavy blocks | section |
| divider in either direction | section |
| text → button / button → text / button → button | close / normal / tight |

Heavy blocks are tables, images, videos, embeds, columns, accordions, tabs,
assessments, and assessment groups. Heading adjacency wins when a heading introduces a
heavy block. `density` continues to control component-owned internal spacing and does
not shrink touch targets or reading rhythm. `blockSpacing` remains accepted for older
files but no longer flattens published content into one gap. `sectionGap` controls
alternate/manual section-band separation: values up to `3` are proportional
multipliers; pixel-era values such as `48` are normalized against the former 48px
default and then applied to the body-relative `section` token.

## Navigation

- `navArchetype`: `sidebar | bottomBar | slides | scroll | minimal | embedded | none | custom`
- `navArchetypeBase`: same option set
- `navLayout`: `sidebar | bottomBar | none` (legacy)
- `navOutline` (boolean)
- `navOutlinePosition`: `left | right`
- `navPrevNext` (boolean)
- `navPrevNextPosition`: `bottom | top`
- `navProgressStyle`: `bar | dots | segments | none`
- `navBottomArrows`, `navTopBar` (boolean)
- `navReadingSupportPlacement`: `topBar | bottomBar | sidebar | none`
- `navFloatingToc`, `navScrollProgress`, `navEdgeArrows`, `navSlideCounter` (boolean)
- `navBreadcrumbs`, `navCourseTitle`, `navLessonTitles`, `navLessonTitleAsHeading` (boolean)
- `navReadingTime`, `navSwipeGestures`, `navKeyboardArrows` (boolean)

The `slides` preset keeps previous/next controls beside its progress dots and leaves
`navEdgeArrows` off by default. Set `navEdgeArrows: true` to opt into the additional decorative
edge arrows; swipe and keyboard paging remain independently configurable.

> **Arrow keys cannot be the last one off.** If a page ends up with no visible way to
> change page — no outline, prev/next, edge arrows, hamburger or floating ToC — then
> `navKeyboardArrows: false` is ignored and arrow navigation stays on. Swipe is the only
> thing left otherwise, which no keyboard user and no mouse user can perform (2.1.1
> Keyboard; 2.5.7 Dragging Movements). Every other toggle is honoured as written, and the
> repair adds nothing visible.

- `showA11yPill` (boolean)
- `glossaryPage` (boolean, default `true`) — publish the aggregated glossary page built from `[term]{definition}` tooltips

Published Reading settings contain Larger text and a Reading guide. Larger text is a convenience
for environments where browser zoom does not persist; it returns to the course default from the
same control. The Reading guide dims top-level content blocks, keeps compound blocks such as
columns and captioned images atomic, and groups a heading with the content it introduces. It is a
reading aid, not an accessibility remediation or efficacy claim. Contrast follows the learner's
platform through `prefers-contrast` and `forced-colors`; there is no course-level contrast toggle.

## Visual effects

- `shadowIntensity`: `flat | subtle | elevated`
- `dividerStyle`: `none | thin | gradient | ornamental | wave | angle | curve | squiggle | blend`
- `backgroundTexture`: `none | grain | paper | noise`
- `highlightStyle`: `none | marker | gradient | scribble`

## Image effects

`imageEffects` supports keyed effect objects. A key's presence enables the effect.

| Effect key | Values | Default intensity |
|---|---|---|
| `grayscale` | `intensity: 0–100` percent | `100` |
| `colorWash` | `intensity: 0–100` percent; `color`: CSS hex | `40`; color `#4269d0` |
| `accentLighting` | `intensity: 0–100` percent | `30` |
| `progressiveBlur` | `intensity: 0–24` px | `8` |
| `accentBlur` | `intensity: 0–16` px | `6` |
| `motionBlur` | `intensity: 0–30` px; `direction`: `0`, `45`, `90`, or `135` degrees | `10`; direction `0` |
| `grain` | `intensity: 0–100` percent | `25` |
| `halftone` | `intensity: 2–16` px | `6` |
| `dithering` | `intensity: 0–100` percent | `50` |

When multiple effects are present, the runtime composites them in this order: grayscale, color
wash, progressive blur, accent blur, motion blur, grain, halftone, dithering, then accent
lighting. If more than one blur is active, each blur is reduced to 60% intensity, except the exact
progressive-blur plus motion-blur pair, which stacks without attenuation. Only the first texture
in compositing order is kept: grain before halftone before dithering.

Also available:

- `imageEffectsAuto` (boolean)
- Legacy: `imageStyle`, `imageHoverEffect`

## Animation

- `motionEntrance`: `none | fade | slide | scale`
- `motionStagger`: `none | sequential`
- `slideTransition`: `none | fade | slide | zoom` — default transition for single-layout Card
  blocks whose own `transition` parameter is unset. A block-level value takes precedence.

Both entrance motion and Card transitions are disabled when the learner requests reduced motion.

## Branding

```yaml
design:
  brand:
    logoUrl: https://example.com/logo-light.svg
    logoDarkUrl: https://example.com/logo-dark.svg
    logoPlacement: left
    logoScope: all-pages
```

- `logoPlacement`: `left | center | hidden`
- `logoScope`: `all-pages | title-only`

## Custom code and defaults

- `customCss` (string) — raw author CSS appended to published output. Praxity's contrast and
  reflow validation cannot vouch for styles introduced here; authors must validate that CSS.
- `componentDefaults` (object)

## Complete example

```yaml
---
title: Incident Response Essentials
lang: en
design:
  palette: universal
  colorMode: auto
  accentHue: 205
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
  lineHeight: 1.6
  density: comfortable
  sectionGap: 1.1
  contentMaxWidth: 880
  borderRadius: 10
  navArchetype: sidebar
  navOutline: true
  navPrevNext: true
  navProgressStyle: segments
  shadowIntensity: subtle
  dividerStyle: thin
  backgroundTexture: grain
  highlightStyle: marker
  motionEntrance: fade
  motionStagger: sequential
  colorBackgroundLight: "#f8fafc"
  colorBackgroundDark: "#0f172a"
  colorText: "#0f172a"
  colorAccent: "oklch(54.6% 0.215 262.9)"
  colorButtonBackground: "#1d4ed8"
  colorButtonText: "#ffffff"
  colorSuccess: "#15803d"
  colorWarning: "#b45309"
  colorError: "#b91c1c"
  imageEffects:
    grain:
      intensity: 20
  imageEffectsAuto: true
  brand:
    logoUrl: https://example.com/logo-light.svg
    logoDarkUrl: https://example.com/logo-dark.svg
    logoPlacement: left
    logoScope: all-pages
  componentDefaults: {}
---
```
