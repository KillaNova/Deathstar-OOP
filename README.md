# 🎬 Film Reseller

> A film-reseller management system built in **PxPlus / NOMADS** — fully object-oriented, strictly layered, and built one tested slice at a time.

A desktop application for a film reseller: manage inventory, set purchase & sale prices, register sales, and see at a glance which titles are actually profitable — all from a clean dashboard.

This is a **learning project**: a ground-up rebuild of a procedural app into a properly layered, object-oriented architecture, to genuinely master OOP and clean design in PxPlus.

---

## ✨ Features

**Working today**
- **Film management (CRUD)** — list, add, edit and delete films; set purchase/sale prices
- **Sales** — register sales; track quantity sold & revenue per film
- **Compare** — two films side by side: profit, markup %, margin %, sold, revenue
- **Dashboard menu** — live KPIs (films, sold, revenue) read straight from the data
- **Computed metrics** — profit, markup and margin are calculated **once** in the model and reused everywhere (`oFilm'profit`)
- **Price seeding** — a small reusable script fills randomized, realistic prices for demo data
- **Safe by design** — destructive actions (delete) ask for confirmation

**Planned** — category management · CSV/XML/JSON import-export · sales charts · OMDb online lookup · remote posters · address verification · dev/user modes

---

## 🏛️ Architecture

The one rule everything follows:

> **Every layer talks only to the layer below it. A program never opens a file itself.**

| Layer | Responsibility | Example |
|---|---|---|
| **Model** | data + its own calculations | `film` → profit/markup/margin |
| **Repository** | the *only* place that touches the data files | `film_repo`, `sales_repo` |
| **Service** | logic across multiple models/repos | `comparison_service` |
| **View helper** | filling controls (grids, dropboxes) | `view_helper` |
| **Program** | thin controller — reads controls, calls services | `film_list_prog` |
| **Panel** | the NOMADS screen | `film_list` |
| **Launcher** | sets the search path, opens the menu | `deathstar_init` |

**Why bother?** The seams stay clean:
- Swap the storage (keyed files → SQL) and only the **Repository** changes.
- Add a web front-end and only the **View** changes; the business logic stays put.
- Programs stay thin: *read a control → call a service/repo → put the result back.*

---

## 🗂️ Project structure

```
Deathstar-OOP/
├─ models/                 # data + own calculations
│  ├─ film.pvc             #   profit / markup / margin
│  └─ sale.pvc             #   line total
├─ repos/                  # the only place that touches the .bst files
│  ├─ film_repo.pvc
│  └─ sales_repo.pvc
├─ services/               # logic across repos
│  └─ comparison_service.pvc
├─ view/                   # fills grids & dropboxes
│  └─ view_helper.pvc
├─ programs/               # thin controllers (one per screen)
│  ├─ main_menu_prog
│  ├─ compare_prog
│  ├─ film_list_prog
│  ├─ sales_form_prog
│  └─ sales_prog
├─ scripts/                # one-off / reusable tools
│  └─ seed_price_script    #   fills randomized demo prices
├─ OOP_lib/
│  └─ oop_lib.en           # NOMADS panel library (all screens)
├─ deathstar_init          # launcher: sets PREFIX, opens the menu
├─ *.bst                   # keyed data files (films, sales, categories)
└─ providex.*              # data dictionary
```

**Layout rule:** code lives in sub-folders; **data (`.bst`) and the dictionary (`providex.*`) stay in the root.** Moving the data layer risks breaking relative file opens and dictionary lookups, and the payoff is only cosmetic.

---

## 🛠️ Tech

- **PxPlus 2025** (v22.10) — Business BASIC with object-oriented features
- **NOMADS** — desktop GUI framework
- Keyed **`.bst`** data files + PxPlus **data dictionary**
- Modern error handling with `TRY / CATCH / FINALLY`
- `.gitattributes` marks the tokenized PxPlus files as **binary** (no line-ending corruption)

---

## 🚦 Roadmap

| | Phase | Status |
|---|---|---|
| ✅ | Film model + repositories + compare screen | done |
| ✅ | `view_helper` + `comparison_service` (thin programs) | done |
| ✅ | Main menu / launcher with live KPIs | done |
| ✅ | Film CRUD | done |
| ✅ | Sales registration | done |
| ✅ | Folder reorganisation (layered directories) | done |
| 🔨 | Category management | next |
| ⬜ | Import / export (CSV · XML · JSON) | planned |
| ⬜ | Charts on the compare screen | planned |
| ⬜ | OMDb online lookup + remote posters | planned |
| ⬜ | Address API + dev/user mode + UI polish | planned |

---

## 🚀 Running

The launcher sets the search **prefix** (root + every code sub-folder) and opens the main menu in one go:

```
RUN "<project path>\deathstar_init"
```

From the menu, jump to **Compare**, **Films** or **Sales**.

> **Working in the NOMADS IDE?** Set the same prefix under **Application Launcher → Defaults**, so the IDE "Test" button can find the programs that now live in the sub-folders.

**Seed demo prices** (optional, reusable):

```
RUN "<project path>\scripts\seed_price_script"
```

---

## 📚 About

A personal learning project by **[KillaNova](https://github.com/KillaNova)**.

Built incrementally and deliberately — **UML → pseudocode → code** — with every engine (model/repository/service) tested in isolation from the console *before* a single screen is wired on top. The goal isn't just a working app; it's understanding *why* a layered architecture is what lets a small app grow without a rewrite.
