# Blade Components

---

<a name="about"></a>
## About

[Blade components](https://laravel.com/docs/blade#components) are a quick way for you to output a bit of HTML... that can then be customized by each Backpack theme. It's a clean way of echoing theme-agnostic components, because for each Component that Backpack provides, the theme itself can customize and make it pretty in their own way.

<a name="how-to-use-components"></a>
### How to Use

Anywhere in your blade files, you can use the Blade components we have. But most likely you'll be using them in your `resources/views/vendor/backpack/ui/inc/menu_items.blade.php`, because all our components currently are concerned with outputting menu items in a theme-agnostic way.


<a name="mandatory-attributes"></a>
### Mandatory Attributes

There are no mandatory attributes.

<a name="optional-attributes"></a>
### Optional Attributes

All components also allow you to specify custom attributes. When you specify a custom attribute, that attribute will be placed on the most likely element of that component. In most cases, that is the anchor element. For example:

```php
<x-backpack::menu-item title="Tags" icon="la la-tag" :link="backpack_url('tags')" target="_blank" />
```

Even though the 'target' attribute doesn't _technically_ exist in the component, that attribute will be placed on that component's `a` element.


<a name="available-components"></a>
## Available Components

<a name="menu-item"></a>
### Menu Item

Shows a menu item, with the title and link you specify:

```php
<x-backpack::menu-item title="Tags" icon="la la-tag" :link="backpack_url('tags')" />
```

Note that you can further customize this using custom attributes. If you define a `target` on it, that will be passed down to the `a` element.

<hr>

<a name="datatable"></a>
### DataTable

Show a datatable _anywhere you want_, so the admin to easily list, filter, search and perform other operations on entries of an Eloquent model. The datatable component is a extension of a CrudController - so a CRUD for that entity needs to be already set up, and passed to this component as a parameter:

```html
<bp-datatable controller="\App\Http\Controllers\InvoiceCrudController" />
```

The datatable will pick up everything that in your `setupListOperation()`. You can then further add/remove/configure functionality using the configuration closure:

```html
<bp-datatable 
    controller="\App\Http\Controllers\InvoiceCrudController" 
    <!-- optional -->
    :setup="function($crud, $parent) { if ($parent) { $crud->addClause('where', 'customer_id', $parent->id); } }"
    name="invoices"
 />
```

<hr>


<a name="menu-separator"></a>
### Menu Separator

Shows a menu separator, with the title you specify:

```php
<x-backpack::menu-separator title="Some text for separation" />
```

Note that you can further customize this using custom attributes. If you define a `class` on it, that will be passed down to the `li` element.

<hr>


<a name="menu-dropdown-and-menu-dropdown-item"></a>
### Menu Dropdown & Menu Dropdown Item

To show a dropdown menu, with elements, you can use the `menu-dropdown` and `menu-dropdown-item` components:

```php
<x-backpack::menu-dropdown title="Authentication" icon="la la-group">
    <x-backpack::menu-dropdown-item title="Users" icon="la la-user" :link="backpack_url('user')" />
    <x-backpack::menu-dropdown-item title="Roles" icon="la la-group" :link="backpack_url('role')" />
    <x-backpack::menu-dropdown-item title="Permissions" icon="la la-key" :link="backpack_url('permission')" />
</x-backpack::menu-dropdown>
```

Notes: 
- on `menu-dropdown` you can define `nested="true"` to flag that dropdown as nested (aka. having a parent); so you can have dropdown in dropdown in dropdown; 
- on both components, you can also define custom attributes; eg. if you define a `target` on one, that will be passed down to the `a` element;

<hr>

<a name="overwriting-default-components"></a>
## Overriding Default Components

You can override a component by placing a file with the same name in your ```resources\views\vendor\backpack\ui\components``` directory. When a file is there, Backpack will pick that one up, instead of the one in the package.

>**Avoid doing this.** When you're overwriting a component, you're forfeiting any future updates for that component. We can't push updates to a file that you're no longer using.

<a name="creating-a-custom-component"></a>
## Creating a Custom Component

We do not support creating custom components within the `backpack` namespace. If you want to create a custom component, please create one in the `app` namespace. The [Laravel docs on Blade Components](https://laravel.com/docs/blade#components) will teach you everything you need to know.
