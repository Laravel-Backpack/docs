---
title: "Field Types"
excerpt: ""
---
[About field types](https://laravel-backpack.readme.io/v3.0/docs/crud-fields#section-about-field-types)
[Existing Field Types](https://laravel-backpack.readme.io/v3.0/docs/crud-fields#standard-field-types)
[Custom Field Types](https://laravel-backpack.readme.io/v3.0/docs/crud-fields#section-custom-field-types)



# ABOUT FIELD TYPES

Field types are defined in the Controller and settle the way that particular field looks in the add / edit view.

Think of the field type as the type of input:

```html
<input type=”text” />
```

For some entities, you won't just need text inputs, you'll need datepickers, upload buttons, 1-n relationship, n-n relationships, textareas, etc.

For each of them, you only need to define it properly in the Controller, in the “fields” array. All field types will need at least three things: 
- the name of the column in the database (and the name of the input, implicitly)
- the human-readable label for the input
- the type of the input

The following fields are to be used as $field_definition_array inside:

```php
$this->crud->addField($field_definition_array, 'update/create/both');
```

# OPTIONAL FIELD ATTRIBUTES

All fields require at least a `name` to be displayed however defining a `label` and `type` are recommended which you will read more about what types are available by default below. All fields also support additional parameters to be passed:

```php
[
    'prefix' => '',
    suffix' => '',
    'default'    => 'some value', // default value
    'hint'       => 'Some hint text', // helpful text, show up after input
    'attributes' => [
       'placeholder' => 'Some text when empty',
       'class' => 'form-control some-class'
     ], // extra HTML attributes and values your input might need
     'wrapperAttributes' => [
       'class' => 'form-group col-md-12'
     ], // extra HTML attributes for the field wrapper - mostly for resizing fields 
     'readonly'=>'readonly',
]
```
[block:api-header]
{
  "type": "basic",
  "title": "STANDARD FIELD TYPES"
}
[/block]

[block:parameters]
{
  "data": {
    "0-0": "[address](#section-address)",
    "1-0": "[browse](#section-browse)",
    "2-0": "[browse_multiple](#section-browse_multiple)",
    "3-0": "[base64_image](#section-base64_image)",
    "4-0": "[checkbox](#section-checkbox)",
    "5-0": "[checklist](#section-checklist)",
    "6-0": "[checklist_dependency](#section-checklist_dependency)",
    "7-0": "[ckeditor](#section-ckeditor)",
    "8-0": "[color](#section-color)",
    "9-0": "[color_picker](#section-color_picker)",
    "10-0": "[custom_html](#section-custom_html-v207)",
    "0-1": "[hidden](#section-hidden)",
    "1-1": "[icon_picker](#section-icon_picker)",
    "2-1": "[image](#section-image)",
    "3-1": "[month](#section-month)",
    "4-1": "[number](#section-number)",
    "5-1": "[page_or_link](#section-page_or_link)",
    "6-1": "[password](#section-password-field)",
    "7-1": "[radio](#section-radio)",
    "8-1": "[range](#section-range)",
    "9-1": "[select](#section-select-1-n-relationship) (1-n relationship)",
    "10-1": "[select2](#section-select2-1-n-relationship) (1-n relationship)",
    "0-2": "[simplemde](#section-simplemde)",
    "1-2": "[summernote](#section-summernote)",
    "2-2": "[table](#section-table)",
    "3-2": "[text](#section-text)",
    "4-2": "[textarea](#section-textarea)",
    "5-2": "[time](#section-time)",
    "6-2": "[tinymce](#section-tinymce)",
    "7-2": "[upload](#section-upload)",
    "8-2": "[upload_multiple](#section-upload_multiple)",
    "9-2": "[url](#section-url)",
    "10-2": "[video](#section-video)",
    "11-0": "[date](#section-date)",
    "11-2": "[view](#section-view)",
    "11-1": "[select_multiple](#section-select_multiple-n-n-relationship) (n-n relationship)",
    "12-1": "[select2_multiple](#section-select2_multiple-n-n-relationship) (n-n relationship)",
    "12-2": "[week](#section-week)",
    "12-0": "[date_picker](#section-date_picker)",
    "13-2": "[wysiwyg](#section-wysiwyg)",
    "14-0": "[datetime](#section-datetime)",
    "13-0": "[date_range](#section-date_range)",
    "15-0": "[datetime_picker](#section-datetime_picker)",
    "13-1": "[select_from_array](#section-select_from_array)",
    "14-1": "[select2_from_array](#section-select2_from_array)",
    "16-0": "[email](#section-email)",
    "15-1": "[select2_from_ajax](#section-select2_from_ajax)",
    "16-1": "[select2_from_ajax_multiple](#section-select2_from_ajax_multiple)",
    "17-0": "[enum](#section-enum)"
  },
  "cols": 3,
  "rows": 18
}
[/block]

[block:api-header]
{
  "type": "basic",
  "title": "address"
}
[/block]
Use [Algolia Places autocomplete](https://community.algolia.com/places/) to help users type their address faster. With the ```store_as_json``` option, it will store the address, postcode, city, country, latitude and longitude in a JSON in the database. Without it, it will just store the address string.

```php
[   // Address
    'name' => 'address',
    'label' => 'Address',
    'type' => 'address',
    // optional
    'store_as_json' => true
],
```

Please note
[block:callout]
{
  "type": "info",
  "title": "Use attribute casting",
  "body": "For information stored as JSON in the database, it's recommended that you use [attribute casting](https://mattstauffer.co/blog/laravel-5.0-eloquent-attribute-casting) to ```array``` or ```object```. That way, every time you get the info from the database you'd get it in a usable format."
}
[/block]

[block:api-header]
{
  "type": "basic",
  "title": "browse"
}
[/block]
Open elFinder and select a file from there.

```php
[   // Browse
    'name' => 'image',
    'label' => 'Image',
    'type' => 'browse'
],
```

Input preview:
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/j2WEXAxQAa8FonURRS2J_Screen%20Shot%202016-05-22%20at%2018.10.34.png",
        "Screen Shot 2016-05-22 at 18.10.34.png",
        "685",
        "93",
        "#eceded",
        ""
      ]
    }
  ]
}
[/block]
Onlick preview:
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/5OvDZHWORcuVOzsMegeq_Screen%20Shot%202016-05-22%20at%2018.10.45.png",
        "Screen Shot 2016-05-22 at 18.10.45.png",
        "690",
        "388",
        "#2b5fa3",
        ""
      ]
    }
  ]
}
[/block]

