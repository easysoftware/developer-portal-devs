# Frontend Project Structure & Architecture

## 1. Overview

This document defines the frontend project structure, directory organization, naming conventions, and best practices to ensure maintainability, scalability, and consistency across the codebase.

## 2. General Principles

- **Modularization**: Organize code into feature-based modules rather than generic categories. In module we can import from shared folder, no from another module (except easy_ui).
- **Separation of Concerns**: Keep UI components, API interactions, state management, and utilities separate.
- **Naming Consistency**: Follow a strict and predictable naming convention for files and folders.
- **Scalability**: Design the structure to accommodate future growth and easy feature addition.
- **Single Responsibility Principle**: Each file should have a clearly defined purpose.

## 3. Structure

### 3.1 Base Structure

```
app/frontend/
├── entrypoints/              # Contains entry points files
│   ├── <module_name>.ts      # Entry point for a specific module
│   ├── application.js        # Global entrypoint aceessible in whole application
├── src/                      # Contains all frontend modules
│   ├── <module_name>/        # Each module contain subset of module structure or submodules with shared folder
│   │   ├── <submodule_name>/ # Nested submodules (if needed) with same structure as module
│   │   ├── shared/           # Shared between submodules, contain subset of module structure
|   |   ├── stylesheets/      # Global stylesheets, contain structure using atomic design, more in detail in styling guidelines
│   ├── easy_ui/              # Global shared UI components, from this module can be imported in any other module
│   ├── shared/               # Global shared, contain subset of module structure
│   ├── tests/                # Unit and integration frontend tests, contains tests structure, more in frontend testing guidelines
```

<!-- theme: danger -->
> **Note**: Module can contain submodules and shared folder or subset of module structure. Not submodules and subset of module structure at the same time. If we need to share somethig between submodules we can use shared folder.

### 3.2 Module Structure

```
<module_name>/
├── api/            # API services
├── components/     # Vue components
├── composables/    # Reusable feature-specific logic (Vue composables)
├── constants/      # Module-related constants
├── directives/     # Module-specific directives
├── graphql/        # GraphQL definitions
│   ├── fragments/  # GraphQL fragments definitions
│   ├── mutations/  # GraphQL mutations definitions
│   ├── queries/    # GraphQL queries definitions
├── store/          # Module-specific state management stores
├── types/          # TypeScript interfaces & types & enums
├── utils/          # Utility functions
```

## 4. Naming Conventions

### 4.3 File Naming

- Use **camelCase** for *.ts and *.js filenames.
- Use **PascalCase** for Vue component filenames.
- API services: Filename ends with `Api` suffix like `easyAIFeedbackApi.ts`
- Composables: Filename starts with `use` prefix like `useScrollToBottomMessages.ts`

### 4.2 Directory Naming

- Use **snake_case** for folder names (`easy_scrum_boards`)
- Use **module-based or shared folders** under `src/`.
- Use **shared/** inside modules when common components/utils exist across submodules.

### 4.3 Code Naming

- **API services:** Class name ends with `Api` suffix like `class EasyAIFeedbackApi ...`
- **Composables:** Function name starts with `use` prefix like `const useScrollToBottomMessages = ...`
- **Directives:** Directive name starts with `v` prefix like `const vTrapTab = ...`
- **Graphql fragments:** Definitions name ends with `Fragment` suffix like `const easyAIFeedbackFragment = gql(...`
- **Graphql mutations:** Definitions name ends with `Mutation` suffix like `const easyAIFeedbackMutation = gql(...`
- **Graphql queries:** Definitions name ends with `Query` suffix like `const easyAIFeedbackQuery = gql(...`
- **Store:** Store name has prefix `use` and suffix `Store` like `const useEasyAIFeedbackStore = ...`
- **Typescript enums:** Enum has singular name like `enum EasyAIFeedbackStatus { ... }`
