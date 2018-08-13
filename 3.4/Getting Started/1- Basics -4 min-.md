---
title: "1. Basics [4 min]"
excerpt: ""
---
Hello newbie,

Thanks for checking out Backpack for Laravel. In this series we’ll walk you through the basics of using Backpack. By the end you should be able to create your own admin panels using Backpack, from the simple to the most complex. It really doesn’t get much more difficult to understand than this simple series.
[block:callout]
{
  "type": "warning",
  "title": "Are you already comfortable with Laravel?",
  "body": "In order to understand this series, and make use of Backpack, you’ll need to have a decent understanding of the Laravel framework. If you don’t, please go ahead and [watch this excellent intro series on Laracasts](https://laracasts.com/series/laravel-from-scratch-2017). We'll wait."
}
[/block]
# What is Backpack?
A software that helps Laravel professionals build administration panels - secure areas where administrators login and create, read, update and delete application information. It is *not* a CMS, it is more a framework that lets you *build your own* CMS. You can install it in your existing project or in a totally new project. 

It’s designed to be flexible enough to allow you to **build admin panels for everything from simple presentation websites to CRMs, ERPs, eCommerce, eLearning, etc**. We can vouch for that, because we have built all that complex stuff using Backpack already.

# How to use?
Each Backpack package has its own specific purpose. But let’s start with the most important packages, the ones you’ll definitely be using in each of your projects:
- **Backpack\Base** - provides the design of the admin area (based on [AdminLTE](https://adminlte.io/themes/AdminLTE/index2.html))
- **Backpack\CRUD** - empowers you to create **CRUDs**, really fast

A **CRUD** is what we call a section of your admin panel that lets the admin Create, Read, Update or Delete entries of a certain entity (or Model). So you can have a CRUD for Products, a CRUD for Articles, a CRUD for Categories, or whatever else you might want to create, read, update or delete.

For the purpose of this series, we’ll show examples on the Tag entry. This is what it could look like:
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/327c2a0-Screen_Shot_2017-11-08_at_17.18.38.png",
        "Screen Shot 2017-11-08 at 17.18.38.png",
        2880,
        1592,
        "#f3f6f7"
      ]
    }
  ]
}
[/block]
## Backpack\Base
After you [install Backpack](https://laravel-backpack.readme.io/v3.4/docs/install-on-laravel-56) (don't do it now), you’ll be able to access your admin panel at ```http://yourapp/admin```. You can change the ```admin``` prefix in your ```config/backpack/base.php``` file, along with a bunch of other configuration options. [Click here](https://github.com/Laravel-Backpack/Base/blob/master/src/config/backpack/base.php) to browse the configuration file and see what it can do for you.

**Backpack/Base** is the package that **will handle the authentication** and provide you with minimal admin area functionality. **Your admin will be able to login and change his password or email.** And that’s pretty much it. 

Backpack\Base pulls in the free [AdminLTE](https://adminlte.io/themes/AdminLTE/index2.html) theme and enhances the design a little bit. So any front-end block that AdminLTE has, you'll also be able to use in your custom admin panel. It also includes a simple notification bubble system, which you can use across the admin panel. You can easily [trigger notification bubbles in PHP](https://laravel-backpack.readme.io/v3.4/docs/base#section-triggering-notification-bubbles-in-php) or [trigger notification bubbles in JavaScript](https://laravel-backpack.readme.io/v3.4/docs/base#section-triggering-notification-bubbles-in-javascript).


## Backpack\CRUD
This is where it gets interesting. As soon as you [install Backpack](https://laravel-backpack.readme.io/docs/install-on-laravel-56) in your project, you can create **CRUDs** for your admins to easily manipulate DB information. Let’s browse through a simple example, of creating a CRUD administration panel for a Tag entity:
[block:code]
{
  "codes": [
    {
      "code": "# STEP 1. create migration\nphp artisan make:migration:schema create_tags_table --model=0 --schema=\"name:string:unique\"\nphp artisan migrate\n\n# STEP 2. create a model, a request and a controller for the admin panel\nphp artisan backpack:crud tag #use singular, not plural\n\n# STEP 3. add a route to routes/backpack/custom.php (under the admin prefix and auth middleware): \nphp artisan backpack:base:add-custom-route \"CRUD::resource('tag', 'TagCrudController');\"\n\n# STEP 4. add a sidebar item\nphp artisan backpack:base:add-sidebar-content \"<li><a href='{{ backpack_url('tag') }}'><i class='fa fa-tag'></i> <span>Tags</span></a></li>\"\n",
      "language": "shell",
      "name": "Terminal commands"
    }
  ]
}
[/block]
This will create a simple CRUD panel, which you should now be able to see in the Sidebar.

For a simple entry like this, the generated CRUD panel will probably work “as is”, but don’t expect it for more complex entities. They will usually require some customization, have particularities, etc. That’s where Backpack shines - modifying anything in the CRUD panel is easy and intuitive, once you understand it.

The code above would generate:
- a **migration** file
- a **model** (app\Models\Tag.php)
- a **request** file, where the validation rules live (app\Http\Requests\TagCrudRequest.php)
- a **controller** file, where you can customize how the CrudPanel looks and feels (app\Http\Controllers\Admin\TagCrudController.php)
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/b3197f1-HX9EsTHTJOjfZjzDG7cc_Screen_Shot_2016-05-20_at_19.22.53.png",
        "HX9EsTHTJOjfZjzDG7cc_Screen Shot 2016-05-20 at 19.22.53.png",
        316,
        1027,
        "#e2e2e1"
      ]
    }
  ]
}
[/block]
**That’s all for today! **If you want to learn more, you can [take a look at the Tag CRUD files in this article](https://laravel-backpack.readme.io/v3.4/docs/crud-example) or go ahead and read the next lesson on the Backpack blog.