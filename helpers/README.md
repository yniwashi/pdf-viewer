# Helpers Guide

The `helpers/` folder stores JSON and HTML helper files served from:

```text
https://docs.niwashibase.com/helpers/
```

These files are used by the iOS webapp and the Android Ambulance app.

## Current Helper Files

Document indexes:

```text
cpg_index.json
sop_index.json
cpm_index.json
pat_index.json
```

Shared app helpers:

```text
flowcharts.json
formulary.json
```

RSI:

```text
rsi_checklist_js_android.html
```

Pediatric dosing:

```text
ccp_pediatric_dosing_helper.json
ap_pediatric_dosing_helper.json
```

## Live URLs

```text
https://docs.niwashibase.com/helpers/cpg_index.json
https://docs.niwashibase.com/helpers/sop_index.json
https://docs.niwashibase.com/helpers/cpm_index.json
https://docs.niwashibase.com/helpers/pat_index.json
https://docs.niwashibase.com/helpers/flowcharts.json
https://docs.niwashibase.com/helpers/formulary.json
https://docs.niwashibase.com/helpers/rsi_checklist_js_android.html
https://docs.niwashibase.com/helpers/ccp_pediatric_dosing_helper.json
https://docs.niwashibase.com/helpers/ap_pediatric_dosing_helper.json
```

## Used By

### iOS Webapp

The iOS webapp reads helper files directly from `docs.niwashibase.com`.

Known iOS-style helper URLs:

```js
urlIndex: "https://docs.niwashibase.com/helpers/cpg_index.json"
urlSopIndex: "https://docs.niwashibase.com/helpers/sop_index.json"
urlFlowcharts: "https://docs.niwashibase.com/helpers/flowcharts.json"
urlFormulary: "https://docs.niwashibase.com/helpers/formulary.json"
urlPageBase: "https://docs.niwashibase.com/viewer/web/?file=/docs/cpg-81w9d1f.pdf#page="
urlSearchBase: "https://docs.niwashibase.com/viewer/web/?file=/docs/cpg-81w9d1f.pdf#search="
```

Do not rename helper files without updating the iOS webapp.

### Android App

Android version `2.1+` reads most helper URLs and versions from `ambulance_app_config.json`, hosted in the `yazan414` Gist.

App config guide:

```text
https://github.com/yniwashi/ambulance-app-dist/blob/main/README.md
```

Android may cache helpers locally. To force refresh, update the matching version in app config.

## Document Index Helpers

Document index helpers connect search results to PDF pages.

Files:

```text
cpg_index.json
sop_index.json
cpm_index.json
pat_index.json
```

Common fields:

```text
id
title
page_start
page_end
aliases
keywords
type
domains
age_groups
cpg_number
cpg_section
sop_number
sop_section
cpm_number
cpm_section
pat_number
pat_section
source_inputs
age_range
```

Required for useful rendering:

```text
title
page_start
```

Rules:

- Keep `id` stable when possible.
- Update `page_start` and `page_end` when PDF pages change.
- Add aliases for common alternate spelling or wording.
- Add keywords for symptom/search matching.
- Use document number fields such as `sop_number`, `cpm_number`, or `pat_number` when available.
- Android Search should tolerate optional fields and ignore invalid items.

When a PDF update changes page numbers:

1. Replace/update the PDF in `docs/`.
2. Update the matching index helper.
3. Increase the document `version` in Android app config.
4. Test viewer page links.
5. Test iOS and Android search results.

## Flowcharts Helper

File:

```text
flowcharts.json
```

Used for:

- Guidelines flowchart list
- Search
- Reference lookups such as WAAFELSS

General rules:

- Keep item IDs stable.
- Keep titles clear and searchable.
- Include aliases and keywords for common user search terms.
- Include page/reference data when the item opens a document page.
- Validate JSON before upload.

If flowcharts are tied to the CPG document version, update the CPG version in app config when a new flowchart helper should refresh with the CPG cache.

## Formulary Helper

File:

```text
formulary.json
```

Used for:

- Formulary list
- Search
- Medication reference buttons
- Pediatric View Formulary actions

General rules:

- Keep medication IDs stable.
- Include common brand/generic aliases.
- Include spelling variants when useful.
- Keep page numbers synchronized with the current CPG/PDF.
- Validate JSON before upload.

