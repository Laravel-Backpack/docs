# Update

---

<a name="about"></a>
## About

This operation allows your admins to add new entries to a database table.

![CRUD Update Operation](https://backpackforlaravel.com/uploads/screenshots/news_add.png)

<a name="requirements"></a>
## Requirements

All editable attributes should be ```$fillable``` on your Model.

<a name="how-to-use"></a>
## How to Use

The ```Update``` operation is **enabled by default**. To disable it, you should use ```$this->crud->denyAccess('update');``` inside your ```setup()``` method. This will make the Update button disappear in ListEntries, in the ```line``` stack.

To use the Update operation, you must:

**Step 1. Specify what field types** you'd like to show for each attribute, in your EntityCrudController's ```setup()``` method. You can do that using the [Fields API](/docs/{{version}}/crud-fields#fields-api). In short you can:

```php
// add a field only to the Update operation
$this->crud->addField($field_definition_array, 'update');
// add a field to both the Update and Update operations
$this->crud->addField($field_definition_array);
```

**Step 2. Specify validation rules, in your the EntityCrudRequest file** that you are typehinting on your ```EntityCrudController::update()``` method. If you need separate validation for Create and Update [look here](#separate-validation).

For more on how to manipulate fields, please read the [Fields documentation page](/docs/{{version}}/crud-fields). For more on validation rules, check out [Laravel's validation docs](https://laravel.com/docs/master/validation#available-validation-rules).

<a name="how-it-works"></a>
## How It Works

CrudController is a RESTful controller, so the ```Update``` operation uses two routes:
- GET to ```/entity-name/{id}/edit``` - points to ```edit()``` which shows the Edit form (```edit.blade.php```);
- POST to ```/entity-name/{id}/edit``` - points to ```store()``` which uses Eloquent to update the entry in the database;

The ```edit()``` method will show all the fields you've defined for this operation using the [Fields API](/docs/{{version}}/crud-fields#fields-api), then upon Save the ```update()``` method will first check the validation from the typehinted FormRequest, then create the entry using the Eloquent model. Only attributes that are ```$fillable``` on the model will actually be updated in the database.

<a name="callbacks"></a>
## Callbacks

Developers coming from GroceryCRUD or other CRUD systems will be looking for callbacks to run before_insert, before_update, after_insert, after_update. **There are no callbacks in Backpack**, because there's no need. The code for the insert&update operations is out in the open for you to customize. Notice your ```EntityCrudController``` **already** has the following methods, which you can modify as you wish:
```php
public function store(StoreRequest $request)
{
    // <---------  here is where a before_insert callback logic would be
    $response = parent::storeCrud();
    // <---------  here is where a after_insert callback logic would be
    return $response;
}

public function update(UpdateRequest $request)
{
    // <---------  here is where a before_update callback logic would be
    $response = parent::updateCrud();
    // <---------  here is where a after_update callback logic would be
    return $response;
}
```

>But before you do that, ask yourself - **_is this something that should be done when an entry is added/updated/deleted from the application, too_**? Not just the admin admin? If so, a better place for it would be the Model. Remember your Model is a pure Eloquent Model, so the cleanest way might be to use [Eloquent Event Observers](https://laravel.com/docs/5.5/eloquent#events) or [accessors and mutators](https://laravel.com/docs/master/eloquent-mutators#accessors-and-mutators).

<a name="translatable-models"></a>
## Translatable models and multi-language CRUDs

![CRUD Update Operation](https://backpackforlaravel.com/uploads/docs/operations/update_translatable.png)

You can let your admins edit multi-lingual entries. Only translations stored the [spatie/laravel-translatable](https://github.com/spatie/laravel-translatable) way are supported right now, but more options will be coming soon.

In order to make one of your Models translatable, you need to:
0. Be running MySQL 5.7+ (or a PosgreSQL with JSON column support);
1. [Install spatie/laravel-translatable](https://github.com/spatie/laravel-translatable#installation);
2. In your database, make all translatable columns either JSON or TEXT.
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

> You DO NOT need to cast translatable string columns as array/json/object in the Eloquent model. From Eloquent's perspective they're strings. So:
> - you _should NOT_ cast ```name```; it's a string in Eloquent, even though it's stored as JSON in the db by SpatieTranslatable;
> - you _should_ cast ```extras``` to ```array```, if each translation stores an array of some sort;

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

<a name="separate-validation"></a>
## Separate Validation Rules for Create and Update

**Differences between the Create and Update validations?** If your Update operation requires a different validation than the Create operation, just:
- create a separate request file for each operation;
- instruct your EntityCrudController to use separate files, in the "use" section;

For example, we could create ```UpdateTagRequest.php``` and ```CreateTagRequest.php```, with different validations, then in TagCrudController just do:
```diff
- use App\Http\Requests\TagRequest as StoreRequest;
+ use App\Http\Requests\CreateTagRequest as StoreRequest;
- use App\Http\Requests\TagRequest as UpdateRequest;
+ use App\Http\Requests\UpdateTagRequest as UpdateRequest;
```
