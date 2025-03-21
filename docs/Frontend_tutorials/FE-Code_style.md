# Frontend Code Style

The main goal of this document is to highlight the rules and recommendations for creating nice and maintainable JavaScript, TypeScript, and CSS/SCSS code.

---

## Code Style Tools

The frontend code style is defined by the following tools:
- [**ESLint:**](https://eslint.org/) For JavaScript and TypeScript linting.
- [**Prettier:**](https://prettier.io/) For code formatting.
- [**Stylelint:**](https://stylelint.io/) For CSS and SCSS linting.
- [**vue-tsc:**](https://www.npmjs.com/package/vue-tsc) For TypeScript type checking in Vue.js projects.

The configuration files for these tools are located in the main repository.

---

## ESLint Configuration

We use two configuration files for ESLint:

1. **`.eslintrc-default.js`**: This configuration file is used in the pipeline for all code. It contains the base set of rules that ensure code quality and consistency across the project.

2. **`.eslintrc.js`**: This configuration file extends the rules from `.eslintrc-default.js` and includes additional or stricter rules. It is used in the IDE for developing new features, ensuring that the code adheres to higher standards during development.

---

## Prettier Configuration

Prettier is used to format the code consistently. The configuration for Prettier is defined in the main repository.

1. **`.prettierrc.js`**: This configuration file contains the rules for code formatting. It ensures that the code style is uniform across the project.

---

## Stylelint Configuration

Stylelint is used to lint CSS and SCSS files. The configuration for Stylelint is also defined in the main repository.

1. **`.stylelintrc.js`**: This configuration file contains the rules for linting stylesheets. It ensures that the stylesheets follow the project's coding standards.

2. **`stylelint_ignore.txt`**: This file lists the files that are ignored from the pipeline check for Stylelint.

---

## vue-tsc Configuration

vue-tsc is used to check TypeScript types in Vue.js projects. The configuration for vue-tsc is defined in the main repository.

1. **`tsconfig.json`**: This file contains the TypeScript configuration.

2. **`tsc.ignored_files.json`**: This file lists the files that are ignored from the pipeline check process.

---

<!-- theme: danger -->
> **It is forbidden to add new files to the ignore files (stylelint_ignore.txt and tsc.ignored_files.json).** The ignore files are a temporary measure due to the initial implementation of Stylelint and the update of ESLint. Some legacy files are placed here, that are waiting for fix typescript/stylelint errors.

By using these tools and configurations, we ensure that the frontend code remains clean, consistent, and maintainable.
