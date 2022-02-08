# Save Actions

---

<a name="about"></a>
## About

`Create` and `Update` forms end in a Save button with a drop menu. Every option in that dropdown is a SaveAction - they determine where the user is redirected after the saving is complete.

<a name="defaults"></a>
## Default Save Actions

There are three save actions registered by Backpack by default. They are:
  - ```save_and_back``` (Save your entity and go back to previous URL)
  - ```save_and_edit``` (Save and edit the current entry)
  - ```save_and_new``` (Save and go to create new entity page)

<a name="save-actions-api"></a>
## Save Actions API

Inside your CrudController, inside your ```setupCreateOperation()``` or ```setupUpdateOperation()``` methods, you can change what save buttons are shown for each operation by using the methods below:

#### addSaveAction(array $saveAction)

Adds a new SaveAction to the "Save" button/dropdown.

```php
$this->crud->addSaveAction([
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
$this->crud->addSaveActions([
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
$this->crud->replaceSaveActions(
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
$this->crud->removeSaveAction('save_action_one');
```

#### removeSaveActions(array $saveActions)

The same principle as `removeSaveAction()` but to remove multiple actions at same time. You should provide an array with save action names.
```php
$this->crud->removeSaveActions(['save_action_one','save_action_two']);
```

#### orderSaveAction(string $saveAction, int $wantedOrder)

You can specify a certain order for a certain save action.

```php
$this->crud->orderSaveAction('save_action_one', 1);
```

We will setup the save action in the desired order and try to re-order the other save actions accordingly. If you want more granular control over all save actions order, you can define ```order``` when creating the save action, or use ```orderSaveActions()```

#### orderSaveActions(array $saveActions)

Allows you to reorder multiple save actions at same time. You can use it by either specifying only the names of the save actions, in the order you want, or by specifying their order number too:

```php
// make save actions show up in this order
$this->crud->orderSaveActions(['save_action_one','save_action_two']);
// or
$this->crud->orderSaveActions(['save_action_one' => 3,'save_action_two' => 2]);
```

#### setSaveActions(array $saveActions)

Alias for ```replaceSaveActions(array $saveActions)```.
