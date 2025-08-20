# List Operation

---

<a name="about"></a>
## About

This operation shows a table with all database entries. It's the first page the admin lands on (for an entity), and it's usually the gateway to all other operations, because it holds all the buttons.

A simple List view might look like this:

![Backpack CRUD ListEntries](https://backpackforlaravel.com/uploads/docs-4-1/operations/listEntries.png)

But a complex implementation of the ListEntries operation, using Columns, Filters, custom Buttons, custom Operations, responsive table, Details Row, Export Buttons will still look pretty good:

![Backpack CRUD ListEntries](https://backpackforlaravel.com/uploads/docs-4-1/operations/listEntries_full.png)

You can easily customize [columns](#columns), [buttons](#buttons), [filters](#filters), [enable/disable additional features we've built](#other-features), or [overwrite the view and build your own features](#how-to-overwrite).

<a name="how-it-works"></a>
## How It Works

The main route leads to ```EntityCrudController::index()```, which shows the table view (```list.blade.php```. Inside that table view, we're using AJAX to fetch the entries and place them inside DataTables. That AJAX points to the same controller, ```EntityCrudController::search()```.

Actions:
- ```index()```
- ```search()```

For views, it uses:
- ```list.blade.php```
- ```columns/```
- ```buttons/```

<a name="how-to-use"></a>
## How to Use

Use the operation trait on your controller:
```php
<?php

namespace App\Http\Controllers\Admin;

use Backpack\CRUD\app\Http\Controllers\CrudController;

class ProductCrudController extends CrudController
{
    use \Backpack\CRUD\app\Http\Controllers\Operations\ListOperation;

    protected function setupListOperation()
    {
       // $this->crud->addColumn();
    }
}
```

Configuration for this operation should be done inside your ```setupListOperation()``` method. **For a minimum setup, you only need to define the columns you need to show in the table.** 

<a name="columns"></a>
### Columns

The List operation uses "columns" to determine how to show the attributes of an entry to the user. All column types must have their ```name```, ```label``` and ```type``` specified, but some could require some additional attributes.

```php
$this->crud->addColumn([
  'name' => 'name', // The db column name
  'label' => "Tag Name", // Table column heading
  'type' => 'Text'
]);
```

Backpack has 22+ [column types](/docs/{{version}}/crud-columns) you can use. Plus, you can easily [create your own type of column](/docs/{{version}}/crud-columns##creating-a-custom-column-type). **Check out the [Columns](/docs/{{version}}/crud-columns##creating-a-custom-column-type) documentation page** for a detailed look at column types, API and usage.

<a name="buttons"></a>
### Buttons

Buttons are used to trigger other operations. Some point to entirely new routes (```create```, ```update```, ```show```), others perform the operation on the current page using AJAX (```delete```).

The ShowList operation has 3 places where buttons can be placed:
  - ```top``` (where the Add button is)
  - ```line``` (where the Edit and Delete buttons are)
  - ```bottom``` (after the table)

Backpack adds a few buttons by default: 
- ```add``` to the ```top``` stack;
- ```edit``` and ```delete``` to the ```line``` stack;

To learn more about buttons, **check out the [Buttons](/docs/{{version}}/crud-buttons) documentation page**.

<a name="filters"></a>
### Filters

Filters show up right before the actual table, and provide a way for the admin to filter the results in the ListEntries table. To learn more about filters, **check out the [Filters](/docs/{{version}}/crud-filters) documentation page**.

<a name="other-features"></a>
### Other Features

<a name="details-row"></a>
#### Details Row

The details row functionality allows you to present more information in the table view of a CRUD. When enabled, a "+" button will show up next to every row, which on click will expand a "details row" below it, showing additional information.

![Backpack CRUD ListEntries Details Row](https://backpackforlaravel.com/uploads/docs-4-0/operations/listEntries_details_row.png)

On click, an AJAX request is sent to the ```entity/{id}/details``` route, which calls the ```showDetailsRow()``` method on your EntityCrudController. Everything returned by that method is then shown in the details row. You'll want to overwrite that method to show anything you'd like in the details row.

To use, inside your ```EntityCrudController```:
1. Enable the functionality: ```$this->crud->enableDetailsRow();```
2. Overwrite the ```showDetailsRow($id)``` method;

Alternative for the 2nd step: overwrite ```views/backpack/crud/details_row.blade.php``` which is called by the default ```showDetailsRow($id)``` functionality.

```php
class EntityCrudController extends CrudController
{
    use \Backpack\CRUD\app\Http\Operations\ListOperation;

    protected $setupDetailsRowRoutes = false;
}
```
<a name="export-buttons"></a>
#### Export Buttons

Exporting the DataTable to PDF, CSV, XLS is as easy as typing ```$this->crud->enableExportButtons();``` in your constructor. 

![Backpack CRUD ListEntries Details Row](https://backpackforlaravel.com/uploads/docs-4-0/operations/listEntries_export_buttons.png)

**Please note that when clicked, the button will export** 
- **the _currently visible_ table columns** (except columns marked as ```visibleInExport => false```); 
- **the columns that are forced to export** (with ```visibleInExport => true``` or ```exportOnlyField => true```);

**In the UI, the admin can use the "Visibility" button, and the "Items per page" dropdown to manipulate what is visible in the table - and consequently what will be exported.**

**Export Buttons Rules**

Available customization: 
```
'visibleInExport' => true/false
'visibleInTable' => true/false
'exportOnlyField' => true
```

By default, the field will start visible in the table. Users can hide it toggling visibility. Will be exported if visible in the table.

If you force `visibleInExport => true` you are saying that independent of field visibility in table it will **always** be exported.

Contrary is `visibleInExport => false`, even if visible in table, field will not be exported as per developer instructions.

Setting `visibleInTable => true` will force the field to stay in the table no matter what. User can't hide it. (By default all fields visible in the table will be exported. If you don't want to export this field use with combination with `visibleInExport => false`)

Using `'visibleInTable' => false` will make the field start hidden in the table. But users can toggle it's visibility.

If you want a field that is not on table, user can't show it, but will **ALWAYS** be exported use the `exportOnlyField => true`. If used will ignore any other custom visibility you defined.

<a name="custom-query"></a>
#### Custom Query

By default, all entries are shown in the ListEntries table, before filtering. If you want to restrict the entries to a subset, you can use the methods below in your EntityCrudController's ```setupListOperation()``` method:

```php
// Change what entries are shown in the table view.
// This changes all queries on the table view,
// as opposed to filters, who only change it when that filter is applied. 
$this->crud->addClause('active'); // apply a local scope
$this->crud->addClause('type', 'car'); // apply local dynamic scope
$this->crud->addClause('where', 'name', '=', 'car');
$this->crud->addClause('whereName', 'car');
$this->crud->addClause('whereHas', 'posts', function($query) {
     $query->activePosts();
 });
$this->crud->groupBy();
$this->crud->limit();

// please note it's generally a good idea to use crud->orderBy() inside 
// "if (!$this->crud->getRequest()->has('order')) {}";
// that way, your custom order is applied ONLY IF the user hasn't forced another order (by clicking a column heading)
$this->crud->orderBy();
```

<a name="responsive-table"></a>
#### Responsive Table

If your CRUD table has more columns than can fit inside the viewport (on mobile / tablet or smaller desktop screens), unimportant columns will start hiding and an expansion icon (three dots) will appear to the left of each row. We call this behaviour "_responsive table_", and consider this to be the best UX. By behaviour we consider the 1st column the most important, then 2nd, then 3rd, etc; the "actions" column is considered as important as the 1st column. You can of course [change the importance of columns](/docs/{{version}}/crud-columns#define-which-columns-to-hide-in-responsive-table).

If you do not like this, you can **toggle off the responsive behaviour for all CRUD tables** by changing this config value in your ```config/backpack/crud.php``` to ```false```:
```php
    // enable the datatables-responsive plugin, which hides columns if they don't fit?
    // if not, a horizontal scrollbar will be shown instead
    'responsive_table' => true
```

To turn off the responsive table behaviour for _just one CRUD panel_, you can use ```$this->crud->disableResponsiveTable()``` in your ```setupListOperation()``` method.

<a name="persistent-query"></a>
#### Persistent Table

By default, ListEntries will NOT remember your filtering, search and pagination when you leave the page. If you want ListEntries to do that, you can enable a ListEntries feature we call ```persistent_table```. 

**This will take the user back to the _filtered table_ after adding an item, previewing an item, creating an item or just browsing around**, preserving the table just like he/she left it - with the same filtering, pagination and search applied. It does so by saving the pagination, search and filtering for an arbitrary amount of time (by default: forever).

To use ```persistent_table``` you can:
- enable it for all CRUDs with the config option ```'persistent_table' => true``` in your ```config/backpack/crud.php```;
- enable it inside a particular crud controller with ```$this->crud->enablePersistentTable();```
- disable it inside a particular crud controller with ```$this->crud->disablePersistentTable();```

> You can configure the persistent table duration in ``` config/backpack/crud.php ``` under `operations > list > persistentTableDuration`. False is forever. Set any amount of time you want in minutes. Note: you can configure it's expiring time on a per-crud basis using `$this->crud->setOperationSetting('persistentTableDuration', 120); in your setupListOperation()` for 2 hours persistency. The default is `false` which means forever. 

<a name="how-to-overwrite"></a>
## How to Overwrite

The main route leads to ```EntityCrudController::index()```, which loads ```list.blade.php```. Inside that table view, we're using AJAX to fetch the entries and place them inside a DataTables. The AJAX points to the same controller, ```EntityCrudController::search()```.

<a name="the-view"></a>
### The View

You can change how the ```list.blade.php``` file looks and works, by just placing a file with the same name in your ```resources/views/vendor/backpack/crud/list.blade.php```. To quickly do that, run:

```zsh
php artisan backpack:publish crud/list
```

Keep in mind that by publishing this file, you won't be getting any updates we'll be pushing to it.

<a name="the-operation-logic"></a>
### The Operation Logic

Getting and showing the information is done inside the ```index()``` method. Take a look at the ```CrudController::index()``` method (your EntityCrudController is extending this CrudController) to see how it works.

To overwrite it, just create an ```index()``` method in your ```EntityCrudController```.

<a name="the-search-logic"></a>
### The Search Logic

An AJAX call is made to the ```search()``` method:
- when entries are shown in the table;
- when entries are filtered in the table;
- when search is performed on the table;
- when pagination is performed on the table;

You can of course overwrite this ```search()``` method by just creating one with the same name in your ```EntityCrudController```. In addition, you can overwrite what a specific column is searching through (and how), by [using the searchLogic attribute](/docs/{{version}}/crud-columns#custom-search-logic) on columns.
