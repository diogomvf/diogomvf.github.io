# CLAUDE.md

Guidance for Claude Code (claude.ai/code) when working in this repository.

## What this is

Personal blog / portfolio site for Diogo Valadas Fernandes, published as a GitHub
Pages **user site** at `https://diogomvf.github.io` from the repo
`diogomvf/diogomvf.github.io`.

It is a hand-written static site: **two standalone HTML files, no build step, no
dependencies, no framework, no package manager.** All CSS lives in a `<style>`
block in each page's `<head>`, and all JavaScript in a `<script>` block before
`</body>`. There are no external assets — no images, fonts, or CDN links.

## Repository layout

```
index.html       Landing page: header, nav, #sobre, #blog, #habilidades, #contato, footer
contactos.html   Contact page: contact details + client-side contact form
.gitattributes   LF normalization (* text=auto)
```

That is the entire tracked tree. Do not introduce a build tool, bundler,
`package.json`, or static-site generator unless explicitly asked — deployment
depends on the HTML being served verbatim from the repo root.

## Deploy

GitHub Pages serves the **root of `main`**. Merging to `main` is the deploy;
there is no CI workflow, no `_config.yml`, and no `CNAME`. Anything pushed to
`main` is live within a minute or two.

## Local preview

No tooling to install. Either open the file directly:

```bash
xdg-open index.html      # or: open index.html
```

or serve it, which is preferable because it makes relative links between the two
pages behave the same way they do on Pages:

```bash
python3 -m http.server 8000   # then visit http://localhost:8000
```

There are no tests, linters, or formatters configured. Verification is visual:
load both pages, check the nav links in both directions, and resize below 768px
to exercise the responsive breakpoint.

## Conventions

**Language.** All user-facing content is Portuguese. `index.html` is written in
European Portuguese (*contactos*, *partilha*, *ténis*); some older placeholder
copy in `contactos.html` is Brazilian Portuguese (*compartilhe*, *você*). Prefer
pt-PT for new copy, matching `index.html`. Note that both files declare
`lang="pt-BR"` in the `<html>` tag — inaccurate for the current content, but
leave it alone unless asked to fix it, and change both files together if so.

**Commit messages** in the existing history are short and in Portuguese
(`contactos`, `mudança de nome`, `Atualizar blog com página de contactos`).
Match that style.

**Formatting.** 4-space indentation in HTML, CSS, and JS. Section boundaries in
markup and stylesheets are marked with comments (`<!-- Navegação -->`,
`/* Responsivo */`). Keep both.

## The one real gotcha: duplicated CSS

Each page carries its own full copy of the stylesheet, and a large prefix of the
two is **byte-identical**:

- the `*` reset and the `:root` design tokens
- `body`, `header`, `@keyframes slideDown`, `.header-content`, `h1`,
  `.subtitle`, `.tagline`
- `nav`, `nav ul`, `nav a`, `nav a:hover`
- `.container`, `section`, `@keyframes fadeIn`, `section h2`
- `footer`, `footer p`, the `@media (max-width: 768px)` block, `html { scroll-behavior }`

`contactos.html` additionally defines `nav a.active`, which `index.html` lacks.

**Any change to a shared rule must be applied to both `index.html` and
`contactos.html`.** Editing only one silently desynchronizes the pages. Between
the shared prefix and the shared footer/responsive tail, each page defines its
own rules (`.about-content` / `.posts-grid` / `.skills-container` in `index.html`;
`.contact-grid` / `.contact-form` / `.success-message` in `contactos.html`) —
those are page-local and safe to edit in place.

### Design tokens

Both files define the same palette. Use these variables rather than raw hex:

```css
--primary: #2c3e50;   /* nav, footer, headings, skill cards */
--accent:  #e74c3c;   /* links, borders, hover states */
--light:   #ecf0f1;   /* declared but currently unused */
--text:    #34495e;   /* body copy */
--border:  #bdc3c7;   /* declared but currently unused */
```

Body font is `'Georgia', 'Garamond', serif`. Content column is `max-width: 1000px`.
The single breakpoint is `768px`, where two-column grids collapse to one.

## Navigation model

`index.html` is a single-page scroll: its nav links are fragments (`#sobre`,
`#blog`, `#habilidades`) plus a real link to `contactos.html`. `contactos.html`
links back with absolute-ish paths (`index.html#sobre`, …) and marks its own
entry with `class="active"`.

**When adding a section to `index.html`, update the nav list in *both* files** —
`contactos.html` mirrors the full menu.

## JavaScript

Two small vanilla scripts, no libraries:

- **Both pages:** an `IntersectionObserver` that replays the `fadeIn` animation
  on each `<section>` as it scrolls into view.
- **`index.html`:** click handlers on nav links that recolor the active item
  inline (`element.style.color`), independent of the `.active` class used on
  `contactos.html`.
- **`contactos.html`:** the contact form is intercepted with `preventDefault()`,
  logged to the console, and answered with a fake success banner
  (`#successMessage`, hidden again after 5s). **Nothing is sent anywhere.** If
  asked to make the form actually work, it needs a third-party form endpoint
  (Formspree, Netlify Forms, etc.) or a backend — GitHub Pages serves static
  files only and cannot process a POST.

## Known inconsistencies in the current content

`index.html` has been personalized; `contactos.html` has not. It still carries
template placeholders that contradict the live site:

| `contactos.html` (placeholder) | `index.html` (real) |
| --- | --- |
| `<h1>Seu Nome Aqui</h1>` | `Diogo Valadas Fernandes` |
| `Escritor • Pensador • Criador` | `Estudante Universitário • Tenista` |
| `seu.email@exemplo.com` | `diogo.v.fernandes@icloud.com` |
| `+55 (11) 99999-9999` (Brazil) | `+351 931 490 561` (Portugal) |
| `© 2024 Seu Nome` | `© 2026 Diogo V. Fernandes` |

Also placeholder, in `index.html`: the three blog posts under `#blog` are lorem
copy dated May 2024 with `href="#"` "Ler mais" links, and every `.social-btn`
on both pages points at `href="#"`.

Treat these as unfinished content rather than bugs — fix them when the task calls
for it, and keep the two pages consistent when you do. The `tel:` href in
`index.html` contains spaces (`tel:+351 931 490 561`); use `tel:+351931490561`
if you touch it.
