# Verdict and health report

The verdict is the part the designer reads. Write it for them, not for a compiler. Diagnose first, then list the fixes, the way a good review names the biggest patterns before the line edits.

## Speak designer, not stack trace

A designer does not want a TypeScript error dump. They want to know what landed, what did not, and what to do about it.

Bad:

```
TS2322: Type '"cta"' is not assignable to type '"primary" | "secondary"'.
```

Good:

```
Header button: built with your Button, primary variant, bottom-right per your
layout rule. Clean.

Filter chips: no component for these in the library yet. I left a placeholder
and added them to the health report below. Closest existing option is Tag, but
it does not support the multi-select behaviour the design implies, so I did not
force it.
```

## Structure of the verdict

1. **One-line summary.** How much mapped cleanly, how many gaps, whether the build is verified (typechecked, linted, screenshot compared) or unverified.
2. **What mapped cleanly.** Brief, so the designer trusts the rest without reading every row.
3. **What needs a human.** The judgment flags: ambiguous component choices, medium-confidence rows, genuine gaps. This is the review list.
4. **What was fixed or can be auto-fixed.** The certain, auto-fixable flags (literal to token, reconstruction to real component).
5. **The health report** (below).

Always label each flag **certain** or **judgment**. The designer should know that "this prop does not exist" is fact and "this should probably be a banner not a toast" is opinion. That labelling is what lets them merge the certain parts and review only the judgment.

## The design-system health report

The gap list is worth more than a build artefact. Read the other way, it is a drift report between the two systems, and a designer would run the skill for this alone. Attach it every time.

```markdown
## Design-system health, from this build

### Off-token values (your scale may have a gap)
- 14px padding used 6 times where the scale jumps 12 to 16. Worth a token?
- One-off border colour #E3E3E3 with no semantic role.

### In Figma, missing from code
- Filter chips: in the design, no component in the library. Raise to eng.

### In code, missing from Figma
- Button has loading and ghost variants your Figma Button does not show.
  Your sketches cannot reach for states the library already supports.

### Forced or low-confidence choices
- Status pill mapped to Badge (medium confidence). No profile rule for
  pill vs tag. Worth deciding once and adding to the profile.
```

The "in code, missing from Figma" section is the one that turns the maturity gap to your advantage: it tells the designer what the system already supports that their sketch did not use, so the build teaches the design rather than only translating it down.

## Persist the record, then close the loop in Figma if you can

Save the verdict and the health report to an audit file at the repo root every run (for example `FUNDS-ACTIVITY-AUDIT.md`). This is the canonical output. It is reliable, it travels with the code, and it doubles as the artefact you hand the design-system owner. Always do this.

Then, **if** the Figma connector supports writing, also post the summary back onto the frame as a comment (`figma_post_comment`), so it lands where the designer works. Treat this as a bonus, not the main delivery. Most Figma connections are read-only, so writing will often be unavailable; when it is, say so and rely on the audit file rather than treating the file as a consolation prize. A verdict the designer has to go looking for is a verdict they do not read, so the file is what guarantees the report survives.

## Feed the profile

Every medium-confidence row and every "forced choice" in the health report is a missing rule in the design-principles profile. Offer to add the resolved decision back to the profile, so the same ambiguity does not recur on the next build. This is how the skill gets more reliable with use rather than repeating the same judgement calls.
