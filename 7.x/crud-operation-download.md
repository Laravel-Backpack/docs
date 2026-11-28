# Download

--

<a name="about"></a>
## About

Download adds PDF export buttons to your Backpack CRUD entries.
By default it generates a PDF version of the entry using your CRUD fields and
[spatie/browsershot](https://github.com/spatie/browsershot) to render the output
in a headless Chrome browser.

![Backpack Download and BulkDownload buttons](https://user-images.githubusercontent.com/1032474/101194862-3f82cc00-3667-11eb-856c-25c21f0181a5.gif)

<a name="requirements"></a>
## Requirements

- [Backpack for Laravel](https://backpackforlaravel.com/) ^7.0
- [spatie/browsershot](https://github.com/spatie/browsershot)
- Puppeteer and a headless Chrome installation

> Because this package uses Browsershot, it relies on a headless Chrome engine.
> That makes it very flexible, but it also means installation may be more complex
> than a normal PHP-only package.

<a name="installation"></a>
## Installation

### Step 0: Install Puppeteer and Browsershot

Follow the Browsershot installation instructions to install Puppeteer and
Chrome. Test it first in Tinker:

```php
\Spatie\Browsershot\Browsershot::url('https://google.com')
    ->ignoreHttpsErrors()
    ->save('example.pdf');
```

If that fails, fix your Browsershot/Puppeteer installation before continuing.

### Step 1: Install the package

```bash
composer require backpack/download-operation
```

### Step 2: Enable the operation

Add the operation to your CRUD controller:

```php
class InvoiceCrudController extends CrudController
{
    use \Backpack\CRUD\app\Http\Controllers\Operations\ListOperation;
    use \Backpack\CRUD\app\Http\Controllers\Operations\DeleteOperation;
    use \Backpack\DownloadOperation\DownloadOperation;
    use \Backpack\DownloadOperation\BulkDownloadOperation;
```

`BulkDownloadOperation` is not a full CRUD operation by itself; it only adds a
bulk button that points to the normal Download operation. You can use
`DownloadOperation` without `BulkDownloadOperation`, but not the other way around.

<a name="setup"></a>
## Setup

Configure the download behavior in `setupDownloadOperation()`:

```php
public function setupDownloadOperation()
{
    // Option 1: manually add columns
    CRUD::column('title');

    // Option 2: reuse your existing List or Show operation
    $this->setupListOperation();
    // or $this->setupShowOperation();

    CRUD::set('download.view', 'user.invoice.download');
    CRUD::set('download.format', 'A3');
    CRUD::set('download.headers', ['Content-Type' => 'application/pdf']);

    // if you need a custom Browsershot builder
    CRUD::set('download.browsershot', \App\DownloadInvoice::class);
}
```

<a name="configuration"></a>
## Configuration

### Publish config

```bash
php artisan vendor:publish --provider="Backpack\DownloadOperation\AddonServiceProvider" --tag=config
```

### Customize Browsershot

If you need custom PDF generation behavior, return a Browsershot result from a
callable class:

```php
namespace App;

use Spatie\Browsershot\Browsershot;

class DownloadInvoice
{
    public function __invoke($data)
    {
        return Browsershot::html(view($data['view'], $data))
            ->noSandbox()
            ->setChromePath('/usr/bin/google-chrome-stable')
            ->format($data['format'])
            ->pdf();
    }
}
```

Then set it in your controller:

```php
CRUD::set('download.browsershot', \App\DownloadInvoice::class);
```

### Customize the download response

To override the way the file is streamed, define `downloadFile()` in your
controller:

```php
protected function downloadFile($data)
{
    return response()->streamDownload(function () use ($data) {
        echo Browsershot::html(view($data['view'], $data))
            ->format($data['format'])
            ->pdf();
    }, $data['filename'], $data['headers']);
}
```

<a name="troubleshooting"></a>
## Troubleshooting

This package depends on Puppeteer and headless Chrome. Common issues include:

- Chromium not installed or not found on the system
- incorrect Puppeteer cache path
- missing OS dependencies for headless Chrome

If you run into problems, test with Tinker and inspect the error message.

For Mac users with Laravel Valet or M1/M2 machines, you may need to set the
Puppeteer cache directory explicitly:

```bash
PUPPETEER_CACHE_DIR="/Users/your-user/.cache/puppeteer"
```

<a name="security"></a>
## Security

If you discover any security issues, email `cristian.tabacitu@backpackforlaravel.com`
instead of opening the issue tracker.

<a name="license"></a>
## License

DownloadOperation is released under MIT License. See the package repository for
details.
