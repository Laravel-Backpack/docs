# Getting Started Videos

---

**Total Duration:** 31 minutes


<a name="intro"></a>
<h2 class="mb-7">Intro</h2>


Meet your teacher, [Cristian Tabacitu](https://twitter.com/tabacitu), the founder of Backpack for Laravel.

<div class='mx-sm-n5 mx-md-n6 mx-lg-n7 mt-7 embed-container'><iframe src='https://player.vimeo.com/video/424693910' frameborder='0' webkitAllowFullScreen mozallowfullscreen allowFullScreen></iframe></div>

<br>
Mentioned in this video:
- [Laravel](https://laravel.com)
- ["Laravel from Scratch" series on Laracasts](https://laravelfromscratch.com/)
- [Backpack's docs repo on Github](https://github.com/laravel-backpack/docs)

----

<a name="the-admin-interface"></a>
<h2 class="mb-7">The Admin Interface</h2>

Before we go deep into the PHP features offered by Backpack, it's important to understand the HTML & CSS it uses, and how that can make your life easier when building admin panels. This is often an afterthought in admin panels, but Backpack makes it really really easy to create admin pages that are 100% custom.

<div class='mx-sm-n5 mx-md-n6 mx-lg-n7 mt-7 embed-container'><iframe src='https://player.vimeo.com/video/424698837' frameborder='0' webkitAllowFullScreen mozallowfullscreen allowFullScreen></iframe></div>

<br>
Mentioned in this video:
- [Laravel docs - installation](https://laravel.com/docs/7.x)
- [Backpack docs - installation](/docs/{{version}}/installation)
- [Backpack docs - how to customize the user interface](/docs/{{version}}/base-how-to#customizing-the-design-of-the-menu-sidebar-footer)
- [Backpack docs - widgets](/docs/{{version}}/base-widgets)
- [Backpack docs - add-ons](/addons)
- [Bootstrap 4 docs](https://getbootstrap.com/)
- [Backstrap.net - the default HTML template & UI blocks](https://backstrap.net/)

----

<a name="generating-and-understanding-cruds"></a>
<h2 class="mb-7">Generating and Understanding CRUDs</h2>

Let's generate a few Backpack CRUDs - places where the admin can Create, Read, Update or Delete entries. CRUDs will make it easy for you to build admin panels, 10x faster than before.

<div class='mx-sm-n5 mx-md-n6 mx-lg-n7 mt-7 embed-container'><iframe src='https://player.vimeo.com/video/424701595' frameborder='0' webkitAllowFullScreen mozallowfullscreen allowFullScreen></iframe></div>

<br>
Mentioned in this video:
- [Backpack's online demo](https://demo.backpackforlaravel.com/admin/login)
- [laravel-shift/blueprint](https://blueprint.laravelshift.com/) - CLI tool to generate Eloquent models
- [Laravel docs - migrations](https://laravel.com/docs/7.x/migrations#introduction)
- [backpack/generators - CLI tool to generate CRUDs](https://github.com/laravel-backpack/generators)
- [Fork - Git client for Mac OS & Windows](https://git-fork.com/)
- [Backpack's List Operation](/docs/{{version}}/crud-operation-list-entries) - features [columns](/docs/{{version}}/crud-columns), [filters](/docs/{{version}}/crud-filters), [buttons](/docs/{{version}}/crud-buttons), [widgets](/docs/{{version}}/base-widgets), [other features](/docs/{{version}}/crud-operation-list-entries#other-features)
- [Backpack's Create Operation](/docs/{{version}}/crud-operation-create) - features [fields](/docs/{{version}}/crud-fields), [widgets](/docs/{{version}}/base-widgets), [save actions](/docs/{{version}}/crud-save-actions), [split fields into tabs](/docs/{{version}}/crud-fields#split-fields-into-tabs), [add custom HTML between the fields](/docs/{{version}}/crud-fields#custom-html)
- [Backpack's Update Operation](/docs/{{version}}/crud-operation-update)
- [Backpack's Delete Operation](/docs/{{version}}/crud-operation-delete)


----

<a name="using-operations-and-features-in-cruds"></a>
<h2 class="mb-7">Using Operations and Features in CRUDs</h2>

I'd argue the best part of Backpack is not how easy it is to generate CRUDs, but how easy it is to customize them. In this video, we'll be taking a look at the default Operations that Backpack offers, and changing things around to fit our purpose. In the process, you'll understand how they work, and just how easy it is to use, customize or overwrite Operations like Create, Update, List, Delete, Reorder, Clone, BulkClone and BulkDelete.

<div class='mx-sm-n5 mx-md-n6 mx-lg-n7 mt-7 embed-container'><iframe src='https://player.vimeo.com/video/424703257' frameborder='0' webkitAllowFullScreen mozallowfullscreen allowFullScreen></iframe></div>

<br>
Mentioned in this video:
- [Backpack operations](/docs/{{version}}/crud-operations)
- Laravel docs - [validation using Laravel Form Requests](https://laravel.com/docs/7.x/validation#form-request-validation)
- Laravel docs - [validation rules](https://laravel.com/docs/7.x/validation#available-validation-rules)
- [Reorder Operation](/docs/{{version}}/crud-operation-reorder)
- [Clone & BulkClone Operations](/docs/{{version}}/crud-operation-clone)
- Columns - [the 20+ column types](/docs/{{version}}/crud-columns#default-column-types), [columns API](/docs/{{version}}/crud-columns#columns-api), [fluent syntax](/docs/{{version}}/crud-fluent-syntax#fluent-columns), [array syntax](/docs/{{version}}/crud-columns#about), [overwriting a column type](/docs/{{version}}/crud-columns#overwriting-default-column-types), [creating a custom column type](/docs/{{version}}/crud-columns#creating-a-custom-column-type)
- Fields - [the 50+ field types](/docs/{{version}}/crud-fields#default-field-types), [fluent syntax](/docs/{{version}}/crud-fluent-syntax#fluent-fields-api), [array syntax](/docs/{{version}}/crud-fields#about), [overwriting a field type](/docs/{{version}}/crud-fields#overwriting-default-field-types), [custom field types](/docs/{{version}}/crud-fields#creating-a-custom-field-type)
- [the contents of an Operation - sidebar item, routes, controller](/docs/{{version}}/crud-operations#contents-of-a-custom-operation)

----


Thank you for dedicating these 31 minutes to learning Backpack. **You should now be able to build your first admin panel.** But if you feel like you're _not quite ready yet_, here are a few more things you can do:

- [Go through the demo](/docs/{{version}}/demo) and play around, browse the features (pay special attention to the Monsters CRUD)
- Read this [CRUD Crash Course](/docs/{{version}}/crud-tutorial) and do the steps yourself
- Take a look at the [CrudController API Cheat Sheet](/docs/{{version}}/crud-cheat-sheet)
- Read our [Getting Started Text Course](/docs/{{version}}/getting-started-basics)


<style>
  .embed-container { 
    position: relative; 
    padding-bottom: 56.25%; 
    height: 0; 
    overflow: hidden; 
    max-width: 100%; 
  } 
  .embed-container iframe, 
  .embed-container object, 
  .embed-container embed { 
    position: absolute; 
    top: 0; 
    left: 0; 
    width: 100%; 
    height: 100%; 
  }
</style>
