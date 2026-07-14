# Activity Log <span class="badge badge-pill badge-success">FREE</span>

--

<a name="about"></a>
## About

Backpack Activity Log provides an admin interface for browsing `spatie/laravel-activitylog` entries.
It lets your admin view who changed what and when, inspect activity details, and optionally add activity buttons to Backpack CRUD controllers.

![Activity Log list view](https://user-images.githubusercontent.com/1032474/264732691-b77f1585-08f0-4eb8-88aa-3b4226a65567.png)

<a name="requirements"></a>
## Requirements

- [Backpack\CRUD](https://github.com/Laravel-Backpack/CRUD) ^7.0
- [spatie/laravel-activitylog](https://github.com/spatie/laravel-activitylog)

> This is a free Backpack add-on. For commercial use and licensing details, see the package license and Backpack pricing pages.

<a name="installation"></a>
## Installation

1) Install the package via Composer:

```bash
composer require backpack/activity-log
```

2) (Optional) Add a menu item for Activity Logs in your Backpack admin panel:

```bash
php artisan backpack:add-menu-content "<x-backpack::menu-item title='Activity Logs' icon='la la-stream' :link=\"backpack_url('activity-log')\" />"
```

If your project uses the legacy sidebar menu, use:

```bash
php artisan backpack:add-sidebar-content "<li class='nav-item'><a class='nav-link' href='{{ backpack_url('activity-log') }}'><i class='nav-icon la la-stream'></i> Activity Logs</a></li>"
```

3) If you do not already have `spatie/laravel-activitylog` installed and configured, follow Spatie's installation steps before using Activity Log.

<a name="usage"></a>
## Usage

Visit the Activity Log UI in your Backpack admin panel:

```text
{your-admin-url}/activity-log
```

Use it to:
- browse activity entries in a searchable, filterable list;
- inspect details for each activity entry;
- review who caused the action and which model was affected.

![Activity Log entry view](https://user-images.githubusercontent.com/1032474/264734509-b13b520f-a732-4f84-be1c-db136e5fa160.png)

<a name="crud-controller-operations"></a>
## CrudController operations

Activity Log includes Backpack CRUD operations that add activity links to your controllers:

- `\Backpack\ActivityLog\Http\Controllers\Operations\ModelActivityOperation`
- `\Backpack\ActivityLog\Http\Controllers\Operations\EntryActivityOperation`

Example usage in a CRUD controller:

```php
use Backpack\ActivityLog\Http\Controllers\Operations\ModelActivityOperation;
use Backpack\ActivityLog\Http\Controllers\Operations\EntryActivityOperation;

class ArticleCrudController extends CrudController
{
    use ModelActivityOperation;
    use EntryActivityOperation;

    public function setup()
    {
        parent::setup();
    }
}
```

These operations let you add links to an entity's activity history directly from the CRUD interface.

<a name="spatie-activitylog-setup"></a>
## Spatie activity log setup

This package only provides the admin UI. To record activity, you must configure `spatie/laravel-activitylog` in your app.

Common setup steps include publishing the Spatie config and migrations:

```bash
php artisan vendor:publish --provider="Spatie\Activitylog\ActivitylogServiceProvider" --tag="activitylog-config"
php artisan vendor:publish --provider="Spatie\Activitylog\ActivitylogServiceProvider" --tag="activitylog-migrations"
php artisan migrate
```

Then enable activity logging on the models you want to track.

<a name="customization"></a>
## Customization

To override Activity Log routes or controllers:

- create `routes/backpack/activity-log.php`; the package will load your routes file instead of its own
- extend package controllers/models and use them in your custom routes

<a name="security"></a>
## Security

If you discover any security issues, email `cristian.tabacitu@backpackforlaravel.com` instead of opening the issue tracker.

Please subscribe to the Backpack Newsletter for security updates and major feature announcements.

<a name="license"></a>
## License

Activity Log is released under the package license. See the package repository for full license details.
