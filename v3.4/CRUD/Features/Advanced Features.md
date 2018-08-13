---
title: "Advanced Features"
excerpt: ""
---
In addition the usual CRUD functionality, Backpack also allows you to do a few more complicated things:
[block:api-header]
{
  "type": "basic",
  "title": "Reordering and nesting items"
}
[/block]
If your model has the proper fields (parent_id, lft, rgt, depth), you can use the built-in reorder functionality. All you need to do is enable it in the EntityCrudController's constructor:

```php
$this->crud->enableReorder('label_name', MAX_TREE_LEVEL);
$this->crud->allowAccess('reorder');
```
*Note: MAX_TREE_LEVEL is the maximum number of nesting elements from parents. For infinit levels, you should set it to 0

For the enableReorder function, the parameters are the db column you need to show on the elements and the number of levels you will allow nesting to (specify 1 for no nesting, just reordering).

This will be your result:
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/ivETb740RuGmsG7ZLA42_Screen%20Shot%202016-05-22%20at%2017.33.04.png",
        "Screen Shot 2016-05-22 at 17.33.04.png",
        "1280",
        "767",
        "#08abad",
        ""
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

[block:api-header]
{
  "type": "basic",
  "title": "Details Row"
}
[/block]
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
[block:api-header]
{
  "type": "basic",
  "title": "Revisions"
}
[/block]
Revisions allows you to store all entry's changes in a table and revert to a specific value, if you'd like. It can work as a backup system and an accountability system for the admins.

When enabled, it will show another button in the table view, between Edit and Delete. On click, that button opens another page which will allow an admin to see all changes and who made them. The revisions page will look like this:
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/0809eae-Screen_Shot_2016-09-19_at_18.20.53.png",
        "Screen Shot 2016-09-19 at 18.20.53.png",
        1280,
        550,
        "#ebf2f2"
      ]
    }
  ]
}
[/block]

In order to enable this functionality for a crud panel, you need to:

1. Create the revisions table:

```bash
cp vendor/venturecraft/revisionable/src/migrations/2013_04_09_062329_create_revisions_table.php database/migrations/ && php artisan migrate
```

2. Use [venturecraft/revisionable](https://github.com/VentureCraft/revisionable#implementation) on your model. If you are using another bootable trait be sure to override the boot method in your model.

```php
namespace MyApp\Models;

class Article extends Eloquent {
    use \Backpack\CRUD\CrudTrait, \Venturecraft\Revisionable\RevisionableTrait;

    // If you are using another bootable trait the be sure to override the boot method in your model
    public static function boot()
    {
        parent::boot();
    }
}
```

3. In your EntityCrudController, enable access to the revisions: 

```php
$this->crud->allowAccess('revisions');
```

4. [optional] If you want the table view to load faster (and why wouldn't you?), you should eager-load the revisions, so there won't be any extra DB queries for the revisions:
```php
$this->crud->with('revisionHistory');
```

For complex usage, head on over to [VentureCraft/revisionable](https://github.com/VentureCraft/revisionable) to see the full documentation and extra configuration options.
[block:api-header]
{
  "type": "basic",
  "title": "Export buttons"
}
[/block]
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
  "title": "Column and field order"
}
[/block]
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
  "title": "Customize Views for each CRUD Panel"
}
[/block]
Previously, if you wanted to create a custom ```create.blade.php``` view for just one CRUD Panel, you needed to overwrite the create() method. The same for edit, list, revisions, reorder, etc.

Now you can specify custom views in your ```setup()``` method:
```
$this->crud->setShowView('your-view');
$this->crud->setEditView('your-view');
$this->crud->setCreateView('your-view');
$this->crud->setListView('your-view');
$this->crud->setReorderView('your-view');
$this->crud->setRevisionsView('your-view');
$this->crud->setRevisionsTimelineView('your-view');
$this->crud->setDetailsRowView('your-view');
```
[block:api-header]
{
  "type": "basic",
  "title": "Custom CSS and JS for CRUD operations"
}
[/block]
Starting with Backpack\CRUD 3.2, there's a place where you can place your custom operations javascript and styles. You'll find a CSS and JS file in your ```public/vendor/backpack/crud``` folder for each Backpack operation. You can place any custom operation code there, without bloating general files css, js or blade files.
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/872b2f8-Screen_Shot_2017-02-13_at_10.54.38.png",
        "Screen Shot 2017-02-13 at 10.54.38.png",
        220,
        641,
        "#e3e3e3"
      ]
    }
  ]
}
[/block]

[block:api-header]
{
  "type": "basic",
  "title": "Extra CRUD Routes"
}
[/block]
Starting with Backpack\CRUD 3.2, you can use the ```with()``` method on ```CRUD::resource``` to better organize your routes. Something like this:

```php
CRUD::resource(‘teams’, ‘Admin\TeamCrudController’)->with(function(){
    // add extra routes to this resource
    Route::get('teams/ajax-name-options', 'Admin\TeamCrudController@nameOptions');
    Route::get('teams/ajax-category-options', 'Admin\TeamCrudController@categoryOptions');
});
```
[block:api-header]
{
  "title": "Custom Search Logic in Table View"
}
[/block]
Starting with Backpack\CRUD 3.3 (Nov 2017), all tables are AjaxDataTables. As such, the search field in your tables will NOT search within the table, but will do an AJAX call. You can choose which columns are searchable, and what those columns _actually_ search, by using the column's ```searchLogic``` property:


