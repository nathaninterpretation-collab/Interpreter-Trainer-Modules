# Copilot Instructions for Interpreter-Trainer-Modules

## Repository Overview

This repository contains **BilinCo Court Interpreter Trainer**, a self-contained, browser-based educational tool that helps users train for court interpreter certification exams. There is no build system, package manager, or server — each module is a single standalone HTML file that can be opened directly in any modern browser.

**Languages/Technologies:** HTML5, CSS3 (with CSS custom properties), vanilla JavaScript (ES6+), Web Speech API, Web Audio API.  
**Target audience:** Aspiring court interpreters (currently English↔Mandarin Chinese).

---

## Repository Layout

```
/
├── .gitattributes                    # LF line-ending normalization for all text files
├── court_interpreter_trainer_v2.35.html   # Original trainer (~1,800 lines)
├── AAG-experiment_v2.38.html              # AAG experiment build (~6,600 lines)
└── AAG-experiment_v2.40.html              # Latest AAG build (~8,200 lines) ← primary file
```

Each HTML file is fully self-contained: CSS is in a `<style>` block, all data is in `const` declarations, and all logic is in `<script>` blocks — no external assets beyond Google Fonts.

**Versioning convention:** Files are named with semantic version suffixes (`v2.35`, `v2.38`, `v2.40`). New versions should follow this pattern. The highest-numbered `AAG-experiment_vX.YZ.html` file is the canonical latest version.

---

## Architecture of the Latest File (`AAG-experiment_v2.40.html`)

### Data Layer (large `const` arrays near the top of `<script>`)
| Variable | Purpose |
|----------|---------|
| `LC` | Legal Concepts — array of `{term, definition, domain}` objects |
| `T` | Vocabulary terms — array with `id, term, ipa, mk, idi, sim, definition, examples, tags` |
| Inline `scenarios` objects | Court dialogue scenarios used by the Interpret mode |

### UI Sections (controlled by `id`-based show/hide)
- **Hub** (`#hub`) — landing card grid (LEARN, QUIZ ME, INTERPRET, WRITTEN EXAM)
- **Learn** (`#learn`) — tabbed reference: VOCAB / WRITTEN EXAM / LEGAL CONCEPTS
- **Quiz** (`#quiz`) — flashcard-style multiple-choice practice
- **Interpret** (`#interpret`) — speech-recognition interpreting exercise (Web Speech API)
- **Written Exam** (`#we`) — timed written question set
- **Legal Concepts** (`#lc`) — expandable definition cards

### AAG Subsystems (prefixed `AAG_` or commented `=== AAG: ===`)
- `AAG_AUDIO` — synthesised ambient/SFX audio engine (Web Audio API)
- Progressive disclosure, streak gamification, omnibox search, keyboard shortcuts, GPU-accelerated progress bars

### Key CSS Conventions
- All colors and spacing are in `:root` CSS custom properties (`--bg`, `--acc`, `--grn`, etc.).
- Class names use short abbreviations (`.qi`, `.qgo`, `.cfg-o`, `.lpill`, etc.) — preserve existing conventions when adding new classes.
- Dark-mode-only UI; do not introduce light-mode styles.

---

## Build, Test, and Validation

There is **no build step and no automated test suite**.

### Opening / Previewing
Open any HTML file directly in a browser:
```
# macOS
open AAG-experiment_v2.40.html

# Linux
xdg-open AAG-experiment_v2.40.html

# Windows
start AAG-experiment_v2.40.html
```

### Validation Checklist (run manually after each change)
1. **HTML validity** — open the file and check the browser console for errors.
2. **All five hub cards load** — Hub → LEARN → QUIZ ME → INTERPRET → WRITTEN EXAM → LEGAL CONCEPTS each render without JS errors.
3. **Vocabulary filter** — enter a rank range and confirm cards filter correctly.
4. **Quiz flow** — start a quiz, answer a question, confirm correct/wrong feedback and streak counter.
5. **Interpret flow** — press "Start" in the Interpret section; verify the Web Speech API prompt appears (requires HTTPS or localhost in Chrome).
6. **Audio** — click any hub card and confirm no uncaught audio context errors in the console.
7. **Responsive layout** — verify the UI renders acceptably at 375 px (mobile) and 1 280 px (desktop) widths.

There are no CI workflows or GitHub Actions in this repository.

---

## Making Changes

- **Adding vocabulary terms:** Append objects to the `T` array (follow the existing schema exactly — all fields required).
- **Adding legal concepts:** Append objects to the `LC` array.
- **Adding quiz questions/scenarios:** Add entries to the relevant inline data structures near the `// === SCENARIOS ===` comments.
- **Styling:** Use existing `:root` variables; avoid inline styles and hard-coded colors.
- **New features:** Follow the AAG comment-block style (`/* === FEATURE NAME === */`) and the existing IIFE/module pattern for new JS subsystems.
- **Do not** split the file into multiple files; the self-contained single-file format is intentional.
- **Do not** introduce `npm`, `webpack`, or any build tooling.

---

## Important Notes

- The Speech Recognition API (`webkitSpeechRecognition`) requires a secure context (HTTPS or `localhost`). Interpret-mode tests must be done in a browser, not a headless environment.
- The file uses `// @ts-nocheck` conventions implicitly — there is no TypeScript.
- All console logs use bracketed prefixes (e.g., `[v2.40]`, `[AAG Audio]`) — follow this pattern for any new diagnostic logging.