If formulary pages change after a CPG update, update `formulary.json` and increase the matching document/config version so Android refreshes its cached data.

## RSI Checklist Helper

File:

```text
rsi_checklist_js_android.html
```

This is the Android remote RSI HTML template.

Android does not display the remote HTML directly. It downloads the template, replaces Android placeholders, caches the processed HTML, then displays the processed cached copy.

Required placeholders:

```text
__ANDROID_RSI_IMAGE_SRC__
__ANDROID_RSI_AUDIO_BASE__
```

App config entry:

```json
{
  "id": "rsi_checklist",
  "type": "html",
  "version": "4.0",
  "url": "https://docs.niwashibase.com/helpers/rsi_checklist_js_android.html",
  "show_image": true
}
```

To update RSI:

1. Edit `rsi_checklist_js_android.html`.
2. Keep required placeholders.
3. Increase the RSI `version` in app config.
4. Set `show_image` in app config if needed.
5. Test the Android RSI screen.

## CCP/AP Pediatric Dosing Helpers

Files:

```text
ccp_pediatric_dosing_helper.json
ap_pediatric_dosing_helper.json
```

Live URLs:

```text
https://docs.niwashibase.com/helpers/ccp_pediatric_dosing_helper.json
https://docs.niwashibase.com/helpers/ap_pediatric_dosing_helper.json
```

Bundled Android fallback assets:

```text
app/src/main/assets/ccp_pediatric_dosing_helper.json
app/src/main/assets/ap_pediatric_dosing_helper.json
```

Android app config entries:

```json
{
  "id": "ccp_pediatric_dosing",
  "scope": "CCP",
  "age_groups": ["months", "years"],
  "schema_version": "0.1",
  "version": "0.1",
  "url": "https://docs.niwashibase.com/helpers/ccp_pediatric_dosing_helper.json",
  "fallback_asset": "ccp_pediatric_dosing_helper.json",
  "enabled": true
}
```

```json
{
  "id": "ap_pediatric_dosing",
  "scope": "AP",
  "age_groups": ["months", "years"],
  "schema_version": "0.1",
  "version": "0.1",
  "url": "https://docs.niwashibase.com/helpers/ap_pediatric_dosing_helper.json",
  "fallback_asset": "ap_pediatric_dosing_helper.json",
  "enabled": true
}
```

## Pediatric Helper Loading

Android loads pediatric helpers in this order:

1. App config tells Android which helper URL, `version`, and `schema_version` to use.
2. If no valid cache exists for that version/schema, Android downloads the remote helper.
3. If remote helper validates, Android saves it to local cache and uses it.
4. If the same helper version/schema is already cached, Android may use cache without downloading again.
5. If remote/cache fail, Android uses the bundled APK fallback asset.

The app accepts a pediatric helper only when:

- `helper_type` matches the requested helper ID.
- `schema_version` matches app config.
- `version` matches app config.
- `medications` exists and is not empty.
- At least one visible medication button can be rendered.

Important:

- Remote updates work only when helper `version` and app-config helper `version` match.
- If you edit helper content but keep the same `version`, devices may keep using cached data.
- Increase `version` for medication content changes.
- Increase `schema_version` only when the JSON contract changes and Android code supports the new contract.
- During testing, ask before increasing versions if version changes are being controlled manually.

## Pediatric Helper Rendering Contract

### Medication Button Grid

Each visible medication becomes one AP/CCP medication button.

Required:

```text
id
enabled: true
availability.enabled must not be false
name or display.name
sections
```

Rendered:

```text
display.name -> fallback name -> fallback id
ui.sort_order
ui.background_color -> fallback ui.theme_color -> app default
ui.text_color -> app default
ui.accent_color -> fallback ui.theme_color -> app default
```

Metadata only for now:

```text
display.subtitle
ui.badge
ui.icon_key
ui.group
search.aliases
search.keywords
```

### Medication Sheet

Rendered medication-level fields:

```text
display.name / name / id
warnings[].title
warnings[].message
warnings[].level
reference_query or formulary_reference.query
concentration, when concentration.mode = user_entered
```

Rendered section-level fields:

