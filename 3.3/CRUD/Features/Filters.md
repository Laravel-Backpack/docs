---
title: "Filters"
excerpt: ""
---
Backpack CRUD allows you to show a filters bar right above the entries table. 
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/4484004-Screen_Shot_2016-11-10_at_16.10.32.png",
        "Screen Shot 2016-11-10 at 16.10.32.png",
        1043,
        479,
        "#f2f5f5"
      ]
    }
  ]
}
[/block]
When selected or modified, the filters work like this :
- on standard DataTables they refresh the page with a new GET parameter;
- on ajax DataTables they reload the DataTables by appling a GET parameter to the search route used by DataTables;

Either way, the search will also work properly - it will search within the filtered elements.

Just like with fields, columns or buttons, you can add existing filters or create a custom filter that fits to your particular needs. Everything's done inside your ```EntityCrudController::setup()```. 

## Methods

```php
$this->crud->addFilter($options, $values, $filter_logic);

$this->crud->removeFilter($name);
$this->crud->removeAllFilters();

$this->crud->filters(); // gets all the filters
```

## Adding a filter

When adding a filter you need to specify the 3 parameters of the ```addFilter()``` method:
- $options - an array of options (name, type, label are most important)
- $values - filter values - can be an array or a closure
- $filter_logic - what should happen if the filter is applied (usually add a clause to the query) - can be a closure, a string for a simple operation or false for a simple "where";
[block:callout]
{
  "type": "warning",
  "title": "In case you updated Backpack to use Filters",
  "body": "If you have already published the list.blade.php file (it is in your ```resources/views/vendor/backpack/crud```), you need to delete and republish it."
}
[/block]
Here's a simple example, with comments that explain what we're doing:
```php
$this->crud->addFilter([ // add a "simple" filter called Draft 
  'type' => 'simple',
  'name' => 'draft',
  'label'=> 'Draft'
],
false, // the simple filter has no values, just the "Draft" label specified above
function() { // if the filter is active (the GET parameter "draft" exits)
    $this->crud->addClause('where', 'draft', '1'); 
    // we've added a clause to the CRUD so that only elements with draft=1 are shown in the table
    // an alternative syntax to this would have been
    // $this->crud->query = $this->crud->query->where('draft', '1'); 
    // another alternative syntax, in case you had a scopeDraft() on your model:
    // $this->crud->addClause('draft'); 
});
```
[block:callout]
{
  "type": "info",
  "title": "Notes about the filter logic closure",
  "body": "- the code will only be run on the controller's ```index()``` or ```search()``` methods;\n- you can get the filter value by specifying a parameter to the function (ex: ```$value```);\n- you have access to other request variables using ```$this->crud->request```;\n- you also have read/write access to public properties using ```$this->crud```;\n- when building complicated \"OR\" logic, make sure the first \"where\" in your closure is a \"where\" and only the subsequent are \"orWhere\"; Laravel 5.3 no longer convers the first \"orWhere\" into a \"where\";"
}
[/block]
## Filter types

### Simple

Only shows a label and can be toggled on/off. Useful for things like active/inactive and easily paired with [Eloquent Scopes](https://laravel.com/docs/5.3/eloquent#local-scopes). The "Draft" and "Has Video" filters in the screenshot above are simple filters.

```php
$this->crud->addFilter([ // simple filter
  'type' => 'simple',
  'name' => 'active',
  'label'=> 'Active'
], 
false, 
function() { // if the filter is active
    // $this->crud->addClause('active'); // apply the "active" eloquent scope 
} );
```

### Text

Shows a text input. Most useful for letting the user filter through information that not shown as a column in the CRUD table - otherwise they could just use the DataTables search field.
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/e236234-Screen_Shot_2017-09-04_at_12.26.32.png",
        "Screen Shot 2017-09-04 at 12.26.32.png",
        534,
        264,
        "#f2f2f2"
      ]
    }
  ]
}
[/block]
```php
$this->crud->addFilter([ // simple filter
  'type' => 'text',
  'name' => 'description',
  'label'=> 'Description'
], 
false, 
function($value) { // if the filter is active
    // $this->crud->addClause('where', 'description', 'LIKE', "%$value%");
} );
```

### Date

