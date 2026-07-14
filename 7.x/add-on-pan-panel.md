# PAN Panel <span class="badge badge-pill badge-success">FREE</span>

--

<a name="about"></a>
## About

Backpack PAN Panel adds a lightweight analytics dashboard to your admin UI by integrating the [panphp/pan](https://github.com/panphp/pan) product analytics package.

This package gives you a quick way to view PAN events in Backpack, with an admin interface for analytics and optional menu integration.

![PAN Panel analytics](https://github.com/user-attachments/assets/572d2b02-c069-4e41-b1f6-5b903d5b2ebd)

<a name="requirements"></a>
## Requirements

- [Backpack\CRUD](https://github.com/Laravel-Backpack/CRUD) ^7.0
- [panphp/pan](https://github.com/panphp/pan)

> This is a free Backpack add-on. For commercial use and licensing details, see the package license and Backpack pricing pages.

<a name="installation"></a>
## Installation

1) Install the package via Composer:

```bash
composer require backpack/pan-panel
```

2) (Optional) Publish the package config file to customize the default values:

```bash
php artisan vendor:publish --tag="pan-config"
```

3) If `panphp/pan` was not already installed, run the PAN installer:

```bash
php artisan pan:install
```
```

4) Configure PAN using `config/backpack/pan.php` after publishing the package config.

5) (Optional) Add a menu item to `resources/views/vendor/backpack/ui/inc/menu_items.blade.php`:

```html
<x-backpack::menu-item title="Analytics" icon="la la-dashboard" :link="backpack_url(config('backpack.pan.panel_route_prefix'))" />
```

<a name="usage"></a>
## Usage

After installation, visit the analytics panel in your Backpack admin and use it to inspect event data captured by PAN.

This package relies on the `panphp/pan` analytics library, so make sure your PAN configuration is correct and the tracking code is enabled in your app.

<a name="filters"></a>
## Filters

Backpack provides some out-of-the-box filters for the analytics UI. These filters only have effect if you have Backpack PRO installed.

<a name="security"></a>
## Security

If you discover a security issue, report it through the package repository or the maintainer's contact channels.

<a name="license"></a>
## License

This project is released under the MIT License. See the package repository for full license details.
