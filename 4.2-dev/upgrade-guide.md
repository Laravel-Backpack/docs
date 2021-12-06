# Upgrade Guide

---

This will guide you to upgrade from Backpack 4.1 to 4.2. The steps are color-coded by the probability that you will need it for your application: <span class="badge badge-info text-white" style="text-decoration: none;">High</span>, <span class="badge badge-warning text-white" style="text-decoration: none;">Medium</span> and <span class="badge badge-secondary-soft" style="text-decoration: none;">Low</span>.

<a name="requirements"></a>
## Requirements

Please make sure your project respects the requirements below, before you start the upgrade process. You can check with ```php artisan backpack:version```:

- PHP 8.x, 7.4.x or 7.3.x
- Laravel 8.x
- Backpack\CRUD 4.1.x
- around 10 minutes for most projects, on top of the Laravel upgrade

**If you're running Backpack version 3.x or 4.0, please follow ALL the minor upgrade guides first, to incrementally get to use Backpack 4.1**. Test that your app works well with each version, after each upgrade. Only _afterwards_ can you follow this guide, to upgrade from 4.0 to 4.1. Previous upgrade guides:
- [upgrade from 4.0 to 4.1](https://backpackforlaravel.com/docs/4.1/upgrade-guide);
- [upgrade from 3.6 to 4.0](https://backpackforlaravel.com/docs/4.0/upgrade-guide); this is a major upgrade, and requires a v4 license code; if you've purchased a Backpack v3 license, but don't have a Backpack v4 license yet, [read the 4.0 Release Notes here](/docs/4.0/release-notes#backpack-v3-buyers).
- [upgrade from 3.5 to 3.6](https://backpackforlaravel.com/docs/3.6/upgrade-guide);
- [upgrade from 3.4 to 3.5](https://backpackforlaravel.com/docs/3.5/upgrade-guide);
- [upgrade from 3.3 to 3.4](https://backpackforlaravel.com/docs/3.4/upgrade-guide);

<a name="upgraade-steps"></a>
## Upgrade Steps


<a name="step-0" href="#step-0" class="badge badge-info" style="text-decoration: none;">Step 0.</a> **[Upgrade to Laravel 8](https://laravel.com/docs/8.x/upgrade)**, then test your app is working fine, then continue with "Step 1" below, to also upgrade Backpack to 4.2.

Note: If you're scared or lazy, for just $9 you can purchase a [Laravel Shift](https://laravelshift.com/?ref=backpack), to automatically upgrade to Laravel 8, and make sure you haven't missed a step.

<a name="composer"></a>
### Composer

<a name="step-1" href="#step-1" class="badge badge-info text-white" style="text-decoration: none;">Step 1.</a> Update your ```composer.json``` file to require ```"backpack/crud": "4.2.*"```

<a name="step-2" href="#step-2" class="badge badge-warning text-white" style="text-decoration: none;">Step 2.</a> If you have a lot of Backpack add-ons installed (and their dependencies), here are their latest versions, that support Backpack 4.2, you can copy-paste the versions of the packages you're using:

// TODO

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

<a name="step-3" href="#step-3" class="badge badge-info text-white" style="text-decoration: none;">Step 3.</a> Run ```composer update``` in the command line.

<a name="models"></a>
### Models

<a name="step-12" href="#step-12" class="badge badge-warning text-white" style="text-decoration: none;">Step 13.</a> The `repeatable` field has been completely rewritten, in order to work with values as a nested PHP array, not a JSON. This has HUGE benefits, but presents a few breaking changes. If you've used the `repeatable` field in your CRUDs
- please make sure that db column is casted as `array` or `json` (eg. add `protected $casts = ['testimonials' => 'array'];` to your model);
- a data syntax bug has been fixed - previously, if inside `repeatable` you used a subfield with multiple values (`select_multiple` or `select2_multiple` etc.), it would not store `'categories': [1, 2]` like you'd expect, but `'categories[]': [1, 2]`; those brackets were not intentional, but we could not fix them without telling you about  it; when upgrading to 4.2:
    - if you've coded a workaround to strip those brackets, you can now remove the workaround;
    - if you use the attribute with brackets anywhere, please expect it to be either _with_ or _without_ brackets; Backpack will NOT strip all the brackets automatically, it will only strip them upon saving, when an admin edits that particular entry;

<a name="form-requests"></a>
### Form Requests

<a name="step-12" href="#step-12" class="badge badge-warning text-white" style="text-decoration: none;">Step 13.</a> One big reason for rewriting the `repeatable` field was to improve validation. Because the result of that field is now a nested PHP array, you no longer have to code your own custom validation logic... you can now use nested array validation inside your FormRequest (eg. `'testimonial.*.name' => 'required|min:5|max:256'`); see [Laravel's nested array input validation rules](https://laravel.com/docs/validation#validating-nested-array-input) for more examples; however, if you do NOT want to update your validation, that's ok too, just make sure you no longer `json_decode()` the value (eg: instead of `$fieldGroups = json_decode($value);` you can do `$fieldGroups = is_array($value) ? $value : [];`);

<a name="routes"></a>
### Routes

No changes needed.  // TODO

<a name="config"></a>
### Config

<a name="step-12" href="#step-12" class="badge badge-warning text-white" style="text-decoration: none;">Step 13.</a> This Backpack version also throttles password request attempts, so that a malicious actor cannot use the "reset password" functionality to send unsolicited emails. We recommend you add this configuration entry to your `config/backpack/base.php` and customize if necessary. See [#3862](https://github.com/Laravel-Backpack/CRUD/pull/3862) for details.

```php
// TODO: code that needs adding
```


<a name="step-12" href="#step-12" class="badge badge-danger text-white" style="text-decoration: none;">Step 14.</a> Operation configurations have been moved from `config/backpack/crud.php`, each to its own file. That way, if an operation config value isn't present, it will fall back to Backpack's default value. To upgrade, please:
- run `php artisan vendor:publish --provider="Backpack\CRUD\BackpackServiceProvider" --tag=config` to publish the new config files (you'll find them as `config/backpack/operations/create.php` for example);
- change the values inside each file to match your values in `config/backpack/crud.php` under `operations`; 
- in your `config/backpack/crud.php` delete the `operations` array entirely;


<a name="step-12" href="#step-12" class="badge badge-warning text-white" style="text-decoration: none;">Step 13.</a> For the Create and Update operations, you were previously able to change what inputs are stripped from the request before saving, by configuring `saveAllInputsExcept` for that operation. We've:
- moved the configuration to `config/backpack/operations/create.php` & `config/backpack/operations/update.php`;
- renamed it to `strippedRequest`;
- expanded the functionality, by requiring you to give a closure instead of an array; 
- kept the default behaviour (safest - strip all inputs that don't have fields) if it's undefined or anything other than a closure;

If (and only if) in your `config/backpack/operations/create.php` or `config/backpack/operations/update.php` you've copied `saveAllInputsExcept` as an array (not `null` or `false`), you can now achieve the same thing by doing, for example:
```diff
-    'saveAllInputsExcept' => ['_token', '_method', 'http_referrer', 'current_tab', 'save_action'],
+    'strippedRequest' => (function ($request) {
+        return $request->except('_token', '_method', '_http_referrer', '_current_tab', '_save_action');
+    }),
```
But you can also do a lot more, because you have the `$request` in that closure. See more info in PR #[3987](https://github.com/Laravel-Backpack/CRUD/pull/3987). In addition, please notice that previously some hidden parameters were prefixed with underscore, some weren't. Now they're all prefixed, to differentiate them from actual database columns.

<a name="controllers"></a>
### CrudControllers

<a name="step-x" href="#step-x" class="badge badge-danger text-white" style="text-decoration: none;">Step x.</a> We have removed the **`simplemde` field**, since it the JS library hasn't received any updates since 2016. However, there is a drop-in replacement called `easymde` which is well maintained. If you're using the `simplemde` field anywhere in your project, please use `easymde` instead. A simple find & replace should do.

<a name="step-y" href="#step-y" class="badge badge-warning text-white" style="text-decoration: none;">Step y.</a> If you're using the **Reorder operation**, but have overriden some of its functionality, please take note that the operation itself and its blade file has had a small change, so that the information is now passed as JSON. Take a look at the [changes here](https://github.com/Laravel-Backpack/CRUD/pull/3808/files) and do them in your own custom code too.

<a name="step-y" href="#step-y" class="badge badge-danger text-white" style="text-decoration: none;">Step z.</a> The default behaviour for the **Show operation**, in Backpack 4.1, has been to guess which columns to show.** You could then create `setupShowOperation()` to add or modify columns, but you couldn't _remove_ columns, because the default logic would add them back. In Backpack 4.2 we've fixed this, by moving default setup logic inside a `setupShowOperation()` method. This means that _if you have a `setupShowOperation()` method in your CrudControllers_, the default behaviour will be _completely overwritten_ - so the columns will no longer be guessed, only what you have inside your own method will be run. **To keep the same behaviour as before (guess columns) but also do stuff _after that_, please rename your `setupShowOperation()` methods to `modifyShowOperation()`.** If it exists, that method will be run _after_ the columns are guessed, but _before_ columns that don't make sense inside Show are removed. More info in the [Show operation docs](/docs/{{version}}/crud-operation-show#modifyshowoperation).

<a name="step-y" href="#step-y" class="badge badge-warning text-white" style="text-decoration: none;">Step y.</a> The **Create and Update operations** no longer save the `request()`, they save your `ProductFormRequest` (the one that contains the validation). So... if you've modified the `request()` or `CRUD::getRequest()` in any of your CrudControllers (most likely to add or remove inputs), theose changes will never reach the db. To give you an example, if you've over overwritten the `store()` or `update()` methods to add an input, it might look something like this:
```php
use \Backpack\CRUD\app\Http\Controllers\Operations\CreateOperation { store as traitStore; }

public function store()
{
    $this->crud->setOperationSetting('saveAllInputsExcept', ['save_action', 'token', 'http_referrer']);
    $this->crud->getRequest()->request->add(['updated_by' => backpack_user()->id]);     
        
    return $this->traitStore();
}
```

That will no longer work. But you can easily change the request inside the `strippedRequest` config:

```php
public function store()
{
    $this->crud->set('strippedRequest', function($request) {
        $request->add([ 'updated_by' => backpack_user()->id ]);
        return $request->except(['_save_action', '_token', '_http_referrer']);
    });
        
    return $this->traitStore();
}
```

Or better yet, instead of overriding the `store()` or `update()` methods in your ProductCrudController just to add an input, you can now do something much cleaner. You can add that input inside your `ProductRequest`, for example:
```php

    protected function prepareForValidation()
    {
        \CRUD::setOperationSetting('strippedRequest', function ($request) {
            $input = $request->only(\CRUD::getAllFieldNames());
            $input['updated_by'] = backpack_user()->id;
            
            return $input;
        });
    }
```
Alternatively, if you want to keep the old behaviour, for ALL CRUDs, you can go to your `config/backpack/opeartions/create.php` and `config/backpack/opeartions/update.php`. That way, you can keep your old CrudController overrides:
```php
    'strippedRequest' => (function ($request) {
        return CRUD::getRequest()->request->except('_token', '_method', '_http_referrer', '_current_tab', '_save_action');
    }),
```


<a name="step-y" href="#step-y" class="badge badge-warning text-white" style="text-decoration: none;">Step y.</a> The `page_or_link` field has been moved from `backpack/crud` to `backpack/menucrud` because it made little sense outside it. If you've used the `page_or_link` field anywhere in your CrudControllers:
- if you have `MenuCRUD` installed: 
    - bump the NewsCRUD version in your `composer.json` (`"backpack/newscrud": "^4.0.3"`)
    - anywhere you've used the `page_or_link` field, make sure to specify its view_namespace (`'view_namespace' => 'menucrud::fields'`);
- if you DO NOT have `MenuCRUD` installed;
    -  tell us that this has affected you, in [this PR](https://github.com/Laravel-Backpack/CRUD/pull/3459);
    -  you can create a new file in your `resources/views/vendor/backpack/fields/page_or_link.blade.php` and paste [the content from here](https://raw.githubusercontent.com/Laravel-Backpack/MenuCRUD/e0afc7960d5513017ef847480f50591400bd4a6b/src/resources/views/fields/page_or_link.blade.php);


<a href="assets"></a>
### CSS & JS Assets

<a name="step-13" href="#step-13" class="badge badge-info text-white" style="text-decoration: none;">Step 13.</a> We've updated most CSS & JS dependencies to their latest versions. There are two ways to publish the latest styles and scripts for these dependencies:
- (A) If you have NOT touched you ```public/packages``` folder, or placed anything custom inside it:
        - delete the ```public/packages``` directory and all its contents;
        - run ```php artisan vendor:publish --provider="Backpack\CRUD\BackpackServiceProvider" --tag=public```
        - if you use elFinder, also delete ```resources/views/vendor/elfinder``` and run ```php artisan backpack:filemanager:install```
- (B) Run ```php artisan vendor:publish --provider="Backpack\CRUD\BackpackServiceProvider" --tag=public --force```. Please note this will overwrite anything that's already there. This B solution has a downside: unused files are not removed. A few files Backpack no longer uses will still be in your ```public/packages``` folder, even though they're no longer used.


<a name="step-14" href="#step-13" class="badge badge-danger text-white" style="text-decoration: none;">Step 13.</a> We've removed the custom CSS & JS files that Backpack provided for each operation (eg. `list.css` and `create.js` - [see why here](https://github.com/Laravel-Backpack/CRUD/pull/3942)).

- If you've added any custom code in `public/packages/backpack/crud/css` and `public/packages/backpack/crud/js`, copy them to a different location (we suggest `public/assets/admin/css` and `public/assets/admin/js`) use the brand-new `script` and `style` widgets to load them only where you need them. See the [updated docs section](/docs/{{version}}/crud-how-to#add-css-and-js-to-a-page-or-operation) for more information. This is only needed if YOU have added any custom code there. The Backpack CSS that was there is now included in the `bundle.css` and `bundle.js` files, so if you haven't modified those at all...
- It is now safe to delete the `public/packages/backpack/crud/css` and `public/packages/backpack/crud/js` directories - those files are no longer loaded.

<a name="views"></a>
### Views

<a name="step-14" href="#step-13" class="badge badge-secondary-soft" style="text-decoration: none;">Step 13.</a> If you've developed custom fields or columns that load CSS or JS, it's recommended you load them using `@loadOnce('path/to/file.css')` and `@loadOnce('path/to/file.js')` instead of `<link href="path/to/file.css>"` and `<script src="path/to/file.js></script>"`. This will make sure that piece of JS/CSS/code is only loaded once per pageload. You can find [more info about it here](https://github.com/digitallyhappy/assets) (and why it's more than `@once`).

<a name="step-14" href="#step-13" class="badge badge-warning text-white" style="text-decoration: none;">Step 13.</a> If you've overwritten any of the default operations in any way (blade files or PHP classes), take note that we've renamed the system GET/POST parameters - they're all prefixed by underscore now, to differentiate them from actual database columns. Please replace `http_referrer`, `locale`, `current_tab` with `_http_referrer`, `_locale`, `_current_tab`, respectively. [Take a look at the PR](https://github.com/Laravel-Backpack/CRUD/pull/3955/files) to see the affected files. In 99% of all cases you won't be affected by this, there's little reason to overwrite the default operations. This also applies if you've overridden the `SaveActions` or `form_content`.

<a name="security"></a>
### Security

<a name="step-17" href="#step-18" class="badge badge-danger text-white" style="text-decoration: none;">Step 17.</a> By default, all columns now echo using `{{ }}` instead of `{!! !!}` (aka. they "_escape the output_", assuming they contain strings, not HTML). This was done to increase _default security_, to protect the admin from any malicious strings that might have been stored in the database. There are two exceptions to this, two columns that are not `escaped` by default: `custom_html` and `markdown`, where Backpack assumes you store HTML. To upgrade:
- If you've been showing HTML using the `array`, `array_count`, `closure`, `model_function`, `model_function_attribute`, `relationship_count` or `textarea` columns, you can use `'escaped' => false` on those columns to go back to the previous behaviour. But please [read more about this](/docs/{{version}}/crud-columns#escape-column-output), it might be a good idea to sanitize your input/output if you've forgotten to do so.
- If you're using the `markdown` or `custom_html` columns, please note that they still DO NOT escape the output by default (since they most likely store HTML); make sure you've properly sanitized your input or output - it's super-easy using an [HTML Purifier package](https://github.com/mewebstudio/Purifier) (you can do that by casting the attribute to `CleanHtmlOutput::class` in your Model or [manually](https://github.com/Laravel-Backpack/demo/commit/7342cffb418bb568b9e4ee279859685ddc0456c1));


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
- start using the [new features in Backpack 4.2](/docs/4.2/release-notes);
