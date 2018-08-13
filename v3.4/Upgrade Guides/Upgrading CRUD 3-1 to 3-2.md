---
title: "Upgrading CRUD 3.1 to 3.2"
excerpt: ""
---
Backpack\CRUD 3.2 brings in a lot of cool features, and works on both Laravel 5.3 and 5.4. **You don't _need_ to upgrade your existing projects to 3.2**, since there are no security issues in Backpack\CRUD 3.1, that we know of.  But if you want any of the cool new features, upgrade using this guide.

**The upgrade process should take anywhere between 15 min and 2 hours**, depending on how complex your projects is and how many customizations you have made to the blade files and EntityCrudControllers.

Here's what's new:
[block:api-header]
{
  "type": "basic",
  "title": "New save button"
}
[/block]
The save button is now a lot sexier. Users can now choose where they want to be redirected after saving, and that preference is saved for the session. This, added to 
- the "autoFocusOnFirstField" feature that was already there
- the fact that Ctrl+S triggers the save button
results in a completely mouse-free experience when adding multiple entries.

Plus, it looks great:
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/c413304-f44f3108-94a1-11e6-8827-c8d062405b1d.png",
        "f44f3108-94a1-11e6-8827-c8d062405b1d.png",
        771,
        349,
        "#eaf1f1"
      ]
    }
  ]
}
[/block]

[block:api-header]
{
  "type": "basic",
  "title": "Cleaner routes"
}
[/block]
You can now use the ```with()``` method on ```CRUD::resource``` to better organize your routes. Something like this:

```php
CRUD::resource(‘teams’, ‘Admin\TeamCrudController’)->with(function(){
    // add extra routes to this resource
    Route::get('teams/ajax-name-options', 'Admin\TeamCrudController@nameOptions');
    Route::get('teams/ajax-category-options', 'Admin\TeamCrudController@categoryOptions');
});
```
[block:api-header]
{
  "type": "basic",
  "title": "CRUDs for translatable entities"
}
[/block]

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/a4fc862-9c920278-c922-11e6-9802-0938011d213c.png",
        "9c920278-c922-11e6-9802-0938011d213c.png",
        1033,
        670,
        "#f5f7f7"
      ]
    }
  ]
}
[/block]
You can now let your admins edit multi-lingual entries. For now it only supports translations stored the [spatie/laravel-translatable](https://github.com/spatie/laravel-translatable) way, but more options will be coming soon.

[Details in the docs](https://laravel-backpack.readme.io/docs/advanced-features#section-translatable-models-and-multi-language-cruds) 
[block:api-header]
{
  "type": "basic",
  "title": "Tabbed Forms"
}
[/block]
You can now split your create/edit inputs into multiple tabs.
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/258e3e4-Screen_Shot_2017-02-12_at_12.44.43.png",
        "Screen Shot 2017-02-12 at 12.44.43.png",
        741,
        568,
        "#db4c3c"
      ]
    }
  ]
}
[/block]
In order to use this feature, you just need to specify the tab name for each of your fields. Example:

```
        $this->crud->addField([ // select_from_array
            'name' => 'select_from_array',
            'label' => "Select from array",
            'type' => 'select_from_array',
            'options' => ['one' => 'One', 'two' => 'Two', 'three' => 'Three'],
            'allows_null' => false,
            'allows_multiple' => true,
            'tab' => 'Tab name here'
        ]);
```

If you forget to specify a tab name for a field, Backpack will place it in the last tab.
[block:api-header]
{
  "type": "basic",
  "title": "Inline Errors"
}
[/block]
In addition to the top errors (grouped), Backpack forms (create & edit) can now also show highlight each input that has errored. You just need to enable the feature in your ```crud.php``` config file. This should improve your admin's UX a lot when making mistakes.
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/759caa3-Screen_Shot_2017-02-13_at_10.17.37.png",
        "Screen Shot 2017-02-13 at 10.17.37.png",
        706,
        491,
        "#da4d3d"
      ]
    }
  ]
}
[/block]

[block:api-header]
{
  "type": "basic",
  "title": "Custom views for each CRUD Panel"
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
  "title": "How to upgrade"
}
[/block]
1. In your composer.json, change your required version from ```"backpack/crud": "^3.1.0",``` to ```"backpack/crud": "^3.2.0",```

2. Run ```composer update```.

3. The lang files need to be republished. Please:

    - delete all the ```crud.php``` files in the subfolders of  ```resources/lang/vendor/backpack```;
    - republish the lang files by running ```php artisan vendor:publish --provider="Backpack\CRUD\CrudServiceProvider" --tag="lang"```

4. A lot of important CRUD blade files have suffered changes (```create```, ```edit```, ```form_content```, ```list```, ```show```, ```revisions```, ```revisions_timeline```). If you've overwritten these by placing files with the same names in your ```/resources/views/vendor/backpack/crud``` folder, unfortunately, the fastest way to upgrade would be to delete your custom files, use the new files and redo your modifications. This shouldn't be a big issue for most admin panels, since they're not published by default and 99% of Backpack users don't overwrite them, but if you did, this is going to be the most laborious step of the process. If you want to [take a look at the changes, you can do that in this diff](https://github.com/Laravel-Backpack/CRUD/compare/3.1...3.2?expand=1#diff-608f6dd12f9c53cd8f33f5d665aa2efd).

5. There have been small changes to the ```create()```, ```storeCrud()```, ```edit()``` and ```updateCrud()``` methods. If you have never overwritten them in your EntityCrudControllers, you're good - nothing to do. Please check all your EntityCrudControllers, see if you have custom code inside those methods. If you _have_ overwritten them or eliminated them completely by doing the same operations in your ```update()``` or ```store()``` methods, please [take a look at the changes in CrudController from this diff](https://github.com/Laravel-Backpack/CRUD/compare/3.1...3.2?expand=1#diff-608f6dd12f9c53cd8f33f5d665aa2efd). Click on the "Files Changed" tab, then search for the "CrudController.php" file. The changes are very small, but you may need to do them in your methods too.

6. [optional] Delete and republish the CRUD config file: 
```php artisan vendor:publish --provider="Backpack\CRUD\CrudServiceProvider" --tag="config"``` 
or copy-paste all options you don't have in your ```config/backpack/crud.php``` from this [up-to-date config file](https://github.com/Laravel-Backpack/CRUD/blob/3.2/src/config/backpack/crud.php).
[block:api-header]
{
  "type": "basic",
  "title": "Feedback or problems?"
}
[/block]
Open a Github issue in the [appropriate repository](https://github.com/laravel-backpack/crud).

For non-bugs, please post on [Stack Overflow](http://stackoverflow.com/questions/tagged/backpack-for-laravel) (using the ```backpack-for-laravel``` and ```laravel``` tags). Keeping Github Issues only for bugs & features will allow us to push fixes and features way faster. Thank you for understanding!

If you want to help people out with development questions, that would be a [HUGE](https://i.imgflip.com/yuegp.jpg) help for the community, and a boost to your StackOverflow profile. You can answer people by [adding a StackOverflow filter](https://stackexchange.com/filters) for the ```backpack-for-laravel``` tag to receive questions by email. You would be helping not only that first person that asked, but any other person that searches and finds the answer. Including you :-) Thank you!