[block:api-header]
{
  "title": "Browse_multiple"
}
[/block]
Open elFinder and select multiple file from there.

```php
[   // Browse multiple
    'name' => 'files',
    'label' => 'Files',
    'type' => 'browse_multiple',
    // 'multiple' => true, // enable/disable the multiple selection functionality
    // 'mime_types' => null, // visible mime prefixes; ex. ['image'] or ['application/pdf']
],
```

We recommend you cast your attribute as ```array``` on your model.  That way, when you do ```$entry->files``` you get a nice array. The field will work even if you don't cast.

Input preview:
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/c93b860-Screen_Shot_2018-03-16_at_08.30.32.png",
        "Screen Shot 2018-03-16 at 08.30.32.png",
        663,
        141,
        "#efeff0"
      ]
    }
  ]
}
[/block]

[block:api-header]
{
  "type": "basic",
  "title": "base64_image"
}
[/block]
Upload an image and store it in the database as Base64. Notes:
- make sure the column type is LONGBLOB;
- detailed [instructions and customizations here](https://github.com/Laravel-Backpack/CRUD/pull/56#issue-164712261); 

```php
$this->crud->addField([ // base64_image
    'label' => "Profile Image",
    'name' => "image",
    'filename' => "image_filename", // set to null if not needed
    'type' => 'base64_image',
    'aspect_ratio' => 1, // set to 0 to allow any aspect ratio
    'crop' => true, // set to true to allow cropping, false to disable
    'src' => NULL, // null to read straight from DB, otherwise set to model accessor function
]);
```

[block:api-header]
{
  "type": "basic",
  "title": "checkbox"
}
[/block]
Checkbox for true/false.

```php
[   // Checkbox
    'name' => 'active',
    'label' => 'Active',
    'type' => 'checkbox'
],
```
[block:api-header]
{
  "type": "basic",
  "title": "checklist"
}
[/block]
```php
[
    'label'     => 'Roles',
    'type'      => 'checklist',
    'name'      => 'roles',
    'entity'    => 'roles',
    'attribute' => 'name',
    'model'     => "Backpack\PermissionManager\app\Models\Role",
    'pivot'     => true,
]);
```
[block:api-header]
{
  "type": "basic",
  "title": "checklist_dependency"
}
[/block]
```php

// two interconnected entities
'label'             => 'User Role Permissions',
'field_unique_name' => 'user_role_permission',
'type'              => 'checklist_dependency',
'name'              => 'roles_and_permissions', // the methods that defines the relationship in your Model
'subfields'         => [
        'primary' => [
            'label'            => 'Roles',
            'name'             => 'roles', // the method that defines the relationship in your Model
            'entity'           => 'roles', // the method that defines the relationship in your Model
            'entity_secondary' => 'permissions', // the method that defines the relationship in your Model
            'attribute'        => 'name', // foreign key attribute that is shown to user
            'model'            => "Backpack\PermissionManager\app\Models\Role", // foreign key model
            'pivot'            => true, // on create&update, do you need to add/delete pivot table entries?]
            'number_columns'   => 3, //can be 1,2,3,4,6
        ],
        'secondary' => [
            'label'          => 'Permission',
            'name'           => 'permissions', // the method that defines the relationship in your Model
            'entity'         => 'permissions', // the method that defines the relationship in your Model
            'entity_primary' => 'roles', // the method that defines the relationship in your Model
            'attribute'      => 'name', // foreign key attribute that is shown to user
            'model'          => "Backpack\PermissionManager\app\Models\Permission", // foreign key model
            'pivot'          => true, // on create&update, do you need to add/delete pivot table entries?]
            'number_columns' => 3, //can be 1,2,3,4,6
        ],
    ],
],
```
[block:api-header]
{
  "type": "basic",
  "title": "ckeditor"
}
[/block]
Show a wysiwyg CKEditor to the user.

```php
[   // CKEditor
    'name' => 'description',
    'label' => 'Description',
    'type' => 'ckeditor',
    // optional:
    'extra_plugins' => ['oembed', 'widget', 'justify']
],
```
[block:api-header]
{
  "type": "basic",
  "title": "color"
}
[/block]
```php
[   // Color
    'name' => 'background_color',
    'label' => 'Background Color',
    'type' => 'color'
],
```
[block:api-header]
{
  "type": "basic",
  "title": "color_picker"
}
[/block]
Show a pretty colour picker using [Bootstrap Colorpicker](https://itsjavi.com/bootstrap-colorpicker/).

```php
[   // color_picker
    'label' => 'Background Color',
    'name' => 'background_color',
    'type' => 'color_picker',
    'color_picker_options' => ['customClass' => 'custom-class']
]
```
[block:api-header]
{
  "type": "basic",
  "title": "custom_html [v2.0.7+]"
}
[/block]
Allows you to insert custom HTML in the create/update forms. Usually used in forms with a lot of fields, to separate them using h1-h5, hr, etc, but can be used for any HTML.

```php
[   // CustomHTML
    'name' => 'separator',
    'type' => 'custom_html',
    'value' => '<hr>'
],
```
[block:api-header]
{
  "type": "basic",
  "title": "date"
}
[/block]
```php
[   // Date
    'name' => 'birthday',
    'label' => 'Birthday',
    'type' => 'date'
],
```
[block:api-header]
{
  "type": "basic",
  "title": "date_picker"
}
[/block]
Show a pretty [Bootstrap Datepicker](http://bootstrap-datepicker.readthedocs.io/en/latest/).

```php
[   // date_picker
   'name' => 'date',
   'type' => 'date_picker',
   'label' => 'Date',
   // optional:
   'date_picker_options' => [
      'todayBtn' => true,
      'format' => 'dd-mm-yyyy',
      'language' => 'fr'
   ],
],
```

Please note it is recommended that you use [attribute casting](https://laravel.com/docs/5.3/eloquent-mutators#attribute-casting) on your model (cast to date).
[block:api-header]
{
  "type": "basic",
  "title": "date_range"
}
[/block]
Starting with Backpack\CRUD 3.1.59

Show a DateRangePicker and let the user choose a start date and end date.

```php
        [
            'name' => 'event_date_range', // a unique name for this field
            'start_name' => 'start_date', // the db column that holds the start_date
            'end_name' => 'end_date', // the db column that holds the end_date
            'label' => 'Event Date Range',
            'type' => 'date_range',
            // OPTIONALS
            'start_default' => '1991-03-28 01:01', // default value for start_date
            'end_default' => '1991-04-05 02:00', // default value for end_date
            'date_range_options' => [ // options sent to daterangepicker.js
                'timePicker' => true,
                'locale' => ['format' => 'DD/MM/YYYY HH:mm']
            ]
        ]
```

Please note it is recommended that you use [attribute casting](https://laravel.com/docs/5.3/eloquent-mutators#attribute-casting) on your model (cast to date).

Your end result will look like this:
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/65ea9f3-a635d4be-8a4b-11e6-94e6-191c3db5d816.png",
        "a635d4be-8a4b-11e6-94e6-191c3db5d816.png",
        1396,
        816,
        "#eaf1f8"
      ]
    }
  ]
}
[/block]

[block:api-header]
{
  "type": "basic",
  "title": "datetime"
}
[/block]
```php
[   // DateTime
    'name' => 'start',
    'label' => 'Event start',
    'type' => 'datetime'
],
```

**Please note:** if you're using datetime [attribute casting](https://laravel.com/docs/5.3/eloquent-mutators#attribute-casting) on your model, you also need to place this mutator inside your model:
```php
	public function setDatetimeAttribute($value) {
		$this->attributes['datetime'] = \Date::parse($value);
	}
```
Otherwise the input's datetime-local formal will cause some errors.
[block:api-header]
{
  "type": "basic",
  "title": "datetime_picker"
}
[/block]
Show a [Bootstrap Datetime Picker](https://eonasdan.github.io/bootstrap-datetimepicker/).

```php
[   // DateTime
    'name' => 'start',
    'label' => 'Event start',
    'type' => 'datetime_picker',
    // optional:
    'datetime_picker_options' => [
        'format' => 'DD/MM/YYYY HH:mm',
        'language' => 'fr'
    ],
    'allows_null' => true,
    // 'default' => '2017-05-12 11:59:59',
],
```

**Please note:** if you're using date [attribute casting](https://laravel.com/docs/5.3/eloquent-mutators#attribute-casting) on your model, you may also need to place this mutator inside your model:
```php
	public function setDatetimeAttribute($value) {
		$this->attributes['datetime'] = \Date::parse($value);
	}
```
Otherwise the input's datetime-local formal will cause some errors. Remeber to change "datetime" with the name of your attribute (column name).
[block:api-header]
{
  "type": "basic",
  "title": "dropzone // TODO"
}
[/block]

[block:api-header]
{
  "type": "basic",
  "title": "email"
}
[/block]
```php
[   // Email
    'name' => 'email',
    'label' => 'Email Address',
    'type' => 'email'
],
```
[block:api-header]
{
  "type": "basic",
  "title": "enum"
}
[/block]
Show a select with the values in the database for that ENUM field. Requires that the db column type is "enum". If the db column allows null, the " - " value will also show up in the select.

```php
[   // Enum
    'name' => 'status',
    'label' => 'Status',
    'type' => 'enum'
],
```

PLEASE NOTE the enum field only works for MySQL databases.
[block:api-header]
{
  "type": "basic",
  "title": "hidden"
}
[/block]
Include an <input type="hidden"> in the form.

```php
[   // Hidden
    'name' => 'status',
    'type' => 'hidden'
],
```
[block:api-header]
{
  "type": "basic",
  "title": "icon_picker"
}
[/block]
Show an icon picker. Supported icon sets are fontawesome, glyphicon, ionicon, weathericon, mapicon, octicon, typicon, elusiveicon, materialdesign as per the jQuery plugin, [bootstrap-iconpicker](http://victor-valencia.github.io/bootstrap-iconpicker/).

The stored value will be the class name (ex: fa-home).

```php
[
    'label' => "Icon",
    'name' => 'icon',
    'type' => 'icon_picker',
    'iconset' => 'fontawesome' // options: fontawesome, glyphicon, ionicon, weathericon, mapicon, octicon, typicon, elusiveicon, materialdesign
],
```

Your input will look like button, with a dropdown where the user can search or pick an icon:
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/4349bcf-Screen_Shot_2016-09-19_at_16.22.10.png",
        "Screen Shot 2016-09-19 at 16.22.10.png",
        801,
        408,
        "#e9f0f1"
      ]
    }
  ]
}
[/block]

