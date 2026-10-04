# Praxity Studio CLI

Praxity Studio includes a headless export command. It uses the same project loader,
accessibility checker and export code as the desktop Publish flow, so a course
exports the same way from the command line as from Studio. Use either the installed
Studio launcher or the standalone Node package.

## Command

```sh
praxity export <project-dir> \
  --format html|scorm-1.2|scorm-2004|pdf \
  --output <file-path> \
  [--paper-size a4|letter] \
  [--include-answer-sheet] \
  [--allow-critical-a11y-issues]
```

The command exports the complete course in the order defined by `course.yaml`. It
does not export single lessons.

`--format` and `--output` are required. The output is the exact filename, and its
parent directory must already exist. HTML and SCORM formats produce a ZIP, so the
filename must end in `.zip`. The `pdf` format produces a single file ending in
`.pdf`. The command replaces an existing file only after the new one has been
built, so a failed export leaves the old file in place.

## Export a PDF workbook

`--format pdf` produces a printable, tagged PDF/UA-1 study workbook. It follows the
course theme's colours and fonts, prints narration scripts after their slides, and
expands interactive blocks such as tabs, accordions and flip cards so every panel
is on the page. Questions print with space to answer. The workbook does not record
completion, attempts or scores.

- `--paper-size` sets `a4` (the default) or `letter`.
- `--include-answer-sheet` appends an answer sheet after the course content. It
  includes each question's authored answer and explanations. Leave it off for a
  learner handout.

These two options only apply to `--format pdf`. The PDF export runs entirely
offline, with the typesetting engine and fonts included in Studio.

A printed page can't react to a learner, so PDF export stops with an error when
the course uses logic rules (`if:`/`then:`), conditional visibility, variables or
answer piping, or branching action buttons. The error gives the lesson and block IDs.
Author a static version of that content to export a PDF. HTML and SCORM exports
are unaffected. Equations also need an authored text alternative before they print.

## Use the command from the Studio installation

Run the bundled launcher directly:

```sh
"/Applications/Praxity Studio.app/Contents/Resources/bin/praxity" --help
```

To make `praxity` available on your PATH, link that launcher rather than Electron's
binary. A raw link to the Electron binary cannot locate its helper applications.

```sh
mkdir -p "$HOME/.local/bin"
ln -s \
  "/Applications/Praxity Studio.app/Contents/Resources/bin/praxity" \
  "$HOME/.local/bin/praxity"
```

Add `$HOME/.local/bin` to `PATH` if it is not already present.

## Standalone package

An integrator can supply the standalone Studio package with Node 24.18.x. Keep
`praxity.mjs`, `export-assets/`, and `node_modules/` together. Studio does not need
to be installed. The command and JSON results are the same:

```sh
node /path/to/studio-cli/praxity.mjs export /path/to/course \
  --format html --output /path/to/course.zip
```

## Project requirements

The input is a complete project directory with:

- `course.yaml`, including the ordered lesson list;
- every listed `.prax` lesson;
- any local files referenced under `assets/`;
- optional `praxity.json` export settings.

## Course cast

Declare shared dialogue speakers in `course.yaml`:

```yaml
cast:
  roger:
    name: Roger
    avatar: assets/roger.png
    voice: v1
    side: end
```

Each key starts with a letter and then uses letters, digits, `_`, or `-` without
spaces. Keys are matched case-insensitively to dialogue turns. `name` is required;
`avatar`, `voice`, and `side` are optional. `side` is `start` or `end` and fixes the speaker's bubble to that logical side. Change `name` to rename the displayed speaker
without changing the key used by lessons. A dialogue's local `speaker:` declaration
overrides the matching cast fields. Set its `avatar: none` to hide the cast portrait;
`none` is case-insensitive.
Invalid sides warn and are ignored. Without a side, the first speaker in a dialogue uses start and other speakers use end.

