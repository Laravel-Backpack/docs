# Auto Translate <span class="badge badge-pill badge-warning">PAID EXTRA</span>

--

<a name="about"></a>
## About

Backpack Auto Translate translates Eloquent model attributes managed by [`spatie/laravel-translatable`](https://github.com/spatie/laravel-translatable). It can translate existing records from Artisan commands, translate models after create or update events, or translate records when called from your application code.

The package supports multiple translation providers through swappable drivers, including OpenAI, DeepL, and Prism. It also supports queued jobs, response caching, and preserving configured HTML tags and placeholders.

![Auto Translate](https://github.com/user-attachments/assets/513101da-4048-4b4f-9156-6a78a3d9be30)

Auto Translate is a paid private add-on. See the [product page](https://backpackforlaravel.com/products/auto-translate) for current access and pricing details.

<a name="requirements"></a>
## Requirements

- Laravel 12 or newer
- PHP 8.2 or newer
- [`spatie/laravel-translatable`](https://github.com/spatie/laravel-translatable) configured on the models you want to translate

<a name="installation"></a>
## Installation

1) Configure Composer access to Backpack's private repository with your access token. If you already installed another private Backpack add-on, the repository and credentials are already configured.

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

2) Install Auto Translate and optionally publish its configuration:

```bash
composer require backpack/auto-translate
php artisan vendor:publish --tag=config --provider="Backpack\AutoTranslate\AddonServiceProvider"
```

The configuration is published to `config/backpack/auto-translate.php`.

3) Choose a translation driver and install its dependencies. The package provides drivers for OpenAI, DeepL, and Prism, and supports custom drivers. See the setup guides for [OpenAI](https://backpackforlaravel.com/products/docs/OPENAI_DRIVER.md), [DeepL](https://backpackforlaravel.com/products/docs/DEEPL_DRIVER.md), [Prism](https://backpackforlaravel.com/products/docs/PRISM_DRIVER.md), or [custom drivers](https://backpackforlaravel.com/products/docs/CUSTOM_DRIVER.md).

4) Configure the driver and its credentials in your environment. For example:

```env
AUTO_TRANSLATE_ENABLED=true
AUTO_TRANSLATE_DRIVER=openai
OPENAI_API_KEY=your-api-key
OPENAI_TRANSLATE_MODEL=gpt-4o-mini
```

5) Confirm your models use JSON columns for their translatable attributes. This package populates existing `spatie/laravel-translatable` attributes; it does not add or migrate those columns for you.

6) If using queued translations, configure a queue worker. For example:

```bash
php artisan queue:work --queue=translations
```

<a name="model-setup"></a>
## Model Setup

Add `HasAutoTranslations` to models that should support automatic translation. Keep Spatie's `HasTranslations` trait and define the translatable attributes as usual:

```php
use Backpack\AutoTranslate\Traits\HasAutoTranslations;
use Illuminate\Database\Eloquent\Model;
use Spatie\Translatable\HasTranslations;

class Event extends Model
{
    use HasTranslations, HasAutoTranslations;

    public array $translatable = ['name', 'description'];
}
```

Alternatively, list models in the `models` setting in `config/backpack/auto-translate.php`.

<a name="usage"></a>
## Usage

### Translate from the command line

Use the Artisan commands to inspect translation status and translate existing records:

```bash
php artisan auto-translate:status
php artisan auto-translate:run
php artisan auto-translate:entry
```

See the [command usage guide](https://backpackforlaravel.com/products/docs/COMMAND_USAGE.md) for options and examples.

### Translate from application code

Use the `AutoTranslate` facade to translate a model, select attributes or target locales, or queue the work:

```php
use Backpack\AutoTranslate\Facades\AutoTranslate;

AutoTranslate::translate($event, attributes: ['name', 'description']);
AutoTranslate::translate($event, locales: ['es', 'fr'], queue: true);
```

See the [manual usage guide](https://backpackforlaravel.com/products/docs/MANUAL_USAGE.md) for the full API.

### Translate on model events

Add `HasAutoTranslations` to the model, then set `enabled_events` in the package config to `created`, `updated`, or both. It defaults to an empty array, so model events do not trigger translations until enabled.

For admin-controlled translation, add a checkbox to your CRUD form and call `AutoTranslate::translate()` after saving only when the admin selects it. The product README includes a complete example.

<a name="configuration"></a>
## Configuration

The package configuration file is `config/backpack/auto-translate.php`. It controls the active driver, supported locales, source locale, queue, caching, model list, event triggers, and translation constraints such as HTML tags or placeholders to preserve.

By default, automatic model-event translation is disabled. Choose deliberately whether translations should run synchronously, in a queue, or only when explicitly requested.

<a name="cascade-translation"></a>
## Cascade Translation

Translations can include related models. Pass relationship names to the `cascade` option when calling `AutoTranslate::translate()`, or configure `$autoTranslateRelationships` on the model. See the [manual usage guide](https://backpackforlaravel.com/products/docs/MANUAL_USAGE.md) for details.

<a name="troubleshooting"></a>
## Troubleshooting

- If translation does not run, verify the model uses `HasTranslations` and defines its translatable attributes.
- If a provider returns errors, check its API credentials and driver configuration.
- If queued jobs remain pending, confirm the queue worker is running on the configured translation queue.
- The package caches identical translation requests; clear or adjust its cache settings when testing repeated changes.

<a name="license"></a>
## License and Support

Auto Translate is proprietary software released under Backpack's [EULA for private add-ons](https://backpackforlaravel.com/eula). For package support, use the product's support channels. Security issues can be reported to `cristian.tabacitu@backpackforlaravel.com`.
