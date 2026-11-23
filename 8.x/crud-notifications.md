# CRUD Notifications

---

<a name="about"></a>
## About

Backpack includes notifications for quick feedback after an action. Toast notifications use the familiar `Noty` JavaScript API and dialogs use the familiar `swal` API, but both render native Bootstrap components. You can trigger them from PHP or JavaScript.

<a name="toasts"></a>
## Toast Notifications

Use a toast for a short, non-blocking message. The PHP API supports four types: `success`, `error`, `warning`, and `info`.

<a name="toasts-php"></a>
### From PHP

Create a toast with the `Alert` facade:

```php
\Alert::success('The article was saved.');
\Alert::error('The article could not be saved.');
\Alert::warning('This action cannot be undone.');
\Alert::info('A new version is available.');

// Or choose the type dynamically.
\Alert::add('success', 'The article was saved.');
```

Alerts are available in the current response. To display one after a redirect, call `flash()` before returning the redirect:

```php
\Alert::success('The article was saved.')
    ->title('Saved')
    ->icon('la la-check-circle')
    ->timeout(5000)
    ->flash();

return redirect()->route('article.index');
```

| Method | Default | Description |
| --- | --- | --- |
| `title(string $title)` | Empty | Adds a title above the message. |
| `icon(string $icon)` | None | Adds an icon class, for example `la la-check-circle`. |
| `timeout(int $milliseconds)` | `2500` | Sets how long the toast is visible. Use `0` for a persistent toast. |
| `dismissible(bool $value = true)` | `true` | Shows or hides the close button. |
| `className(string $class)` | None | Replaces the type color with custom CSS classes. |
| `position(string $position)` | Configured default | Places the toast using a configured position alias. |
| `flash()` | N/A | Stores all queued notifications in the session for the next request. |

The default position aliases are `topRight`, `topLeft`, `bottomRight`, `bottomLeft`, `topCenter`, `bottomCenter`, and `center`. Configure the default container, aliases, timeout, and close behavior in `config/backpack/alerts.php`.

<a name="toasts-javascript"></a>
### From JavaScript

Use `Noty` anywhere Backpack's JavaScript is loaded:

```js
new Noty({
    type: 'success',
    title: 'Saved',
    text: 'The article was saved.',
    icon: 'la la-check-circle',
    timeout: 5000,
    position: 'bottomRight',
}).show();
```

`text` and `message` are interchangeable. For a simple notification, pass a string:

```js
new Noty('The article was saved.').show();
```

| Option | Default | Description |
| --- | --- | --- |
| `type` | `info` | Determines the color. Supported values are `success`, `error` or `danger`, `warning`, `info`, `information`, `alert`, `notice`, `primary`, and `secondary`. |
| `text` or `message` | Empty | The toast message. HTML is rendered, so do not pass untrusted content. |
| `title` | Empty | Adds a title above the message. |
| `icon` | None | Icon CSS classes to show before the message. |
| `timeout` | Configured default | Visibility time in milliseconds. Use `0` for a persistent toast. |
| `dismissible` | Configured default | Shows or hides the close button. |
| `close_on_click` | Configured default | Closes the toast when it is clicked. |
| `className` | Type color | Replaces the type color with custom CSS classes. |
| `position` | Configured default | Uses a position alias from `backpack.alerts.toast.positions`. |

Close every visible toast with:

```js
Noty.closeAll();
```

<a name="modals"></a>
## Modal Notifications

Use a modal when the message needs the user's full attention. The PHP API uses `dialog...` methods; the JavaScript API uses `swal`.

<a name="modals-php"></a>
### From PHP

To show a `swal`-style modal from PHP, queue a dialog with the `Alert` facade. It is rendered in the response just like a toast. Call `flash()` before a redirect so it is available on the next page:

```php
\Alert::dialog('Article saved', 'The article is now visible to editors.')
    ->flash();

return redirect()->route('article.index');
```

`dialog()` creates an informational modal. For the other icon types, use `dialogSuccess()`, `dialogError()`, `dialogWarning()`, or `dialogInfo()`. These methods accept the message first and an optional title second:

```php
\Alert::dialogError('The article could not be saved.', 'Save failed')
    ->backdrop(false)
    ->escapeKey(false)
    ->button('Review the form')
    ->showCloseButton()
    ->flash();

return redirect()->back();
```

Without `flash()`, the modal is rendered in the current response only. PHP dialogs are display-only; use JavaScript `swal()` when the current page needs to react to the user's choice.

```php
\Alert::dialogSuccess('The article was saved.', 'Saved')
    ->timer(5000)
    ->button('Continue')
    ->showCloseButton();
```

#### PHP Modal Limitations

PHP dialogs support one optional dismiss button, whose label you can set with `button()`. They do not support multiple buttons or button values, custom content or inputs, button callbacks, or handling the user's selection: the PHP request has already finished when the modal is displayed. Use JavaScript `swal()` for confirmation flows, custom actions, input dialogs, or any workflow that needs to react to the selected button.

