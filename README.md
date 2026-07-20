# create-course

A Claude Code skill that builds a complete interactive course — curriculum,
lesson scripts, interactive demos, visual components, and audio/video — on
any topic you give it.

## What it does

`/create-course` walks through 6 phases, each gated on your answers before
any content or code gets written:

1. **Topic & audience scoping** — outcome, audience, region, age, language
   level, course depth.
2. **Curriculum arc** — a module/lesson outline, locked before any lesson
   content is drafted.
3. **Lesson scripting** — a fixed 5-part template (Hook → Case study →
   Explain → Reflect → Domain map, adaptable per topic), scripted one lesson
   at a time.
4. **Interactive demo design** — a demo format and delivery shell per
   lesson (before/after, step-flow, quiz, decision tree, etc.), spec'd
   before any component is built.
5. **Component assembly** — media (audio/video) readiness decided first,
   then a visual draft locked via mockups before real reusable components
   (including course-level home/index/completion pages) are built.
6. **QA** — an 8-point checklist run per-lesson as it's built, plus a
   separate course-completion pass once the last lesson ships.

Each phase runs as an **interview loop**: ask questions, produce a draft,
ask what's missing or off, revise, repeat until you say it's good enough —
not a single Q&A followed by a yes/no.

Production is **sequential per lesson** (script → demo → component → QA →
next lesson), not batched, so problems get caught early instead of
compounding silently across a whole course.

## Why

Course production has a lot of places to quietly cut corners: assumed tone,
unstructured audio/video work, drifted visuals, "coming soon" links that
are actually broken, batching so many lessons that early mistakes get
copied across all of them. This skill encodes a disciplined, checkpoint-based
production process so the output is complete and consistent rather than a
best-effort first draft.

## Usage

Once installed, invoke it in Claude Code:

```
/create-course
```

or trigger it naturally by asking to "build a course on X," "design a
course for Y," etc.

## Install

```bash
npx skills add <owner>/<repo>
```

(replace with this skill's published GitHub location)

## Origin

This skill was generalized from the process used to build [Fintech
Compliance for Designers](https://fintechux.framer.website/), a real
interactive course built in Framer. That build happened lesson by lesson,
by hand, without a structured process — which meant a lot of back-and-forth
between platforms: writing lesson copy in one place, jumping into Framer to
rebuild or restyle components, going back to rewrite a script after seeing
how it actually read inside the component, then back to Framer again to fix
what broke. Design decisions (colors, layout, the phone-mockup shell) got
made mid-build rather than locked up front, so early lessons sometimes
needed retrofitting once later ones revealed a better pattern.

What would have made that process easier — and what this skill is built to
enforce — is locking each layer *before* moving to the next one: a locked
curriculum outline before any lesson is scripted, a locked lesson-script
pattern before any demo is designed, a locked visual draft before any real
component code is written, and one lesson finished end-to-end (script →
demo → component → QA) before starting the next. That ordering is what
turns "keep bouncing between the doc and the design tool" into a straight
line per lesson, with revisions caught at the draft stage instead of after
the component's already built.

## Notes

- Defaults (component patterns, lesson template, visual delivery shell) are
  starting points, not requirements — the skill asks what fits your topic
  and platform rather than assuming one visual system.
- Nothing gets built ahead of an approved phase. If you skip a question or
  answer out of order, it'll be asked again rather than silently assumed —
  unless you explicitly say "use your judgment."
