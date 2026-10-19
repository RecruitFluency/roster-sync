# Coach Sync — Engineering Spec

**Status:** Draft for engineering review · **Owner:** TBD · **Reviewer:** Jen (workflow), Founder (scope)
**Target:** MVP in ~1 week; event-driven sync in ~3 weeks

---

## 1. Problem

Today, Jen manually researches and updates our coach records every month: finding staff changes across tracked programs, looking up new hires, and correcting titles and contact details by hand. This is slow, error-prone, and gets worse with every sport and level we add.

Magisterial's API gives us this data directly:

- `GET /v1/teams/{team_id}/coaches` — full staff per team: name, role/title, head-coach flag, season, bio URL, biography, headshot, profile slug, and (with authorization) email/phone.
- `GET /v1/movements` — published roster **and coaching-staff changes**, newest first, with observation/resolution/publication timestamps.
- `POST /v1/alerts` — standing watches with a filter DSL that push matches to a webhook (usage-billed, $5/alert/month).
- Every response carries a freshness object (`observed_at`, `checked_at`, `changed_at`, `next_check_at`, `cadence_hours`) so we know how stale a record is.

## 2. Goal

Replace the manual monthly research with an automated pipeline that produces a **reviewable change list**. Jen's job shifts from *finding* changes to *approving* them:

1. The system detects coach additions, departures, and title changes across all tracked programs.
2. Once a month (MVP) — later continuously — it generates a diff: `added / departed / changed`, with the evidence attached (movement event, freshness timestamps, bio link).
3. Jen reviews the diff, approves or flags each line, and approved changes are applied to our coach records.

Explicit non-goals for v1: no automatic writes without review, no contact-field (email/phone) syncing until the authorization scope is sorted out with Magisterial (see §7), no UI beyond the generated review document.

## 3. Architecture overview

```
                       ┌─────────────────────┐
  Magisterial API      │  sync job (monthly) │──► snapshots table
  /v1/teams/·/coaches ─┤                     │
  /v1/movements       ─┤  diff engine        │──► change_sets + review doc
                       └─────────────────────┘
  /v1/alerts webhook  ──► listener (phase 2) ──► pending events feed the next diff
                                                        │
                                              Jen approves / flags
                                                        │
                                              apply job ──► coaches (canonical)
```

Three components, buildable in order:

1. **Sync job** — pulls current staff for every tracked team, stores an immutable snapshot per run.
2. **Diff engine** — compares the latest snapshot against our canonical coach records, cross-references `/v1/movements` for corroborating events, and emits a change set.
3. **Review + apply** — renders the change set as a review document; approved rows update the canonical table with full audit history.

Phase 2 adds the **webhook listener**: standing alerts on coaching-staff movements so changes queue up as they happen and the monthly diff becomes a formality (or moves to weekly).

## 4. Data model (suggested — Postgres/Supabase)

```sql
-- Canonical, Jen-approved records. Never written directly by sync.
CREATE TABLE coaches (
  id             uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  magisterial_id text UNIQUE,            -- their coach ID
  person_id      text,                   -- their cross-program person ID
  team_id        text NOT NULL,          -- Magisterial team ID
  full_name      text NOT NULL,
  role_title     text,
  is_head_coach  boolean DEFAULT false,
  season         text,
  bio_url        text,
  status         text NOT NULL DEFAULT 'active',  -- active | departed
  approved_at    timestamptz,
  approved_by    text,
  created_at     timestamptz DEFAULT now(),
  updated_at     timestamptz DEFAULT now()
);

-- Raw API pulls, immutable. One row per coach per sync run.
CREATE TABLE coach_snapshots (
  id           uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  sync_run_id  uuid NOT NULL,
  team_id      text NOT NULL,
  payload      jsonb NOT NULL,           -- full API record incl. freshness object
  observed_at  timestamptz,              -- from freshness metadata
  pulled_at    timestamptz DEFAULT now()
);

-- One row per detected change, grouped into a change set per run.
CREATE TABLE coach_changes (
  id             uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  change_set_id  uuid NOT NULL,
  kind           text NOT NULL,          -- added | departed | title_changed | corrected
  coach_id       uuid REFERENCES coaches(id),   -- null for 'added'
  magisterial_id text,
  team_id        text NOT NULL,
  before         jsonb,
  after          jsonb,
  evidence       jsonb,                  -- movement event(s), freshness timestamps
  decision       text DEFAULT 'pending', -- pending | approved | rejected | flagged
  decided_by     text,
  decided_at     timestamptz
);
```