| Method | Default | Description |
| --- | --- | --- |
| `title(string $title)` | Empty | Sets the dialog title. |
| `text(string $text)` | Empty | Sets the dialog message. |
| `icon(string $icon)` | Dialog type | Uses `success`, `error`, `warning`, or `info`. |
| `timer(?int $milliseconds)` | `null` | Closes the dialog automatically after the given time. |
| `button(string|bool $text = 'OK')` | `OK` | Sets the confirm button text. Pass `false` to hide it. |
| `backdrop(bool $value = true)` | `true` | Allows or prevents closing the dialog by clicking outside it. |
| `escapeKey(bool $value = true)` | `true` | Allows or prevents closing the dialog with the Escape key. |
| `className(?string $class)` | None | Adds CSS classes to the modal content. |
| `showCloseButton(bool $value = true)` | `false` | Shows or hides the close button in the top-right corner. |
| `flash()` | N/A | Stores all queued notifications in the session for the next request. |

<a name="modals-javascript"></a>
### From JavaScript

`swal()` returns a promise that resolves with the selected button value, or `null` when the dialog is dismissed.

```js
swal({
    title: 'Delete this article?',
    text: 'This action cannot be undone.',
    icon: 'warning',
    buttons: {
        cancel: { text: 'Cancel', value: null, className: 'bg-secondary' },
        confirm: { text: 'Delete', value: true, className: 'bg-danger' },
    },
    dangerMode: true,
}).then((confirmed) => {
    if (confirmed) {
        // Delete the article.
    }
});
```

| Option | Description |
| --- | --- |
| `title`, `text` | Dialog title and message. The message is rendered as text. |
| `icon` | One of `success`, `error`, `warning`, or `info`. |
| `button` | A confirm button label or object, for example `{ text: 'Save', value: true, className: 'bg-primary' }`. |
| `buttons` | `true` for Cancel and OK, `false` for no buttons, an array of labels, or an object of named button definitions. |
| `timer` | Auto-closes the dialog after this number of milliseconds. |
| `closeOnClickOutside` | Set to `false` to require an explicit action. |
| `closeOnEsc` | Set to `false` to disable the Escape key. |
| `showCloseButton` | Shows a close button in the top-right corner. |
| `className` | Adds CSS classes to the modal content. |
| `dangerMode` | Styles the final action button as dangerous. |
| `content` | Use `'input'`, a DOM node, or `{ element, attributes }` to add content to the dialog. |

For short calls, use positional arguments:

```js
swal('Article saved', 'The article is now visible to editors.', 'success');
swal('The article was saved.');
```

You can close the active dialog with `swal.close()`. Use `swal.setActionValue(value)` to change the confirm value, or pass an object keyed by button name to change several button values.

<a name="legacy-libraries"></a>
## Use The Full Noty Or SweetAlert Library

Backpack ships small, Bootstrap-based compatibility layers for `Noty` and `swal`. They support the options documented above. If you need an option that Backpack does not support, you can load the original library instead.

For JavaScript calls, publish or create the shared UI overrides at `resources/views/vendor/backpack/ui/inc/scripts.blade.php` and `resources/views/vendor/backpack/ui/inc/styles.blade.php`. Copy their current contents from `vendor/backpack/crud/src/resources/views/ui/inc/`, then add the following **after** Backpack loads `backpack-notifications.js` in `scripts.blade.php`:

```blade
@basset('https://cdn.jsdelivr.net/npm/noty@3.2.0-beta-deprecated/lib/noty.min.js')
@basset('https://cdn.jsdelivr.net/npm/sweetalert@2.1.2/dist/sweetalert.min.js')
```

Add the Noty styles to `styles.blade.php`:

```blade
@basset('https://cdn.jsdelivr.net/npm/noty@3.2.0-beta-deprecated/lib/noty.css')
@basset('https://cdn.jsdelivr.net/npm/@digitallyhappy/backstrap@0.5.1/dist/css/legacy.min.css')
```

The upstream scripts replace Backpack's `window.Noty` and `window.swal` implementations, so your subsequent JavaScript can use the complete library APIs. Loading the upstream SweetAlert library does not need an additional stylesheet.

For PHP-flashed notifications, also override your active theme's `inc/alerts.blade.php` view. Backpack's toast payload uses `message`, whereas upstream Noty expects `text`. Before calling `new Noty(msg).show()`, adapt each message:

```js
if (typeof msg === 'string') {
    msg = { text: msg };
} else {
    msg.text = msg.message || msg.text || '';
}

msg.type = type;
new Noty(msg).show();
```

For the full legacy PHP behavior, start from the alerts view in the Backpack version you are upgrading from and adapt it to your active theme. Keep this override maintained when upgrading Backpack, because it replaces the built-in notification rendering.