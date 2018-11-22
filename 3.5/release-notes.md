# Release Notes

---

**Launch date:** November 2018

Here are the main differences between [Backpack 3.4](https://backpackforlaravel.com/doc/3.4) and Backpack 3.5.

<a name="backpack-base-1-0-x"></a>
## Backpack\Base 1.0.x

### Added

- added a ```layout_guest``` template which has no sidebar and no top menu;
- **login, register, reset password views look much better**, because they're using this new layout with no sidebar; [details and screenshots here](https://github.com/Laravel-Backpack/Base/pull/330);
- **Backpack authentication is now completely separate** from the scaffolded Laravel authentication; Backpack uses its own auth provider, guard and password broker, that developers can overwrite by adding one with the same name (_backpack_) to their ```config/auth.php``` file; [details here](https://github.com/Laravel-Backpack/Base/pull/293);
- added ```php artisan backpack:base:publish-user-model``` command; [details here](https://github.com/Laravel-Backpack/Base/pull/334);
- added ```php artisan backpack:base:publish-middleware``` command; [details here](https://github.com/Laravel-Backpack/Base/pull/334);
- upon installation, ```BackpackUser``` model and ```CheckIfAdmin``` middleware are published by default - so they can EASILY be customized; [details here](https://github.com/Laravel-Backpack/Base/pull/334);
- two separate files: ```inc/topbar_left_content.blade.php``` and ```inc/topbar_right_content.blade.php``` where the user can specify additional content for the top menu; [details here](https://github.com/Laravel-Backpack/Base/pull/302); [documentation here](docs/{{version}}/base-how-to#use-separate-login-register-forms-for-users-and-admins);
- error views now use a layout file, so it’s easier to customize how all error pages look; defaut error view design is now consistent with default AdminLTE design;
- split ```layout``` into multiple views (```head```, ```scripts```), so it’s easier to customize just one part of it;
- ```backpack_url()``` can now take parameters, just like ```url()```;
- password reset page is a clear two-step process, and pre-populates the email field; big UX improvement for something that is often used by inexperienced users (they're the ones losing their password);

### Fixed
- reset password procedure - you might have it broken in 0.9.0 because of a missing step in the upgrade guide, an using ```App/User``` instead of ```BackpackUser``` in your ```config/backpack/base.php``` file;
- bug where you couldn't use permissions and roles when using ```backpack_auth()``` helper; ```BackpackUser``` now picks up relationships from the User model it extends; [details here](https://github.com/Laravel-Backpack/Base/pull/323);
- default CSS file now uses ```body.skin-purple``` as a selector, to fix the paint glitch, where buttons and other things were shown blue, then changed to purple, when using the purple skin;
- now using jquery and font-awesome from adminlte package instead of CDN;
- language folders like ```da_DK```, ```fr_CA``` and ```pt_br``` have been duplicated into their standardized form (```da-DK```, ```fr-CA``` and ```pt-BR```); introduced notice that the old folders will be deprecated in the next release;
- the footer is now transparent; it is not a primary piece of content, it shouldn’t stand out;

### Removed
- removed Laravel 5.5 support;
- remvoved PHP 7.0 support (since Laravel 5.6 does not support it);

<a name="backpack-crud-3-5-x"></a>
## Backpack\CRUD 3.5.x

### Added

#### List Entries

- [#1675](https://github.com/Laravel-Backpack/CRUD/issues/1675) - **design facelift** for the list view - a lot cleaner; [before & after photo here](https://user-images.githubusercontent.com/1032474/47909985-1dbc2700-de9a-11e8-9f8a-2b1c797e37a4.png);
- [#1702](https://github.com/Laravel-Backpack/CRUD/issues/1702) - **added ```persistent_table``` functionality**, and save state datatables; [documentation here](/docs/3.5/crud-operation-list-entries#persistent-table);
- [#1702](https://github.com/Laravel-Backpack/CRUD/issues/1702) - **sharable links for ListEntries** - filters now add their GET parameters to the current page, so that you can share the URL and the person that gets it will see exactly what you saw;

#### Columns

- [#1703](https://github.com/Laravel-Backpack/CRUD/issues/1703) - **added ```visibleInTable``` attribute to columns**; [documentation here](/docs/3.5/crud-columns#choose-where-columns-are-visible);
- [#1703](https://github.com/Laravel-Backpack/CRUD/issues/1703) - added ```visibleInExport``` attribute to columns; [documentation here](/docs/3.5/crud-columns#choose-where-columns-are-visible);
- [#1706](https://github.com/Laravel-Backpack/CRUD/issues/1706) - **added ```visibleInShow``` attribute to columns**; [documentation here](/docs/3.5/crud-columns#choose-where-columns-are-visible);
- [#1704](https://github.com/Laravel-Backpack/CRUD/issues/1704) - added ```orderLogic``` attribute to columns; [documentation here](/docs/{{version}}/crud-columns#custom-order-logic-for-columns);
- [#1674](https://github.com/Laravel-Backpack/CRUD/issues/1674) - you can now pass parameters to ```model_function``` and ```model_function_attribute``` column types; added to [documentation here](/docs/3.5/crud-columns#model_function);

#### Fields

- [#1694](https://github.com/Laravel-Backpack/CRUD/issues/1694) - you can now further filter the results shown in a ```select```, ```select2```, ```select_multiple```, ```select2_multiple``` field, buy specifying a custom query or scope; adds a new ```options``` attribute to the mentioned field types, so that they behave similarly to ```select_from_array```; [documentation here](/docs/3.5/crud-fields#select-1-n-relationship);
- [#1687](https://github.com/Laravel-Backpack/CRUD/issues/1687) - ```select2_nested``` field type; [documentation here](/docs/3.5/crud-fields#select2_nested);
- [#1695](https://github.com/Laravel-Backpack/CRUD/issues/1695) - added ```select_and_order``` field type; [documentation here](/docs/3.5/crud-fields#select_and_order);
- [#1688](https://github.com/Laravel-Backpack/CRUD/pull/1688) - added ```select_grouped``` and ```select2_grouped``` field types; documentation [here](/docs/3.5/crud-fields#select2_grouped) and [here](/docs/3.5/crud-fields#select_grouped);
- [#1712](https://github.com/Laravel-Backpack/CRUD/issues/1712) - added ```address_google``` field type; [documentation here](/docs/3.5/crud-fields#address_google);
- [#1484](https://github.com/Laravel-Backpack/CRUD/issues/1484) - **the ```select2_from_ajax``` field allows you to filter the results of a select2, depending on what has already been selected in a form** - this means we can now have a select2 that depends on another select2, or a checkbox, or a radio, or whatever; [documentation here](/docs/3.5/crud-how-to#add-a-select2-field-that-depends-on-another-field);
- [#1484](https://github.com/Laravel-Backpack/CRUD/issues/1484) - added ```method``` attribute to ajax select2s; [documentation here](/docs/3.5/crud-fields#select2_from_ajax);
- [#1484](https://github.com/Laravel-Backpack/CRUD/issues/1484) - added ```dependencies``` attribute to ajax select2s; [documentation here](/docs/3.5/crud-fields#select2_from_ajax);

#### Operations

- [#1708](https://github.com/Laravel-Backpack/CRUD/issues/1708) - **added ```Clone``` operation**; [documentation here](/docs/{{version}}/crud-operation-clone);
- [#1516](https://github.com/Laravel-Backpack/CRUD/issues/1516) - setters and getters for the current operation; [documentation here](/docs/{{version}}/crud-operations#getting-and-setting-an-operation-name);
- [#1527](https://github.com/Laravel-Backpack/CRUD/issues/1527) - custom titles, headings and subheadings; [documentation here](/docs/3.5/crud-operations#titles-headings-and-subheadings);
- [#1518](https://github.com/Laravel-Backpack/CRUD/issues/1518) - **CrudPanel class is now Macroable**; [documentation here](/docs/3.5/crud-operations#adding-methods-to-the-crudpanel-object);
- [#1659](https://github.com/Laravel-Backpack/CRUD/pull/1659) - you can now easily change the content class for each operation, so that the content is larger / smaller; [documentation her](/docs/3.5/crud-how-to#change-the-content-class-for-an-operation)

### Fixed

- [#1658](https://github.com/Laravel-Backpack/CRUD/issues/1658) - model function button did not pass ```$crud``` to button;
- [#1680](https://github.com/Laravel-Backpack/CRUD/issues/1680) - Backpack checks that ```CrudTrait``` is used on the Model; otherwise it throws a 500 error with a clear message; you can no longer create CRUD panels for Models that do not use CrudTrait;
- [#1535](https://github.com/Laravel-Backpack/CRUD/issues/1535) - **orderBy got ignored when the user clicked on a column heading to reoder the datatable**; now it doesn't; would only _maybe_ be a breaking change for entities that have an field named ```order```;
- [#1390](https://github.com/Laravel-Backpack/CRUD/issues/1390) - using our own helper ```mb_ucfirst()``` instead of ```ucfirst()```;
- [#791](https://github.com/Laravel-Backpack/CRUD/issues/791) - could not revert changes made in fake field holders; now we can;
- [#1712](https://github.com/Laravel-Backpack/CRUD/issues/1712) - renamed ```address``` field type to ```address_algolia```; alias keeps backwards-compatibility;
- [#1692](https://github.com/Laravel-Backpack/CRUD/issues/1692) - **we can now use arrays for field names**, like ```category[0][name]```;