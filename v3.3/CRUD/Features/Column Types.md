---
title: "Column Types"
excerpt: ""
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
   'type' => 'array_count'
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
  "title": "date"
}
[/block]
The date column will show a localized date in the default date format (as specified in the ```config/backpack/base.php``` file), whether the attribute is casted as date in the model or not:

```php
[
   'name' => "name", // The db column name
   'label' => "Tag Name", // Table column heading
   'type' => "date"
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
   'type' => "datetime"
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
   // 'limit' => 120, // character limit; default is 80;
],
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
   'attribute' => 'route'
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
- $entry - the database entry you're showing (Eloquent object);
- $crud - all the crudpanel settings, options and variables;

By default, custom columns are not searchable. In order to make your column searchable [specify a custom ```searchLogic``` in your declaration](https://laravel-backpack.readme.io/docs/advanced-features#section-custom-search-logic-in-table-view).