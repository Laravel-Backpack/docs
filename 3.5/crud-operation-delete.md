# Delete

--

<a href="about"></a>
## About

This CRUD operation allows your admins to remove one or more entries from the table. 

>In case your entity has SoftDeletes, it will perform a soft delete. The admin will _not_ know that his entry has been hard or soft deleted, since it will no longer show up in the ListEntries view.

<a href="delete-a-single-item"></a>
## Delete a Single Item

<a href="how-it-works"></a>
### How it Works

Using AJAX, a DELETE request is performed towards ```/entity-name/{id}```, which points to the ```destroy()``` method in your EntityCrudController.

<a href="enabling"></a>
### How to Use

The ```Delete``` action is **enabled by default**. To disable it, you should use ```$this->crud->denyAccess('delete');``` inside your ```setup()``` method. This will prevent the Delete button from appearing in the table view, and will deny access to the controller method if manually accessed. 

<a href="how-to-overwrite"></a>
### How to Overwrite

In case you need to change how this operation works, just create a ```destroy()``` method in your EntityCrudController:

```php
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

<a href="delete-multiple-items-bulk-delete"></a>
## Delete Multiple Items (Bulk Delete)

In addition to the button for each entry, you can show checkboxes next to each element, and allow your admin to delete multiple entries at once.


<a href="how-it-works"></a>
### How it Works

Using AJAX, a DELETE request is performed towards ```/entity-name/bulk-delete```, which points to the ```bulkDelete()``` method in your EntityCrudController.

<a href="enabling"></a>
### How to Use

The ```bulkDelete()``` action is **enabled by default**, but there are no buttons using it. To make the buttons show up, inside your ```setup()``` method you should:

```php
$this->crud->enableBulkActions();
$this->crud->addBulkDeleteButton();
```

<a href="how-to-overwrite"></a>
### How to Overwrite

In case you need to change how this operation works, just create a ```bulkDelete()``` method in your EntityCrudController:

```php
public function bulkDelete($id)
{
    // your custom code here
}
```

You can also overwrite the bulk delete button by creating a file with the same name inside your ```resources/views/vendor/backpack/crud/buttons/```. You can easily publish the delete button there to make changes using:

```zsh
php artisan backpack:crud:publish buttons/bulk_delete
```