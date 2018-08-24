# Operations

---

When creating a CRUD Panel, your ```EntityCrudController``` (where Entity = your model name) is extending ```CrudController```. Inside it, we've already provided the logic for [the most important operations](/#supported-operations), you just need to enable or configure them. Also, you can easily [add custom operations](/#creating-a-custom-operation).

<a name="supported-operations"></a>
## Supported Operations

Operations enabled by default:
- [ListEntries](/docs/{{version}}/crud-operation-list-entries) - allows the admin to see all entries for an Eloquent model, with pagination, search, filters;
- [Create](/docs/{{version}}/crud-operation-create) - allows the admin to add a new entry;
- [Update](/docs/{{version}}/crud-operation-update) - allows the admin to edit an existing entry;
- [Delete](/docs/{{version}}/crud-operation-delete) - allows the admin to remove and entry;

Operations disabled by default:
- [Show](/docs/{{version}}/crud-operation-show) - allows the admin to preview an entry;
- [Reorder](/docs/{{version}}/crud-operation-reorder) - allows the admin to reorder all entries of a model;
- [Revisions](/docs/{{version}}/crud-operation-revisions) - shows an audit log of all changes to an entry, and allows the admin to undo modifications;

<a name="access-to-operations"></a>
## Handling Access to Operations

Admins are allowed to do an operation or not using a very simple system: ```$crud``` holds an array with all operations they can perform. By default it will look like this:

```php
public $access = [
    'list', 
    'create', 
    'update', 
    'delete'
    /* 'revisions', reorder', 'show', 'details_row' */
];
```

You can easily add or remove elements to this access array in your ```setup()``` method, or your custom methods, using:
```php
$this->crud->allowAccess('operation');
$this->crud->allowAccess(['list', 'update', 'delete']);
$this->crud->denyAccess('operation');
$this->crud->denyAccess(['update', 'create', 'delete']);

$this->crud->hasAccess('operation'); // returns true/false
$this->crud->hasAccessOrFail('create'); // throws 403 error
$this->crud->hasAccessToAll(['create', 'update']); // returns true/false
$this->crud->hasAccessToAny(['create', 'update']); // returns true/false
```

<a name="creating-a-custom-operation"></a>
## Creating a Custom Operation

