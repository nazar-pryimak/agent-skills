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
A central element with its grid template inline. It keeps `__layout__ --wide` because a shared panel rule reads the variant:

```html
<div
  class="dashboard panel  __layout__ --wide  g content-x-center content-y--sp-evenly"
  style="
    grid-template-rows:
      [toolbar] min-content
      [status ] min-content
      [content] minmax(0, auto);
    grid-template-columns: minmax(0, var(--content-max-width));
    gap: .4rem; padding: .5rem;
  "
>...</div>
```

Identity on its own line for a long class list:

```html
<div class="status-bar
           fix r b l  grid items-y-center"></div>
```

Markup with its component CSS, from a Vue single-file component. Only `entry` gets `__layout__`: its columns change at 640px, so their base value sits in the same CSS, where the query can override it.

```vue
<template>
  <nav
    class="account-exit-links  grid content-y-start"
    style="gap: 0; max-width: 44rem; padding-top: 1.25rem"
  >
    <div class="entry  __layout__  grid items-y-center" style="gap: 0.6rem 1.5rem; padding-block: 1rem">
      <div class="description  grid" style="gap: 0.35rem">...</div>
    </div>
  </nav>
</template>

<style>
@layer components {
@scope (.account-exit-links) {
:scope { border-top: 1px solid var(--color-border-soft); }
div.entry + div.entry { border-top: 1px solid var(--color-border-section); }
div.entry.__layout__ { grid-template-columns: minmax(0, 1fr) max-content; }

@media (max-width: 640px) {
  div.entry.__layout__ { grid-template-columns: minmax(0, 1fr); }
}
}
}
</style>
```

## Trigger examples
- "Update this component markup to follow the semantic positioning class pattern."
- "Move this panel's layout into the markup with positioning tokens and inline styles."
