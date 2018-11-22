# Upgrade Guide

---

This will guide you through upgrading from Backpack 3.4 to 3.5. For upgrading from 3.3 to 3.4 [check out the previous upgrade guide](https://backpackforlaravel.com/docs/3.4/upgrade-guide).

<a name="requirements"></a>
## Requirements
- Laravel 5.6+
- PHP 7.1.3+
- 10-30 minutes

<a name="breaking-changes"></a>
## Breaking Changes

// TODO

<a name="upgraade-steps"></a>
## Upgrade Steps

<a name="require-the-lastest-versions"></a>
### Require the latest versions
1. Run the commands below:

```bash
composer require backpack/base:"^1.0.0"
composer require backpack/crud:"^3.5.0"
```

Alternative: make the version changes above in your ```composer.json```, then and run ```composer update```.

<a name="upgrade-backpack-base-to-1-0-x"></a>
### Upgrade Backpack\Base to 1.0.x 

1. After you upgrade to Laravel 5.6 or 5.7, make sure you have ```\Illuminate\Foundation\Http\Middleware\ConvertEmptyStringsToNull::class,``` in your ```app/Http/Kernel.php```. Backpack\CRUD no longer converts empty strings to null, since Laravel 5.6 and 5.7 installations do that by default. Alternatively, if you can't place it there, you can:
- add it to your ```config/backpack/base.php```, under ```middleware_class```;
- (OR) add it directly to your admin panel routes, in ```routes/backpack/custom.php```;

2. Make sure your ```config/backpack/base.php``` file has:
```php
    // The guard that protects the Backpack admin panel.
    // If null, the config.auth.defaults.guard value will be used.
    'guard' => 'backpack',

    // The password reset configuration for Backpack.
    // If null, the config.auth.defaults.passwords value will be used.
    'passwords' => 'backpack',
```

3. Make sure you're using ```backpack_auth()``` instead of ```Auth``` in all custom files in the admin panel. For example, check all your Request files (ex: ```app/Http/Requests/Monster.php```) and make sure all your ```authorize()``` methods return ```backpack_auth()``` instead of ```Auth::check()```.

```diff
    /**
     * Determine if the user is authorized to make this request.
     *
     * @return bool
     */
    public function authorize()
    {
        // only allow updates if the user is logged in
-        return \Auth::check();
+        return backpack_auth()->check();
    }
```

Search any custom files you use in your admin panels for ```Auth::```. You _might have_ already done this when you upgraded to 0.9.0, but this wasn't properly stressed in the upgrade guide, so please make sure.

4. If you haven’t created any custom **error views**, re-publish the ```resources/views/errors``` folder. Please note that this will delete your existing folder. 

```bash
php artisan vendor:publish --provider="Backpack\Base\BaseServiceProvider" --tag=errors --force
```

If you don't like how these new Backpack error views look, and would rather just use Laravel's, just remove that folder entirely. Do keep in mind that Laravel does NOT provide templates for some common errors (like 503), so it will show ```Whoops``` to your end-users instead. Our recommendation - use Backpack's error views.

```bash
# OPTIONAL! see notice above
rm resources/views/errors
```

5. We've changed the CSS quite a bit, and its folder structure. If you haven't overwritten backpack's CSS or JS, please republish it using 
```bash
php artisan vendor:publish --provider="Backpack\Base\BaseServiceProvider" --tag=public --force
```

Then delete the old CSS file you're no longer going to use:
```bash
rm public/vendor/backpack/backpack.base.css
```

And change the overlays path in your ```config/backpack/base.php``` file (```overlays``` has been renamed to ```base```):

```php
    // Overlays - CSS files that change the look and feel of the admin panel
    'overlays' => [
        'vendor/backpack/base/backpack.bold.css',
        // 'vendor/backpack/base/backpack.content.is.king.css', // opinionized borderless alternative
    ],
```

5. **ALL Backpack/Base views have suffered some changes**. If you’ve published/customized/overwritten any of the Backpack/Base views, please [take a look at the changes](https://github.com/Laravel-Backpack/Base/pull/324/files) and implement them in your file. To rephrase this:
- if you have files in you ```resources/views/vendor/backpack/base/``` it means you're actually using _that_ file, instead of the new one provided by upgrading to Base 1.0.0;
- you need to either (A) delete that file and forfeit any changes you've made (Backpack will pick up the new one) OR (B) apply the changes yourself

The non-trivial changes in views are that:
- ```layout.blade.php``` has been split into multiple files, so that it can be overwritten less often (```head```, ```scripts```);
- in ```inc/menu.blade.php``` we're now loading ```inc/topbar_left_content.blade.php``` and ```inc/topbar_right_content.blade.php```, so that developers can specify additional content for the top menu; [details here](https://github.com/Laravel-Backpack/Base/pull/302);

6. If you've had workarounds in place to use separate sessions for Users and Admins, that's no longer needed - **separate sessions are a default in Base 1.0.0**.

7. If you use the Backpack authentication for your users to login as well, nothing changes for you. You're good.

<a name="upgrade-backpack-crud-to-3-5-x"></a>
### Upgrade Backpack\CRUD to 3.5.x

2. 
