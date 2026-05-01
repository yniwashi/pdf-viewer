# PDF Viewer with Direct Links

This repository hosts a self-contained PDF.js viewer on GitHub Pages.

It allows you to open PDF files, jump to a specific page, and highlight/search for text directly from the link.

---

## Live Viewer

The viewer is available at:

https://docs.niwashibase.com/viewer/web/

---

## Adding or Updating PDFs

Place PDF files inside the `/docs` folder in this repository.

For the CPG file, keep the current filename:

```txt
/docs/cpg-81w9d1f.pdf
```

When updating to a new CPG version, replace the existing PDF with the new file using the same filename. This keeps all app links working without changing the PDF URL.

Example structure:

```txt
/pdf-viewer
  /viewer
    /web
    /build
    /locale
  /docs
    cpg-81w9d1f.pdf
```

---

## Linking to Files

Use the following URL format:

```txt
https://docs.niwashibase.com/viewer/web/?file=/docs/<filename>.pdf
```

### Current CPG File

Open the CPG at page 15:

```txt
https://docs.niwashibase.com/viewer/web/?file=/docs/cpg-81w9d1f.pdf#page=15
```

Search the CPG for `adenosine`:

```txt
https://docs.niwashibase.com/viewer/web/?file=/docs/cpg-81w9d1f.pdf#search=adenosine
```

Open at page 15 and search for `adenosine`:

```txt
https://docs.niwashibase.com/viewer/web/?file=/docs/cpg-81w9d1f.pdf#page=15&search=adenosine
```

---

## Helper JSON Files

The iOS app uses helper JSON files for CPG navigation, flowcharts, and formulary page links.

Current helper paths:

```txt
/helpers/cpg_index.json
/helpers/flowcharts.json
/helpers/formulary.json
```

When the CPG PDF is updated and page numbers change, update these JSON files at the same time.

The iOS app currently expects:

```js
urlIndex: "https://docs.niwashibase.com/helpers/cpg_index.json"
urlFlowcharts: "https://docs.niwashibase.com/helpers/flowcharts.json"
urlFormulary: "https://docs.niwashibase.com/helpers/formulary.json"
urlPageBase: "https://docs.niwashibase.com/viewer/web/?file=/docs/cpg-81w9d1f.pdf#page="
urlSearchBase: "https://docs.niwashibase.com/viewer/web/?file=/docs/cpg-81w9d1f.pdf#search="
```

---

## Features

- Jump directly to any page using `#page=<number>`.
- Highlight and search for terms using `#search=<keyword>`.
- Clean search bar with navigation.
- Toolbar with First / Last Page buttons.
- Theme-aware design with light and dark mode support.

---

## Deployment

1. Go to **Settings -> Pages** in GitHub.
2. Under **Build and Deployment**, select:
   - Source: `Deploy from branch`
   - Branch: `main` -> `/ (root)`
3. Save and wait for GitHub Pages to build.
4. Your site will be available at:

```txt
https://docs.niwashibase.com/
```

---

## Notes

- Keep filenames stable when app links depend on them.
- Avoid spaces in filenames. Use `-` or `_`.
- Replacing `/docs/cpg-81w9d1f.pdf` with a new PDF keeps existing app links working.
- After replacing the CPG PDF, update the helper JSON files so page links match the new PDF.
- Browser or CDN cache may temporarily show the old PDF after replacement. Test in a private window if needed.
