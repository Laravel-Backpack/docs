---
title: "Advanced Features"
excerpt: ""
---
In addition the usual CRUD functionality, Backpack also allows you to do a few more complicated things:

[block:api-header]
{
  "type": "basic",
  "title": "Customize Views for each CRUD Panel"
}
[/block]
Previously, if you wanted to create a custom ```create.blade.php``` view for just one CRUD Panel, you needed to overwrite the create() method. The same for edit, list, revisions, reorder, etc.

Now you can specify custom views in your ```setup()``` method:
```
$this->crud->setShowView('your-view');
$this->crud->setEditView('your-view');
$this->crud->setCreateView('your-view');
$this->crud->setListView('your-view');
$this->crud->setReorderView('your-view');
$this->crud->setRevisionsView('your-view');
$this->crud->setRevisionsTimelineView('your-view');
$this->crud->setDetailsRowView('your-view');
```
[block:api-header]
{
  "type": "basic",
  "title": "Custom CSS and JS for CRUD operations"
}
[/block]
Starting with Backpack\CRUD 3.2, there's a place where you can place your custom operations javascript and styles. You'll find a CSS and JS file in your ```public/vendor/backpack/crud``` folder for each Backpack operation. You can place any custom operation code there, without bloating general files css, js or blade files.
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/872b2f8-Screen_Shot_2017-02-13_at_10.54.38.png",
        "Screen Shot 2017-02-13 at 10.54.38.png",
        220,
        641,
        "#e3e3e3"
      ]
    }
  ]
}
[/block]

[block:api-header]
{
  "type": "basic",
  "title": "Extra CRUD Routes"
}
[/block]
Starting with Backpack\CRUD 3.2, you can use the ```with()``` method on ```CRUD::resource``` to better organize your routes. Something like this:

```php
CRUD::resource(‘teams’, ‘Admin\TeamCrudController’)->with(function(){
    // add extra routes to this resource
    Route::get('teams/ajax-name-options', 'Admin\TeamCrudController@nameOptions');
    Route::get('teams/ajax-category-options', 'Admin\TeamCrudController@categoryOptions');
});
```

[block:api-header]
{
  "title": "Publishing a column / field / filter / button and modifying it"
}
[/block]
All Backpack packages allow you to easily overwrite and customize the views. If you want Backpack to pick up _your_ file, instead of the one in the package, you can do that by just placing a file with the same name in your views. So if you want to overwrite the select2 field (```vendor/backpack/crud/src/resources/views/fields/select2.blade.php```) to change some functionality, you need to create ```resources/views/vendor/backpack/crud/fields/select2.blade.php```. You can do that manually, or use this command:
```shell
php artisan backpack:crud:publish fields/select2
```
This will look for the blade file and copy it inside the folder, so you can edit it.

**Please note:** Once you place a file with the same name inside your project, Backpack will pick that one up instead of the one in the package. That means that even though the file in the package is updated, you won't be getting those updates, since you're not using that file. Blade modifications are almost never breaking changes, but it's a good thing to receive updates with zero effort. Even small ones. So please overwrite the files as little as possible. Best to create your own custom fields/column/filters/buttons, whenever you can.