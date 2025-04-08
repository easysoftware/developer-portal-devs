# Frontend State Management Guidelines

This section outlines standards and best practices for managing application state in frontend projects. The goal is to ensure clean, predictable, and maintainable state flows.

---

## Local vs Global State

- **Local State:**
  - Defined and managed inside a Vue component using `ref`, `reactive`, or the Composition API.
  - Not accessible outside the component.
  - Examples: form inputs, toggle states, dialog visibility within a single component.

- **Global State:**
  - Centralized state defined in a store (e.g., Pinia or singleton object).
  - Shared across multiple components or modules.
  - Ensures a single source of truth for application-wide data.
  - Examples: user session, theme, menu state, global dialogs.

---

## Global State Patterns (Stores)

We use two main approaches for global state:

### 1. Pinia Store (Vue App Scoped)

Use when state is shared between components in a single Vue application.

#### Example: `easyAIMenu.ts`
```ts
import { defineStore } from "pinia";
import { ref, readonly } from "vue";
import { MenuType } from "../types/menu";

export const useMenuStore = defineStore("easyAIMenu", () => {
  const menuOpened = ref(false);
  const menuType = ref<MenuType | null>(null);

  const setMenuOpened = (value: boolean) => {
    menuOpened.value = value;
  };

  const setMenuType = (type: MenuType) => {
    menuType.value = type;
  };

  return {
    menuOpened: readonly(menuOpened),
    menuType: readonly(menuType),
    setMenuOpened,
    setMenuType,
  };
});
```

#### Usage
```ts
import { useMenuStore } from "@/src/store/menu";

const menuStore = useMenuStore();
menuStore.setMenuOpened(true);
```

✅ Ideal for UI state, shared data, and coordination within one Vue app.

---

### 2. Singleton Store (Framework-Agnostic)

Use when state must be accessible across microfrontends or from vanilla JS.

#### Example: `easyDialogStore.ts`

```ts
class EasyDialogStore {
  private static instance: EasyDialogStore = new EasyDialogStore();
  private dialogStates: Record<string, { isOpened: boolean; hideBodyScrollbar: boolean }> = {};

  private constructor() {}

  static getInstance(): EasyDialogStore {
    return EasyDialogStore.instance;
  }

  public registerNewDialog({ isOpened, hideBodyScrollbar = true }: { isOpened: boolean; hideBodyScrollbar?: boolean }): string {
    const dialogId = this.generateNewDialogId();
    this.dialogStates[dialogId] = { isOpened, hideBodyScrollbar };
    this.processHideBodyScrollbar(dialogId);
    return dialogId;
  }

  public updateDialogOpened(dialogId: string, opened: boolean): void {
    if (!this.dialogStates[dialogId]) throw new Error(`Dialog with ID ${dialogId} not found`);
    this.dialogStates[dialogId].isOpened = opened;
    this.processHideBodyScrollbar(dialogId);
  }
}

export default EasyDialogStore.getInstance();
```

#### Usage

```ts
import easyDialogStore from "@/src/shared/store/easyDialogStore";

const dialogId = easyDialogStore.registerNewDialog({ isOpened: true });
easyDialogStore.updateDialogOpened(dialogId, false);
```

✅ Ideal for shared concerns (e.g. dialogs, overlays) across apps or non-Vue contexts.

---

## Best Practices

- **Use Pinia** for Vue component state sharing.
- **Use Singleton Stores** for cross-app/shared logic.
- **Avoid Prop/Emit Drilling** by using stores for shared state.
- **Encapsulate Updates** via methods or actions.
- **Use `readonly()`** in stores to expose immutable state and prevent accidental mutation outside the store.
- **Keep Stores Modular** – one concern per store.
- **Don't use storeToRefs** prefer work with store through named constant `const menuStore = useMenuStore();`
