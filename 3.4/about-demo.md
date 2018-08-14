# Demo

The best way to see if you like Backpack is to install a working demo of Laravel with all Backpack packages pre-installed.

However, if you do like it, please install it using [the recommended installation procedure](https://laravel-backpack.readme.io/v3.4/docs/install-on-laravel-56). That will take you through each package individually and you'll end up understanding how it works a lot better.
[block:callout]
{
  "type": "warning",
  "title": "Please don't use this to start your real projects",
  "body": "Unless all Backpack packages are needed in your new project, you'll end up having packages that you're not using in your ```vendor/``` folder."
}
[/block]
1) Run in your terminal:

```shell
git clone https://github.com/Laravel-Backpack/demo.git backpack-demo
```

2) Set your database information in your .env file (use the .env.example as an example);

3) Run in your backpack-demo folder:
``` shell
composer install
php artisan key:generate
php artisan migrate
php artisan db:seed --class="Backpack\Settings\database\seeds\SettingsTableSeeder"
php artisan db:seed
```

## Usage 

Once your database has been set up:

1. Your admin panel is available at http://localhost/backpack-demo/admin
2. Login with email ```admin@example.com```, password ```admin```
3. You can register a different account, to check out the process and see your gravatar inside the admin panel. 
4. By default, registration is open only in your local environment. Check out ```config/backpack/base.php``` to change this and other preferences.
5. The real benefit of Backpack is not in its packages, but in how easy it is to code something custom. Go through the [CRUD How it works](https://laravel-backpack.readme.io/docs/crud-example) to understand it, then create a CRUD panel for an entity, using the fast procedure outlined at the end of that page. Say... Product.


[block:callout]
{
  "type": "info",
  "title": "vhost configurations",
  "body": "Depending on your vhost configuration you might need to access the application via a different url, for example if you're using `artisan serve` you can access it on http://127.0.0.1:8000/admin/register - if you're using laravel valet, then it may look like http://backpack-demo.local/admin/register - you will need to access the url which matches your systems configuration. If you do not understand how to configure your virtual hosts, we suggest watching Laracasts Episode #1 to quickly get started https://laracasts.com/series/laravel-from-scratch/episodes/1"
}
[/block]