# CLAUDE.md — jeshuatromp.com

Personal academic website for **Dr. Jeshua Tromp**, postdoctoral researcher in cognitive neuroscience at the Donders Institute (Radboudumc, Nijmegen). Built with Quarto and hosted on GitHub Pages.

---

## Stack

| Layer | Tool |
|---|---|
| Site generator | [Quarto](https://quarto.org) |
| Theme | Bootstrap / Cosmo |
| Fonts | Open Sans (Google Fonts) |
| Icons | Font Awesome 6, Academicons |
| Contact form | Formspree (`mjgjwwbp`) |
| Hosting | GitHub Pages → `docs/` folder |
| Domain | `jeshuatromp.com` (CNAME file) |

---

## File structure

```
/                        ← Quarto source lives at the repo root
├── _quarto.yml          ← Site config: theme, navbar, bibliography, output-dir
├── styles.css           ← All custom CSS
├── _sidebar.html        ← Sidebar HTML injected via include-before-body; JS repositions it
├── header.html          ← External CSS links (fonts, icons) in <head>
├── scripts.html         ← Custom JS injected via include-after-body
├── banner.html          ← (unused standalone; banner is in _sidebar.html)
├── publications.bib     ← BibTeX reference list
├── apa-cv.csl           ← Citation Style Language file (APA-CV variant)
├── images/              ← Source images (profile photo, banner, logo)
├── docs/                ← Rendered output — GitHub Pages serves this
│   ├── CNAME
│   ├── *.html           ← Rendered pages
│   ├── images/
│   └── site_libs/       ← Quarto-managed JS/CSS libs (never edit manually)
└── CNAME                ← Custom domain declaration
```

### Pages (`.qmd` files)

| File | Nav label | Notes |
|---|---|---|
| `index.qmd` | Home | Welcome blurb; suppresses auto-generated title |
| `publications.qmd` | Publications | Renders `publications.bib` via `nocite: @*` |
| `projects.qmd` | Projects | Current research projects |
| `hersenvoer.qmd` | Hersenvoer (NL) | Dutch-language science blog |
| `contact.qmd` | Contact | Formspree contact form |
| `resume.qmd` | — | CV (not in navbar) |
| `about.qmd` | — | Stub, not in navbar |
| `acknowledgements.qmd` | — | Linked in page footer |

---

## Layout architecture

Quarto's default layout is overridden with a custom two-column design. This is the most non-obvious part of the codebase.

### How it works

1. `_sidebar.html` is injected via `include-before-body`. This puts the `.site-banner` and `.profile-sidebar` elements inside `<main>` initially — **wrong position**.
2. `scripts.html` runs JS on `DOMContentLoaded` to **move** them:
   - `.site-banner` → direct child of `<body>` after the navbar (so no parent constrains its width).
   - `.profile-sidebar` + `#quarto-content` → wrapped in a new `.two-col-wrapper` div.
3. Result: `navbar → full-width banner → [sidebar | page content]`.

### Key CSS variables

```css
--nav-height: 60px;    /* updated by JS after measuring the real navbar */
--photo-overlap: 70px; /* how far the profile photo overlaps the bottom of the banner */
```

The sticky sidebar uses `top: var(--nav-height)`, which JS recalculates on load and resize.

### Responsive breakpoint: `860px`

Below 860px the layout switches from two-column (sidebar left, content right) to stacked:
- Sidebar becomes a horizontal row: photo + name on the left, title/affiliation/icons on the right.
- `.profile-affiliation` hides; `.profile-affiliation-short` shows.
- `.profile-divider` hides.
- Banner shrinks from 220px to 130px tall.
- Navbar padding resets from `12rem` to `1rem`.
- Body text is justified on mobile.

---

## Build & deploy workflow

```bash
# Render the site (outputs to docs/)
quarto render

# Preview locally with live reload
quarto preview

# Deploy: commit docs/ and push — GitHub Pages serves automatically
git add docs/
git commit -m "Render: ..."
git push
```

- `output-dir: docs` is set in `_quarto.yml`.
- Never manually edit anything inside `docs/site_libs/`.
- The `CNAME` file at the root is copied to `docs/` on each render (declared under `project.resources`).

---

## Publications

Publications are managed via BibTeX:
- Add entries to `publications.bib`.
- `publications.qmd` uses `nocite: @*` to render all entries automatically.
- Citation style is `apa-cv.csl` (CV-style APA, no "In:" prefix etc.).
- `scripts.html` has a JS function (`boldOwnName`) that post-processes the rendered `#refs` div to bold **Tromp, J.** in every entry.

---

## External services

- **Formspree** endpoint: `https://formspree.io/f/mjgjwwbp` — handles contact form submissions, no backend required.
- **Google Scholar**: `https://scholar.google.com/citations?user=vJ3tg9UAAAAJ`
- **ORCID**: `0000-0001-9782-6076`
- **Substack** (Hersenvoer): `https://hersenvoer.substack.com`

---

## Brand / design tokens

| Token | Value |
|---|---|
| Primary color | `#0096AB` (teal) |
| Hover color | `#007a8a` |
| Body text | `#444444` |
| Sidebar border | `#e0e0e0` |
| Font | Open Sans 400/600/700 |
| Photo style | Square, 4px white border, rounded corners (4px), shadow |