Course and lesson settings take precedence over an immediate parent
`workspace.praxity.yaml`. Fixed defaults apply when neither defines a value.
`praxity.json` can also set a pass mark, `export.passingScore`, as a fraction such as
`0.8`. SCORM and xAPI exports then report passed or failed from the learner's score on
required questions marked `scored: true`, once every one has been answered.
`praxity.json` supplies the completion threshold when valid; otherwise the fixed
threshold is `0.8`. The command does not read Studio's global settings.

The CLI uses Studio's project identity loader. It can add a missing course or lesson
ID, create or update `.praxity/content-identity.json`, and migrate older generated
page/block UUID rows out of lesson source into that sidecar. Preserve the whole
project folder, including `.praxity`, when copying a course whose learner identity
must stay the same. Do not edit project files while export is running. Concurrent
project writes are unsupported.

## Machine-readable results

Each `export` run writes one JSON object to stdout. A successful export looks like
this:

```json
{
  "ok": true,
  "outputPath": "/absolute/path/course.zip",
  "format": "scorm-2004",
  "pageCount": 12,
  "accessibility": {
    "status": "passed"
  }
}
```

Failures use this shape:

```json
{
  "ok": false,
  "error": {
    "code": "project_invalid",
    "message": "Missing course manifest: course.yaml"
  }
}
```

A PDF export also returns `diagnostics`: a list of conversion notices naming
the lesson and, when available, the block, such as media that has no transcript to print. Review them
before you share the workbook.

If a content block is empty or has unusable data, PDF export skips that block and
returns a localized diagnostic with a code, block ID, and page title when available.
Other blocks still print. The CLI also prints these diagnostics to stderr and exits
with status 0 when the export otherwise succeeds. PDF export still fails when
printing would expose answers, misrepresent branching content, break internal links,
or leave the PDF incomplete because of a compiler or asset error.

A SCORM 1.2 export also returns `scorm12Storage` with `atRisk`, `estimatedLength`
and `limit`. SCORM 1.2 gives a course 4,096 characters to save learner progress.
The estimate counts every page ID and one short answer per question, plus room for
block state. When `atRisk` is `true`, the CLI prints a warning to stderr and still
exits with status 0. Learners in such a course may stop saving progress partway
through, so export SCORM 2004 when the LMS supports it.

Human-readable failures, PDF diagnostics, override warnings, and SCORM 1.2 storage
warnings go to stderr.
Export emits no progress messages and sends no telemetry. `--help` and `--version`
return plain text.

Stable error codes are:

- `invalid_arguments`
- `project_not_found`
- `project_invalid`
- `accessibility_blocked`
- `export_assets_missing`
- `output_invalid`
- `output_write_failed`
- `export_failed`

Exit status is `0` for success, `1` for project or export failure, and `2` for invalid
command usage.

## Accessibility override

Export blocks critical accessibility issues by default. During the early alpha,
`--allow-critical-a11y-issues` permits an export after the checker runs. The success
JSON then reports `"status": "overridden"`, the critical issue count, and every issue.
The flag applies only to that invocation and cannot be enabled through project files.

## Inspect a course

`praxity inspect <project-dir>` prints the parsed course as one JSON object and
does not modify the project. Unlike export, it adds no IDs and writes no
identity sidecar. Other tools can read this output instead of
parsing `.prax` themselves.

```json
{
  "ok": true,
  "schema": "praxity-inspect/0",
  "studioVersion": "0.2.0",
  "warnings": [],
  "course": { "id": "…", "title": "Safety", "locale": "en", "description": null },
  "lessons": [
    {
      "file": "start.prax",
      "id": "…",
      "title": "Before You Start",
      "sha256": "…",
      "frontmatter": { "title": "Before You Start" },
      "pages": [
        {
          "id": "…",
          "number": 1,
          "title": "Welcome",
          "data": {},
          "blocks": [
            { "id": "…", "type": "heading", "line": 5, "data": { "content": "Welcome", "level": 2 } }
          ]
        }
      ]
    }
  ]
}
```

