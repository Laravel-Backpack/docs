# MenuCRUD <span class="badge badge-pill badge-success">FREE</span>

--

<a name="about"></a>
## About

Backpack MenuCRUD provides an admin interface for managing frontend menu items. It lets your admin add, edit, reorder, nest and rename links, and connect them to
Backpack
do pages, external URLs, or custom internal routes.

MenuCRUD is usually used for site navigation menus rather than the Backpack admin sidebar.

<a name="requirements"></a>
## Requirements

- [Backpack\CRUD](https://github.com/Laravel-Backpack/CRUD) ^7.0
- [Backpack\PageManager](https://github.com/Laravel-Backpack/pagemanager)

> MenuCRUD depends on Backpack PageManager for page content linking. Install PageManager first if you have not already.

<a name="installation"></a>
## Installation

1) Install the package via Composer:

```bash
composer require backpack/menucrud
```

2) Publish the migration:

```bash
php artisan vendor:publish --provider="Backpack\MenuCRUD\MenuCRUDServiceProvider"
```

3) Run the migration:

```bash
php artisan migrate
```

4) (Optional) Add a menu item for it in your Backpack admin panel:

```bash
php artisan backpack:add-menu-content "<x-backpack::menu-item title='Menu' icon='la la-list' :link=\"backpack_url('menu-item')\" />"
```

If your project uses the legacy sidebar menu, use:

```bash
php artisan backpack:add-sidebar-content "<li class='nav-item'><a class='nav-link' href='{{ backpack_url('menu-item') }}'><i class='nav-icon la la-list'></i> Menu</a></li>"
```

<a name="usage"></a>
## Usage

Visit the MenuCRUD interface in your admin panel:

```text
{your-admin-url}/menu-item
```

Use it to manage menu item structure and navigation links.

<a name="template-usage"></a>
## Template usage

Render the menu in your frontend using the `MenuItem` model.

Example:

```blade
@foreach (\App\MenuItem::getTree() as $item)
  <a href="{{ $item->url() }}">{{ $item->name }}</a>
@endforeach
```

If you installed MenuCRUD as a package, the model namespace is `Backpack\MenuCRUD\app\Models\MenuItem`.

<a name="customization"></a>
## Customization

To override routes or controllers:

- create `routes/backpack/menucrud.php`; the package will load your routes file instead of its own
- extend package controllers/models and use them in your custom routes

<a name="security"></a>
## Security

If you discover any security issues, email `tabacitu@backpackforlaravel.com` instead of opening the issue tracker.

Please subscribe to the Backpack Newsletter for security updates and major feature announcements.

<a name="license"></a>
## License

MenuCRUD is released under the package license. See the package repository for full license details.
