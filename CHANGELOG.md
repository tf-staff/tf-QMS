# TF-QMS — Change Log

**Standing instruction (humans and AI assistants alike):** every change to this
Power-Up — the CORE FRAMEWORK script block in `index.html`, any module
script block, the shell/CSS, this file's format, anything — gets an entry
here **before the change is considered
complete**. Each entry must include the date, the author (person or AI
assistant name), a short summary of what changed and why, and the specific
file(s) touched. Bump `QMS.VERSION` in the CORE FRAMEWORK script block of
`index.html` alongside each entry; the "Power-Up Info" board button
surfaces the version and the newest entry's Summary line to end users in
Trello, and it parses this file — keep the `Date:` / `Author:` / `Summary:` /
`Files:` field format below.

Newest entries at the top. Entries are never rewritten or deleted.

---

## v1.3 — 2026-07-07
**Author:** Claude (with Steven Lucero)
**Summary:** Bug fix — card-back QMS section flashed white and jittered every few seconds (reported live, dark mode). Root cause: QMS.url() appended cb=Date.now() to every internal URL, including the one returned by the card-back-section capability. Trello re-evaluates capabilities every few seconds and on data changes; each evaluation produced a different URL, so Trello reloaded the section iframe each time (white paint during reload + sizeTo height re-negotiation). Fix: internal iframe URLs now carry only v=VERSION, which is byte-stable within a release and changes on deploy — the correct cache-bust. The one-shot CHANGELOG.md fetch in the Info popup keeps its timestamp (a fetch, not a capability iframe; always-fresh changelog is desired). Header comment updated so the lesson is recorded next to the URL convention. The section body's transparent background was inspected and left as-is — it is intentional, matching Trello's card-back color.
**Files:** index.html, CHANGELOG.md

## v1.2 (docs) — 2026-07-07
**Author:** Claude (with Steven Lucero)
**Summary:** Added USAGE.md — staff-facing usage guide (daily workflows per board, badge glossary, append-only/edit-window rules, storage roll-up explanation, editor checklist). Documentation only; index.html untouched, so QMS.VERSION stays 1.2 (no connector cache to bust).
**Files:** USAGE.md, CHANGELOG.md

## v1.2 — 2026-07-07
**Author:** Claude (with Steven Lucero)
**Summary:** Domain-knowledge iteration — the generic scaffold now reflects Tech Foundry operations and standard QMS practice for a fab/prototyping facility. Equipment: equipment-category presets matching the actual TF fleet (CNC, FDM, SLA, SLS/MJF, PolyJet, laser cutter, metrology, post-processing) with suggested intervals and PM checklist hints in the log form; calibration events capture an as-left result (in tolerance / adjusted / out of tolerance) and nudge (soft, non-blocking) for a cert link when logging a calibration without one. Intake: services-requested multi-select from the TF service catalog, Site (GBSF / Aggie Square), billing reference (chart string / PO / grant fund) with a soft nudge when accepting Recharge work without one, required decline reason, and a second orange RUSH card badge; Intake Queue sorts rush first. Inspection: fixed Hold/Fail reason-code taxonomy (required, so failure patterns are countable) with free-text details, and an optional measurement-tool field tying dimensional checks to a calibrated instrument. Core: log compaction stage 1 also strips the tool field. New .ckgrid CSS for the services checkboxes. All new fields are optional-by-default and backward compatible with cards created under v1.0/1.1.
**Files:** index.html, CHANGELOG.md, README.md

## v1.1 — 2026-07-07
**Author:** Claude (with Steven Lucero)
**Summary:** Consolidated to single-file layout to match the TF Power-Up family chassis (TF-Recharge / TF-Message / TF-Plan). js/core.js and the three module files are now ordered script blocks inside index.html; manifest.json dropped (capabilities are configured in the Trello Power-Up admin UI — see README). No functional changes: storage schemas, badges, whitelist, log compaction, and due-date sync are identical to v1.0. New modules are added as a new script block calling QMS.register(); existing blocks are never edited for that.
**Files:** index.html, CHANGELOG.md, README.md (removed: js/*, manifest.json)

## v1.0
Date: 2026-07-07
Author: Claude (with Steven Lucero)
Summary: Initial build — modular framework with per-board module activation, Card Type classification gate on Daily Operations, Equipment Maintenance & Calibration Logs (badge, auto Next Due, native due-date sync via optional REST authorization, append-only log, Maintenance Overview), Project Intake (guided form, status badge, Intake Queue), Inspection & Verification (append-only history, second badge, Hold & Fail Overview), self-governing per-board whitelist with in-app seeding, Power-Up Info button, and this change log.
Files: index.html, js/core.js, js/mod-equipment.js, js/mod-intake.js, js/mod-inspection.js, manifest.json, CHANGELOG.md, README.md
