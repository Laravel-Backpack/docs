# Language Switcher <span class="badge badge-pill badge-success">FREE</span>

--

<a name="about"></a>
## About

Backpack Language Switcher adds a simple language dropdown to your admin UI.
It works with Backpack for Laravel and lets your users switch the app locale from the admin panel using a middleware + Blade include.

With Language Switcher, you can:
- show a language selector in your admin topbar;
- include flags and an optional label;
- use the switcher inside or outside Backpack by applying middleware to the desired routes;
- customize the route behavior through the published config.

![Language Switcher preview](https://github.com/Laravel-Backpack/activity-log/assets/1838187/abdb196f-1d41-4b14-9dc5-2ac7c64cc84d)

<a name="requirements"></a>
## Requirements

- [Backpack\CRUD](https://github.com/Laravel-Backpack/CRUD) ^7.0

> This is a free Backpack add-on. For commercial use and licensing details, see the package license and Backpack pricing pages.

<a name="installation"></a>
## Installation

1) Install the package via Composer:

```bash
composer require backpack/language-switcher
```

2) (Optional) Publish the package config:

```bash
php artisan vendor:publish --provider="Backpack\LanguageSwitcher\LanguageSwitcherServiceProvider" --tag="config"
```

3) Add the middleware to your Backpack middleware stack in `config/backpack/base.php`:

```php
'middleware_class' => [
    ...
    \Backpack\LanguageSwitcher\Http\Middleware\LanguageSwitcherMiddleware::class,
],
```

_Optionally, you may add the middleware to the `web`, `api` or other middleware groups in `app/Http/Kernel.php` if you want the switcher outside the admin scope._

4) Add the dropdown view wherever you want the switcher to appear, for example in `topbar_right_content.blade.php`:

```php
@include('backpack.language-switcher::language-switcher')
```

5) Enable the languages your app supports in `config/backpack/crud.php`:

```php
'locales' => [
    'en' => 'English',
    'pt' => 'Portuguese',
    'ro' => 'Romanian',
    ...
],
```

Keep your app default locale in `config/app.php` under `locale` and `fallback_locale`.

<a name="usage"></a>
## Usage

### Show or hide flags and the label

You can pass options to the include:

```php
@include('backpack.language-switcher::language-switcher', [
    'flags' => true, // true by default
    'main_label' => false, // false by default; or a string like "Language"
])
```

### Use the switcher outside the Backpack admin

If you want the switcher on non-admin routes, register the middleware in `app/Http/Kernel.php` for the desired middleware group:

```php
protected $middlewareGroups = [
    'web' => [
        ...
        \Backpack\LanguageSwitcher\Http\Middleware\LanguageSwitcherMiddleware::class,
    ],
];
```

### Customize routes

Publish the config and customize the package route if needed. If you overwrite the endpoint, make sure to protect it with throttling or another security measure. The default route uses `'throttle:60,1'`.

<a name="notes"></a>
## Notes

This package uses [`outhebox/blade-flags`](https://github.com/MohmmedAshraf/blade-flags) to render flags for the locales.
If a flag is missing or incorrect, report the issue to the package maintainer.

<a name="security"></a>
## Security

If you discover a security issue, email `cristian.tabacitu@backpackforlaravel.com` instead of using the issue tracker.

<a name="license"></a>
## License

Language Switcher is released under the MIT License. See the package repository for full license details.
