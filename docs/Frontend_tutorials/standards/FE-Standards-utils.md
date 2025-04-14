# Utils Guidelines

This section outlines best practices for creating and maintaining helper functions within the `utils/` folder. These functions provide reusable logic that is not specific to Vue or reactive state—typically dealing with DOM interactions, string/array manipulations, layout adjustments, or other side-effect–free helpers.

---

## Example: Layout Scrollbar Visibility

```ts
import { NO_SCROLLBARS_CLASS } from "@/src/shared/constants/layout";

export const setLayoutScrollbarsVisibility = (scrollbarsVisible: boolean): void => {
  document.body.classList.toggle(NO_SCROLLBARS_CLASS, !scrollbarsVisible);
};
```

---

## Usage Example

```ts
import { setLayoutScrollbarsVisibility } from "@/src/shared/utils/layout";

setLayoutScrollbarsVisibility(false);

setLayoutScrollbarsVisibility(true);
```

---


## Best Practices

- Place generic, reusable, non-Vue logic in the `utils/` folder. (Vue-specific logic should be in `composables/`.)
- Organize files by purpose (e.g., `utils/layout.ts`, `utils/string.ts`, `utils/dom.ts`).
- Prefer named exports for each utility function.
- Keep functions small, focused, and easy to test.
- Use descriptive names that reflect the function’s purpose (e.g., `setLayoutScrollbarsVisibility` instead of `toggleScrollbars`).
- Fully type all function parameters and return values.
