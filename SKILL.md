---
name: create-course
version: 1.0.0
description: Build a complete interactive course (curriculum, lessons, demos, audio) on any topic, phased and gated on user answers at each step.
allowed-tools:
  - Read
  - Write
  - Edit
  - Glob
  - Grep
  - AskUserQuestion
  - Bash
  - Artifact
triggers:
  - create a course
  - build a course
  - make a course on
  - design a course
---

# /create-course — Build a Course on Any Topic

You are producing a complete course: curriculum, lesson scripts, interactive
demos, and visual components (with audio/video, if any, decided and wired
in as part of component assembly). Work in the 6 phases below, in order. **Do not skip a phase and do not generate code or
copy until the phase's questions are answered and the phase's output is
approved by the user.** Each phase ends with a explicit approval checkpoint
before moving on.

Default component pattern (use as a starting point, not a requirement): a
lesson built from labeled content blocks (script paragraph, director note,
visual cue, callout), delivered inside a device-mockup shell with a step
indicator and short consequence/outcome pills, sharing a single design-token
set across the course. Treat these **as defaults, not requirements** — a
topic-specific course may need a different delivery format (e.g. no device
mockup, a different visual system) depending on what's decided in Phase 5.

Use `AskUserQuestion` for every question block below. Group related
questions into a single call (up to 4 per call — the tool caps it there); if
a phase has more than 4 questions, split into sequential calls. Never
silently assume an answer — if the user skips a question, ask it again
before moving on, unless they explicitly say "use your judgment," in which
case state the default you're picking and continue.

### The interview loop (applies to every phase)

Each phase is not a single Q&A round followed by a yes/no approval — it's an
**interview that iterates until the user says the result is good enough.**
For every phase:

1. Ask the phase's questions (via `AskUserQuestion`).
2. Produce a draft of that phase's output (outline, sample lesson, demo
   spec, etc.) from the answers.
3. Present the draft and ask a genuine improvement question, not just
   "approve? yes/no" — e.g. "What's missing here? What would you change?
   Anything feel off in tone/depth/format?"
4. Revise based on the answer and show the updated draft.
5. Repeat steps 3-4 until the user explicitly signals satisfaction ("good,"
   "ship it," "that's it," "move on," etc.). Don't assume silence or a
   lukewarm "sure" is satisfaction — if the signal is ambiguous, ask
   directly: "Good to lock this in and move to the next phase, or another
   pass?"
6. Only then advance to the next phase.

Don't loop forever on trivial questions (e.g. the yes/no on whether audio
is wanted at all doesn't need an improvement round) — use judgment on which
checkpoints benefit from iteration (Phases 1-4 and the Phase 5b visual draft
almost always do; the rest mostly need one solid pass plus a chance to
object).

### Reopening a locked phase (retroactive changes)

Locking a phase (curriculum outline, sample lesson pattern, demo pattern,
visual draft) is not a promise it can never change — it's a promise nothing
moves forward silently drifted from it. If the user asks to change
something in an already-locked phase after later lessons were already
built against it (e.g. "actually change the accent color" after 4 lessons
are done):

1. **Name the blast radius before making the change.** State plainly which
   already-completed lessons/components were built against the thing being
   changed, and that they'll now be out of sync unless updated too.
2. **Ask how far back to apply it** — this lesson forward only (old ones
   stay as-is, a known inconsistency), or retrofit everything already
   built?
3. If retrofitting: treat it as a normal edit pass across the affected
   lessons — re-run the relevant QA checks (Phase 6) on each one touched,
   don't assume the change was clean.
4. Update the locked draft/spec itself (Phase 2 outline, Phase 5b visual
   draft, etc.) so it reflects the new decision — a "locked" artifact that
   silently goes stale is worse than not locking anything.

Small clarifications and typo-level fixes don't need this whole process —
use judgment; this is for changes that actually touch what was approved as
the pattern.

---

### Output conventions

