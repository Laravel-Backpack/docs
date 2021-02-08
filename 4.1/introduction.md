# Introduction

---

Backpack is a collection of Laravel packages that help you **build custom administration panels**, for anything from presentation websites to complex web applications. You can install them on top of existing Laravel installations _or_ fresh projects.

In a nutshell:

- Backpack will provide you with a _visual interface_ for the admin panel (the HTML, the CSS, the JS); it pulls in the excellent [CoreUI](https://coreui.io/) theme, with our own design called [Backstrap](https://backstrap.net), adds authentication functionality & bubble notifications; when you decide to build a custom feature for your admin panel, you already have the HTML blocks for the UI, and it will look good;
- Backpack will help you build _sections where your admins can manipulate entries for Eloquent models_; we call them _CRUD Panels_ after the most basic operations: Create/Read/Update/Delete; after [understanding Backpack](/docs/{{version}}/getting-started-basics), you'll be able to create a CRUD panel for each entity in about 10 minutes / model:

```bash
# STEP 0. create migration (in case you're starting from scratch)
php artisan make:migration:schema create_tags_table --model=0 --schema="name:string:unique"
php artisan migrate

# STEP 1. create a Model, Request, Controller, Route and sidebar item for the admin panel
php artisan backpack:crud tag #use singular, not plural

# STEP 2. go through the generated files, customize according to your needs
```

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

  - Laravel 8.x, 7.x or 6.x
  - MySQL (recommended) / PostgreSQL / SQLite / SQL Server

<a name="how-does-it-look"></a>
### How does it look?

- Take a look at [our homepage](https://backpackforlaravel.com/).
- Play around in our [live demo](https://demo.backpackforlaravel.com/admin/login).
- [Install the demo](/docs/{{version}}/demo) and fiddle with the code.

<a name="security"></a>
### Security

Backpack has never had a critical vulnerability/hack. But there _have_ been important security updates for dependencies (including Laravel). Please [login with Github](/auth/github) or  [subscribe to our security newsletter](https://backpackforlaravel.com/newsletter), so we can reach you in case anything bad happens. No spam, no marketing emails, we promise. We only send about 2 emails per year, when we introduce major Backpack updates or there's a problem you should know about.

<a name="maintenance"></a>
### Maintenance

Backpack 4.1 is the current version, and is being actively maintained by the Backpack team, with the help of a wonderful community of Backpack veterans. [See all contributors](https://github.com/Laravel-Backpack/CRUD/graphs/contributors).

<a name="license"></a>
### License

Backpack is under a license we call "_You make money, I make money_" (YummY). Backpack's source is public, and you can use it for free for non-commercial purposes (testing, non-profits, personal use, etc), but if you make money using it, you need to purchase a commercial license. Please see  [the pricing section](https://backpackforlaravel.com/pricing) for more details. In production, you need a license code for both commercial and non-commercial use, to prevent nagging notification bubbles. **On localhost, you don't need a license code at all.**

<a name="versioning"></a>
### Versioning

When installing Backpack, require its minor version (currently ```4.1.*```). Backpack follows the same versioning system as did prior to Laravel 6 - minor Backpack versions _will_ include breaking changes. This allows us to push new features without charging our users again. For us, this is what ```major.minor.patch``` means:

- ```major``` - **PAID upgrade; MAJOR breaking changes;** historically every 2-3 years; upgrading may take even 2-3 hours; includes MAJOR new features, MAJOR changes in how the whole system works, and complete rewrites; it allows us to _considerably_ improve how we build admin panels and add features that were previously impossible;
- ```minor``` - **FREE upgrade; SOME breaking changes**; historically every 6 months; upgrading takes 15-60 minutes; offers big new features, for free;
- ```patch``` - **FREE upgrade; NO breaking changes**; historically every week; upgrading can be done automatically with composer; includes bug fixes and non-breaking new features;

<a name="add-ons"></a>
### Add-ons

In addition to our core CRUD package, there are a few additional packages you might want to take a look at, that treat common use cases. Some have been developed by our core team, some by our wonderful community. You can just install interfaces to manage [site-wide settings](https://github.com/Laravel-Backpack/Settings), [the default Laravel users table](https://github.com/eduardoarandah/UserManager), [users, groups & permissions](https://github.com/Laravel-Backpack/PermissionManager), [content for custom pages, using page templates](https://github.com/Laravel-Backpack/PageManager), [news articles, categories and tags](https://github.com/Laravel-Backpack/NewsCRUD), etc.

For more information, please see:
- [all official add-ons](/docs/{{version}}/add-ons-official)
- [all community add-ons](/docs/{{version}}/add-ons-community)
