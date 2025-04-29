# Frontend Constants Guidelines

This section provides conventions and examples for defining and using constants in the frontend codebase. Constants improve readability, maintainability, and help eliminate magic numbers or strings scattered throughout the code.

---

## Example: Message Constants and UI Offsets

```ts
import { EasyAIMessage, MessageOriginType, StreamMessageType } from "../types/message";

export const EMPTY_STREAM_AI_MESSAGE: Readonly<EasyAIMessage> = {
  origin: MessageOriginType.Stream,
  message: "",
  type: StreamMessageType.Message,
};

export const ACTIVATOR_POSITION_OFFSET_TOP_PX = 25;
export const ACTIVATOR_POSITION_OFFSET_LEFT_PX = 5;
```

---

## Usage Example

```ts
import { EMPTY_STREAM_AI_MESSAGE } from "@/src/shared/constants/messages";

function createInitialMessage() {
  return { ...EMPTY_STREAM_AI_MESSAGE }; // Safe to copy, can't be mutated
}
```

---

## Best Practices

- Group constants by purpose into logically named files (e.g., `constants/ui.ts`, `constants/messages.ts`).
- If a module has only a few constants (1-4), it is allowed to use an `index.ts` file in the `constants` folder for simplicity.
- Use explicit type annotations for structured or domain-specific constants.
- Write descriptive names that clarify intent and usage.
- Use `export const` to guarantee immutability.
- Use `UPPER_SNAKE_CASE` for all constant names.
- Include unit suffixes in names when applicable (e.g., `_PX`, `_MS`, `_CHARS`) to clarify what the value represents.
- Use `Readonly<Type>` for object constants to prevent accidental mutation.
