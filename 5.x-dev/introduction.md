# Introduction

---

Backpack is a collection of Laravel packages that help you **build custom administration panels**, for anything from presentation websites to complex web applications. You can install them on top of existing Laravel installations _or_ fresh projects.

In a nutshell:

- Backpack will provide you with a _visual interface_ for the admin panel (the HTML, the CSS, the JS); it pulls in the excellent [CoreUI](https://coreui.io/) theme, with our own design called [Backstrap](https://backstrap.net), adds authentication functionality & bubble notifications; when you decide to build a custom feature for your admin panel, you already have the HTML blocks for the UI, and it will look good;
- Backpack will help you build _sections where your admins can manipulate entries for Eloquent models_; we call them _CRUD Panels_ after the most basic operations: Create/Read/Update/Delete; after [understanding Backpack](/docs/{{version}}/getting-started-basics), you'll be able to create a CRUD panel for each entity in about 10 minutes / model:

```bash
# STEP 0. create migration (in case you're starting from scratch)
composer require --dev laracasts/generators
php artisan make:migration:schema create_tags_table --model=0 --schema="name:string:unique"
php artisan migrate

# STEP 1. create a Model, Request, Controller, Route and sidebar item for the admin panel
php artisan backpack:crud tag #use singular, not plural

# STEP 2. go through the generated files, customize according to your needs
```

Better yet, if you purchase our brand-new [Backpack DevTools](https://backpackforlaravel.com/products/devtools) (premium addon, paid extra) you can generate better Migrations, Models, Seeders, Factories and CRUDs... from the comfort of your browser window 🤯 It's never been this easy:

![](https://user-images.githubusercontent.com/1032474/128379216-72ae55fa-fcff-4747-8c35-42c733923c94.gif)

---

<a name="how-to-start"></a>
## How to Start

We heavily recommend you spend a little time to understand Backpack, and only afterwards install and use it. Fortunately it's super-simple to get started. Using any of the options below will get you to a point where you can use Backpack in your projects:
- **[Video Course](/docs/{{version}}/getting-started-videos)** - 31 minutes
- **[Text Course](/docs/{{version}}/getting-started-basics)** - 20 minutes
- **[Email Course](https://backpackforlaravel.com/getting-started-emails)** - 1 email per day, for 4 days, 5 minutes each


<br>
<a href="/docs/{{version}}/getting-started-videos" class="btn btn-outline-info btn-sm shadow"><i class="fe fe-video"></i> Video Course</a>
<a href="/docs/{{version}}/getting-started-basics" class="btn btn-outline-info btn-sm shadow"><i class="fe fe-file-text"></i> Text Course</a>
<a href="https://backpackforlaravel.com/getting-started-emails" class="btn btn-outline-info btn-sm shadow"><i class="fe fe-inbox"></i> Email Course</a>

---

<a name="need-to-know"></a>
## Need to Know

<a name="requirements"></a>
### Requirements

  - Laravel 9.x or 8.x
  - MySQL (recommended) / PostgreSQL / SQLite / SQL Server

<a name="how-does-it-look"></a>
### How does it look?

- Take a look at [our homepage](https://backpackforlaravel.com/).
- Play around in our [live demo](https://demo.backpackforlaravel.com/admin/login).
- [Install the demo](/docs/{{version}}/demo) and fiddle with the code.

<a name="security"></a>
### Security

Backpack has never had a critical vulnerability/hack. But there _have_ been important security updates for dependencies (including Laravel). Please [login with GitHub](/auth/github) or  [subscribe to our security newsletter](https://backpackforlaravel.com/newsletter), so we can reach you in case anything bad happens. No spam, no marketing emails, we promise. We only send about 2 emails per year, when there's a problem you should know about or we introduce major Backpack upgrades.

<a name="maintenance"></a>
### Maintenance

Backpack v5 is the current version, and is being actively maintained by the Backpack team, with the help of a wonderful community of Backpack veterans. [See all contributors](https://github.com/Laravel-Backpack/CRUD/graphs/contributors).

<a name="license"></a>
### License

Starting with v5, Backpack has become open-core. The main features are now split into two packages:

- [Backpack\CRUD](https://github.com/laravel-backpack/crud) is the core, released under the [MIT License](https://github.com/Laravel-Backpack/CRUD/blob/master/LICENSE.md) (free, open-source); <span class="badge badge-pill badge-success">FREE</span>
- [Backpack\PRO](https://backpackforlaravel.com/products/pro) is a Backpack add-on, released under our [EULA](https://backpackforlaravel.com/eula) (paid, closed-source); <span class="badge badge-pill badge-info">PRO</span>

Backpack\CRUD is perfect if you're building a simple admin panel - it's packed with features! It's also perfect if you're building an open-source project, the permissive license allows you to do whatever you want.

When your admin panel grows and your needs become more complex, you can purchase our [Backpack\PRO](https://backpackforlaravel.com/products/pro) add-on, which adds A LOT of features for complex use-cases (see [list here]([Backpack\Pro](https://backpackforlaravel.com/products/pro))). Our documentation includes instructions on how to use both Backpack\CRUD and Backpack\PRO, with all the PRO features clearly labeled <span class="badge badge-pill badge-info">PRO</span>


<a name="versioning"></a>
### Versioning, Updates and Upgrades

Starting with Backpack v5, all our packages follow [semantic versioning](https://semver.org/). Here's what `major.minor.patch` (eg. `5.0.1`) means for Backpack\CRUD:
- `major` - breaking changes, major new features, complete rewrites; released **once a year**, in February; it adds features that were previously impossible and upgrades our dependencies; upgrading is done by following our clear and detailed upgrade guides;
- `minor` - new features, released in backwards-compatible ways; **every few months**; update takes seconds;
- `patch` - bug fixes & small non-breaking changes; historically **every week**; update takes seconds;

When we release a new Backpack\CRUD version, all paid addons receive support for it the same day. And because (1) we release a new version every year and (2) when you buy a Backpack addon, you get access to not only _updates_, but also _upgrades_ (for 12mo), that means that... **any time you buy a Backpack addon, it is very likely that you're not only buying the _current_ version** (`v5` at the moment), **but also the upgrade to the _next version_** (`v6` for example).

<a name="add-ons"></a>
### Add-ons

In addition to our core CRUD package, there are a few additional packages you might want to take a look at, that treat common use cases. Some have been developed by our core team, some by our wonderful community. You can just install interfaces to manage [site-wide settings](https://github.com/Laravel-Backpack/Settings), [the default Laravel users table](https://github.com/eduardoarandah/UserManager), [users, groups & permissions](https://github.com/Laravel-Backpack/PermissionManager), [content for custom pages, using page templates](https://github.com/Laravel-Backpack/PageManager), [news articles, categories and tags](https://github.com/Laravel-Backpack/NewsCRUD), etc.

For more information, please see:
- [all official add-ons](/docs/{{version}}/add-ons-official)
- [all community add-ons](/docs/{{version}}/add-ons-community)
