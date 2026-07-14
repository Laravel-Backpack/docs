# MediaLibrary Uploaders <span class="badge badge-pill badge-success">FREE</span>

--

<a name="about"></a>
## About

Backpack MediaLibrary Uploaders integrates Backpack CRUD with [Spatie Media Library](https://github.com/spatie/laravel-medialibrary).
It makes it easy to store uploaded files as media and retrieve them using Backpack fields and columns.

With this package, you can:
- store uploaded files as Spatie media using `->withMedia()`;
- use the same helper on fields, columns and repeatable subfields;
- override defaults and customize the media-saving process.

<a name="requirements"></a>
## Requirements

- [Backpack\CRUD](https://github.com/Laravel-Backpack/CRUD)
- [spatie/laravel-medialibrary](https://github.com/spatie/laravel-medialibrary) v10 or v11

> Make sure you have installed and configured Spatie Media Library before using this package.

<a name="installation"></a>
## Installation

Install the package via Composer:

```bash
composer require backpack/medialibrary-uploaders
```

Then install Spatie Media Library and its migrations:

```bash
composer require "spatie/laravel-medialibrary:^11.0"
php artisan vendor:publish --provider="Spatie\MediaLibrary\MediaLibraryServiceProvider" --tag="migrations"
php artisan migrate
php artisan storage:link
```

Optionally publish the Spatie Media Library config file:

```bash
php artisan vendor:publish --provider="Spatie\MediaLibrary\MediaLibraryServiceProvider" --tag="config"
```

<a name="usage"></a>
## Usage

Use `->withMedia()` on any upload field or column to store uploaded files as media.

```php
CRUD::field('avatar')->type('image')->withMedia();
CRUD::column('avatar')->type('image')->withMedia();
```

For repeatable fields with uploads:

```php
CRUD::field('gallery')
    ->label('Image Gallery')
    ->type('repeatable')
    ->subfields([
        [
            'name' => 'main_image',
            'label' => 'Main Image',
            'type' => 'image',
            'withMedia' => true,
        ],
    ]);
```

<a name="advanced"></a>
## Advanced Use

### Override the defaults

You can pass a configuration array to `->withMedia([])` or `'withMedia' => []` to change the default media collection settings.

```php
CRUD::field('main_image')
    ->label('Main Image')
    ->type('image')
    ->withMedia([
        'collection' => 'my_collection',
        'disk' => 'my_disk',
        'mediaName' => 'custom_media_name',
    ]);
```

### Customize the saving process

Use the `whenSaving` callback to customize the Spatie media object before it is saved.

```php
CRUD::field('main_image')
    ->label('Main Image')
    ->type('image')
    ->withMedia([
        'whenSaving' => function ($spatieMedia, $backpackMediaObject) {
            return $spatieMedia->usingFileName('main_image.jpg')
                                ->withResponsiveImages();
        },
    ]);
```

Do not call Spatie methods that Backpack already handles automatically, such as `toMediaCollection()`, `setName()`, `usingName()`, or `toMediaLibrary()` inside the callback.

<a name="security"></a>
## Security

If you discover a security issue, report it through the package repository or maintainer contacts.

<a name="license"></a>
## License

This project is released under the package license. See the repository for full license details.
