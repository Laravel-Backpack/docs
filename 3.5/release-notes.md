# Release Notes

---

**Launch date:** March 22nd 2018

Here are the main differences between [Backpack 3.3](https://laravel-backpack.readme.io/v3.3/docs) and Backpack 3.4.

<a name="backpack-base-0-9-x"></a>
## Backpack\Base 0.9.x
- installation:
  - a single ```php artisan backpack:base:install``` command is needed to install Backpack\Base, instead of multiple steps;
  - a minimum amount of views is published (only ```sidebar_contents.blade.php```), so that updates can be easily pushed to the other view files;
- you can now have HTML messages inside notification bubbles, for example ```Alert::danger('<strong>Error!</strong><br>No idea what happened.');```;
- in the past ```layout.blade.php``` provided us with a few [sections](https://laravel.com/docs/5.6/blade#template-inheritance) we could reuse (```before_scripts```, ```after_scripts``` etc.); since Laravel now has [stacks](https://laravel.com/docs/5.6/blade#stacks), we've created stacks with the same names, so that we can easily push content from any view; they have the same names as the sections (```before_scripts```, ```after_scripts```, ```before_styles```, ```after_styles```); the sections are still there, so you can use those too;
- [easily change admin middleware](https://github.com/Laravel-Backpack/Base/pull/258)
   - config option to change the admin middleware name;
   - config option to change the admin middleware class;
   - the admin middleware class is now actually a middleware group, so you can add stuff to it;
- [easily change the login to use username instead of email](https://github.com/Laravel-Backpack/Base/pull/257) 
- [easily change the authentication guard](https://github.com/Laravel-Backpack/Base/pull/165) aka separate logins for users and admins, using different sessions;
- a command to create a new route, behind the default middleware; that route will be placed inside ```routes/backpack/custom.php```;
- a command to create a new sidebar item; it will publish the given HTML inside the new ```resources/views/vendor/backpack/base/inc/sidebar_contents.blade.php``` file;
- upgraded to AdminLTE 2.4.x, and all its dependencies;

<a name="backpack-crud-3-4-x"></a>
## Backpack\CRUD 3.4.x

- CRUD list view (datatables) now [looks very good](https://github.com/Laravel-Backpack/CRUD/pull/1180#issuecomment-357522025) and is easy to use on mobile devices and tablets;
- added [support for 1-to-1 relationship](https://github.com/Laravel-Backpack/CRUD/pull/865);
- one-line installation command
- upgraded dependencies;
- removed ```barryvdh/laravel-elfinder``` requirement, since not every project needs it; the functionality is still there, you're asked upon installation if you also want a media library;
- the create/update endpoints [now return JSON responses](https://github.com/Laravel-Backpack/CRUD/pull/1249) in case you want to use them with an API;