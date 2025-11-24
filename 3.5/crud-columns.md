# Columns

---

<a name="about"></a>
## About

A column shows the information of an Eloquent attribute, in a user-friendly format. 

It's used inside default operations to:
- show a table cell in **ListEntries**;
- show an attribute value in **Show**;

A column consists of only one file - a blade file with the same name as the column type (ex: ```text.blade.php```). Backpack provides you with [default column types](#default-column-types) for the common use cases, but you can easily [change how a default field type works](#overwriting-default-column-types), or [create an entirely new field type](#creating-a-custom-column-type).

<a name="mandatory-attributes"></a>
### Mandatory Attributes

When passing a column array, you need to specify at least these attributes:
```php
[
   'name' => 'options', // the db column name (attribute name)
   'label' => "Options", // the human-readable label for it
   'type' => 'text' // the kind of column to show
],
```

<a name="optional-attributes"></a>
### Optional Attributes

- [```searchLogic```](#custom-search-logic)
- [```orderLogic```](#custom-order-logic)
- [```orderable```](#custom-order-logic)
- [```visibleInTable```](#choose-where-columns-are-visible)
- [```visibleInModal```](#choose-where-columns-are-visible)
- [```visibleInExport```](#choose-where-columns-are-visible)
- [```visibleInShow```](#choose-where-columns-are-visible)
- [```priority```](#define-which-columns-to-hide-in-responsive-table)

<a name="columns-api"></a>
### Columns API

Inside your ```setup()``` method there are a few calls you can make to configure or manipulate columns:

```php
// add a column, at the end of the stack
$this->crud->addColumn($column_definition_array);

// add multiple columns, at the end of the stack
$this->crud->addColumns([$column_definition_array, $another_column_definition_array]);

// remove a column from the stack
$this->crud->removeColumn('column_name');

// remove an array of columns from the stack
$this->crud->removeColumns(['column_name_1', 'column_name_2']);

// change the attributes of a column
$this->crud->modifyColumn($name, $modifs_array);
$this->crud->setColumnDetails('column_name', ['attribute' => 'value']);

// change the attributes of multiple columns
$this->crud->setColumnsDetails(['column_1', 'column_2'], ['attribute' => 'value']);

// forget what columns have been previously defined, only use these columns
$this->crud->setColumns([$column_definition_array, $another_column_definition_array]);
```

In addition, to manipulate the order columns are shown in, you can:

```php
// add this column before a given column
$this->crud->addColumn('text')->beforeColumn('name');

// add this column after a given column
$this->crud->addColumn()->afterColumn('name');

// make this column the first one in the list
$this->crud->addColumn()->makeFirstColumn();
```

<a name="default-column-types"></a>
## Default Column Types

<a name="array"></a>
### array

Enumerate an array stored in the db column as JSON.
```php
[
   'name' => 'options', // The db column name
   'label' => "Options", // Table column heading
   'type' => 'array'
],
```

<a name="array_count"></a>
### array_count

Count the items in an array stored in the db column as JSON.

```php
[
   'name' => 'options', // The db column name
   'label' => "Options", // Table column heading
   'type' => 'array_count',
   // 'suffix' => 'options', // if you want it to show "2 options" instead of "2 items"
],
```

<a name="boolean"></a>
### boolean

Show Yes/No (or custom text) instead of 1/0.

```php
[
    'name' => 'name',
    'label' => 'Status',
    'type' => 'boolean',
    // optionally override the Yes/No texts
    // 'options' => [0 => 'Active', 1 => 'Inactive']
],
```

<a name="check"></a>
### check

Show a favicon with a checked or unchecked box, depending on the given boolean.
```php
[
   'name' => 'featured', // The db column name
   'label' => "Featured", // Table column heading
   'type' => 'check'
],
```


<a name="checkbox"></a>
### checkbox

Shows a checkbox (the form element), and inserts the js logic needed to select/deselect multiple entries. It is mostly used for [the Bulk Delete action](/docs/{{version}}/crud-operation-delete#delete-multiple-items-bulk-delete), and [custom bulk actions](/docs/{{version}}/crud-operations#creating-a-new-operation-with-a-bulk-action-no-interface).

Shorthand:
```php
$this->crud->enableBulkActions();
```
(will also add an empty custom_html column)

Verbose:
```php
$this->crud->addColumn([
    'type' => 'checkbox',
    'name' => 'bulk_actions',
    'label' => ' <input type="checkbox" class="crud_bulk_actions_main_checkbox" style="width: 16px; height: 16px;" />',
    'priority' => 1,
    'searchLogic' => false,
    'orderable' => false,
    'visibleInModal' => false,
])->makeFirstColumn();
```

<a name="closure"></a>
### closure


Show custom HTML based on a closure you specify in your EntityCrudController. Please note this column does not escape HTML before rendering. You need to do that yourself, if you consider it necessary.

```php
[
    'name' => 'created_at',
    'label' => 'Created At',
    'type' => 'closure',
    'function' => function($entry) {
        return 'Created on '.$entry->created_at;
    }
],
```

<a name="date"></a>
### date


The date column will show a localized date in the default date format (as specified in the ```config/backpack/base.php``` file), whether the attribute is casted as date in the model or not.

Note that the ```format``` attribute uses ISO date formatting parameters and not PHP ```date()``` formatters.  See <https://carbon.nesbot.com/docs/#iso-format-available-replacements> for more information.

```php
[
   'name' => "name", // The db column name
   'label' => "Tag Name", // Table column heading
   'type' => "date",
    // 'format' => 'l j F Y', // use something else than the base.default_date_format config value
],
```

<a name="datetime"></a>
### datetime


The date column will show a localized datetime in the default datetime format (as specified in the ```config/backpack/base.php``` file), whether the attribute is casted as datetime in the model or not.

Note that the ```format``` attribute uses ISO date formatting parameters and not PHP ```date()``` formatters.  See <https://carbon.nesbot.com/docs/#iso-format-available-replacements> for more information.


```php
[
   'name' => "name", // The db column name
   'label' => "Tag Name", // Table column heading
   'type' => "datetime",
    // 'format' => 'l j F Y H:i:s', // use something else than the base.default_datetime_format config value
],
```

<a name="email"></a>
### email

The email column will output the email address in the database (truncated to 254 characters if needed), with a ```mailto:``` link towards the full email. Its definition is:
```php
[
   'name' => 'email', // The db column name
   'label' => "Email Address", // Table column heading
   'type' => 'email',
   // 'limit' => 500, // if you want to truncate the text to a different number of characters
],
```

<a name="image"></a>
### image


Show a thumbnail image.

```php
[
   'name' => 'profile_image', // The db column name
   'label' => "Profile image", // Table column heading
   'type' => 'image',
    // 'prefix' => 'folder/subfolder/',
    // optional width/height if 25px is not ok with you
    // 'height' => '30px',
    // 'width' => '30px',
],
```

<a name="model_function"></a>
### model_function


The model_function column will output a function on your main model. Its definition is:
```php
[
   // run a function on the CRUD model and show its return value
   'name' => "url",
   'label' => "URL", // Table column heading
   'type' => "model_function",
   'function_name' => 'getSlugWithLink', // the method in your Model
   // 'function_parameters' => [$one, $two], // pass one/more parameters to that method
   // 'limit' => 100, // Limit the number of characters shown
],
```
For this example, if your model would feature this method, it would return the link to that entity:
```php
public function getSlugWithLink() {
    return '<a href="'.url($this->slug).'" target="_blank">'.$this->slug.'</a>';
}
```

**Note:** When displaying this column's value, the text is not escaped. That is intentional. This way, you can use it to show labels, color text, italic, bold, links, etc. If you might have malicious JS or CSS in your values, you can create a new escaped field yourself. But it's probably better to treat the problem at the source, and prevent JS and CSS from reaching your DB in the first place.

<a name="model_function_attribute"></a>
### model_function_attribute


If the function you're trying to use returns an object, not a string, you can use the model_function_attribute column, which will output the attribute on the function result. Its definition is:
```php
[
   'name' => "url",
   'label' => "URL", // Table column heading
   'type' => "model_function_attribute",
   'function_name' => 'getSlugWithLink', // the method in your Model
   // 'function_parameters' => [$one, $two], // pass one/more parameters to that method
   'attribute' => 'route',
   // 'limit' => 100, // Limit the number of characters shown
],
```

**Note:** When displaying this column's value, the text is not escaped. That is intentional. This way, you can use it to show labels, color text, italic, bold, links, etc. If you might have malicious JS or CSS in your values, you can create a new escaped field yourself. But it's probably better to treat the problem at the source, and prevent JS and CSS from reaching your DB in the first place.

<a name="multidimensional_array"></a>
### multidimensional_array


Enumerate the values in a multidimensional array, stored in the db as JSON.

```php
[
   'name' => 'options', // The db column name
   'label' => "Options", // Table column heading
   'type' => 'multidimensional_array',
   'visible_key' => 'name' // The key to the attribute you would like shown in the enumeration
],
```

<a name="number"></a>
### number


The text column will just output the number value of a db column (or model attribute). Its definition is:
```php
[
   'name' => 'name', // The db column name
   'label' => "Tag Name", // Table column heading
   'type' => "number",
   // 'prefix' => "$",
   // 'suffix' => " EUR",
   // 'decimals' => 2,
],
```

<a name="radio"></a>
### radio


Show a pretty text instead of the database value, according to an associative array. Usually used as a column for the "radio" field type.

```php
[
    'name'        => 'status',
    'label'       => 'Status',
    'type'        => 'radio',
    'options'     => [
                        0 => "Draft",
                        1 => "Published"
                    ]
],
```

This example will show: 
- "Draft" when the value stored in the db is 0;
- "Published" when the value stored in the db is 1;  

<a name="row_number"></a>
### row_number


Show the row number (index). The number depends strictly on the result set (x records per page, pagination, search, filters, etc). It does not get any information from the database. It is not searchable. It is only useful to show the current row number.

```
$this->crud->addColumn([
    'name' => 'row_number',
    'type' => 'row_number',
    'label' => '#',
    'orderable' => false,
])->makeFirstColumn();
```

Notes:
- you can have a different ```name```; just make sure your model doesn't have that attribute;
- you can have a different label;
- you can place the column as second / third / etc if you remove ```makeFirstColumn()```;
- this column type allows the use of suffix/prefix just like the text column type;
- if upon placement you notice it always shows ```false``` then please note there have been changes in the ```search()``` method - you need to add another parameter to your ```getEntriesAsJsonForDatatables()``` call;

<a name="text"></a>
### text

The text column will just output the text value of a db column (or model attribute). Its definition is:
```php
[
   'name' => 'name', // The db column name
   'label' => "Tag Name", // Table column heading
   // 'prefix' => "Name: ",
   // 'suffix' => "(user)",
   // 'limit' => 120, // character limit; default is 50;
],
```

**Advanced use case:** The ```text``` column type can also show the attribute of a 1-1 relationship. If you have a relationship (like ```parent()```) set up in your Model, you can use relationship and attribute in the ```name```, using dot notation:
```php
[
    'name' => 'parent.title',
    'label' => 'Title',
    'type' => 'text'
],
```

<a name="select"></a>
### select

The select column will output its connected entity. Used for relationships like hasOne() and belongsTo(). Its name and definition is the same as for the select *field type*:
```php
[
   // 1-n relationship
   'label' => "Parent", // Table column heading
   'type' => "select",
   'name' => 'parent_id', // the column that contains the ID of that connected entity;
   'entity' => 'parent', // the method that defines the relationship in your Model
   'attribute' => "name", // foreign key attribute that is shown to user
   'model' => "App\Models\Category", // foreign key model
],
```

<a name="select_from_array"></a>
### select_from_array

Show a particular text depending on the value of the attribute.

```php
[ // select_from_array
    'name' => 'status',
    'label' => "Status",
    'type' => 'select_from_array',
    'options' => ['draft' => 'Draft (invisible)', 'published' => 'Published (visible)'],
],
```

<a name="select_multiple"></a>
### select_multiple

The select_multiple column will output a comma separated list of its connected entities. Used for relationships like hasMany() and belongsToMany(). Its name and definition is the same as the select_multiple field:
```php
[
   // n-n relationship (with pivot table)
   'label' => "Tags", // Table column heading
   'type' => "select_multiple",
   'name' => 'tags', // the method that defines the relationship in your Model
   'entity' => 'tags', // the method that defines the relationship in your Model
   'attribute' => "name", // foreign key attribute that is shown to user
   'model' => "App\Models\Tag", // foreign key model
],
```

<a name="table"></a>
### table


The ```table``` column will output a condensed table, when used on an attribute that stores a JSON array or object. It is meant to be used inside the show functionality (not list, though it also works there).

Its definition is very similar to the [table *field type*](/docs/{{version}}/crud-fields#table).

```php
[
    'name' => 'features', 
    'label' => 'Features', 
    'type' => 'table', 
    'columns' => [
        'name' => 'Name',
        'description' => 'Description',
        'price' => 'Price',
        'obs' => 'Observations'
    ]
],
```

<a name="upload_multiple"></a>
### upload_multiple


The ```table``` column will output a list of files and links, when used on an attribute that stores a JSON array of file paths. It is meant to be used inside the show functionality (not list, though it also works there), to preview files uploaded with the ```upload_multiple``` field type.

Its definition is very similar to the [upload_multiple *field type*](/docs/{{version}}/crud-fields#upload_multiple).

```php
[
    'name' => 'photos',
    'label' => 'Photos',
    'type' => 'upload_multiple',
    // 'disk' => 'public', // filesystem disk if you're using S3 or something custom
],
```

<a name="video"></a>
### video


Display a small screenshot for a Youtube or Vimeo video, stored in the database as JSON using the "video" field type.

```php
[
   'name' => 'name', // The db column name
   'label' => "Tag Name", // Table column heading
   'type' => 'video',
],
```

<a name="view"></a>
### view

Display any custom column type you want. Usually used by Backpack package developers, to use views from within their packages, instead of having to publish the views. 

```php
[
   'name' => 'name', // The db column name
   'label' => "Tag Name", // Table column heading
   'type' => 'view',
   'view' => 'package::columns.column_type_name', // or path to blade file
],
```

<a name="overwriting-default-column-types"></a>
## Overwriting Default Column Types

You can overwrite a column type by placing a file with the same name in your ```resources\views\vendor\backpack\crud\columns``` directory. When a file is there, Backpack will pick that one up, instead of the one in the package. You can do that from command line using ```php artisan backpack:crud:publish columns/column-file-name```

Examples:
- creating a ```resources\views\vendor\backpack\crud\columns\number.blade.php``` file would overwrite the ```number``` column functionality;
- ```php artisan backpack:crud:publish columns/text``` will take the view from the package and copy it to the directory above, so you can edit it;

>Keep in mind that when you're overwriting a default column type, you're forfeiting any future updates for that column. We can't push updates to a file that you're no longer using.

<a name="creating-a-custom-column-type"></a>
## Creating a Custom Column Type

Columns consist of only one file - a blade file with the same name as the column type (ex: ```text.blade.php```). You can create one by placing a new blad file inside ```resources\views\vendor\backpack\crud\columns```. Be careful to choose a distinctive name, otherwise you might be overwriting a default column type (see above).

For example, you can create a ```markdown.blade.php```:
```php
<span>{!! \Markdown::convertToHtml($entry->{$column['name']}) !!}</span>
```

The most useful variables you'll have in this file here are:
- ```$entry``` - the database entry you're showing (Eloquent object);
- ```$crud``` - the entire CrudPanel object, with settings, options and variables;

By default, custom columns are not searchable. In order to make your column searchable you need to [specify a custom ```searchLogic``` in your declaration](#custom-search-logic).


<a name="advanced-columns-use"></a>
## Advanced Columns Use

<a name="custom-search-logic"></a>
### Custom Search Logic for Columns

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

<a name="custom-order-logic"></a>
### Custom Order Logic for Columns

If your column points to something atypical (not a value that is stored as plain text in the database column, maybe a model function, or a JSON, or something else), you might find that the ordering doesn't work for that column. You can choose which columns are orderable, and how those columns actually get ordered, by using the column's ```orderLogic``` attribute.

For example, to order Articles not by its Category ID (as default, but by the Category Name), you can do:

```php
$this->crud->addColumn([  // Select
   'label' => "Category",
   'type' => 'select',
   'name' => 'category_id', // the db column for the foreign key
   'entity' => 'category', // the method that defines the relationship in your Model
   'attribute' => 'name', // foreign key attribute that is shown to user
   'orderable' => true,
   'orderLogic' => function ($query, $column, $columnDirection) {
        return $query->leftJoin('categories', 'categories.id', '=', 'articles.category_id')
            ->orderBy('categories.name', $columnDirection)->select('articles.*');
    }
]);
```

If you want a column to not be orderable at all, just pass ```'orderable' => false```

<a name="choose-where-columns-are-visible"></a>
### Choose Where Columns are Visible

Starting with Backpack\CRUD 3.5.0, you can choose to show/hide columns in different contexts. You can pass ```true``` / ```false``` to the column attributes below, and Backpack will know to show the column or not, in different contexts:

```php
$this->crud->addColumn([
   'name' => 'description',
   'visibleInTable' => false, // no point, since it's a large text
   'visibleInModal' => false, // would make the modal too big
   'visibleInExport' => false, // not important enough
   'visibleInShow' => true, // sure, why not
]);
```

This also allows you to do tricky things like:
- add a column that's hidden from the table view, but WILL get exported;
- adding a column that's hidden everywhere, but searchable (even with a custom ```searchLogic```);

<a name="multiple-columns-with-the-same-name"></a>
### Multiple Columns With the Same Name

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

<a name="define-which-columns-to-hide-in-responsive-table"></a>
### Define which columns to show or hide in the responsive table

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

>Note that responsive tables adopt special behavior if the table is not able to show all columns. This includes displaying a vertical ellipsis to the left of the row, and making the row clickable to reveal more detail. This behavior is automatic and is not manually controllable via a field property.
