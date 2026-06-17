# high-plains-drifter

**Rides into the drift between your design and your code, and brings them into line.**

A Claude skill that aligns a design to your real Storybook components, instead of reconstructing it as a fresh pile of divs.

Most Figma-to-code tools read the picture and emit new markup. That is the wrong job when your React library is more mature than your Figma file, which is the common case once a team moves to design in code. This skill flips it: the component library is the source of truth, the design is intent, and every element resolves to a real component or it gets flagged. It never invents one.

It is an alignment pass, not a converter. The starting material can be a clean Figma frame or the rough code you wrote while exploring a direction. Either way the job is the same: pull it toward the system you already have.

Built by a designer, for teams whose code design system has outgrown their Figma one.

## Two modes

- **Build**: a Figma frame to React, composed from real components. The clean handoff.
- **Align**: scrappy exploratory code brought into line with the library, once a direction feels right. The consolidation step.

It stays out of the way while you are still exploring. It is a consolidation tool, not an always-on gate.

## The idea

The prompt is guidance. The compiler is enforcement.

A rule asking the agent not to invent components reduces how often it does. A typecheck makes an invented component fail to build. So the skill leans on the things the agent cannot talk its way past (typecheck, lint, a rendered screenshot) and uses prose only for the judgment the compiler cannot reach. It is reliable on the mechanical half (no invented component, no made-up prop, no raw hex) and honest about the judgment half (the right component for an ambiguous element), and it labels which is which so a human reviews the right things.

## What it does

- **Discovers** your component library and tokens as the source of truth, and reads a design-principles profile seeded from your own Figma system.
- **Resolves** each element to a real component by role, recording every decision in a manifest. No match means a placeholder and a flag, never an invention. A genuine new component is raised as a candidate, not absorbed.
- **Audits** the result on two layers: a mechanical floor (typecheck, no-raw-value lint, library-only imports, a rendered screenshot compared to the design) and a craft tier (design quality, accessibility, design-system integrity).
- **Reports** in language a designer reads, saves the verdict and a design-system health report to an audit file every run, and posts back to Figma where the connector allows.

## What's inside

```
high-plains-drifter/
├── SKILL.md                          The skill: modes, workflow, hard gates, rules
└── references/
    ├── setup.md                      First-run setup: detect, confirm, write config
    ├── project-config.example.md     Where your paths, tokens, and commands live
    ├── design-profile.md             Fill-once design conventions, auto-seeded
    ├── discovery-and-mapping.md      Inventory, resolution, the manifest
    ├── auditor.md                    The floor and the craft tier
    └── verdict.md                    Designer-facing verdict and health report
```

## Installation

### Claude Code

```
mkdir -p ~/.claude/skills
git clone https://github.com/alexconner-79/high-plains-drifter.git ~/.claude/skills/high-plains-drifter
```

### Claude.ai / Claude Desktop

Download the packaged `high-plains-drifter.skill` from [Releases](https://github.com/alexconner-79/high-plains-drifter/releases) and upload it in Settings, under Capabilities.

### First run

On first use it runs setup: it detects your component paths, tokens, and the typecheck, lint, and Storybook commands, proposes them, and writes a config so it only asks once.

## What you need

- A React component library Claude can read (source or a published Storybook), ideally typed.
- A Figma frame or some rough code to align, with the Figma MCP connected for the frame case.
- A typecheck and lint command. Without these the skill still builds, but the enforcement floor is gone, and it says so.

## The hard rules

1. The library is the source of truth, the design is intent.
2. Never invent to fill a gap. No match means a placeholder and a flag.
3. Tokens, never literals.
4. The build step is not optional. No typecheck and screenshot means the verdict is labelled unverified.
5. Accessibility is a first-class check on every run.
6. Refuse to fragment the design system quietly.
7. Stay out of exploration.

## Composes with

`accessibility-review` for the WCAG pass, `design-system` for integrity and drift, the Figma MCP and figma-console tools for parity and accessibility checks, and a UX-writing pass so the copy in the build is not slop either. These are optional enhancers. The skill works without them; where they are installed it uses them instead of reinventing their checks.

## The name

High Plains Drifter is the 1973 Clint Eastwood western, and, less obviously, a track on the Beastie Boys' Paul's Boutique. So it is a quiet sibling to ill-communication, another skill named from the same record collection. The fit: a lone stranger rides into a town that has drifted into disorder, sets it straight, and leaves. Swap the town for a codebase that has drifted away from its design system and you have the skill. It brings the drift into line, then rides out.
