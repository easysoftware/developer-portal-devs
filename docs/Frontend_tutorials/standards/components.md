# Vue.js Components

This section outlines the standards and best practices for developing Vue.js components within our projects.

## Using Script Setup with TypeScript

All Vue components should use the Composition API syntax along with TypeScript `<script setup lang="ts">`. This approach provides a concise, readable, and efficient way to define components, ensuring full type safety and a better developer experience.

## Single Responsibility Principle (SRP)

Components should adhere to the Single Responsibility Principle. Each component should have one clear purpose or responsibility. Avoid creating overly large components that handle multiple concerns. Instead, break them down into smaller, more focused, reusable components.

## State Management

When sharing data between components, prefer utilizing a centralized store (refer to the state management section) instead of passing numerous props. This helps maintain clean, readable, and easily maintainable components by centralizing the state logic.
Except for universal components that are used in multiple use cases, then make sense use props and emits. 

## Descriptive Component Naming

Always use descriptive component names that clearly indicate their functionality. Descriptive naming significantly improves code readability and simplifies navigation within the codebase.
Following these guidelines will ensure your Vue.js components are clear, maintainable, and consistent with the project's overall standards.

## Test attributes

Element that have some functional meaning (elements with that can user interact or render some specific content) should have data-cy attribute that is used for testing.

## Example Component

```vue
<template>
  <EasySlider
    v-model="depthValue"
    :max="3"
    :min="1"
    :step="1"
    data-cy="slider__depth--data-source-url"
    name="depth"
  >
    <template #label>
      {{ t("easy_ai.data_source.label_depth") }}
      <EasyIcon
        v-easy-tooltip="t('easy_ai.data_source.info_depth')"
        icon="info"
        class="ml-ms-2"
      />
    </template>
  </EasySlider>
</template>

<script setup lang="ts">
import EasySlider from "@/src/easy-ui/components/EasySlider.vue";
import EasyIcon from "@/src/easy-icons/EasyIcon.vue";
import { computed } from "vue";
import { useDataSourceWebStore } from "@/src/easy-ai/dataSourceWeb/shared/store/dataSourceWebStore";

const t = window.EasyLocale.getLocale;
const dataSourceWebStore = useDataSourceWebStore();

const depthValue = computed({
  get: () => dataSourceWebStore.depth,
  set: (value: number) => dataSourceWebStore.setDepth(value),
});
</script>
```

### Explanation

1. **Script Setup with TypeScript:**
   The example uses the concise `<script setup lang="ts">` syntax, leveraging TypeScript for better readability, robustness, and maintainability.

2. **Single Responsibility Principle (SRP):**
   The component clearly focuses on a single responsibility: managing the depth value with a slider. This simplicity ensures clarity and ease of maintenance.

3. **Data Sharing:**
   The component relies on a centralized store (`useDataSourceWebStore`), which promotes clean and maintainable state management.

4. **Descriptive Component Naming:**
   The use of descriptive names (`EasySlider`, `EasyIcon`) enhances readability and simplifies code navigation.

5. **Focused, Small Components:**
   This example clearly illustrates the guideline of creating small, focused components to enhance maintainability and ease of testing.

