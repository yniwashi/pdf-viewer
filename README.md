# Niwashi Docs Host / PDF Viewer

This repository hosts the static files used by the iOS webapp and the Android Ambulance app.

Live host:

```text
https://docs.niwashibase.com
```

Repository:

```text
https://github.com/yniwashi/pdf-viewer
```

## Repository Structure

```text
pdf-viewer/
  docs/
  helpers/
  viewer/
  README.md
  CNAME
  robots.txt
  .nojekyll
```

## What Each Folder Does

```text
docs/
```

Stores PDF files such as CPG, SOP, CPM, and PAT.

Folder guide:

```text
https://github.com/yniwashi/pdf-viewer/blob/main/docs/README.md
```

```text
helpers/
```

Stores JSON and HTML helper files such as document indexes, flowcharts, formulary, websites, RSI checklist HTML, and pediatric dosing helpers.

Folder guide:

```text
https://github.com/yniwashi/pdf-viewer/blob/main/helpers/README.md
```

```text
viewer/
```

Stores the bundled PDF.js viewer used for direct PDF links, page jumps, and text search.

Folder guide:

```text
https://github.com/yniwashi/pdf-viewer/blob/main/viewer/README.md
```

## Live Viewer

The PDF.js viewer is available at:

```text
https://docs.niwashibase.com/viewer/web/
```

Open a PDF:

```text
https://docs.niwashibase.com/viewer/web/?file=/docs/cpg-81w9d1f.pdf
```

Open a page:

```text
https://docs.niwashibase.com/viewer/web/?file=/docs/cpg-81w9d1f.pdf#page=15
```

Search inside a PDF:

```text
https://docs.niwashibase.com/viewer/web/?file=/docs/cpg-81w9d1f.pdf#search=adenosine
```

## Used By

### iOS Webapp

The iOS webapp uses this repository directly for document viewing and helper JSON files.

Current iOS-style helper and viewer examples:

```js
urlIndex: "https://docs.niwashibase.com/helpers/cpg_index.json"
urlSopIndex: "https://docs.niwashibase.com/helpers/sop_index.json"
urlCpmIndex: "https://docs.niwashibase.com/helpers/cpm_index.json"
urlFlowcharts: "https://docs.niwashibase.com/helpers/flowcharts.json"
urlFormulary: "https://docs.niwashibase.com/helpers/formulary.json"
urlWebsites: "https://docs.niwashibase.com/helpers/websites.json"
urlPageBase: "https://docs.niwashibase.com/viewer/web/?file=/docs/cpg-81w9d1f.pdf#page="
urlSopPageBase: "https://docs.niwashibase.com/viewer/web/?file=/docs/sop-101qq9f2w.pdf#page="
urlCpmPageBase: "https://docs.niwashibase.com/viewer/web/?file=/docs/cpm-202e9d33q.pdf#page="
urlSearchBase: "https://docs.niwashibase.com/viewer/web/?file=/docs/cpg-81w9d1f.pdf#search="
```

The iOS webapp preloads and locally caches shared helper data for up to 7 days, then refreshes in the background when the app opens. Do not remove or rename these files unless the iOS webapp has been updated.

### Android App

The Android app does not hardcode every docs URL in the UI. Version `2.1+` reads most remote document/helper URLs from:

```text
ambulance_app_config.json
```

The app config is hosted separately in a `yazan414` Gist:

```text
https://gist.github.com/yazan414/2ed2d30193b3dedffcf789981ad14c0e
```

The Android app config points to files hosted here, for example:

```text
https://docs.niwashibase.com/docs/cpg-81w9d1f.pdf
https://docs.niwashibase.com/helpers/cpg_index.json
https://docs.niwashibase.com/helpers/rsi_checklist_js_android.html
https://docs.niwashibase.com/helpers/ccp_pediatric_dosing_helper.json
https://docs.niwashibase.com/helpers/ap_pediatric_dosing_helper.json
```

Full Android app config guide:

```text
https://github.com/yniwashi/ambulance-app-dist/blob/main/README.md
```

## Current Important Live Paths

PDFs:

```text
https://docs.niwashibase.com/docs/cpg-81w9d1f.pdf
https://docs.niwashibase.com/docs/sop-101qq9f2w.pdf
https://docs.niwashibase.com/docs/cpm-202e9d33q.pdf
https://docs.niwashibase.com/docs/pat-301h6j54r.pdf
```

Document indexes:

```text
https://docs.niwashibase.com/helpers/cpg_index.json
https://docs.niwashibase.com/helpers/sop_index.json
https://docs.niwashibase.com/helpers/cpm_index.json
https://docs.niwashibase.com/helpers/pat_index.json
```

Shared helpers:

```text
https://docs.niwashibase.com/helpers/flowcharts.json
https://docs.niwashibase.com/helpers/formulary.json
https://docs.niwashibase.com/helpers/websites.json
```

Website icons:

```text
https://docs.niwashibase.com/website-icons/web_oracle.png
```

Android-specific helpers:

```text
https://docs.niwashibase.com/helpers/rsi_checklist_js_android.html
https://docs.niwashibase.com/helpers/ccp_pediatric_dosing_helper.json
https://docs.niwashibase.com/helpers/ap_pediatric_dosing_helper.json
```

## Deployment

GitHub Pages should be configured as:

```text
Source: Deploy from branch
Branch: main
Folder: / (root)
```

The custom domain is controlled by:

```text
CNAME
```

The current domain should be:

```text
docs.niwashibase.com
```

## Rules

- Keep filenames stable when app links depend on them.
- Avoid spaces in filenames. Use `-` or `_`.
- Use exact direct URLs. Folder URLs may return `404` because GitHub Pages does not list directories.
- After replacing PDFs, update matching index JSON files if page numbers changed.
- Keep `helpers/websites.json` and `website-icons/` available for the iOS Websites tool.
- For Android, increase the matching version in `ambulance_app_config.json` when a document/helper should refresh.
- Browser/CDN cache may temporarily show old files. Test in a private window or wait for GitHub Pages cache to expire.