Show a datepicker. The user can select one day.
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/70b37df-Screen_Shot_2017-07-10_at_15.56.10.png",
        "Screen Shot 2017-07-10 at 15.56.10.png",
        666,
        678,
        "#f3f3f3"
      ]
    }
  ]
}
[/block]
```php
        $this->crud->addFilter([ // date filter
          'type' => 'date',
          'name' => 'date',
          'label'=> 'Date'
        ],
        false,
        function($value) { // if the filter is active, apply these constraints
          // $this->crud->addClause('where', 'date', '=', $value);
        });
```

### Date range

Show a daterange picker. The user can select a start date and an end date.
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/bd3b6e5-Screen_Shot_2017-07-10_at_15.56.28.png",
        "Screen Shot 2017-07-10 at 15.56.28.png",
        1440,
        820,
        "#246074"
      ]
    }
  ]
}
[/block]
```php
        $this->crud->addFilter([ // daterange filter
           'type' => 'date_range',
           'name' => 'from_to',
           'label'=> 'Date range'
         ],
         false,
         function($value) { // if the filter is active, apply these constraints
           // $dates = json_decode($value);
           // $this->crud->addClause('where', 'date', '>=', $dates->from);
           // $this->crud->addClause('where', 'date', '<=', $dates->to);
         });
```

### Dropdown

Shows a list of elements (that you provide) in a dropdown. The user can only select one of these elements.
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/1aa1b83-Screen_Shot_2016-11-10_at_16.39.01.png",
        "Screen Shot 2016-11-10 at 16.39.01.png",
        200,
        207,
        "#f1f1f1"
      ]
    }
  ]
}
[/block]
```php
$this->crud->addFilter([ // dropdown filter
  'name' => 'status',
  'type' => 'dropdown',
  'label'=> 'Status'
], [
  1 => 'In stock',
  2 => 'In provider stock',
  3 => 'Available upon ordering',
  4 => 'Not available',
], function($value) { // if the filter is active
    // $this->crud->addClause('where', 'status', $value);
});
```

### Select2

Shows a select2 and allows the user to select one item from the list or search for an item. Useful when the values list is long (over 10 elements).
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/73fadbc-Screen_Shot_2016-11-10_at_16.43.36.png",
        "Screen Shot 2016-11-10 at 16.43.36.png",
        314,
        258,
        "#555592"
      ]
    }
  ]
}
[/block]
```php
$this->crud->addFilter([ // select2 filter
  'name' => 'status',
  'type' => 'select2',
  'label'=> 'Status'
], function() {
    return [
            1 => 'In stock',
            2 => 'In provider stock',
            3 => 'Available upon ordering',
            4 => 'Not available',
            ];
}, function($value) { // if the filter is active
    // $this->crud->addClause('where', 'status', $value);
});
```

### Select2_multiple

Shows a select2 and allows the user to select one or more items from the list or search for an item. Useful when the values list is long (over 10 elements) and your user should be able to select multiple elements. You can decide yourself if the query for each element should use 'where' or 'orWhere', in the third parameter of the ```addFilter()``` method.
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/15a10fd-Screen_Shot_2016-11-10_at_16.48.10.png",
        "Screen Shot 2016-11-10 at 16.48.10.png",
        243,
        219,
        "#f1f1f1"
      ]
    }
  ]
}
[/block]
```php
$this->crud->addFilter([ // select2_multiple filter
  'name' => 'status',
  'type' => 'select2_multiple',
  'label'=> 'Status'
], function() {
    return [
            1 => 'In stock',
            2 => 'In provider stock',
            3 => 'Available upon ordering',
            4 => 'Not available',
            ];
}, function($values) { // if the filter is active
    // foreach (json_decode($values) as $key => $value) {
    //     $this->crud->addClause('where', 'published', $value);
    // }
});
```

### Select2_ajax

Shows a select2 and allows the user to select one item from the list or search for an item. This list is fetched through an AJAX call by the select2. Useful when the values list is long (over 1000 elements).
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/c092b95-Screen_Shot_2016-11-10_at_17.00.18.png",
        "Screen Shot 2016-11-10 at 17.00.18.png",
        252,
        177,
        "#f1f1f1"
      ]
    }
  ]
}
[/block]
1. Add a route for the ajax search, right above your usual ```CRUD::resource()``` route. Example:

```php
Route::get('test/ajax-category-options', 'TestCrudController@categoryOptions');
CRUD::resource('test', 'TestCrudController');
```

