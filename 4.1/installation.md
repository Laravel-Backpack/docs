# Installation

---

<a name="requirements"></a>
## Requirements

If you can run Laravel 8, 7 or 6, you can install Backpack. Backpack does _not_ have additional requirements. For the following process, we assume:

- you have a [working installation of Laravel](https://laravel.com/docs/7.x#installation) (an existing project is fine, you don't need a *fresh* Laravel install);

- you have configured your .ENV file with your database and mail information;

- you can run the ```composer``` command from any directory (you have ```composer``` registered as a global command); if you need to run ```php composer.phar``` or reference another directory, please remember to adapt the commands below to your configuration;

<a name="installation"></a>
## Installation

<a name="install-core-packages"></a>
### Install Core Packages

0) Open your project folder in your terminal:

```bash
cd your-laravel-project-name
```

1) In your project's main directory, install CRUD using composer:

``` bash
composer require backpack/crud:"4.1.*"

# you might also want to install these tools that help during development
composer require backpack/generators --dev
composer require laracasts/generators --dev
```

2) Now run the installation commands for Backpack:

``` bash
php artisan backpack:install
```

3) Take note that:
- By default all users are considered admins; If that's not what you want in your application (you have both users and admins), please:
    - Change ```app/Http/Middleware/CheckIfAdmin.php```, particularly ```checkIfUserIsAdmin($user)```, to make sure you only allow admins to access the admin panel;
    - Change ```app/Providers/RouteServiceProvider::HOME```, which will send logged in (but not admin) users to `/home`, to something that works for your app; This is only needed in Laravel 7+; 
- Change configuration values in ```config/backpack/base.php``` to make the admin panel your own. Backpack is white label, so you can change everything: menu color, project name, developer name etc.
- If your User model has been moved (it is not ```App\User.php```), please change ```config/backpack/base.php``` to use the correct user model using the ```user_model_fqn``` config key. If you are using Laravel 8 and up, you need to change this. Because by default Laravel 8 and up save the ```User``` model inside the ```Models``` directory.

That's it. If you already know how to use Backpack, next up you'll probably want to [create CRUD Panels](/docs/{{version}}/crud-tutorial#generate-files).

> If it's your first time installing Backpack, it is **highly recommended** that you go through our [Getting Started series](/docs/{{version}}/getting-started-basics), to understand how Backpack works. That's why we created it - to help you learn how to use this admin panel framework. In ~23 minutes we'll teach you 80% of what you can do, and how.


<a name="install-add-ons"></a>
### Install Add-ons

In case you want to add extra functionality that's already been built, check out [the installation steps for the add-ons we've developed](/docs/{{version}}/install-optionals).

<a name="frequently-asked-questions"></a>
## Frequently Asked Questions

- **Error: The process X exceeded the timeout of 60 seconds.** It might mean Github or Packagist is unavailable at the moment. This usually doesn't last for more than a few minutes, so you can run ```php artisan backpack:install --timeout=600``` to increase the timeout to 10 minutes. If this doesn't work either, take a look behind the scenes with ```php artisan backpack:install --timeout=600 --debug```;

- **Error: SQLSTATE[42000]: Syntax error or access violation: 1071 Specified key was too long**. Your MySQL version might be a bit old. Please [apply this quick fix](https://laravel-news.com/laravel-5-4-key-too-long-error), then run ```php artisan migrate:fresh```.

- **Any other installation error?** If you can't install Backpack because of a different error, you can [try the manual installation process](/docs/{{version}}/crud-how-to#manually-install-backpack), which you can tweak to your needs.
