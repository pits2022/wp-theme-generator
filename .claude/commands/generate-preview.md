# generate-preview

Olvasd el a `brief.md` tartalmát. Ha a fájl hiányzik vagy üres, állj meg és jelezd a hiányt.

## Feladatod

Generálj **3 egymástól vizuálisan markánsan különböző design variánst** (A, B, C), mindegyikhez **világos és sötét színváltozattal** – összesen 6 design kártya.

Az eredményt írd egyetlen `preview.html` fájlba a repo gyökerébe.

---

## A preview.html felépítése

Egyetlen, böngészőben megnyitható HTML/CSS fájl. Felépítése:

1. **Fejléc szekció** – az ügyfél neve, a brief alapján
2. **3 design sor**, mindegyik tartalmaz:
   - Bal kártya: világos változat
   - Jobb kártya: sötét változat
3. Minden kártyán belül megjelenik:
   - Design neve és 1-2 mondatos ügyfélbarát leírása
   - Tipográfia preview: H1, H2, bekezdés szöveg
   - Szín paletta csíkok (primary, secondary, accent, background, text)
   - Mini layout preview: hero szekció, egy tartalom blokk, footer csík
   - „Ezt választom" gomb (JavaScript: kiemeli a kiválasztott kártyát, a többit halványítja)

---

## Design variánsok generálásának szabályai

- A 3 design **eltérő tipográfiai karakterrel, eltérő layout arányokkal és eltérő hangulattal** rendelkezzen.
- **Tilos**: generikus AI-esztétika – Inter/Roboto/Arial font, lila gradiens fehér háttéren, "corporate blue" alapértelmezések.
- A sötét változat **ne csak color-invertált legyen** – legyen saját atmoszférája (más árnyalatú háttér, más accent szín is megengedett).
- A design elnevezések legyenek **ügyfélbarátak és leíróak** (pl. „Klasszikus Elegancia", „Modern Minimál", „Nyers Erő") – nem „Design A/B/C".
- A `brief.md`-ben megadott stílus preferenciák, brand színek és kerülendő elemek **kötelezően érvényesítendők**.
- A Google Fonts helyett használj **system font stack-et vagy CSS `@font-face`-t** a preview-ban (nincs külső CDN függőség).

---

## Technikai követelmények

- Pure HTML + CSS, egyetlen fájl.
- Reszponzív: mobilon is használható (az ügyfél esetleg telefonon nézi).
- JavaScript csak a kártya kiválasztás interakcióhoz.
- Nincs külső CDN, nincs `<link rel="stylesheet" href="...">` külső forrásra.
- **Képek helyett inline SVG placeholder-ek** – ne tölts le képet, ne hivatkozz külső URL-re (sem picsum, sem unsplash, sem más). Minden képterületet (hero, featured image, tartalom kép) egy beágyazott SVG jelöl, amely mutatja a terület arányait és egy semleges feliratot (pl. „Hero kép", „Kiemelt kép"). Az SVG háttérszíne illeszkedjen a kártya design palettájához.
- A fájl neve: `preview.html`, helye: repo gyökér.

---

## Amit NE csinálj

- Ne kérdezz rá olyasmire, ami a `brief.md`-ben szerepel.
- Ne generálj theme fájlokat ebben a lépésben – csak a `preview.html` az output.
- Ne módosítsd a `CLAUDE.md`-t, `CONTEXT.md`-t, `README.md`-t, `brief.md`-t.
