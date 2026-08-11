# RFX — UX/UI Evaluation & Product-Strategy Review

**Reviewer role:** Senior UX/UI Designer & Product Strategist
**Scope:** 5 screens of the athlete/parent experience — Home, My Recruitment (Dashboard), Program Pipeline, Update Stage modal, Notifications
**Date:** August 2026
**Lens:** Nielsen's usability heuristics, modern mobile patterns, conversion & trust design

> **Note on scope.** This review is based on the five static screens and the written descriptions provided. Where a judgment depends on behavior I can't see from a screenshot (loading states, error states, tap targets, whether a card is interactive), I've flagged it as an **open question** rather than an assumption.

---

## Executive summary

RFX has a **strong, familiar backbone**: a stage-based recruitment pipeline is exactly the right mental model, and the dark, focused visual language feels modern and premium. The core "track my schools through stages" loop is legible on first glance — that's the hard part, and it's largely working.

The biggest opportunities fall into four buckets:

1. **Navigation is hub-and-spoke.** Almost everything routes back through the Home menu, and "Home" is really an *account settings* screen wearing a dashboard's clothes. This costs taps and buries the product's most valuable surface (My Recruitment).
2. **The differentiator is invisible.** RFX's whole pitch is *"quantifying the unquantifiable"* — grit, character, culture fit via Vekt. None of that appears in the athlete's own app. The screens are a competent recruitment CRM that any competitor could ship. The thing that makes RFX *RFX* isn't on screen.
3. **Stage semantics are internally contradictory.** "Engaged = Offer received" collides with a later "Offer Extended = The program has made an offer," and "Stage 3 of 7" appears next to a 5-step timeline. For a product whose value is *trustworthy searchable data*, inconsistent taxonomy is an existential UX bug, not a cosmetic one.
4. **Red is doing too many jobs.** Brand, primary action, upgrade nudge, alerts, completed-state, and tags are all red. When everything shouts, nothing does. Visual hierarchy suffers most where it matters — deadlines and next actions.

None of these are hard to fix. The rest of this document is specific and prioritized, ending with a **Top 3 Quick Wins** and an **impact/effort roadmap**.

---

## What's already working (don't break these)

- **Pipeline-as-timeline** (Screen 3) is intuitive, and the **"YOU ARE HERE"** marker is a textbook example of *visibility of system status*. Keep it.
- **Deadline surfacing** ("Offer decision due Aug 12") on the dashboard card is genuinely valuable — it's the single most important thing on that screen.
- **The verification loop** (Screen 5: someone moves your stage → you *Approve* or flag *Not accurate*) is a thoughtful trust mechanic. Most competitors don't have this.
- **The bottom-sheet stage picker** (Screen 4) is the correct pattern, correctly executed — radio semantics, clear selected state, Cancel + confirm.
- **Dark, restrained aesthetic** reads as premium and keeps focus on content.

---

## The strategic gap worth naming first

Before the heuristic detail: **the athlete never sees their Vekt/character intelligence anywhere in this flow.** The product promises to map "grit, self-awareness, and culture fit into searchable intelligence." Yet the athlete's dashboard shows only GPA, acceptance rate, and pipeline stages — commodity recruitment data.

This is both an **engagement** miss and a **trust** miss:

- **Engagement:** The athlete has no reason to return daily, no sense of progress on the thing RFX uniquely measures, and no motivation loop. A visible **"Character Profile / Vekt Score"** (with a completeness meter and "answer 3 more questions to strengthen your profile") would create the habit the CRM alone won't.
- **Trust & upgrade rationale:** "Upgrade for More" is asking for money without showing the goods. If the athlete could *see* how their intangibles are surfacing them to coaches ("Your grit score put you in front of 3 D1 programs this month"), the value — and the upgrade — sells itself.

**Recommendation:** Give Vekt a home on the dashboard and a first-class node in the athlete's profile. Right now the app's marketing promise and its UI are two different products.

