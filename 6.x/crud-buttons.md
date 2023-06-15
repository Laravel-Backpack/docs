# Buttons

---

<a name="about"></a>
## About

Buttons are used inside the ListEdit operation, to allow the admin to trigger other operations. Some point to entirely new routes (```create```, ```update```, ```show```), others perform the operation on the current page using AJAX (```delete```).

<a name="button-stacks"></a>
### Button Stacks

The ShowList operation has 3 places where buttons can be placed:
  - ```top``` (where the Add button is)
  - ```line``` (where the Edit and Delete buttons are)
  - ```bottom``` (after the table)

When adding a button to the stack, you can choose whether to insert it at the ```beginning``` or ```end``` of the stack by specifying that as a last parameter.

<a name="default-buttons"></a>
### Default Buttons

Backpack adds a few buttons by default: 
- ```create``` to the ```top``` stack;
- ```update``` and ```delete``` to the ```line``` stack;

Default buttons are invisible if an operation has been disabled. For example, you can: 
- hide the "delete" button using ```$this->crud->denyAccess('delete')```;
- show a "preview" button by using ```$this->crud->allowAccess('show')```;


<a name="buttons-api"></a>
### Buttons API

Here are a few things you can call in your EntityCrudController's ```setupListOperation()``` method, to manipulate buttons:

```php
// possible stacks: 'top', 'line', 'bottom';
// possible positions: 'beginning' and 'end'; defaults to 'beginning' for the 'line' stack, 'end' for the others;

// collection of all buttons
$this->crud->buttons();

// add a button; possible types are: view, model_function
$this->crud->addButton($stack, $name, $type, $content, $position);

// add a button whose HTML is returned by a method in the CRUD model
$this->crud->addButtonFromModelFunction($stack, $name, $model_function_name, $position);

// add a button whose HTML is in a view placed at resources\views\vendor\backpack\crud\buttons
$this->crud->addButtonFromView($stack, $name, $view, $position);

// remove a button
$this->crud->removeButton($name);

// remove a button for a certain stack
$this->crud->removeButtonFromStack($name, $stack);

// remove multiple buttons
$this->crud->removeButtons($names, $stack);

// remove all buttons
$this->crud->removeAllButtons();

// remove all buttons for a certain stack
$this->crud->removeAllButtonsFromStack($stack);

// order buttons in a stack, order is an array with the ordered names of the buttons
$this->crud->orderButtons($stack, $order);

// modify button, modifications are the attributes and their new values.
$this->crud->modifyButton($name, $modifications);

// Move the target button to the destination position, target and destion are the button names, where is 'before' or 'after'
$this->crud->moveButton($target, $where, $destination);
```

<a name="overwriting-a-default-button"></a>
### Overwriting a Default Button

Before showing any buttons, Backpack will check your ```resources\views\vendor\backpack\crud\buttons``` directory, to see if you've overwritten any default buttons. If it finds a blade file with the same name there as the default buttons, it will use your blade file, instead of the default.

That means **you can overwrite an existing button simply by creating a blade file with the same name inside this directory**.

<a name="creating-a-custom-button"></a>
### Creating a Custom Button

To create a custom button:
- run `php artisan backpack:button new-button-name` to create a new blade file in `resources\views\vendor\backpack\crud\buttons`
- add that button using the ```addButton()``` syntax above, in the EntityCrudControllers you want, inside the ```setupListOperation()``` method;

In this blade file, you can use:
- ```$entry``` - the database entry you're showing (only inside the ```line``` stack);
- ```$crud``` - the entire CrudPanel object;
- ```$button``` - the button you're currently showing;

Note: If you've opted to add a button from a model function (not a blade file), inside your model function you can use `$this` to get the current entry (so for example, you can do `$this->id`.


<a name="creating-a-quick-button"></a>
### Creating a Quick Button

Most of the times, the buttons you want to create aren't complex at all. They're just an `<a>` element, with a `href` and `class` that is show if the admin has access to that particular operation. That's why we've created the `quick.blade.php` button, that allows you to _quickly_ create a button, right from your Operation or CrudController. This covers most simple use cases:

```php
// by default, the quick button will figure out the Name and Label from the button name
$this->crud->button('email')->stack('line')->view('crud::buttons.quick');

// but you can easily customize Access, Name, Label, Icon in `metas`
// and even the attributes of the <a> element in meta's `wrapper`
$this->crud->button('email')->stack('line')->view('crud::buttons.quick')->meta([
    'access' => 'Email',
    'label' => 'Email',
    'icon' => 'la la-envelope',
    'wrapper' => [
        // 'element' => 'a',
        'href' => url('something'),
        'target' => '_blank',
        'title' => 'Create a new email to this user',
    ]
]);
```

<a name="examples"></a>
## Examples

