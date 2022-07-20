# Introduction

---

Backpack is a collection of Laravel packages that help you **build custom administration panels**, for anything from presentation websites to complex web applications. You can install them on top of existing Laravel installations _or_ fresh projects.

In a nutshell:

- Backpack will provide you with a _visual interface_ for the admin panel (the HTML, the CSS, the JS); it pulls in the excellent [CoreUI](https://coreui.io/) theme, with our own design called [Backstrap](https://backstrap.net), and adds authentication functionality & bubble notifications; when you decide to build a custom feature for your admin panel, you already have the HTML blocks for the UI, and it will look good;
- Backpack will also help you build _sections where your admins can manipulate entries for Eloquent models_; we call them _CRUD Panels_ after the most basic operations: Create, Read, Update & Delete; after [understanding Backpack](/docs/{{version}}/getting-started-basics), you'll be able to create a CRUD panel in a few minutes per model:

![](https://user-images.githubusercontent.com/1032474/86720524-c5a1d480-c02d-11ea-87ed-d03b0197eb25.gif)

If you already have your Eloquent models, generating Backpack CRUDs is as simple as:
```bash
# -------------------------------
# For one specific Eloquent Model
# -------------------------------
# Create a Model, Request, Controller, Route and sidebar item, so
# that one Eloquent model you specify has an admin panel.

php artisan backpack:crud tag # use singular, not plural (like the Model name)

# -----------------------
# For all Eloquent Models
# -----------------------
# Create a Model, Request, Controller, Route and sidebar item for
# all Eloquent models that don't already have one.

php artisan backpack:build
```

If you have NOT created your Eloquent models yet, you can use whatever you want for that. We recommend:
- FREE - [`laracasts/generators`](https://github.com/laracasts/Laravel-5-Generators-Extended) as the best **command-line tool** for this;
- FREE - [`laravel-shift/blueprint`](https://github.com/laravel-shift/blueprint) as the best **YAML-based tool** for this;
- PAID - [`backpack/devtools`](https://backpackforlaravel.com/products/devtools) as the best **web interface** for this; it makes it dead-simple to create Eloquent models, from the comfort of your web browser;

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
  - MySQL / PostgreSQL / SQLite / SQL Server

<a name="how-does-it-look"></a>
### How does it look?

**Take a look at our [live demo](https://demo.backpackforlaravel.com/admin/login).** If you've purchased ["Everything"](https://backpackforlaravel.com/pricing) you can even [install the demo](/docs/{{version}}/demo) and fiddle with the code. Otherwise, you can just start a new Laravel project, [install Backpack\CRUD](/docs/{{version}}/installation) on top, and [follow our text course](/docs/{{version}}/getting-started-basics) to create a few CRUDs.

<a name="security"></a>
### Security

Backpack has never had a critical vulnerability/hack. But there _have_ been important security updates for dependencies (including Laravel). Please [register using Github](/auth/github) or  [subscribe to our twice-a-year newsletter](https://backpackforlaravel.com/newsletter), so we can reach you in case your admin panel becomes vulnerable in any way.

<a name="maintenance"></a>
### Maintenance

Backpack v5 is the current version, and is being actively maintained by the Backpack team, with the help of a wonderful community of Backpack veterans. [See all contributors](https://github.com/Laravel-Backpack/CRUD/graphs/contributors).

<a name="license"></a>
### License

Starting with v5, Backpack has become open-core. Its features have been separated into two packages:
- **Backpack CRUD is licensed under the [MIT License](LICENSE.md)** (open-source free software); it is perfect if you're building a simple admin panel - it's packed with features! it's also perfect if you're building an open-source project, the permissive license allows you to do whatever you want;
- **Backpack PRO is licensed under our [EULA](https://backpackforlaravel.com/eula)**; it is a closed-source, paid add-on; [PRO](https://backpackforlaravel.com/products/pro) will be useful when your admin panel needs grow, because it adds adds A LOT of features for complex use cases (see our [FREE vs PRO comparison](https://backpackforlaravel.com/docs/5.x/features-free-vs-paid));

[Our documentation](https://backpackforlaravel.com/docs) covers both CRUD and PRO, with all the PRO features clearly labeled <span class="badge badge-pill badge-info">PRO</span>.


<a name="versioning"></a>
### Versioning, Updates and Upgrades

Starting with Backpack v5, all our packages follow [semantic versioning](https://semver.org/). Here's what `major.minor.patch` (eg. `5.0.1`) means for us:
- `major` - breaking changes, major new features, complete rewrites; released **once a year**, in February; it adds features that were previously impossible and upgrades our dependencies; upgrading is done by following our clear and detailed upgrade guides;
- `minor` - new features, released in backwards-compatible ways; **every few months**; update takes seconds;
- `patch` - bug fixes & small non-breaking changes; historically **every week**; update takes seconds;

When we release a new Backpack\CRUD version, all paid addons receive support for it the same day. 

When you buy a premium Backpack addon, you get access to not only _updates_, but also _upgrades_ (for 12mo), that means that... **any time you buy a Backpack addon, it is very likely that you're not only buying the _current_ version** (`v5` at the moment), **but also the upgrade to the _next version_** (`v6` for example).

<a name="add-ons"></a>
### Add-ons

Backpack's core is open-source and free (Backpack\CRUD). <span class="badge badge-pill badge-success">FREE</span>

The reason we've been able to build and maintain Backpack since 2016 is that Laravel professionals have supported us, by buying our paid products. As of 2022, these are all Backpack add-ons, which we highly recommend:
- [Backpack PRO](/pricing) - a crazy amount of added features <span class="badge badge-pill badge-warning">PAID</span>
- [Backpack DevTools](/products/devtools) - a developer UI for generating migrations, models and CRUDs; <span class="badge badge-pill badge-warning">PAID</span>
- [Backpack FigmaTemplate](/products/figma-template) - quickly create designs and mockups, using Backpack's design; <span class="badge badge-pill badge-warning">PAID</span>
- [Backpack EditableColumns](/products/editable-columns) - let your admins do quick edits, right in the table view; <span class="badge badge-pill badge-warning">PAID</span>


In addition to our open-source core and our closed-source addons, there are a few other addons you might want to take a look at, that treat common use cases. Some have been developed by our core team, some by our wonderful community. You can just install interfaces to manage [site-wide settings](https://github.com/Laravel-Backpack/Settings), [the default Laravel users table](https://github.com/eduardoarandah/UserManager), [users, groups & permissions](https://github.com/Laravel-Backpack/PermissionManager), [content for custom pages, using page templates](https://github.com/Laravel-Backpack/PageManager), [news articles, categories and tags](https://github.com/Laravel-Backpack/NewsCRUD), etc. <span class="badge badge-pill badge-success">FREE</span>

For more information, please see [our addons page](/addons).
