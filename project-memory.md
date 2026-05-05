# Project Memory
Last updated: 2026-04-28

This file captures decisions, reasoning, and session context that
project-context.md doesn't hold. It is Claude's memory between sessions.

---

## Session — 2026-05-05

**Focus:** Victorian era implementation — full skill list, 23 occupations, era-aware base values sourced from Cthulhu by Gaslight PDF (pages 69–88).

**Decisions made:**
- Victorian era uses identical core CoC 7e mechanics (same stat rolls, derived stat formulas) — only skill list and occupation set differ from Modern/1920s
- Era-specific base values use string token sentinels in `getSkillBase()`: ACCOUNTING (5 modern+1920s / 10 victorian), ELEC_REPAIR (10/1), MECH_REPAIR (10/20), RIDE_BASE (5/20), SWIM_BASE (20/30)
- REASSURE is Victorian-only with base = APP÷5 — handled by REASSURE sentinel in `getSkillBase()`
- Occupation formulas needing "higher of two stats" use pipe syntax: `(STR|DEX)×2`; `calcOccPts()` extended with regex `\(([A-Z|]+)\)×(\d+)` to extract and max the listed stats
- Three-tier occupation structure: `eras:['all']` (8 universal), `eras:['modern','1920s']` (17), `eras:['victorian']` (23)
- 23 Victorian occupations sourced from Cthulhu by Gaslight pages 77–88 (user-provided PDF)
- Victorian rules validation is now complete — this item resolved

**Left unresolved:**
- Browser test all three era themes end-to-end
- Verify Pulp Cthulhu archetype/talent mappings against physical book
- Additional skills audit (Demolitions, Hypnosis, Read Lips, Artillery — eras/bases)
- Engineer occupation chooseFrom still includes Electronics (Modern-only) — Victorian engineers have fewer valid choices

**Files changed this session:**
 index.html | 232 +++++++++++++++++++++++++++++++++--------------------
 1 commit: 5064091 Victorian era: full skill list, 23 occupations, era-aware base values

---

## Session — 2026-04-28

**Focus:** AI portrait feature, weapons & equipment panel, step order flip, occupation selection bug, derived stats fix, occupation choose-from skill picker, Pulp Cthulhu mode.

**Decisions made:**
- Confirmed 3 eras (Modern, 1920s, Victorian); Victorian PDF pending for rules validation — noted in project memory
- AI portrait: era-specific backdrop language in assembled prompt (Modern = contemporary urban; 1920s = Jazz Age/prohibition; Victorian = gas-lit London fog)
- Portrait upload widget added to Personal Info step, mirroring Delta Green implementation
- Weapons catalog: 35 weapons across 6 categories (Brawl, Edged, Thrown, Handguns, Rifles/Shotguns, SMGs), era-filtered at render time; catalog collapses until expanded
- Step order swapped: Occupation now before Characteristics — matches CoC rulebook flow (pick concept first, then roll stats)
- Occupation onclick bug root cause: `JSON.stringify(name)` wraps names in double quotes inside a double-quoted HTML `onclick="..."` attribute, silently breaking every click — fixed by switching to single-quoted HTML attribute
- Sanity formula corrected: was `POW × 5` (gave values like 300 when POW=60); correct CoC 7e rule is Sanity = POW directly (POW is already stored as a percentage value from 3D6×5 roll)
- Occupation choose-from skills: interactive chip buttons in occupation detail box, capped at `chooseN` selections, chosen skills feed into Skills step as occupation skills (highlighted, point-assignable)
- CoC 7e has no "preferred stat" bonus mechanic like Delta Green — the occupation's skill point formula (EDU×4, EDU×2+APP×2, etc.) is the only mechanism encoding which stats matter
- Pulp Cthulhu: global toggle on Era step; 28 archetypes each with name, bonus characteristic, and talent pool; 29 talents each with category and rule description; archetype+talent section renders on printed sheet
- Pulp data from training knowledge (not from linked GitHub repo, which was personal sci-fi campaign material unrelated to the supplement)

**Problems solved:**
- Occupation cards not selectable: double-quote collision in onclick attribute — single-quoted attribute fix
- Sanity displaying values like 300: POW×5 when POW is already a percentage — corrected to = POW
- GitHub repo markjlyon/cocpulp linked by user contained sci-fi campaign characters, not Pulp Cthulhu rulebook data — implemented from training knowledge instead

**Left unresolved:**
- Victorian rules validation: pending PDF reference from user
- Engineer occupation `chooseFrom` still includes Electronics (Modern-only) and Pilot (Modern/1920s) — leaves Victorian engineers with fewer valid choices; revisit after Victorian PDF arrives
- Pulp Cthulhu archetype/talent pool mappings need verification against the physical Pulp Cthulhu book
- Browser test all three eras end-to-end not done
- Additional skills from Roll20 (Demolitions, Hypnosis, Read Lips, Artillery) not yet reviewed

**Files changed this session:**
 index.html | 600+ insertions across 6 commits (portrait, weapons, step swap, occ fix, sanity fix, pulp)

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
