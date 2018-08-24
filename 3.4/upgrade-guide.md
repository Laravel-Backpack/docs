# Upgrade Guide

---

This will guide you through upgrading from Backpack 3.3 to 3.4. For upgrading from 3.2 to 3.3 [check out the previous upgrade guide](https://laravel-backpack.readme.io/v3.3/docs/upgrading-crud-32-to-33).

<a name="requirements"></a>
## Requirements
- PHP 7+
- Laravel 5.5+
- 10-30 minutes

<a name="breaking-changes"></a>
## Breaking Changes
- completely rewritten AjaxTables functionality - we fixed all AjaxTables issues and now AjaxDataTables are the default and only option;
- in create/update forms, fields without a tab are displayed *before* all tabs;

As you can see, we've struggled very hard to make this version as backwards-compatible as possible. As such, the upgrade steps are small, and few projects will be negatively affected, unless they've overwritten default functionality.

<a name="upgraade-steps"></a>
## Upgrade Steps

<a name="require-the-lastest-versions"></a>
### Require the latest versions
1. run the commands below (or make these changes in your ```composer.json``` and run ```composer update```)

```bash
composer require backpack/base:"^0.9.0"
composer require backpack/crud:"^3.4.0"
```

<a name="upgrade-backpack-base-to-0-9-x"></a>
### Upgrade Backpack\Base to 0.9.x 

This brings [A LOT of new features](/docs/{{version}}/release-notes) and makes sure all components are up-to-date.

**[OPTIONAL, but recommended]**
2. inside your ```config/app.php``` file, if you have any Backpack service providers or aliases (any for any Backpack package), you can remove them; they're no longer needed, since L5.5 loads them automatically; 
3. Starting with Backpack\Base 0.9.0, we have a new place for you to store your admin routes: ```routes/backpack/custom.php```. That's where ```CRUD::resource()``` routes are generated automatically, when you generate a new CRUD. To future-proof your application, you can publish that file using ```php artisan vendor:publish --provider="Backpack\Base\BaseServiceProvider" --tag=custom_routes```, then manually move your existing admin routes there (from your ```routes/web.php```).
4. inside your routes and controllers, replace the ```admin``` middleware with the ```backpack_middleware()``` helper; this will pull in the admin middleware name, since you can now change that in the config file;
5. The admin middleware's class has been renamed to CheckIfAdmin; if you were extending ```Backpack\Base\app\Http\Middleware\Admin``` anywhere, make sure to extend the new class name: ```Backpack\Base\app\Http\Middleware\CheckIfAdmin```;
6. Replace your ```Auth::user()``` and ```Auth::check()``` calls with ```backpack_auth()->user()``` and ```backpack_auth()->check()``` etc, everywhere in your *admin panel's* custom controllers, models, views;

**[MANDATORY]**
7. Inside ```resources/views/vendor/backpack/base```:
  7.1. Delete any ```base``` blade files that you have not personally customized; namely ```layout.blade.php```; in most cases you should be left with only ```inc/sidebar.blade.php```;
  7.2. Create a ```inc//sidebar_content.blade.php``` file and copy all of your sidebar items and subitems there (```<li><a href=""></a></li>```; here's an [example file](https://github.com/Laravel-Backpack/Base/pull/252/files#diff-6ec82d09b237df882e119c54ec1be9f4));
  7.3. Delete your ```resources/views/vendor/backpack/base/inc/sidebar.blade.php``` file; this is no longer used to store sidebar items, the one above is; and we have the added benefit of being able to do so from the command line;
8. Delete your ```public/vendor/backpack``` and ```public/vendor/adminlte``` folders; we will republish them shortly;
9. Run ```php artisan backpack:base:install```; this might take a few minutes, but it will republish all assets needed;

<a name="upgrade-backpack-crud-to-3-4-x"></a>
### Upgrade Backpack\CRUD to 3.4.x

10. if you're using the filemanager (elFinder) or the ```browse``` field type, run ```composer require barryvdh/laravel-elfinder``` to install it, since it's no longer a mandatory requirement;
11. If you've overwritten the ```syncPivot()``` method, please note that [it's no longer used inside the Create trait](https://github.com/Laravel-Backpack/CRUD/pull/1251/files#diff-be76b927e5cdfce4834d99bd54320930) and [in the Update trait](https://github.com/Laravel-Backpack/CRUD/pull/1251/files#diff-4ee9c00438f5f14f91c9b342e527bf1a); It hasn't been deprecated yet, but we do plan to remove it in the future since ```createRelations()``` is a complete replacement for it, is more legible and supports 1-n relationships too;
12. If you've overwritten the ```search()``` method [take a look at the changes](https://github.com/Laravel-Backpack/CRUD/pull/1251/files#diff-5e51b956834ee63032f4486191667a86) and apply them to your own;
13. If you're using elFinder you should probably replace the ```admin``` middleware with the configurable name, in you ```config/elfinder.php```, [like so](https://github.com/Laravel-Backpack/CRUD/pull/1251/files#diff-81ea2104cbc3733d9b5c623e83e3e40f);
14. If you've overwritten the delete button, it has [suffered massive and important changes](https://github.com/Laravel-Backpack/CRUD/pull/1251/files#diff-715aa5ba925a0c21766b59e5d0ea8f55); we recommend you delete it from your project, so that the new one gets picked up;
15. If you have custom column type, replace your ```<td>```s with ```<span>```s; [example here](https://github.com/Laravel-Backpack/CRUD/pull/1251/files#diff-078af4b74c6b799e80aa8196dfa99173);
16. If you've overwritten one of the following field types (```browse_multiple```, ```date_picker```, ```date_range```, ```datetime_picker```, ```select```, ```select2```, ```select2_from_ajax```, ```select2_from_ajax_multiple```, ```select2_from_array```, ```select2_multiple```, ```date```), you should update their JS and CSS assets [like we have done here](https://github.com/Laravel-Backpack/CRUD/pull/1251/files#diff-078af4b74c6b799e80aa8196dfa99173), to make sure it can still reach those assets; 
17. If you've overwritten the ```list.blade.php``` file, you're in a pickle; the [changes are massive](https://github.com/Laravel-Backpack/CRUD/pull/1251/files#diff-a5044a994cc483a0b10086f52ad4678d); best thing to do would be to delete your file, copy the new one, and make the modifications you need again;
18. Since all DataTables are now responsive, you should check out how your admin panel looks in a small viewport; columns that won't fit will be hidden, so sometimes all that's left is the first column; if that column is *date* or something like that, *no bueno*; best thing to do is to reorder the columns so that the most important column is the first one; you can do that by putting one ```addColumn()``` statement before all the others, in your ```ExampleCrudController::setup()```;
19. Run ```php artisan view:clear``` to make sure your views are using the new assets.