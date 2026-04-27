# Project: Call of Cthulhu Character Creator
Last updated: 2026-04-27 by Claude Code

## What this project is
A browser-based character creation tool for the Call of Cthulhu 7th Edition tabletop RPG.
Users fill out a wizard covering era, personal info, stats, occupation, skills, and equipment,
then get a formatted investigator sheet they can print or save as PDF.
Supports three eras: Modern, 1920s, and Victorian England.
Characters can be saved locally and shared via URL.

## Architecture decision
**Does this app need to save data between sessions or between users?**
- [x] Yes — via localStorage (same device) and shareable URLs (between users)

**Does this app need to hide secrets from the browser?**
- [x] No

**Current answer:** Static frontend — no backend needed. localStorage handles
saves; URL encoding handles sharing.

## Tech stack
| Layer | Tool/Language | Notes |
|---|---|---|
| Frontend | HTML/CSS/JS (single file) | No framework — keep it vanilla |
| Backend | None | Not needed |
| Database | localStorage | Browser-native, no server |
| Auth | None | No accounts |
| Deployment | Vercel | Free tier, static hosting |
| AI / APIs | None | None planned |

## Data model
Single Investigator object — era, personal info (name, occupation, residence, birthplace,
age/DOB, sex, appearance), stats (STR/CON/SIZ/DEX/APP/INT/POW/EDU), derived stats
(HP, Sanity, Luck, Magic Points, Build, Damage Bonus, Move Rate), selected occupation +
skill point distribution, personal interest skills, equipment/weapons. Stored as JSON in
localStorage and encoded in shareable URLs.

## Era differences
| Era | Setting | Key notes |
|---|---|---|
| Modern | Present day | Credit Rating, modern equipment |
| 1920s | 1920s classic | Period equipment, Jazz Age flavour |
| Victorian | 1890s England | Period equipment, pre-modern medicine |

## Stats (CoC 7th Edition)
STR, CON, SIZ, DEX, APP, INT, POW, EDU
- Most rolled 3D6×5; SIZ and INT rolled (2D6+6)×5
- EDU rolled (2D6+6)×5; optional EDU improvement rolls for older investigators
- Luck rolled separately 3D6×5 (not used for most checks, can be spent)

## Derived stats
- HP = (CON + SIZ) ÷ 10 (round down)
- Sanity = POW × 5 (max = 99 − Cthulhu Mythos%)
- Luck = 3D6 × 5 (rolled separately)
- Magic Points = POW ÷ 5 (round down)
- Build & Damage Bonus: from STR+SIZ table
- Move Rate: from STR/DEX/SIZ comparison table

## Skill system
- ~50 skills with base values per era
- Occupation skill points = EDU×4 (or variant formula per occupation)
- Personal Interest skill points = INT×2
- Hard success = skill÷2; Extreme success = skill÷5
- Improvement: tick on fail; roll over skill at end of session to advance (+1d10%)

## User flow
User selects era → fills personal info → rolls/assigns stats → picks occupation →
distributes occupation skill points → distributes personal interest points →
adds equipment → views investigator sheet → saves/shares

## Where we left off
Last commit: (initial setup)
In progress: index.html starter file
Branch: main

## What's next

### Phase 1 — Core wizard
- [ ] Era selection step (Modern / 1920s / Victorian)
- [ ] Personal info step (name, occupation title, residence, birthplace, age, sex, appearance)
- [ ] Stats step — roll 3D6×5 / (2D6+6)×5, Quick Assign, manual entry; show derived stats live
- [ ] Occupation step — pick from occupation list, see skill point formula, priority stats highlighted
- [ ] Skill distribution step — spend occupation points + personal interest points on skills
- [ ] Equipment step — weapons and gear

### Phase 2 — Sheet & Print
- [ ] Investigator sheet render (all sections)
- [ ] Print/PDF layout (clean page breaks, compact skill grid)

### Phase 3 — Save/Load & Sharing
- [ ] localStorage save/load with named slots
- [ ] JSON export/import
- [ ] URL sharing (?c= base64)

### Phase 4 — Play & Advancement
- [ ] Play panel (HP/SAN/Luck/MP trackers, skill chips, weapons)
- [ ] Improvement ticks + end-of-session roll (+1d10 on success)
- [ ] Downtime/Between Adventures panel

## File structure
```
/Call of Cthulhu Project
  index.html         → single-file app
  project-context.md → project status
  project-memory.md  → session memory
```

## Environment and credentials
- .env file: not needed
- Variables needed: none
- Where secrets are stored: N/A

## Key decisions made
- 2026-04-27 — Project initialized
- 2026-04-27 — Architecture: Static frontend (localStorage + URL sharing, no backend) — same as Delta Green project
- 2026-04-27 — Tech stack: vanilla HTML/CSS/JS, Vercel — same as Delta Green project
- 2026-04-27 — Three eras: Modern, 1920s, Victorian England
- 2026-04-27 — 7th Edition rules

## Change log
- 2026-04-27 — Project initialized — Source: Claude Code
