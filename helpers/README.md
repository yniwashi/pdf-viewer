# Helpers Guide

The `helpers/` folder stores JSON and HTML helper files served from:

```text
https://docs.niwashibase.com/helpers/
```

Current migration state:

- iOS webapp still reads helper files directly from `https://docs.niwashibase.com/helpers/`.
- Android now reads lightweight helper/app-data files through the API:

```text
https://api.niwashibase.com/api/v1/ambulance/app-data/{resource}
```

- The API Worker serves Android app-data from Cloudflare R2 bucket `ambulance-app-configs`, folder `app-data/`.
- Until iOS is migrated, helper updates must be published to both:
  - `docs.niwashibase.com/helpers/` for iOS;
  - R2 `app-data/` for Android.
- PDFs and website icons remain on `docs.niwashibase.com` for now.

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
websites.json
as_call.json
hos_sites.json
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

## Live iOS / Docs Helper URLs

```text
https://docs.niwashibase.com/helpers/cpg_index.json
https://docs.niwashibase.com/helpers/sop_index.json
https://docs.niwashibase.com/helpers/cpm_index.json
https://docs.niwashibase.com/helpers/pat_index.json
https://docs.niwashibase.com/helpers/flowcharts.json
https://docs.niwashibase.com/helpers/formulary.json
https://docs.niwashibase.com/helpers/websites.json
https://docs.niwashibase.com/helpers/as_call.json
https://docs.niwashibase.com/helpers/hos_sites.json
https://docs.niwashibase.com/helpers/rsi_checklist_js_android.html
https://docs.niwashibase.com/helpers/ccp_pediatric_dosing_helper.json
https://docs.niwashibase.com/helpers/ap_pediatric_dosing_helper.json
```

## Android API/R2 App-Data URLs

Android app config should point lightweight helper/index URLs to:

```text
https://api.niwashibase.com/api/v1/ambulance/app-data/cpg-index
https://api.niwashibase.com/api/v1/ambulance/app-data/sop-index
https://api.niwashibase.com/api/v1/ambulance/app-data/cpm-index
https://api.niwashibase.com/api/v1/ambulance/app-data/pat-index
https://api.niwashibase.com/api/v1/ambulance/app-data/flowcharts
https://api.niwashibase.com/api/v1/ambulance/app-data/formulary
https://api.niwashibase.com/api/v1/ambulance/app-data/websites
https://api.niwashibase.com/api/v1/ambulance/app-data/as-call
https://api.niwashibase.com/api/v1/ambulance/app-data/hos-sites
https://api.niwashibase.com/api/v1/ambulance/app-data/analytics-config
https://api.niwashibase.com/api/v1/ambulance/app-data/rsi-checklist
https://api.niwashibase.com/api/v1/ambulance/app-data/ccp-pediatric-dosing
https://api.niwashibase.com/api/v1/ambulance/app-data/ap-pediatric-dosing
```

R2 object mapping:

```text
app-data/cpg_index.json
app-data/sop_index.json
app-data/cpm_index.json
app-data/pat_index.json
app-data/flowcharts.json
app-data/formulary.json
app-data/websites.json
app-data/as_call.json
app-data/hos_sites.json
app-data/analytics_config.json
app-data/rsi_checklist_js_android.html
app-data/ccp_pediatric_dosing_helper.json
app-data/ap_pediatric_dosing_helper.json
```

Local TEMP helper drafts are stored in:

```text
/mnt/e/code assist/apps - work/android projects/TEMP/App Data/
```

Private staff-review helper files are not app-data and are stored separately in:

```text
/mnt/d/programming/Ambulance Private/Staff Review/
```

Do not store private staff review files in TEMP because TEMP is backed up to GitHub.

## How-To: Publish A Helper Update

Use this for document indexes, flowcharts, formulary, websites, AS-Call, HOS, analytics config, RSI HTML, or pediatric dosing helpers.

