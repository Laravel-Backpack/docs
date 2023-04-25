# Uploaders

---

<a name="upload-about"></a>
## About

Uploading and managing files is a common task in Admin Panels. Starting with Backpack v6, you can fully setup your upload fields in your field definition. No more mutators, manual validation of input, or custom code to handle the files.

<a name="upload-how-it-works"></a>
## How it works

When adding an upload field (`upload`, `upload_multiple`, `image` or `dropzone`) to your operations, you can now tell Backpack that you want it to upload those files to a specific disk, path and customize some aditional things. Get to know `withFiles()`. 
    
```php
CRUD::field('avatar')
    ->type('upload')
    ->withFiles();
```
> **IMPORTANT**: make sure you linked the `storage` folder to your `public` folder. You can do that by running `php artisan storage:link` in your terminal.

That's it. Backpack will now handle the upload, storage and deletion of the files for you. By default it will use `public` disk, and will delete the files when the entry is deleted.

<a name="upload-configuration"></a>
## Configuring the uploads

The `withFiles()` method accepts an array of options that you can use to customize the upload. 

```php
CRUD::field('avatar')
    ->type('upload')
    ->withFiles([
        'disk' => 'public', // the disk where file will be stored
        'path' => 'uploads', // the path inside the disk where file will be stored
        'deleteWhenEntryIsDeleted' => true, // if the files should be deleted when entry is deleted

        // cloud disks like s3 allow the usage of temporary urls
        'temporary' => false, 
        'expiration' => 1 // the corresponding temporary url expiration time in minutes
]);
```
**Note**: `disk` and `prefix` (called `path` in withFiles() configuration), when defined in the field as attributes, will take precedence over the definition in `withFiles()` configuration. 
Make sure you are not defining those attributes in both places.

<a name="upload-name-files"></a>
#### Naming uploaded files

Backpack implements a default naming strategy for uploaded files that we have found out to work well in majority of scenarios. 

For `upload`, `upload_multiple` and `dropzone` fields, the file name will be the original file name slugged and with a random 4 character string appended to it. This is to avoid name collisions. Eg: `my file.pdf` becomes `my-file-aY5x.pdf`.

For `image` it will generate a unique name for the file, and will keep the original extension. Eg: `my file.jpg` becomes `5f4a5b6c7d8e9f0a1b2c3d4e5f6a7b8c.jpg`.

You can customize the naming strategy by creating a class that implements `FileNameGeneratorInterface` and pass it to the upload configuration (the default used by Backpack).

```php
CRUD::field('avatar')
    ->type('upload')
    ->withFiles([
        'fileNamer' => \Backpack\CRUD\app\Library\Uploaders\Support\FileNameGenerator::class,
]);

// alternativelly you can pass a closure:
->withFiles([
    'fileNamer' => function($file, $uploader) { return 'the_file_name.png'; },
])
```
<a name="upload-subfields-array"></a>
#### Subfields and array field definition

Subfields and array defined fields are also supported. You can use the `withFiles` as an attribute on them too.

```php
$this->crud->addField([
    'name' => 'attachments',
    'type' => 'upload_multiple',
    'withFiles' => true,
]);

// subfields
'subfields' => [
    [
        'name' => 'avatar',
        'type' => 'upload', 
        'withFiles' => true
    ]
]

// or if you need to configure the upload pass the configuration array:
$this->crud->addField([
    'name' => 'profile_picture',
    'type' => 'image',
    'withFiles' => [
        'path' => 'uploads/profile_pictures',
    ],
]);
```