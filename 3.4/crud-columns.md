# Columns

---

[block:api-header]
{
  "type": "basic",
  "title": "array"
}
[/block]
Enumerate an array stored in the db column as JSON.
```php
[
   'name' => 'options', // The db column name
   'label' => "Options", // Table column heading
   'type' => 'array'
],
```
[block:api-header]
{
  "type": "basic",
  "title": "array_count"
}
[/block]
Count the items in an array stored in the db column as JSON.

```php
[
   'name' => 'options', // The db column name
   'label' => "Options", // Table column heading
   'type' => 'array_count',
   // 'suffix' => 'options', // if you want it to show "2 options" instead of "2 items"
],
```
[block:api-header]
{
  "type": "basic",
  "title": "boolean"
}
[/block]
Show Yes/No (or custom text) instead of 1/0.

```php
[
    'name' => 'name',
    'label' => 'Status',
    'type' => 'boolean',
    // optionally override the Yes/No texts
    // 'options' => [0 => 'Active', 1 => 'Inactive']
]
```
[block:api-header]
{
  "type": "basic",
  "title": "check"
}
[/block]
Show a favicon with a checked or unchecked box, depending on the given boolean.
```php
[
   'name' => 'featured', // The db column name
   'label' => "Featured", // Table column heading
   'type' => 'check'
],
```
[block:api-header]
{
  "title": "closure"
}
[/block]
Show custom HTML based on a closure you specify in your EntityCrudController. Please note this column does not escape HTML before rendering. You need to do that yourself, if you consider it necessary.

```php
$this->crud->addColumn([
    'name' => 'created_at',
    'label' => 'Created At',
    'type' => 'closure',
    'function' => function($entry) {
        return 'Created on '.$entry->created_at;
    }
]);
```
[block:api-header]
{
  "title": "date"
}
[/block]
The date column will show a localized date in the default date format (as specified in the ```config/backpack/base.php``` file), whether the attribute is casted as date in the model or not:

```php
[
   'name' => "name", // The db column name
   'label' => "Tag Name", // Table column heading
   'type' => "date",
    // 'format' => 'l j F Y', // use something else than the base.default_date_format config value
],
```
[block:api-header]
{
  "title": "datetime"
}
[/block]
The date column will show a localized datetime in the default datetime format (as specified in the ```config/backpack/base.php``` file), whether the attribute is casted as datetime in the model or not:

```php
[
   'name' => "name", // The db column name
   'label' => "Tag Name", // Table column heading
   'type' => "datetime",
    // 'format' => 'l j F Y H:i:s', // use something else than the base.default_datetime_format config value
],
```
[block:api-header]
{
  "title": "image"
}
[/block]
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
]
```
[block:api-header]
{
  "type": "basic",
  "title": "multidimensional_array"
}
[/block]
Enumerate the values in a multidimensional array, stored in the db as JSON.

```php
[
   'name' => 'options', // The db column name
   'label' => "Options", // Table column heading
   'type' => 'multidimensional_array',
   'visible_key' => 'name' // The key to the attribute you would like shown in the enumeration
],
```
[block:api-header]
{
  "title": "number"
}
[/block]
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
[block:api-header]
{
  "type": "basic",
  "title": "radio"
}
[/block]
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
]
```

This example will show: 
- "Draft" when the value stored in the db is 0;
- "Published" when the value stored in the db is 1;  
[block:api-header]
{
  "title": "row_number"
}
[/block]
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
[block:api-header]
{
  "type": "basic",
  "title": "text"
}
[/block]
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

**Advanced use case:** The ```text``` column type can also show the attribute of a 1-1 relationship. If you have a relationship (like ```parent()```) set up in your Model, you can use relationship and attibute in the ```name```, using dot notation:
```php
$this->crud->addColumn([
    'name' => 'parent.title',
    'label' => 'Title',
    'type' => 'text'
]);
```
[block:api-header]
{
  "type": "basic",
  "title": "select"
}
[/block]
The select column will output its connected entity. Its name and definition is the same as for the select *field type*:
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

**Note:** When displaying this column's value, the text is not escaped. That is intentional. This way, you can use it to show labels, color text, italic, bold, links, etc. If you might have malicious JS or CSS in your values, you can create a new escaped field yourself. But it's probably better to treat the problem at the source, and prevent that JS and CSS from reaching your DB in the first place.
[block:api-header]
{
  "title": "select_from_array"
}
[/block]
Show a particular text depending on the value of the attribute.

```php
[ // select_from_array
    'name' => 'status',
    'label' => "Status",
    'type' => 'select_from_array',
    'options' => [‘draft’ => ‘Draft (invisible)’, ‘published’ => ‘Published (visible)’],
]
```
[block:api-header]
{
  "type": "basic",
  "title": "select_multiple"
}
[/block]
The select_multiple column will output a comma separated list of its connected entities. Its name and definition is the same as the select_multiple field:
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

