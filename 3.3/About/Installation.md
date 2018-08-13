---
title: "Installation"
excerpt: ""
---
Each Backpack package has its own installation instructions in its readme file. We duplicate them here for easy access.

This page assumes that you have composer registered as a global command, and you can run the ```composer``` command from any directory (you don't need to run ```php composer.phar``` or reference another directory).
[block:api-header]
{
  "type": "basic",
  "title": "1. Laravel 5.6"
}
[/block]
To install Laravel, use the steps in the [Laravel Documentation](https://laravel.com/docs/5.6#installing-laravel). Most likely it's running:

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
  "title": "2. Backpack\\Base"
}
[/block]

1) Run in your terminal:

``` bash
composer require backpack/base
 
php artisan backpack:base:install
```
[block:callout]
{
  "type": "info",
  "body": "If you get the error above, it might mean Github or Packagist is unavailable at the moment. This usually doesn't last for more than a few minutes, so you can run ```php artisan backpack:base:install --timeout=600``` to increase the timeout to 10 minutes. If this doesn't work either, take a look behind the scenes with ```php artisan backpack:base:install --timeout=600 --debug```, and refer to [this thread](https://github.com/Laravel-Backpack/Base/issues/217).",
  "title": "Error: The process X exceeded the timeout of 60 seconds."
}
[/block]

[block:callout]
{
  "type": "info",
  "body": "If you receive the error above, your MySQL version might be a bit old. Please [apply this quick fix](https://laravel-news.com/laravel-5-4-key-too-long-error), then run ```php artisan migrate:fresh```.",
  "title": "Error: SQLSTATE[42000]: Syntax error or access violation: 1071 Specified key was too long;"
}
[/block]

[block:callout]
{
  "type": "info",
  "title": "Any other installation error?",
  "body": "If you can't install Backpack\\Base because of a different error, you can [try the manual installation process](https://laravel-backpack.readme.io/docs/frequently-asked-questions#section-how-do-i-manually-install-base), which you can tweak to your needs."
}
[/block]
2) Make sure the reset password emails have the correct reset link by adding these to your User model:
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

Done!

You can now optionally:
- Change configuration values in ```config/backpack/base.php``` to make the admin panel your own. Backpack is white label, so you can change everything: menu color, project name, developer name etc.
- If your application only has one login screen (for the admins), that means you're not going to use the auth controllers that Laravel provided by default. You're only going to use Backpack's auth controllers. You can keep the Laravel ones in your project, of course. But some people like to delete them to not get confused later on:   
``` bash
# OPTIONAL! Please read the notice above.
rm -rf app/Http/Controllers/Auth #deletes laravel's demo auth controllers
```
[block:api-header]
{
  "type": "basic",
  "title": "3. Backpack\\CRUD"
}
[/block]
1) In your terminal:

``` bash
composer require backpack/crud
```

2) Run these command in this exact order:
```bash
mkdir public/uploads #create a public/uploads directory
php artisan elfinder:publish #published elfinder assets
php artisan vendor:publish --provider="Backpack\CRUD\CrudServiceProvider" --tag="public" #publish CRUD assets
php artisan vendor:publish --provider="Backpack\CRUD\CrudServiceProvider" --tag="lang" #publish CRUD lang files 
php artisan vendor:publish --provider="Backpack\CRUD\CrudServiceProvider" --tag="config" #publish CRUD and custom elfinder config files
php artisan vendor:publish --provider="Backpack\CRUD\CrudServiceProvider" --tag="elfinder" #publish custom elFinder views
```

3) [Optional] If you'd also like a file manager, define an 'uploads' disk. In your **config/filesystems.php** add this disk:

```php
'uploads' => [
            'driver' => 'local',
            'root' => public_path('uploads'),
        ],
```

You can now add the file manager to the menu, in resources/views/vendor/backpack/base/inc/sidebar.blade.php or menu.blade.php:
```html
<li><a href="{{  backpack_url('elfinder') }}"><i class="fa fa-files-o"></i> <span>File manager</span></a></li>
```

[block:api-header]
{
  "type": "basic",
  "title": "---- OPTIONALS ----"
}
[/block]
Everything else is optional. Your project might use them or it might not. Only do each following steps if you need the functionality that package provides.
[block:api-header]
{
  "type": "basic",
  "title": "4. LangFileManager"
}
[/block]
[>> See screenshots and installation](https://github.com/Laravel-Backpack/langfilemanager)


Step 1. Install via Composer

``` bash
composer require backpack/langfilemanager
```

Step 2. Run the migration, seed and file publishing

``` bash
provider="Backpack\LangFileManager\LangFileManagerServiceProvider" --tag="migrations" #publish the config file
php artisan migrate
php artisan db:seed --class="Backpack\LangFileManager\database\seeds\LanguageTableSeeder"
php artisan vendor:publish --provider="Backpack\LangFileManager\LangFileManagerServiceProvider" --tag="config" #publish the config file
php artisan vendor:publish --provider="Backpack\LangFileManager\LangFileManagerServiceProvider" --tag="lang" #publish the lang files
```

Step 3. [Optional] You can now add it to the menu, in resources/views/vendor/backpack/base/inc/sidebar.blade.php or menu.blade.php:
```html
<li><a href="{{  backpack_url('language') }}"><i class="fa fa-flag-o"></i> <span>Languages</span></a></li>
<li><a href="{{ backpack_url( 'language/texts') }}"><i class="fa fa-language"></i> <span>Language Files</span></a></li>
```
[block:api-header]
{
  "type": "basic",
  "title": "5. BackupManager"
}
[/block]
[>> See screenshots and installation](https://github.com/Laravel-Backpack/BackupManager)

1) In your terminal

``` bash
composer require backpack/backupmanager
```

2) Publish the config file and lang files:

