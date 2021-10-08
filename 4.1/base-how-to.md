# FAQs for the admin UI

---


<a name="look-and-feel"></a>
## Look and feel

<a name="customize-menu-or-sidebar"></a>
### Customize the menu or sidebar

During installation, Backpack publishes a few files in you ```resources/views/vendor/backpack/base/inc``` folder. In there, you'll also find:
- ```sidebar_content.php```
- ```topbar_left_content.php```
- ```topbar_right_content.php```

Change those files as you please.

<a name="customize-dashboard"></a>
### Customize the dashboard

The dashboard is shown from ```Backpack\Base\app\Http\Controller\AdminController.php::dashboard()```. If you take a look at that method, you'll see that the only thing it does is to set a title, breadcrumbs, and return a view: ```backpack::dashboard```.

In order to place something else inside that view, like [widgets](/docs/{{version}}/base-widgets), simply publish that view in your project, and Backpack will pick it up, instead of the one in the package. Create a ```resources/views/vendor/backpack/base/dashboard.blade.php``` file:

```html
@extends(backpack_view('blank'))

@php
    $widgets['before_content'][] = [
        'type'        => 'jumbotron',
        'heading'     => trans('backpack::base.welcome'),
        'content'     => trans('backpack::base.use_sidebar'),
        'button_link' => backpack_url('logout'),
        'button_text' => trans('backpack::base.logout'),
    ];
@endphp

@section('content')
  <p>Your custom HTML can live here</p>
@endsection
```

