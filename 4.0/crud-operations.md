# Operations

---

When creating a CRUD Panel, your ```EntityCrudController``` (where Entity = your model name) is extending ```CrudController```. Inside it, we've already provided the logic for [the most important operations](/#supported-operations), you just need to enable or configure them. Also, you can easily [add custom operations](/#creating-a-custom-operation).

<a name="supported-operations"></a>
## Standard Operations

Operations enabled by default:
- [ListEntries](/docs/{{version}}/crud-operation-list-entries) - allows the admin to see all entries for an Eloquent model, with pagination, search, filters;
- [Create](/docs/{{version}}/crud-operation-create) - allows the admin to add a new entry;
- [Update](/docs/{{version}}/crud-operation-update) - allows the admin to edit an existing entry;
- [Delete](/docs/{{version}}/crud-operation-delete) - allows the admin to remove and entry;

Operations disabled by default:
- [Show](/docs/{{version}}/crud-operation-show) - allows the admin to preview an entry;
- [Reorder](/docs/{{version}}/crud-operation-reorder) - allows the admin to reorder all entries of a model;
- [Revisions](/docs/{{version}}/crud-operation-revisions) - shows an audit log of all changes to an entry, and allows the admin to undo modifications;


<a name="operation-actions"></a>
### Operation Actions

Each Operation is actually _a trait_, which can be used on CrudControllers. This trait can contain one or more methods (or functions). Since Laravel calls each Controller method an _action_, that means each _Operation_ can have one or many _actions_. For example, we have the ```clone``` operation and two actions: ```clone``` and ```bulkClone```.

```php
trait CloneOperation
{
    public function clone($id)
    {
        // ...
    }

    public function bulkClone()
    {
        // ...
    }
}
```

An action can do something with AJAX and return true/false, it can return an AJAX, or it can return a view - or whatever else you can do inside a controller method.