1. Edit the helper file locally.
2. Validate the JSON if the helper is JSON.
3. Upload the docs helper copy for iOS if iOS still uses that helper:

```text
https://docs.niwashibase.com/helpers/
```

4. Upload the Android R2 copy:

```text
ambulance-app-configs/app-data/
```

5. Increase the matching helper/document version in Android app config when Android needs to refresh its cache.
6. Upload the changed app config to R2.
7. Test the Android API route:

```text
https://api.niwashibase.com/api/v1/ambulance/app-data/{resource}
```

8. Test the affected app screen.

Important:

- Editing the helper without increasing the matching app-config version may leave Android users on cached data.
- Editing the R2 helper without updating the docs helper can leave iOS users on old data.
- Editing the docs helper without updating R2 can leave Android users on old data.

## Used By

### iOS Webapp

The iOS webapp reads helper files directly from `docs.niwashibase.com`.

Known iOS-style helper and viewer URLs:

```js
urlIndex: "https://docs.niwashibase.com/helpers/cpg_index.json"
urlSopIndex: "https://docs.niwashibase.com/helpers/sop_index.json"
urlCpmIndex: "https://docs.niwashibase.com/helpers/cpm_index.json"
urlFlowcharts: "https://docs.niwashibase.com/helpers/flowcharts.json"
urlFormulary: "https://docs.niwashibase.com/helpers/formulary.json"
urlWebsites: "https://docs.niwashibase.com/helpers/websites.json"
urlAsCall: "https://docs.niwashibase.com/helpers/as_call.json"
urlHosSites: "https://docs.niwashibase.com/helpers/hos_sites.json"
urlPageBase: "https://docs.niwashibase.com/viewer/web/?file=/docs/cpg-81w9d1f.pdf#page="
urlSopPageBase: "https://docs.niwashibase.com/viewer/web/?file=/docs/sop-101qq9f2w.pdf#page="
urlCpmPageBase: "https://docs.niwashibase.com/viewer/web/?file=/docs/cpm-202e9d33q.pdf#page="
urlSearchBase: "https://docs.niwashibase.com/viewer/web/?file=/docs/cpg-81w9d1f.pdf#search="
```

The iOS webapp preloads CPG, SOP, CPM, flowcharts, formulary, websites, and AS-Call helper data when the app opens. It keeps helper data in local storage for up to 7 days and refreshes in the background. Do not rename helper files without updating the iOS webapp.

### Android App

Android version `2.1+` reads most helper URLs and versions from `ambulance_app_config.json`.

Current Android app config source is the NiwashiBase API backed by Cloudflare R2:

```text
https://api.niwashibase.com/api/v1/ambulance/app-config/production
https://api.niwashibase.com/api/v1/ambulance/app-config/testing
https://api.niwashibase.com/api/v1/ambulance/app-config/backup
```

The Android helper URLs inside app config now use API/R2 app-data endpoints. Do not point Android helper URLs back to docs unless intentionally rolling back.

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
- iOS and Android search should tolerate optional fields and ignore invalid items.

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

## Websites Helper

File:

```text
websites.json
```

Used for:

- iOS webapp Websites list
- Android Websites list
- Remote website titles, categories, subtitles, links, and icon URLs

Live URL:

```text
https://docs.niwashibase.com/helpers/websites.json
```

Android app-config entry:

```json
"websites": {
  "enabled": true,
  "schema_version": "0.1",
  "version": "0.1",
  "url": "https://api.niwashibase.com/api/v1/ambulance/app-data/websites",
  "fallback_asset": "websites.json"
}
```

Helper shape:

```json
{
  "helper_type": "websites",
  "schema_version": "0.1",
  "version": "0.1",
  "websites": [
    {
      "id": "oracle",
      "enabled": true,
      "title": "Oracle",
      "category": "HMCAS",
      "subtitle": "Leave, salary, and HR services",
      "url": "https://ebusiness.hamad.qa/",
      "icon_url": "https://docs.niwashibase.com/website-icons/web_oracle.png"
    }
  ]
}
```

