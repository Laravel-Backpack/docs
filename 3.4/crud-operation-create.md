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


[block:api-header]
{
  "type": "basic",
  "title": "Extras (fake fields, stored as JSON in the database)"
}
[/block]
**Step 1.** You can define any number of fields as fake. Instead of being stored in the database table in their own column, they will be compacted and stored as a JSON array in the “extras” column in the database table. Optionally, you can choose what that column is called.

Basic definition example:
[block:code]
{
  "codes": [
    {
      "code": "<?php\n\n[\n    'name' => 'name', // JSON variable name\n    'label' => \"Tag Name\", // human-readable label for the input\n\n    'fake' => true, // show the field, but don’t store it in the database column above\n    'store_in' => 'extras' // [optional] the database column name where you want the fake fields to be stored as a JSON array \n],",
      "language": "php"
    }
  ]
}
[/block]
Example usage:
[block:code]
{
  "codes": [
    {
      "code": "<?php\n\n[\n    'name' => 'meta_title',\n    'label' => \"Meta Title\", \n    'fake' => true, \n    'store_in' => 'metas' // [optional]\n],\n[\n    'name' => 'meta_description',\n    'label' => \"Meta Description\", \n    'fake' => true, \n    'store_in' => 'metas' // [optional]\n],\n[\n    'name' => 'meta_keywords',\n    'label' => \"Meta Keywords\", \n    'fake' => true, \n    'store_in' => 'metas' // [optional]\n],",
      "language": "php"
    }
  ]
}
[/block]
In this example, these 3 fields will show up in the create & update forms, the CRUD will process as usual, but in the database these values won’t be stored in the “meta_title”, “meta_description” and “meta_keywords” columns. They will be stored in the “metas” column as a JSON array: 

```
{"meta_title":"title","meta_description":"desc","meta_keywords":"keywords"}
```
If “store_in” wasn’t defined, they would have been stored in the “extras” column. 

Step 2. (optional) If you’ve chosen anything else than “extras” to store the fake fields in, define the fakeColumns property on the model. It needs to be an array with all of the columns where you store fake fields. This will allow you to use Entity::find(1)->withFakes();
[block:callout]
{
  "type": "info",
  "title": "Note",
  "body": "Make sure “extras” or any other database column where you want to store fake fields is defined as “fillable” in the model. Otherwise your extra values will be ignored by the CRUD. Also, if you’re using any other column name for fake fields other than ‘extras’, you need to cast it as array in your model, so that your JSON results are always turned to PHP arrays."
}
[/block]

[block:callout]
{
  "type": "info",
  "title": "Translatable Fakes",
  "body": "If you want fakes to also be translatable, remember to also place ```extras``` in your model's ```$translatable``` property."
}
[/block]
Example model:
[block:code]
{
  "codes": [
    {
      "code": "<?php\n\nclass Tag extends Model {\n\n\tuse CrudTrait;\n\n\tprotected $table = 'tags';\n\tprotected $fakeColumns = ['extras', 'metas'];\n\tprotected $fillable = ['name', 'extras', 'metas'];\n  protected $casts = [\n        'metas' => 'array',\n       ];\n\n    public function articles()\n    {\n        return $this->hasMany('App\\Models\\Article', 'article_tag');\n    }\n\n}",
      "language": "php"
    }
  ]
}
[/block]

