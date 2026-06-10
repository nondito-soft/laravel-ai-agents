---
description: "Use when creating or editing Vue components, Inertia pages, or frontend assets. Covers Inertia patterns, shared data, and Tailwind conventions."
paths: ["resources/js/**"]
---

# Vue / Inertia Frontend

> **Active only when `FRONTEND: vue-inertia` (see AGENTS.md → Stack Configuration).** If the project uses another stack, follow the matching file in this folder instead.

## Inertia Pages
- Pages live under `resources/js/Pages/` — directory mirrors route structure
- Always receive `pageTitle` and `breadcrumbs` as props from the controller

```vue
<script setup>
const props = defineProps({
    pageTitle: String,
    breadcrumbs: Array,
    users: Array,
});
</script>
```

## Shared Data (from HandleInertiaRequests)
**This project shares the following via HandleInertiaRequests; your project may differ.**

Available globally via `usePage().props`:
- `auth.user` — authenticated user
- `flash.success` — success message
- `flash.error` — error message

```js
import { usePage } from '@inertiajs/vue3';
const { auth, flash } = usePage().props;
```

## Inertia Navigation
- Use `<Link>` component for navigation — never plain `<a>` tags for internal routes
- Use `useForm()` for form submissions with validation error handling

```js
import { useForm } from '@inertiajs/vue3';
const form = useForm({ name: '', email: '' });
form.post(route('users.store'));
```

## Translation
- Use the `$t()` helper or `trans()` for user-facing strings
- Keys follow: `message.custom.resource.action.outcome`
