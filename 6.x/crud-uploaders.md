# Uploaders

---

<a name="upload-about"></a>
## About

Uploading and managing files is a common task in Admin Panels. Starting with Backpack v6, you can fully setup your upload fields in your field definition, using purpose-built classes we call Uploaders. No more need to create mutators, manual validation of input or custom code to handle the files - though you can still do that, if you want.

<a name="upload-how-it-works"></a>
## How it works

When adding an upload field (`upload`, `upload_multiple`, `image` or `dropzone`) to your operation, tell Backpack that you want to use the appropriate Uploader, by using `withFiles()`:

```php
CRUD::field('avatar')->type('upload')->withFiles();
```

That's it. Backpack will now handle the upload, storage and deletion of the files for you. By default it will use `public` disk, and will delete the files when the entry is deleted(*).

> **IMPORTANT**:
> - Make sure you've linked the `storage` folder to your `public` folder. You can do that by running `php artisan storage:link` in your terminal.
> - (*) If you want your files to be deleted when the entry is deleted, please [Configure File Deletion](#deleting-files-when-entry-is-deleted)


<a name="upload-configuration"></a>
## Configuring the Uploaders

The `withFiles()` method accepts an array of options that you can use to customize the upload.

```php
CRUD::field('avatar')
    ->type('upload')
    ->withFiles([
        'disk' => 'public', // the disk where file will be stored
        'path' => 'uploads', // the path inside the disk where file will be stored
]);
```
**Note**: If you've defined `disk` or `prefix` on the field, you no longer need to define `disk` or `path` within `withFiles()` - it will pick those up. Make sure you are not defining both.


**Configuration options:**

- **`disk`** - default: **`public`**
The disk where the file will be stored. You can use any disk defined in your `config/filesystems.php` file.
- **`path`** - default: **`/`**
The path inside the disk where the file will be stored. It maps to `prefix` in field definition.
- **`deleteWhenEntryIsDeleted`** - default: **`true`** (**NEED ADDITIONAL CONFIGURATION**!! See: [Configure File Deletion](#deleting-files-when-entry-is-deleted))
The files will be deleted when the entry is deleted. Please take into consideration that `soft deleted models` don't delete the files.
- **`temporaryUrl`** - default: **`false`**
Some cloud disks like `s3` support the usage of temporary urls for display. Set this option to true if you want to use them.
- **`temporaryUrlExpirationTime`** - default: **`1`**
When `temporaryUrl` is set to `true`, this configures the amount of time in minutes the temporary url will be valid for.
- **`uploader`** - default: **null**
This allows you to overwrite or set the uploader class for this field. You can use any class that implements `UploaderInterface`.
- **`fileNamer`** - default: **null**
It accepts a `FileNameGeneratorInterface` instance or a closure. As the name implies, this will be used to generate the file name. Read more about in the [Naming uploaded files](#upload-name-files) section.

<a name="upload-validation"></a>
### Upload Validation

We can't stress enough how **IMPORTANT** is to properly validate and autenticate the file uploads and the upload endpoints. We have created a set of custom validation rules that will make validation of upload fields dead-simple. Please see the [Custom Validation Rules](https://backpackforlaravel/docs/custom-validation-rules) section for more information.

<a name="handling-uploaders-in-relationship-fields"></a>
### Handling uploads in relationship fields

**IMPORTANT**: Please make sure you are **NOT** casting the uploaders attributes in your model. If you need a casted attribute to work with the values somewhere else, please create a different attribute that copies the uploader attribute value and manually cast it how you need it.

Some relationships require additional configuration to properly work with the Uploaders, here are some examples:

- **`BelongsToMany`** 

In this relationships, you should add the upload fields to the `withPivot()` method and create a Pivot model where Uploaders register their events. [Laravel Docs - Pivot Models](https://laravel.com/docs/10.x/eloquent-relationships#defining-custom-intermediate-table-models)

Take for example an `Article` model has a `BelongsToMany` relationship defined with `Categories` model:

```php
// Article model
public function categories() {
    $this->belongsToMany(Category::class);
}
```

To use an Uploader in this relation, you should create the `ArticleCategory` pivot model, and tell Laravel to use it. 

```php
use Illuminate\Database\Eloquent\Relations\Pivot;

class ArticleCategory extends Pivot
{

}


// and in your article/category models, update the relationship to:
public function categories() {
    $this->belongsToMany(Category::class)->withPivot('picture')->using(ArticleCategory::class); //assuming picture is the pivot field where you store the uploaded file path.
}
```

- **`MorphToMany`** 

Everything like the previous `belongsToMany`, but the pivot model needs to extend `MorphPivot`.

```php
use Illuminate\Database\Eloquent\Relations\MorphPivot;

class ArticleCategory extends MorphPivot
{

}


//in your model
public function categories() {
    $this->morphToMany(Category::class)->withPivot('picture')->using(ArticleCategory::class); //assuming picture is the pivot field where you store the uploaded file path.
}
```

<a name="naming-files-when-using-uploaders"></a>
### Naming files when using Uploaders

Backpack provides a naming strategy for uploaded files that works well for most scenarios:
- For `upload`, `upload_multiple` and `dropzone` fields, the file name will be the original file name slugged and with a random 4 character string appended to it, to avoid name collisions. Eg: `my file.pdf` becomes `my-file-aY5x.pdf`.
- For `image` it will generate a unique name for the file, and will keep the original extension. Eg: `my file.jpg` becomes `5f4a5b6c7d8e9f0a1b2c3d4e5f6a7b8c.jpg`.

You can customize the naming strategy by creating a class that implements `FileNameGeneratorInterface` and pass it to the upload configuration (the default used by Backpack).

```php
CRUD::field('avatar')->type('upload')->withFiles([
        'fileNamer' => \Backpack\CRUD\app\Library\Uploaders\Support\FileNameGenerator::class,
]);

// alternativelly you can pass a closure:
->withFiles([
    'fileNamer' => function($file, $uploader) { return 'the_file_name.png'; },
])
```
<a name="subfields-in-uploaders"></a>
### Subfields in Uploaders

You can also use uploaders in subfields. The configuration is the same as for regular fields, just use the same `withFiles` key and pass it `true` if no further configuration is required.

```php
// subfields array
[
    [
        'name' => 'avatar',
        'type' => 'upload',
        'withFiles' => true
    ],
    [
        'name' => 'attachments',
        'type' => 'upload_multiple',
        'withFiles' => [
            'path' => 'attachments',
        ],
    ],
]
```

<a name="deleting-files-when-entry-is-deleted"></a>
### Configure uploaded files to be automatically deteled

To automatically delete the uploaded files when the entry is deleted _in the admin panel_, we need to setup the upload fields in the `DeleteOperation` too:

```php
protected function setupDeleteOperation()
{
    CRUD::field('photo')->type('upload')->withFiles();

    // Alternatively, if you are not doing much more than defining fields in your create operation:
    // $this->setupCreateOperation();
}
```

Alternatively, you can manually delete the file in your Model, using the `deleted` Eloquent model event. That would ensure the file gets deleted _even if_ the entry was deleted from outside the admin panel.

```php
class SomeModel extends Model
{
    protected static function booted()
    {
        static::deleted(function ($model) {
            // delete the file
            Storage::disk('my_disk')->delete($model->photo);
        });
    }
}
```

<a name="custom-upload-fields"></a>
### Configuring uploaders in custom fields

When using uploads in custom fields, you need to tell Backpack what Uploader to use for that custom field type. 

Imagine that you created a custom upload field starting from backpack `upload` field type with: `php artisan backpack:field custom_upload --from=upload`.

You can tell Backpack what Uploader to use in 2 ways:

- In the custom field defininiton inside the uploader configuration:
```php
CRUD::field('custom_upload')->withFiles([
    'uploader' => \Backpack\CRUD\app\Library\Uploaders\SingleFile::class,
]);
```
- Or you can add it globally for that field type by adding in your Service Provider `boot()` method: 
```php
app('UploadersRepository')->addUploaderClasses(['custom_upload' => \Backpack\CRUD\app\Library\Uploaders\SingleFile::class], 'withFiles');
```

<a name="spatie-media-library"></a>
### Uploaders for Spatie MediaLibrary

The 3rd party package [`spatie/laravel-medialibrary`](https://spatie.be/docs/laravel-medialibrary/) gives you the power to easily associate files with Eloquent models. The package is incredibly popular, time-tested and well maintained.

To have Backpack upload and retrieve files using this package, we've created special Uploaders. Then it will be as easy as doing `CRUD::field('avatar')->type('image')->withMedia();`. For more information and installation instructions please see the docs on Github for [`backpack/medialibrary-uploaders`](https://github.com/Laravel-Backpack/medialibrary-uploaders).
