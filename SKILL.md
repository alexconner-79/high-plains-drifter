---
name: high-plains-drifter
description: Align a design to your real Storybook components instead of reconstructing it. Use when turning a Figma frame into React built from an existing component library (Build mode), or when pulling scrappy exploratory code into line with the library once a direction feels right (Align mode). Triggers include "build this Figma screen with our components", "turn this design into React", "align this code to our library", "map this to Storybook", "make this real with our components", "design to code". Reads the library as the source of truth, resolves every element to a real component, refuses to invent or reconstruct, and audits the result for design quality, accessibility, and design-system integrity.
---

# High Plains Drifter

**Rides into the drift between your design and your code, and brings them into line.**

Most Figma-to-code tools reconstruct what they see: they read the picture and emit new markup. That is the wrong job when your React library is more mature than your Figma file, which is the common case once a team moves to design in code. This skill flips it. The library is the source of truth. The design is intent. Every element resolves to a real component or it gets flagged, never invented.

It is an alignment pass, not a converter. Whether the starting material is a clean frame or rough code you wrote while exploring, the job is the same: pull it toward the system you already have.

Built by a designer, for teams whose code design system has outgrown their Figma one.

## Two modes

Decide which one applies, then follow it.

- **Build mode**: a Figma frame to React, composed from real components. The clean-handoff case.
- **Align mode**: scrappy code (and optionally a frame for reference) brought into line with the library. The consolidation case, for when you have sketched loosely between Figma and code, found a direction you like, and now want it built on the real components and tokens.

Both run the same engine: discover the library, resolve toward it, record a manifest, audit, then report. Only the starting material differs.

### When to stay out of the way

During exploration, when you are still sketching and throwing up throwaway components to test a direction, this skill should not run. "Never invent, tokens only, flag every gap" is the wrong constraint while you are still finding the shape, and imposing it would kill the explore. The skill is a consolidation tool. It earns its keep at the "now make it real" moment, not before.

## The one idea

The prompt is guidance. The compiler is enforcement. A rule asking the agent not to invent components reduces how often it does; a typecheck makes an invented component fail to build. So this skill leans on the things the agent cannot talk its way past (typecheck, lint, a rendered screenshot) and uses prose only for the judgment the compiler cannot reach.

This means it is most reliable on the mechanically checkable things (no invented component, no made-up prop, no raw hex) and least reliable on judgment (the right component for an ambiguous element). It labels which is which in the verdict, so a human reviews the judgment and trusts the rest.

## What you need before it works well

- A React component library the agent can read (source or a published Storybook), ideally typed.
- A Figma frame or some rough code to align, with the Figma MCP connected for the frame case.
- A typecheck and lint command it can run. Without these the skill still produces a build, but the enforcement floor is gone and you are back to trusting the model.
- A headless browser for the visual check (Claude in Chrome, or a Playwright or Puppeteer install). Without one, the build still runs but the verdict is visually unverified, which means it can be all-correct components yet wrong on spacing or layout.

If any of these is missing, say so up front rather than promising reliability the setup cannot deliver. The visual check in particular is the difference between "structurally clean" and "actually looks right", so flag a missing headless browser at setup, not at the verdict.

## The workflow

Follow the stages in order. Each stage writes down what it did so the next stage can check the claim rather than guess at intent. The references hold the detail.

### Stage 0: Set up, then discover

First, check for `project-config.md` (repo root, or `.drifter/`). If it is missing, run first-run setup: detect the component paths, token source, and the typecheck, lint, and Storybook commands from the repo, propose them, confirm with the user, and write the config. The config is read on every later run with no further questions. See [references/setup.md](references/setup.md) and [references/project-config.example.md](references/project-config.example.md).

Then read two things and cache them.

1. The **component inventory**: every component in the library with its real prop and variant API, read from source or stories, plus the design tokens. See [references/discovery-and-mapping.md](references/discovery-and-mapping.md).
2. The **design-principles profile**: the team's conventions, read at the top of every run. Seed it from the Figma file's own tokens and rules the first time, then let the team edit. See [references/design-profile.md](references/design-profile.md).

