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
- `colorSuccess`, `colorWarning`, `colorError`
- `colorSecondary`, `colorTertiary`

## Typography

- `bodyFont` (string)
- `headingFont` (string)
- `headingSize` (number)
- `lineHeight` (number)

## Spacing and layout

- `density`: `compact | comfortable | spacious`
- `sectionGap` (number)
- `sectionRhythmMode`: `uniform | alternate | manual`
- `contentMaxWidth` (number)
- `borderRadius` (number)

## Navigation

- `navArchetype`: `sidebar | bottomBar | slides | scroll | minimal | embedded | none | custom`
- `navArchetypeBase`: same option set
- `navLayout`: `sidebar | bottomBar | none` (legacy)
- `navOutline` (boolean)
- `navOutlinePosition`: `left | right`
- `navPrevNext` (boolean)
- `navPrevNextPosition`: `bottom | top`
- `navProgressStyle`: `bar | dots | segments | none`
- `navFloatingToc`, `navScrollProgress`, `navEdgeArrows`, `navSlideCounter` (boolean)
- `navBreadcrumbs`, `navCourseTitle`, `navLessonTitles`, `navLessonTitleAsHeading` (boolean)
- `navReadingTime`, `navSwipeGestures`, `navKeyboardArrows` (boolean)
- `showA11yPill`, `showBrandingPill` (boolean)

## Visual effects

- `shadowIntensity`: `flat | subtle | elevated`
- `dividerStyle`: `none | thin | gradient | ornamental | wave | angle | curve | squiggle | blend`
- `backgroundTexture`: `none | grain | paper | noise`
- `highlightStyle`: `none | marker | gradient | scribble`

## Image effects

`imageEffects` supports keyed effect objects:

- `grayscale: { intensity }`
- `colorWash: { intensity, color }`
- `accentLighting: { intensity }`
- `progressiveBlur: { intensity }`
- `accentBlur: { intensity }`
- `motionBlur: { intensity, direction }`
- `grain: { intensity }`
- `halftone: { intensity }`
- `dithering: { intensity }`

Also available:

- `imageEffectsAuto` (boolean)
- Legacy: `imageStyle`, `imageHoverEffect`

## Animation

- `motionEntrance`: `none | fade | slide | scale`
- `motionStagger`: `none | sequential`

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

- `customCss` (string)
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
  bodyFont: Inter
  headingFont: Inter
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
  colorAccent: "#2563eb"
  colorButtonBackground: "#1d4ed8"
  colorButtonText: "#ffffff"
  colorSuccess: "#15803d"
  colorWarning: "#b45309"
  colorError: "#b91c1c"
  imageEffects:
    grain:
      intensity: 0.2
  imageEffectsAuto: true
  brand:
    logoUrl: https://example.com/logo-light.svg
    logoDarkUrl: https://example.com/logo-dark.svg
    logoPlacement: left
    logoScope: all-pages
  componentDefaults: {}
---
```
