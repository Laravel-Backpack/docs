---
title: "CrudController API Cheat Sheet"
excerpt: ""
---
Here are all the functions you will need in order to build admin panels, sorted by how much you will most likely use them:
[block:api-header]
{
  "type": "basic",
  "title": "Always Used"
}
[/block]

[block:code]
{
  "codes": [
    {
      "code": "<?php\n\n$this->crud->setModel(\"App\\Models\\Example\");\n$this->crud->setRoute(\"admin/example\");\n// OR $this->crud->setRouteName(\"admin.example\");\n$this->crud->setEntityNameStrings(\"example\", \"examples\");\n\n// check the FormRequests used in that EntityCrudController for required fields, and add an asterisk to them in the create/edit form\n$this->crud->setRequiredFields(StoreRequest::class, 'create');\n$this->crud->setRequiredFields(UpdateRequest::class, 'edit');\n\n$this->crud->setFromDb();  \n   // automatically sets fields and columns from your database columns;\n   // this method will be deprecated in a near release\n   // so please try not to use it;\n   // best to use addField() and addColumn() below to manually\n   // define what fields and columns your admins should see;",
      "language": "php"
    }
  ]
}
[/block]

[block:api-header]
{
  "type": "basic",
  "title": "Often Used"
}
[/block]

[block:code]
{
  "codes": [
    {
      "code": "<?php\n\n// -------\n// COLUMNS\n// -------\n// Manipulate what columns are shown in the table view.\n// \n// Note: check out CRUD > Features > Column Types in the docs to see examples of $column_definition_array\n\n$this->crud->addColumn($column_definition_array); // add a single column, at the end of the stack\n$this->crud->addColumns([$column_definition_array, $another_column_definition_array]); // add multiple columns, at the end of the stack\n$this->crud->modifyColumn($name, $modifs_array);\n$this->crud->removeColumn('column_name'); // remove a column from the stack\n$this->crud->removeColumns(['column_name_1', 'column_name_2']); // remove an array of columns from the stack\n$this->crud->setColumnDetails('column_name', ['attribute' => 'value']);\n$this->crud->setColumnsDetails(['column_1', 'column_2'], ['attribute' => 'value']);\n\n$this->crud->setColumns(); // set the columns you want in the table view, either as array of column names, or multidimensional array with all columns detailed with their types\n\n// ------ REORDER COLUMNS\n$this->crud->addColumn()->beforeColumn('name'); // will show this before the given column\n$this->crud->addColumn()->afterColumn('name'); // will show this after the given column\n\n$this->crud->addColumn()->makeFirstColumn(); \n  // will make this column the first one in the list\n  // you need to also specify priority 1 in your addColumn statement for details_row or responsive expand buttons to show\n\n// ------\n// FIELDS\n// ------\n// Manipulate what fields are shown in the create / update forms.\n// \n// Note: check out CRUD > Features > Field Types in the docs to see examples of $field_definition_array\n\n$this->crud->addField($field_definition_array, 'update/create/both');\n$this->crud->addField('db_column_name', 'update/create/both'); // a lazy way to add fields: let the CRUD decide what field type it is and set it automatically, along with the field label\n$this->crud->addFields($array_of_fields_definition_arrays, 'update/create/both');\n$this->crud->modifyField($name, $modifs_array, 'update/create/both');\n$this->crud->removeField('name', 'update/create/both');\n$this->crud->removeFields($array_of_names, 'update/create/both');\n$this->crud->removeAllFields();\n\n// Note: the last parameter is always the form - create or update; if missing, it's assumed 'both';\n\n\n// ------ REORDER FIELDS\n$this->crud->addField()->beforeField('name'); // will show this before the given field\n$this->crud->addField()->afterField('name'); // will show this after the given field\n\n// ------ CRUD BUTTONS\n// possible positions: 'beginning' and 'end'; defaults to 'beginning' for the 'line' stack, 'end' for the others;\n$this->crud->addButton($stack, $name, $type, $content, $position); // add a button; possible types are: view, model_function\n$this->crud->addButtonFromModelFunction($stack, $name, $model_function_name, $position); // add a button whose HTML is returned by a method in the CRUD model\n$this->crud->addButtonFromView($stack, $name, $view, $position); // add a button whose HTML is in a view placed at resources\\views\\vendor\\backpack\\crud\\buttons\n$this->crud->removeButton($name);\n$this->crud->removeButtonFromStack($name, $stack);\n\n\n// -------\n// FILTERS\n// -------\n// Manipulate what filters are shown in the table view.\n// \n// Note: check out CRUD > Features > Filters in the docs to see examples of $filter_definition_array\n$this->crud->addFilter($filter_definition_array, $values, $filter_logic);\n$this->crud->modifyFilter($name, $modifs_array);\n$this->crud->removeFilter($name);\n$this->crud->removeAllFilters();\n$this->crud->filters(); // gets all the filters\n\n\n// ------ \n// ACCESS\n// ------\n// Prevent or allow users from accessing different CRUD operations.\n\n$this->crud->allowAccess('list');\n$this->crud->allowAccess(['list', 'create', 'delete']);\n$this->crud->denyAccess('list');\n$this->crud->denyAccess(['list', 'create', 'delete']);\n\n$this->crud->hasAccess('add'); // returns true/false\n$this->crud->hasAccessOrFail('add'); // throws 403 error\n$this->crud->hasAccessToAll(['create', 'update']); // returns true/false\n$this->crud->hasAccessToAny(['create', 'update']); // returns true/false\n\n\n// --------------\n// REORDER & NEST\n// --------------\n// Show a reorder button in the table view, next to Add\n// Provide an interface to reorder & nest elements, provided the parent_id, lft, rgt, depth columns are in the database, and fillable on the model.\n$this->crud->enableReorder('label_name', 3);\n// NOTE: the second parameter is the maximum nesting depth; this example will prevent the user from creating trees deeper than 3 levels;\n// NOTE: you also need to do allow access to the right users: $this->crud->allowAccess('reorder');\n\n$this->crud->disableReorder();\n$this->crud->isReorderEnabled(); // return true/false\n\n// -----------\n// DETAILS ROW\n// -----------\n// Shows a + sign next to each table row, so that the user can expand that row and reveal details. You are responsible for creting the view with those details.\n$this->crud->enableDetailsRow();\n// NOTE: you also need to do allow access to the right users: $this->crud->allowAccess('details_row');\n// NOTE: you also need to do overwrite the showDetailsRow($id) method in your EntityCrudController to show whatever you'd like in the details row OR overwrite the views/backpack/crud/details_row.blade.php\n\n$this->crud->disableDetailsRow();\n\n// -------------------------\n// REVISIONS aka Audit Trail\n// -------------------------\n// Tracks all changes to an entry and provides an interface to revert to a previous state.\n//\n// IMPORTANT: You also need to use \\Venturecraft\\Revisionable\\RevisionableTrait;\n// Please check out: https://laravel-backpack.readme.io/docs/crud#revisions\n$this->crud->allowAccess('revisions');\n\n// ------------------------\n// DATATABLE EXPORT BUTTONS\n// ------------------------\n// Show export to PDF, CSV, XLS and Print buttons on the table view. Please note it will only export the current _page_ of results. So in order to export all entries the user needs to make the current page show \"All\" entries from the top-left picker.\n$this->crud->enableExportButtons();\n\n// other LIST VIEW options\n$this->crud->disableResponsiveTable();\n$this->crud->enableResponsiveTable();\n\n// ----------------\n// ADVANCED QUERIES\n// ----------------\n// Change what entries are show in the table view.\n// This changes all queries on the table view,\n// as opposed to filters, who only change it when that filter is applied. \n$this->crud->addClause('active'); // apply local scope\n$this->crud->addClause('type', 'car'); // apply local dynamic scope\n$this->crud->addClause('where', 'name', '=', 'car');\n$this->crud->addClause('whereName', 'car');\n$this->crud->addClause('whereHas', 'posts', function($query) {\n     $query->activePosts();\n });\n$this->crud->groupBy();\n$this->crud->limit();\n\n$this->crud->orderBy();\n// please note it's generally a good idea to use crud->orderBy() inside \"if (!$this->request->has('order')) {}\"; that way, your custom order is applied ONLY IF the user hasn't forced another order (by clicking a column heading)",
      "language": "php"
    }
  ]
}
[/block]

