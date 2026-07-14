# LogManager <span class="badge badge-pill badge-success">FREE</span>

--

<a name="about"></a>
## About

Backpack LogManager provides a simple interface for previewing, downloading and deleting Laravel log files from the Backpack admin panel.
It works with the default Laravel logging setup and makes logs easy to review without leaving your application.

![LogManager interface](https://user-images.githubusercontent.com/1032474/131472960-be77e37c-313d-4049-ae54-95248cf3ad48.gif)

<a name="requirements"></a>
## Requirements

- [Backpack\CRUD](https://github.com/Laravel-Backpack/CRUD) ^7.0
- Laravel logging configured with a daily file rotation for the best experience

> This is a free Backpack add-on. For commercial use and licensing details, see the package license and Backpack pricing pages.

<a name="installation"></a>
## Installation

1) Install the package via Composer:

```bash
composer require backpack/logmanager
```

2) (Optional) Add a menu item for it in your Backpack admin panel:

```bash
php artisan backpack:add-menu-content "<x-backpack::menu-item title='Logs' icon='la la-terminal' :link=\"backpack_url('log')\" />"
```

If your project uses the legacy sidebar menu, use:

```bash
php artisan backpack:add-sidebar-content "<li class='nav-item'><a class='nav-link' href='{{ backpack_url('log') }}'><i class='nav-icon la la-terminal'></i> Logs</a></li>"
```

<a name="logging-configuration"></a>
## Logging configuration

For a better user experience, configure Laravel to create a new log file each day. In `config/logging.php`, make sure the `daily` channel is included in the default `stack` channel:

```php
'channels' => [
    'stack' => [
        'driver' => 'stack',
        'channels' => ['daily'],
    ],
    'daily' => [
        'driver' => 'daily',
        'path' => storage_path('logs/laravel.log'),
        'level' => 'debug',
        'days' => 7,
    ],
    // ...
],
```

You can adjust the number of days, file path, logging level, and other options as needed.

<a name="usage"></a>
## Usage

Visit the LogManager UI in your Backpack admin panel:

```text
{your-admin-url}/log
```

The interface lets you browse log entries by file, download backups of log files, and remove old logs.

<a name="customization"></a>
## Customization

To override LogManager behavior in your application:

- create `routes/backpack/logmanager.php`; the package will load your routes file instead of its own
- extend package controllers and models, then use them in your custom routes

<a name="security"></a>
## Security

If you discover any security issues, email `tabacitu@backpackforlaravel.com` instead of opening the issue tracker.

Please subscribe to the Backpack Newsletter for security updates and major feature announcements.

<a name="license"></a>
## License

LogManager is released under the package license. See the package repository for full license details.
