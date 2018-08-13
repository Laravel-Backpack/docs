---
title: "NewsCRUD"
excerpt: ""
---
Follow this example to have an admin panel for 3 interconnected entities:
- news article
- category (1-n relationship with news article)
- tag (n-n relationship with news article)




Since NewsCRUD is just a Backpack\CRUD example, you can choose to install it one of two ways.

**(A) Download and place files in your application** (recommended)

or

**(B) As a package**

or

**(C) Manually** (a good way to understand how CRUDs work)

The only PRO of installing it as a package is that you may benefit from updates. But the reality is there is very little (if any) bug fixing to do, so you probably won't need to update it, ever.



#### Intallation type (A) - download


1) [Download the latest build](https://github.com/Laravel-Backpack/NewsCRUD/archive/master.zip).

2) Paste the 'app' and 'database' folders over your projects (merge them). No file overwrite warnings should come up.

3) Replace all mentions of 'Backpack\NewsCRUD\app' in the pasted files with your application's namespace ('App' if you haven't changed it):
- app/Http/Controllers/Admin/ArticleCrudController.php
- app/Http/Controllers/Admin/CategoryCrudController.php
- app/Http/Controllers/Admin/TagCrudController.php
- app/Http/Requests/ArticleRequest.php
- app/Http/Requests/CategoryRequest.php
- app/Http/Requests/TagRequest.php
- app/Models/Article.php
- app/Models/Category.php
- app/Models/Tag.php

4) Run the migration to have the database table we need:
```
php artisan migrate
```

5) Add NewsCRUD to your routes file:

```
Route::group(['prefix' => config('backpack.base.route_prefix', 'admin'), 'middleware' => ['auth'], 'namespace' => 'Admin'], function () {
    // Backpack\NewsCRUD
    CRUD::resource('article', 'ArticleCrudController');
    CRUD::resource('category', 'CategoryCrudController');
    CRUD::resource('tag', 'TagCrudController');
});
```

6) If you haven't already, install the sluggable package:
```php
composer require cviebrock/eloquent-sluggable
```
Add the provider in your config/app.php:
```php
Cviebrock\EloquentSluggable\ServiceProvider::class,
```
Publish the sluggable config file:
```php
php artisan vendor:publish --provider="Cviebrock\EloquentSluggable\ServiceProvider"
```

7) [optional] Add a menu item for it in resources/views/vendor/backpack/base/inc/sidebar.blade.php or menu.blade.php:

```html
<li class="treeview">
    <a href="#"><i class="fa fa-newspaper-o"></i> <span>News</span> <i class="fa fa-angle-left pull-right"></i></a>
    <ul class="treeview-menu">
      <li><a href="{{ url(config('backpack.base.route_prefix', 'admin') . '/article') }}"><i class="fa fa-newspaper-o"></i> <span>Articles</span></a></li>
      <li><a href="{{ url(config('backpack.base.route_prefix', 'admin') . '/category') }}"><i class="fa fa-list"></i> <span>Categories</span></a></li>
      <li><a href="{{ url(config('backpack.base.route_prefix', 'admin') . '/tag') }}"><i class="fa fa-tag"></i> <span>Tags</span></a></li>
    </ul>
</li>
```



#### Intallation type (B) - package

1) In your terminal, run:

``` bash
$ composer require backpack/newscrud
```

2) Then add the service providers to your config/app.php file:

```
'Backpack\NewsCRUD\NewsCRUDServiceProvider',
```

3) Publish the migration:

```
php artisan vendor:publish --provider="Backpack\NewsCRUD\NewsCRUDServiceProvider"
```

4) Run the migration to have the database table we need:

```
php artisan migrate
```

5) [optional] Add a menu item for it in resources/views/vendor/backpack/base/inc/sidebar.blade.php or menu.blade.php:

```html
<li class="treeview">
    <a href="#"><i class="fa fa-newspaper-o"></i> <span>News</span> <i class="fa fa-angle-left pull-right"></i></a>
    <ul class="treeview-menu">
      <li><a href="{{ url(config('backpack.base.route_prefix', 'admin') . '/article') }}"><i class="fa fa-newspaper-o"></i> <span>Articles</span></a></li>
      <li><a href="{{ url(config('backpack.base.route_prefix', 'admin') . '/category') }}"><i class="fa fa-list"></i> <span>Categories</span></a></li>
      <li><a href="{{ url(config('backpack.base.route_prefix', 'admin') . '/tag') }}"><i class="fa fa-tag"></i> <span>Tags</span></a></li>
    </ul>
</li>
```