Thanks to [Backpack's simple architecture](/docs/{{version}}/crud-basics#architecture), each CRUD panel uses a controller and a route, that are placed inside your project. That means you hold the keys to how this controller works.

To add an operation to an ```EntityCrudController```, you can just:
- create a new route in ```routes/backpack/custom.php``` that points to a new method in that controller;
- create that method inside your ```EntityCrudController```;
- [add a new button for this operation to the ListEntries view](/docs/{{version}}/crud-buttons#creating-a-custom-button);

Take a look at the examples below for a better picture and code examples.

<a name="access-to-custom-operations"></a>
### Access to Custom Operations

Since you're creating a new operation, in terms of restricting access you can:
1. allow access to this new operation depending on access to a default operation (usually if the admin can ```update```, he's ok to perform custom operations);
2. customize access to this particular operation, by just using a different key than the default ones; for example, you can allow access by using ```$this->crud->allowAccess('moderate')``` in your ```setup()``` method, then check for access to that operation using ```$this->crud->hasAccess('moderate')```;

<a name="examples"></a>
### Examples

<a name="creatig-a-new-operation-with-no-interface"></a>
#### Creating a New Operation With No Interface

Let's say we have a ```UserCrudController``` and we want to create a simple ```Ban``` operation, which would set ```banned``` to ```true``` for a user. So very similar to ```Delete```. What we need to do is:

1. Create a route for this operation - we can add it anywhere, but it's recommended we keep all admin routes within ```routes/backpack/custom.php```:

```php
Route::post('user/{id}/ban', 'UserCrudController@ban');
```

2. Add the method inside ```UserCrudController```:

```php
public function ban($id) 
{
    $this->crud->hasAccessOrFail('update');

    $entry = $this->crud->getEntry($id);

    if ($entry && !$entry->banned) {
        $entry->banned = true;
        return (string)$entry->save();
    }

    return 'false';
}
```

3. Create a button for this method. Since our operation is similar to "Delete", lets start from that one and customize what we need. The button should ban the entry using an AJAX call. No need to load another page for an operation this simple. We'll create a ```resources\views\vendor\backpack\crud\buttons\ban.blade.php``` file:

```php
@if ($crud->hasAccess('update'))
    <a href="javascript:void(0)" onclick="banEntry(this)" data-route="{{ url($crud->route.'/'.$entry->getKey()) }}" class="btn btn-xs btn-default" data-button-type="ban"><i class="fa fa-ban"></i> Ban</a>
@endif

<script>
    if (typeof banEntry != 'function') {
      $("[data-button-type=ban]").unbind('click');

      function banEntry(button) {
          // ask for confirmation before deleting an item
          // e.preventDefault();
          var button = $(button);
          var route = button.attr('data-route');
          var row = $("#crudTable a[data-route='"+route+"']").closest('tr');

          if (confirm("Are you sure you want to ban this entry?") == true) {
              $.ajax({
                  url: route,
                  type: 'POST',
                  success: function(result) {
                      // Show an alert with the result
                      new PNotify({
                          title: "Banned",
                          text: "This entry has been successfully banned.",
                          type: "success"
                      });

                      // Hide the modal, if any
                      $('.modal').modal('hide');
                  },
                  error: function(result) {
                      // Show an alert with the result
                      new PNotify({
                          title: "Did not ban.",
                          text: "This entry could not be banned.",
                          type: "warning"
                      });
                  }
              });
          } else {
              // Show an alert telling the user we don't know what went wrong
              new PNotify({
                  title: "Couldn't ban.",
                  text: "So sorry, don't know what went wrong. Please try again.",
                  type: "info"
              });
          }
      }
    }

    // make it so that the function above is run after each DataTable draw event
    // crud.addFunctionToDataTablesDrawEventQueue('banEntry');
</script>
```

4. We can now actually add this button to our ```UserCrudController::setup()```:

```php
$this->crud->addButtonFromView('line', 'ban', 'ban', 'beginning');
```

>Of course, **if you plan to re-use this operation on another EntityCrudController**, it's a good idea to isolate the method inside a trait, then use that trait on each EntityCrudController where you want the operation to work.

<a name="creating-a-new-operation-with-an-interface"></a>
#### Creating a New Operation With An Interface

Let's say we have a ```UserCrudController``` and we want to create a simple ```Moderate``` operation, where we show a form where the admin can add his observations and what not. In this respect, it should be similar to ```Update``` - the button should lead to a separate form, then that form will probably have a Save button. So when creating the methods, we should look at ```CrudController::edit()``` and ```CrudController::updateCrud()``` for working examples.

What we need to do is:

1. Create a route for this operation - we can add it anywhere, but it's recommended we keep all admin routes within ```routes/backpack/custom.php```:

```php
Route::get('user/{id}/moderate', 'UserCrudController@getModerateForm');
Route::post('user/{id}/moderate', 'UserCrudController@postModerateForm');
```

2. Add the methods inside ```UserCrudController```:

```php
public function getModerateForm($id) 
{
    $this->crud->hasAccessOrFail('update');

    // get the info for that entry
    $this->data['entry'] = $this->crud->getEntry($id);
    $this->data['crud'] = $this->crud;
    $this->data['title'] = 'Moderate '.$this->crud->entity_name;

    return view('vendor.backpack.crud.moderate', $this->data);
}

public function postModerateForm(Request $request = null)
{
    $this->crud->hasAccessOrFail('update');

    // TODO: do whatever logic you need here
    // ...
    // You can use 
    // - $this->crud
    // - $this->crud->getEntry($id)
    // - $request
    // ...

    // show a success message
    \Alert::success('Moderation saved for this entry.')->flash();

    return \Redirect::to($this->crud->route);
}
```

3. Create the ```/resources/views/vendor/backpack/crud/moderate.php``` blade file, which shows the moderate form and what not. Best to start from the ```edit.blade.php``` file and customize:

```html
@extends('backpack::layout')

@section('header')
    <section class="content-header">
      <h1>
        <span class="text-capitalize">{{ $crud->entity_name_plural }}</span>
        <small>{{ trans('backpack::crud.edit').' '.$crud->entity_name }}.</small>
      </h1>
      <ol class="breadcrumb">
        <li><a href="{{ url(config('backpack.base.route_prefix'),'dashboard') }}">{{ trans('backpack::crud.admin') }}</a></li>
        <li><a href="{{ url($crud->route) }}" class="text-capitalize">{{ $crud->entity_name_plural }}</a></li>
        <li class="active">Moderate</li>
      </ol>
    </section>
@endsection

@section('content')
<div class="row">
    <div class="col-md-8 col-md-offset-2">
        <!-- Default box -->
        @if ($crud->hasAccess('list'))
            <a href="{{ url($crud->route) }}"><i class="fa fa-angle-double-left"></i> {{ trans('backpack::crud.back_to_all') }} <span>{{ $crud->entity_name_plural }}</span></a><br><br>
        @endif

          <div class="box">
            <div class="box-header with-border">
                <h3 class="box-title">Moderate</h3>
            </div>
            <div class="box-body row display-flex-wrap" style="display: flex;flex-wrap: wrap;">
              Something in the box body
            </div><!-- /.box-body -->

            <div class="box-footer">
                Something in the box footer
            </div><!-- /.box-footer-->
          </div><!-- /.box -->
          </form>
    </div>
</div>
@endsection

```

4. Create a button for this operation. Since our operation is similar to "Update", lets start from that one and customize what we need. The button should just take the admin to the route that shows the Moderate form. Nothing fancy. We'll create a ```resources\views\vendor\backpack\crud\buttons\moderate.blade.php``` file:

```php
@if ($crud->hasAccess('update'))
    <a href="{{ url($crud->route.'/'.$entry->getKey().'/moderate') }}" class="btn btn-xs btn-default"><i class="fa fa-list"></i> Moderate</a>
@endif
```

4. We can now actually add this button to our ```UserCrudController::setup()```:

```php
$this->crud->addButtonFromView('line', 'moderate', 'moderate', 'beginning');
```

>Of course, **if you plan to re-use this operation on another EntityCrudController**, it's a good idea to isolate the method inside a trait, then use that trait on each EntityCrudController where you want the operation to work.