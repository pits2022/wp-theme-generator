# CLAUDE.md – WordPress Theme Generator

Ez a fájl a Claude Code számára tartalmazza az összes instrukciót ehhez a projekthez.
A teljes projekt dokumentációja a `CONTEXT.md` fájlban található.

---

## Mi ez a projekt?

Automatizált WordPress FSE (Full Site Editing) theme generáló pipeline.
Inputja: `brief.md` (agency tölti ki).
Outputja: `output/{theme-slug}.zip` (feltölthető WordPress theme).

Részletes leírás: @CONTEXT.md

---

## Elérhető parancsok

| Parancs | Leírás |
|---|---|
| `/project:generate-preview` | Olvassa a `brief.md`-t, generálja a `preview.html`-t |
| `/project:create-theme [design] [variant]` | Legenerálja a kész theme zip fájlt |

---

## Általános viselkedési szabályok

1. **Mindig olvasd el a `brief.md`-t** mielőtt bármit generálsz – ne kérdezz olyat, ami abban szerepel.
2. **Mindig olvasd el a `CONTEXT.md`-t** ha nem vagy biztos valamiben – az az egyetlen forrás az igazságnak.
3. **Ne kérdezz feleslegesen** – ha minden szükséges adat megvan a `brief.md`-ben, azonnal kezdj el dolgozni.
4. **Ha a brief hiányos**, pontosan jelezd mi hiányzik, mielőtt megállsz.
5. **WooCommerce / webshop funkciók nem támogatottak v1-ben** – ha a brief ilyet tartalmaz, jelezd vissza és folytasd anélkül.
6. **Brief validáció kötelező** – mielőtt generálsz, ellenőrizd, hogy a `brief.md` minden mezője ki van töltve. Ha bármely mező értéke `[pl. ...]` vagy `[...]` formátumú (kitöltetlen placeholder), **állj meg** és jelezd, melyik mezők hiányoznak.
7. **Theme slug sanitálás** – a `brief.md`-ből kiolvasott slug-ot mindig ellenőrizd: csak kisbetű, kötőjel és szám szerepelhet benne. Szóközöket cseréld kötőjelre, ékezetes karaktereket írj át (pl. `á→a`, `ő→o`), egyéb karaktereket hagyd el. Ha a slug érvénytelen és nem lehet automatikusan javítani, jelezd vissza.

---

## Kódminőségi szabályok

- A `theme.json` a design **single source of truth** – soha ne írj hard-coded színt, fontot vagy spacinget a template HTML fájlokba.
- A template `.html` fájlokban **kizárólag natív WordPress core blokkok** szerepelhetnek (`<!-- wp:... -->`). Sem plugin blokkok, sem shortcode-ok.
- A `preview.html` **pure HTML/CSS** – JavaScript kizárólag a design kiválasztó gomb interakcióhoz engedélyezett. Külső CDN (Google Fonts stb.) nem szükséges, system font stack elfogadható a preview-ban.
- A theme slug mindig: **kisbetű, kötőjel elválasztó, ékezet nélkül** (pl. `kovacs-tarsai`).
- A `style.css` fejlécében lévő `Theme Name` pontosan egyezzen a `brief.md`-ben megadott ügyfélnévvel.

---

## Output szabályok

- A `preview.html` a repo gyökerébe kerüljön.
- A kész theme zip az `output/` mappába kerüljön: `output/{theme-slug}.zip`
- A zip fájlban a theme könyvtár neve egyezzen a theme slug-gal.
- **Kötelező zip parancs:** `cd output && zip -r {theme-slug}.zip {theme-slug}/` – TILOS az `output/` könyvtárból zipzelni, mert akkor az `output/{theme-slug}/` kerül a zip gyökerébe és a WordPress nem tudja telepíteni.
- **Ha az `output/{theme-slug}/` már létezik**, töröld le újragenerálás előtt: `rm -rf output/{theme-slug}/` – hogy ne maradjanak régi futásból való fájlok.
- Soha ne módosítsd a `CLAUDE.md`-t, `CONTEXT.md`-t, `README.md`-t, `.gitignore`-t generálás közben.

## Skill routing

When the user's request matches an available skill, ALWAYS invoke it using the Skill
tool as your FIRST action. Do NOT answer directly, do NOT use other tools first.
The skill has specialized workflows that produce better results than ad-hoc answers.

Key routing rules:
- Product ideas, "is this worth building", brainstorming → invoke office-hours
- Bugs, errors, "why is this broken", 500 errors → invoke investigate
- Ship, deploy, push, create PR → invoke ship
- QA, test the site, find bugs → invoke qa
- Code review, check my diff → invoke review
- Update docs after shipping → invoke document-release
- Weekly retro → invoke retro
- Design system, brand → invoke design-consultation
- Visual audit, design polish → invoke design-review
- Architecture review → invoke plan-eng-review
- Save progress, checkpoint, resume → invoke checkpoint
- Code quality, health check → invoke health
