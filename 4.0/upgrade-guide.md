# Upgrade Guide

---

This will guide you through upgrading from Backpack 3.6 to 4.0. For upgrading from 3.5 to 3.6 [check out the previous upgrade guide](https://backpackforlaravel.com/docs/3.6/upgrade-guide).

<a name="requirements"></a>
## Requirements

- Backpack\CRUD 3.6.x installed
- Laravel 5.8 / Laravel 6.0 installed
- PHP 7.2+
- 1-2 hours on top of your normal [Laravel 6 upgrade](https://laravel.com/docs/6.0/upgrade)

<a name="upgraade-steps"></a>
## Upgrade Steps

0. Update your ```composer.json``` file to require ```"backpack/crud": "4.*"``` along with ```"laravel/framework": "6.*"```. Then run ```composer update```.

### Models

We've moved the model traits, so please do a search-and-replace in your ```app``` or ```Models``` folder:
- replace ```Backpack\CRUD\CrudTrait``` with ```Backpack\CRUD\app\Models\Traits\CrudTrait```
- replace ```Backpack\CRUD\ModelTraits\SpatieTranslatable``` with ```Backpack\CRUD\app\Models\Traits\SpatieTranslatable```

### Routes

Developers should change all their routes as shown below:
```diff
Route::group([
    'prefix' => config('backpack.base.route_prefix'),
    'middleware' => ['web', 'admin'],
    'namespace' => 'Backpack\MenuCRUD\app\Http\Controllers\Admin',
], function () {
-    CRUD::resource('menu-item', 'MenuItemCrudController');
+    Route::crud( 'menu-item', 'MenuItemCrudController');
});
```

### CrudControllers

**Step 1.** Make sure your ```$crud``` object is configured inside the ```setup()``` method, not ``__construct()```, especially if you've generated your cruds using old versions of Backpack v3 (from 2016-2017). In most cases you can just rename ```__construct()``` to ```setup()```, since ```setup()``` is called inside ```__construct()``` anyway.

**Step 2.** ZERO operations and routes are loaded by default by CrudController. Every EntityCrudController needs the following changes to be able to have all operations that were previously loaded by default:

```diff

class SettingCrudController extends CrudController
{
+    use \Backpack\CRUD\app\Http\Controllers\Operations\ListOperation;
+    use \Backpack\CRUD\app\Http\Controllers\Operations\CreateOperation;
+    use \Backpack\CRUD\app\Http\Controllers\Operations\UpdateOperation;
+    use \Backpack\CRUD\app\Http\Controllers\Operations\DeleteOperation;
+    use \Backpack\CRUD\app\Http\Controllers\Operations\SaveActions;

    public function setup()
    {
        // ...
```

**Step 3.** This is a great time to think about which operations you're NOT using with each CrudController. If you're actually not using an operation, just commend or delete the ```use``` statement for that operation. This will also prevent the routes for that operation from being registered, so your ```php artisan route:list``` will be cleaner.

**Step 4.** If you're calling a method using ```parent::action()``` anywhere inside your EntityCrudController, it will not work anymore. To be able to call the same method, but from the trait (not the parent), please rename the method from the trait, and call that name instead:

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

This is inconvenient, but I think it's a small price to pay for having the convenience of:
- routes only being loaded for each operation
- easily using operations with routes
- easily using fields with routes

You do NOT need to do this for every method in your EntityCrudController that you've overwritten completely. Only for the methods that have a ```parent::smth()``` call inside them.


**Step 5.** The Create and Update operations used to store all inputs the form sent, _except for_ special inputs (like ```_token```, ```_method```, ```current_tab``` etc.). This process has now been changed: they now store _only_ the inputs that the fields specify as names. So:
- if you've used the ```checklist_dependency``` field type, its definition has changed - it should now have an array for name, instead of a string:
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
- if you've used the ```date_range``` field type, it's definition has changed - its ```start_name``` and ```end_name``` have been merged into ```name``` (an array), and its ```default_start``` and ```default_end``` have been merged into ```default``` (an array);
```diff
$this->crud->addField([
-    'name' => 'date_range', // a unique name for this field
-    'start_name' => , // the db column that holds the start_date
-    'end_name' => , // the db column that holds the end_date
+    'name' => ['start_date', 'end_date'] // the db column that holds the start_date and end_date
    'label' => 'Event Date Range',
    'type' => 'date_range',
    // OPTIONALS
-    'start_default' => , // default value for start_date
-    'end_default' => , // default value for end_date
+    'default' => ['2019-03-28 01:01', '2019-04-05 02:00'], // default value for start_date and end_date
    'date_range_options' => [ // options sent to daterangepicker.js
        'timePicker' => true,
        'locale' => ['format' => 'DD/MM/YYYY HH:mm']
    ],
]);
```
- if you have custom field types, where the input name that you want saved differs from ```$field['name']```, you need to modify your field type so that it takes that name;
- if you have custom field types, where there are other inputs that you want stored in the db, in addition to ```$field['name']```, you can pass an array in the field definition, for a name; see examples above;

### Views

Most views have suffered big changes, since we've moved from Bootstrap 3 to Bootstrap 4, and from AdminLTE to CoreUI. If you've overwritten many Backpack views, the upgrade process will be more difficult for you: you have to start from our new views and make the changes again.

Step 1. Check your ```resources/views/vendor/backpack``` folder for any views. If you find anything there beside ```base/inc/sidebar_content.blade.php```, you'll need to take a look at that file in the package - it most likely has changed. We recommend you use a diff tool - should save some time. Kaleidoscope is our preffered diff tool, on Mac OS.

Step 2. In your ```resources/views/vendor/backpack/base/inc/sidebar_content.blade.php```, apply the new classes to your sidebar elements (notice ```nav-item```, ```nav-link``` and ```nav-icon```):

```html
<!-- This file is used to store sidebar items, starting with Backpack\Base 0.9.0 -->
<li class='nav-item'><a class='nav-link' href="{{ backpack_url('dashboard') }}"><i class="fa fa-dashboard nav-icon"></i> {{ trans('backpack::base.dashboard') }}</a></li>
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

---

**That's it**. Thank you for taking the time to upgrade. Keep in mind that Backpack v4 is not a free upgrade. A different license code is required. More details inside this version's [release notes](/docs/{{version}}/release-notes#licensing).