[block:api-header]
{
  "type": "basic",
  "title": "image"
}
[/block]
Upload an image and store it on the disk. 

**Step 1.** Show the field. 
```php
$this->crud->addField([ // image
    'label' => "Profile Image",
    'name' => "image",
    'type' => 'image',
    'upload' => true,
    'crop' => true, // set to true to allow cropping, false to disable
    'aspect_ratio' => 1, // ommit or set to 0 to allow any aspect ratio
    // 'disk' => 's3_bucket', // in case you need to show images from a different disk
    // 'prefix' => 'uploads/images/profile_pictures/' // in case you only store the filename in the database, this text will be prepended to the database value
]);
```

Here's how that looks:
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/312d5ff-Screen_Shot_2016-09-15_at_14.56.49.png",
        "Screen Shot 2016-09-15 at 14.56.49.png",
        664,
        305,
        "#3f673f"
      ]
    }
  ]
}
[/block]
**Step 2.** Set a mutator on your Model, so that the file can be stored. You can use this boilerplate code and modify it to match your use case:

```php
public function setImageAttribute($value)
    {
    	$attribute_name = "image";
    	$disk = "public_folder";
    	$destination_path = "uploads/folder_1/subfolder_3";

    	// if the image was erased
    	if ($value==null) {
    		// delete the image from disk
    		\Storage::disk($disk)->delete($this->{$attribute_name});

    		// set null in the database column
    		$this->attributes[$attribute_name] = null;
    	}

    	// if a base64 was sent, store it in the db
    	if (starts_with($value, 'data:image'))
    	{
    		// 0. Make the image
    		$image = \Image::make($value);
    		// 1. Generate a filename.
	    	$filename = md5($value.time()).'.jpg';
	    	// 2. Store the image on disk.
	    	\Storage::disk($disk)->put($destination_path.'/'.$filename, $image->stream());
	        // 3. Save the path to the database
	        $this->attributes[$attribute_name] = $destination_path.'/'.$filename;
    	}
    }
```
[block:callout]
{
  "type": "danger",
  "title": "The uploaded images are not deleted for you",
  "body": "If you delete an entry (using the CRUD or anywhere inside your app), the image file won't be deleted from the disk."
}
[/block]
If you're NOT using soft deletes on that Model and want the image to be deleted at the same time the entry is, just specify that in your Model's ```deleting``` event:
```php
	public static function boot()
	{
		parent::boot();
		static::deleting(function($obj) {
			\Storage::disk('public_folder')->delete($obj->image);
		});
	}
```

