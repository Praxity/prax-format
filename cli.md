# Praxity Studio CLI

Praxity Studio includes a headless export command. It uses the same project loader,
accessibility checker, viewer assets, and packaging code as the desktop export flow.
Studio must be installed.

## Command

```sh
praxity export <project-dir> \
  --format html|scorm-1.2|scorm-2004 \
  --output <zip-path> \
  [--allow-critical-a11y-issues]
```

Every format produces a ZIP. The command exports the complete course in the order
defined by `course.yaml`. It does not expose single-lesson export.

Both `--format` and `--output` are required. The output is the exact filename, must
end in `.zip`, and its parent directory must already exist. An existing ZIP is
replaced only after the new package has built successfully.

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

## Project requirements

The input is a complete project directory with:

- `course.yaml`, including the ordered lesson list;
- every listed `.prax` lesson;
- any local files referenced under `assets/`;
- optional `praxity.json` export settings.

Course and lesson settings take precedence over an immediate parent
`workspace.praxity.yaml`. Fixed defaults apply when neither defines a value.
`praxity.json` supplies the completion threshold when valid; otherwise the fixed
threshold is `0.8`. The command does not read Studio's global settings.

The CLI reads the project but never updates or migrates it. Do not edit project files
while export is running. Concurrent project writes are unsupported.

## Machine-readable results

Each `export` invocation writes one JSON object to stdout. A successful export looks
like this:

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

Human-readable failures and override warnings go to stderr. Export emits no progress
messages and sends no telemetry. `--help` and `--version` return plain text.

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