---

## 1. User Flow & Navigation

**Heuristics in play:** *Flexibility & efficiency of use · Recognition rather than recall · User control & freedom*

### Findings

| # | Issue | Evidence | Impact |
|---|-------|----------|--------|
| 1.1 | **"Home" is an account menu, not a home.** Core product features (My Recruitment, Messages, Coach's Interest) sit in the same flat list as low-frequency settings (Password, Update Profile, Log Out). | Screen 1 | High — the most-used destinations get the same weight as "change password." |
| 1.2 | **Hub-and-spoke navigation.** No persistent tab bar; every screen has "< Back." Moving from a school's pipeline to Messages means backing all the way out to Home. | Screens 2–5 all lead with "< Back" | High — multiplies taps on the daily path. |
| 1.3 | **Three overlapping inboxes.** "Messages," "New Responses" (Coach's Interest?), and "Notifications" appear to be separate streams with unclear boundaries. | Screen 1 metrics + menu vs. Screen 5 | Med — fragmented comms create "which one has the thing I need?" anxiety. |
| 1.4 | **Primary CTA may be mis-pointed.** The fixed bottom button is "View My Profile," but the athlete's core job-to-be-done is *advancing recruitment*, not admiring their profile. | Screen 1 | Med — the loudest button on the home screen isn't the North Star action. |
| 1.5 | **No "add a school."** A recruitment tracker with no visible way to add a target program is missing its front door. | Screen 2 | Med–High — depends on whether adds happen elsewhere (open question). |
| 1.6 | **Summary stats may be dead ends.** "6 Active / 2 Offers / 3 To Do" and "486 Views / 9 Responses / 4 Messages" read like they should filter or drill in. Unclear if tappable. | Screens 1–2 | Med — undermines *recognition* if they're purely decorative. |

### Recommendations

- **Introduce a persistent bottom tab bar** — e.g. `Home · Recruitment · Inbox · Alerts · Profile`. This single change collapses most of the back-button chains and gives the product a spine. Move Password / Update Profile / Log Out under a **Profile / Settings** tab, off the main surface.
- **Make every stat card a filter/drill-in.** Tapping "2 Offers" should filter the program list to offers; tapping "3 To Do" should open the task list. This turns vanity metrics into navigation.
- **Consolidate or clearly delineate the three inboxes.** Ideal: one **Inbox** with typed items (Messages, Coach responses, Pipeline updates) and filter chips. If they must stay separate, label the boundary explicitly ("Notifications = pipeline changes; Messages = conversations").
- **Rename/re-point the home CTA.** Consider "**Continue My Recruitment**" (deep-links to the highest-priority school) or a next-best-action button. Keep "View My Profile" as a secondary link.
- **Add an unmistakable "+ Add School / Find Programs"** entry on the Recruitment dashboard.

---

## 2. UI & Visual Hierarchy

**Heuristics in play:** *Aesthetic & minimalist design · Consistency & standards · Visibility of system status*

### Findings

- **2.1 — Red overload.** Red is the logo, the "Upgrade" link, the primary CTA, the alert text, the stage tags, *and* the completed-step checkmarks. When the action color and the "done" color and the "urgent" color are all identical, the eye can't triage. **Completed** should not share a color with **do this now**.
- **2.2 — One rogue accent.** The chat-bubble metric icon is **blue** while everything else is red — the only blue in the system, with no rule behind it. Either blue becomes a deliberate secondary accent (applied consistently) or it goes.
- **2.3 — Deadlines are under-weighted.** "Offer decision due Aug 12" — the highest-stakes datum on the dashboard — is small, low-contrast body text at the bottom of a card. It should be the loudest thing on that card.
- **2.4 — False precision.** "Acceptance Rate 78.94" reads as spurious accuracy. Round to `79%`. Also interrogate *relevance*: acceptance rate and GPA are admissions stats sitting inside a *recruitment* pipeline with no framing of academic fit.
- **2.5 — Ambiguous metric labels.** "486 Views This Month" — views *of what, by whom*? "9 New Responses" — responses to what? Labels state the number but not the meaning; that's *recognition* failing.
- **2.6 — Timestamp inconsistency.** "just now," "1 day ago," "9d in stage," and calendar dates ("Apr 2") coexist, and they mix two different meanings — *last activity* vs. *time spent in stage*. Pick one relative format and separate the two concepts visually.

### Recommendations

- **Define a disciplined color system** and apply it everywhere:
  - **Red** → primary action / brand only (CTAs, logo).
  - **Green** → completed / positive stage progress (the checkmarks).
  - **Amber** → deadlines & urgency (the Aug 12 chip, "9d in stage" if it's stalling).
  - **Neutral/blue-gray** → informational, non-actionable.
- **Promote deadlines to a chip** with an urgency tint and a countdown ("⏳ Offer due in 8 days"). Surface the *same* deadline on the program detail screen (Screen 3) — right now it appears on the card but vanishes on the detail view.
- **Add units/context to every metric** — "486 profile views by coaches," "9 coaches replied," and make them tappable.
- **Round admissions stats** and, ideally, reframe them as *fit* ("Your GPA 3.9 vs. school avg 3.83 ✓").
- **Normalize timestamps.** Last-activity in one consistent relative format; keep "N days in stage" as a distinct, deliberately styled *staleness* signal (it's actually a useful nudge).

---

## 3. UX & Interaction

**Heuristics in play:** *Visibility of system status · Error prevention · Match between system & real world · User control & freedom*

### Findings

- **3.1 — Contradictory stage taxonomy (highest severity).** The stage definitions conflict:
  - Screen 3/4: **"Engaged — Offer received"**
  - Screen 4: **"Offer Extended — The program has made an offer"**

  Two stages can't both mean "an offer arrived." And the overview says **"Stage 3 of 7"** while the timeline shows **5 steps**. Users can't trust a status they can't parse — and downstream, RFX's "searchable intelligence" is only as good as this taxonomy.
- **3.2 — No feedback after "Update."** The modal's confirm gives no visible result state. Did the stage change save? Was the coach notified? Did anything happen? *Visibility of system status* is missing at the exact moment of the app's core write action.
- **3.3 — No guardrails on self-reported stage.** The athlete can freely set themselves to any stage, including jumping straight to "Offer Extended." No confirmation on large or backward jumps, no "are you sure?" for consequential moves. This is both an *error-prevention* gap and a *data-integrity* risk for the matching engine.
- **3.4 — Ambiguous notification state.** Screen 5 says the stage "moved forward" (past tense) but asks you to "Approve." Is the move *already applied* and this is post-hoc confirmation, or *pending* your approval? The user can't tell what state the world is in.
- **3.5 — No next-best-action.** The pipeline shows *where* you are but never *what to do next*. "You're Engaged with UAB" — okay, now what? The most valuable coaching ("Send your season footage; that's what moved LMU") lives only in a notification, not in the flow where the athlete acts.
- **3.6 — Missing states.** No empty states (zero schools, zero notifications), error states (failed save), or loading states are shown. These are where apps feel either *forgiving* or *broken*.

### Recommendations

- **Fix the stage taxonomy first** (see Quick Win #1). One canonical, non-overlapping set of stages; one count that matches the timeline; subtitles that describe distinct milestones.
- **Add explicit success feedback** to "Update": inline toast ("✓ Stage updated to Engaged — UAB's coach won't be notified" or "…coach notified"), animate the timeline node, and reflect the new "days in stage" reset.
- **Guard consequential changes.** If the athlete jumps more than one stage or moves backward, confirm ("You're skipping *Evaluating*. Sure?"). Consider making *offer-related* stages coach-verifiable rather than freely self-set.
- **Clarify notification state** with explicit language and controls: either "**Marcus moved you to Evaluating.** Confirm this is right → *Approve* / *Not accurate*" (pending model) or "This has been applied. Flag if wrong." Make the default outcome of *doing nothing* obvious.
- **Add a next-best-action block** to each program detail: "Next step: upload season footage" with a direct button.
- **Design the empty/error/loading states** deliberately — they set the tone for *forgiveness*.

---

## Pipeline Specification — the seven stages, clarified

*Resolves §3.1. The seven stages and their order are sound — keep all of them. The confusion is fixable without adding or removing a single stage.*

**The stages are:** `1 Identified → 2 Contacted → 3 Engaged → 4 Evaluating → 5 Offer Extended → 6 Committed → 7 Signed`. Each marks a genuinely distinct threshold in a real recruiting relationship, and they climb in a clean, monotonic line. What makes the pipeline feel murky is *how the stages are framed and displayed*, not which stages exist.

### Five design principles

1. **One point of view.** Every stage describes the **state of the relationship**, defined by the single milestone that moves the athlete into it — never a mix of "what *they* did" (Identified — "added to their board") and "what *I* did" (Contacted — "you replied"). That flip is the core reason users can't tell where they stand.
2. **Definitions are always visible.** Whatever the labels, each stage carries a plain-language subtitle *and* an "enters when" trigger, inline. This kills the guessing regardless of naming.
3. **Chunk seven into four phases.** Show `Phase 2 of 4 · Building the relationship`, not `Stage 3 of 7`. Four phases are easier to hold in the head; the seven fine-grained nodes still live inside, color-coded by phase.
4. **Status is separate from stage.** Add an orthogonal `Active / Stalled / Closed` flag so a school that goes cold can tell the truth — a forward-only 7-step bar can't express "they stopped replying" or "the offer was pulled."
5. **Authoritative source per stage.** Early stages can be self-set; high-stakes ones must be confirmed/documented via the existing *Approve / Not accurate* loop — this is where data integrity for the matching engine is won or lost.

### Canonical stage table (with inline microcopy)

| # | Phase | Stage label | UI subtitle *(athlete POV — use in timeline **and** the Update Stage modal)* | Enters when *(tooltip / helper)* | Source of truth |
|:--:|---|---|---|---|---|
| 1 | Getting noticed | **Identified** | You're on their radar | You're on the program's board, or you've added them as a target | Self / program |
| 2 | Getting noticed | **Contacted** | First contact made | A coach reaches out, or replies to your outreach | Logged message |
| 3 | Building the relationship | **Engaged** | You're in active conversation | Communication is regular and two-way — not a one-off | Self |
| 4 | Building the relationship | **Evaluating** | They're weighing an offer | They're watching games, reviewing full film, hosting a visit, or requesting transcripts | Self / program |
| 5 | The decision | **Offer Extended** | You have an offer to consider | The program makes a concrete offer (roster spot / scholarship / PWO) | **Documented ✓** |
| 6 | The decision | **Committed** | You've verbally committed | You accept the offer | **Confirmed ✓** |
| 7 | Locked in | **Signed** | It's official | Signed NLI / letter of intent / enrollment | **Documented ✓** |

### Microcopy notes

- **Render "Offer Extended" as two words.** `OfferExtended` is a code identifier leaking into the UI.
- **Use the same subtitle in both places** — the pipeline timeline *and* the "Update My Stage" radio list — so a stage means one thing everywhere.
- **The "enters when" line is the anti-confusion device.** It removes any doubt about which stage applies, and is what fixes the fuzzy `Engaged`/`Evaluating` middle without a rename.
- **This retires the existing bug:** today the app labels *Engaged* as "Offer received," which collides with *Offer Extended*. Under this spec, `Engaged` = conversation and an offer only appears at stage 5. One contradiction gone; "3 of 7" now matches a 7-node track.

### Progress display

- Headline the **phase**, not the raw step: `Phase 2 of 4 · Building the relationship`.
- Color by phase — e.g. *Getting noticed* = neutral, *Building* = blue/info, *Decision* = amber, *Locked in* = green. Keep **red for the primary action button only** (ties back to §2.1).

### Status flag (orthogonal to stage)

| Status | Meaning | Behavior |
|---|---|---|
| **Active** | Normal, progressing | Default |
| **Stalled** | No movement in a while | Auto-applied after *N* idle days (you already track "days in stage"); shows an amber pill and can trigger a next-best-action nudge |
| **Closed** | Athlete withdrew, or program passed / pulled the offer | Removed from the *Active* count without deleting history |

---

## 4. Client Engagement & Trust-Building

**Heuristics in play:** *Match between system & real world · Help & documentation · Error recovery*

### Microcopy & tone

- **4.1 — "Not accurate" is faintly adversarial.** It frames the user's club coach as wrong. Softer, less binary: **"Needs a correction"** or **"That's not right."**
- **4.2 — "Upgrade for More" is value-free.** *More what?* Name the benefit at the point of friction ("See who viewed your profile — upgrade to unlock").
- **4.3 — "Update My Stage" / "View My Profile" are functional but flat.** They describe mechanics, not outcomes. Where possible, use outcome verbs ("Advance my recruitment," "Continue where I left off").
- **4.4 — Stage subtitles are a trust surface.** "Added to their board," "You replied to the program" are good, human, specific — extend that voice consistently and resolve the contradictions in §3.1.

### Trust & role clarity

- **4.5 — Who is Marcus, and why can he edit my pipeline?** Screen 5 shows "Marcus Jennings • **Riverside Club**" moving the stage — but the athlete plays for **Surrey FC** (Screen 2). Different club editing your record, with no explanation of role or permission. For a data-trust product, **who can see and change what** must be legible. Add a one-line relationship label ("Marcus — your RFX recruiting advisor") and a lightweight permissions/roles view.
- **4.6 — Role ambiguity across the whole app.** RFX serves athletes/parents, club directors, *and* college coaches — three very different jobs. "Welcome back, John" doesn't establish *which* John I am or what I can do. Make role explicit and tailor the primary surface to it.
- **4.7 — Show the data's provenance.** Because the matching engine depends on trustworthy data, small "who set this / when" attributions on stages build confidence (and double as an audit trail).

### Recommendations

- Rewrite CTAs and destructive/negative actions toward **outcome-oriented, non-blaming** language.
- Make **contextual upgrade prompts** that name the locked value at the moment it's wanted.
- Add **explicit role & relationship labels** wherever a third party (coach/advisor) touches the athlete's data, plus a simple "Who can see my profile / edit my pipeline" screen.
- Lean into the **verification loop** as a signature trust feature — make it a strength you talk about, not a buried notification.

---

## 5. Top 3 Quick Wins (highest impact, lowest effort)

> These are copy/config/style changes — no new architecture — that punch above their weight.

### 🥇 #1 — Fix the stage taxonomy & labels
**Effort: Low (copy + config) · Impact: High (clarity + data trust)**
Resolve the "Engaged = Offer received" vs. "Offer Extended = offer made" contradiction, and reconcile "Stage 3 of **7**" with the **5**-step timeline. Ship one canonical set of non-overlapping stages with distinct, human subtitles, and make the count match the visual. This is the cheapest fix with the deepest payoff — it protects both comprehension *and* the integrity of the searchable data RFX sells.

### 🥈 #2 — Elevate deadlines & add a next step
**Effort: Low (component + one line of content) · Impact: High (drives the core action)**
Turn "Offer decision due Aug 12" into a prominent **amber urgency chip with a countdown** ("⏳ Offer due in 8 days"), surface it on *both* the dashboard card and the program detail screen, and add a one-line **next-best-action** ("Next: upload season footage →"). This converts a passive tracker into an app that tells you what to do today.

### 🥉 #3 — Impose color discipline
**Effort: Low–Med (design-token pass) · Impact: High (instant hierarchy)**
Reserve **red for primary actions/brand only**; make completed steps **green**, deadlines **amber**, and info **neutral**; remove or systematize the lone **blue** chat icon. The screens are already clean — this one pass makes the *right* things pop and stops the current "everything is red" flattening of hierarchy.

*(Honorable mention, slightly more effort: add a persistent bottom tab bar — §1 — to kill the back-button chains.)*

---

## Prioritized roadmap (impact × effort)

| Priority | Change | Section | Effort | Impact |
|:--:|---|:--:|:--:|:--:|
| P0 | Fix stage taxonomy / count / subtitles | §3.1 | Low | High |
| P0 | Deadline urgency chip + next-best-action | §2.3 / §3.5 | Low | High |
| P0 | Color-token discipline (green/amber/red/neutral) | §2.1 | Low–Med | High |
| P1 | Persistent bottom tab bar | §1.2 | Med | High |
| P1 | Success/empty/error/loading states + save feedback | §3.2 / §3.6 | Med | High |
| P1 | Role & relationship labels + "who can edit" view | §4.5 | Med | High (trust) |
| P1 | Surface Vekt / character profile on dashboard | Strategic gap | Med–High | High (differentiation) |
| P2 | Make stat cards tappable filters; add context to metrics | §1.6 / §2.5 | Low–Med | Med |
| P2 | Consolidate the three inboxes | §1.3 | Med | Med |
| P2 | Contextual upgrade prompts | §4.2 | Low | Med (revenue) |
| P2 | Guardrails/confirmation on big stage jumps | §3.3 | Low | Med (data quality) |

---

## Nielsen heuristic scorecard

| Heuristic | Read | Where |
|---|:--:|---|
| Visibility of system status | ⚠️ Mixed | Pipeline ✅ / no save feedback ❌ / ambiguous notification state ❌ |
| Match between system & real world | ⚠️ Mixed | Good voice, but contradictory stage semantics |
| User control & freedom | ✅ OK | Back + Cancel present; undo of stage change unclear |
| Consistency & standards | ❌ Weak | Timestamps, stage count, color usage all inconsistent |
| Error prevention | ⚠️ Gap | No guardrails on self-reported stage jumps |
| Recognition rather than recall | ⚠️ Mixed | 7 stages unlabeled on overview; metrics lack context |
| Flexibility & efficiency | ❌ Weak | Hub-and-spoke; no tabs, filters, or sort |
| Aesthetic & minimalist design | ✅ Strong | Clean, premium dark UI |
| Help users recognize/recover from errors | ⚠️ Partial | "Not accurate" is a nice recovery path; no error states shown |
| Help & documentation | ❌ Missing | Stage meanings/next steps not explained inline |

---

## Open questions to validate before building

1. **What is the single primary goal** for the athlete persona — land offers, maximize profile views, complete the Vekt profile? The home CTA should point at it. *(The brief left "Key Goal" blank; naming it sharpens everything above.)*
2. Are the **stat cards and metric cards interactive**? Several recommendations hinge on this.
3. **How are schools added** to the pipeline, and by whom?
4. What's the **permission model** — who (athlete, parent, club coach, RFX advisor, college coach) can view or edit which fields?
5. Does **"Update My Stage" notify the college coach**, or is it private tracking? The right microcopy depends on the answer.
6. Where does **Vekt/character data** live today, and why is it absent from the athlete's own view?

---

### A small QA note

Screens 3 and 4 in the source images have a **Gmail inbox browser tooltip** floating over them. Harmless in a working review, but scrub these before any deck, investor doc, or store listing — stray browser chrome undercuts a premium first impression.

---

*Prepared as a constructive, prioritized review. Happy to turn any section into annotated redlines, a Figma-ready spec, or a revised stage-taxonomy proposal.*
