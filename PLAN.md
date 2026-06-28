# Deathstar-OOP — Compleet Plan & Architectuur

> Film-reseller beheersysteem in PxPlus/NOMADS.
> Volledig object-georiënteerd, gelaagd, incrementeel gebouwd.

---

## 1. Wat de app moet kunnen

- **Films** — zoeken, toevoegen, bewerken, verwijderen (CRUD)
- **Categorieën** — beheren (toevoegen/verwijderen)
- **Prijzen** — inkoop + verkoop → winst, markup, marge (automatisch berekend)
- **Verkopen** — registreren + totalen per film (aantal verkocht, omzet)
- **Vergelijken** — 2 films naast elkaar (winst/marge/verkocht/omzet) + grafiek
- **Import/export** — CSV, XML, JSON
- **Online** — filmgegevens ophalen via OMDb-API
- **Remote foto's** — filmposters van een externe server
- **Adres-verificatie** — via een gratis API
- **Dev/user-modus** — twee versies van de app
- **Layout** — rustig, modern, makkelijk op de ogen

---

## 2. Architectuur-principes (de regels)

1. **Gelaagd** — Model → Repository → Service → (Gateway) → View.
2. **Gouden regel** — elke laag praat alleen met de laag eronder. Een programma opent NOOIT zelf een bestand.
3. **Programma's zijn dun** — geen business-logica, geen berekeningen, geen filling-boilerplate. Alleen: control lezen → service/helper aanroepen → resultaat in control zetten.
4. **Alle logica in classes** — een berekening (bv. winst) schrijf je **één keer**, en overal roep je `oFilm'profit` aan.
5. **Code in het Engels** — class-, method-, variabele- en commentaarnamen. (DB-veldnamen ook.)
6. **Incrementeel** — klein bouwen + testen, niet alles ineens. Eén fase tegelijk.
7. **Werkwijze** — bij grote stappen: UML → pseudo-code → echte code.

---

## 3. De lagen

