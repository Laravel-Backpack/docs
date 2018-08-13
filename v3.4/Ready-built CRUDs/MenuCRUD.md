---
title: "MenuCRUD"
excerpt: ""
---
>> [Github](https://github.com/laravel-backpack/menucrud)

An admin panel for menu items on Laravel 5, using [Backpack\CRUD](https://github.com/Laravel-Backpack/crud). Add, edit, reorder, nest, rename menu items and link them to [Backpack\PageManager](https://github.com/Laravel-Backpack/pagemanager) pages, external link or custom internal link.

## Install

Since MenuCRUD is just a Backpack\CRUD example, you can choose to install it one of two ways.

**(A) Download and place files in your application** (recommended)

or

**(B) As a package**

The only PRO of installing it as a package is that you may benefit from updates. But the reality is there is very little (if any) bug fixing to do, so you probably won't need to update it, ever.



#### Installation type (A) - download


1) [Download the latest build](https://github.com/Laravel-Backpack/MenuCRUD/archive/master.zip).

2) Paste the 'app' and 'database' folders over your projects (merge them). No file overwrite warnings should come up.

3) Replace all mentions of 'Backpack\MenuCRUD\app' in the pasted files with your application's namespace ('App' if you haven't changed it):
- app/Http/Controllers/Admin/MenuItemCrudController.php
- app/Models/MenuItem.php

4) Run the migration to have the database table we need:
```
php artisan migrate
```

5) Add MenuCRUD to your routes file:

```
Route::group(['prefix' => 'admin', 'middleware' => ['web', 'auth'], 'namespace' => 'Admin'], function () {
    // Backpack\MenuCRUD
    CRUD::resource('menu-item', 'MenuItemCrudController');
});
```

6) [optional] Add a menu item for it in resources/views/vendor/backpack/base/inc/sidebar.blade.php or menu.blade.php:

```html
<li><a href="{{ url('admin/menu-item') }}"><i class="fa fa-list"></i> <span>Menu</span></a></li>
```



#### Installation type (B) - package

1) In your terminal, run:

``` bash
$ composer require backpack/MenuCRUD
```

2) Then add the service providers to your config/app.php file:

```
'Backpack\MenuCRUD\MenuCRUDServiceProvider',
```

3) Publish the migration:

```
php artisan vendor:publish --provider="Backpack\MenuCRUD\MenuCRUDServiceProvider"
```

4) Run the migration to have the database table we need:

```
php artisan migrate
```

5) [optional] Add a menu item for it in resources/views/vendor/backpack/base/inc/sidebar.blade.php or menu.blade.php:

```html
<li><a href="{{ url('admin/menu-item') }}"><i class="fa fa-list"></i> <span>Menu</span></a></li>
```