Before Phase 1's questions, or as soon as it's clear where output should
land, confirm (one quick question, doesn't need the full interview loop):
does an existing course/project structure exist in this repo to follow
(e.g. the Fintech Compliance course layout), or does a new structure need
to be created? If new, propose a convention and get a quick nod before
writing files against it:

- One directory per course (kebab-case course name).
- One subdirectory or file per module, one file per lesson within it,
  named with a stable order prefix (`01-`, `02-`...) so lesson order is
  obvious from the filesystem, not just from a doc.
- A single media manifest file (e.g. `media.json` or equivalent for the
  target stack) mapping lesson → audio/video link, so Step 5c's "swap a
  link in" promise is a one-line diff against one file, not a hunt through
  component code.
- Keep narration/video scripts alongside their lesson (not in a separate
  scripts-only directory) so the two never drift out of sync silently.
- Course-level pages (home, index, completion) live at the course root,
  not nested inside a module — they're not part of any one module's
  sequence.

If the repo already has a convention (e.g. from a prior course), follow it
instead of introducing a second, inconsistent one — check for that first.

---

## Phase 1 — Topic & Audience Scoping

Ask (batch into ≤4-question calls):

1. **Topic** — what is the course about, and what's the single outcome a
   learner should walk away able to do?
2. **Audience role/background** — who is this for (job title, experience
   level, prior domain knowledge)?
3. **Country/region** — where is the audience based? (affects regulatory
   examples, currency, cultural references, spelling conventions)
4. **Age range** — affects tone, pacing, and example choice.
5. **Language level** — reading/literacy level and word choice: plain
   everyday language, professional/business register, or technical/expert
   jargon-heavy? Any words or phrases to avoid or definitely use?
6. **Course depth/length** — roughly how many modules and lessons (default
   template: 5-6 modules × 3 lessons), or should that be derived from the
   topic in Phase 2?

Do not proceed to Phase 2 until topic, audience, and language level are
answered. Country/age/length can take a stated default if the user waves
them off.

**Checkpoint:** run the interview loop — summarize the scoping in 3-4
bullets, ask what's missing or off, revise, repeat until the user confirms
it's good enough to draft a curriculum from.

---

## Phase 2 — Curriculum Arc

Draft a proposed module/lesson outline (narrative arc: foundational →
applied → emotional/advanced, or another arc if better suited to the
topic). Present it as a list, not code.

Then ask:
1. Does the module count/order match what they need, or should any module
   be added/removed/reordered?
2. Is the arc (foundational→applied→advanced) right, or is there a
   different narrative shape they want (e.g. problem→solution, chronological,
   skill-tree/non-linear)?

**Checkpoint:** run the interview loop on the outline — ask what to add,
cut, reorder, or reshape, revise, and show the updated outline each round.
Curriculum outline must reach explicit "good enough" approval before any
lesson content or code is written. This is a hard gate.

**If the arc is skill-tree/non-linear:** everything downstream in this
skill (sequential per-lesson production in Phase 3, the index's
locked/"coming soon" ordering, and "last planned lesson" in the Phase 6
completion path) assumes a linear curriculum order by default. For a
non-linear arc, explicitly ask the user to define a build order (which
lesson ships first, second, etc.) separate from the learner-facing
navigation order — production still happens one lesson at a time in that
build order, but the index/unlocking logic should reflect the actual
skill-tree structure (e.g. a lesson might unlock based on prerequisites
completed, not simply "next in the list"), and "course complete" means all
nodes in the tree are built, not "reached the last item in a line."

---

## Phase 3 — Lesson Scripting

Default template per lesson: **Hook → Case study → Explain → Reflect →
Domain map.**

Before scripting, ask (per-course, not necessarily per-lesson, unless the
user wants variation):
1. **Content types wanted** (multiSelect) — which of these should lessons
   include: worked examples/case studies, practice exercises, checklists,
   quizzes/knowledge checks, reflection prompts, downloadable
   templates/cheat-sheets, glossary/definitions? Any lesson-specific
   variation (e.g. only later modules get quizzes)?
2. **Tone/voice** — instructor persona (peer, mentor, expert-authority),
   and how directive vs. exploratory the writing should be.
3. Does the 5-part template (Hook → Case study → Explain → Reflect → Domain
   map) fit this topic, or does a step need renaming/dropping/replacing
   (e.g. a technical course might want Hook → Demo → Explain → Practice →
   Reference instead of Reflect/Domain map)?

Script one lesson first as a sample and run the interview loop on it —
show the draft, ask what to change (tone, pacing, which content types
landed vs. felt forced, structure), revise, repeat until the user says it's
good enough. This sample sets the pattern for the rest.

**Production is sequential per lesson, not batched.** Once the pattern is
approved, don't write all remaining lesson scripts, then all demos, then
all components in separate batch passes. Instead take each lesson through
the full pipeline — script (Phase 3 pattern) → demo spec (Phase 4) →
component incl. media (Phase 5) → QA gate (Phase 6) — one lesson at a
time, in curriculum order. Only start the next lesson's production once
the current lesson clears its Phase 6 per-lesson gate. Don't re-run the
full interview loop on every single lesson's script (that was already
locked via the sample) — but do keep proposing each new lesson's draft for
a quick look before moving on, so drift gets caught early rather than at
the end.

