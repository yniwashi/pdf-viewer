# PDF Viewer with Direct Links

This repository hosts a self-contained PDF.js viewer on GitHub Pages.  
It allows you to open **any PDF file**, jump to a specific page, and highlight/search for text directly from the link.

---

## 🔗 Live Viewer
The viewer is available at:
https://yniwashi.github.io/pdf-viewer/viewer/web/

---

## 📂 Adding PDFs
- Place your PDF files inside the **/docs** folder in this repository.  
- Example structure:
```

/pdf-viewer
/viewer
/web
/build
/locale
/docs
cpg.pdf
sop.pdf
checklist.pdf

```

---

## 📑 Linking to Files
Use the following URL format:
```

[https://yniwashi.github.io/pdf-viewer/viewer/web/?file=/pdf-viewer/docs/](https://yniwashi.github.io/pdf-viewer/viewer/web/?file=/pdf-viewer/docs/)<filename>.pdf

```

### Current Files
- **CPG (Clinical Practice Guidelines)**  
  - Open at page 15:  
    https://yniwashi.github.io/pdf-viewer/viewer/web/?file=/pdf-viewer/docs/cpg.pdf#page=15  
  - Open at page 15 and search for *adenosine*:  
    https://yniwashi.github.io/pdf-viewer/viewer/web/?file=/pdf-viewer/docs/cpg.pdf#page=15&search=adenosine  

(Add new files under `/docs` and follow the same pattern above.)

---

## 🛠 Features
- Jump directly to any page using `#page=<number>`.
- Highlight and search for terms using `&search=<keyword>`.
- Clean search bar always visible with ▲ ▼ navigation.
- Toolbar improved with **First / Last Page** buttons.
- Theme-aware design (light and dark mode support).

---

## 🚀 Deployment
1. Go to **Settings → Pages** in GitHub.  
2. Under **Build and Deployment**, select:
   - Source: `Deploy from branch`
   - Branch: `main` → `/ (root)`  
3. Save and wait for GitHub Pages to build.  
4. Your site will be available at:
```

[https://yniwashi.github.io/pdf-viewer/](https://yniwashi.github.io/pdf-viewer/)

```

---

## 💡 Notes
- Filenames should avoid spaces. Use `-` or `_` instead.  
- You can update this README as an **index** for your most-used files.  
- Works best on modern browsers (desktop and mobile).  

✨ With this setup, you can share direct links to **specific pages and terms** inside large PDFs — no more telling others to “scroll down to page X.”

