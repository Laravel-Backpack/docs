---
title: "Upgrading CRUD 3.2 to 3.3"
excerpt: ""
---
## Requirements
- PHP 7+
- Laravel 5.5
- 15-45 minutes

## Breaking changes
- completely rewritten AjaxTables functionality - we fixed all AjaxTables issues and now AjaxDataTables are the default and only option;
- in create/update forms, fields without a tab are displayed *before* all tabs;

As you can see, we've struggled very hard to make this version as backwards-compatible as possible. As such, the upgrade steps are small, and few projects will be negatively affected, unless they've overwritten default functionality.

## Upgrade steps

(0) **Upgrade dependencies and require the latest versions**:
- upgrade your server to use PHP 7+, if you haven't already;
- [upgrade Laravel to 5.5](https://laravel.com/docs/5.5/upgrade), if you haven't already;
- upgrade Backpack\Base and Backpack\CRUD to the latest versions:
[block:code]
{
  "codes": [
    {
      "code": "composer require backpack/base:\"^0.8.0\"\ncomposer require backpack/crud:\"^3.3.0\"",
      "language": "shell",
      "name": "Command line"
    }
  ]
}
[/block]
(1) **Backpack\CRUD 3.3 comes with Backpack\Base 0.8**, which brings in:

- a design facelift (cleaner and more modern than standard AdminLTE); 
- automatic user profile images using gravatar, [with fallback to placehold or custom method](https://github.com/Laravel-Backpack/Base/pull/137/files#diff-41252036ab915c73d727350733dde754);
- views and logic for logged in user to change his account info ([screenshot here](https://user-images.githubusercontent.com/1032474/30285474-e1e5b888-9726-11e7-91d1-db8f43b9092f.png));
- views and logic for logged in user to change his password ([screenshot here](https://user-images.githubusercontent.com/1032474/30285534-0e684538-9727-11e7-9ddb-6912d73bc0e7.png));

**If you want to use these Backpack\Base features**, please:

- delete any ```base``` blade files that you have not personally customized, from your ```resources/views/vendor/backpack/base```, namely ```layout.blade.php```; or manually make the [small modification we've done to them](https://github.com/Laravel-Backpack/Base/pull/137/files#diff-2ebc13c56479282b02cdc2e35b3f7837);
- make [the following changes](https://github.com/Laravel-Backpack/Base/pull/183/files#diff-02c54bde9c6cf9ec3cfb193f9aa29518) to your ```resources/views/backpack/base/inc/sidebar.blade.php```;
- republish the CSS and JS assets by running ```php artisan vendor:publish --provider='Backpack\Base\BaseServiceProvider' --tag=public```;
- if you've customized the Base routes by adding a ```routes/backpack/base.php``` file to your project, please make sure you also include [the new routes](https://github.com/Laravel-Backpack/Base/pull/183/files#diff-155286bef496591139bf3ecbedae2a55), for changing the password and the user info;

(2) **If you use tabs in your CRUD's create/update forms**, please make sure all your fields have the "tab" attribute specified. Fields that *do not* have a tab specified were put in the last tab in 3.2, but they will now be put *before* all tabs.

(3) **If you're using AjaxDataTables** and you've customized the ```search()``` method in one of your CrudControllers to fix one of the "unfixable" issues in the prior implementation, you can now delete that method. It should "*just work*".

(4) **If you're NOT using AjaxDataTables**,  you will, after you upgrade. All CRUD tables are now AjaxDataTables.

(5)  **If you've used ```model_function```, ```model_function_attribute``` columns or the ```text``` column to actually get an accessor**, in any of your CRUDs, they will now work, even in your shiny new Ajax tables. But the search functionality will ignore them. This is a natural limitation - a model function pulls something from the database and manipulates it, no DBMS can search within something that's not *inside* the DBMS. But we have introduced a nifty system so that you *can* allow your users to search those columns: **you can now customize how a column is being searched**, using the ```searchLogic``` attribute. Set it to ```false``` and the column won't be searchable. Set it to a closure and that closure will be run upon search. Here are some relevant examples:
[block:code]
{
  "codes": [
    {
      "code": "<?php\n\n$this->crud->addColumn([\n    'name' => 'full_name', // there is no full_name column in the db, it's an accessor \n    'type' => 'text',\n    'label' => 'Full Name',\n    'searchLogic' => function ($query, $column, $searchTerm) {\n        $query->orWhere('first_name', 'like', '%'.$searchTerm.'%');\n        $query->orWhere('last_name', 'like', '%'.$searchTerm.'%');\n    }\n]);\n\n$this->crud->addColumn([\n    'name' => 'getColoredStatus', \n    'type' => 'model_function',\n    'label' => 'Status',\n    'searchLogic' => function ($query, $column, $searchTerm) {\n        $query->orWhere('status', 'like', '%'.$searchTerm.'%');\n    }\n]);",
      "language": "php"
    }
  ]
}
[/block]
(6) **Please note**, for ```select``` and ```select_multiple``` columns, AjaxDataTable now searches within the connected entities.

(7) **If you're using the ```Backpack\PageManager``` package**, you might need to run a ```composer update``` to update that to the latest version too. Your installed package might not have the search route for Pages, but the latest version does.