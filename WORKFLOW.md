# WORKFLOW.md: Vague vs. Spec-Driven AI Development Experiment

## Overview
This experiment compares two approaches to building a core feature (a User Profile & Settings Form with validation in Next.js/Tailwind):
* **Branch 1 (`feat/settings-vague`):** Built using a single-line, lazy prompt in an isolated session.
* **Branch 2 (`feat/settings-spec`):** Built using a detailed spec prompt with file references, constraints, validation rules, and an explicit test/verification loop.

---

## 1. Correctness & Architectural Differences

* **State & Architecture:**
  * `feat/settings-vague` created an uncontrolled native `<form>` using basic `useState` handlers mixed directly inside `app/settings/page.tsx`. It lacked schema validation, leading to invalid state submissions.
  * `feat/settings-spec` adhered to the project's MVVM structure. It defined a Zod validation schema (`lib/schemas/settings.ts`), used `react-hook-form` via `@hookform/resolvers/zod`, and isolated state management into a custom hook (`hooks/useSettingsViewModel.ts`).

* **AI Mistake Caught:**
  In `feat/settings-vague`, the AI generated an uncontrolled file input for user avatar uploads and attempted to read `e.target.files[0].path` directly on the browser client, which threw a runtime `TypeError` on submit. In Round Two (`feat/settings-spec`), the spec explicitly required a client-safe base64/URL object reader, which the model implemented cleanly without DOM crashes.

---

## 2. Accessibility & Edge Cases

* **Accessibility (a11y):**
  * **Vague:** Form inputs lacked explicit `<label>` bindings (`htmlFor`), input error messages were displayed purely through color (`text-red-500`) without `aria-describedby` or `aria-invalid` attributes, failing screen reader checks.
  * **Spec:** Inputs feature explicit label IDs, proper `aria-invalid` attributes when validation fails, and focus styles matching design tokens.

* **Edge Cases:**
  * **Vague:** Allowed empty spaces for required fields, lacked email regex verification, and failed to disable the submit button during asynchronous save states (allowing duplicate submissions).
  * **Spec:** Enforced minimum character lengths, validated email formats, handled network timeout fallbacks, and correctly set `isSubmitting` disabled states on the save button.

---

## 3. Review Effort & Time to Complete

| Metric | Round 1 (`feat/settings-vague`) | Round 2 (`feat/settings-spec`) |
| :--- | :--- | :--- |
| **Prompting Time** | ~10 seconds | ~4 minutes |
| **Generation Time** | ~30 seconds | ~1.5 minutes |
| **Manual Bug Fixing / Review** | ~25 minutes (adding validation, fixing runtime errors) | ~2 minutes (verifying test output) |
| **Total End-to-End Time** | **~25.5 minutes** | **~7.5 minutes** |

While Round Two required more up-front drafting, it eliminated 90% of the downstream review and debugging overhead, resulting in an overall 3x faster delivery.