#### Intallation type (C) - Manually (tutorial)

[block:api-header]
{
  "type": "basic",
  "title": "1. Eloquent Sluggable"
}
[/block]
First off, we're going to use [cviebrock/eloquent-sluggable](https://github.com/cviebrock/eloquent-sluggable) to auto-generate slugs, so you'll need to install that first:
```
composer require cviebrock/eloquent-sluggable
```
And place this Provider in your **config/app.php**:
```
'Cviebrock\EloquentSluggable\ServiceProvider',
``` 
[block:api-header]
{
  "type": "basic",
  "title": "2. Database tables"
}
[/block]
Here are the migrations. Notice the tabs. You should place them in your /database/migrations folder, then run **php artisan migrate**
[block:code]
{
  "codes": [
    {
      "code": "<?php\n\n// file name: database/migrations/2015_08_04_130520_create_articles_table.php\n\nuse Illuminate\\Database\\Schema\\Blueprint;\nuse Illuminate\\Database\\Migrations\\Migration;\n\nclass CreateArticlesTable extends Migration\n{\n    /**\n     * Run the migrations.\n     *\n     * @return void\n     */\n    public function up()\n    {\n        Schema::create('articles', function (Blueprint $table) {\n            $table->increments('id');\n            $table->integer('category_id')->unsigned();\n            $table->string('title');\n            $table->string('slug')->default('');\n            $table->text('content');\n            $table->string('image')->nullable();\n            $table->enum('status', ['PUBLISHED', 'DRAFT'])->default('PUBLISHED');\n            $table->date('date');\n            $table->boolean('featured')->default(0);\n            $table->timestamps();\n            $table->softDeletes();\n        });\n    }\n\n    /**\n     * Reverse the migrations.\n     *\n     * @return void\n     */\n    public function down()\n    {\n        Schema::drop('articles');\n    }\n}",
      "language": "php",
      "name": "articles"
    },
    {
      "code": "<?php\n\n// file name: database/migrations/2015_08_04_130507_create_article_tag_table.php\n\nuse Illuminate\\Database\\Schema\\Blueprint;\nuse Illuminate\\Database\\Migrations\\Migration;\n\nclass CreateCategoriesTable extends Migration\n{\n    /**\n     * Run the migrations.\n     *\n     * @return void\n     */\n    public function up()\n    {\n\n        Schema::create('categories', function (Blueprint $table) {\n            $table->increments('id');\n            $table->integer('parent_id')->default(0)->nullable();\n            $table->integer('lft')->unsigned()->nullable();\n            $table->integer('rgt')->unsigned()->nullable();\n            $table->integer('depth')->unsigned()->nullable();\n            $table->string('name');\n            $table->timestamps();\n            $table->softDeletes();\n        });\n    }\n\n    /**\n     * Reverse the migrations.\n     *\n     * @return void\n     */\n    public function down()\n    {\n        Schema::drop('categories');\n    }\n}",
      "language": "php",
      "name": "categories"
    },
    {
      "code": "<?php\n\n// file name: database/migrations/2015_08_04_131626_create_tags_table.php\n\nuse Illuminate\\Database\\Schema\\Blueprint;\nuse Illuminate\\Database\\Migrations\\Migration;\n\nclass CreateTagsTable extends Migration\n{\n    /**\n     * Run the migrations.\n     *\n     * @return void\n     */\n    public function up()\n    {\n        Schema::create('tags', function (Blueprint $table) {\n            $table->increments('id');\n            $table->string('name');\n            $table->timestamps();\n            $table->softDeletes();\n        });\n    }\n\n    /**\n     * Reverse the migrations.\n     *\n     * @return void\n     */\n    public function down()\n    {\n        Schema::drop('tags');\n    }\n}\n",
      "language": "php",
      "name": "tags"
    },
    {
      "code": "<?php\n\n// file name: database/migrations/2015_08_04_130507_create_article_tag_table.php\n\nuse Illuminate\\Database\\Schema\\Blueprint;\nuse Illuminate\\Database\\Migrations\\Migration;\n\nclass CreateArticleTagTable extends Migration\n{\n    /**\n     * Run the migrations.\n     *\n     * @return void\n     */\n    public function up()\n    {\n        Schema::create('article_tag', function (Blueprint $table) {\n            $table->increments('id');\n            $table->integer('article_id')->unsigned();\n            $table->integer('tag_id')->unsigned();\n            $table->nullableTimestamps();\n            $table->softDeletes();\n        });\n    }\n\n    /**\n     * Reverse the migrations.\n     *\n     * @return void\n     */\n    public function down()\n    {\n        Schema::drop('article_tag');\n    }\n}",
      "language": "php",
      "name": "article_tag"
    }
  ]
}
[/block]

