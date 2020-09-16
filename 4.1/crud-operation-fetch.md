# Fetch Operation

---

<a name="about"></a>
## About

This operation allows an EntityCrudController to respond to AJAX requests with entries in the database for _a different entity_, in a format that can be used by the ```relationship```, ```select2_from_ajax``` and ```select2_from_ajax_multiple``` fields.


<a name="requirements"></a>
## Requirements

None.

<a name="how-to-use"></a>
## How to Use

In order to enable this operation in your CrudController, you need to:
1. Use the ```FetchOperation``` trait;
2. Add a ```fetchEntityName()``` method, that will respond to the AJAX requests (following this  naming convention);

```php
    use \Backpack\CRUD\app\Http\Controllers\Operations\FetchOperation;

    protected function fetchTag()
    {
        return $this->fetch(\App\Models\Tag::class);
    }
```

To customize the FetchOperation, pass an array to the ```fetch()``` call, rather than a class name. For example:

```php
<?php

namespace App\Http\Controllers\Admin;

use Backpack\CRUD\app\Http\Controllers\CrudController;

class ProductCrudController extends CrudController
{
    use \Backpack\CRUD\app\Http\Controllers\Operations\FetchOperation;

    protected function fetchTag()
    {
        return $this->fetch([
            'model' => App\Models\Tag::class, // required
            'searchable_attributes' => ['name', 'description'],
            'paginate' => 10, // items to show per page
            'query' => function($model) {
                return $model->active();
            } // to filter the results that are returned
        ]);
    }
}
```

3. A route following has been created automatically to point to the ```fetchTag``` method you created. You now point your AJAX select to this route, using ```backpack_url('your-main-entity/fetch/tag')``` .


<a name="how-it-works"></a>
## How It Works

Based on the fact that the ```fetchTag()``` method exist, the Fetch operation will create a ```/product/fetch/tag``` POST route, which points to ```fetchTag()```. Inside ```fetchTag()``` we call ```fetch()```, that responds with entries in the format ```select2``` needs.


<a name="how-to-overwrite"></a>
## How to Overwrite

In case you need to change how this operation works, it's best to take a look at the ```FetchOperation.php``` trait to understand how it works. It's a pretty simple operation. Most common ways to overwrite the Fetch operation are documented below:

**Custom behaviour for one fetch method**

To make a ```fetchCategory()``` method behave differently, you can copy-paste the logic inside the ```FetchOperation::fetch()``` and change it to do whatever you need. Instead of returing ```$this->fetch()``` you can return your own results.

**Custom behaviour for multiple fetch methods inside a Controller**

To make all calls to ```fetch()``` inside an EntityCrudController behave differently, you can easily overwrite the ```fetch()``` method in that controller:

```php
use \Backpack\CRUD\app\Http\Controllers\Operations\FetchOperation;

public function fetch($arg)
{
    // your custom code here
}
```

Then all ```$this->fetch()``` calls from that Controller will be using your custom code.

In case you need to call the original ```fetch()``` method (from the trait) inside your custom ```fetch()``` method (inside the controller), you can do:

```php
use \Backpack\CRUD\app\Http\Controllers\Operations\FetchOperation { fetch as traitFetch; }

public function fetch($arg)
{
    // your custom code here
    
    // call the method in the trait
    return $this->traitFetch();
}
```

**Custom behaviour for all fetch calls, in all Controllers**

If you want all your ```fetch()``` calls to behave differently, no matter what Controller they are in, you can:
- duplicate the ```FetchOperation``` trait inside your application;
- instead of using ```\Backpack\CRUD\app\Http\Controllers\Operations\FetchOperation``` inside your controllers, use your custom operation trait;
