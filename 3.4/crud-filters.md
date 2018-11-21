# Filters

---

<a name="about"></a>
## About

Backpack CRUD allows you to show a filters bar right above the entries table. When selected or modified, they reload the DataTables results. The search will then search within the filtered elements.

![Backpack CRUD Filters](https://backpackforlaravel.com/uploads/docs/filters/filters.png)

Just like with fields, columns or buttons, you can add existing filters or create a custom filter that fits to your particular needs. Everything's done inside your ```EntityCrudController::setup()```. 

<a name="methods"></a>
### Filters API

In order to manipulate filters, you can use:

```php
$this->crud->addFilter($options, $values, $filter_logic);

$this->crud->removeFilter($name);
$this->crud->removeAllFilters();

$this->crud->filters(); // gets all the filters
```

<a name="adding-a-filter"></a>
### Adding a filter

When adding a filter you need to specify the 3 parameters of the ```addFilter()``` method:
- $options - an array of options (name, type, label are most important)
- $values - filter values - can be an array or a closure
- $filter_logic - what should happen if the filter is applied (usually add a clause to the query) - can be a closure, a string for a simple operation or false for a simple "where";

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
> Notes about the filter logic closure
> - the code will only be run on the controller's ```index()``` or ```search()``` methods;
> - you can get the filter value by specifying a parameter to the function (ex: ```$value```);
> - you have access to other request variables using ```$this->crud->request```;
> - you also have read/write access to public properties using ```$this->crud```;
> - when building complicated "OR" logic, make sure the first "where" in your closure is a "where" and only the subsequent are "orWhere"; Laravel 5.3+ no longer convers the first "orWhere" into a "where";

<a name="filter-types"></a>
## Filter types

<a name="simple"></a>
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

<a name="text"></a>
### Text

Shows a text input. Most useful for letting the user filter through information that not shown as a column in the CRUD table - otherwise they could just use the DataTables search field.

![Backpack CRUD Text Filter](https://backpackforlaravel.com/uploads/docs/filters/text.png)

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

<a name="date"></a>
### Date

Show a datepicker. The user can select one day.

![Backpack CRUD Date Filter](https://backpackforlaravel.com/uploads/docs/filters/date.png)

```php
        $this->crud->addFilter([ // date filter
          'type' => 'date',
          'name' => 'date',
          'label'=> 'Date'
        ],
        false,
        function($value) { // if the filter is active, apply these constraints
          // $this->crud->addClause('where', 'date', $value);
        });
```

<a name="date-range"></a>
### Date range

Show a daterange picker. The user can select a start date and an end date.

![Backpack CRUD Date Range Filter](https://backpackforlaravel.com/uploads/docs/filters/date_range.png)

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
           // $this->crud->addClause('where', 'date', '<=', $dates->to . ' 23:59:59');
         });
```

<a name="dropdown"></a>
### Dropdown

Shows a list of elements (that you provide) in a dropdown. The user can only select one of these elements.

![Backpack CRUD Dropdown Filter](https://backpackforlaravel.com/uploads/docs/filters/dropdown.png)

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

<a name="select2"></a>
### Select2

Shows a select2 and allows the user to select one item from the list or search for an item. Useful when the values list is long (over 10 elements).

![Backpack CRUD Select2 Filter](https://backpackforlaravel.com/uploads/docs/filters/select2.png)

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

**Note:** If you want to pass all entries of a Laravel model to your filter, you can do it in the second parameter (the closure), with something like ```return \Backpack\NewsCRUD\app\Models\Category::all()->keyBy('id')->pluck('name', 'id')->toArray();```;

<a name="select2_multiple"></a>
### Select2_multiple

Shows a select2 and allows the user to select one or more items from the list or search for an item. Useful when the values list is long (over 10 elements) and your user should be able to select multiple elements. You can decide yourself if the query for each element should use 'where' or 'orWhere', in the third parameter of the ```addFilter()``` method.

![Backpack CRUD Select2_multiple Filter](https://backpackforlaravel.com/uploads/docs/filters/select2_multiple.png)

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

<a name="select2_ajax"></a>
### Select2_ajax

Shows a select2 and allows the user to select one item from the list or search for an item. This list is fetched through an AJAX call by the select2. Useful when the values list is long (over 1000 elements).

![Backpack CRUD Select2_ajax Filter](https://backpackforlaravel.com/uploads/docs/filters/select2_ajax.png)

1. Add a route for the ajax search, right above your usual ```CRUD::resource()``` route. Example:

```php
Route::get('test/ajax-category-options', 'TestCrudController@categoryOptions');
CRUD::resource('test', 'TestCrudController');
```

2. Add a method to your EntityCrudController that responds to a search term. The result should be an array with ```id => value```. Example for a 1-n relationship:

```php
public function categoryOptions(Request $request) {
  $term = $request->input('term');
  $options = App\Models\Category::where('name', 'like', '%'.$term.'%')->get()->pluck('name', 'id');
  return $options;
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

<a name="range"></a>
### Range

Shows two number inputs, for min and max.

![Backpack CRUD Range Filter](https://backpackforlaravel.com/uploads/docs/filters/range.png)

```php
$this->crud->addFilter([
  'name' => 'number',
  'type' => 'range',
  'label'=> 'Range',
  'label_from' => 'min value',
  'label_to' => 'max value'
],
false,
function($value) { // if the filter is active
            $range = json_decode($value);
            if ($range->from) {
                $this->crud->addClause('where', 'number', '>=', (float) $range->from);
            }
            if ($range->to) {
                $this->crud->addClause('where', 'number', '<=', (float) $range->to);
            }
});
```
<a name="view"></a>
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

<a name="creating-a-custom-filter-type"></a>
## Creating custom filters

Creating a new filter type is as easy as using the template below and placing a new view in your ```resources/views/vendor/backpack/crud/filters``` folder. You can then call this new filter by its view's name (ex: ```custom_select.blade.php``` will mean your filter type is called ```custom_select```).

The filters bar is actually a [bootstrap navbar](http://getbootstrap.com/components/#navbar) at its core, but slimmer.  So adding a new filter will be just like adding a menu item (for the HTML). Start from the ```text``` filter below and build your functionality.

Inside this file, you'll have:
- ```$filter``` object - includes everything you've defined on the current field;
- ```$crud``` - the CrudPanel object;

```php
{{-- Text Backpack CRUD filter --}}

<li filter-name="{{ $filter->name }}"
  filter-type="{{ $filter->type }}"
  class="dropdown {{ Request::get($filter->name) ? 'active' : '' }}">
  <a href="#" class="dropdown-toggle" data-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false">{{ $filter->label }} <span class="caret"></span></a>
  <div class="dropdown-menu">
    <div class="form-group backpack-filter m-b-0">
      <div class="input-group">
            <input class="form-control pull-right"
                id="text-filter-{{ str_slug($filter->name) }}"
                type="text"
            @if ($filter->currentValue)
              value="{{ $filter->currentValue }}"
            @endif
                >
            <div class="input-group-addon">
              <a class="text-filter-{{ str_slug($filter->name) }}-clear-button" href=""><i class="fa fa-times"></i></a>
            </div>
        </div>
    </div>
  </div>
</li>

{{-- ########################################### --}}
{{-- Extra CSS and JS for this particular filter --}}


{{-- FILTERS EXTRA JS --}}
{{-- push things in the after_scripts section --}}

@push('crud_list_scripts')
  <!-- include select2 js-->
  <script>
    jQuery(document).ready(function($) {
      $('#text-filter-{{ str_slug($filter->name) }}').on('change', function(e) {

        var parameter = '{{ $filter->name }}';
        var value = $(this).val();

          // behaviour for ajax table
        var ajax_table = $('#crudTable').DataTable();
        var current_url = ajax_table.ajax.url();
        var new_url = addOrUpdateUriParameter(current_url, parameter, value);

        // replace the datatables ajax url with new_url and reload it
        new_url = normalizeAmpersand(new_url.toString());
        ajax_table.ajax.url(new_url).load();

        // mark this filter as active in the navbar-filters
        if (URI(new_url).hasQuery('{{ $filter->name }}', true)) {
          $('li[filter-name={{ $filter->name }}]').removeClass('active').addClass('active');
        } else {
          $('li[filter-name={{ $filter->name }}]').trigger('filter:clear');
        }
      });

      $('li[filter-name={{ str_slug($filter->name) }}]').on('filter:clear', function(e) {
        $('li[filter-name={{ $filter->name }}]').removeClass('active');
        $('#text-filter-{{ str_slug($filter->name) }}').val('');
      });

      // datepicker clear button
      $(".text-filter-{{ str_slug($filter->name) }}-clear-button").click(function(e) {
        e.preventDefault();

        $('li[filter-name={{ str_slug($filter->name) }}]').trigger('filter:clear');
        $('#text-filter-{{ str_slug($filter->name) }}').val('');
        $('#text-filter-{{ str_slug($filter->name) }}').trigger('change');
      })
    });
  </script>
@endpush
{{-- End of Extra CSS and JS --}}
{{-- ########################################## --}}
```

<a name="examples"></a>
## Examples

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
