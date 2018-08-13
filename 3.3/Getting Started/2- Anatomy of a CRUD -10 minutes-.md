---
title: "2. Anatomy of a CRUD [10 minutes]"
excerpt: ""
---
Let’s bring back the example in our first lesson. The Tags CRUD:
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/d983b6a-Screen_Shot_2017-11-08_at_17.18.38.png",
        "Screen Shot 2017-11-08 at 17.18.38.png",
        2880,
        1592,
        "#f3f6f7"
      ]
    }
  ]
}
[/block]
In its simplest form, a CRUD will let you:
- **Create** entries, using a create form (aka “*add form*”)
- **Read** entries, using a table that uses AJAX DataTables (aka “*list view*”, aka “*table view*”)
- **Update** entries, using an update form (aka “*edit form*”)
- **Delete** entries, using a *button* in the *list view* 

Additionally, you’ll be able to reorder, preview, export, search or filter entries and other stuff, but let’s not get ahead of ourselves. Baby steps. 

Let’s go through the files a Backpack CRUD panel uses one more time:
- ```app\Models\Tag.php``` - the **model**
- ```app\Http\Requests\TagCrudRequest.php``` - the **request** file for validation rules
- ```app\Http\Controllers\Admin\TagCrudController.php``` - the **controller**, where you can customize how the CRUD looks and feels

We won’t be covering the **model** and **request** files here, as they are in no way custom. The only thing you need to make sure is that the Model is properly configured (db table, relationships, ```$fillable``` or ```$guarded``` properties, etc). What we will be covering is the TagCrudController file. Here’s a copy of a simple one you might use to achieve the above:
[block:code]
{
  "codes": [
    {
      "code": "<?php namespace App\\Http\\Controllers\\Admin;\n\nuse Backpack\\CRUD\\app\\Http\\Controllers\\CrudController;\n\n// VALIDATION: change the requests to match your own file names if you need form validation\nuse App\\Http\\Requests\\TagCrudRequest as StoreRequest;\nuse App\\Http\\Requests\\TagCrudRequest as UpdateRequest;\n\nclass TagCrudController extends CrudController {\n\n\tpublic function setup() \n  \t{\n       \t\t$this->crud->setModel(\"App\\Models\\Tag\");\n        \t$this->crud->setRoute(\"admin/tag\");\n        \t$this->crud->setEntityNameStrings('tag', 'tags');\n\n        \t$this->crud->setColumns(['name', 'slug']);\n        \t$this->crud->addField([\n\t\t\t'name' => 'name',\n\t\t\t'label' => \"Tag name\"\n\t\t]);\n    \t\t$this->crud->addField([\n\t\t\t'name' => 'slug',\n\t\t\t'label' => \"URL Segment (slug)\"\n\t\t]);\n  \t}\n\n\tpublic function store(StoreRequest $request)\n\t{\n\t\treturn parent::storeCrud();\n\t}\n\n\tpublic function update(UpdateRequest $request)\n\t{\n\t\treturn parent::updateCrud();\n\t}\n}",
      "language": "php",
      "name": "app\\Http\\Controllers\\Admin\\TagCrudController.php"
    }
  ]
}
[/block]
You should notice:
- That it uses basic inheritance, so if you want to modify a behaviour (save, update, reorder, etc), you can easily do that by overwriting a method in your TagCrudController;
- That the request file is typehinted in the store() and update() methods, so the information won’t even reach these methods if it doesn’t pass the validation you define in the Request file;
- That all the CRUD setup is done in the setup() method;

# Create/Update Forms
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/34e4f74-Screen_Shot_2017-11-09_at_09.24.00.png",
        "Screen Shot 2017-11-09 at 09.24.00.png",
        2880,
        1600,
        "#ebf3f3"
      ]
    }
  ]
}
[/block]
Backpack makes it *ridiculously* easy to customize how a create/update form looks and works. 
[block:callout]
{
  "type": "warning",
  "body": "After you’ve generated a CrudController, you’ll be using the ```$this->crud->setFromDb();``` method, which tries to intuitively figure out what fields you might need in your create/update forms and in your list view. While ```setFromDb()``` might be fine for simple CRUDs, it’s a long way from making CRUDs 100% automatic. So for anything but the simplest cases, I recommend you delete that and manually define the **fields** your Create/Update forms will have. We'll show how to do that below.",
  "title": "Manually is always better"
}
[/block]
Think of **fields** as your form’s inputs. A lot of times, you won't just need text inputs, you'll need datepickers, upload buttons, 1-n relationship, n-n relationships, textareas, etc. For each field, you only need to define it properly in the Controller's ```setup()``` method, using one of the API calls below. 