**A note about aspect_ratio**
The value for aspect ratio is a float that represents the ratio of the cropping rectangle height and width. By way of example,

- Square = 1
- Landscape = 2
- Portrait = 0.5

And you can, of course, use any value for more extreme rectangles.
[block:api-header]
{
  "type": "basic",
  "title": "month"
}
[/block]
```php
[   // Month
    'name' => 'month',
    'label' => 'Month',
    'type' => 'month'
],
```
[block:api-header]
{
  "type": "basic",
  "title": "number"
}
[/block]
Shows an input type=number to the user, with optional prefix and suffix:
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/3816ba5-Screen_Shot_2016-09-16_at_09.18.55.png",
        "Screen Shot 2016-09-16 at 09.18.55.png",
        660,
        62,
        "#d5d5dc"
      ]
    }
  ]
}
[/block]
```php
[   // Number
    'name' => 'number',
    'label' => 'Number',
    'type' => 'number',
    // optionals
    // 'attributes' => ["step" => "any"], // allow decimals
    // 'prefix' => "$",
    // 'suffix' => ".00",
],
```
[block:api-header]
{
  "type": "basic",
  "title": "page_or_link"
}
[/block]
Select an existing page from PageManager or an internal or external link. It’s used in the MenuManager package, but can be used in any other model just as well. Looks like this:
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/j18y2tY9T4uu2MkWp8tg_Screen%20Shot%202016-05-22%20at%2018.08.19.png",
        "Screen Shot 2016-05-22 at 18.08.19.png",
        "681",
        "91",
        "#a18671",
        ""
      ]
    }
  ]
}
[/block]
And its definition looks like this:
```php
[   // PageOrLink
    'name' => 'type',
    'label' => "Type",
    'type' => 'page_or_link',
    'page_model' => '\Backpack\PageManager\app\Models\Page'
]
```
[block:api-header]
{
  "type": "basic",
  "title": "password field"
}
[/block]
```php
[   // Password
    'name' => 'password',
    'label' => 'Password',
    'type' => 'password'
],
```
[block:api-header]
{
  "type": "basic",
  "title": "radio"
}
[/block]
Show radios according to an associative array you give the input and let the user pick from them. You can choose for the radio options to be displayed inline or one-per-line.

