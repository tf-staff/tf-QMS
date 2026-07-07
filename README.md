# TF-QMS — Trello Power-Up for the Tech Foundry Minimum Viable QMS

Three QMS functions across two boards, on the same chassis conventions as the
rest of the TF Power-Up family (TF-Recharge, TF-Message, TF-Manufacture,
TF-Plan): a single hosted page routing by `?view=`, version-stamped internal
iframe URLs to defeat Trello's connector caching, dark/light theme adaptation,
and Trello's built-in Power-Up storage — no external database. Trello is the
tracking/visibility layer; **Box remains the source of truth for controlled
documents.**

| Module | Board | What it adds |
|---|---|---|
| Equipment Maintenance & Calibration | Equipment and Maintenance | Last Serviced, Interval (30/60/90/180/365/custom), auto Next Due, append-only maintenance log, green/yellow/red badge, native due-date sync, Maintenance Overview button |
| Project Intake | Daily Operations (Job/Request cards) | Requester, Project Type, Intake/Requested dates, Priority, Intake Status, guided edit form, status badge, Intake Queue button |
| Inspection & Verification | Daily Operations (Job/Request cards) | Append-only inspection history, current status + inspector + reason, second badge, Hold & Fail Overview button |

Plus, in the core framework (not a module): the **Card Type** gate on Daily
Operations (Job/Request vs Internal — internal cards show no QMS UI), the
per-board **QMS whitelist**, **Manage QMS Access**, and **Power-Up Info**.

## File map

```
index.html     the entire Power-Up: CSS theme, view containers, boot
               router, then ordered <script> blocks — CORE FRAMEWORK
               (module registry, board→module map, Card Type, whitelist,
               connector capabilities, view router, access/overview/
               info/classify views, shared helpers) followed by one
               block per module (Equipment Maintenance, Project Intake,
               Inspection & Verification), each self-contained.
CHANGELOG.md   mandatory change log (see standing instruction inside).
               The in-app "Power-Up Info" button fetches this file live.
USAGE.md       staff-facing usage guide (day-to-day workflows).
README.md      this file (setup & administration).
```

There is no `manifest.json`: the connector URL and capability list are
configured in the Trello Power-Up admin UI (step 2 below).

## Setup

### 1. Host the static files
Push this folder to the `tf-staff` GitHub account as a new repo (suggested:
`tf-qms`) with GitHub Pages enabled on the main branch, same as the other TF
Power-Ups. The connector URL becomes:

```
https://tf-staff.github.io/tf-qms/index.html
```

(If the repo name differs, update the connector URL in the Trello Power-Up
admin UI accordingly — and log it in CHANGELOG.md.)

### 2. Register the Power-Up
At <https://trello.com/power-ups/admin>: **New** → name `TF-QMS`, pick the
Tech Foundry workspace, set the **Iframe connector URL** to the URL above.
Under **Capabilities**, enable exactly: `card-badges`, `card-back-section`,
`board-buttons`, `card-buttons`.

### 3. Enable on the two boards
On each of **UC Davis Tech Foundry Daily Operations** and **Equipment and
Maintenance**: board menu → Power-Ups → Custom → add **TF-QMS**. Do not enable
it on the other two boards ("Notes, Quotes, Improvement, Storage" and
"Completed TF Projects") — they are out of scope and the framework will show
nothing there anyway (unmapped boards get no QMS UI).

