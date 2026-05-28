# Documents Guide


The `docs/` folder stores the PDF documents served from:

```text
https://docs.niwashibase.com/docs/
```

These PDFs are used by the iOS webapp and the Android Ambulance app.

## Current PDFs

```text
/docs/cpg-81w9d1f.pdf
/docs/sop-101qq9f2w.pdf
/docs/cpm-202e9d33q.pdf
/docs/pat-301h6j54r.pdf
```

Live URLs:

```text
https://docs.niwashibase.com/docs/cpg-81w9d1f.pdf
https://docs.niwashibase.com/docs/sop-101qq9f2w.pdf
https://docs.niwashibase.com/docs/cpm-202e9d33q.pdf
https://docs.niwashibase.com/docs/pat-301h6j54r.pdf
```

## Related Helper Indexes

Each document has a matching helper index in `helpers/`:

```text
/helpers/cpg_index.json
/helpers/sop_index.json
/helpers/cpm_index.json
/helpers/pat_index.json
```

Live URLs:

```text
https://docs.niwashibase.com/helpers/cpg_index.json
https://docs.niwashibase.com/helpers/sop_index.json
https://docs.niwashibase.com/helpers/cpm_index.json
https://docs.niwashibase.com/helpers/pat_index.json
```

The index files control search results, document numbers, sections, and page jumps.

Full helper guide:

```text
https://github.com/yniwashi/pdf-viewer/blob/main/helpers/README.md
```

## PDF Viewer Links

The PDF.js viewer is in:

```text
/viewer
```

Open CPG:

```text
https://docs.niwashibase.com/viewer/web/?file=/docs/cpg-81w9d1f.pdf
```

Open CPG page 15:

```text
https://docs.niwashibase.com/viewer/web/?file=/docs/cpg-81w9d1f.pdf#page=15
```

Search CPG:

```text
https://docs.niwashibase.com/viewer/web/?file=/docs/cpg-81w9d1f.pdf#search=adenosine
```

Full viewer guide:

```text
https://github.com/yniwashi/pdf-viewer/blob/main/viewer/README.md
```

## Android App Config

Android version `2.1+` reads document URLs and versions from `ambulance_app_config.json`.

The live app config is served by Cloudflare Worker + R2:

```text
https://api.niwashibase.com/api/v1/ambulance/app-config/production
https://api.niwashibase.com/api/v1/ambulance/app-config/testing
https://api.niwashibase.com/api/v1/ambulance/app-config/backup
```

The app config guide is in:

```text
https://github.com/yniwashi/ambulance-app-dist/blob/main/README.md
```

Document entries look like this:

```json
{
  "type": "CPG",
  "version": "2.5",
  "pdf_url": "https://docs.niwashibase.com/docs/cpg-81w9d1f.pdf",
  "index_url": "https://api.niwashibase.com/api/v1/ambulance/app-data/cpg-index"
}
```

## Android Cache Behavior

The Android app refreshes cached PDFs and indexes when the matching document `version` in app config changes.

If the URL changes but the version does not change, devices may keep using the old cached document/index.

To force Android devices to refresh a document:

1. Upload the new PDF and/or index JSON.
2. Update `pdf_url` or `index_url` in app config if the filename changed.
3. Increase the matching document `version`.
4. Upload the updated app config JSON to R2.

Until iOS is migrated, keep the docs helper copy and R2 app-data copy synchronized when document indexes change.

## Updating A PDF Without Changing Filename

Use this when you want links to keep working.

1. Replace the PDF in `docs/` with the new file.
2. Keep the same filename.
3. Update the matching helper index if page numbers changed.
4. Increase the document `version` in app config.
5. Test direct PDF URL.
6. Test viewer page links.
7. Test Android Guidelines/Search if the update affects Android.
8. Test iOS webapp links if the update affects iOS.

## How-To: Update A Document Index

Use this when the PDF page numbers, search aliases, keywords, or document sections change.

1. Edit the matching helper:

```text
cpg_index.json
sop_index.json
cpm_index.json
pat_index.json
```

2. Validate the JSON.
3. Upload the helper to the docs helper path for iOS:

```text
https://docs.niwashibase.com/helpers/
```

4. Upload the same helper to R2 `app-data/` for Android:

```text
app-data/cpg_index.json
app-data/sop_index.json
app-data/cpm_index.json
app-data/pat_index.json
```

5. Increase the matching document `version` in Android app config.
6. Upload the updated app config to R2.
7. Test:
   - direct helper URL;
   - Android Guidelines/Search;
   - iOS webapp search/page links.

Example:

```text
/docs/cpg-81w9d1f.pdf
```

Keeping the filename stable preserves links such as:

```text
https://docs.niwashibase.com/viewer/web/?file=/docs/cpg-81w9d1f.pdf#page=15
```

## Updating A PDF With A New Filename

Use this when you intentionally want a new stable URL.

1. Add the new PDF file to `docs/`.
2. Update app config `pdf_url`.
3. Update any iOS webapp JavaScript URL constants that point directly to the old file.
4. Update related helper index page numbers.
5. Increase the document `version` in app config.
6. Test old and new viewer links.

Do not delete the old PDF until you are sure no active iOS/Android code still references it.

## Document Index Requirements

Document index files may be top-level arrays or objects with an `items` array.

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

Required for a useful search/page result:

```text
title
page_start
```

The app should ignore items without a usable title or page.

## RSI Checklist

The Android RSI checklist HTML is stored in `helpers/`, not `docs/`, but it is configured as a document-like entry in app config.

Current Android RSI helper:

```text
https://api.niwashibase.com/api/v1/ambulance/app-data/rsi-checklist
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

1. Update `helpers/rsi_checklist_js_android.html`.
2. Keep Android placeholders intact:

```text
__ANDROID_RSI_IMAGE_SRC__
__ANDROID_RSI_AUDIO_BASE__
```

3. Increase RSI `version` in app config.
4. Set `show_image` to `true` or `false`.
5. Upload the Android copy to R2:

```text
app-data/rsi_checklist_js_android.html
```

6. Upload app config to R2.
7. Test the RSI screen in Android.

Android downloads the remote RSI HTML, replaces placeholders with local app asset/resource paths, caches the processed HTML, and displays the cached processed copy.

If remote loading fails, Android uses cached RSI HTML if available, then bundled fallback HTML inside the APK.

## GitHub Pages Notes

- Direct file URLs work.
- Folder URLs may return `404`; this is normal if no `index.html` exists.
- GitHub Pages may cache files for several minutes.
- Use private/incognito browser windows when checking replaced PDFs.
- Avoid spaces in filenames.
- Keep filenames stable when apps depend on them.

## Testing Checklist

After updating a PDF or document index:

1. Open the direct PDF URL.
2. Open the PDF through `/viewer/web/`.
3. Open a known page number.
4. Search for a known word.
5. Confirm the helper index JSON is valid.
6. Confirm app config points to the correct URLs.
7. Increase app config document version if Android should refresh.
8. Test Android Guidelines/Search.
9. Test iOS webapp search/page links.
