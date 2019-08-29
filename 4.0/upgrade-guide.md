# Upgrade Guide

---

This will guide you through upgrading from Backpack 3.5 to 3.6. For upgrading from 3.4 to 3.5 [check out the previous upgrade guide](https://backpackforlaravel.com/docs/3.5/upgrade-guide).

<a name="requirements"></a>
## Requirements

- Backpack\CRUD 3.6.x installed
- Laravel 5.7/5.8 installed
- PHP 7.1.3+
- 30 minutes on top of your normal [Laravel 6 upgrade](https://laravel.com/docs/5.8/upgrade)

<a name="upgraade-steps"></a>
## Upgrade Steps

0. Update your ```composer.json``` file to require ```"backpack/crud": "4.*"``` along with ```"laravel/framework": "6.*"```. Then run ```composer update```.


### Routes

Developers should change all their routes as shown below. All Backpack packages should also do this. Generators should also use this new (and only) syntax:
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

**Step 4.** Additionally in CrudControllers, the actions operations for create() and update() have been renamed. Please make the following change in your EntityCrudControllers:

```diff
    public function store(StoreRequest $request)
    {
-        return parent::storeCrud($request);
+        return $this->storeEntry($request);
    }

    public function update(UpdateRequest $request)
    {
-        return parent::updateCrud($request);
+        return $this->updateEntry($request);
    }
```

Notice you're now calling ```$this``` not ```parent```. That's because **there are no parent methods now**. All operation are loaded as traits directly on your EntityCrudController, nothing on the CrudController it extends. 

**Step 5.** If you're calling a method using ```parent::action()``` anywhere inside your EntityCrudController, it will not work anymore. To be able to call the same method, but from the trait (not the parent), please rename the method from the trait, and call that name instead:

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
- easily using fields with operations with routes

You do NOT need to do this for everything the methods in your EntityCrudController that you've overwritten completely. Only for the methods that have a ```parent::smth()``` call inside them.


---

**That's it**. Thank you for taking the time to upgrade.