| Laag | Doet | Voorbeeld |
|---|---|---|
| **Model** | data + eigen berekeningen | `film` (profit/markup/margin) |
| **Repository** | bestand-toegang (lezen/schrijven), geeft modellen terug | `film_repo` |
| **Service** | logica over meerdere modellen/repo's (use-cases) | `comparison_service` |
| **Gateway** | de externe wereld (API's, remote) | `omdb_gateway` |
| **View helper** | controls vullen (dropbox/grid/listbox) | `view_helper` |
| **Programma** (dunne controller) | UI-glue per scherm: leest controls, roept services/helper aan | `compare_prog` |
| **Panel** | het NOMADS-scherm | `compare` |
| **App** | launcher: menu → start schermen | `app` |

---

## 4. Mappenstructuur

```
Deathstar-OOP/
├─ models/
│   ├─ film.pvc
│   ├─ sale.pvc
│   └─ category.pvc
├─ repos/
│   ├─ film_repo.pvc
│   ├─ sales_repo.pvc
│   └─ category_repo.pvc
├─ services/
│   ├─ comparison_service.pvc
│   └─ import_export_service.pvc
├─ gateways/
│   ├─ omdb_gateway.pvc
│   ├─ address_gateway.pvc
│   └─ photo_gateway.pvc
├─ view/
│   └─ view_helper.pvc
├─ programs/
│   ├─ compare_prog
│   ├─ film_list_prog
│   ├─ film_form_prog
│   ├─ sales_form_prog
│   └─ category_prog
├─ data/
│   ├─ films.bst
│   ├─ sales.bst
│   └─ categories.bst
├─ panels.en          (NOMADS-bibliotheek met alle schermen)
└─ app                (launcher)
```

---

## 5. De classes (de API)

### Models — data + eigen berekeningen. Geen bestand, geen controls.

**film.pvc**
- properties: `FILM_ID`, `FILM_TITLE$`, `FILM_YEAR`, `CAT_ID`, `PURCHASE_PRICE`, `SALE_PRICE`, `FOTO_URL$`
- computed (read-only): `profit`, `markup`, `margin`

**sale.pvc**
- properties: `SALE_ID`, `FILM_ID`, `SALE_DATE$`, `QUANTITY`, `UNIT_PRICE`
- computed (read-only): `line_total` (= QUANTITY × UNIT_PRICE)

**category.pvc**
- properties: `CAT_ID`, `CAT_NAME$`

### Repositories — enige plek met bestand-toegang. Geven modellen terug.

**film_repo.pvc**
- `load(id)` → film-object
- `save(film)` → schrijft weg (auto-id bij nieuw)
- `delete(id)`
- `all_titles$()` / `all_ids$()` — voor dropboxes
- `all()` → lijst (voor listbox/grid)

**sales_repo.pvc**
- `load(id)` → sale-object
- `save(sale)`
- `total_quantity(film_id)` / `total_revenue(film_id)`
- `all_for_film(film_id)` → lijst

**category_repo.pvc**
- `load(id)` → category, `save(category)`, `delete(id)`
- `all_names$()` / `all_ids$()`

### Services — logica over meerdere modellen/repo's.

**comparison_service.pvc**
- `compare_grid$(id_a, id_b)` → bouwt de 8-rij vergelijk-string (gebruikt film_repo + sales_repo)

**import_export_service.pvc**
- `export_csv(file$)` / `export_xml(file$)` / `export_json(file$)`
- `import_csv(file$)` / `import_xml(file$)` / `import_json(file$)`

### Gateways — de externe wereld. (Syntax per stuk verifiëren bij die fase.)

**omdb_gateway.pvc** — `search$(title$)`, `details(imdb_id$)`
**address_gateway.pvc** — `verify$(postcode$, number)`
**photo_gateway.pvc** — `url$(film_id)` of fetch van remote server

### View — controls vullen, overal hergebruikt.

**view_helper.pvc**
- `load_dropbox(ctl, text$, ids$)` → `drop_box load` + `TBL$` + `TBLWIDTH`
- `load_grid(ctl, data$)` → `grid load`
- `load_listbox(ctl, data$)` → `list_box load`

### App

**app** — launcher: toont het hoofdmenu, start per keuze het juiste scherm.

---

## 6. De schermen

| Paneel | Programma | Roept aan | Doet |
|---|---|---|---|
| `main_menu` | `app` | — | hoofdmenu, knoppen naar schermen |
| `compare` | `compare_prog` | comparison_service, view_helper | 2 films vergelijken + grid |
| `film_list` | `film_list_prog` | film_repo, view_helper | lijst + zoek + bewerk/verwijder/bulk |
| `film_form` | `film_form_prog` | film_repo, category_repo | film toevoegen/bewerken |
| `sales_form` | `sales_form_prog` | sales_repo, film_repo | verkoop registreren |
| `category` | `category_prog` | category_repo, view_helper | categorieën beheren |

---

## 7. De data (bestanden + sleutels)

**films.bst** — `FILM_ID` (pk, kno=0), `FILM_TITLE$`, `FILM_YEAR`, `CAT_ID` (kno=1), `PURCHASE_PRICE` (Num 8.2), `SALE_PRICE` (Num 8.2), `FOTO_URL$` (Str 250)
**sales.bst** — `SALE_ID` (pk, kno=0), `FILM_ID` (kno=1), `SALE_DATE$` (Str 10), `QUANTITY` (Num 5), `UNIT_PRICE` (Num 8.2)
**categories.bst** — `CAT_ID` (pk, kno=0), `CAT_NAME$` (Str 50)

---

## 8. Roadmap (incrementeel — klein + testen)

| Fase | Levert op | Bestanden |
|---|---|---|
| ✅ 0 | film-model, film/sales-repo, vergelijk-scherm werkend | film, film_repo, sales_repo, compare_prog |
| **1** | `view_helper` + `comparison_service` → `compare_prog` echt dun | view_helper, comparison_service |
| 2 | mappenstructuur + `app`-launcher (hoofdmenu) | folders, app, main_menu |
| 3 | film CRUD + categorie | film_form, film_list, category (+ models/repos) |
| 4 | verkoop registreren | sale-model, sales_form |
| 5 | import/export (CSV/XML/JSON) | import_export_service |
| 6 | grafiek bij vergelijk | NOMADS-chart óf zelf |
| 7 | OMDb online-zoek + remote foto's | omdb_gateway, photo_gateway |
| 8 | adres-API + dev/user-modus + layout-overhaul | address_gateway |

---

## 9. Conventies & valkuilen (PxPlus/NOMADS — uit ervaring)

- Class-naam = bestandsnaam. Methode aanroepen via objecthandle: `oFilm'profit`.
- **Computed read-only property:** `property X get LABEL set err`; lezen ZONDER haakjes.
- `drop_box load` / `grid load` / `X.CTL'property` horen in **POST-DISPLAY** of een control-event (in pre-display bestaan de controls nog niet → Error #65). Bound variabelen (`VAR$`) mogen wél in pre-display.
- Een control = een **numeriek id** (`film_a.ctl` ís dat nummer) → kan aan een methode worden meegegeven.
- Grid-laad-string: kolommen `$8A$`, rij eindigt op `$00$`.
- `select * from FILE,kno=0 begin 0 ... next record` leest álle records. Secundaire-key range (`kno=1 begin/end`) is onbetrouwbaar bij dubbele waardes → filter in code met `if film_id=FID`.
- Stringveld → variabele MET `$`; getalveld → ZONDER `$`. (Verkeerd = Error #26.)
- `T$=` overschrijft in een loop; `T$+=` plakt aan (lijst bouwen).
- Na een `.pvc`-edit blijft de oude class gecached → `begin` of IDE herstarten.
- Een dropbox die groter is dan z'n eigen ruimte op het paneel bugt → toont maar 1 item.
- Na elke dictionary-wijziging: **Update File**. De bestandsnaam in de dictionary moet matchen met de naam in de code.
- `.pvc` is getokeniseerd/binair → buiten de IDE niet leesbaar; exporteer als tekst om te delen.
