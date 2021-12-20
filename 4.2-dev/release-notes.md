# Release Notes

---

**Launch date:** (expected) end of Dec, 2021

Anybody with a 4.0 or 4.1 license can install and use 4.2, but in order to get the new features in a project that currently runs 4.1, you need to [follow the upgrade guide](/docs/{{version}}/upgrade-guide). Don't get fooled by the fact that we kept the 4.x.x prefix - that's just to show that **Backpack 4.2 is a FREE upgrade from 4.1**. But **Backpack 4.2 is a MAJOR new version**, with 12 months of work put into it, and major improvements under-the-hood. It is the current and recommended version of Backpack and it's got so many cool new things that we couldn't fit them all inside this page.

But what Backpack 4.2 brings to the table and why you should upgrade from [Backpack 4.1](/docs/4.1) to 4.2:


<a name="added"></a>
## Added

### Operations

#### `warnBeforeLeaving` inside Create & Update

For large forms, it's often smart to ask the user if they really want to lose their form changes, before they leave the page. That's exactly what `CRUD::set('warnBeforeLeaving', true)` will do, just call it in your `setupCreateOperation()` or `setupUpdateOperation()`.

<hr>

### Fields

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