<a name="adding-a-custom-button-with-a-blade-file"></a>
### Adding a Custom Button with a Blade File

Let's say we want to create a simple ```moderate.blade.php``` button. This button would just open a ```user/{id}/moderate/``` route, which would point to ```UserCrudController::moderate()```. The steps would be:

- Create the ```resources\views\vendor\backpack\crud\buttons\moderate.blade.php``` file:
```php
@if ($crud->hasAccess('update'))
  <a href="{{ url($crud->route.'/'.$entry->getKey().'/moderate') }} " class="btn btn-xs btn-default"><i class="la la-ban"></i> Moderate</a>
@endif
```
- Add the new route, next to ```UserCrudController```'s route (most likely inside ```routes/backpack/custom.php```):
```php
Route::get('user/{id}/moderate', 'UserCrudController@moderate');
```

- We can now add a ```moderate()``` method to our ```UserCrudController```, which would moderate the user, and redirect back.
```php
public function moderate() 
{
    // show a form that does something
}
```

- Now we can actually add this button to any of ```UserCrudController::setupListOperation()```:
```php
$this->crud->addButtonFromView('line', 'moderate', 'moderate', 'beginning');
```

<a name="adding-a-custom-button-without-a-blade-file"></a>
### Adding a Custom Button without a Blade File

Instead of creating a blade file for your button, you can use a function on your model to output the button's HTML.

In your ```ArticleCrudController::setupListOperation()```:
```php
// add a button whose HTML is returned by a method in the CRUD model
$this->crud->addButtonFromModelFunction('line', 'open_google', 'openGoogle', 'beginning');
```

In your ```Article``` model:

```php
public function openGoogle($crud = false)
{
    return '<a class="btn btn-sm btn-link" target="_blank" href="http://google.com?q='.urlencode($this->text).'" data-toggle="tooltip" title="Just a demo custom button."><i class="la la-search"></i> Google it</a>';
}
```


<a name="adding-a-custom-button-with-a-blade-file"></a>
### Adding a Custom Button with JavaScript to the "top" stack

Let's say we want to create an ```import.blade.php``` button. For simplicity, this button would just run an AJAX call which handles everything, and shows a status report to the user through notification bubbles. 

The "top" buttons are not bound to any certain entry, like buttons from the "list" stack. They can only do general things. And if they do general things, it's _generally_ recommended that you move their JavaScript to the bottom of the page. You can easily do that with ```@push('after_scripts')```, because the Backpack default layout has an ```after_scripts``` stack. This way, you can make sure your JavaScript is moved at the bottom of the page, after all other JavaScript has been loaded (jQuery, DataTables, etc). Check out the example below.

The steps would be:

- Create the ```resources\views\vendor\backpack\crud\buttons\import.blade.php``` file:

```php
@if ($crud->hasAccess('create'))
    <a href="javascript:void(0)" onclick="importTransaction(this)" data-route="{{ url($crud->route.'/import') }}" class="btn btn-sm btn-link" data-button-type="import">
<span class="ladda-label"><i class="la la-plus"></i> Import {{ $crud->entity_name }}</span>
</a>
@endif

@push('after_scripts')
<script>
    if (typeof importTransaction != 'function') {
      $("[data-button-type=import]").unbind('click');

      function importTransaction(button) {
          // ask for confirmation before deleting an item
          // e.preventDefault();
          var button = $(button);
          var route = button.attr('data-route');

          $.ajax({
              url: route,
              type: 'POST',
              success: function(result) {
                  // Show an alert with the result
                  console.log(result,route);
                  new Noty({
                      text: "Some Tx had been imported",
                      type: "success"
                  }).show();

                  // Hide the modal, if any
                  $('.modal').modal('hide');

                  crud.table.ajax.reload();
              },
              error: function(result) {
                  // Show an alert with the result
                  new Noty({
                      text: "The new entry could not be created. Please try again.",
                      type: "warning"
                  }).show();
              }
          });
      }
    }
</script>
@endpush
```
- Add the new route, next to ```UserCrudController```'s route (most likely inside ```routes/backpack/custom.php```):
```php
Route::get('user/import', 'UserCrudController@import');
```

- We can now add a ```import()``` method to our ```UserCrudController```, which would import the users.
```php
public function import() 
{
    // whatever you decide to do
}
```

- Now we can actually add this button to any of ```UserCrudController::setupListOperation()```:
```php
$this->crud->addButtonFromView('top', 'import', 'import', 'end');
```

### Reorder buttons

The default order of line stack buttons is 'edit', 'delete'. Let's say you are using the `ShowOperation`, by default the preview button gets placed in the beggining of that stack, if you want to move it to the end of the stack you may use `orderButtons` or `moveButton`.

```php
CRUD::orderButtons('line', ['update', 'delete', 'show']);
```

```php
CRUD::moveButton('show', 'after', 'delete');
```
