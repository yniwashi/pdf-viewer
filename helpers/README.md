# CCP Pediatric Dosing Helper Guide

This guide explains how to update `ccp_pediatric_dosing_helper.json` safely.

## Files

Live helper:

```text
https://docs.niwashibase.com/helpers/ccp_pediatric_dosing_helper.json
```

Working copy:

```text
TEMP/ccp_pediatric_dosing_helper.json
```

Android bundled fallback:

```text
app/src/main/assets/ccp_pediatric_dosing_helper.json
```

App config entry:

```text
ambulance_app_config.json -> pediatric_dosing.helpers[id=ccp_pediatric_dosing]
```

## Version Rules

The Android app accepts the helper only when these values match app config:

```json
{
  "schema_version": "0.1",
  "helper_type": "ccp_pediatric_dosing",
  "version": "0.1"
}
```

Use this rule:

- Change helper `"version"` for dose/content changes.
- Change helper `"schema_version"` only when the JSON structure/contract changes.
- After changing either value in the helper, update the same value in `ambulance_app_config.json`.

If the values do not match, Android rejects the remote helper and uses cached or bundled fallback data.

## Safe Update Workflow

1. Edit `TEMP/ccp_pediatric_dosing_helper.json`.
2. Validate JSON syntax.
3. Review every changed medication and section clinically.
4. Increase helper `"version"` if any medication, indication, route, dose, note, warning, color, concentration guide, or enabled flag changed.
5. Increase helper `"schema_version"` only if the app contract changed.
6. Upload the helper to the docs URL.
7. Update `TEMP/ambulance_app_config.json` with the same helper `version` and `schema_version`.
8. Upload the app config.
9. Copy the helper into `app/src/main/assets/ccp_pediatric_dosing_helper.json` before release.
10. Build and test Android.

Validation command:

```bash
python3 -m json.tool TEMP/ccp_pediatric_dosing_helper.json > /tmp/ccp_peds_validated.json
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

Use when CCP Months and CCP Years differ.

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

- If the user entered age, the app uses `age_tiers`.
- If the user entered direct weight, the app uses `weight_tiers`.
- Tiers are checked in the listed order.
- The first matching tier is used.

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

The helper should keep original medication packaging as a guide only.

Supported concentration modes:

```json
"concentration": {
  "mode": "user_entered",
  "guide": {
    "amount": 500,
    "amount_unit": "mg",
    "volume": 10,
    "volume_unit": "ml",
    "label": "500mg/10ml vial"
  }
}
```

The app does not use guide values automatically. The user must confirm or enter the concentration they prepared before volume is calculated.

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

Notes can include calculated values:

```json
"notes_template": "MAX dose is 0.5mg\nRef. Dose Calculation: {weight_kg}kg x 0.01mg"
```

Supported placeholders include values already calculated by the engine, such as:

- `{weight_kg}`
- dose amounts generated by the rule, depending on rule type

Keep notes simple and clinical. Do not put a second dose formula in notes if it conflicts with `dose_rule`.

## Review Checklist

For every medication update, check:

- Medication `id` is stable and unique.
- `enabled` is correct.
- `applies_to` includes the correct profiles: `ccp_months`, `ccp_years`, or both.
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

## Current Next Steps

- Device-test CCP Months and CCP Years with remote helper, cached helper, and bundled fallback behavior.
- Test disabled medications, blocked sections, conditional/static/tiered dose rules, max caps, and concentration/volume calculations.
- Add a helper source/status indicator only if needed for testing.
- After CCP is approved, create an AP Pediatric helper and wire AP Months/Years to the same architecture.
