---
description: "Use when creating or editing Laravel controllers, middleware, or FormRequests. Covers HasMiddleware, route model binding, frontend responses, and permission authorization."
applyTo: "app/Http/**/*.php"
---

# Laravel Controllers & FormRequests

## Controllers
- Implement `HasMiddleware` and define `public static function middleware(): array`
- Use `new Middleware('permission:can-*', only: [...])` — lowercase `new middleware(...)` is also valid (PHP class names are case-insensitive)
- Always use route model binding — never `User::find($id)` inside a controller
- Call `getUserDetails($model)` or equivalent before passing to service (eager-loads relationships)
- Inject service via constructor; never instantiate with `new`
- This pattern applies equally to all resources (users, roles, permissions, departments, etc.)

```php
public function store(UserCreateRequest $request): RedirectResponse
{
    $result = $this->userService->createUser($request->validated());
    return $result
        ? Redirect::route('users.index')->with('success', __('message.custom.user.store.success'))
        : Redirect::back()->with('error', __('message.custom.user.store.error'));
}
```

- Render via the **active frontend rule** (`FRONTEND` config in AGENTS.md → `instructions/frontend/{stack}.instructions.md`). The response **must always include `breadcrumbs` and `pageTitle`**, whatever the stack. The data array is identical across stacks — only the render call differs:

```php
// vue-inertia / react-inertia:
return Inertia::render('Users/Index', $responseData);

// blade:
return view('users.index', $responseData);

// livewire: route to the full-page component, exposing the same data

// $responseData is always:
$responseData = [
    'pageTitle'   => __('page.users.index.title'),
    'breadcrumbs' => breadcrumbs()->generate('users.index'),
    'users'       => $this->userService->getUsers(),
];
```


## JSON / API Responses

API controllers (`app/Http/Controllers/API/**`) follow the same "build then return once" rule as Inertia renders. Assemble a `$responseData` array — and a `$httpStatus` variable when the status differs per branch — then end the action with a single `return response()->json($responseData, $httpStatus);`. Do **not** inline arrays into `response()->json([...])`, and do **not** scatter multiple early `return response()->json(...)` statements; branch by assigning `$responseData`/`$httpStatus`, then return once.

- Use the envelope shape `['success' => bool, 'message' => __('message.api.*'), 'data' => [...]]` — `success` boolean, a translated `message`, and a `data` payload (`[]` when there is none).
- All user-facing strings go through `__('message.api.*')` keys; never hardcode response text.
- Use `Illuminate\Http\Response` status constants (`Response::HTTP_OK`, `Response::HTTP_UNAUTHORIZED`, …), not raw integers.

Resource read — assemble `$responseData`, then return once:

```php
public function index(): JsonResponse
{
    $responseData = [
        'success' => true,
        'message' => __('message.api.user.index'),
        'data'    => $this->userService->getUsers(),
    ];

    return response()->json($responseData, Response::HTTP_OK);
}
```

When the status code differs per branch, assign both `$responseData` and `$httpStatus` in each branch, then return once at the end:

```php
public function store(UserCreateRequest $request): JsonResponse
{
    $user = $this->userService->createUser($request->validated());

    if ($user) {
        $responseData = [
            'success' => true,
            'message' => __('message.api.user.store.success'),
            'data'    => $user,
        ];
        $httpStatus = Response::HTTP_CREATED;
    } else {
        $responseData = [
            'success' => false,
            'message' => __('message.api.user.store.error'),
            'data'    => [],
        ];
        $httpStatus = Response::HTTP_UNPROCESSABLE_ENTITY;
    }

    return response()->json($responseData, $httpStatus);
}
```

## FormRequests
- `authorize()` always returns `true` — authorization is handled at the controller layer via `HasMiddleware` + `permission:can-*` middleware.

```php
public function authorize(): bool
{
    return true;
}
```

- For unique-except-current rules, use `$this->route('user')` to get the bound model:

```php
'email' => ['required', 'email', Rule::unique('users')->ignore($this->route('user'))],
```

## Permission Naming
`can-{view|create|edit|delete}-{resource}` — e.g. `can-create-user`, `can-edit-role`
