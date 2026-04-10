# create-theme

Argumentumok: `$ARGUMENTS` (formátum: `design-A light` vagy `design-B dark` stb.)

Olvasd el a `brief.md` és a `CONTEXT.md` tartalmát. Olvasd el a `preview.html`-t is, hogy pontosan tudd milyen design tokeneket (színek, fontok) rendeltél az adott variánshoz.

Ha az argumentum hiányzik vagy értelmezhetetlen, kérj pontosítást: „Melyik designt generáljam? Formátum: design-A light / design-A dark / design-B light / stb."

---

## Feladatod

A kiválasztott design és színváltozat alapján generáld le a teljes WordPress FSE theme-et, majd csomagold zip fájlba.

---

## Kötelező fájlstruktúra

```
{theme-slug}/
├── style.css
├── theme.json
├── functions.php
├── templates/
│   ├── index.html
│   ├── single.html
│   └── page.html
└── parts/
    ├── header.html
    └── footer.html
```

A zip fájl neve: `output/{theme-slug}.zip`
A zip-en belül a gyökér könyvtár neve: `{theme-slug}/`

---

## style.css

Kötelező WordPress theme header komment, minden mező kitöltve:

```css
/*
Theme Name: {brief.md-ben megadott ügyfélnév}
Theme URI:
Author: {agency neve – ha nincs a brief-ben, hagyd üresen}
Author URI:
Description: Custom FSE theme generated for {ügyfélnév}
Version: 1.0.0
Requires at least: 6.4
Tested up to: 6.7
Requires PHP: 8.0
License: All Rights Reserved
Text Domain: {theme-slug}
*/
```

Tényleges CSS szabályok NEM kerülnek ide – minden stílus a `theme.json`-ban van.

---

## theme.json

A design **single source of truth**. Kötelező szekciók:

```json
{
  "$schema": "https://schemas.wp.org/trunk/theme.json",
  "version": 3,
  "settings": {
    "color": {
      "custom": true,
      "customDuotone": false,
      "customGradient": false,
      "defaultDuotone": false,
      "defaultGradients": false,
      "defaultPalette": false,
      "palette": [
        { "slug": "primary",    "color": "...", "name": "Primary" },
        { "slug": "secondary",  "color": "...", "name": "Secondary" },
        { "slug": "accent",     "color": "...", "name": "Accent" },
        { "slug": "background", "color": "...", "name": "Background" },
        { "slug": "surface",    "color": "...", "name": "Surface" },
        { "slug": "text",       "color": "...", "name": "Text" },
        { "slug": "text-muted", "color": "...", "name": "Text Muted" }
      ]
    },
    "typography": {
      "customFontSize": true,
      "defaultFontSizes": false,
      "fontFamilies": [
        {
          "slug": "heading",
          "name": "Heading",
          "fontFamily": "...",
          "fontFace": [{ "fontFamily": "...", "fontStyle": "normal", "fontWeight": "400 700", "src": ["..."] }]
        },
        {
          "slug": "body",
          "name": "Body",
          "fontFamily": "...",
          "fontFace": [{ "fontFamily": "...", "fontStyle": "normal", "fontWeight": "400 600", "src": ["..."] }]
        }
      ],
      "fontSizes": [
        { "slug": "small",   "size": "0.875rem", "name": "Small" },
        { "slug": "medium",  "size": "1rem",     "name": "Medium" },
        { "slug": "large",   "size": "1.25rem",  "name": "Large" },
        { "slug": "x-large", "size": "1.75rem",  "name": "X Large" },
        { "slug": "huge",    "size": "2.5rem",   "name": "Huge" }
      ]
    },
    "spacing": {
      "spacingScale": { "steps": 7, "mediumStep": 1.5, "type": "fluid", "unit": "rem", "operator": "*" },
      "units": ["px", "rem", "%", "vw"]
    },
    "layout": {
      "contentSize": "760px",
      "wideSize": "1200px"
    }
  },
  "styles": {
    "color": {
      "background": "var(--wp--preset--color--background)",
      "text": "var(--wp--preset--color--text)"
    },
    "typography": {
      "fontFamily": "var(--wp--preset--font-family--body)",
      "fontSize": "var(--wp--preset--font-size--medium)",
      "lineHeight": "1.6"
    },
    "elements": {
      "link": {
        "color": { "text": "var(--wp--preset--color--primary)" },
        ":hover": { "color": { "text": "var(--wp--preset--color--accent)" } }
      },
      "h1": { "typography": { "fontFamily": "var(--wp--preset--font-family--heading)", "fontWeight": "700", "lineHeight": "1.2" } },
      "h2": { "typography": { "fontFamily": "var(--wp--preset--font-family--heading)", "fontWeight": "600", "lineHeight": "1.3" } },
      "h3": { "typography": { "fontFamily": "var(--wp--preset--font-family--heading)", "fontWeight": "600", "lineHeight": "1.4" } },
      "button": {
        "color": { "background": "var(--wp--preset--color--primary)", "text": "var(--wp--preset--color--background)" },
        "border": { "radius": "4px" },
        ":hover": { "color": { "background": "var(--wp--preset--color--accent)" } }
      }
    }
  },
  "templateParts": [
    { "name": "header", "title": "Header", "area": "header" },
    { "name": "footer", "title": "Footer", "area": "footer" }
  ]
}
```

