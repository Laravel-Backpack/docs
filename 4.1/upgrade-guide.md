# Upgrade Guide

---

This will guide you to upgrade from Backpack 4.0 to 4.1. The steps are color-coded by the probability that you will need it for your application: <span class="badge badge-info text-white" style="text-decoration: none;">High</span>, <span class="badge badge-warning text-white" style="text-decoration: none;">Medium</span> and <span class="badge badge-secondary-soft" style="text-decoration: none;">Low</span>.

<a name="requirements"></a>
## Requirements

Please make sure your project respects the requirements below, before you start the upgrade process. You can check with ```php artisan backpack:version```:

- PHP 7.4.x, 7.3.x or 7.2.5+ 
- Laravel 7.x or 6.x
- Backpack\CRUD 4.0.x
- 10-30 minutes for most projects, on top of the Laravel upgrade

**If you're running Backpack version 3.x, please follow ALL the minor upgrade guides first, to incrementally get to use Backpack 4.0**. Test that your app works well with each version, after each upgrade. Only _afterwards_ can you follow this guide, to upgrade from 4.0 to 4.1. Previous upgrade guides:
- [upgrade from 3.6 to 4.0](https://backpackforlaravel.com/docs/4.0/upgrade-guide); this is a major upgrade, and requires a v4 license code; if you've purchased a Backpack v3 license, but don't have a Backpack v4 license yet, [read the 4.0 Release Notes here](/docs/4.0/release-notes#backpack-v3-buyers).
- [upgrade from 3.5 to 3.6](https://backpackforlaravel.com/docs/3.6/upgrade-guide);
- [upgrade from 3.4 to 3.5](https://backpackforlaravel.com/docs/3.5/upgrade-guide);
- [upgrade from 3.3 to 3.4](https://backpackforlaravel.com/docs/3.4/upgrade-guide);

<a name="upgraade-steps"></a>
## Upgrade Steps


<a name="step-0" href="#step-0" class="badge badge-info" style="text-decoration: none;">Step 0.</a> **[Upgrade to Laravel 6](https://laravel.com/docs/6.x/upgrade). Then [upgrade to Laravel 7](https://laravel.com/docs/7.x/upgrade), if you can.** This is not _required_, but it's _recommended_. You need to do this _one day_ anyway - might as well do it now. Backpack 4.0 (that your project is already running) supports Laravel 5.8, 6 and 7. So you should:
- **upgrade your project to use Laravel v6 or v7**
- test your app is working fine with them
- continue with "Step 1" below, to also upgrade Backpack to 4.1

Our recommendation is to _not_ stick to Laravel 6.0 just because it's a [LTS version](https://en.wikipedia.org/wiki/Laravel#Release_history), but to upgrade to the latest Laravel. All upgrades after Laravel 5.6 have been very _very_ easy, and it's expected for this to continue, since Laravel has reached stability for some years now. If you're scared or lazy, for just $9 you can purchase a [Laravel Shift](https://laravelshift.com/), to automatically upgrade, and make sure you haven't missed a step.

<a name="composer"></a>
### Composer

<a name="step-1" href="#step-1" class="badge badge-info text-white" style="text-decoration: none;">Step 1.</a> Update your ```composer.json``` file to require ```"backpack/crud": "4.1.*"```

<a name="step-2" href="#step-2" class="badge badge-warning text-white" style="text-decoration: none;">Step 2.</a> If you have a lot of Backpack add-ons installed (and their dependencies), here are their latest versions, that support Backpack 4.1, you can copy-paste the versions of the packages you're using:
```
        "backpack/logmanager": "^4.0.0",
        "backpack/settings": "^3.0.0",
        "backpack/pagemanager": "^3.0.0",
        "backpack/menucrud": "^2.0.0",
        "backpack/newscrud": "^4.0.0",
        "backpack/permissionmanager": "^6.0.0",
        "backpack/backupmanager": "^2.0.0",
        "spatie/laravel-translatable": "^4.0",
        "backpack/langfilemanager": "^3.0.0",

        /* and in require-dev */

        "backpack/generators": "^3.0",
        "laracasts/generators": "^1.0"
```

<a name="step-3" href="#step-3" class="badge badge-warning text-white" style="text-decoration: none;">Step 3.</a> If you're using the Revisions operation, it has now been split into a separate package. So please add ```"backpack/revise-operation": "^1.0",``` to your composer's require section.

<a name="step-4" href="#step-4" class="badge badge-warning text-white" style="text-decoration: none;">Step 4.</a> Backpack itself is no longer using ```laravel/helpers```. Instead of using helpers like ```str_slug()``` we're now doing ```Str::slug()``` everywhere. We recommend you do the same. But if you want to keep using string and array helpers, please add ```"laravel/helpers": "^1.1",``` to your composer's require section.

<a name="step-5" href="#step-5" class="badge badge-warning text-white" style="text-decoration: none;">Step 5.</a> Since we're no longer using ```laravel/helpers```, we need the ```Str``` and ```Arr``` classes to be aliased. You should have already done this when upgrading to Laravel 5.8/6.x/7.x. But please make sure that in your ```config/app.php``` you have these aliases:
```php
    'aliases' => [
        // ...
        'Arr'       => Illuminate\Support\Arr::class,
        // ..
        'Str'       => Illuminate\Support\Str::class,
        // ..
    ],
```

<a name="step-6" href="#step-6" class="badge badge-warning text-white" style="text-decoration: none;">Step 6.</a> Backpack no longer installs the FileManager by default (elFinder), since most projects don't need it. If you did use the FileManager, or the ```browse``` or ```browse_multiple``` fields, make sure you require ```"backpack/filemanager": "^1.0",``` in your ```composer.json``` - we've separated it into an add-on.

<a name="step-7" href="#step-7" class="badge badge-warning text-white" style="text-decoration: none;">Step 7.</a> Backpack no longer installs ```intervention/image``` by default. If you use the ```image``` field type and you've used our example Mutator in your Model, you might be using ```intervention/image``` - please check. If so, please add ```"intervention/image": "^2.3",``` to your ```composer.json```'s require section.

<a name="step-8" href="#step-8" class="badge badge-info text-white" style="text-decoration: none;">Step 8.</a> Run ```composer update``` in the command line.

<a name="models"></a>
### Models

No changes needed.

<a name="routes"></a>
### Routes

No changes needed.

<a name="config"></a>
### Config

<a name="step-9" href="#step-9" class="badge badge-info text-white" style="text-decoration: none;">Step 9.</a> Backpack no longer provides, needs or uses the ```App\Models\BackpackUser``` model for authentication. New installs default to using ```App\User```. Since that model was only used for authentication & forgotten-password functionality, we recommend you:
- delete the ```App\Models\BackpackUser.php``` file;
- change your ```config/backpack/base.php``` file to:

```diff
-   'user_model_fqn' => App\Models\BackpackUser::class,
+   'user_model_fqn' => App\User::class,
```
- if you've used PermissionManager, change your ```config/backpack/permissionmanager.php``` file to:

```diff
    'models' => [
-       'user'       => App\Models\BackpackUser::class,
+       'user'       => App\User::class,
        'permission' => Backpack\PermissionManager\app\Models\Permission::class,
        'role'       => Backpack\PermissionManager\app\Models\Role::class,
    ],
```

- your ```App\User``` will need some changes for backpack to use it properly. It must use ```CrudTrait``` and ```HasRoles``` traits:

```diff
<?php namespace App;

+ use Backpack\CRUD\app\Models\Traits\CrudTrait;
+ use Spatie\Permission\Traits\HasRoles;
use Illuminate\Foundation\Auth\User as Authenticatable; 

class User extends Authenticatable
{
+    use CrudTrait;
+    use HasRoles;

```

Of course, if you actually _liked_ having a separate model for your admins, you can keep it. But we recommend removing it - since it's unnecessary. If you decide to keep the ```BackpackUser``` model, take note that we also removed the ```InheritsRelationsFromParentModel``` trait, that only ```BackpackUser``` was using. To pull that trait in, install [calebporzio/parental](https://github.com/calebporzio/parental) and use ```\Parental\HasParent``` instead of ```InheritsRelationsFromParentModel```.

> **IMPORTANT:** If you use polymorphic relationships, you might have mentions of the ```App\Models\BackpackUser``` model in your BD. You will need to replace all those mentions in the DB with the standard ```App\User``` model.  

**If you've used ```PermissionManager``` or ```spatie/laravel-permission```, please note that you DO use polymorphic relationships.** All permissions and roles that pointed to ```App\Models\BackpackUser``` in the database need to be pointed to ```App\User``` model now:

```bash
# -------------------
# ONLY if you've used PermissionManager and/or spatie/laravel=permission
# -------------------

# Step 9.1.
# Create a database backup. Please. Pretty please.

# Step 9.2.
# Publish the migration for the standard configuration.
# This should work as-is if you haven't changed table names.
php artisan vendor:publish --provider="Backpack\PermissionManager\PermissionManagerServiceProvider" --tag="migrations"

# Step 9.3.
# Take a look at the migration and change whatever you need:
# - are the table names the same in the DB & in the migration?
# - are the column names the same?
# - are there entries in the DB with App\Models\BackpackUser?
# - did you notice there's no down() method for the migration? maybe you'll create a DB backup now, if you haven't?

# Step 9.4.
# Run the migration.
php artisan migrate

# Step 9.5.
# Take another look at your model_has_roles and model_has_permissions tables.
# Are there any more entries that point to App\Models\BackpackUser?
```

Another example of polymorphic relationships is the ```revisions``` table, if you've used it you may have references to ```App\Models\BackpackUser``` there too, please replace it with ```App\User```.

<a name="controllers"></a>
### CrudControllers

The steps below should apply for each of your CrudControllers. For each Step, go through every one of your CrudControllers (usually stored in ```app\Http\Controllers\Admin```:

<a name="step-10" href="#step-10" class="badge badge-warning text-white" style="text-decoration: none;">Step 10.</a> If you're using the ```RevisionsOperation``` inside your CrudControllers, that operation has been moved to a separate package (that you've already installed in steps 3 & 8). Now you need to:
- inside your CrudControllers, search for ```Backpack\CRUD\app\Http\Controllers\Operations\RevisionsOperation``` and replace with ```Backpack\ReviseOperation\ReviseOperation```
- if you've customized the Revisions operation, take note that the specialty methods to set a Revisions view no longer exist; inside your CrudController, please use the normal ```get()```/```set()```, but please note that the operation is now called "_revise_" (verb) not "_revisions_" (noun), so your changes should be:
    - from ```$this->crud->setRevisionsView()``` to ```$this->crud->set('revise.view')```
    - from ```$this->crud->getRevisionsView()``` to ```$this->crud->get('revise.view')```
    - from ```$this->crud->setRevisionsTimelineView()``` to ```$this->crud->set('revise.timelineView')```
    - from ```$this->crud->getRevisionsTimelineView()``` to ```$this->crud->set('revise.timelineView')```
    - from ```$this->crud->setRevisionsTimelineContentClass()``` to ```$this->crud->set('revise.timelineContentClass')```
    - from ```$this->crud->getRevisionsTimelineContentClass()``` to ```$this->crud->set('revise.timelineContentClass')```
- if you've defined settings for the "_revisions_" operation for all CRUDs, inside your ```config/backpack/crud.php```, please note that the operation name has changed, so inside ```operations``` you should change ```revisions``` to ```revise```;

<a name="step-11" href="#step-11" class="badge badge-secondary-soft" style="text-decoration: none;">Step 11.</a> Inside CrudControllers, Backpack 4.1:
- no longer defines ```$this->request```
- still defines ```$this->crud->request``` (aka ```CRUD::request```) but uses getters and setters to work with it (```$this->crud->getRequest()``` and ```$this->crud->setRequest()```);

Why? Since ```$this->request``` did nothing at all, we've removed it, to avoid any confusion between working with ```$this->request``` and ```$this->crud->request```. Make sure that:
- **If you have ```$this->request``` anywhere in your CrudControllers custom logic**, please replace it with either Laravel's ```request()``` helper or with ```$this->crud->getRequest()```.
- **If you have ```$this->crud->request``` anywhere inside your custom CrudController logic**, please replace it with either ```$this->crud->getRequest()``` or ```$this->crud->setRequest()``` depending on what your intention is.

<a name="step-12" href="#step-12" class="badge badge-warning text-white" style="text-decoration: none;">Step 12.</a> Inside CrudControllers, if you've used ```wrapperAttributes``` on fields, please note that it's now called ```wrapper```. Please search & replace ```wrapperAttributes``` with ```wrapper``` in your CrudControllers.

Additionally, for the `select2_from_ajax` and `select2_from_ajax_multiple` field types, the inputs on the main form are no longer sent through the AJAX request by default. If you're using [dependant selects](https://backpackforlaravel.com/docs/4.1/crud-how-to#add-a-select2-field-that-depends-on-another-field), please make sure that you add `'include_all_form_fields' => true` to your `select2_from_ajax` and `select2_from_ajax_multiple` field definition to keep the same behaviour as before.

<a href="assets"></a>
### CSS & JS Assets

<a name="step-13" href="#step-13" class="badge badge-info text-white" style="text-decoration: none;">Step 13.</a> We've updated most CSS & JS dependencies to their latest versions. There are two ways to publish the latest styles and scripts for these dependencies:
- (A) If you have NOT touched you ```public/packages``` folder, or placed anything custom inside it:
        - delete the ```public/packages``` directory and all its contents;
        - run ```php artisan vendor:publish --provider="Backpack\CRUD\BackpackServiceProvider" --tag=public```
        - if you use elFinder, also delete ```resources/views/vendor/elfinder``` and run ```php artisan backpack:filemanager:install```
- (B) Run ```php artisan vendor:publish --provider="Backpack\CRUD\BackpackServiceProvider" --tag=public --force```. Please note this will overwrite anything that's already there. This B solution has a downside: unused files are not removed. A few files Backpack no longer uses will still be in your ```public/packages``` folder, even though they're no longer used.



<a name="views"></a>
### Views


<a name="step-14" href="#step-14" class="badge badge-info text-white" style="text-decoration: none;">Step 14.</a> **If you've overwritten default Fields, or have custom Fields**, take note that ALL of them have suffered changes (for the better); as a minimum, if you have any files in ```resources/views/vendor/backpack/crud/fields``` you should:
- find & replace ```crud::inc.field_attributes``` with ```crud::fields.inc.attributes```
- find & replace ```crud::inc.field_translatable_icon``` with ```crud::fields.inc.translatable_icon```
- change the wrapping element; take a look at the diff below or at [the diff for the text field](https://github.com/Laravel-Backpack/CRUD/pull/2601/files#diff-9b83997dcde20848b90e97048aca5485), and do the same for all the fields you've created or overwritten:

```diff
-   <div @include('crud::inc.field_wrapper_attributes') >
+   @include('crud::fields.inc.wrapper_start')

// Your actual field HTML

-   </div>
+   @include('crud::fields.inc.wrapper_end')
```


<a name="step-15" href="#step-15" class="badge badge-warning text-white" style="text-decoration: none;">Step 15.</a> **If you've overwritten Columns, or have custom Columns**, take note that ALL columns have suffered changes (for the better). Most notably:
- ```wrapper``` allows you to add links to your columns;
- ```escaped``` allows you to output HTML instead of text; 

If you want your custom/overwritten columns to have these cool new features, they're very easy to implement:
- [take a look here](https://github.com/Laravel-Backpack/CRUD/pull/2508/files#diff-e82fc7ec56e0617fb83762cc2a9467d4) for a simple column (ex: ```text```);
- [take a look here](https://github.com/Laravel-Backpack/CRUD/pull/2508/files#diff-ab965b3cd4720c4c2ec4a2423f3ea648) for a column that has multiple entries (ex: ```select```);


If you need a difftool, we recommend [Kaleidoscope](https://www.kaleidoscopeapp.com) on Mac OS, [WinMerge](https://winmerge.org) on Windows, or [Fork](https://git-fork.com/) on both Mac OS and Windows.


<a name="step-16" href="#step-16" class="badge badge-info text-white" style="text-decoration: none;">Step 16.</a> Backpack 4.1 uses the same icon set, [Line Awesome](https://icons8.com/line-awesome), but we've upgraded to the latest version. 
- The good news - this new version brings a total of 1000+ icons, and complete icon-parity with Font Awesome 5.11.2. Any icon Font Awesome 5 has, Line Awesome has it too. 
- The bad news:
    - it's not 100% backwards-compatible. The same icons are there, but a few of them have changed names. For example, there's no more ```newspaper-o``` it's now just ```newspaper```;
    - additionally, we're no longer using the "_font awesome compatible syntax_" to load Line Awesome Icons (```fa fa-home```), we're now using the "_line awesome syntax_" (```la la-home```), to prevent conflicts when both fonts are used at the same time;

In order to account for this icon font change please:
- inside your ```resources/views/vendor/backpack``` folder, search & replace ```fa fa-``` with ```la la-```;
- for each icon you fix above, double-check that it shows in the browser; if it doesn't, search for an alternative on the [Line Awesome website](https://icons8.com/line-awesome); usually the syntax change is very _very_ small; and there are a lot more icons now, so you'll definitely find something;

<a name="step-17" href="#step-17" class="badge badge-info text-white" style="text-decoration: none;">Step 17.</a> In order to be able to use the new fluent syntax for Widgets, you should make sure all main admin panel views (ex: dashboard, create, update, etc) extend the ```blank``` template:

```diff
//from 
- @extends('backpack::layouts.top_left')
- @extends(backpack_view('layouts.top_left'))

// to
+ @extends(backpack_view('blank'))
// or
+ @extends('backpack::blank')

```

<a name="cache"></a>
### Cache

<a name="step-18" href="#step-18" class="badge badge-warning text-white" style="text-decoration: none;">Step 18.</a> Clear your app's cache:
```
php artisan config:clear
php artisan cache:clear
php artisan view:clear
```

---

**You're done! Good job.** Thank you for taking the time to upgrade. Now you can:
- thoroughly test your application and your admin panel;
- start using the [new features in Backpack 4.1](/docs/4.1/release-notes);
