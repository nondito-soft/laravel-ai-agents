---
description: "Use when creating or editing Livewire components or their Blade views. Covers component structure, shared data, and Tailwind conventions."
applyTo: "app/Livewire/**, resources/views/livewire/**"
---

# Livewire Frontend

> **Active only when `FRONTEND: livewire` (see AGENTS.md → Stack Configuration).** If the project uses another stack, follow the matching file in this folder instead.

## Components
- Component classes live under `app/Livewire/`; their views under `resources/views/livewire/`
- A full-page component is reached via a route → `Route::get('/users', Index::class)`
- Expose `pageTitle` and `breadcrumbs` to the view (public properties or `render()` data)

```php
class Index extends Component
{
    public string $pageTitle;
    public array $breadcrumbs;

    public function render()
    {
        return view('livewire.users.index', [
            'users' => $this->userService->getUsers(),
        ])->layout('layouts.app');
    }
}
```

## Shared Data
**Shared globally via `View::share()` / a view composer (e.g. in a service provider); your project may differ.**

Available in every Blade/Livewire view:
- `auth()->user()` — authenticated user
- `session('success')` / `session('error')` — flash messages

## Navigation & Forms
- Use `wire:navigate` on links for SPA-style navigation — avoid full page reloads for internal routes
- Bind inputs with `wire:model`; submit with `wire:submit` and validate via `$this->validate()`

```blade
<form wire:submit="store">
    <input type="text" wire:model="name">
    @error('name') <span>{{ $message }}</span> @enderror
</form>
```

## Translation
- Use `__()` / `@lang()` for user-facing strings
- Keys follow: `message.custom.resource.action.outcome`
