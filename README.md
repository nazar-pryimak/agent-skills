# Agent Skills

**Layout in the markup. Looks in the CSS.**

Two skills that give coding agents a CSS house style built on modern browser features, for projects that prefer native CSS over Tailwind or other utility frameworks. They:

- separate layout from styling, keeping layout in the HTML and looks in the CSS
- make class lists readable at a glance: what an element is, where it sits, how it lays out its children
- use a small set of positioning classes for the usual cases, with anything bigger kept custom
- scope component styles with native `@scope` and `@layer`, so class names stay plain: no BEM, prefixes or generated names
- keep your coding agent consistent, instead of it inventing a new convention in every component

They work with Claude Code, Codex, Cursor and other coding agents that support the `SKILL.md` format.

| Skill | Covers |
|---|---|
| [`component-css`](skills/component-css/SKILL.md) | Component class naming and CSS built on `@layer components` and `@scope`, plus theme tokens and CSS variables. |
| [`semantic-positioning`](skills/semantic-positioning/SKILL.md) | Class order, `__layout__` hooks, and positioning tokens that keep layout readable from the markup. The tokens ship as [`positioning.css`](skills/semantic-positioning/assets/positioning.css). |

## Install

With the [`skills` CLI](https://github.com/vercel-labs/skills):

```sh
npx skills add nazar-pryimak/agent-skills
```

Add `--skill <name>` to install a single skill, or `--list` to see what is available. You can also copy a skill's folder into your agent's skills directory, such as `.claude/skills/` for Claude Code.

## Using the UI skills

Use `component-css` and `semantic-positioning` together. Class lists follow a fixed order, with groups separated by double spaces: identity, placement, content layout, then utilities:

```html
<div class="status-bar  fix r b l  grid items-y-center"></div>
```

A component with its CSS, where only `row` needs `__layout__` because its columns change at 640px:

```vue
<template>
  <ul class="settings-list  grid">
    <li class="row  __layout__  grid items-y-center" style="gap: 0.5rem 1.5rem; padding-block: 0.75rem">
      <div class="label  grid" style="gap: 0.25rem">...</div>
      <button class="action">...</button>
    </li>
  </ul>
</template>

<style>
@layer components {
@scope (.settings-list) {
li.row + li.row { border-top: 1px solid var(--color-border); }
li.row.__layout__ { grid-template-columns: minmax(0, 1fr) max-content; }

@media (max-width: 640px) {
  li.row.__layout__ { grid-template-columns: minmax(0, 1fr); }
}
}
}
</style>
```

For a fuller component, see the [examples in `semantic-positioning`](skills/semantic-positioning/SKILL.md#examples).

They assume a project where:

- Global CSS is organized in cascade layers with `components` before `utilities`, e.g. `@layer theme, base, framework, components, utilities, overrides;`.
- The positioning tokens are in the `utilities` layer: copy `positioning.css` into the project and import it with `@import './positioning.css' layer(utilities);`.
- Component styles are global CSS wrapped in `@layer components` and `@scope`. The skills were written for and used with Vue single-file components; `component-css` lists the equivalents for Astro, Svelte, React-style frameworks, Angular and server templates.
- Target browsers support cascade layers and `@scope` (Chrome and Edge 118, Safari 17.4, Firefox 146). A browser without `@scope` drops component styles entirely; the positioning tokens still work.

They are not a good fit for projects built on Tailwind or another utility framework (two vocabularies, some clashing names), CSS Modules or CSS-in-JS (generated class names), or Shadow DOM components (styles cannot cross the shadow boundary).

Name the project's files in its agent instructions (`AGENTS.md` or `CLAUDE.md`) so the skills can find them, for example:

```md
Use `component-css` and `semantic-positioning` for UI work. Positioning utilities: `src/styles/positioning.css`. Theme tokens: `src/styles/theme.css`, with role rules in `docs/theme.md`. Cascade layers: `src/styles/main.css`.
```

## License

[MIT](LICENSE)
