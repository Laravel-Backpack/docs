# Custom Validation Rules

---

<a name="about"></a>
## About

Some Backpack fields are more difficult to validate using standard Laravel validation rules. So we've created a few custom validation rules, that will make validation them dead-simple.

<a name="valid-upload-validation-rule"></a>
## `ValidUpload` for `upload` field type

The `ValidUpload` rule is used to validate the `upload` field type. Using the custom rule helps developer avoid to setting different validation rules for different operations.(create/update).

```php
// for the field
CRUD::field('avatar')->type('upload');

// you can write the validation rule as
'avatar' => ValidUpload::fileRules(File::types(['jpg','png'])->max(2048))->attributeRules('required'),
```

The uploader will automatically handle the `sometimes` case for you and will only validate the field if it's present in the request.

<a name="valid-upload-multiple-validation-rule"></a>
## `ValidUploadMultiple` for `upload_multiple` field type

The `ValidUploadMultiple` rule helps you define two sets of validation rules, in one go:
- `arrayRules()` for the entire field (the array of entries);
- `fileRules()` for each uploaded file;

```php
// for the field
CRUD::field('attachments')->type('upload_multiple');

// you can write the validation rule as
'attachments' => ValidUploadMultiple::fileRules(File::types(['pdf'])->max(10000))
            ->arrayRules(['required_if:other_field,test_value', 'min:2', 'max:5'])
            ,    

```

<a name="valid-repeatable-validation-rule"></a>
## `ValidRepeatable` for `repeatable` field type

The `ValidRepeatable` makes it easy to validate fields that have subfields (the `repeatable` field, but also the `relationship` field when you define subfields). You can define two sets of validation:
- `fieldRules()` to define rules on the field itself;
- `itemRules()` to define rules on the sub-items (rows);

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
'gallery' => ValidRepeatable::fieldRules(['min:1', 'max:3'])
            ->itemRules([
                'title' => 'required',
                'image' => ValidUpload::fileRules(File::types(['jpg','png'])->max(2048))
            ])      

```