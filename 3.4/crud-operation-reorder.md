# Reordering and Nesting Items

If your model has the proper fields (parent_id, lft, rgt, depth), you can use the built-in reorder functionality. All you need to do is enable it in the EntityCrudController's constructor:

```php
$this->crud->enableReorder('label_name', MAX_TREE_LEVEL);
$this->crud->allowAccess('reorder');
```
*Note: MAX_TREE_LEVEL is the maximum number of nesting elements from parents. For infinit levels, you should set it to 0

For the enableReorder function, the parameters are the db column you need to show on the elements and the number of levels you will allow nesting to (specify 1 for no nesting, just reordering).

This will be your result:
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/ivETb740RuGmsG7ZLA42_Screen%20Shot%202016-05-22%20at%2017.33.04.png",
        "Screen Shot 2016-05-22 at 17.33.04.png",
        "1280",
        "767",
        "#08abad",
        ""
      ]
    }
  ]
}
[/block]