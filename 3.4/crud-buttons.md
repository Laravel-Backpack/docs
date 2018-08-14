# Buttons

---

## Working with Buttons

Buttons work similarly to fields and columns. But, unlike fields and columns:
- you can choose where a button should be added, since there is more than one place you can display a button; **there are 3 stacks in the list view where you can add buttons**:
  - ```top``` (where the Add button is)
  - ```line``` (where the Edit and Delete buttons are)
  - ```bottom``` (after the table)
- when adding a button to the stack, you can choose whether to insert it at the ```beginning``` or ```end``` of the stack by specifying that as a last parameter;

Here are a few things you can call in your EntityCrudController's ```setup()``` method, to work with buttons:

```php
// possible positions: 'beginning' and 'end'; defaults to 'beginning' for the 'line' stack, 'end' for the others;

// add a button; possible types are: view, model_function
$this->crud->addButton($stack, $name, $type, $content, $position);

// add a button whose HTML is returned by a method in the CRUD model
$this->crud->addButtonFromModelFunction($stack, $name, $model_function_name, $position);

// add a button whose HTML is in a view placed at resources\views\vendor\backpack\crud\buttons
$this->crud->addButtonFromView($stack, $name, $view, $position);

$this->crud->removeButton($name);

$this->crud->removeButtonFromStack($name, $stack);
```

## Overwriting a Default Button

Before showing any buttons, Backpack will check your ```resources\views\vendor\backpack\crud\buttons``` directory, to see if you've overwritten any default buttons. If it finds a blade file with the same name there as the default buttons, it will use your blade file, instead of the default.

That means **you can overwrite an existing button simply by creating a blade file with the same name inside this directory**.

## Creating a Custom Button

To create a custom button:
- create a new blade file in ```resources\views\vendor\backpack\crud\buttons```;
- add that button using the ```addButton()``` syntax above, in the EntityCrudControllers you want, inside the ```setup()``` method;

In this blade file, you can use:
- ```$entry``` - the database entry you're showing (only inside the ```line``` stack);
- ```$crud``` - the entire CrudPanel object;
- ```$button``` - the button you're currently showing;

## Examples

### Adding a Custom Button with a Blade File

Let's say we want to create a simple ```ban.blade.php``` button. This button would just open a ```user/{id}/ban/``` route, which would point to ```UserCrudController::ban()```. The steps would be:

- Create the ```resources\views\vendor\backpack\crud\buttons\moderate.blade.php``` file:
```php
@if ($crud->hasAccess('update'))
  <a href="{{ Request::url().'/'.$entry->getKey() }}/moderate" class="btn btn-xs btn-default"><i class="fa fa-ban"></i> Ban</a>
@endif
```
- Add the new route, next to ```UserCrudController```'s route (most likely inside ```routes/backpack/custom.php```):
```php
Route::get('user/{id}/ban', 'UserCrudController@ban');
```

- We can now create add a ```ban()``` method to our ```UserCrudController```, which would ban the user, and redirect back.
```php
public function ban() 
{
    // do stuff
    return redirect()->back();
}
```


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