# LangFileManager <span class="badge badge-pill badge-success">FREE</span>

--

<a name="about"></a>
## About

Backpack LangFileManager provides a quick admin interface for editing Laravel
language files stored in `resources/lang`. It is designed to make it easier for
admins to manage translations from the panel.

> Note: LangFileManager edits translation files directly on disk. This can be
> convenient, but it may not be ideal for production workflows where language
> files are committed to git or deployed automatically.

<a name="deprecation-note"></a>
## Deprecation note

LangFileManager has been superseded by [TranslationManager](/docs/{{version}}/add-on-translation-manager),
which stores editable translations in the database and is more predictable for
production use.

We recommend switching to TranslationManager for most new projects.

<a name="requirements"></a>
## Requirements

- [Backpack\CRUD](https://github.com/Laravel-Backpack/CRUD) ^7.0
- [spatie/laravel-translation-loader](https://github.com/spatie/laravel-translation-loader) is not required for the basic editor, but recommended for advanced translation workflows.

<a name="installation"></a>
## Installation

1) Install the package via Composer:

```bash
composer require backpack/langfilemanager
```

2) Publish the package resources, migrations, config and language files:

```bash
php artisan vendor:publish --provider="Backpack\LangFileManager\LangFileManagerServiceProvider" --tag=migrations
php artisan migrate
php artisan db:seed --class="Backpack\LangFileManager\database\seeds\LanguageTableSeeder"
php artisan vendor:publish --provider="Backpack\LangFileManager\LangFileManagerServiceProvider" --tag=config
php artisan vendor:publish --provider="Backpack\LangFileManager\LangFileManagerServiceProvider" --tag=lang
```

3) Add menu items in `resources/views/vendor/backpack/ui/inc/menu_items.blade.php`:

```blade
<x-backpack::menu-dropdown title="Translations" icon="la la-globe">
  <x-backpack::menu-dropdown-item title="Languages" icon="la la-flag-checkered" :link="backpack_url('language')" />
  <x-backpack::menu-dropdown-item title="Site texts" icon="la la-language" :link="backpack_url('language/texts')" />
</x-backpack::menu-dropdown>
```

<a name="usage"></a>
## Usage

After installation, LangFileManager is available in your Backpack panel under:

```text
{your-admin-url}/language
{your-admin-url}/language/texts
```

Use these screens to manage available languages and edit text entries for each
language file.

<a name="configuration"></a>
## Configuration

You can hide language files from the LangFileManager UI in
`config/backpack/langfilemanager.php` using the `language_ignore` setting.

Example:

```php
'language_ignore' => ['admin', 'pagination', 'reminders', 'validation', 'log', 'crud'],
```

<a name="customization"></a>
## Customization

If you need to change the package behavior:

- publish the config file and adjust the settings;
- override views or controllers by publishing package resources;
- build your own routes if you want a different admin path.

<a name="security"></a>
## Security

This package directly edits language files. Do not expose it to untrusted
users and ensure only administrators can access it.

If you discover any security issues, email `cristian.tabacitu@backpackforlaravel.com`
instead of opening the issue tracker.

<a name="license"></a>
## License

LangFileManager is released under MIT License. See the package repository for
full license details.
