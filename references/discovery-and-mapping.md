# Discovery and mapping

Two stages live here: building the inventory (Stage 0) and resolving the frame against it (Stage 1). The thread running through both is that every claim gets written down, so the auditor checks reality against a record instead of reverse-engineering intent from raw code.

## Stage 0: Build the inventory

The inventory is the vocabulary. Build it fresh each run. Do not rely on component names from memory or a previous session, because that is how the agent ends up referencing props that were renamed or components that were removed.

Read, in order of trust:

1. **Component source or stories.** For each component, record its name, import path, and its real prop and variant API. Read the actual prop types, not a guess. A typed library makes this exact. Where there is a Storybook, the stories show the supported states, which the rough Figma frame often does not.
2. **Tokens.** Pull the token names and values with the Figma MCP `get_variable_defs`, and cross-check against the code token source (the theme file or token package). The names in code are the allow-list the build must use.
3. **Two libraries, one precedence rule.** If more than one library is in play (for example a vendored `ui/` directory and a separate gallery), decide which wins when both expose a Button, and record it. Ambiguity here produces drift, where two builds resolve the same element differently.

Record the inventory as a structured list the later stages can read:

```
Component: Button
  import: @app/ui
  props: { variant: 'primary'|'secondary'|'ghost', size: 'sm'|'md'|'lg', disabled: boolean, icon: ReactNode }
  states in stories: default, hover, focus, disabled, loading, with-icon

Component: Card
  import: @app/ui
  props: { density: 'comfortable'|'compact', interactive: boolean }
  ...
```

> **Hard gate:** Stage 1 does not start until the inventory exists. The inventory is the thing that turns "use the library" from a hope into a check.

## Stage 1: Resolve and build

### Read the starting material for intent

The input depends on the mode, but the reading is the same: what is each element meant to be, where does it sit, what is the hierarchy, which actions are primary.

- **Build mode:** pull the frame with the Figma MCP (`get_design_context`, `get_metadata`, `get_screenshot`). Do not read it as pixels to reproduce. The frame is the weaker system; reproducing it faithfully would downgrade the build to its level.
- **Align mode:** read the rough code the user wrote while exploring. Read it for intent, not as something to preserve. A hardcoded colour, an inline one-off component, a div doing a button's job: these are notes toward a direction, not decisions to keep. A frame, if supplied, is extra reference.

### Resolve each element by role

For each element, find the inventory component that matches its role. Use the design-profile to settle the ambiguous calls (toast vs banner, which card, modal vs drawer). Three outcomes, and only three:

1. **Mapped.** A real component fits with confidence. Use it, with props drawn only from its real API.
2. **Composed.** No single component is the whole element, but existing primitives arranged together are. Build it from them. A card with an unusual header is a composition, not a gap.
3. **No match.** Nothing real fits. Drop a clearly named placeholder (for example `<TODO_FilterChips />`) and log it. Do not invent. In Align mode, where the no-match started life as a throwaway component you wrote while exploring, log it as a **candidate** for the health report, so the team can decide whether to promote it to the system or replace it with something existing. Do not force-fit it onto an ill-matching component to avoid the flag.

The line between "composed" and "no match" is the one that decides whether the skill is usable. Flag too eagerly and the screen is all placeholders and nobody uses it. Force matches too hard and you get a Toast standing in for an inline alert. The profile's component-choice rules are what keep this line steady.

### Write the manifest

One row per element. This is the artefact that makes the audit possible and cheap, because "did you do what you said" is far easier to verify than "is this code right".

```
| element (frame)      | outcome   | resolved to              | confidence | notes                          |
|----------------------|-----------|--------------------------|------------|--------------------------------|
| Header CTA           | mapped    | Button variant=primary   | high       | profile: primary bottom-right  |
| Recent activity card | mapped    | ContentCard              | high       |                                |
| Stat tiles           | composed  | Card + Text + Icon       | high       | no StatCard in library         |
| Filter chips         | no match  | <TODO_FilterChips/>      | n/a        | gap: raise to design system    |
| Status pill          | mapped    | Badge tone=warning       | medium     | could be Tag; profile unclear  |
```

Medium-confidence rows are a signal to the verdict that a human should look, and a signal to the profile that a rule is missing.

### Tokens, not literals, as you build

Bind every colour, spacing, radius, and type value to a token from the inventory. A value in the frame with no matching token is not rounded silently to the nearest one; it is built with the closest token and the substitution is logged, because a recurring off-token value usually means the token scale itself has a gap worth raising.

Hand the code and the manifest to the auditor.
