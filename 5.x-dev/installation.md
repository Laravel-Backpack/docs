# Installation

---

<a name="requirements"></a>
## Requirements

If you can run Laravel 8 or 9, you can install Backpack. Backpack does _not_ have additional requirements. For the following process, we assume:

- you have a [working installation of Laravel](https://laravel.com/docs/9.x#installation) (an existing project is fine, you don't need a *fresh* Laravel install);

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

1) In your project's main directory:

``` bash
# require Backpack using Composer
composer require backpack/crud:"v5.x-dev"
composer require --dev backpack/generators

# run the installation command
php artisan backpack:install
```

> Backpack install is interactive and will ask questions during installation. You can bypass the questions by adding the `--no-interaction` argument to the install command.

Backpack assumes you already have your Eloquent Models properly set up. If you don't, **consider using something to quickly generate Migrations & Models**. You can use anything you want, but here are the options we recommend:

- a) Generate from a **web interface** - [Backpack Devtools](https://backpackforlaravel.com/products/devtools) - premium product, paid separately. A simple GUI to quickly generate Migrations, Models, Factories, Seeders and CRUDs, right from your browser. Works well for entities of all sizes.

- b) Generate from the **command-line** - [Laracasts Generators](https://github.com/laracasts/Laravel-5-Generators-Extended) - free & open-source. Adds a new artisan command so that you can do `php artisan make:migration:schema create_users_table --schema="username:string, email:string:unique"`. Works well for smaller entities.

- c) Generate from a **YAML file** - [LaravelShift's Blueprint](https://blueprint.laravelshift.com/) - free & open-source. Enables you to create a `draft.yml` file in your repo, where you can specify the column using their custom YAML syntax. Works well for small & medium entities.

Take note that:
- By default all users are considered admins; If that's not what you want in your application (you have both users and admins), please:
    - Change ```app/Http/Middleware/CheckIfAdmin.php```, particularly ```checkIfUserIsAdmin($user)```, to make sure you only allow admins to access the admin panel;
    - Change ```app/Providers/RouteServiceProvider::HOME```, which will send logged in (but not admin) users to `/home`, to something that works for your app;
- You should the configuration values in ```config/backpack/base.php``` to make the admin panel your own. Backpack is white label, so you can change everything: menu color, project name, developer name etc.
- If your User model has been moved (it is not ```App\Models\User.php```), please change ```config/backpack/base.php``` to use the correct user model using the ```user_model_fqn``` config key.

That's it. If you already know how to use Backpack, next up you'll probably want to [create CRUD Panels](/docs/{{version}}/crud-tutorial#generate-files).

> If it's your first time installing Backpack, it is **highly recommended** that you go through our [Getting Started series](/docs/{{version}}/getting-started-basics), to understand how Backpack works. That's why we created it - to help you learn how to use this admin panel framework. In ~23 minutes we'll teach you 80% of what you can do, and how.


<a name="install-add-ons"></a>
### Install Add-ons

In case you want to add extra functionality that's already been built, check out [the installation steps for the add-ons we've developed](/docs/{{version}}/install-optionals).

<a name="frequently-asked-questions"></a>
## Frequently Asked Questions

- **Error: The process X exceeded the timeout of 60 seconds.** It might mean GitHub or Packagist is unavailable at the moment. This usually doesn't last for more than a few minutes, so you can run ```php artisan backpack:install --timeout=600``` to increase the timeout to 10 minutes. If this doesn't work either, take a look behind the scenes with ```php artisan backpack:install --timeout=600 --debug```;

- **Error: SQLSTATE[42000]: Syntax error or access violation: 1071 Specified key was too long**. Your MySQL version might be a bit old. Please [apply this quick fix](https://laravel-news.com/laravel-5-4-key-too-long-error), then run ```php artisan migrate:fresh```.

- **Any other installation error?** If you can't install Backpack because of a different error, you can [try the manual installation process](/docs/{{version}}/crud-how-to#manually-install-backpack), which you can tweak to your needs.
