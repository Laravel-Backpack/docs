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
## Generated File Structure

The command generates a structured set of test files for each controller. For a controller named `UserCrudController`, the structure will be:

```
tests/Feature/Admin/UserCrud/
├── UserCrudTestBase.php    # Base class containing setup and configuration
├── ListTest.php            # Tests for List operation
├── CreateTest.php          # Tests for Create operation
├── UpdateTest.php          # Tests for Update operation
└── ...
```

The `UserCrudTestBase` class extends `Backpack\CRUD\app\Library\CrudTesting\CrudFeatureTestCase` and handles authentication and test configuration setup. Each operation test class extends this base class.


<a name="how-to-configure-the-generated-tests"></a>
## How to configure the generated tests

By default, the generated tests rely on your Model Factories and standard Backpack conventions to create valid data for requests. However, complex real-world applications often require specific setup, valid data states, or mocked dependencies.

You can customize the test behavior for each CrudController by creating a **Test Configuration** class.

### 1. Create a Configuration Class

Create a class that extends `Backpack\CRUD\app\Library\CrudTesting\TestConfigHelper`. This allows you to override only the methods you need to customize, while keeping the default behavior for others.

```php
namespace Tests\Config;

use Backpack\CRUD\app\Library\CrudTesting\TestConfigHelper;

class ProductConfig extends TestConfigHelper
{
    public function setup()
    {
        // E.g., create a specific category required for products
        // \App\Models\Category::factory()->create();
    }

    public function getRouteParameters()
    {
        // If your route is /admin/category/{category_id}/product
        // return ['category_id' => 1];
        return [];
    }

    public function validCreateInput($model)
    {
        // Return valid data for a Create Request
        // Defaults to: return $model::factory()->raw();
        
        $data = parent::validCreateInput($model);
        $data['name'] = 'Valid Product Name';
        return $data;
    }

    public function validUpdateInput($model)
    {
        // Return valid data for an Update Request
        // Defaults to: return $model::factory()->raw();
        
        $data = parent::validUpdateInput($model);
        $data['price'] = 200;
        return $data;
    }

    public function invalidInput()
    {
        // Return data that should trigger a validation error
        // Defaults to: return [];
        
        return ['name' => '']; // assuming name is required
    }
    
    public static function getDatabaseAssertInput(string $model, array $data = []): array
    {
        // Helper to filter data for database assertion (e.g. remove password_confirmation)
        $data = parent::getDatabaseAssertInput($model, $data);
        
        // unset($data['password_confirmation']);
        
        return $data;
    }
}
```

### 2. Register the Configuration

You can configure the test generator globally in `config/backpack/testing.php`. This file allows you to map your controllers to configuration classes, but also control generation behavior.

```php
// config/backpack/testing.php

return [
    // The path where your CrudControllers are located
    'controllers_path' => app_path('Http/Controllers'),

    // Toggle test generation for specific operations
    'coverage' => [
        'operations' => [
            // 'list' => true,
            // 'create' => false, // Don't generate create tests
        ],
    ],

    // Map your CrudControllers to TestConfiguration classes
    'configurations' => [
        \App\Http\Controllers\Admin\ProductCrudController::class => \Tests\Config\ProductConfig::class,
    ],
    
    // ... other advanced options
];
```

When you run your tests, `TestConfigHelper` will automatically load this configuration and use your provided inputs instead of the defaults.

<a name="customizing-test-stubs"></a>
## Customizing Test Stubs

You can customize the test stubs used by the generator by publishing them to your application.

```bash
php artisan vendor:publish --provider="Backpack\CRUD\BackpackServiceProvider" --tag=stubs
```

This will create a `resources/views/vendor/backpack/crud/stubs/testing` directory in your application root. Any changes you make to these stubs will be used when generating tests.

### Minimal Stub Example

Here is a minimal example of what a custom operation stub (e.g., `clone.stub`) might look like:

```php
    /**
     * Test that the clone operation functions correctly.
     */
    public function test_can_clone_item()
    {
        // The setUp() method in the base class authenticates the user
        
        $entry = $this->testHelper->createEntry();
        
        $response = $this->post($this->testHelper->getCrudUrl($entry->getKey().'/clone'));
        
        $response->assertStatus(200);
        // Add more specific assertions here
    }
```

The stub receives method content that will be injected into the generated test class. You can use available properties like `$this->model`, `$this->testHelper`, etc.

<a name="how-to-add-new-operations-for-test-generation"></a>
### How to add new operations for test generation

The `backpack:tests` command generates tests based on **stubs** defined in the Backpack package. Currently, stubs are provided for standard operations: `list`, `create`, `update`, `delete`, and `show`.

If you use custom operations (e.g., `clone`, `reorder`, or your own custom actions) and want to test them you can create a **stub file** for your custom operation in `resources/views/vendor/backpack/crud/stubs/testing/feature`. The file name should be `{operation}.stub` (e.g., `clone.stub`). The generator will automatically use this stub when it encounters the custom operation in your controllers.



