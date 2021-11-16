# Release Notes

---

**Launch date:** (expected) end of Nov, 2021

Anybody with a 4.0 or 4.1 license can install and use 4.2, but in order to get the new features in a project that currently runs 4.1, you need to [follow the upgrade guide](/docs/{{version}}/upgrade-guide). Don't get fooled by the fact that we kept the 4.x.x prefix - that's just to show that **Backpack 4.2 is a FREE upgrade from 4.1**. But **Backpack 4.2 is a MAJOR new version**, with 12 months of work put into it, and major improvements under-the-hood. It is the current and recommended version of Backpack and it's got so many cool new things that we couldn't fit them all inside this page.

But what Backpack 4.2 brings to the table and why you should upgrade from [Backpack 4.1](/docs/4.1) to 4.2:


<a name="added"></a>
## Added

### Operations

// TODO

<hr>

### Fields

// TODO

<hr> 

### Widgets

// TODO

<hr>

### Developer Experience

#### Easily Debug AJAX Errors using the Error Frame Modal

Did your AJAX request error? No need to dig into your Chrome Devtools and read that awful output. When you have your APP_DEBUG to `true`, Backpack will show that error in a modal. This seems like a small thing, but it makes it _much_ easier to work with some complex Backpack features, like [ListOperation](https://backpackforlaravel.com/docs/4.1/crud-operation-list-entries), [InlineCreateOperation](https://backpackforlaravel.com/docs/4.1/crud-operation-inline-create), [filters](https://backpackforlaravel.com/docs/4.1/crud-filters), [`select2_from_ajax`](https://backpackforlaravel.com/docs/4.1/crud-fields#select2_from_ajax) field, [`relationship`](https://backpackforlaravel.com/docs/4.1/crud-fields#relationship-1) field. During development, if something goes wrong, Backpack will show it to you - it's that simple:

![image](https://user-images.githubusercontent.com/1838187/141500871-039ef53f-4207-4018-ac93-c41b547b62a5.png)

<hr>


<a name="changed"></a>
## Changed

// TODO

<a name="removed"></a>
## Removed

- Support for Laravel 6;
- Support for Laravel 7;
- Support for PHP lower than 7.3 (since Laravel 8 does not support them);

---

In order to get all of the features above (and a few more hidden gems), please [follow the upgrade guide](/docs/{{version}}/upgrade-guide), to get from Backpack 4.1 to Backpack 4.2.
