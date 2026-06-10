---
description: "Use when creating or editing React components, Inertia pages, or frontend assets. Covers Inertia patterns, shared data, and Tailwind conventions."
applyTo: "resources/js/**"
---

# React / Inertia Frontend

> **Active only when `FRONTEND: react-inertia` (see AGENTS.md → Stack Configuration).** If the project uses another stack, follow the matching file in this folder instead.

## Inertia Pages
- Pages live under `resources/js/Pages/` — directory mirrors route structure
- Use `.jsx` (or `.tsx`) files; default-export the page component
- Always receive `pageTitle` and `breadcrumbs` as props from the controller

```jsx
export default function Index({ pageTitle, breadcrumbs, users }) {
    return (/* ... */);
}
```

## Shared Data (from HandleInertiaRequests)
**This project shares the following via HandleInertiaRequests; your project may differ.**

Available globally via `usePage().props`:
- `auth.user` — authenticated user
- `flash.success` — success message
- `flash.error` — error message

```jsx
import { usePage } from '@inertiajs/react';
const { auth, flash } = usePage().props;
```

## Inertia Navigation
- Use the `<Link>` component for navigation — never plain `<a>` tags for internal routes
- Use `useForm()` for form submissions with validation error handling

```jsx
import { useForm } from '@inertiajs/react';
const { data, setData, post } = useForm({ name: '', email: '' });
post(route('users.store'));
```

## Translation
- Use your i18n helper (e.g. `t()` from `laravel-react-i18n` or an equivalent) for user-facing strings
- Keys follow: `message.custom.resource.action.outcome`
