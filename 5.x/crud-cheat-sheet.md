# CRUD API Cheat Sheet

---

Here are all the functions you will be using **inside your EntityCrudController's ```setup()``` method**, grouped by the operation you will most likely use them for.

## Operations

<a name="list-entries-api"></a>
### ListEntries

<a name="columns-api"></a>
#### Columns

<small>Methods: addColumn(), addColumns(), modifyColumn(), removeColumn(), removeColumns(), setColumnDetails(), setColumnsDetails(), setColumns(), beforeColumn(), afterColumn(), makeFirstColumn()</small>

```php
// Manipulate what columns are shown in the table view.
$this->crud->addColumn($column_definition_array); // add a column, at the end of the stack
$this->crud->addColumns([$column_definition_array, $another_column_definition_array]); // add multiple columns, at the end of the stack
$this->crud->modifyColumn($name, $modifs_array);
$this->crud->removeColumn('column_name'); // remove a column from the stack
$this->crud->removeColumns(['column_name_1', 'column_name_2']); // remove an array of columns from the stack
$this->crud->setColumnDetails('column_name', ['attribute' => 'value']);
$this->crud->setColumnsDetails(['column_1', 'column_2'], ['attribute' => 'value']);

$this->crud->setColumns(); // set the columns you want in the table view, either as array of column names, or multidimensional array with all columns detailed with their types

// ------ REORDER COLUMNS
$this->crud->addColumn()->beforeColumn('name'); // will show this before the given column
$this->crud->addColumn()->afterColumn('name'); // will show this after the given column

$this->crud->addColumn()->makeFirstColumn(); 
  // will make this column the first one in the list
  // you need to also specify priority 1 in your addColumn statement for details_row or responsive expand buttons to show
```

<a name="buttons-api"></a>
#### Buttons

<small>Methods: buttons(), addButton(), addButtonFromModelFunction(), addButtonFromView(), removeButton(), removeButtonFromStack(), removeButtons(), removeAllButtons(), removeAllButtonsFromStack(), modifyButton(), moveButton()</small>

```php
// possible stacks: 'top', 'line', 'bottom';
// possible positions: 'beginning' and 'end'; defaults to 'beginning' for the 'line' stack, 'end' for the others;
$this->crud->buttons(); // collection of all buttons
$this->crud->addButton($stack, $name, $type, $content, $position); // possible types are: 'view', 'model_function'
$this->crud->addButtonFromModelFunction($stack, $name, $model_function_name, $position); // add a button whose HTML is returned by a method in the CRUD model
$this->crud->addButtonFromView($stack, $name, $view, $position); // add a button whose HTML is in a view placed at resources\views\vendor\backpack\crud\buttons
$this->crud->removeButton($name);
$this->crud->removeButtonFromStack($name, $stack);
$this->crud->removeButtons($names, $stack);
$this->crud->removeAllButtons();
$this->crud->removeAllButtonsFromStack($stack);
$this->crud->orderButtons($stack, $order); // order is an array with button names in the new order
$this->crud->modifyButton($name, $modifications); // modifications are the attributes and their new values
$this->crud->moveButton($target, $where, $destination); // move the target button to the destination position, target and destion are the button names, where is 'before' or 'after'
```

<a name="filters-api"></a>
#### Filters

<small>Methods: addFilter(), modifyFilter(), removeFilter(), removeAllFilters(), filters()</small>

```php
// Manipulate what filters are shown in the table view.
// 
// Note: check out CRUD > Features > Filters in the docs to see examples of $filter_definition_array
$this->crud->addFilter($filter_definition_array, $values, $filter_logic);
$this->crud->modifyFilter($name, $modifs_array);
$this->crud->removeFilter($name);
$this->crud->removeAllFilters();
$this->crud->filters(); // gets all the filters
```

<a name="details-row-api"></a>
#### Details Row

<small>Methods: enableDetailsRow(), disableDetailsRow()</small>

```php
// Shows a + sign next to each table row, so that the user can expand that row and reveal details. You are responsible for creating the view with those details.
$this->crud->enableDetailsRow();
// NOTE: you also need to do allow access to the right users: $this->crud->allowAccess('details_row');
// NOTE: you also need to do overwrite the showDetailsRow($id) method in your EntityCrudController to show whatever you'd like in the details row OR overwrite the views/backpack/crud/details_row.blade.php

$this->crud->disableDetailsRow();
```