Here’s a quick look at the most useful commands in the CrudController API Cheat Sheet that help you manipulate fields:
[block:code]
{
  "codes": [
    {
      "code": "<?php\n\n// ------ FIELDS (the last parameter is always the form - either create, update or both)\n$this->crud->addField($field_definition_array, 'update/create/both');\n$this->crud->addField('db_column_name', 'update/create/both'); // a lazy way to add fields: let the CRUD decide what field type it is and set it automatically, along with the field label\n$this->crud->addFields($array_of_fields_definition_arrays, 'update/create/both');\n$this->crud->removeField('name', 'update/create/both');\n$this->crud->removeFields($array_of_names, 'update/create/both');",
      "language": "php",
      "name": "CRUD Field API Calls "
    }
  ]
}
[/block]
A typical *field definition array* will need at least three things:
- the name of the column in the database (and the name of the input, implicitly)
- the human-readable label for the input
- the type of the field

Pretty simple, right? Just by giving these commands a different ```$field_definition_array``` you get different input types, that *just work*. You can use [one of the 44+ field types we’ve provided](https://laravel-backpack.readme.io/v3.3/docs/crud-fields), or easily [create a custom field type](https://laravel-backpack.readme.io/v3.3/docs/crud-fields#section-custom-field-types) if you have some super-specific need that we haven’t covered yet. Take a few minutes and [browse the 44+ field types](https://laravel-backpack.readme.io/v3.3/docs/crud-fields#section-standard-field-types), to understand:
- how fields look
- how the definition array differs from one to another
- how many use cases we’ve covered already

Let's take another example, slightly more complicated than the ```text``` fields we used above. Something you'll find encounter all the time is relationship fields. So let's say the Tag model has an **n-n relationship** with an Article model:

```php
    public function articles()
    {
        return $this->belongsToMany('App\Models\Article', 'article_tag');
    }
```

We could use the code below to add a ```select2_multiple``` field to the Tag update forms:

```
$this->crud->addField([       // Select2Multiple = n-n relationship (with pivot table)
    'label' => "Articles",
    'type' => 'select2_multiple',
    'name' => 'articles', // the method that defines the relationship in your Model
    'entity' => 'articles', // the method that defines the relationship in your Model
    'attribute' => 'title', // foreign key attribute that is shown to user
    'model' => "App\Models\Article", // foreign key model
    'pivot' => true, // on create&update, do you need to add/delete pivot table entries?
], ‘update’);
```

**Note: **Because of the last parameter, the field will only be added on the Update form.

If we had an Articles CRUD, and the reverse relationship defined on the Article model too, we could also add a ```select2_multiple``` field in the Article CRUD, to allow the user to choose which tags apply to each article. This actually makes more sense than the above :-)

```
$this->crud->addField([       // Select2Multiple = n-n relationship (with pivot table)
    'label' => "Tags",
    'type' => 'select2_multiple',
    'name' => 'tags', // the method that defines the relationship in your Model
    'entity' => 'tags', // the method that defines the relationship in your Model
    'attribute' => 'name', // foreign key attribute that is shown to user
    'model' => "App\Models\Tag", // foreign key model
    'pivot' => true, // on create&update, do you need to add/delete pivot table entries?
]);
```

**Note: **Because the last parameter is missing, the field will be added to both Create and Update forms.

# The List View

That’s what we call the table which shows all entries. It’s generated for you automatically by ```setFromDb()```, but in real life scenarios you’ll most likely need to customize how it looks and feels, to make it more useful and intuitive, rename columns, reorder columns, add buttons, features, etc. 

## Columns

