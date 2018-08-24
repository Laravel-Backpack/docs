# Reorder

---

## About

This operation allows your admins to reoder & nest entries.

![CRUD Reorder Operation](https://backpackforlaravel.com/uploads/docs/operations/reorder.png)

## Requirements

Your model should have the following fields: ```parent_id```, ```lft```, ```rgt```, ```depth```

## How to Use

The ```Reorder``` operation is **disabled by default**. To enable it, you should use ```$this->crud->allowAccess('disabled');``` inside your ```setup()``` method. This will make a Reorder button appear in ListEntries, in the ```top``` button stack. Then, in your EntityCrudController's ```setup()``` method:

```php
$this->crud->enableReorder('attribute_name', ALLOWED_DEPTH);
```
Where:
- ```attribute_name``` should be the attribute you want shown on the draggable elements (ex: ```name```);
- ```ALLOWED_DEPTH``` should be an integer, how many levels deep would you allow your admin to go when nesting; for infinit levels, you should set it to ```0```;

## How It Works

The ```/reorder``` route points to a ```reorder()``` method in your ```EntityCrudController```.