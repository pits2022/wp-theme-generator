# WordPress FSE Theme Generator

Automatizált WordPress Full Site Editing theme generáló pipeline Claude Code segítségével.

**Input:** `brief.md` (agency tölti ki) → **Output:** feltölthető `.zip` WordPress theme

---

## Előfeltételek

- [Claude Code](https://claude.ai/code) telepítve és bejelentkezve
- Git
- Böngésző (a preview.html megnyitásához)

---

## Első beállítás (csak egyszer)

Klónozd vagy forkold ezt a repót:

```bash
git clone git@github.com:pits2022/wp-theme-generator.git
```

Ellenőrizd, hogy a Claude Code látja-e a projekt parancsokat:

```bash
claude
```

A Claude Code indításakor automatikusan beolvassa a `CLAUDE.md`-t, és elérhetővé válnak a `/project:` parancsok.

---

## Workflow – lépésről lépésre

### Új ügyfél esetén

**1. lépés – Fork a repo-t**

Minden ügyfélhez külön repo javasolt:

```bash
# GitHub UI-on: Fork → {ügyfél-neve}-theme
# vagy lokálisan új branch:
git checkout -b kovacs-tarsai
```

**2. lépés – Töltsd ki a `brief.md`-t**

Nyisd meg a `brief.md` fájlt és töltsd ki az összes mezőt az ügyfél igényei alapján:

```markdown
# Ügyfél Brief

## Alapadatok
- Ügyfél neve: Kovács és Társai Kft.
- Theme slug: kovacs-tarsai
- Weboldal típusa: Ügyvédi iroda
- Célközönség: 35-55 éves vállalkozók
...
```

> **Fontos:** A theme slug mindig kisbetű, kötőjellel elválasztva, ékezet nélkül legyen (pl. `kovacs-tarsai`, nem `Kovács-Társai`).

**3. lépés – Design preview generálása**

Indítsd el a Claude Code-ot a projekt mappájában, és futtasd:

```
/project:generate-preview
```

Claude Code beolvassa a `brief.md`-t, és legenerálja a `preview.html` fájlt. Ez 1-2 percet vesz igénybe.

**4. lépés – Preview megosztása az ügyféllel**

Nyisd meg a `preview.html` fájlt böngészőben. A fájl tartalmaz 3 design variánst (A, B, C), mindegyikhez világos és sötét változattal – összesen 6 kártyát.

Az ügyfélnek elküldheted:
- magát a `preview.html` fájlt (megnyitja lokálisan)
- vagy egy képernyőfotót / screen share-t

Minden kártyán látható:
- Design neve és leírása
- Tipográfia és szín paletta
- Mini layout preview (hero, tartalom blokk, footer)
- „Ezt választom" gomb

**5. lépés – Ügyfél visszajelzése alapján theme generálás**

Ha az ügyfél kiválasztotta a designt (pl. „B variáns, sötét"), futtasd:

```
/project:create-theme design-B dark
```

Elfogadott argumentumok:

| Argumentum | Leírás |
|---|---|
| `design-A light` | A variáns, világos |
| `design-A dark` | A variáns, sötét |
| `design-B light` | B variáns, világos |
| `design-B dark` | B variáns, sötét |
| `design-C light` | C variáns, világos |
| `design-C dark` | C variáns, sötét |

Claude Code legenerálja a teljes theme struktúrát és zip fájlba csomagolja. Az eredmény: `output/{theme-slug}.zip`

**6. lépés – Feltöltés WordPressbe**

1. Jelentkezz be a WordPress adminba
2. Navigálj: **Megjelenés → Sablonok → Sablon feltöltése**
3. Válaszd ki az `output/{theme-slug}.zip` fájlt
4. Kattints: **Telepítés most**
5. Kattints: **Aktiválás**

A theme azonnal aktív és szerkeszthető a WordPress Site Editorban (Megjelenés → Szerkesztő).

---

## Könyvtárstruktúra

```
/
├── CLAUDE.md                        # Claude Code instrukciók (ne módosítsd)
├── CONTEXT.md                       # Részletes projekt dokumentáció (ne módosítsd)
├── README.md                        # Ez a fájl
├── brief.md                         # ← Te töltöd ki ügyfelenként
├── preview.html                     # ← Claude generálja (ügyfélnek küldhető)
├── .claude/
│   └── commands/
│       ├── generate-preview.md      # /project:generate-preview parancs definíciója
│       └── create-theme.md          # /project:create-theme parancs definíciója
├── output/
│   └── {theme-slug}.zip             # ← Claude generálja (WordPress-be feltölthető)
└── .gitignore
```

---

## Generált WordPress theme struktúrája

A zip fájl kibontva:

```
{theme-slug}/
├── style.css              # Theme fejléc komment (Theme Name, Version stb.)
├── theme.json             # Design tokens: színek, fontok, spacing, layout
├── functions.php          # Minimális theme support deklarációk
├── templates/
│   ├── index.html         # Blog lista / főoldal fallback
│   ├── single.html        # Blog bejegyzés sablon
│   └── page.html          # Statikus oldal sablon
└── parts/
    ├── header.html        # Globális fejléc (logo + navigáció)
    └── footer.html        # Globális lábléc
```

---

## Hibaelhárítás

**„Parancs nem található" – `/project:generate-preview` nem működik**

Ellenőrizd, hogy a Claude Code-ot a projekt gyökerében indítottad-e el:

```bash
claude
```

**A generált theme aktiválás után fehér képernyőt mutat**

Leggyakoribb ok: szintaktikai hiba a `theme.json`-ban vagy hibás Gutenberg block markup. Ellenőrzési lépések:

1. Csomagold ki a zip-et
2. Ellenőrizd a `theme.json`-t egy JSON validátorral (pl. [jsonlint.com](https://jsonlint.com))
3. Futtasd újra a `/project:create-theme` parancsot és kérd meg Claude Code-ot, hogy validálja a generált JSON-t

**A design nem egyezik a preview-val**

A `preview.html` CSS-alapú mock – a valódi WordPress renderelés kis eltéréseket mutathat. Ez normális. Ha nagyobb eltérés van, kérd meg Claude Code-ot:

```
Nézd át a generált theme.json-t és ellenőrizd, hogy a design-B dark tokenek (színek, fontok) egyeznek-e a preview.html-ben látható B sötét kártyával.
```

**A brief.md hiányos mezői**

Ha a brief hiányos, Claude Code pontosan jelzi mi hiányzik. Töltsd ki a hiányzó mezőket, majd futtasd újra a parancsot.

---

## Verzió és korlátok

**Támogatott (v1):**
- Alap WordPress site (blog, statikus oldalak)
- Full Site Editing (WordPress 6.4+)
- 3 template: főoldal lista, blog bejegyzés, statikus oldal

**Nem támogatott (v1) – tervezett v2-ben:**
- WooCommerce / webshop
- Landing page template
- Contact page template
- Automatikus WordPress feltöltés
- ACF / egyedi mezők

---

## .gitignore ajánlás

```gitignore
# Generált output fájlok – ne kerüljenek a repóba
output/*.zip

# OS fájlok
.DS_Store
Thumbs.db
```

A `preview.html` viszont **kerüljön be** a repóba – így megőrződik, melyik designt hagyta jóvá az ügyfél.
