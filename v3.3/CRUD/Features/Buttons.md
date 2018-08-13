---
title: "Buttons"
excerpt: ""
---
Since CRUD v2.1, buttons are defined the same way fields and columns are: each button type has its own blade file, which can be overwritten by placing a file with the same name in your ```resources\views\vendor\backpack\crud\buttons```.

For example, here's how the ```update.blade.php``` file looks like:
[block:code]
{
  "codes": [
    {
      "code": "@if ($crud->hasAccess('update'))\n\t<a href=\"{{ Request::url().'/'.$entry->getKey() }}/edit\" class=\"btn btn-xs btn-default\"><i class=\"fa fa-edit\"></i> {{ trans('backpack::crud.edit') }}</a>\n@endif",
      "language": "php"
    }
  ]
}
[/block]
The most useful variables you'll have in this file here are:
- $entry - the database entry you're showing (only inside the ```line``` stack);
- $crud
[block:api-header]
{
  "type": "basic",
  "title": "Buttons Stacks"
}
[/block]
**There are 3 places** in the list view (table view) **where you can place extra buttons**:
- **top** (where the Add button is)
- **line** (where the Edit and Update buttons are)
- **bottom** (at the bottom of the table box)

When adding a button to the stack, you can choose whether to insert it at the ```beginning``` or ```end``` of the stack by specifying that as a last parameter.
[block:api-header]
{
  "type": "basic",
  "title": "Button Functions"
}
[/block]

[block:code]
{
  "codes": [
    {
      "code": "<?php\n\n// possible positions: 'beginning' and 'end'; defaults to 'beginning' for the 'line' stack, 'end' for the others;\n\n// add a button; possible types are: view, model_function\n$this->crud->addButton($stack, $name, $type, $content, $position);\n\n// add a button whose HTML is returned by a method in the CRUD model\n$this->crud->addButtonFromModelFunction($stack, $name, $model_function_name, $position);\n\n// add a button whose HTML is in a view placed at resources\\views\\vendor\\backpack\\crud\\buttons\n$this->crud->addButtonFromView($stack, $name, $view, $position);\n\n$this->crud->removeButton($name);\n\n$this->crud->removeButtonFromStack($name, $stack);",
      "language": "php"
    }
  ]
}
[/block]
### Example model_function usage

You can use a function on your model to output the HTML of a button that has something particular to that model, or a conditional logic behind it. It's not sexy (HTML in PHP), but you can do it. Notice you also have the CRUD variable as a parameter here.

In the controller:
```php
        $this->crud->addButtonFromModelFunction('line', 'open_google', 'openGoogle', 'beginning'); // add a button whose HTML is returned by a method in the CRUD model
```

In the model:

```php
    public function openGoogle($crud = false)
    {
        return '<a class="btn btn-xs btn-default" target="_blank" href="http://google.com?q='.urlencode($this->text).'" data-toggle="tooltip" title="Just a demo custom button."><i class="fa fa-search"></i> Google it</a>';
    }
```