```php
[
    'name'        => 'status', // the name of the db column
    'label'       => 'Status', // the input label
    'type'        => 'radio',
    'options'     => [ // the key will be stored in the db, the value will be shown as label; 
                        0 => "Draft",
                        1 => "Published"
                    ],
    // optional
    //'inline'      => false, // show the radios all on the same line?
]
```
[block:api-header]
{
  "type": "basic",
  "title": "range"
}
[/block]
```php
[   // Range
    'name' => 'range',
    'label' => 'Range',
    'type' => 'range'
],
```
[block:api-header]
{
  "type": "basic",
  "title": "select (1-n relationship)"
}
[/block]
Show a Select with the names of the connected entity and let the user select one of them.
Your relationships should already be defined on your models.

```php
[  // Select
   'label' => "Category",
   'type' => 'select',
   'name' => 'category_id', // the db column for the foreign key
   'entity' => 'category', // the method that defines the relationship in your Model
   'attribute' => 'name', // foreign key attribute that is shown to user
   'model' => "App\Models\Tag" // foreign key model
]
```
[block:api-header]
{
  "type": "basic",
  "title": "select2 (1-n relationship)"
}
[/block]
[Works just like the SELECT field, but prettier]

Show a Select2 with the names of the connected entity and let the user select one of them. 
Your relationships should already be defined on your models.

```php
[  // Select2
   'label' => "Category",
   'type' => 'select2',
   'name' => 'category_id', // the db column for the foreign key
   'entity' => 'category', // the method that defines the relationship in your Model
   'attribute' => 'name', // foreign key attribute that is shown to user
   'model' => "App\Models\Tag" // foreign key model
]
```
[block:api-header]
{
  "type": "basic",
  "title": "select_multiple (n-n relationship)"
}
[/block]
Show a Select with the names of the connected entity and let the user select any number of them.
Your relationships should already be defined on your models.

```php
[       // SelectMultiple = n-n relationship (with pivot table)
	'label' => "Tags",
	'type' => 'select_multiple',
	'name' => 'tags', // the method that defines the relationship in your Model
	'entity' => 'tags', // the method that defines the relationship in your Model
	'attribute' => 'name', // foreign key attribute that is shown to user
	'model' => "App\Models\Tag", // foreign key model
	'pivot' => true, // on create&update, do you need to add/delete pivot table entries?
]
```
[block:api-header]
{
  "type": "basic",
  "title": "select2_multiple (n-n relationship)"
}
[/block]
[Works just like the SELECT field, but prettier]

Show a Select2 with the names of the connected entity and let the user select any number of them.
Your relationships should already be defined on your models.

```php
[       // Select2Multiple = n-n relationship (with pivot table)
	'label' => "Tags",
	'type' => 'select2_multiple',
	'name' => 'tags', // the method that defines the relationship in your Model
	'entity' => 'tags', // the method that defines the relationship in your Model
	'attribute' => 'name', // foreign key attribute that is shown to user
	'model' => "App\Models\Tag", // foreign key model
	'pivot' => true, // on create&update, do you need to add/delete pivot table entries?
	// 'select_all' => true, // show Select All and Clear buttons?
]
```
[block:api-header]
{
  "type": "basic",
  "title": "select_from_array"
}
[/block]
Display a select with the values you want:

```php
[ // select_from_array
    'name' => 'template',
    'label' => "Template",
    'type' => 'select_from_array',
    'options' => [‘one’ => ‘One’, ‘two’ => ‘Two’],
    'allows_null' => false,
    'default' => 'one',
    // 'allows_multiple' => true, // OPTIONAL; needs you to cast this to array in your model;
],
```
[block:api-header]
{
  "type": "basic",
  "title": "select2_from_array"
}
[/block]
Display a select2 with the values you want:

```php
[ // select_from_array
    'name' => 'template',
    'label' => "Template",
    'type' => 'select2_from_array',
    'options' => [‘one’ => ‘One’, ‘two’ => ‘Two’],
    'allows_null' => false,
    'default' => 'one',
    // 'allows_multiple' => true, // OPTIONAL; needs you to cast this to array in your model;
],
```
[block:api-header]
{
  "type": "basic",
  "title": "select2_from_ajax"
}
[/block]
Display a select2 that takes its values from an AJAX call.

```
[
            // 1-n relationship
            'label' => "End", // Table column heading
            'type' => "select2_from_ajax",
            'name' => 'category_id', // the column that contains the ID of that connected entity
            'entity' => 'city', // the method that defines the relationship in your Model
            'attribute' => "name", // foreign key attribute that is shown to user
            'model' => "App\Models\Category", // foreign key model
            'data_source' => url("api/category"), // url to controller search function (with /{id} should return model)
            'placeholder' => "Select a category", // placeholder for the select
            'minimum_input_length' => 2, // minimum characters to type before querying results
 ]
```

Of course, you also need to create a controller and routes for the data_source above. Here's an example:

```
Route::get('/api/category', 'Api\CategoryController@index');
Route::get('/api/category/{id}', 'Api\CategoryController@show');
```

