# About Backpack's User Interface

---

Backpack helps you build admin panels faster by:
- installing our custom HTML theme, [Backstrap](https://backstrap.net), based on Bootstrap 4 and [CoreUI](https://coreui.io);
- installing [sweetalert](https://sweetalert.js.org/) for triggering pretty Confirm modals;
- installing [noty](https://ned.im/noty/#/) to show notification bubbles upon error/success/warning/info - triggered from Javascript;
- installing [prologue/alerts](https://github.com/prologuephp/alerts) for triggering notification bubbles from PHP (both on the same page and using flashdata);
- providing a separate authentication system for your admins;
- providing pretty error pages for most common errors;
- providing a horizontal menu and a side menu you can customize;
- providing a place for your admin to to change his email/name/password;
- providing a few helpers you can use throughout your admin panel;

For the simplest projects, you will never need to know how it works, never need to customize anything but the ```config/backpack/base.php``` file. But here's how everything works, below.

<a name="layout-and-design"></a>
## Layout & Design

<a name="general"></a>
### General

Backpack pulls in our custom HTML template, [Backstrap](https://www.npmjs.com/package/@digitallyhappy/backstrap), and adds our own CSS file on top, for a few cosmetic improvements. We've chosen to base Backstrap on [CoreUI](https://coreui.io), because it provides design blocks for all common features of an administration panel. When you decide to build custom pages for your Admin Panel, you can just use Backstrap's HTML blocks - no designer needed. You can see all the HTML components Backstrap provides on [backstrap.net](https://backstrap.net), and copy-paste HTML from there, or use [CoreUI](https://coreui.io)'s documentation for details.

<a name="published-views"></a>
### New Files in Your App

After installation, you'll notice Backpack has added a few files:

**1) View to ```resources/views/vendor/backpack/base/inc/sidebar_content.blade.php```**

This  file is used to show the contents of the menu to the left (sidebar). It's been published there so that you can easily modify its contents, by editing its HTML.

**2) Middleware to ```app/Http/Middleware/CheckIfAdmin.php```**

This middleware is used to test if users have access to admin panel pages. You can (and should customize it) if you have both users and admins in your app.

**3) Route file to ```routes/backpack/custom.php```**
    
This route file is for convenience and convention. We recommend you place all your admin panel routes here.


<a name="published-views"></a>
### Published Views

After installation, you'll notice Backpack has added a new blade file in ```resources/views/vendor/backpack/base/```:
    - ```inc/sidebar_content.blade.php```;

That file is used to show the contents of the menu to the left (sidebar). It's been published there so that you can easily modify its contents, by editing its HTML or adding dynamic content through [widgets](/docs/{{version}}/base-widgets).

<a name="unpublished-views"></a>
### Unpublished Views

You can change any blade file to your own needs. Determine what file you'd need to modify if you were to edit directly in the project's vendor folder, then go to ```resources/views/vendor/backpack/base``` and create a file with the exact same name. Backpack\Base will use this new file, instead of the one in the package. 

For example:
- if you want to add an item to the top menu, you could just create a file called ```resources/views/vendor/backpack/base/inc/topbar_left_content.blade.php```; Backpack will now use this file's contents, instead of  ```vendor/backpack/base/src/resources/views/inc/topbar_left_content.php```;
- if you want to change the contents of the dashboard page, you can just create a file called `resources/views/vendor/backpack/base/dashboard.blade.php` and Backpack will use that one, instead of the one in the package;

You can create blade views from scratch, or you can use our command to publish the view from the package and edit it to your liking:
```
php artisan backpack:publish base/dashboard
```

Then inside the blade files, you can use either plain-old HTML or add dynamic content through [Backpack widgets](/docs/{{version}}/base-widgets).

<a name="folder-structure"></a>
### Folder Structure

If you'll take a look inside any Backpack package, you'll notice the ```src``` directory is organised like a standard Laravel app. This is intentional. It should help you easily understand how the package works, and how you can overwrite/customize its functionality.

- ```app```
  - ```Console```
    - ```Commands```
  - ```Http```
    - ```Controllers```
    - ```Middleware```
    - ```Requests```
  - ```Notifications```
- ```config```
- ```resources```
  - ```lang```
  - ```views```
- ```routes```

<a name="authentication"></a>
## Authentication

When installed, Backpack provides a way for admins to login, recover password and register (don't worry, register is only enabled on ```localhost```). It does so with its own authentication controllers, models and middleware. If you have regular end-users (not admins), you can keep the _user_ authentication completely separate from _admin_ authentication. You can change which model, middleware classes Backpack uses, inside the ```config/backpack/base.php``` config file.

> **Backpack uses Laravel's default ```App\User``` model**. This assumes you weren't already using this model, or the ```users``` table, for anything else. Or that you plan to use it for both users & admins. Otherwise, please read below.

<a name="using-a-different-user-model"></a>
### Using a Different User Model

If you want to use a different User model than ```App\User``` or you've changed its location, please, you can tell Backpack to use _a different_ model in ```config/backpack/base.php``` instead of the ```App\User``` model that Laravel apps usually have. Look for ```user_model_fqn```.


<a name="having-both-regular-users-and-admins"></a>
### Having Both Regular Users and Admins

If you already use the ```users``` table to store end-users (not admins), you will need a way to differentiate admins from regular users. Backpack does not force one method on you. Here are two methods we recommend, below:
- (A) adding an ```is_admin``` column to your ```users``` table, then changing the ```app/Http/Middleware/CheckIfAdmin::checkIfUserIsAdmin()``` method to test that attribute exists, and is true;
- (B) using the [PermissionManager](https://github.com/Laravel-Backpack/PermissionManager) extension - this will also add groups and permissions; Then tell Backpack to use _your new permission middleware_ to check if a logged in user is an admin, inside ```config/backpack/base.php```;

<a name="routes"></a>
### Routes

By default, all administration panel routes will be behind an ```/admin/``` prefix, and under an ```CheckIfAdmin``` middleware. You can change that inside ```config/backpack/base.php```. 

Inside your _admin controllers or views_, please:
- use ```backpack_auth()``` instead of ```auth()```;
- use ```backpack_user()``` instead of ```auth()->user```;
- use ```backpack_url()``` instead of ```url()```;

This will make sure you're using the model, prefix & middleware that you've defined in ```config/backpack/base.php```. In case you decide to make changes there later, you won't need to change anything else. There are also [other backpack helpers you can use](#helpers).

<a name="admin-account"></a>
## Admin Account

When logged in, the admin can click his/her name to go to his "account" page. There, they will be able to do a few basic operations: change name, email or password.

<a name="change-name-and-email"></a>
### Change Name and Email

Changing name and email is done inside ```Backpack\Base\app\Http\Controllers\Auth\MyAccountController```, using the ```getAccountInfoForm()``` and ```postAccountInfoForm()``` methods. If you want to change how this works, we recommend you create a ```routes/backpack/base.php``` file, copy-paste all Backpack\Base routes there and change whatever routes you need, to point to _your own controller_, where you can do whatever you want.

If you only want to add a few new inputs, you can do that by creating a file in ```resources/views/vendor/backpack/base/my_account.blade.php``` that uses code from the same file in the Backpack package, but adds the inputs you need. Remember to also make these fields ```$fillable``` in your User model.

<a name="change-password"></a>
### Change Password

Password changing is done inside ```Backpack\Base\app\Http\Controllers\Auth\MyAccountController```. If you want to change how this works, we recommend you create a ```routes/backpack/base.php``` file, copy-paste all Backpack\Base routes there and change whatever you need. You can then point the route to your own controller, where you can do whatever you want.


<a name="helpers"></a>
## Helpers

You can use these helpers anywhere in your app (models, views, controllers, requests, etc), except the config files, since the config files are loaded _before_ the helpers.

- **```backpack_url($path)```** - Use this helper instead of ```url()``` to generate paths with the admin prefix prepended.
- **```backpack_auth()```** - Returns the Auth facade, using the current Backpack guard. Basically a shorthand for ```\Auth::guard(backpack_guard_name())```. Use this instead of ```auth()``` inside your admin panel pages.
- **```backpack_middleware()```** - Returns the key for the admin middleware. Default is ```admin```.
- ```backpack_authentication_column()``` - Returns the username column. The Laravel and Backpack default is ```email```.
- ```backpack_users_have_email()``` - Tests that the ```email``` column exists on the users table and returns true/false.
- ```backpack_avatar($user)``` - Receives a user object and returns a path to an avatar image, according to the preferences in the config file (gravatar, placeholder or custom).
- ```backpack_guard_name()``` - Returns the guard used for Backpack authentication.
- ```backpack_user()``` - Returns the current Backpack user, if logged in. Basically a shorthand for ```\Auth::guard(backpack_guard_name())->user()```. Use this instead of ```auth()->user()``` inside your admin pages.

<a name="error-pages"></a>
## Error Pages

When installing Backpack, a few error views are published into ```resources/views/errors```, if you don't already have other files there. This is because Laravel does not provide error pages for all HTTP error codes. Having these files in your project will make sure that, if a user gets an HTTP error, at least it will look decent. Error pages are provided for the following error codes: ```400```, ```401```, ```403```, ```404```, ```405```, ```408```, ```429```, ```500```, ```503```.
