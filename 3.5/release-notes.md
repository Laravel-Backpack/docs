# Release Notes

---

**Launch date:** November 2018

Here are the main differences between [Backpack 3.4](https://backpackforlaravel.com/doc/3.4) and Backpack 3.5.

<a name="backpack-base-1-0-x"></a>
## Backpack\Base 1.0.x

### Added

- added a ```layout_guest``` template which has no sidebar and no top menu;
- **login, register, reset password views look much better**, because they're using this new layout with no sidebar; [details and screenshots here](https://github.com/Laravel-Backpack/Base/pull/330);
- **Backpack authentication is now completely separate** from the scaffolded Laravel authentication; Backpack uses its own auth provider, guard and password broker, that developers can overwrite by adding one with the same name (_backpack_) to their ```config/auth.php``` file; [details here](https://github.com/Laravel-Backpack/Base/pull/293);
- added ```php artisan backpack:base:publish-user-model``` command; [details here](https://github.com/Laravel-Backpack/Base/pull/334);
- added ```php artisan backpack:base:publish-middleware``` command; [details here](https://github.com/Laravel-Backpack/Base/pull/334);
- upon installation, ```BackpackUser``` model and ```CheckIfAdmin``` middleware are published by default - so they can EASILY be customized; [details here](https://github.com/Laravel-Backpack/Base/pull/334);
- two separate files: ```inc/topbar_left_content.blade.php``` and ```inc/topbar_right_content.blade.php``` where the user can specify additional content for the top menu; [details here](https://github.com/Laravel-Backpack/Base/pull/302); [documentation here](docs/{{version}}/base-how-to#use-separate-login-register-forms-for-users-and-admins);
- error views now use a layout file, so it’s easier to customize how all error pages look; defaut error view design is now consistent with default AdminLTE design;
- split ```layout``` into multiple views (```head```, ```scripts```), so it’s easier to customize just one part of it;
- ```backpack_url()``` can now take parameters, just like ```url()```;
- password reset page is a clear two-step process, and pre-populates the email field; big UX improvement for something that is often used by inexperienced users (they're the ones losing their password);

### Fixed
- reset password procedure - you might have it broken in 0.9.0 because of a missing step in the upgrade guide, an using ```App/User``` instead of ```BackpackUser``` in your ```config/backpack/base.php``` file;
- bug where you couldn't use permissions and roles when using ```backpack_auth()``` helper; ```BackpackUser``` now picks up relationships from the User model it extends; [details here](https://github.com/Laravel-Backpack/Base/pull/323);
- default CSS file now uses ```body.skin-purple``` as a selector, to fix the paint glitch, where buttons and other things were shown blue, then changed to purple, when using the purple skin;
- now using jquery and font-awesome from adminlte package instead of CDN;
- language folders like ```da_DK```, ```fr_CA``` and ```pt_br``` have been duplicated into their standardized form (```da-DK```, ```fr-CA``` and ```pt-BR```); introduced notice that the old folders will be deprecated in the next release;
- the footer is now transparent; it is not a primary piece of content, it shouldn’t stand out;

### Removed
- removed Laravel 5.5 support;
- remvoved PHP 7.0 support (since Laravel 5.6 does not support it);

<a name="backpack-crud-3-5-x"></a>
## Backpack\CRUD 3.5.x

// TODO