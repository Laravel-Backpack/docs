# Theme Tabler

<a name="install-and-configure">
### About

For more information about installation and/or configuration steps, see [`backpack/theme-tabler`](https://github.com/Laravel-Backpack/theme-tabler) on Github.

<a name="components"></a>
## Components

<a name="view-components">
### View Components

`MenuDropdownMenu` - In addition to regular menu components provided by backpack [Menu Dropdown and Menu Dropdown Item](https://backpackforlaravel.com/docs/base-components#menu-dropdown-and-menu-dropdown-item), Tabler theme provides a new component `Menu Dropdown Menu` which is used to create side by side menus on horizontal layouts. 

```blade
<x-backpack::menu-dropdown title=" Documents" icon="la la-group">
    <x-backpack::menu-dropdown-item title=" Invoices" icon="las la-euro-sign" link="https://example.com" />
        <x-backpack::menu-separator />
        <x-backpack::menu-dropdown title=" Accounting" icon="la la-group" nested="true">
            <x-backpack::menu-dropdown-item title=" Process Invoice Materials" icon="las la-euro-sign" link="https://example.com" />
            <x-backpack::menu-dropdown-item title=" Mark as Paid" icon="las la-euro-sign" link="https://example.com" />
            <x-backpack::menu-dropdown title=" Finance" icon="la la-group" nested="true" :withColumns="true">
                <x-theme-tabler::menu-dropdown-column>
                    <x-backpack::menu-dropdown-item title=" Request SAD" icon="las la-caret-right" link="https://example.com" />
                    <x-backpack::menu-dropdown-item title=" EIOL (pcam)" icon="las la-caret-right" link="https://example.com" />
                </x-theme-tabler::menu-dropdown-column>
                <x-theme-tabler::menu-dropdown-column>
                    <x-backpack::menu-dropdown-item title=" SBOM" icon="las la-caret-right" link="https://example.com" />
                    <x-backpack::menu-dropdown-item title=" BOM" icon="las la-caret-right" link="https://example.com" />
                </x-theme-tabler::menu-dropdown-column>
            </x-backpack::menu-dropdown>
        </x-backpack::menu-dropdown>         
</x-backpack::menu-dropdown>
```
