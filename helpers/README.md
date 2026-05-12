# CCP and AP Pediatric Dosing Helper Guide

This guide explains how to update the remote pediatric dosing helpers safely.

## What The Helpers Control

The CCP and AP pediatric helpers are remote JSON files used by the Android pediatric medication screens.

They control:

- Medication button list and order.
- Medication button colors.
- Medication names and subtitles.
- Which medications and sections are visible.
- Indications.
- Dose rules.
- Routes.
- Notes.
- Warnings.
- Formulary/reference lookup keys.
- Original concentration/package guide.
- Whether the section can show the dose-volume calculator.

They do not control:

- The screen layout.
- The AP/CCP age and weight input UI.
- WAAFELSS calculation logic. WAAFELSS remains local app logic.
- The actual volume formula code. The app calculates volume from the rendered dose and the user-confirmed concentration.
- New dose-rule types unless Android code has been updated first.

## How Android Uses The Helpers

Android loads each helper in this order:

1. Remote helper URL from `ambulance_app_config.json`.
2. Cached helper, if the remote helper is unavailable or unchanged.
3. Bundled fallback asset inside the APK.

The app accepts a remote helper only when:

- `helper_type` matches the requested helper.
- `schema_version` matches app config.
- `version` matches app config.
- The helper has a valid `medications` array.

If these values do not match, Android rejects the remote helper and uses cached or bundled fallback data.

Current helper usage:

- CCP Months uses `ccp_pediatric_dosing`.
- CCP Years uses `ccp_pediatric_dosing`.
- AP Months uses `ap_pediatric_dosing`.
- AP Years uses `ap_pediatric_dosing`.

Current app-supported note placeholders are only:

- `{weight_kg}`
- `{dose_factor}`

`{concentration_label}` is not used in medication notes. It is reserved metadata for volume/concentration display wording and is not currently rendered by Android.

## Rendering Contract

This is what the current Android app actually renders from the helpers.

### Medication Button Grid

Each visible medication becomes one button in the AP/CCP medication grid.

Required for a medication button:

- `id`
- `enabled: true`
- `availability.enabled` must not be `false`
- At least one visible section for the selected profile

Rendered fields:

- Button text: `display.name`, fallback `name`, fallback `id`
- Button order: `ui.sort_order`
- Button background: `ui.background_color`, fallback `ui.theme_color`, fallback app default
- Button text color: `ui.text_color`, fallback app default
- Button border/accent: `ui.accent_color`, fallback `ui.theme_color`, fallback app default

Optional fields that are currently metadata only:

- Medication `display.subtitle`
- Medication `ui.badge` such as `CCP` or `AP`
- Medication `ui.icon_key`
- Medication `ui.group`
- Medication `search.aliases`
- Medication `search.keywords`

These optional fields can stay in the helper for future use, but they do not currently render on the medication grid.

### Medication Sheet

When the user taps a medication button, the app opens a medication sheet.

Rendered medication-level fields:

- Sheet title: `display.name`, fallback `name`, fallback `id`
- Warning cards: `warnings[].title`, `warnings[].message`, and `warnings[].level`
- Formulary/reference action: `reference_query` or `formulary_reference.query`, depending on helper structure
- Concentration panel, if `concentration.mode = user_entered`

Rendered section-level fields:

- `indication`, fallback `display.title`
- Dose generated from `dose_rule`
- Dose label from `display.dose_label`, fallback `Dose`
- `route`
- Notes from `notes`, `notes_template`, `notes_template_when_calculated`, or `notes_when_not_indicated`
- Volume display if `display.show_volume_calculator = true`, concentration is saved, route is supported, and dose can be parsed

Section fields that are currently metadata only:

- `ui.badge`
- `ui.priority`
- `ui.accent_color`
- `display.show_route`
- `display.show_notes`

Important: section `ui.badge` used to render above the indication and looked confusing. Current Android does not render section badges in AP/CCP medication sheets. You can keep badges as metadata, set them to `null`, or set them to an empty string.

### Empty String And Null Rules

The app treats these as missing values for rendered text:

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

Practical examples:

- If `ui.badge` is `""`, it does not render.
- If `route` is `""`, the Route row does not render.
- If `notes_template` is `""`, Notes do not render.
- If `display.dose_label` is `""`, the app uses `Dose`.
- If a color value is `""` or invalid, the app uses a safe fallback color.
- If `display.name` is `""`, the app falls back to `name`, then `id`.

If a section has no visible indication, dose, route, or notes after cleanup, the app drops that section from the sheet.

Do not depend on empty strings to hide medications or sections. Use `enabled: false` for that.

## Required And Optional Fields

### Medication Object

Recommended medication structure:

```json
{
  "id": "fentanyl",
  "name": "Fentanyl",
  "enabled": true,
  "reference_query": "Fentanyl",
  "sections": [],
  "display": {
    "name": "Fentanyl",
    "subtitle": "Analgesia"
  },
  "availability": {
    "enabled": true,
    "profiles": ["ccp_months", "ccp_years"],
    "requires_scope": "CCP"
  },
  "ui": {
    "sort_order": 100,
    "background_color": "#B39DDB",
    "text_color": "#111827",
    "accent_color": "#7E57C2"
  },
  "concentration": {
    "mode": "user_entered",
    "guide": {
      "amount": 100,
      "unit": "mcg",
      "volume_ml": 2
    }
  }
}
```

Medication fields required for current rendering:

- `id`
- `name` or `display.name`
- `enabled`
- `sections`

Strongly recommended:

- `availability.enabled`
- `availability.profiles`
- `ui.sort_order`
- `ui.background_color`
- `ui.text_color`
- `ui.accent_color`
- `reference_query` or `formulary_reference.query`
- `concentration.mode`

Optional / future metadata:

- `display.subtitle`
- `ui.group`
- `ui.theme_color`
- `ui.icon_key`
- `ui.badge`
- `search`
- `content_version`
- `review_status`

### Section Object

Recommended section structure:

```json
{
  "id": "fentanyl_analgesia_iv_io",
  "enabled": true,
  "applies_to": ["ccp_months", "ccp_years"],
  "indication": "Analgesia (IV/IO)",
  "dose_rule": {
    "type": "weight_based_single",
    "factor": 1,
    "unit": "mcg",
    "per": "kg"
  },
  "route": "IV/IO",
  "notes_template": "MAX Total Dose 2 mcg/kg\nRef. Dose Calculation: {weight_kg} kg x 1mcg",
  "display": {
    "show_volume_calculator": true,
    "dose_label": "Dose"
  },
  "ui": {
    "sort_order": 10,
    "badge": null
  }
}
```

Section fields required for current rendering:

- `id`
- `enabled`
- `applies_to`
- `dose_rule`

Strongly recommended:

- `indication`
- `route`
- one of `notes`, `notes_template`, `notes_template_when_calculated`, or `notes_when_not_indicated` when extra guidance is needed
- `display.show_volume_calculator`
- `ui.sort_order`

Optional / currently metadata only:

- `ui.badge`
- `ui.priority`
- `ui.accent_color`
- `display.show_route`
- `display.show_notes`

Current Android ignores section `ui.sort_order`; sections render in the order they appear in the `sections` array. Keep the array order correct.

## Common Editing Tasks

### Update A Medication Dose

1. Find the medication by `id`.
2. Find the matching section by `section.id`.
3. Update only the relevant `dose_rule` values such as `factor`, `amount`, `min_factor`, `max_factor`, `min`, or `max`.
4. Update `notes_template` only if the explanatory note needs to change.
5. Validate JSON.
6. Increase helper `version` and update app config version when ready to publish.
7. Upload helper and app config.
8. Device-test the medication in Months and/or Years as applicable.

Do not change `id` unless you are intentionally creating a new medication/section identity.

### Update A Medication Name Or Button Color

Name:

```json
"display": {
  "name": "Fentanyl"
}
```

Button colors:

```json
"ui": {
  "background_color": "#B39DDB",
  "text_color": "#111827",
  "accent_color": "#7E57C2"
}
```

If a color is empty or invalid, Android falls back to a safe default. Still keep colors valid `#RRGGBB` values.

### Hide A Medication Temporarily

