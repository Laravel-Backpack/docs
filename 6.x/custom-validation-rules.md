# Custom Validation Rules

---

<a name="about"></a>
## About

Some Backpack fields are more difficult to validate using standard Laravel validation rules. So we've created a few custom validation rules, that will make validation them dead-simple.

<a name="valid-upload-validation-rule"></a>
## `ValidUpload` for `upload` field type

The `ValidUpload` rule is used to validate the `upload` field type. Using the custom rule helps developer avoid to setting different validation rules for different operations, (create/update).

```php
// for the field
CRUD::field('avatar')->type('upload');

// you can write the validation rule as

use Backpack\CRUD\app\Library\Validation\Rules\ValidUpload;

'avatar' => ValidUpload::field('required')->file(File::types(['jpg','png'])->max(2048)),
```

The `::field()` constructor accepts the rules for the field, while `->file()` accepts the specific rules for files sent in field.
This helps the uploader to automatically handle the `sometimes` case for you.

<a name="valid-upload-multiple-validation-rule"></a>
## `ValidUploadMultiple` for `upload_multiple` field type

This rule replaces the definitions of  `attribute` and `attribute.*` rules, plus handling the scenario of create vs. update.

The `::field()` constructor accepts the rules for the field, while `->file()` accepts the specific rules for files sent in field.

```php
// for the field
CRUD::field('attachments')->type('upload_multiple');

// you can write the validation rule as

use Backpack\CRUD\app\Library\Validation\Rules\ValidUploadMultiple;

'attachments' => ValidUploadMultiple::field(['required_if:other_field,test_value', 'min:2', 'max:5'])->file(File::types(['pdf'])->max(10000)),    

```