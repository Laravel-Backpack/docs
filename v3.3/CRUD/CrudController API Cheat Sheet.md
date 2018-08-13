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
      "code": "<?php\n\n$this->crud->setModel(\"App\\Models\\Example\");\n$this->crud->setRoute(\"admin/example\");\n// OR $this->crud->setRouteName(\"admin.example\");\n$this->crud->setEntityNameStrings(\"example\", \"examples\");\n\n$this->crud->setFromDb();  // automatically set fields and columns from your database columns  TODO: rephrase",
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
      "code": "<?php\n\n// ------ FIELDS (the last parameter is always the form - create/update/both)\n$this->crud->addField($field_definition_array, 'update/create/both');\n$this->crud->addField('db_column_name', 'update/create/both'); // a lazy way to add fields: let the CRUD decide what field type it is and set it automatically, along with the field label\n$this->crud->addFields($array_of_fields_definition_arrays, 'update/create/both');\n$this->crud->removeField('name', 'update/create/both');\n$this->crud->removeFields($array_of_names, 'update/create/both');\n\n\n// ------ COLUMNS\n$this->crud->addColumn(); // add a single column, at the end of the stack\n$this->crud->addColumns(); // add multiple columns, at the end of the stack\n$this->crud->removeColumn('column_name'); // remove a column from the stack\n$this->crud->removeColumns(['column_name_1', 'column_name_2']); // remove an array of columns from the stack\n$this->crud->setColumnDetails('column_name', ['attribute' => 'value']);\n$this->crud->setColumnsDetails(['column_1', 'column_2'], ['attribute' => 'value']);\n\n$this->crud->setColumns(); // set the columns you want in the table view, either as array of column names, or multidimensional array with all columns detailed with their types\n\n\n// ------ REORDER FIELDS\n$this->crud->addField()->beforeField('name'); // will show this before the given field\n$this->crud->addField()->afterField('name'); // will show this after the given field\n\n// ------ REORDER COLUMNS\n$this->crud->addColumn()->beforeColumn('name'); // will show this before the given column\n$this->crud->addColumn()->afterColumn('name'); // will show this after the given column\n\n\n// ------ CRUD BUTTONS\n// possible positions: 'beginning' and 'end'; defaults to 'beginning' for the 'line' stack, 'end' for the others;\n$this->crud->addButton($stack, $name, $type, $content, $position); // add a button; possible types are: view, model_function\n$this->crud->addButtonFromModelFunction($stack, $name, $model_function_name, $position); // add a button whose HTML is returned by a method in the CRUD model\n$this->crud->addButtonFromView($stack, $name, $view, $position); // add a button whose HTML is in a view placed at resources\\views\\vendor\\backpack\\crud\\buttons\n$this->crud->removeButton($name);\n$this->crud->removeButtonFromStack($name, $stack);\n\n\n// ------ ACCESS\n$this->crud->allowAccess('list');\n$this->crud->allowAccess(['list', 'create', 'delete']);\n$this->crud->denyAccess('list');\n$this->crud->denyAccess(['list', 'create', 'delete']);\n\n$this->crud->hasAccess('add'); // returns true/false\n$this->crud->hasAccessOrFail('add'); // throws 403 error\n$this->crud->hasAccessToAll(['create', 'update']); // returns true/false\n$this->crud->hasAccessToAny(['create', 'update']); // returns true/false\n\n\n// ------ REORDER\n$this->crud->enableReorder('label_name', MAX_TREE_LEVEL);\n// NOTE: you also need to do allow access to the right users: $this->crud->allowAccess('reorder');\n\n$this->crud->disableReorder();\n$this->crud->isReorderEnabled(); // return true/false\n\n\n// ------ DETAILS ROW\n$this->crud->enableDetailsRow();\n// NOTE: you also need to do allow access to the right users: $this->crud->allowAccess('details_row');\n// NOTE: you also need to do overwrite the showDetailsRow($id) method in your EntityCrudController to show whatever you'd like in the details row OR overwrite the views/backpack/crud/details_row.blade.php\n\n$this->crud->disableDetailsRow();\n\n// ------ REVISIONS\n// You also need to use \\Venturecraft\\Revisionable\\RevisionableTrait;\n// Please check out: https://laravel-backpack.readme.io/docs/crud#revisions\n$this->crud->allowAccess('revisions');\n\n// ------ DATATABLE EXPORT BUTTONS\n// Show export to PDF, CSV, XLS and Print buttons on the table view.\n// Does not work well with AJAX datatables.\n$this->crud->enableExportButtons();\n\n// ------ ADVANCED QUERIES\n$this->crud->addClause('active');\n$this->crud->addClause('type', 'car');\n$this->crud->addClause('where', 'name', '=', 'car');\n$this->crud->addClause('whereName', 'car');\n$this->crud->addClause('whereHas', 'posts', function($query) {\n     $query->activePosts();\n });\n$this->crud->orderBy();\n$this->crud->groupBy();\n$this->crud->limit();",
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
  "type": "basic",
  "title": "Rarely Used"
}
[/block]

[block:code]
{
  "codes": [
    {
      "code": "<?php\n\n$this->crud->getEntry($entry_id);\n$this->crud->getEntries();\n\n$this->crud->getFields('create/update/both');\n\n$this->crud->create($entry_request);\n$this->crud->update($entry_id, $entry_request);\n$this->crud->delete($entry_id);",
      "language": "php"
    }
  ]
}
[/block]

[block:api-header]
{
  "type": "basic",
  "title": "Almost Never Used"
}
[/block]

[block:code]
{
  "codes": [
    {
      "code": "<?php\n\n$this->crud->updateTreeOrder($all_entries);",
      "language": "php"
    }
  ]
}
[/block]