---

## Phase 4 — Interactive Demo Design

For each lesson (or module) that gets an interactive component, ask which
visual/interaction format fits, offering concrete options rather than an
open-ended question:
1. **Demo format** — before/after comparison, step-by-step flow, decision
   tree/branching scenario, interactive checklist, drag-and-drop, quiz/knowledge
   check, annotated screenshot/mockup walkthrough, video/animation, or none
   needed for this lesson?
2. **Delivery shell** — does it live inside a phone mockup (matches the
   Fintech course default), a browser/desktop mockup, a plain in-page
   component, or something else?
3. Any specific reference example (screenshot, competitor product, prior
   course) they want the visual style to match?

**Checkpoint:** sketch the demo as a spec/description (not code) for the
first instance, run the interview loop — what should change about the
interaction, the states shown, the visual weight — revise, repeat until
good enough. Get the demo-format choice locked for that pattern before
building any actual demo component. As with Phase 3, this locks the
pattern — subsequent lessons follow it as part of their place in the
sequential per-lesson pipeline (see Phase 3's "Production is sequential"
note), not as a separate batch pass.

---

## Phase 5 — Component Assembly (incl. audio/video decision)

### Step 5a — Media decision (before any design or code work)

Ask (batch into ≤4-question calls):
1. **Media type** — does this course use audio narration, video, both, or
   neither?
2. **Voice/style** (if audio) — narrator style: calm expert, energetic
   coach, neutral professional? **Video style** (if video) — talking-head,
   screen recording, animated, or other?
3. **Readiness, per media type wanted** — is the audio/video already
   produced and ready to link in, or does it still need to be created?

**If not ready**, don't just ask a generic "how will you make it" — ask
**production method**, since the guide you give depends entirely on it:
- **Text-to-speech** — feed the narration text into a TTS tool/service
- **AI-generated video** — feed a script/prompt into an AI video tool
- **Record it themselves (voice)** — read the narration into a mic
- **Record it themselves (video, e.g. talking-head or screen capture)**

This can be a single AskUserQuestion (single-select, or multiSelect if
different lessons will use different methods — ask which).

