# PageManager <span class="badge badge-pill badge-success">FREE</span>

--

<a name="about"></a>
## About

Backpack PageManager provides an admin interface for creating and editing presentation pages in your application.
It lets developers define page templates with fields, and administrators use those templates to create pages without writing code.

![PageManager edit page](https://user-images.githubusercontent.com/1032474/106446854-6dc73100-6489-11eb-9e4c-b21273cef23e.png)

<a name="requirements"></a>
## Requirements

- [Backpack\CRUD](https://github.com/Laravel-Backpack/CRUD) ^7.0
- Laravel logging configured for daily files is not required by PageManager itself, but good logging practices are still recommended for your project.

> This is a free Backpack add-on. For commercial use and licensing details, see the package license and Backpack pricing pages.

<a name="installation"></a>
## Installation

1) Install the package via Composer:

```bash
composer require backpack/pagemanager
```

2) Publish the package views, migrations and config files:

```bash
php artisan vendor:publish --provider="Backpack\PageManager\PageManagerServiceProvider"
```

3) Run the migration:

```bash
php artisan migrate
```

4) (Optional) Add a menu item for Pages in your Backpack admin panel:

```bash
php artisan backpack:add-menu-content "<x-backpack::menu-item title='Pages' icon='la la-file-o' :link=\"backpack_url('page')\" />"
```

If your project uses the legacy sidebar menu, add:

```bash
php artisan backpack:add-sidebar-content "<li class='nav-item'><a class='nav-link' href='{{ backpack_url('page') }}'><i class='nav-icon la la-file-o'></i> Pages</a></li>"
```

<a name="page-templates"></a>
## Page templates

Define your page templates in `app/PageTemplates.php` using Backpack CRUD field definitions. Each template is a method that adds fields to the create/update page form.

Example:

```php
<?php

namespace App;

trait PageTemplates
{
    private function services()
    {
        $this->crud->addField([
            'name' => 'content',
            'label' => trans('backpack::pagemanager.content'),
            'type' => 'summernote',
            'placeholder' => trans('backpack::pagemanager.content_placeholder'),
        ]);
    }
}
```

<a name="usage"></a>
## Usage

Visit the PageManager admin interface:

```text
{your-admin-url}/page
```

Create pages using the available templates, then use the generated page slug in your front-end routing.

<a name="example-front-end"></a>
## Example front-end

PageManager does not provide a front-end by default. A common pattern is to create a catch-all route and render pages using a controller.

Example route:

```php
Route::get('{page}/{subs?}', [App\Http\Controllers\PageController::class, 'index'])
    ->where(['page' => '^(((?=(?!admin))(?=(?!\/)).))*$', 'subs' => '.*']);
```

In your controller, load the page model and render the template view:

```php
use Backpack\PageManager\app\Models\Page;

public function index($slug, $subs = null)
{
    $page = Page::findBySlug($slug);

    if (! $page) {
        abort(404);
    }

    return view('pages.' . $page->template, ['page' => $page->withFakes()]);
}
```

<a name="customization"></a>
## Customization

To customize PageManager behavior:

- publish the config file with `php artisan vendor:publish --provider="Backpack\PageManager\PageManagerServiceProvider"`
- create `routes/backpack/pagemanager.php`; the package will load your routes instead of its own
- extend package controllers and models, then register them in your new configuration/routes

<a name="security"></a>
## Security

If you discover any security issues, email `hello@backpackforlaravel.com` instead of opening the issue tracker.

Please subscribe to the Backpack Newsletter for security updates and major feature announcements.

<a name="license"></a>
## License

PageManager is released under the package license. See the package repository for full license details.
