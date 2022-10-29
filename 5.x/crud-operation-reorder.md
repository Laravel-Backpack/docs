# Reorder Operation

---

<a name="about"></a>
## About

This operation allows your admins to reorder & nest entries.

![CRUD Reorder Operation](https://backpackforlaravel.com/uploads/docs-4-0/operations/reorder.png)

<a name="requirements"></a>
## Requirements

Your model should have the following integer fields, with a default value of 0: ```parent_id```, ```lft```, ```rgt```, ```depth```.

Additionally, the `parent_id` field has to be nullable.

<a name="how-to-use"></a>
## How to Use

In order to enable this operation in your CrudController, you need to use the ```ReorderOperation``` trait, and have a ```setupReorderOperation()``` method that defines the ```label``` and ```max_level``` of allowed depth.

```php
<?php

namespace App\Http\Controllers\Admin;

use Backpack\CRUD\app\Http\Controllers\CrudController;

class ProductCrudController extends CrudController
{
    use \Backpack\CRUD\app\Http\Controllers\Operations\ReorderOperation;

    protected function setupReorderOperation()
    {
    	// define which model attribute will be shown on draggable elements 
        $this->crud->set('reorder.label', 'name');
        // define how deep the admin is allowed to nest the items
        // for infinite levels, set it to 0
        $this->crud->set('reorder.max_level', 2);
    }
}
```

This will:
- allow access to the Reorder operation;
- make a "Reorder" button appear next to "Add entry" in the List view, if the List operation is enabled;
- enable the routes needed by the Reorder operation;

Where:
- ```attribute_name``` should be the attribute you want shown on the draggable elements (ex: ```name```);
- ```ALLOWED_DEPTH``` should be an integer, how many levels deep would you allow your admin to go when nesting; for infinite levels, you should set it to ```0```;

<a name="how-it-works"></a>
## How It Works

The ```/reorder``` route points to a ```reorder()``` method in your ```EntityCrudController```.

<a name="widget"></a>
## How to add custom sections(aka. Widgets)

[Widgets](https://backpackforlaravel.com/docs/5.x/base-widgets) (aka cards, aka charts, aka graphs) provide a simple way to insert blade files into admin panel pages. You can use them to insert cards, charts, notices or custom content into pages. You can find available widget [here](https://backpackforlaravel.com/docs/5.x/base-widgets#default-widget-types).

Backpack template includes two [sections](https://backpackforlaravel.com/docs/5.x/base-widgets#requirements-1) where you can push widgets:

* before_content
* after_content

To use widgets on reorder operation, define them inside `setupReorderOperation()` function.

```php
public function setupReorderOperation()
{    
    // dynamic data to render in the following widget
    $userCount = \App\Models\User::count();

    //add div row using 'div' widget and make other widgets inside it to be in a row
    Widget::add()->to('before_content')->type('div')->class('row')->content([
        
        //widget made using fluent syntax
        Widget::make()
            ->type('progress')
            ->class('card border-0 text-white bg-primary')
            ->progressClass('progress-bar')
            ->value($userCount)
            ->description('Registered users.')
            ->progress(100 * (int)$userCount / 1000)
            ->hint(1000 - $userCount . ' more until next milestone.'),

        //widget made using the array definition 
        Widget::make(
            [
                'type'       => 'card',
                'class'   => 'card bg-dark text-white',
                'wrapper' => ['class' => 'col-sm-3 col-md-3'],
                'content'    => [
                    'header' => 'Example Widget',
                    'body'   => 'Widget placed at "before_content" secion in same row',
                ]
            ]
        ),
    ]);

    //you can also add Script & CSS to your page using 'script' & 'style' widget
    Widget::add()->type('script')->stack('after_scripts')->content('https://code.jquery.com/ui/1.12.0/jquery-ui.min.js');
    Widget::add()->type('style')->stack('after_styles')->content('https://cdn.jsdelivr.net/npm/@shoelace-style/shoelace@2.0.0-beta.58/dist/themes/light.css');
}
```

#### Output:
* Using `before_content`:

![](https://i.imgur.com/MF9ePIM.png)
* Using `after_content`

![](https://i.imgur.com/AxC3lAZ.png)

<a name="how-to-overwrite"></a>
## How to Overwrite

In case you need to change how this operation works, take a look at the ```ReorderOperation.php``` trait to understand how it works. You can easily overwrite the ```reorder()``` or the ```saveReorder()``` methods:

```php
use \Backpack\CRUD\app\Http\Controllers\Operations\ReorderOperation { reorder as traitReorder; }

public function reorder()
{
    // your custom code here
    
    // call the method in the trait
    return $this->traitReorder();
}
```

You can also overwrite the reorder button by creating a file with the same name inside your ```resources/views/vendor/backpack/crud/buttons/```. You can easily publish the reorder button there to make changes using:

```zsh
php artisan backpack:button --from=reorder
```
