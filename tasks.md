# Translation Tasks

Repo: 86 markdown files (~101,666 lines) of Chinese algorithm tutorials + ~70 Chinese-named files/folders.

Decisions:
- Scope: All markdown content + rename Chinese files/folders to English
- Style: Faithful translation; keep code, images, links unchanged
- Mode: Replace originals in place (git-tracked)

## Phase 1 — Top-level & section READMEs
- [x] `README.md` (root)
- [x] `dynamic-programming/README.md`
- [x] `data-structures/README.md`
- [x] `algorithmic-thinking/README.md`
- [x] `interview-frequent/README.md`
- [x] `multi-language-solutions/contribution-guide.md`
- [x] `multi-language-solutions/solution_code.md` *(100% translated; 72,141 lines, 0 Chinese characters remaining. Translation infra at `.translate/`)*

## Phase 2 — Rename Chinese files & folders to English
- [x] All section folders renamed to English (`dynamic-programming`, `data-structures`, `algorithmic-thinking`, `interview-frequent`, `tech`, `multi-language-solutions`)
- [x] All Chinese-named `pictures/` subfolders renamed
- [x] All Chinese-named `.md` files inside each section renamed
- [x] All cross-references in `.md` files updated via sed

## Phase 3 — Translate contents folder-by-folder
- [x] `dynamic-programming/` (17 files done)
- [ ] `data-structures/` (~15 files)
- [ ] `algorithmic-thinking/` (~17 files)
- [ ] `interview-frequent/` (~17 files)
- [x] `tech/` (7 files done)
- [x] `multi-language-solutions/solution_code.md` (translated above)

## Notes
- Preserve image paths even if folder names change (update references when folders are renamed).
- Keep code blocks verbatim; only translate comments inside them.
- Track progress by checking off boxes above as batches complete.
