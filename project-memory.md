# Project Memory
Last updated: 2026-04-27

This file captures decisions, reasoning, and session context that
project-context.md doesn't hold. It is Claude's memory between sessions.

---

## Session — 2026-04-27 (session 2)

**Focus:** Era-specific visual themes; skill data reconciliation against Roll20 official sheet.

**Decisions made:**
- Three era CSS themes implemented via `body.era-X` class — variables cascade so all `var()` references update automatically on selection
- Modern: cold blue-gray palette (`#0e1117` bg, `#2ab0dc` accent), Oswald font, gradient header line
- 1920s: warm sepia/gold (`#18110a` bg, `#d09020` accent), Playfair Display italic, Art Deco `— ◆ ✦ ◆ —` ornament + double-rule header line
- Victorian: dark forest green (`#0c1410` bg, `#6ab048` accent), Cinzel serif, `❦` floral ornament
- Each era card has a hardcoded background tint and ornament via ID selectors (`#era-modern`, `#era-1920s`, `#era-victorian`) so they look distinct before selection
- `applyEraTheme(era)` helper function centralizes body class + subtitle text update; called from `selectEra()`, `applyCharData()`, `newInvestigator()`, and `DOMContentLoaded`
- Era-specific header subtitle text: Modern → "CLASSIFIED INVESTIGATOR FILE", 1920s → "ARKHAM INVESTIGATOR DOSSIER", Victorian → "INVESTIGATOR OF UNEXPLAINED PHENOMENA"
- Sheet top bar and accent colors overridden per era via `body.era-X .sheet-top-bar` etc.
- Fetched Roll20 official CoC 7e sheet HTML for skill reference (1.2MB, 18k lines); extracted actual tracked skills per era by targeting comment-delimited sections
- Dodge formula was a bug: `Math.floor(DEX/5)*2` gave wrong values (DEX 60 → 24 instead of 30); corrected to `Math.floor(DEX/2)` (half DEX per 7e rules)
- Listen base is era-specific per Roll20: 20% for 1920s/Victorian, 25% for Modern; implemented via `base:'LISTEN'` sentinel and era check in `getSkillBase()`
- Firearms (SMG) added at base 15%, Modern-only — present in Roll20 Modern list but not 1920s
- Roll20 reference stored locally at `/tmp/coc_sheet.html` for future use

**Problems solved:**
- Dodge displaying wrong base value — formula bug from session 1; fixed with `Math.floor(DEX/2)`
- WebFetch couldn't fully parse the 1.2MB Roll20 HTML — resolved by downloading with curl and grepping section-delimited blocks directly

**Approaches discussed:**
- Using Roll20's sheet.json as a data source — it turned out to be metadata only; the actual game data is in `coc_7th_ed.html`
- Era card CSS specificity: ID selectors (100) override `.era-card` class (10) so per-card backgrounds/colors needed explicit ID-scoped rules including hover and selected states

**Left unresolved:**
- Occupation skill lists not yet validated against Roll20 source
- Additional skills in Roll20 (Demolitions, Hypnosis, Read Lips, Artillery etc.) — eras/bases not yet reviewed
- App not browser-tested since era theme changes and skill fixes applied
- Equipment step still a placeholder

**Files changed this session:**
 index.html | 144 insertions, 13 deletions (across 3 commits)

---

## Session — 2026-04-27 (session 1)

**Focus:** Project initialization. Set up directory, context files, and starter index.html ported from Delta Green project.

**Decisions made:**
- Three eras: Modern, 1920s, Victorian England — user specified
- 7th Edition rules — user specified
- Same architecture as Delta Green: single-file vanilla HTML/CSS/JS, localStorage, Vercel
- Ported from Delta Green: dark terminal aesthetic, wizard step framework, save/load system, URL sharing, JSON export/import, print CSS framework, play panel structure
- Rebuilt for CoC: stats (STR/CON/SIZ/DEX/APP/INT/POW/EDU), derived stats (HP/SAN/Luck/MP/Build/DB/Move), occupation system with EDU×4 skill points, personal interest pool (INT×2), CoC skill list with base values, era-specific content

**Files changed this session:**
 index.html          | (new)
 project-context.md  | (new)
 project-memory.md   | (new)

---

<!-- end-of-day skill appends new sessions here -->