[block:api-header]
{
  "type": "basic",
  "title": "Translatable models and multi-language CRUDs"
}
[/block]

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/f67da47-a4fc862-9c920278-c922-11e6-9802-0938011d213c.png",
        "a4fc862-9c920278-c922-11e6-9802-0938011d213c.png",
        1033,
        670,
        "#f5f7f7"
      ]
    }
  ]
}
[/block]
You can let your admins edit multi-lingual entries. Only translations stored the [spatie/laravel-translatable](https://github.com/spatie/laravel-translatable) way are supported right now, but more options will be coming soon.

In order to make one of your Models translatable, you need to:
0. Be running MySQL 5.7+ (or a PosgreSQL with JSON column support);
1. [Install spatie/laravel-translatable](https://github.com/spatie/laravel-translatable#installation);
2. Make all the columns you will want to be translatable either JSON or TEXT;
3. Use Backpack's ```HasTranslations``` trait on your model (instead of using spatie's ```HasTranslations```) and define what fields are translatable, inside the ```$translatable``` property. For example:

```php
<?php

namespace App\Models;

use Backpack\CRUD\CrudTrait;
use Illuminate\Database\Eloquent\Model;
use Backpack\CRUD\ModelTraits\SpatieTranslatable\HasTranslations;

class Product extends Model
{
    use CrudTrait;
    use HasTranslations;

     /*
  |--------------------------------------------------------------------------
  | GLOBAL VARIABLES
  |--------------------------------------------------------------------------
  */

    protected $table = 'products';
    protected $primaryKey = 'id';
    protected $fillable = ['name', 'category_id', 'options', 'price', 'tags'];
    protected $translatable = ['name', 'options'];
```

Change the languages available to translate to/from, in your crud config file (```config/backpack/crud.php```). By default there are quite a few enabled (English, French, German, Italian, Romanian).

Additionally, if you have slugs, you'll need to use backpack's classes instead of the ones provided by cviebrock/eloquent-sluggable:

```php
<?php

namespace Backpack\NewsCRUD\app\Models;

use Illuminate\Database\Eloquent\Model;
use Backpack\CRUD\CrudTrait;
use Backpack\CRUD\ModelTraits\SpatieTranslatable\Sluggable;
use Backpack\CRUD\ModelTraits\SpatieTranslatable\SluggableScopeHelpers;
use Backpack\CRUD\ModelTraits\SpatieTranslatable\HasTranslations;

class Category extends Model
{
    use CrudTrait;
    use Sluggable, SluggableScopeHelpers;
    use HasTranslations;

    /*
    |--------------------------------------------------------------------------
    | GLOBAL VARIABLES
    |--------------------------------------------------------------------------
    */

    protected $table = 'categories';
    protected $primaryKey = 'id';
    // public $timestamps = false;
    // protected $guarded = ['id'];
    protected $fillable = ['name', 'parent_id'];
    // protected $hidden = [];
    // protected $dates = [];
    protected $translatable = ['name', 'slug'];

    /**
     * Return the sluggable configuration array for this model.
     *
     * @return array
     */
    public function sluggable()
    {
        return [
            'slug' => [
                'source' => 'slug_or_name',
            ],
        ];
    }
}
```
[block:api-header]
{
  "type": "basic",
  "title": "Tabbed Forms"
}
[/block]
You can now split your create/edit inputs into multiple tabs.
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/da959e4-Screen_Shot_2017-02-12_at_12.44.43.png",
        "Screen Shot 2017-02-12 at 12.44.43.png",
        741,
        568,
        "#db4c3c"
      ]
    }
  ]
}
[/block]
In order to use this feature, you just need to specify the tab name for each of your fields. Example:

```
        $this->crud->addField([ // select_from_array
            'name' => 'select_from_array',
            'label' => "Select from array",
            'type' => 'select_from_array',
            'options' => ['one' => 'One', 'two' => 'Two', 'three' => 'Three'],
            'allows_null' => false,
            'allows_multiple' => true,
            'tab' => 'Tab name here'
        ]);
```

If you forget to specify a tab name for a field, Backpack will place it in the last tab.
[block:api-header]
{
  "type": "basic",
  "title": "Inline Errors"
}
[/block]
In addition to the top errors (grouped), Backpack forms (create & edit) can now also show highlight each input that has errored. The feature comes enabled by default in Backpack\CRUD 3.2. You just can disable it in your ```crud.php``` config file. This should improve your admin's UX a lot when making mistakes.
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/9e59ec2-Screen_Shot_2017-02-13_at_10.17.37.png",
        "Screen Shot 2017-02-13 at 10.17.37.png",
        706,
        491,
        "#da4d3d"
      ]
    }
  ]
}
[/block]


[block:api-header]
{
  "type": "basic",
  "title": "Media Library (file manager)"
}
[/block]
The file manager used is [elFinder](http://elfinder.org/). It’s integrated in Backpack into:
- TinyMCE (as CRUD “tinymce” fieldtype)
- CKEditor (as CRUD “ckeditor” fieldtype)
- CRUD “browse” fieldtype
- standalone, at the *your-project/admin/elfinder* route (for the integration, barryvdh's [laravel-elfinder](https://github.com/barryvdh/laravel-elfinder) package is used)
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/DnLUQ1b5Q1KDjTa5lxou_68747470733a2f2f646c2e64726f70626f7875736572636f6e74656e742e636f6d2f752f323433313335322f6261636b7061636b5f637275645f656c66696e6465722e706e67.png",
        "68747470733a2f2f646c2e64726f70626f7875736572636f6e74656e742e636f6d2f752f323433313335322f6261636b7061636b5f637275645f656c66696e6465722e706e67.png",
        "1280",
        "612",
        "#645caa",
        ""
      ]
    }
  ]
}
[/block]

