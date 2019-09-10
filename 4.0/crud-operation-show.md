# Show

--

<a name="about"></a>
## About

This CRUD operation allows your admins to preview an entry. When enabled, it will add a "Preview" button in the ListEntries view, that points to a show page:

![Backpack CRUD Show Operation](https://backpackforlaravel.com/uploads/docs/operations/show.png)

In case your entity is translatable, it will show a multi-language dropdown, just like Edit.

<a name="how-it-works"></a>
## How it Works

The ```/entity-name/{id}/show``` route points to the ```show()``` method in your EntityCrudController. Inside this method, it uses ```setFromDb()``` to try to magically figure out all attributes you would like shown for this Model, and shows them using [Column types](/docs/{{version}}/crud-columns) inside ```show.blade.php```.

<a name="enabling"></a>
## How to Use

To enable this operation, you need to use the ```ShowOperation``` trait on your CrudController:

```php
<?php

namespace App\Http\Controllers\Admin;

use Backpack\CRUD\app\Http\Controllers\CrudController;

class ProductCrudController extends CrudController
{
    use \Backpack\CRUD\app\Http\Controllers\Operations\ShowOperation;
}
```

This will:
- make a Preview button appear inside the List view; 
- allow access to the show view;

The operation uses ```setFromDb()``` to try to determine how each attribute should be shown to the user. Most likely this won't be enough, and you'll need to manually define the more complicated column types. You should do this inside the ```setupShowOperation()``` method, which gets called automatically when the Show operation is being performed.

```php
    protected setupShowOperation()
    {
        // example logic
        $this->crud->addColumn([
            'name' => 'table',
            'label' => 'Table',
            'type' => 'table',
            'columns' => [
                'name'  => 'Name',
                'desc'  => 'Description',
                'price' => 'Price',
            ]
        ]);
        $this->crud->addColumn([
            'name' => 'fake_table',
            'label' => 'Fake Table',
            'type' => 'table',
            'columns' => [
                'name'  => 'Name',
                'desc'  => 'Description',
                'price' => 'Price',
            ],
        ]);
        $this->crud->addColumn('text');
        $this->crud->removeColumn('date');
        $this->crud->removeColumn('extras');
    }
```

This view uses the same column types you've defined in ```setup()``` and adds all other **fillable** attributes on the model. If you need to hide a ListEntries column from the Show operation, you can specify ```'visibleInShow' => false,``` to the column in ```setup()```, and it will be hidden from the preview page.

<a name="how-to-overwrite"></a>
## How to Overwrite

In case you need to modify the show logic in a meaningful way, you can create a ```show()``` method in your EntityCrudController. The route will then point to your method, instead of the one in the trait. For example:

```php
use \Backpack\CRUD\app\Http\Controllers\Operations\ShowOperation { show as traitShow; }

public function show($id)
{
    // custom logic before
    $content = $this->traitShow($id);
    // cutom logic after
    return $content;
}
```
