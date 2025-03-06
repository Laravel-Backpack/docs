# Upgrade Guide

---

This will guide you to upgrade from Backpack v6 to v7. The steps are color-coded by the probability that you will need it for your application: <span class="badge badge-danger text-white" style="text-decoration: none;">High</span>, <span class="badge badge-warning text-white" style="text-decoration: none;">Medium</span> and <span class="badge badge-secondary-soft" style="text-decoration: none;">Low</span>.  **At the very least, please read what's in bold**.

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

<a name="step-0" href="#step-0" class="badge badge-danger" style="text-decoration: none;">Step 0.</a> **[Upgrade to Laravel 11](https://laravel.com/docs/11.x/upgrade) if you don't use it yet, then test to confirm your app is working fine.**

<a name="composer"></a>
### Composer

<a name="step-1" href="#step-1" class="badge badge-danger text-white" style="text-decoration: none;">Step 1.</a> Update your ```composer.json``` file to require:

```
        "backpack/crud": "v7-dev",
```

<a name="step-2" href="#step-2" class="badge badge-danger text-white" style="text-decoration: none;">Step 2.</a> Bump the version of any first-party Backpack add-ons you have installed (eg. `backpack/pro`, `backpack/editable-columns` etc.) to the versions that support Backpack v6. For 3rd-party add-ons, please check each add-on's Github page. Here's a quick list of 1st party packages and versions:

```js
        "backpack/crud": "v7-dev",
        "backpack/pro": "v3-dev",
        "backpack/filemanager": "^3.0",
        "backpack/theme-coreuiv2": "^1.0",
        "backpack/theme-coreuiv4": "^1.0",
        "backpack/theme-tabler": "^1.0",
        "backpack/logmanager": "^5.0",
        "backpack/settings": "^3.1",
        "backpack/newscrud": "^5.0",
        "backpack/permissionmanager": "^7.0",
        "backpack/pagemanager": "^3.2",
        "backpack/menucrud": "^4.0",
        "backpack/backupmanager": "^5.0",
        "backpack/editable-columns": "^3.0",
        "backpack/revise-operation": "^2.0",
        "backpack/medialibrary-uploaders": "^1.0",
        "backpack/devtools": "^2.0",
```

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

No changes needed.

<a name="controllers"></a>
### CrudControllers

<a name="step-x" href="#step-x" class="badge badge-danger text-white" style="text-decoration: none;">Step X.</a> The `wysiwyg` field and column have been removed, because they were hiding functionality. But don't worry, they were just alias columns. Behind the scenes, they were just loading the `ckeditor` field and `text` column respectively. If you're using the `wysiwyg` column of field in your CrudController, replace them with their originals.

<a name="step-x" href="#step-x" class="badge badge-danger text-white" style="text-decoration: none;">Step X.</a> The `ckeditor` field and column have been moved from the PRO package to an open-source addon. If you are using either of them and want to keep doing so, just run `composer require backpack/ckeditor-field`. Please note that CKEditor is a 3rd party JS library that requires _payment_ when used for commercial purposes (GPL licensed). See their [pricing page](https://ckeditor.com/pricing/) for options. We can give away a few sub-licenses every year - [contact us](https://backpackforlaravel.com/contact) to see if any are still available. If you would rather move away from CKEditor, you can just change your field/column to use `summernote` instead, they are both HTML editors so your content should be compatible. Summernote has more limited editing options, but it's MIT-licensed.

<a name="step-x" href="#step-x" class="badge badge-danger text-white" style="text-decoration: none;">Step X.</a> Similarly, the `tinymce` field and column have been moved from the PRO package to an open-source addon. If you are using either of them and want to keep doing so, just run `composer require backpack/tinymce-field`. Please note that TinyMCE is a 3rd party JS library that normally requires _payment_ when used for commercial purposes (GLP licensed). See their [pricing page](https://www.tiny.cloud/pricing/) for options. You can sign-up for [free commercial licenses on their website here](https://www.tiny.cloud/get-tiny/). If you would rather move away from TinyMCE, you can just change your field/column to use `summernote` instead, they are both HTML editors so your content should be compatible. Summernote has more limited editing options, but it's MIT-licensed.


<a href="assets"></a>
### CSS & JS Assets

No changes needed.

<a name="views"></a>
### Views

No changes needed.

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

For any addons you might have upgraded, please double-check if they have an upgrade guide. For example:
- Xx package has the upgrade guide here;
- Yy package has the upgrade guide here;

---

**You're done! Good job.** Thank you for taking the time to upgrade. Now you can:
- thoroughly test your application and your admin panel;
- start using the [new features in Backpack v7](/docs/{{version}}/release-notes);
