# Upgrade Guide

---

This will guide you through upgrading from Backpack 3.6 to 4.0. 


> **Backpack v4 is a paid upgrade.** You can upgrade your project and run it just fine on localhost - no license key needed for that. But in production you'll need a different license code for v4. For details please see this version's [release notes](/docs/{{version}}/release-notes#licensing).


<a name="requirements"></a>
## Requirements

Please make sure your project respects the requirements below, before you start the upgrade process. You can check with ```php artisan backpack:base:version```:

- Backpack\CRUD 3.6.x installed; if not, please follow the minor upgrade guides below to get to 3.6;
- Laravel 5.8 / Laravel 6.0 installed
- PHP 7.2+
- 30-60 minutes for most projects

**If you're running Backpack/CRUD version 3.3, 3.4 or 3.5, please follow the minor upgrade guide first, to get to 3.6**. Only _afterwards_ can you upgrade from 3.6 to 4.0. Guides:
- [upgrade from 3.5 to 3.6](https://backpackforlaravel.com/docs/3.6/upgrade-guide);
- [upgrade from 3.4 to 3.5](https://backpackforlaravel.com/docs/3.5/upgrade-guide);
- [upgrade from 3.3 to 3.4](https://backpackforlaravel.com/docs/3.4/upgrade-guide);

<a name="upgraade-steps"></a>
## Upgrade Steps

<a name="composer"></a>
### Composer

**Step 0.** 

Update your ```composer.json``` file to require:
- ```"backpack/crud": "^4.0.0"```
- ```"laravel/framework": "5.8.*|^6.0"```
- ```"backpack/generators": "^2.0"``` (in require-dev)

Then run ```composer update```.

[OPTIONAL] If you have a lot of Backpack add-ons installed (and their dependencies), here are their latest versions, you can copy-paste the versions of the packages you're using:
```
        "backpack/crud": "^4.0.0",
        "backpack/logmanager": "^3.0.0",
        "backpack/settings": "^3.0.0",
        "backpack/pagemanager": "^2.0.0",
        "backpack/menucrud": "^2.0.0",
        "backpack/newscrud": "^3.0.0",
        "backpack/permissionmanager": "^5.0",
        "backpack/backupmanager": "^2.0.0",
        "backpack/generators": "^2.0",
        "spatie/laravel-translatable": "^4.0",
        "barryvdh/laravel-elfinder": "^0.4.2",
        "spatie/laravel-backup": "^6.1"
```

**Note:** If you're using ```webfactor/laravel-generators```, please remove it for now. It does not support Backpack v4 yet.

<a name="models"></a>
### Models

**Step 1.** We've moved the model traits and notifications, so please do a search-and-replace in your ```app``` or ```Models``` folder:
- replace ```Backpack\CRUD\CrudTrait``` with ```Backpack\CRUD\app\Models\Traits\CrudTrait```
- replace ```Backpack\Base\app\Models\Traits\InheritsRelationsFromParentModel``` with ```Backpack\CRUD\app\Models\Traits\InheritsRelationsFromParentModel```
- replace ```Backpack\Base\app\Notifications\ResetPasswordNotification``` with ```Backpack\CRUD\app\Notifications\ResetPasswordNotification```
- replace ```Backpack\CRUD\ModelTraits\SpatieTranslatable``` with ```Backpack\CRUD\app\Models\Traits\SpatieTranslatable```

<a name="routes"></a>
### Routes

**Step 2.0** Change all you CRUD routes as shown below (most developers hold their CRUD routes inside ```routes/backpack/custom.php```):
```diff
Route::group([
    'prefix'     => config('backpack.base.route_prefix', 'admin'),
    'middleware' => ['web', config('backpack.base.middleware_key', 'admin')],
    'namespace'  => 'App\Http\Controllers\Admin',
], function () {
-    CRUD::resource('menu-item', 'MenuItemCrudController');
+    Route::crud( 'menu-item', 'MenuItemCrudController');
});
```

Best to search-and-replace in your entire ```routes``` folder:
- replace ```CRUD::resource``` with ```Route::crud```

**Step 2.1** Backpack no longer provides a `->with()` function for adding extra routes on your controllers. If you've used ```->with()``` on your CRUD routes, please either:
- (A) move your routes outside the with() closure, as normal routes, in your ```custom.php```;
- (B) move your routes to the controller, inside a ```setupXxxRoutes()``` method; any method on the controller that looks like that will automatically be called when loading the routes; take a look at [any operation](https://github.com/Laravel-Backpack/CRUD/tree/master/src/app/Http/Controllers/Operations) to see an example;

See the latest docs for more details [Add Extra CRUD Routes](https://backpackforlaravel.com/docs/4.0/crud-how-to#add-extra-crud-routes)

**Step 2.2**
If you refer to CRUD routes by name - we've dropped the "crud" prefix. You'll need to update any references in your code. So `crud.model-name.index` is now `model-name.index`. This Regex search should help you find any calls to `route()` which use the `crud.` prefix: `/route\s*\(\s*['"]crud./`

<a name="controllers"></a>
### CrudControllers

The steps below should apply for each of your CrudControllers. For each Step, go through every one of your CrudControllers (usually stored in ```app\Http\Controllers\Admin```:

**Step 3.** Make sure the method where you set up your CrudPanel is called ```setup()```, not ```__construct()```. Especially if you've generated your CRUDs using versions of Backpack v3 from 2016-2017. In most cases you can just rename ```__construct()``` to ```setup()```, since ```setup()``` is called inside ```CrudController::__construct()``` anyway.

**Step 4.** In v4, CrudController comes with ZERO operations loaded by default (instead of create, read, update, delete). You need to use a few operation traits in each of you EntityCrudControllers, to enable the previously-default operations:

```php
    
    use \Backpack\CRUD\app\Http\Controllers\Operations\ListOperation;
    use \Backpack\CRUD\app\Http\Controllers\Operations\CreateOperation;
    use \Backpack\CRUD\app\Http\Controllers\Operations\UpdateOperation;
    use \Backpack\CRUD\app\Http\Controllers\Operations\DeleteOperation;
    
```

Make sure they're used INSIDE you EntityCrudController, not just OUTSIDE it:

```diff

class SettingCrudController extends CrudController
{
+    use \Backpack\CRUD\app\Http\Controllers\Operations\ListOperation;
+    use \Backpack\CRUD\app\Http\Controllers\Operations\CreateOperation;
+    use \Backpack\CRUD\app\Http\Controllers\Operations\UpdateOperation;
+    use \Backpack\CRUD\app\Http\Controllers\Operations\DeleteOperation;

    public function setup()
    {
        // ...
```

This is a great time to think about which default operations you're NOT using with each CrudController. If you're actually not using an operation, just commend or delete the ```use``` statement for that operation. This will also prevent the routes for that operation from being registered, so your ```php artisan route:list``` will be cleaner.

**Step 5.** You might also be using other, non-default operations, in your CrudControllers. If so, you need to do the same for them (use the operation trait on the EntityCrudController where you'd like to use that operation). Here are all the operations Backpack v4 provides, use them on your EntityCrudControllers as you see fit:

```php
    // previously default operations (in Backpack v3)
    use \Backpack\CRUD\app\Http\Controllers\Operations\ListOperation;
    use \Backpack\CRUD\app\Http\Controllers\Operations\CreateOperation;
    use \Backpack\CRUD\app\Http\Controllers\Operations\UpdateOperation;
    use \Backpack\CRUD\app\Http\Controllers\Operations\DeleteOperation;
    
    // previously optional operations (in Backpack v3)
    use \Backpack\CRUD\app\Http\Controllers\Operations\ShowOperation;
    use \Backpack\CRUD\app\Http\Controllers\Operations\ReorderOperation;
    use \Backpack\CRUD\app\Http\Controllers\Operations\RevisionsOperation;
    use \Backpack\CRUD\app\Http\Controllers\Operations\CloneOperation;
    use \Backpack\CRUD\app\Http\Controllers\Operations\BulkCloneOperation;
    use \Backpack\CRUD\app\Http\Controllers\Operations\BulkDeleteOperation;
    
```

**Step 6.** If in any of your EntityCrudControllers, you're using ```parent::``` to call a method from Backpack's CrudController, it will not work anymore. Since the methods are now applied using a trait, not by extending a CrudController. You will now have to rename the trait's method in the Use statement in order to call it from your custom method, for example:

```
class MyCrudController extends CrudController
{
    use \Backpack\CRUD\app\Http\Controllers\Operations\UpdateOperation {
        edit as parentEdit;
    }

    public function edit($id)
    {
        // customizations go here
        // then we call the parent
	return $this->parentEdit($id);
    }
        
```        

**(6.1)** If your ```store()``` and ```update()``` methods don't have any custom logic apart than calling the parent method, you can delete them. We no longer need the Request type-hinted. So if they look like this, you can delete them:
```php
    public function store(StoreRequest $request)
    {
        // your additional operations before save here
        $redirect_location = parent::storeCrud($request);
        // your additional operations after save here
        // use $this->data['entry'] or $this->crud->entry
        return $redirect_location;
    }

    public function update(UpdateRequest $request)
    {
        // your additional operations before save here
        $redirect_location = parent::updateCrud($request);
        // your additional operations after save here
        // use $this->data['entry'] or $this->crud->entry
        return $redirect_location;
    }
```

**IMPORTANT!!!** For the validation to still take place, using the same FormRequest, you need to instruct that operation to do so. In v4 you can setup an individual operation in methods that look like this: ```setupXxxOperation()```. So in this case, if you've deleted the previous ```store()``` and ```update()``` methods, it's now:

```php
    protected function setupCreateOperation()
    {
        $this->crud->setValidation(StoreRequest::class);
    }

    protected function setupUpdateOperation()
    {
        $this->crud->setValidation(UpdateRequest::class);
    }
```

**(6.2)** If you have custom logic inside your ```store()``` and ```update()``` methods, note that we've changed the parent method names:
- from ```updateCrud()``` to ```update()```
- from ```storeCrud()``` to ```store()```

Follow step 6.1 with this in mind. The end result should be something like this:

```php
    use \Backpack\CRUD\app\Http\Controllers\Operations\CreateOperation { store as traitStore; }
    use \Backpack\CRUD\app\Http\Controllers\Operations\UpdateOperation { update as traitUpdate; }

    public function store(StoreRequest $request)
    {
        // do something before validation, before save, before everything; for example:
        
        // $this->crud->request->request->add(['author_id'=> backpack_user()->id]);
        // $this->crud->addField(['type' => 'hidden', 'name' => 'author_id']);
        // $this->crud->request->request->remove('password_confirmation');
        // $this->crud->removeField('password_confirmation');
        
        $redirect_location = $this->traitStore();
        
        // do something after save
        
        return $redirect_location;
    }

    public function update(UpdateRequest $request)
    {
        // ..
        $redirect_location = $this->traitUpdate($request);
        // ..
        return $redirect_location;
    }

```


**(6.3)** To be able to call the same method, but from the trait (not the parent), please rename the method from the trait, and call that name instead:

```diff
use Backpack\CRUD\app\Http\Controllers\Operations\ShowOperation;

class MonsterCrudController extends CrudController
{
-    use ShowOperation;
+    use ShowOperation {
+          show as traitShow;
+       }

    public function show()
    {
        // do sth custom here
-        return parent::show();
+        return $this->traitShow();
    }
```

Notice it's now ```$this->```, not ```parent::```. You do NOT need to do this for every method in your EntityCrudController that you've overwritten completely. Only for the methods that have a ```parent::smth()``` call inside them.


**Step 7.** The ```store()``` and ```update()``` methods previously stored all inputs the form, _except for_ special inputs (like ```_token```, ```_method```, ```current_tab``` etc.). This process has now been changed: they now store _only_ the inputs defined by the fields.

**(7.1)** If you've used the ```checklist_dependency``` field type, its definition has changed - it should now have an array for name, instead of a string:

```diff
$this->crud->addField([
    'label'             => 'Roles and Permissions',
    'field_unique_name' => 'user_role_permission',
    'type'              => 'checklist_dependency',
-   'name'              => 'roles_and_permissions',
+   'name'              => ['roles', 'permissions'],
    'subfields'         => [...] 
]);
```

**(7.2)** If you've used the ```date_range``` field type, it's definition has changed. Its ```start_name``` and ```end_name``` have been merged into ```name``` (an array), and its ```default_start``` and ```default_end``` have been merged into ```default``` (an array) too:

```diff
$this->crud->addField([
-    'name' => 'date_range', // a unique name for this field
-    'start_name' => 'start_date', // the db column that holds the start_date
-    'end_name' => 'end_date', // the db column that holds the end_date
+    'name' => ['start_date', 'end_date'] // the db column that holds the start_date and end_date
    'label' => 'Event Date Range',
    'type' => 'date_range',
    // OPTIONALS
-    'start_default' => '2019-10-05 09:00', // default value for start_date
-    'end_default' => '2019-10-10 10:00', // default value for end_date
+    'default' => ['2019-10-05 09:00', '2019-10-10 10:00'], // default value for start_date and end_date
    'date_range_options' => [ // options sent to daterangepicker.js
        'timePicker' => true,
        'locale' => ['format' => 'DD/MM/YYYY HH:mm']
    ],
]);
```

**(7.3)** If you have custom field types, where the ```<input name="xxx">``` differs from ```$field['name']```, you need to modify your field type so that it uses ```$field['name']``` as its name.

**(7.4)** If you have custom field types, where there are other inputs that you want stored in the database in addition to ```$field['name']```, you can use ```$field['name']``` as an array. Make sure you pass an array when you call addField(), then use that array in the blade file. Take a look at how the ```date_range``` field does it, you can do the same.

**Step 8.** In Backpack v4, you can no longer specify which form to add a field to, as a second parameter to ```addField()```. Doing ```$this->crud->addField('smth', 'create');``` inside ```setup()``` won't add it to Create, it will add it to whatever operation is currently being performed. Which sometimes is the Update operation. The way to define which fields show up in Create and which in Update has become the same as ANY feature, for ANY operation - you place the calls inside the closure/method for that operation.

It is recommended you split all the feature calls (fields, columns, filters, buttons) in your ```setup()``` method by operation, to avoid conflicts between operations that use the same features. We've provided two methods. Take a look at both, and decide which one you like better:

**Option 8.A. Operation Closures**. Move your calls to an operation closure, and that code will be run ONLY when that operation is being performed.

```php
public function setup() 
{
    //..
    
    // run calls for this one operation
    $this->crud->operation('list', function() {
       // your addColumn, addFilter, addButton calls here, for the List operation
    });
    
    // run calls for one of these operations
    $this->crud->operation(['create', 'update'], function() {
       // your addField, etc for the Create and Update operations
    });
    
    // ..
}
```

**Option 8.B. setupXxxOperation methods**. Move your calls to a method that follows the ```setupXxxOperation()``` naming convention, where ```Xxx``` is the operation name, and that code will only be run when operation ```Xxx``` is being performed.

```php
public function setupListOperation()
{
   // calls to addColumn, addFilter, addButton, etc
}

public function setupCreateOperation()
{
   // calls to addField
}

public function setupUpdateOperation()
{
   // calls to addField
   // $this->setupCreateOperation(); // if it's the same as Create
}

public function setupShowOperation()
{
   // calls to addColumn
   // $this->setupListOperation(); // if you want it to have the same columns as List
}
```

_Note: You can keep your calls inside ```setup()``` too, but they will be applied for ALL operations, like in Backpack v3. In most cases, if you haven't had a conflict in v3, you won't have a conflict in v4. But we recommended you take the time now, and split up your ```setup()```. It will also make your admin panels faster._

**Step 9.** Optional. We've added a ```CRUD``` facade, so that you can do ```CRUD::smth() instead of ```$this->crud->smth()```. You can replace all your ```$this->crud->smth()``` calls to ```CRUD::smth()```, if you like it better that way, if you import the facade at the top of your EntityCrudController:
- (A) ```use CRUD;``` or 
- (B) ```use Backpack\CRUD\app\Library\CrudPanel\CrudPanelFacade as CRUD;```

**Step 10.** Some operations that were previously non-default (Revisions, Reorder, Clone, BulkClone, BulkDelete) can now set themselves up automatically, if you use the trait on your EntityCrudController. If you're using one of these operations on your EntityCrudController, consider taking a look at the changes, and removing the useless code from your controllers.

**(10.1) RevisionsOperation**. To enable the operation, you have to ```use use \Backpack\CRUD\app\Http\Controllers\Operations\RevisionsOperation;``` on your EntityCrudController. If you've done so, you can delete calls to ```$this->crud->allowAccess('revisions')``` from your ```setup()```. This is performed automatically by the operation now.

**(10.2) ReorderOperation**. To enable the operation, you have to ```use use \Backpack\CRUD\app\Http\Controllers\Operations\ReorderOperation;``` on your EntityCrudController. If you've done so, you can delete calls to ```$this->crud->allowAccess('reorder')``` from your ```setup()```. This is performed automatically by the operation now. The same goes with ```$this->crud->enableReorder()``` - you can delete that, it's performed by the operation. In fact, all you need to configure the operation now is:
```php
    protected function setupReorderOperation()
    {
        $this->crud->set('reorder.label', 'name'); // the attribute on the Model which will be shown on draggable elements
        $this->crud->set('reorder.max_level', 2); // how deep do you want to allow the nesting
    }
```

**(10.3) CloneOperation**. To enable the operation, you have to ```use use \Backpack\CRUD\app\Http\Controllers\Operations\CloneOperation;``` on your EntityCrudController. If you've done so, you can delete the calls to ```$this->crud->allowAccess('clone')``` from your ```setup()```. It's performed by default by the operation.

**(10.4) BulkCloneOperation**. Previously to enable the BulkClone operation you needed to do this in your ```setup()```:
```php
$this->crud->enableBulkActions();
$this->crud->allowAccess('clone');
$this->crud->addButton('bottom', 'bulk_clone', 'view', 'crud::buttons.bulk_clone', 'beginning');
```
You can delete that now, if you've added ```use use \Backpack\CRUD\app\Http\Controllers\Operations\BulkCloneOperation;``` on your EntityCrudController - it's being performed by default by the operation.

**(10.5) BulkDeleteOperation**. Previously to enable the BulkDelete operation you needed to do this in your ```setup()```:
```php
$this->crud->enableBulkActions();
$this->crud->addBulkDeleteButton();
```
You can delete that now, if you've added ```use use \Backpack\CRUD\app\Http\Controllers\Operations\BulkDeleteOperation;``` on your EntityCrudController - it's being performed by default by the operation.


**Step 11.** Only affects advanced usage. If you've used _properties_ to interact with operation features on ```$this->crud```, instead of getters & setters, most likely that property no longer exists. So if you've used the undocumented ```$this->crud->columns```, ```$this->crud->create_fields```, ```$this->crud->update_fields```, ```$this->crud->buttons```, ```$this->crud->access``` properties or anything like that, instead of using the methods that were documented, this step is for you. 

All CrudPanel properties that were there only to support a feature for a CRUD Operation have been removed. Instead, all those things are stored in one array, ```$this->crud->settings```, and we've provided an API to easily work with that array. [Read this detailed explanation](https://github.com/Laravel-Backpack/CRUD/pull/1997#issuecomment-536255543) if you're interested in this huge change. An easier way to migrate your usage of properties would be to see how it now works inside that operation trait, and use the new Settings API to interact with operation features, just like the operation does.

Using CrudPanel properties instead of getters & setters was undocumented, but possible. So most people will not be affected by this. But if you have used CrudPanel properties directly, use the new Settings API instead of direct properties. Your old code will not work under v4.

**Step 12.** [OPTIONAL] If you want your calls to be shorter, you can now do ```CRUD::smth()``` instead of ```$this->crud->smth()```. For this to work, make sure your CrudController uses our new Facade. So it would be:

```php

// ..
use Backpack\CRUD\app\Library\CrudPanel\CrudPanelFacade as CRUD;
// ..

public function CategoryCrudController {
     // .. 
    public function setup()
    {
        CRUD::setModel("App\Models\Category");
        CRUD::setRoute(config('backpack.base.route_prefix', 'admin').'/category');
        CRUD::setEntityNameStrings('category', 'categories');
    }
}
```

instead of

```php
    public function setup()
    {
        $this->crud->setModel("App\Models\Category");
        $this->crud->setRoute(config('backpack.base.route_prefix', 'admin').'/category');
        $this->crud->setEntityNameStrings('category', 'categories');
    }
```

This is completely optional - whatever you prefer.


<a href="views"></a>
### Views

Most views have suffered big changes, since we've moved from Bootstrap 3 to Bootstrap 4, and from AdminLTE to CoreUI. If you've overwritten many Backpack views, the upgrade process will be more difficult for you: you have to start from our new views and make the changes again.

**Step 13.** Check your ```resources/views/vendor/backpack``` folder for any views. If you find anything there beside ```base/inc/sidebar_content.blade.php```, you'll need to take a look at that file in our package - it most likely has changed, and you may need to make changes to your file too. We recommend you use a diff tool - should save some time. [Kaleidoscope](https://www.kaleidoscopeapp.com) is our preferred diff tool, on Mac OS. [WinMerge](https://winmerge.org) is a good option for Windows.

If you've overwritten a lot of blade files, you may hate us for this, we know :-) But keep in mind that we've moved from Bootstrap 3 to Bootstrap 4. And from AdminLTE to CoreUI. We tried to keep the changes to a minimum. But this major change was _impossible_ to do without changing a lot of blade files.

**Step 14.** Fix you sidebar menu. In your ```resources/views/vendor/backpack/base/inc/sidebar_content.blade.php```, apply the new classes to your sidebar elements (notice ```nav-item```, ```nav-link``` and ```nav-icon```):

```html
<!-- This file is used to store sidebar items, starting with Backpack\Base 0.9.0 -->
<li class='nav-item'><a class='nav-link' href="{{ backpack_url('dashboard') }}"><i class="nav-icon fa fa-dashboard"></i> {{ trans('backpack::base.dashboard') }}</a></li>
<li class='nav-item'><a class='nav-link' href="{{ backpack_url('elfinder') }}"><i class="nav-icon fa fa-files-o"></i> <span>{{ trans('backpack::crud.file_manager') }}</span></a></li>

<!-- News: Articles, Categories, Tags  -->
<li class='nav-item nav-dropdown'>
    <a class='nav-link nav-dropdown-toggle' href="#"><i class="nav-icon fa fa-newspaper-o"></i>News</a>
    <ul class="nav-dropdown-items">
      <li class='nav-item'><a class='nav-link' href="{{ backpack_url('article') }}"><i class="nav-icon fa fa-newspaper-o"></i> Articles</a></li>
      <li class='nav-item'><a class='nav-link' href="{{ backpack_url('category') }}"><i class="nav-icon fa fa-list"></i> Categories</a></li>
      <li class='nav-item'><a class='nav-link' href="{{ backpack_url('tag') }}"><i class="nav-icon fa fa-tag"></i> Tags</a></li>
    </ul>
</li>
```

**Step 15.** Publish the new CSS&JS, for Backpack v4: ```php artisan vendor:publish --provider="Backpack\CRUD\BackpackServiceProvider" --tag=minimum```

**Step 16.** Delete the old CSS & JS, for Backpack v3:
```bash
# delete the AdminLTE css and js
rm -rf public/vendor/adminlte

# MANUAL: if you've added any custom css & js for Backpack/CRUD or Backpack/Base, move them:
# - from vendor/backpack/base to packages/backpack/base
# - from vendor/backpack/crud to packages/backpack/crud

# delete the CSS and JS for Backpack v3
rm -rf public/vendor/backpack

# if the public/vendor directory is empty now, delete it
rmdir public/vendor
```

**Step 17.** If you have custom buttons in your ```resources/views/vendor/backpack/crud/buttons```, or added through a model function, consider using the ```btn btn-sm btn-link``` classes on the anchor, so that they match the rest of the buttons.


**Step 18.** If you've installed and used the File Manager (elFinder), please:
```php
# delete its published views
rm -rf resources/views/vendor/elfinder

# re-publish them
php artisan backpack:install
```

<a name="config"></a>
### Config

** Step 19. ```config/backpack/crud.php```**

Most variables have been renamed and reordered - they're now sorted by operation name. Please manually insert take [the contents of the new file](https://github.com/Laravel-Backpack/CRUD/blob/v4/src/config/backpack/crud.php). Change the values to match your old config file. [Diff here](https://github.com/Laravel-Backpack/CRUD/pull/2064/files#diff-d548ca942f541d9c99eaf64f83e92bf9).


** Step 20. ```config/backpack/base.php```**

Follow the same process as with the config file above, making sure your file will have [the new content](https://github.com/Laravel-Backpack/CRUD/blob/v4/src/config/backpack/base.php). There have been NO changes in the following sections:
- Registration
- Routing
- Authentication
- File System
- License Code


<a name="cache"></a>
### Cache

**Step 21.** Clear your app's cache:
```bash
php artisan config:clear
php artisan cache:clear
php artisan view:clear
```

---

**You're done! Good job.** Thank you for taking the time to upgrade. Keep in mind that Backpack v4 is not a free upgrade. A different license code is required. More details inside this version's [release notes](/docs/{{version}}/release-notes#licensing).
