# BackupManager <span class="badge badge-pill badge-success">FREE</span>

--

<a name="about"></a>
## About

Backpack BackupManager provides a simple admin UI for managing application backups.
It builds on [spatie/laravel-backup](https://github.com/spatie/laravel-backup) and
lets your admin create, download, and delete backups from the Backpack panel.

![BackupManager screenshot](https://user-images.githubusercontent.com/1032474/161931994-dc044bb2-a459-4863-9262-ed91f3e5e35b.gif)

<a name="requirements"></a>
## Requirements

- [Backpack\CRUD](https://github.com/Laravel-Backpack/CRUD) ^7.0
- [spatie/laravel-backup](https://github.com/spatie/laravel-backup)

> This is a free Backpack add-on. For commercial use and licensing details, see the package license and Backpack pricing pages.

<a name="installation"></a>
## Installation

1) Install the package using Composer:

```bash
composer require backpack/backupmanager
```

2) Publish the BackupManager config, backup config and language files:

```bash
php artisan vendor:publish --provider="Backpack\BackupManager\BackupManagerServiceProvider" --tag=backup-config --tag=lang
```

3) (Optional) Add a menu item for the BackupManager page:

```bash
php artisan backpack:add-menu-content "<x-backpack::menu-item title='Backups' icon='la la-hdd-o' :link=\"backpack_url('backup')\" />"
```

If your project still uses the legacy sidebar menu, use:

```bash
php artisan backpack:add-sidebar-content "<li class='nav-item'><a class='nav-link' href='{{ backpack_url('backup') }}'><i class='nav-icon la la-hdd-o'></i> Backups</a></li>"
```

<a name="scheduling"></a>
## Scheduling backups

If you want backups to run automatically, add the commands to `app/Console/Kernel.php`:

```php
protected function schedule(Schedule $schedule)
{
    $schedule->command('backup:clean')->daily()->at('04:00');
    $schedule->command('backup:run')->daily()->at('05:00');
}
```

<a name="usage"></a>
## Usage

Visit the BackupManager UI in your admin panel:

```text
{your-admin-url}/backup
```

The interface should let you create backups on demand, download available backup files, and delete old backups.

<a name="config-troubleshooting"></a>
## Configuration & Troubleshooting

For additional backup configuration and notifications:

- Publish the Spatie backup config:

```bash
php artisan vendor:publish --provider="Spatie\Backup\BackupServiceProvider" --tag=backup-config
```

- Configure `config/backup.php` according to the [Spatie Laravel Backup documentation](https://spatie.be/docs/laravel-backup/v8/installation-and-setup).
- Review `config/backpack/backupmanager.php` to customize how the backup UI triggers commands. By default, BackupManager runs `backup:run --disable-notifications`.

If backups fail with:

> Backup failed because The dump process failed with exitcode 127 : Command not found.

then ensure `mysqldump` or `pg_dump` is installed and, if needed, set the dump binary path in `config/database.php`:

```php
'mysql' => [
    // ...
    'dump' => [
        'dump_binary_path' => '/path/to/directory/',
        'timeout' => 60 * 5,
        // 'exclude_tables' => ['table1', 'table2'],
        // 'add_extra_option' => '--optionname=optionvalue',
    ],
],
```

After modifying backup configuration, run `php artisan backup:run` manually to verify the setup. If necessary, clear compiled config:

```bash
php artisan optimize:clear
php artisan config:clear
```

<a name="customization"></a>
## Customization

To override BackupManager routes, controllers or models in your app:

- create `routes/backpack/backupmanager.php`; the package will load your routes file instead of its own,
- extend the package controllers or models and register your custom classes in your own routes.

<a name="security"></a>
## Security

If you discover any security issues, email `tabacitu@backpackforlaravel.com` instead of opening the issue tracker.

Please subscribe to the Backpack Newsletter for security updates and major feature announcements.

<a name="license"></a>
## License

BackupManager is released under the package license. See the package repository for full license details.