```
<?php

namespace App\Http\Controllers\Api;

use Illuminate\Http\Request;
use App\Http\Controllers\Controller;
use Backpack\NewsCRUD\app\Models\Category;

class CategoryController extends Controller
{
    public function index(Request $request)
    {
        $search_term = $request->input('q');
        $page = $request->input('page');

        if ($search_term)
        {
            $results = Category::where('name', 'LIKE', '%'.$search_term.'%')->paginate(10);
        }
        else
        {
            $results = Category::paginate(10);
        }

        return $results;
    }

    public function show($id)
    {
        return Category::find($id);
    }
}
```
[block:api-header]
{
  "type": "basic",
  "title": "select2_from_ajax_multiple"
}
[/block]
Display a select2 that takes its values from an AJAX call. Same as [select2_from_ajax](#section-select2_from_ajax) above, but allows for multiple items to be selected. The only difference in the field definition is the "pivot" attribute.

```
[
            // n-n relationship
            'label' => "End", // Table column heading
            'type' => "select2_from_ajax_multiple",
            'name' => 'city_id', // the column that contains the ID of that connected entity
            'entity' => 'city', // the method that defines the relationship in your Model
            'attribute' => "name", // foreign key attribute that is shown to user
            'model' => "App\Models\City", // foreign key model
            'data_source' => url("api/cities"), // url to controller search function (with /{id} should return model)
            'placeholder' => "Select a city", // placeholder for the select
            'minimum_input_length' => 2, // minimum characters to type before querying results
            'pivot' => true, // on create&update, do you need to add/delete pivot table entries?
 ]
```

Of course, you also need to create a controller and routes for the data_source above. Here's an example:

```
Route::get('/api/category', 'Api\CategoryController@index');
Route::get('/api/category/{id}', 'Api\CategoryController@show');
```

```
<?php

namespace App\Http\Controllers\Api;

use Illuminate\Http\Request;
use App\Http\Controllers\Controller;
use Backpack\NewsCRUD\app\Models\Category;

class CategoryController extends Controller
{
    public function index(Request $request)
    {
        $search_term = $request->input('q');
        $page = $request->input('page');

        if ($search_term)
        {
            $results = Category::where('name', 'LIKE', '%'.$search_term.'%')->paginate(10);
        }
        else
        {
            $results = Category::paginate(10);
        }

        return $results;
    }

    public function show($id)
    {
        return Category::find($id);
    }
}
```
[block:api-header]
{
  "type": "basic",
  "title": "simplemde"
}
[/block]
Show a [SimpleMDE markdown editor](https://simplemde.com/) to the user.

```php
[   // SimpleMDE
    'name' => 'description',
    'label' => 'Description',
    'type' => 'simplemde',
    // optional
   // 'simplemdeAttributes' => [
   //   'promptURLs' => true,
   //   'status' => false,
   //   'spellChecker' => false,
   //   'forceSync' => true,
   // ],
   // 'simplemdeAttributesRaw' => $some_json
],
```
[block:api-header]
{
  "type": "basic",
  "title": "summernote"
}
[/block]
Show a [Summernote wysiwyg editor](http://summernote.org/) to the user.

```php
[   // Summernote
    'name' => 'description',
    'label' => 'Description',
    'type' => 'summernote',
    // 'options' => [], // easily pass parameters to the summernote JS initialization 
],
```
[block:api-header]
{
  "type": "basic",
  "title": "table"
}
[/block]
Show a table with multiple inputs per row and store the values as JSON in the database. The user can add more rows and reorder the rows as they please.

 ```php
[ // Table
    'name' => 'options',
    'label' => 'Options',
    'type' => 'table',
    'entity_singular' => 'option', // used on the "Add X" button
    'columns' => [
        'name' => 'Name',
        'desc' => 'Description',
        'price' => 'Price'
    ],
    'max' => 5, // maximum rows allowed in the table
    'min' => 0 // minimum rows allowed in the table
],
```

The end result will look like this:
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/6ac79c0-Screen_Shot_2016-09-26_at_19.54.04.png",
        "Screen Shot 2016-09-26 at 19.54.04.png",
        666,
        256,
        "#f4f4f5"
      ]
    }
  ]
}
[/block]

[block:callout]
{
  "type": "info",
  "title": "Cast the attribute as 'object' or 'array' in your model",
  "body": "It's highly recommended that you use [attribute casting](https://mattstauffer.co/blog/laravel-5.0-eloquent-attribute-casting) on your model when working with JSON stored in database columns."
}
[/block]

[block:api-header]
{
  "type": "basic",
  "title": "text"
}
[/block]
The basic field type, all it needs is the two mandatory parameters: name and label.

```php
[ // Text
    'name' => 'title',
    'label' => "Title",
    'type' => 'text',

    // optional
    //'prefix' => '',
    //'suffix' => '',
    //'default'    => 'some value', // default value
    //'hint'       => 'Some hint text', // helpful text, show up after input
    //'attributes' => [
       //'placeholder' => 'Some text when empty',
       //'class' => 'form-control some-class'
     //], // extra HTML attributes and values your input might need
     //'wrapperAttributes' => [
       //'class' => 'form-group col-md-12'
     //], // extra HTML attributes for the field wrapper - mostly for resizing fields 
     //'readonly'=>'readonly',
],

```

You can use the optional 'prefix' and 'suffix' attributes to display something before and after the input, like icons, path prefix, etc:
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/449b26d-Screen_Shot_2016-09-16_at_09.30.33.png",
        "Screen Shot 2016-09-16 at 09.30.33.png",
        666,
        70,
        "#d4d4dc"
      ]
    }
  ]
}
[/block]

