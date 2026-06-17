# First-run setup

The skill needs to know where this project keeps its components, tokens, and stories, and which commands run the typecheck, lint, and Storybook. It learns this once and writes it to `project-config.md`, then reads it on every run.

## When setup runs

At the top of Stage 0, check for `project-config.md` next to the skill (or at the repo root, see below).

- **Config present and current:** read it and proceed to discovery. Do not ask the user anything.
- **Config absent:** run the setup routine below before building.
- **User says "re-run setup", or paths and commands have changed:** run it again and overwrite.

> Treat this as a hard gate. Building without a config means guessing at the component paths and skipping the enforcement floor, which is the unreliable version the skill exists to avoid.

## The routine: detect, propose, confirm, write

Do not interrogate the user with a blank form. Detect what you can from the repo, propose a filled-in config, and ask them only to confirm or correct it.

### 1. Detect

- **Commands.** Read `package.json` scripts. Map the obvious ones: a `tsc` or `typecheck` script, a `lint` script, a `storybook` or `storybook dev` script, a `build` script. Note the package manager from the lockfile (npm, pnpm, yarn).
- **Component libraries.** Glob for likely component homes (`**/components/ui/**`, `**/packages/*/src/**`, a `ui` package, a `react-components` or gallery directory). Read a couple of files to confirm they are components with exported props. If more than one library turns up, note all of them; precedence is a question for the user.
- **Import aliases.** Read `tsconfig.json` `paths` and any bundler alias config so the config records how components are imported (for example `@app/ui`), not just where they live on disk.
- **Tokens.** Find the token source: a theme file, a token package, CSS custom properties (`:root { --... }`), a Tailwind config. Record the path. Cross-reference with the Figma MCP `get_variable_defs` when a Figma file is available.
- **Storybook.** Look for `.storybook/`. Record whether it runs locally (and the dev command and port) or is hosted (ask for the URL).
- **Headless browser for the visual check.** The visual step renders the screen and screenshots it, which needs a headless browser (a browser that runs invisibly so it can be driven by code, for example via Claude in Chrome or a Playwright or Puppeteer install). Check whether one is reachable in this environment. If not, the visual check cannot run and every verdict will be visually unverified until one is added.
- **Framework and typing.** Confirm React, and whether the project is TypeScript. The typing answer decides how strong the enforcement floor is.

### 2. Propose

Show the detected config back as a draft and say plainly what you inferred and what you are unsure about. For example: "I found two component directories. I am guessing `packages/ui` is the primary library and `apps/.../react-components` is the gallery. Which wins when both export a Button?"

### 3. Confirm

Get the user to settle the things detection cannot:

- Precedence between multiple libraries.
- The correct typecheck and lint commands if the scripts were ambiguous or missing.
- The Storybook URL if hosted.
- A default Figma file, if they want one saved.

If the project has no typecheck or lint, say so directly: the enforcement floor will be weak, and the skill will label its verdicts unverified until those exist. Offer to add a basic `tsc --noEmit` script and a no-raw-value lint rule.

If no headless browser is reachable, warn loudly here, at setup, not at the verdict: "I cannot run the visual check in this environment, so every verdict will be visually unverified until you add a headless browser. The build can still be all-correct components and zero hardcoded values yet look wrong on spacing or layout, and this is the step that would catch that." Then let the user decide to proceed with that known limitation or to add one. Record the answer so later runs do not re-surprise them.

### 4. Write

Write `project-config.md` from the confirmed answers (see `project-config.example.md` for the shape). Then trigger the design-profile auto-seed in the same pass, since both are first-run and both read from the same tokens and inventory. Tell the user where both files live and that they can edit them by hand any time.

## Where the config lives

Prefer the repo root or a `.drifter/` directory in the project, so the config travels with the codebase and the whole team shares one. The skill's own folder is a fallback for when the skill is run outside a repo. Record the chosen location so later runs look in the right place first.
