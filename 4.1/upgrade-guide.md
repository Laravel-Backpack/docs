# Upgrade Guide

---

This will guide you to upgrade from Backpack 4.0 to 4.1. 

<a name="requirements"></a>
## Requirements

Please make sure your project respects the requirements below, before you start the upgrade process. You can check with ```php artisan backpack:version```:

- PHP 7.2.5+
- Laravel 7, Laravel 6 or Laravel 5.8
- Backpack\CRUD 4.0.x
- 5-10 minutes for most projects, on top of the Laravel upgrade

**If you're running Backpack version 3.x, please follow ALL the minor upgrade guides first, to incrementally get to use Backpack 4.0**. Test that your app works well with each version, after each upgrade. Only _afterwards_ can you follow this guide, to upgrade from 4.0 to 4.1. Previous upgrade guides:
- [upgrade from 3.6 to 4.0](https://backpackforlaravel.com/docs/4.0/upgrade-guide); this is a major upgrade, and requires a v4 license code; if you've purchased a Backpack v3 license, but don't have a Backpack v4 license yet, [read the 4.0 Release Notes here](/docs/4.0/release-notes#backpack-v3-buyers).
- [upgrade from 3.5 to 3.6](https://backpackforlaravel.com/docs/3.6/upgrade-guide);
- [upgrade from 3.4 to 3.5](https://backpackforlaravel.com/docs/3.5/upgrade-guide);
- [upgrade from 3.3 to 3.4](https://backpackforlaravel.com/docs/3.4/upgrade-guide);

<a name="upgraade-steps"></a>
## Upgrade Steps


**Step 0.** **[Upgrade to Laravel 6](https://laravel.com/docs/6.x/upgrade), then [upgrade to Laravel 7](https://laravel.com/docs/7.x/upgrade), if you can.** This is not _required_, but it's _recommended_. You need to do this _one day_ anyway - might as well do it now. 

Our recommendation is to _not_ stick to Laravel 6.0 just because it's a [LTS version](https://en.wikipedia.org/wiki/Laravel#Release_history), but to upgrade to the latest Laravel, once a year or so. All upgrades after Laravel 5.6 have been very _very_ easy, and it's expected for this to continue, since Laravel has reached a pretty stable state. In addition, you can purchase a [Laravel Shift](https://laravelshift.com/) for as low as $9, to automatically upgrade, and make sure you haven't missed a step.

Backpack 4.0 (that you should already be running) supports Laravel 5.8, 6 and 7. So you **can upgrade Laravel to version 6 or 7, test your app, then continue with the Backpack upgrade**. This guide will wait.

<a name="composer"></a>
### Composer

**Step 1.** Update your ```composer.json``` file to require ```"backpack/crud": "4.1.x-dev as 4.0.99"``` _NOTE - THIS IS THE DEV VERSION; WHEN 4.1 IS LAUNCHED THE OFFICIAL STABLE REQUIRED VERSION WILL BE ^4.1.0_

**Step 2.** [OPTIONAL] If you have a lot of Backpack add-ons installed (and their dependencies), here are their latest versions, that support Backpack 4.1, you can copy-paste the versions of the packages you're using:
```
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

        /* and in require-dev */

        "backpack/generators": "^2.0",
        "laracasts/generators": "^2.0"
```

**Step 3.** If you're using the Revisions operation, it has now been split into a separate package. So please:
- add ```"backpack/revise-operation": "^1.0",``` to your composer's require section
- inside your CrudControllers, search for ```Backpack\CRUD\app\Http\Controllers\Operations\RevisionsOperation``` and replace with ```Backpack\ReviseOperation\ReviseOperation```
- inside your CrudController, if you've used the specialty methods to set a Revisions view, they no longer exist; please use the normal ```get()```/```set()```, but please note that he operation is now called "_revise_" (verb) not "_revisions_" (noun), so your changes should be:
    - from ```$this->crud->setRevisionsView()``` to ```$this->crud->set('revise.view')```
    - from ```$this->crud->getRevisionsView()``` to ```$this->crud->get('revise.view')```
    - from ```$this->crud->setRevisionsTimelineView()``` to ```$this->crud->set('revise.timelineView')```
    - from ```$this->crud->getRevisionsTimelineView()``` to ```$this->crud->set('revise.timelineView')```
    - from ```$this->crud->setRevisionsTimelineContentClass()``` to ```$this->crud->set('revise.timelineContentClass')```
    - from ```$this->crud->getRevisionsTimelineContentClass()``` to ```$this->crud->set('revise.timelineContentClass')```
- if you've defined settings for the "_revisions_" operation for all CRUDs, inside your ```config/backpack/crud.php```, please note that the operation name has changed, so inside ```operations``` you should change ```revisions``` to ```revise```;

**Step 4.** Backpack itself is no longer using ```laravel/helpers```. Instead of using helpers like ```str_slug()``` we're now doing ```Str::slug()``` everywhere. We recommend you do the same. But if you want to keep using string and array helpers, please add ```"laravel/helpers": "^1.1",``` to your composer's require section.

**Step 5.** Since we're no longer using ```laravel/helpers```, we need the ```Str``` and ```Arr``` classes to be aliased. You should have already done this when upgrading to Laravel 5.8/6.x/7.x. But please make sure that in your ```config/app.php``` you have these ```Arr``` and ```Str``` aliases. If you don't, please add them:
```php
    'aliases' => [
        // ...
        'Arr'       => Illuminate\Support\Arr::class,
        // ..
        'Str'       => Illuminate\Support\Str::class,
        // ..
    ],
```

**Step 6.** Backpack no longer installs the FileManager by default (elFinder). Since a lot of developers don't need it. If you did use the FileManager, the ```browse``` or the ```browse_multiple``` fields, require ```"backpack/filemanager": "^1.0",``` in your ```composer.json```.

**Step 7.** Backpack no longer installs ```intervention/image``` by default. If you use the ```image``` field type, and you've used our example Mutator in your Model, you might be using ```intervention/image``` - please check. If so, please add ```"intervention/image": "^2.3",``` to your ```composer.json```'s require section.

**Step 8.** Run ```composer update``` in the command line.

<a name="models"></a>
### Models

No changes needed.

<a name="routes"></a>
### Routes

No changes needed.

<a name="config"></a>
### Config

No changes needed.

<a name="controllers"></a>
### CrudControllers

The steps below should apply for each of your CrudControllers. For each Step, go through every one of your CrudControllers (usually stored in ```app\Http\Controllers\Admin```:

**Step 9.** If you're using the ```RevisionsOperation``` inside your CrudControllers, that operation has been moved to a separate package (that you've already installed in steps 1-8). You just need to change the class namespace:
- from ```\Backpack\CRUD\app\Http\Controllers\Operations\RevisionsOperation```
- to ```\Backpack\RevisionsOperation\RevisionsOperation```

**Step 10.** Inside CrudControllers, Backpack 4.1:
- no longer defines ```$this->request```
- still defines ```$this->crud->request``` (aka ```CRUD::request```) but uses getters and setters to work with it (```$this->crud->getRequest()``` and ```$this->crud->setRequest()```);

Why? Since ```$this->request``` did nothing at all, we've removed it, to avoid any confusion between working with ```$this->request``` and ```$this->crud->request```. To do:
- **If you have ```$this->request``` anywhere in your CrudControllers custom logic**, please replace it with either the global ```request()``` or with ```$this->crud->getRequest()```.
- **If you have ```$this->crud->request``` anywhere inside your custom CrudController logic**, please replace it with either ```$this->crud->getRequest()``` or ```$this->crud->setRequest()``` depending on what you're doing there.

<a href="assets"></a>
### CSS & JS Assets

**Step 11.** We've updated most CSS & JS dependencies to their latest versions. There are two ways to publish the latest styles and scripts for these dependencies:
- (A) If you have NOT touched you ```public/packages``` folder, or placed anything custom inside it:
        - delete the ```public/packages``` directory and all its contents;
        - run ```php artisan vendor:publish --provider="Backpack\CRUD\BackpackServiceProvider" --tag=public```
        - additionally, if you use elFinder:
            - ```php artisan elfinder:publish```
            - ```php artisan vendor:publish --provider="Backpack\CRUD\BackpackServiceProvider" --tag=elfinder```
- (B) Run ```php artisan vendor:publish --provider="Backpack\CRUD\BackpackServiceProvider" --tag=public --force```. Please note this will overwrite anything that's already there. This B solution has a downside: unused files are not removed. A few files Backpack no longer uses will still be in your ```public/packages``` folder, even though they're no longer used.



<a name="cache"></a>
### Cache

**Step 12.** Clear your app's cache:
```bash
php artisan config:clear
php artisan cache:clear
php artisan view:clear
```

<a href="views"></a>
### Views

**Step 13.** Backpack 4.1 uses the same icon set, [Line Awesome](https://icons8.com/line-awesome), but we've upgraded to the latest version. 
- The good news - this new version brings a total of 1000+ icons, and commplete icon-parity with Font Awesome 5.11.2. Any icon Font Awesome has, Line Awesome has too. 
- The bad news 
    - it's not 100% backwards-compatible. The same icons are there, but a few of them have changed names. For example, there's no more ```newspaper-o``` it's now just ```newspaper```;
    - additionally, we're no longer using the "_font awesome compatible syntax_" to load Line Awesome Icons (```fa fa-home```), we're now using the "_line awesome syntax_" (```la la-home```), to prevent conflicts when both fonts are used at the same time;

In order to account for this icon font change please:
- inside your ```resources/views/vendor/backpack``` folder, search for ```fa fa-``` and replace with ```la la-```;
- for each icon you fix above, double-check that it shows in the browser; if it doesn't, search for an alternative on the [Line Awesome website](https://icons8.com/line-awesome); usually the syntax change is very _very_ small; and there are a lot more icons now, so you'll definitely find something;


**Step 14.** **If you've published and customized Field or Column views**, you might need to take a look at the changes 4.1 brings to those files, and do the changes inside your new files, if you want those columns/fields to support the new 4.1 features. Check your ```resources/views/vendor/backpack/crud/fields``` and ```resources/views/vendor/backpack/crud/columns``` folders for any files - if you have files there, this should concern you. We recommend you use a diff tool - should save some time. [Kaleidoscope](https://www.kaleidoscopeapp.com) is our preffered diff tool on Mac OS. [WinMerge](https://winmerge.org) is a good option on Windows.

Most ```field```views have suffered changes, in order to make the JavaScript work inside modals and ```repeatable``` fields. Most ```columns``` views have suffered changes, in order to make add the ```wrapper``` and ```escaped``` functionality. If you _haven't_ overwritten the fields/columns to customize them, you're ok, changes were made in a backwards-compatible manner.

---

**You're done! Good job.** Thank you for taking the time to upgrade. Now you can:
- thoroughly test your application and your admin panel;
- start using the [new features in Backpack 4.1](/docs/4.1/release-notes);
