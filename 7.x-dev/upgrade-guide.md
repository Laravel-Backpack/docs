# Upgrade Guide

---

This will guide you to upgrade from Backpack v6 to v7. The steps are color-coded by the probability that you will need it for your application: <span class="badge badge-danger text-white" style="text-decoration: none;">High</span>, <span class="badge badge-warning text-white" style="text-decoration: none;">Medium</span> and <span class="badge badge-secondary-soft" style="text-decoration: none;">Low</span>.  **At the very least, please read what's in bold**.

> IMPORTANT NOTE: This upgrade guide is NOT AT ALL final. We do not recommend you upgrade your existing project to v7. We're working on making the upgrade process easier for you. We will let you know when this changes.

<a name="requirements"></a>
## Requirements

Please make sure your project respects the requirements below, before you start the upgrade process. You can check with ```php artisan backpack:version```:

- PHP 8.1+
- Laravel 11.x
- Backpack\CRUD 6.x
- 5-10 minutes (for most projects)

**If you're running Backpack version 3.x-5.x, please follow ALL other upgrade guides first, to incrementally get to use Backpack v6**. Test that your app works well with each version, after each upgrade. Only _afterwards_ can you follow this guide, to upgrade from v5 to v6. Previous upgrade guides:
- [upgrade from 5.x to 6.x](https://backpackforlaravel.com/docs/6.x/upgrade-guide);
- [upgrade from 4.1 to 5.x](https://backpackforlaravel.com/docs/5.x/upgrade-guide);
- [upgrade from 4.0 to 4.1](https://backpackforlaravel.com/docs/4.1/upgrade-guide);
- [upgrade from 3.6 to 4.0](https://backpackforlaravel.com/docs/4.0/upgrade-guide);
- [upgrade from 3.5 to 3.6](https://backpackforlaravel.com/docs/3.6/upgrade-guide);
- [upgrade from 3.4 to 3.5](https://backpackforlaravel.com/docs/3.5/upgrade-guide);
- [upgrade from 3.3 to 3.4](https://backpackforlaravel.com/docs/3.4/upgrade-guide);

<a name="upgraade-steps"></a>
## Upgrade Steps

<a name="step-0" href="#step-0" class="badge badge-danger" style="text-decoration: none;">Step 0.</a> **[Upgrade to Laravel 12](https://laravel.com/docs/12.x/upgrade) if you don't use it yet, then test to confirm your app is working fine.**

<a name="composer"></a>
### Composer

<a name="step-1" href="#step-1" class="badge badge-danger text-white" style="text-decoration: none;">Step 1.</a> Update your ```composer.json``` file to require:

```
        "backpack/crud": "^7.0.0-beta",
```

<a name="step-2" href="#step-2" class="badge badge-danger text-white" style="text-decoration: none;">Step 2.</a> Bump the version of any first-party Backpack add-ons you have installed (eg. `backpack/pro`, `backpack/editable-columns` etc.) to the versions that support Backpack v6. For 3rd-party add-ons, please check each add-on's Github page. Here's a quick list of 1st party packages and versions:

```js
        "backpack/crud": "^7.0.0-beta",
        "backpack/pro": "^3.0@dev",
        "backpack/filemanager": "dev-next",
        "backpack/theme-coreuiv2": "dev-next",
        "backpack/theme-coreuiv4": "dev-next",
        "backpack/theme-tabler": "dev-next",
        "backpack/logmanager": "dev-next",
        "backpack/settings": "dev-next",
        "backpack/newscrud": "dev-next",
        "backpack/permissionmanager": "dev-next",
        "backpack/pagemanager": "dev-next",
        "backpack/menucrud": "dev-next",
        "backpack/backupmanager": "dev-next",
        "backpack/editable-columns": "dev-next",
        "backpack/revise-operation": "dev-next",
        "backpack/medialibrary-uploaders": "dev-next",
        "backpack/devtools": "dev-next",
        "backpack/generators": "dev-next",
```

> **Note:** To install Backpack v7 beta and its add-ons, set `"minimum-stability": "beta"` in your `composer.json`.

<a name="step-3" href="#step-3" class="badge badge-danger text-white" style="text-decoration: none;">Step 3.</a> Let's get the latest Backpack and install it. If you get any conflicts with **Backpack 1st party add-ons**, most of the time you just need to move one version up, eg: from `backpack/menucrud: ^3.0` to `backpack/menucrud: ^4.0`. See the step above again. Please run:

```bash
composer update

# before calling the next command make sure you have no pending migrations with `php artisan migrate:status`
# and that your webserver is running and accessible in the URL you defined in .env `APP_URL`.
php artisan backpack:upgrade
```

// TODO: actually create an upgrade command that does only the things we need from the `install` command.

<a name="models"></a>
### Models

No changes needed.

<a name="form-requests"></a>
### Form Requests

No changes needed.

<a name="routes"></a>
### Routes

No changes needed.

<a name="config"></a>
### Config

**Theme Tabler** - The default layout for theme tabler has changed. If you had the tabler config published you are good to go. **In case you don't have the tabler theme config published** and want to keep the old layout, you should publish it by running `php artisan vendor:publish --tag="theme-tabler-config"` and changing:
```diff
- 'layout' => 'horizontal',
+ 'layout' => 'horizontal_overlap',
```
You should also remove the glass skin and fuzzy background from the theme styles:
```diff
'styles' => [
        base_path('vendor/backpack/theme-tabler/resources/assets/css/skins/backpack-color-palette.css'),
-        base_path('vendor/backpack/theme-tabler/resources/assets/css/skins/glass.css'),
-        base_path('vendor/backpack/theme-tabler/resources/assets/css/skins/fuzzy-background.css'),
    ],
```

<a name="controllers"></a>
### CrudControllers

<a name="step-x" href="#step-x" class="badge badge-danger text-white" style="text-decoration: none;">Step X.</a> The `wysiwyg` field and column have been removed, because they were hiding functionality. But don't worry, they were just alias columns. Behind the scenes, they were just loading the `ckeditor` field and `text` column respectively. If you're using the `wysiwyg` column of field in your CrudController, replace them with their originals.

<a name="step-x" href="#step-x" class="badge badge-danger text-white" style="text-decoration: none;">Step X.</a> The `ckeditor` field and column have been moved from the PRO package to an open-source addon. If you are using either of them and want to keep doing so, just run `composer require backpack/ckeditor-field`. Please note that CKEditor is a 3rd party JS library that requires _payment_ when used for commercial purposes (GPL licensed). See their [pricing page](https://ckeditor.com/pricing/) for options. We can give away a few sub-licenses every year - [contact us](https://backpackforlaravel.com/contact) to see if any are still available. If you would rather move away from CKEditor, you can just change your field/column to use `summernote` instead, they are both HTML editors so your content should be compatible. Summernote has more limited editing options, but it's MIT-licensed.

<a name="step-x" href="#step-x" class="badge badge-danger text-white" style="text-decoration: none;">Step X.</a> Similarly, the `tinymce` field and column have been moved from the PRO package to an open-source addon. If you are using either of them and want to keep doing so, just run `composer require backpack/tinymce-field`. Please note that TinyMCE is a 3rd party JS library that normally requires _payment_ when used for commercial purposes (GLP licensed). See their [pricing page](https://www.tiny.cloud/pricing/) for options. You can sign-up for [free commercial licenses on their website here](https://www.tiny.cloud/get-tiny/). If you would rather move away from TinyMCE, you can just change your field/column to use `summernote` instead, they are both HTML editors so your content should be compatible. Summernote has more limited editing options, but it's MIT-licensed.


<a href="assets"></a>
### CSS & JS Assets



<a name="views"></a>
### Views

**List Operation View** - The List Operation view got a huge change. We decoupled the datatable from the view, so that you can use the table anywhere you would like.
Most of the code is still identical but moved to `datatable.blade.php`. The `list.blade.php` view now only includes the mentioned datatable component.

If you had customized the `list.blade.php` you should move your customizations to `datatable.blade.php`.

<a name="security"></a>
### Security

No changes needed.

<a name="cache"></a>
### Cache

<a name="step-Xx" href="#step-xx" class="badge badge-danger text-white" style="text-decoration: none;">Step xx.</a> Clear your app's cache:
```
php artisan basset:clear
php artisan config:clear
php artisan cache:clear
php artisan view:clear
```

If the table view still looks wonky (search bar out of place, big + instead of ellipsis), then do a hard-reload in your browser (Cmd+Shift+R or Ctrl+Shift+F5) to purge the browser cache too.

---

<a name="step-yy" href="#step-yy" class="badge badge-danger text-white" style="text-decoration: none;">Step yy.</a> If your pages are slow to load, that's because Basset caching the assets as you load the pages, so your first pageload will be quite slow. If you find that annoying, run `php artisan basset:cache` to cache all CSS and JS assets. Alternatively, if you want Basset NOT to run because you're making changes to CSS and JS files, you can add `BASSET_DEV_MODE=true` to your `.ENV` file.

---

<a name="addons"></a>
### Upgrade Add-ons

**backpack/file-manager** Using the File Manager package? Most of the views that weren't in use  were removed, and the dependencies were bumped. If you didn't do any customization you should delete the `resources/views/vendor/elfinder` (`rm -rf resources/views/vendor/elfinder`) folder.
No need to publish any views anymore if you are not customizing them. If you were, publish the new view files (`php artisan vendor:publish --provider="Backpack\FileManager\FileManagerServiceProvider" --tag="elfinder-views"`). Then apply your customization on the new files, now located at: `resources/views/vendor/backpack/filemanager/`

Additional the `browse` and `browse_multiple` **fields/columns** are now part of this package. If you previously made any modifications to this fields/columns you should publish the new views (`php artisan vendor:publish --provider="Backpack\FileManager\FileManagerServiceProvider" --tag="filemanger-fields"` and `php artisan vendor:publish --provider="Backpack\FileManager\FileManagerServiceProvider" --tag="filemanager-columns"`), and carry over the modifications from the old files to this new files.

---

**You're done! Good job.** Thank you for taking the time to upgrade. Now you can:
- thoroughly test your application and your admin panel;
- start using the [new features in Backpack v7](/docs/{{version}}/release-notes);