2. Add a method to your EntityCrudController that responds to a search term. The result should be an array with ```id => value```. Example for a 1-n relationship:

```php
public function categoryOptions() {
  $term = $this->request->input('term');
  $options = App\Models\Category::where('name', 'like', '%'.$term.'%')->get();
  return $options->pluck('name', 'id');
}
```

3. Add the select2_ajax filter and for the second parameter ("values") specify the exact route.

```php
$this->crud->addFilter([ // select2_ajax filter
  'name' => 'category_id',
  'type' => 'select2_ajax',
  'label'=> 'Category',
  'placeholder' => 'Pick a category'
],
url('admin/test/ajax-category-options'), // the ajax route
function($value) { // if the filter is active
    // $this->crud->addClause('where', 'category_id', $value);
});
```

### View

Display any custom column filter you want. Usually used by Backpack package developers, to use views from within their packages, instead of having to publish them. 

```php
$this->crud->addFilter([ // custom filter view
  'name' => 'category_id',
  'type' => 'view',
  'view' => 'package::columns.column_type_name', // or path to blade file
  'label'=> 'Category',
  'placeholder' => 'Pick a category',
],
false, 
function($value) { // if the filter is active
    // $this->crud->addClause('where', 'category_id', $value);
});
```

## Creating custom filters

Creating a new filter type is as easy as using the template below and placing a new view in your ```resources/views/vendor/backpack/crud/filters``` folder. You can then call this new filter by its view's name (ex: ```custom_select.blade.php``` will mean your filter type is called ```custom_select```).

