# Release Notes

---

**Launch date:** September 24th, 2019

Here are the main differences between [Backpack 3.6](https://backpackforlaravel.com/docs/3.6) and Backpack 4.0.

<a name="added"></a>
# Added
- using Bootstrap 4 instead of Bootstrap 3; 
- new design; we're now using a custom design we made, called [Backstrap](https://backstrap.net), based on [CoreUI](http://coreui.io); (_get it? Backpack... Bootstrap... Backstrap? Pff..._)
- no CSS and JS assets are loaded from CDNs; everything's inside the Backpack/CRUD package, installed through NPM, and gets published into your ```public``` folder upon installation; you don't need to use NPM yourself to install or update assets - the Backpack maintainers do that for you;
- offline & intranet support - thanks to our move away from CDNs, Backpack now works without an internet connection;
- ability to easily add Vue.JS, React, or any other JS file inside all your admin panel pages, by modifying an array in ```config/backpack/base.php```;
- ability to easily remove the bundled JS and CSS and use CDNs if you want to;
- ability to toggle breadcrumbs on/off, in the ```config/backpack/base.php``` config file;
- ability to choose a different theme for Backpack views, in ```config/backpack/base.php```;
- SweetAlerts - instead of showing the default browser alert(), Backpack now uses much prettier SweetAlert pop-ups;
- API to [change breadcrumb links from controllers or from views](/docs/{{version}}/base-how-to#use-breadcrumbs);
- [Widgets](/docs/{{version}}/base-widgets) - easily add stats, pie charts, line charts and other quick stats to a dashboard or CRUD (or any part of your admin panel);
- support for Right-to-Left (RTL) languages; just change ```html_direction``` to ```rtl``` inside ```config/backpack/base.php```;

<a name="removed"></a>
# Removed
- AdminLTE design; we're now using Bootstrap 4 + [CoreUI](http://coreui.io), with a custom design we made, called [Backstrap](https://backstrap.net); (_get it? Backpack... Bootstrap... Backstrap?_)
- Pnotify was removed in favour of Noty to show notificatio bubbles;
- loading multiple files for CSS and JS, on every page; now Backpack's default is to load one CSS bundle file, and one JS bundle file; CSS and JS for field types is still loaded separately, when needed, of course - you wouldn't want all CSS & JS loaded for all 40+ field types, on every page load;
- ```backpack/generators``` and ```laracasts/generators``` are no longer installed automatically; however, the official installation process does instruct you to install them;
- Backpack/Base; All the functionality in Backpack\Base has now been included in the Backpack\CRUD repository; However, we've kept the separate config files (```config/backpack/base.php``` and ```config/backpack/crud.php```) and views are still in the two folders you're used to, ```resources/views/vendor/backpack/base``` and ```resources/views/vendor/backpack/crud```; We've done this so it's easier for you to upgrade, but we _will_ merge the config files and views in the next Backpack 5.0 or 6.0;

<a name="licensing"></a>
# Licensing

<a name="licensing-new-buyers"></a>
## New Buyers

All licenses you can purchase now on BackpackForLaravel.com are valid for _both_ v3 and v4. We've bumped the prices a bit, they're now:
- 0 EUR for non-commercial projects (unlimited developers, one project);
- 69 EUR for a single commercial project (unlimited developers, one project);
- 399 EUR for unlimited commercial projects (unlimited developers, unlimited projects);

Check out our [pricing page](https://backpackforlaravel.com/pricing) for more details, 

<a name="licensing-v3-buyers"></a>
## Backpack v3 Buyers

You can still use Backpack v3.6 for your projects. There's nothing wrong with it, if you don't want the new features in v4. But please know - v3 will only receive _security_ updates in the future: no bug fixes, no new features. We've provided upgrades and new features for Backpack v3 for _more than 3 years_. We hope this means you've already gotten a great bang for your buck out of your Backpack v3 license.

### Single License

Depending on _when_ you've purchased your Backpack v3 Project license, you'll qualify for a discount for upgrading your project to v4:
- bought in 2016 - 10 EUR discount (aprox 14%);
- bought in 2017 - 15 EUR discount (aprox 21%);
- bought in 2018 - 20 EUR discount (aprox 28%);
- bought in January-June 2019 - 25 EUR discount (aprox 36%);
- bought in July-August 2019 - 39 EUR discount (aprox 56%);
- bought in September 2019 - 49 EUR discount (aprox 71%);

If you're an EU resident/company, VAT may be added to your invoice.

There's only one catch: **To receive the discount, you have to purchase the upgrade before October 27th 2019**. You can purchase the upgrade in your Backpack account - you'll notice a new button has shown up next to your v3 license "_Upgrade for xxx EUR_". That button will disappear on October 28th 2019, 00:01 GMT. You will not be able to purchase with a discount after that.


### Unlimited License

Depending on _when_ you've purchased your Backpack v3 Unlimited license, you'll qualify for a discount for upgrading all your projects to v4 _and_ creating new projects with Backpack v4:
- bought in 2016 - 20 EUR discount (aprox 5%);
- bought in 2017 - 40 EUR discount (aprox 10%);
- bought in 2018 - 80 EUR discount (aprox 20%);
- bought in January-June 2019 - 120 EUR discount (aprox 30%);
- bought in July-August 2019 - 200 EUR discount (aprox 51%);
- bought in September 2019 - 299 EUR discount (aprox 75%);

If you're an EU resident/company, VAT may be added to your invoice.

There's only one catch: **To receive the discount, you have to purchase the upgrade before October 27th 2019**. You can purchase the upgrade in your Backpack account - you'll notice a new button has shown up next to your v3 license "_Upgrade for xxx EUR_". That button will disappear on October 28th 2019, 00:01 GMT. You will not be able to purchase with a discount after that.

If you've bought the Unlimited License very recently, and haven't launched any projects using Backpack v3, we don't want you to feel cheated. We can make a special discount for you - we'll discount the entire v3 Unlimited cost from your v4 Unlimited License. You'd only pay the price difference from v3 to v4 (100 EUR), and you'd be licensed to use both v3 and v4 in production. [Contact us](https://backpackforlaravel.com/contact).


<a name="versioning"></a>
# Versioning

When installing Backpack, require its minor version (currently ```4.0.*```). Backpack follows the same versioning system as Laravel 5.x - minor versions include minor breaking changes. This allows us to push new features without charging our users again. For us, this is what ```major.minor.patch``` means:

- ```major``` - **PAID upgrade; MAJOR breaking changes;** historically every 2-3 years; upgrading may take even 2-3 hours; includes major new features, major changes in how the whole system works, and complete rewrites; it allows us to _considerably_ improve the product, and add features that were previously impossible;
- ```minor``` - **FREE upgrade; MINOR breaking changes**; historically every 6-12 months; upgrading takes less than 30 minutes; it allows us to add big new features, for free;
- ```patch``` - **FREE upgrade; NO breaking changes**; historically every week; upgrading can be done automatically with composer; includes bug fixes and non-breaking new features;