Rules:

- Keep `id` stable when possible; Android can use it for cache identity.
- `enabled: false` hides a website without deleting it from the helper.
- Missing `enabled` should be treated as enabled by apps.
- `title` and `url` are required for a usable website entry.
- `category`, `subtitle`, and `icon_url` are optional but recommended.
- Host website icons under `https://docs.niwashibase.com/website-icons/`.
- Android accepts website destination URLs only when they are valid `https://` browser links with a host.
- Android accepts `icon_url` only when it is valid HTTPS under `niwashibase.com` or a subdomain such as `docs.niwashibase.com`.
- Do not use `http://`, `file:`, `javascript:`, `content:`, or other non-browser/non-HTTPS schemes.
- Increase helper `version` when website text, URLs, enabled states, order, or icon URLs change if Android needs a forced refresh.
- Keep helper top-level `version` and `schema_version` synchronized with Android app config when publishing Android-facing changes.
- The iOS webapp uses the same helper, displays a searchable alphabetical list, lets users filter by category, and loads icons from `icon_url`.
- The iOS webapp caches the websites helper for up to 7 days and refreshes in the background when the app opens.
- iOS webapp may ignore Android-only fields, but should not break when they are present.

Remote icon guidance:

- Host icons under `https://docs.niwashibase.com/website-icons/`.
- The iOS webapp warms enabled website icons after loading `websites.json` and falls back to a letter tile when an icon is unavailable.
- If an icon image changes but the URL stays the same, increase the websites helper `version` and Android app-config `websites.version` when Android needs a forced refresh.
- Prefer changing the icon filename or adding a URL cache-busting query if CDN/browser cache causes stale icons.

## AS-Call Helper

File:

```text
as_call.json
```

Used for:

- iOS webapp AS-Call list
- Android AS-Call list
- Remote contact names and lightly obfuscated phone references

Live URL:

```text
https://docs.niwashibase.com/helpers/as_call.json
```

Android app-config entry:

```json
"as_call": {
  "enabled": true,
  "schema_version": "0.1",
  "version": "0.1",
  "url": "https://api.niwashibase.com/api/v1/ambulance/app-data/as-call",
  "fallback_asset": "as_call.json"
}
```

Current helper shape:

```json
{
  "helper_type": "as_call",
  "schema_version": "0.1",
  "version": "0.1",
  "contacts": [
    {
      "id": "scheduling",
      "enabled": true,
      "name": "Scheduling",
      "number_ref": "v1:Nzc5NTkwNzE"
    }
  ]
}
```

Legacy shape still accepted by Android during transition:

```json
{
  "addressbook": {
    "Scheduling": 40328200,
    "CC Emergency": 44398833
  }
}
```

Rules:

- Use the `contacts` array for new edits.
- `enabled: false` hides one contact without deleting it.
- Missing `enabled` should be treated as enabled by apps.
- Contact `name` and `number_ref` are required for the obfuscated shape.
- Android also accepts legacy plain `number` for transition only, but new helper updates should use `number_ref`.
- Decoded numbers are accepted only if they contain normal phone characters: digits, `+`, `#`, `*`, spaces, hyphen, and parentheses.
- Increase Android app-config `as_call.version` when names, numbers, enabled states, or order change and Android needs a forced refresh.
- Keep bundled fallback `assets/as_call.json` updated before release builds.

### AS-Call Number Reference Encoding

`number_ref` is light obfuscation only. It prevents phone numbers from being obvious to a casual reader, but it is not encryption and is not a security boundary. The app decodes it before showing and dialing the number.

Encoding scheme `as_call_ref_v1`:

1. Start with the phone number as text.
2. Replace each digit with `(digit + 7) mod 10`.
3. Replace these optional phone characters:

```text
+ -> p
*  -> s
#  -> h
space -> _
-  -> d
(  -> l
)  -> r
```

4. Reverse the transformed string.
5. Base64URL encode it and remove `=` padding.
6. Prefix with `v1:`.

