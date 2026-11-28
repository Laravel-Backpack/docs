# Dataform Modal <span class="badge badge-pill badge-info">PRO</span>

--

<a name="about"></a>
## About

`Dataform Modal` lets you show a Backpack form inside a modal dialog so admins can create or edit entries without leaving the current page.
It is built on top of Backpack CRUD and the Dataform component, and it requires the paid `backpack/pro` add-on to use.

> IMPORTANT: This is a **PAID** package. You can get access to it by purchasing [our Everything bundle](https://backpackforlaravel.com/pricing) or by [buying separate access to dataform-modal here](https://backpackforlaravel.com/products/dataform-modal).

![Backpack v7 Dataform Modal component](https://backpackforlaravel.com/uploads/v7/dataform_component.jpg)

<a name="requirements"></a>
## Requirements

- [Backpack\CRUD](https://github.com/Laravel-Backpack/CRUD) ^7.0
- [Backpack\PRO](https://backpackforlaravel.com/products/pro-for-unlimited-projects)

<a name="installation"></a>
## Installation

Install and configure the package as part of your Backpack setup according to your license.

Once installed, make sure your CrudController uses the modal-capable operations:

```php
use \Backpack\DataformModal\Http\Controllers\Operations\CreateInModalOperation;
use \Backpack\DataformModal\Http\Controllers\Operations\UpdateInModalOperation;
```

You can keep `CreateOperation` alongside `CreateInModalOperation` if you want both a regular create route and modal form support.

<a name="usage"></a>
## Usage

Use the `dataform-modal` component in your view and pass it a configured CrudController:

```html
<x-bp-dataform-modal controller="\App\Http\Controllers\Admin\InvoiceCrudController" />
```

This renders a modal form that uses the controller’s setup for the create or update operation.

<a name="configuration"></a>
## Configuration

### Set the form operation

By default, the modal will use the controller’s configured create operation. To override it, pass the operation name:

```html
<x-bp-dataform-modal controller="\App\Http\Controllers\Admin\InvoiceCrudController" form-operation="updateInModal" />
```

### Provide an entry for update mode

When using update mode, pass the model entry:

```html
<x-bp-dataform-modal
    controller="\App\Http\Controllers\Admin\InvoiceCrudController"
    :entry="\App\Models\Invoice::find(1)"
/>
```

### Customize the CRUD setup

Use the `:setup` callback to customize the operation after it has been configured:

```html
<x-bp-dataform-modal
    controller="\App\Http\Controllers\Admin\InvoiceCrudController"
    :setup="function($crud, $parent) {
        $crud->removeColumn('notes');
    }"
/>
```

### Replace the default save actions

You can replace the modal’s default save buttons with custom save actions:

```php
@php
    use App\Backpack\Crud\SaveActions\SaveAndApprove;
    use Backpack\CRUD\app\Library\CrudPanel\SaveActions\SaveAndBack;
@endphp

<x-bp-dataform-modal
    controller="\App\Http\Controllers\Admin\InvoiceCrudController"
    :save-actions="[
        new SaveAndApprove,
        SaveAndBack::class,
    ]"
/>
```

<a name="advanced"></a>
## Advanced example

```html
<x-bp-dataform-modal
    controller="\App\Http\Controllers\Admin\InvoiceCrudController"
    name="invoice_form"
    form-operation="updateInModal"
    :entry="\App\Models\Invoice::find(1)"
    :setup="function($crud, $parent) {
        $crud->removeColumn('notes');
    }"
/>
```

<a name="notes"></a>
## Notes

- This component requires a CrudController that is already set up for the entity you want to edit.
- `CreateInModalOperation` and/or `UpdateInModalOperation` must be available on the controller.
- The default create/update setup is reused inside the modal, so your controller configuration is still the source of truth.

<a name="warning"></a>
## Warning

The jQuery version of `date_picker` may not work correctly inside the modal. Use an alternative date input field if you need date selection in this context.
