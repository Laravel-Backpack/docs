# Reorder Operation

---

<a name="about"></a>
## About

This operation allows your admins to reorder & nest entries.

![CRUD Reorder Operation](https://backpackforlaravel.com/uploads/docs-4-0/operations/reorder.png)

<a name="requirements"></a>
## Requirements

Your model should have the following integer fields, with a default value of 0: ```parent_id```, ```lft```, ```rgt```, ```depth```.

Additionally, the `parent_id` field has to be nullable.

<a name="how-to-use"></a>
## How to Use

In order to enable this operation in your CrudController, you need to use the ```ReorderOperation``` trait, and have a ```setupReorderOperation()``` method that defines the ```label``` and ```max_level``` of allowed depth.

```php
<?php

namespace App\Http\Controllers\Admin;

use Backpack\CRUD\app\Http\Controllers\CrudController;

class ProductCrudController extends CrudController
{
    use \Backpack\CRUD\app\Http\Controllers\Operations\ReorderOperation;

    protected function setupReorderOperation()
    {
    	// define which model attribute will be shown on draggable elements 
        $this->crud->set('reorder.label', 'name');
        // define how deep the admin is allowed to nest the items
        // for infinite levels, set it to 0
        $this->crud->set('reorder.max_level', 2);
    }
}
```

This will:
- allow access to the Reorder operation;
- make a "Reorder" button appear next to "Add entry" in the List view, if the List operation is enabled;
- enable the routes needed by the Reorder operation;

Where:
- ```attribute_name``` should be the attribute you want shown on the draggable elements (ex: ```name```);
- ```ALLOWED_DEPTH``` should be an integer, how many levels deep would you allow your admin to go when nesting; for infinite levels, you should set it to ```0```;

<a name="how-it-works"></a>
## How It Works

The ```/reorder``` route points to a ```reorder()``` method in your ```EntityCrudController```.


<a name="how-to-overwrite"></a>
## How to Overwrite

In case you need to change how this operation works, take a look at the ```ReorderOperation.php``` trait to understand how it works. You can easily overwrite the ```reorder()``` or the ```saveReorder()``` methods:

```php
use \Backpack\CRUD\app\Http\Controllers\Operations\ReorderOperation { reorder as traitReorder; }

public function reorder()
{
    // your custom code here
    
    // call the method in the trait
    return $this->traitReorder();
}
```

You can also overwrite the reorder button by creating a file with the same name inside your ```resources/views/vendor/backpack/crud/buttons/```. You can easily publish the reorder button there to make changes using:

```zsh
php artisan backpack:publish crud/buttons/reorder
```
