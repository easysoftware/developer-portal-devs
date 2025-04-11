# Vue.js Composables Guidelines

This document provides key practices for creating and using Vue composables in our application. Focus on application-specific logic, consistency, and maintainability.

---

## Implementation Guidelines

- **Encapsulation:**  
  Keep business logic self-contained. Only expose reactive state and functions that components really need.

- **Lean Design:**  
  Do not include extraneous details; return only the minimal set of reactive properties and methods.

- **When to Use:**  
  Use composables if your functionality depends on reactive data or computed values. They seamlessly integrate with Vue's reactivity system, unlike plain utility functions.

- **Single Responsibility:**  
  Each composable should encapsulate one specific aspect of our application logic.

---

## Example: useCounter

The following example manages incrementation of a counter value.

```ts
import { ref } from "vue";

export const useCounter = (defaultValue: number = 0) => {
  const count = ref<number>(defaultValue);

  function increment(): void {
    count.value++;
  }

  return { count, increment };
}
```

### Usage Example

Import and use the composable within a component to increment count.

```vue
<template>
  <div>
    <p>Current Count: {{ count }}</p>
    <button @click="increment">Increment</button>
  </div>
</template>

<script setup lang="ts">
  import { useCounter } from "@/src/shared/composables/useCounter";

  const { count, increment } = useCounter(1);
</script>
```