```
php artisan vendor:publish --provider="Backpack\BackupManager\BackupManagerServiceProvider"
```

3) Add a new "disk" to config/filesystems.php:

```php
        // used for Backpack/BackupManager
        'backups' => [
            'driver' => 'local',
            'root'   => storage_path('backups'), // that's where your backups are stored by default: storage/backups
        ],
```
This is where you choose a different driver if you want your backups to be stored somewhere else (S3, Dropbox, Google Drive, Box, etc).

4) [optional] Add a menu item for it in resources/views/vendor/backpack/base/inc/sidebar.blade.php or menu.blade.php:

```html
<li><a href="{{ backpack_url('backup') }}"><i class="fa fa-hdd-o"></i> <span>Backups</span></a></li>
```

5) [optional] Modify your backup options in ```config/laravel-backup.php```, then run ```php artisan backup:run``` to make sure it's still working.

6) [optional] Instruct Laravel to run the backups automatically in your console kernel:

```php
// app/Console/Kernel.php

protected function schedule(Schedule $schedule)
{
   $schedule->command('backup:clean')->daily()->at('04:00');
   $schedule->command('backup:run')->daily()->at('05:00');
}
```

7) [optional] If you need to change the path to the mysql_dump command, you can do that in your config/database.php file. For MAMP on Mac OS, add these to your mysql connection:
```
            'dump_command_path' => '/Applications/MAMP/Library/bin/', // only the path, so without 'mysqldump' or 'pg_dump'
            'dump_command_timeout' => 60 * 5, // 5 minute timeout
            'dump_using_single_transaction' => true,
```
[block:api-header]
{
  "type": "basic",
  "title": "6. LogManager"
}
[/block]
[>> See screenshots and installation](https://github.com/Laravel-Backpack/logmanager)


1) Install via composer:

``` bash
composer require backpack/logmanager
```

2) Add a "storage" filesystem disk in config/filesystems.php:

```
// used for Backpack/LogManager
'storage' => [
            'driver' => 'local',
            'root'   => storage_path(),
        ],
```

3) [Optional] Configure Laravel to create a new log file for every day, in your .ENV file, if it's not already. Otherwise there will only be one file at all times.

```
    APP_LOG=daily
```

or directly in your config/app.php file:
```
    'log' => env('APP_LOG', 'daily'),
```

4) [optional] Add a menu item for it in resources/views/vendor/backpack/base/inc/sidebar.blade.php or menu.blade.php:

```html
<li><a href="{{ backpack_url('log') }}"><i class="fa fa-terminal"></i> <span>Logs</span></a></li>
```

[block:api-header]
{
  "type": "basic",
  "title": "7. Settings"
}
[/block]
An interface for the administrator to easily change application settings. Uses Laravel Backpack. 

[>> See screenshots and installation](https://github.com/Laravel-Backpack/settings)


1) In your terminal:

``` bash
composer require backpack/settings
```

2) Run the migration and add some example settings:
```bash
php artisan vendor:publish --provider="Backpack\Settings\SettingsServiceProvider"
php artisan migrate
php artisan db:seed --class="Backpack\Settings\database\seeds\SettingsTableSeeder"
```

3) [Optional] Add a menu item for it in resources/views/vendor/backpack/base/inc/sidebar.blade.php or menu.blade.php:

```html
<li><a href="{{ backpack_url('setting') }}"><i class="fa fa-cog"></i> <span>Settings</span></a></li>
```

[block:api-header]
{
  "type": "basic",
  "title": "8. PageManager"
}
[/block]
[>> See screenshots and installation](https://github.com/Laravel-Backpack/pagemanager)

1) In your terminal

``` bash
composer require backpack/pagemanager
```

2) Publish the views, migrations and the PageTemplates trait; run the migrations:

```
php artisan vendor:publish --provider="Backpack\PageManager\PageManagerServiceProvider"
php artisan migrate
```

3) [optional] Add a menu item for it in resources/views/vendor/backpack/base/inc/sidebar.blade.php or menu.blade.php:

```html
<li><a href="{{backpack_url('page') }}"><i class="fa fa-file-o"></i> <span>Pages</span></a></li>
```
[block:api-header]
{
  "type": "basic",
  "title": "9. PermissionManager"
}
[/block]
An admin panel for user authentication on Laravel 5, using Backpack\CRUD. Add, edit, delete users, roles and permission.

[>> Installation](https://github.com/Laravel-Backpack/PermissionManager#install)
[>> Github](https://github.com/Laravel-Backpack/PermissionManager)
[block:api-header]
{
  "type": "basic",
  "title": "10. MenuCRUD"
}
[/block]
An admin panel for menu items on Laravel 5, using Backpack\CRUD. Add, edit, reorder, nest, rename menu items and link them to Backpack\PageManager pages, external link or custom internal link.

[>> Github](https://github.com/Laravel-Backpack/MenuCRUD)
[block:api-header]
{
  "type": "basic",
  "title": "11. NewsCRUD"
}
[/block]
Since NewsCRUD does not provide any extra functionality other than Backpack\CRUD, it is not a package. It's just a tutorial to show you how this can be achieved. In the future, CRUD examples like this one will be easily installed from the command line, from a central repository. Until then, you will need to manually create the files.

[>> Github](https://github.com/Laravel-Backpack/NewsCRUD)