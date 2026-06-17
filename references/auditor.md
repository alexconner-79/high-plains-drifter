# The auditor

A separate pass that does not trust the builder. The separation matters: the builder is biased toward producing a complete-looking screen, and that bias is exactly what makes it inline and invent. An auditor with the opposite incentive, fresh context, and no shortcuts of its own to defend is a better critic of the build than the builder is.

The audit has two layers. The floor is mechanical and close to certain. The craft tier is judgment, backed by tools where they exist. The verdict labels which flags came from which, so the human knows what is fact and what is opinion.

## The floor: things the agent cannot talk its way past

Run these as actual commands and read the output. They are the reason the skill is reliable on the mechanical half.

1. **Typecheck.** An invented component or a made-up prop (`variant="cta"` when only primary and secondary exist) fails to compile. This is deterministic. It does not matter whether the builder "followed the rules"; the build fails and the hallucination cannot ship. If the library is untyped, say so, because this floor weakens and the verdict is less certain.
2. **Lint: no raw hex or px.** A lint rule that bans literal colours and spacing turns every hardcoded value into an error with a file and line. This is the token rule, enforced rather than requested.
3. **Imports only from the library.** A check that every component import resolves to an allowed library path. A reconstructed component built from raw `<div>`s either imports nothing from the library or trips this check.
4. **Render and compare.** Render the story in Storybook (via Claude in Chrome: navigate, screenshot the live component), then compare it against the Figma `get_screenshot`. This is the step that makes the auditor look rather than imagine. A build that typechecks can still be visually wrong; the screenshot catches it.

Cross-check the floor's findings against the manifest. The dangerous case is a row that claims "mapped to Button" sitting above code that contains a hand-rolled div button. That mismatch is the silent reconstruction, and it is only catchable because Stage 1 was forced to write down what it claimed to do.

## The craft tier: the designer's eye

The compiler cannot tell a valid-but-wrong choice from a right one. This tier can, and where a tool exists it is used instead of a prompt-and-hope judgement.

### Design quality

Audited against the design-principles profile, not a generic ideal.

- **Spacing rhythm.** Are gaps and padding on the team's scale, or has the build drifted to off-scale values?
- **Hierarchy.** Does the built hierarchy match the frame's intent, or has a secondary element been promoted?
- **Alignment and balance.** Optical misalignment, lopsided fill, elements hugging when they should fill.

Where the figma-console MCP is available, `figma_check_design_parity` compares the build against the design directly, and `figma_lint_design` surfaces structural issues. Use them; they move this from opinion toward something checkable.

### Accessibility (first-class, every build)

Not a nice-to-have, and not deferred. Audited against the floor stated in the profile.

- **Contrast.** Body text and UI against their backgrounds, measured against the profile's minimum (AA: 4.5:1 body, 3:1 large and UI). A design that shows sub-AA contrast is flagged, not reproduced.
- **Target size.** Interactive elements meet the minimum (commonly 44x44).
- **Names and labels.** Every control has an accessible name. Icon-only buttons have a label. Link text is descriptive, never "learn more" alone.
- **Focus order.** The DOM order implies a sensible focus path; nothing interactive is unreachable.

Compose, do not reinvent: call the installed `accessibility-review` skill for the full WCAG 2.1 AA pass, and `figma_audit_component_accessibility` / `figma_scan_code_accessibility` where the figma-console MCP is connected. The auditor's job is to run these and fold the result into one verdict.

### Design-system integrity

The point-of-view tier. It refuses to let the build quietly fragment the system.

- **No silent reconstruction.** A component rebuilt from primitives when a real one exists is flagged with the real component named.
- **No one-offs.** A bespoke element added to match the sketch is raised as a gap, not absorbed into the screen.
- **No theme-breaking literals.** Caught by the floor's lint, reported here with the design consequence (this value will not respond to theming).

Where the figma-console MCP is available, `figma_audit_design_system` supports the drift findings that feed the health report in the verdict.

## Output of the audit

A single list of flags, each tagged:

- **certain** (from the floor: compile, lint, import, screenshot mismatch) or **judgment** (from the craft tier).
- **auto-fixable** (literal to token, reconstruction to real component) or **needs-human** (genuine gap, ambiguous component choice).

Hand this to the verdict stage. Auto-fixable certain flags can loop back into a fix pass; everything else is for the human.
