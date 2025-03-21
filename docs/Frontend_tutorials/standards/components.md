# Vue.js components

In this section, we will cover the standards and best practices for developing Vue.js components in our projects.

## Script Setup with TypeScript

We use the `<script setup lang="ts">` syntax with TypeScript for all our Vue components. This approach provides a more concise and efficient way to write components, leveraging the full power of TypeScript for type safety and better developer experience.

## Single Responsibility Principle (SRP)

Each component should adhere to the Single Responsibility Principle (SRP). This means that a component should have one clear purpose or responsibility. Avoid creating large components that handle multiple concerns. Instead, break them down into smaller, more focused components.

## Data Sharing

When sharing data between components, prefer using a store (more in state management section) rather than passing a large number of props. This approach helps to keep components clean and maintainable, and it centralizes the state management.

## Descriptive Component Names

Components should have descriptive names that clearly indicate their purpose. This makes the codebase more readable and easier to navigate.

By following these guidelines, you will ensure that your Vue.js components are clean, maintainable, and consistent with our project's standards.

## Example

Here is an example of a Vue component written in the `<script setup lang="ts">` syntax with TypeScript. This example demonstrates how to create a reusable and maintainable component following the guidelines mentioned above.


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

<script lang="ts" setup>
import EasySlider from "../../../../easy-ui/components/EasySlider.vue";
import { computed } from "vue";
import { useDataSourceWebStore } from "@/src/easy-ai/dataSourceWeb/shared/store/dataSourceWebStore";
import EasyIcon from "@/src/easy-icons/EasyIcon.vue";

const t = window.EasyLocale.getLocale;

const dataSourceWebStore = useDataSourceWebStore();

const depthValue = computed({
  get: () => dataSourceWebStore.depth,
  set: (value: number) => {
    dataSourceWebStore.setDepth(value);
  },
});
</script>
```

### Explanation

1. **Script Setup with TypeScript**: The component uses the `<script setup lang="ts">` syntax, which is a concise and efficient way to write Vue components. TypeScript provides type safety, making the code more robust and easier to maintain.

2. **Single Responsibility Principle (SRP)**: The component adheres to SRP by focusing on a single responsibility - managing the depth value using a slider. This makes the component easier to understand, test, and maintain.

3. **Data Sharing**: Instead of passing multiple props, the component uses a store (`useDataSourceWebStore`) to manage the state. This centralizes state management and keeps the component clean.

4. **Descriptive Component Names**: The components used here have descriptive names (`EasySlider`, `EasyIcon`), making the code more readable and easier to navigate.

5. **Smaller Components**: The example demonstrates a small, focused component that handles a specific functionality. This approach aligns with the guideline of creating smaller components with clear functionality.
