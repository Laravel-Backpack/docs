# ListEntries

---

## Columns

Columns represent the way your information is shown in the table view. 

All column types must have their name, label and type specified, but some could require some other attributes too.

```php
$this->crud->addColumn([
         'name' => 'name', // The db column name
         'label' => "Tag Name", // Table column heading
         'type' => 'Text'
         ]);
```

Check out the 12+ column types in the [CRUD > Features > Column Types](https://laravel-backpack.readme.io/docs/crud-column-types) section.

Each column type has its own blade file, which can be customized by placing a file with the same name in your ```resources\views\vendor\backpack\crud\columns```. To publish a view from the package to your resources folder use the ```backpack:crud:publish``` command. For example ```php artisan backpack:crud:publish columns/text``` will take the view from the package and copy it to the directory above, so you can edit it. Backpack will the use _your_ view, instead of the one in the package. Please note once you publish a view you won't get any updates we push to that view, so don't overdo this.

If you can't find the functionality you need in the existing column types, you can [very very easily roll your own](https://laravel-backpack.readme.io/v3.4/docs/crud-columns-types#section-roll-your-own).

## Details Row

The details row functionality allows you to present more information in the table view of a CRUD. When enabled, a "+" button will show up next to every row, which on click will expand a "details row" below it, showing additional information.
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/1214ccf-details_row.gif",
        "details row.gif",
        1034,
        688,
        "#e7e7dc"
      ],
      "border": false
    }
  ]
}
[/block]
Behind the scenes, on click an AJAX request is sent to the ```entity/{id}/details``` route, which calls the ```showDetailsRow()``` method on your EntityCrudController. Everything returned by that method is then shown in the details row. You'll want to overwrite that method to show anything you'd like in the details row.

**Usage** (everything in your EntityCrudController):
1. Enable the functionality: ```$this->crud->enableDetailsRow();```
2. Allow access to all users: ```$this->crud->allowAccess('details_row');```; Wrap an "if" statement around this if you don't want everybody to be able to see it.
3. Overwrite the ```showDetailsRow($id)``` method;

Alternative for the 3rd step: overwrite ```views/backpack/crud/details_row.blade.php``` which is called by the default showDetailsRow($id) functionality;

## Export buttons

Exporting the DataTable to PDF, CSV, XLS is as easy as typing ```$this->crud->enableExportButtons();``` in your constructor. 
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/7241f6e-Screen_Shot_2016-09-22_at_12.29.31.png",
        "Screen Shot 2016-09-22 at 12.29.31.png",
        1036,
        315,
        "#f3f6f6"
      ]
    }
  ]
}
[/block]

## Column and field order

You can use the ```beforeColumn('name')```, ```afterColumn('name')```, ```beforeField('name')```, ```afterField('name')``` methods to change the order of the fields/columns after the fact. Otherwise they will be shown in the order you write them.

Example usage:
```php
$this->crud->addColumn([
         'name' => 'name', // The db column name
         'label' => "Tag Name", // Table column heading
       'type' => 'text'
         ]);
$this->crud->addColumn([
         'name' => 'type', // The db column name
         'label' => "Tag Type", // Table column heading
       'type' => 'text'
         ]);
$this->crud->addColumn([
         'name' => 'id', // The db column name
         'label' => "Tag ID", // Table column heading
       'type' => 'text'
         ])->beforeColumn('name'); // ======> because of this, it will show up first
```

## Custom Search Logic in ListEntries View

If your column points to something atypical (not a value that is stored as plain text in the database column, maybe a model function, or a JSON, or something else), you might find that the search doesn't work for that column. You can choose which columns are searchable, and what those columns actually search, by using the column's ```searchLogic``` attribute:

```php
// column with custom search logic
$this->crud->addColumn([
    'name' => 'slug_or_title',
    'label' => 'Title',
    'searchLogic' => function ($query, $column, $searchTerm) {
        $query->orWhere('title', 'like', '%'.$searchTerm.'%');
    }
]);


// 1-n relationship column with custom search logic
$this->crud->addColumn([
    'label' => "Cruise Ship",
    'type' => "select",
    'name' => 'cruise_ship_id',
    'entity' => 'cruise_ship',
    'attribute' => "cruise_ship_name_date", // combined name & date column
    'model' => "App\Models\CruiseShip",
    'searchLogic' => function ($query, $column, $searchTerm) {
        $query->orWhereHas('cruise_ship', function ($q) use ($column, $searchTerm) {
            $q->where('name', 'like', '%'.$searchTerm.'%')
              ->orWhereDate('depart_at', '=', date($searchTerm));
        });
    }
]);


// column that doesn't need to be searchable
$this->crud->addColumn([
    'name' => 'slug_or_title',
    'label' => 'Title',
    'searchLogic' => false
]);

// column whose search logic should behave like it were a 'text' column type
$this->crud->addColumn([
    'name' => 'slug_or_title',
    'label' => 'Title',
    'searchLogic' => 'text'
]);
```

## Multiple Columns With the Same Name

Starting with Backpack\CRUD 3.3 (Nov 2017), you can have multiple columns with the same name, by specifying a unique ```key``` property. So if you want to use the same column name twice, you can do that. Notice below we have the same name for both columns, but one of them has a ```key```. This additional key will be used as an array key, if provided.

```php
// column that shows the parent's first name
$this->crud->addColumn([
   'label' => "Parent First Name", // Table column heading
   'type' => "select",
   'name' => 'parent_id', // the column that contains the ID of that connected entity;
   'entity' => 'parent', // the method that defines the relationship in your Model
   'attribute' => "first_name", // foreign key attribute that is shown to user
   'model' => "App\Models\User", // foreign key model
]);

// column that shows the parent's last name
$this->crud->addColumn([
   'label' => "Parent Last Name", // Table column heading
   'type' => "select",
   'name' => 'parent_id', // the column that contains the ID of that connected entity;
   'key' => 'parent_last_name', // the column that contains the ID of that connected entity;
   'entity' => 'parent', // the method that defines the relationship in your Model
   'attribute' => "last_name", // foreign key attribute that is shown to user
   'model' => "App\Models\User", // foreign key model
]);
```

## Define which columns to show or hide in the responsive table

By default, DataTables-responsive will try his best to show:
- **the first column** (since that usually is the most important for the user, plus it holds the modal button and the details_row button so it's crucial for usability);
- **the last column** (the actions column, where the action buttons reside);

When giving priorities, lower is better. So a column with priority 4 will be hidden BEFORE a column with priority 2. The first and last columns have a priority of 1. You can define a different priority for a column using the ```priority``` attribute. For example:

```php
$this->crud->addColumn([
                'name' => 'details',
                'type' => 'text',
                'label' => 'Details',
                'priority' => 2,
            ]);
$this->crud->addColumn([
                'name' => 'obs',
                'type' => 'text',
                'label' => 'Observations',
                'priority' => 3,
            ]);
```
In the example above, depending on how much space it's got in the viewport, DataTables will first hide the ```obs``` column, then ```details```, then the last column, then the first column.

You can make the last column be less important (and hide) by giving it an unreasonable priority:

```php
$this->crud->setActionsColumnPriority(10000);
```