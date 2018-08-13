---
title: "Basic Features"
excerpt: ""
---
In a basic CRUD, you will only need to:
- specify what fields the create/update form should show;
- specify what columns the table view should show;
[block:api-header]
{
  "type": "basic",
  "title": "Fields"
}
[/block]
Fields are defined in your ```EntityCrudController::setup()``` method and determine the way that particular field looks in the add / edit view. 

Think of the fields as ```<input type=”text” />``` but that does everything for you - triggers a datepicker, an upload button, validation, a 1-n relationship, an n-n relationship, etc.

## Existing field types
For each of them, you only need to define it properly in the Controller's ```setup()``` method. Let's take an example field type, for a text input. You only need to define name, label and type, all field types also have the optional attributes below:
[block:code]
{
  "codes": [
    {
      "code": "<?php\n\n$this->crud->addField([\n     // MANDATORY\n    'name'  => 'address', // DB column name (will also be the name of the input)\n    'label' => 'Street address', // the human-readable label for the input\n    'type'  => 'text', // the field type (text, number, select, checkbox, etc)\n    \n     // OPTIONAL + example values\n    'default'    => 'some value', // default value\n    'hint'       => 'Some hint text', // helpful text, show up after input\n    'attributes' => [\n       'placeholder' => 'Some text when empty',\n       'class' => 'form-control some-class'\n     ], // extra HTML attributes and values your input might need\n     'wrapperAttributes' => [\n       'class' => 'form-group col-md-12'\n     ] // extra HTML attributes for the field wrapper - mostly for resizing fields using the bootstrap column classes\n]);",
      "language": "php"
    }
  ]
}
[/block]
**In addition:**
If you don't specify the label, the CRUD will try to guess one for you, based on the name.
If you don't specify a field type, the CRUD will try to guess one for you, based on the name.

Check out the existing 44+ field types in the [CRUD > Features > Fields section](https://laravel-backpack.readme.io/docs/crud-fields).

Each field type has its own blade file, which can be customized by placing a file with the same name in your ```resources\views\vendor\backpack\crud\fields``` folder. To publish a view from the package to your resources folder use the ```backpack:crud:publish``` command. For example ```php artisan backpack:crud:publish fields/text``` will take the view from the package and copy it to the directory above, so you can edit it. Backpack will the use _your_ view, instead of the one in the package. Please note once you publish a view you won't get any updates we push to that view, so don't overdo this.

## Custom field types (roll your own)

If you can't find the functionality you need in the existing field types, you can [very very easily roll your own](https://laravel-backpack.readme.io/docs/crud-fields#section-custom-field-types).
[block:api-header]
{
  "type": "basic",
  "title": "Columns"
}
[/block]
Columns represent the way your information is shown in the table view. 

All column types must have their name, label and type specified, but some could require some other attributes too.
[block:code]
{
  "codes": [
    {
      "code": "<?php\n\n$this->crud->addColumn([\n         'name' => 'name', // The db column name\n         'label' => \"Tag Name\", // Table column heading\n  \t\t\t 'type' => 'Text'\n         ]);",
      "language": "php"
    }
  ]
}
[/block]
Check out the 12+ column types in the [CRUD > Features > Column Types](https://laravel-backpack.readme.io/docs/crud-column-types) section.

Each column type has its own blade file, which can be customized by placing a file with the same name in your ```resources\views\vendor\backpack\crud\columns```. To publish a view from the package to your resources folder use the ```backpack:crud:publish``` command. For example ```php artisan backpack:crud:publish columns/text``` will take the view from the package and copy it to the directory above, so you can edit it. Backpack will the use _your_ view, instead of the one in the package. Please note once you publish a view you won't get any updates we push to that view, so don't overdo this.

If you can't find the functionality you need in the existing column types, you can [very very easily roll your own](https://laravel-backpack.readme.io/v3.4/docs/crud-columns-types#section-roll-your-own).
[block:api-header]
{
  "type": "basic",
  "title": "Callbacks"
}
[/block]
Developers coming from GroceryCRUD or other CRUD systems will be looking for callbacks to run before_insert, before_update, after_insert, after_update. 

There are no callbacks in Backpack, because there's no need. The code for the insert&update operations is out in the open for you to customize. Notice your ```EntityCrudController``` **already** has the following methods, which you can modify as you wish:
- you can copy-paste the logic in the package and modify it to fit your use case;
or
- you can do things before or after the logic is called;
[block:code]
{
  "codes": [
    {
      "code": "\t\tpublic function store(StoreRequest $request)\n    {\n        // <---------  here is where a before_insert callback logic would be\n        $response = parent::storeCrud();\n        // <---------  here is where a after_insert callback logic would be\n        return $response;\n    }\n\n    public function update(UpdateRequest $request)\n    {\n        // <---------  here is where a before_update callback logic would be\n        $response = parent::updateCrud();\n        // <---------  here is where a after_update callback logic would be\n         return $response;\n    }",
      "language": "php"
    }
  ]
}
[/block]
Please see this [github issue](https://github.com/Laravel-Backpack/CRUD/issues/51) for a rephrased explanation.