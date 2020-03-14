# Clone

--

<a name="about"></a>
## About

This CRUD operation allows your admins to duplicate one or more entries from the database.

>**IMPORTANT:** The clone operation does NOT duplicate related entries. So n-n relationships will be unaffected. However, this also means that n-n relationships are ignored. So when you clone an entry, the new entry:
>- will NOT have the same 1-1 relationships
>- will have the same 1-n relationships
>- will NOT have the same n-1 relationships
>- will NOT have the same n-n relationships
>
>This might be somewhat counterintuitive for end users - though it should make perfect sense for us developers. This is why the Clone operation is NOT enabled by default.

<a name="clone-a-single-item"></a>
## Clone a Single Item

<a name="how-it-works"></a>
### How it Works

Using AJAX, a POST request is performed towards ```/entity-name/{id}/clone```, which points to the ```clone()``` method in your EntityCrudController.

<a name="enabling"></a>
### How to Use

To enable it, you need to ```use \Backpack\CRUD\app\Http\Controllers\Operations\CloneOperation;``` on your EntityCrudController. For example:

```php
<?php

namespace App\Http\Controllers\Admin;

use Backpack\CRUD\app\Http\Controllers\CrudController;

class ProductCrudController extends CrudController
{
    use \Backpack\CRUD\app\Http\Controllers\Operations\CloneOperation;

    public function setup() 
    {
        $this->crud->setModel(\App\Models\Product::class);
        $this->crud->setRoute(backpack_url('product'));
        $this->crud->setEntityNameStrings('product', 'products');
    }
}
```

This will make the Clone button appear in the table view, and will allow access to the controller method if manually accessed.

<a name="how-to-overwrite"></a>
### How to Overwrite

In case you need to change how this operation works, overwrite the ```clone()``` trait method in your EntityCrudController; make sure you give the method in the trait a different name, so that there are no conflicts:

```php
use \Backpack\CRUD\app\Http\Controllers\Operations\CloneOperation { clone as traitClone; }

public function clone($id)
{
    $this->crud->hasAccessOrFail('clone');
    $this->crud->setOperation('clone');

    // whatever you want
    
    // if you still want to call the old clone method
    $this->traitClone($id);
}
```

You can also overwrite the clone button by creating a file with the same name inside your ```resources/views/vendor/backpack/crud/buttons/```. You can easily publish the clone button there to make changes using:

```zsh
php artisan backpack:publish buttons/clone
```

<a name="clone-multiple-items-bulk-clone"></a>
## Clone Multiple Items (Bulk Clone)

In addition to the button for each entry, you can show checkboxes next to each element, and allow your admin to clone multiple entries at once.


<a name="how-it-works"></a>
### How it Works

Using AJAX, a POST request is performed towards ```/entity-name/bulk-clone```, which points to the ```bulkClone()``` method in your EntityCrudController.

<a name="enabling"></a>
### How to Use

To enable it, you need to ```use \Backpack\CRUD\app\Http\Controllers\Operations\BulkCloneOperation;``` on your EntityCrudController.

<a name="how-to-overwrite"></a>
### How to Overwrite

In case you need to change how this operation works, just create a ```bulkClone()``` method in your EntityCrudController:

```php
use \Backpack\CRUD\app\Http\Controllers\Operations\BulkCloneOperation { bulkClone as traitBulkClone; }

public function bulkClone($id)
{
    // your custom code here
    // 
    // then you can call the old bulk clone if you want
    $this->traitBulkClone($id);
}
```

You can also overwrite the bulk clone button by creating a file with the same name inside your ```resources/views/vendor/backpack/crud/buttons/```. You can easily publish the clone button there to make changes using:

```zsh
php artisan backpack:publish buttons/bulk_clone
```
