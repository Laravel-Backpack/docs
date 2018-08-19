# Operations

---

In a basic CRUD, you will only need to:
- specify what fields the create/update form should show;
- specify what columns the table view should show;

## CREATE & UPDATE
- [44+ field types](https://laravel-backpack.readme.io/docs/crud-fields#section-standard-field-types) (including 1-n and n-n relationship)
- [customize field attributes/appearance](https://laravel-backpack.readme.io/docs/crud#section-fields);
- [create your own field type](https://laravel-backpack.readme.io/docs/crud-fields#section-custom-field-types)
- extras ([fake fields](https://laravel-backpack.readme.io/docs/advanced-features#section-extras-fake-fields-stored-as-json-in-the-database) stored as JSON in the db)
- [back-end validation using form requests](https://laravel-backpack.readme.io/docs/crud-example#section-the-requests)
- [custom code before/after the entry is created or updated](https://laravel-backpack.readme.io/docs/crud#section-callbacks)
- [media library](https://laravel-backpack.readme.io/docs/advanced-features#section-media-library-file-manager) (file manager)
- [reordering/nesting entries](https://laravel-backpack.readme.io/docs/advanced-features#section-reordering-and-nesting-items)
- [show an entry's history](https://laravel-backpack.readme.io/docs/advanced-features#section-revisions) (create, update - we call it revisions)

## READ
- [12+ column types](https://laravel-backpack.readme.io/docs/crud-columns-types)
- [create your own column type](https://laravel-backpack.readme.io/docs/crud-columns-types#section-roll-your-own)
- [custom buttons](https://laravel-backpack.readme.io/docs/crud-buttons)
- [filters](https://laravel-backpack.readme.io/docs/filters)
- [click table row to show details](https://laravel-backpack.readme.io/docs/advanced-features#section-details-row) (details row)
- [export to PDF, CSV, XLS](https://laravel-backpack.readme.io/docs/advanced-features#section-export-buttons)


## DELETE
- in the DataTable, the entries are deleted using AJAX; you can customize [the default functionality](https://github.com/Laravel-Backpack/CRUD/blob/master/src/app/Http/Controllers/CrudController.php#L209-L221) by creating a ```destroy()``` method in your EntityCrudController