# Contributing

This skill aligns a design to a real component library instead of reconstructing it. Contributions that sharpen that job are welcome. Contributions that turn it into a generic Figma-to-code converter are not.

## Good places to help

- **The auditor checks.** Better heuristics for the craft tier (spacing rhythm, hierarchy, accessibility, integrity), or wiring more of them to real tools instead of judgement.
- **The design-profile template.** More of the ambiguous calls that teams actually disagree on, so the profile settles them.
- **Setup detection.** More reliable detection of component paths, token sources, and the typecheck, lint, and Storybook commands across different repo shapes.
- **Align mode.** This is the newest and least proven part. Real examples where it preserved scrappy decisions instead of reading intent are especially useful as test cases.

## How to propose a change

1. Open an issue describing the case, ideally with a small before and after.
2. Keep the hard rules intact. The skill's whole value is that it refuses to invent, refuses raw literals, and refuses to fragment the design system quietly. A change that softens those needs a strong argument.
3. Match the writing style: British English, no em dashes, plain and direct. The same standard the skill holds the build to.

## What it is not

Not a pixel-perfect reproduction tool. Not a replacement for design review. Not a way to ship a screen without a human looking at the judgement calls. It is a strong, honest first pass that cannot fabricate a component. Keep it that.
