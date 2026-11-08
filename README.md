# Agent Skills

Skills for Claude Code, Codex, and other coding agents that support the `SKILL.md` format.

| Skill | Covers |
|---|---|
| [`component-design`](skills/component-design/SKILL.md) | Component class naming and CSS built on `@layer components` and `@scope`, plus theme tokens and CSS variables. |
| [`semantic-positioning`](skills/semantic-positioning/SKILL.md) | Class order, `__layout__` hooks, and positioning tokens that keep layout readable from the markup. The tokens ship as [`positioning.css`](skills/semantic-positioning/assets/positioning.css). |

## Install

With the [`skills` CLI](https://github.com/vercel-labs/skills):

```sh
npx skills add nazar-pryimak/agent-skills
```

Add `--skill <name>` to install a single skill, or `--list` to see what is available. You can also copy a skill's folder into your agent's skills directory, such as `.claude/skills/` for Claude Code.

## Using the UI skills

`component-design` and `semantic-positioning` work as a pair: component CSS holds visual styling, while layout stays readable from the markup as ordered class groups (identity, placement, content, utilities):

```html
<div class="status-bar  __layout__ fix r b l  grid items-y-center"></div>
```

They assume a project where:

- Global CSS is organized in cascade layers with `components` before `utilities`, e.g. `@layer theme, base, framework, components, utilities, overrides;`.
- The positioning tokens are in the `utilities` layer: copy `positioning.css` into the project and import it with `@import './positioning.css' layer(utilities);`.
- Component styles are non-scoped `<style>` blocks wrapped in `@layer components` and `@scope`. The skills were written for Vue single-file components.
- Target browsers support cascade layers and `@scope`.

Name the project's files in its agent instructions (`AGENTS.md` or `CLAUDE.md`) so the skills can find them, for example:

```md
Use `component-design` and `semantic-positioning` for UI work. Positioning utilities: `src/styles/positioning.css`. Theme tokens: `src/styles/theme.css`, with role rules in `docs/theme.md`. Cascade layers: `src/styles/main.css`.
```

## License

[MIT](LICENSE)
