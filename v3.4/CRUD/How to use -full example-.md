---
title: "How to use (full example)"
excerpt: ""
---
This page will help you understand how Backpack\CRUD works, how you can use it and ends with a tip of how you can do it all very very fast.

The easiest way to read this:
1. Read everything except the blue bubbles. They go in a bit of detail. 
2. Read the blue bubbles. If you don't understand what they're talking about, re-read that section.
3. You got this. Start coding!
[block:api-header]
{
  "type": "basic",
  "title": "Introduction"
}
[/block]
Backpack\CRUD is a fast way to build admin panels with Create, Read, Update, Delete functionality. **One panel provides functionality for CRUD operations on one Eloquent Model.** 

Most of the times, **a CRUD Panel consists of**:
- **a database table** (and maybe connection tables for relationships);
- **a Model**;
- **a Controller** for the CRUD operations;
- 0, 1 or 2 **Request files** (if form validation is needed);
- **a route**;



[block:api-header]
{
  "type": "basic",
  "title": "Simple example"
}
[/block]
Let’s take the Tag entity and create a CRUD Panel for it. We're going to create the CRUD Panel manually, which will take some time but is useful in order to understand how it works. At the end of this page you'll be presented with a way to do it all in under a minute, using generators. 

Its CRUD interface consists of:
- a database table (tags)
- a model (app/Models/Tag.php)
- a controller (app/Http/Controllers/Admin/TagCrudController.php)
- a request (app/Http/Requests/TagCrudRequest.php)
- a route


[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/HX9EsTHTJOjfZjzDG7cc_Screen%20Shot%202016-05-20%20at%2019.22.53.png",
        "Screen Shot 2016-05-20 at 19.22.53.png",
        "316",
        "1027",
        "#dd3e2d",
        ""
      ]
    }
  ]
}
[/block]
#The database table

Of course, any entity stored in a database needs at least a table. The tag entity needs one too, “tags”, which looks like this:

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/WXaqi900REeb6JkW1kqO_Screen%20Shot%202016-05-20%20at%2019.24.41.png",
        "Screen Shot 2016-05-20 at 19.24.41.png",
        "514",
        "212",
        "#926454",
        ""
      ]
    }
  ]
}
[/block]
   Nothing fancy here.

#The model

On your standard Eloquent model, make sure you:
- use the CrudTrait;
- properly define your relationships to other Models (to be able to use them in the CRUD interface);
- define either $guarded or $fillable for the CRUD to know which columns are editable;
- [optional] store it in app/Models (because you may end up having a lot of them);


[block:code]
{
  "codes": [
    {
      "code": "<?php\n\nnamespace App\\Models;\n\nuse Illuminate\\Database\\Eloquent\\Model;\nuse Backpack\\CRUD\\CrudTrait;\n\nclass Tag extends Model {\n\n\tuse CrudTrait;\n\n    /*\n\t|--------------------------------------------------------------------------\n\t| GLOBAL VARIABLES\n\t|--------------------------------------------------------------------------\n\t*/\n\n\tprotected $table = 'tags';\n\t// protected $primaryKey = 'id';\n\t// protected $guarded = [];\n\t// protected $hidden = ['id'];\n\tprotected $fillable = ['name'];\n\tpublic $timestamps = true;\n\n\t/*\n\t|--------------------------------------------------------------------------\n\t| FUNCTIONS\n\t|--------------------------------------------------------------------------\n\t*/\n\n\t/*\n\t|--------------------------------------------------------------------------\n\t| RELATIONS\n\t|--------------------------------------------------------------------------\n\t*/\n\n\tpublic function articles()\n    {\n        return $this->hasMany('App\\Models\\Article', 'article_tag');\n    }\n\n\t/*\n\t|--------------------------------------------------------------------------\n\t| SCOPES\n\t|--------------------------------------------------------------------------\n\t*/\n\n\t/*\n\t|--------------------------------------------------------------------------\n\t| ACCESORS\n\t|--------------------------------------------------------------------------\n\t*/\n\n\t/*\n\t|--------------------------------------------------------------------------\n\t| MUTATORS\n\t|--------------------------------------------------------------------------\n\t*/\n}",
      "language": "php"
    }
  ]
}
[/block]
#The controller

As an official convention:
- the controller is stored in ```app/Http/Controllers/Admin```
- its filename is EntityCrudController.php
 