**Note:** When displaying this column's value, the text is not escaped. That is intentional. This way, you can use it to show labels, color text, italic, bold, links, etc. If you might have malicious JS or CSS in your values, you can create a new escaped field yourself. But it's probably better to treat the problem at the source, and prevent that JS and CSS from reaching your DB in the first place.
[block:api-header]
{
  "title": "table"
}
[/block]
The ```table``` column will output a condensed table, when used on an attribute that stores a JSON array or object. It is meant to be used inside the show functionality (not list, though it also works there).

Its definition is very similar to the [table *field type*](https://laravel-backpack.readme.io/docs/crud-fields#section-table).

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
[block:api-header]
{
  "title": "upload_multiple"
}
[/block]
The ```table``` column will output a list of files and links, when used on an attribute that stores a JSON array of file paths. It is meant to be used inside the show functionality (not list, though it also works there), to preview files uploaded with the ```upload_multiple``` field type.

Its definition is very similar to the [upload_multiple *field type*](https://laravel-backpack.readme.io/docs/crud-fields#section-upload_multiple).

```php
[
    'name' => 'photos',
    'label' => 'Photos',
    'type' => 'upload_multiple',
    // 'disk' => 'public', // filesystem disk if you're using S3 or something custom
],
```
[block:api-header]
{
  "type": "basic",
  "title": "video"
}
[/block]
Display a small screenshot for a Youtube or Vimeo video, stored in the database as JSON using the "video" field type.

```php
[
   'name' => 'name', // The db column name
   'label' => "Tag Name", // Table column heading
   'type' => 'video',
],
```
[block:api-header]
{
  "title": "view"
}
[/block]
Display any custom column type you want. Usually used by Backpack package developers, to use views from within their packages, instead of having to publish the views. 

```php
[
   'name' => 'name', // The db column name
   'label' => "Tag Name", // Table column heading
   'type' => 'view',
   'view' => 'package::columns.column_type_name', // or path to blade file
],
```
[block:api-header]
{
  "type": "basic",
  "title": "model_function"
}
[/block]
The model_function column will output a function on your main model. Its definition is:
```php
[
   // run a function on the CRUD model and show its return value
   'name' => "url",
   'label' => "URL", // Table column heading
   'type' => "model_function",
   'function_name' => 'getSlugWithLink', // the method in your Model
   // 'limit' => 100, // Limit the number of characters shown
],
```
For this example, if your model would feature this method, it would return the link to that entity:
```php
public function getSlugWithLink() {
        return '<a href="'.url($this->slug).'" target="_blank">'.$this->slug.'</a>';
    }
```

**Note:** When displaying this column's value, the text is not escaped. That is intentional. This way, you can use it to show labels, color text, italic, bold, links, etc. If you might have malicious JS or CSS in your values, you can create a new escaped field yourself. But it's probably better to treat the problem at the source, and prevent that JS and CSS from reaching your DB in the first place.
[block:api-header]
{
  "type": "basic",
  "title": "model_function_attribute"
}
[/block]
If the function you're trying to use returns an object, not a string, you can use the model_function_attribute column, which will output the attribute on the function result. Its definition is:
```php
[
   'name' => "url",
   'label' => "URL", // Table column heading
   'type' => "model_function_attribute",
   'function_name' => 'getSlugWithLink', // the method in your Model
   'attribute' => 'route',
   // 'limit' => 100, // Limit the number of characters shown
],
```

**Note:** When displaying this column's value, the text is not escaped. That is intentional. This way, you can use it to show labels, color text, italic, bold, links, etc. If you might have malicious JS or CSS in your values, you can create a new escaped field yourself. But it's probably better to treat the problem at the source, and prevent that JS and CSS from reaching your DB in the first place.
[block:api-header]
{
  "type": "basic",
  "title": "ROLL YOUR OWN"
}
[/block]
Since CRUD v2.1, columns are defined the same way fields are: each column type has its own blade file, which can be overwritten by placing a file with the same name in your ```resources\views\vendor\backpack\crud\columns```.

For example, here's how the ```text.blade.php``` file looks like:
```php
{{-- regular object attribute --}}
<td>{{ str_limit(strip_tags($entry->{$column['name']}), 80, "[...]") }}</td>
```

The most useful variables you'll have in this file here are:
- ```$entry``` - the database entry you're showing (Eloquent object);
- ```$crud``` - all the crudpanel settings, options and variables;

By default, custom columns are not searchable. In order to make your column searchable [specify a custom ```searchLogic``` in your declaration](https://laravel-backpack.readme.io/docs/advanced-features#section-custom-search-logic-in-table-view).