**Then write the per-lesson narration/video script FROM the actual lesson
content** (not a generic placeholder script) — pull the Hook/Problem, key
explanation points, and any case study beats from that lesson's Phase 3
draft into a narration-ready script, paced for spoken delivery (shorter
sentences, natural pauses marked, no visual-only references like "as you
can see below"). Run the interview loop on the **first lesson's** script:
draft, get feedback, revise, repeat until good enough — this sets the
narration pattern (pacing style, how much of the lesson content gets
condensed, pause conventions), same as Phase 3's sample-lesson approach.
From there, narration scripts follow the same sequential-per-lesson
pipeline as everything else (see Phase 3's "Production is sequential"
note) — do not batch-write all remaining scripts ahead of their lesson's
turn in the pipeline.

**Then give a production guide matched to the chosen method:**
- **Text-to-speech:** hand them the finalized script formatted for
  drop-in use, plus practical notes — pick a TTS voice that matches the
  agreed voice/style from question 2, watch for mispronounced
  jargon/acronyms (flag any from this course's glossary), keep pacing/pause
  markers if the tool supports SSML.
- **AI-generated video:** hand them the finalized script as the video
  prompt/voiceover text, plus notes on matching the agreed video style
  (talking-head/screen-recording/animated), and any visual cues from the
  lesson (from Phase 4's demo spec) worth telling the tool to depict.
- **Record it themselves (voice):** hand them the script formatted for
  natural reading (line breaks at breath points), plus basic setup notes
  (quiet room, mic distance, one lesson per take) and the agreed pacing.
- **Record it themselves (video):** hand them the script plus notes tied
  to the chosen video style and any visual cues from Phase 4 they should
  show on screen while narrating.

**The readiness answer determines what Phase 5b builds:**
- **Ready now (have files/links):** collect the actual link or file path
  per lesson (or agree on a naming convention/manifest file the user will
  fill in) before building components, so the real media gets wired in
  directly.
- **Not ready yet:** tell the user plainly that component code will ship
  with a placeholder media slot only — do not write any code that assumes
  audio/video features (no cue timing, no transcript sync, no captions
  logic, etc.) until real media exists to build those features against.
  Then point them back to the method-matched script + guide above and ask
  them to come back with a link or file per lesson once ready.

**Hard gate:** don't write final component code that wires up audio/video
until you have either (a) real links/files for that media, or (b) explicit
confirmation the user wants placeholders shipped now and will supply links
later. Never fabricate placeholder "features" (fake waveforms, fake
captions, fake video-scrubber behavior) — an empty, clearly-labeled slot
(e.g. "🔊 audio coming soon" / a bordered box with a placeholder icon) is
the only acceptable stand-in.

### Step 5b — Design + component build

Ask (batch into ≤4-question calls):
1. **Design system** — reuse the existing shared design tokens/components
   from this repo, start a new design system for this course, or match a
   specific reference (brand guide, existing product, competitor)?
2. **Branding constraints** — specific colors, fonts, logo, or visual style
   (minimal, playful, corporate, editorial) to apply?
3. **Platform/target** — where does this course run: Framer site, a web
   app (React/Next/etc.), a native mobile app, a plain static site/LMS
   embed (e.g. Teachable, Notion), or something else?
4. **Responsiveness** — what devices/breakpoints matter: desktop-first with
   mobile support, mobile-first (learners primarily on phones), or a fixed
   single layout (e.g. presentation-style, no responsive requirement)? Any
   specific breakpoints or device constraints (e.g. must work well in a
   phone-mockup shell at a fixed width)?

**Course-level pages, not just lesson components.** Besides the per-lesson
component pattern, a course needs a small set of course-wide pages built
once (not per lesson). Confirm scope with the user before drafting:
- **Home/landing page** — the course's front door: title, outcome
  statement (from Phase 1), what's covered (from the Phase 2 outline), and
  a start/enroll call to action. Always in scope unless the user says the
  course lives inside an existing site's page and doesn't need its own
  landing page.
- **Course index** — navigation across all modules/lessons (progress
  indicator if the platform supports tracking completion), lets a learner
  jump to any lesson. Always in scope.
- **Completion page** — shown when a learner finishes all lessons (e.g. a
  "you did it" summary, recap of what was covered, next-steps/roadmap
  links). **Optional — ask explicitly** whether the user wants this at
  all, since not every course needs a formal completion moment.

Ask (can combine with the design-direction questions above): do they want
a completion page, and if so, what should it contain beyond a generic
"congrats" (e.g. a certificate-style visual, a recap of the roadmap from
the course topic, links to what's next)?

**Visual draft, not just a text description.** Once the design direction
questions are answered, don't just describe tokens/components in prose —
build actual visual mockups the user can look at before any real component
work starts. Use the Artifact tool (self-contained HTML/CSS) to produce
sample screens covering: one lesson page, the home/landing page, the
course index, and the completion page if in scope — styled with the
proposed colors, fonts, layout, and responsive behavior (show it adapting
to the chosen breakpoints if relevant — e.g. a note or toggle for mobile
vs desktop). If media is ready, show it wired in for real; if not, show
the placeholder slot exactly as it will ship. These drafts are
internal/exploratory, not the final production components — they exist
purely to get the design locked before building real, reusable components.

Run the interview loop on the visual drafts: publish them, ask what to
change (colors, spacing, type, layout, tone of the visual style, how it
handles the responsive breakpoints, how the media slot/embed looks, whether
the home page and index actually orient a new learner well), revise and
republish, repeat until the user explicitly approves. **Do not proceed to
building the actual reusable components/tokens until the visual drafts are
locked** — this is a hard gate, same as the Phase 2 curriculum gate.

Then assemble the real components from the locked drafts: shared design
tokens, home/index/completion pages, per-lesson hero/next-step components,
`addPropertyControls` where relevant (Framer) or the equivalent for
whatever stack/platform was chosen, built to the agreed responsiveness
target, with real media wired in where links were provided and clean
placeholders everywhere else. The home page and course index are built
once, early — they don't wait for every lesson to finish the sequential
per-lesson pipeline (Phase 3's "Production is sequential" note), since a
learner needs somewhere to land and navigate from before all lessons
exist. The completion page (if in scope) can be built alongside them since
its content doesn't depend on lesson components being finished, only on the
locked curriculum outline.

### Step 5c — Closing the loop on placeholders

If any lesson shipped with a media placeholder, track it as open work, not
done. When the user later sends a link/file for a placeholder slot, treat
that as a small targeted update (drop the link into the manifest/prop, no
other code changes) rather than reopening design or component work — the
whole point of gating on readiness upfront is that swapping real media in
later is a one-line change, not a rebuild.

---

## Phase 6 — QA

QA is a verification pass against every decision locked earlier — not a
fresh design review. Check each of these and report findings as a punch
list (pass / drifted / missing), don't silently patch anything without
flagging it first:

1. **Content fidelity** — for each lesson, check tone/voice, content types
   present (exercises, checklists, quizzes, etc. from Phase 3), and lesson
   template structure against what was approved. Flag any lesson that
   drifted.
2. **Curriculum completeness** — every module/lesson from the locked Phase
   2 outline exists and is in the right order; nothing silently dropped or
   added.
3. **Course-level pages** — home/landing page, course index, and
   completion page (if in scope) exist, match their locked Phase 5b visual
   drafts, and actually work: the index links to every real lesson, the
   home page's CTA leads somewhere, and (if built) the completion page is
   reachable after finishing all lessons.
4. **Demo fidelity** — each interactive demo matches its locked Phase 4
   spec (format, delivery shell); flag any that were simplified or changed
   without a check-in.
5. **Design fidelity** — take screenshots of each lesson/demo and compare
   against the locked Phase 5 visual draft: colors, type, spacing, layout
   match what was approved, not a drifted approximation.
6. **Responsiveness** — always check mobile/vertical layout regardless of
   what Phase 5 agreed as the primary target: stacks should reflow
   sensibly, no horizontal overflow, no clipped or unreadable text, on a
   narrow vertical viewport. This is a baseline check on every course, not
   opt-in. On top of that baseline, also verify against whatever the Phase
   5 target actually was (e.g. desktop-first with mobile support, a fixed
   single layout inside a phone-mockup shell) — resize/screenshot at
   mobile and desktop widths, or verify inside the agreed shell. Flag
   anything that breaks, overflows, or degrades at any checked width.
7. **Media ledger** — report the placeholder ledger from Step 5c: which
   lessons have real audio/video wired in vs. which still have open
   placeholders, and for open ones, whether a script + production guide was
   already handed off (from Step 5a) or is still outstanding.
8. **Broken links/embeds** — spot-check that any provided media links
   actually resolve/play, and that downloadable templates/cheat-sheets
   (Phase 3) actually exist and link correctly.
9. **Accessibility** — images/visual cues have alt text, video has (or has
   a plan for) captions/transcript, text-on-background meets reasonable
   contrast, and interactive demos (Phase 4) are operable by keyboard, not
   only by mouse/touch. Flag gaps rather than silently accepting them —
   this wasn't asked about upfront, so treat anything found here as a
   recommendation to fix, not an approved-scope violation.

**Per-lesson gate before moving to the next lesson's production.** Don't
run QA once at the very end of the whole course and call it done — after a
lesson's component is built, run checks 1, 4-9 against that lesson (skip
check 3, it's course-wide — see the completion path below; treat check 2
as "every lesson planned up through this one exists," not the full course,
since later lessons haven't been produced yet). Then hand the lesson to the
user to actually check themselves (open/run the live component, not just
look at your screenshots) and ask explicitly: does the code need any
adjustment, or is this lesson good to lock? Wait for their answer before
starting production on the next lesson's component. If they flag an issue,
fix it and re-run the relevant checks before asking again — don't move on
with a known issue outstanding. This keeps problems from compounding
across many lessons' worth of components before anyone notices.

**In-progress index behavior.** While lessons are still being produced
sequentially, the course index must never link to a lesson that doesn't
exist yet or isn't locked. Not-yet-built lessons show as visibly
locked/"coming soon" (from the Phase 2 outline's titles, so the shape of
the course is visible even before content exists) rather than a dead link
or an empty nav item. Update a lesson's index entry from locked to live
only once it clears its per-lesson gate.

### Course completion path (separate from per-lesson production)

Finishing the *last* planned lesson doesn't just clear one more per-lesson
gate — it triggers a distinct, course-wide finalization pass that the
per-lesson pipeline (Phase 3's "Production is sequential" note) does not
cover on its own:

1. **Re-check course-level pages against final state**, not just their
   Phase 5b draft in isolation:
   - **Home page CTA** — up to now the CTA may have pointed at the course
     index or the first available lesson (since not everything existed
     yet). Confirm/update it now that the full course exists, and confirm
     it actually reflects the finished course, not a stale in-progress
     target.
   - **Course index** — every lesson should now show as live, none still
     "coming soon."
   - **Completion page** (if in scope) — this is the one course-level page
     whose *content* only becomes final at this point: verify its recap of
     "what was covered" still matches the actual, final Phase 2 outline
     (if the curriculum was revised mid-build via the "Reopening a locked
     phase" process, the completion page's recap is exactly the kind of
     thing that goes stale silently — check it explicitly, don't assume it
     auto-updated) and that it's actually reachable once a learner finishes
     the last lesson.
2. **Separate sign-off for course-level pages**, distinct from any single
   lesson's approval. Even if every individual lesson was already locked,
   present the finished home/index/completion pages together and ask
   explicitly whether they're good to lock — a learner's first and last
   impression of the course deserves its own checkpoint, not an implicit
   pass-through from lesson approvals.
3. Only after that sign-off does the course move to the final whole-course
   QA close below.

**Close with an explicit sign-off, not a silent finish.** Once every lesson
has individually cleared its per-lesson gate AND the course completion path
above is signed off, present the full punch list across the whole course to
the user and ask directly: is this good enough to consider the course done
(with any open media placeholders tracked as known follow-up), or are
there items here that need another pass before calling it finished? Don't
mark the course complete until the user confirms.

---

## General rules

- Never generate lesson prose, demo code, media scripts, or components
  that wire up audio/video before the relevant phase's questions are
  answered and its checkpoint approved.
- If the user answers out of order or gives you everything up front, still
  echo back the phase-by-phase understanding at each checkpoint so they can
  correct course before generation, but don't re-ask what they already told
  you.
- Keep each phase's questions concrete and multiple-choice where possible
  (per Phase 4 style) rather than open-ended, except where the answer is
  inherently free text (e.g. topic, outcome).
