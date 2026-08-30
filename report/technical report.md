# Technical Report
## Personal Portfolio Website — Abdulhameed Abdullahi

---

## 1. Visual Design Rationale

The site uses a dark, high-contrast theme (deep navy background, soft blue/teal accents) to read as modern and professional while keeping text legible for long reading sessions. Typographic hierarchy is enforced through a consistent scale (`h1` > `h2` > `h3` > body), and all interactive elements (links, buttons, cards) carry visible `:hover` and `:focus-visible` states to support both mouse and keyboard users. Layout uses **CSS Grid** for card-based content (projects, hobbies, skills) and **Flexbox** for one-dimensional arrangements (navigation, hero section, footer columns), switching to single-column stacks below a 760px breakpoint for a mobile-first responsive experience. Colour contrast between text (`#e8ecf7`) and background (`#0b0f19`) exceeds WCAG AA minimums for body text.

## 2. JSON Data Structure Definitions

`data/data.json` models the site's content as six entities:

- **person** — profile identity, contact details, social links
- **education[]** — id, title, institution, date range, category, description
- **certifications[]** — id, name, status
- **skills{}** — grouped by category, each item with id and name
- **projects[]** — id, title, date, category, description, image_url, url
- **hobbies[]** — id, title, description

Every array item carries a stable `id` so entities can be referenced or cross-linked without ambiguity. This structure mirrors what a database schema or CMS content model would look like, making a future migration straightforward (see Section 4). Because the brief excludes JavaScript, `data.json` is not fetched client-side — it instead serves as the canonical data source that the hand-written HTML and the JSON-LD blocks in each page's `<head>` are kept consistent with.

## 3. HTTP/HTTPS Protocol and MIME Type Overview

When a browser requests a page (e.g., `GET /projects.html`), the web server responds over HTTP/HTTPS with a status line, headers, and a body. The **`Content-Type` header** tells the browser how to interpret that body:

| File | MIME Type | Browser Behaviour |
|---|---|---|
| `.html` | `text/html` | Parsed into the DOM as markup |
| `.css` | `text/css` | Parsed as a stylesheet and applied to matching elements |
| `.json` | `application/json` | Treated as raw structured data (not rendered) |
| `.jpg/.png` | `image/jpeg`, `image/png` | Decoded and painted as a raster image |

HTTPS wraps this exchange in TLS, encrypting the request/response so the HTML, CSS, and JSON payloads can't be read or altered in transit — important even for a static, no-login site, since GitHub Pages and most hosts now serve HTTPS by default. Because every asset here is static (no server-side scripting), the server's job is simple: match the request path to a file and return it with the correct MIME type. Browsers rely on that header — not the file extension — to decide how to render content, which is why serving `data.json` with the wrong `Content-Type` (e.g., `text/plain`) could cause some tools to mishandle it even though the file itself is valid JSON.

## 4. CMS Selection Justification (Manual Architecture vs. CMS)

This project was deliberately built as hand-coded XHTML/HTML5, CSS, and JSON — no WordPress, no static site generator, no client-side JavaScript. That decision was appropriate here, but it comes with trade-offs worth stating plainly.

**Why manual architecture fits this project:** a personal portfolio is a small, single-owner site with infrequent content changes — a new project gets added every few months, not every day. A CMS's core value proposition is separating content from presentation so *non-technical editors* can update pages without touching code. For a solo developer who is the *only* editor and *is* technically fluent, that separation is overhead rather than benefit. Hand-coded pages also mean zero framework dependencies, no plugin security surface, no database to back up, and total control over every byte shipped to the browser — which keeps the site fast and trivially hostable on free static hosting (GitHub Pages). The JSON data file gives a lightweight taste of content/presentation separation without the weight of a full CMS stack.

**When migration to a CMS would be justified:** the calculus flips once any of the following becomes true. First, **team size** — if multiple people (e.g., a hiring collaborator, a co-founder, or a design partner) need to edit content independently, a CMS's editorial workflow (drafts, roles, permissions) becomes valuable; hand-editing HTML doesn't scale past one comfortable editor. Second, **update frequency** — a portfolio that grows into a blog or a project log posting weekly benefits enormously from a CMS's templating (new posts auto-generate listing pages, RSS feeds, pagination) rather than manually wiring new `<article>` blocks and updating every listing page by hand. Third, **non-technical editor access** — if the site owner wants a manager, client, or family member to update content (e.g., a testimonials section or news updates) without learning HTML, a CMS's WYSIWYG editor is the entire point. Fourth, **scale of content relationships** — once the JSON file's projects, tags, and categories need querying, filtering, or search (e.g., "show all Python projects from 2025"), a real database-backed CMS or headless CMS with an API becomes more maintainable than hand-maintained JSON.

In short: the manual approach optimizes for a single technical owner with a slow-changing, small site — exactly this project's profile. A CMS optimizes for collaboration, content velocity, and non-technical access, which would justify migration if the portfolio evolves into a team-run or frequently-updated platform.

---

## 5. Directory Structure

```
/
├── index.html
├── about.html
├── education.html
├── skills.html
├── projects.html
├── hobbies.html
├── cv.html
├── contact.html
├── assets/
│   ├── css/
│   │   └── style.css
│   └── images/
│       └── (Profile Picture, project screenshots — referenced in data.json)
├── data/
│   └── data.json
└── report/
    └── technical-report.md (this document)
```

## 6. Deployment Note

All files are static and can be deployed directly to GitHub Pages, Netlify, or any standard web host — no build step, server-side runtime, or database is required. The `contact.html` form posts via standard HTML `<form method="POST">` to a static form backend (e.g., Formspree); the placeholder endpoint in that file must be replaced with a real form ID before going live.
