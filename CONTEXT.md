# CONTEXT.md – WordPress Theme Automatizálás

## Projekt áttekintés

Automatizált WordPress FSE (Full Site Editing) theme generáló pipeline, amelyet az agency használ ügyfelek részére. A folyamat Claude Code segítségével fut, a végeredmény egy feltölthető, azonnal aktiválható WordPress theme zip fájl.

---

## Célok

1. Az ügyfél `brief.md` alapján Claude Code generál több design variánst (HTML/CSS preview formájában).
2. Az ügyfél kiválasztja a neki tetsző designt a `preview.html` fájlból.
3. A kiválasztott design alapján Claude Code legenerálja a kész WordPress theme zip fájlt.
4. A zip fájlt az agency manuálisan tölti fel a WordPress admin felületen keresztül.

---

## Technológiai döntések

### Theme típus: Branded Standalone FSE Theme

- **Nem child theme** – nincs parent theme függőség.
- Minimális PHP, a design 80%-a `theme.json`-ban van definiálva.
- Fix template struktúra (Claude Code sablonból generálja, nem kreatívan "találja ki").
- Az ügyfél a saját nevű, brandelt theme-et látja a WordPress adminban.

### WordPress verzió és funkciók

- WordPress 6.4+ (Full Site Editing támogatás)
- Gutenberg block-alapú templates
- **Nem támogatott (v1):** WooCommerce, webshop funkciók

### Szükséges template fájlok (minimális készlet)

```
{theme-slug}/
├── style.css              # Kötelező header kommenttel (Theme Name, Version, stb.)
├── theme.json             # Design tokens: színek, tipográfia, spacing, layout
├── functions.php          # Minimális – csak theme support deklarációk
├── templates/
│   ├── index.html         # Főoldal / blog lista fallback
│   ├── single.html        # Blog post / cikk oldal
│   └── page.html          # Statikus oldalak (ritkán frissülő tartalom)
└── parts/
    ├── header.html        # Globális fejléc (navigáció, logo)
    └── footer.html        # Globális lábléc
```

---

## Workflow lépései

### 1. lépés – Brief kitöltése

Az agency kitölti a `brief.md` fájlt az ügyfél igényei alapján. Ez az egyetlen input Claude Code-nak.

**brief.md struktúra:**

```markdown
# Ügyfél Brief

## Alapadatok
- Ügyfél neve: [pl. Kovács és Társai Kft.]
- Theme slug: [pl. kovacs-tarsai] (kisbetű, kötőjel, ékezet nélkül)
- Weboldal típusa: [pl. ügyvédi iroda / étterem / tech startup / stb.]
- Célközönség: [pl. 35-55 éves vállalkozók]

## Design irány
- Stílus preferencia: [pl. letisztult és professzionális / játékos és modern / klasszikus és megbízható]
- Kerülendő: [pl. túl színes, ne legyen zsúfolt]
- Referencia oldalak (opcionális): [URL-ek]

## Színek
- Van-e meglévő brand szín? [Igen/Nem]
- Ha igen, elsődleges szín: [pl. #1A3C6E]
- Hangulat: [pl. sötét és elegáns / világos és légies / erős kontrasztok]

## Tipográfia
- Preferált karakter: [pl. serif = klasszikus, sans-serif = modern, display = egyedi]
- Különleges igény: [pl. legyen jól olvasható kis képernyőn]

## Tartalom struktúra
- Logo: [szöveges / képfájl lesz feltöltve]
- Navigációs menü elemek száma: [pl. 5]
- Footer tartalom: [pl. copyright, közösségi média ikonok, rövid leírás]

## Egyéb megjegyzések
[Szabadszöveges mező]
```

---

### 2. lépés – Design variánsok generálása

Claude Code a `brief.md` alapján létrehoz **3 különböző design irányvonalat**, mindegyikhez **világos és sötét színváltozattal** – összesen 6 design variáns.

**Output: egyetlen `preview.html` fájl**

A fájl böngészőben megnyitható, és egy oldalon tartalmazza az összes variánst egymás alatt kártyákban:

```
┌──────────────────────────────────────────────┐
│  Design A – Világos    │  Design A – Sötét    │
├──────────────────────────────────────────────┤
│  Design B – Világos    │  Design B – Sötét    │
├──────────────────────────────────────────────┤
│  Design C – Világos    │  Design C – Sötét    │
└──────────────────────────────────────────────┘
```

Minden kártyán látható:
- Tipográfia (H1, H2, body text minta)
- Szín paletta (primary, secondary, accent, background, text)
- Egy egyszerű layout preview (hero szekció, egy content blokk, footer csík)
- A design neve és rövid leírása (pl. "Letisztult Minimalista – sötét változat")

Az ügyfélnek nem kell mást tennie, mint megnyitni a fájlt és jelezni, melyiket választja.

**Design variánsok generálásának szabályai Claude Code számára:**
- A 3 design egymástól **vizuálisan markánsan különbözzön** (eltérő tipográfiai karakter, eltérő layout arányok, eltérő hangulat)
- Kerülendő: generikus AI-esztétika (Inter/Roboto font, lila gradiens fehér háttéren)
- Minden designhoz tartozzon egy rövid, ügyfélbarát elnevezés és 1-2 mondatos leírás
- A sötét változat ne csak invertált legyen – legyen saját hangulata

---

### 3. lépés – Design kiválasztása

