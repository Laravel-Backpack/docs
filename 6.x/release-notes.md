# Release Notes

---

**Launch date:** <span class="badge badge-danger">PUBLIC BETA</span> on May 31st, 2023

Backpack v6 is a major release, with major changes. But don't worry, we've kept MOST changes backwards-compatible. And even for the things that are hugely breaking, we have provided 3 different upgrade paths, so that you can _easily_ use the latest version of Backpack, even if you've _heavily_ customized it. But first, a reminder:

> **If you've purchased a Backpack add-on after 1st of July 2022  (eg. PRO, DevTools), you get free access to the version that supports Backpack v6.** All purchases get you 12 months of updates _and_ upgrades.

Here are the BIG things Backpack v6 brings to the table and why you should upgrade from [Backpack v5](/docs/5.x) to v6... in both video and text:

![Backpack v6 new features and changes](https://placehold.co/1280x900?text=TODO+video+casual+screencast+5-10+min)


<a name="added"></a>
## Added

### User Interface

Backpack v6 brings a MAJOR overhaul of the user interface. We've:
- upgraded from Bootstrap v4 to Bootstrap v5
- invented a whole new way to use CSS and JS assets in Laravel projects
- added the ability to easily create new themes for Backpack
- created 3 themes: Tabler, CoreUIv4 and CoreUIv2

Read on to find out more.

#### Themes

Done, TODO: describe; mention Tabler, CoreUI v4, CoreUI v2 and ability to easily create new themes

Starting with Backpack v6, themes are first-class citizens:
- we have an official, documented way to create Backpack themes;
- our 1st party themes use that system themselves; even the default Backpack theme can now be uninstalled completely, it's an add-on;
- it's reasonably fast to create new themes (5 hours max for any Boostrap admin panel template);
- we have developed 3 themes for you: [Tabler](https://github.com/Laravel-Backpack/theme-tabler), [CoreUI v4](https://github.com/Laravel-Backpack/theme-coreuiv4) and [CoreUI v2](https://github.com/Laravel-Backpack/theme-coreuiv2);

##### Tabler Theme (new default)

We've been working with [Tabler](https://tabler.io/) for a long time, and it is _spectactular_. It's by far the best Bootstrap-based HTML template we've worked with: it's open-source, well-maintained, has a great team and a great community around it. And it has TONS of components baked-in, and tons of layouts too (horizontal, vertical etc.). It's by far the best open-source, Bootstrap-based admin template in 2023... which is why we've chosen it for the _default_ Backpack v6 theme.
There's lots and lots to love here:

![Backpack v6 Tabler theme - layouts horizontal vertical etc](https://user-images.githubusercontent.com/1032474/240274915-f45460a7-b876-432c-82c3-b0b3c60a39f2.png)

Sounds interesting? [Read more here](https://github.com/Laravel-Backpack/theme-tabler).

##### CoreUI v4 Theme (easy upgrade)

If you have lots of customizations... but would still like to upgrade to Booststrap v5, we've developed a theme for you too. You can use the CoreUI v4, which uses the latest version of Bootstrap and CoreUI, but without bringing too many breaking changes:

![Backpack v6 CoreUI v4 theme](https://user-images.githubusercontent.com/1032474/240274314-184d328e-0e6c-4d67-942b-4e4d4efd96c8.png)

Sounds interesting? [Read more here](https://github.com/Laravel-Backpack/theme-coreuiv4).

##### CoreUI v2 Theme (for maximum backwards compatibility)

If you've made TONS of customizations to your blade files... it might be too time-consuming for you to upgrade to Tabler and Bootstrap v5. It's sad... but we understand that use case. Which is why we've developed a Backpack v6 theme specifically for you, where... not a lot changes. We use the new asset-loading system but other than that... no changes. This theme should allow you to easily upgrade to Backpack v6 and keep you custom pages, changes etc. because we're prioritized backwards-compatibility over adding new features and modernizing:

![Backpack v6 CoreUI v2 theme](https://user-images.githubusercontent.com/1032474/240272550-456499a0-ef31-48a1-a985-1de3ff6107e5.png)

Sounds interesting? [Read more here](https://github.com/Laravel-Backpack/theme-coreuiv2).

#### Dark Mode (in Tabler theme)

You wanted it, you've got it! Starting v6, Backpack's default theme ([Tabler](https://github.com/Laravel-Backpack/theme-tabler)) has dark mode... out of the box! And not only that... we've spent a lot of time polishing our dark mode, to make sure it's beautiful, legible and usable. Take a look:

![Backpack v6 Tabler theme with dark mode](https://user-images.githubusercontent.com/1032474/240271611-b028aa6c-eb53-495a-b7bb-66b10880fce9.gif)

#### Layouts (in Tabler theme)

One regular need we've identified was needing to change the layout _slightly_. In Backpack v5 some customization was possible using classes, but now, in Backpack v6 and specifically in the Tabler theme, we've overhauled it, and we offer:
- the 9 most common layouts out-of-the-box (vertical, horizontal, horizontal overlap, right vertical etc.);
- an easy way for you to create your own layout, and have all Backpack pages use _your custom layout_;

![Backpack v6 Tabler theme now has layouts](https://user-images.githubusercontent.com/1032474/240273221-8d5f5d53-ead0-4242-b83c-7dbb56028085.gif)

Go head, check out the new layouts in [our online demo](http://v6demo.backpackforlaravel.com/).

#### Auth layouts (in Tabler theme)

One other customization we've often seen people make is changing the way the login/register screens look. To make that easier, in our new default theme, we've created a few alternative auth layouts. You can choose between `default`, `illustration` and `cover`... or create your own. Of course, all of them have dark mode too:

![Backpack v6 Tabler theme auth layouts](https://user-images.githubusercontent.com/1032474/240278320-df379c68-0d37-4f7a-a8e0-ec2b1175e67f.gif)

For details, check out the [Tabler theme docs](https://github.com/Laravel-Backpack/theme-tabler).

#### Basset - an innovative new way to load CSS and JS assets

We've never been happy with "traditional" ways of loading CSS and JS assets in Laravel (link tags, script tags), nor with using NPM. That's why we've invented a new way, which makes it dead-simple to load assets... either from your device... or from a CDN... but fixing all the privacy problems you might have. We've made that solution open-source, and we call this better assets solution... [Basset](https://github.com/Laravel-Backpack/basset). Now it's easy to:

```php
// just do
@basset('https://unpkg.com/vue@3/dist/vue.global.js')

// and the system will internalize it and output something like
<script src="https://your-app.com/bassets/vue@3/dist/vue.global.js"></script>
```

This means you don't have to compile or bundle assets. You don't have to publish assets. Backpack's core uses this system behind-the-hood, and it's made our CRUD repo go from 90.3 MB... down to 1.9 MB 🤯 Crazy, right?! And with the improvements in HTTP... most of the time the assets get loaded at the same time... so pageload times have improved considerably too!

![Backpack Basset for asset loading](https://user-images.githubusercontent.com/1032474/240285417-786c4cd4-d51c-49ba-a90a-8f1cade965f3.png)

Sounds interesting? [Read more about Basset](https://github.com/Laravel-Backpack/basset).

#### CSS hook classes

TODO before launch

<hr>

### CRUD

#### Spatie MediaLibrary Support

One popular way to add media (pictures, videos etc) to your Laravel models is to use [Spatie MediaLibrary](https://github.com/spatie/laravel-medialibrary). We're happy to announce that Backpack now has first-party support for this very popular Laravel package. In short, **all you need to do is to [install our add-on](https://github.com/laravel-backpack/medialibrary-uploaders) and call `withMedia()` on your Backpack fields and columns**. And that's it, files will get uploaded using MediaLibrary, fetched using MediaLibrary etc. Uploads just became dead-simple!

Interested? [Read more about it here](https://github.com/laravel-backpack/medialibrary-uploaders).

#### 23 New Column Types

We've hunkered down and created a column type... for every field type we have, both FREE and PAID. So yes, starting with Backpack v6... all fields now have corresponding column types. That makes it MUCH easier to use the equivalent of a field... in the List or Show operation.

Interested? [See all the column types we have, here](https://backpackforlaravel.com/docs/{{version}}/crud-columns).

#### New Field: Dropzone

Backpack v5 provided you with a few ways to upload files: `upload`, `upload_multiple`, `browse`, `browse_multiple`. They are perfectly fine for most use cases, but they had technical limitations, due to their dependencies (how HTML works or elFinder).

That's why we've created _one upload field to rule them all_... the `dropzone` field. This field was a difficult one to achieve, so big BIG thanks to Jorge and Pedro for working 3+ months at it. This new PRO field will allow your admins to drag&drop files to upload them... using AJAX. This means they can:
- upload bigger files
- upload more files than ever
- no longer "_wait for the form to load_"

And as a developer, it means you hava an upload field... that you can use as a subfield, in your `repeatable` and `relationship` fields 🤯 We are super-excited about this field, because it covers so much more ground than our previous upload fields. We think you'll love it too - in fact we think this will become your _default_ upload field. It has already become ours:

![Backpack v6 Dropzone field](https://placehold.co/1280x900?text=TODO+GIF+with+dropzone+field)

#### Uploaders - making upload fields easier than ever

Backpack v5 already made uploads pretty easy - by using model mutators or model events. With Backpack v6, uploads take another huge leap forward. You can now attach Uploaders to fields and columns. And those uploaders encapsulate all the logic needed to do the upload & retrieval. That way... we can provide you with some standard uploaders, that cover 90% of all use cases. To be brief, in order to have a field upload a file, the only thing you need to do is:

```php
CRUD::field('document')->type('upload')->hasFiles();
```

And that is all 🤯 That's crazy-easy, right? Of course, we have a bunch of configurations for the most common scenarios. And you can create your own Uploader class if you want. [Read more about Uploader classes here](/docs/{{version}}/crud-uploaders).

#### Custom form operations, now easy!

Another thing that was possible in Backpack v5, but not very easy, was to create a custom operation that included a custom form. You could do it... but you had to re-do a lot of the things we do in the Create & Update operations. No longer! Now we've changed the way Create & Update work, so that you can re-use the form they use. That means you can easily create a custom operation, that has Backpack fields, and has _nothing_ to do with Create or Update. For more info, see [the docs here](#).

TODO: feature + docs

#### Custom buttons, now one-line

Another thing that's ridiculously easy now... is for you custom operation to provide simple buttons. Previously you had to create a blade file just to hold that simple button (bleah). Now, it's as easy as:

```php
CRUD::addLineButton($name, $icon, $label, $position, $url, $access);
CRUD::addTopButton($name, $icon, $label, $position, $url, $access);
CRUD::addBulkButton($name, $icon, $label, $position, $url, $access);

// for example:
CRUD::addLineButton('refund', 'la la-cash-register', 'Refund', 'beginning', 'https://stripe.com/refund', true);
```

<a name="changed"></a>
## Changed

#### We've merged the fluent and array syntaxes

In 2018-2022, we had one way of adding fields & columns, the array syntax:
```php
CRUD::addField([
    'name'  => 'description',
    'label' => 'Article Description',
    'type'  => 'textarea',
]);
```

In 2022, we added a new (fluent) way of defining fields and columns:
```php
CRUD::field('description')->label('Article Description')->type('textarea');
```

In 2023, we're merging the two syntaxes. You can now use them together, first the array syntax then the fluent syntax:
```php
CRUD::field([
    'name'  => 'description',
    'label' => 'Article Description',
    'type'  => 'textarea',
])->label('Overriden Description');
```

This means you have all the benefits of the array syntax... and all the benefits of the fluent syntax (chaining), in one simple `CRUD::field()` call.

<a name="removed"></a>
## Removed

- Support for Laravel 8 and 9;
- Support for PHP lower than 8.1 (since Laravel 10 does not support them);
- `address_algolia` field, since their API got discontinued;

---

If you like what you see, please help us out - share what you like on social media or tell a friend. To get all of the features above (and a lot more), please [follow the upgrade guide](/docs/{{version}}/upgrade-guide).
