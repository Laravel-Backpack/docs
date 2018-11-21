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
- ```add``` to the ```top``` stack;
- ```edit``` and ```delete``` to the ```line``` stack;

Default buttons are invisible if an operation has been disabled. For example, you can: 
- hide the "delete" button using ```$this->crud->denyAccess('delete')```;
- show a "preview" button by using ```$this->crud->allowAccess('show')```;

<a name="buttons-api"></a>
### Buttons API

Here are a few things you can call in your EntityCrudController's ```setup()``` method, to manipulate buttons:

```php
// possible positions: 'beginning' and 'end'; defaults to 'beginning' for the 'line' stack, 'end' for the others;

// add a button; possible types are: view, model_function
$this->crud->addButton($stack, $name, $type, $content, $position);

// add a button whose HTML is returned by a method in the CRUD model
$this->crud->addButtonFromModelFunction($stack, $name, $model_function_name, $position);

// add a button whose HTML is in a view placed at resources\views\vendor\backpack\crud\buttons
$this->crud->addButtonFromView($stack, $name, $view, $position);

// remove a button
$this->crud->removeButton($name);

// remove a button for a certain stack (top, line, bottom)
$this->crud->removeButtonFromStack($name, $stack);
```
<a name="overwriting-a-default-button"></a>
### Overwriting a Default Button

Before showing any buttons, Backpack will check your ```resources\views\vendor\backpack\crud\buttons``` directory, to see if you've overwritten any default buttons. If it finds a blade file with the same name there as the default buttons, it will use your blade file, instead of the default.

That means **you can overwrite an existing button simply by creating a blade file with the same name inside this directory**.

<a name="creating-a-custom-button"></a>
### Creating a Custom Button

To create a custom button:
- create a new blade file in ```resources\views\vendor\backpack\crud\buttons```;
- add that button using the ```addButton()``` syntax above, in the EntityCrudControllers you want, inside the ```setup()``` method;

In this blade file, you can use:
- ```$entry``` - the database entry you're showing (only inside the ```line``` stack);
- ```$crud``` - the entire CrudPanel object;
- ```$button``` - the button you're currently showing;

<a name="examples"></a>
## Examples

<a name="adding-a-custom-button-with-a-blade-file"></a>
### Adding a Custom Button with a Blade File

Let's say we want to create a simple ```moderate.blade.php``` button. This button would just open a ```user/{id}/moderate/``` route, which would point to ```UserCrudController::moderate()```. The steps would be:

- Create the ```resources\views\vendor\backpack\crud\buttons\moderate.blade.php``` file:
```php
@if ($crud->hasAccess('update'))
  <a href="{{ Request::url().'/'.$entry->getKey() }}/moderate" class="btn btn-xs btn-default"><i class="fa fa-ban"></i> Moderate</a>
@endif
```
- Add the new route, next to ```UserCrudController```'s route (most likely inside ```routes/backpack/custom.php```):
```php
Route::get('user/{id}/moderate', 'UserCrudController@ban');
```

- We can now create add a ```moderate()``` method to our ```UserCrudController```, which would moderate the user, and redirect back.
```php
public function moderate() 
{
    // show a form that does something
}
```

- Now we can actually add this button to any of ```UserCrudController::setup()```:
```php
$this->crud->addButtonFromView('line', 'moderate', 'moderate', 'beginning');
```

<a name="adding-a-custom-button-without-a-blade-file"></a>
### Adding a Custom Button without a Blade File

Instead of creating a blade file for your button, you can use a function on your model to output the button's HTML.

In your ```ArticleCrudController::setup()```:
```php
// add a button whose HTML is returned by a method in the CRUD model
$this->crud->addButtonFromModelFunction('line', 'open_google', 'openGoogle', 'beginning');
```

In your ```Article``` model:

```php
    public function openGoogle($crud = false)
    {
        return '<a class="btn btn-xs btn-default" target="_blank" href="http://google.com?q='.urlencode($this->text).'" data-toggle="tooltip" title="Just a demo custom button."><i class="fa fa-search"></i> Google it</a>';
    }
```