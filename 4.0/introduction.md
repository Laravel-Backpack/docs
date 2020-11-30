# Getting Started

---

Backpack is a collection of Laravel packages that help you **build custom administration panels**, for anything from presentation websites to complex web applications. You can install them on top of existing Laravel installations _or_ fresh projects.

In a nutshell:

- Backpack will provide you with a _visual interface_ for the admin panel (the HTML, the CSS, the JS); it pulls in the excellent [CoreUI](https://coreui.io/) theme, with our own design called [Backstrap](https://backstrap.net), adds authentication functionality & bubble notifications; when you decide to build a custom feature for your admin panel, you already have the HTML blocks for the UI, and it will look good;
- Backpack will help you build _sections where your admins can manipulate entries for Eloquent models_; we call them _CRUD Panels_ after the most basic operations: Create/Read/Update/Delete; after [understanding Backpack](/docs/{{version}}/getting-started-basics), you'll be able to create a CRUD panel for each entity in about 10 minutes / model:

```bash
# STEP 0. create migration (in case you're starting from scratch)
php artisan make:migration:schema create_tags_table --model=0 --schema="name:string:unique"
php artisan migrate

# STEP 1. create a model, a request and a controller for the admin panel
php artisan backpack:crud tag #use singular, not plural

# STEP 2. add a route for this admin panel to routes/backpack/custom.php
php artisan backpack:add-custom-route "Route::crud('tag', 'TagCrudController');"

# STEP 3. go through the generated files, customize according to your needs
```

<a name="need-to-know"></a>
## Need to Know

<a name="requirements"></a>
### Requirements

  - Laravel 7, Laravel 6 or Laravel 5.8
  - PHP 7.2.5+
  - MySQL (recommended) / PostgreSQL / SQLite / SQL Server

<a name="screenshots"></a>
### Screenshots

Take a look at [our homepage](https://backpackforlaravel.com/).

<a name="demo"></a>
### Demo

You can easily [install our demo Laravel project with Backpack installed](/docs/{{version}}/demo) and play around.

<a name="security"></a>
### Security

Backpack has never had a critical vulnerability/hack. But there _have_ been important security updates for dependencies (including Laravel). Please [login with Github](/auth/github) or  [subscribe to our monthly newsletter](https://backpackforlaravel.com/newsletter), so we can reach you in case anything bad happens. No spam, no marketing emails, we promise. We only send one email per month max, when we introduce major Backpack updates.

<a name="maintenance"></a>
### Maintenance

Backpack 4.0 is the current version, and is being actively maintained by Backpack's creator, [Cristian Tabacitu](https://tabacitu.ro), with the help of a wonderful community of Backpack veterans. [See all contributors](https://github.com/Laravel-Backpack/CRUD/graphs/contributors).

<a name="license"></a>
### License

Backpack is under a license we call "_You make money, I make money_" (YummY). Backpack's source is public, and you can use it for free for non-commercial purposes (testing, non-profits, personal use, etc), but if you make money using it, you need to purchase a commercial license. Please see  [the pricing section](https://backpackforlaravel.com/pricing) for more details. In production, you need a license code for both commercial and non-commercial use, to prevent nagging notification bubbles. **On localhost, you don't need a license code.**

<a name="add-ons"></a>
### Add-ons

In addition to our core CRUD package, there are a few extra that treat common use cases. Some have been developed by our core team, some by our wonderful community. You can just install interfaces to manage [site-wide settings](https://github.com/Laravel-Backpack/Settings), [the default Laravel users table](https://github.com/eduardoarandah/UserManager), [users, groups & permissions](https://github.com/Laravel-Backpack/PermissionManager), [content for custom pages, using page templates](https://github.com/Laravel-Backpack/PageManager), [news articles, categories and tags](https://github.com/Laravel-Backpack/NewsCRUD), etc.

For more, please see:
- [all official add-ons](/docs/{{version}}/add-ons-official)
- [all community add-ons](/docs/{{version}}/add-ons-community)

<a name="how-to-start"></a>
## How to Start

We heavily recommend you spend a little time to understand Backpack, and only afterwards install and use it. Currently your options are:
- **[Text Tutorial](/docs/{{version}}/getting-started-basics)** - 23 minutes
- **[Email Tutorial](https://backpackforlaravel.com/getting-started-emails)** - 1 email per day, for 5 days, 5 minutes each
- **Video Tutorial** - working on it
