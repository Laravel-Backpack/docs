# Introduction

---

Backpack is a collection of Laravel packages. It helps you build your own CMS, for anything from presentation websites to complex web applications. You can drop it on existing Laravel installations _or_ fresh projects.

In a nutshell:

- **Backpack\Base** will provide you with a visual interface for the admin panel; it pulls in the excellent AdminLTE HTML theme and adds authentication functionality & bubble notifications; so that when you decide to build a custom admin page/functionality, you already have a bunch of building blocks to use for the UI;
- **Backpack\CRUD** will help you build admin panels for your Eloquent models; a place for your admins to easily create/read/update/delete/reoder/preview/etc entries;

<a name="creating-crud-panels"></a>
## Creating CRUD Panels

After [understanding Backpack](/docs/{{version}}/getting-started-1-basics), you'll be able to create a CRUD panel for each entity in 10 minutes / model:

```bash
# STEP 0. create migration (in case you're starting from scratch)
php artisan make:migration:schema create_tags_table --model=0 --schema="name:string:unique"
php artisan migrate

# STEP 1. create a model, a request and a controller for the admin panel
php artisan backpack:crud tag #use singular, not plural

# STEP 2. add a route for this admin panel to routes/backpack/custom.php
php artisan backpack:base:add-custom-route "CRUD::resource('tag', 'TagCrudController');"

# STEP 3. add sidebar item to resources/views/vendor/backpack/base/inc/sidebar_content.blade.php
php artisan backpack:base:add-sidebar-content "<li><a href='{{ backpack_url('tag') }}'><i class='fa fa-tag'></i> <span>Tags</span></a></li>"

# STEP 4. go through the generated files, customize according to your needs
```

<a name="need-to-know"></a>
## Need to Know
- **Requirements:**
  - Laravel 5.5 or 5.6
  - PHP 7+
  - MySQL (recommended) / PosgreSQL / SQLite / SQL Server
- **Screenshots:** take a look at [our homepage](http://www.backpackforlaravel.com/).
- **Security** : Backpack has never had a critical vulnerability/hack. But there have been important security updates for some dependencies (including Laravel). Please  [subscribe to the monthly newsletter](https://backpackforlaravel.com/newsletter), so we can reach you in case anything bad happens. We only send one email per month, and we never ever share our list with anyone.
- **License**: Backpack is under a license we call "_You make money, I make money_" (YummY). Its source is public, and you can use it for free for non-commercial purposes (testing, non-profits, personal use, etc), but if you make money using it you need to purchase a commercial license. Please see  [the pricing section](https://backpackforlaravel.com/pricing) for more details.

<a name="extensions"></a>
## Extensions

Packages you can install or download, that treat common use cases. Some have been developed by us, some by our wonderful community. Check them out:

  - PermissionManager - interface to manage users & permissions;
  - Settings - interface to edit site-wide settings;
  - PageManager - CRUD for custom pages, using page templates;
  - MenuCRUD
  - NewsCRUD
  - LangFileManager - interface to edit Laravel language files;
  - LogManager - interface to preview Laravel log files;
  - BackupManager - interface to backup your files & db;

<a name="how-to-start"></a>
## How to Start

We heavily recommend you spend a little time to understand Backpack, and only afterwards install and use it. Currently your options are:
- **[Text Tutorial](/docs/{{version}}/getting-started-1-basics)** - 23 minutes
- **[Email Tutorial](http://backpackforlaravel.test/getting-started-emails)** - 1 email per day, for 5 days, 5 minutes each
- **Video Tutorial** - working on it