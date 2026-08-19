# Block Index

This index is generated from `packages/grammar/src/manifest.ts` by `docs/prax-format/scripts/generate-block-index.mjs`.

| Block | Category | Syntax | Reference |
|---|---|---|---|
| text | Content | `paragraph text` | [blocks-content.md#text](blocks-content.md#text) |
| heading | Content | `## Heading` | [blocks-content.md#heading](blocks-content.md#heading) |
| image | Content | `/assets/image.png` | [blocks-content.md#image](blocks-content.md#image) |
| video | Content | `/assets/video.mp4` | [blocks-content.md#video](blocks-content.md#video) |
| audio | Content | `/assets/audio.mp3` | [blocks-content.md#audio](blocks-content.md#audio) |
| divider | Content | `--` | [blocks-content.md#divider](blocks-content.md#divider) |
| embed | Content | `as: embed` | [blocks-content.md#embed](blocks-content.md#embed) |
| bookmark | Content | `as: bookmark` | [blocks-content.md#bookmark](blocks-content.md#bookmark) |
| note | Content | `as: note` | [blocks-content.md#note](blocks-content.md#note) |
| quote | Content | `> quote` | [blocks-content.md#quote](blocks-content.md#quote) |
| button | Content | `[label](url) + as: button` | [blocks-content.md#button](blocks-content.md#button) |
| data-table | Content | `\| table \|` | [blocks-content.md#data-table](blocks-content.md#data-table) |
| accordion | Container | `as: accordion` | [blocks-containers.md#accordion](blocks-containers.md#accordion) |
| tabs | Container | `as: tab` | [blocks-containers.md#tabs](blocks-containers.md#tabs) |
| columns | Container | `as: col` | [blocks-containers.md#columns](blocks-containers.md#columns) |
| card | Container | `as: card` | [blocks-containers.md#card](blocks-containers.md#card) |
| comparison | Container | `as: comparison` | [blocks-containers.md#comparison](blocks-containers.md#comparison) |
| choose-one | Assessment | `as: choice + (x)/( )` | [blocks-assessments.md#choose-one](blocks-assessments.md#choose-one) |
| choose-many | Assessment | `as: choice + [x]/[ ]` | [blocks-assessments.md#choose-many](blocks-assessments.md#choose-many) |
| match | Assessment | `as: match` | [blocks-assessments.md#match](blocks-assessments.md#match) |
| order | Assessment | `as: order` | [blocks-assessments.md#order](blocks-assessments.md#order) |
| free-response | Assessment | `as: free-response` | [blocks-assessments.md#free-response](blocks-assessments.md#free-response) |
| hotspot | Assessment | `as: hotspot` | [blocks-assessments.md#hotspot](blocks-assessments.md#hotspot) |
| fill-blank | Assessment | `as: fill-blank` | [blocks-assessments.md#fill-blank](blocks-assessments.md#fill-blank) |
| categorize | Assessment | `as: categorize` | [blocks-assessments.md#categorize](blocks-assessments.md#categorize) |
| matrix | Assessment | `as: matrix` | [blocks-assessments.md#matrix](blocks-assessments.md#matrix) |
| assessment-group | Assessment | `as: assessment-group` | [blocks-assessments.md#assessment-group](blocks-assessments.md#assessment-group) |
| checklist | Interactive | `as: checklist` | [blocks-interactive.md#checklist](blocks-interactive.md#checklist) |

For matrix syntax, use a heading prompt, numbered scale points, and bullet-list statements.

## Parameters that are not applied yet

These parse and validate, so a course using them stays valid, but nothing reads
them and they make no difference to exported output. They are listed so the
spec does not promise behaviour the exporter does not deliver.

- `competency`
- `confidence`
- `retrieval`
- `timed`

See `blocks.json` for the full per-block parameter list, including which
parameters each block accepts.
