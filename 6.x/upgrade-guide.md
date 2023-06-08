# Upgrade Guide

---

This will guide you to upgrade from Backpack v5 to v6. The steps are color-coded by the probability that you will need it for your application: <span class="badge badge-danger text-white" style="text-decoration: none;">High</span>, <span class="badge badge-warning text-white" style="text-decoration: none;">Medium</span> and <span class="badge badge-secondary-soft" style="text-decoration: none;">Low</span>.  **At the very least, please read what's in bold**.

<a name="requirements"></a>
## Requirements

Please make sure your project respects the requirements below, before you start the upgrade process. You can check with ```php artisan backpack:version```:

- PHP 8.1+
- Laravel 10.x
- Backpack\CRUD 5.x
- 15-30 minutes (for most projects)

**If you're running Backpack version 3.x-5.x, please follow ALL other upgrade guides first, to incrementally get to use Backpack v6**. Test that your app works well with each version, after each upgrade. Only _afterwards_ can you follow this guide, to upgrade from v5 to v6. Previous upgrade guides:
- [upgrade from 4.1 to 5.x](https://backpackforlaravel.com/docs/5.x/upgrade-guide);
- [upgrade from 4.0 to 4.1](https://backpackforlaravel.com/docs/4.1/upgrade-guide);
- [upgrade from 3.6 to 4.0](https://backpackforlaravel.com/docs/4.0/upgrade-guide);
- [upgrade from 3.5 to 3.6](https://backpackforlaravel.com/docs/3.6/upgrade-guide);
- [upgrade from 3.4 to 3.5](https://backpackforlaravel.com/docs/3.5/upgrade-guide);
- [upgrade from 3.3 to 3.4](https://backpackforlaravel.com/docs/3.4/upgrade-guide);

<a name="upgraade-steps"></a>
## Upgrade Steps

<a name="step-0" href="#step-0" class="badge badge-danger" style="text-decoration: none;">Step 0.</a> **[Upgrade to Laravel 10](https://laravel.com/docs/10.x/upgrade) if you don't use it yet, then test to confirm your app is working fine.**

<a name="composer"></a>
### Composer

<a name="step-1" href="#step-x1" class="badge badge-info text-white" style="text-decoration: none;">Step B1.</a> While in beta, in your `composer.json` please change `minimum-stability` to `dev`.

<a name="step-1" href="#step-1" class="badge badge-danger text-white" style="text-decoration: none;">Step 1.</a> Update your ```composer.json``` file to require:

```
        "backpack/crud": "^6.0.0",
```

<a name="step-2" href="#step-2" class="badge badge-danger text-white" style="text-decoration: none;">Step 2.</a> **Bump the version of any first-party Backpack add-ons you have installed (eg. `backpack/pro`, `backpack/editable-columns` etc.) to `v6.x-dev`**. For third-party add-ons, please check each add-on's Github page.

<a name="step-3" href="#step-3" class="badge badge-danger text-white" style="text-decoration: none;">Step 3.</a> Run ```composer update``` in the command line. If you get any conflicts with **Backpack 1st party add-ons**, most of the time you just need to move one version up, eg: from `backpack/menucrud: ^3.0` to `backpack/menucrud: ^4.0`.

<a name="models"></a>
### Models

**No changes needed.** But there are a few improvements you _could_ make, if you want, that will help clean up your Models and move a bit of logic that might be admin-panel-only to the admin panel files:

<a name="step-4" href="#step-4" class="badge badge-secondary-soft" style="text-decoration: none;">Step 10.</a> (OPTIONAL) If you use acessors and mutators for upload fields, you can now use Uploaders instead. TODO: link to migration steps

<a name="step-5" href="#step-5" class="badge badge-secondary-soft" style="text-decoration: none;">Step 10.</a> (OPTIONAL) If you use accessors and mutators any other fields, you can [use Eloquent model events on fields](/docs/{{version}}/crud-operation-create#use-events-in-your-field-definition).

<a name="form-requests"></a>
### Form Requests

**No changes needed.** But there are a few improvements you _could_ make, if you want

<a name="step-6" href="#step-6" class="badge badge-secondary-soft" style="text-decoration: none;">Step 10.</a> (OPTIONAL) If any of your requests have only a few attributes, you can delete the request file and move the validation to the CrudController; you can specify the [validation rules as an array](/docs/{{version}}/crud-operation-create#b-validating-fields-using-a-rules-array) or [validate directly on fields](/docs/{{version}}/crud-operation-create#c-validating-fields-using-field-attributes);

<a name="step-7" href="#step-7" class="badge badge-secondary-soft" style="text-decoration: none;">Step 10.</a> (OPTIONAL) If you were manually validating the `upload`, `upload_multiple` or `image` field to do complex stuff, v6 provides an easier way to do it; check [the `upload` field docs](/docs/{{version}}/crud-fields#upload), [`upload_multiple` field docs](/docs/{{version}}/crud-fields#upload_ultiple_) for the new validation classes we've introduced - `ValidUpload` and `ValidUploadMultiple`;

<a name="routes"></a>
### Routes

No changes needed.

<a name="config"></a>
### Config

<a name="step-8" href="#step-8" class="badge badge-danger" style="text-decoration: none;">Step 6.</a> We've moved all user-interface-related configs from `config/backpack/base.php` to `config/backpack/ui.php`. Please:
- create a `ui.php` file by copying the content from [here](https://github.com/Laravel-Backpack/CRUD/blob/v6/src/config/backpack/ui.php). re-do any modifications you've made previously in `base.php`.
- delete all the configs that have been moved to `ui.php` from `base.php`; you should end up with a `base.php` file that has [these options only](https://github.com/Laravel-Backpack/CRUD/blob/v6/src/config/backpack/base.php).
- make sure you remove this line from the global scripts section in `ui.php`: 

```php
'packages/backpack/base/js/bundle.js',
```

----

<a name="step-9" href="#step-9" class="badge badge-danger" style="text-decoration: none;">Step 6.</a> Backpack v6 comes with brand new themes! And the default theme is `theme-tabler`, a competely new theme, that introduces quite a few breaking changes. It'll be easier to upgrade if you don't use Tabler from the start, but use `theme-coreuiv2` instead. That theme is there for maximum backwards compatiblity. So please do `composer require backpack/theme-coreuiv2`, then go to `config/backpack/ui.php` and change your view namespace to use that theme:

```php
    'view_namespace'          => 'backpack.theme-coreuiv2::',
    'view_namespace_fallback' => 'backpack.theme-coreuiv2::',
```

<a name="controllers"></a>
### CrudControllers

<a name="step-10" href="#step-10" class="badge badge-warning" style="text-decoration: none;">Step 10.</a> We have removed the `address_algolia` field type, because the API it was using has been sunsetted. You're unlikely to use it at this point, and if you are it's already broken for you. You can use our `google_address` or `google_map` fields as alternatives, but please note you will probably need to edit the DB entries too.

----

<a name="step-11" href="#step-11" class="badge badge-secondary-soft" style="text-decoration: none;">Step 10.</a> (OPTIONAL) We have added column types for all field types (33+ new columns). If previously you weren't showing an attribute in your List and Show operation, because we didn't have a column for it... now we do. Feel free to use the same name for the column as you're using for the field.

----

<a name="step-12" href="#step-12" class="badge badge-secondary-soft" style="text-decoration: none;">Step 10.</a> (OPTIONAL) We've merged the array and fluent syntaxes. If you're using `$this->crud->addField()` and `$this->crud->addColumn()`, then using the fluent syntax to modify that field or column, you can now do them in one go. Both `CRUD::field()` and `CRUD::column()` methods now support an array too, so you can do:

```php
CRUD::field([
    'name' => 'price',
    'label' => 'Product price',
])->type('number');
```

----

<a name="step-13" href="#step-13" class="badge badge-warning" style="text-decoration: none;">Step 10.</a> When defining multi-input fields, use comma instead of array. If you're using the `date_range` or `checklist_dependency` fields, change the name definition from `['first_input', 'second_input']` to `'first_input,second_input'`.

----

<a name="step-14" href="#step-14" class="badge badge-secondary-soft" style="text-decoration: none;">Step 10.</a> (OPTIONAL) If you have your `store()` or `update()` methods overriden just as callbacks, to do stuff when entries are `created` or `updated`, we have a suggestion for you that will clean up your code. Instead of overriding those methods, you can use Eloquent model events to achieve the same thing. See [the updated callbacks docs](/docs/6.x/crud-operation-create#callbacks) for all the options available.

<a href="assets"></a>
### CSS & JS Assets

<a name="step-15" href="#step-15" class="badge badge-warning text-white" style="text-decoration: none;">Step 18.</a> We have invented a whole new way of using CSS & JS assets in Laravel projects. We heavily recommend you use it too for you custom assets. Instead of installing assets using NPM, then minimizing and compiling them:

```php
// you can now do
@basset('https://example.com/link/to/asset.js')
// instead of
<script src="https://example.com/link/to/asset.js">
```

It's as easy as using an asset from a CDN, but it's much _much_ better than that. What happens is that asset is being cached on your machine, and delivered from there. So you get all benefits of CDNs, with no privacy concerns to your users. We heavily recommend you use Basset to load all of your custom assets. Read [the docs](https://github.com/Laravel-Backpack/basset) to find out more stuff you can do with it.

----

<a name="step-16" href="#step-16" class="badge badge-danger text-white" style="text-decoration: none;">Step 18.</a> Since all Backpack packages are now using Basset, assets are no longer picked up from the `public/packages` directory. If you haven't added any custom files to it, **you can now remove that directory using `rm -rf public/packages`**. If you _have_ added or modified things in `public/packages`, please:
- move the custom assets to a different directory
- load them using `@basset(public_path('path/to/file.css'))`
- then remove the `public/packages` directory

<a name="views"></a>
### Views

<a name="step-17" href="#step-17" class="badge badge-secondary-soft" style="text-decoration: none;">Step 20.</a> **Have you developed any custom fields, columns, operations etc?** Rephrased: do you have anything inside your `resources/views/vendor/backpack/crud` directory? If so, we recommended you load your CSS & JS assets using `@basset('path/to/file.css')` instead of `@loadonce('path/to/file.css')` or `<link href="path/to/file.css">`. This will not only make sure that piece of JS/CSS/code is only loaded once per pageload, but it will also help serve all assets from one central location (your `storage/app/public` directory) which can be easily cleared using `php artisan basset:clear` to force cache-busting. You can find [more info about it here](https://github.com/laravel-backpack/basset).

----

<a name="step-18" href="#step-18" class="badge badge-danger" style="text-decoration: none;">Step 21.</a> If you've published and overriden any CRUD blade files, please take a look at the changes, and re-do them in your files too. We've done quite a few changes, to account for different themes in v6. For custom components, a good way to make them pretty would be to copy-paste HTML from [Tabler](https://tabler.io/preview), [CoreUIv4](https://coreui.io/demos/bootstrap/4.2/free/), [Backstrap](https://backstrap.net/) (depending on what theme you've chosen to use) or straight out [Bootstrap](https://getbootstrap.com/docs/5.3/examples/). // TODO: link to diff


----

<a name="step-19" href="#step-19" class="badge badge-danger" style="text-decoration: none;">Step 21.</a> Please rename `resources/views/vendor/backpack/base` directory, from `base` to `ui`.


----

<a name="step-20" href="#step-20" class="badge badge-danger" style="text-decoration: none;">Step 21.</a> Please rename `resources/views/vendor/backpack/ui/inc/sidebar_content.blade.php` from `sidebar_content.blade.php` to `menu_items.blade.php`.

----

<a name="step-21" href="#step-21" class="badge badge-danger" style="text-decoration: none;">Step 21.</a> If you have any other blade files in this new `ui` directory, please note that those files have been moved to themes. Backpack v6 offers three themes right from the start:
- [`backpack/theme-coreuiv2`](https://github.com/Laravel-Backpack/theme-coreuiv2) - maximum backwards-compatibility (basically no breaking changes)
- [`backpack/theme-coreuiv4`](https://github.com/Laravel-Backpack/theme-coreuiv4) - medium backwards-compatibility (some BC from upgrading to Bootstrap 5)
- [`backpack/theme-tabler`](https://github.com/Laravel-Backpack/theme-tabler) - for new projects, and projects who have NOT overridden too many blade files;

Depending on how many files are there, you might want to use `theme-coreuiv2` to check everything still working, THEN change themes to `theme-coreuiv4` or `theme-tabler`. If you want to use any of the new themes, but stuff is breaking, look at the differences between _that_ theme's files and _your_ files, and do the changes needed.

<a name="security"></a>
### Security

<a name="step-23" href="#step-23" class="badge badge-danger" style="text-decoration: none;">Step 23.</a> If your error views have been published a long time ago, they might have a security bug. If you haven't done this already, please run `php artisan backpack:fix` to start escaping the error messages in those views.


<a name="cache"></a>
### Cache

<a name="step-24" href="#step-24" class="badge badge-danger text-white" style="text-decoration: none;">Step 24.</a> Clear your app's cache:
```
php artisan config:clear
php artisan cache:clear
php artisan view:clear
```

If the table view still looks wonky (search bar out of place, big + instead of ellipsis), then do a hard-reload in your browser (Cmd+Shift+R or Ctrl+Shift+F5) to purge the browser cache too.

---

<a name="step-24" href="#step-24" class="badge badge-danger text-white" style="text-decoration: none;">Step 24.</a> If your pages are slow to load, that's because Basset caching the assets as you load the pages, so your first pageload will be quite slow. If you find that annoying, run `php artisan basset:internalize` to cache all CSS and JS assets. Alternatively, if you want Basset NOT to run because you're making changes to CSS and JS files, you can add `BASSET_DEV_MODE=true` to your `.ENV` file.

---

**You're done! Good job.** Thank you for taking the time to upgrade. Now you can:
- thoroughly test your application and your admin panel;
- start using the [new features in Backpack v6](/docs/{{version}}/release-notes);
