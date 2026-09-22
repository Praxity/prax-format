# Module deck settings

Studio's Design panel enables module playback with `moduleDeck: true` in `course.yaml`. Missing or false keeps the existing page runtime. The value must be a boolean.

```yaml
title: Example course
moduleDeck: true
lessons:
  - introduction.prax
```

The Course settings panel sets the current slide's narration stop and each assessment's release rule. These choices live with their content in `.prax` source:

```prax
---
title: Introduction
firstPage:
  deckStop: true
---

### Try it
as: choice
deckGate: attempt

(x) First answer
( ) Second answer

--- Reflection
deckStop: true

Read and reflect.
```

`deckStop` is an optional boolean on a page break or `firstPage`. True pauses after the slide's final narration segment. `deckGate` is an optional assessment or assessment-group parameter, `attempt` or `pass`. An explicit rule also gates an assessment marked `required: false`. With no rule, required checks with an automatically checked answer use pass, required surveys or manually reviewed responses use attempt, and optional ungraded checks add no gate. A pass rule needs an automatically checked answer; private responses cannot release a gate.

Studio saves these settings through its normal save flow and resolves them to durable page/block IDs after loading the content identity sidecar. The shared input builders apply the same settings to full preview and export. Source choices survive switching module playback off. Do not put compiler gate/stop ID maps in `course.yaml`, or put `moduleDeck` in lesson frontmatter.

For source compiler callers, set:

```ts
moduleDeck: {
  gates: { "practice-question-id": "attempt", "certification-question-id": "pass" },
  stops: ["reflection-slide-id"]
}
```

Desktop compiler callers can supply the same object in `DesktopExportInput.config.moduleDeck`. Both paths use the canonical artifact for HTML, LMS packaging and Electron preview deployment. Studio input builders derive this option from the persisted settings above.

`gates` maps stable assessment or assessment-group block IDs to release rules. An attempt rule accepts a submitted answer; a pass rule requires a correct answer or a passing group result. Listening duration never releases a gate. A blocked advance shows a persistent notice beside the first unfinished activity: in the left gutter when space allows, or above the activity on narrow layouts. Activating the notice moves focus to the activity. It clears when the required activities are satisfied or the learner navigates away. If the blocked activity is on another slide, the controls retain a general notice instead. Routine playback pauses do not add a visible status row. Authored attempt limits still apply, and an exhausted incorrect attempt does not release a pass gate. Earned access remains available for the learner attempt; course reset clears it. Course completion and scoring keep their separate existing configuration.

`stops` lists stable slide IDs where narration pauses after the final segment. Questions also pause after their prompt. Audio begins only after explicit Play. It continues through playable segments until a stop, a missing segment or module boundary. Manual permitted navigation preserves playback intent. A new module starts paused.

Each contiguous lesson compiles to one document. Stable page URLs remain aliases to module fragments. Slides, history, reading seeks and module controls share sequential access rules. The full script is readable without generated audio. The content keeps the native Praxity layout and authored width/spacing. The optional outline sidebar sits on the left and the optional full-script sidebar on the right, outside the content area. Both start closed by default and share sidebar styling. Authors can choose the initially open panel through `design.deck`. On desktop the reading panel scrolls independently. On narrow or short screens, an open panel replaces the slide area while playback and navigation remain available. Closing the panel returns to the slide and restores focus to its toggle. The slide scrollport spans the available width, including the margins around the authored content. The course outline and script open one at a time. Timing data may drive seeking and highlighting, but timestamps are not displayed in the transcript. The reading panel stops at the first unreleased gate. No separate captions interface is added in this mode.

This slice rejects logic, variables, conditional content, branching action buttons, private gated responses, pass gates on manually reviewed responses and required non-assessment activities. It does not silently translate their behavior. Blocks must have unique IDs and each lesson's pages must be contiguous.

## Longer assessments and surveys

A logical slide can contain a full, scrollable assessment or survey. Keep related
questions between the same pair of `---` page boundaries and use an assessment
group for a shared submit action. There is no fixed slide height, and scrolling
through questions does not advance to the next slide. Existing block and section
width settings still apply.

```prax
--- Experience survey

## Your experience
as: assessment-group
mode: all
requireAll: true
deckGate: attempt

### How confident do you feel?
as: choice
scored: false

( ) Confident
( ) I would like more practice

### Which support would help?
as: choice
scored: false

( ) A worked example
( ) A discussion

close: assessment-group

--- Reflection

Review what you learned.
```

The group releases access as one unit. `mode: all` submits the questions together;
`mode: oneOf` submits only the selected question, without requiring hidden
alternatives. A group-level `deckGate` overrides its members' release rules.
Otherwise, the group uses pass when any member requires pass, attempt when a
member requires attempt, and no gate when all members are optional and ungraded.
A pass gate uses the group's `passingScore`, or all automatically checked members'
pass results when no threshold is set. Retrying remains subject to each question's
attempt limit. A survey uses attempt, without turning its answers into a score.
Group members must stay on the same logical slide. An `all` group can combine
automatically checked questions with written responses; a `oneOf` pass gate needs
an automatically checked answer for every selectable question.

The no-JavaScript baseline exposes all semantic slides and full reading text. These packaged access rules guide the learner flow; they do not protect confidential content. Physical Safari, assistive-technology and real LMS validation remain separate release checks.

## Learner controls and section navigation

The footer groups outline/transcript and reading-support controls, the shared audio
transport when recordings exist, and labelled previous/next arrows with a slide
count. The outline supplies direct slide navigation. Follow narration and automatic
slide advancement are separate icon toggle buttons in the Transcript panel, with accessible names and pressed states; disabling automatic
advancement still plays the remaining narration within the current slide. Transcript
text remains selectable, with quiet block-level seek actions only for recorded
segments. Production narration is not required for reading.

