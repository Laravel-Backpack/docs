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
It accepts a `FileNameGeneratorInterface` instance or a closure. As the name implies, this will be used to generate the file name. Read more about in the [Naming uploaded files](#naming-files-when-using-uploaders) section.
- **`allowedExtensions`** - default: **null** (uses the `allowed_upload_extensions` config)
The extensions files uploaded to this field can be stored with. Read more about it in the [Allowed file types](#allowed-file-types) section.

<a name="allowed-file-types"></a>
### Allowed file types

On top of your validation rules, Uploaders only store files whose extension is in an allow list (since `backpack/crud` 6.8.17). This protects your admins even when a field has no validation rules: a file that browsers render as a page (like `svg` or `html`) could run scripts in the session of the admin that opens it, when served from your application domain.

With the default [file namer](#naming-files-when-using-uploaders), the extension a file is stored with comes from **its content**, not from the name the user sent. So a `payload.png` file that contains SVG markup is detected as `svg`, and rejected. When the content can't be identified, the file is stored with the `bin` extension.

By default, the following extensions are allowed:
- **images:** `jpg`, `jpeg`, `png`, `gif`, `webp`, `avif`, `bmp`, `tif`, `tiff`, `ico`, `heic`, `heif`
- **documents:** `pdf`, `txt`, `csv`, `rtf`, `json`, `epub`, `doc`, `docx`, `xls`, `xlsx`, `ppt`, `pptx`, `odt`, `ods`, `odp`
- **archives:** `zip`, `rar`, `7z`, `gz`, `tgz`, `tar`, `bz2`
- **audio:** `mp3`, `wav`, `ogg`, `oga`, `opus`, `m4a`, `aac`, `flac`, `weba`
- **video:** `mp4`, `m4v`, `webm`, `mov`, `avi`, `mkv`, `mpeg`, `mpg`, `ogv`, `3gp`
- **unidentified content:** `bin`

Extensions that web servers may execute (`php`, `phtml`, `phar`, `shtml`, `pl`, `py`, `cgi`, `asp`, `jsp`, `sh`, `exe`, `htaccess` and similar) are **always** rejected, even if you add them to the allow list. They are checked in every part of the file name, so a name like `shell.php.jpg` is rejected too.

**Changing the allowed extensions for all fields**

Set the `allowed_upload_extensions` key in your `config/backpack/crud.php` file:

```php
use Backpack\CRUD\app\Library\Uploaders\Support\FileExtensions;

'allowed_upload_extensions' => [...FileExtensions::DEFAULT_ALLOWED, 'dwg'],
```

If you published the config file before this option existed, you don't need to add it - Backpack will use the default list.

**Changing the allowed extensions for one field**

Pass `allowedExtensions` to the uploader configuration. It replaces the list from the config for that field, so you can use it both to restrict and to extend what is allowed:

```php
use Backpack\CRUD\app\Library\Uploaders\Support\FileExtensions;

// only accept pdfs
CRUD::field('invoice')->type('upload')->withFiles([
    'allowedExtensions' => ['pdf'],
]);

// the default extensions, plus svg
CRUD::field('logo')->type('upload')->withFiles([
    'allowedExtensions' => [...FileExtensions::DEFAULT_ALLOWED, 'svg'],
]);
```

> **IMPORTANT**: Only allow `svg`, `html`, `xml` or other file types that browsers can render when you trust everyone who can upload to that field, or when the files are served from a different domain than your admin panel (eg. a cloud disk). Otherwise, a crafted file can run scripts in the session of the admin who opens it.

**What happens when a file is not allowed**

The form is returned with a validation error for that field, just like any other validation rule. For fields inside a `repeatable` or a relationship, the error is reported on the subfield of the row that sent the file (eg. `gallery.2.photos`).

All the files sent in the form are checked **before** any uploader stores or deletes files. So if one file is rejected, nothing is changed: the new files are not stored and the previous files of the entry are kept. This applies to all fields in the form, including fields inside repeatables and relationships, and to the [Spatie MediaLibrary uploaders](#spatie-media-library).

The `dropzone` field (PRO) checks the files when they are sent to the dropzone upload endpoint, so files that are not allowed never reach the temporary folder, and the error is shown in the field. That endpoint uses the options of fields defined with the array syntax (`'withFiles' => [...]`); for fields defined with `->withFiles([...])` it uses the default file namer and the `allowed_upload_extensions` config.

> **NOTE**: The allow list is a safety net, not a replacement for validation. You should still validate each upload field with the file types it expects, using the [Custom Validation Rules](https://backpackforlaravel.com/docs/{{version}}/custom-validation-rules).

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
- For `upload`, `upload_multiple` and `dropzone` fields, the file name will be the original file name slugged and with a random 4 character string appended to it, to avoid name collisions. The extension is detected from the file content. Eg: `my file.pdf` becomes `my-file-aY5x.pdf`.
- For `image` it will generate a random name for the file, with the extension of the image type. Eg: `5f4a5b6c7d8e9f0a1b2c3d4e5f6a7b8c.jpeg`. Only `jpeg`, `png`, `gif`, `webp` and `avif` images are accepted, and the image content must match its type.

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

The names returned by your file namer must end with an [allowed extension](#allowed-file-types), otherwise the file is rejected. Keep in mind that the original file name (`$file->getClientOriginalName()`) is sent by the user: if you use it, the extension the user picked is the one that gets checked, not the file content. Names without an extension are rejected too.

The default name generator can be changed for all fields in the `file_name_generator` key of your `config/backpack/crud.php` file.

> **NOTE**: If you create your own Uploader class, always name the files you store with `$this->getFileName($file)`: besides calling the configured `fileNamer`, it makes sure the file type is allowed. Call it for all new files **before** deleting or replacing any previous file, so a rejected upload doesn't remove the files the entry already has.

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