[block:api-header]
{
  "type": "basic",
  "title": "3. Models"
}
[/block]
Place these in your **app\Models\** folder:
[block:code]
{
  "codes": [
    {
      "code": "<?php\n\nnamespace App\\Models;\n\nuse Illuminate\\Database\\Eloquent\\Model;\nuse Backpack\\CRUD\\CrudTrait;\nuse Cviebrock\\EloquentSluggable\\Sluggable;\n\nclass Article extends Model\n{\n    use CrudTrait;\n    use Sluggable;\n\n    /*\n    |--------------------------------------------------------------------------\n    | GLOBAL VARIABLES\n    |--------------------------------------------------------------------------\n    */\n\n    protected $table = 'articles';\n    protected $primaryKey = 'id';\n    public $timestamps = true;\n    // protected $guarded = ['id'];\n    protected $fillable = ['slug', 'title', 'content', 'image', 'status', 'category_id', 'featured', 'date'];\n    // protected $hidden = [];\n    // protected $dates = [];\n    protected $casts = [\n        'featured'  => 'boolean',\n        'date'      => 'date',\n    ];\n\n    /**\n     * Return the sluggable configuration array for this model.\n     *\n     * @return array\n     */\n    public function sluggable()\n    {\n        return [\n            'slug' => [\n                'source' => 'slug_or_title',\n            ],\n        ];\n    }\n\n    /*\n    |--------------------------------------------------------------------------\n    | FUNCTIONS\n    |--------------------------------------------------------------------------\n    */\n\n    /*\n    |--------------------------------------------------------------------------\n    | RELATIONS\n    |--------------------------------------------------------------------------\n    */\n\n    public function category()\n    {\n        return $this->belongsTo('App\\Models\\Category', 'category_id');\n    }\n\n    public function tags()\n    {\n        return $this->belongsToMany('App\\Models\\Tag', 'article_tag');\n    }\n\n    /*\n    |--------------------------------------------------------------------------\n    | SCOPES\n    |--------------------------------------------------------------------------\n    */\n\n    public function scopePublished($query)\n    {\n        return $query->where('status', 'PUBLISHED')\n                    ->where('date', '<=', date('Y-m-d'))\n                    ->orderBy('date', 'DESC');\n    }\n\n    /*\n    |--------------------------------------------------------------------------\n    | ACCESORS\n    |--------------------------------------------------------------------------\n    */\n\n    // The slug is created automatically from the \"title\" field if no slug exists.\n    public function getSlugOrTitleAttribute()\n    {\n        if ($this->slug != '') {\n            return $this->slug;\n        }\n\n        return $this->title;\n    }\n\n    /*\n    |--------------------------------------------------------------------------\n    | MUTATORS\n    |--------------------------------------------------------------------------\n    */\n}\n",
      "language": "php",
      "name": "Article"
    },
    {
      "code": "<?php\n\nnamespace App\\Models;\n\nuse Illuminate\\Database\\Eloquent\\Model;\nuse Backpack\\CRUD\\CrudTrait;\nuse Cviebrock\\EloquentSluggable\\Sluggable;\n\nclass Category extends Model\n{\n    use CrudTrait;\n    use Sluggable;\n\n    /*\n    |--------------------------------------------------------------------------\n    | GLOBAL VARIABLES\n    |--------------------------------------------------------------------------\n    */\n\n    protected $table = 'categories';\n    protected $primaryKey = 'id';\n    // public $timestamps = false;\n    // protected $guarded = ['id'];\n    protected $fillable = ['name', 'parent_id'];\n    // protected $hidden = [];\n    // protected $dates = [];\n\n    /**\n     * Return the sluggable configuration array for this model.\n     *\n     * @return array\n     */\n    public function sluggable()\n    {\n        return [\n            'slug' => [\n                'source' => 'slug_or_name',\n            ],\n        ];\n    }\n\n    /*\n    |--------------------------------------------------------------------------\n    | FUNCTIONS\n    |--------------------------------------------------------------------------\n    */\n\n    /*\n    |--------------------------------------------------------------------------\n    | RELATIONS\n    |--------------------------------------------------------------------------\n    */\n\n    public function parent()\n    {\n        return $this->belongsTo('App\\Models\\Category', 'parent_id');\n    }\n\n    public function children()\n    {\n        return $this->hasMany('App\\Models\\Category', 'parent_id');\n    }\n\n    public function articles()\n    {\n        return $this->hasMany('App\\Models\\Article');\n    }\n\n    /*\n    |--------------------------------------------------------------------------\n    | SCOPES\n    |--------------------------------------------------------------------------\n    */\n\n    public function scopeFirstLevelItems($query)\n    {\n        return $query->where('depth', '1')\n                    ->orWhere('depth', null)\n                    ->orderBy('lft', 'ASC');\n    }\n\n    /*\n    |--------------------------------------------------------------------------\n    | ACCESORS\n    |--------------------------------------------------------------------------\n    */\n\n    // The slug is created automatically from the \"name\" field if no slug exists.\n    public function getSlugOrNameAttribute()\n    {\n        if ($this->slug != '') {\n            return $this->slug;\n        }\n\n        return $this->name;\n    }\n\n    /*\n    |--------------------------------------------------------------------------\n    | MUTATORS\n    |--------------------------------------------------------------------------\n    */\n}",
      "language": "php",
      "name": "Category"
    },
    {
      "code": "<?php\n\nnamespace App\\Models;\n\nuse Illuminate\\Database\\Eloquent\\Model;\nuse Backpack\\CRUD\\CrudTrait;\nuse Cviebrock\\EloquentSluggable\\Sluggable;\n\nclass Tag extends Model\n{\n    use CrudTrait;\n    use Sluggable;\n\n    /*\n    |--------------------------------------------------------------------------\n    | GLOBAL VARIABLES\n    |--------------------------------------------------------------------------\n    */\n\n    protected $table = 'tags';\n    protected $primaryKey = 'id';\n    public $timestamps = true;\n    // protected $guarded = ['id'];\n    protected $fillable = ['name'];\n    // protected $hidden = [];\n    // protected $dates = [];\n\n    /**\n     * Return the sluggable configuration array for this model.\n     *\n     * @return array\n     */\n    public function sluggable()\n    {\n        return [\n            'slug' => [\n                'source' => 'slug_or_name',\n            ],\n        ];\n    }\n\n    /*\n    |--------------------------------------------------------------------------\n    | FUNCTIONS\n    |--------------------------------------------------------------------------\n    */\n\n    /*\n    |--------------------------------------------------------------------------\n    | RELATIONS\n    |--------------------------------------------------------------------------\n    */\n\n    public function articles()\n    {\n        return $this->hasMany('App\\Models\\Article');\n    }\n\n    /*\n    |--------------------------------------------------------------------------\n    | SCOPES\n    |--------------------------------------------------------------------------\n    */\n\n    /*\n    |--------------------------------------------------------------------------\n    | ACCESORS\n    |--------------------------------------------------------------------------\n    */\n\n    // The slug is created automatically from the \"name\" field if no slug exists.\n    public function getSlugOrNameAttribute()\n    {\n        if ($this->slug != '') {\n            return $this->slug;\n        }\n\n        return $this->name;\n    }\n\n    /*\n    |--------------------------------------------------------------------------\n    | MUTATORS\n    |--------------------------------------------------------------------------\n    */\n}\n",
      "language": "php",
      "name": "Tag"
    }
  ]
}
[/block]

