# Multi-Auth <span class="badge badge-pill badge-warning">PAID EXTRA</span>

--

<a name="about"></a>
## About

Backpack Multi-Auth replaces Backpack CRUD's built-in authentication with Laravel Fortify while keeping Backpack's existing login URLs, route names, and theme views. It provides email and password login, with optional two-factor authentication, magic-link login, passkeys, and social login.

![Multi-Auth login screen](https://backpackforlaravel.com/uploads/multi-auth/multi-auth-login.jpg)

> Installing Multi-Auth activates it and replaces the legacy Backpack auth routes. Do not install it if you want to keep Backpack's built-in authentication.

Multi-Auth is a paid private add-on. See the [product page](https://backpackforlaravel.com/products/multi-auth) for current pricing and access details.

<a name="requirements"></a>
## Requirements

- PHP 8.2 or newer
- Backpack CRUD 7.x
- Laravel 12 or 13
- A working Laravel session guard and password broker

<a name="installation"></a>
## Installation

1) Disable Backpack CRUD's auth routes in `config/backpack/base.php`:

```php
'setup_auth_routes' => false,
'setup_email_verification_middleware' => false,
```

2) Configure access to the private Composer repository using your Backpack access token:

```bash
composer config http-basic.repo.backpackforlaravel.com your-token-username your-token-password
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

3) Install Multi-Auth:

```bash
composer require backpack/multi-auth
```

4) Publish its configuration:

```bash
php artisan vendor:publish --provider="Backpack\MultiAuth\MultiAuthServiceProvider" --tag=backpack-multi-auth-config
```

5) Clear cached configuration and routes, then inspect the registered auth routes:

```bash
php artisan optimize:clear
php artisan route:list --name=backpack.auth
```

For an existing Backpack v7 application, the package uses your current Backpack authentication settings when its configuration overrides are left `null`; existing users and password hashes do not need to be migrated. Test login, logout, password reset, and any enabled registration or email-verification flows in staging before deploying.

<a name="features"></a>
## Optional Login Features

Each feature is disabled by default unless otherwise noted. Enable only the login methods your application needs in `config/backpack/multi-auth.php` or with the corresponding environment variable.

### Two-factor authentication

Enable `features.two_factor` or set `BACKPACK_MULTI_AUTH_FEATURES_TWO_FACTOR=true`. Add Laravel Fortify's `TwoFactorAuthenticatable` trait to your user model, publish the `backpack-multi-auth-migrations-two-factor` migration, and run `php artisan migrate`. Users can then manage two-factor authentication and recovery codes from the My Account page.

![Multi-Auth two-factor login challenge](https://backpackforlaravel.com/uploads/multi-auth/2fa-login.jpg)

![Multi-Auth account settings](https://backpackforlaravel.com/uploads/multi-auth/multi-auth-settings.jpg)

### Magic-link login

Enable `features.magic_link` or set `BACKPACK_MULTI_AUTH_FEATURES_MAGIC_LINK=true`. Publish the `backpack-multi-auth-migrations-magic-link` migration and run it. Users can request a single-use login code by email instead of entering a password.

![Multi-Auth magic-link login](https://backpackforlaravel.com/uploads/multi-auth/magic-link-login.jpg)

### Passkey login

Enable `features.passkeys` or set `BACKPACK_MULTI_AUTH_FEATURES_PASSKEYS=true`. Add Laravel Fortify's `PasskeyAuthenticatable` trait to your user model and implement Laravel Passkeys' `PasskeyUser` contract. Publish the `backpack-multi-auth-migrations-passkeys` migration and run it.

### Social login

Install `laravel/socialite`, configure the provider credentials and callback URL in `config/services.php`, and enable `socialite.enabled`. Publish the `backpack-multi-auth-migrations-socialite` migration and run it. Users authenticate through linked provider accounts; social registration can also be enabled with the registration feature.

For feature-specific options and setup details, see the [package README](https://backpackforlaravel.com/products/multi-auth/readme.md).

<a name="migrations"></a>
## Migrations

The package provides a combined `backpack-multi-auth-migrations` tag to publish migrations for all optional features, or individual tags when you only need selected features:

- `backpack-multi-auth-migrations-two-factor`
- `backpack-multi-auth-migrations-socialite`
- `backpack-multi-auth-migrations-magic-link`
- `backpack-multi-auth-migrations-passkeys`

Publish all feature migrations with:

```bash
php artisan vendor:publish --provider="Backpack\MultiAuth\MultiAuthServiceProvider" --tag=backpack-multi-auth-migrations
```

To publish only one feature's migrations, use its tag instead. Then run:

```bash
php artisan migrate
```

<a name="uninstall"></a>
## Uninstall

To return to Backpack's legacy authentication, remove the package, restore your previous auth settings in `config/backpack/base.php`, then clear cached configuration and routes:

```bash
composer remove backpack/multi-auth
php artisan optimize:clear
```

Users may need to sign in again after switching authentication systems.

<a name="license"></a>
## License and Support

Multi-Auth is proprietary software released under Backpack's [EULA for private add-ons](https://backpackforlaravel.com/eula). For package support, use the product's support channels. Security issues can be reported to `cristian.tabacitu@backpackforlaravel.com`.
