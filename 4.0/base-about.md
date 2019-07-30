# About Backpack\Base

---

Backpack/Base kickstarts your admin panel building by:
- pulling in the AdminLTE HTML theme, based on Bootstrap 3;
- providing different views and functionality for authentication;
- integrating [prologue/alerts](https://github.com/prologuephp/alerts) and and [pnotify](https://github.com/sciactive/pnotify) for showing notification bubbles upon error/success/warning/info;
- providing pretty error pages for most common errors;
- providing a horizontal menu and a side menu you can customize;
- providing a place for your admin to to change his email/name/password;
- providing a few helpers you can use throughout your admin panel;

For the simplest projects, you will never need to know how it works, never need to customize anything but the ```config/backpack/base.php``` file. But here's how everything works, below.

<a name="layout-and-design"></a>
## Layout & Design

<a name="general"></a>
### General

Backpack\Base pulls in the free [AdminLTE](https://adminlte.io/themes/AdminLTE/index2.html) theme and adds our own CSS file on top, for a few cosmetic improvements. We've chosen AdminLTE because it provides design blocks for all common features of an administration panel. When you decide to build something from scratch, you can just use its HTML blocks - no designer needed.

<a name="published-views"></a>
### Published Views

After installation, you'll notice Backpack has added one or more files to ```resources/views/vendor/backpack/base/```. By default, it only publishes:
- ```inc/sidebar_content.blade.php```;
- ```dashboard.blade.php```;

Those files are used to show the contents of the menu to the left (sidebar), and the first page the admin sees when logging in (dashboard). They've been published there so that you can easily modify their contents.

<a name="unpublished-views"></a>
### Unpublished Views

You can change any blade file to your own needs. Determine what file you'd need to modify if you were to edit directly in the project's vendor folder, then go to ```resources/views/vendor/backpack/base``` and create a file with the exact same name. Backpack\Base will use this new file, instead of the one in the package. 

For example, if you want to add an item to the top menu, you could just create a file called ```resources/views/vendor/backpack/base/inc/menu.php```. Backpack will now use this file's contents, instead of  ```vendor/backpack/base/src/resources/views/inc/menu.php```

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

When installed, Backpack provides a way for admins to login, recover password and register (don't worry, register is only enabled on ```localhost```). It does so with its own authentication controllers, models and middleware. If you have regular end-users (not admins), you can keep the _user_ authentication completely separate from _admin_ authentication. You can change which model, middleware classes inside the ```config/backpack/base.php``` config file.


> **The ```BackpackUser``` model extends Laravel's default ```App\User``` model**. This assumes you weren't already using this model, or the ```users``` table, for anything else. If you were, please read below.

<a name="using-a-different-user-model"></a>
### Using a Different User Model

If you want to use a different User model than ```App\User``` or you've changed its location, please:
- take a look at [```\Backpack\Base\app\Models\BackpackUser::class```](https://github.com/Laravel-Backpack/Base/blob/master/src/app/Models/BackpackUser.php);
- include the methods there in _your_ user model; they're important for password recovery;
- tell Backpack to use _your_ model in ```config/backpack/base.php```;

<a name="having-both-regular-users-and-admins"></a>
### Having Both Regular Users and Admins

If you already use the ```users``` table to store end-users (not admins), you will need a way to differentiate admins from regular users. Backpack does not force one method on you. Here are two methods we recommend, below:
- (A) adding an ```is_admin``` column to your ```users``` table, then creating a middleware that checks that that column is true. You can use ```\Backpack\Base\app\Http\Middleware\CheckIfAdmin::class``` as a starting point;
- (B) using the [PermissionManager](https://github.com/Laravel-Backpack/PermissionManager) extension - this will also add groups and permissions;

Please remember to tell Backpack to use _this new middleware_ to check if a logged in user is an admin, inside ```config/backpack/base.php```.

<a name="routes"></a>
### Routes

By default, all administration panel routes will be behind an ```/admin/``` prefix, and under an ```admin``` middleware. You can change that inside ```config/backpack/base.php```. Inside your _custom admin pages or admin features_, please:
- use ```backpack_auth()``` instead of ```auth()```;
- use ```backpack_url()``` instead of ```url()```;

This will make sure you're using the prefix & middleware that you've defined in ```config/backpack/base.php```. In case you decide to make changes there later, you won't need to change anything else. There are also [other backpack helpers you can use](#helpers).

<a name="admin-account"></a>
## Admin Account

When logged in, the admin can click his/her name to go to his "account" page. There, they will be able to do a few basic operations: change name, email or password.

<a name="change-name-and-email"></a>
### Change Name and Email

Changing name and email is done inside ```Backpack\Base\app\Http\Controllers\Auth\MyAccountController```, using the ```getAccountInfoForm()``` and ```postAccountInfoForm()``` methods. If you want to change how this works, we recommend you create a ```routes/backpack/base.php``` file, copy-paste all Backpack\Base routes there and change whatever you need. You can then point the route to your own controller, where you can do whatever you want.

If you only want to add a few new inputs, you can do that by creating a file in ```resources/views/vendor/backpack/base/auth/account/update_info.blade.php``` that uses code from the same file in the Backpack package, but adds the inputs you need. Remember to also make these fields ```$fillable``` in your User model.

<a name="change-password"></a>
### Change Password

Password changing is done inside ```Backpack\Base\app\Http\Controllers\Auth\MyAccountController```, using the ```getChangePasswordForm()``` and ```postChangePasswordForm()``` methods. If you want to change how this works, we recommend you create a ```routes/backpack/base.php``` file, copy-paste all Backpack\Base routes there and change whatever you need. You can then point the route to your own controller, where you can do whatever you want.

<a name="notification-bubbles"></a>
## Notification Bubbles 

<a name="notification-bubbles-in-php"></a>
### Triggering Notification Bubbles in PHP

We use [prologue/alerts](https://github.com/prologuephp/alerts#adding-alerts-through-alert-levels) to trigger notifications. Check out its documentation for the entire syntax. Basic examples:

```php
public function foo() 
{
  \Alert::info('This is a blue bubble.');
  \Alert::warning('This is a yellow/orange bubble.');
  \Alert::error('This is a red bubble.');
  \Alert::success('This is a green bubble.');
  \Alert::success('<strong>Got it</strong><br>This is an HTML message.');

  
  // the layout will make sure to show your notifications
  return view('some_view');
}

public function bar()
{
  \Alert::success('You have successfully logged in')->flash();
 
  // please note the above flash() method; this will store your notification in a session variable, so that you can redirect to another page, but the notification will still be shown (on the page you redirect to)
  return Redirect::to('some-url');
}
```

<a name="notification-bubbles-in-javascript"></a>
### Triggering Notification Bubbles in JavaScript

We use [PNotify](https://sciactive.com/pnotify/) to show notifications from JavaScript, on the same page. Check out its page for more detailed use. Basic example:

```php
new PNotify({
  title: "Operation successful",
  text: "You have deleted the internet.",
  type: "success"
});

// available types: success, info, warning, error
// PLEASE NOTE it's "error" here, not "danger"
```

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

When installing Backpack, a few error views are published into ```resources/views/errors```, if you don't already have other files there. This will make sure that, if a user gets an HTTP error, at least it will look decent. Error pages are provided for the following error codes: ```400```, ```401```, ```403```, ```404```, ```405```, ```408```, ```429```, ```500```, ```503```.
