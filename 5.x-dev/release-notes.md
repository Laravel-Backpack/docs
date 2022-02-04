# Release Notes

---

**Launch date:** (expected) Feb 9th, 2022

Backpack v5 is a major release, but NOT a huge upgrade. Think of it as a "4.2" release, that we've had to name "v5", because we've also changed our pricing. We have added new features and major improvements, but also kept the upgrading process as easy as possible. Plus...

> 🎉 &nbsp; 🎉 &nbsp; 🎉 &nbsp; **Backpack v5 is a FREE upgrade, for everybody who's purchased a Backpack v4 license after 9 Feb 2021.** Please read on to understand why.  &nbsp;  🎉 &nbsp; 🎉 &nbsp; 🎉


But yes, **Backpack v5 is a MAJOR new version**, with 8+ months of work put into it, and major improvements under-the-hood. It is the current and recommended version of Backpack and it's got so many cool new things... that we couldn't fit them all inside this page. Here's what Backpack v5 brings to the table and why you should upgrade from [Backpack 4.1](/docs/4.1) to v5:

<a name="new-pricing"></a>
## New Pricing

**The good news** - if you've purchased Backpack v4 after 9 Feb 2021:
- you have access to all v5.x.x updates for 12 months from your purchase date;
- if we release a v6 in those 12 months, you'll also have access to the v6 upgrade;
- even after those 12 months go buy, you'll still have access to those versions, for ever;
- you can [generate a token & password for v5 automatically, in your Backpack account](https://backpackforlaravel.com/user/tokens);

Basically, if you expected your updates to end soon... they don't. We've made sure that you receive at least 12 months of updates & upgrades, even though you purchased towards the end of Backpack's release cycle. We believe that's the _fair_ thing to do - give you guarantees that whenever you buy Backpack, you'll receive updates for a reasonable amount of time. It's one of the reasons we changed our pricing.

**The bad news**... it's a little more difficult to understand WHY we've done that. Please read on, it makes perfect sense, we promise.

### Backpack is now open-core

We try not to make a big fuss out of this, but this is HUGE - we consider it a _bug fix_. A _licensing bug fix_.

Previously, when you were buying a Backpack v4 license, you didn't know how many updates you're getting. Depending on _when_ you purchased, you could get 2 years of updates or 2 days. You'd then have to purchase the next version (using the limited-time discount we always give when launching). We consider that a stressful experience, both for you and us, so we've found a way to eliminate that stress too.

**Starting with v5, the features that were previously in "Backpack\CRUD" are now split between two packages:**
- **[Backpack\CRUD](https://github.com/laravel-backpack/crud) is the free & open-source core**, released under the [MIT License](https://github.com/Laravel-Backpack/CRUD/blob/master/LICENSE.md); <span class="badge badge-pill badge-success">FREE</span>
- **[Backpack\PRO](https://backpackforlaravel.com/products/pro) is a paid closed-source Backpack add-on**, released under our [EULA](https://backpackforlaravel.com/eula); <span class="badge badge-pill badge-info">PRO</span>

Since `backpack/pro` is released under the same terms of our `backpack/devtools` package:
- you get 12 months of updates _and upgrades_, guaranteed;
- after those 12 months you still have access to all versions you purchased;

We're very happy with this new pricing, because we believe it is _fair to everyone_:
- Open-source devs - you can now use Backpack for open-source projects;
- Devs with low/no budget - you can now use Backpack\CRUD: no strings attached, no application form;
- Professionals - when you buy `backpack/pro`, you get 12 months of updates and upgrades, _guaranteed_; no more worries about where we are in the release cycle; it's _always_ a good time to buy Backpack;
- Maintainers - only people who purchase `backpack/pro` can now use the most complex features we've built and maintain; we hope that'll mean less piracy _and_ fewer reported issues;

You can read more about this on [our pricing page](https://backpackforlaravel.com/pricing). We hope now you understand _why_ we're giving free access to v5 to everyone who purchased after 9 Feb 2021. We consider it _a bug fix_. We think it's only _fair_ for devs who have bought in 2021 to the same a dev that buys in 2022 gets (12 months of updates & upgrades).

**After this open-core split, we no longer offer non-commercial licenses for free. We've disabled our application form.** But... if you've previously received a free non-commercial Backpack v4 license, we've got your back. Reach out by email, we'll give you access to `backpack/pro` if your project is still non-commercial.


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

This is particularly useful if you've ever overridden your `create()` or `store()` methods in your CrudController. Now... you don't really have to do that any more, you can do stuff directly on the model, upon an event. But it's not limited to that use case, since it supports [all Eloquent events](https://laravel.com/docs/master/eloquent#events).

<hr>

### Fields

#### Define validation rules directly on fields

In addition to the array validation above... Backpack v5 has one more trick up its sleeve. You can now also define the validation rules and validation messages right when you define the field. No more `FormRequest`, no more defining the validation rules separately:

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


#### Better than ever `relationship` field, now with `subfields` too

We've spent _months_ building up and polishing the `relationship` field. It's now a one-stop-shop for all your needs, when creating/editing a relationship. Because it supports all common Eloquent relationships:
- ✅ `hasOne` (1-1) - shows subform if you define `subfields`
- ✅ `belongsTo` (n-1) - shows a select2 (single)
- ✅ `hasMany` (1-n) - shows a select2_multiple OR a subform if you define `subfields`
- ✅ `belongsToMany` (n-n) - shows a select2_multiple OR a subform if you define `subfields` for pivot extras
- ✅ `morphOne` (1-1) - shows a subform if you define `subfields`
- ✅ `morphMany` (1-n) - shows a select2_multiple OR a subform if you define `subfields`
- ✅ `morphToMany` (n-n) - shows a select2_multiple OR a subform if you define `subfields` for pivot extras

One HUGE addition we've made in v5 is... 🥁🥁🥁 subfields. For complex relationship, you just need to define `subfields` and the select2 will be turned into a subform, allowing your admin to define extra attributes. This will save you _hours_ or even _days_ when you have complex relationships. Because right in the current form, your admin can now easily:
- [create/update/delete a related 1-1 entry](/docs/{{version}}/crud-how-to#hasone-1-1-relationship) (`hasOne` and `morphOne`, eg. User and UserDetail)
- [create/update/delete related 1-n entries](/docs/{{version}}/crud-fields#manage-related-entries-in-the-same-form-create-update-delete) (`hasMany` and `morphMany`, eg. Invoice and InvoiceItem)
- [edit columns on the pivot table of n-n relationships](/docs/{{version}}/crud-fields#save-additional-data-to-pivot-table) (`belongsToMany` and `morphToMany`, eg. 'position' on company_user)

Check out the links above for more info, but in short, `subfields` will look and work just as you'd expect, like a `repeatable` field (because, under the hood, that's what it uses):

![](https://backpackforlaravel.com/uploads/docs-4-2/fields/repeatable_hasMany_entries.png)

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

Starting with v5, Backpack will throttle password request attempts. This will prevent malicious actors from using the "reset password" functionality of your admin panel to send unsolicited emails.

In addition, most column types now escape the output by default. This will better prevent your admins from XSS attacks, in case where you do not trust the information in the database to not contain JavaScript payloads.

<a name="removed"></a>
## Removed

- Support for Laravel 6;
- Support for Laravel 7;
- Support for PHP lower than 7.3 (since Laravel 8 does not support them);
- `simplemde` field, in favor of `easymde`;

---

In order to get all of the features above (and a few more hidden gems), please [follow the upgrade guide](/docs/{{version}}/upgrade-guide), to get from Backpack 4.1 to Backpack v5.
