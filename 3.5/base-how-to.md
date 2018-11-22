# How To for Backpack\Base

---

<a name="customize-menu-or-sidebar"></a>
## Customize the menu or sidebar

During installation, Backpack publishes a few files in you ```resources/views/vendor/backpack/base/inc``` folder. In there, you'll also find:
- ```sidebar_content.php```
- ```topbar_left_content.php```
- ```topbar_right_content.php```

Change those files as you please.

<a name="customize-dashboard"></a>
## Customize the dashboard

The dashboard is shown from ```Backpack\Base\app\Http\Controller\AdminController.php::dashboard()```. If you take a look at that method, you'll see that the only thing it does is to set a title from the language file, and return a view: ```backpack::dashboard```.

In order to place something else inside that view, simply publish that view in your project, and Backpack will pick it up, instead of the one in the package. Create a ```resources/views/backpack/base/dashboard.blade.php``` file:

```html
@extends('backpack::layout')

@section('header')
    <section class="content-header">
      <h1>
        {{ trans('backpack::base.dashboard') }}<small>{{ trans('backpack::base.first_page_you_see') }}</small>
      </h1>
      <ol class="breadcrumb">
        <li><a href="{{ backpack_url() }}">{{ config('backpack.base.project_name') }}</a></li>
        <li class="active">{{ trans('backpack::base.dashboard') }}</li>
      </ol>
    </section>
@endsection


@section('content')
    <div class="row">
        <div class="col-md-12">
            <div class="box">
                <div class="box-header with-border">
                    <div class="box-title">{{ trans('backpack::base.login_status') }}</div>
                </div>

                <div class="box-body">{{ trans('backpack::base.logged_in') }}</div>
            </div>
        </div>
    </div>
@endsection
```

