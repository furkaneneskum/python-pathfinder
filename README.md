# Python Pathfinder

**A gamified, serverless Python learning platform that runs entirely in the browser.**

Python Pathfinder turns a 32-chapter Python curriculum into an interactive staircase: every chapter is a step with its own lesson notes, a live Python code lab, flashcards and an end-of-chapter "boss fight" test. Progress, XP, streaks and study time are tracked locally — no account, no backend, no install.

**Live demo:** [furkaneneskum.github.io/python-pathfinder](https://furkaneneskum.github.io/python-pathfinder/)

![Python Pathfinder landing screen](assets/screenshots/01-landing.png)

---

## Highlights

- **32-chapter learning staircase** — a two-lane, zigzag roadmap from basic data types to advanced topics.
- **Real Python in the browser** — code runs client-side through [Pyodide](https://pyodide.org/) (CPython compiled to WebAssembly).
- **Gamification engine** — XP, levels, five developer ranks, daily streaks, daily focus goals and a 30-day activity heatmap.
- **Per-chapter study timer** — start / pause / save sessions; saved minutes convert into XP.
- **Developer panel** — a compact profile widget that expands into a centered, full-detail dashboard.
- **Zero backend in production** — all state lives in `localStorage`; the site is plain static files on GitHub Pages.
- **Optional FastAPI + SQLite backend** for local development and API experiments.
- **Responsive cyberpunk UI** — glassmorphism, 3D tilt cards, dark/light themes, keyboard shortcuts and a mobile layout.

---

## Tour

### 1. Landing & boot sequence

The entry screen combines a mission brief (curriculum modules and key numbers) with an animated system terminal that "boots" the platform. The learner enters a name and launches the app; the name is stored locally and used across the interface.

![Landing screen with mission brief and boot terminal](assets/screenshots/01-landing.png)

### 2. Dashboard

After entering, the learner lands on the main dashboard:

- **HUD bar** — daily streak, level, current rank, XP progress to the next level and overall completion percentage.
- **Operator greeting panel** — time-of-day greeting plus a *Resume where you left off* button that jumps to the next chapter.
- **Activity heatmap** — the last 30 days of study time, colored by intensity.
- **Developer panel summary** — avatar with progress ring, completed chapters, minutes studied today and total XP.

![Dashboard with HUD, greeting panel and activity heatmap](assets/screenshots/02-dashboard.png)

### 3. The learning staircase

The core of the product. Chapters are laid out as a two-lane staircase that descends left and right, connected by a dashed path. Each step shows:

- a completion checkbox (+100 XP on first completion),
- the chapter number and title,
- a live per-chapter study clock,
- a **Boss** badge for the chapter's final test,
- an **Akademi** button that opens the chapter's study room.

Completed steps glow gold; the current chapter is marked with a *you are here* tag.

![Two-lane zigzag learning staircase](assets/screenshots/03-staircase.png)

### 4. Developer panel

The sidebar keeps only the essentials. *Open developer panel* expands a centered modal (slightly smaller than full screen) with everything needed to track progress:

- daily focus goal (15 / 30 / 45 / 60 minutes) with a progress ring,
- streak, level and rank,
- XP bar to the next level,
- **You are here** and **Next target** cards with one-click *Start studying*,
- module progress list, a flippable *Python tip of the day* card and a 7-day activity summary.

![Developer panel opened as a centered modal](assets/screenshots/04-dev-panel.png)

### 5. Chapter command center

Opening a chapter shows its command center: a large study timer (start, save, reset) with session / saved / total breakdowns, and four routes into the learning material — lesson notes, code lab, flashcards and the boss fight.

![Chapter command center with study timer](assets/screenshots/05-command-center.png)

### 6. Lesson notes

Each chapter ships with a long-form lesson written as a mini-article: concept introduction, problem modelling, step-by-step reading of examples, common pitfalls and real-world usage, with syntax-highlighted code. Learners can also keep personal Markdown notes with a live preview.

![Lesson notes for the Functions chapter](assets/screenshots/06-lesson-notes.png)

### 7. Live code lab

A browser-based Python terminal powered by Pyodide. Code is executed locally in WebAssembly — nothing is sent to a server — and output (including errors) is rendered in the terminal panel.

![Pyodide code lab running Python in the browser](assets/screenshots/07-code-lab.png)

### 8. Boss fight

Every chapter ends with a coding challenge against a themed "boss". The learner writes a function, and the platform runs hidden checks against it in Pyodide. Passing the chapter test for the first time marks the chapter complete and awards XP.

![Boss fight coding challenge](assets/screenshots/08-boss-fight.png)

### 9. Mobile layout

On small screens the staircase collapses into a single readable column, the HUD stacks vertically and a sticky *next target* bar stays at the bottom of the screen.

<p align="center">
  <img src="assets/screenshots/09-mobile.png" alt="Mobile layout of the learning staircase" width="320">
</p>

---

## Gamification rules

| Action | Reward |
| --- | --- |
| Complete a chapter (first time) | +100 XP |
| Pass a chapter test (first time) | +100 XP, chapter marked complete |
| Understand a flashcard | +10 XP |
| Save study time | +10 XP per minute |
| Level up | every 500 XP |

Developer ranks unlock new avatar effects:

| XP | Rank |
| --- | --- |
| 0 | Python Apprentice |
| 500 | Code Developer |
| 1,500 | Python Expert |
| 3,000 | Data Master |
| 5,000 | Full-Stack Developer |

---

## Tech stack

| Layer | Technology |
| --- | --- |
| UI | HTML5, CSS3 (glassmorphism, 3D transforms, CSS grid), vanilla JavaScript (ES2020+) |
| In-browser Python | Pyodide (WebAssembly CPython) |
| Persistence | `localStorage` (versioned state object) |
| Code highlighting | Prism.js |
| Hosting | GitHub Pages (static, from `docs/`) |
| Optional backend | Python, FastAPI, SQLite, Uvicorn |

---

## Architecture

```
┌─────────────────────────── Browser ───────────────────────────┐
│  index.html + style.css                                        │
│  script.js ── UI, staircase, timer, XP engine, dev panel        │
│  academy-content.js ── lessons, flashcards, boss fights         │
│  storage.js ── localStorage state (topics, stats, activity)     │
│  Pyodide (WebAssembly) ── executes learner code locally         │
└────────────────────────────────────────────────────────────────┘
          ▲ static files                      ▲ optional REST API
   GitHub Pages (docs/)              FastAPI + SQLite (backend/)
```

- The production build is fully static: GitHub Pages serves `docs/`, and all learner data stays on the device.
- `frontend/` is the source of truth; `scripts/sync-docs.ps1` mirrors it into `docs/` for deployment.
- The FastAPI backend mirrors the same data model (topics, XP, streaks, activity) in SQLite for local development.

---

## Project structure

```
frontend/          Source UI (HTML, CSS, JS, lesson content)
docs/              GitHub Pages build (mirror of frontend/)
backend/           FastAPI + SQLite API for local development
scripts/           Helper scripts (sync-docs.ps1)
assets/screenshots Screenshots used in this README
```

---

## Running locally

**Static version (same as production):**

```powershell
cd docs
python -m http.server 8000
```

Open [http://127.0.0.1:8000](http://127.0.0.1:8000).

**With the FastAPI backend:**

```powershell
pip install -r requirements.txt
cd backend
python -m uvicorn main:app --reload
```

Open [http://127.0.0.1:8000](http://127.0.0.1:8000).

---

## Deployment

1. Edit files in `frontend/`.
2. Mirror them into `docs/`:

   ```powershell
   .\scripts\sync-docs.ps1
   ```

3. Commit and push to `main`. GitHub Pages publishes `main` → `/docs` automatically.

---

## Türkçe özet

**Python Pathfinder**, 32 bölümlük Python müfredatını oyunlaştırılmış bir merdivene dönüştüren, tamamen tarayıcıda çalışan bir öğrenme platformudur. Her bölümde ders notları, Pyodide ile çalışan canlı kod laboratuvarı, bilgi kartları ve bölüm sonu "boss fight" testi bulunur. XP, seviye, unvan, günlük seri, çalışma sayacı ve aktivite haritası `localStorage` üzerinde tutulur; sunucu veya hesap gerekmez.

---

## License

Personal educational project by [Furkan Enes Kum](https://github.com/furkaneneskum).
