# Release Notes

---

**Planned launch date:** Dec 4th, 2024

For the past 2 years, we've done our very best to make all changes to Backpack in a backwards-compatible way. To not push a new version, because we know it's a pain in the bee's hind to upgrade stuff. But... it's time for a new release. Have no fear though, we've made this super easy for you.

Backpack v7 is a maintenance release. Yes, it does have a few breaking changes, but they will not impact 99.9% of projects. But that doesn't mean you shouldn't follow the upgrade guide! Please do - read it from top to bottom and make sure none of the changes impact you.

Here are the BIG things Backpack v7 brings to the table and why you should upgrade from [Backpack v6](/docs/6.x) to v7. But first... we should give credit where credit is due. **Big BIG thanks to**:
- **[Pedro Martins](https://github.com/pxpm)** for x;
- **[Jorge Castro](https://github.com/jcastroa87)** for y;
- **[Karan Datwani](https://github.com/karandatwani92)** for z;
- **[Cristian Tabacitu](https://github.com/tabacitu)** for t;
- **our paying customers**, who have made all of this possible by supporting our work 🙏

Together, our team has put in an incredible amount of work to make v7 what it is - more than XXX commits, across YYY months, all while still maintaining, bug fixing and improving v6. Again, big thanks to everybody who has helped made this happen 🙏

<a name="added"></a>
## Added

### CRUD Lifecycle Hooks

Previously when working with Operations, developers found themselves needing to _override_ an entire operation method, in order to do things before/after the routes/defaults/operation is set up. This created a lot of duplicate code, and made it hard to maintain. Now, you can use CRUD Lifecycle Hooks to add your own code before/after each operation method. This is a much cleaner way to add your own code, without having to override the entire method. [Read more](/docs/{{version}}/crud-operations#lifecycle-hooks).

### Two-Factor Authentication

// TODO

### Re-Usable Filters

// just like your air purifier
// TODO

### Filters inside CustomViews

Filters can now be used inside [Custom Views for your List operation](https://backpackforlaravel.com/docs/{{version}}/crud-operation-list-entries#custom-views-for-listoperation-pro). This means once the admin has selected the Custom View, they can further drill down in the list, using the filters. But not only that... you can _remove_ the general filters and add entirely new filters, just for that Custom View.

### Browser Tests

// TODO


<a name="changed"></a>
## Changed

### Uploaders

// TODO

### Moved TinyMCE and CKEditor fields & columns

// TODO

### Basset

// TODO

### Parent Theme

// TODO

<a name="removed"></a>
## Removed

- Support for Laravel 10?! 👀
- Support for PHP lower than 8.2?

---

If you like what you see, please help us out - share what you like on social media or tell a friend. To get all of the features above (and a lot more), please [follow the upgrade guide](/docs/{{version}}/upgrade-guide).
