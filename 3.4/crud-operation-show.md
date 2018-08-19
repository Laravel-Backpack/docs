# Show (preview and entry)

If you want your entries to show a "Preview" button in the table, you can enable the "Show" operation, which does just that.

1. In your ```EntityCrudController::setup()``` method specify ```$this->crud->allowAccess('show');``` - this will make a Preview button appear in the table view, and allow access to the show view.

2. By default, the "show" functionality uses the same column types you've defined in ```setup()``` and adds all other fillable attributes. In case you need to add/change/remove any columns, create a ```show()``` method in the controller. Using the ```addColumn()``` you're already familiar with you'll be replacing any columns automagically added (since they'll have the same name):

Example customization of ```show()``` method:

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

In case your entity is translatable, it will show a multi-language dropdown, just like Edit.
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/6bc4e1e-43760301-4f2696e0-9a2a-11e8-8292-e2d2730a9198.gif",
        "43760301-4f2696e0-9a2a-11e8-8292-e2d2730a9198.gif",
        1279,
        1344,
        "#c7cbd2"
      ]
    }
  ]
}
[/block]