[block:api-header]
{
  "type": "basic",
  "title": "4. Controllers"
}
[/block]
Place these in your **app\Http\Controllers\Admin** folder:
[block:code]
{
  "codes": [
    {
      "code": "<?php namespace App\\Http\\Controllers\\Admin;\n\nuse Backpack\\CRUD\\app\\Http\\Controllers\\CrudController;\n\n// VALIDATION: change the requests to match your own file names if you need form validation\nuse App\\Http\\Requests\\ArticleRequest as StoreRequest;\nuse App\\Http\\Requests\\ArticleRequest as UpdateRequest;\n\nclass ArticleCrudController extends CrudController {\n\n\tpublic function setup() {\n\n        /*\n\t\t|--------------------------------------------------------------------------\n\t\t| BASIC CRUD INFORMATION\n\t\t|--------------------------------------------------------------------------\n\t\t*/\n        $this->crud->setModel(\"App\\Models\\Article\");\n        $this->crud->setRoute(\"admin/article\");\n        $this->crud->setEntityNameStrings('article', 'articles');\n\n        /*\n\t\t|--------------------------------------------------------------------------\n\t\t| COLUMNS AND FIELDS\n\t\t|--------------------------------------------------------------------------\n\t\t*/\n\n        // ------ CRUD COLUMNS\n        $this->crud->addColumn([\n                                'name' => 'date',\n                                'label' => 'Date',\n                                'type' => 'date'\n                            ]);\n        $this->crud->addColumn([\n                                'name' => 'status',\n                                'label' => \"Status\"\n                            ]);\n        $this->crud->addColumn([\n                                'name' => 'title',\n                                'label' => \"Title\"\n                            ]);\n        $this->crud->addColumn([\n                                'name' => 'featured',\n                                'label' => \"Featured\",\n                                'type' => \"model_function\",\n                                'function_name' => 'getFeaturedColumn'\n                            ]);\n        $this->crud->addColumn([\n                                'label' => \"Category\",\n                                'type' => 'select',\n                                'name' => 'category_id',\n                                'entity' => 'category',\n                                'attribute' => 'name',\n                                'model' => \"App\\Models\\Category\"\n                            ]);\n\n        // ------ CRUD FIELDS\n        $this->crud->addField([    // TEXT\n                                'name' => 'title',\n                                'label' => 'Title',\n                                'type' => 'text',\n                                'placeholder' => 'Your title here'\n                            ]);\n        $this->crud->addField([\n                                'name' => 'slug',\n                                'label' => \"Slug (URL)\",\n                                'type' => 'text',\n                                'hint' => 'Will be automatically generated from your title, if left empty.'\n                                // 'disabled' => 'disabled'\n                            ]);\n\n        $this->crud->addField([    // TEXT\n                                'name' => 'date',\n                                'label' => 'Date',\n                                'type' => 'date',\n                                'value' => date('Y-m-d')\n                            ], 'create');\n        $this->crud->addField([    // TEXT\n                                'name' => 'date',\n                                'label' => 'Date',\n                                'type' => 'date'\n                            ], 'update');\n\n        $this->crud->addField([    // WYSIWYG\n                                'name' => 'content',\n                                'label' => 'Content',\n                                'type' => 'ckeditor',\n                                'placeholder' => 'Your textarea text here'\n                            ]);\n        $this->crud->addField([    // Image\n                                'name' => 'image',\n                                'label' => 'Image',\n                                'type' => 'browse'\n                            ]);\n        $this->crud->addField([    // SELECT\n                                'label' => \"Category\",\n                                'type' => 'select2',\n                                'name' => 'category_id',\n                                'entity' => 'category',\n                                'attribute' => 'name',\n                                'model' => \"App\\Models\\Category\"\n                            ]);\n        $this->crud->addField([    // Select2Multiple = n-n\n                                'label' => 'Tags',\n                                'type' => 'select2_multiple',\n                                'name' => 'tags',\n                                'entity' => 'tags',\n                                'attribute' => 'name',\n                                'model' => \"App\\Models\\Tag\",\n                                'pivot' => true,\n                            ]);\n        $this->crud->addField([    // ENUM\n                                'name' => 'status',\n                                'label' => \"Status\",\n                                'type' => 'enum'\n                            ]);\n        $this->crud->addField([    // CHECKBOX\n                                'name' => 'featured',\n                                'label' => \"Featured item\",\n                                'type' => 'checkbox'\n                            ]);\n    }\n\n\tpublic function store(StoreRequest $request)\n\t{\n\t\treturn parent::storeCrud($request);\n\t}\n\n\tpublic function update(UpdateRequest $request)\n\t{\n\t\treturn parent::updateCrud($request);\n\t}\n}",
      "language": "php",
      "name": "ArticleCrudController"
    },
    {
      "code": "<?php namespace App\\Http\\Controllers\\Admin;\n\nuse Backpack\\CRUD\\app\\Http\\Controllers\\CrudController;\n\n// VALIDATION: change the requests to match your own file names if you need form validation\nuse App\\Http\\Requests\\CategoryRequest as StoreRequest;\nuse App\\Http\\Requests\\CategoryRequest as UpdateRequest;\n\nclass CategoryCrudController extends CrudController {\n\n\tpublic function setup() {\n\n        /*\n\t\t|--------------------------------------------------------------------------\n\t\t| BASIC CRUD INFORMATION\n\t\t|--------------------------------------------------------------------------\n\t\t*/\n        $this->crud->setModel(\"App\\Models\\Category\");\n        $this->crud->setRoute(\"admin/category\");\n        $this->crud->setEntityNameStrings('category', 'categories');\n\n        /*\n\t\t|--------------------------------------------------------------------------\n\t\t| COLUMNS AND FIELDS\n\t\t|--------------------------------------------------------------------------\n\t\t*/\n\n        $this->crud->allowAccess('reorder');\n        $this->crud->enableReorder('name', 2);\n\n        // ------ CRUD COLUMNS\n        $this->crud->addColumn([\n                                'name' => 'name',\n                                'label' => 'Name',\n                            ]);\n        $this->crud->addColumn([\n                                'label' => 'Parent',\n                                'type' => 'select',\n                                'name' => 'parent_id',\n                                'entity' => 'parent',\n                                'attribute' => 'name',\n                                'model' => \"App\\Models\\Category\",\n                            ]);\n\n        // ------ CRUD FIELDS\n        $this->crud->addField([\n                                'name' => 'name',\n                                'label' => 'Name',\n                            ]);\n        $this->crud->addField([\n                                'label' => 'Parent',\n                                'type' => 'select',\n                                'name' => 'parent_id',\n                                'entity' => 'parent',\n                                'attribute' => 'name',\n                                'model' => \"App\\Models\\Category\",\n                            ]);\n    }\n\n\tpublic function store(StoreRequest $request)\n\t{\n\t\treturn parent::storeCrud($request);\n\t}\n\n\tpublic function update(UpdateRequest $request)\n\t{\n\t\treturn parent::updateCrud($request);\n\t}\n}",
      "language": "php",
      "name": "CategoryCrudController"
    },
    {
      "code": "<?php namespace App\\Http\\Controllers\\Admin;\n\nuse Backpack\\CRUD\\app\\Http\\Controllers\\CrudController;\n\n// VALIDATION: change the requests to match your own file names if you need form validation\nuse App\\Http\\Requests\\TagRequest as StoreRequest;\nuse App\\Http\\Requests\\TagRequest as UpdateRequest;\n\nclass TagCrudController extends CrudController {\n\n\tpublic function setup() {\n\n        /*\n\t\t|--------------------------------------------------------------------------\n\t\t| BASIC CRUD INFORMATION\n\t\t|--------------------------------------------------------------------------\n\t\t*/\n        $this->crud->setModel(\"App\\Models\\Tag\");\n        $this->crud->setRoute(\"admin/tag\");\n        $this->crud->setEntityNameStrings('tag', 'tags');\n\n        /*\n\t\t|--------------------------------------------------------------------------\n\t\t| COLUMNS AND FIELDS\n\t\t|--------------------------------------------------------------------------\n\t\t*/\n\n\t\t$this->crud->setFromDb();\n    }\n\n\tpublic function store(StoreRequest $request)\n\t{\n\t\treturn parent::storeCrud($request);\n\t}\n\n\tpublic function update(UpdateRequest $request)\n\t{\n\t\treturn parent::updateCrud($request);\n\t}\n}",
      "language": "php",
      "name": "TagCrudController"
    }
  ]
}
[/block]

