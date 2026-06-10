---
description: "Use when creating or editing Blade views, layouts, or frontend assets. Covers view structure, shared data, and Tailwind conventions."
applyTo: "resources/views/**"
---

# Blade Frontend

> **Active only when `FRONTEND: blade` (see AGENTS.md → Stack Configuration).** If the project uses another stack, follow the matching file in this folder instead.

## Views
- Views live under `resources/views/` — directory mirrors route structure (`resources/views/users/index.blade.php`)
- Controllers return `view('users.index', $responseData)`
- Always pass `pageTitle` and `breadcrumbs` into the view data

```php
return view('users.index', [
    'pageTitle'   => __('page.users.index.title'),
    'breadcrumbs' => breadcrumbs()->generate('users.index'),
    'users'       => $this->userService->getUsers(),
]);
```

## Shared Data
**Shared globally via `View::share()` / a view composer (e.g. in a service provider); your project may differ.**

Available in every view:
- `auth()->user()` — authenticated user
- `session('success')` / `session('error')` — flash messages

## Layouts & Navigation
- Extend a shared layout (`@extends('layouts.app')`) or use components (`<x-app-layout>`)
- Use named routes in links: `<a href="{{ route('users.index') }}">` — never hardcoded URLs

## Forms
- Include `@csrf` in every form; use `@method('PUT')` / `@method('DELETE')` for non-POST verbs
- Display validation errors with `@error('field')`

```blade
<form method="POST" action="{{ route('users.store') }}">
    @csrf
    <input type="text" name="name" value="{{ old('name') }}">
    @error('name') <span>{{ $message }}</span> @enderror
</form>
```

## Translation
- Use `__()` / `@lang()` for user-facing strings
- Keys follow: `message.custom.resource.action.outcome`