<a name="export-buttons-api"></a>
#### Export Buttons

<small>Methods: enableExportButtons()</small>

```php
// Show export to PDF, CSV, XLS and Print buttons on the table view. Please note it will only export the current _page_ of results. So in order to export all entries the user needs to make the current page show "All" entries from the top-left picker.
$this->crud->enableExportButtons();
```

<a name="responsive-table-api"></a>
#### Responsive Table

<small>Methods: enableResponsiveTable(), disableResponsiveTable()</small>

```php
$this->crud->disableResponsiveTable();
$this->crud->enableResponsiveTable();
```

<a name="persistent-table-api"></a>
#### Persistent Table

<small>Methods: enablePersistentTable(), disablePersistentTable()</small>

```php
$this->crud->disablePersistentTable();
$this->crud->enablePersistentTable();
```

<a name="page-length"></a>
#### Page Length

<small>Methods: setDefaultPageLength(), setPageLengthMenu()</small>

```php
// you can define the default page length. If it does not exist we will add it to the pagination array.
$this->crud->setDefaultPageLength(10);

// you can configure the paginator shown to the user in various ways

// values and labels, 1st array the values, 2nd array the labels:
$this->crud->setPageLengthMenu([[100, 200, 300], ['one hundred', 'two hundred', 'three hundred']]); 

// values and labels in one array:
$this->crud->setPageLengthMenu([100 => 'one hundred', 200 => 'two hundred', 300 => 'three hundred']); 

// only values, we will use the values as labels:
$this->crud->setPageLengthMenu([100, 200, 300]); // OR
$this->crud->setPageLengthMenu([[100, 200, 300]]); 

// only one option available:
$this->crud->setPageLengthMenu(10); 
```

<small>NOTE: Do not use 0 as a key, if you want to represent "ALL" use -1 instead.</small>

<a name="action-column-api"></a>
#### Actions Column

<small>Methods: setActionsColumnPriority()</small>

```php
// make the actions column (in the table view) hide when not enough space is available, by giving it an unreasonable priority
$this->crud->setActionsColumnPriority(10000);
```

<a name="custom-queries-api"></a>
#### Custom / Advanced Queries

<small>Methods: addClause(), groupBy(), limit(), orderBy()</small>

```php
// Change what entries are shown in the table view.
// This changes all queries on the table view,
// as opposed to filters, who only change it when that filter is applied. 
$this->crud->addClause('active'); // apply local scope
$this->crud->addClause('type', 'car'); // apply local dynamic scope
$this->crud->addClause('where', 'name', '=', 'car');
$this->crud->addClause('whereName', 'car');
$this->crud->addClause('whereHas', 'posts', function($query) {
     $query->activePosts();
 });
$this->crud->groupBy();
$this->crud->limit();

// please note it's generally a good idea to use crud->orderBy() inside 
// "if (!$this->crud->getRequest()->has('order')) {}";
// that way, your custom order is applied ONLY IF the user hasn't forced another order (by clicking a column heading)
$this->crud->orderBy();
```

<a name="show-api"></a>
### Show

Use the same Columns API as for the ListEntries operation, but inside your ```show()``` method.

<a name="create-and-update-api"></a>
### Create & Update Operations

<small>Methods: addField(), addFields(), modifyField(), modifyFields(), removeField(), removeFields(), removeAllFields(), beforeField(), afterField() </small>

```php
// ------
// FIELDS
// ------
// Manipulate what fields are shown in the create / update forms.
// 
// Note: check out CRUD > Features > Field Types in the docs to see examples of $field_definition_array

$this->crud->addField($field_definition_array);
$this->crud->addField('db_column_name'); // a lazy way to add fields: let the CRUD decide what field type it is and set it automatically, along with the field label
$this->crud->addFields($array_of_fields_definition_arrays);
$this->crud->modifyField($name, $modifs_array);
$this->crud->removeField('name');
$this->crud->removeFields($array_of_names);
$this->crud->removeAllFields();

// ------ REORDER FIELDS
$this->crud->addField()->beforeField('name'); // will show this before the given field
$this->crud->addField()->afterField('name'); // will show this after the given field
```

<a name="reorder-api"></a>
### Reorder

<small>Methods: enableReorder(), disableReorder(), isReorderEnabled()</small>