[block:api-header]
{
  "type": "basic",
  "title": "5. Requests"
}
[/block]
Place these in your **app\Http\Requests** folder:  
[block:code]
{
  "codes": [
    {
      "code": "<?php\n\nnamespace App\\Http\\Requests;\n\nuse App\\Http\\Requests\\Request;\n\nclass ArticleRequest extends \\Backpack\\CRUD\\app\\Http\\Requests\\CrudRequest\n{\n    /**\n     * Determine if the user is authorized to make this request.\n     *\n     * @return bool\n     */\n    public function authorize()\n    {\n        // only allow updates if the user is logged in\n        return \\Auth::check();\n    }\n\n    /**\n     * Get the validation rules that apply to the request.\n     *\n     * @return array\n     */\n    public function rules()\n    {\n        return [\n            'title' => 'required|min:2|max:255',\n            'slug' => 'unique:articles,slug,'.\\Request::get('id'),\n            'content' => 'required|min:2',\n            'date' => 'required|date',\n            'status' => 'required',\n            'category_id' => 'required',\n        ];\n    }\n\n    /**\n     * Get the validation attributes that apply to the request.\n     *\n     * @return array\n     */\n    public function attributes()\n    {\n        return [\n            //\n        ];\n    }\n\n    /**\n     * Get the validation messages that apply to the request.\n     *\n     * @return array\n     */\n    public function messages()\n    {\n        return [\n            //\n        ];\n    }\n}",
      "language": "php",
      "name": "ArticleRequest"
    },
    {
      "code": "<?php\n\nnamespace App\\Http\\Requests;\n\nuse App\\Http\\Requests\\Request;\n\nclass CategoryRequest extends \\Backpack\\CRUD\\app\\Http\\Requests\\CrudRequest\n{\n    /**\n     * Determine if the user is authorized to make this request.\n     *\n     * @return bool\n     */\n    public function authorize()\n    {\n        // only allow updates if the user is logged in\n        return \\Auth::check();\n    }\n\n    /**\n     * Get the validation rules that apply to the request.\n     *\n     * @return array\n     */\n    public function rules()\n    {\n        return [\n            'name' => 'required|min:2|max:255'\n        ];\n    }\n\n    /**\n     * Get the validation attributes that apply to the request.\n     *\n     * @return array\n     */\n    public function attributes()\n    {\n        return [\n            //\n        ];\n    }\n\n    /**\n     * Get the validation messages that apply to the request.\n     *\n     * @return array\n     */\n    public function messages()\n    {\n        return [\n            //\n        ];\n    }\n}",
      "language": "php",
      "name": "CategoryRequest"
    },
    {
      "code": "<?php\n\nnamespace App\\Http\\Requests;\n\nuse App\\Http\\Requests\\Request;\n\nclass TagRequest extends \\Backpack\\CRUD\\app\\Http\\Requests\\CrudRequest\n{\n    /**\n     * Determine if the user is authorized to make this request.\n     *\n     * @return bool\n     */\n    public function authorize()\n    {\n        // only allow updates if the user is logged in\n        return \\Auth::check();\n    }\n\n    /**\n     * Get the validation rules that apply to the request.\n     *\n     * @return array\n     */\n    public function rules()\n    {\n        return [\n            'name' => 'required|min:2|max:255'\n        ];\n    }\n\n    /**\n     * Get the validation attributes that apply to the request.\n     *\n     * @return array\n     */\n    public function attributes()\n    {\n        return [\n            //\n        ];\n    }\n\n    /**\n     * Get the validation messages that apply to the request.\n     *\n     * @return array\n     */\n    public function messages()\n    {\n        return [\n            //\n        ];\n    }\n}",
      "language": "php",
      "name": "TagRequest"
    }
  ]
}
[/block]

