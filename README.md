# PDF Viewer with Direct Links

This repository hosts a self-contained PDF.js viewer on GitHub Pages. It allows you to open **any PDF file**, jump to a specific page, and even highlight/search for text directly from the link.

## 🔗 Live Viewer

Once deployed with GitHub Pages, your viewer will be accessible at:

https\://<username>.github.io/<repo-name>/pdfjs/web/viewer.html

## 📂 Adding PDFs

1. Place your PDF files in the **root** of the repository (same level as the `pdfjs` folder). Example:

/pdfjs
/my-guidelines.pdf
/protocols.pdf

2. Open them with a link like:

https\://<username>.github.io/<repo-name>/pdfjs/web/viewer.html?file=/my-guidelines.pdf

## 📑 Linking to a Page

Append `#page=<number>` to the URL:

...?file=/my-guidelines.pdf#page=42

## 🔍 Linking with Search

Append `&search=<keyword>` to automatically search when the PDF loads:

...?file=/my-guidelines.pdf#page=15\&search=adenosine

* The search bar will show the keyword.
* All occurrences in the document are highlighted.
* Use ▲ ▼ arrows in the search bar to move between matches.

## 🛠 Toolbar Customizations

* **Previous/Next Page** buttons replaced with **First/Last Page**.
* The **search bar** is always visible, cleaner, and adapts to light/dark mode.
* Highlight color and spacing improved for readability.

## 🚀 Deploying to GitHub Pages

1. Go to your repository **Settings → Pages**.
2. Under **Build and Deployment**, select:

   * **Source**: `Deploy from branch`
   * **Branch**: `main` (or `master`) → `/ (root)`
3. Save and wait for the site to build.
4. Open:

https\://<username>.github.io/<repo-name>/

## ✅ Example Links

* Open *my-guidelines.pdf* on page 10:

https\://<username>.github.io/<repo-name>/pdfjs/web/viewer.html?file=/my-guidelines.pdf#page=10

* Open *protocols.pdf* on page 5 and search for “epinephrine”:

https\://<username>.github.io/<repo-name>/pdfjs/web/viewer.html?file=/protocols.pdf#page=5\&search=epinephrine

## 💡 Notes

* You can rename this repo anytime (e.g., `pdf-viewer`, `guideline-viewer`, `doc-links`).
* If you plan to use a **custom domain**, configure it under **Settings → Pages → Custom Domain**.
* Works best on modern browsers (desktop + mobile).

✨ With this setup, you can share **direct links to specific pages and terms** inside large PDFs — no more telling others to “scroll to page 42.”
