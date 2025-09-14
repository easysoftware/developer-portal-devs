# Design System Validation

This document outlines how to validate a design system using an automated workflow powered by **n8n**, with **Figma** as the single source of truth and **Storybook** as the only documentation environment. It is tailored for projects using **Vue** on the frontend, **Ruby** on the backend, and **Tailwind CSS** for layout.

---

## 🔍 Purpose

- Ensure full alignment between Figma, Storybook and code implementation.
- Catch missing, outdated, or inconsistent components, tokens, and layout structures.
- Automate validation through n8n for scalability and repeatability.
- Guarantee proper documentation coverage in Storybook.

---

## 🧱 What We Validate

| Area                 | What We Check                                                           |
|----------------------|-------------------------------------------------------------------------|
| **Tokens**           | Colors, typography, spacing, elevation, breakpoints, corner radius     |
| **Components**       | Naming, variants, structure, props, implementation presence             |
| **Documentation**    | Storybook stories exist and are complete                                |
| **Tailwind Layout**  | Correct usage of utility classes, responsive breakpoints, grid structure|
| **Conventions**      | Naming (e.g. `DSButton`), folder structure, reuse consistency            |

---

## 🔁 Validation Process (via n8n)

The validation workflow is automated using **n8n** and follows these steps:

```plaintext
Start
→ Fetch design data from Figma (tokens, components, layout)
→ Fetch implementation data from GitLab and Storybook
→ Compare each category (tokens, components, layout, docs)
→ Generate validation report
```

---

## 🎨 Figma as Source of Truth

- Tokens (colors, spacing, typography, breakpoints, border radius) exported via Figma API or plugin
- Components and their variants defined with standard naming and structure
- All layout and grid principles defined in design files

---

## 📚 Storybook as Documentation

- Every coded component must have a Storybook entry
- Stories must demonstrate states (default, hover, disabled, etc.)
- Stories must reflect the component’s Figma structure and naming

---

## 🧪 Token Validation

- Tokens from Figma exported as JSON
- Match token name, value, and type (e.g. `primaryColor`, `lineHeightBase`, `smBreakpoint`, `radiusSm`)
- Tokens must be present and correctly used in codebase (SCSS, Tailwind config, utility classes)
- Validate:
  - **Colors** (`#HEX`, RGBA, etc.)
  - **Typography** (font, size, weight, line height)
  - **Spacing** (padding, margin)
  - **Elevation** (shadows)
  - **Breakpoints** (`sm`, `md`, `lg`, etc.)
  - **Corner Radius** (`rounded-sm`, `rounded-lg`, etc.)

---

## 🧩 Component Validation

### Vue (Frontend)
- Component names start with `DS` prefix (e.g. `DSModal.vue`)
- Props, emits, slots match the Figma structure
- All components have `*.stories.js/ts` in Storybook
- Tailwind classes reflect grid, spacing, breakpoints, and border radius

### Ruby (Backend)
- Views (`.erb` or `.haml`) use the correct component includes
- Use helpers or partials for reused design elements
- Tailwind utilities consistent with Figma-defined layout and tokens

---

## 🧱 Tailwind Layout Validation

- Responsive classes (`sm:`, `md:`, `lg:`) reflect design breakpoints
- Grid and flex utilities used correctly (`grid-cols-12`, `gap-4`, etc.)
- Consistent use of spacing (`p-4`, `mb-8`) and corner radius (`rounded-md`) per design tokens

---

## 📩 Reporting & Notification

The n8n workflow posts validation results directly as a comment in the relevant merge request on GitLab. This ensures clear visibility for reviewers and contributors without relying on external channels.

---

## 🛠 Tools & Integrations

| Tool         | Purpose                                       |
|--------------|-----------------------------------------------|
| **Figma API**| Source of design data (tokens, components)    |
| **n8n**      | Automate the validation pipeline              |
| **GitLab**   | Source repo and CI/CD pipeline integration    |
| **Storybook**| Documentation and reference implementation    |
| **Tailwind** | Utility-first layout and responsive design    |

---

## 📆 Validation Cadence

| Task                        | Frequency              |
|-----------------------------|------------------------|
| Automated n8n validation    | On every merge request |
| Manual spot-check (QA)      | Before releases        |
| Full audit                  | Per sprint             |
