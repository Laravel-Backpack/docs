# Create

---

<a name="about"></a>
## About

This operation allows your admins to add new entries to a database table.

![CRUD Create Operation](https://backpackforlaravel.com/uploads/screenshots/news_add.png)

<a name="requirements"></a>
## Requirements

All editable attributes should be ```$fillable``` on your Model.

<a name="how-to-use"></a>
## How to Use

The ```Create``` operation is **enabled by default**. To disable it, you should use ```$this->crud->denyAccess('create');``` inside your ```setup()``` method. This will make the Add button disappear in ListEntries, in the ```top``` button stack.

To use the Create operation, you must:

**Step 1. Specify what field types** you'd like to show for each editable attribute, in your EntityCrudController's ```setup()``` method. You can do that using the [Fields API](/docs/{{version}}/crud-fields#fields-api). In short you can:

```php
// add a field only to the Create operation
$this->crud->addField($field_definition_array, 'create');
// add a field to both the Create and Update operations
$this->crud->addField($field_definition_array);
```

**Step 2. Specify validation rules, in your the EntityCrudRequest file** that you are typehinting on your ```EntityCrudController::store()``` method. If you need separate validation for Create and Update [look here](#separate-validation).

For more on how to manipulate fields, please read the [Fields documentation page](/docs/{{version}}/crud-fields). For more on validation rules, check out [Laravel's validation docs](https://laravel.com/docs/master/validation#available-validation-rules).

<a name="how-it-works"></a>
## How It Works

CrudController is a RESTful controller, so the ```Create``` operation uses two routes:
- GET to ```/entity-name/create``` - points to ```create()``` which shows the Add New Entry form (```create.blade.php```);
- POST to ```/entity-name``` - points to ```store()``` which does the actual storing operation;

The ```create()``` method will show all the fields you've defined for this operation using the [Fields API](/docs/{{version}}/crud-fields#fields-api), then upon Save the ```store()``` method will first check the validation from the typehinted FormRequest, then create the entry using the Eloquent model. Only attributes that are ```$fillable``` on the model will actually be stored in the database.

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

For UX purposes, when creating multi-language entries, the Create form will only allow the admin to add an entry in one language, the default one. The admin can then edit that entry in all available languages, to translate it. Check out [this same section in the Update operation](/docs/{{version}}/crud-operation-update#translatable-models) for how to enable multi-language functionality.

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