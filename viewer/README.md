# PDF.js Viewer Guide

This folder contains the PDF.js viewer used by:

- the iOS webapp
- Android external/browser document links
- direct links from helper JSON search results

Live viewer:

```text
https://docs.niwashibase.com/viewer/web/
```

## What This Folder Does

The viewer lets the apps open a hosted PDF and jump directly to:

- a specific page
- a text search
- a page plus a search

The PDFs are stored in:

```text
/docs
```

The viewer is stored in:

```text
/viewer
```

## URL Format

Open a PDF:

```text
https://docs.niwashibase.com/viewer/web/?file=/docs/<filename>.pdf
```

Open a page:

```text
https://docs.niwashibase.com/viewer/web/?file=/docs/<filename>.pdf#page=<page_number>
```

Search text:

```text
https://docs.niwashibase.com/viewer/web/?file=/docs/<filename>.pdf#search=<search_text>
```

Open a page and search:

```text
https://docs.niwashibase.com/viewer/web/?file=/docs/<filename>.pdf#page=<page_number>&search=<search_text>
```

## Examples

Open CPG:

```text
https://docs.niwashibase.com/viewer/web/?file=/docs/cpg-81w9d1f.pdf
```

Open CPG page 15:

```text
https://docs.niwashibase.com/viewer/web/?file=/docs/cpg-81w9d1f.pdf#page=15
```

Search CPG for adenosine:

```text
https://docs.niwashibase.com/viewer/web/?file=/docs/cpg-81w9d1f.pdf#search=adenosine
```

Open SOP page 20:

```text
https://docs.niwashibase.com/viewer/web/?file=/docs/sop-101qq9f2w.pdf#page=20
```

## Current Document Files

```text
/docs/cpg-81w9d1f.pdf
/docs/sop-101qq9f2w.pdf
/docs/cpm-202e9d33q.pdf
/docs/pat-301h6j54r.pdf
```

## App Expectations

The iOS webapp may build viewer links directly from JavaScript config.

The Android app may receive viewer/page URLs from:

- `ambulance_app_config.json`
- document helper index JSON files
- search results
- formulary/flowchart references

Do not change the viewer path unless both apps are updated:

```text
/viewer/web/
```

## Editing Rules

- Avoid editing PDF.js core files unless there is a clear viewer bug.
- Test direct page links after viewer changes.
- Test search links after viewer changes.
- Keep the `file=/docs/...` behavior working.
- Keep `#page=` behavior working.
- Keep `#search=` behavior working.

## Testing Checklist

After changing viewer files:

1. Open the viewer base URL.
2. Open CPG by direct URL.
3. Open CPG at a page number.
4. Search for a known term such as `adenosine`.
5. Test on mobile browser.
6. Test from the iOS webapp if the change affects iOS.
7. Test from Android Search/Guidelines if the change affects Android.
