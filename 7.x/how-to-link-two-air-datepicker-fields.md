# How to link two air-datepicker fields

---

<a name="about"></a>
## About

The [`air-datepicker`](/docs/{{version}}/crud-fields#air-datepicker) field is a Backpack PRO field type, powered by [air-datepicker](https://air-datepicker.com/). It can work as a date picker, datetime picker or range picker.

Sometimes you want **two separate pickers** that depend on each other - for example a *From date* and a *To date*, where the *To date* should never be before the *From date*. Instead of letting the admin pick invalid dates and catching it in validation, you can make the second picker simply **disable** the invalid dates.

All it takes is a small JavaScript file. This page shows how.

<a name="the-fields"></a>
## Step 1. The fields

Add two single `air-datepicker` fields to your Create and Update operations:

```php
CRUD::field([
    'name'  => 'start_date',
    'label' => 'From date',
    'type'  => 'air-datepicker',
]);

CRUD::field([
    'name'  => 'end_date',
    'label' => 'To date',
    'type'  => 'air-datepicker',
]);
```

<a name="the-script"></a>
## Step 2. Add the script to the operation

In your CrudController, inside `setupCreateOperation()` and/or `setupUpdateOperation()`, load a JavaScript file using a `script` widget:

```php
use Backpack\CRUD\app\Library\Widget;

Widget::add()->type('script')->content('assets/js/admin/forms/link-dates.js');
```

<a name="the-javascript"></a>
## Step 3. The JavaScript

Create the JS file and link the two pickers:

```js
// assets/js/admin/forms/link-dates.js
$(function () {
    // Backpack initializes its fields inside its own document-ready handler,
    // registered after this one - defer with a macrotask so the instances
    // already exist.
    setTimeout(linkDates, 0);
});

function linkDates() {
    var startDp = bpFieldAirDatepickerInstance('start_date');
    var endDp   = bpFieldAirDatepickerInstance('end_date');

    if (!startDp || !endDp) return;

    // What minDate/maxDate each picker was configured with in PHP ('' by default).
    var startDefaultMaxDate = startDp.opts.maxDate;
    var endDefaultMinDate   = endDp.opts.minDate;

    // One way: "To date" can't be before "From date"...
    linkAirDatepickers(startDp, endDp, 'minDate', endDefaultMinDate);
    // ...and the other way: "From date" can't be after "To date".
    linkAirDatepickers(endDp, startDp, 'maxDate', startDefaultMaxDate);
}

function linkAirDatepickers(sourceDp, targetDp, limit, targetDefaultLimit) {
    var originalOnSelect = sourceDp.opts.onSelect; // the field's own handler

    sourceDp.opts.onSelect = function (args) {
        // keep the field's built-in behavior (updates the hidden input)
        originalOnSelect.apply(this, arguments);

        if (args.date) {
            targetDp.update({ [limit]: args.date });

            // optional: clear the target if its current selection is now invalid
            var targetSelected = targetDp.selectedDates[0];
            if (targetSelected && (
                (limit === 'minDate' && targetSelected.getTime() < args.date.getTime()) ||
                (limit === 'maxDate' && targetSelected.getTime() > args.date.getTime())
            )) {
                targetDp.clear();
            }
        } else {
            // source cleared: restore the originally configured limit.
            // NOTE: passing null throws in air-datepicker 3.6.0 - restore ''
            // (or the original value) instead.
            targetDp.update({ [limit]: targetDefaultLimit || '' });
        }
    };

    // also apply the link on page load, when editing an entry that already
    // has a source value
    var initialSource = sourceDp.selectedDates[0];
    if (initialSource) {
        targetDp.update({ [limit]: initialSource });
    }
}
```

<a name="how-it-works"></a>
## How it works

- The `air-datepicker` field stores its air-datepicker instance on the visible input, and provides one JS helper to reach it - `bpFieldAirDatepickerInstance(fieldName)`, which returns the instance (or `false`). With the instance you can use the [full air-datepicker API](https://air-datepicker.com/docs): `dp.update()`, `dp.selectDate()`, `dp.clear()`, `dp.onSelect`, etc.
- We wrap the source picker's `onSelect` callback, being careful to call the field's original handler first, so the hidden input keeps getting updated as usual.
- When the source date is picked, we update the target's `minDate` or `maxDate`. When the source is cleared, we restore the limit it was configured with in PHP.

<a name="notes"></a>
## Notes

- `onSelect` fires when a date is selected **and** when the picker is cleared; on clear `date` is `undefined`, so check for it (like above) to distinguish the two.
- `dp.update({ minDate: null })` throws in air-datepicker 3.6.0 - restore the originally configured value, or `''` when there was none, instead.
- The instances only exist after Backpack initializes its fields (in its own ready handler). Defer your code with `$(function(){ setTimeout(fn, 0); })`, or call `bpFieldAirDatepickerInstance()` from an event that runs after init.
- `bpFieldAirDatepickerInstance()` works with top-level form fields. For fields inside repeatable rows or modals, grab the row-scoped instance instead - for example `$('input[data-air-datepicker]', row).data('air-datepicker-instance')`.

<a name="advanced"></a>
## Doing more with the instance

The instance getter is not just for linking fields. Use it for anything the air-datepicker API supports:

```js
var dp = bpFieldAirDatepickerInstance('start_date');

dp.update({ minDate: new Date('2026-01-01') }); // change limits on the fly
dp.selectDate(new Date());                      // pick a date programmatically
dp.clear();                                     // clear the picker
```

See the air-datepicker [docs](https://air-datepicker.com/docs) and [methods](https://air-datepicker.com/methods) for the full list.