When a module's opening slide has playable narration, a native "Play narration"
button appears beneath its first heading, before the introductory content. It
shares playback with the footer player and changes to "Pause narration" while
playing. The button stays in content order on desktop, tablet, and mobile; it
does not require the module header. If the slide has no heading, the button appears
at the top of its content. This is default Guided slides behaviour,
with no separate frontmatter option. Silent opening slides omit the button.

An authored `--` divider stays inside the current slide and introduces no playback
stop or tracking event. A `---` page break defines the next logical slide. `deckStop`
and interaction prompts define playback stops. Scrolling and section-heading links
do not advance slides or seek audio. When enabled, the existing floating section
navigation lists headings in the active slide and stays outside the transcript rail.
On narrow layouts, headings remain available in ordinary content order.

Manual slide changes start the content at the top; same-slide transcript seeks do
not reset content scrolling. Arrow-key navigation respects the authored keyboard
setting and leaves form controls and arrow-key widgets alone. Space toggles narration playback when focus is outside interactive controls and the current segment has audio; it does not replace native button or form keyboard behavior. In a mixed narrated module, an intentionally unnarrated slide shows a headphones-off control in the playback position. It remains keyboard-focusable, exposes its unavailable state, and explains “No narration on this slide” on hover or focus. Missing or failed recordings are not treated as intentional silence. Automatic advancement
waits when focus is within the outgoing slide.

At a module boundary, the same Previous and Next arrow buttons move to the adjacent
module. Their labels and tooltips change to Previous module or Next module. Left
and right arrow keys do the same when keyboard navigation is enabled. Previous is
disabled at the start of the course; Next is disabled at its end. Assessment gates
still apply. Automatic narration stops at each module boundary, and the new module
starts paused. Enhanced decks hide the separate footer links between modules;
the no-JavaScript fallback keeps those links available.

## Studio presentation metadata

Studio's Design panel places Presentation above Theme. Course format offers Pages
and Guided slides and saves the choice as `moduleDeck` in `course.yaml`.
Edit settings for selects Course defaults or This module. Course defaults save
under `design` in `course.yaml`; This module writes lesson-level overrides.
Existing module overrides take precedence over course defaults.
Changing format applies its presentation defaults at course level and preserves
module overrides. Changing an option retains the format name with a Modified
indicator. Reset defaults restores the selected scope's presentation defaults
without changing its theme, assessment gates, or narration follow/auto-advance
preferences.

Selecting Guided slides opens the transcript initially, includes links to other
modules, and shows the course/module header. Its module-links switch maps to
`deck.outlineDetail`. Selecting Pages opens the course outline initially and shows
the header; Show page sections enables `navFloatingToc` for long pages. Pages
always retains links to other modules. Turning off Show outline sidebar when module starts
keeps an outline menu available. Glossary page is also in Presentation. Previously authored navigation values remain
readable, but the panel offers only these two presentation families.

Studio-specific YAML options and authoring steps are documented in
[Studio Help: Edit YAML settings](https://praxity.io/en/help/studio/yaml-settings).
The content format carries these values as metadata; other renderers need not
implement Studio's navigation controls.

`design.deck` accepts these optional keys in course design defaults or lesson frontmatter:

| Key | Values | Default |
| --- | --- | --- |
| `preset` | `compact`, `transcript`, `selfPaced`, `custom` | `compact` |
| `outline` | boolean | `true` |
| `outlineDetail` | `currentModule`, `moduleLinks` | `moduleLinks` |
| `initialPanel` | `closed`, `outline`, `transcript` | `closed`, or `transcript` for that preset |
| `slideCounter` | boolean | `true` |
| `followNarration` | boolean | `true` |
| `autoAdvance` | boolean | `true`, or `false` for `selfPaced` |

Explicit values override preset defaults. Custom uses Compact defaults for omitted
keys. Course and lesson deck settings merge per key; each module retains its own
resolved settings in full-course preview and export. An initial outline falls back
to closed when outline access is disabled. Transcript and reading tools remain
available. Follow/advance are learner starting preferences, not completion rules;
learner changes currently last within the module. Disabling the slide count does
not remove screen-reader slide announcements. Transcript seeking preserves the
learner's Follow narration choice. Manual reading can temporarily suspend follow
scrolling; selecting a narration passage resumes it when Follow narration is on.

In Studio, **Outline detail** offers **Current module only** or **Current module
and links to other modules**. Both list the current module's slides. The default,
`moduleLinks`, also shows one link to each other module in course order, using its
module title and opening its first slide. Those links obey the same assessment
gates as slide navigation. Other modules' slides are not listed until the learner
enters that module. This setting does not change authored titles or content. For a lesson override:

```yaml
design:
  deck:
    outlineDetail: currentModule
```

`design.navTopBar: true` enables a compact identity header above the slide and
sidebars. Omitted or false keeps the header hidden. Existing `navCourseTitle` and
`navLessonTitles` switches control the course and module title, respectively, and
default to true. The module title is primary; identical course and module titles
appear once. Disabling both titles removes the header. Course defaults and lesson
overrides apply to these switches, which are available in the deck Design panel.
The header wraps long titles and uses the current navigation theme.

`design.navFloatingToc` and `design.navKeyboardArrows` also apply to decks. Ordinary
page-navigation presets and their chrome options apply to standard pages. Deck
presets do not change theme, gates, stops or course playback mode. The no-JavaScript
fallback still exposes readable content and outline navigation.
