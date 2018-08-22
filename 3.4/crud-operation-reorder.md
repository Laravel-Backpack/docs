# Reorder

---

# About

This operation allows your admins to reoder & nest entries.

![CRUD Reorder Operation](https://backpackforlaravel.com/uploads/docs/operations/reorder.png)

## Requirements

Your model should have the following fields: ```parent_id```, ```lft```, ```rgt```, ```depth```

## Enabling

In your EntityCrudController's ```setup()``` method:

```php
$this->crud->enableReorder('attribute_name', ALLOWED_DEPTH);
$this->crud->allowAccess('reorder');
```
Where:
- ```attribute_name``` should be the attribute you want shown on the draggable elements (ex: ```name```);
- ```ALLOWED_DEPTH``` should be an integer, how many levels deep would you allow your admin to go when nesting; for infinit levels, you should set it to ```0```;
