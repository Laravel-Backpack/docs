---
title: "Upgrading CRUD 2.x to 3.0"
excerpt: ""
---
Starting with July 22nd 2016, **Backpack\CRUD has hit 3.0**. That's because **the syntax has changed a bit** and most projects are configured to pull ^2.0. This was the only way not to influence anybody's work. The changes are nice and big. But not *that* big.

Here's what's new:
[block:api-header]
{
  "type": "basic",
  "title": "Field types - BIG CHANGES"
}
[/block]
- **extra html attributes are now placed in their own array** (```attributes```);
- you can set a **default value** for most fields;
- you can set a **hint** for most fields;
- **you can make the fields a different size**, by changing the class on its wrapper (```wrapperAttributes```)
 
Here's what a bunch of ```form-group col-md-6``` fields look like together:
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/159e783-85bcd5d5-26fe-41dc-b9ac-36f4f4d7068f.png",
        "85bcd5d5-26fe-41dc-b9ac-36f4f4d7068f.png",
        979,
        727,
        "#e9f0f0"
      ]
    }
  ]
}
[/block]
Here's the syntax for adding a field, and the differences:
[block:code]
{
  "codes": [
    {
      "code": "<?php\n\n$this->crud->addField([\n  \t// ---------\n    // MANDATORY  <--- NO CHANGES\n  \t// ---------\n    'name'  => 'address',\n    'label' => 'Street address',\n    'type'  => 'text'\n    \n  \t// ---------\n    // OPTIONAL \t<--- CHANGES ARE HERE\n  \t// ---------\n  \n  \t// default value  [!!! NEW !!!]\n    'default'    => 'some value', \n  \n  \t// hint that shows up after input [!!! NEW !!!]\n    'hint'       => 'Some hint text', \n  \n  \t// extra HTML attributes your input might need [!!! CHANGED !!!]\n    'attributes' => [\n       'placeholder' => 'Some text when empty',\n       'disabled' => 'disabled',\n       'class' => 'form-control some-class'\n     ], \n  \n  \t// extra HTML attributes for the field wrapper [!!! NEW !!!]\n     'wrapperAttributes' => [\n       'class' => 'form-group col-md-12'\n     ] \n]);",
      "language": "php"
    }
  ]
}
[/block]
Don't worry, most of your fields definitions will *not* be that long. But you *can* do that, if you want.

Details in the [CRUD > Field Types](https://laravel-backpack.readme.io/docs/crud-fields) page.
[block:api-header]
{
  "type": "basic",
  "title": "Column types - OVERHAUL"
}
[/block]
Columns now work the same way fields do: **each column type has it own blade file, and you can easily extend them or overwrite them by creating blade files in the ```columns``` folder**. 

Backpack\CRUD now ships with these column types: 
- check 
- date 
- datetime
- model_function
- model_function_attribute
- select
- select_multiple
- text

Details in the [CRUD > Column Types](https://laravel-backpack.readme.io/docs/crud-columns-types) page.
[block:api-header]
{
  "type": "basic",
  "title": "Buttons - OVERHAUL"
}
[/block]
- Buttons now work the same way fields and columns do: **each button type has it own blade file, and you can easily extend add new buttons to your CRUDs**. 
- Also, you can add new buttons from model functions. 
- There are also three places where you can add buttons, called stacks: top, line and bottom.

Details in the [CRUD > Buttons](https://laravel-backpack.readme.io/docs/crud-buttons) page.
[block:api-header]
{
  "type": "basic",
  "title": "Others"
}
[/block]
- Backpack\Base now **includes the wonderful [jenssegers/date](https://github.com/jenssegers/date) package**, which is an extension of Carbon that helps localise dates; you have a config option in ```config/backpack/base.php``` for the default date and datetime format;
- A ```config/backpack/crud.php``` has appeared, which currently only has one option, that helps you **customise how many rows the CRUD tables should show by default**. [You can overwrite](https://laravel-backpack.readme.io/docs/crud-full-api#usage-level-3-sometimes) that in each CRUD using ```$this->crud->setDefaultPageLength(10);```;  
- **Fields and columns now support [attribute casting](https://laravel.com/docs/5.2/eloquent-mutators#attribute-casting)** for everything but ```object``` and ```collection```; since those can be so different, developers should roll their own; but that does mean that we support ```integer```, ```real```, ```float```, ```double```, ```string```, ```boolean```, ```array```, ```date``` and ```datetime```. 
- Simplified the installation process a bit, by registering the required service providers automatically;
- Removed deprecated code;
- Bugfixes;
[block:api-header]
{
  "type": "basic",
  "title": "What could break if I update?"
}
[/block]
1. If you've been using the old syntax for fields to place classes or other custom attributes on the them, **those attributes will no longer be placed on the fields if you update**. You have to place all of them inside the 'attributes' array now. 
2. **If you've customised the "list.blade.php" file** (table view), you won't benefit from the Column Types and Buttons updates. Your CRUD will probably still work, but without those updates it doesn't make a lot of sense to update from 2.x. 
3. If you've created custom fields, they will probably still work, but you need to check that.
[block:api-header]
{
  "type": "basic",
  "title": "How to update?"
}
[/block]
1. In your composer.json, change your required version from ```"backpack/crud": "^2.0",``` to ```"backpack/crud": "^3.0.0",```
2. Then run ```composer update```.
[block:api-header]
{
  "type": "basic",
  "title": "Feedback or problems?"
}
[/block]
Open a Github issue on the [appropriate repository](https://github.com/laravel-backpack).