### 4. Pin the board IDs
The framework first matches boards by pinned ID, falling back to name patterns
(`/daily operations/i`, `/equipment/i`) so it works out of the box. Name
matching is brittle against renames, so pin the IDs right away: open each
board, copy the ID from the URL (`trello.com/b/<shortlink>` → use the long ID
shown in the board's JSON at `trello.com/b/<shortlink>.json`, field `id`), and
paste into `QMS.BOARD_IDS` in `the CORE FRAMEWORK script block in index.html`. Commit, bump nothing (config, not
behavior — but a CHANGELOG entry is still required per the standing
instruction).

### 5. Seed the whitelist (per board — do this immediately)
The whitelist is self-governing once seeded. On each board, open **Manage QMS
Access** from the board buttons: while the whitelist is empty, the form offers
a one-click **"Add myself as first QMS member"** seed action to whoever opens
it first. Do this on both boards right after enabling, then add Valerie,
Tristan, and anyone else who should have QMS write access. (Alternative manual
seed, if ever needed: from the board with the Power-Up open, run
`t.set('board','shared','qmsWl',[{id:'<memberId>',n:'<Name>'}])` in the
connector iframe's console.)

A member may be whitelisted on one board and not the other — the lists are
independent, stored in each board's Power-Up shared storage.

### 6. Optional: native due-date sync (Equipment module)
Logging a maintenance event always recalculates **Next Due** and the badge.
To also mirror Next Due onto Trello's **native card due date** (so assigned
members get Trello's own reminders), the Power-Up needs the Trello REST API:

1. In the Power-Up admin page, open the **API Key** tab and generate/copy the key.
2. Paste it into `QMS.APP_KEY` in `the CORE FRAMEWORK script block in index.html` (and log the change).
3. Each staff member clicks **Enable** once in the Maintenance section when
   prompted, granting a write token stored by Trello for that member.

If `APP_KEY` is left blank or a member hasn't authorized, sync is skipped
gracefully and everything else still works.

## How this maps to QMS practice at the Tech Foundry

TF-QMS is deliberately a *minimum viable* QMS layer over the boards you already
run — not an ISO 9001 registration project. The v1.2 fields encode the specific
practices a facility like this gets asked about:

- **Calibration traceability.** Metrology (calipers, mics, gauges) defaults to a
  365-day external calibration cycle; each calibration event records an as-left
  result (found in tolerance / adjusted / out of tolerance — limited) and the
  form nudges for a cert link. The Box link *is* the traceability chain — the
  card is the index, Box is the record.
- **Preventive maintenance by equipment category.** Category presets carry the
  checks that actually matter per machine class (laser optics + extraction flow,
  SLA tank/window, MJF printheads/sieve, CNC coolant concentration & tram…).
  They're suggestions in the form, never enforced — the technician on the
  machine outranks the checklist.
- **Countable failure modes.** Inspection Hold/Fail requires a reason *code*
  from a fixed taxonomy, with free-text details alongside. Codes are what let
  you ask "what fails most, and is it us, the machine, or the client?" —
  free text alone never adds up.
- **Measurement-tool linkage.** An inspection can name the instrument used
  (e.g. "Calipers TF-CAL-01"), connecting a dimensional pass to a calibrated
  tool — the question every reviewer eventually asks.
- **Recharge hygiene.** Accepting Recharge work with no billing reference
  (Aggie Enterprise chart string / PO / grant fund) triggers a confirm-to-
  proceed nudge: un-invoiceable work shouldn't enter the queue silently.
  Declines require a reason — the card keeps the record of *why*.
- **Two-site awareness.** Intake carries a Site field (Davis — GBSF /
  Sacramento — Aggie Square) so queue reviews and workload splits don't need
  tribal knowledge.
- **Document control stays in Box.** Trello is the tracking and visibility
  layer; controlled documents, certs, inspection reports, and archived full
  histories live in Box, linked from cards.

Where this deliberately stops: no CAPA workflow, no risk register, no training
matrix (all future modules), and access control is a client-side UI restriction,
not server-side security — see the limitations section below.

## Operating notes and honest limitations

- **Whitelist is a client-side UI restriction only.** A Power-Up with no
  backend server cannot cryptographically enforce permissions: write-gated
  buttons render disabled for non-whitelisted members and the forms re-check
  before saving, which deters casual edits — but a determined user with board
  access and dev tools could write to Power-Up storage directly. This is the
  strongest enforcement the architecture provides; controlled records live in
  Box.
- **Append-only logs vs. Trello's storage cap.** Trello caps Power-Up storage
  values around 4 KB. Logs are byte-budgeted: entries are never deleted, but
  once a log approaches the cap, the *oldest* entries are progressively
  compacted — first notes and attachment links are stripped, then author
  display names (member IDs kept), and as a last resort the oldest compacted
  entries collapse into a visible roll-up stub ("N older entries rolled up,
  <date range>") so a write can never fail against Trello's hard limit and
  nothing ever vanishes without a trace. The section shows a warning to
  archive the full history to Box. For the
  expected event rates (a handful of maintenance events per machine per year)
  this ceiling is years away.
- **Author edit window.** Log entries are editable by their original author
  for 24 hours (`QMS.EDIT_WINDOW_MS`), then frozen; nothing is ever deletable.
  Corrections after the window = log a new entry.
- **Attachments** on log entries are links (Box URLs), optional and never
  required — the client SDK can't upload files without the REST API, and Box
  links keep the source of truth where it belongs anyway.
- **Internal cards show no QMS UI by design.** The one exception: whitelisted
  members see a small "QMS: Card Type" card button on Internal cards, which is
  the reclassification path (otherwise a misclassified card would be
  unreachable). Job cards get a "change" link in the section header instead.
- **No historical backfill.** All fields start blank and populate as events
  are logged going forward.
- **Stale connector code after a deploy?** Bump `QMS.VERSION` — every internal
  iframe URL is stamped with `v` + a cache-buster, the family-wide fix for
  Trello's aggressive connector caching. If the connector itself is stale,
  disable/re-enable the Power-Up on the board.

## Adding a future QMS module (CAPA, Risk Register, …)

1. Copy the PROJECT INTAKE `<script>` block (the smallest module) as a
   template into a **new** `<script>` block at the bottom of `index.html`,
   after the existing modules.
2. Implement the module object: `id`, `badges(t)`, `section(t, mount, ctx)`,
   optional `boardButtons` + matching `overviews` entries, and any
   `QMS.views.<form>` popup views. Use its own storage keys.
3. Register the id in `QMS.BOARD_MODULES` (CORE FRAMEWORK block) for the board
   kind(s) it applies to, and add one `<script>` tag in `index.html`.
4. Bump `QMS.VERSION`, add the CHANGELOG.md entry.

No existing module file changes. The Card Type gate and whitelist come for
free from the framework.