Use this when you may want to bring the medication back later:

```json
"enabled": false,
"availability": {
  "enabled": false
}
```

The medication button will disappear from AP/CCP screens after the helper refreshes.

### Remove A Medication Permanently

Delete the medication object from the `medications` array.

Use this only when you are sure it should no longer exist in the helper. After refresh, the app will no longer show it.

### Hide A Section Temporarily

```json
"enabled": false
```

The section will not render in the medication sheet.

### Remove A Section Permanently

Delete the section object from the medication `sections` array.

### Add A New Medication

1. Copy an existing medication with a similar type.
2. Change `id` to a stable lowercase identifier.
3. Set `name` and `display.name`.
4. Set `enabled: true`.
5. Set `availability.enabled: true`.
6. Set correct profiles:
   - CCP: `ccp_months`, `ccp_years`
   - AP: `ap_months`, `ap_years`
7. Set `ui.sort_order` so the button appears in the right place.
8. Set valid button colors.
9. Add at least one section with a supported `dose_rule`.
10. Set concentration mode:
    - `user_entered` if volume can be calculated from a prepared concentration
    - `not_required` if no draw-up volume should be calculated
11. Add formulary/reference key.
12. Validate JSON, publish with version update, and device-test.

Minimum visible medication example:

```json
{
  "id": "example_medication",
  "name": "Example Medication",
  "enabled": true,
  "reference_query": "Example Medication",
  "availability": {
    "enabled": true,
    "profiles": ["ccp_months", "ccp_years"]
  },
  "ui": {
    "sort_order": 999,
    "background_color": "#BFDBFE",
    "text_color": "#111827",
    "accent_color": "#2563EB"
  },
  "concentration": {
    "mode": "not_required"
  },
  "sections": [
    {
      "id": "example_medication_indication",
      "enabled": true,
      "applies_to": ["ccp_months", "ccp_years"],
      "indication": "Example Indication",
      "dose_rule": {
        "type": "static",
        "amount": 1,
        "unit": "mg"
      },
      "route": "IV",
      "display": {
        "show_volume_calculator": false
      }
    }
  ]
}
```

### Add A New Indication To An Existing Medication

Add a new object inside that medication's `sections` array.

Required:

- new stable `id`
- `enabled: true`
- correct `applies_to`
- `indication`
- supported `dose_rule`
- `route`

Optional:

- `notes_template`
- `display.dose_label`
- `display.show_volume_calculator`
- `ui.sort_order` for editor readability, though current Android follows array order

### Remove Strange Extra Text From The Sheet

If you see unexpected text above an indication, check whether it is a section badge:

```json
"ui": {
  "badge": "Arrest"
}
```

Current Android no longer renders section badges, but older builds did. To avoid confusion across builds, use:

```json
"badge": null
```

or:

```json
"badge": ""
```

Do not use `badge` for route values like `PO`; use the section `route` field.

## Files

CCP live helper:

```text
https://docs.niwashibase.com/helpers/ccp_pediatric_dosing_helper.json
```

AP live helper:

```text
https://docs.niwashibase.com/helpers/ap_pediatric_dosing_helper.json
```

Working copies:

```text
TEMP/ccp_pediatric_dosing_helper.json
TEMP/ap_pediatric_dosing_helper.json
```

Android bundled fallbacks:

```text
app/src/main/assets/ccp_pediatric_dosing_helper.json
app/src/main/assets/ap_pediatric_dosing_helper.json
```

App config entries:

```text
ambulance_app_config.json -> pediatric_dosing.helpers[id=ccp_pediatric_dosing]
ambulance_app_config.json -> pediatric_dosing.helpers[id=ap_pediatric_dosing]
```

## App Config

Both helpers must be listed under `pediatric_dosing.helpers`:

```json
"pediatric_dosing": {
  "enabled": true,
  "helpers": [
    {
      "id": "ccp_pediatric_dosing",
      "scope": "CCP",
      "age_groups": ["months", "years"],
      "schema_version": "0.1",
      "version": "0.1",
      "url": "https://docs.niwashibase.com/helpers/ccp_pediatric_dosing_helper.json",
      "fallback_asset": "ccp_pediatric_dosing_helper.json",
      "enabled": true
    },
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
  ]
}
```