Decode by reversing those steps.

Example Python encoder:

```python
import base64

def encode_as_call_number_ref(number):
    replacements = {
        "+": "p",
        "*": "s",
        "#": "h",
        " ": "_",
        "-": "d",
        "(": "l",
        ")": "r",
    }
    transformed = []
    for char in str(number).strip():
        if char.isdigit():
            transformed.append(str((int(char) + 7) % 10))
        elif char in replacements:
            transformed.append(replacements[char])
        else:
            raise ValueError(f"Unsupported phone character: {char}")
    payload = "".join(transformed)[::-1].encode("utf-8")
    return "v1:" + base64.urlsafe_b64encode(payload).decode("ascii").rstrip("=")
```

## HOS Sites Helper

File:

```text
hos_sites.json
```

Used for:

- Android HOS site lookup after the app is wired to the helper
- Future iOS/webapp HOS site lookup if implemented
- Remote HOS site names, active/closed/restricted state, order, and encoded location references

Live URL:

```text
https://docs.niwashibase.com/helpers/hos_sites.json
```

Android app-config entry:

```json
"hos_sites": {
  "enabled": true,
  "schema_version": "0.1",
  "version": "0.1",
  "url": "https://api.niwashibase.com/api/v1/ambulance/app-data/hos-sites",
  "fallback_asset": "hos_sites.json"
}
```

Helper shape:

```json
{
  "helper_type": "hos_sites",
  "schema_version": "0.1",
  "version": "0.1",
  "sites": [
    {
      "id": "hos_1",
      "enabled": true,
      "status": "active",
      "hos_number": 1,
      "title": "HOS 1",
      "name": "Al Shamal Health Center",
      "details": "Select an app for directions, or enter another HOS",
      "location_ref": "v1:encoded_value_here",
      "display_order": 1
    }
  ]
}
```

Rules:

- Keep `id` stable when possible. Use `hos_1`, `hos_2`, etc.
- `enabled: false` hides or disables the site remotely without deleting it.
- `status` can be `active`, `closed`, `restricted`, or `inactive`.
- Apps should show directions only for enabled active sites.
- `hos_number` is the number users enter in the HOS screen.
- `title` is usually `HOS X`.
- `name` is the location/site display text.
- `details` is optional supporting text.
- `location_ref` is required for enabled active sites.
- `display_order` controls list/order behavior.
- Increase helper `version` when adding, removing, renaming, enabling/disabling, reordering, or changing a site location.
- Keep helper top-level `version` synchronized with Android app-config `hos_sites.version`.
- Change `schema_version` only when the helper contract changes and app code supports that new contract.
- Keep bundled fallback `assets/hos_sites.json` updated before release builds.

### HOS Location Reference Encoding

`location_ref` is light obfuscation only. It prevents plain latitude/longitude from being obvious to a casual reader, but it is not encryption and is not a security boundary. The app must decode it to real coordinates before opening Maps/Waze.

Encoding scheme `hos_ref_v1`:

1. Start with decimal coordinates.
2. Convert to integer E5 values:

```text
lat_e5 = round(lat * 100000)
lng_e5 = round(lng * 100000)
```

3. Apply fixed offsets:

```text
encoded_lat = lat_e5 + 73129
encoded_lng = lng_e5 - 41857
```

4. Join as `encoded_lat:encoded_lng`.
5. Base64URL encode the joined string and remove `=` padding.
6. Prefix with `v1:`.

Decode by reversing those steps:

1. Remove `v1:`.
2. Base64URL decode, adding padding if needed.
3. Split on `:`.
4. `lat_e5 = encoded_lat - 73129`.
5. `lng_e5 = encoded_lng + 41857`.
6. `lat = lat_e5 / 100000`.
7. `lng = lng_e5 / 100000`.

Example Python encoder:

```python
import base64

def encode_location_ref(lat, lng):
    encoded_lat = round(lat * 100000) + 73129
    encoded_lng = round(lng * 100000) - 41857
    payload = f"{encoded_lat}:{encoded_lng}".encode("utf-8")
    return "v1:" + base64.urlsafe_b64encode(payload).decode("ascii").rstrip("=")
```

Example Kotlin decoder:

```kotlin
fun decodeHosLocationRef(ref: String): Pair<Double, Double>? {
    if (!ref.startsWith("v1:")) return null
    val raw = ref.removePrefix("v1:")
    val padded = raw + "=".repeat((4 - raw.length % 4) % 4)
    val decoded = android.util.Base64.decode(
        padded,
        android.util.Base64.URL_SAFE or android.util.Base64.NO_WRAP,
    ).toString(Charsets.UTF_8)
    val parts = decoded.split(":")
    if (parts.size != 2) return null
    val latE5 = parts[0].toLongOrNull()?.minus(73129) ?: return null
    val lngE5 = parts[1].toLongOrNull()?.plus(41857) ?: return null
    return latE5 / 100000.0 to lngE5 / 100000.0
}
```

How to update one HOS site:

1. Edit the matching item in `hos_sites.json`.
2. If disabling, set `enabled` to `false` and set `status` to `closed`, `restricted`, or `inactive`.
3. If changing coordinates, replace `location_ref` with the updated encoded value.
4. Increase helper top-level `version`.
5. Increase matching `hos_sites.version` in Android app config.
6. Validate JSON before uploading.

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
  "url": "https://api.niwashibase.com/api/v1/ambulance/app-data/rsi-checklist",
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
  "url": "https://api.niwashibase.com/api/v1/ambulance/app-data/ccp-pediatric-dosing",
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
  "url": "https://api.niwashibase.com/api/v1/ambulance/app-data/ap-pediatric-dosing",
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

The pediatric helpers now keep Android-used data at the normal medication/section/rule locations and move non-rendered documentation/editor fields into:

```json
"app_unused_metadata": {}
```

Android does not read `app_unused_metadata`. It is safe for editor notes, review history, schema notes, search hints that are not used by Android, and other helper documentation.

Do not move dose, route, indication, warning, UI color, enabled, availability, concentration, or section fields into `app_unused_metadata`; Android needs those in their normal positions.

Top-level Android-used fields:

```text
schema_version
helper_type
version
status
medications
```

Top-level fields currently kept only as `app_unused_metadata`:

```text
source
app_support_contract
input_profiles
dose_formatting
concentration_guides
excluded_tools
review_flags
ui_contract
default_result_format
default_concentration_behavior
section_schema_contract
schema_version_history
medication_lifecycle_contract
editor_guide
```

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

Medication fields used by Android:

```text
id
name
enabled
availability.enabled
display.name
ui.sort_order
ui.background_color
ui.text_color
ui.theme_color
ui.accent_color
warnings
concentration
sections
```

Medication fields currently kept only as `app_unused_metadata`:

```text
content_version
disabled_reason
reference_query
result_format
review_status
search
```

Important: Android Search does not use `search` from pediatric dosing helpers. Android Search uses document/formulary/flowchart/search targets instead.

### Medication Sheet

Rendered medication-level fields:

