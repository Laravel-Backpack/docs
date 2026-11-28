# Calendar Operation <span class="badge badge-info">Add-On</span>

--

<a name="about"></a>
## About

This package provides a dead-simple way to add a **Calendar** view to your Backpack CRUDs. It saves many hours of integrating [FullCalendar](https://fullcalendar.io/) and follows Backpack best practices so your admin UI stays consistent and easy to extend.

With Calendar Operation, admins can:
- switch from the List view to a Calendar view;
- preview, edit, or delete events from the calendar;
- create events by selecting a date or range;
- customize the event menu and calendar options without significant code changes.

<a name="requirements"></a>
## Requirements

- [Backpack\CRUD](https://github.com/Laravel-Backpack/CRUD) ^7.0
- [Backpack CalendarOperation](https://backpackforlaravel.com/products/calendar-operation) — paid add-on

> You can also buy Backpack premium add-ons in a bundle at discount prices. See [Backpack pricing](/pricing) for bundle options instead of purchasing individually.

<a name="installation"></a>
## Installation

Install the package via Composer:

```bash
composer require backpack/calendar-operation
```

<a name="how-to-use"></a>
## How to Use

Add the CalendarOperation trait to your CrudController. This also adds the Calendar button toggle to your List view:

```php
use Backpack\CRUD\app\Http\Controllers\CrudController;
use Backpack\CRUD\app\Http\Controllers\Operations\ListOperation;
use Backpack\CalendarOperation\CalendarOperation;

class EntityCrudController extends CrudController
{
    use ListOperation;
    use CalendarOperation;

    // ...
}
```

Then configure the calendar by defining `setupCalendarOperation()` on your CrudController.

<a name="default-configuration"></a>
## Default Configuration

These are the default settings for Calendar Operation. Use them only if you want to change the default behavior.

```php
public function setupCalendarOperation()
{
    // Set the initial view
    CRUD::setOperationSetting('initial-view', 'dayGridMonth');

    // Set the available views
    CRUD::setOperationSetting('views', ['dayGridMonth', 'timeGridWeek', 'timeGridDay']);

    // Are the calendar events editable?
    CRUD::setOperationSetting('editable', true);

    // Set the default colors for events
    CRUD::setOperationSetting('background_color', '#3788d8');
    CRUD::setOperationSetting('text_color', '#ffffff');

    // Inject Backpack's JS widget handling for date fields on create/update views
    CRUD::setOperationSetting('with-javascript-widget', true);
}
```

<a name="calendar-fields"></a>
## Calendar Fields

Calendar Operation uses the following fields by default:

- `title`
- `start`
- `end`
- `background_color`
- `text_color`

You can map your own field names to these calendar fields by overriding `getCalendarFieldsMap()` in your CrudController.

```php
protected function getCalendarFieldsMap(): array
{
    return [
        'title'           => 'event_name',
        'start'           => 'start_date',
        'end'             => 'end_date',
        'background_color'=> 'bg_color',
        'text_color'      => 'text_color',
        'all_day'         => 'all_day',
    ];
}
```

If you are creating a new CRUD, we recommend using the default field names so no mapping is required.

<a name="customizing"></a>
## Customizing

<a name="javascript-options"></a>
### Edit calendar JavaScript options

Backpack provides opinionated defaults, but you can override any FullCalendar option using `javascript-configuration`.

```php
public function setupCalendarOperation()
{
    CRUD::setOperationSetting('javascript-configuration', [
        // FullCalendar options
        // https://fullcalendar.io/docs
        'dayMaxEvents' => false, // default Backpack uses 5
    ]);
}
```

<a name="line-buttons"></a>
### Add/Remove menu items to the calendar event menu

Calendar Operation ships with three default event menu buttons:
- Preview — redirects to the event Show page
- Edit — redirects to the event Edit page
- Delete — deletes the event

You can remove the default buttons or replace them entirely using `CRUD::setOperationSetting('line-buttons', [])`.

```php
public function setupCalendarOperation()
{
    CRUD::setOperationSetting('line-buttons', []);
    // add your custom buttons instead
}
```

If you want custom calendar menu actions, use `addCalendarLineButton()`.

```php
$this->addCalendarLineButton(
    action: 'email',
    label: 'Send Email',
    group: 'send',
    url: fn($event) => backpack_url('send-email', ['email' => $event->email]),
);
```

The button `url` may be generated from the current model instance. The `access` attribute also supports a closure:

```php
$this->addCalendarLineButton(
    action: 'email',
    label: 'Send Email',
    group: 'send',
    url: fn($event) => backpack_url('send-email', ['email' => $event->email]),
    access: fn($event) => backpack_user()->hasRole('admin'),
);
```

<a name="event-colors"></a>
### Configure event colors

You can set event color fields directly in the database or use a closure to compute them from the event model.

```php
public function setupCalendarOperation()
{
    CRUD::setOperationSetting('background_color', fn($event) => $event->active ? 'green' : 'red');
    CRUD::setOperationSetting('text_color', fn($event) => $event->active ? 'white' : 'black');
}
```

<a name="javascript-menu-events"></a>
### Using JavaScript to handle the menu items

Every time a calendar menu item is clicked, a `menuClick` event is dispatched in JavaScript with the button action and properties.

```php
$this->addCalendarLineButton(
    action: 'alert',
    label: 'Javascript Event',
    group: 'alert',
    properties: [
        'message' => 'Alert message!',
    ],
);
```

```javascript
document.getElementById('calendar').addEventListener('menuClick', e => {
  let { action, event, properties } = e.detail;
  if (action === 'alert') {
    alert(`Event ${event.id} — ${properties.message}`);
  }
});
```

This will trigger an alert like `"Event 1 — Alert message!"`.

<a name="support"></a>
## Support

This is a first-party Backpack add-on. If you need help, open a discussion in our [community forum](https://github.com/Laravel-Backpack/community-forum/discussions).

<a name="license"></a>
## License

Calendar Operation is released under Backpack's EULA. See the add-on license file for full details.
