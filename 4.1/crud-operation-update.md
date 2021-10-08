# Update Operation

---

<a name="about"></a>
## About

This operation allows your admins to edit entries from the database.

![CRUD Update Operation](https://backpackforlaravel.com/uploads/docs-4-1/operations/update.png)

<a name="requirements"></a>
## Requirements

All editable attributes should be ```$fillable``` on your Model.

<a name="how-to-use"></a>
## How to Use

**Step 0. Use the operation trait on your controller**:

```php
<?php

namespace App\Http\Controllers\Admin;

use Backpack\CRUD\app\Http\Controllers\CrudController;

class ProductCrudController extends CrudController
{
    use \Backpack\CRUD\app\Http\Controllers\Operations\UpdateOperation;

    protected function setupUpdateOperation()
    {
        // $this->crud->setValidation(StoreRequest::class);
        // $this->crud->addField()
	
        // or just do everything you've done for the Create Operation
        // $this->crud->setupCreateOperation();
	
	// You can also do things depending on the current entry
	// (the database item being edited or updated)
	// if ($this->crud->getCurrentEntry()->smth == true) {}
    }
}
```

This will:
- allow access to this operation;
- make an Edit button appear in the ```line``` stack, next to each entry, in the List operation view;

To use the Update operation, you must:

**Step 1. Specify what field types** you'd like to show for each attribute, in your controller's ```setupUpdateOperation()``` method. You can do that using the [Fields API](/docs/{{version}}/crud-fields#fields-api). In short you can:

```php
// add a field only to the Update operation
$this->crud->addField($field_definition_array);
// add a field to both the Update and Update operations
$this->crud->addField($field_definition_array);
```

**Step 2. Specify which FormRequest file to use for validation and authorization**, inside your ```setupUpdateOperation()``` method. You can pass the same FormRequest as you did for the Create operation if there are no differences. If you need separate validation for Create and Update [look here](#separate-validation).
```php
$this->crud->setValidation(StoreRequest::class);
```

For more on how to manipulate fields, please read the [Fields documentation page](/docs/{{version}}/crud-fields). For more on validation rules, check out [Laravel's validation docs](https://laravel.com/docs/master/validation#available-validation-rules).

<a name="how-it-works"></a>
## How It Works

CrudController is a RESTful controller, so the ```Update``` operation uses two routes:
- GET to ```/entity-name/{id}/edit``` - points to ```edit()``` which shows the Edit form (```edit.blade.php```);
- POST to ```/entity-name/{id}/edit``` - points to ```update()``` which uses Eloquent to update the entry in the database;

The ```edit()``` method will show all the fields you've defined for this operation using the [Fields API](/docs/{{version}}/crud-fields#fields-api), then upon Save the ```update()``` method will first check the validation from the typehinted FormRequest, then create the entry using the Eloquent model. Only attributes that have a field type added and are ```$fillable``` on the model will actually be updated in the database.

<a name="callbacks"></a>
## Callbacks

Developers coming from GroceryCRUD or other CRUD systems will be looking for callbacks to run before_insert, before_update, after_insert, after_update. **There are no callbacks in Backpack**. The store code is inside a trait, so you can easily overwrite it:

```php
<?php

namespace App\Http\Controllers\Admin;

use Backpack\CRUD\app\Http\Controllers\CrudController;

class ProductCrudController extends CrudController
{
    use \Backpack\CRUD\app\Http\Controllers\Operations\UpdateOperation { update as traitUpdate; }

    // ...
    
    public function update()
    {
        // do something before validation, before save, before everything; for example:
        // $this->crud->addField(['type' => 'hidden', 'name' => 'author_id']);
        // $this->crud->removeField('password_confirmation');
	
	// Note: By default Backpack ONLY saves the inputs that were added on page using Backpack fields.
	// This is done by stripping the request of all inputs that do NOT match Backpack fields for this
	// particular operation. This is an added security layer, to protect your database from malicious
	// users who could theoretically add inputs using DeveloperTools or JavaScript. If you're not properly
	// using $guarded or $fillable on your model, malicious inputs could get you into trouble.
	
	// However, if you know you have proper $guarded or $fillable on your model, and you want to manipulate 
	// the request directly to add or remove request parameters, you can also do that.
	// We have a config value you can set, either inside your operation in `config/backpack/crud.php` if
	// you want it to apply to all CRUDs, or inside a particular CrudController:
    	// $this->crud->setOperationSetting('saveAllInputsExcept', ['_token', '_method', 'http_referrer', 'current_tab', 'save_action']);
	// The above will make Backpack store all inputs EXCEPT for the ones it uses for various features.
	// So you can manipulate the request and add any request variable you'd like.
	// $this->crud->getRequest()->request->add(['author_id'=> backpack_user()->id]);
	// $this->crud->getRequest()->request->remove('password_confirmation');
	// $this->crud->getRequest()->request->add(['author_id'=> backpack_user()->id]);
	// $this->crud->getRequest()->request->remove('password_confirmation');
	
        $response = $this->traitUpdate();
        // do something after save
        return $response;
    }
}
```

>But before you do that, ask yourself - **_is this something that should be done when an entry is added/updated/deleted from the application, too_**? Not just the admin admin? If so, a better place for it would be the Model. Remember your Model is a pure Eloquent Model, so the cleanest way might be to use [Eloquent Event Observers](https://laravel.com/docs/5.5/eloquent#events) or [accessors and mutators](https://laravel.com/docs/master/eloquent-mutators#accessors-and-mutators).

<a name="translatable-models"></a>
## Translatable models and multi-language CRUDs

![CRUD Update Operation](https://backpackforlaravel.com/uploads/docs-4-0/operations/update_translatable.png)

For localized apps, you can let your admins edit multi-lingual entries. Translations are stored using [spatie/laravel-translatable](https://github.com/spatie/laravel-translatable).

In order to make one of your Models translatable (localization), you need to:
0. Be running MySQL 5.7+ (or a PostgreSQL with JSON column support);
1. [Install spatie/laravel-translatable](https://github.com/spatie/laravel-translatable#installation);
2. In your database, make all translatable columns either JSON or TEXT.
3. Use Backpack's ```HasTranslations``` trait on your model (instead of using spatie's ```HasTranslations```) and define what fields are translatable, inside the ```$translatable``` property. For example:

```php
<?php

namespace App\Models;

use Backpack\CRUD\app\Models\Traits\CrudTrait;
use Illuminate\Database\Eloquent\Model;
use Backpack\CRUD\app\Models\Traits\SpatieTranslatable\HasTranslations;

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

Additionally, if you have slugs (but only if you need translatable slugs), you'll need to use backpack's classes instead of the ones provided by cviebrock/eloquent-sluggable:

```php
<?php

namespace Backpack\NewsCRUD\app\Models;

use Illuminate\Database\Eloquent\Model;
use Backpack\CRUD\app\Models\Traits\CrudTrait;
use Backpack\CRUD\app\Models\Traits\SpatieTranslatable\Sluggable;
use Backpack\CRUD\app\Models\Traits\SpatieTranslatable\SluggableScopeHelpers;
use Backpack\CRUD\app\Models\Traits\SpatieTranslatable\HasTranslations;

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
> If your slugs are not translatable, use the ```cviebrock/eloquent-sluggable``` traits. The Backpack's ```Sluggable``` trait saves your slug as a JSON object, regardless of the ```slug``` field being defined inside the ```$translatable``` property.

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
