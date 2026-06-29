# 🎬 Film Reseller

> A film-reseller management system built in **PxPlus / NOMADS** — fully object-oriented, strictly layered, and built one tested slice at a time.

A desktop application for a film reseller: manage your inventory, set purchase & sale prices, register sales, and see at a glance which titles are actually profitable — all from a clean dashboard.

This is a **learning project**: a ground-up rebuild of a procedural app into a properly layered, object-oriented architecture, to genuinely master OOP and clean design in PxPlus.

---

## ✨ Features

- **Film management (CRUD)** — list, add, edit and delete films; set purchase/sale prices
- **Sales** — register sales and track quantity sold & revenue per film
- **Compare** — put two films side by side: profit, markup %, margin %, sold, revenue
- **Dashboard menu** — live KPIs (films, sold, revenue) read straight from the data
- **Computed metrics** — profit, markup and margin are calculated **once** in the model and reused everywhere (`oFilm'profit`)
- **Safe by design** — destructive actions (delete) ask for confirmation

**Planned:** category management · CSV/XML/JSON import-export · sales charts · OMDb online lookup · remote posters · address verification · dev/user modes.

---

## 🏛️ Architecture

The one rule everything follows:

> **Every layer talks only to the layer below it. A program never opens a file itself.**

| Layer | Responsibility | Example |
|---|---|---|
| **Model** | data + its own calculations | `film` → profit/markup/margin |
| **Repository** | the *only* place that touches the data files | `film_repo`, `sales_repo` |
| **Service** | logic across multiple models/repos | `comparison_service` |
| **Gateway** | the outside world (APIs, remote) | `omdb_gateway` *(planned)* |
| **View helper** | filling controls (grids, dropboxes) | `view_helper` |
| **Program** | thin controller — reads controls, calls services | `film_list_prog` |
| **Panel** | the NOMADS screen | `film_list` |
| **App** | launcher / main menu | `main_menu` |

**Why bother?** Because the seams are clean:
- Swap the storage (keyed files → SQL) and only the **Repository** changes — the rest of the app never notices.
- Add a web front-end and only the **View** changes; the business logic stays put.
- Need an API for a partner? It's just another caller of your **Services**.

The programs stay thin: *read a control → call a service/repo → put the result back.* No business logic, no file access.

---

## 🗂️ Project structure

```
Deathstar-OOP/
├─ film.pvc                 # Model — a film (data + profit/markup/margin)
├─ Sale.pvc                 # Model — a sale (line total)
├─ film_repo.pvc            # Repository — film data access (load/save/delete/...)
├─ sales_repo.pvc           # Repository — sales data access (totals, grand totals)
├─ services/
│  └─ comparison_service.pvc
├─ view/
│  └─ view_helper.pvc       # Fills grids & dropboxes
├─ *_prog                   # Thin programs (controllers), one per screen
├─ oop_lib.en               # NOMADS panel library (the screens)
└─ *.bst                    # Keyed data files
```

---

## 🛠️ Tech

- **PxPlus 2025** (v22.10) — Business BASIC, object-oriented features
- **NOMADS** — desktop GUI framework
- Keyed **`.bst`** data files
- Modern error handling with `TRY / CATCH / FINALLY`

---

## 🚦 Roadmap

| | Phase | Status |
|---|---|---|
| ✅ | Film model + repositories + compare screen | done |
| ✅ | `view_helper` + `comparison_service` (thin programs) | done |
| 🔨 | Main menu / launcher with live KPIs | in progress |
| 🔨 | Film CRUD + category management | film CRUD done |
| ✅ | Sales registration | done |
| ⬜ | Import / export (CSV · XML · JSON) | planned |
| ⬜ | Charts on the compare screen | planned |
| ⬜ | OMDb online lookup + remote posters | planned |
| ⬜ | Address API + dev/user mode + UI polish | planned |

---

## 🚀 Running

1. Point the PxPlus **prefix** at the project (and its `services/` and `view/` folders).
2. Open the **`main_menu`** panel from `oop_lib.en`.
3. From the menu, jump to Compare, Films or Sales.

---

## 📚 About

A personal learning project by **[KillaNova](https://github.com/KillaNova)**.

Built incrementally and deliberately — **UML → pseudocode → code** — with every engine (model/repository/service) tested in isolation from the console *before* a single screen is wired on top. The goal isn't just a working app; it's understanding *why* a layered architecture is what lets a small app grow without a rewrite.
