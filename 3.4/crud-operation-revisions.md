
[block:api-header]
{
  "type": "basic",
  "title": "Revisions"
}
[/block]
Revisions allows you to store all entry's changes in a table and revert to a specific value, if you'd like. It can work as a backup system and an accountability system for the admins.

When enabled, it will show another button in the table view, between Edit and Delete. On click, that button opens another page which will allow an admin to see all changes and who made them. The revisions page will look like this:
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/0809eae-Screen_Shot_2016-09-19_at_18.20.53.png",
        "Screen Shot 2016-09-19 at 18.20.53.png",
        1280,
        550,
        "#ebf2f2"
      ]
    }
  ]
}
[/block]


In order to enable this functionality for a crud panel, you need to:

1. Create the revisions table:

```bash
cp vendor/venturecraft/revisionable/src/migrations/2013_04_09_062329_create_revisions_table.php database/migrations/ && php artisan migrate
```

2. Use [venturecraft/revisionable](https://github.com/VentureCraft/revisionable#implementation) on your model. If you are using another bootable trait be sure to override the boot method in your model.

```php
namespace MyApp\Models;

class Article extends Eloquent {
    use \Backpack\CRUD\CrudTrait, \Venturecraft\Revisionable\RevisionableTrait;

    // If you are using another bootable trait the be sure to override the boot method in your model
    public static function boot()
    {
        parent::boot();
    }
}
```

3. In your EntityCrudController, enable access to the revisions: 

```php
$this->crud->allowAccess('revisions');
```

4. [optional] If you want the table view to load faster (and why wouldn't you?), you should eager-load the revisions, so there won't be any extra DB queries for the revisions:
```php
$this->crud->with('revisionHistory');
```

For complex usage, head on over to [VentureCraft/revisionable](https://github.com/VentureCraft/revisionable) to see the full documentation and extra configuration options.