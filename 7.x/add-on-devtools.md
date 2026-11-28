# DevTools <span class="badge badge-pill badge-warning">PAID EXTRA</span>

---

<a name="about"></a>
## About

Backpack DevTools provides a browser interface for generating and managing development files for your admin panel. It can help create migrations, models, factories, seeders and CRUDs, and manage migrations and models.

![DevTools generating an entity from the browser](https://user-images.githubusercontent.com/1032474/128379216-72ae55fa-fcff-4747-8c35-42c733923c94.gif)

See [Generating Code](https://backpackforlaravel.com/docs/{{version}}/generating-code#devtools) for examples of what the web interface can do.

<a name="requirements"></a>
## Requirements

- An installed [Backpack CRUD](https://backpackforlaravel.com/docs/{{version}}/installation) application
- Access to Backpack's private Composer repository

<a name="installation"></a>
## Installation

In your Laravel project, run:

```bash
php artisan backpack:require:devtools
```

Follow the prompts and provide your Backpack access token credentials when requested. Your credentials are available in your [Backpack account](https://backpackforlaravel.com/user/tokens).

Alternatively, configure Composer to access the private repository:

```bash
composer config http-basic.backpackforlaravel.com [your-token-username] [your-token-password]
```

Add the private repository to `composer.json` if it is not already configured:

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

Install DevTools as a development dependency, then run its installer:

```bash
composer require --dev --with-all-dependencies backpack/devtools
php artisan backpack:devtools:install
```

<a name="usage"></a>
## Usage

Open `/admin/devtools` in your application to use the interface. Generate the files you need, then review and customize the generated code for your application.

![DevTools entity generation form](https://user-images.githubusercontent.com/1032474/128375953-72b637c5-75cd-4626-a36b-e49f9f4d9952.png)

> DevTools is intended for development. Do not deploy its interface or functionality to staging or production. Install production dependencies with `composer install --no-dev`, or remove DevTools before deployment.

<a name="license"></a>
## License and Support

DevTools is proprietary software covered by Backpack's [EULA for private add-ons](https://backpackforlaravel.com/eula). See [Backpack pricing](https://backpackforlaravel.com/pricing) for current access details.
