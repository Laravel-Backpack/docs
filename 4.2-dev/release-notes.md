# Release Notes

---

**Launch date:** (expected) end of Dec, 2021

Anybody with a 4.0 or 4.1 license can install and use 4.2, but in order to get the new features in a project that currently runs 4.1, you need to [follow the upgrade guide](/docs/{{version}}/upgrade-guide). Don't get fooled by the fact that we kept the 4.x.x prefix - that's just to show that **Backpack 4.2 is a FREE upgrade from 4.1**. But **Backpack 4.2 is a MAJOR new version**, with 12 months of work put into it, and major improvements under-the-hood. It is the current and recommended version of Backpack and it's got so many cool new things that we couldn't fit them all inside this page.

But what Backpack 4.2 brings to the table and why you should upgrade from [Backpack 4.1](/docs/4.1) to 4.2:


<a name="added"></a>
## Added

### Operations

#### Warning before leaving the Create or Update form

For large forms, it's often smart to ask the user if they really want to lose their form changes, before they leave the page. That's exactly what `CRUD::set('warnBeforeLeaving', true)` will do, just call it in your `setupCreateOperation()` or `setupUpdateOperation()`.


#### Quickly validate forms inside CrudController, no FormRequest needed

Previously, you could validate the Create & Update forms in one way - by telling Backpack which FormRequest holds the validation rules: `CRUD::setValidation(TagRequest::class)`. Which works very well for large models. But for very simple models... like Tags... do you really _need_ a `TagRequest` to say the name is required? If you've found that inconvenient, we have good news:

```php
protected function setupCreateOperation()
{
    // instead of these (which still work, btw)
    $this->crud->setValidation(TagRequest::class);
    $this->crud->setValidation('App\Http\Requests\TagRequest');

    // you can now also do this
    $this->crud->setValidation([
        'name' => 'required|min:2',
    ]);

    // you even define custom validation messages, using the second parameter
    $rules = ['name' => 'required|min:2'];
    $messages = [
        'name.required' => 'You gotta give it a name, man.',
        'name.min' => 'You came up short. Try more than 2 characters.',
    ];
    $this->crud->setValidation($rules, $messages);
}
```

#### Use Model Events inside CrudControllers

You can now define closures to be run when Model Events get triggered, right inside your CrudController:

```php
public function setup() {
    // this will get run for all operations that trigger the "deleting" model event
    // by default, that's the Delete operation
    Monster::deleting(function ($entry) {
        // TODO: delete that entry's files from the disk
    });

    // this will get run on all operations that trigger the "saving" model event
    // by default, that's the Create and Update operations
    Monster::saving(function ($entry) {
        // TODO: change one value to another or something
    });
}

public function setupCreateOperation()
{
    // this will only get triggered inside the Create operation because
    // that's where we've defined it
    Monster::creating(function ($entry) {
        // TODO: something to the entry
    });
}
```

This is particularly useful if you've ever overriden your `create()` or `store()` methods in your CrudController. Now... you don't really have to do that any more, you can do stuff directy on the model, upon an event. But it's not limited to that use case, since it supports [all Eloquent events](https://laravel.com/docs/master/eloquent#events).

<hr>

### Fields

#### Define validation rules directly on fields

In addition to the array validation above... Backpack 4.2 has one more trick up its sleeve. You can now also define the validation rules and vlidation messages right when you define the field. No more `FormRequest`, no more defining the validation rules separately:

```php
protected function setupCreateOperation()
{
    $this->crud->addField([
        'name' => 'content',
        'label' => 'Content',
        'type' => 'ckeditor',
        'placeholder' => 'Your text here',
        'validationRules' => 'required|min:10',
        'validationMessages' => [
            'required' => 'You gotta write smth man.',
            'min' => 'More than 10 characters, bro. Wtf... You can do this!',
        ]
    ]);
    // This MUST be called AFTER the fields are defined, never before.
    $this->crud->setValidation();
}
```

In short, just call `CRUD::setValidation()` without passing anything and it will validate all the fields that have `validationRules` defined. But be careful - you have to call `setValidation()` _after_ you've defined your fields.


#### Define Model Events directly on fields

Previously, if you wanted to change the value of a field when retrieving it or storing in the database, Backpack encouraged you to use Accessors and Mutators. You can still do that, they're stock Laravel features and they'll work fine. But sometimes... you might not want to change the Model itself.

Now, you can define closures to run when standard Eloquent events are triggered, right on your fields:

