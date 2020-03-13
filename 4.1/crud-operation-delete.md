# Delete

--

<a name="about"></a>
## About

This CRUD operation allows your admins to remove one or more entries from the table. 

>In case your entity has SoftDeletes, it will perform a soft delete. The admin will _not_ know that his entry has been hard or soft deleted, since it will no longer show up in the ListEntries view.

<a name="delete-a-single-item"></a>
## Delete a Single Item

<a name="how-it-works"></a>
### How it Works

Using AJAX, a DELETE request is performed towards ```/entity-name/{id}```, which points to the ```destroy()``` method in your EntityCrudController.

<a name="enabling"></a>
### How to Use

To enable it, you need to ```use \Backpack\CRUD\app\Http\Controllers\Operations\DeleteOperation;``` on your EntityCrudController. For example:

```php
<?php

namespace App\Http\Controllers\Admin;

use Backpack\CRUD\app\Http\Controllers\CrudController;

class ProductCrudController extends CrudController
{
    use \Backpack\CRUD\app\Http\Controllers\Operations\DeleteOperation;
}
```

This will make a Delete button show up in the list view, and will enable the routes and functionality needed for the operation.

<a name="how-to-overwrite"></a>
### How to Overwrite

In case you need to change how this operation works, just create a ```destroy()``` method in your EntityCrudController:

```php
use \Backpack\CRUD\app\Http\Controllers\Operations\DeleteOperation { destroy as traitDestroy; }

public function destroy($id)
{
    $this->crud->hasAccessOrFail('delete');

    return $this->crud->delete($id);
}
```

You can also overwrite the delete button by creating a file with the same name inside your ```resources/views/vendor/backpack/crud/buttons/```. You can easily publish the delete button there to make changes using:

```zsh
php artisan backpack:crud:publish buttons/delete
```

<a name="delete-multiple-items-bulk-delete"></a>
## Delete Multiple Items (Bulk Delete)

In addition to the button for each entry, you can show checkboxes next to each element, and allow your admin to delete multiple entries at once.


<a name="how-it-works"></a>
### How it Works

Using AJAX, a DELETE request is performed towards ```/entity-name/bulk-delete```, which points to the ```bulkDelete()``` method in your EntityCrudController.

<a name="enabling"></a>
### How to Use

You need to ```use \Backpack\CRUD\app\Http\Controllers\Operations\BulkDeleteOperation;``` on your EntityCrudController.

<a name="how-to-overwrite"></a>
### How to Overwrite

In case you need to change how this operation works, just create a ```bulkDelete()``` method in your EntityCrudController:

```php
use \Backpack\CRUD\app\Http\Controllers\Operations\BulkDeleteOperation { bulkDelete as traitBulkDelete; }

public function bulkDelete($id)
{
    // your custom code here
}
```

You can also overwrite the bulk delete button by creating a file with the same name inside your ```resources/views/vendor/backpack/crud/buttons/```. You can easily publish the delete button there to make changes using:

```zsh
php artisan backpack:crud:publish buttons/bulk_delete
```
