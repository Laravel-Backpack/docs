# Clone

--

<a href="about"></a>
## About

This CRUD operation allows your admins to duplicate one or more entries from the database.

>**IMPORTANT:** The clone operation does NOT duplicate related entries. So n-n relationships will be unaffected. However, this also means that n-n relationships are ignored. So when you clone an entry, the new entry:
>- will NOT have the same 1-1 relationships
>- will have the same 1-n relationships
>- will NOT have the same n-1 relationships
>- will NOT have the same n-n relationships
>
>This might be somewhat counterintuitive for end users - though it should make perfect sense for us developers. This is why the Clone operation is NOT enabled by default.

<a href="clone-a-single-item"></a>
## Clone a Single Item

<a href="how-it-works"></a>
### How it Works

Using AJAX, a POST request is performed towards ```/entity-name/{id}```, which points to the ```clone()``` method in your EntityCrudController.

<a href="enabling"></a>
### How to Use

The ```clone()``` action is **disabled by default**. To enable it, you should use ```$this->crud->allowAccess('clone');``` inside your ```setup()``` method. This will make the Clone button appear in the table view, and will allow access to the controller method if manually accessed.

<a href="how-to-overwrite"></a>
### How to Overwrite

In case you need to change how this operation works, just create a ```clone()``` method in your EntityCrudController:

```php
public function clone($id)
{
    $this->crud->hasAccessOrFail('clone');
    $this->crud->setOperation('clone');

    // whatever you want
}
```

You can also overwrite the clone button by creating a file with the same name inside your ```resources/views/vendor/backpack/crud/buttons/```. You can easily publish the clone button there to make changes using:

```zsh
php artisan backpack:crud:publish buttons/clone
```

<a href="clone-multiple-items-bulk-clone"></a>
## Clone Multiple Items (Bulk Clone)

In addition to the button for each entry, you can show checkboxes next to each element, and allow your admin to clone multiple entries at once.


<a href="how-it-works"></a>
### How it Works

Using AJAX, a POST request is performed towards ```/entity-name/bulk-clone```, which points to the ```bulkClone()``` method in your EntityCrudController.

<a href="enabling"></a>
### How to Use

The ```bulkClone()``` action is **disabled by default**, and there are no buttons using it. To make the buttons show up, inside your ```setup()``` method you should:

```php
$this->crud->enableBulkActions(); // if you haven't already

$this->crud->allowAccess('clone');
$this->crud->addButton('bottom', 'bulk_clone', 'view', 'crud::buttons.bulk_clone', 'beginning');
```

<a href="how-to-overwrite"></a>
### How to Overwrite

In case you need to change how this operation works, just create a ```bulkClone()``` method in your EntityCrudController:

```php
public function bulkClone($id)
{
    // your custom code here
}
```

You can also overwrite the bulk clone button by creating a file with the same name inside your ```resources/views/vendor/backpack/crud/buttons/```. You can easily publish the clone button there to make changes using:

```zsh
php artisan backpack:crud:publish buttons/bulk_clone
```
