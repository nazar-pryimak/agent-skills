---
name: semantic-first-css
description: Apply semantic-first CSS to components (the semantic positioning pattern in the markup, with ordered class groups, positioning tokens and __layout__ hooks; plain class names and component CSS in @layer and @scope). Use when writing, reviewing or refactoring component markup and styles in a project that follows this pattern or asks for it.
license: MIT
---

Write semantic-first CSS: each element's class says what it is first. Layout stays readable in the markup through the semantic positioning pattern (ordered class groups, positioning tokens and inline styles), and visual styling lives in plain component CSS scoped with `@layer` and `@scope`.

The project's agent instructions (e.g., `AGENTS.md`) normally name its theme token file, theme rules, and CSS entry point. Otherwise locate them, and ask if they are unclear or missing.

## Class order
Keep class groups in this order and omit any group an element does not need:

1. Identity: the element's class. On a component root, this is the unique scope class, followed by any shared scope class, single-space separated (e.g., `dashboard panel`).
2. Placement: `__layout__` and any bare `--variant` when the element has `__layout__` rules, then placement tokens (position, z-index, anchoring, self-alignment).
3. Content: child layout tokens (`grid`, `flex`, and child alignment).
4. Utilities: non-positioning classes (color, text, etc.).

Separate groups with a double space by default. Use a newline instead when it makes a long class list clearer. Tokens within a group are single-space separated.

## `__layout__`
- Write layout with tokens where one exists and the rest in an inline `style`, including grid templates of any length, so the markup shows the element's whole layout.
- Move layout into component CSS only when an inline `style` cannot express it: media and container queries, together with the base values they change (an inline value would override the query); states and pseudo-classes; styling children or descendants; and values a variant or a parent overrides.
- Write that CSS as `__layout__` rules on the element (e.g., `div.entry.__layout__ { … }`) and add `__layout__` to its class list. The class has no global CSS: it says the element's layout continues in the stylesheet, so elements without `__layout__` rules do not get it.
- In component CSS, layout properties go only in `__layout__` rules, never in the element's other rules: `display`, `position` and inset, `z-index`, sizing (`width`, `height` and their min/max), spacing (`margin`, `padding`, `gap`), grid and flex properties, and alignment (`align-*`, `justify-*`, `place-*`). Borders, outlines and shadows are visual styling, border width included, so each border stays in one visual rule.
- Keep `__layout__` rules for layout only; do not attach visual styling to them.

## Positioning rules
- Prefer `grid` over `flex`. Use `flex` only when it is the right tool.
- Use `one` to stack children in a single grid cell only when you need to. Children opt out with `cell-auto` or `rel-to`.
- Keep markup minimal: add wrappers only for layout or semantics, and prefer `grid`, `one`, and `rel-to` over deep wrappers.
- Inline styles are for layout and one-off spacing only. Visual styling goes in component CSS.

## Tokens
Tokens cover common placement and alignment, plus small patterns composed from them, such as `one`. Larger arrangements, such as multi-track grids, stay custom in an inline `style` or `__layout__` rules.

Use the project's positioning utilities, at the path named in its agent instructions (e.g., `AGENTS.md`); otherwise find the stylesheet that defines these tokens. This skill's `assets/positioning.css` defines the set below. If the project has none, propose adding it to the project's `utilities` cascade layer; where the project's copy differs, follow it. Ask before adding or reinterpreting utilities.

