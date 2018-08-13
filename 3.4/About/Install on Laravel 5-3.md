---
title: "Install on Laravel 5.3"
excerpt: ""
---
Each Backpack package has its own installation instructions in its readme file. We duplicate them here for easy access.
[block:callout]
{
  "type": "info",
  "body": "A command-line-wizard installer is planned, but will take some time to develop. Until then, this is the recommended way to install the packages.",
  "title": "Fast installer in the works"
}
[/block]

[block:api-header]
{
  "type": "basic",
  "title": "1. Install Laravel 5.3"
}
[/block]
Use the steps in the [Laravel Documentation](https://laravel.com/docs/5.3#installing-laravel). Most likely it's running:

[block:code]
{
  "codes": [
    {
      "code": "composer create-project --prefer-dist laravel/laravel yourprojectname",
      "language": "shell"
    }
  ]
}
[/block]
Remember to:
- "chmod  -R o+w" the *storage* and *bootstrap/cache* directories
- enter your database information in the *.env* file

[block:callout]
{
  "type": "warning",
  "title": "You may need to generate a key",
  "body": "If you've chosen NOT to install Laravel using the steps above, and you've done it using *laravel new yourprojectname*, remember you need to generate a key, using *php artisan key:generate*"
}
[/block]

[block:api-header]
{
  "type": "basic",
  "title": "2. Install Backpack\\Base"
}
[/block]

1) Run in your terminal:

``` bash
cd yourprojectname
composer require backpack/base
```

2) Add the service providers in config/app.php, at the end of the providers array:
``` php
/*
* Backpack Service Providers...
*/
Backpack\Base\BaseServiceProvider::class,
```

3) Then run a few commands in the terminal:
``` bash
php artisan vendor:publish --provider="Backpack\Base\BaseServiceProvider" #publishes configs, langs, views and AdminLTE files
php artisan vendor:publish --provider="Prologue\Alerts\AlertsServiceProvider" # publish config for notifications - prologue/alerts
php artisan migrate #generates users table (using Laravel's default migrations)
```

4) Make sure the reset password emails have the correct reset link by editing the adding these to your User model:
- before class name: 

```php
use Backpack\Base\app\Notifications\ResetPasswordNotification as ResetPasswordNotification;
```
- as a method inside the User class:

```php
    /**
     * Send the password reset notification.
     *
     * @param  string  $token
     * @return void
     */
    public function sendPasswordResetNotification($token)
    {
        $this->notify(new ResetPasswordNotification($token));
    }
```

5) [optional] Backpack comes with a [generators package](https://github.com/laravel-backpack/generators) that helps you create models, controllers, requests, etc from the command line. [laracasts/generators](https://github.com/laracasts/Laravel-5-Generators-Extended) is an awesome package you can use to generate migrations. Install them only if you want (for your local environment only - that's why the --dev flag). But using them can greatly improve your speed:
```bash
composer require backpack/generators --dev
composer require laracasts/generators --dev
```

6) [optional] Change values in ```config/backpack/base.php``` to make the admin panel your own. Backpack is white label, so you can change everything: menu color, project name, developer name etc.

7) [optional] If your application only has one login screen (for the admins), that means you're not going to use the auth controllers that Laravel provided by default. You're only going to use Backpack's auth controllers. You can keep the Laravel ones in your project, of course. But some people like to delete them to not get confused later on:   
``` bash
# OPTIONAL! Please read the notice above.
rm -rf app/Http/Controllers/Auth #deletes laravel's demo auth controllers
```
[block:api-header]
{
  "type": "basic",
  "title": "3. Install Backpack\\CRUD"
}
[/block]
1) In your terminal:

``` bash
composer require backpack/crud
```

2) Add this provider to your config/app.php, after "base":
```php
        Backpack\CRUD\CrudServiceProvider::class,
```

3) Run these command in this exact order:
```bash
php artisan elfinder:publish #published elfinder assets
php artisan vendor:publish --provider="Backpack\CRUD\CrudServiceProvider" --tag="public" #publish CRUD assets
php artisan vendor:publish --provider="Backpack\CRUD\CrudServiceProvider" --tag="lang" #publish CRUD lang files 
php artisan vendor:publish --provider="Backpack\CRUD\CrudServiceProvider" --tag="config" #publish CRUD and custom elfinder config files
php artisan vendor:publish --provider="Backpack\CRUD\CrudServiceProvider" --tag="elfinder" #publish custom elFinder views
```

4) Define an 'uploads' disk. In your **config/filesystems.php** add this disk:

```php
'uploads' => [
            'driver' => 'local',
            'root' => public_path('uploads'),
        ],
```

5) Create "uploads" folder in your public folder. 

6) [Optional] You can now add the file manager to the menu, in resources/views/vendor/backpack/base/inc/sidebar.blade.php or menu.blade.php:
```html
<li><a href="{{ url(config('backpack.base.route_prefix', 'admin') . '/elfinder') }}"><i class="fa fa-files-o"></i> <span>File manager</span></a></li>
```

[block:api-header]
{
  "type": "basic",
  "title": "---- OPTIONALS ----"
}
[/block]
Everything else is optional. Your project might use them or it might not. Only do each following step if you need the functionality that package provides.
[block:api-header]
{
  "type": "basic",
  "title": "4. Install Backpack\\LangFileManager [optional]"
}
[/block]
[>> See screenshots and installation](https://github.com/Laravel-Backpack/langfilemanager)


Step 1. Install via Composer

``` bash
composer require backpack/langfilemanager
```

Step 2. Add the service provider 

In your config/app.php, add this to the providers array:

``` bash
Backpack\LangFileManager\LangFileManagerServiceProvider::class,
```

Step 3. Run the migration, seed and file publishing

``` bash
php artisan migrate --path=vendor/backpack/langfilemanager/src/database/migrations
php artisan db:seed --class="Backpack\LangFileManager\database\seeds\LanguageTableSeeder"
php artisan vendor:publish --provider="Backpack\LangFileManager\LangFileManagerServiceProvider" --tag="config" #publish the config file
php artisan vendor:publish --provider="Backpack\LangFileManager\LangFileManagerServiceProvider" --tag="lang" #publish the lang files
```

Step 4. [Optional] You can now add it to the menu, in resources/views/vendor/backpack/base/inc/sidebar.blade.php or menu.blade.php:
```html
<li><a href="{{ url(config('backpack.base.route_prefix', 'admin') . '/language') }}"><i class="fa fa-flag-o"></i> <span>Languages</span></a></li>
<li><a href="{{ url(config('backpack.base.route_prefix', 'admin') . '/language/texts') }}"><i class="fa fa-language"></i> <span>Language Files</span></a></li>
```
[block:api-header]
{
  "type": "basic",
  "title": "5. Install Backpack\\BackupManager [optional]"
}
[/block]
[>> See screenshots and installation](https://github.com/Laravel-Backpack/BackupManager)

1) In your terminal

``` bash
composer require backpack/backupmanager
```

2) Then add the service providers to your config/app.php file:

``` 
Spatie\Backup\BackupServiceProvider::class,
Backpack\BackupManager\BackupManagerServiceProvider::class,
```

3) Publish the config file and lang files:

```
php artisan vendor:publish --provider="Backpack\BackupManager\BackupManagerServiceProvider"
```

4) Add a new "disk" to config/filesystems.php:

```php
        // used for Backpack/BackupManager
        'backups' => [
            'driver' => 'local',
            'root'   => storage_path('backups'), // that's where your backups are stored by default: storage/backups
        ],
```
This is where you choose a different driver if you want your backups to be stored somewhere else (S3, Dropbox, Google Drive, Box, etc).

5) [optional] Add a menu item for it in resources/views/vendor/backpack/base/inc/sidebar.blade.php or menu.blade.php:

```html
<li><a href="{{ url(config('backpack.base.route_prefix', 'admin').'/backup') }}"><i class="fa fa-hdd-o"></i> <span>Backups</span></a></li>
```

6) [optional] Modify your backup options in config/laravel-backup.php

7) [optional] Instruct Laravel to run the backups automatically in your console kernel:

```php
// app/Console/Kernel.php

protected function schedule(Schedule $schedule)
{
   $schedule->command('backup:clean')->daily()->at('04:00');
   $schedule->command('backup:run')->daily()->at('05:00');
}
```

8) [optional] If you need to change the path to the mysql_dump command, you can do that in your config/database.php file. For MAMP on Mac OS, add these to your mysql connection:
```
            'dump_command_path' => '/Applications/MAMP/Library/bin/', // only the path, so without 'mysqldump' or 'pg_dump'
            'dump_command_timeout' => 60 * 5, // 5 minute timeout
            'dump_using_single_transaction' => true,
```
[block:api-header]
{
  "type": "basic",
  "title": "6. Install Backpack\\LogManager [optional]"
}
[/block]
[>> See screenshots and installation](https://github.com/Laravel-Backpack/logmanager)


1) Install via composer:

``` bash
composer require backpack/logmanager
```

2) Then add the service provider to your config/app.php file:

```
    Backpack\LogManager\LogManagerServiceProvider::class,
```

3) Add a "storage" filesystem in config/filesystems.php:

```
// used for Backpack/LogManager
'storage' => [
            'driver' => 'local',
            'root'   => storage_path(),
        ],
```

4) [Optional] Configure Laravel to create a new log file for every day, in your .ENV file, if it's not already. Otherwise there will only be one file at all times.

```
    APP_LOG=daily
```

or directly in your config/app.php file:
```
    'log' => env('APP_LOG', 'daily'),
```

5) [Optional] Publish the lang files if you think you'll need to modify them.

```bash
    php artisan vendor:publish --provider="Backpack\LogManager\LogManagerServiceProvider"
```

6) [optional] Add a menu item for it in resources/views/vendor/backpack/base/inc/sidebar.blade.php or menu.blade.php:

```html
<li><a href="{{ url(config('backpack.base.route_prefix', 'admin').'/log') }}"><i class="fa fa-terminal"></i> <span>Logs</span></a></li>
```

[block:api-header]
{
  "type": "basic",
  "title": "7. Useful CRUD: Backpack\\Settings [optional]"
}
[/block]
An interface for the administrator to easily change application settings. Uses Laravel Backpack. 

[>> See screenshots and installation](https://github.com/Laravel-Backpack/settings)


1) In your terminal:

``` bash
composer require backpack/settings
```

2) Add the service provider to your config/app.php file:
```php
Backpack\Settings\SettingsServiceProvider::class,
```

3) Run the migration and add some example settings:
```bash
php artisan vendor:publish --provider="Backpack\Settings\SettingsServiceProvider"
php artisan migrate
php artisan db:seed --class="Backpack\Settings\database\seeds\SettingsTableSeeder"
```

4) [Optional] Add a menu item for it in resources/views/vendor/backpack/base/inc/sidebar.blade.php or menu.blade.php:

```html
<li><a href="{{ url(config('backpack.base.route_prefix', 'admin').'/setting') }}"><i class="fa fa-cog"></i> <span>Settings</span></a></li>
```

[block:api-header]
{
  "type": "basic",
  "title": "8. Useful CRUD: Backpack\\PageManager [optional]"
}
[/block]
[>> See screenshots and installation](https://github.com/Laravel-Backpack/pagemanager)

1) In your terminal

``` bash
composer require backpack/pagemanager
```

2) Then add the service providers to your config/app.php file:

```
Cviebrock\EloquentSluggable\ServiceProvider::class, 
Backpack\PageManager\PageManagerServiceProvider::class,
```

3) Publish the views, migrations and the PageTemplates trait:

```
php artisan vendor:publish --provider="Backpack\PageManager\PageManagerServiceProvider"
```

4) Run the migration to have the database table we need:

```
php artisan migrate
```

5) [optional] Add a menu item for it in resources/views/vendor/backpack/base/inc/sidebar.blade.php or menu.blade.php:

```html
<li><a href="{{ url(config('backpack.base.route_prefix', 'admin').'/page') }}"><i class="fa fa-file-o"></i> <span>Pages</span></a></li>
```
[block:api-header]
{
  "type": "basic",
  "title": "9. Useful CRUD: Backpack\\PermissionManager [optional]"
}
[/block]
An admin panel for user authentication on Laravel 5, using Backpack\CRUD. Add, edit, delete users, roles and permission.

[>> Installation](https://github.com/Laravel-Backpack/PermissionManager#install)
[>> Github](https://github.com/Laravel-Backpack/PermissionManager)
[block:api-header]
{
  "type": "basic",
  "title": "10. Useful CRUD: Backpack\\MenuCRUD [optional]"
}
[/block]
An admin panel for menu items on Laravel 5, using Backpack\CRUD. Add, edit, reorder, nest, rename menu items and link them to Backpack\PageManager pages, external link or custom internal link.
[>> Github](https://github.com/Laravel-Backpack/MenuCRUD)
[block:api-header]
{
  "type": "basic",
  "title": "11. Useful CRUD: Backpack\\NewsCRUD [optional]"
}
[/block]
Since NewsCRUD does not provide any extra functionality other than Backpack\CRUD, it is not a package. It's just a tutorial to show you how this can be achieved. In the future, CRUD examples like this one will be easily installed from the command line, from a central repository. Until then, you will need to manually create the files.
[>> Github](https://github.com/Laravel-Backpack/NewsCRUD)