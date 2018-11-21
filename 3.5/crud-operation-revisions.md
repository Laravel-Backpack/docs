# Revisions

---

<a name="about"></a>
## About

Revisions allows your admins to store, see and undo changes to entries on an Eloquent model.

The operation provides you with a simple interface to work with [venturecraft/revisionable](https://github.com/VentureCraft/revisionable#implementation), which is a great pacakge that stores all changes in a separate table. It can work as an audit trail, a backup system and an accountability system for the admins.

When enabled, ```Revisions``` will show another button in the table view, between _Edit_ and _Delete_. On click, that button opens another page which will allow an admin to see all changes and who made them:


![CRUD Revision Operation](https://backpackforlaravel.com/uploads/docs/operations/revisions.png)


<a name="how-to-use"></a>
## How to Use

The ```Reorder``` operation is **disabled by default**. In order to enable this functionality for a CRUD panel, you need to:

1. Create the revisions table:

```bash
cp vendor/venturecraft/revisionable/src/migrations/2013_04_09_062329_create_revisions_table.php database/migrations/ && php artisan migrate
```

2. Use [venturecraft/revisionable](https://github.com/VentureCraft/revisionable#implementation) on your model. If you are using another bootable trait be sure to override the boot method in your model.

```php
namespace MyApp\Models;

class Article extends Eloquent {
    use \Backpack\CRUD\CrudTrait, \Venturecraft\Revisionable\RevisionableTrait;

    // If you are using another bootable trait
    // be sure to override the boot method in your model
    public static function boot()
    {
        parent::boot();
    }
}
```

3. In your EntityCrudController's ```setup()``` method, enable access to the Revisions operation: 

```php
$this->crud->allowAccess('revisions');
```

4. [optional] If you want the table view to load faster (and why wouldn't you?), you should eager-load the revisions, so there won't be any extra DB queries for the revisions:

```php
$this->crud->with('revisionHistory');
```

For complex usage, head on over to [VentureCraft/revisionable](https://github.com/VentureCraft/revisionable) to see the full documentation and extra configuration options.