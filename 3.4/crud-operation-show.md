# Show

--

## About

This CRUD operation allows your admins to preview an entry. When enabled, it will add a "Preview" button in the ListEntries view, that points to a show page:

![Backpack CRUD Show Operation](https://backpackforlaravel.com/uploads/docs/operations/show.png)

In case your entity is translatable, it will show a multi-language dropdown, just like Edit.

## Enabling

To enable the "Show" operation:

1. In your ```EntityCrudController::setup()``` method specify ```$this->crud->allowAccess('show');``` - this will make a Preview button appear in the table view, and allow access to the show view. This view uses the same column types you've defined in ```setup()``` and adds all other fillable attributes.

2. In case you need to add/change/remove any columns, create a ```show()``` method in your EntityCrudController. Using the ```addColumn()``` you're already familiar with, you can change how those attributes are shown to the admin. For example:

```php
public function show($id)
{
    $content = parent::show($id);

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

    return $content;
}
```
