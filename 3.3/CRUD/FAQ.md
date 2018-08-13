---
title: "FAQ"
excerpt: ""
---
[block:api-header]
{
  "title": "How do I filter the options in a select field?"
}
[/block]
This also applies to: select2, select2_multiple, select2_from_ajax, select2_from_ajax_multiple.

There are 3 possible solutions:
1. If there will be few options (dozens), the easiest way would be to use ```select_from_array``` or ```select2_from_array``` instead. Since you tell it what the options are, you can do any filtering you want there.
2. If there are a lot of options (100+), best to use ```select2_from_ajax``` instead. You'll be able to filter the results any way you want in the controller method (the one that responds with the results to the AJAX call).
3. If you can't use ```select2_from_array``` for ```select2_from_ajax```, you can create another model and add a global scope to it. For example, say you only want to show the users that belong to the user's company. You can create ```App\Models\CompanyUser``` and use that in your ```select2``` field instead of ```App\Models\User```:

```php
<?php

namespace App\Models;

use App\User;
use Auth;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Builder;

class CompanyUser extends User
{
    /**
     * The "booting" method of the model.
     *
     * @return void
     */
    protected static function boot()
    {
        parent::boot();

        // never let a company user see the users of other companies
        if (Auth::check() && Auth::user()->company_id) {
            $companyId = Auth::user()->company->id;

            static::addGlobalScope('company_id', function (Builder $builder) use ($companyId) {
                $builder->where('company_id', $companyId);
            });
        }
    }
}
```
[block:api-header]
{
  "title": "Use the same column name multiple times in a CRUD"
}
[/block]
If you try to add multiple columns with the same ```name```, by default Backpack will only show the last one. That's because ```name``` is also used as a key in the ```$column``` array. So when you ```addColumn()``` with the same name twice, it just overwrites the previous one.

In order to insert two column with the same name, use the ```key``` attribute on the second column (or both columns). If this attribute is present for a column, Backpack will use ```key``` instead of ```name```. Example:

```diff
        $this->crud->addColumn([
           'label' => "Location",
           'type' => 'select',
           'name' => 'location_id',
           'entity' => 'location',
           'attribute' => 'title',
           'model' => "App\Models\Location"
        ]);

        $this->crud->addColumn([
           'label' => "Location Type",
           'type' => 'radio_location_type',
           'options' => [
               1 => "Country",
               2 => "Region"
           ],
           'name' => 'location_id',
+          'key' => 'location_type',
           'entity' => 'location',
           'attribute' => 'type',
           'model' => "App\Models\Location"
        ]);
```