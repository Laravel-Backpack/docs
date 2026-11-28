# Omni Search <span class="badge badge-pill badge-warning">GOLD</span>

--

<a name="about"></a>
## About

Omni Search adds a global command palette to the Backpack admin panel. Admins can search configured Eloquent models, jump to pages from the sidebar, and run application actions from one place.

Search results are grouped by entity, ranked with exact and prefix matches first, and rendered using Backpack chip views or configurable titles and details. Permissions are checked both when results are shown and again by the endpoints that return results or run actions.

![Omni Search palette](https://backpackforlaravel.com/uploads/news_images/omnisearch.jpg)

Omni Search is a GOLD-exclusive private add-on. It is no longer sold separately; access is included with every [GOLD plan](https://backpackforlaravel.com/pricing#gold).

<a name="requirements"></a>
## Requirements

- Backpack for Laravel v7.1 or newer
- Laravel 12 or newer
- PHP 8.2 or newer
- The Tabler theme (`backpack/theme-tabler`)

<a name="installation"></a>
## Installation

1) Configure Composer access to Backpack's private repository with your access token. If you already installed Backpack PRO or another private add-on, the repository and credentials are already configured.

```bash
composer config http-basic.backpackforlaravel.com [your-token-username] [your-token-password]
```

Add the private Composer repository to `composer.json` if it is not already configured:

```json
{
    "repositories": [
        {
            "type": "composer",
            "url": "https://repo.backpackforlaravel.com/"
        }
    ]
}
```

2) Install the package and run its installer:

```bash
composer require backpack/omnisearch
php artisan omnisearch:install
```

The installer publishes `config/backpack/omnisearch.php` and adds the palette button to the Tabler top bar. It leaves existing files unchanged and can be run again safely.

3) Configure at least one searchable entity or leave the default menu shortcuts enabled. Then open the admin panel and press `Cmd`+`K` on macOS or `Ctrl`+`K` on other platforms.

<a name="configuration"></a>
## Configuration

Configuration lives in `config/backpack/omnisearch.php`. Entries are cacheable: do not put closures in this file. For dynamic URLs and actions, use a class and method or an invokable class.

### Search entities

Add a model and the columns to search under `entities`:

```php
'entities' => [
    \App\Models\Invoice::class => [
        'can' => 'manage invoices',
        'search' => ['customer_name', 'customer_email'],
        'exact' => ['id', 'number'],
        'url' => 'invoice/{id}/show',
        'title' => '#{number} {customer_name}',
        'details' => [
            'la la-envelope' => '{customer_email}',
        ],
    ],
],
```

Only `search` is needed for a basic entity. The label defaults to the model's plural name, and the URL defaults to `<kebab-case model>/{id}/show`. Use `url` when your CRUD route differs or you want the result to open another page. Patterns can use model attributes, including relationship attributes such as `{author.name}`.

Other useful entity options include:

- `chip`: a Blade view to render a result; it receives `$entry`;
- `with`: relationships to eager-load for result rendering;
- `scopes`: Eloquent model scopes to apply to the search;
- `query`: a class method or invokable handler that narrows the query;
- `limit`: a per-entity result count, overriding the global limit.

### Add shortcuts and actions

Sidebar links become shortcuts by default, respecting their menu visibility checks. Add explicit shortcuts for other pages or application actions in the `shortcuts` array:

```php
'shortcuts' => [
    [
        'label' => 'Reorder categories',
        'url' => 'category/reorder',
        'description' => 'Sort blog categories',
        'keywords' => 'sort',
        'can' => 'manage blog categories',
    ],
    [
        'label' => 'Rebuild the search index',
        'run' => \App\Actions\RebuildSearchIndex::class,
        'can' => 'manage settings',
        'confirm' => 'Rebuild the search index now?',
    ],
],
```

A shortcut can navigate to a static path or URL, build a URL through a container-resolved class method, or run an action. Action handlers can return a string or an array containing a toast `message`, optional `type`, and optional `redirect`.

<a name="permissions"></a>
## Permissions and row access

Set `can` on entities and shortcuts to require a Laravel Gate ability. The palette hides unauthorized items, and the JSON endpoints enforce the same ability when searched or run.

Entity permissions control access to the entity, not individual rows. Search queries the model directly rather than running through the CrudController, so clauses configured in the controller do not automatically apply. To restrict rows, use model `scopes` or a `query` handler, and apply the same rule used by your CRUD list.

<a name="customization"></a>
## Customization

Publish the views to customize the button, palette, result chips, and shortcut rows:

```bash
php artisan vendor:publish --tag=omnisearch-views
```

The palette uses Tabler theme variables and adapts to skins and dark mode. To open it from another element, add the `data-omnisearch-open` attribute.

A custom search driver can be bound to `Backpack\Omnisearch\Contracts\Driver` if you need to use another backend. The default driver searches through the database and requires no separate search service.

<a name="localization"></a>
## Localization

The palette ships with English, Romanian, Bulgarian, Portuguese, Spanish, French, and German translations. Labels in the config may be literal strings or Laravel translation keys. Publish the language files to customize them or add a locale:

```bash
php artisan vendor:publish --tag=omnisearch-lang
```

If you use locale middleware, make sure it also runs on Omni Search endpoints. The package uses Backpack's admin middleware; locale middleware registered through Backpack's `middleware_class` applies there as well.

<a name="keyboard-shortcuts"></a>
## Keyboard shortcuts

| Shortcut | Action |
| --- | --- |
| `Cmd`+`K` / `Ctrl`+`K` or `/` | Open the palette (`/` is ignored in form fields) |
| `Up` / `Down` | Move between results |
| `Enter` | Open the selected result or run the action |
| `Cmd`+`Enter` / `Ctrl`+`Enter` | Open the selected result in a new tab |
| `Esc` | Close the palette |

<a name="license"></a>
## License and support

Omni Search is proprietary software released under Backpack's [EULA for private add-ons](https://backpackforlaravel.com/eula). For package support, open a ticket from your Backpack account. Security issues can be reported to `cristian.tabacitu@backpackforlaravel.com`.
