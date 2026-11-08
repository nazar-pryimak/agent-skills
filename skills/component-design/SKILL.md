---
name: component-design
description: Apply component structure, class naming, and selector conventions built on @layer components and @scope. Use when designing or refactoring component markup and styles in a project that follows these conventions or asks for them.
license: MIT
---

Follow these conventions for component naming and CSS. Use them together with `semantic-positioning`, which owns class order, markup layout, and positioning tokens.

The project's agent instructions (e.g., `AGENTS.md`) normally name its theme token file, theme rules, and CSS entry point. Otherwise locate them, and ask if they are unclear or missing.

## Scoping and naming
- Use a unique root class per component and anchor `@scope` to it.
- Reusing child class names across components is acceptable because `@scope` keeps a component's styles from reaching the rest of the page.
- Styles intentionally reach nested child components, which is why `<style>` is not `scoped`. A child overrides a parent rule by declaring the same property with the same selector shape, because the closer scope wins a specificity tie.
- Prefer plain, descriptive class names for internal elements (e.g., `inputs`, `input`, `close`).
- Do not name classes after global utilities or positioning tokens (e.g., `empty`, `mono`, `center`). The `utilities` layer overrides `components` regardless of specificity, so component CSS cannot override what the utility sets.
- BEM is optional; use it only when it improves clarity for a complex component.
- Use `name--variant` modifiers for state or variants (e.g., `dirty-dot--interactive`).
- Use a bare `--variant` only for a shared variant that a parent or shared rule reads (e.g., `__layout__ --wide` on panels).
- Avoid project-wide prefixes for component classes; rely on the root scope instead.

## Component CSS
- Use non-scoped `<style>` blocks with `@layer components` and `@scope`.
- Style the component root with `:scope`.
- Prefer tag + class selectors to make targets obvious (e.g., `div.input`, `button.close`).
- Keep selectors shallow so a child can still override them with the same selector shape.
- Keep visual styling in component CSS. Positioning belongs in markup or `__layout__` rules, as defined in `semantic-positioning`.

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

## Trigger examples
- "Refactor this component to use plain class names and scoped selectors."
- "Adjust the component styles to follow the component-design conventions."