Lessons follow `course.yaml` order. `line` is the 1-based line where a top-level
block starts in its lesson file, or `null` when it cannot be located. Nested blocks
appear inside their container's `data`. Narration from `narration.yaml` is merged
into the page and block `data` it belongs to. `sha256` hashes the lesson source.
Dialogue blocks have `type: "dialogue"` and resolved turns in `data.turns`, including `side` when set.
`warnings` lists dialogue speaker resolution notices as `{ "blockId", "message" }`.

Schema `praxity-inspect/0` is experimental. Block `data` mirrors the parsed grammar
([block reference](reference/blocks.json)) and may change between Studio versions.
A project that Studio has never saved has no durable page or block IDs, so those IDs
change on every run; line numbers do not. Errors and exit codes match export.

### Typed inspection, schema 1

Opt in with `praxity inspect <project-dir> --schema 1`. Omitting `--schema`, or
passing `--schema 0`, retains the schema 0 shape above. Unknown versions, missing
values, duplicate flags, and malformed values return `invalid_arguments` with
exit status 2. The schema identifier, not `studioVersion`, selects the contract.
Consumers should tolerate added fields and reject unsupported schema identifiers.

Schema 1 returns `ok`, `schema: "praxity-inspect/1"`, `studioVersion`, `revision`, `warnings`,
`projectionVersion: 2`, `course`, and `lessons`. The projection marker identifies the expanded
evidence described below; old schema 1 producers omit it. Consumers must explicitly map
these additions and bump their own interpretation version when they start using them.
Course metadata has the same shape as schema 0. Each
lesson contains `file`, `id`, `title`, `sha256`, `pages`, `narration`, and
`unlinkedNarrationCount` and `unlinkedNarration`. Each page contains `ref`, `id`, `number`, `title`, and
`blocks`. Pages use Studio's page splitter, which omits hidden pages.

A page's blocks form a flat preorder list, including nested blocks. `parentRef`
links a child to its containing block and is `null` at the top level. Each block
contains `ref`, `id`, `type`, `coverage`, `location`, and `texts`. Each text has
`ref`, `role`, `value`, `format`, and `location`, for example:

```json
{
  "ref": "lesson/start.prax/blocks/0/text/content",
  "role": "heading",
  "value": "<p>Welcome</p>",
  "format": "html",
  "location": { "file": "start.prax", "startLine": 5, "endLine": 6 }
}
```

Text formats are explicit. `html` preserves parsed markup, `markdown` preserves
inline grammar formatting, and `plain` needs no markup interpretation. These
values are inspection data, not trusted HTML for direct insertion into a page.
Choice prompt and overall feedback use HTML; option labels and option feedback
use Markdown. Feedback can occur both on an option and as the whole-question
fallback, matching Studio's choice adapter.

This version covers a bounded set of authored text:

| Block or content | Roles and coverage |
|---|---|
| Heading, body paragraph | `heading`, `body`; `coverage: "text"` |
| Choice, matching, ordering, categorization, fill-blank, free-response, matrix and hotspot assessments | `prompt`, description as `body`, `option`, `feedback`; `coverage: "text"` |
| Card, columns, accordion, tabs, sequence | Container/item labels as `heading`, descendants as separate blocks; `coverage: "container"` |
| Inline glossary in projected text | `glossary-term` as plain text and `glossary-definition` as decoded tooltip HTML |
| Media titles/captions, notes, quotes, tables, charts, stats, checklists, rating/signature labels, code, equations, buttons and assessment-group headings | `heading`, `body`, `prompt`, `option` as appropriate; `coverage: "text"` |
| Dialogue captions and turns | `body` as Markdown; `coverage: "text"` |
| Other blocks and unknown response types | `coverage: "unsupported"`; no claim that their prose was extracted |