[block:api-header]
{
  "type": "basic",
  "title": "6. Routes"
}
[/block]
Place these routes in your **app/routes.php** file. 

```php
Route::group(['prefix' => 'admin', 'middleware' => ['web', 'auth'], 'namespace' => 'Admin'], function()
{
	// Backpack\NewsManager routes
	CRUD::resource('article', 'ArticleCrudController');
	CRUD::resource('category', 'CategoryCrudController');
	CRUD::resource('tag', 'TagCrudController');
});
```
[block:api-header]
{
  "type": "basic",
  "title": "7. Menu items"
}
[/block]
Put menu items in your **sidebar.blade.php**:
[block:code]
{
  "codes": [
    {
      "code": "<li class=\"treeview\">\n  <a href=\"#\"><i class=\"fa fa-newspaper-o\"></i> <span>News</span> <i class=\"fa fa-angle-left pull-right\"></i></a>\n  <ul class=\"treeview-menu\">\n    <li><a href=\"{{ url('admin/article') }}\"><i class=\"fa fa-newspaper-o\"></i> <span>Articles</span></a></li>\n    <li><a href=\"{{ url('admin/category') }}\"><i class=\"fa fa-list\"></i> <span>Categories</span></a></li>\n    <li><a href=\"{{ url('admin/tag') }}\"><i class=\"fa fa-tag\"></i> <span>Tags</span></a></li>\n  </ul>\n</li>",
      "language": "html"
    }
  ]
}
[/block]