**Fontosnál fontosabb:** a `theme.json`-ba a kiválasztott design és színváltozat token értékeit írd be – ne generic placeholder értékeket.

---

## functions.php

Minimális, csak theme support deklarációkkal:

```php
<?php
if ( ! defined( 'ABSPATH' ) ) exit;

function {theme_slug_underscore}_setup() {
    add_theme_support( 'wp-block-styles' );
    add_theme_support( 'editor-styles' );
    add_theme_support( 'responsive-embeds' );
    add_theme_support( 'title-tag' );
    add_theme_support( 'post-thumbnails' );
    add_theme_support( 'custom-logo' );
}
add_action( 'after_setup_theme', '{theme_slug_underscore}_setup' );
```

(A `{theme_slug_underscore}` a theme slug kötőjelek helyett alulvonással, pl. `kovacs_tarsai`.)

---

## Template HTML fájlok – általános szabályok

- Kizárólag natív `<!-- wp:... -->` core blokkok.
- Minden template tartalmazza a header és footer template part-ot:
  ```html
  <!-- wp:template-part {"slug":"header","tagName":"header"} /-->
  <!-- wp:template-part {"slug":"footer","tagName":"footer"} /-->
  ```
- Soha ne írj inline `style=""` attribútumot – csak `className` és `fontSize`, `backgroundColor` stb. Gutenberg attribútumokat.
- Hard-coded szín hex értékek tilosak – csak `"textColor":"primary"` formátumú preset hivatkozások.

---

## templates/index.html

Blog lista / főoldal fallback. Tartalom:
- Header template part
- Query Loop blokk (Post Template: Post Title link, Post Featured Image, Post Excerpt, Post Date)
- Pagination blokk
- Footer template part

---

## templates/single.html

Egyedi blog bejegyzés oldal. Tartalom:
- Header template part
- Post Featured Image (wide align)
- Post Title (H1)
- Post Meta: Post Date, Post Author (opcionális)
- Post Content
- Comments blokk (placeholder: `<!-- wp:comments /-->`)
- Footer template part

---

## templates/page.html

Statikus oldal. Tartalom:
- Header template part
- Page Title (H1)
- Post Content
- Footer template part

(Egyszerűbb mint a single – nincs featured image kiemelve, nincs comments.)

---

## parts/header.html

Tartalom:
- Site Logo blokk (`<!-- wp:site-logo /-->`)
- Site Title blokk (`<!-- wp:site-title /-->`) – ha nincs logo, ez az elsődleges
- Navigation blokk (`<!-- wp:navigation /-->`)

Javasolt struktúra: Group blokk `tagName="header"` wrapper, Columns vagy Row layout a logo + navigáció elrendezéséhez.

---

## parts/footer.html

Tartalom:
- Site Title vagy szöveges copyright sor
- Navigation blokk (opcionális – footer menu)
- Paragraph blokk a brief-ben megadott footer tartalommal

Javasolt struktúra: Group blokk `tagName="footer"` wrapper.

---

## Zip generálás

1. Hozd létre a teljes könyvtárstruktúrát az `output/{theme-slug}/` mappában.
2. Csomagold zip-be: `output/{theme-slug}.zip`
3. Ellenőrzési lista zip előtt:
   - [ ] `style.css` megvan és a header komment kitöltött
   - [ ] `theme.json` valid JSON (ne legyen szintaktikai hiba)
   - [ ] `functions.php` megvan
   - [ ] `templates/index.html`, `templates/single.html`, `templates/page.html` megvan
   - [ ] `parts/header.html`, `parts/footer.html` megvan
   - [ ] A zip gyökér könyvtára `{theme-slug}/` (nem `output/`)

---

## Amit NE csinálj

- Ne módosítsd a `CLAUDE.md`-t, `CONTEXT.md`-t, `README.md`-t, `brief.md`-t, `preview.html`-t.
- Ne adj hozzá WooCommerce vagy plugin-függő blokkokat.
- Ne használj külső CDN-t vagy remote font URL-t a theme fájlokban (Google Fonts API helyett `fontFace` + bundle, vagy system font stack).
- Ne generálj `screenshot.png`-t – nem kötelező és időpazarló.
- **Ne várj képfájlt, ne hivatkozz külső képre.** A hero és featured image területek üres `wp:cover` illetve `wp:image` blokkok maradnak – az ügyfél tölti fel a tartalmakat aktiválás után a Site Editorban. A logó `wp:site-logo` blokk, szöveges fallback: `wp:site-title`.
