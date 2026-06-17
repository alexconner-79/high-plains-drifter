# Design-principles profile

The profile is to design decisions what a voice profile is to writing: the part the team disagrees on, written down once so the build matches the house style instead of a generic one. The mechanical correctness is baked into the skill. The taste is yours.

Read the profile at the top of every build, in Stage 0, alongside the component inventory.

## Do not start with a blank page

The voice-profile pattern has one weakness: an empty template that nobody fills in. Avoid it. The first time the skill runs against a file, seed the profile from the team's actual system, then ask them to correct it.

- Pull the token scale and names with the Figma MCP `get_variable_defs`, and the design-system rules with `create_design_system_rules`. These give you the real spacing scale, colour roles, radii, and type ramp without asking.
- Read the component inventory (see discovery-and-mapping) to list the components that exist and the variants each supports.
- Write a draft profile from those facts, then say to the user: "Here is what I read from your system. Correct anything that is convention rather than fact." A profile they edit is far more likely to be right than one they author from scratch.

## The template

Fill this in. Leave a field blank and the skill falls back to reading it from the tokens and inventory each run.

```markdown
# Design principles: [team or product name]

## Spacing
- Scale: [e.g. 4, 8, 12, 16, 24, 32, 48]. Values off this scale are a flag.
- Default gap between sections: [token]
- Default padding inside cards and panels: [token]

## Layout and density
- Default density: [comfortable | compact]. When does the other one apply?
- Grid: [columns, gutter]
- Where do primary actions sit: [e.g. bottom-right of a form, top-right of a page]

## Component choices (the ambiguous calls)
- Inline message vs toast vs banner: [when each]
- Which card for which job: [e.g. StatCard for metrics, ContentCard for lists]
- Modal vs drawer vs full page: [when each]. What do we never put in a modal?
- Primary vs secondary vs tertiary button: [rule]

## Type
- Heading ramp: [tokens and when each applies]
- Body default: [token]
- Never: [e.g. never two H1s on a screen]

## Colour
- Semantic roles only in product UI: [background, foreground, border, etc.]. No primitive colours direct.
- Brand accent usage: [where it is allowed, where it is not]

## House refusals (the point-of-view bit)
- [e.g. no one-off components to match a sketch; raise the gap instead]
- [e.g. no sub-AA contrast, even if the design shows it]
- [e.g. no hardcoded values that break theming]

## Accessibility floor
- Minimum contrast: [AA 4.5:1 body, 3:1 large and UI]
- Minimum target size: [e.g. 44x44]
- Every interactive element has a name and a visible focus state.
```

## How the build uses it

- **Spacing and type** become the token allow-list. A value off the scale is flagged in the audit, not rounded silently.
- **Component choices** resolve the ambiguous calls the compiler cannot. When the frame shows something that could be a toast or a banner, the profile decides, and the manifest records which rule it followed.
- **House refusals** become auditor flags. They are the design equivalent of refusing a dark pattern: the build will not do them quietly.
- **Accessibility floor** feeds the accessibility check directly, so "pass" means "pass against your stated floor", not a generic one.

## Keep it true

The profile drifts as the system changes. Re-seed the token and inventory sections from Figma on a cadence, and treat a profile older than the last design-system release as suspect. A stale profile gives confident, wrong verdicts.
