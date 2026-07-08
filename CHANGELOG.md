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

## v2.0 — 2026-07-07
**Author:** Claude (with Steven Lucero)
**Summary:** Structural simplification, prompted by live testing: forms still dead after v1.6, board cluttered by badges, board menu cluttered by five buttons. Root learning — the family precedent (TF-Recharge) was re-read and its actual pattern adopted: the card-back section NEVER launches UI from inside its iframe; it is a read-only summary, and the capability's `action: {text:'Open'}` (rendered by Trello in the section header, executing in connector context) opens ONE editor modal where modals work unconditionally. v1.0–1.6 deviated by putting launch buttons inside the section and calling t.popup from there, which is unreliable from embedded iframes regardless of URL signing. Changes: (1) NEW editor modal (?view=editor) — Card Type control plus every module's form (intake; maintenance with due-date-sync authorize; inspection), each with a recent-entries list for within-window edits; sections are now read-only summaries with full history display. (2) ONE board button "TF-QMS" opening a hub modal with tabs: board-appropriate overviews, Access, Info (was five buttons). (3) Exception-based badges, at most one per card, quiet when healthy: ops priority Inspection FAIL > On HOLD > RUSH > New intake > nothing (accepted+passing, declined, internal); equipment shows only OVERDUE / Due soon / No maint log, nothing when green. (4) card-buttons capability removed — Internal cards now show a minimal one-line section whose Open action reaches the Card Type control, making reclassification self-discoverable (deviation from the original spec's "no QMS UI on internal cards", traded for findability; flagged for review). (5) Dead popup machinery removed (QMS.pop, classify popup view, per-module popup view registrations). Storage schemas unchanged; all existing card data reads back identically. Capabilities are now three: card-badges, card-back-section, board-buttons — card-buttons can be unchecked in the admin.
**Files:** index.html, CHANGELOG.md, README.md, USAGE.md

## v1.6 — 2026-07-07
**Author:** Claude (with Steven Lucero)
**Summary:** Fixed "Enter Intake Info" (and every other section-launched form) doing nothing on click. Root cause: t.popup called from a non-connector iframe requires a signed URL; all six popups opened from inside the card-back section (Project Intake, Log/Edit Maintenance Event, Log/Edit Inspection Result, and the card-type "change" link) passed unsigned URLs, so Trello rejected them via an uncaught promise rejection — visually a silent no-op. Popups opened from connector context (board buttons, the Internal-card "QMS: Card Type" button) already signed and were unaffected, which is why some UI worked and some didn't. Added QMS.pop(), a shared opener that signs per click (one-shot URLs — the per-poll caching rule from v1.4 applies only to capability-returned iframes) and logs any rejection to the console instead of swallowing it; all six call sites now use it.
**Files:** index.html, CHANGELOG.md

## v1.5 — 2026-07-07
**Author:** Claude (with Steven Lucero; responding to a student-authored bug report)
**Summary:** Reviewed the student bug document against the actual source, per its own instruction. BUG 1 (panel flicker, unusable buttons) — hypothesized timer loop REFUTED: no setInterval exists and the only setTimeout fans are one-shot ladders (theme attribute, sizeTo); hypothesized badge-capability coupling REFUTED: badges share no render path with the section. The real residual mechanism: the section's t.render callback did a full innerHTML rebuild on every Trello render event (Trello fires these liberally — board activity, context sync, our own writes echoing back), destroying buttons mid-click; and QMS.fit blind-fired sizeTo four times per render. Fixed: (a) snapshot-skip — render() now fetches the card's full shared blob in one call, serializes [whitelist, member id, blob], and leaves the DOM untouched when unchanged (verified: 5 no-change render events → 0 rebuilds; 1 real change → exactly 1 rebuild); user actions pass force=true; (b) QMS.fit only calls sizeTo when measured content height changed by >2px. BUG 2 (Card Type control invisible to whitelisted user on internal cards) — hypothesized private-scope storage REFUTED: zero private-scope usage in the file, qt is card/shared, whitelist is board/shared; hypothesized unresolved-async REFUTED: card-buttons returns its full promise chain, which Trello awaits; hypothesized identifier mismatch REFUTED: one shared QMS.isWl comparing member id to member id on every path. No code defect found; likely real-world causes are per-board whitelist membership, the card-buttons capability being disabled, or Trello's card layout tucking Power-Up buttons into the actions group. Shipped hardening + diagnosis: id comparison now String-coerced (insurance, not a found bug), and the Manage QMS Access panel states directly whether YOU are authorized on THIS board and where the Internal-card button lives. USAGE.md troubleshooting expanded accordingly.
**Files:** index.html, USAGE.md, CHANGELOG.md

## v1.4 — 2026-07-07
**Author:** Claude (with Steven Lucero)
**Summary:** Follow-up to v1.3 — flashing persisted on the live board because a second URL-variance source remained: the card-back-section capability called t.signUrl() on every evaluation, and signUrl embeds a fresh signed token each call, so the URL still differed between Trello's polls and the iframe still reloaded. The section URL is now signed once and cached per card + kind + version (QMS._secUrl); a deploy or card switch re-signs naturally, and one-shot popups/modals continue signing per click. Also fixed the classify-view scrollbar: the capability's initial height (110) was below actual content, so each poll re-applied a too-short height that fought t.sizeTo — initial heights raised to match real content (classify 155, job section 360) and the section document now clips overflow, since sizeTo owns the height. Enabled native due-date sync by embedding the public app key in QMS.APP_KEY (public identifier, not a secret; per-member token authorization unchanged). Reminder recorded: the API key's Allowed Origins must include https://tf-staff.github.io or the authorize popup will not open.
**Files:** index.html, CHANGELOG.md

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