[block:api-header]
{
  "type": "basic",
  "title": "Sometimes Used"
}
[/block]

[block:code]
{
  "codes": [
    {
      "code": "<?php\n\n$this->crud->setDefaultPageLength(10); // number of rows shown in list view\n$this->crud->setPageLengthMenu([100, 200, 300]); // page length menu to show in the list view\n$this->crud->addButton($stack, $name, $type, $content); // add a button; possible types are: view, model_function\n$this->crud->addButtonFromModelFunction($stack, $name, $model_function_name); // add a button whose HTML is returned by a method in the CRUD model\n$this->crud->addButtonFromView($stack, $name, $view); // add a button whose HTML is in a view placed at resources\\views\\vendor\\backpack\\crud\\buttons\n$this->crud->removeButton($name);\n$this->crud->removeButtonFromStack($name, $stack);\n\n// eager load a relationship\n$this->crud->with('relationship_name');\n\n// use a custom view for a CRUD operation\n$this->crud->setShowView('your-view');\n$this->crud->setEditView('your-view');\n$this->crud->setCreateView('your-view');\n$this->crud->setListView('your-view');\n$this->crud->setReorderView('your-view');\n$this->crud->setRevisionsView('your-view');\n$this->crud->setRevisionsTimelineView('your-view');\n$this->crud->setDetailsRowView('your-view');",
      "language": "php"
    }
  ]
}
[/block]

[block:api-header]
{
  "title": "Rarely Used"
}
[/block]

[block:code]
{
  "codes": [
    {
      "code": "<?php\n\n$this->crud->getEntry($entry_id);\n$this->crud->getEntries();\n\n$this->crud->getFields('create/update/both');\n\n// in your update() method, after calling parent::updateCrud()\n$this->crud->getCurrentEntry();\n\n// make the cctions column (in the table view) hide when not enough space is available, by giving it an unreasonable priority\n$this->crud->setActionsColumnPriority(10000);\n\n// -------\n// ACTIONS\n// -------\n\n$this->crud->getActionMethod(); // returns the method on the controller that was called by the route; ex: create(), update(), edit() etc;\n$this->crud->actionIs('create'); // checks if the controller method given is the one called by the route",
      "language": "php"
    }
  ]
}
[/block]