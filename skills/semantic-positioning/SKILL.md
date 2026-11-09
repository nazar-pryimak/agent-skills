---
name: semantic-positioning
description: Apply the semantic positioning class pattern (ordered class groups, __layout__ hooks, positioning tokens) to component markup. Use when writing or reviewing frontend templates in a project that follows this pattern or asks for it.
license: MIT
---

Follow the semantic positioning pattern to make layout intent readable from the HTML. Use it together with `component-css`, which owns component CSS and naming.

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
- In component CSS, layout properties go only in `__layout__` rules, never in the element's other rules: `display`, `position` and inset, `z-index`, sizing (`width`, `height` and their min/max), spacing (`margin`, `padding`, `gap`), grid and flex properties, and alignment (`align-*`, `justify-*`, `place-*`).
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

## Trigger examples
- "Update this component markup to follow the semantic positioning class pattern."
- "Move this panel's layout into the markup with positioning tokens and inline styles."
