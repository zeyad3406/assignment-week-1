# Project Rules & Guidelines

## Architecture & State
- All forms MUST use `react-hook-form` paired with `zod` schema validation. Uncontrolled inputs or raw `useState` form bundles are prohibited.
- Maintain MVVM boundary: Page routes (`app/**/page.tsx`) remain pure presentation views. All business logic, fetch side-effects, and state handlers must live inside custom hooks (`hooks/use*ViewModel.ts`).

## Security & Environment
- Never hardcode API keys or credentials. All external endpoints and keys must reference `process.env.NEXT_PUBLIC_*` or server-side env variables.

## Accessibility (a11y) & Testing
- Every form input must include an explicit `<label htmlFor="...">` and `aria-invalid={!!errors.field}` attributes.
- Any pull request modifying business or component logic must include corresponding Jest/React Testing Library specs (`*.test.tsx`) covering success, failure, and loading states before merging.