Az agency közvetíti az ügyfél döntését Claude Code-nak a következő formában:

```
Válaszott design: [A/B/C], [világos/sötét]
```

---

### 4. lépés – Theme generálása

Claude Code legenerálja a teljes theme könyvtárstruktúrát, majd zip fájlba csomagolja.

**theme.json kötelező szekciók:**

```json
{
  "$schema": "...",
  "version": 3,
  "settings": {
    "color": { "palette": [...], "custom": true },
    "typography": { "fontFamilies": [...], "fontSizes": [...] },
    "spacing": { "spacingScale": {...}, "units": ["px", "rem", "%"] },
    "layout": { "contentSize": "760px", "wideSize": "1200px" }
  },
  "styles": {
    "color": { "background": "...", "text": "..." },
    "typography": { "fontFamily": "...", "fontSize": "...", "lineHeight": "..." },
    "elements": { "link": {...}, "h1": {...}, "h2": {...} }
  },
  "templateParts": [
    { "name": "header", "title": "Header", "area": "header" },
    { "name": "footer", "title": "Footer", "area": "footer" }
  ]
}
```

**Template HTML fájlok generálási szabályai:**
- Kizárólag natív Gutenberg core blokkok használhatók (pl. `<!-- wp:group -->`, `<!-- wp:navigation -->`)
- Nem használható: külső plugin blokkok, shortcode-ok
- Minden template tartalmazza a `header` és `footer` template part-ot
- A `single.html` tartalmaz: post title, featured image, content, post meta (dátum, szerző opcionálisan), comments block placeholder
- A `page.html` tartalmaz: page title, content – egyszerűbb, mint a single
- A `index.html` tartalmaz: post lista (Query Loop blokk), pagination

**Output fájl neve:** `{theme-slug}.zip`

---

### 5. lépés – Feltöltés (manuális)

1. WordPress admin → Megjelenés → Sablonok → Sablon feltöltése
2. A `{theme-slug}.zip` fájl kiválasztása
3. Telepítés → Aktiválás

---

## Képkezelési stratégia

A generálási folyamat **nem igényel semmilyen képfájlt** sem az agencytől, sem az ügyféltől előzetesen. A három képtípus kezelése:

### 1. Preview képek (`preview.html`)

A `preview.html` design mockup, nem pixel-perfect prototípus – valódi képek nélkül is megítélhető a dizájn. Képek helyett **inline SVG placeholder-ek** jelzik a képterületeket (hero, featured image stb.). Az SVG-k beágyazottak a HTML fájlba – nincs külső CDN függőség. A placeholder megjelenítse a képterület arányait és egy semleges feliratot (pl. „Hero kép").

### 2. Hero / szekció képek a theme-ben

A WordPress FSE template fájlok nem tartalmaznak tényleges képeket – csak blokk struktúrát. A hero terület például egy `<!-- wp:cover -->` blokk tartalom nélkül. Az ügyfél a képeket aktiválás után a WordPress Site Editorban tölti fel.

### 3. Logo

A `brief.md`-ben megadott logo típus alapján:

- **Szöveges logo** → `<!-- wp:site-title -->` blokk, nincs képfájl szükséges
- **Képfájl logo** → `<!-- wp:site-logo -->` blokk, az ügyfél tölti fel WordPress adminban (Megjelenés → Testreszabás → Logo) aktiválás után

A theme nem tartalmaz logo képfájlt – ez mindig WordPress adminból kerül be.

---

## Fájl és könyvtárstruktúra a projekten belül

```
/wp-theme-generator/
├── CONTEXT.md              # Ez a fájl – a teljes projekt leírása
├── brief.md                # Ügyfél brief (minden projektnél újratöltve)
├── preview.html            # Generált design preview (ügyfélnek küldhető)
└── output/
    └── {theme-slug}.zip    # Kész WordPress theme
```

---

## Claude Code számára fontos szabályok

1. **Mindig a `brief.md`-t olvasd el először** – ne kérdezz olyat, ami abban szerepel.
2. **A template HTML fájlokban csak WordPress core blokkokat használj** – plugintől független legyen a theme.
3. **A `theme.json` a design single source of truth** – ne írj hard-coded színt vagy fontot a template fájlokba.
4. **A preview.html pure HTML/CSS legyen** – JavaScript csak a "Ezt választom" gomb interakcióhoz engedélyezett, külső CDN nem szükséges.
5. **Minden generálásnál ellenőrizd a zip struktúrát** – a `style.css`-ben lévő `Theme Name` egyezzen a brief-ben megadott névvel.
6. **Ékezetes karakterek:** a theme slug mindig ékezet nélküli, kisbetűs, kötőjeles legyen (pl. `kovacs-tarsai`).
7. **v1 scope:** WooCommerce és webshop funkciók nem részei a projektnek – ha a brief ilyet tartalmaz, jelezd vissza.

---

## Jövőbeli fejlesztések (v2 scope – nem része a jelenlegi projektnek)

- WooCommerce / webshop template támogatás
- Automatikus WordPress feltöltés (WP REST API egyedi endpoint vagy WP-CLI via SSH)
- Több alap page template (landing page, contact page, archive)
- ACF / custom fields integráció
- Multisite támogatás

---

## Verzió

| Verzió | Dátum | Változások |
|--------|-------|------------|
| 1.0 | 2026-04-10 | Első verzió |
| 1.1 | 2026-04-10 | Képkezelési stratégia hozzáadva |
