# Settings <span class="badge badge-pill badge-success">FREE</span>

--

<a name="about"></a>
## About

Backpack Settings provides a simple admin interface for managing site-wide application settings from the database. It stores settings in a `settings` table and loads them on boot, so your app can use them like config values while still letting administrators change them in the UI.

The package is especially useful when you want to make values such as email addresses, feature toggles, or other global options editable without redeploying code.

<a name="requirements"></a>
## Requirements

- [Backpack\CRUD](https://github.com/Laravel-Backpack/CRUD) ^7.0

<a name="installation"></a>
## Installation

Install the package using Composer:

```bash
composer require backpack/settings
```

Publish the config file if you need to customize the table name or migration file name:

```bash
php artisan vendor:publish --provider="Backpack\Settings\SettingsServiceProvider" --tag="config"
```

Publish the migration and run it:

```bash
php artisan vendor:publish --provider="Backpack\Settings\SettingsServiceProvider"
php artisan migrate
```

Optionally add a menu item for the Settings page:

```bash
php artisan backpack:add-menu-content "<x-backpack::menu-item title='Settings' icon='la la-cog' :link=\"backpack_url('setting')\" />"
```

For older Backpack versions, you can add a sidebar menu item instead:

```bash
php artisan backpack:add-sidebar-content "<li class='nav-item'><a class='nav-link' href='{{ backpack_url('setting') }}'><i class='nav-icon la la-cog'></i><span>Settings</span></a></li>"
```

If you want example data in the database, seed it:

```bash
php artisan db:seed --class="Backpack\Settings\database\seeds\SettingsTableSeeder"
```

<a name="usage"></a>
## Usage

### Admin interface

The Settings UI is available at:

```text
{your-admin-url}/setting
```

The interface shows the settings stored in the database and allows active settings to be edited using Backpack field definitions configured in the `field` column.

### Accessing settings from code

Settings behave like config values and can be read with either the `Setting` facade or the standard Laravel config helper:

```php
Setting::get('contact_email');

Config::get('settings.contact_email');
```

<a name="add-new-settings"></a>
## Add new settings

Settings are stored in the `settings` database table. Each row should contain:

- `key` — the settings key used in code
- `name` — human-friendly label
- `description` — help text shown in the admin UI
- `value` — the current value
- `field` — Backpack CRUD field configuration in JSON format (the field name must be `value`)
- `active` — whether the setting is enabled
- `created_at`, `updated_at`

There is no built-in UI to create new settings entries. Developers should add them manually in the database or via seeders, because the `field` column must contain valid Backpack field JSON.

### Example setting row

For example, to make `show_powered_by` editable as a checkbox:

| key | name | description | value | field | active |
| --- | ---- | ----------- | ----- | ----- | ------ |
| `show_powered_by` | `Show Powered By` | `Whether to show the powered by Backpack text.` | `1` | `{"name":"value","label":"Value","type":"checkbox"}` | `1` |

<a name="override-existing-configurations"></a>
## Override existing configurations

You can use Settings to override application configuration values from the database. For example, to override Backpack UI settings:

1. Create the setting entry in the database.
2. Read the setting in `AppServiceProvider::boot()` and apply it to config.

```php
public function boot()
{
    $this->overrideConfigValues();
}

protected function overrideConfigValues()
{
    $config = [];

    if (config('settings.show_powered_by')) {
        $config['backpack.ui.show_powered_by'] = config('settings.show_powered_by') == '1';
    }

    config($config);
}
```

<a name="overwriting"></a>
## Overwriting

If you need to customize the package behavior, you can override its routes, controllers, and models:

- create `routes/backpack/settings.php`; the package will load your routes file instead of its own
- extend package controllers and models, then use them in your custom routes

<a name="security"></a>
## Security

If you discover any security issues, email `tabacitu@backpackforlaravel.com` instead of opening an issue tracker entry.

Please subscribe to the Backpack Newsletter for security updates and major feature announcements.

<a name="license"></a>
## License

Backpack Settings is licensed under Backpack's licensing terms. See the package `LICENSE.md` file for details and review Backpack pricing for commercial use guidelines.