```php
    protected function setupReorderOperation()
    {
    	// model attribute to be shown on draggable items
        $this->crud->set('reorder.label', 'name'); 
        // maximum number of nesting allowed
        $this->crud->set('reorder.max_level', 2);

        // extras:
		// $this->crud->disableReorder();
		// $this->crud->isReorderEnabled();
    }
```

<a name="revisions-api"></a>
### Revisions

```php
// -------------------------
// REVISIONS aka Audit Trail
// -------------------------
// Tracks all changes to an entry and provides an interface to revert to a previous state.
//
// IMPORTANT: You also need to use \Venturecraft\Revisionable\RevisionableTrait;
// Please check out: https://backpackforlaravel.com/docs/crud-operation-revisions
$this->crud->allowAccess('revisions');
```

<a name="all-operations-api"></a>
## All Operations

<small>Methods: allowAccess(), denyAccess(), hasAccess(), hasAccessOrFail(), hasAccessToAll(), hasAccessToAny(), setShowView(), setEditView(), setCreateView(), setListView(), setReorderView(), setRevisionsView, setRevisionsTimelineView(), setDetailsRowView(), getEntry(), getFields(), getColumns(), getCurrentEntry(), getTitle(), setTitle(), getHeading(), setHeading(), getSubheading(), setSubheading(), </small>

```php
// ------ 
// ACCESS
// ------
// Prevent or allow users from accessing different CRUD operations.

$this->crud->allowAccess('list');
$this->crud->allowAccess(['list', 'create', 'delete']);
$this->crud->denyAccess('list');
$this->crud->denyAccess(['list', 'create', 'delete']);

$this->crud->hasAccess('add'); // returns true/false
$this->crud->hasAccessOrFail('add'); // throws 403 error
$this->crud->hasAccessToAll(['create', 'update']); // returns true/false
$this->crud->hasAccessToAny(['create', 'update']); // returns true/false

// -------------
// EAGER LOADING
// -------------

// eager load a relationship
$this->crud->with('relationship_name');

// ------------
// CUSTOM VIEWS
// ------------

// use a custom view for a CRUD operation
$this->crud->setShowView('your-view');
$this->crud->setEditView('your-view');
$this->crud->setCreateView('your-view');
$this->crud->setListView('your-view');
$this->crud->setReorderView('your-view');
$this->crud->setRevisionsView('your-view');
$this->crud->setRevisionsTimelineView('your-view');
$this->crud->setDetailsRowView('your-view');

// -------------
// CONTENT CLASS
// -------------

// use a custom CSS class for the content of a CRUD operation
$this->crud->setShowContentClass('col-md-12');
$this->crud->setEditContentClass('col-md-12');
$this->crud->setCreateContentClass('col-md-12');
$this->crud->setListContentClass('col-md-12');
$this->crud->setReorderContentClass('col-md-12');
$this->crud->setRevisionsContentClass('col-md-12');
$this->crud->setRevisionsTimelineContentClass('col-md-12');

// -------
// GETTERS
// -------

$this->crud->getEntry($entry_id);
$this->crud->getEntries();

$this->crud->getFields('create/update/both');

// in your update() method, after calling parent::updateCrud()
$this->crud->getCurrentEntry();

// -------
// OPERATIONS
// -------

$this->crud->setOperation('list');
$this->crud->getOperation();

// -------
// ACTIONS
// -------

$this->crud->getActionMethod(); // returns the method on the controller that was called by the route; ex: create(), update(), edit() etc;
$this->crud->actionIs('create'); // checks if the controller method given is the one called by the route

$this->crud->getTitle('create'); // get the Title for the create action
$this->crud->getHeading('create'); // get the Heading for the create action
$this->crud->getSubheading('create'); // get the Subheading for the create action

$this->crud->setTitle('some string', 'create'); // set the Title for the create action
$this->crud->setHeading('some string', 'create'); // set the Heading for the create action
$this->crud->setSubheading('some string', 'create'); // set the Subheading for the create action

// ---------------------------
// CrudPanel Basic Information
// ---------------------------
$this->crud->setModel("App\Models\Example");
$this->crud->setRoute("admin/example");
// OR $this->crud->setRouteName("admin.example");
$this->crud->setEntityNameStrings("example", "examples");

// check the FormRequests used in that EntityCrudController for required fields, and add an asterisk to them in the create/edit form
$this->crud->setRequiredFields(StoreRequest::class, 'create');
$this->crud->setRequiredFields(UpdateRequest::class, 'edit');
```