| Purpose | Tokens | Notes |
|---|---|---|
| Position | `rel`, `rel-to`, `fix`, `stick` | `rel-to` is `position: absolute`, for an element positioned relative to a `rel` ancestor. |
| Anchors | `t`, `r`, `b`, `l`, `top-left`, `top-right`, `bottom-left`, `bottom-right`, `inset0` | `inset0` sets all four sides to `0`. |
| Size and inset | `stretch`, `stretch-x`, `stretch-y`, `full-width`, `full-height`, `fixed-stretch`, `fixed-stretch-x`, `fixed-stretch-y` | With `fix`, `stretch` sets inset; otherwise it sets 100% width and height. Each `fixed-stretch` token equals `fix` plus the matching `stretch` token. |
| Stacking | `z-1`, `z0`, `z1`, `z2`, `z3` | |
| Display | `grid` or `g`, `one`, `cell-one`, `flex`, `if` | `one` stacks its children in the first grid cell; `cell-one` puts a single element there. `flex` and `if` accept `wrap`, `nowrap`; `flex` also accepts `col`, `row`. |
| Centering | `center`, `items-xy-center`, `content-center`, `ch-center` | `center` and `items-xy-center` set `display: grid`; `flex center` stays flex. |
| Grid child alignment | `items-x-*`, `items-y-*`, `content-x-*`, `content-y-*` | |
| Flex child alignment | `items-c-*`, `content-m-*`, `content-c-*` | `m` is the main axis and `c` is the cross axis, so they stay correct in column layouts. Prefer them on new `flex` markup. |
| Self-alignment | `-x-*`, `-y-*`, `-c-*` | Sets the element's own alignment; belongs in the placement group. |

Alignment suffixes are `start`, `center`, and `end`. `content-x`, `content-y`, and `content-m` also accept `--sp-btwn`, `--sp-around`, and `--sp-evenly`; `content-y` accepts `stretch` as well.

Aliases behave exactly like their token. New markup uses the token; leave existing aliases as they are.

| Token | Aliases |
|---|---|
| `rel` | `relative`, `anchor` |
| `rel-to` | `abs`, `absolute` |
| `fix` | `fixed` |
| `stick` | `sticky` |
| `b` | `b0` |
| `inset0` | `tlbr` |
| `full-width` | `width100`, `w100` |
| `full-height` | `height100`, `h100` |
| `grid` | `gap`, `block-fix` |
| `items-xy-center` | `items-center` |
| `col` | `column` |

## Scoping and naming
- Use a unique root class per component and anchor `@scope` to it.
- Reusing child class names across components is acceptable because `@scope` keeps a component's styles from reaching the rest of the page.
- Styles intentionally reach nested child components, which is why component CSS is not scoped by the framework (see Framework notes). A child overrides a parent rule by declaring the same property with the same selector shape, because the closer scope wins a specificity tie.
- Prefer plain, descriptive class names for internal elements (e.g., `inputs`, `input`, `close`).
- Do not name classes after global utilities or positioning tokens (e.g., `empty`, `mono`, `center`). The `utilities` layer overrides `components` regardless of specificity, so component CSS cannot override what the utility sets.
- BEM is optional; use it only when it improves clarity for a complex component.
- Use `name--variant` modifiers for state or variants (e.g., `dirty-dot--interactive`).
- Use a bare `--variant` only for a shared variant that a parent or shared rule reads (e.g., `__layout__ --wide` on panels).
- Avoid project-wide prefixes for component classes; rely on the root scope instead.

## Component CSS
- Write component CSS as global CSS wrapped in `@layer components` and `@scope`, placed as the Framework notes describe.
- Style the component root with `:scope`.
- Prefer tag + class selectors to make targets obvious (e.g., `div.input`, `button.close`).
- Keep selectors shallow so a child can still override them with the same selector shape.
- Keep visual styling in component CSS. Layout belongs in the markup or in `__layout__` rules, as described under `__layout__`.

## Theme tokens and CSS variables
- Choose tokens from the project's theme token file by role, not by similar value, for colors, radii, shadows, and other shared values.
- If no token's role matches, define one following the project's theme rules rather than hardcoding the value, apart from any exceptions those rules list.
- If a component-local CSS variable would be used only once, write the value where it is used instead of defining a variable.
- If a component-local value is reused, define the variable in the component CSS on the element it belongs to, then reference it with `var()` wherever needed, including inline styles.
- Do not prefix CSS variables just to namespace them; only prefix when collision risk is real.

## Shared micro-utilities
- If a non-positioning utility is shared by several sibling components, define it once in a shared wrapper scope.
- Allow per-component overrides when needed, but do not duplicate the base utility in each component.

## Global CSS
- Global CSS lives in cascade layers declared in the project's CSS entry point, with `components` before `utilities` (e.g., `@layer theme, base, framework, components, utilities, overrides;`).
- Do not add unlayered global rules; put each rule in the layer it belongs to.

