# NewsCRUD <span class="badge badge-pill badge-success">FREE</span>

--

<a name="about"></a>
## About

Backpack NewsCRUD is an admin panel example for creating and managing news articles, categories and tags using Backpack CRUD.
It includes everything you need to write articles, organize them into categories, tag them, and display the list in your admin panel.

This package is built as a Backpack CRUD example, so you can install it as a package or merge the example files directly into your application.

<a name="requirements"></a>
## Requirements

- [Backpack\CRUD](https://github.com/Laravel-Backpack/CRUD)
- [Backpack PRO](https://backpackforlaravel.com/pricing)

> NewsCRUD is an example package that uses Backpack PRO fields by default. If you do not have Backpack PRO, you can still install it manually and replace PRO fields with free equivalents.

<a name="installation"></a>
## Installation

NewsCRUD can be installed in two ways: download the example files into your project, or install it as a package.

### Option A: Download and merge files

1) Download the latest build from the repository.
2) Merge the `app` and `database` folders into your application.
3) Replace any namespace references from `Backpack\NewsCRUD\app` to your application namespace (usually `App`).
4) Run the migrations:

```bash
php artisan migrate
```

5) Add the package routes to your Backpack route group:

```php
Route::group([
    'prefix' => config('backpack.base.route_prefix', 'admin'),
    'middleware' => ['web', 'auth'],
    'namespace' => 'Admin',
], function () {
    Route::crud('article', 'ArticleCrudController');
    Route::crud('category', 'CategoryCrudController');
    Route::crud('tag', 'TagCrudController');
});
```

6) (Optional) Add a NewsCRUD menu dropdown in `resources/views/vendor/backpack/ui/inc/menu_items.blade.php`.

7) (Optional) If you need file uploads in the browse field, install [Laravel-Backpack/FileManager](https://github.com/Laravel-Backpack/FileManager#installation).

### Option B: Install as a package

1) Require the package:

```bash
composer require backpack/newscrud
```

2) Publish the package migration:

```bash
php artisan vendor:publish --provider="Backpack\NewsCRUD\NewsCRUDServiceProvider"
```

3) Run the migrations:

```bash
php artisan migrate
```

4) (Optional) Add the NewsCRUD menu dropdown to `resources/views/vendor/backpack/ui/inc/menu_items.blade.php`.

<a name="usage"></a>
## Usage

Once installed and migrated, NewsCRUD registers CRUD routes for:

- `article`
- `category`
- `tag`

You can manage articles, categories and tags from the Backpack admin panel.

<a name="customization"></a>
## Customization

If you want to customize the package, you can:

- create your own `routes/backpack/newscrud.php` file and register custom routes,
- extend the package controllers and models in your app,
- replace any PRO fields with free fields if you are not using Backpack PRO.

<a name="security"></a>
## Security

If you discover a security issue, email `tabacitu@backpackforlaravel.com` instead of using the issue tracker.

<a name="license"></a>
## License

NewsCRUD is released under the package license. See the repository for full license details.
