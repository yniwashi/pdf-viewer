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
