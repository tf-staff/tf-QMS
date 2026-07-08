# TF-QMS — Usage Guide

*For Tech Foundry staff using the Power-Up day to day. Setup and administration
live in README.md; change history in CHANGELOG.md. Current as of v2.0.*

**The one interaction to learn:** every card's QMS panel is a read-only
summary. To edit anything — intake, inspections, maintenance, card type —
click **Open** in the panel's header. That opens the editor, the single
place all changes happen. The board menu likewise has a single **TF-QMS**
button containing the overviews, access management, and version info.

TF-QMS adds three quality-system functions to the two boards you already work
in. Nothing about how you move cards changes — the Power-Up rides along on the
cards, adding a badge when something needs attention, a QMS panel on the
card back (edited via its **Open** action), and one **TF-QMS** board button.

**One rule above all: Trello is the index, Box is the record.** Certs,
inspection reports, photos, and archived histories live in Box; cards carry
the status and the links.

---

## The two boards

| Board | What TF-QMS does there |
|---|---|
| **UC Davis Tech Foundry Daily Operations** | Project Intake + Inspection & Verification, on Job/Request cards only |
| **Equipment and Maintenance** | Maintenance & Calibration tracking, one card per machine |

## Who can write

Reading is open to everyone on the board. Writing (logging events, editing
intake, classifying cards) is limited to QMS-authorized members — the list is
self-managed via **TF-QMS → Access** on the board menu. If editor controls are
greyed out with "QMS-authorized members only," ask any current QMS member to
add you there. This is a workflow guardrail, not security — treat it as a
"who's trained" list.

---

## Daily Operations board

### First: classify the card

New cards show a grey **Classify this card** badge. Open the card and pick:

- **Job / Request** — client or internal work that flows through intake and
  inspection. Gets the full QMS treatment.
- **Internal** — team tasks, admin, reminders. QMS leaves these alone
  entirely (no badges, no section).

Misclassified something as Internal? Internal cards keep a one-line QMS
panel — its **Open** action reaches the Card Type control.

Job cards carry **at most one badge**, and only when action is needed:
**Inspection FAIL** (red) → **On HOLD** (orange) → **RUSH** (yellow) →
**New intake** (grey). Accepted work that's passing shows nothing — a
quiet card is a healthy card.

### Project Intake (when a request arrives)

Open the card → **Open** on the QMS panel → **Project Intake** form:

- **Requester** and **Intake date** (required)
- **Project type** — Recharge / Grant / Class / Internal Lab / Other
- **Services requested** — check all that apply (jobs often span print +
  finish, scan + print)
- **Site** — GBSF or Aggie Square
- **Priority** — Rush adds a loud orange **RUSH** badge to the card front and
  floats the card to the top of the Intake Queue
- **Requested due date**
- **Billing reference** — chart string / PO / grant fund
- **Intake status** — New / Accepted / Declined

Two behaviors to know: **declining requires a reason** (it stays on the card
as the record of why), and **accepting Recharge work with no billing
reference asks you to confirm** — un-invoiceable work shouldn't slip into the
queue silently. Click Save a second time if you genuinely mean to.

**TF-QMS → Intake Queue** on the board menu lists every Job/Request card sitting
at New, rush first, oldest first.

### Inspection & Verification (when work gets checked)

**Open** on the QMS panel → **Inspection & Verification** form:

- **Result** — Pass / Fail / Hold
- **Hold/Fail reason** — required for Hold and Fail, picked from a fixed list
  (dimensional out of tolerance, finish defect, machine failure, awaiting
  client, equipment down, …) plus free-text details. The fixed codes are what
  let us count failure patterns later — "what fails most, and is it us, the
  machine, or the client?"
- **Measurement tool used** — optional but valuable: "Calipers TF-CAL-01"
  ties a dimensional pass to a calibrated instrument on the equipment board.
- **Notes** and an optional Box link (report, photos, CMM data).

Your name and the date are recorded automatically. The card badge follows the
most recent result: green Pass, red Fail, orange Hold. The **Hold & Fail
Overview** tab (board menu → TF-QMS) gathers everything stuck, failures first.

Inspection history is **append-only**: you can edit *your own* entry for
24 hours (typo window), after which it's permanent. Never "fix" the past —
log a new result; the current status always follows the newest entry.

---

## Equipment and Maintenance board

Each machine is a card. Badges only appear when something needs you —
**a quiet card is a healthy card**:

- **Red** — overdue
- **Yellow** — due within 14 days
- **Grey** — nothing logged yet
- *(no badge)* — up to date; next-due lives on the card panel

### Logging a maintenance or calibration event

**Open** on the QMS panel → **Maintenance & Calibration** form:

- **Equipment category** — pick the machine class (CNC, FDM, SLA, SLS/MJF,
  PolyJet, laser cutter, metrology, post-processing). This suggests a typical
  interval and shows a checklist hint of what that class usually needs —
  suggestions only; you're on the machine, you outrank the checklist.
- **Event date**, **Maintenance type** (Calibration / PM / Repair /
  Inspection), and **interval** — the interval drives the auto-calculated
  Next Due. Next Due is never typed by hand; it's always Last Serviced +
  Interval, so it can't drift.
- **Calibration events** additionally ask for the as-left result: found in
  tolerance / adjusted / out of tolerance. Logging a calibration without a
  cert link gets a one-time "are you sure" — the Box cert link *is* the
  traceability chain, especially for calipers and gauges.
- **Notes** and an optional Box link.

Laser cutters: the checklist hint includes extraction/filtration flow — that
one is also a fire-safety control, so log it every time.

**TF-QMS → Maintenance Overview** lists everything yellow or red,
most-overdue first. Same append-only and 24-hour edit rules as inspections.

If you see an "Enable" prompt about due-date sync in the section, clicking it
(once, per person) lets logged events also set Trello's native due date so
you get Trello's own reminders. Optional; everything works without it.

---

## Things that look odd but are correct

- **"…N older entries rolled up…"** in a log: Trello caps how much data a
  card can hold. When a log gets long, the oldest entries lose their notes
  first, then collapse into a visible roll-up line with a date range —
  nothing is ever deleted silently. When you see the storage warning, export
  the full history to Box and link it on the card.
- **Edit link disappeared from my log entry:** the 24-hour author window
  closed. Add a new entry instead.
- **Buttons greyed out:** you're not on the QMS access list for that board —
  see "Who can write" above.
- **Can't change an Internal card's type:** use **Open** on its one-line
  QMS panel — the Card Type control is in the editor. If the buttons are
  disabled, authorization is **per-board**: check **TF-QMS → Access** on the
  board you're standing on; it tells you directly whether *you* are
  authorized there.
- **A change I just deployed isn't showing:** Trello caches connectors
  aggressively. Editors: bump `QMS.VERSION`; users: hard-refresh, or
  disable/re-enable the Power-Up on the board if it persists.

## For anyone editing the code

One file: `index.html`. Before any change is complete, add a CHANGELOG.md
entry (date, author, summary, files) and bump `QMS.VERSION` in the CORE
FRAMEWORK script block — **TF-QMS → Info** shows users the
version and the newest changelog summary, and the version bump is also what
busts Trello's cache. New modules are new script blocks; existing blocks
don't get touched for that. Full guide in README.md.