Coverage describes the text projection. Layout and logic remain outside this contract.
Answer values embedded in fill-blank templates are replaced with `____`; dropdown
choices and word-bank entries are `option` text. Feedback, glossary definitions,
media alternatives and transcripts are separate channels, not visible prose. Supported descendants remain visible even inside an
unsupported parent. Glossary entries use Studio's collector, with the first
case-insensitive definition winning within each text field. Definitions and
terms remain associated with that field's source location. Schema 1 omits the
opaque parser `data`; consumers that need it can still request schema 0.

A schema 1 dialogue block also has `style` (`"bubbles"` or `"script"`),
`speakers`, and `turns`. `speakers` lists distinct speaker keys in first-turn order;
each entry has `key`, resolved `name`, boolean `avatar` and `voice` flags, and optional resolved `side`.
`turns` preserves all turns, including unattributed ones. Each has Markdown
`text` and a 1-based source `line` (or `null` when unavailable). Attributed
turns also have `speaker`; unattributed turns omit it. Turns with an explicit resolved side also have `side`. Paragraphs in a turn
are separated by a blank line in `text`. Unknown speaker notices appear in
the top-level `warnings` array as `{ "blockId", "message" }`.

`narration` lists the scripts resolved by Studio's narration helper, including
disabled candidates. Entries have `ref`, `role: "narration"`, `value`,
`format: "plain"`, `blockRef`, `pageRef`, `origin`, `disabled`, and `location`.
Studio's speech-text helper removes script emphasis and list markers while
preserving paragraph boundaries. `origin: "sidecar"` means the script came from
a linked sidecar override, whose location covers its authored `narration.yaml` entry. `origin: "derived"` uses
the owning block's location where available. Derived page-title narration has a block reference and source location when Studio
associates it with a matching source heading; otherwise both are `null`. An audio/settings-only sidecar entry does not
change script provenance. `unlinkedNarrationCount` reports entries that could
not be attached; their scripts are not silently added to the resolved list.
`unlinkedNarration` exposes their authored scripts and sidecar locations with
`blockRef: null` and `pageRef: null`. `unresolvedAnchor` gives the saved kind, type,
page number and label, not an asserted current link or raw internal AST. A missing
script is an empty value; Studio does not invent a script from an unresolved anchor.

#### Assessment evidence

An assessment block adds `assessment` with `responseType`, `correctness`,
`selectionMode`, `inputMethod`, and `scoring`. `correctness` is `keyed`, `open`, or
`unavailable`; it does not classify the pedagogical quality of the question.
Choice options include `correct`; matching pairs include `prompt` and `correctMatch`;
ordering items have zero-based `correctPosition`; categories include their assigned
items; hotspots include shape, percentage coordinates and correctness. In schema 1,
`hotspots[].coordinates` uses `[left, top, width, height]` for rectangles,
`[centreX, centreY, radiusX, radiusY]` for ellipses, and flattened `x, y`
pairs for polygons. Point spots keep `[x, y, 6]`. Schema 0 exposes authored
regions as `data.spots[].shape` and `data.spots[].region`.
Fill-blank
assessments include `blankStyle`, a shared `wordBank`, and `blanks` with `answers`,
`answerRules` and `choices`. Duplicate bank entries are consumable occurrences.
Each answer rule declares `match: "literal"`
or `"wildcard"`; a wildcard also retains its authored pattern after the leading `~`,
including escapes for literal `*` or `?`. Typed comparisons normalize Unicode, case
and whitespace; selectable choices compare literal values. These records use deterministic snapshot
refs. Answer values are evidence only and must not be added to visible prose.
Matrix statements/scale points and open response types do not imply correctness.

`scoring.gradingMode` is the configured mode after project course/lesson component
defaults. Workspace configuration is outside this source inspection contract. `scored` reports whether the runtime
can emit a score; an all-open fill-blank remains completion-only even with configured
grading. `points` is the authored finite value or null; the runtime score maximum
when omitted is 100. `required` and `attempts` include runtime defaults; zero attempts
means unlimited. `feedbackMode` is authored or null. Ungraded keyed activities can
still check correctness. Open responses never acquire an inferred answer key.