[block:api-header]
{
  "type": "basic",
  "title": "textarea"
}
[/block]
Show a textarea to the user.

```php
[   // Textarea
    'name' => 'description',
    'label' => 'Description',
    'type' => 'textarea'
],
```
[block:api-header]
{
  "type": "basic",
  "title": "time"
}
[/block]
```php
[   // Time
    'name' => 'start',
    'label' => 'Start time',
    'type' => 'time'
],
```
[block:api-header]
{
  "type": "basic",
  "title": "time_picker // TODO"
}
[/block]

[block:api-header]
{
  "type": "basic",
  "title": "tinymce"
}
[/block]
Show a wysiwyg (TinyMCE) to the user.

```php
[   // TinyMCE
    'name' => 'description',
    'label' => 'Description',
    'type' => 'tinymce'
],
```
[block:api-header]
{
  "type": "basic",
  "title": "upload"
}
[/block]
**Step 1.** Show a file input to the user:
```php
[   // Upload
    'name' => 'image',
    'label' => 'Image',
    'type' => 'upload',
    'upload' => true,
    'disk' => 'uploads' // if you store files in the /public folder, please ommit this; if you store them in /storage or S3, please specify it;
],
```

**Step 2.** In order to save/update/delete the file from disk&db, we need to create [a mutator](https://laravel.com/docs/5.3/eloquent-mutators#defining-a-mutator) on your model:
```php
public function setImageAttribute($value)
    {
    	$attribute_name = "image";
    	$disk = "public";
    	$destination_path = "folder_1/subfolder_1";

    	$this->uploadFileToDisk($value, $attribute_name, $disk, $destination_path);
    }
```

**How it works:**

The field sends the file, through a Request, to the Controller. The Controller then tries to create/update the Model. That's when the mutator on your model will run. That also means we can do any [file validation](https://laravel.com/docs/5.3/validation#rule-file) (```file```, ```image```, ```mimetypes```, ```mimes```) in the Request, before the file is stored on the disk.

[The ```uploadFileToDisk()``` method](https://github.com/Laravel-Backpack/CRUD/blob/master/src/CrudTrait.php#L108-L129) will take care of everything for most use cases:

```
/**
     * Handle file upload and DB storage for a file:
     * - on CREATE
     *     - stores the file at the destination path
     *     - generates a name
     *     - stores the full path in the DB;
     * - on UPDATE
     *     - if the value is null, deletes the file and sets null in the DB
     *     - if the value is different, stores the different file and updates DB value
     * /
public function uploadFileToDisk($value, $attribute_name, $disk, $destination_path) {}
```

If you wish to have a different functionality, you can delete the method call from your mutator and do your own thing.
[block:callout]
{
  "type": "danger",
  "title": "The uploaded files are not deleted for you",
  "body": "When you delete an entry (wether through CRUD or the application), the uploaded files will not be deleted."
}
[/block]
If you're NOT using soft deletes on that Model and want the file to be deleted at the same time the entry is, just specify that in your Model's ```deleting``` event:
```php
	public static function boot()
	{
		parent::boot();
		static::deleting(function($obj) {
			\Storage::disk('public_folder')->delete($obj->image);
		});
	}
```
[block:api-header]
{
  "type": "basic",
  "title": "upload_multiple"
}
[/block]
Shows a multiple file input to the user and stores the values as a JSON array in the database.

**Step 1.** Show a multiple file input to the user:
```php
[   // Upload
    'name' => 'photos',
    'label' => 'Photos',
    'type' => 'upload_multiple',
    'upload' => true,
    'disk' => 'uploads' // if you store files in the /public folder, please ommit this; if you store them in /storage or S3, please specify it;
],
```

**Step 2.** In order to save/update/delete the files from disk&db, we need to create [a mutator](https://laravel.com/docs/5.3/eloquent-mutators#defining-a-mutator) on your model:
```php
public function setPhotosAttribute($value)
    {
    	$attribute_name = "photos";
    	$disk = "public";
    	$destination_path = "folder_1/subfolder_1";

    	$this->uploadMultipleFilesToDisk($value, $attribute_name, $disk, $destination_path);
    }
```

**Step 3.** Since the filenames are stored in the database as a JSON array, we're going to use [attribute casting](https://laravel.com/docs/5.3/eloquent-mutators#attribute-casting) on your model, so every time we get the filenames array from the database it's converted from a JSON array to a PHP array:
```php
    protected $casts = [
    	'photos' => 'array'
    ];
```

**How it works:**

The field sends the files, through a Request, to the Controller. The Controller then tries to create/update the Model. That's when the mutator on your model will run. That also means we can do any [file validation](https://laravel.com/docs/5.3/validation#rule-file) (```file```, ```image```, ```mimetypes```, ```mimes```) in the Request, before the files are stored on the disk.

[The ```uploadMultipleFilesToDisk()``` method](https://github.com/Laravel-Backpack/CRUD/blob/master/src/CrudTrait.php#L154-L189) will take care of everything for most use cases:

```
/**
     * Handle multiple file upload and DB storage:
     * - if files are sent
     *     - stores the files at the destination path
     *     - generates random names
     *     - stores the full path in the DB, as JSON array;
     * - if a hidden input is sent to clear one or more files
     *     - deletes the file
     *     - removes that file from the DB.
     * /
public function uploadMultipleFilesToDisk($value, $attribute_name, $disk, $destination_path) {}
```

If you wish to have a different functionality, you can delete the method call from your mutator and do your own thing.
[block:callout]
{
  "type": "danger",
  "title": "The uploaded files are not deleted for you",
  "body": "When you delete an entry (wether through CRUD or the application), the uploaded files will not be deleted."
}
[/block]
If you're NOT using soft deletes on that Model and want the files to be deleted at the same time the entry is, just specify that in your Model's ```deleting``` event:
```php
	public static function boot()
	{
		parent::boot();
		static::deleting(function($obj) {
			if (count((array)$obj->photos)) {
				foreach ($obj->photos as $file_path) {
					\Storage::disk('public_folder')->delete($file_path);
				}
			}
		});
	}
```

You might notice the field is using a ```clear_photos``` variable. Don't worry, you don't need it in your db table. That's just used to delete photos upon "update". If you use ```$fillable``` on your model, just don't include it. If you use ```$guarded``` on your model, place it in guarded.
[block:api-header]
{
  "type": "basic",
  "title": "url"
}
[/block]
```php
[   // URL
    'name' => 'link',
    'label' => 'Link to video file',
    'type' => 'url'
],
```
[block:api-header]
{
  "type": "basic",
  "title": "video"
}
[/block]
Allow the user to paste a Youtube/Vimeo link. That will get the video information with Javascript and store it as a JSON in the database.

Field definition:
```php
[   // URL
    'name' => 'video',
    'label' => 'Link to video file on Youtube or Vimeo',
    'type' => 'video',
],
```

An entry stored in the database will look like this:
```
$video = {
    id: 234324,
    title: ‘my video title’,
    image: ‘https://provider.com/image.jpg',
    url: ‘http://provider.com/video',
    provider: ‘youtube’
}
```

So you should use [attribute casting](https://mattstauffer.co/blog/laravel-5.0-eloquent-attribute-casting) in your model, to cast the video as ```array``` or ```object```.
[block:api-header]
{
  "type": "basic",
  "title": "view"
}
[/block]
Load a custom view in the form.

```php
[
        'name' => 'custom-ajax-button',
        'type' => 'view',
        'view' => 'partials/custom-ajax-button'
],
```

**Note:** the same functionality can be achieved using a [custom field type](https://laravel-backpack.readme.io/v3.0/docs/crud-fields#section-custom-field-types), or using the [custom_html field type](https://laravel-backpack.readme.io/docs/crud-fields#custom_html-v207) (if the content is really simple).
[block:api-header]
{
  "type": "basic",
  "title": "week"
}
[/block]
```php
[   // Week
    'name' => 'first_week',
    'label' => 'First week',
    'type' => 'week'
],
```
[block:api-header]
{
  "type": "basic",
  "title": "wysiwyg"
}
[/block]
Show a wysiwyg (CKEditor) to the user.

```php
[   // WYSIWYG Editor
    'name' => 'description',
    'label' => 'Description',
    'type' => 'wysiwyg'
],
```

# CUSTOM FIELD TYPES

The actual field types are stored in the Backpack/CRUD package in ```/resources/views/fields```. If you need to extend the CRUD with a new field type or overwrite an existing field, you don’t need to modify the package, you just need to add a file in your application in ```/resources/views/vendor/backpack/crud/fields```. The package checks there first, and only if there's no file there, it will load it from the package.

Your field definition will be something like:

```php
[
  // Custom Field
  'name' => 'address',
  'label' => 'Home address',
  'type' => 'address'
  /// 'view_namespace' => 'yourpackage' // use a custom namespace of your package to load views within a custom view folder.
]);
```

And your blade file something like:
[block:code]
{
  "codes": [
    {
      "code": "<!-- field_type_name -->\n<div @include('crud::inc.field_wrapper_attributes') >\n    <label>{!! $field['label'] !!}</label>\n    <input\n        type=\"text\"\n        name=\"{{ $field['name'] }}\"\n        value=\"{{ old($field['name']) ? old($field['name']) : (isset($field['value']) ? $field['value'] : (isset($field['default']) ? $field['default'] : '' )) }}\"\n        @include('crud::inc.field_attributes')\n    >\n\n    {{-- HINT --}}\n    @if (isset($field['hint']))\n        <p class=\"help-block\">{!! $field['hint'] !!}</p>\n    @endif\n</div>\n\n\n@if ($crud->checkIfFieldIsFirstOfItsType($field, $fields))\n  {{-- FIELD EXTRA CSS  --}}\n  {{-- push things in the after_styles section --}}\n\n      @push('crud_fields_styles')\n          <!-- no styles -->\n      @endpush\n\n\n  {{-- FIELD EXTRA JS --}}\n  {{-- push things in the after_scripts section --}}\n\n      @push('crud_fields_scripts')\n          <!-- no scripts -->\n      @endpush\n@endif\n\n{{-- Note: most of the times you'll want to use @if ($crud->checkIfFieldIsFirstOfItsType($field, $fields)) to only load CSS/JS once, even though there are multiple instances of it. --}}",
      "language": "html"
    }
  ]
}
[/block]
You will find everything you need inside the  ```$crud```, ```$entry```, and ```$field``` variables:
- ```$crud``` - all the crudpanel settings, options and variables;
- ```$entry``` - in EDIT forms, the current entry being modified (the actual values);
- ```$field``` - all options that have been passed for this field;