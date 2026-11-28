# Editable Columns <span class="badge badge-info">Add-On</span>

--

<a name="about"></a>
## About

Editable Columns adds inline-editable columns to your Backpack List operation. It lets admins update values directly in the table using AJAX, without leaving the page.

Supported editable column types:
- `editable_text`
- `editable_checkbox`
- `editable_switch`
- `editable_select`

This operation is powered by the `MinorUpdateOperation`, which exposes a small update endpoint and keeps the inline editing experience fast and responsive.

<a name="requirements"></a>
## Requirements

- [Backpack\CRUD](https://github.com/Laravel-Backpack/CRUD) ^7.0
- [Backpack EditableColumns](https://backpackforlaravel.com/products/editable-columns) — paid add-on

> You can also buy Backpack premium add-ons in a bundle at discount prices. See [Backpack pricing](/pricing) for bundle options instead of purchasing individually.

<a name="installation"></a>
## Installation

### Quick Installation

If you have access to the package, run:

```bash
php artisan backpack:require:editablecolumns
```

This will ask for your token and password, which you get after purchasing the package. If you've purchased previously, you can find those credentials in your Backpack account at [https://backpackforlaravel.com/user/tokens](https://backpackforlaravel.com/user/tokens).

### Manual Installation

If the quick install does not work, install manually:

1. Add your Backpack token to `auth.json`:

```bash
composer config http-basic.backpackforlaravel.com [your-token-username] [your-token-password]
```

2. Add the Backpack private repository to `composer.json`:

```json
"repositories": [
    {
        "type": "composer",
        "url": "https://repo.backpackforlaravel.com/"
    }
]
```

3. Require the package:

```bash
composer require backpack/editable-columns
```

<a name="how-to-use"></a>
## How to Use

**Step 1.** Add the operation trait to your CrudController:

```php
use Backpack\CRUD\app\Http\Controllers\CrudController;
use Backpack\CRUD\app\Http\Controllers\Operations\ListOperation;
use Backpack\EditableColumns\Http\Controllers\Operations\MinorUpdateOperation;

class ProductCrudController extends CrudController
{
    use ListOperation;
    use MinorUpdateOperation;
}
```

**Step 2.** Use editable column types in `setupListOperation()`:

```php
public function setupListOperation()
{
    CRUD::addColumn([
        'name'       => 'price',
        'type'       => 'editable_text',
        'label'      => 'Price',
        'underlined' => true,
        'min_width'  => '120px',
        'searchLogic' => 'text',
    ]);

    CRUD::addColumn([
        'name'  => 'agreed',
        'type'  => 'editable_checkbox',
        'label' => 'Agreed',
    ]);

    CRUD::addColumn([
        'name'  => 'active',
        'type'  => 'editable_switch',
        'label' => 'Active',
        'color' => 'success',
    ]);

    CRUD::addColumn([
        'name'    => 'category_id',
        'type'    => 'editable_select',
        'label'   => 'Category',
        'options' => \App\Models\Category::all()->pluck('name', 'id')->toArray(),
        'underlined' => true,
        'save_on_change' => true,
    ]);
}
```

<a name="editable-column-types"></a>
## Editable Column Types

### `editable_text`

An inline editable text cell. Click to edit, `Enter` saves, `Esc` cancels, and arrow keys navigate between rows.

### `editable_checkbox`

A checkbox that saves immediately and can show success or error state.

### `editable_switch`

A switch control that saves immediately and supports color and label customization.

### `editable_select`

A select dropdown where options can be an array or a closure.

```php
CRUD::addColumn([
    'name'    => 'category_id',
    'type'    => 'editable_select',
    'label'   => 'Category',
    'options' => function ($entry) {
        return \App\Models\Category::whereDate('created_at', '<=', $entry->created_at)
            ->pluck('name', 'id')
            ->toArray();
    },
    'save_on_change' => true,
]);
```

<a name="validation"></a>
## Validation

Define validation for inline edits in `setupMinorUpdateOperation()`:

```php
protected function setupMinorUpdateOperation()
{
    $this->crud->setValidation(StoreRequest::class);
}
```

Alternatively, override validation and saving behavior:

```php
public function saveMinorUpdateFormValidation()
{
    if (request('attribute') === 'price' && ! is_numeric(request('value'))) {
        throw ValidationException::withMessages([
            'price' => ['The price has to be a number.'],
        ]);
    }
}

public function saveMinorUpdateEntry()
{
    $entry = $this->crud->getModel()->find(request('id'));
    $entry->{request('attribute')} = request('value');
    $entry->status = 'draft';
    $entry->save();

    return $entry->refresh();
}
```

<a name="search-logic"></a>
## Search Logic

Editable columns often need explicit search logic.

For `editable_text`:

```php
CRUD::addColumn([
    'name'        => 'email',
    'type'        => 'editable_text',
    'label'       => 'Email',
    'searchLogic' => 'text',
]);
```

For other editable columns, provide custom search logic when needed:

```php
CRUD::addColumn([
    'name'        => 'status',
    'type'        => 'editable_select',
    'label'       => 'Status',
    'searchLogic' => function ($query, $column, $searchTerm) {
        $query->orWhere('status', 'like', '%'.$searchTerm.'%');
    },
]);
```

### Search logic guidance

| Editable Column | Suggested Search Logic |
|-----------------|------------------------|
| `editable_text` | `text` |
| `editable_checkbox` | custom |
| `editable_switch` | custom |
| `editable_select` | custom |

<a name="reloading-after-update"></a>
## Reloading After Update

If you want the table to redraw after a column update:

```php
CRUD::setOperationSetting('forceReloadAfterUpdate', true);
```

To reload only when specific columns change:

```php
CRUD::setOperationSetting('forceReloadAfterUpdate', ['price', 'status']);
```

<a name="support"></a>
## Support

This is a first-party Backpack add-on. If you need help, open a discussion in the Backpack community forum:

- https://github.com/Laravel-Backpack/community-forum/discussions

<a name="license"></a>
## License

Editable Columns is released under Backpack's EULA. See the add-on license file for full details.