`coach_snapshots.payload` keeps the raw API response verbatim so we can re-derive anything later without re-querying (and it starts our own immutable archive of vendor data — a policy we want on every Magisterial integration).

## 5. Sync + diff logic

**Sync run (monthly cron → later weekly):**

1. Resolve tracked teams. Source of truth: a `tracked_teams` config table (sport, division, conference filters), populated once via `GET /v1/teams`.
2. For each team, `GET /v1/teams/{team_id}/coaches` for the current season. Respect rate limits (Developer plan: 300 req/min — a full run over even a few thousand teams fits in one job with modest throttling; use cursor pagination as returned).
3. Insert one snapshot row per coach. Record the run in a `sync_runs` table with counts and errors.

**Diff (same job, after snapshots land):**

- **Added:** `magisterial_id` in snapshot, absent from `coaches`.
- **Departed:** active in `coaches`, absent from the team's snapshot. Corroborate against `GET /v1/movements?kind=coach` before flagging — a missing record with no movement event and stale freshness (`checked_at` old) gets marked `needs_verification` rather than `departed`, so API gaps don't generate false departures.
- **Title changed / head-coach flag flipped:** same `magisterial_id`, differing fields. Diff only fields we display; ignore churn in bio text.
- Attach evidence: the matching movement event (with its observation/resolution timestamps) and the freshness object from the snapshot.

**Review document (MVP: generated Markdown committed to this repo, or a Slack post — Jen's call):**

One section per change kind, one line per change:

> **ADDED** · Jane Smith — Assistant Coach, W. Soccer, Example University (D2) · observed 2026-09-02 · [bio] · movement event `mv_…` · ✅ / ❌ / 🚩

Decisions get written back to `coach_changes.decision` (MVP: Jen edits the doc / reacts in Slack and an engineer applies; phase 2: a lightweight approve UI or a Supabase view with an editable decision column).

**Apply job:** takes approved rows, upserts `coaches`, stamps `approved_by`/`approved_at`. Rejected rows are kept — a rejection is signal about data quality we report back to Magisterial's corrections process.

## 6. Phase 2 — event-driven

- Register standing alerts (`POST /v1/alerts`) for coaching-staff movements scoped to our sports/divisions; point them at a webhook endpoint (e.g. a Supabase Edge Function).
- Webhook payloads land in a `movement_events` table; each event pre-stages a `coach_changes` row so the periodic diff mostly confirms what the events already queued.
- This is deliberately the same alert/webhook infrastructure the fit-intelligence products will need (portal watches, target-list ripples) — coach sync is the low-stakes proving ground for it.
- Cost note: alerts are $5/alert/month. One broad alert per sport/division beats many narrow ones; confirm filter-DSL expressiveness with Magisterial (question below) before designing the subscription set.

## 7. Open questions for the Magisterial call (Monday)

1. **Coach data coverage:** what fraction of programs in our sports have staff data, and how complete are role/title/tenure? (`GET /v1/coverage` walk-through.)
2. **Contact fields:** exact authorization requirements for coach email/phone, and whether RFX qualifies. Until then, contact fields are **out of scope** for sync.
3. **Movement latency:** typical delay from a real-world staff change to a movement event / alert firing; what `cadence_hours` looks like for coach records specifically.
4. **Alert filter DSL:** can one alert cover "all coaching-staff changes in sport X across divisions Y,Z", or do we need per-division/per-conference alerts (cost driver)?
5. **Season rollover:** how staff records behave at season boundaries — do we diff within a season, across seasons, or both?
6. **Corrections:** the process for reporting bad records (Jen will find them), and expected turnaround.

## 8. Acceptance criteria (MVP)

- [ ] One command/cron produces a complete snapshot of staff for all tracked teams, with run stats logged.
- [ ] The diff catches a known recent coaching change (validate against 2–3 changes Jen already knows about from her last manual pass).
- [ ] No false "departed" entries caused by API gaps: missing + no movement event + stale freshness ⇒ `needs_verification`.
- [ ] Review document is generated and Jen can complete a full monthly review from it alone, without visiting a single athletics website.
- [ ] Approved changes land in `coaches` with full audit trail; nothing writes to `coaches` without a decision.
- [ ] Jen's monthly time on coach updates drops from hours of research to a review session.

## 9. Rollout

1. **Pilot scope:** one sport, one division (suggest: the set Jen updated most recently, so she can verify against fresh memory).
2. Run the first sync + diff alongside Jen's normal manual pass for one cycle; compare outputs.
3. Expand to all tracked sports/divisions; retire the manual process.
4. Phase 2 webhooks once the Magisterial call answers §7 Q3–Q4.
