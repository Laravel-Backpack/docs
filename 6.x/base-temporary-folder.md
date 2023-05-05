# Backpack Temporary Folder

---

Starting v6 Backpack comes with a pre-defined temporary folder. We use it to store temporary files needed by our components, like dropzone temporary files for example.

<a name="temporary-folder-configuration"></a>
## Configuration

The temporary folder is configured in `config/backpack/base.php`:

```php
    'temporary_disk' => 'local',
    'temporary_folder' => 'backpack/temp/',
    'purge_temporary_files_older_than' => 72 // hours
```

The first two options are self-explanatory. The third one is the number of hours after which temporary files are deleted and requires further atention.

The process of file **deletion does not happen** automatically.
> **Note**: Backpack components that make use of the temporary folder usually provide an "easier" way to clean up the temporary files. Read the corresponding component documentation.

To make the cleanup process more general, we created a command that you can run periodically, `backpack:purge-temporary-files`. 
It accepts an optional parameter: `--older-than=24`, the number of hours after which temporary files are deleted. If you don't pass it, it will use the value from the config file.

```bash
php artisan backpack:purge-temporary-files --older-than=24
```

You can use any strategy to run this command periodically, like a cron job, a scheduled task or hooking into application termination hooks.

Laravel provides a very easy way to setup your scheduled tasks, if you are on board with that idea. You can read more about it [here](https://laravel.com/docs/10.x/scheduling).

As an example, you can run the command every hour by adding the following line to your `app/Console/Kernel.php` file in the `schedule()` method:
```php
// app/Console/Kernel.php
$schedule->command('backpack:purge-temporary-files')->hourly();
```

After adding this, you need to setup a cron job that will process the Laravel scheduler. You can manually run it in development with `php artisan schedule:run`, for production, you can setup a cron job take care of it for you. You can read more about it [here](https://laravel.com/docs/10.x/scheduling#running-the-scheduler).
