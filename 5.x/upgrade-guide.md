# Upgrade Guide

---

This will guide you to upgrade from Backpack 4.1 to v5. The steps are color-coded by the probability that you will need it for your application: <span class="badge badge-danger text-white" style="text-decoration: none;">High</span>, <span class="badge badge-warning text-white" style="text-decoration: none;">Medium</span> and <span class="badge badge-secondary-soft" style="text-decoration: none;">Low</span>.

<a name="requirements"></a>
## Requirements

Please make sure your project respects the requirements below, before you start the upgrade process. You can check with ```php artisan backpack:version```:

- PHP 8.x, 7.4 or 7.3
- Laravel 9.x or 8.x
- Backpack\CRUD 4.1.x
- 10-15 minutes (for most projects)

**If you're running Backpack version 3.x or 4.0, please follow ALL other upgrade guides first, to incrementally get to use Backpack 4.1**. Test that your app works well with each version, after each upgrade. Only _afterwards_ can you follow this guide, to upgrade from 4.1 to v5. Previous upgrade guides:
- [upgrade from 4.0 to 4.1](https://backpackforlaravel.com/docs/4.1/upgrade-guide);
- [upgrade from 3.6 to 4.0](https://backpackforlaravel.com/docs/4.0/upgrade-guide);
- [upgrade from 3.5 to 3.6](https://backpackforlaravel.com/docs/3.6/upgrade-guide);
- [upgrade from 3.4 to 3.5](https://backpackforlaravel.com/docs/3.5/upgrade-guide);
- [upgrade from 3.3 to 3.4](https://backpackforlaravel.com/docs/3.4/upgrade-guide);

<a name="upgraade-steps"></a>
## Upgrade Steps

The upgrade guide might seem long or intimidating, but really, it's an easy upgrade. **Most projects will only be affected by a few of the breaking changes outlined below**. And when there are changes needed, they're pretty small.

Please **go thorough all steps**, to ensure a smooth upgrade process. The steps are color-coded by how likely we think that step is needed for your project: <span class="badge badge-danger text-white" style="text-decoration: none;">High</span>, <span class="badge badge-warning text-white" style="text-decoration: none;">Medium</span> and <span class="badge badge-secondary-soft" style="text-decoration: none;">Low</span>. **At the very least, read what's in bold**.

<br>


<a name="step-0" href="#step-0" class="badge badge-danger" style="text-decoration: none;">Step 0.</a> **[Upgrade to Laravel 8](https://laravel.com/docs/8.x/upgrade) or [Laravel 9](https://laravel.com/docs/9.x/upgrade), then test to confirm your app is working fine.**

<a name="composer"></a>
### Composer

<a name="step-1" href="#step-1" class="badge badge-danger text-white" style="text-decoration: none;">Step 1.</a> Update your ```composer.json``` file to require:

```
        "backpack/crud": "^5.0.0",
        "backpack/pro": "^1.0.0",
```

These two packages together will help you have all the features in v4.1 and more. But since `backpack/pro` is a closed-source package, to download it, you need to generate [your token and password here](https://backpackforlaravel.com/user/tokens). If no button is there for you, it means you don't have free access to `backpack/pro`, so you'll need to [purchase it](https://backpackforlaravel.com/pricing). **[Follow the 2-step process called "Instructions" in your token](https://backpackforlaravel.com/user/tokens), if you haven't already done that on this project.**


<a name="step-2" href="#step-2" class="badge badge-warning text-white" style="text-decoration: none;">Step 2.</a> If you have first-party addons installed (eg. Backpack\PermissionManager), there's nothing you need to do. They all work without a version bump. However, if you have third-party Backpack add-ons installed, you might want to bump their versions - please check each addon's page.

<a name="step-3" href="#step-3" class="badge badge-danger text-white" style="text-decoration: none;">Step 3.</a> Run ```composer update``` in the command line.

<a name="models"></a>
### Models

<a name="step-4" href="#step-4" class="badge badge-warning text-white" style="text-decoration: none;">Step 4.</a> The `repeatable` field has been completely rewritten, in order to work with values as a nested PHP array, not a JSON. This has HUGE benefits, but it does present some small breaking changes. **If you've used the `repeatable` field in your CRUDs**
- please make sure that db column is cast as `array` or `json` (eg. add `protected $casts = ['testimonials' => 'array'];` to your model);
- a data syntax bug has been fixed - previously, if inside `repeatable` you used a subfield with multiple values (`select_multiple` or `select2_multiple` etc.), it would not store `'categories': [1, 2]` like you'd expect, but `'categories[]': [1, 2]`; those brackets were not intentional, but we could not fix them without telling you about  it; so, when upgrading to v5:
    - if you've coded a workaround to strip those brackets, you can now remove the workaround;
    - if you use the attribute with brackets anywhere, please expect it to be either _with_ or _without_ brackets; Backpack will NOT strip all the brackets automatically, it will only strip them upon saving, when an admin edits that particular entry;

<a name="form-requests"></a>
### Form Requests

<a name="step-5" href="#step-5" class="badge badge-warning text-white" style="text-decoration: none;">Step 5.</a> **For `repeatable` fields, you no longer have to create custom validation logic. You can now use Laravel's [nested array validation](https://laravel.com/docs/validation#validating-nested-array-input).** If you've used a `repeatable` in your CRUDs and validated it in your FormRequest:
- you can keep your custom validation logic, but make sure you no longer `json_decode()` the value in the input; if you've used our validation example, that means instead of `$fieldGroups = json_decode($value);` you should do `$fieldGroups = is_array($value) ? $value : [];`;
- you can rewrite that validation in a cleaner and more concise way, using [Laravel's nested array input validation rules](https://laravel.com/docs/validation#validating-nested-array-input); for example, you can do `'testimonial.*.name' => 'required|min:5|max:256'` to validate all `name` subfields inside a repeatable `testimonial`;

<a name="routes"></a>
### Routes

No changes needed.

<a name="config"></a>
### Config

<a name="step-6" href="#step-6" class="badge badge-secondary-soft" style="text-decoration: none;">Step 6.</a> Security improvement: Starting with v5, Backpack will throttle password request attempts, so that a malicious actor cannot use the "reset password" functionality to send unsolicited emails. We recommend you add this configuration entry to your `config/backpack/base.php` (before "_Authentication_"). See [#3862](https://github.com/Laravel-Backpack/CRUD/pull/3862) for details.

```php
    /*
    |--------------------------------------------------------------------------
    | Security
    |--------------------------------------------------------------------------
    */

    // Backpack will prevent visitors from requesting password recovery too many times
    // for a certain email, to make sure they cannot be spammed that way.
    // How many seconds should a visitor wait, after they've requested a
    // password reset, before they can try again for the same email?
    'password_recovery_throttle_notifications' => 600, // time in seconds

    // Backpack will prevent an IP from trying to reset the password too many times,
    // so that a malicious actor cannot try too many emails, too see if they have
    // accounts or to increase the AWS/SendGrid/etc bill.
    //
    // How many times in any given time period should the user be allowed to
    // attempt a password reset? Take into account that user might wrongly
    // type an email at first, so at least allow one more try.
    // Defaults to 3,10 - 3 times in 10 minutes.
    'password_recovery_throttle_access' => '3,10',
```

----


<a name="step-7" href="#step-7" class="badge badge-danger text-white" style="text-decoration: none;">Step 7.</a> **Operation configurations have been moved from `config/backpack/crud.php`, each to its own file.** That way, if an operation config value isn't present, it will fall back to Backpack's default value. To upgrade, please:
- run `php artisan vendor:publish --provider="Backpack\CRUD\BackpackServiceProvider" --tag=config` - this will publish a new config file for each operation (eg. `config/backpack/operations/create.php`, `update.php` etc.);
- if you've changed things inside your `config/backpack/crud.php`, then make the same changes inside the config files you've published above;
- in your `config/backpack/crud.php` delete the `operations` array entirely;

----

<a name="step-8" href="#step-8" class="badge badge-secondary-soft" style="text-decoration: none;">Step 8.</a> Inside `config/backpack/crud.php`, you were previously able to change what inputs are stripped from the request before saving, by configuring `saveAllInputsExcept` for the Create and Update operations. We have moved the configuration to `config/backpack/operations/create.php` & `config/backpack/operations/update.php`, then:
- renamed it to `strippedRequest`;
- given you the possibility to do whatever you want to the request, by using a `closure` instead of `array`;
- kept the default behaviour; if `strippedRequest` is undefined or false, Backpack will strip all inputs that don't have fields, which we consider is the safest approach;

**If in your `config/backpack/operations/create.php` or `config/backpack/operations/update.php` you've copied `saveAllInputsExcept` as `null` or `false`, you don't have to do anything.**

However, **if you have an array for your `saveAllInputsExcept`**, you can now achieve the same thing by stripping the request yourself in a closure. Basically:
```diff
-    'saveAllInputsExcept' => ['_token', '_method', 'http_referrer', 'current_tab', 'save_action'],
+    'strippedRequest' => (function ($request) {
+        return $request->except('_token', '_method', '_http_referrer', '_current_tab', '_save_action');
+    }),
```
But you can also do a lot more, because you have the `$request` in that closure. See more info in PR #[3987](https://github.com/Laravel-Backpack/CRUD/pull/3987). In addition, please notice that **all hidden parameters are now prefixed by an underscore**. Starting with v5, if it starts with an underscore, you know it's not an actual database column.


----


<a name="step-9" href="#step-9" class="badge badge-secondary-soft" style="text-decoration: none;">Step 9.</a> The **Show operation** will now show `created_at`, `updated_at` columns by default, if they exist. In most cases, this is what you want - show as many things as possible inside the Show operation - and `created_at` and `updated_at` provide some useful information about the entry. So it should NOT negatively affect you. But if you _don't_ want to show those columns, you can turn off this new default behavior in your `config/backpack/operations/show.php`, by defining `'timestamps' => false,`. You can also do `'softDeletes' => true,` if you want to show that column, by the way.

<a name="controllers"></a>
### CrudControllers

<a name="step-10" href="#step-10" class="badge badge-secondary-soft" style="text-decoration: none;">Step 10.</a> We've improved the guessing of column types, by also taking into consideration your Model casts ([PR here](https://github.com/Laravel-Backpack/CRUD/pull/3618)). If you have places in your CrudControllers where you have NOT defined a column type (and just assumed it'll probably be `text`), but that item is cast as `date`, `json`, `array` etc... Backpack will now try to show a more appropriate column type for it, instead of `text`. This is unlikely to affect you negatively, but... it's not a terrible idea to go through all your ListOperation and ShowOperation views, to make sure you're happy with what's displayed.

----

<a name="step-11" href="#step-11" class="badge badge-secondary-soft" style="text-decoration: none;">Step 11.</a> There have been some **changes in how the `repeatable` field works** by default:
- it now shows no rows when empty (previously it was showing one); we believe that provides a better UX for most projects, but if you don't like it, please define `'min_rows' => 1`;
- we're renamed the `fields` attribute to `subfields` for more clarity;
- if you have subfields that do NOT have their type defined, Backpack will now assume you wanted a `text` field;
- we've fixed a bug in the unofficial "_subfield type guessing_" functionality: previously, if you added a subfield with the name `category` (for example), and your main entity happened to have a `category` relationship on it, then the repeatable field would show a relationship field, which is probably not what you wanted;

----

<a name="step-12" href="#step-12" class="badge badge-danger text-white" style="text-decoration: none;">Step 12.</a> We have **removed the `simplemde` field**, since that JS library hasn't received any updates since 2016. However, there is a drop-in replacement called `easymde` which is well maintained. If you're using the `simplemde` field type anywhere in your project, please use `easymde` instead. A simple find & replace in your Controllers should do.

----

<a name="step-13" href="#step-13" class="badge badge-secondary-soft" style="text-decoration: none;">Step 13.</a> If you're using the **Reorder operation** _and_ have overridden some of its functionality in your CrudController, please take note that the information is now passed as JSON. Replicate the [small changes here](https://github.com/Laravel-Backpack/CRUD/pull/3808/files) in your custom code too.

----

<a name="step-14" href="#step-14" class="badge badge-danger text-white" style="text-decoration: none;">Step 14.</a> When setting up your **Show operation** in Backpack 4.1, after your `setupShowOperation()` was run, Backpack would try to add more columns. That made it very difficult to delete auto-added columns. You might have used a workaround for this.

Starting with Backpack v5, the operation will no longer do any "_automatic setup_" inside `setupShowOperation()`... nor after it. Instead, if you want Backpack to guess column names, you have to manually call a new method, `$this->autoSetupShowOperation()`. To upgrade:
- if you _do not_ have a `setupShowOperation()` in your CrudControllers, you are not affected by this - don't worry;
- if you _do_ have a `setupShowOperation()` method in your CrudControllers:
    - **to keep the same behaviour as before (guess columns), please run `$this->autoSetupShowOperation();` wherever you want inside your `setupShowOperation()`;** add it to the end of your `setupShowOperation()` to keep the same behaviour as before;
    - if you have any workarounds for the problem mentioned above (difficult to delete columns in `setupShowOperation()`), call `$this->autoSetupShowOperation();` at the start of your `setupShowOperation()` call and eliminate your workarounds, it should now "_just work_";

----

<a name="step-15" href="#step-15" class="badge badge-warning text-white" style="text-decoration: none;">Step 15.</a> The **Create and Update operations** no longer save the `request()`, they save your `ProductFormRequest` (the one that contains the validation). **If you have NOT modified the `request()` or `CRUD::getRequest()` in any of your CrudControllers, you will not be affected by this, move on.**

However, **if you _have_ modified the request (most likely to add or remove inputs)**, those changes will never reach the database now. To give you an example, if you've ever overridden the `store()` or `update()` methods to add an input, it might look something like this:
```php
use \Backpack\CRUD\app\Http\Controllers\Operations\CreateOperation { store as traitStore; }

public function store()
{
    $this->crud->setOperationSetting('saveAllInputsExcept', ['save_action', 'token', 'http_referrer']);
    $this->crud->getRequest()->request->add(['updated_by' => backpack_user()->id]);     
        
    return $this->traitStore();
}
```

That will no longer work, because Backpack won't save the `request()` or `$this->crud->getRequest()` any more, but your `ProductRequest`. But there are now more ways than ever to achieve the same thing. Please read all solutions below and decide which one is best for you:

**Solution (A)**

That actually means it's easier to change the request now inside a CrudController, but you need to do it inside the `strippedRequest` closure:

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

**Solution (B)**

If you feel overriding the `store()` or `update()` methods was messy... you're not alone. We have good news for you, you can now move that logic to your `ProductRequest`, for example inside the lesser-known `prepareForValidation()` method:
```php
// app/Http/Requests/ProductRequest.php

    protected function prepareForValidation()
    {
        \CRUD::setOperationSetting('strippedRequest', function ($request) {
            $input = $request->only(\CRUD::getAllFieldNames());
            $input['updated_by'] = backpack_user()->id;
            
            return $input;
        });
    }
```

**Solution (C)**

Alternatively... if you absolutely _hate_ this new behaviour and want your previous code to continue working, you can now easily tell Backpack to save the `CRUD::getRequest()`, for all CRUDs, in your `config/backpack/operations/create.php` and `config/backpack/operations/update.php`. That way, you can keep your old CrudController overrides:
```php
    'strippedRequest' => (function ($request) {
        return CRUD::getRequest()->request->except('_token', '_method', '_http_referrer', '_current_tab', '_save_action');
    }),
```


----

<a name="step-16" href="#step-16" class="badge badge-secondary-soft" style="text-decoration: none;">Step 16.</a> If you've customized the saving process of the Create or Update operations (read: you've overridden the `store()` or `update()` methods), please take into consideration that starting with Backpack v5, **when a select multiple is emptied, it will still be part of the request, as `null`**. Whereas previously (if emptied) it was missing entirely. This applies to all `select` and `select2` fields when used as `multiple`. You might need to change your saving logic accordingly, instead of expecting them to be missing, to expect them to be `null`.

----


<a name="step-17" href="#step-17" class="badge badge-secondary-soft" style="text-decoration: none;">Step 17.</a> The `page_or_link` field has been moved from `backpack/crud` to `backpack/menucrud` because it made little sense outside it. If you've used the `page_or_link` field anywhere in your CrudControllers:
- if you have `MenuCRUD` installed: 
    - bump the MenuCRUD version in your `composer.json` (`"backpack/menucrud": "^3.0.0"`)
    - anywhere you've used the `page_or_link` field, make sure to specify its view_namespace (`'view_namespace' => 'menucrud::fields'`);
- if you DO NOT have `MenuCRUD` installed;
    -  tell us that this has affected you, in [this PR](https://github.com/Laravel-Backpack/CRUD/pull/3459);
    -  you can create a new file in your `resources/views/vendor/backpack/fields/page_or_link.blade.php` and paste [the content from here](https://raw.githubusercontent.com/Laravel-Backpack/MenuCRUD/e0afc7960d5513017ef847480f50591400bd4a6b/src/resources/views/fields/page_or_link.blade.php);


<a href="assets"></a>
### CSS & JS Assets

<a name="step-18" href="#step-18" class="badge badge-danger text-white" style="text-decoration: none;">Step 18.</a> We've removed the custom CSS & JS files that Backpack provided for each operation (eg. `list.css` and `create.js` - [see why here](https://github.com/Laravel-Backpack/CRUD/pull/3942)).

- If you've added any custom code in `public/packages/backpack/crud/css` and `public/packages/backpack/crud/js`, copy them to a different location (we suggest `public/assets/admin/css` and `public/assets/admin/js`). Then use the brand-new `script` and `style` widgets to load them only where you need them. See the [updated docs section](/docs/{{version}}/crud-how-to#add-css-and-js-to-a-page-or-operation) for more information. This is only needed if YOU have added any custom code there. The Backpack CSS that was there is now included in the `bundle.css` and `bundle.js` files.
- if you haven't modified those at all... it is now safe to delete the `public/packages/backpack/crud/css` and `public/packages/backpack/crud/js` directories - those files are no longer loaded.

----

<a name="step-19" href="#step-19" class="badge badge-danger text-white" style="text-decoration: none;">Step 19.</a> We've updated a lot of CSS & JS dependencies to their latest versions - with one notable exception - Bootstrap. Since not all dependencies support Bootstrap 5 yet, we'll still be using Bootstrap 4 for a while. But as soon as that changes, we'll release a new version for that upgrade alone. This will also make it easier to upgrade - no worries that the interface breaks now, since we haven't upgraded Bootstrap. And thanks to our new business model - you'll most likely have access to the next Backpack version too.

There are two ways to publish the latest styles and scripts for these dependencies:
- (A) If you have NOT touched you ```public/packages``` folder, or placed anything custom inside it:
        - delete the ```public/packages``` directory and all its contents;
        - run ```php artisan vendor:publish --provider="Backpack\CRUD\BackpackServiceProvider" --tag=public```
        - if you use elFinder, also delete ```resources/views/vendor/elfinder``` and run ```php artisan backpack:filemanager:install```
- (B) Run ```php artisan vendor:publish --provider="Backpack\CRUD\BackpackServiceProvider" --tag=public --force```. Please note this will overwrite anything that's already there. This B solution has a downside: unused files are not removed. A few files Backpack no longer uses will still be in your ```public/packages``` folder, even though they're no longer used.

<a name="views"></a>
### Views

<a name="step-20" href="#step-20" class="badge badge-secondary-soft" style="text-decoration: none;">Step 20.</a> **Have you developed any custom fields or columns?** Rephrased: do you have anything inside your `resources/views/vendor/backpack/crud/fields` or `resources/views/vendor/backpack/crud/columns`? If so, and those fields or columns load any external CSS or JS, we recommended you load them using `@loadOnce('path/to/file.css')` and `@loadOnce('path/to/file.js')` instead of `<link href="path/to/file.css">` and `<script src="path/to/file.js"></script>`. This will make sure that piece of JS/CSS/code is only loaded once per pageload. You can find [more info about it here](https://github.com/digitallyhappy/assets) (and why it's more than `@once`). Your custom fields should still work without this change, but it's such an easy one.

----

<a name="step-21" href="#step-21" class="badge badge-secondary-soft" style="text-decoration: none;">Step 21.</a> If you've overwritten any of the default operations in any way (blade files or PHP classes), take note that we've renamed the system GET/POST parameters (aka hidden inputs) - they're all prefixed by underscore now, to differentiate them from actual database columns. Please replace `http_referrer`, `locale`, `current_tab` with `_http_referrer`, `_locale`, `_current_tab`, respectively. [Take a look at the PR](https://github.com/Laravel-Backpack/CRUD/pull/3955/files) to see the affected files. In 99% of all cases you won't be affected by this, there's little reason to overwrite the default operations. This also applies if you've overridden the `SaveActions` or `form_content`.

<a name="security"></a>
### Security

<a name="step-22" href="#step-22" class="badge badge-danger" style="text-decoration: none;">Step 22.</a> By default, all columns now echo using `{{ }}` instead of `{!! !!}`. That means they "_escape the output_", assuming they contain strings, not HTML. This was done to increase _default security_, to protect the admin from any malicious strings that might have been stored in the database. There are two exceptions to this, two columns that are not `escaped` by default: `custom_html` and `markdown`, where Backpack assumes you store HTML. To upgrade:
- If you've been showing HTML using the `array`, `array_count`, `closure`, `model_function`, `model_function_attribute`, `relationship_count` or `textarea` columns, you can use `'escaped' => false` on those columns to go back to the previous behaviour. But please [read more about this](/docs/{{version}}/crud-columns#escape-column-output), it might be a good idea to sanitize your input/output if you've forgotten to do so.
- If you're using the `markdown` or `custom_html` columns, please note that they still DO NOT escape the output by default (since they most likely store HTML); make sure you've properly sanitized your input or output - it's super-easy using an [HTML Purifier package](https://github.com/mewebstudio/Purifier) (you can do that by casting the attribute to `CleanHtmlOutput::class` in your Model or [manually](https://github.com/Laravel-Backpack/demo/commit/7342cffb418bb568b9e4ee279859685ddc0456c1));


<a name="cache"></a>
### Cache

<a name="step-23" href="#step-23" class="badge badge-danger text-white" style="text-decoration: none;">Step 23.</a> Clear your app's cache:
```
php artisan config:clear
php artisan cache:clear
php artisan view:clear
```

---

**You're done! Good job.** Thank you for taking the time to upgrade. Now you can:
- thoroughly test your application and your admin panel;
- start using the [new features in Backpack v5](/docs/{{version}}/release-notes);
