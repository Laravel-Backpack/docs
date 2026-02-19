# Testing

---

<a name="about-testing"></a>
## About

Testing your CRUD panels ensures that your admin interfaces work as expected and continue to function correctly as your application evolves. Backpack provides a dedicated command to generate **Feature** tests for your CrudControllers automatically.

These generated tests cover standard operations like:
- **List**: Asserts the table loads and columns are visible.
- **Create**: Asserts the form loads, validates inputs, and stores entries.
- **Update**: Asserts the form loads with existing data and updates entries.
- **Delete**: Asserts entries can be deleted.
- **Show**: Asserts the details view loads.

The tests are designed to be "smart" — they inspect your CrudController's configuration (fields, columns, validation rules) to generate relevant assertions.

<a name="generating-tests"></a>
## Generate Tests

You can generate feature tests for your CRUD controllers using the artisan command:

```bash
php artisan backpack:tests
```

This will scan your controllers directory (configurable via `backpack.testing.controllers_path`) and generate test files for all supported operations.

### Options

| Option | Description |
| --- | --- |
| `--controller=Name` | Only generate tests for the specific controller class name (e.g., `UserCrudController`) |
| `--operation=list` | Only generate tests for the given CRUD operation (list, create, update, etc.) |
| `--type=feature` | The type of test to generate (`feature` is currently the only supported type) |
| `--framework=phpunit` | The testing framework to use (`phpunit` or `pest`). Defaults to `phpunit` |
| `--force` | Overwrite existing test classes |

### Examples

Generate tests for all controllers:
```bash
php artisan backpack:tests
```

Generate tests for a specific controller:
```bash
php artisan backpack:tests --controller=UserCrudController
```

Generate only list operation tests:
```bash
php artisan backpack:tests --operation=list
```

<a name="generated-file-structure"></a>
## Generated testes file structure

Generated tests rely on a small hierarchy of base classes, reusable traits and on per-controller test bases inside your app's `tests/Feature` folder. 

You will notice that there will be a new "Backpack" folder. That folder contain the "base" tests that each of your crud controllers will re-use. eg:

```markdown
tests/Feature/Admin/SomeController/
├─ TestBase.php            # extends Tests\\Feature\\Backpack\\DefaultTestBase and sets properties
├─ CreateTest.php          # uses DefaultCreateTests trait
├─ UpdateTest.php          # uses DefaultUpdateTests trait
├─ ListTest.php            # uses DefaultListTests trait
├─ ShowTest.php            # uses DefaultShowTests trait
```

<a name="configurations-available-in-test-traits"></a>
## Operation test traits and configuration variables

The operation test traits implement the assert logic and expose variables you can set in your test `setUp()` to customise behaviour:

- `DefaultCreateTests` exposes `$createInput` and `$assertCreateInput`.
- `DefaultUpdateTests` exposes `$updateInput` and `$assertUpdateInput`.
- `DefaultListTests` and `DefaultShowTests` inspect the CRUD configuration via the test helper.

Usage pattern:

- In your operation test's `setUp()` (or the controller `TestBase::setUp()`), create and set `$this->createInput` or `$this->updateInput` when you need to submit additional or transformed data. The trait will use those arrays when performing the POST/PUT requests.
- Use `$assertCreateInput` / `$assertUpdateInput` when the database assertion differs from the raw submission (for example, do not include `password` or file upload metadata in assertions).

Example (from `tests/Feature/Admin/PetShop/PetCrud/CreateTest.php`):

- set an avatar URL to be submitted with the create request:

```php
$this->createInput = array_merge($this->model::factory()->make()->toArray(), [
    'avatar' => ['url' => 'https://lorempixel.com/400/200/animals'],
]);
```
<a name="route-parameters-and-controller-initialization"></a>
## Route parameters and controller initialization

Controllers that require route parameters for their routes (for example nested resources like an owner ID) can expose defaults for tests using the `TestingRouteParameters` attribute. Example found in the demo application controller:

- [app/Http/Controllers/Admin/PetShop/OwnerPetsCrudController.php](app/Http/Controllers/Admin/PetShop/OwnerPetsCrudController.php) uses the attribute:

```php
#[\Backpack\CRUD\app\Library\CrudTesting\TestingRouteParameters(['owner' => 1])]
class OwnerPetsCrudController extends PetCrudController { ... }
```

The attribute provides default route parameter values when the package's test helpers mock the current route. Tests can rely on those defaults. The generated controller `TestBase` sets `public string $route = 'pet-shop/owner/1/pets';` so trait requests properly include the parameter.


<a name="overriding-the-traits-behaviour"></a>
## Overriding trait behaviour

If the default trait behaviour doesn't match your controller logic (e.g., you need to attach relationships before asserting the edit page), override the trait methods inside your test class. You can keep the original trait implementation available by aliasing it when importing the trait. Example from `tests/Feature/Admin/PetShop/OwnerPetsCrud/UpdateTest.php`:

```php
use \\Tests\\Feature\\Backpack\\DefaultUpdateTests {
    test_update_page_loads_successfully as default_test_update_page_loads_successfully;
}

public function test_update_page_loads_successfully(): void
{
    $this->skipIfModelDoesNotHaveFactory();

    $entry = $this->model::factory()->create();
    $entry->owners()->attach(1, ['role' => 'Owner']);

    $response = $this->get($this->testHelper->getCrudUrl($entry->getKey().'/edit'));
    $response->assertStatus(200);
}
```

### Short checklist when adapting or writing tests

- Ensure the controller's required route parameters are provided by the `TestingRouteParameters` attribute or the generated `TestBase::$route` includes concrete values.
- In `TestBase::setUp()` create any related models your controller requires (attached owners, categories, etc.).
- Set `$createInput` / `$updateInput` in tests when the form requires additional structured data (files, nested arrays, relationship ids).
- Use `$assertCreateInput` / `$assertUpdateInput` to shape the expected DB assertion.
- Override trait methods only when you need custom assertions; alias the trait method if you still want to call the default behaviour.

<a name="publish-the-configuration"></a>
## Publishing the Configuration

You can publish the configuration by running `php artisan vendor:publish --provider="Backpack\CRUD\BackpackServiceProvider" --tag=config`. There you will be able to change your controllers path. 

<a name="customizing-or-creating-test-stubs"></a>
## Customizing or creating test stubs

You can customize or add new operation test stubs used by the generator by publishing them to your application.

```bash
php artisan vendor:publish --provider="Backpack\CRUD\BackpackServiceProvider" --tag=stubs
```

This will create a `resources/views/vendor/backpack/crud/stubs/testing` directory in your application root. Any changes you make to these stubs will be used when generating tests.
Here is an example of what a custom operation stub (e.g., `clone.stub`) might look like:

```php
<?php

namespace Tests\Feature\Backpack;

trait DefaultCloneTests
{
    /**
     * Test that the list page loads and there is a clone button on page
     */
    public function test_create_clone_button_is_on_page(): void
    {
        $response = $this->get($this->testHelper->getCrudUrl('list'));
        $response->assertStatus(200);
        $response->assertSee('bp-button="clone"', true);
    }
}
```

<a name="troubleshooting"></a>
## Troubleshooting

The test generation highly relies on your Model Factories. It's highly important that your Factories are up-to-date with the database/model requirements. 