You can check which action is currently being performed using the [standard Laravel Route API](https://laravel.com/api/5.7/Illuminate/Routing/Route.html):

- ```\Route::getCurrentRoute()->getAction()``` or ```$this->request->route()->getAction()```:
```
array:7 [▼
  "middleware" => array:2 [▼
    0 => "web"
    1 => "admin"
  ]
  "as" => "crud.monster.index"
  "uses" => "App\Http\Controllers\Admin\MonsterCrudController@index"
  "controller" => "App\Http\Controllers\Admin\MonsterCrudController@index"
  "namespace" => "App\Http\Controllers\Admin"
  "prefix" => "admin"
  "where" => []
]
```
- ```\Route::getCurrentRoute()->getActionName()``` or ```$this->request->route()->getActionName()```:
```
App\Http\Controllers\Admin\MonsterCrudController@index
```
- ```\Route::getCurrentRoute()->getActionMethod()``` or ```$this->request->route()->getActionMethod()```:
```
index
```

You can also use the shortcuts on the CrudPanel object:
```php
$this->crud->getActionMethod(); // returns the method on the controller that was called by the route; ex: create(), update(), edit() etc;
$this->crud->actionIs('create'); // checks if the controller method given is the one called by the route
```

<a name="titles-heading-and-subheadings"></a>
### Titles, Headings and Subheadings

For standard CRUD operations, each _action_ that shows an interface uses some texts to show the user what page, operation or action he is currently performing:
- **Title** - page title, shown in the browser's title bar;
- **Heading** - biggest heading on page;
- **Subheading** - short description of the current page, sits beside the heading;

![CRUD Operation Headings](https://backpackforlaravel.com/uploads/docs-3-5/operations/headings.png)

You can get and set the above using:
```php
// Getters
$this->crud->getTitle('create'); // get the Title for the create action
$this->crud->getHeading('create'); // get the Heading for the create action
$this->crud->getSubheading('create'); // get the Subheading for the create action

// Setters
$this->crud->setTitle('some string', 'create'); // set the Title for the create action
$this->crud->setHeading('some string', 'create'); // set the Heading for the create action
$this->crud->setSubheading('some string', 'create'); // set the Subheading for the create action
```

There methods are usually useful inside actions, not in ```setup()```. Since action methods are called _after_ ```setup()```, any call to these getters and setters in ```setup()``` would get overwritten by the call in the action.

<a name="access-to-operations"></a>
### Handling Access to Operations

Admins are allowed to do an operation or not using a very simple system: ```$crud``` holds an array with all operations they can perform. By default it will look like this:

```php
public $access = [
    'list', 
    'create', 
    'update', 
    'delete'
    /* 'revisions', reorder', 'show', 'clone' */
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


<a name="getting-and-setting-operation"></a>
### Getting and Setting an Operation Name

Inside a CrudController method all default operations use ```$this->crud->setOperation('show')``` to define which operation is currently being performed. So you can do ```$crud->getOperation()``` inside your views and do things according to this.

When you create custom operation, it's recommended that you also do ```$this->crud->setOperation('show')``` in each custom method, so that have the ability to check later on.

<a name="adding-methods-to-the-crud-panel-object"></a>
### Adding Methods to the CrudPanel Object

Every time you call ```$this->crud```, you're referring to a ```CrudPanel``` object, where we store all information about the current CRUD and perform all computation.

Starting with CRUD 3.5 you can add static methods to this ```CrudPanel``` object with ```$this->crud->macro()```, because the object is [macroable](https://unnikked.ga/understanding-the-laravel-macroable-trait-dab051f09172). So you can do:

```php
class MonsterCrudController extends CrudController 
{
   public function setup() 
   {
       $this->crud->macro('doStuff', function($something) {
            echo '<pre>'; var_dump($something); echo '</pre>';
            dd($this);
        });
       $this->crud->macro('getColumnsInTheFormatIWant', function() {
            $columns = $this->columns;
            // ... do something to $columns;
            return $columns;
        });

       // bla-bla-bla the actual setup code
   }
   public function sendEmail() 
   {
      // ...
      $this->crud->doStuff();
      dd($this->crud->getColumnsInTheFormatIWant());
      // ...
   }
   public function markPending() 
   {
      // ...
      $this->crud->doStuff();
      dd($this->crud->getColumnsInTheFormatIWant());
      // ...
   }
}
```

So if you define a custom operation that needs some static methods, you can add them. You can also use the ```$this->crud->settings``` object, to store various settings. Use it as an associative array, with the operation as key:

```php
$this->crud->settings['moderate']['show_title'] = false;
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

Let's say we have a ```UserCrudController``` and we want to create a simple ```Clone``` operation, which would create another entry with the same info. So very similar to ```Delete```. What we need to do is:

1. Create a route for this operation - we can add it anywhere, but it's recommended we keep all admin routes within ```routes/backpack/custom.php```:

```php
Route::post('user/{id}/clone', 'UserCrudController@clone');
```

2. Add the method inside ```UserCrudController```:

```php
public function clone($id) 
{
    $this->crud->hasAccessOrFail('create');
    $this->crud->setOperation('Clone');

    $clonedEntry = $this->crud->model->findOrFail($id)->replicate();

    return (string) $clonedEntry->push();
}
```

3. Create a button for this method. Since our operation is similar to "Delete", lets start from that one and customize what we need. The button should clone the entry using an AJAX call. No need to load another page for an operation this simple. We'll create a ```resources\views\vendor\backpack\crud\buttons\clone.blade.php``` file:

```php
@if ($crud->hasAccess('create'))
	<a href="javascript:void(0)" onclick="cloneEntry(this)" data-route="{{ url($crud->route.'/'.$entry->getKey().'/clone') }}" class="btn btn-xs btn-default" data-button-type="clone"><i class="fa fa-clone"></i> Clone</a>
@endif

<script>
	if (typeof cloneEntry != 'function') {
	  $("[data-button-type=clone]").unbind('click');

	  function cloneEntry(button) {
	      // ask for confirmation before deleting an item
	      // e.preventDefault();
	      var button = $(button);
	      var route = button.attr('data-route');

          $.ajax({
              url: route,
              type: 'POST',
              success: function(result) {
                  // Show an alert with the result
                  new PNotify({
                      title: "Entry cloned",
                      text: "A new entry has been added, with the same information as this one.",
                      type: "success"
                  });

                  // Hide the modal, if any
                  $('.modal').modal('hide');

                  crud.table.ajax.reload();
              },
              error: function(result) {
                  // Show an alert with the result
                  new PNotify({
                      title: "Cloning failed",
                      text: "The new entry could not be created. Please try again.",
                      type: "warning"
                  });
              }
          });
      }
	}

	// make it so that the function above is run after each DataTable draw event
	// crud.addFunctionToDataTablesDrawEventQueue('cloneEntry');
</script>
```

4. We can now actually add this button to our ```UserCrudController::setup()```:

```php
$this->crud->addButtonFromView('line', 'clone', 'clone', 'beginning');
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
    $this->crud->setOperation('Moderate');

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

<a name="creating-a-new-operation-with-bulk-action"></a>
#### Creating a New Operation With a Bulk Action (No Interface)

Say we want to create a ```Clone``` button which clones multiple entries at the same time. So very similar to our ```Bulk Delete```. What we need to do is:

1. ```$this->crud->enableBulkActions()``` to make the checkboxes show up;

2. Create a new button and add it to our buttom stack:

```html
@if ($crud->hasAccess('create') && $crud->bulk_actions)
  <a href="javascript:void(0)" onclick="bulkCloneEntries(this)" class="btn btn-default bulk-button"><i class="fa fa-clone"></i> Clone</a>
@endif

@push('after_scripts')
<script>
  if (typeof bulkCloneEntries != 'function') {
    function bulkCloneEntries(button) {

        if (typeof crud.checkedItems === 'undefined' || crud.checkedItems.length == 0)
        {
          new PNotify({
                title: "{{ trans('backpack::crud.bulk_no_entries_selected_title') }}",
                text: "{{ trans('backpack::crud.bulk_no_entries_selected_message') }}",
                type: "warning"
            });

          return;
        }

        var message = "Are you sure you want to clone these :number entries?";
        message = message.replace(":number", crud.checkedItems.length);

        // show confirm message
        if (confirm(message) == true) {
            var ajax_calls = [];
            var clone_route = "{{ url($crud->route) }}/bulk-clone";

        // submit an AJAX delete call
        $.ajax({
          url: clone_route,
          type: 'POST',
          data: { entries: crud.checkedItems },
          success: function(result) {
            // Show an alert with the result
            new PNotify({
                title: "Entries cloned",
                text: crud.checkedItems.length+" new entries have been added.",
                type: "success"
            });

            crud.checkedItems = [];
            crud.table.ajax.reload();
          },
          error: function(result) {
            // Show an alert with the result
            new PNotify({
                title: "Cloning failed",
                text: "One or more entries could not be created. Please try again.",
                type: "warning"
            });
          }
        });
        }
      }
  }
</script>
@endpush
```

3. In our ```setup()``` method, add this button to the bottom stack:

```php
$this->crud->addButtonFromView('bottom', 'bulk_clone', 'bulk_clone', 'end');
```

4. Create a method in your EntityCrudController (or in a trait, if you want to re-use it for multiple CRUDs):

```php
    public function bulkClone()
    {
        $this->crud->hasAccessOrFail('create');

        $entries = $this->request->input('entries');
        $clonedEntries = [];

        foreach ($entries as $key => $id) {
            if ($entry = $this->crud->model->find($id)) {
                $clonedEntries[] = $entry->replicate()->push();
            }
        }

        return $clonedEntries;
    }
```

5. Add a route to point to this new method:

```php
CRUD::resource('monster', 'MonsterCrudController')->with(function() {
  Route::post('monster/bulk-clone', 'MonsterCrudController@bulkClone');
});
```

Now there's a Clone button on our bottom stack, that works as expected for multiple entries.

The button makes one call for all entries, and only triggers one notification. If you would rather make a call for each entry, you can use something like below:

```html
@if ($crud->hasAccess('create') && $crud->bulk_actions)
  <a href="javascript:void(0)" onclick="bulkCloneEntries(this)" class="btn btn-default"><i class="fa fa-clone"></i> Clone</a>
@endif

@push('after_scripts')
<script>
  if (typeof bulkCloneEntries != 'function') {
    function bulkCloneEntries(button) {

        if (typeof crud.checkedItems === 'undefined' || crud.checkedItems.length == 0)
        {
          new PNotify({
                title: "{{ trans('backpack::crud.bulk_no_entries_selected_title') }}",
                text: "{{ trans('backpack::crud.bulk_no_entries_selected_message') }}",
                type: "warning"
            });

          return;
        }

        var message = "Are you sure you want to clone these :number entries?";
        message = message.replace(":number", crud.checkedItems.length);

        // show confirm message
        if (confirm(message) == true) {
            var ajax_calls = [];

            // for each crud.checkedItems
            crud.checkedItems.forEach(function(item) {
              var clone_route = "{{ url($crud->route) }}/"+item+"/clone";

              // submit an AJAX delete call
              ajax_calls.push($.ajax({
                  url: clone_route,
                  type: 'POST',
                  success: function(result) {
                      // Show an alert with the result
                      new PNotify({
                          title: "Entry cloned",
                          text: "A new entry has been added, with the same information as this one.",
                          type: "success"
                      });
                  },
                  error: function(result) {
                      // Show an alert with the result
                      new PNotify({
                          title: "Cloning failed",
                          text: "The new entry could not be created. Please try again.",
                          type: "warning"
                      });
                  }
              }));

          });

          $.when.apply(this, ajax_calls).then(function ( ajax_calls ) {
              crud.checkedItems = [];
              crud.table.ajax.reload();
        });
        }
      }
  }
</script>
@endpush
```