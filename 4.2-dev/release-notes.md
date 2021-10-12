# Release Notes

---

**Launch date:** May 5th, 2020

Anybody with a 4.0 license can install and use 4.1, but in order to get the new features in a project that currently runs 4.0, you need to [follow the upgrade guide](/docs/{{version}}/upgrade-guide). Don't get fooled by the fact that we kept the 4.x.x prefix - that's just to show that **Backpack 4.1 is a FREE upgrade from 4.0**. But **Backpack 4.1 is a MAJOR new version**, with over 6 months of work put into it, and major improvements under-the-hood too. It is the current and recommended version of Backpack and it's got so many cool new things that we couldn't fit them all inside this page.

But here's the big stuff Backpack 4.1 brings to the table, and why you should upgrade from [Backpack 4.0](/docs/4.0) to 4.1.


<a name="added"></a>
## Added

### Operations

#### **New operation: ```InlineCreate```** 

- allows admins to create related items on-the-fly, without leaving the current form;
- shows a new button next to your relationship fields; when you click [+ Add], a modal is shown with all the fields needed to create a related item on-the-fly; it basically brings that entry's Create form on this page;
- it works well for entities with a few inputs, but it also works well for huge entities with dozens of inputs, tabs, etc;
- see [docs](https://backpackforlaravel.com/docs/4.1/crud-operation-inline-create), [PR](https://github.com/Laravel-Backpack/CRUD/pull/2311), [demo](https://demo.backpackforlaravel.com/admin/monster)

![Backpack InlineCreate Operation](https://backpackforlaravel.com/uploads/docs-4-1/release_notes/inline_create_small.gif)

<hr>

#### **New operation: ```Fetch```** 

- responds to AJAX requests and returns the results in a format the Select2 likes;
- this operation makes adding AJAX selects much MUCH easier; you don't even have to leave the controller;
- see [docs](/docs/4.1/crud-operation-fetch), [PR](https://github.com/Laravel-Backpack/CRUD/pull/2308)

![Backpack Fetch Operation](https://backpackforlaravel.com/uploads/docs-4-1/release_notes/fetch.png)

<hr>

### Fields

#### **New field type: ```relationship```** 

- **one relationship field to rule them all**; instead of thinking whether you need ```select2```, ```select2_multiple```, ```select2_from_ajax``` or ```select2_from_ajax_multiple``` you can now just use the ```relationship``` field, that includes the functionality of all of them... and more;
- **intelligent defaults**; it automatically figures out the ```entity```, ```model```, ```attribute```, ```multiple```, ```pivot``` and  ```label```, so most of the time you'll just need to define two things for it to work - ```name``` and ```type```;
- **supports both non-AJAX and AJAX**; for the dropdown options, it defaults to immediate querying; if you want to load the options using AJAX, you just have to point it to the correct route, using ```data_source```; which is super-easy to do using the new Fetch operation detailed above;
- see [docs](/docs/4.1/crud-fields#relationship), [PR](https://github.com/Laravel-Backpack/CRUD/pull/2311), [demo](https://demo.backpackforlaravel.com/admin/monster)

![Backpack Relationship Field](https://backpackforlaravel.com/uploads/docs-4-1/release_notes/relationship.png)

<hr>

#### **New field type: ```repeatable```** (aka ```matrix```, aka ```groups```, aka ```multiply```) 

- **lets your admin define one or multiple "_somethings_" right inside their create/update form**, when a "_something_" includes more than one input (ex: Testimonial can require ```name```, ```position```, ```company``` and ```quote```);
- the end result is stored as JSON by default, but you can intercept the saving and store it any way you like;
- makes it dead-simple to allow your admins to add/edit/delete multiple "things" inside an entry, when that "thing" is not important enough to have its own database table and Model;
- see [docs](/docs/4.1/crud-fields#repeatable), [PR](https://github.com/Laravel-Backpack/CRUD/pull/2266)

![Backpack InlineCreate Operation](https://backpackforlaravel.com/uploads/docs-4-1/release_notes/repeatable_small.gif)

<hr>

#### **New field type: ```easymde```** (alternative to ```simplemde```) 

- SimpleMDE (the JS library) still works, but has not received any updates in 4 years; that's why we recommend using a different markdown editor - one that is maintained;
- fortunately, EasyMDE is a SimpleMDE fork that looks and works the same, and it is well looked after;
- we recommend replacing all your SimpleMDE fields with EasyMDE - the change couldn't be simpler;
- see [docs](/docs/4.1/crud-fields#easymde), [PR](https://github.com/Laravel-Backpack/CRUD/pull/2737)


![Backpack InlineCreate Operation](https://backpackforlaravel.com/uploads/docs-4-1/fields/easymde.png)

<hr>
### Columns

#### **New columns feature: ```wrapper```** 

- allows you to **change how columns look and feel, by wrapping the text into a custom HTML element**; similarly to how we've been able to do for years with Fields, you can now specify a wrapper for columns, and change that wrapper's ```class```, ```style```, etc.
- you can now **easily add links to your columns**; make it easy for admins to jump from one CRUD to another, by making relationship columns point to that Model's CRUD; but they can also point to whatever else you want;
- you can now **easily make your column text a different color depending on its content**, just by adding a Bootstrap class (ex: add ```badge badge-warning``` or ```text-success```);
- see [docs](/docs/{{version}}/crud-columns#wrap-column-text-in-an-html-element), [PR](https://github.com/Laravel-Backpack/CRUD/pull/2448), [demo](https://demo.backpackforlaravel.com/admin/article)


![Backpack Column Wrapper](https://backpackforlaravel.com/uploads/docs-4-1/release_notes/column_wrapper_small.gif)

<hr> 

#### **New column: ```relationship```** 

- the perfect companion to the new ```relationship``` field;
- **intelligent defaults**; it automatically figures out the ```entity```, ```model```, ```attribute```, ```multiple```, ```pivot``` and  ```label```, so most of the time you'll just need to define two things for it to work - ```name``` and ```type```;
- see [docs](/docs/{{version}}/crud-columns#relationship), [PR](https://github.com/Laravel-Backpack/CRUD/pull/2615), [demo](https://demo.backpackforlaravel.com/admin/monster)


<hr> 

#### **New column: ```relationship_count```** 

- shows the number of items related to the current entry, on that relationship, for example "413 items";
- perfect for when your entity points to A LOT of other entries;
- if you have a CRUD with Filters for that related Model, you can add the `wrapper` attribute mentioned above to this column, to turn it into a link that points to a filtered view of that related CRUD; that way the admin can click this column and see those 413 items;
- see [docs](/docs/{{version}}/crud-columns#relationship_count), [PR](https://github.com/Laravel-Backpack/CRUD/pull/2615), [demo](https://demo.backpackforlaravel.com/admin/monster)


<hr> 

### Widgets

#### **New widget: ```chart```** 

- easily add a widget with a chart, on any admin panel page, directly from your Controller or blade file;
- the same/similar syntax for multiple chart libraries: ChartJS, Highcharts, Fusioncharts, Echarts, Frappe, C3, thanks to [Laravel Charts](https://charts.erik.cat/); easily switch between charting libraries;
- easily defer DB queries to an AJAX call (recommended) or make them upon page load;   
- see [docs](/docs/4.1/base-widgets#chart), [PR](https://github.com/Laravel-Backpack/CRUD/pull/2596), [demo](https://demo.backpackforlaravel.com/admin/)


![Backpack Chart Widget](https://backpackforlaravel.com/uploads/docs-4-1/release_notes/chart_widget_small.gif)

<hr>

#### **New ```Widgets``` class** 

- a global object that allows you to more easily add/edit Widgets to your admin panel pages;
- you can still manipulate the ```$data['widgets']``` directly, but you can also use this new class;
- you can pass the entire widget definition array to ```Widget::add()``` or you can use the new fluent syntax;
- see [docs](/docs/4.1/base-widgets#widgets-api), [PR](https://github.com/Laravel-Backpack/CRUD/pull/2599), [demo](https://demo.backpackforlaravel.com/admin/)


![Backpack Widget Class](https://backpackforlaravel.com/uploads/docs-4-1/release_notes/widget_class.png)

<hr>

### One more thing

#### **Alternative fluent syntax for fields, columns, filters, buttons** 

- An optional new way of interacting with Fields, Columns, Filters, Buttons;
- **Add or modify. It's the same method.** Create or modify a field/column/filter/button/widget with one call - think of ```CRUD::field()``` as a new ```$this->crud->addOrModifyField()```;
- **Reduce your CrudController length by 20-60%.** Most field&column definitions will fit on just one line.
- **Better syntax highlighting. Better auto-completion.** For most IDEs at least.
- **Intelligent defaults.** Most fields only actually need the `name` from you - Backpack will try to guess everything else. Even the `type`. Even the relationship.
- see [docs](/docs/4.1/crud-fluent-syntax), [PR](https://github.com/Laravel-Backpack/CRUD/pull/2513)


![Backpack Chart Widget](https://backpackforlaravel.com/uploads/docs-4-1/release_notes/before_after_maybe_fluent_syntax.png)

<hr>


<a name="changed"></a>
## Changed

- For the ```List Operation```, you can now easily:
	- **hide/show the search bar** - see docs, [PR](https://github.com/Laravel-Backpack/CRUD/pull/2479);
	- **hide/show a "Reset" button** that resets the DataTable to its default search, filtering, pagination - see docs, [PR](https://github.com/Laravel-Backpack/CRUD/pull/2509);
- You can now easily **customize the buttons at the end of the Create/Update forms** - see docs, [PR](https://github.com/Laravel-Backpack/CRUD/pull/2356);
- You can now make all Backpack routes **use a different web middleware** than ```web``` - see docs, [PR](https://github.com/Laravel-Backpack/CRUD/pull/2408);
- Field ```wrapperAttributes``` was renamed to ```wrapper```;


<a name="removed"></a>
## Removed

- Support for Laravel 5.8;
- Support for PHP lower than 7.2.5;
- ```laravel/helpers``` dependency, but you can still install it yourself, if you want to use the array and string helpers; 
- ```venturecraft/revisionable``` dependency; in order to use the Revisions operation you now have to install [the backpack/revise-operation add-on](https://github.com/laravel-backpack/revise-operation);
- ```barryvdh/laravel-elfinder``` dependency; in order to use the File Manager screen, the ```browse``` or ```browse_multiple``` field types, you now need to install the [backpack/filemanager add-on](https://github.com/Laravel-Backpack/FileManager) that we created;
- ```intervention/image``` dependency; in order to use the ```image``` field type you might need to [install the package](http://image.intervention.io/getting_started/installation), if you've copy-pasted our example mutator in your Model; 
- ```App\Models\BackpackUser``` is no longer needed, recommended or published when installing Backpack; authentication now works with your default ```App\User``` model (or whatever it is); this eliminates a bit of unneeded complexity, and fixes a bunch of problems when there are morph relationships towards the User model; but you can still keep it if you like to have a separate model for you admins;


---

In order to get all of the features above (and a few more hidden gems), please [follow the upgrade guide](/docs/{{version}}/upgrade-guide), to get from Backpack 4.0 to Backpack 4.1.
