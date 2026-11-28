# FileManager <span class="badge badge-pill badge-success">FREE</span>

--

<a name="about"></a>
## About

Backpack FileManager adds a file browser to your admin panel using [barryvdh/laravel-elfinder](https://github.com/barryvdh/laravel-elfinder).
It creates a `public/uploads` folder, publishes the elFinder config and views, and adds a sidebar menu item so your admins can browse and manage files directly from Backpack.

![FileManager screenshot](https://backpackforlaravel.com/uploads/docs-4-0/media_library.png)

<a name="requirements"></a>
## Requirements

- [Backpack\CRUD](https://github.com/Laravel-Backpack/CRUD) ^7.0
- [barryvdh/laravel-elfinder](https://github.com/barryvdh/laravel-elfinder)

> This is a free Backpack add-on. For commercial use and licensing details, see the package license and Backpack pricing pages.

<a name="installation"></a>
## Installation

1) Install the package:

```bash
composer require backpack/filemanager
```

2) Run the package installer:

```bash
php artisan backpack:filemanager:install
```

3) Refresh your Backpack admin panel. You should now see a new sidebar item for FileManager.

<a name="usage"></a>
## Usage

You can use elFinder in Backpack:
- stand-alone, by visiting `/admin/elfinder` in your admin panel;
- in the `browse` field;
- in the `browse_multiple` field.

<a name="customization"></a>
## Customization

Publish the package views if you want to customize them:

```bash
php artisan vendor:publish --provider="Backpack\FileManager\FileManagerServiceProvider" --tag="elfinder-views"
php artisan vendor:publish --provider="Backpack\FileManager\FileManagerServiceProvider" --tag="elfinder-fields"
php artisan vendor:publish --provider="Backpack\FileManager\FileManagerServiceProvider" --tag="elfinder-columns"
```

<a name="security"></a>
## Security

If you discover a security issue, email `tabacitu@backpackforlaravel.com` instead of using the issue tracker.

<a name="license"></a>
## License

FileManager is released under the MIT License. See the package repository for full license details.