But you can can use another location/name if you want to. Take a look at it:
[block:code]
{
  "codes": [
    {
      "code": "<?php namespace App\\Http\\Controllers\\Admin;\n\nuse Backpack\\CRUD\\app\\Http\\Controllers\\CrudController;\n\n// VALIDATION: change the requests to match your own file names if you need form validation\nuse App\\Http\\Requests\\TagCrudRequest as StoreRequest;\nuse App\\Http\\Requests\\TagCrudRequest as UpdateRequest;\n\nclass TagCrudController extends CrudController {\n\n\tpublic function setup() {\n        $this->crud->setModel('App\\Models\\Tag');\n        $this->crud->setRoute(config('backpack.base.route_prefix')  . '/tag');\n        $this->crud->setEntityNameStrings('tag', 'tags');\n\n        $this->crud->setColumns(['name']);\n        $this->crud->addField([\n\t'name' => 'name',\n\t'label' => 'Tag name'\n\t]);\n    }\n\n\tpublic function store(StoreRequest $request)\n\t{\n\t\treturn parent::storeCrud();\n\t}\n\n\tpublic function update(UpdateRequest $request)\n\t{\n\t\treturn parent::updateCrud();\n\t}\n}",
      "language": "php"
    }
  ]
}
[/block]
Because your controller (TagCrudController) extends the default CrudController class, every CRUD functionality is available for you to use. If you don't like a functionality, you can:
1. [take a look at how it's done in CrudController](https://github.com/Laravel-Backpack/CRUD/blob/master/src/app/Http/Controllers/CrudController.php);
2. place a method with the same name in your TagCrudController, with the custom logic you need.
[block:callout]
{
  "type": "info",
  "title": "Notes",
  "body": "For most cases, your entire panel's logic will be in the ```setup()``` method; this is called in the constructor, so anything you customize there will be applied to all CRUD operations; you have access to all variables you need here (the request, the crud, etc); this is where you'll define the basics for the CRUD and specify columns, fields.\n\nThe store() and update() functions are needed in each EntityCrudController because the Request needs to be type-hinted in Laravel in order for validation to happen; it's also where you can do custom operations before or after the item is inserted/updated - no callbacks needed."
}
[/block]
#The requests

This is where you write your validation rules for the Create/Update operations. The **app/Http/Requests/TagCrudRequest.php** file:

[block:code]
{
  "codes": [
    {
      "code": "<?php namespace App\\Http\\Requests;\n\nuse App\\Http\\Requests\\Request;\n\nclass TagCrudRequest extends \\Backpack\\CRUD\\app\\Http\\Requests\\CrudRequest {\n\n    /**\n     * Determine if the user is authorized to make this request.\n     *\n     * @return bool\n     */\n    public function authorize()\n    {\n        // only allow updates if the user is logged in\n        return \\Auth::check();\n    }\n\n    /**\n     * Get the validation rules that apply to the request.\n     *\n     * @return array\n     */\n    public function rules()\n    {\n        return [\n            'name' => 'required|min:5|max:255'\n        ];\n    }\n\n}",
      "language": "php"
    }
  ]
}
[/block]

[block:callout]
{
  "type": "info",
  "title": "Validation not necessary?",
  "body": "If you need form validation on the create/update views, this request file is necessary. Otherwise, just point the controller to the ```Backpack\\CRUD\\app\\Http\\Requests\\CrudRequest``` class instead, which has no validation rules on it, but does require the user to be logged in."
}
[/block]

[block:callout]
{
  "type": "info",
  "title": "Differences between the Create and Update validations?",
  "body": "If your Update operation requires a different validation than the Create operation, you can create separate request files and use them in you TagCrudController, for ex: ```UpdateTagRequest.php``` and ```CreateTagRequest.php```."
}
[/block]
#The route

The CRUD routes are based on RESTful controllers, so you only need to define one line for each CRUD Panel.
[block:code]
{
  "codes": [
    {
      "code": "<?php\n\n// Admin Interface Routes\nRoute::group(['prefix' => 'admin', 'middleware' => 'admin'], function()\n{\n  // Backpack\\CRUD: Define the resources for the entities you want to CRUD.\n    CRUD::resource('tag', 'Admin\\TagCrudController');\n  \n  // [...] other routes\n});",
      "language": "php"
    }
  ]
}
[/block]
It's generally a good idea to have the admin routes in a separate file. That's why Backpack 3.4+ publishes the ```routes/backpack/custom.php``` file, where you can put new admin routes, behind the middleware and guard defined in the config file.

It should look something like this:
[block:code]
{
  "codes": [
    {
      "code": "<?php\n\n// --------------------------\n// Custom Backpack Routes\n// --------------------------\n// This route file is loaded automatically by Backpack\\Base.\n// Routes you generate using Backpack\\Generators will be placed here.\n\nRoute::group([\n    'prefix'     => config('backpack.base.route_prefix', 'admin'),\n    'middleware' => ['web', config('backpack.base.middleware_key', 'admin')],\n    'namespace'  => 'App\\Http\\Controllers\\Admin',\n], function () { // custom admin routes\n    // CRUD resources and other admin routes\n    CRUD::resource('monster', 'MonsterCrudController');\n    CRUD::resource('icon', 'IconCrudController');\n    CRUD::resource('product', 'ProductCrudController');\n}); // this should be the absolute last line of this file",
      "language": "php",
      "name": "admin.php"
    }
  ]
}
[/block]
Note: if you want the item to appear in the top admin menu, it’s as easy as including it in the **views/vendor/backpack/base/inc/sidebar_content.php** file:
[block:code]
{
  "codes": [
    {
      "code": "<li><a href=\"{{ backpack_url('tag') }}\"><i class=\"fa fa-tag\"></i> <span>Manage Tags</span></a></li>",
      "language": "html"
    }
  ]
}
[/block]

#The result

The table (or list) view:
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/lSbO0GwNSPCdGXSIRVfd_Screen%20Shot%202016-05-20%20at%2020.16.04.png",
        "Screen Shot 2016-05-20 at 20.16.04.png",
        "1281",
        "613",
        "#1eb178",
        ""
      ]
    }
  ]
}
[/block]
The insert (or add) view:

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/UDiAr1gTb2FNfkpI2Upg_Screen%20Shot%202016-05-20%20at%2020.14.20.png",
        "Screen Shot 2016-05-20 at 20.14.20.png",
        "1281",
        "614",
        "#16aa71",
        ""
      ]
    }
  ]
}
[/block]
The edit (or update) view:
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/xSSMSxibTpWJHIrTye0C_Screen%20Shot%202016-05-20%20at%2020.15.38.png",
        "Screen Shot 2016-05-20 at 20.15.38.png",
        "1281",
        "613",
        "#15aa6f",
        ""
      ]
    }
  ]
}
[/block]
**Congratulations, you now understand how Backpack\CRUD works!** This is a very very basic example, but the process will be identical for tables with 50+ columns, complicated logic, etc. 
[block:callout]
{
  "type": "info",
  "title": "Scroll through the stock functionality",
  "body": "You might end up using [different field types](https://laravel-backpack.readme.io/v3.0/docs/crud-fields), [different column types](https://laravel-backpack.readme.io/v3.0/docs/crud-columns-types), [filters](https://laravel-backpack.readme.io/v3.0/docs/filters), [custom buttons](https://laravel-backpack.readme.io/v3.0/docs/crud-buttons), [advanced features](https://laravel-backpack.readme.io/v3.0/docs/advanced-features)."
}
[/block]