The most important feature you’ll need to use in the table view is **Columns**. *Which* columns show up in the table and *in which order*. **They’re defined in the same place as fields** (in the CrudControllers’s setup() method) **and their syntax is super-similar to fields**:
[block:code]
{
  "codes": [
    {
      "code": "<?php\n\n// ------ COLUMNS\n$this->crud->addColumn($column_definition_array); // add a single column, at the end of the stack\n$this->crud->addColumns($array_of_column_definition_arrays); // add multiple columns, at the end of the stack\n$this->crud->removeColumn('column_name'); // remove a column from the stack\n$this->crud->removeColumns(['column_name_1', 'column_name_2']); // remove an array of columns from the stack\n$this->crud->setColumnDetails('column_name', ['attribute' => 'value']);\n$this->crud->setColumnsDetails(['column_1', 'column_2'], ['attribute' => 'value']);\n\n$this->crud->setColumns($array_of_column_definition_arrays); // set the columns you want in the table view, either as array of column names, or multidimensional array with all columns detailed with their types",
      "language": "php",
      "name": "CRUD Column API Calls"
    }
  ]
}
[/block]
You can use one of the [14+ column types](https://laravel-backpack.readme.io/v3.3/docs/crud-columns-types) to show information to the user in the table, or easily [create a custom column type](https://laravel-backpack.readme.io/v3.3/docs/crud-columns-types#section-roll-your-own), if you have a super-specific need.

Using the methods and definition arrays above, you can do things like:

```php
$this->crud->addColumn('text'); // add a text column, at the end of the stack
$this->crud->addColumn([
            'name' => 'date',
            'type' => 'date',
            'label' => 'Date',
 ]);
```

## Filters
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/8be2b49-Screen_Shot_2017-11-07_at_18_16_54.png",
        "Screen_Shot_2017-11-07_at_18_16_54.png",
        1281,
        631,
        "#eef1f3"
      ]
    }
  ]
}
[/block]
Filters provide an easy way for the admin to well… filter the entries in the table. The syntax is very similar to the Fields and Columns and you can use one of the [existing 8 filter types](https://laravel-backpack.readme.io/v3.3/docs/filters) or easily [create a custom filter](https://laravel-backpack.readme.io/v3.3/docs/filters#section-creating-custom-filters). 
[block:code]
{
  "codes": [
    {
      "code": "<?php\n\n$this->crud->addFilter($options, $values, $filter_logic);\n$this->crud->removeFilter($name);\n$this->crud->removeAllFilters();",
      "language": "php",
      "name": "CRUD Filters API Calls"
    }
  ]
}
[/block]
## Buttons
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/761b1c5-Screen_Shot_2017-11-07_at_18_16_54.png",
        "Screen_Shot_2017-11-07_at_18_16_54.png",
        1281,
        631,
        "#eef1f3"
      ]
    }
  ]
}
[/block]
If you want to add a custom button to an entry, you can do that. If you want to remove a button, you can also do that. Look for the [buttons documentation](https://laravel-backpack.readme.io/v3.3/docs/crud-buttons) when you need it.  
[block:code]
{
  "codes": [
    {
      "code": "<?php\n\n// possible positions: 'beginning' and 'end'; defaults to 'beginning' for the 'line' stack, 'end' for the others;\n\n// add a button; possible types are: view, model_function\n$this->crud->addButton($stack, $name, $type, $content, $position);\n// add a button whose HTML is returned by a method in the CRUD model\n$this->crud->addButtonFromModelFunction($stack, $name, $model_function_name, $position);\n// add a button whose HTML is in a view placed at resources\\views\\vendor\\backpack\\crud\\buttons\n$this->crud->addButtonFromView($stack, $name, $view, $position);\n\n$this->crud->removeButton($name);\n$this->crud->removeButtonFromStack($name, $stack);",
      "language": "php",
      "name": "CRUD Buttons API Calls"
    }
  ]
}
[/block]
## Callbacks
Developers coming from GroceryCRUD on CodeIgniter or other CRUD systems will be looking for callbacks to run ```before_insert```, ```before_update```, ```after_insert```, ```after_update```.

**There are no callbacks in Backpack**, because there's no need. The code for the create & update operations is out in the open for you to customize. Notice your EntityCrudController already has the following methods, which you can modify as you wish: 
- you can copy-paste the logic in the package and modify it to fit your use case 
- OR 
- you can do things before or after the logic is called
[block:code]
{
  "codes": [
    {
      "code": "<?php\n\npublic function store(StoreRequest $request)\n    {\n        // <---------  here is where a before_insert callback logic would be\n        $response = parent::storeCrud();\n        // <---------  here is where a after_insert callback logic would be\n        return $response;\n    }\n\npublic function update(UpdateRequest $request)\n    {\n        // <---------  here is where a before_update callback logic would be\n        $response = parent::updateCrud();\n        // <---------  here is where a after_update callback logic would be\n         return $response;\n    }",
      "language": "php",
      "name": "part of any CrudController"
    }
  ]
}
[/block]
But before you do that, ask yourself - *is this something that should be done when an entry is added/updated/deleted from the application, too*? Not just the CRUD admin? If so, a better place for it would be the Model. Backpack uses Eloquent all the way, so the cleanest way might be to use [Eloquent Event Observers](https://laravel.com/docs/5.5/eloquent#events).

**That’s it for today!** Thanks for sticking with me this long. This has been the last important and long lesson (10 min). You can go ahead and [install Backpack](https://laravel-backpack.readme.io/v3.3/docs/install-on-laravel-55) now, as you’ve already gone through the most important features. Or read the next lesson, about advanced features.