Assessment-group blocks add `assessmentGroup` with `memberRefs`,
`unresolvedMemberCount`, `mode`, `requireAll`, `passingScore`, `pointsOverride`, and
`aggregation: "mean-of-scored-member-percentages"`. `oneOf` uses the selected member;
`all` uses the participating members. Open/completion-only members are excluded from
percentages. A null passing score means every checked member must pass. The
`pointsOverride` field is authored metadata, not a claim that the runtime applies it.

#### Media and recorded narration

Media-bearing blocks add `media`. Each item has `kind`, `source`, plain `alt`,
`decorative`, formatted `description`, `caption`, `transcript`, and `captionTracks`.
Absent text/source fields are null. Caption tracks retain source, language, label,
kind and default status. Transcripts are authored plain text, not extracted speech.
Track contents and remote transcripts are not downloaded or converted to prose.
Captions may also appear in visible block text; consumers must not count them twice.

Asset references have `uri`, `availability` (`present`, `missing`, `remote`, or
`blocked`), and `sha256` for readable local bytes, otherwise null. Studio reads only
referenced project files, confines reads through symlink resolution, and never
fetches remote URLs. A leading slash is a project asset path. Invalid schemes,
traversal, out-of-project symlinks and unreadable files are blocked.

Resolved and unlinked narration add `audio`, `timings`, `audioProvenance`, and
`duration`. Audio provenance records saved script/language/voice/speed; it does not
prove that an audio file exists or matches the current script. `duration: null`
means unknown, never zero. A stored duration has `seconds`,
`provenance: "sidecar-recorded"`, `measurement: "unknown"`, and
`isFullFileDuration: false`. Studio's generated duration is a synthesis alignment
endpoint; manual attachment clears duration. Persisted sidecars do not establish
which process produced a particular number, so this contract does not call it a
measured full-file duration or use it to assert a measured speaking rate.

#### Source coordinates and identity

Block and text locations refer to the original UTF-8 lesson file whose exact bytes
are hashed by `sha256`. Sidecar narration locations refer to their `narration.yaml`
entries. `startLine` and `endLine` are 1-based, inclusive line numbers, with
frontmatter and legacy generated-ID rows included. Lines split at LF, including
CRLF files. There are no byte or JavaScript character offsets. Text locations
cover their owning block, not the exact substring. Containers cover their full
parser-recorded range. Nested body blocks retain their own ranges; choice
prompt/options/feedback share their assessment range. `null` means no authored
location is available. Non-ASCII text does not change line coordinates.

`ref` values are deterministic addresses within one inspection snapshot. Treat
them as opaque and correlate them only together with `revision`; inserting or
moving blocks may change them. They are not edit targets or durable learner IDs.
Studio preserves existing authored and matched sidecar identities in `id`, but
unsaved pages and stateful blocks may have transient IDs that differ between
processes. Inspection never persists identities or upgrades identity sidecars,
and leaves a missing course or lesson ID as `null`.

`revision` is a SHA-256 fingerprint of schema 1's inspection inputs: normalized
course manifest and returned course metadata, producer `studioVersion`, ordered
lesson paths and source hashes, narration sidecar source, and persisted identity
sidecar source, projection version and referenced local asset availability/content hashes. It changes for relevant course metadata or sidecar-only edits even when lesson
`sha256` values stay the same. Sidecar formatting changes may also change it.
Fresh transient IDs are excluded, so inspecting unchanged inputs with the same
Studio version in separate processes gives the same revision and refs. A Studio
upgrade changes the revision because parser and narration behavior may change.
Media-only replacements and appearance/disappearance of referenced files change the
revision. Unreferenced assets and remote contents are excluded, as is the published
artifact. Do not modify files during inspection; concurrent edits are unsupported.