## Framework notes
Keep component CSS global, where the framework does not rewrite or isolate it:
- Vue: a `<style>` block without `scoped`.
- Astro: `<style is:global>`.
- Svelte: a plain `.css` file imported as global CSS, since Svelte scopes `<style>` blocks.
- React, Preact, Solid and similar: a plain `.css` file per component, imported as global CSS rather than a CSS Module. Classes go in `className`, and inline layout styles are style objects.
- Angular: component styles with `encapsulation: ViewEncapsulation.None`.
- Server templates and plain HTML: a `.css` file per partial or component.

These conventions do not suit CSS Modules, CSS-in-JS or Shadow DOM components: generated class names and shadow boundaries break the root class, shared child names and rules that reach child components.

## Examples
A central element with its grid template inline. Nothing overrides it, so it does not need `__layout__`:

```html
<main
  class="page  grid content-x-center"
  style="
    grid-template-rows:
      [header ] min-content
      [content] minmax(0, 1fr)
      [footer ] min-content;
    grid-template-columns: minmax(0, 72rem);
    gap: 1rem; padding: 1rem;
  "
>...</main>
```

Identity on its own line for a long class list:

```html
<div class="status-bar
           fix r b l  grid items-y-center"></div>
```

A Vue single-file component where only `row` gets `__layout__`: its columns change at 640px, so their base value sits in the same CSS, where the query can override it.

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

A fuller component that combines the rules:

```vue
<template>
  <section
    class="gallery  grid"
    :class="{ 'gallery--dense': dense }"
    style="
      grid-template-rows:
        [header] min-content
        [items ] minmax(0, 1fr);
      gap: 0.75rem;
    "
  >
    <header class="toolbar  stick t z2  flex items-c-center content-m--sp-btwn" style="padding-block: 0.5rem">
      <h2 class="title">Photos</h2>
      <span class="count  mono">{{ items.length }} items</span>
    </header>

    <ul class="items  __layout__  grid">
      <li v-for="item in items" :key="item.id" class="item  rel  one">
        <img class="thumb" :src="item.src" alt="" />
        <div class="caption  __layout__ -y-end  grid" style="padding: 0.5rem">
          <strong class="name">{{ item.name }}</strong>
          <p class="details">{{ item.details }}</p>
        </div>
        <button class="remove  rel-to t r z1" aria-label="Remove" style="margin: 0.25rem">×</button>
      </li>
    </ul>
  </section>
</template>

<style>
@layer components {
@scope (.gallery) {
header.toolbar { background: var(--color-surface); border-bottom: 1px solid var(--color-border); }
div.caption { background: var(--color-overlay); color: var(--color-on-overlay); }
p.details { overflow: hidden; }

ul.items.__layout__ { grid-template-columns: repeat(3, minmax(0, 1fr)); gap: 1rem; }
:scope.gallery--dense ul.items.__layout__ { gap: 0.25rem; }

div.caption.__layout__ { grid-template-rows: min-content 0fr; }
li.item:is(:hover, :focus-within) div.caption.__layout__ { grid-template-rows: min-content 1fr; }

@media (max-width: 720px) {
  ul.items.__layout__ { grid-template-columns: repeat(2, minmax(0, 1fr)); }
}
}
}
</style>
```

- `section.gallery` keeps its row template inline: it is the central layout and nothing overrides it, so it does not need `__layout__`.
- `ul.items` has `__layout__` because its columns change at 720px and its gap changes with `gallery--dense`, so both base values live in CSS.
- `div.caption` has `__layout__` because its second row opens on hover or focus, a state that inline styles cannot express.
- `li.item` stacks the image and caption in one cell with `one`; `-y-end` pins the caption to the bottom, and `rel-to t r z1` takes the remove button out of the cell into the top-right corner of the `rel` item.
- `header.toolbar` sticks to the top with `stick t z2`, above the buttons' `z1`, and spreads its children with main-axis flex tokens.
- Visual styling stays in plain rules that use theme tokens by role; `mono` stands for a project text utility in the last class group.

## Trigger examples
- "Update this component to follow semantic-first CSS."
- "Move this panel's layout into the markup with positioning tokens and inline styles."
- "Refactor this component to use plain class names and scoped selectors."
