# Release Notes

---

**Launch date:** March 1st 2018

**Backpack 3.6 has no new features**. It is just a maintenance upgrade, which adds support for Laravel 5.8 and removes support for Laravel 5.7 and 5.6.

Here are the main differences between [Backpack 3.5](https://backpackforlaravel.com/docs/3.5) and Backpack 3.6.

<a name="backpack-base-1-1-x"></a>
## Backpack\Base 1.1.x

### Added
- support for Laravel 5.8;

### Removed
- removed Laravel 5.6 and 5.7 support;
- ```jenssegers/date``` dependency, since Carbon v2 can now do the same thing well;
- ```Tightenco\Parental``` dependency, since it proved unstable; the trait we were using is now included in Base as ```Backpack\Base\app\Models\Traits\InheritsRelationsFromParentModel;```;

<a name="backpack-crud-3-6-x"></a>
## Backpack\CRUD 3.6.x

### Fixed

- ```date``` and ```datetime``` column use Carbon localized dates, instead of ```jenssegers/date``` ;