[block:api-header]
{
  "type": "basic",
  "title": "Doing it all very fast"
}
[/block]
Here's what you can use, to create each file for the CRUD interface very fast:
- a database table (tags) - [Jeffrey Way's generators](https://github.com/laracasts/Laravel-5-Generators-Extended) for migrations and seeds
- a model (app/Models/Tag.php) - [Backpack\Generators](https://github.com/laravel-backpack/generators)
- a controller (app/Http/Controllers/Admin/TagCrudController.php) - [Backpack\Generators](https://github.com/laravel-backpack/generators)
- a request (app/Http/Requests/TagCrudRequest.php) - [Backpack\Generators](https://github.com/laravel-backpack/generators)
- a route - manually

So when you use generators, the entire process is:
[block:code]
{
  "codes": [
    {
      "code": "# STEP 1. create migration\nphp artisan make:migration:schema create_tags_table --model=0 --schema=\"name:string:unique\"\nphp artisan migrate\n\n# STEP 2. create a model, a request and a controller for the admin panel\nphp artisan backpack:crud tag #use singular, not plural\n\n# STEP 3. add a route to routes/backpack/custom.php (under the admin prefix and auth middleware): \nphp artisan backpack:base:add-custom-route \"CRUD::resource('tag', 'TagCrudController');\"\n\n# STEP 4. add a sidebar item\nphp artisan backpack:base:add-sidebar-content \"<li><a href='{{ backpack_url('tag') }}'><i class='fa fa-tag'></i> <span>Tags</span></a></li>\"",
      "language": "shell"
    }
  ]
}
[/block]
That's a lot faster, right? Don't worry, you'll get used to it, and it'll get faster. Plus, a user interface to generate all this is in the works, if that's more your type of thing.

**Please note:** You still have to go through the created files, check everything's alright and customize them to your specifications, but a lot of the heavy-lifting is already done. Most notably, you'll need to go in the generated Model and define the ```$table```, ```$primaryKey``` and ```$fillable```. 

Also, it is NOT recommended that you keep ```$this->setFromDb()```, which automatically tries to figure out what columns and fields you need. It's only smart enough for super-simple CRUDs. Best to insert your own columns and fields, using the syntax that's already there, but commented out. Check out the [API cheatsheet](https://laravel-backpack.readme.io/docs/crud-full-api) to see what's available, and browse through our 44+ [field types](https://laravel-backpack.readme.io/docs/crud-fields), 20+ [column types](https://laravel-backpack.readme.io/docs/crud-columns-types) and 10+ [filter types](https://laravel-backpack.readme.io/docs/filters).