```text
display.name / name / id
warnings[].title
warnings[].message
warnings[].level
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

Section fields read by Android but not visibly rendered today:

```text
ui.badge
```

Section `ui.badge` is parsed into the model but current Android does not render section badges in AP/CCP medication sheets.

Dose-rule fields currently kept only as dose-rule `app_unused_metadata`:

```text
per
dose_text
selection
```

Do not move any active dose-rule calculation field into `app_unused_metadata`.

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

For every editing task below:

1. Edit the helper JSON.
2. Validate JSON.
3. If publishing remotely, increase the helper top-level `version`.
4. Increase the matching helper `version` in `ambulance_app_config.json`.
5. Upload both files.
6. Test the affected AP/CCP Months and Years flows.

### Update A Dose

1. Find medication by `id`.
2. Find section by `section.id`.
3. Update only the relevant `dose_rule` values.
4. Update notes only if the explanation changed.
5. Do not change `schema_version` unless Android code changes.

Common dose fields:

```text
factor
min_factor
max_factor
amount
min
max
unit
time
display
reason
```

For conditional rules, edit the matching `overrides[].when` condition or the nested `overrides[].rule`. If no override matches, Android uses `default`.

For tiered rules, edit `age_tiers`, `weight_tiers`, `block_when`, or `no_matching_tier`.

### Update An Indication

1. Find medication by `id`.
2. Find section by `section.id`.
3. Edit `indication`.
4. If `indication` is missing, Android can fall back to `display.title`.
5. Bump helper/app-config `version` before publishing.

### Update A Route

Edit the normal route:

```json
"route": "IV/IO"
```

Use these only when the route should change depending on the result:

```json
"route_when_calculated": "IV/IO",
"route_when_not_indicated": "-"
```

### Update Notes

Use:

```text
notes
notes_template
notes_template_when_calculated
notes_when_not_indicated
```

Supported placeholders:

```text
{weight_kg}
{dose_factor}
```

Lines with unsupported `{...}` placeholders are hidden by Android so raw placeholders do not appear to users.

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

### Add A Medication

1. Add a new object to `medications`.
2. Use a stable lowercase `id`, for example `new_medication_name`.
3. Add `name` or `display.name`.
4. Set `enabled: true`.
5. Set `availability.enabled: true`.
6. Add `ui.sort_order` and button colors.
7. Add at least one enabled section with `applies_to`.
8. Add a supported `dose_rule`.
9. Add `concentration` if volume-to-draw support is needed.
10. Validate and test Months and Years.

### Hide A Section Temporarily

```json
"enabled": false
```

### Remove A Section Permanently

Delete the section object from the medication `sections` array.

### Add A Section

Required section fields:

```text
id
enabled
applies_to
indication or display.title
dose_rule
route
```

Example:

```json
{
  "id": "example_section",
  "enabled": true,
  "applies_to": ["ccp_months", "ccp_years"],
  "indication": "Example indication",
  "dose_rule": {
    "type": "weight_based_single",
    "factor": 1,
    "unit": "mg"
  },
  "route": "IV/IO",
  "notes_template": "Based on {dose_factor} mg/kg."
}
```

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

The app remembers the last concentration used by the user per medication. The original package concentration remains a guide only.

To edit a concentration guide:

1. Find medication by `id`.
2. Edit medication-level `concentration.guide`.
3. Keep `amount`, `unit`, and `volume_ml` numeric/valid.
4. Use `unit` as one of:

```text
mcg
mg
g
```

5. Keep `mode: "user_entered"` if Android should show the concentration card and calculate volume.
6. Use `mode: "not_required"` if Android should not show the concentration card.

Android calculates volume only when all are true:

- medication `concentration.mode` is `user_entered`
- user saved a valid concentration in the app
- section dose can be parsed as a single dose or dose range in `mcg`, `mg`, or `g`
- route looks injectable/nebulized and is not an infusion
- section `display.show_volume_calculator` is not `false`

### Update Metadata Only

Use `app_unused_metadata` for notes that Android should not use:

```json
"app_unused_metadata": {
  "review_status": "reviewed",
  "content_version": "2026-05-18",
  "search": {
    "aliases": ["example"]
  }
}
```

Metadata-only changes do not affect Android behavior. If you want installed Android devices to download the changed file anyway, bump helper/app-config `version`. If the change is only for your records and does not need to reach installed apps, a version bump is not clinically necessary.

Do not place active fields inside `app_unused_metadata`. Android will ignore them.

## Pediatric Version Rules

Change helper `version` when:

- dose changes
- indication changes
- route changes
- notes or warning text changes
- concentration guide or concentration mode changes
- medication added/removed
- section added/removed
- medication or section enabled/disabled
- button color/order changes and you need installed devices to refresh
- metadata changes that you still want installed Android apps to download/cache

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