## Version Rules

The Android app accepts a helper only when these values match the matching app-config helper entry:

```json
{
  "schema_version": "0.1",
  "helper_type": "ccp_pediatric_dosing",
  "version": "0.1"
}
```

```json
{
  "schema_version": "0.1",
  "helper_type": "ap_pediatric_dosing",
  "version": "0.1"
}
```

Use these rules:

- Change helper `version` for dose/content changes.
- Change helper `schema_version` only when the JSON structure/contract changes and Android support is updated.
- After changing either value in the helper, update the same value in `ambulance_app_config.json`.
- Ask before increasing any helper or app-config version during testing.

If the values do not match, Android rejects the remote helper and uses cached or bundled fallback data.

## Safe Update Workflow

1. Edit the matching TEMP helper file.
2. Validate JSON syntax.
3. Review every changed medication and section clinically.
4. Increase helper `version` if any medication, indication, route, dose, note, warning, color, concentration guide, or enabled flag changed.
5. Increase helper `schema_version` only if the app contract changed.
6. Upload the helper to the docs URL.
7. Update `TEMP/ambulance_app_config.json` with the same helper `version` and `schema_version`.
8. Upload the app config.
9. Copy the helper into the matching Android bundled fallback before release.
10. Build and test Android.

Validation commands:

```bash
python3 -m json.tool TEMP/ccp_pediatric_dosing_helper.json > /tmp/ccp_peds_validated.json
python3 -m json.tool TEMP/ap_pediatric_dosing_helper.json > /tmp/ap_peds_validated.json
```

Android verification:

```bat
cmd.exe /c gradlew.bat assembleDebug
cmd.exe /c gradlew.bat testDebugUnitTest --tests com.example.ambulancedesign.pediatrics.PediatricDosingEngineTest
```

## Medication Visibility

To hide a medication without deleting it:

```json
"enabled": false
```

The app will not show the medication card.

To remove a medication completely, delete its object from the `medications` array. Use this only when you are sure you no longer need the medication in the helper.

Sections can also be hidden:

```json
"enabled": false
```

## Supported Dose Rules

Current Android supports these rule types:

- `static`
- `static_range`
- `not_indicated`
- `weight_based_single`
- `weight_based_range`
- `weight_based_with_min_max`
- `weight_based_rate_single`
- `weight_based_rate_range`
- `conditional`
- `age_or_weight_tier`
- `display_only`

Do not add a new `dose_rule.type` unless Android has been updated to support it. Unknown rule types are not calculated.

## Common Rule Patterns

### Static Dose

Use when the dose is fixed.

```json
"dose_rule": {
  "type": "static",
  "amount": 5,
  "unit": "mg"
}
```

### Weight-Based Single Dose

Use for one factor such as `0.01 mg/kg`.

```json
"dose_rule": {
  "type": "weight_based_single",
  "factor": 0.01,
  "unit": "mg",
  "per": "kg",
  "max": 0.5
}
```

### Weight-Based Range

Use for ranges such as `1-2 mcg/kg`.

```json
"dose_rule": {
  "type": "weight_based_range",
  "min_factor": 1,
  "max_factor": 2,
  "unit": "mcg",
  "per": "kg",
  "max": 100
}
```

### Conditional Rule

Use when months and years differ, or when one profile has a fixed dose and another is calculated.

```json
"dose_rule": {
  "type": "conditional",
  "default": {
    "type": "weight_based_single",
    "factor": 0.5,
    "unit": "mg",
    "per": "kg",
    "max": 5
  },
  "overrides": [
    {
      "when": {
        "profile": "ccp_years"
      },
      "rule": {
        "type": "static",
        "amount": 5,
        "unit": "mg"
      }
    }
  ]
}
```

The app starts with `default`, then uses the first matching override.

### Age Or Weight Tiers

Use when the dose is selected by age tier or weight tier, such as Hydrocortisone or Ibuprofen.

Important behavior:

- If the screen has direct age input, the app can use `age_tiers`.
- If the screen has direct weight input, the app can use `weight_tiers`.
- AP screens are currently age-input driven and estimate weight from age.
- Tiers are checked in the listed order.
- The first matching tier is used.

