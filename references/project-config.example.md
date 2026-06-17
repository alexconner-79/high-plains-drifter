# Project config (example)

Written by first-run setup, read at the top of every run. Edit by hand any time. Copy this to `project-config.md` at the repo root (or `.drifter/project-config.md`) and fill it in, or let setup generate it for you.

```yaml
# What the project is
framework: react
typescript: true
package_manager: pnpm

# Component libraries, in precedence order (first wins when both export the same component)
libraries:
  - name: ui
    path: packages/ui/src
    import: "@app/ui"
  - name: gallery
    path: apps/client-portal-sandbox/src/components/ui
    import: "@app/sandbox-ui"

# Where tokens come from. The names here are the allow-list the build must use.
tokens:
  source: packages/ui/src/theme/tokens.css
  figma_file: "https://www.figma.com/design/XXXX/Design-System"   # optional, for get_variable_defs

# Commands the auditor floor runs. No typecheck or lint means verdicts are labelled unverified.
commands:
  typecheck: "pnpm tsc --noEmit"
  lint: "pnpm lint"
  storybook_dev: "pnpm storybook"     # for the render-and-compare step
  build: "pnpm build"

# How the screen gets rendered for the visual diff
storybook:
  mode: local            # local | hosted
  url: "http://localhost:6006"

# Visual check: needs a headless browser to render and screenshot.
# If false, the visual step is skipped and every verdict is visually unverified.
visual_check:
  headless_browser: true   # true | false, set at setup
  tool: chrome             # e.g. chrome (Claude in Chrome), playwright, puppeteer

# Optional defaults
defaults:
  figma_file: "https://www.figma.com/design/YYYY/Payment-Management"
  target_app: apps/client-portal-sandbox   # where built screens are composed
```

## Notes on each field

- **libraries / precedence:** the single most important thing to get right. Without a precedence rule, two builds resolve the same Button differently and you get drift.
- **tokens.source:** the code token file is the allow-list. The Figma file is the cross-reference. When they disagree, the disagreement is itself a health-report finding.
- **commands.typecheck and lint:** these are the enforcement floor. If they are blank, the skill still builds but says so and downgrades its confidence.
- **storybook:** local means the skill starts the dev server and screenshots via Chrome; hosted means it navigates to the URL. Either way this powers the render-and-compare step, the part that makes the auditor look rather than imagine.
- **defaults:** purely convenience, so the user does not paste the same file URL every time.
