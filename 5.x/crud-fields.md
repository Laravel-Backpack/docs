# Fields

---

<a name="about"></a>
## About

Field types define how the admin can manipulate an entry's values. They're used by the Create and Update operations.

Think of the field type as the type of input: ```<input type="text" />```. But for most entities, you won't just need text inputs - you'll need datepickers, upload buttons, 1-n relationship, n-n relationships, textareas, etc.

We have a lot of default field types, detailed below. If you don't find what you're looking for, you can [create a custom field type](/docs/{{version}}/crud-fields#creating-a-custom-field-type). Or if you just want to tweak a default field type a little bit, you can [overwrite default field types](/docs/{{version}}/crud-fields#overwriting-default-field-types).

> NOTE: Starting with Backpack 4.1, if the _field name_ is the exact same as a relation method in the model, Backpack will assume you're adding a field for that relationship and infer relation attributes from it. To disable this behaviour, you can use `'entity' => false` in your field definition. 

<a name="fields-api"></a>
### Fields API

To manipulate fields, you can use the methods below. The action will be performed on the currently running operation. So make sure you run these methods inside ```setupCreateOperation()```, ```setupUpdateOperation()``` or in ```setup()``` inside operation blocks:

```php
// add a field 
$this->crud->addField($field_definition_array);

// shorthand: add a text field 
$this->crud->addField('db_column_name');

// add multiple fields
$this->crud->addFields([$field_definition_array_1, $field_definition_array_2]);

// change the attributes of a field
$this->crud->modifyField($name, $modifs_array);

// remove a field from both operations
$this->crud->removeField('name');

// remove multiple fields from both operations
$this->crud->removeFields($array_of_names);

// remove all fields from all operations
$this->crud->removeAllFields();

// FIELD ORDER

// add a field before a given field
$this->crud->addField($field_definition_array)->beforeField('name');

// add a field after a given field
$this->crud->addField($field_definition_array)->afterField('name');


// -------------------
// New in Backpack 4.1
// -------------------
// add a field with this name
$this->crud->field('price');

// change the type attribute on the 'price' field
$this->crud->field('price')->type('number');
```

<a name="field-attributes"></a>
### Field Attributes

<a name="mandatory-field-attributes"></a>
#### Mandatory Field Attributes

**The only attribute that's mandatory when you define a field is its `name`**, which will be used:
- inside the inputs, as `<input name='your_db_column' />`;
- to store the information in the database, so your `name` should correspond to a database column (if the field type doesn't have different instructions); 

Every other field attribute other than `name`, Backpack 4.1+ will try to guess.

<a name="recommended-field-attributes"></a>
#### Recommended Field Attributes

Usually developers define the following attributes for all fields:
- the ```name``` of the column in the database (ex: "title")
- the human-readable ```label``` for the input (ex: "Title")
- the ```type``` of the input (ex: "text")

So at minimum, a field definition array usually looks like:
```php
[
    'name'  => 'description',
    'label' => 'Article Description',
    'type'  => 'textarea',
]
```

Please note that `label` and `type` are not _mandatory_, just _recommended_:
- `label` can be omitted, and Backpack will try to construct it from the `name`;
- `type` can be omitted, and Backpack will try to guess it from the column type, or if there's a relationship on the Model with the same `name`;

<a name="optional-field-attributes-for-presentation-purposes"></a>
#### Optional - Field Attributes for Presentation Purposes

There are a few optional attributes on most default field types, that you can use to easily achieve a few common customisations:

```php
[
    'prefix'     => '',
    'suffix'     => '',
    'default'    => 'some value', // set a default value
    'hint'       => 'Some hint text', // helpful text, shows up after the input
    'attributes' => [
       'placeholder' => 'Some text when empty',
       'class'       => 'form-control some-class',
       'readonly'    => 'readonly',
       'disabled'    => 'disabled',
     ], // change the HTML attributes of your input
     'wrapper'   => [ 
        'class'      => 'form-group col-md-12'
     ], // change the HTML attributes for the field wrapper - mostly for resizing fields 
]
```

These will help you:

- **prefix** - add a text or icon _before_ the actual input;
- **suffix** - add a text or icon _after_ the actual input;
- **default** - specify a default value for the input, on create;
- **hint** - add descriptive text for this input;
- **attributes** - change or add actual HTML attributes of the input (ex: readonly, disabled, class, placeholder, etc);
- **wrapper** - change or add actual HTML attributes to the div that contains the input; 

<a name="fake-fields"></a>
#### Optional - Fake Field Attributes (stores fake attributes as JSON in the database)

In case you want to store information for an entry that doesn't need a separate database column, you can add any number of Fake Fields, and their information will be stored inside a column in the db, as JSON. By default, an ```extras``` column is used and assumed on the database table, but you can change that.

**Step 1.** Use the fake attribute on your field:
```php
[
    'name'     => 'name', // JSON variable name
    'label'    => "Tag Name", // human-readable label for the input
    
    'fake'     => true, // show the field, but don't store it in the database column above
    'store_in' => 'extras' // [optional] the database column name where you want the fake fields to ACTUALLY be stored as a JSON array 
],
```

**Step 2.** On your model, make sure the db columns where you store the JSONs (by default only ```extras```):
- are in your ```$fillable``` property;
- are on a new ```$fakeColumns``` property (create it now);
- are cast as array in ```$casts```;

>If you need your fakes to also be translatable, remember to also place ```extras``` in your model's ```$translatable``` property and remove it from ```$casts```.

Example:
```php
[
    'name'     => 'meta_title',
    'label'    => "Meta Title", 
    'fake'     => true, 
    'store_in' => 'metas' // [optional]
],
[
    'name'     => 'meta_description',
    'label'    => "Meta Description", 
    'fake'     => true, 
    'store_in' => 'metas' // [optional]
],
[
    'name'     => 'meta_keywords',
    'label'    => "Meta Keywords", 
    'fake'     => true, 
    'store_in' => 'metas' // [optional]
],
```

In this example, these 3 fields will show up in the create & update forms, the CRUD will process as usual, but in the database these values won't be stored in the ```meta_title```, ```meta_description``` and ```meta_keywords``` columns. They will be stored in the ```metas``` column as a JSON array:

```php
{"meta_title":"title","meta_description":"desc","meta_keywords":"keywords"}
```

If the ```store_in``` attribute wasn't used, they would have been stored in the ```extras``` column.

If you need to perform any manipulation of the data before storing the fake fields, you should define a mutator for the column used to store the fake attributes, by default it's `extras`. It can be any other column you define in `store_in`. 
```php
// in YourModel.php
public function setExtrasAttribute($value)
{
	// Your code
}
<a name="split-fields-into-tabs"></a>
#### Optional - Tab Attribute Splits Forms into Tabs

You can now split your create/edit inputs into multiple tabs.

![CRUD Fields Tabs](https://backpackforlaravel.com/uploads/docs-4-0/operations/create_tabs.png)

In order to use this feature, you just need to specify the tab name for each of your fields. Example:

```php
// select_from_array
$this->crud->addField([
    'name'            => 'select_from_array',
    'label'           => "Select from array",
    'type'            => 'select_from_array',
    'options'         => ['one' => 'One', 'two' => 'Two', 'three' => 'Three'],
    'allows_null'     => false,
    'allows_multiple' => true,
    'tab'             => 'Tab name here',
]);
```

If you forget to specify a tab name for a field, Backpack will place it above all tabs.


<a name="entity-model-and-attribute"></a>
#### Optional - Attributes for Fields Containing Related Entries

When a field works with related entities (relationships like `BelongsTo`, `HasOne`, `HasMany`, `BelongsToMany`, etc), Backpack needs to know how the current model (being create/edited) and the other model (that shows up in the field) are related. And it stores that information in a few additional field attributes, right after you add the field.

*Normally, Backpack 4.1+ will guess all this relationship information for you.* If you have your relationships properly defined in your Models, you can just use a relationship field the same way you would a normal field. Pretend that _the method in your Model that defines your relationship_ is a real column, and Backpack will do all the work for you.

But if you want to overwrite any of the relationship attributes Backpack guesses, here they are:
- `entity` - points to the method on the model that contains the relationship; having this defined, Backpack will try to guess from it all other field attributes; ex: `category` or `tags`;
- `model` - the classname (including namespace) of the related model (ex: `App\Models\Category`); usually deduced from the relationship function in the model;
- `attribute` - the attribute on the related model (aka foreign attribute) that will be show to the user; for example, you wouldn't want a dropdown of categories showing IDs - no, you'd want to show the category names; in this case, the `attribute` will be `name`; usually deduced using the [identifiable attribute functionality explained below](#identifiable-attribute);
- `multiple` - boolean, allows the user to pick one or multiple items; usually deduced depending on whether it's a 1-to-n or n-n relationship;
- `pivot` - boolean, instructs Backpack to store the information inside a pivot table; usually deduced depending on whether it's a 1-to-n or n-n relationship;
- `relation_type` - text, deduced from `entity`; not a good idea to overwrite;

If you do need a field that contains relationships to behave a certain way, it's usually enough to just specify a different `entity`. However, you _can_ specify any of the attributes above, and Backpack will take your value for it, instead of trying to guess one.


<a name="identifiable-attribute"></a>
**Identifiable Attribute for Relationship Fields**

Fields that work with relationships will allow you to select which ```attribute``` on the related entry you want to show to the user. All relationship fields (relationship, select, select2, select_multiple, select2_multiple, select2_from_ajax, select2_from_ajax_multiple) let you define the ```attribute``` for this specific purpose.

For example, when the admin creates an ```Article``` they'll have to select a ```Category``` from a dropdown. It's important to show an attribute for ```Category``` that will help the admin easily identify the category, even if it's not the ID. In this example, it would probably be the category name - that's what you'd like the dropdown to show.

In Backpack, you can explicitly define this, by giving the field an ```attribute```. But you can also NOT explicitly define this - Backpack will try to guess it. If you don't like what Backpack guessed would be a good identifiable attribute, you can either:
- (A) explicitly define an ```attribute``` for that field

>**Note**: If the attribute you want to show is an acessor in Model, you need to add it to the `$appends` property of the said Model. https://laravel.com/docs/9.x/eloquent-serialization#appending-values-to-json

- (B) you can specify the identifiable attribute in your model, and all fields will pick this up:

```php

use Backpack\CRUD\app\Models\Traits\CrudTrait;

class Category
{
    use CrudTrait;

    // you can define this

    /**
     * Attribute shown on the element to identify this model.
     *
     * @var string
     */
    protected $identifiableAttribute = 'title';

    // or for more complicated use cases you can do
    
    /**
     * Get the attribute shown on the element to identify this model.
     *
     * @return string
     */
    public function identifiableAttribute()
    {
        // process stuff here
        return 'whatever_you_want_even_an_accessor';
    }
}
```
<a name="default-field-types"></a>
## FREE Field Types

<a name="checkbox"></a>
### checkbox

Checkbox for true/false.

```php
[   // Checkbox
    'name'  => 'active',
    'label' => 'Active',
    'type'  => 'checkbox'
],
```

Input preview: 

![CRUD Field - checkbox](https://backpackforlaravel.com/uploads/docs-4-2/fields/checkbox.png)

<hr>

<a name="checklist"></a>
### checklist

Show a list of checkboxes, for the user to check one or more of them.

```php
[   // Checklist
    'label'     => 'Roles',
    'type'      => 'checklist',
    'name'      => 'roles',
    'entity'    => 'roles',
    'attribute' => 'name',
    'model'     => "Backpack\PermissionManager\app\Models\Role",
    'pivot'     => true,
    // 'number_of_columns' => 3,
],
```

**Note: If you don't use a pivot table (pivot = false), you need to cast your db column as `array` in your model,by adding your column to your model's `$casts`. **

Input preview: 

![CRUD Field - checklist](https://backpackforlaravel.com/uploads/docs-4-2/fields/checklist.png)

<hr>

<a name="checklist-dependency"></a>
### checklist_dependency

```php
[   // two interconnected entities
    'label'             => 'User Role Permissions',
    'field_unique_name' => 'user_role_permission',
    'type'              => 'checklist_dependency',
    'name'              => ['roles', 'permissions'], // the methods that define the relationship in your Models
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

Input preview: 

![CRUD Field - checklist_dependency](https://backpackforlaravel.com/uploads/docs-4-2/fields/checklist_dependency.png)

<hr>

<a name="color"></a>
### color

```php
[   // Color
    'name'    => 'background_color',
    'label'   => 'Background Color',
    'type'    => 'color',
    'default' => '#000000',
],
```

Input preview: 

![CRUD Field - color](https://backpackforlaravel.com/uploads/docs-4-2/fields/color.png)

<hr>

<a name="custom-html"></a>
### custom_html

Allows you to insert custom HTML in the create/update forms. Usually used in forms with a lot of fields, to separate them using h1-h5, hr, etc, but can be used for any HTML.

```php
[   // CustomHTML
    'name'  => 'separator',
    'type'  => 'custom_html',
    'value' => '<hr>'
],
```
**NOTE** If you would like to disable the `wrapper` on this field, eg. when using a `<fieldset>` tag in your custom html, you can achieve it by using `wrapper => false` on field definition.

<a name="date"></a>
### date

```php
[   // Date
    'name'  => 'birthday',
    'label' => 'Birthday',
    'type'  => 'date'
],
```

Input preview: 

![CRUD Field - date](https://backpackforlaravel.com/uploads/docs-4-2/fields/date.png)

<hr>

<a name="datetime"></a>
### datetime

```php
[   // DateTime
    'name'  => 'start',
    'label' => 'Event start',
    'type'  => 'datetime'
],
```

**Please note:** if you're using datetime [attribute casting](https://laravel.com/docs/5.3/eloquent-mutators#attribute-casting) on your model, you also need to place this mutator inside your model:
```php
	public function setDatetimeAttribute($value) {
		$this->attributes['datetime'] = \Carbon\Carbon::parse($value);
	}
```
Otherwise the input's datetime-local format will cause some errors.

Input preview: 

![CRUD Field - datetime](https://backpackforlaravel.com/uploads/docs-4-2/fields/datetime.png)

<hr>

<a name="email"></a>
### email

```php
[   // Email
    'name'  => 'email',
    'label' => 'Email Address',
    'type'  => 'email'
],
```

Input preview: 

![CRUD Field - email](https://backpackforlaravel.com/uploads/docs-4-2/fields/email.png)


<hr>

<a name="enum"></a>
### enum

Show a select with the values for an ENUM database column, or an PHP enum (introduced in PHP 8.1).

##### Database ENUM
When used with a database enum it requires that the database column type is `enum`. In case it's nullable it will also show `-` (empty) option.

PLEASE NOTE the `enum` field using database enums only works for MySQL.

```php
[
    'name'  => 'status',
    'label' => 'Status',
    'type'  => 'enum',
    // optional, specify the enum options with custom display values
    'options' => [
        'DRAFT' => 'Is Draft',
        'PUBLISHED' => 'Is Published'
    ]
],
```

##### PHP enum

If you are using a `BackedEnum` your best option is to cast it in your model, and Backpack know how to handle it without aditional configuration. 

```php
// in your model (eg. Article)

protected $casts = ['status' => \App\Enums\StatusEnum::class]; //assumes you have this enum created

// and in your controller
[
    'name'  => 'status',
    'label' => 'Status',
    'type'  => 'enum'
    // optional
    //'enum_class' => 'App\Enums\StatusEnum',
    //'enum_function' => 'readableStatus',
],
```

In case it's not a `BackedEnum` or you don't want to cast it in your Model, you should provide the enum class to the field:

```php
[
    'name'  => 'status',
    'label' => 'Status',
    'type'  => 'enum',
    'enum_class' => \App\Enums\StatusEnum::class
],
```

Input preview: 

![CRUD Field - enum](https://backpackforlaravel.com/uploads/docs-4-2/fields/enum.png)

<hr>

<a name="hidden"></a>
### hidden

Include an `<input type="hidden">` in the form.

```php
[   // Hidden
    'name'  => 'status',
    'type'  => 'hidden',
    'value' => 'active',
],
```

<hr>

<a name="month"></a>
### month

Include an `<input type="month">` in the form.

**Important**: This input type is supported by most modern browsers, but not all. [See compatibility table here](https://caniuse.com/mdn-html_elements_input_type_month). We have a workaround below.

```php
[   // Month
    'name'  => 'month',
    'label' => 'Month',
    'type'  => 'month'
],
```

Input preview: 

![CRUD Field - month](https://backpackforlaravel.com/uploads/docs-4-2/fields/month.png)

**Workaround**

Since not all browsers support this input type, if you are using [Backpack PRO](https://backpackforlaravel.com/products/pro-for-one-project) you can customize the `date_picker` field to have a similar behavior:
```php
[   
    'name'  => 'month',
    'type'  => 'date_picker',
    'date_picker_options' => [
        'format'   => 'yyyy-mm',
        'minViewMode' => 'months'
    ],
]
```
**Important**: you should be using a date/datetime column as database column type if using `date_picker`.

<hr>

<a name="number"></a>
### number

Shows an input type=number to the user, with optional prefix and suffix:

```php
[   // Number
    'name' => 'number',
    'label' => 'Number',
    'type' => 'number',

    // optionals
    // 'attributes' => ["step" => "any"], // allow decimals
    // 'prefix'     => "$",
    // 'suffix'     => ".00",
],
```

Input preview:

![CRUD Field - number](https://backpackforlaravel.com/uploads/docs-4-2/fields/number.png)

<hr>

<a name="password"></a>
### password

```php
[   // Password
    'name'  => 'password',
    'label' => 'Password',
    'type'  => 'password'
],
```

Input preview:

![CRUD Field - password](https://backpackforlaravel.com/uploads/docs-4-2/fields/password.png)


Please note that this will NOT hash/encrypt the string before it stores it to the database. You need to hash the password manually. The most popular way to do that are:

1. Using [a mutator on your Model](https://laravel.com/docs/7.x/eloquent-mutators#defining-a-mutator). For example:

```php
public function setPasswordAttribute($value) {
    $this->attributes['password'] = Hash::make($value);
}
```

2. By overwriting the Create/Update operation methods, inside the Controller. There's a working example [in our PermissionManager package](https://github.com/Laravel-Backpack/PermissionManager/blob/master/src/app/Http/Controllers/UserCrudController.php#L103-L124) but the gist of it is this:

```php
    use \Backpack\CRUD\app\Http\Controllers\Operations\CreateOperation { store as traitStore; }
    
    public function store()
    {
        $this->crud->setRequest($this->crud->validateRequest());
    
        /** @var \Illuminate\Http\Request $request */
        $request = $this->crud->getRequest();

        // Encrypt password if specified.
        if ($request->input('password')) {
            $request->request->set('password', Hash::make($request->input('password')));
        } else {
            $request->request->remove('password');
        }

        $this->crud->setRequest($request);
        $this->crud->unsetValidation(); // Validation has already been run

        return $this->traitStore();
    }
```


<hr>

<a name="radio"></a>
### radio

Show radios according to an associative array you give the input and let the user pick from them. You can choose for the radio options to be displayed inline or one-per-line.

```php
[   // radio
    'name'        => 'status', // the name of the db column
    'label'       => 'Status', // the input label
    'type'        => 'radio',
    'options'     => [
        // the key will be stored in the db, the value will be shown as label; 
        0 => "Draft",
        1 => "Published"
    ],
    // optional
    //'inline'      => false, // show the radios all on the same line?
],
```

Input preview: 

![CRUD Field - radio](https://backpackforlaravel.com/uploads/docs-4-2/fields/radio.png)

<hr>

<a name="range"></a>
### range

Shows an HTML5 range element, allowing the user to drag a cursor left-right, to pick a number from a defined range.

```php
[   // Range
    'name'  => 'range',
    'label' => 'Range',
    'type'  => 'range',
    //optional
    'attributes' => [
        'min' => 0,
        'max' => 10,
    ],
],
```

Input preview: 

![CRUD Field - range](https://backpackforlaravel.com/uploads/docs-4-2/fields/range.png)

<hr>

<a name="select"></a>
### select (1-n relationship)

Show a Select with the names of the connected entity and let the user select one of them.
Your relationships should already be defined on your models as hasOne() or belongsTo().

```php
[  // Select
   'label'     => "Category",
   'type'      => 'select',
   'name'      => 'category_id', // the db column for the foreign key

   // optional
   // 'entity' should point to the method that defines the relationship in your Model
   // defining entity will make Backpack guess 'model' and 'attribute'
   'entity'    => 'category',

   // optional - manually specify the related model and attribute
   'model'     => "App\Models\Category", // related model
   'attribute' => 'name', // foreign key attribute that is shown to user

   // optional - force the related options to be a custom query, instead of all();
   'options'   => (function ($query) {
        return $query->orderBy('name', 'ASC')->where('depth', 1)->get();
    }), //  you can use this to filter the results show in the select
],
```

For more information about the optional attributes that fields use when they interact with related entries - [look here](#optional-attributes-for-fields-containing-related-entries).

Input preview:

![CRUD Field - select](https://backpackforlaravel.com/uploads/docs-4-2/fields/select.png)


<hr>


<a name="select-grouped"></a>
### select_grouped

Display a select where the options are grouped by a second entity (like Categories).

```php
[   // select_grouped
    'label'     => 'Articles grouped by categories',
    'type'      => 'select_grouped', //https://github.com/Laravel-Backpack/CRUD/issues/502
    'name'      => 'article_id',
    'entity'    => 'article',
    'attribute' => 'title',
    'group_by'  => 'category', // the relationship to entity you want to use for grouping
    'group_by_attribute' => 'name', // the attribute on related model, that you want shown
    'group_by_relationship_back' => 'articles', // relationship from related model back to this model
],
```

For more information about the optional attributes that fields use when they interact with related entries - [look here](#optional-attributes-for-fields-containing-related-entries).

Input preview:

![CRUD Field - select_grouped](https://backpackforlaravel.com/uploads/docs-4-2/fields/select_grouped.png)

<hr>

<a name="select-multiple"></a>
### select_multiple (n-n relationship)

Show a Select with the names of the connected entity and let the user select any number of them.
Your relationship should already be defined on your models as belongsToMany().

```php
[   // SelectMultiple = n-n relationship (with pivot table)
    'label'     => "Tags",
    'type'      => 'select_multiple',
    'name'      => 'tags', // the method that defines the relationship in your Model

    // optional
    'entity'    => 'tags', // the method that defines the relationship in your Model
    'model'     => "App\Models\Tag", // foreign key model
    'attribute' => 'name', // foreign key attribute that is shown to user
    'pivot'     => true, // on create&update, do you need to add/delete pivot table entries?

    // also optional
    'options'   => (function ($query) {
        return $query->orderBy('name', 'ASC')->where('depth', 1)->get();
    }), // force the related options to be a custom query, instead of all(); you can use this to filter the results show in the select
],
```

For more information about the optional attributes that fields use when they interact with related entries - [look here](#optional-attributes-for-fields-containing-related-entries).

Input preview:

![CRUD Field - select_multiple](https://backpackforlaravel.com/uploads/docs-4-2/fields/select_multiple.png)


<hr>

<a name="select-from-array"></a>
### select_from_array

Display a select with the values you want:

```php
[   // select_from_array
    'name'        => 'template',
    'label'       => "Template",
    'type'        => 'select_from_array',
    'options'     => ['one' => 'One', 'two' => 'Two'],
    'allows_null' => false,
    'default'     => 'one',
    // 'allows_multiple' => true, // OPTIONAL; needs you to cast this to array in your model;
],
```

Input preview:

![CRUD Field - select_from_array](https://backpackforlaravel.com/uploads/docs-4-2/fields/select_from_array.png)

<hr>

<a name="summernote"></a>
### summernote

Show a [Summernote wysiwyg editor](http://summernote.org/) to the user.

```php
[   // Summernote
    'name'  => 'description',
    'label' => 'Description',
    'type'  => 'summernote',
    'options' => [],
],

// the summernote field works with the default configuration options but allow developer to configure to his needs
// optional configuration check https://summernote.org/deep-dive/ for a list of available configs
[
    'name'  => 'description',
    'label' => 'Description',
    'type'  => 'summernote',
    'options' => [
        'toolbar' => [
            ['font', ['bold', 'underline', 'italic']]
        ]
    ],
],

```

> NOTE: Summernote does NOT sanitize the input. If you do not trust the users of this field, you should sanitize the input or output using something like HTML Purifier. Personally we like to use install [mewebstudio/Purifier](https://github.com/mewebstudio/Purifier) and add an [accessor or mutator](https://laravel.com/docs/8.x/eloquent-mutators#accessors-and-mutators) on the Model, so that wherever the model is created from (admin panel or app), the output will always be clean. [Example here](https://github.com/Laravel-Backpack/demo/commit/7342cffb418bb568b9e4ee279859685ddc0456c1).

Input preview:

![CRUD Field - summernote](https://backpackforlaravel.com/uploads/docs-4-2/fields/summernote.png)

<hr>

<a name="switch"></a>
### switch

Show a switch (aka toggle) for boolean attributes (true/false). It's an alternative to the `checkbox` field type - prettier and more customizable: it allows the dev to choose the background color and what shows up on the on/off sides of the switch.

```php
[   // Switch
    'name'  => 'switch',
    'type'  => 'switch',
    'label'    => 'I have not read the terms and conditions and I never will',

    // optional
    'color'    => 'primary', // May be any bootstrap color class or an hex color
    'onLabel' => '✓',
    'offLabel' => '✕',
],
```

Input preview: 

![CRUD Field - switch](https://backpackforlaravel.com/uploads/docs-5-0/fields/switch.png)

<hr>

<a name="text"></a>
### text

The basic field type, all it needs is the two mandatory parameters: name and label.

```php
[   // Text
    'name'  => 'title',
    'label' => "Title",
    'type'  => 'text',

    // optional
    //'prefix'     => '',
    //'suffix'     => '',
    //'default'    => 'some value', // default value
    //'hint'       => 'Some hint text', // helpful text, show up after input
    //'attributes' => [
       //'placeholder' => 'Some text when empty',
       //'class' => 'form-control some-class',
       //'readonly'  => 'readonly',
       //'disabled'  => 'disabled',
     //], // extra HTML attributes and values your input might need
     //'wrapper'   => [
       //'class' => 'form-group col-md-12'
     //], // extra HTML attributes for the field wrapper - mostly for resizing fields

],
```

You can use the optional 'prefix' and 'suffix' attributes to display something before and after the input, like icons, path prefix, etc:

Input preview:

![CRUD Field - text](https://backpackforlaravel.com/uploads/docs-4-2/fields/text.png)

<hr>

<a name="textarea"></a>
### textarea

Show a textarea to the user.

```php
[   // Textarea
    'name'  => 'description',
    'label' => 'Description',
    'type'  => 'textarea'
],
```

Input preview:

![CRUD Field - textarea](https://backpackforlaravel.com/uploads/docs-4-2/fields/textarea.png)

<hr>

<a name="time"></a>
### time

```php
[   // Time
    'name'  => 'start',
    'label' => 'Start time',
    'type'  => 'time'
],
```

<hr>

<a name="upload"></a>
### upload

**Step 1.** Show a file input to the user:
```php
[   // Upload
    'name'      => 'image',
    'label'     => 'Image',
    'type'      => 'upload',
    'upload'    => true,
    'disk'      => 'uploads', // if you store files in the /public folder, please omit this; if you store them in /storage or S3, please specify it;
    // optional:
    'temporary' => 10 // if using a service, such as S3, that requires you to make temporary URLs this will make a URL that is valid for the number of minutes specified
],
```

**Step 2.** In order to save/update/delete the file from disk&db, we need to create [a mutator](https://laravel.com/docs/5.3/eloquent-mutators#defining-a-mutator) on your model:
```php
public function setImageAttribute($value)
    {
    	$attribute_name = "image";
    	$disk = "public";
    	$destination_path = "folder_1/subfolder_1";

    	$this->uploadFileToDisk($value, $attribute_name, $disk, $destination_path, $fileName = null);

	// return $this->attributes[{$attribute_name}]; // uncomment if this is a translatable field
    }
```

**How it works:**

The field sends the file, through a Request, to the Controller. The Controller then tries to create/update the Model. That's when the mutator on your model will run. That also means we can do any [file validation](https://laravel.com/docs/5.3/validation#rule-file) (```file```, ```image```, ```mimetypes```, ```mimes```) in the Request, before the file is stored on the disk.

>NOTE: If this field is mandatory (required in validation) please use the [sometimes laravel validation rule](https://laravel.com/docs/9.x/validation#conditionally-adding-rules) together with **required** in your validation. (sometimes|required|file etc... )

[The ```uploadFileToDisk()``` method](https://github.com/Laravel-Backpack/CRUD/blob/master/src/app/Models/Traits/HasUploadFields.php#L31-L59) will take care of everything for most use cases:

```php
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
public function uploadFileToDisk($value, $attribute_name, $disk, $destination_path, $fileName = null) {}
```

If you wish to have a different functionality, you can delete the method call from your mutator and do your own thing.

>**The uploaded files are not deleted for you.** When you delete an entry (whether through CRUD or the application), the uploaded files will not be deleted.

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

Input preview:

![CRUD Field - upload](https://backpackforlaravel.com/uploads/docs-4-2/fields/upload.png)

<hr>

<a name="upload-multiple"></a>
### upload_multiple

Shows a multiple file input to the user and stores the values as a JSON array in the database.

**Step 0.** Make sure the db column can hold the amount of text this field will have. For example, for MySQL, VARCHAR(255) might not be enough all the time (for 3+ files), so it's better to go with TEXT. Make sure you're using a big column type in your migration or db.

**Step 1.** Show a multiple file input to the user:
```php
[   // Upload
    'name'      => 'photos',
    'label'     => 'Photos',
    'type'      => 'upload_multiple',
    'upload'    => true,
    'disk'      => 'uploads', // if you store files in the /public folder, please omit this; if you store them in /storage or S3, please specify it;
    // optional:
    'temporary' => 10 // if using a service, such as S3, that requires you to make temporary URLs this will make a URL that is valid for the number of minutes specified
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

**Step 4.** Validation - If you need to validate the field within your `EntityRequest.php`, you can use Laravel's built-in array validation (notice the asterisk):

```php
return [
    'upload_multiple.*' => [
        'nullable',
        'max:2048', // file size in KB
        'mimetypes:image/jpeg', // allow only some mimetypes
    ],
];
```
**How it works:**

The field sends the files, through a Request, to the Controller. The Controller then tries to create/update the Model. That's when the mutator on your model will run. That also means we can do any [file validation](https://laravel.com/docs/5.3/validation#rule-file) (```file```, ```image```, ```mimetypes```, ```mimes```) in the Request, before the files are stored on the disk.

[The ```uploadMultipleFilesToDisk()``` method](https://github.com/Laravel-Backpack/CRUD/blob/master/src/app/Models/Traits/HasUploadFields.php#L76-L113) will take care of everything for most use cases:

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
 */
public function uploadMultipleFilesToDisk($value, $attribute_name, $disk, $destination_path) {}
```

If you wish to have a different functionality, you can delete the method call from your mutator and do your own thing.

>**The uploaded files are not deleted for you.** When you delete an entry (whether through CRUD or the application), the uploaded files will not be deleted.

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

Input preview:

![CRUD Field - upload_multiple](https://backpackforlaravel.com/uploads/docs-4-2/fields/upload_multiple.png)

<hr>

<a name="url"></a>
### url

```php
[   // URL
    'name'  => 'link',
    'label' => 'Link to video file',
    'type'  => 'url'
],
```

<hr>

<a name="view"></a>
### view

Load a custom view in the form.

```php
[   // view
    'name' => 'custom-ajax-button',
    'type' => 'view',
    'view' => 'partials/custom-ajax-button'
],
```

**Note:** the same functionality can be achieved using a [custom field type](/docs/{{version}}/crud-fields#creating-a-custom-field-type), or using the [custom_html field type](/docs/{{version}}/crud-fields#custom-html) (if the content is really simple).

**NOTE** If you would like to disable the `wrapper` on this field, you can achieve it by using `wrapper => false` on field definition.

<hr>

<a name="week"></a>
### week

Include an `<input type="week">` in the form.

**Important**: This input type is supported by most modern browsers, not all. [See compatibility table here](https://caniuse.com/mdn-html_elements_input_type_week).

```php
[   // Week
    'name'  => 'first_week',
    'label' => 'First week',
    'type'  => 'week'
],
```

Input preview:

![CRUD Field - week](https://backpackforlaravel.com/uploads/docs-4-2/fields/week.png)



<a name="pro-field-types"></a>
## PRO Field Types


<a name="address_algolia"></a>
### address_algolia <span class="badge badge-pill badge-info">PRO</span>

Use [Algolia Places autocomplete](https://community.algolia.com/places/) to help users type their address faster. With the ```store_as_json``` option, it will store the address, postcode, city, country, latitude and longitude in a JSON in the database. Without it, it will just store the address string. For information stored as JSON in the database, it's recommended that you use [attribute casting](https://mattstauffer.co/blog/laravel-5.0-eloquent-attribute-casting) to ```array``` or ```object```. That way, every time you get the info from the database you'd get it in a usable format.

```php
[   // Address algolia
    'name'          => 'address',
    'label'         => 'Address',
    'type'          => 'address_algolia',
    // optional
    'store_as_json' => true
],
```

> **Algolia is killing Places.** Please note that Algolia Places **will stop working in May 2022**, as reported in [this announcement](https://www.algolia.com/blog/product/sunseting-our-places-feature/). For that reason, it's probably a good idea to use the `address_google` field instead (it's right after this one).


Input preview:

![CRUD Field - address](https://backpackforlaravel.com/uploads/docs-4-2/fields/address.png)

<hr>

<a name="address_google"></a>
### address_google <span class="badge badge-pill badge-info">PRO</span>

Use [Google Places Search](https://developers.google.com/places/web-service/search) to help users type their address faster. With the ```store_as_json``` option, it will store the address, postcode, city, country, latitude and longitude in a JSON in the database. Without it, it will just store the complete address string.

```php
[   // Address google
    'name'          => 'address',
    'label'         => 'Address',
    'type'          => 'address_google',
    // optional
    'store_as_json' => true
],
```

Using Google Places API is dependent on using an API Key. Please [get an API key](https://console.cloud.google.com/apis/credentials) - you do have to configure billing, but you qualify for $200/mo free usage, which covers most use cases. Then copy-paste that key as your ```services.google_places.key``` value. So inside your ```config/services.php``` please add the items below:

```php
'google_places' => [
    'key' => 'the-key-you-got-from-google-places'
],
```

> **Use attribute casting.** For information stored as JSON in the database, it's recommended that you use [attribute casting](https://mattstauffer.co/blog/laravel-5.0-eloquent-attribute-casting) to ```array``` or ```object```. That way, every time you get the info from the database you'd get it in a usable format. Also, it is heavily recommended that your database column can hold a large JSON - so use `text` rather than `string` in your migration (in MySQL this translates to `text` instead of `varchar`).

Input preview:

![CRUD Field - address](https://backpackforlaravel.com/uploads/docs-4-2/fields/address_google.png)

<hr>

<a name="browse"></a>
### browse <span class="badge badge-pill badge-info">PRO</span>

This button allows the admin to open [elFinder](http://elfinder.org/) and select a file from there. Run ```composer require backpack/filemanager && php artisan backpack:filemanager:install``` to install [FileManager](https://github.com/laravel-backpack/filemanager), then you can use the field:

```php
[   // Browse
    'name'  => 'image',
    'label' => 'Image',
    'type'  => 'browse'
],
```


Input preview:

![CRUD Field - browse](https://backpackforlaravel.com/uploads/docs-4-2/fields/browse.png)

Onclick preview:

![CRUD Field - browse popup](https://backpackforlaravel.com/uploads/docs-4-2/fields/browse_popup.png)

<hr>

<a name="browse-multiple"></a>
### browse_multiple <span class="badge badge-pill badge-info">PRO</span>

Open elFinder and select multiple files from there. Run ```composer require backpack/filemanager && php artisan backpack:filemanager:install``` to install [FileManager](https://github.com/laravel-backpack/filemanager), then you can use the field:

```php
[   // Browse multiple
    'name'          => 'files',
    'label'         => 'Files',
    'type'          => 'browse_multiple',
    // 'multiple'   => true, // enable/disable the multiple selection functionality
    // 'sortable'   => false, // enable/disable the reordering with drag&drop
    // 'mime_types' => null, // visible mime prefixes; ex. ['image'] or ['application/pdf']
],
```

The field assumes you've cast your attribute as ```array``` on your model.  That way, when you do ```$entry->files``` you get a nice array.
**NOTE:** If you use `multiple => false` you should NOT cast your attribute as ```array```

Input preview:

![CRUD Field - browse_multiple](https://backpackforlaravel.com/uploads/docs-4-2/fields/browse_multiple.png)

<hr>

<a name="base64-image"></a>
### base64_image <span class="badge badge-pill badge-info">PRO</span>

Upload an image and store it in the database as Base64. Notes:
- make sure the column type is LONGBLOB;
- detailed [instructions and customisations here](https://github.com/Laravel-Backpack/CRUD/pull/56#issue-164712261);

```php
// base64_image
$this->crud->addField([
    'label'        => "Profile Image",
    'name'         => "image",
    'filename'     => "image_filename", // set to null if not needed
    'type'         => 'base64_image',
    'aspect_ratio' => 1, // set to 0 to allow any aspect ratio
    'crop'         => true, // set to true to allow cropping, false to disable
    'src'          => NULL, // null to read straight from DB, otherwise set to model accessor function
]);
```

Input preview:

![CRUD Field - base64_image](https://backpackforlaravel.com/uploads/docs-4-2/fields/base64_image.png)

<hr>

<a name="ckeditor"></a>
### ckeditor <span class="badge badge-pill badge-info">PRO</span>

Show a wysiwyg CKEditor to the user.

```php
[   // CKEditor
    'name'          => 'description',
    'label'         => 'Description',
    'type'          => 'ckeditor',

    // optional:
    'extra_plugins' => ['oembed', 'widget'],
    'options'       => [
        'autoGrow_minHeight'   => 200,
        'autoGrow_bottomSpace' => 50,
        'removePlugins'        => 'resize,maximize',
    ]
],
```

If you'd like to be able to select files from elFinder, you need to also run ```composer require backpack/filemanager``` to install elFinder.

Input preview:

![CRUD Field - ckeditor](https://backpackforlaravel.com/uploads/docs-4-2/fields/ckeditor.png)

<hr>

<a name="color-picker"></a>
### color_picker <span class="badge badge-pill badge-info">PRO</span>

Show a pretty colour picker using [Bootstrap Colorpicker](https://itsjavi.com/bootstrap-colorpicker/).

```php
[   // color_picker
    'label'                => 'Background Color',
    'name'                 => 'background_color',
    'type'                 => 'color_picker',
    'default'              => '#000000',

    // optional
    // Anything your define inside `color_picker_options` will be passed as JS
    // to the JavaScript plugin. For more information about the options available
    // please see the plugin docs at:
    //  ### https://itsjavi.com/bootstrap-colorpicker/module-options.html
    'color_picker_options' => [
        'customClass' => 'custom-class',
        'horizontal' => true,
        'extensions' => [
            [
                'name' => 'swatches', // extension name to load
                'options' => [ // extension options
                    'colors' => [
                        'primary' => '#337ab7',
                        'success' => '#5cb85c',
                        'info' => '#5bc0de',
                        'warning' => '#f0ad4e',
                        'danger' => '#d9534f'
                    ],
                    'namesAsValues' => false
                ]
            ]
        ]
    ]
],
```

Input preview:

![CRUD Field - color_picker](https://backpackforlaravel.com/uploads/docs-4-2/fields/color_picker.png)

<hr>

<a name="date-range"></a>
### date_range <span class="badge badge-pill badge-info">PRO</span>

Show a DateRangePicker and let the user choose a start date and end date.

```php
[   // date_range
    'name'  => ['start_date', 'end_date'], // db columns for start_date & end_date
    'label' => 'Event Date Range',
    'type'  => 'date_range',

    // OPTIONALS
    // default values for start_date & end_date
    'default'            => ['2019-03-28 01:01', '2019-04-05 02:00'],
    // options sent to daterangepicker.js
    'date_range_options' => [
        'drops' => 'down', // can be one of [down/up/auto]
        'timePicker' => true,
        'locale' => ['format' => 'DD/MM/YYYY HH:mm']
    ]
],
```

Please note it is recommended that you use [attribute casting](https://laravel.com/docs/5.3/eloquent-mutators#attribute-casting) on your model (cast to date).

Your end result will look like this:

Input preview:

![CRUD Field - date_range](https://backpackforlaravel.com/uploads/docs-4-2/fields/date_range.png)



<hr>

<a name="date-picker"></a>
### date_picker <span class="badge badge-pill badge-info">PRO</span>

Show a pretty [Bootstrap Datepicker](http://bootstrap-datepicker.readthedocs.io/en/latest/).

```php
[   // date_picker
   'name'  => 'date',
   'type'  => 'date_picker',
   'label' => 'Date',

   // optional:
   'date_picker_options' => [
      'todayBtn' => 'linked',
      'format'   => 'dd-mm-yyyy',
      'language' => 'fr'
   ],
],
```

Please note it is recommended that you use [attribute casting](https://laravel.com/docs/5.3/eloquent-mutators#attribute-casting) on your model (cast to date).


Input preview:

![CRUD Field - date_picker](https://backpackforlaravel.com/uploads/docs-4-2/fields/date_picker.png)

<hr>

<a name="datetime-picker"></a>
### datetime_picker <span class="badge badge-pill badge-info">PRO</span>

Show a [Bootstrap Datetime Picker](https://eonasdan.github.io/bootstrap-datetimepicker/).

```php
[   // DateTime
    'name'  => 'start',
    'label' => 'Event start',
    'type'  => 'datetime_picker',

    // optional:
    'datetime_picker_options' => [
        'format' => 'DD/MM/YYYY HH:mm',
        'language' => 'pt',
        'tooltips' => [ //use this to translate the tooltips in the field
                'today' => 'Hoje',
                'selectDate' => 'Selecione a data',
                // available tooltips: today, clear, close, selectMonth, prevMonth, nextMonth, selectYear, prevYear, nextYear, selectDecade, prevDecade, nextDecade, prevCentury, nextCentury, pickHour, incrementHour, decrementHour, pickMinute, incrementMinute, decrementMinute, pickSecond, incrementSecond, decrementSecond, togglePeriod, selectTime, selectDate
        ]
    ],
    'allows_null' => true,
    // 'default' => '2017-05-12 11:59:59',
],
```

**Please note:** if you're using date [attribute casting](https://laravel.com/docs/5.3/eloquent-mutators#attribute-casting) on your model, you may also need to place this mutator inside your model:
```php
    public function setDatetimeAttribute($value) {
        $this->attributes['datetime'] = \Carbon\Carbon::parse($value);
    }
```
Otherwise the input's datetime-local format will cause some errors. Remember to change "datetime" with the name of your attribute (column name).

Input preview:

![CRUD Field - datetime_picker](https://backpackforlaravel.com/uploads/docs-4-2/fields/datetime_picker.png)

<hr>

<a name="easymde"></a>
### easymde <span class="badge badge-pill badge-info">PRO</span>

Show an [EasyMDE - Markdown Editor](https://github.com/Ionaru/easy-markdown-editor) to the user. EasyMDE is a well-maintained fork of SimpleMDE.

```php
[   // easymde
    'name'  => 'description',
    'label' => 'Description',
    'type'  => 'easymde',
    // optional
    // 'easymdeAttributes' => [
    //   'promptURLs'   => true,
    //   'status'       => false,
    //   'spellChecker' => false,
    //   'forceSync'    => true,
    // ],
    // 'easymdeAttributesRaw' => $some_json
],
```

> NOTE: The contents displayed in this editor are NOT stripped, sanitized or escaped by default. Whenever you store Markdown or HTML inside your database, it's HIGHLY recommended that you sanitize the input or output. Laravel makes it super-easy to do that on the model using [accessors](https://laravel.com/docs/8.x/eloquent-mutators#accessors-and-mutators). If you do NOT trust the admins who have access to this field (or end-users can also store information to this db column), please make sure this attribute is always escaped, before it's shown. You can do that by running the value through `strip_tags()` in an accessor on the model (here's [an example](https://github.com/Laravel-Backpack/demo/commit/509c0bf0d8b9ee6a52c50f0d2caed65f1f986385)) or better yet, using an [HTML Purifier package](https://github.com/mewebstudio/Purifier) (here's [an example](https://github.com/Laravel-Backpack/demo/commit/7342cffb418bb568b9e4ee279859685ddc0456c1)).

Input preview:

![CRUD Field - easymde](https://backpackforlaravel.com/uploads/docs-4-2/fields/easymde.png)

<hr>

<a name="google-map"></a>
### google_map <span class="badge badge-pill badge-info">PRO</span>

Shows a map and allows the user to navigate and select a position on that map (using the Google Places API). The field stores the latitude, longitude and the address string as a JSON in the database ( eg. `{lat: 123, lng: 456, formatted_address: 'Lisbon, Portugal'}`). If you want to save the info in separate db columns, continue reading below.

```php
CRUD::addField([
    'name' => 'location',
    'type' => 'google_map',
    // optionals
    'map_options' => [
        'default_lat' => 123,
        'default_lng' => 456,
        'locate' => false, // when false, only a map is displayed. No value for submition.
        'height' => 400 // in pixels
    ]
]);
```

Using Google Places API is dependent on using an API Key. Please [get an API key](https://console.cloud.google.com/apis/credentials) - you do have to configure billing, but you qualify for $200/mo free usage, which covers most use cases. Then copy-paste that key as your ```services.google_places.key``` value. So inside your ```config/services.php``` please add the items below:

```php
'google_places' => [
    'key' => 'the-key-you-got-from-google-places'
],
```

**How to save in multiple inputs?**

There are cases where you rather save the information on separate inputs in the database. In that scenario you should use [Laravel mutators and accessors](https://laravel.com/docs/9.x/eloquent-mutators). Using the same field as previously shown (**field name is `location`**), and having `latitude`, `longitude`, `full_address` as the database columns, we can save and retrieve them separately too:
```php

//add all the fields to model fillable property, including the one that we are not going to save (location in the example)
$fillable = ['location', 'latitude', 'longitude', 'full_address'];

// 
protected function location(): \Illuminate\Database\Eloquent\Casts\Attribute
{
    return \Illuminate\Database\Eloquent\Casts\Attribute::make(
        get: function($value, $attributes) {
            return json_encode([
            'lat' => $attributes['lat'], 
            'lng' => $attributes['lng'], 
            'formatted_address' => $attributes['full_address'] ?? ''
            ], JSON_HEX_TAG | JSON_HEX_APOS | JSON_HEX_AMP | JSON_HEX_QUOT | JSON_THROW_ON_ERROR);
        },
        set: function($value) {
            $location = json_decode($value);
            return [
                'lat' => $location->lat,
                'lng' => $location->lng,
                'full_address' => $location->formatted_address ?? ''
            ];
        }
    );
}

```

Input preview:

![image](https://user-images.githubusercontent.com/7188159/208295372-f2dcbe71-73b7-452d-9904-428f725cdbce.png)

<hr>

<a name="icon-picker"></a>
### icon_picker <span class="badge badge-pill badge-info">PRO</span>

Show an icon picker. Supported icon sets are fontawesome, lineawesome, glyphicon, ionicon, weathericon, mapicon, octicon, typicon, elusiveicon, materialdesign as per the jQuery plugin, [bootstrap-iconpicker](http://victor-valencia.github.io/bootstrap-iconpicker/).

The stored value will be the class name (ex: fa-home).

```php
[   // icon_picker
    'label'   => "Icon",
    'name'    => 'icon',
    'type'    => 'icon_picker',
    'iconset' => 'fontawesome' // options: fontawesome, lineawesome, glyphicon, ionicon, weathericon, mapicon, octicon, typicon, elusiveicon, materialdesign
],
```

Your input will look like button, with a dropdown where the user can search or pick an icon:

Input preview:

![CRUD Field - icon_picker](https://backpackforlaravel.com/uploads/docs-4-2/fields/icon_picker.png)

<hr>

<a name="image"></a>
### image <span class="badge badge-pill badge-info">PRO</span>

Upload an image and store it on the disk.

**Step 1.** Show the field.
```php
// image
$this->crud->addField([
    'label' => "Profile Image",
    'name' => "image",
    'type' => 'image',
    'crop' => true, // set to true to allow cropping, false to disable
    'aspect_ratio' => 1, // omit or set to 0 to allow any aspect ratio
    // 'disk'      => 's3_bucket', // in case you need to show images from a different disk
    // 'prefix'    => 'uploads/images/profile_pictures/' // in case your db value is only the file name (no path), you can use this to prepend your path to the image src (in HTML), before it's shown to the user;
]);
```

**Step 2.** Add a [mutator](https://laravel.com/docs/7.x/eloquent-mutators#defining-a-mutator) to your Model, where you pick up the uploaded file and store it wherever you want. You can use this boilerplate code and modify it to match your use case.

**NOTE: The code below requires that you have ```intervention/image``` installed. If you don't, please do ```composer require intervention/image``` first.**

```php
// ..

use Illuminate\Support\Str;
use Intervention\Image\ImageManagerStatic as Image;

// ..

Class Product extends Model
{
    // ..

    public function setImageAttribute($value)
    {
        $attribute_name = "image";
        // or use your own disk, defined in config/filesystems.php
        $disk = config('backpack.base.root_disk_name');
        // destination path relative to the disk above
        $destination_path = "public/uploads/folder_1/folder_2";

        // if the image was erased
        if (empty($value)) {
            // delete the image from disk
            if (isset($this->{$attribute_name}) && !empty($this->{$attribute_name})) {
                \Storage::disk($disk)->delete($this->{$attribute_name});  
            }
            // set null on database column
            $this->attributes[$attribute_name] = null;
        }


        // if a base64 was sent, store it in the db
        if (Str::startsWith($value, 'data:image'))
        {
            // 0. Make the image
            $image = \Image::make($value)->encode('jpg', 90);

            // 1. Generate a filename.
            $filename = md5($value.time()).'.jpg';

            // 2. Store the image on disk.
            \Storage::disk($disk)->put($destination_path.'/'.$filename, $image->stream());

            // 3. Delete the previous image, if there was one.
            if (isset($this->{$attribute_name}) && !empty($this->{$attribute_name})) {
                \Storage::disk($disk)->delete($this->{$attribute_name});
            }

            // 4. Save the public path to the database
            // but first, remove "public/" from the path, since we're pointing to it
            // from the root folder; that way, what gets saved in the db
            // is the public URL (everything that comes after the domain name)
            $public_destination_path = Str::replaceFirst('public/', '', $destination_path);
            $this->attributes[$attribute_name] = $public_destination_path.'/'.$filename;
        } elseif (!empty($value)) {
            // if value isn't empty, but it's not an image, assume it's the model value for that attribute.
            $this->attributes[$attribute_name] = $this->{$attribute_name};
        }
    }

// ..
```
> **The uploaded images are not deleted for you.** If you delete an entry (using the CRUD or anywhere inside your app), the image file won't be deleted from the disk.
> If you're NOT using soft deletes on that Model and want the image to be deleted at the same time the entry is, just specify that in your Model's ```deleting``` event:
> ```php
>   public static function boot()
>   {
>       parent::boot();
>       static::deleted(function($obj) {
>           \Storage::disk('public_folder')->delete($obj->image);
>       });
>   }
> ```

**A note about aspect_ratio**
The value for aspect ratio is a float that represents the ratio of the cropping rectangle height and width. By way of example,

- Square = 1
- Landscape = 2
- Portrait = 0.5

And you can, of course, use any value for more extreme rectangles.

Input preview:

![CRUD Field - image](https://backpackforlaravel.com/uploads/docs-4-2/fields/image.png)

> NOTE: if you are having trouble uploading big images, please check your php extensions **apcu** and/or **opcache**, users have reported some issues with these extensions when trying to upload very big images. REFS: https://github.com/Laravel-Backpack/CRUD/issues/3457

<hr>

<a name="phone"></a>
### phone <span class="badge badge-pill badge-info">PRO</span>

Show a telephone number input. Lets the user choose the prefix using a flag from dropdown.

```php
[   // phone
    'name'  => 'phone', // db column for phone
    'label' => 'Phone',
    'type'  => 'phone',

    // OPTIONALS
    // most options provided by intlTelInput.js are supported, you can try them out using the `config` attribute;
    //  take note that options defined in `config` will override any default values from the field;
    'config' => [
        'onlyCountries' => ['bd', 'cl', 'in', 'lv', 'pt', 'ro'],
        'initialCountry' => 'cl', // this needs to be in the allowed country list, either in `onlyCountries` or NOT in `excludeCountries`
        'separateDialCode' => true,
        'nationalMode' => true,
        'autoHideDialCode' => false,
        'placeholderNumberType' => 'MOBILE',
    ]
],
```

For more info about parameters please see this JS plugin's [official documentation](https://github.com/jackocnr/intl-tel-input).

Your end result will look like this:

![CRUD Field - phone](https://user-images.githubusercontent.com/1032474/204588174-48935030-54e6-4a30-b34c-7e94220ae242.png)

> NOTE: you can validate this using Laravel's default **numeric** or if you want something advanced, we recommend [Laravel Phone](https://github.com/Propaganistas/Laravel-Phone)

<hr>

<a name="relationship"></a>
### relationship <span class="badge badge-pill badge-info">PRO</span>

Shows the user a `select2` input, allowing them to choose one/more entries of a related Eloquent Model. In order to work, the relationships need to be properly defined on the Model.

Input preview (for both 1-n and n-n relationships):

![CRUD Field - relationship](https://backpackforlaravel.com/uploads/docs-4-2/fields/relationship.png)

To achieve the above, you just need to point the field to the relationship method on your Model (eg. `category`, not `category_id`):

```php
[   // relationship
    'name' => 'category', // the method on your model that defines the relationship
    'type' => "relationship",

    // OPTIONALS:
    // 'label' => "Category",
    // 'attribute' => "title", // attribute on model that is shown to user
    // 'placeholder' => "Select a category", // placeholder for the select2 input
 ],
```

More more optional attributes on relationship fields [look here](#optional-attributes-for-fields-containing-related-entries).

Out of the box, it supports all common relationships:
- ✅ `hasOne` (1-1) - shows subform if you define `subfields`
- ✅ `belongsTo` (n-1) - shows a select2 (single)
- ✅ `hasMany` (1-n) - shows a select2_multiple OR a subform if you define `subfields`
- ✅ `belongsToMany` (n-n) - shows a select2_multiple OR a subform if you define `subfields` for pivot extras
- ✅ `morphOne` (1-1) - shows a subform if you define `subfields`
- ✅ `morphMany` (1-n) - shows a select2_multiple OR a subform if you define `subfields`
- ✅ `morphToMany` (n-n) - shows a select2_multiple OR a subform if you define `subfields` for pivot extras
- ✅ `morphTo` (n-1) - shows the `_type` and `_id` selects for morphTo relations

It does NOT support the following Eloquent relationships, since they don't make sense in this context:
- ❌ `hasOneThrough` (1-1-1) - it's read-only, no sense having a field for it;
- ❌ `hasManyThrough` (1-1-n) - it's read-only, no sense having a field for it;
- ❌ Has One Of Many (1-n turned into 1-1) - it's read-only, no sense having a field for it;
- ❌ Morph One Of Many (1-n turned into 1-1) - it's read-only, no sense having a field for it;
- ❌ `morphedByMany` (n-n inverse) - never needed, UI would be very difficult to understand & use at this moment.

The relationship field is a plug-and-play solution, 90% of the time it will cover all you need by just pointing it to a relationship on the model. But it also has a few optional features, that will greatly help you out in more complex use cases:

<a name="load-entries-from-ajax-calls-using-the-fetch-operation"></a>
#### Load entries from AJAX calls - using the Fetch Operation

If your related entry has hundreds, thousands or millions of entries, it's not practical to load the options using an onpage Eloquent query, because the Create/Update page would be very slow to load. In this case, you should instruct the `relationship` field to fetch the entries using AJAX calls.

**Step 1.** Add `'ajax' => true` to your relationship field definition:

```php
[   // relationship
    'type' => "relationship",
    'name' => 'category', // the method on your model that defines the relationship
    'ajax' => true,

    // OPTIONALS:
    // 'label'       => "Category",
    // 'attribute'   => "name", // foreign key attribute that is shown to user (identifiable attribute)
    // 'entity'      => 'category', // the method that defines the relationship in your Model
    // 'model'       => "App\Models\Category", // foreign key Eloquent model
    // 'placeholder' => "Select a category", // placeholder for the select2 input

    // AJAX OPTIONALS:
    // 'delay'                   => 500, // the minimum amount of time between ajax requests when searching in the field
    // 'data_source'             => url("fetch/category"), // url to controller search function (with /{id} should return model)
    // 'minimum_input_length'    => 2, // minimum characters to type before querying results
    // 'dependencies'            => ['category'], // when a dependency changes, this select2 is reset to null
    // 'method'                  => 'POST', // optional - HTTP method to use for the AJAX call (GET, POST)
    // 'include_all_form_fields' => false, // optional - only send the current field through AJAX (for a smaller payload if you're not using multiple chained select2s)
 ],
```

**Step 2.** Create the route and method that responds to the AJAX calls. Fortunately, the `FetchOperation` allows you to easily do just that. Inside the same CrudController where you've defined the `relationship` field, use the `FetchOperation` trait, and define a new method that will respond to AJAX queries for that entity:

```php
    use \Backpack\CRUD\app\Http\Controllers\Operations\FetchOperation;

    public function fetchCategory()
    {
        return $this->fetch(\App\Models\Category::class);
    }
```

This will set up a ```/fetch/category``` route, which points to ```fetchCategory()```, which returns the search results. For more on how this operation works (and how you can customize it), see the [FetchOperation docs](/docs/{{version}}/crud-operation-fetch).


<a name="create-related-entries-in-a-modal-using-the-inlinecreate-operation"></a>
#### Create related entries in a modal - using the InlineCreate Operation

Works for the `BelongsTo`, `BelongsToMany` and `MorphToMany` relationships.

Searching with AJAX provides a great UX. But what if the user doesn't find what they're looking for? In that case, it would be useful to add a related entry on-the-fly, without leaving the main form:

![CRUD Field - relationship fetch with inline create](https://backpackforlaravel.com/uploads/docs-4-2/fields/relationship_inlineCreate.gif)

If you are using the Fetch operation to get the entries, you're already halfway there. In addition to using Fetch as instructed in the section above, you should perform two additional steps:

**Step 1.** Add `inline_create` to your field definition in **the current CrudController**:

```php
// for 1-n relationships (ex: category)
[
    'type'          => "relationship",
    'name'          => 'category',
    'ajax'          => true,
    'inline_create' => true, // <--- THIS
],
// for n-n relationships (ex: tags)
[
    'type'          => "relationship",
    'name'          => 'tags', // the method on your model that defines the relationship
    'ajax'          => true,
    'inline_create' => [ 'entity' => 'tag' ]  // <--- OR THIS
],
// in this second example, the relation is called `tags` (plural),
// but we need to define the entity as "tag" (singural)
```

**Step 2.** On the CrudController of that secondary entity (that the user will be able to create on-the-fly, eg. `CategoryCrudController` or `TagCrudController`), you'll need to enable the InlineCreate operation:
```php
class CategoryCrudController extends CrudController
{
    use \Backpack\CRUD\app\Http\Controllers\Operations\InlineCreateOperation;

   // ...
}
```

This ```InlineCreateOperation``` will allow us to show _the same fields that are inside the Create operation_, inside a new operation _InlineCreate_, that is available in a modal. For more information, check out the [InlineCreate Operation docs](/docs/{{version}}/crud-operation-inline-create).

Remember, ```FetchOperation``` is still needed on the main crud (ex: ```ArticleCrudController```) so that the entries are fetched by ```select2``` using AJAX.

#### Save additional data to pivot table

For relationships with a pivot table (n-n relationships: `BelongsToMany`, `MorphToMany`), that contain other columns other than the foreign keys themselves, the `relationship` field provides a quick way for your admin to edit those "extra attributes on the pivot table". For example, if you have these database tables:

```php
// - companies: id, name
// - company_person: company_id, person_id, job_title, job_description
// - persons: id, first_name, last_name
```

You might want the admin to define the `job_title` and `job_description` of a person, when creating/editing a company. So instead of this:


![CRUD Field - belongsToMany relationship without custom pivot table attributes](https://backpackforlaravel.com/uploads/docs-4-2/fields/relationship_belongsToMany_noPivot.png)

you might your admin to see this:

![CRUD Field - belongsToMany relationship with custom pivot table attributes](https://backpackforlaravel.com/uploads/docs-4-2/fields/relationship_belongsToMany_withPivot.png)



The `relationship` field allows you to easily do that. Here's how:

**Step 1.** On your models, make sure the extra database columns are defined, using `withPivot()`:

```php
// inside the Company model
public function people()
{
    return $this->belongsToMany(\App\Models\Person::class)
                ->withPivot('job_title', 'job_description');
}
// inside the Person model
public function companies()
{
    return $this->belongsToMany(\App\Models\Company::class)
                ->withPivot('job_title', 'job_description');
}
```

**Step 2.** Inside your `relationship` field definition, add `subfields` for those two db columns:

```php
// Inside PersonCrudController
[
    'name'          => 'companies',
    'type'          => "relationship",
     // ..
    'subfields'   => [
        [
            'name' => 'job_title',
            'type' => 'text',
            'wrapper' => [
                'class' => 'form-group col-md-3',
            ],
        ],
        [
            'name' => 'job_description',
            'type' => 'text',
            'wrapper' => [
                'class' => 'form-group col-md-9',
            ],
        ],
    ],
],
```

**That's it.** Backpack now will save those additional inputs on the pivot table.

Additionally, if you want to change something about the primary select, you can do that using the `pivotSelect` attribute:

```php
[
    'name'          => 'companies',
    'type'          => "relationship",
    'subfields'   => [ ... ],
     // ..
    'pivotSelect'=> [
        // 'ajax' => true,
        'placeholder' => 'Pick a company',
        'wrapper' => [
            'class' => 'col-md-6',
        ],
    ],
]
```

#### Manage related entries in the same form (create, update, delete)

Sometimes for `hasMany` and `morphMany` relationships, the secondary entry cannot stand on its own. It's so dependent on the primary entry, that you don't want it to have a Create/Update form of its own - you just want it to be managed inside its parent. Let's take `Invoice` and `InvoiceItem` as an example, with the following database structure:

```php
// - invoices: id, number, due_date, payment_status
// - invoice_items: id, order, description, unit, quantity, unit_price
```

Most likely, what you _really_ want is to be able to create/update/delete `InvoiceItems` right inside the `Invoice` form:

![CRUD Field - hasMany relationship editing the items on-the-fly](https://backpackforlaravel.com/uploads/docs-4-2/fields/repeatable_hasMany_entries.png)

To achieve the above, you just need to add a `relationship` field for your `hasMany` or `morphMany` relationship and define the `subfields` you want for that secondary Model:

```php
[
    'name'          => 'items',
    'type'          => "relationship",
    'subfields'   => [
        [
            'name' => 'order',
            'type' => 'number',
            'wrapper' => [
                'class' => 'form-group col-md-1',
            ],
        ],
        [
            'name' => 'description',
            'type' => 'text',
            'wrapper' => [
                'class' => 'form-group col-md-6',
            ],
        ],
        [
            'name' => 'unit',
            'label' => 'U.M.',
            'type' => 'text',
            'wrapper' => [
                'class' => 'form-group col-md-1',
            ],
        ],
        [
            'name' => 'quantity',
            'type' => 'number',
            'attributes' => ["step" => "any"],
            'wrapper' => [
                'class' => 'form-group col-md-2',
            ],
        ],
        [
            'name' => 'unit_price',
            'type' => 'number',
            'attributes' => ["step" => "any"],
            'wrapper' => [
                'class' => 'form-group col-md-2',
            ],
        ],
    ],
]
```

Backpack will then take care of the creating/updating/deleting of the secondary model, after the "Save" button is clicked on the form.


<a name="create-related-entries-in-a-modal-using-the-inlinecreate-operation"></a>
#### Delete related entries or fall back to default

Normally, when the admin removes a relationship from the "select", only the relationship gets deleted, _not_ the related entry. But for the `hasMany` and `morphMany` relationships, it can also make sense to want to remove the related entry entirely. That's why for those relationships, you can also instruct Backpack to remove the _related entry_ upon saving OR change the foreign key to a default value (fallback).

```php
// Inside ArticleCrudController
[
    'type'          => "relationship",
    'name'          => 'comments',
    // when removed, use fallback_id
    'fallback_id'   => 3, // will relate to the comment with ID "3"
    // when removed, delete the entry
    'force_delete'  => true, // will delete that comment
],
```

<hr>

<a name="repeatable"></a>
### repeatable <span class="badge badge-pill badge-info">PRO</span>

Shows a group of inputs to the user, and allows the user to add or remove groups of that kind:

![CRUD Field - repeatable](https://backpackforlaravel.com/uploads/docs-4-2/fields/repeatable.png)

**Since v5**: repeatable returns an array when the form is submitted instead of the already parsed json. **You must cast** the repeatable field to **ARRAY** or **JSON** in your model.

Clicking on the "New Item" button will add another group with the same subfields (in the example, another Testimonial).

You can use most field types inside the field groups, add as many subfields you need, and change their width using ```wrapper``` like you would do outside the repeatable field. But please note that:
- **all subfields defined inside a field group need to have their definition valid and complete**; you can't use shorthands, you shouldn't assume fields will guess attributes for you;
- some field types do not make sense as subfields inside repeatable (for example, relationship fields might not make sense; they will work if the relationship is defined on the main model, but upon save the selected entries will NOT be saved as relationships, they will be saved as JSON; you can intercept the saving if you want and do whatever you want);
- a few fields _make sense_, but _cannot_ work inside repeatable (ex: upload, upload_multiple); [see the notes inside the PR](https://github.com/Laravel-Backpack/CRUD/pull/2266#issuecomment-559436214) for more details, and a complete list of the fields; the few fields that do not work inside repeatable have sensible alternatives;
- **VALIDATION**: you can validate subfields the same way you validate [nested arrays in Laravel](https://laravel.com/docs/8.x/validation#validating-nested-array-input) Eg: `testimonial.*.name => 'required'`
- **FIELD USAGE AND RELATIONSHIPS**: note that it's not possible to use a repeatable field inside other repeatable field. Relationships that use `subfields` are under the hood repeatable fields, so the relationship subfields cannot include other repeatable field.

```php
[   // repeatable
    'name'  => 'testimonials',
    'label' => 'Testimonials',
    'type'  => 'repeatable',
    'subfields' => [ // also works as: "fields"
        [
            'name'    => 'name',
            'type'    => 'text',
            'label'   => 'Name',
            'wrapper' => ['class' => 'form-group col-md-4'],
        ],
        [
            'name'    => 'position',
            'type'    => 'text',
            'label'   => 'Position',
            'wrapper' => ['class' => 'form-group col-md-4'],
        ],
        [
            'name'    => 'company',
            'type'    => 'text',
            'label'   => 'Company',
            'wrapper' => ['class' => 'form-group col-md-4'],
        ],
        [
            'name'  => 'quote',
            'type'  => 'ckeditor',
            'label' => 'Quote',
        ],
    ],

    // optional
    'new_item_label'  => 'Add Group', // customize the text of the button
    'init_rows' => 2, // number of empty rows to be initialized, by default 1
    'min_rows' => 2, // minimum rows allowed, when reached the "delete" buttons will be hidden
    'max_rows' => 2, // maximum rows allowed, when reached the "new item" button will be hidden
    // allow reordering?
    'reorder' => false, // hide up&down arrows next to each row (no reordering)
    'reorder' => true, // show up&down arrows next to each row
    'reorder' => 'order', // show arrows AND add a hidden subfield with that name (value gets updated when rows move)
    'reorder' => ['name' => 'order', 'type' => 'number', 'attributes' => ['data-reorder-input' => true]], // show arrows AND add a visible number subfield
],
```

<hr>

<a name="select2"></a>
### select2 (1-n relationship) <span class="badge badge-pill badge-info">PRO</span>

Works just like the SELECT field, but prettier. Shows a Select2 with the names of the connected entity and let the user select one of them.
Your relationships should already be defined on your models as hasOne() or belongsTo().

```php
[  // Select2
   'label'     => "Category",
   'type'      => 'select2',
   'name'      => 'category_id', // the db column for the foreign key

   // optional
   'entity'    => 'category', // the method that defines the relationship in your Model
   'model'     => "App\Models\Category", // foreign key model
   'attribute' => 'name', // foreign key attribute that is shown to user
   'default'   => 2, // set the default value of the select2

    // also optional
   'options'   => (function ($query) {
        return $query->orderBy('name', 'ASC')->where('depth', 1)->get();
    }), // force the related options to be a custom query, instead of all(); you can use this to filter the results show in the select
],
```

For more information about the optional attributes that fields use when they interact with related entries - [look here](#optional-attributes-for-fields-containing-related-entries).

Input preview:

![CRUD Field - select2](https://backpackforlaravel.com/uploads/docs-4-2/fields/select2_nested.png)


<hr>

<a name="select2-multiple"></a>
### select2_multiple (n-n relationship) <span class="badge badge-pill badge-info">PRO</span>

[Works just like the SELECT field, but prettier]

Shows a Select2 with the names of the connected entity and let the user select any number of them.
Your relationship should already be defined on your models as belongsToMany().

```php
[    // Select2Multiple = n-n relationship (with pivot table)
     'label'     => "Tags",
     'type'      => 'select2_multiple',
     'name'      => 'tags', // the method that defines the relationship in your Model

     // optional
     'entity'    => 'tags', // the method that defines the relationship in your Model
     'model'     => "App\Models\Tag", // foreign key model
     'attribute' => 'name', // foreign key attribute that is shown to user
     'pivot'     => true, // on create&update, do you need to add/delete pivot table entries?
     // 'select_all' => true, // show Select All and Clear buttons?

     // optional
     'options'   => (function ($query) {
         return $query->orderBy('name', 'ASC')->where('depth', 1)->get();
     }), // force the related options to be a custom query, instead of all(); you can use this to filter the results show in the select
],
```

For more information about the optional attributes that fields use when they interact with related entries - [look here](#optional-attributes-for-fields-containing-related-entries).

Input preview:

![CRUD Field - select2_multiple](https://backpackforlaravel.com/uploads/docs-4-2/fields/select2_multiple.png)

<hr>

<a name="select2-nested"></a>
### select2_nested <span class="badge badge-pill badge-info">PRO</span>

Display a select2 with the values ordered hierarchically and indented, for an entity where you use [Reorder](https://backpackforlaravel.com/docs/5.x/crud-operation-reorder). Please mind that the connected model needs:
- a ```children()``` relationship pointing to itself;
- the usual ```lft```, ```rgt```, ```depth``` attributes;

```php
[   // select2_nested
    'name'      => 'category_id',
    'label'     => "Category",
    'type'      => 'select2_nested',
    'entity'    => 'category', // the method that defines the relationship in your Model
    'attribute' => 'name', // foreign key attribute that is shown to user

    // optional
    'model'     => "App\Models\Category", // force foreign key model
],
```

For more information about the optional attributes that fields use when they interact with related entries - [look here](#optional-attributes-for-fields-containing-related-entries).

Input preview:

![CRUD Field - select2_nested](https://backpackforlaravel.com/uploads/docs-4-2/fields/select2_nested.png)

<hr>

<a name="select2-grouped"></a>
### select2_grouped <span class="badge badge-pill badge-info">PRO</span>

Display a select2 where the options are grouped by a second entity (like Categories).

```php
[   // select2_grouped
    'label'     => 'Articles grouped by categories',
    'type'      => 'select2_grouped', //https://github.com/Laravel-Backpack/CRUD/issues/502
    'name'      => 'article_id',
    'entity'    => 'article', // the method that defines the relationship in your Model
    'attribute' => 'title',
    'group_by'  => 'category', // the relationship to entity you want to use for grouping
    'group_by_attribute' => 'name', // the attribute on related model, that you want shown
    'group_by_relationship_back' => 'articles', // relationship from related model back to this model
],
```

For more information about the optional attributes that fields use when they interact with related entries - [look here](#optional-attributes-for-fields-containing-related-entries).

Input preview:

![CRUD Field - select2_grouped](https://backpackforlaravel.com/uploads/docs-4-2/fields/select2_grouped.png)


<hr>

<a name="select_and_order"></a>
### select_and_order <span class="badge badge-pill badge-info">PRO</span>

Display items on two columns and let the user drag&drop between them to choose which items are selected and which are not, and reorder the selected items with drag&drop.

Its definition is exactly as ```select_from_array```, but the value will be stored as JSON in the database: ```["3","5","7","6"]```, so it needs the attribute to be cast to array on the Model:

```php
protected $casts = [
    'featured' => 'array'
];
```

Definition:

```php
[   // select_and_order
    'name'  => 'featured',
    'label' => "Featured",
    'type'  => 'select_and_order',
    'options' => [
        1 => "Option 1",
        2 => "Option 2"
    ]
],
```

Also possible:

```php
[   // select_and_order
    'name'    => 'featured',
    'label'   => 'Featured',
    'type'    => 'select_and_order',
    'options' => Product::get()->pluck('title','id')->toArray(),
],
```

Input preview:

![CRUD Field - select_and_order](https://backpackforlaravel.com/uploads/docs-4-2/fields/select_and_order.png)


<hr>

<a name="select2-from-array"></a>
### select2_from_array <span class="badge badge-pill badge-info">PRO</span>

Display a select2 with the values you want:

```php
[   // select2_from_array
    'name'        => 'template',
    'label'       => "Template",
    'type'        => 'select2_from_array',
    'options'     => ['one' => 'One', 'two' => 'Two'],
    'allows_null' => false,
    'default'     => 'one',
    // 'allows_multiple' => true, // OPTIONAL; needs you to cast this to array in your model;
],
```

Input preview:

![CRUD Field - select2_from_array](https://backpackforlaravel.com/uploads/docs-4-2/fields/select2_from_array.png)

<hr>

<a name="select2-from-ajax"></a>
### select2_from_ajax <span class="badge badge-pill badge-info">PRO</span>

Display a select2 that takes its values from an AJAX call.

```php
[   // 1-n relationship
    'label'       => "End", // Table column heading
    'type'        => "select2_from_ajax",
    'name'        => 'category_id', // the column that contains the ID of that connected entity
    'entity'      => 'category', // the method that defines the relationship in your Model
    'attribute'   => "name", // foreign key attribute that is shown to user
    'data_source' => url("api/category"), // url to controller search function (with /{id} should return model)

    // OPTIONAL
    // 'delay'                   => 500, // the minimum amount of time between ajax requests when searching in the field
    // 'placeholder'             => "Select a category", // placeholder for the select
    // 'minimum_input_length'    => 2, // minimum characters to type before querying results
    // 'model'                   => "App\Models\Category", // foreign key model
    // 'dependencies'            => ['category'], // when a dependency changes, this select2 is reset to null
    // 'method'                  => 'POST', // optional - HTTP method to use for the AJAX call (GET, POST)
    // 'include_all_form_fields' => false, // optional - only send the current field through AJAX (for a smaller payload if you're not using multiple chained select2s)
],
```

For more information about the optional attributes that fields use when they interact with related entries - [look here](#optional-attributes-for-fields-containing-related-entries).

Of course, you also need to create make the data_source above respond to AJAX calls. You can use the [FetchOperation](https://backpackforlaravel.com/docs/4.1/crud-operation-fetch) to quickly do that in your current CrudController, or you can set up your custom API by creating a custom Route and Controller. Here's an example:

```php
Route::post('/api/category', 'Api\CategoryController@index');
```

```php
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
}
```

**Note:** If you want to also make this field work inside `repeatable` too, your API endpoint will also need to respond to the `keys` parameter, with the actual items that have those keys. For example:

```php
        if ($request->has('keys')) {
            return Category::findMany($request->input('keys'));
        }
```

Input preview:

![CRUD Field - select2_from_array](https://backpackforlaravel.com/uploads/docs-4-2/fields/select2_from_array.png)

<hr>

<a name="select2-from-ajax-multiple"></a>
### select2_from_ajax_multiple <span class="badge badge-pill badge-info">PRO</span>

Display a select2 that takes its values from an AJAX call. Same as [select2_from_ajax](#section-select2_from_ajax) above, but allows for multiple items to be selected. The only difference in the field definition is the "pivot" attribute.

```php
[   // n-n relationship
    'label'       => "Cities", // Table column heading
    'type'        => "select2_from_ajax_multiple",
    'name'        => 'cities', // a unique identifier (usually the method that defines the relationship in your Model)
    'entity'      => 'cities', // the method that defines the relationship in your Model
    'attribute'   => "name", // foreign key attribute that is shown to user
    'data_source' => url("api/city"), // url to controller search function (with /{id} should return model)
    'pivot'       => true, // on create&update, do you need to add/delete pivot table entries?

    // OPTIONAL
    'delay'                      => 500, // the minimum amount of time between ajax requests when searching in the field
    'model'                      => "App\Models\City", // foreign key model
    'placeholder'                => "Select a city", // placeholder for the select
    'minimum_input_length'       => 2, // minimum characters to type before querying results
    // 'method'                  => 'POST', // optional - HTTP method to use for the AJAX call (GET, POST)
    // 'include_all_form_fields' => false, // optional - only send the current field through AJAX (for a smaller payload if you're not using multiple chained select2s)
],
```

For more information about the optional attributes that fields use when they interact with related entries - [look here](#optional-attributes-for-fields-containing-related-entries).

Of course, you also need to create a controller and routes for the data_source above. Here's an example:

```php
Route::post('/api/city', 'Api\CityController@index');
Route::post('/api/city/{id}', 'Api\CityController@show');
```

```php
<?php

namespace App\Http\Controllers\Api;

use Illuminate\Http\Request;
use App\Http\Controllers\Controller;
use App\Models\City;

class CityController extends Controller
{
    public function index(Request $request)
    {
        $search_term = $request->input('q');
        $page = $request->input('page');

        if ($search_term)
        {
            $results = City::where('name', 'LIKE', '%'.$search_term.'%')->paginate(10);
        }
        else
        {
            $results = City::paginate(10);
        }

        return $results;
    }

    public function show($id)
    {
        return City::find($id);
    }
}
```

Input preview:

![CRUD Field - select2_from_ajax_multiple](https://backpackforlaravel.com/uploads/docs-4-2/fields/select2_from_ajax_multiple.png)

**Note:** If you want to also make this field work inside `repeatable` too, your API endpoint will also need to respond to the `keys` parameter, with the actual items that have those keys. For example:

```php
        if ($request->has('keys')) {
            return City::findMany($request->input('keys'));
        }
```

<hr>

### slug  <span class="badge badge-pill badge-info">PRO</span>

Track the value of a different text input and turn it into a valid URL segment (aka. slug), as you type, using JS:

```php
[   // Text
    'name'  => 'slug',
    'target'  => 'title', // will turn the title input into a slug
    'label' => "Slug",
    'type'  => 'slug',

    // optional
    //'prefix'     => '',
    //'suffix'     => '',
    //'default'    => 'some value', // default value
    //'hint'       => 'Some hint text', // helpful text, show up after input
    //'attributes' => [
       //'placeholder' => 'Some text when empty',
       //'class' => 'form-control some-class',
       //'readonly'  => 'readonly',
       //'disabled'  => 'disabled',
     //], // extra HTML attributes and values your input might need
     //'wrapper'   => [
       //'class' => 'form-group col-md-12'
     //], // extra HTML attributes for the field wrapper - mostly for resizing fields

],
```

Input preview:
![CleanShot 2022-06-04 at 13 13 40](https://user-images.githubusercontent.com/1032474/171994919-cbdd8b9d-6823-4b26-82ed-7c2868c0cee8.gif)


By default, it will also slugify when the target input is edited. If you want to stop that behaviour, you can do that by removing the `target` on your edit operation. For example:

```php
    protected function setupUpdateOperation()
    {
        $this->setupCreateOperation();

        // disable editing the slug when editing
        $this->crud->field('slug')->target('')->attributes(['readonly' => 'readonly']);
    }
```

<hr>

<a name="table"></a>
### table <span class="badge badge-pill badge-info">PRO</span>

Show a table with multiple inputs per row and store the values as JSON array of objects in the database. The user can add more rows and reorder the rows as they please.

```php
[   // Table
    'name'            => 'options',
    'label'           => 'Options',
    'type'            => 'table',
    'entity_singular' => 'option', // used on the "Add X" button
    'columns'         => [
        'name'  => 'Name',
        'desc'  => 'Description',
        'price' => 'Price'
    ],
    'max' => 5, // maximum rows allowed in the table
    'min' => 0, // minimum rows allowed in the table
],
```

>It's highly recommended that you use [attribute casting](https://mattstauffer.co/blog/laravel-5.0-eloquent-attribute-casting) on your model when working with JSON arrays stored in database columns, and cast this attribute to either ```object``` or ```array``` in your Model.

Input preview:

![CRUD Field - table](https://backpackforlaravel.com/uploads/docs-4-2/fields/table.png)


<hr>

<a name="tinymce"></a>
### tinymce <span class="badge badge-pill badge-info">PRO</span>

Show a wysiwyg (TinyMCE) to the user.

```php
[   // TinyMCE
    'name'  => 'description',
    'label' => 'Description',
    'type'  => 'tinymce',
    // optional overwrite of the configuration array
    // 'options' => [ 
        //'selector' => 'textarea.tinymce',  
        //'skin' => 'dick-light', 
        //'plugins' => 'image link media anchor' 
    // ],
],
```

Input preview:

![CRUD Field - tinymce](https://backpackforlaravel.com/uploads/docs-4-2/fields/tinymce.png)

**NOTE**: if you want to modify the toolbar buttons (add or remove), here is the default configured toolbar so you can modify it:

```php
'options' => ['toolbar' => 'undo redo | styleselect | bold italic | alignleft aligncenter alignright alignjustify | outdent indent'],
```

Some buttons are related to specific plugins and need them to work, please read more about it here: [tiny mce available toolbar buttons](https://www.tiny.cloud/docs/advanced/available-toolbar-buttons/)

<hr>

<a name="video"></a>
### video <span class="badge badge-pill badge-info">PRO</span>

Allow the user to paste a YouTube/Vimeo link. That will get the video information with JavaScript and store it as a JSON in the database.

Field definition:
```php
[   // URL
    'name'            => 'video',
    'label'           => 'Link to video file on YouTube or Vimeo',
    'type'            => 'video',
    'youtube_api_key' => 'AIzaSycLRoVwovRmbIf_BH3X12IcTCudAErRlCE',
],
```

An entry stored in the database will look like this:
```
$video = {
    id: 234324,
    title: 'my video title',
    image: 'https://provider.com/image.jpg',
    url: 'http://provider.com/video',
    provider: 'youtube'
}
```

So you should use [attribute casting](https://mattstauffer.co/blog/laravel-5.0-eloquent-attribute-casting) in your model, to cast the video as ```array``` or ```object```.

Vimeo does not require an API key in order to query their DB, but YouTube does, even though their free quota is generous. You can get a free YouTube API Key inside [Google Developers Console](https://console.developers.google.com/) ([video tutorial here](https://www.youtube.com/watch?v=pP4zvduVAqo)). Please DO NOT use our API Key - create your own. The key above is there just for your convenience, to easily try out the field. As soon as you decide to use this field type, create an API Key and use _your_ API Key. Our key hits its ceiling every month, so if you use our key most of the time it won't work.


<hr>

<a name="wysiwyg"></a>
### wysiwyg <span class="badge badge-pill badge-info">PRO</span>

Show a wysiwyg (CKEditor) to the user.

```php
[   // WYSIWYG Editor
    'name'  => 'description',
    'label' => 'Description',
    'type'  => 'wysiwyg'
],
```


<a name="overwriting-default-field-types"></a>
## Overwriting Default Field Types

The actual field types are stored in the Backpack/CRUD package in ```/resources/views/fields```. If you need to change an existing field, you don't need to modify the package, you just need to add a blade file in your application in ```/resources/views/vendor/backpack/crud/fields```, with the same name. The package checks there first, and only if there's no file there, will it load it from the package.

To quickly publish a field blade file in your project, you can use ```php artisan backpack:field --from=field_name```. For example, to publish the number field type, you'd type ```php artisan backpack:field --from=number```

>Please keep in mind that if you're using _your_ file for a field type, you're not using the _package file_. So any updates we push to that file, you're not getting them. In most cases, it's recommended you create a custom field type for your use case, instead of overwriting default field types.

<a name="creating-a-custom-field-type"></a>
## Creating a Custom Field Type

If you need to extend the CRUD with a new field type, you create a new file in your application in ```/resources/views/vendor/backpack/crud/fields```. Use a name that's different from all default field types.

```bash
// to create one using Backpack\Generators, run:
php artisan backpack:field new_field_name

// alternatively, to create a new field similar an existing field, run: 
php artisan backpack:field new_field_name --from=old_field_name
```


That's it, you'll now be able to use it just like a default field type.

Your field definition will be something like:

```php
[   // Custom Field
    'name'  => 'address',
    'label' => 'Home address',
    'type'  => 'address'
    /// 'view_namespace' => 'yourpackage' // use a custom namespace of your package to load views within a custom view folder.
],
```

And your blade file something like:
```php
<!-- field_type_name -->
@include('crud::fields.inc.wrapper_start')
    <label>{!! $field['label'] !!}</label>
    <input
        type="text"
        name="{{ $field['name'] }}"
        value="{{ old($field['name']) ? old($field['name']) : (isset($field['value']) ? $field['value'] : (isset($field['default']) ? $field['default'] : '' )) }}"
        @include('crud::fields.inc.attributes')
    >

    {{-- HINT --}}
    @if (isset($field['hint']))
        <p class="help-block">{!! $field['hint'] !!}</p>
    @endif
@include('crud::fields.inc.wrapper_end')


@if ($crud->fieldTypeNotLoaded($field))
    @php
        $crud->markFieldTypeAsLoaded($field);
    @endphp

    {{-- FIELD EXTRA CSS  --}}
    {{-- push things in the after_styles section --}}
    @push('crud_fields_styles')
        <!-- no styles -->
    @endpush


    {{-- FIELD EXTRA JS --}}
    {{-- push things in the after_scripts section --}}
    @push('crud_fields_scripts')
        <!-- no scripts -->
    @endpush
@endif
```

Inside your custom field type, you can use these variables:
- ```$crud``` - all the CRUD Panel settings, options and variables;
- ```$entry``` - in the Update operation, the current entry being modified (the actual values);
- ```$field``` - all attributes that have been passed for this field;

If your field type uses JavaScript, we recommend you:
- put a ```data-init-function="bpFieldInitMyCustomField"``` attribute on your input;
- place your logic inside the scripts section mentioned above, inside ```function bpFieldInitMyCustomField(element) {}```; of course, you choose the name of the function but it has to match whatever you specified as data attribute on the input, and it has to be pretty unique; inside this method, you'll find that ```element``` is jQuery-wrapped object of the element where you specified ```data-init-function```; this should be enough for you to not have to use IDs, or any other tricks, to determine other elements inside the DOM - determine them in relation to the main element; if you want, you can choose to put the ```data-init-function``` attribute on a different element, like the wrapping div;


<a name="manipulating-fields-with-javascript"></a>
## Manipulating Fields with JavaScript

When you need to add custom interactions (if field is X then do Y), we have just the thing for you. You can easily add custom interactions, using our **CrudField JavaScript API**. It's already loaded on our Create / Update pages, in the global `crud` object, and it makes it dead-simple to select a field - `crud.field('title')` - using a syntax that's very familiar to our PHP syntax, then do the most common things on it.

For more information, please see the dedicated page about our [CrudField Javascript API](/docs/{{version}}/crud-fields-javascript-api).
