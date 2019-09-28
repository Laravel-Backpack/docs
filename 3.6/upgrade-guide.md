# Upgrade Guide

---

This will guide you through upgrading from Backpack 3.5 to 3.6. For upgrading from 3.4 to 3.5 [check out the previous upgrade guide](https://backpackforlaravel.com/docs/3.5/upgrade-guide).

<a name="requirements"></a>
## Requirements

- Backpack\Base 1.0.x installed
- Backpack\CRUD 3.5.x installed
- Laravel 5.7 installed
- PHP 7.1.3+
- 5 minutes on top of your normal [Laravel 5.8 upgrade](https://laravel.com/docs/5.8/upgrade)

<a name="upgraade-steps"></a>
## Upgrade Steps

1. Update your ```composer.json``` file to require ```"backpack/crud": "3.6.*"``` along with ```"laravel/framework": "5.8.*"```. Remove ```backpack/base``` from your requirements - backpack/crud will take care of that. Then run ```composer update```.

2. In your ```App\Models\BackpackUser``` instead of ```Tightenco\Parental\HasParent```, please use ```Backpack\Base\app\Models\Traits\InheritsRelationsFromParentModel```; [here's the diff](https://github.com/Laravel-Backpack/Base/pull/362/files#diff-f075b83ebb2b1ef3ba84dec14b395607);

3. In your ```app/config/backpack/base.php``` please change your ```default_date_format``` and ```default_datetime_format``` to ```Do MMMM YYYY``` and ```Do MMMM YYYY, HH:mm``` respectively;

4. If you've overwritten ```inc/head.blade.php``` or ```inc/scripts.blade.php```, please make sure you [use the newest version of Bootstrap](https://github.com/Laravel-Backpack/Base/pull/362/files#diff-96ac3ea4d0cb85053acf44e3772eb5f1); they've fixed a security vulnerability (XSS);

---

**That's it**. Thank you for taking the time to upgrade.