## Profiles

CCP profiles:

- `ccp_months`
- `ccp_years`

AP profiles:

- `ap_months`
- `ap_years`

Every visible section should list the correct profiles in `applies_to`.

## Blocking Or Not Indicated

Use `not_indicated` for a section that must show but not calculate.

```json
"dose_rule": {
  "type": "not_indicated",
  "message": "Not for use in children under 7 kg."
}
```

For conditional blocking, use an override to `not_indicated`. Do not use old or custom keys like `blocked_when` or `when_blocked`.

## Concentration And Volume

The helper should keep original medication packaging as a guide only. This is not automatically used as the actual prepared concentration until the user confirms or changes it in the app.

Supported concentration mode for user-confirmed volume calculation:

```json
"concentration": {
  "mode": "user_entered",
  "remember_last_used": true,
  "show_warning": true,
  "guide": {
    "amount": 500,
    "unit": "mg",
    "volume_ml": 10,
    "label": "500mg/10ml vial"
  }
}
```

The app uses this as the original concentration guide shown to the user.

Example user-facing guide text:

```text
Original concentration guide: 500 mg / 10 ml = 50 mg/ml
```

The user can then press `Set concentration` or `Change concentration`, confirm/edit amount, unit, and volume, and save the prepared concentration.

Example user-confirmed concentration text:

```text
Confirm this is the prepared concentration. Volume is calculated using: 500 mg / 10 ml = 50 mg/ml
```

When volume is calculated, the app shows:

```text
Volume to draw
2 ml
Based on 500 mg / 10 ml = 50 mg/ml
```

Current important behavior:

- The app reads concentration guide fields from `concentration.guide.amount`, `concentration.guide.unit`, and `concentration.guide.volume_ml`.
- The guide is educational/original packaging guidance only.
- The user must save a concentration before volume is shown.
- Saved concentration is remembered per medication.
- If a medication is diluted differently, the user must change the concentration before using the volume.
- Volume is shown only when `display.show_volume_calculator` allows it and the dose/route can be safely parsed.

Use this for medications that should not show volume calculation:

```json
"concentration": {
  "mode": "not_required"
}
```

For Dextrose 10%, the dose is already in ml/kg, so keep:

```json
"dose_is_administration_volume": true
```

### `{concentration_label}`

`{concentration_label}` appears in the CCP helper under:

```json
"default_result_format": {
  "calculation_basis_template": "Dose and volume are based on {weight_kg} kg and concentration {concentration_label}."
}
```

Keep it there as metadata for future result-format wording.

Current Android behavior:

- Android does not currently render `calculation_basis_template`.
- Android does not currently replace `{concentration_label}` from helper text.
- Android builds the visible concentration wording directly in Kotlin from the saved concentration values.
- Do not use `{concentration_label}` inside `notes`, `notes_template`, or `notes_template_when_calculated`.

If Android later uses `calculation_basis_template`, `{concentration_label}` should mean the rendered concentration summary, for example:

```text
100 mcg / 2 ml = 50 mcg/ml
```

Then the future rendered text could become:

```text
Dose and volume are based on 20 kg and concentration 100 mcg / 2 ml = 50 mcg/ml.
```

Until Android explicitly supports this template, keep concentration wording controlled by the app.

## UI Colors

Each medication can define UI colors:

```json
"ui": {
  "background_color": "#FEE2E2",
  "text_color": "#7F1D1D",
  "accent_color": "#B91C1C"
}
```

The app validates/falls back if a color is missing or invalid. Colors should be readable in light mode.

## Notes Templates

Notes can include a small set of app-supported placeholders. These are replaced by the Android dosing engine before the note is shown to the user.

```json
"notes_template": "MAX dose is 0.5mg\nRef. Dose Calculation: {weight_kg}kg x 0.01mg"
```

Supported note placeholders:

- `{weight_kg}`
- `{dose_factor}`

### `{weight_kg}`

`{weight_kg}` is replaced with the patient weight used by the app for the calculation.

