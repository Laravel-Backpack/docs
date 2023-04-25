# Custom Validation Rules

---

<a name="about"></a>
## About

Along with the provided Laravel validation rules, we created a few custom validation rules that you can use to validate Backpack fields in an easier way.
No more messing with `.*` and such on validation rules. Just define fluently what you want to validate.

<a name="valid-upload-validation-rule"></a>
## `ValidUpload` for `upload` field type

The `ValidUpload` rule is used to validate the `upload` field type. Using the custom rule helps developer avoid to setting different validation rules for different operations.(create/update).

```php
// for the field
CRUD::field('avatar')->type('upload');

// you can write the validation rule as
'avatar' => ValidUpload::fileRules(['required', File::types(['jpg','png'])->max(2048)])
```

The uploader will automatically handle the `sometimes` case for you and will only validate the field if it's present in the request.

<a name="valid-upload-multiple-validation-rule"></a>
## `ValidUploadMultiple` for `upload_multiple` field type

The `ValidUploadMultiple` rule helps developer by having two sets of specific validation, one that applies to the "global array of files", and the "file validation" for the uploaded files.

```php
// for the field
CRUD::field('attachments')->type('upload_multiple');

// you can write the validation rule as
'attachments' => ValidUploadMultiple::make()
            ->arrayRules(['required_if:other_field,test_value', 'min:2', 'max:5'])
            ->itemRules(File::types(['pdf'])->max(10000)),    

```

<a name="valid-repeatable-validation-rule"></a>
## `ValidRepeatable` for `repeatable` field type

The `ValidRepeatable` (works the same for relationships managed through the repeatable interface), is a validation rule that eases developer work by validating the repeatable fields in a more convinient way.

```php
// for the field
CRUD::field('gallery')->type('repeatable')->subfields([
    [
        'name' => 'title'
        'type' => 'text'
    ],
    [
        'name' => 'image'
        'type' => 'upload'
    ]
]);

// you can write the validation rule as
'gallery' => ValidRepeatable::make()
            ->arrayRules(['min:1', 'max:3'])
            ->namedRules([
                'title' => ['required', 'min:5'],
                'image' => [ValidUpload::arrayRules('required')
                    ->fileRules(File::types(['jpg','png'])->max(2048))]
            ])      

```