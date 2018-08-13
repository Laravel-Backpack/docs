---
title: "FAQ"
excerpt: ""
---
Here's how you can do most things that might not be that intuitive in Backpack\Base:
[block:api-header]
{
  "type": "basic",
  "title": "Customizing the menu or sidebar"
}
[/block]
During installation, Backpack publishes a few files in you ```resources/views/vendor/backpack/base``` folder. In there, you'll also find ```inc/sidebar.php``` and ```inc/menu.php```. Change those files as you please.
[block:api-header]
{
  "type": "basic",
  "title": "Customizing the dashboard"
}
[/block]
In ```config/app.php```, Make sure the "RouteServiceProvider" is set **after** "BaseServiceProvider". 

Add a route, so that the standard route will take the user to a controller you want:
[block:code]
{
  "codes": [
    {
      "code": "Route::get('dashboard', 'DashboardController@index');",
      "language": "php"
    }
  ]
}
[/block]
Inside DashboardController on index method return dashboard view.
[block:code]
{
  "codes": [
    {
      "code": "public function index() {\n    $someVar = 'Some text';\n    return view('vendor.backpack.base.dashboard', compact('someVar'));\n}",
      "language": "php"
    }
  ]
}
[/block]
Update the dashboard view located at: ```resources/view/vendor/backpack/base/dashboard.blade.php```

NOTE: if you don't have this folder you need to publish vendor assets
php artisan vendor:publish --provider="Backpack\Base\BaseServiceProvider" 

[block:api-header]
{
  "type": "basic",
  "title": "Customizing the general layout/design"
}
[/block]
// TODO - use explanation in github issue to write steps
[block:api-header]
{
  "type": "basic",
  "title": "Customizing the Auth controllers"
}
[/block]
In ```config/backpack/base.php``` you'll find these configuration options:

```php
    // Set this to false if you would like to use your own AuthController and PasswordController
    // (you then need to setup your auth routes manually in your routes.php file)
    'setup_auth_routes' => true,
```

You can change both ```setup_auth_routes``` to ```false```. This means Backpack\Base won't register the Auth routes any more, so you'll have to manually register them in your route file, to point to the Auth controllers you want. If you're going to use the Auth controllers that Laravel generates, these are the routes you can use:
```php
Route::group(['middleware' => 'web', 'prefix' => config('backpack.base.route_prefix')], function () {
    Route::auth();
    Route::get('logout', 'Auth\LoginController@logout');
});
```
[block:api-header]
{
  "type": "basic",
  "title": "Customizing the routes"
}
[/block]
In ```config/backpack/base.php``` you'll find these configuration options:

```php

    /*
    |--------------------------------------------------------------------------
    | Routing
    |--------------------------------------------------------------------------
    */

    // The prefix used in all base routes (the 'admin' in admin/dashboard)
    'route_prefix' => 'admin',

    // Set this to false if you would like to use your own AuthController and PasswordController
    // (you then need to setup your auth routes manually in your routes.php file)
    'setup_auth_routes' => true,

    // Set this to false if you would like to skip adding the dashboard routes
    // (you then need to overwrite the login route on your AuthController)
    'setup_dashboard_routes' => true,
```

In order to completely customize the auth routes, you can change both ```setup_auth_routes``` and ```setup_dashboard_routes``` to ```false```. This means Backpack\Base won't register any routes any more, so you'll have to manually register them in your route file. Here's what you can use to get started:
```php
Route::group(['middleware' => 'web', 'prefix' => config('backpack.base.route_prefix', 'namespace' => 'Backpack\Base\app\Http\Controllers')], function () {
    Route::auth();
    Route::get('logout', 'Auth\LoginController@logout');
    Route::get('dashboard', 'AdminController@dashboard');
    Route::get('/', 'AdminController@redirect');
});
```

Additionally, you can place a new routes file in your ```app/routes/backpack/base.php```. If a file is present there, no default Backpack\Base routes will be loaded, only what's present in that file.

[block:api-header]
{
  "type": "basic",
  "title": "Use separate login/register forms for users and admins"
}
[/block]
If you need separate login for user and admin, probably the fastest way would be to use:
- the backpack login for the admins
- the default Laravel login for the users

That way, you have both AND you have most of your code already written - Backpack comes with its own Authentication controllers, Laravel too. 

For this to happen, keep the ```setup_auth_routes``` and ```setup_dashboard_routes``` variables ```true``` in your config file. You'll be using the Backpack authentication, after all, for admins. Just [add the Laravel authentication, like instructed in the Laravel documentation](https://laravel.com/docs/5.5/authentication#authentication-quickstart): ```php artisan make:auth```. You'll then have:
- the user login at ```/login``` -> using the AuthenticationController Laravel provides
- the admin login at ```/admin/login``` -> using the AuthenticationControllers Backpack provides
[block:api-header]
{
  "type": "basic",
  "title": "Use the same login/register forms for users and admins"
}
[/block]
// TODO - use explanation in github issue to write steps
[block:api-header]
{
  "title": "Login with username instead of email"
}
[/block]
MarcosBL, one of our core Backpack veterans, has documented his steps in doing so in [this comment](https://github.com/Laravel-Backpack/Base/issues/55#issuecomment-347828058).


[block:api-header]
{
  "title": "How do I manually install Base?"
}
[/block]
If for any reason the Backpack/Base installation process fails for you, you can manually run all the commands in the installer, which are listed below. Failure to install can happens sometimes if the user does not have enough permissions (sudo access is needed) or if the composer command is not registered (and ```php composer``` needs to be run instead).
[block:code]
{
  "codes": [
    {
      "code": "# Install backpack/generators\ncomposer require backpack/generators --dev\n\n# Install laracasts/generators\ncomposer require laracasts/generators:dev-master --dev\n\n# Publish configs, langs, views and AdminLTE files\nphp artisan vendor:publish --provider=\"Backpack\\Base\\BaseServiceProvider\"\n\n# Publish config for notifications - prologue/alerts\nphp artisan vendor:publish --provider=\"Prologue\\Alerts\\AlertsServiceProvider\"\n\n# Generate users table (using Laravel's default migrations)\nphp artisan migrate",
      "language": "shell"
    }
  ]
}
[/block]