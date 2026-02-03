# Portfolio Section Files — Mapping & Include Instructions

Each section from the main portfolio page is stored as a separate HTML fragment in the `sections/` folder. Use this mapping and the methods below to reassemble or maintain the page.

---

## Section → File Mapping

| Section   | File             | Source (index.html) | Notes |
|----------|------------------|----------------------|--------|
| **Hero** | `hero.html`      | `<section id="home">` | Hero with avatar, title, “Read more”, location, CTA buttons. |
| **About**| `about.html`     | `<section id="about">` | About text + languages block. |
| **Skills** | `skills.html`  | `<section id="skills">` | 3D skill cards (languages, frameworks, architecture, Firebase, state, realtime, payment, social). |
| **Experience** | `experience.html` | `<section id="experience">` | Accordion: Code Pro, Code Exal, InoSwift. |
| **Education** | `education.html` | `<section id="education">` | Degree card + Flutter & software courses card. |
| **Projects** | `projects.html` | `<section id="projects">` + Project modal | Projects grid (Medical, Educational, E-commerce), “Scalable” block, and **Project Details Modal** (`#projectModal`). |
| **Contact** | `contact.html`   | Extracted from **contact.html** page | Contact section (form + contact info). Not present on index.html; nav links to full contact page. Use this fragment if you add a contact section to the main page. |
| **Footer** | `footer.html`   | `<footer>` | Copyright and “All rights reserved.” |

**Order on the main page:** Hero → About → Skills → Experience → Education → Projects → (optional Contact) → Footer.

Between sections, the original page uses:

```html
<div class="section-divider"></div>
```

Add this between each pair of sections when you compose the main page (e.g. after hero, after about, and so on), except before the footer if you prefer no divider there.

---

## How to Include Sections in the Main Page

You have three practical options. The section files contain **only HTML** (no `<!DOCTYPE>`, `<html>`, or `<head>`); they are meant to be included into a single main document.

### Option 1: JavaScript fetch (no server includes)

Place a container in the main page where the sections should appear, then load each section with `fetch` and insert its HTML. Works with `file://` only if the browser allows it; normally use a local server (e.g. Live Server, `npx serve`, or your host).

**Main page (e.g. index.html) — placeholder:**

```html
<body>
  <!-- Nav/header stays here -->
  <main id="main-content">
    <!-- Sections will be injected here -->
  </main>
  <!-- Scripts stay here -->
</body>
```

**JavaScript (e.g. before `</body>` or in your main script):**

```javascript
const sectionOrder = [
  'sections/hero.html',
  'sections/about.html',
  'sections/skills.html',
  'sections/experience.html',
  'sections/education.html',
  'sections/projects.html',
  // 'sections/contact.html',  // optional
  'sections/footer.html'
];

const divider = '<div class="section-divider"></div>';
const main = document.getElementById('main-content');

async function loadSections() {
  for (let i = 0; i < sectionOrder.length; i++) {
    const response = await fetch(sectionOrder[i]);
    const html = await response.text();
    main.insertAdjacentHTML('beforeend', html);
    if (i < sectionOrder.length - 1) {
      main.insertAdjacentHTML('beforeend', divider);
    }
  }
}

loadSections();
```

- **Important:** Run the page over **HTTP** (local or remote server).  
- Your existing scripts (translations, theme, accordion, project modal, etc.) must stay on the main page and run **after** the sections are injected, or they won’t see the new DOM. You can call them from a `.then(() => { /* init */ })` after `loadSections()` or use `DOMContentLoaded` and a small delay if needed.

### Option 2: Server-side includes (SSI)

If your host supports SSI (e.g. Apache with `Include`), build the main page from fragments:

```html
<!--#include virtual="sections/hero.html" -->
<div class="section-divider"></div>
<!--#include virtual="sections/about.html" -->
<div class="section-divider"></div>
<!-- ... repeat for each section ... -->
<!--#include virtual="sections/footer.html" -->
```

The server replaces each `<!--#include -->` with the file contents. Paths may need to be relative to the document root or the current file depending on server config.

### Option 3: Build step / template engine

Use a static site generator (Eleventy, Hugo, etc.) or a template engine (EJS, Pug, Nunjucks, etc.) that supports partials. For example:

**EJS:**

```html
<%- include('sections/hero') %>
<div class="section-divider"></div>
<%- include('sections/about') %>
<!-- ... -->
<%- include('sections/footer') %>
```

Then run your build so the main `index.html` is generated with all sections in place.

---

## Paths and Scripts

- **Images:** Section HTML uses paths like `image/p11.jpg`, `image/my.jpg`, etc. Keep the same folder structure relative to the **main page** (e.g. `index.html` in project root, `image/` next to it). If you move the main page, adjust paths or use a base tag.
- **Contact form:** `sections/contact.html` uses `onsubmit="handleContactForm(event)"`. If you include this section on the main page, the main page must define `handleContactForm` (and any EmailJS or other script the contact page uses). The full contact page at `contact.html` already has that script.
- **IDs and scripts:** The main page scripts expect specific IDs (e.g. `#projectModal`, `#projectModalClose`, `#readMoreBtn`, `#readMoreContent`, accordion `data-accordion-trigger`). These are unchanged in the section files; keep your existing script tags on the main page and ensure they run after the section HTML is in the DOM when using fetch.

---

## Summary

- **Mapping:** Hero → `hero.html`, About → `about.html`, Skills → `skills.html`, Experience → `experience.html`, Education → `education.html`, Projects (+ modal) → `projects.html`, Contact → `contact.html`, Footer → `footer.html`.
- **Order:** Use the table order and insert `<div class="section-divider"></div>` between sections if you want the original look.
- **Including:** Use **JavaScript fetch** (with a local server), **SSI**, or a **template/build** step; ensure images and scripts are correct and that scripts run after the sections are in the page.
