# CrudFields JavaScript API

---

<a name="about"></a>
## About

If you need to add custom interactions (if field is X then do Y), we have just the thing for you. You can easily add custom interactions, using our **CrudField JavaScript API**. It's already loaded on our Create / Update pages, in the global `crud` object, and it makes it dead-simple to select a field - `crud.field('title')` - using a syntax that's very familiar to our PHP syntax, then do the most common things on it.

<a name="syntax"></a>
## Syntax

Here's everything our **CrudField JavaScript API** provides:
- selectors:
    - `crud.field('title')` -> returns the `CrudField` object for a field with the name `title`;
    - `crud.field('testimonials').subfield('text')` -> returns the `CrudField` for the `text` subfield within the `testimonials` repeatable field;
- properties on `CrudField`:
    - `.name` - returns the field name (eg. `title`);
    - `.type` - returns the field type (eg. `text`);
    - `.input` - returns the DOM element that actually holds the value;
    - `.value` - returns the value of that field (as `string`);
- events on `CrudField`:
    - `.onChange(function(field) { do_someting(); })` - calls that function each type the field changes (which for most fields is upon each keytype);
- methods on `CrudField`:
    - `.hide()` - hides that field;
    - `.show()` - shows that field, if it was previously hidden;
    - `.disable()` - makes that field's input `disabled`;
    - `.enable()` - removes `disabled` from that field's input;
    - `.require()` - adds an asterisk next to that field's label;
    - `.unrequire()` - removes any asterisk next to that field's label;
- specialty methods on `CrudField`:
    - `.check()` - if the field is a checkbox, checks it;
    - `.uncheck()` - if the field is a checkbox, unchecks it;

The beauty of this solution is that... it has no limit. Since it's only a JS library that makes the most difficult things easy... there is no limit to what you can do with it. Just write pure JS or jQuery on top of it, to achieve your business logic.

<a name="how-to-use"></a>
## How to Use

**Step 1.** Create a file to hold the custom JS. As a convention, we recommend you split up the JS by entity name, so for a Product entity we recommend creating `public/assets/js/admin/forms/product.js`.

**Step 2.** Load that script file in your CrudController, within `setupCreateOperation()` or `setupUpdateOperation()` (depending on when you want it loaded):

```php
    Widget::add()->type('script')->content('public/assets/js/admin/forms/product.js');
```

**Step 3.** Inside that file, use the CrudField JS API to manipulate fields, in any way you want. For example:

```javascript
 crud.field('agree_to_terms').onChange(function(field) {
    if (field.value == 1) {
        crud.field('agree_to_marketing_email').show();
    } else {
        crud.field('agree_to_marketing_email').hide();
    }
 });
```

Alternatively, since the `show()` and `hide()` methods also take a `boolean` as a parameter, the above can also become:

```javascript
 crud.field('agree_to_terms').onChange(function(field) {
    crud.field('agree_to_marketing_email').show(field.value == 1);
 });
```


<a name="examples"></a>
## Examples

We've identified the most common ways developers need to add interaction to their forms. Then we've documented them below. That way, it's easy for you to just copy-paste a solution, then customize to your needs. Take note that you can also see these examples working live, in [our demo](https://demo.backpackforlaravel.com/admin/field-monster/create).

<a name="scenario-1"></a>
### Scenario 1. Show / hide a field

When a checkbox is checked, show a second field:

```javascript
//
crud.field('visible').onChange(field => {
  crud.field('visible_where').show(field.value == 1);
}).change();
```

TODO: add GIF

<a name="scenario-2"></a>
### Scenario 2. TODO

<a name="scenario-3"></a>
### Scenario 3. TODO

<a name="scenario-4"></a>
### Scenario 4. TODO

<a name="scenario-5"></a>
### Scenario 5. TODO

<a name="scenario-6"></a>
### Scenario 6. TODO

<a name="scenario-7"></a>
### Scenario 7. TODO

<a name="scenario-8"></a>
### Scenario 8. TODO

<a name="scenario-9"></a>
### Scenario 9. TODO

<a name="scenario-10"></a>
### Scenario 10. TODO

<a name="scenario-11"></a>
### Scenario 11. TODO

<a name="scenario-12"></a>
### Scenario 12. TODO