```php
// using the ARRAY SYNTAX, define an array of events and closures
CRUD::addField([
    'name' => 'name',
    'events' => [
        'saving' => function ($entry) {
            $entry->name = strtoupper($entry->name);
        },
    ],
]);

// using the FLUENT SYNTAX, you can define the same array
CRUD::field('name')->events([
    'saving' => function ($entry) {
        $entry->name = strtoupper($entry->name);
    },
]);

// BUT you can also use the convenience method "on" to define just ONE event
CRUD::field('name')->on('saving', function ($entry) {
    $entry->name = strtoupper($entry->name);
});
```

It supports [all Eloquent events](https://laravel.com/docs/master/eloquent#events), but... the ones that make sense are just: ~~`retrieved`~~, `creating`, `created`, `updating`, `updated`, `saving`, `saved`, ~~`deleting`~~, ~~`deleted`~~, ~~`restoring`~~, ~~`restored`~~, and ~~`replicating`~~, because those are the events that actually take place inside the Create and Update operations.

We think you'll find this particularly useful for the `image`, `upload` and `upload_multiple` fields. Previously, they required you to create Mutators for them to work. Which meant you had to go to the Model to do that. But now... you can just do that in your CrudController:

```php
CRUD::field('attachment')->on('saving', function ($entry) {
    $value = request()->file('upload');

    if (!is_null($value)) {
        $entry->uploadFileToDisk($value, 'upload', 'public', 'uploads/monsters/upload_field');
    }
});
```

#### Simple validation for `repeatable` entries

Previously, in order to validate the contents of a subfield in `repeatable`, you had to write your own custom validation, which could take you quite some time. Now, you can just do `'testimonial.*.name' => 'required|min:5|max:256'` and use [Laravel's nested array validation](https://laravel.com/docs/8.x/validation#validating-nested-array-input).


<hr> 

### Widgets

#### New widget - `script`

You can now easily "push" a JS file to any page you want, straight from a Controller or view. This is particularly useful when you want something custom done inside a CRUD Operation. See [the `script` widget docs](/docs/{{version}}/base-widgets#script-1) for more details.

#### New widget - `style`

You can now easily "push" a CSS file to any page you want, straight from a Controller or view. This is particularly useful when you want something custom done inside a CRUD Operation. See [the `script` widget docs](/docs/{{version}}/base-widgets#style-1) for more details.

<hr>

### Developer Experience

#### Easily Debug AJAX Errors using the Error Frame Modal

Did your AJAX request error? No need to dig into your Chrome Devtools and read that awful output. When you have your APP_DEBUG to `true`, Backpack will show that error in a modal. This seems like a small thing, but it makes it _much_ easier to work with some complex Backpack features, like [ListOperation](https://backpackforlaravel.com/docs/4.1/crud-operation-list-entries), [InlineCreateOperation](https://backpackforlaravel.com/docs/4.1/crud-operation-inline-create), [filters](https://backpackforlaravel.com/docs/4.1/crud-filters), [`select2_from_ajax`](https://backpackforlaravel.com/docs/4.1/crud-fields#select2_from_ajax) field, [`relationship`](https://backpackforlaravel.com/docs/4.1/crud-fields#relationship-1) field. During development, if something goes wrong, Backpack will show it to you - it's that simple:

![image](https://user-images.githubusercontent.com/1838187/141500871-039ef53f-4207-4018-ac93-c41b547b62a5.png)

<hr>


<a name="changed"></a>
## Changed

#### Easily Include CSS and JS Assets Only Once Per Page

Backpack now ships with a new `@loadOnce()` directive, which makes sure that piece of JS/CSS/code is only loaded once per pageload. You can see [more info about it here](https://github.com/digitallyhappy/assets) (and why it's more than `@once`).

Of course, Backpack now uses this directive in all views that could get repeatedly loaded, so for example if you're loading in a single form a `select2`, a `select2_multiple` and a `select2_from_ajax`... you won't be getting the assets three times... but once.

#### Increased Default Security

Starting with v4.2, Backpack will throttle password request attempts. This will prevent malicious actors from using the "reset password" functionality of your admin panel to send unsolicited emails.

In addition, most column types now escape the output by default. This will better prevent your admins from XSS attacks, in case where you do not trust the information in the database to not contain javascript payloads.

<a name="removed"></a>
## Removed

- Support for Laravel 6;
- Support for Laravel 7;
- Support for PHP lower than 7.3 (since Laravel 8 does not support them);
- `simplemde` field, in favor of `easymde`;

---

In order to get all of the features above (and a few more hidden gems), please [follow the upgrade guide](/docs/{{version}}/upgrade-guide), to get from Backpack 4.1 to Backpack 4.2.
