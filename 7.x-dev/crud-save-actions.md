# Save Actions

---

<a name="about"></a>
## About

`Create` and `Update` forms end in a Save button with a drop menu. Every option in that dropdown is a SaveAction - they determine where the user is redirected after the saving is complete.

<a name="defaults"></a>
## Default Save Actions

There are four save actions registered by Backpack by default. They are:
  - ```save_and_back``` (Save your entity and go back to previous URL)
  - ```save_and_edit``` (Save and edit the current entry)
  - ```save_and_new``` (Save and go to create new entity page)
  - ```save_and_preview``` (Save and go to show the current entity)

<a name="save-action-classes"></a>
## Save Action Classes

Save actions are now first-class citizens. Instead of maintaining large array definitions in each CrudController, you can encapsulate the behaviour inside PHP classes that implement `Backpack\CRUD\app\Library\CrudPanel\SaveActions\SaveActionInterface`. Backpack ships with `SaveAndBack`, `SaveAndEdit`, `SaveAndNew`, and `SaveAndPreview` as examples, and also provides `SaveAndList` for projects that want an explicit "Save and go to list" button.

### Quick start

1. **Create a class** inside your application (for example `app/Backpack/Crud/SaveActions/SaveAndApprove.php`).
2. **Extend** `AbstractSaveAction` (recommended) or implement `SaveActionInterface` directly.
3. **Override** the methods that describe your button.
4. **Register** the class with `CRUD::addSaveAction()` / `CRUD::replaceSaveActions()` or pass it to Blade components like `<x-bp-dataform>`.

```php
<?php

namespace App\Backpack\Crud\SaveActions;

use Backpack\CRUD\app\Library\CrudPanel\CrudPanel;
use Backpack\CRUD\app\Library\CrudPanel\SaveActions\AbstractSaveAction;
use Illuminate\Http\Request;

class SaveAndApprove extends AbstractSaveAction
{
    protected ?int $order = 2;

    public function getName(): string
    {
        return 'save_and_approve';
    }

    public function getButtonText(): string
    {
        return trans('backpack::crud.save_action_save_and_approve');
    }

    public function isVisible(CrudPanel $crud): bool
    {
        return $crud->hasAccess('update') && $crud->entry?->canBeApproved();
    }

    public function getRedirectUrl(CrudPanel $crud, Request $request, $itemId = null): ?string
    {
        return route('admin.invoices.approve', $itemId ?? $request->input('id'));
    }
}
```

> **Tip:** `AbstractSaveAction` already implements `toArray()`, order handling, and sensible defaults. Override only what you need. If you must store additional data, you can still return a custom array by implementing `SaveActionInterface` yourself.

### Registering class-based actions

Inside your CrudController you can now pass the class instead of an array:

```php
use App\Backpack\Crud\SaveActions\SaveAndApprove;

CRUD::replaceSaveActions([
    new SaveAndApprove(),
    \Backpack\CRUD\app\Library\CrudPanel\SaveActions\SaveAndBack::class,
]);
```

Backpack recognizes three inputs when registering save actions:
- an instantiated save action class;
- the fully qualified class name (it is resolved via the container so dependencies can be injected);
- the legacy associative array definition (still supported).

The action `order` is taken from the class (or array) and Backpack reorders conflicts automatically. If you need to adjust the order later you can still call `CRUD::orderSaveActions()`.

### Using classes in Blade components

The `bp-dataform` component accept save action classes through the `:save-actions` attribute. This allows you to reuse the same custom buttons outside CrudControllers:

```php
<x-bp-dataform
    controller="\App\Http\Controllers\Admin\InvoiceCrudController"
    :save-actions="[
        new App\\Backpack\\Crud\\SaveActions\\SaveAndApprove(),
        Backpack\\CRUD\\app\\Library\\CrudPanel\\SaveActions\\SaveAndBack::class,
    ]"
/>
```

When no save actions are provided, Backpack falls back to the defaults registered on the controller.

<a name="save-actions-api"></a>
## Save Actions API

Inside your CrudController, inside your ```setupCreateOperation()``` or ```setupUpdateOperation()``` methods, you can change what save buttons are shown for each operation by using the methods below:

#### addSaveAction(array $saveAction)

Adds a new SaveAction to the "Save" button/dropdown.

```php
CRUD::addSaveAction([
    'name' => 'save_action_one',
    'redirect' => function($crud, $request, $itemId) {
        return $crud->route;
    }, // what's the redirect URL, where the user will be taken after saving?

    // OPTIONAL:
    'button_text' => 'Custom save message', // override text appearing on the button
    // You can also provide translatable texts, for example:
    // 'button_text' => trans('backpack::crud.save_action_one'),
    'visible' => function($crud) {
        return true;
    }, // customize when this save action is visible for the current operation
    'referrer_url' => function($crud, $request, $itemId) {
        return $crud->route;
    }, // override http_referrer_url
    'order' => 1, // change the order save actions are in
]);
```

#### addSaveActions(array $saveActions)

The same principle of `addSaveAction([])` but for adding multiple actions with only one crud call.

```php
CRUD::addSaveActions([
    [
        'name' => 'save_action_one',
        'visible' => function($crud) {
            return true;
        },
        'redirect' => function($crud, $request, $itemId) {
            return $crud->route;
        },
    ],
    [
        'name' => 'save_action_two',
        'visible' => function($crud) {
            return true;
        },
        'redirect' => function($crud, $request, $itemId) {
            return $crud->route;
        },
    ],
]);
```

#### replaceSaveActions(array $saveActions)

This allows you to replace the current save actions with the ones provided in an array.

```php
CRUD::replaceSaveActions(
    [
        'name' => 'save_action_one',
        'visible' => function($crud) {
            return true;
        },
        'redirect' => function($crud, $request, $itemId) {
            return $crud->route;
        },
    ],
);
```


#### removeSaveAction(string $saveAction)

This allows you to remove a specific save action from the save actions array. Provide the name of the save action that you would like to remove.
```php
CRUD::removeSaveAction('save_action_one');
```

#### removeSaveActions(array $saveActions)

The same principle as `removeSaveAction()` but to remove multiple actions at same time. You should provide an array with save action names.
```php
CRUD::removeSaveActions(['save_action_one','save_action_two']);
```

#### orderSaveAction(string $saveAction, int $wantedOrder)

You can specify a certain order for a certain save action.

```php
CRUD::orderSaveAction('save_action_one', 1);
```

We will setup the save action in the desired order and try to re-order the other save actions accordingly. If you want more granular control over all save actions order, you can define ```order``` when creating the save action, or use ```orderSaveActions()```

#### orderSaveActions(array $saveActions)

Allows you to reorder multiple save actions at same time. You can use it by either specifying only the names of the save actions, in the order you want, or by specifying their order number too:

```php
// make save actions show up in this order
CRUD::orderSaveActions(['save_action_one','save_action_two']);
// or
CRUD::orderSaveActions(['save_action_one' => 3,'save_action_two' => 2]);
```

#### setSaveActions(array $saveActions)

Alias for ```replaceSaveActions(array $saveActions)```.

## Action change notification

By default, a change of the save action is shown to the user with a notification. If you want to disable the notification
you can configure this in the setup of you CRUD controller:

```php
CRUD::setOperationSetting('showSaveActionChange', false);
```
