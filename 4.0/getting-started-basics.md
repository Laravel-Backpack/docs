# 1. Basics

---

**Duration:** 5 minutes

> **Are you already comfortable with Laravel?** In order to understand this series and make use of Backpack, you'll need to have a decent understanding of the Laravel framework. If you don't, please go ahead and [watch this excellent intro series on Laracasts](https://laracasts.com/series/laravel-from-scratch-2018) and accommodate yourself with Laravel first.


<a name="what-is-backpack"></a>
## What is Backpack?
A software that helps Laravel professionals build administration panels - secure areas where administrators login and create, read, update and delete application information. It is *not* a CMS, it is more a framework that lets you *build your own* CMS. You can install it in your existing project or in a totally new project. 

It's designed to be flexible enough to allow you to **build admin panels for everything from simple presentation websites to CRMs, ERPs, eCommerce, eLearning, etc**. We can vouch for that, because we have built all that stuff using Backpack already.

<a name="how-to-use-backpack"></a>
## What's a CRUD?

A **CRUD** is what we call a section of your admin panel that lets the admin _Create, Read, Update or Delete_ entries of a certain entity (or Model). So you can have a CRUD for Products, a CRUD for Articles, a CRUD for Categories, or whatever else you might want to create, read, update or delete.

For the purpose of this series, we'll show examples on the ```Tag``` entity. This is what a Tag CRUD could look like:

![Tag CRUD - List Entries Operation](https://backpackforlaravel.com/uploads/docs-4-0/getting_started/tag_crud_list_entries.png)

But Backpack is prepared for feature-packed CRUDs - since it's a good tool for very complex projects too. Here's what a CRUD that uses all of Backpack's features could look like:

![Monsters CRUD - List Entries Operation](https://backpackforlaravel.com/uploads/docs-4-0/getting_started/monster_crud_list_entries.png)

Mind that you will _almost never_ use all of Backpack's features in one CRUD. But if you do... it still looks good, and it'll be intuitive to use.

<a name="core-packages"></a>
## Main Features

<a name="backpack-design"></a>
### Front-End Design

Backpack installs the [CoreUI](https://coreui.io) HTML theme, and our own design on top - [Backstrap](https://backstrap.net). It uses Bootstrap 4, and has many HTML blocks ready for you to use. When you're building a custom page in your admin panel, it's easy to just copy-paste the HTML from our [Backstrap demo](https://backstrap.net), or from the [CoreUI documentation](https://coreui.io/docs/getting-started/introduction/), and it look good, without you having to design anything.

It also installs Noty for triggering JS notification bubbles, and SweetAlerts. So you can easily use these across your admin panel. You can [trigger notification bubbles in PHP](/docs/{{version}}/base-about#triggering-notification-bubbles-in-php) or [trigger notification bubbles in JavaScript](/docs/{{version}}/base-about#triggering-notification-bubbles-in-javascript).


<a name="backpack-authentication"></a>
### Authentication

Backpack comes with a basic authentication system that's separate from Laravel's. This way, you can have different login screens for users & admins, if you need. If not, you can choose to use only one authentication - either Laravel's, or Backpack's.

![Backpack 3.5 Authentication Screens](https://backpackforlaravel.com/uploads/docs-4-0/getting_started/auth_screens.png)

After you [install Backpack](/docs/{{version}}/installation) (don't do it now), you'll be able to log into your admin panel at ```http://yourapp/admin```. You can change the URL prefix from ```admin``` to something else in your ```config/backpack/base.php``` file, along with a bunch of other configuration options. [Click here](https://github.com/Laravel-Backpack/CRUD/blob/master/src/config/backpack/base.php) to browse the configuration file and see what it can do for you.


<a name="backpack-crud"></a>
### CRUDs

This is where it gets interesting. As soon as you [install Backpack](/docs/{{version}}/installation) in your project, you can create **CRUDs** for your admins to easily manipulate DB information. Let's browse through a simple example, of creating a CRUD administration panel for a Tag entity:

```zsh
# STEP 1. create migration
php artisan make:migration:schema create_tags_table --model=0 --schema="name:string:unique,slug:string:unique"
php artisan migrate

# STEP 2. create crud
php artisan backpack:crud tag #use singular, not plural
```

This will create a simple CRUD panel, which you should now be able to see in the Sidebar.

For a simple entry like this, the generated CRUD panel will even work "as is", no need for customizations. But don't expect this for more complex entities. They will usually have particularities and need customization. That's where Backpack shines - modifying anything in the CRUD Panel is easy and intuitive, once you understand how it works.

The code above would generate:
- a **migration** file
- a **model** (```app\Models\Tag.php```)
- a **request** file, for form validation (```app\Http\Requests\TagCrudRequest.php```)
- a **controller** file, where you can customize how the CrudPanel looks and feels (```app\Http\Controllers\Admin\TagCrudController.php```)
- a **route**, as a line inside ```routes/backpack/custom.php```

It will also add:
- a route inside ```routes/backpack/custom.php```, pointing to that controller;
- a sidebar item inside ```resources/views/vendor/backpack/base/inc/sidebar_content.blade.php```;

You might have noticed that **no views** are generated. That's because in most cases you _don't need_ custom views with Backpack. All your custom code is in the controller, model or request, so the default views are loaded, from the package. If you do, however, need to customize a view, it is [ridiculously easy](/docs/{{version}}/crud-how-to#customize-views-for-each-crud-panel).

Also, we won't be covering the **migration**, **model** and **request** files here, as they are in no way custom. The only thing you need to make sure is that the Model is properly configured (db table, relationships, ```$fillable``` or ```$guarded``` properties, etc) and that it uses our ```CrudTrait```. What we _will_ be covering is ```TagCrudController``` - which is where most of your logic will reside. Here's a copy of a simple one you might use to achieve the above:

```php
<?php namespace App\Http\Controllers\Admin;

use Backpack\CRUD\app\Http\Controllers\CrudController;
use App\Http\Requests\TagCrudRequest;

class TagCrudController extends CrudController {

  use \Backpack\CRUD\app\Http\Controllers\Operations\ListOperation;
  use \Backpack\CRUD\app\Http\Controllers\Operations\ShowOperation;
  use \Backpack\CRUD\app\Http\Controllers\Operations\CreateOperation;
  use \Backpack\CRUD\app\Http\Controllers\Operations\UpdateOperation;
  use \Backpack\CRUD\app\Http\Controllers\Operations\DeleteOperation;

  public function setup() 
  {
      $this->crud->setModel("App\Models\Tag");
      $this->crud->setRoute("admin/tag");
      $this->crud->setEntityNameStrings('tag', 'tags');
  }

  public function setupListOperation()
  {
      $this->crud->setColumns(['name', 'slug']);
  }

  public function setupCreateOperation()
  {
      $this->crud->setValidation(TagCrudRequest::class);

      $this->crud->addField([
        'name' => 'name',
        'type' => 'text',
        'label' => "Tag name"
      ]);
      $this->crud->addField([
        'name' => 'slug',
        'type' => 'text',
        'label' => "URL Segment (slug)"
      ]);
  }

  public function setupUpdateOperation()
  {
      $this->setupCreateOperation();
  }
}
```

You should notice:
- It uses basic inheritance (```TagCrudController extends CrudController```); so if you want to modify a behaviour (save, update, reorder, etc), you can easily do that by overwriting the corresponding method in your ```TagCrudController```;
- All operations are enabled by using that operation's trait on the controller;
- The ```setup()``` method defines the basics of the CRUD panel;
- Each operation is set up inside a ```setupXxxOperation()``` method;

**That's all for today! **If you want to learn more, go ahead and [read the next lesson](/docs/{{version}}/getting-started-crud-operations) of this series.
