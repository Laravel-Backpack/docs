# PermissionManager <span class="badge badge-pill badge-success">FREE</span>

--

<a name="about"></a>
## About

Backpack PermissionManager adds a complete admin UI for managing users, roles and permissions using [spatie/laravel-permission](https://github.com/spatie/laravel-permission). It gives your admin panel a ready-made interface for role-based access control while keeping the underlying API fully compatible with the Spatie package.

With PermissionManager, you can:
- manage users and assign multiple roles;
- manage roles and permissions;
- allow users to have permissions directly, in addition to role-based permissions;
- integrate permission checks with Backpack CRUD access.

![PermissionManager admin interface](https://user-images.githubusercontent.com/1032474/149489620-a3e54d6e-db5f-4241-9afc-dc9451e54b64.gif)

<a name="requirements"></a>
## Requirements

- [Backpack\CRUD](https://github.com/Laravel-Backpack/CRUD) ^7.0
- [spatie/laravel-permission](https://github.com/spatie/laravel-permission)

> This is a free Backpack add-on. For commercial use and licensing details, see the package license and Backpack pricing pages.

<a name="installation"></a>
## Installation

1) Install the package via Composer:

```bash
composer require backpack/permissionmanager
```

2) Install the `spatie/laravel-permission` dependency and run its migrations:

```bash
php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider" --tag="permission-migrations"
php artisan migrate
php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider" --tag="permission-config"
```

3) Publish the PermissionManager config and migrations:

```bash
php artisan vendor:publish --provider="Backpack\PermissionManager\PermissionManagerServiceProvider" --tag="config" --tag="migrations"
```

4) Run the package migrations:

```bash
php artisan migrate
```

5) Make sure your user model uses both `CrudTrait` and `HasRoles`:

```php
use Backpack\CRUD\app\Models\Traits\CrudTrait;
use Spatie\Permission\Traits\HasRoles;

class User extends Authenticatable
{
    use CrudTrait;
    use HasRoles;
}
```

6) (Optional) Add menu links for Users, Roles and Permissions in your Backpack menu:

```html
<x-backpack::menu-dropdown title="Add-ons" icon="la la-puzzle-piece">
    <x-backpack::menu-dropdown-header title="Authentication" />
    <x-backpack::menu-dropdown-item title="Users" icon="la la-user" :link="backpack_url('user')" />
    <x-backpack::menu-dropdown-item title="Roles" icon="la la-group" :link="backpack_url('role')" />
    <x-backpack::menu-dropdown-item title="Permissions" icon="la la-key" :link="backpack_url('permission')" />
</x-backpack::menu-dropdown>
```

<a name="optional-guard-setup"></a>
## Optional Guard Setup

If you want to use Blade `@can` directives inside Backpack routes, you may need to align Backpack's auth guard with Laravel's default guard.

In `config/backpack/base.php`, set:

```php
'guard' => null,
```

This makes `auth()` behave like `backpack_auth()` on Backpack routes. If you prefer not to change the default guard, you can still use permission checks with `backpack_user()->can('...')`.

<a name="usage"></a>
## Usage

### Accessing the admin UI

The package installs CRUD controllers for users, roles and permissions. Once installed, the routes are available under your Backpack admin prefix, like:

- `/admin/user`
- `/admin/role`
- `/admin/permission`

### Using permissions in code

The package exposes the same API as Spatie:

```php
backpack_user()->givePermissionTo('edit articles');
backpack_user()->revokePermissionTo('edit articles');
backpack_user()->hasPermissionTo('edit articles');
backpack_user()->can('edit articles');
```

### Using roles in code

```php
backpack_user()->assignRole('writer');
backpack_user()->removeRole('writer');
backpack_user()->hasRole('writer');
backpack_user()->hasAnyRole(['writer', 'admin']);
backpack_user()->hasAllRoles(['writer', 'editor']);
```

### Permissions on roles

```php
$role->givePermissionTo('edit articles');
$role->revokePermissionTo('edit articles');
$role->hasPermissionTo('edit articles');
```

<a name="customize"></a>
## Customize

You can override package controllers by binding your own controller class in a service provider:

```php
$this->app->bind(
    \Backpack\PermissionManager\app\Http\Controllers\UserCrudController::class,
    \App\Http\Controllers\Admin\UserCrudController::class
);
```

If you prefer, define your own Backpack routes in `routes/backpack/permissionmanager.php` and point them to your customized controllers.

<a name="api-usage"></a>
## API Usage

Because PermissionManager builds on `spatie/laravel-permission`, most of the API is the same. Use Spatie's documentation for full details, but this package gives you the admin UI and integrates the same permission and role methods into Backpack.

<a name="use-permissions-in-crud-controllers"></a>
## Use permissions in CRUD controllers

You can integrate permissions with Backpack CRUD access by dynamically allowing or denying operations based on Spatie permissions.

For example, create a trait that maps permission names to CRUD operations:

```php
trait CrudPermissionTrait
{
    public array $operations = ['list', 'show', 'create', 'update', 'delete'];

    public function setAccessUsingPermissions()
    {
        $this->crud->denyAccess($this->operations);

        $table = CRUD::getModel()->getTable();
        $user = request()->user();

        if (! $user) {
            return;
        }

        foreach ([
            'see' => ['list', 'show'],
            'edit' => ['list', 'show', 'create', 'update', 'delete'],
        ] as $level => $operations) {
            if ($user->can("{$table}.{$level}")) {
                $this->crud->allowAccess($operations);
            }
        }
    }
}
```

Then use it in a controller that extends the package controller:

```php
use Backpack\PermissionManager\app\Http\Controllers\UserCrudController as BackpackUserCrudController;

class UserCrudController extends BackpackUserCrudController
{
    use \App\Traits\CrudPermissionTrait;

    public function setup()
    {
        parent::setup();
        $this->setAccessUsingPermissions();
    }
}
```

<a name="upgrading"></a>
## Upgrading from 3.x to 4.x

To upgrade from PermissionManager 3.x to 4.x:
- upgrade `spatie/laravel-permission` to `^2.28.2`
- update `backpack/permissionmanager` to `4.0.*`
- delete your old `config/backpack/permissionmanager.php`
- follow the package installation steps again
- if you are on Laravel 8, ensure `config('backpack.base.user_model_fqn')` points to the correct `App\Models\User` class

<a name="overwriting"></a>
## Overwriting

To overwrite package routes or controllers:
- create `routes/backpack/permissionmanager.php`
- create your own controllers/models extending the package classes
- use your custom routes to register the controllers instead of the package defaults

<a name="security"></a>
## Security

If you discover security-related issues, email `tabacitu@backpackforlaravel.com` instead of opening an issue tracker entry.

<a name="credits"></a>
## Credits

- [Marius Constantin](https://github.com/updivision) - Lead Developer
- [Cristian Tabacitu](https://github.com/tabacitu)
- All contributors

<a name="license"></a>
## License

Backpack PermissionManager is free for non-commercial use and may require a commercial license for commercial use. See the package `LICENSE.md` file and Backpack pricing for details.
