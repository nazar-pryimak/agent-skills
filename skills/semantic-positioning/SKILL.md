---
name: semantic-positioning
description: Apply the semantic positioning class pattern (ordered class groups, __layout__ hooks, positioning tokens) to component markup. Use when writing or reviewing frontend templates in a project that follows this pattern or asks for it.
license: MIT
---

Follow the semantic positioning pattern to make layout intent readable from the HTML. Use it together with `component-design`, which owns component CSS and naming.

## Class order
Keep class groups in this order and omit any group an element does not need:

1. Identity: the element's class. On a component root, this is the unique scope class, followed by any shared scope class, single-space separated (e.g., `dashboard panel`).
2. Placement: `__layout__`, its bare `--variant` if any, then placement tokens (position, z-index, anchoring, self-alignment).
3. Content: child layout tokens (`grid`, `flex`, and child alignment).
4. Utilities: non-positioning classes (color, text, etc.).

Separate groups with a double space by default. Use a newline instead when it makes a long class list clearer. Tokens within a group are single-space separated.

## `__layout__`
- Add `__layout__` to any element with layout tokens in its class list or layout rules in its component CSS. An inline `style` alone does not need it.
- It has no global CSS. It marks the element's layout and selects its component layout rules (e.g., `div.entry.__layout__ { grid-template-columns: minmax(0, 1fr) max-content; }`).
- Use tokens when one exists. Otherwise use an inline `style`, so the layout stays readable from the HTML.
- Use `__layout__` rules only when an inline `style` can't do the job or would be too long: responsive changes (including the base value they change), states, descendant targeting, values a variant or parent overrides, or a larger block of layout.
- Keep `__layout__` rules for layout only; do not attach visual styling to them.

## Positioning rules
- Prefer `grid` over `flex`. Use `flex` only when it is the right tool.
- Use `one` to stack children in a single grid cell only when you need to. Children opt out with `cell-auto` or `rel-to`.
- Keep markup minimal: add wrappers only for layout or semantics, and prefer `grid`, `one`, and `rel-to` over deep wrappers.
- Inline styles are for layout and one-off spacing only. Visual styling goes in component CSS.

## Tokens
Use the project's positioning utilities, at the path named in its agent instructions (e.g., `AGENTS.md`); otherwise find the stylesheet that defines these tokens. This skill's `assets/positioning.css` defines the set below. If the project has none, propose adding it to the project's `utilities` cascade layer; where the project's copy differs, follow it. Ask before adding or reinterpreting utilities.

| Purpose | Tokens | Notes |
|---|---|---|
| Position | `rel`, `rel-to`, `fix`, `stick` | `rel-to` is `position: absolute`, for an element positioned relative to a `rel` ancestor. |
| Anchors | `t`, `r`, `b`, `l`, `top-left`, `top-right`, `bottom-left`, `bottom-right` | |
| Size and inset | `stretch`, `stretch-x`, `stretch-y`, `full-width`, `full-height`, `fixed-stretch` | With `fix`, `stretch` sets inset; otherwise it sets 100% width and height. |
| Stacking | `z-1`, `z0`, `z1`, `z2`, `z3` | |
| Display | `grid` or `g`, `one`, `flex`, `if` | `flex` and `if` accept `wrap`, `nowrap`; `flex` also accepts `col`, `row`. |
| Centering | `center`, `items-xy-center`, `content-center`, `ch-center` | `center` and `items-xy-center` set `display: grid`; `flex center` stays flex. |
| Grid child alignment | `items-x-*`, `items-y-*`, `content-x-*`, `content-y-*` | |
| Flex child alignment | `items-c-*`, `content-m-*`, `content-c-*` | `m` is the main axis and `c` is the cross axis, so they stay correct in column layouts. Prefer them on new `flex` markup. |
| Self-alignment | `-x-*`, `-y-*`, `-c-*` | Sets the element's own alignment; belongs in the placement group. |

Alignment suffixes are `start`, `center`, and `end`. `content-x`, `content-y`, and `content-m` also accept `--sp-btwn`, `--sp-around`, and `--sp-evenly`.

## Examples
Placement and content groups on the same line:

```html
<div class="dashboard panel  __layout__ --wide  g content-x-center content-y--sp-evenly"></div>
```

Identity on its own line for a long class list:

```html
<div class="status-bar
           __layout__ fix r b l  grid items-y-center"></div>
```

Markup with its component CSS, from a Vue single-file component:

```vue
<template>
  <nav
    class="account-exit-links  __layout__  grid content-y-start"
    style="gap: 0; max-width: 44rem; padding-top: 1.25rem"
  >
    <div class="entry  __layout__  grid items-y-center" style="gap: 0.6rem 1.5rem; padding-block: 1rem">
      <div class="description  __layout__  grid" style="gap: 0.35rem">...</div>
    </div>
  </nav>
</template>

<style>
@layer components {
@scope (.account-exit-links) {
:scope { border-top: 1px solid var(--color-border-soft); }
div.entry.__layout__ { grid-template-columns: minmax(0, 1fr) max-content; }
div.entry + div.entry { border-top: 1px solid var(--color-border-section); }

@media (max-width: 640px) {
  div.entry.__layout__ { grid-template-columns: minmax(0, 1fr); }
}
}
}
</style>
```

## Trigger examples
- "Update this component markup to follow the semantic positioning class pattern."
- "Use __layout__ hooks and placement tokens in the template for this panel."
