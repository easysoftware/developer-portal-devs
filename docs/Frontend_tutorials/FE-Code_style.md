# Frontend Code Style

This document outlines rules and recommendations for creating clear, maintainable, and consistent JavaScript, TypeScript, and CSS/SCSS code.

---

## Code Style Tools

Frontend code style is defined by using these tools:

- [**ESLint:**](https://eslint.org/) JavaScript and TypeScript linting.
- [**Prettier:**](https://prettier.io/) Code formatting.
- [**Stylelint:**](https://stylelint.io/) CSS and SCSS linting.
- [**vue-tsc:**](https://www.npmjs.com/package/vue-tsc) TypeScript type checking in Vue.js projects.

---

## ESLint Configuration

- **`.eslintrc-default.js`**:  
   Used by the CI/CD pipeline. It contains essential rules ensuring code quality and consistency throughout the project.

- **`.eslintrc.js`**:  
   Extends `.eslintrc-default.js` with additional or stricter rules. This file is intended for IDE use during feature development to enforce higher coding standards.

---

## Prettier Configuration

- **`.prettierrc.js`**:  
  Specifies formatting rules, ensuring uniform code style across the entire project.

---

## Stylelint Configuration

- **`.stylelintrc.js`**:  
  Defines linting rules for stylesheets to uphold the project's coding standards.

- **`stylelint_ignore.txt`**:  
  Lists files temporarily excluded from Stylelint checks during pipeline execution.

---

## vue-tsc Configuration

- **`tsconfig.json`**:  
  Contains global TypeScript settings.

- **`tsc.ignored_files.json`**:  
  Lists files temporarily excluded from TypeScript checks in the pipeline.

---

<!-- theme: danger -->
> **Do not add new entries to the ignore files (`stylelint_ignore.txt` and `tsc.ignored_files.json`).**  
> These files are ignored temporarily due to the initial introduction of Stylelint and ESLint updates. They include legacy files pending fixes for TypeScript or Stylelint errors.