[block:code]
{
  "codes": [
    {
      "code": "<?php\n\n// column with custom search logic\n$this->crud->addColumn([\n    'name' => 'slug_or_title',\n    'label' => 'Title',\n    'searchLogic' => function ($query, $column, $searchTerm) {\n        $query->orWhere('title', 'like', '%'.$searchTerm.'%');\n    }\n]);\n\n\n// 1-n relationship column with custom search logic\n$this->crud->addColumn([\n    'label' => \"Cruise Ship\",\n    'type' => \"select\",\n    'name' => 'cruise_ship_id',\n    'entity' => 'cruise_ship',\n    'attribute' => \"cruise_ship_name_date\", // combined name & date column\n    'model' => \"App\\Models\\CruiseShip\",\n\t\t'searchLogic' => function ($query, $column, $searchTerm) {\n    \t\t$query->orWhereHas('cruise_ship', function ($q) use ($column, $searchTerm) {\n        \t\t$q->where('name', 'like', '%'.$searchTerm.'%')\n          \t\t->orWhereDate('depart_at', '=', date($searchTerm));\n    \t\t});\n\t\t}\n]);\n\n\n// column that doesn't need to be searchable\n$this->crud->addColumn([\n    'name' => 'slug_or_title',\n    'label' => 'Title',\n    'searchLogic' => false\n]);\n\n// column whose search logic should behave like it were a 'text' column type\n$this->crud->addColumn([\n    'name' => 'slug_or_title',\n    'label' => 'Title',\n    'searchLogic' => 'text'\n]);",
      "language": "php"
    }
  ]
}
[/block]

[block:api-header]
{
  "title": "Multiple Columns With the Same Name"
}
[/block]
Starting with Backpack\CRUD 3.3 (Nov 2017), you can have multiple columns with the same name, by specifying a unique ```key``` property. So if you want to use the same column name twice, you can do that. Notice below we have the same name for both columns, but one of them has a ```key```. This additional key will be used as an array key, if provided.
[block:code]
{
  "codes": [
    {
      "code": "<?php\n\n// column that shows the parent's first name\n$this->crud->addColumn([\n   'label' => \"Parent First Name\", // Table column heading\n   'type' => \"select\",\n   'name' => 'parent_id', // the column that contains the ID of that connected entity;\n   'entity' => 'parent', // the method that defines the relationship in your Model\n   'attribute' => \"first_name\", // foreign key attribute that is shown to user\n   'model' => \"App\\Models\\User\", // foreign key model\n]);\n\n// column that shows the parent's last name\n$this->crud->addColumn([\n   'label' => \"Parent Last Name\", // Table column heading\n   'type' => \"select\",\n   'name' => 'parent_id', // the column that contains the ID of that connected entity;\n   'key' => 'parent_last_name', // the column that contains the ID of that connected entity;\n   'entity' => 'parent', // the method that defines the relationship in your Model\n   'attribute' => \"last_name\", // foreign key attribute that is shown to user\n   'model' => \"App\\Models\\User\", // foreign key model\n]);",
      "language": "php"
    }
  ]
}
[/block]

[block:api-header]
{
  "title": "Define which columns to show or hide in the responsive table"
}
[/block]
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
[block:api-header]
{
  "title": "Publishing a column / field / filter / button and modifying it"
}
[/block]
All Backpack packages allow you to easily overwrite and customize the views. If you want Backpack to pick up _your_ file, instead of the one in the package, you can do that by just placing a file with the same name in your views. So if you want to overwrite the select2 field (```vendor/backpack/crud/src/resources/views/fields/select2.blade.php```) to change some functionality, you need to create ```resources/views/vendor/backpack/crud/fields/select2.blade.php```. You can do that manually, or use this command:
```shell
php artisan backpack:crud:publish fields/select2
```
This will look for the blade file and copy it inside the folder, so you can edit it.

**Please note:** Once you place a file with the same name inside your project, Backpack will pick that one up instead of the one in the package. That means that even though the file in the package is updated, you won't be getting those updates, since you're not using that file. Blade modifications are almost never breaking changes, but it's a good thing to receive updates with zero effort. Even small ones. So please overwrite the files as little as possible. Best to create your own custom fields/column/filters/buttons, whenever you can.
[block:api-header]
{
  "title": "Preview / show an entry"
}
[/block]
If you want your entries to show a "Preview" button in the table, you can enable the "Show" operation, which does just that.

1. In your ```EntityCrudController::setup()``` method specify ```$this->crud->allowAccess('show');``` - this will make a Preview button appear in the table view, and allow access to the show view.

2. By default, the "show" functionality uses the same column types you've defined in ```setup()``` and adds all other fillable attributes. In case you need to add/change/remove any columns, create a ```show()``` method in the controller. Using the ```addColumn()``` you're already familiar with you'll be replacing any columns automagically added (since they'll have the same name):

Example customization of ```show()``` method:

```php
public function show($id)
    {
        $content = parent::show($id);

        $this->crud->addColumn([
            'name' => 'table',
            'label' => 'Table',
            'type' => 'table',
            'columns' => [
                'name'  => 'Name',
                'desc'  => 'Description',
                'price' => 'Price',
            ]
        ]);
        $this->crud->addColumn([
            'name' => 'fake_table',
            'label' => 'Fake Table',
            'type' => 'table',
            'columns' => [
                'name'  => 'Name',
                'desc'  => 'Description',
                'price' => 'Price',
            ],
        ]);
        $this->crud->addColumn('text');
        $this->crud->removeColumn('date');
        $this->crud->removeColumn('extras');

        return $content;
    }
```

In case your entity is translatable, it will show a multi-language dropdown, just like Edit.
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/6bc4e1e-43760301-4f2696e0-9a2a-11e8-8292-e2d2730a9198.gif",
        "43760301-4f2696e0-9a2a-11e8-8292-e2d2730a9198.gif",
        1279,
        1344,
        "#c7cbd2"
      ]
    }
  ]
}
[/block]