The filters bar is actually a [bootstrap navbar](http://getbootstrap.com/components/#navbar) at its core, but slimmer.  So adding a new filter will be just like adding a menu item (for the HTML).
[block:code]
{
  "codes": [
    {
      "code": "{{-- Example Backpack CRUD filter --}}\n\n<li filter-name=\"{{ $filter->name }}\"\n\tfilter-type=\"{{ $filter->type }}\"\n\tclass=\"dropdown {{ Request::get($filter->name)?'active':'' }}\">\n    <a href=\"#\" class=\"dropdown-toggle\" data-toggle=\"dropdown\" role=\"button\" aria-haspopup=\"true\" aria-expanded=\"false\">{{ $filter->label }} <span class=\"caret\"></span></a>\n    <div class=\"dropdown-menu padding-10\">\n\n\t\t{{-- dropdown content: everything you need is inside $filter --}}\n\t\tLorem ipsum dolor sit amet, consectetur adipisicing elit. Unde, inventore assumenda voluptate accusantium recusandae ipsam magni atque vel omnis est debitis, neque nam aspernatur ex quo fuga, nulla soluta. Rerum.\n\n    </div>\n  </li>\n\n\n{{-- ########################################### --}}\n{{-- Extra CSS and JS for this particular filter --}}\n\n{{-- FILTERS EXTRA CSS  --}}\n{{-- push things in the after_styles section --}}\n\n    {{-- @push('crud_list_styles')\n        <!-- no css -->\n    @endpush --}}\n\n\n{{-- FILTERS EXTRA JS --}}\n{{-- push things in the after_scripts section --}}\n\n\n{{-- FILTER JAVASCRIPT CHECKLIST\n\n- redirects to a new URL for standard DataTables\n- replaces the search URL for ajax DataTables\n- users have a way to clear this filter (and only this filter)\n- filter:clear event on li[filter-name], which is called by the \"Remove all filters\" button, clears this filter;\n\nEND OF FILTER JAVSCRIPT CHECKLIST --}}\n\n@push('crud_list_scripts')\n    <script>\n\t\tjQuery(document).ready(function($) {\n\t\t\t$(\"li[filter-name={{ $filter->name }}] a\").click(function(e) {\n\t\t\t\te.preventDefault();\n\n\t\t\t\tvar parameter = $(this).attr('parameter');\n\n\t\t\t\t@if (!$crud->ajaxTable())\n\t\t\t\t\t// behaviour for normal table\n\t\t\t\t\tvar current_url = normalizeAmpersand(\"{{ Request::fullUrl() }}\");\n\n\t\t\t\t\tif (URI(current_url).hasQuery(parameter)) {\n\t\t\t\t\t\tvar new_url = URI(current_url).removeQuery(parameter, true);\n\t\t\t\t\t} else {\n\t\t\t\t\t\tvar new_url = URI(current_url).addQuery(parameter, true);\n\t\t\t\t\t}\n\n\t\t\t\t\t// refresh the page to the new_url\n\t\t\t    \tnew_url = normalizeAmpersand(new_url.toString());\n\t\t\t    \twindow.location.href = new_url.toString();\n\t\t\t    @else\n\t\t\t    \t// behaviour for ajax table\n\t\t\t\t\tvar ajax_table = $(\"#crudTable\").DataTable();\n\t\t\t\t\tvar current_url = ajax_table.ajax.url();\n\n\t\t\t\t\tif (URI(current_url).hasQuery(parameter)) {\n\t\t\t\t\t\tvar new_url = URI(current_url).removeQuery(parameter, true);\n\t\t\t\t\t} else {\n\t\t\t\t\t\tvar new_url = URI(current_url).addQuery(parameter, true);\n\t\t\t\t\t}\n\n\t\t\t\t\t// replace the datatables ajax url with new_url and reload it\n\t\t\t\t\tnew_url = normalizeAmpersand(new_url.toString());\n\t\t\t\t\tajax_table.ajax.url(new_url).load();\n\n\t\t\t\t\t// mark this filter as active in the navbar-filters\n\t\t\t\t\tif (URI(new_url).hasQuery('{{ $filter->name }}', true)) {\n\t\t\t\t\t\t$(\"li[filter-name={{ $filter->name }}]\").removeClass('active').addClass('active');\n\t\t\t\t\t}\n\t\t\t\t\telse\n\t\t\t\t\t{\n\t\t\t\t\t\t$(\"li[filter-name={{ $filter->name }}]\").trigger(\"filter:clear\");\n\t\t\t\t\t}\n\t\t\t    @endif\n\t\t\t});\n\n\t\t\t// clear filter event (used here and by the Remove all filters button)\n\t\t\t$(\"li[filter-name={{ $filter->name }}]\").on('filter:clear', function(e) {\n\t\t\t\t// console.log('dropdown filter cleared');\n\t\t\t\t$(\"li[filter-name={{ $filter->name }}]\").removeClass('active');\n\t\t\t});\n\t\t});\n\t</script>\n@endpush\n\n{{-- End of Extra CSS and JS --}}\n{{-- ########################################## --}}",
      "language": "php"
    }
  ]
}
[/block]
## Extra Examples

Use a dropdown to filter by the values of a MySQL ENUM column:

```php
$this->crud->addFilter([ // select2 filter
  'name' => 'published',
  'type' => 'select2',
  'label'=> 'Published'
], function() {
    return \App\Models\Test::getEnumValuesAsAssociativeArray('published');
}, function($value) { // if the filter is active
        $this->crud->addClause('where', 'published', $value);
});
```

Use a select2 to filter by a 1-n relationship:
```php
$this->crud->addFilter([ // select2 filter
  'name' => 'category_id',
  'type' => 'select2',
  'label'=> 'Category'
], function() {
    return \App\Models\Category::all()->pluck('name', 'id')->toArray();
}, function($value) { // if the filter is active
        $this->crud->addClause('where', 'category_id', $value);
});
```

Use a select2_multiple to filter Products by an n-n relationship:
```php
$this->crud->addFilter([ // select2_multiple filter
  'name' => 'tags',
  'type' => 'select2_multiple',
  'label'=> 'Tags'
], function() { // the options that show up in the select2
    return Product::all()->pluck('name', 'id')->toArray();
}, function($values) { // if the filter is active
    foreach (json_decode($values) as $key => $value) {
        $this->crud->query = $this->crud->query->whereHas('tags', function ($query) use ($value) {
            $query->where('tag_id', $value);
        });
    }
});
```
    
Use a simple filter to add a scope if the filter is active:
```php
$this->crud->addFilter([ // add a "simple" filter called Published 
  'type' => 'simple',
  'name' => 'published',
  'label'=> 'Published'
], 
false,
function() { // if the filter is active (the GET parameter "published" exits)
    $this->crud->addClause('published');
});
```

Use a simple filter to show the softDeleted items (trashed):
```php
        $this->crud->addFilter([
          'type' => 'simple',
          'name' => 'trashed',
          'label'=> 'Trashed'
        ],
        false,
        function($values) { // if the filter is active
            $this->crud->query = $this->crud->query->onlyTrashed();
        });
```