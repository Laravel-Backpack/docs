# Release Notes

---

**Launch date:** March 24th, 2020

Backpack 4.1 is a FREE upgrade for Backpack 4.0 users. Anybody with a 4.0 license can install and use 4.1, but in order to get the new features you need to [follow the upgrade guide](/docs/{{version}}/upgrade-guide).

Here's what Backpack 4.1 brings to the table, and why you should upgrade from [Backpack 4.0](/docs/4.0) to Backpack 4.1.


<a name="added"></a>
# Added

!! ```GIF, SCREENSHOT or CODE BLOCK``` !!

#### **New field type: ```repeatable```** (aka ```matrix```, aka ```multiply```) 

- group multiple fields into one (ex: Testimonial includes full_name, company_name, position, text)
- allow the user to add many such groups (ex: Testimonials) to the current entry, stored as JSON
- see docs, [PR](https://github.com/Laravel-Backpack/CRUD/pull/2266)

!! ```GIF, SCREENSHOT or CODE BLOCK``` !!

#### **New field type: ```relationship```** 

- includes the functionality of all select2 fields
- can replace both AJAX and non-AJAX selects
- can replace single and multiple selects (1-n and n-n relationships) 
- finally one relationship field to rule them all
- see docs, [PR](https://github.com/Laravel-Backpack/CRUD/pull/2311)

!! ```GIF, SCREENSHOT or CODE BLOCK``` !!

#### **New operation: ```InlineCreate```** 

- shows an "+ Add" button next to your select field
- that button shows a modal, that allows the user to Create an item on-the-fly, without leaving the page;
- see docs, [PR](https://github.com/Laravel-Backpack/CRUD/pull/2311)

!! ```GIF, SCREENSHOT or CODE BLOCK``` !!

#### **New operation: ```Fetch```** 

- fields with AJAX selects are now much easier to create & use 
- see docs, [PR](https://github.com/Laravel-Backpack/CRUD/pull/2308)


!! ```GIF, SCREENSHOT or CODE BLOCK``` !!

#### **New columns feature: ```wrapper```** 

- you can now easily add links around your column text
- those links can point to a Preview operation for that entry, or whatever you want
- the same feature can be used to change the look&feel of columns (ex: add badge-warning, etc)
- see docs, [PR](https://github.com/Laravel-Backpack/CRUD/pull/2448)


!! ```GIF, SCREENSHOT or CODE BLOCK``` !!

#### **New widget: ```chart```** 

- easily add a widget with a pie chart, or bar chart, from your Controller
- see docs, PR


!! ```GIF, SCREENSHOT or CODE BLOCK``` !!

#### **Fluent syntax for fields & columns (alternative)** 

- create or modify a field/column with one call
- instead of defining field/column attributes as an array, easily chain the attributes to the first call 
- comfortably fit most field/column definitions on one line, instead of multiple
- see docs, [PR](https://github.com/Laravel-Backpack/CRUD/pull/2513)




<a name="changed"></a>
# Changed

- For the ```List Operation```, you can now easily:
	- **hide/show the search bar** - see docs, [PR](https://github.com/Laravel-Backpack/CRUD/pull/2479);
	- **hide/show a "Reset" button** that resets the DataTable to its default search, filtering, pagination - see docs, [PR](https://github.com/Laravel-Backpack/CRUD/pull/2509);
	- **extend the ListOperation to show the DataTable from a different perspective** (ex: a monthlyReport page that filters entries, shows different columns, different pagination, different buttons, etc) - see docs , PR;
- You can now easily **customize the buttons at the end of the Create/Update forms** - see docs, [PR](https://github.com/Laravel-Backpack/CRUD/pull/2356);
- You can now make all Backpack routes **use a different web middleware** than ```web``` - see docs, [PR](https://github.com/Laravel-Backpack/CRUD/pull/2408);


<a name="removed"></a>
# Removed

- Support for PHP 7.1;
- ```laravel/helpers``` dependency 
	- but you can install it, if you want to use the array and string helpers; 
	- see upgrade guide;
- ```venturecraft/revisionable``` dependency 
	- in order to use the Revisions operation you now have to install a first-party Backpack add-on; 
	- see docs, PR or upgrade guide;
- ```barryvdh/laravel-elfinder``` dependency 
	- in order to use the File Manager screen, the ```browse``` or ```browse_multiple``` field types, you need to install a first-party Backpack add-on; 
	- see docs, PR or upgrade guide;
- ```intervention/image``` dependency 
	- in order to use the ```image``` field type you need to require the package yourself; 
	- see updated docs, PR and upgrade guide; 


---

In order to get all of the features above (and a few more hidden gems), please [follow the upgrade guide](/docs/{{version}}/upgrade-guide), to get from Backpack 4.0 to Backpack 4.1.