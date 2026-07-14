# TranslationManager <span class="badge badge-pill badge-success">FREE</span>

--

<a name="about"></a>
## About

Backpack TranslationManager provides a simple admin UI for managing your application's translation strings. It uses
[spatie/laravel-translation-loader](https://github.com/spatie/laravel-translation-loader) to store editable translations in the database,
while keeping your original `lang/` files intact.

With TranslationManager, your admin can:
- view a list of every translation key loaded from your `lang/` files;
- edit translations from the UI without touching source files;
- search and filter translations for easy management;
- optionally edit translations inline in the list view when [Editable Columns](/docs/{{version}}/crud-editable-columns) is installed.

<a name="requirements"></a>
## Requirements

- [Backpack\CRUD](https://github.com/Laravel-Backpack/CRUD) ^7.0
- [spatie/laravel-translation-loader](https://github.com/spatie/laravel-translation-loader)

> This is a free Backpack add-on. For commercial use and licensing details, see the package license and Backpack pricing pages.

<a name="installation"></a>
## Installation

1) Install the package using Composer:

```bash
composer require backpack/translation-manager
```

2) If you do not already use Spatie Translation Loader, replace Laravel's translation service provider in `config/app.php`:

```php
- Illuminate\Translation\TranslationServiceProvider::class,
+ Spatie\TranslationLoader\TranslationServiceProvider::class,
```

3) Publish and run the migrations for the Spatie package:

```bash
php artisan vendor:publish --provider="Spatie\TranslationLoader\TranslationServiceProvider" --tag="migrations"
php artisan migrate
```

4) Publish the package config files:

```bash
php artisan vendor:publish --provider="Spatie\TranslationLoader\TranslationServiceProvider" --tag="config"
php artisan vendor:publish --provider="Backpack\TranslationManager\AddonServiceProvider" --tag="config"
```

5) (Optional) Add a menu item for Translation Manager:

```bash
php artisan backpack:add-menu-content "<x-backpack::menu-item title=\"Translation Manager\" icon=\"la la-stream\" :link=\"backpack_url('translation-manager')\" />"
```

6) (Optional) If you want quick language switching in the admin, install [Language Switcher](https://github.com/Laravel-Backpack/language-switcher).

<a name="usage"></a>
## Usage

### Access the admin UI

After installation, Translation Manager is available in your Backpack admin panel at:

```text
{your-admin-url}/translation-manager
```

### Working with translations

- The package loads translation keys from your `lang/` folder.
- `lang/vendor/` translations are not supported by this package.
- When a translation is edited in the UI, the new value is saved to the database and takes priority over the language file.
- Because the database stores overrides, your deployments remain safe and no source files are modified.

### Editable Columns support

If you also install [Editable Columns](/docs/{{version}}/crud-editable-columns), administration becomes faster because translations can be edited directly in the Translation Manager list view.

If you prefer to disable inline editing, update `config/backpack/translation-manager.php`:

```php
'use_editable_columns' => false,
```

<a name="customization"></a>
## Customization

You can override Translation Manager routes, controllers or views by publishing the package files and registering your own classes in your application.

If you want to use a different storage model or load translations from a custom source, extend the package classes and swap them in your own service provider.

<a name="security"></a>
## Security

If you discover any security issues, email `tabacitu@backpackforlaravel.com` instead of opening the issue tracker.

Please subscribe to the Backpack Newsletter for security updates and major feature announcements.

<a name="license"></a>
## License

TranslationManager is released under the package license. See the package repository for full license details.
