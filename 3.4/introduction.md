# Introduction

Backpack v3.4 is a collection of packages for Laravel 5.5 / 5.6. You can use them to build custom admin panels fast, for anything from presentation websites to complex web applications. 

After getting to know Backpack, you'll be able to create a CRUD panel for each entity in just a few steps:

```bash
# STEP 1. create migration
php artisan make:migration:schema create_tags_table --model=0 --schema="name:string:unique"
php artisan migrate

# STEP 2. create a model, a request and a controller for the admin panel
php artisan backpack:crud tag #use singular, not plural

# STEP 3. manually add this to your routes.php file (under the admin prefix and admin middleware):
# CRUD::resource('tag', 'TagCrudController');
# 
# For example:
# Route::group(['prefix' => config('backpack.base.route_prefix'), 'middleware' => ['admin'], 'namespace' => 'Admin'], function()
# {
#    CRUD::resource('tag', 'TagCrudController');
# });
```

# Updates
If you're using Backpack, **please [subscribe to the monthly newsletter](http://backpackforlaravel.com/newsletter)**. That way we can let you know about security updates, breaking changes and new packages. We send newsletters every 1-3 months, only when there's something important.

# Requirements
- Laravel 5.5 or 5.6
- PHP 7+

# License
Backpack is free for non-commercial use and 39 EUR/project for commercial use. Please see  [the pricing section](https://backpackforlaravel.com/pricing) for details.

# Screenshots & Package Descriptions
Take a look at [backpackforlaravel.com](http://www.backpackforlaravel.com/)'s homepage.

# Quick List of Packages

You'll probably want to install both core packages. **All other packages are not mandatory** - you only install it if you need the feature:


## Core Packages:
  - Base - provides the layout (blade files, html and js);
  - CRUD - provides the logic for all CRUD operations; 

## Ready-built CRUDs
  - PermissionManager - interface to manage users & permissions;
  - Settings - interface to edit site-wide settings;
  - PageManager - CRUD for custom pages, using page templates;
  - MenuCRUD
  - NewsCRUD

## Extensions
  - LangFileManager - interface to edit Laravel language files;
  - LogManager - interface to preview Laravel log files;
  - BackupManager - interface to backup your files & db;
