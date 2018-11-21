# ListEntries

---

<a name="about"></a>
## About

This operation shows a table with all database entries. It's the first page the admin lands on (for an entity), and it's usually the gateway to all other operations, because it holds all the buttons.

A simple ListEntries view might look like this:

![Backpack CRUD ListEntries](https://backpackforlaravel.com/uploads/docs/operations/listEntries.png)

But a complex implementation of the ListEntries operation, using Columns, Filters, custom Buttons, custom Operations, responsive table, Details Row, Export Buttons will still look pretty good:

![Backpack CRUD ListEntries](https://backpackforlaravel.com/uploads/docs/operations/listEntries_full.png)

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

The ```ListEntries``` operation is **enabled by default**. To disable it, you can use ```$this->crud->denyAccess('list');``` inside your ```setup()``` method.

Configuration for this operation is usually done inside your ```setup()``` method. That's recommended, because the columns you define here are used by a few actions (```index()```, ```search()```, ```show()```).

**For a minimum setup, you only need to define the columns you need to show in the table.**

<a name="columns"></a>
### Columns

Columns represent the way your information is shown in the table view. All column types must have their ```name```, ```label``` and ```type``` specified, but some could require some other attributes too.

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

![Backpack CRUD ListEntries Details Row](https://backpackforlaravel.com/uploads/docs/operations/listEntries_details_row.png)

On click, an AJAX request is sent to the ```entity/{id}/details``` route, which calls the ```showDetailsRow()``` method on your EntityCrudController. Everything returned by that method is then shown in the details row. You'll want to overwrite that method to show anything you'd like in the details row.

To use, inside your ```EntityCrudController```:
1. Enable the functionality: ```$this->crud->enableDetailsRow();```
2. Allow access to all admins: ```$this->crud->allowAccess('details_row');```; Wrap an "if" statement around this if you don't want everybody to be able to see it.
3. Overwrite the ```showDetailsRow($id)``` method;

Alternative for the 3rd step: overwrite ```views/backpack/crud/details_row.blade.php``` which is called by the default ```showDetailsRow($id)``` functionality.

<a name="export-buttons"></a>
#### Export Buttons

Exporting the DataTable to PDF, CSV, XLS is as easy as typing ```$this->crud->enableExportButtons();``` in your constructor. 

![Backpack CRUD ListEntries Details Row](https://backpackforlaravel.com/uploads/docs/operations/listEntries_export_buttons.png)

>**Please note that when clicked, each button will export the _currently visible_ table.** You can use the "visibility" button, and the "Items per page" dropdown to manipulate what is inside the export.

<a name="custom-query"></a>
#### Custom Query

By default, all entries are shown in the ListEntries table, before filtering. If you want to restrict the entries to a subset, you can use the methods below in your EntityCrudController's ```setup()``` method:

```php
// Change what entries are show in the table view.
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

$this->crud->orderBy();
// please note it's generally a good idea to use crud->orderBy() inside "if (!$this->request->has('order')) {}"; that way, your custom order is applied ONLY IF the user hasn't forced another order (by clicking a column heading)
```

<a name="custom-query"></a>
#### Responsive Table

If you CRUD table has more columns than can fit inside the viewport (on mobile / tablet or smaller desktop screens), unimportant columns will start hiding and an expansion icon (three dots) will appear to the left of each row. We call this behaviour "_responsive table_", and consider this to be the best UX. By behaviour we consider the 1st column the most important, than 2nd, than 3rd, etc; the "actions" column is considered as important as the 1st column. You can of course [change the importance of columns](/docs/{{version}}/crud-columns#define-which-columns-to-hide-in-responsive-table).

If you do not like this, you can **toggle off the responsive behaviour for all CRUD tables** by changing this config value in your ```config/backpack/crud.php``` to ```false```:
```php
    // enable the datatables-responsive plugin, which hides columns if they don't fit?
    // if not, a horizontal scrollbar will be shown instead
    'responsive_table' => true
```

To turn off the responsive table behaviour for _just one CRUD panel_, you can use ```$this->crud->disableResponsiveTable()``` in your ```setup()``` method.

<a name="how-to-overwrite"></a>
## How to Overwrite

The main route leads to ```EntityCrudController::index()```, which loads ```list.blade.php```. Inside that table view, we're using AJAX to fetch the entries and place them inside a DataTables. The AJAX points to the same controller, ```EntityCrudController::search()```.

<a name="the-view"></a>
### The View

You can change how the ```list.blade.php``` file looks and works, by just placing a file with the same name in your ```resources/views/vendor/backpack/crud/list.blade.php```. To quickly do that, run:

```zsh
php artisan backpack:crud:publish list
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
