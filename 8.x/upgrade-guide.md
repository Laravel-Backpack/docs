# Upgrade Guide

---

This will guide you to upgrade from Backpack v7 to v8. The steps are color-coded by the probability that you will need it for your application: <span class="badge badge-danger text-white" style="text-decoration: none;">High</span>, <span class="badge badge-warning text-white" style="text-decoration: none;">Medium</span> and <span class="badge badge-secondary-soft" style="text-decoration: none;">Low</span>.  **At the very least, please read what's in bold**.

<a name="requirements"></a>
## Requirements

Please make sure your project respects the requirements below, before you start the upgrade process. You can check with ```php artisan backpack:version```:

- PHP 8.2+
- Laravel 12.x
- Backpack\CRUD 6.x
- 5-10 minutes (for most projects)

**If you're running Backpack version 3.x-5.x, please follow ALL other upgrade guides first, to incrementally get to use Backpack v6**. Test that your app works well with each version, after each upgrade. Only _afterwards_ can you follow this guide, to upgrade from v6 to v7. Previous upgrade guides:
- [upgrade from 6.x to 7.x](https://backpackforlaravel.com/docs/7.x/upgrade-guide);
- [upgrade from 5.x to 6.x](https://backpackforlaravel.com/docs/6.x/upgrade-guide);
- [upgrade from 4.1 to 5.x](https://backpackforlaravel.com/docs/5.x/upgrade-guide);
- [upgrade from 4.0 to 4.1](https://backpackforlaravel.com/docs/4.1/upgrade-guide);
- [upgrade from 3.6 to 4.0](https://backpackforlaravel.com/docs/4.0/upgrade-guide);
- [upgrade from 3.5 to 3.6](https://backpackforlaravel.com/docs/3.6/upgrade-guide);
- [upgrade from 3.4 to 3.5](https://backpackforlaravel.com/docs/3.5/upgrade-guide);
- [upgrade from 3.3 to 3.4](https://backpackforlaravel.com/docs/3.4/upgrade-guide);

<a name="upgraade-steps"></a>
## Upgrade Steps



<a name="composer"></a>
### Composer

<a name="step-1" href="#step-1" class="badge badge-danger text-white" style="text-decoration: none;">Step 1.</a> Your `composer.json` should require the new version of Backpack CRUD:

```
        "backpack/crud": "^8.0",
```

<a name="step-2" href="#step-2" class="badge badge-danger text-white" style="text-decoration: none;">Step 2.</a> Bump the version of any first-party Backpack add-ons you have installed (eg. `backpack/pro`, `backpack/editable-columns` etc.) to the versions that support Backpack v8. For 3rd-party add-ons, please check each add-on's Github page. Here's a quick list of 1st party packages and versions:

```js
  // no changes yet.
```


<a name="controllers"></a>
### CrudControllers

<a href="assets"></a>
### CSS & JS Assets

All 3rd party dependencies have been updated to their latest versions. A lot of CSS & JS assets have suffered changes. Fortunately, thanks to Basset, we can easily clear the old files and get the new ones. Run `php artisan basset:clear` and then `php artisan basset:cache` to get the new assets.

Most notable changes in asset files are: 
- select2 updated to the latest version, that included a few changes on field markup and css. 
- select2 bootstrap theme removed from the core files. It was a file designed for bootstrap 3&4, so it now only lives in coreui-v2.

<a name="notifications"></a>
### Notifications

Backpack now renders native Bootstrap toasts and modals through built-in `Noty` and `swal` compatible APIs; the `prologue/alerts` dependency is no longer used. Existing `Alert::success()`, `Alert::error()`, `Alert::warning()`, and `Alert::info()` calls continue to work. Replace custom PHP alert types such as `primary`, `secondary`, `light`, and `dark` with one of those four types, or use a custom `className`.

If you have published a theme `inc/alerts.blade.php` view, update it from the current theme before upgrading. Then run `php artisan basset:clear` and `php artisan basset:cache`. See the [Notifications documentation](/docs/{{version}}/crud-notifications) for the complete PHP and JavaScript API.



<a name="views"></a>
### Views

<a name="step-10" href="#step-10" class="badge badge-warning text-white" style="text-decoration: none;">Step 10.</a>  Many **views** have suffered tiny changes, for various reasons. They should still work in your context, but you should consider updating them to get the latest features. Here's a quick way to see if you have overidden any core files from Backpack packages, and those files have had changes:

```
# run this in your terminal, to register these 2 custom functions
# the functions will only be available in the current terminal session
# (if you close the terminal, the functions need to be pasted again)

# anychange()
# - receives 2 directories as parameters
# - the result is a list of files that are present in both directories, but have differences

anychange() { src=${1%/}; dst=${2%/}; find "$src" -type f -print0 | while IFS= read -r -d '' f; do rel=${f#"$src/"}; g="$dst/$rel"; if [ -f "$g" ] && ! cmp -s "$f" "$g"; then echo "$rel"; fi; done; }

# anydiff()
# - receives 2 directories as parameters
# - the result is the actual diffs of files that are present in both directories, but have differences

anydiff() { src=${1%/}; dst=${2%/}; find "$src" -type f -print0 | while IFS= read -r -d '' f; do rel=${f#"$src/"}; g="$dst/$rel"; [ -f "$g" ] && echo ">>> Diff for $rel" && diff -u "$f" "$g" && echo; done; }
```

Here are the commands we recommend you run, at a minimum:
```
anychange resources/views/vendor/backpack/crud vendor/backpack/crud/src/resources/views/crud
anychange resources/views/vendor/backpack/ui vendor/backpack/crud/src/resources/views/ui
anychange resources/views/vendor/backpack/crud vendor/backpack/pro/resources/views
```

<a name="security"></a>
### Security

No changes needed.

<a name="cache"></a>
### Cache

<a name="step-11" href="#step-11" class="badge badge-danger text-white" style="text-decoration: none;">Step 11.</a> Clear your app's cache:
```
php artisan basset:clear
php artisan config:clear
php artisan cache:clear
php artisan view:clear
```

---

<a name="step-12" href="#step-12" class="badge badge-danger text-white" style="text-decoration: none;">Step 12.</a> If your pages are slow to load, that's because Basset caching the assets as you load the pages, so your first pageload will be quite slow. If you find that annoying, run `php artisan basset:cache` to cache all CSS and JS assets. Alternatively, if you want Basset NOT to run because you're making changes to CSS and JS files, you can add `BASSET_DEV_MODE=true` to your `.ENV` file.

---

<a name="addons"></a>
### Upgrade Add-ons

---

**You're done! Good job.** Thank you for taking the time to upgrade. Now you can:
- thoroughly test your application and your admin panel;
- start using the [new features in Backpack v8](/docs/{{version}}/release-notes);