```text
indication or display.title
dose generated from dose_rule
display.dose_label, fallback Dose
route
notes / notes_template / notes_template_when_calculated / notes_when_not_indicated
display.show_volume_calculator
```

Section fields currently metadata only:

```text
ui.badge
ui.priority
ui.accent_color
display.show_route
display.show_notes
```

Section `ui.badge` used to render above the indication and looked confusing. Current Android does not render section badges in AP/CCP medication sheets.

## Pediatric Empty String And Null Rules

Android treats these as missing for rendered text:

```json
""
```

```json
null
```

```json
"null"
```

Blank spaces are also treated as missing.

Examples:

- Empty `ui.badge` does not render.
- Empty `route` does not render a route row.
- Empty `notes_template` does not render notes.
- Empty `display.dose_label` falls back to `Dose`.
- Empty/invalid colors fall back to safe app defaults.
- Empty `display.name` falls back to `name`, then `id`.

Do not use empty strings to hide a medication or section. Use `enabled: false`.

## Pediatric Supported Note Placeholders

Current Android-supported medication note placeholders:

```text
{weight_kg}
{dose_factor}
```

Unknown placeholders are hidden line-by-line so raw `{...}` text does not show to users.

`{concentration_label}` is not used inside medication notes. It is reserved for concentration/volume display wording.

## Pediatric Dose Rule Types

Current supported dose rule types:

```text
static
static_range
not_indicated
weight_based_single
weight_based_range
weight_based_with_min_max
weight_based_rate_single
weight_based_rate_range
conditional
age_or_weight_tier
display_only
```

Do not add a new dose rule type unless Android code is updated first.

## Pediatric Common Editing Tasks

### Update A Dose

1. Find medication by `id`.
2. Find section by `section.id`.
3. Update only the relevant `dose_rule` values.
4. Update notes only if the explanation changed.
5. Validate JSON.
6. Increase helper `version` when publishing.
7. Update matching helper `version` in app config.
8. Upload helper and app config.
9. Test AP/CCP Months and Years as applicable.

### Hide A Medication Temporarily

```json
"enabled": false,
"availability": {
  "enabled": false
}
```

### Show A Medication Again

```json
"enabled": true,
"availability": {
  "enabled": true
}
```

### Remove A Medication Permanently

Delete the medication object from the `medications` array.

### Hide A Section Temporarily

```json
"enabled": false
```

### Remove A Section Permanently

Delete the section object from the medication `sections` array.

### Change Button Color

Use valid `#RRGGBB` colors:

```json
"ui": {
  "background_color": "#B39DDB",
  "text_color": "#111827",
  "accent_color": "#7E57C2"
}
```

## Pediatric Concentration / Volume

For medications where a dose is converted to volume:

```json
"concentration": {
  "mode": "user_entered",
  "guide": {
    "amount": 100,
    "unit": "mcg",
    "volume_ml": 2,
    "label": "100mcg/2ml ampule"
  }
}
```

Use `not_required` when no draw-up volume should be calculated:

```json
"concentration": {
  "mode": "not_required"
}
```

The app remembers the last concentration used by the user. The original package concentration remains a guide only.

## Pediatric Version Rules

Change helper `version` when:

- dose changes
- indication changes
- route changes
- medication added/removed
- section added/removed
- button color/order changes and you need installed devices to refresh

Change `schema_version` only when:

- new contract fields are required
- new dose rule types are added
- Android code must change to understand the helper

Keep helper top-level values synchronized with app config:

```json
"version": "0.1",
"schema_version": "0.1"
```

## Validation Checklist

Before uploading a helper:

1. Confirm JSON is valid.
2. Confirm `helper_type` is correct.
3. Confirm `version` matches app config.
4. Confirm `schema_version` matches app config.
5. Confirm medication count is expected.
6. Confirm disabled medications are intentionally disabled.
7. Confirm all clinical dose changes were reviewed.
8. Test direct helper URL.
9. Test Android after cache refresh or app reinstall.

## Safe Rules

- Do not rename files unless both apps are updated.
- Do not rename `helper_type`.
- Do not rename `schema_version`.
- Do not rename `version`.
- Do not add unsupported dose rule types.
- Keep direct URLs working.
- Keep iOS webapp expectations in mind when changing shared helpers.
- For Android remote refresh, update app config versions.