If the user entered an age, this is the estimated weight for that age. If the user entered a direct weight, this is the direct entered weight.

Example:

```json
"notes_template": "Ref. Dose Calculation: {weight_kg} kg x 0.01mg"
```

If the app is calculating with `20 kg`, the note shown to the user becomes:

```text
Ref. Dose Calculation: 20 kg x 0.01mg
```

### `{dose_factor}`

`{dose_factor}` is replaced with the dose factor selected by the active `dose_rule`.

Use it when the factor can change because of a `conditional` rule, age/weight override, or another supported rule path.

Example:

```json
"notes_template": "Ref. Dose Calculation: {weight_kg} kg x {dose_factor}mg"
```

If the app is calculating with `10 kg` and the selected factor is `7.5`, the note shown to the user becomes:

```text
Ref. Dose Calculation: 10 kg x 7.5mg
```

For a range rule, `{dose_factor}` may render as a range such as:

```text
25-50
```

Example:

```json
"notes_template": "Ref. Dose Calculation: {weight_kg} kg x {dose_factor}mg"
```

May become:

```text
Ref. Dose Calculation: 20 kg x 25-50mg
```

### Not Supported In Notes

Do not use calculation expressions inside notes:

```text
{weight_kg * 2}
```

Do not use concentration placeholders inside notes:

```text
{concentration_label}
```

`{concentration_label}` belongs to volume/concentration display logic only, not medication notes.

If an unsupported `{...}` placeholder is accidentally added to a note, the app will hide that note line instead of showing raw template text to users. Treat this as a helper mistake and fix the helper.

Keep notes simple and clinical. Do not put a second dose formula in notes if it conflicts with `dose_rule`.

## Review Checklist

For every medication update, check:

- Medication `id` is stable and unique.
- `enabled` is correct.
- `applies_to` includes the correct profiles.
- Every visible section has a clear `indication`.
- Route is correct.
- Dose rule type is supported by Android.
- Factor/unit/max/min values are clinically correct.
- Age/weight tier ranges do not overlap incorrectly.
- Not-indicated sections are represented with `not_indicated`.
- Concentration mode is correct.
- `display.show_volume_calculator` is correct.
- Warnings are clear if volume/dilution risk exists.
- Formulary reference keys are still valid.
- Note templates use only `{weight_kg}` and `{dose_factor}`.
- `{concentration_label}` is not used in notes.
- No expression placeholders exist, such as `{weight_kg * 2}`.
- Concentration guide uses `amount`, `unit`, and `volume_ml`.
- Helper `version` and app-config helper `version` match after upload.
- Bundled fallback helper is updated before release.

## Production Check Commands

Use these before publishing helper changes:

```bash
python3 -m json.tool TEMP/ccp_pediatric_dosing_helper.json > /tmp/ccp_peds_validated.json
python3 -m json.tool TEMP/ap_pediatric_dosing_helper.json > /tmp/ap_peds_validated.json
```

Check helper placeholders:

```bash
rg -o '\{[^{}]+\}' TEMP/ccp_pediatric_dosing_helper.json | sort -u
rg -o '\{[^{}]+\}' TEMP/ap_pediatric_dosing_helper.json | sort -u
```

Expected placeholders:

```text
{weight_kg}
{dose_factor}
{concentration_label}
```

Important:

- `{concentration_label}` should appear only in result-format metadata, not notes.
- AP may only show `{weight_kg}` and `{dose_factor}`.
- Do not publish if notes contain unknown placeholders.

Android verification:

```bat
cmd.exe /c gradlew.bat testDebugUnitTest --tests com.example.ambulancedesign.pediatrics.PediatricDosingEngineTest
cmd.exe /c gradlew.bat assembleDebug
```

Device-test:

- CCP Months and Years.
- AP Months and Years.
- Remote helper behavior.
- Cached helper behavior.
- Bundled fallback behavior.
- Disabled medications.
- Hidden sections.
- Conditional/static/tiered/display-only rules.
- Max/min caps.
- Not-indicated sections.
- Concentration guide.
- Saved concentration.
- Changed/diluted concentration.
- Volume visibility.
- Formulary/Guide buttons.
- WAAFELSS local behavior.