To use information from the database, you can:
- [use view composers](https://laravel.com/docs/5.7/views#view-composers) to push variables inside this view, when it's loaded;
- load all your dashboard information using AJAX calls, if you're loading charts, reports, etc, and the DB queries might take a long time;
- use the full namespace for your models, like ```\App\Models\Product::count()```;

Take a look at the [widgets](/docs/{{version}}/base-widgets) we have - you can easily use those in your dashboard. You can also add whatever HTML you want inside the content block - check the [Backstrap HTML Template](https://backstrap.net/widgets.html) for design components you can copy-paste to speed up your custom HTML.

<a name="customize-general-layout-design"></a>
### Customizing the design of the menu / sidebar / footer

In ```config/backpack/base.php``` you'll notice there are variables where you can change exactly what CSS classes are placed on the HTML elements that represent the header, body, sidebar and footer:

```php
    // Horizontal navbar classes. Helps make the admin panel look similar to your project's design.
    'header_class' => 'app-header bg-light border-0 navbar',
        // Try adding bg-dark, bg-primary, bg-secondary, bg-danger, bg-warning, bg-success, bg-info, bg-blue, bg-light-blue, bg-indigo, bg-purple, bg-pink, bg-red, bg-orange, bg-yellow, bg-green, bg-teal, bg-cyan
        // You might need to add "navbar-dark" too if the background color is a dark one.
        // Add header-fixed if you want the header menu to be sticky
    
    // Body element classes.
    'body_class' => 'app aside-menu-fixed sidebar-lg-show',
        // Try sidebar-hidden, sidebar-fixed, sidebar-compact, sidebar-lg-show
    

    // Sidebar element classes.
    'sidebar_class' => 'sidebar sidebar-pills bg-light',
        // Remove "sidebar-transparent" for standard sidebar look 
        // Try "sidebar-light" or "sidebar-dark" for dark/light links
        // You can also add a background class like bg-dark, bg-primary, bg-secondary, bg-danger, bg-warning, bg-success, bg-info, bg-blue, bg-light-blue, bg-indigo, bg-purple, bg-pink, bg-red, bg-orange, bg-yellow, bg-green, bg-teal, bg-cyan

    // Footer element classes.
    'footer_class' => 'app-footer',
```

Our default design might not be pleasant for your, or you might need to make the UI integrate better into your project. We totally understand. You can use the classes above to make it look considerably different.

You'll find a few examples below - but you should use which classes you want to get the result you need.

<a href="ui-with-dark-sidebar"></a>
#### Backstrap

Transparent top menu, transparent sidebar, transparent footer. This is the default. This is what _we_ think is best for most users, from our 8+ years of experience building admin panels. Prioritising _content_ over _menus_.

![Backstrap design](https://backpackforlaravel.com/uploads/docs-4-0/ui/examples/default.png)

```php
    'header_class' => 'app-header bg-light border-0 navbar',
    'body_class' => 'app aside-menu-fixed sidebar-lg-show',
    'sidebar_class' => 'sidebar sidebar-pills bg-light',
    'footer_class' => 'app-footer',
```

<a href="inspired-by-coreui"></a>
#### Inspired by CoreUI

White top menu, dark sidebar.

![CoreUI design](https://backpackforlaravel.com/uploads/docs-4-0/ui/examples/coreui.png)

```php
    'header_class' => 'app-header navbar',
    'body_class' => 'app aside-menu-fixed sidebar-lg-show',
    'sidebar_class' => 'sidebar',
    'footer_class' => 'app-footer d-none',
```

<a href="inspired-by-github"></a>
#### Inspired by Github

Black top menu, white sidebar.

![CoreUI design](https://backpackforlaravel.com/uploads/docs-4-0/ui/examples/github.png)

```php
    'header_class' => 'app-header bg-dark navbar',
    'body_class' => 'app aside-menu-fixed sidebar-lg-show',
    'sidebar_class' => 'sidebar bg-white sidebar-pills',
    'footer_class' => 'app-footer d-none',
```

<a href="blue-top-menu"></a>
#### Blue Top Menu

Blue top menu, dark sidebar.

![Construction or warning design](https://backpackforlaravel.com/uploads/docs-4-0/ui/examples/blue.png)

```php
    'header_class' => 'app-header navbar navbar-color bg-primary border-0',
    'body_class' => 'app aside-menu-fixed sidebar-lg-show',
    'sidebar_class' => 'sidebar', // add "bg-white sidebar-pills" for light sidebar
    'footer_class' => 'app-footer d-none',
```


<a href="inspired-by-contruction"></a>
#### Construction / Warning

Yellow top menu, dark sidebar.

![Construction or warning design](https://backpackforlaravel.com/uploads/docs-4-0/ui/examples/construction.png)

```php
    'header_class' => 'app-header navbar navbar-light bg-warning',
    'body_class' => 'app aside-menu-fixed sidebar-lg-show',
    'sidebar_class' => 'sidebar', // add "bg-white sidebar-pills" for light sidebar
    'footer_class' => 'app-footer d-none',
```

<a href="red-top-menu"></a>
#### Red Top Menu

Red top menu, dark sidebar.

![Construction or warning design](https://backpackforlaravel.com/uploads/docs-4-0/ui/examples/red.png)

```php
    'header_class' => 'app-header navbar navbar-color bg-error border-0',
    'body_class' => 'app aside-menu-fixed sidebar-lg-show',
    'sidebar_class' => 'sidebar', // add "bg-white sidebar-pills" for light sidebar
    'footer_class' => 'app-footer d-none',
```

<a href="pink-top-menu"></a>
#### Pink Top Menu

Pink top menu, transparent sidebar.

![Construction or warning design](https://backpackforlaravel.com/uploads/docs-4-0/ui/examples/pink.png)

```php
    'header_class' => 'app-header navbar navbar-color bg-error border-0',
    'body_class' => 'app aside-menu-fixed sidebar-lg-show',
    'sidebar_class' => 'sidebar sidebar-pills bg-light',
    'footer_class' => 'app-footer d-none',
```


<a href="green-top-menu"></a>
#### Green Top Menu

Green top menu, white sidebar.

![Construction or warning design](https://backpackforlaravel.com/uploads/docs-4-0/ui/examples/green.png)

```php
    'header_class' => 'app-header navbar navbar-color bg-green border-0',
    'body_class' => 'app aside-menu-fixed sidebar-lg-show',
    'sidebar_class' => 'sidebar sidebar-pills bg-white',
    'footer_class' => 'app-footer d-none',
```

<a name="create-a-new-theme"></a>
### Create a new theme / child theme

You can create a theme with your own HTML. Create a folder with all the views you want to overwrite, then change ```view_namespace``` inside your ```config/backpack/base.php``` to point to that folder. All views will be loaded from _that_ folder if they exist, then from ```resources/views/vendor/backpack/base```, then from the Base package.

You can use child themes to:
- create packages for your Backpack admin panels to look different (and re-use across projects)
- use a different CSS framework (ex: Tailwind, Bulma)


<a name="add-custom-javascript"></a>
### Add custom Javascript to all admin panel pages

In ```config/backpack/base.php``` you'll notice this config option:

```php
    // JS files that are loaded in all pages, using Laravel's asset() helper
    'scripts' => [
        // Backstrap includes jQuery, Bootstrap, CoreUI, PNotify, Popper
        'packages/backpack/base/js/bundle.js?v='.\PackageVersions\Versions::getVersion('backpack/base'),

        // examples (everything inside the bundle, loaded from CDN)
        // 'https://code.jquery.com/jquery-3.4.1.min.js',
        // 'https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js',
        // 'https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js',
        // 'https://unpkg.com/@coreui/coreui/dist/js/coreui.min.js',
        // 'https://cdnjs.cloudflare.com/ajax/libs/pace/1.0.2/pace.min.js',
        // 'https://unpkg.com/sweetalert/dist/sweetalert.min.js',
        // 'https://cdnjs.cloudflare.com/ajax/libs/noty/3.1.4/noty.min.js'

        // examples (VueJS or React)
        // 'https://unpkg.com/vue@2.4.4/dist/vue.min.js',
        // 'https://unpkg.com/react@16/umd/react.production.min.js',
        // 'https://unpkg.com/react-dom@16/umd/react-dom.production.min.js',
    ],
```

You can add files to this array, and they'll be loaded in all admin panels pages.

<a name="add-custom-css"></a>
### Add custom CSS to all admin panel pages

In ```config/backpack/base.php``` you'll notice this config option:

```php
    // CSS files that are loaded in all pages, using Laravel's asset() helper
    'styles' => [
        'packages/@digitallyhappy/backstrap/css/style.min.css',

        // Examples (the fonts above, loaded from CDN instead)
        // 'https://maxcdn.icons8.com/fonts/line-awesome/1.1/css/line-awesome-font-awesome.min.css',
        // 'https://fonts.googleapis.com/css?family=Source+Sans+Pro:300,400,600,700,300italic,400italic,600italic',
    ],
```

You can add files to this array, and they'll be loaded in all admin panels pages.

<a name="customize-overlays-css"></a>
### Customize the look and feel of the admin panel (using CSS)

If you want to change the look and feel of the admin panel, you can create a custom CSS file wherever you want. We recommend you do it inside ```public/packages/myname/mycustomthemename/css/style.css``` folder so that it's easier to turn into a theme, if you decide later to share or re-use your CSS in other projects.

In ```config/backpack/base.php``` add your file to this config option:

```php
    // CSS files that are loaded in all pages, using Laravel's asset() helper
    'styles' => [
        'packages/@digitallyhappy/backstrap/css/style.min.css',
         // ...
        'packages/myname/mycustomthemename/css/style.css',
    ],
```

This config option allows you to add CSS files that add style _on top_ of Backstrap, to make it look different. You can create a CSS file anywhere inside your ```public``` folder, and add it here.

<a name="how-to-add-vue-js-on-all-Backpack-pages"></a>
### How to add VueJS to all Backpack pages

You can add any script you want inside all Backpack's pages by just adding it in your ```config/backpack/base.php``` file:

```php

    // JS files that are loaded in all pages, using Laravel's asset() helper
    'scripts' => [
        // Backstrap includes jQuery, Bootstrap, CoreUI, PNotify, Popper
        'packages/backpack/base/js/bundle.js',

        // examples (everything inside the bundle, loaded from CDN)
        // 'https://code.jquery.com/jquery-3.4.1.min.js',
        // 'https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js',
        // 'https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js',
        // 'https://unpkg.com/@coreui/coreui/dist/js/coreui.min.js',
        // 'https://cdnjs.cloudflare.com/ajax/libs/pace/1.0.2/pace.min.js',
        // 'https://unpkg.com/sweetalert/dist/sweetalert.min.js',
        // 'https://cdnjs.cloudflare.com/ajax/libs/noty/3.1.4/noty.min.js'

        // examples (VueJS or React)
        // 'https://unpkg.com/vue@2.4.4/dist/vue.min.js',
        // 'https://unpkg.com/react@16/umd/react.production.min.js',
        // 'https://unpkg.com/react-dom@16/umd/react-dom.production.min.js',
    ],
```

You should be able to load Vue.JS by just uncommenting that one line. Or providing a link to a locally stored VueJS file.


<a name="customize-translations"></a>
### Customize the translated strings (aka overwrite the language files)

Backpack uses Laravel translations across the admin panel, to easily translate strings (ex: `{{ trans('backpack::base.already_have_an_account') }}`). If you don't like a translation, you're welcome to submit a PR to correct it for all users of your language. If you only want to correct it inside your app, or need to add a new translation string, you can *create a new file in your `resources/lang/vendor/backpack/en/base.php`* (similarly, `crud.php` or any other file). Any language strings that are inside your app, in the right folder, will be preferred over the ones in the package.

Alternatively, if you need to customize A LOT of strings, you can use: 
```bash
php artisan vendor:publish --provider="Backpack\CRUD\BackpackServiceProvider" --tag="lang"
```
which will publish ALL lang files, for ALL languages, inside `resources/lang/vendor/backpack`. But it's highly unlikely you need to modify all of them. In case you do publish all languages, please delete the ones you didn't change. That way, you only keep what's custom in your custom files, and it'll be easier to upgrade those files in the future.


<a name="use-the-same-html-and-css-for-front-end"></a>
### Use the HTML & CSS for the front-end (Backstrap for front-facing website)

If you like how Backpack looks and feels you can use the same interface to power your front-end, simply by making sure your blade view extend Backpack's layout file, instead of a layout file you'd create. Make sure your blade views extend `backpack_view('blank')` or create a layout file similar to our `layouts/top_left.blade.php` that better fits your needs. Then use it across your app:

```php
@extends(backpack_view('blank'))

<div>Something</div>
```

It's a good idea to go through our main layout file - [`layouts/top_left.blade.php`](https://github.com/Laravel-Backpack/CRUD/blob/master/src/resources/views/base/layouts/top_left.blade.php) - to understand how it works and how you can use it to your advantage. Most notably, you can:
- use our `before_styles` and `after_styles` sections to easily _include_ CSS there - `@section('after_styles')`;
- use our `before_styles` and `after_styles` stacks to easily _push_ CSS there - `@push('after_styles')`;
- use our `before_scripts` and `after_scripts` sections to easily _include_ JS there - `@section('after_scripts')`;
- use our `before_scripts` and `after_scripts` stacks to easily _push_ JS there - `@push('after_scripts')`;



<a name="authentication"></a>
## Authentication

<a name="customize-auth-controllers"></a>
### Customizing the Auth controllers

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
### Customize the routes

#### Custom routes - option 1

You can place a new routes file in your ```app/routes/backpack/base.php```. If a file is present there, no default Backpack\Base routes will be loaded, only what's present in that file. You can use the routes file ```vendor/backpack/base/src/resources/views/base.php``` as an example, and customize whatever you want.

#### Custom routes - option 2

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

<a name="use-sparate-login-for-users-and-admins"></a>
### Use separate login/register forms for users and admins

This is a default in Backpack v4.

Backpack's authentication uses a completely separate authentication driver, provider, guard and password broker. They're all named ```backpack```, and registered in the vendor folder, invisible to you. 

If you need a separate login for user, just go ahead and create it. [Add the Laravel authentication, like instructed in the Laravel documentation](https://laravel.com/docs/5.7/authentication#authentication-quickstart): ```php artisan make:auth```. You'll then have:
- the user login at ```/login``` -> using the AuthenticationController Laravel provides
- the admin login at ```/admin/login``` -> using the AuthenticationControllers Backpack provides

The user login will be using Laravel's default authentication driver, provider, guard and password broker, from ```config/auth.php```.

Backpack's authentication driver, provider, guard and password broker can easily be overwritten by creating a driver/provider/guard/broker with the ```backpack``` name inside your ```config/auth.php```. If one named ```backpack``` exists there, Backpack will use that instead.

<a name="overwrite-authentication-driver-provider-guard-or-password-broker"></a>
### Overwrite Backpack authentication driver, provider, guard or password broker

Backpack's authentication uses a completely separate authentication driver, provider, guard and password broker. Backpack adds them to what's defined in ```config/auth.php``` on runtime, and they're all named ```backpack```.

To change a setting in how Backpack's driver/provider/guard or password broker works, create a driver/provider/guard/broker with the ```backpack``` name inside your ```config/auth.php```. If one named ```backpack``` exists there, Backpack will use that instead.

<a name="use-separate-sessions-for-admins-and-users"></a>
### Use separate sessions for admin&user authentication

This is a default in Backpack v4.

<a name="login-with-username-instead-of-email"></a>
### Login with username instead of email

1. Create a ```username``` column in your users table and add it in ```$fillable``` on your ```User``` model. Best to do this with a migration.
2. Remove the UNIQUE and NOT NULL constraints from ```email``` on your table. Best to do this with a migration. Alternatively, delete your ```email``` column and remove it from ```$fillable``` on your ```User``` model. If you already have a CRUD for users, you might also need to delete it from the Request, and from your UserCrudController. 
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
### Use your own User model instead of App\User

By default, authentication and everything else inside Backpack is done using the ```App\User``` model. If you change the location of ```App\User```, or want to use a different User model for whatever other reason, you can do so by changing ```user_model_fqn``` in ```config/backpack/base.php``` to your new class.


<a name="use-your-own-profile-image-avatar"></a>
### Use your own profile image (avatar)

By default, Backpack will use Gravatar to show the profile image for the currently logged in backpack user. In order to change this, you can use the option in ```config/backpack/base.php```:
```php
// What kind of avatar will you like to show to the user?
// Default: gravatar (automatically use the gravatar for his email)
//
// Other options:
// - placehold (generic image with his first letter)
// - example_method_name (specify the method on the User model that returns the URL)
'avatar_type' => 'gravatar',
```

Please note that this does not allow the user to change his profile image.


<a name="add-one-or-more-fields-to-the-register-form"></a>
### Add one or more fields to the Register form

To add a new field to the Registration page, you should:

**Step 1.** Overwrite the registration route, so it leads to _your_ controller, instead of the one in the package. We recommend you add it your ```routes/backpack/custom.php```, BEFORE the route group where you define your CRUDs:

```php
Route::get('admin/register', 'App\Http\Controllers\Admin\Auth\RegisterController')->name('backpack.auth.register');
```

**Step 2.** Create the new RegisterController somewhere in your project, that extends the RegisterController in the package, and overwrites the validation & user creation methods. For example:

```php
<?php
namespace App\Http\Controllers\Admin\Auth;

use Backpack\CRUD\app\Http\Controllers\Auth\RegisterController as BackpackRegisterController;

class RegisterController extends BackpackRegisterController 
{
    /**
     * Get a validator for an incoming registration request.
     *
     * @param array $data
     *
     * @return \Illuminate\Contracts\Validation\Validator
     */
    protected function validator(array $data)
    {
        $user_model_fqn = config('backpack.base.user_model_fqn');
        $user = new $user_model_fqn();
        $users_table = $user->getTable();
        $email_validation = backpack_authentication_column() == 'email' ? 'email|' : '';

        return Validator::make($data, [
            'name'                             => 'required|max:255',
            backpack_authentication_column()   => 'required|'.$email_validation.'max:255|unique:'.$users_table,
            'password'                         => 'required|min:6|confirmed',
        ]);
    }

    /**
     * Create a new user instance after a valid registration.
     *
     * @param array $data
     *
     * @return User
     */
    protected function create(array $data)
    {
        $user_model_fqn = config('backpack.base.user_model_fqn');
        $user = new $user_model_fqn();

        return $user->create([
            'name'                             => $data['name'],
            backpack_authentication_column()   => $data[backpack_authentication_column()],
            'password'                         => bcrypt($data['password']),
        ]);
    }
}
```
Add whatever validation rules & inputs you want, in addition to name and password.

**Step 3.** Add the actual inputs to your HTML. You can easily overwrite the register view by adding this method to the same RegisterController:

```php
    public function showRegistrationForm()
    {

        return backpack_view('auth.register');
    }
```
This will make the registration process pick up a view you can create, in ```resources/views/vendor/backpack/base/auth/register.blade.php```. You can copy-paste the original view, and modify as you please. Including adding your own custom inputs.