> **Hard gate:** no building starts until the config and a fresh inventory exist. "Use the library" without them just becomes "use what the agent imagines the library has", which is the same invention wearing a library badge.

### Stage 1: Resolve and build

Take the starting material and resolve it toward the library.

- In **Build mode**, read the Figma frame for intent and structure (layout, hierarchy, what each element is), not for pixels to copy.
- In **Align mode**, read the rough code for the same: what each piece is meant to be, regardless of how scrappily it was thrown together. A frame, if supplied, is reference.

Resolve each element to an inventory component by role and compose the React. Output two artefacts: the code, and a **mapping manifest**, one row per element recording the decision (mapped to X, composed from primitives, or no match and placeholder). The manifest is what makes everything downstream auditable. See [references/discovery-and-mapping.md](references/discovery-and-mapping.md).

> **Hard gate:** when no component matches with confidence, drop a clearly named placeholder and log it. Never synthesise a new component, never inline markup that duplicates something the library already has, never pass a prop that is not in the component's real API.

> **New components are a finding, not a failure.** Align mode often surfaces a throwaway you invented while exploring that turns out to be a genuine gap in the system. Do not force it onto an ill-fitting existing component. Route it to the health report as a candidate, so "promote to the design system" versus "replace with existing" is a decision the team makes, not one the skill makes silently.

### Stage 2: Audit

A separate pass that does not trust Stage 1. It runs a floor and a craft tier. See [references/auditor.md](references/auditor.md).

The **floor** is mechanical and close to certain: typecheck (invented components and props fail), a no-raw-hex-or-px lint rule, an imports-only-from-the-library check, and a rendered screenshot of the story compared against the design.

The **craft tier** is the designer's eye, backed by tools where they exist: design quality (spacing rhythm, hierarchy, alignment), **accessibility** (contrast, target size, labels, focus order), and design-system integrity (no silent reconstructions, no theme-breaking literals, no one-off that fragments the system).

### Stage 3: Verdict and reconcile

Write the result for a designer, not as a stack trace. Diagnose first, then list fixes. Split flags into machine-certain and judgment so the human knows what to review. Reframe the gap list as a **design-system health report**: off-token usage, what the design has that code lacks, what code has that the design lacks, and any candidate new components Align mode surfaced. Save the verdict and health report to an audit file at the repo root as the canonical record, every run. Where the Figma connector supports writing, also post it back as a comment on the frame as a bonus; most connections are read-only, so do not treat the file as a fallback. See [references/verdict.md](references/verdict.md).

## The hard rules

1. **The library is the source of truth, the design is intent.** Read the design for what each thing is and where it goes. Read the library for how it is actually built. Where the design shows fewer states than the component supports, the component wins.
2. **Never invent to fill a gap.** No new component, no inlined duplicate, no made-up prop or variant value. No match means a placeholder and a flag.
3. **Tokens, never literals.** Every colour, spacing, radius, and type value resolves to a token. A value with no token is a flag, not a quiet rounding to the nearest one.
4. **The build step is not optional.** A run that does not end in typecheck, lint, and a rendered screenshot is the unreliable version. If they cannot run, say the verdict is unverified.
5. **Accessibility is a first-class check, not a nice-to-have.** Contrast, target size, labels, and focus order are audited on every run and reported with the rest.
6. **Refuse to fragment the design system quietly.** A reconstruction of a component that already exists gets flagged, not shipped. A genuine new component gets raised as a candidate, not absorbed.
7. **Stay out of exploration.** This is a consolidation tool. It does not impose its gates while the team is still sketching a direction.

## Composing with other skills

Do not reinvent what is already installed. The auditor can call `accessibility-review` for the WCAG pass and `design-system` for the integrity and drift checks, and any copy in the built screen should be vetted with a UX-writing pass so the labels are not slop either.

## Final pass

Before delivering: confirm the build typechecks, the screenshot was taken and compared, every manifest row is accounted for in the verdict, and the design-system health report is attached. An unverified build is labelled unverified.