To use information from the database, [use view composers](https://laravel.com/docs/5.7/views#view-composers) to push variables inside this view, when it's loaded. Or better yet, load all your dashboard information using AJAX calls, if you're loading charts, reports, etc, and the DB queries might take a long time.

Take a look at the [AdminLTE dashboards](https://adminlte.io/themes/AdminLTE/index.html) - you can easily use whatever content block you want from there.

## Customizing the general layout/design

See [the docs](/docs/{{version}}/base-about#layout-design).

## Customizing the Auth controllers

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

<a name="customize-routes"></a>
## Customize the routes

### Custom routes - option 1

You can place a new routes file in your ```app/routes/backpack/base.php```. If a file is present there, no default Backpack\Base routes will be loaded, only what's present in that file. You can use the routes file ```vendor/backpack/base/src/resources/views/base.php``` as an example, and customize whatever you want.

### Custom routes - option 2

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

<a name="customize-overlays-css"></a>
## Customize the look and feel of AdminLTE (using CSS)

In ```config/app.php``` you should have a config option that looks like this:

```php
    // Overlays - CSS files that change the look and feel of the admin panel
    'overlays' => [
        'vendor/backpack/base/backpack.bold.css',
        // 'vendor/backpack/base/backpack.content.is.king.css', // opinionized borderless alternative
    ],
```

If you don't (it was added in Base 0.9.9), you can create it.

This config option allows you to add CSS files that add style _on top_ of AdminLTE, to make it look different. Our ```backpack.bold.css``` file is included by default, which makes AdminLTE look more modern. But if you want your backend to match your front-end, you can create a CSS file anywhere inside your ```public``` folder, and add it here.

For example, if you're using the [Stack HTML template](https://themeforest.net/item/stack-multipurpose-html-with-page-builder/19337626?ref=medium_rare) on your front-end, you can just [add this overlay](https://gist.github.com/tabacitu/4f7eae0519e37aef46cbb959b8ab01a9) to make AdminLTE look very similar.

<a name="use-sparate-login-for-users-and-admins"></a>
## Use separate login/register forms for users and admins

This is a default in Backpack\Base 1.0.0.

Backpack's authentication uses a completely separate authentication driver, provider, guard and password broker. They're all named ```backpack```, and registered in the vendor folder, invisible to you. 

If you need a separate login for user, just go ahead and create it. [Add the Laravel authentication, like instructed in the Laravel documentation](https://laravel.com/docs/5.7/authentication#authentication-quickstart): ```php artisan make:auth```. You'll then have:
- the user login at ```/login``` -> using the AuthenticationController Laravel provides
- the admin login at ```/admin/login``` -> using the AuthenticationControllers Backpack provides

The user login will be using Laravel's default authentication driver, provider, guard and password broker, from ```config/auth.php```.

Backpack's authentication driver, provider, guard and password broker can easily be overwritten by creating a driver/provider/guard/broker with the ```backpack``` name inside your ```config/auth.php```. If one named ```backpack``` exists there, Backpack will use that instead.

<a name="overwrite-authentication-driver-provider-guard-or-password-broker"></a>
## Overwrite Backpack authentication driver, provider, guard or password broker

Backpack's authentication uses a completely separate authentication driver, provider, guard and password broker. Backpack adds them to what's defined in ```config/auth.php``` on runtime, and they're all named ```backpack```.

To change a setting in how Backpack's driver/provider/guard or password broker works, create a driver/provider/guard/broker with the ```backpack``` name inside your ```config/auth.php```. If one named ```backpack``` exists there, Backpack will use that instead.

<a name="use-separate-sessions-for-admins-and-users"></a>
## Use separate sessions for admin&user authentication

This is a default in Backpack\Base 1.0.0.

<a name="login-with-username-instead-of-email"></a>
## Login with username instead of email

1. Create a ```username``` column in your users table and add it in ```$fillable``` on your ```User``` model. Best to do this with a migration.
2. Delete your ```email``` column and remove it from ```$fillable``` on your ```User``` model. Alternatively, just remove UNIQUE and NOT NULL from it. Best to do this with a migration.
3. Change your ```config/backpack/base.php``` config options:
```php
    // Username column for authentication
    // The Backpack default is the same as the Laravel default (email)
    // If you need to switch to username, you also need to create that column in your db
    'authentication_column' => 'username',
    'authentication_column_name' => 'Username',
```
That's it. This will:
- use ```username``` for login;
- use ```username``` for registration;
- use ```username``` in My Account, when a user wants to change his info;
- completely disable the password recovery (if you've deleted the ```email``` db column);


<a name="use-your-own-user-model-instead-of-backpack-user"></a>
## Use your own User model instead of BackpackUser

By default, authentication and everything else inside Backpack is done using the ```Backpack\Base\app\Models\BackpackUser``` model, which extends Laravel's default ```App\User``` model. If you change the location of ```App\User```, or want to use a different User model for whatever other reason, you can do so by
- changing ```user_model_fqn``` in ```config/backpack/base.php``` to your new class;
- making sure everything inside ```BackpackUser``` is also inside your new model (this is important for recovering password, etc);


<a name="manually-install-backpack-base"></a>
## Manually install Base

If for any reason the Backpack/Base installation process fails for you, you can manually run all the commands in the installer, which are listed below. Failure to install can happens sometimes if the user does not have enough permissions (sudo access is needed) or if the composer command is not registered (and ```php composer``` needs to be run instead).

```bash
# Install backpack/generators
composer require backpack/generators --dev

# Install laracasts/generators
composer require laracasts/generators:dev-master --dev

# Publish configs, langs, views and AdminLTE files
php artisan vendor:publish --provider="Backpack\Base\BaseServiceProvider" --tag="minimum"

# Publish config for notifications - prologue/alerts
php artisan vendor:publish --provider="Prologue\Alerts\AlertsServiceProvider"

# Generate users table (using Laravel's default migrations)
php artisan migrate

# Publish the BackpackUser model inside your app/Models directory
php artisan backpack:base:publish-user-model

# Publish the CheckIfAdmin middleware inside your app/Http/Middleware directory
php artisan backpack:base:publish-middleware
```
