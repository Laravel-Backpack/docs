# Features (Free vs Paid)

---

Starting with Backpack v5, our software is open-core. That means there are features that you can use for free, and features you can only access by purchasing. Our goal with this split was to have:
- a simplified version, that includes what most admin panel absolutely need, in `backpack\crud`; <span class="badge badge-pill badge-success">FREE</span>
- a plug-and-play addon, that adds the features for more complex admin panels, in `backpack\pro`; <span class="badge badge-pill badge-info">PRO</span>

You do not _need to_ purchase anything from us. But we hope that:
- if you're making money from your project, as soon as you need _one_ paid feature, you can justify the cost, to save the time it takes to build that yourself;
- if you're _not_ making money from your project yet, as project grows and starts making a profit, you'll _want to_ purchase, to get accesss to the paid features and support its development;

<a name="feature-list"></a>
## Features

Everywhere in our docs, you'll see the <span class="badge badge-pill badge-info">PRO</span> label if that needs the `backpack\pro` package. Everything else... is free, as part of `backpack\crud`. But here's a comparison table of all features, so you can easily understand what will be a good fit for you:

<table class="table table-sm table-striped table-hover">
  <thead>
    <tr>
      <th></th>
      <th class="text-center">Backpack\CRUD</th>
      <th class="text-center">Backpack\CRUD + <br>Backpack\PRO</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Admin UI</strong></td>
      <td class="text-center"></td>
      <td class="text-center"></td>
    </tr>
    <tr>
      <td> &nbsp; - Alerts</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; - Authentication</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; - Breadcrumbs</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; - HTML Components</td>
      <td class="text-center"><span class="badge badge-success">180+ components</span></td>
      <td class="text-center"><span class="badge badge-success">180+ components</span></td>
    </tr>
    <tr>
      <td> &nbsp; - Widgets</td>
      <td class="text-center"><span class="badge badge-success">9 widgets</span></td>
      <td class="text-center"><span class="badge badge-success">9 widgets</span> + <a href="/docs/5.x-dev/base-widgets#chart-pro" class="badge badge-info text-white" data-toggle="tooltip" title="Easily create charts from your database entries!">chart</a></td>
    </tr>
    <tr>
      <td><strong>CRUD Panels</strong></td>
      <td class="text-center"></td>
      <td class="text-center"></td>
    </tr>
    <tr>
      <td> &nbsp; - List Operation</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; - Columns</td>
      <td class="text-center"><span class="badge badge-success">25 column types</span></td>
      <td class="text-center"><a href="/docs/5.x-dev/crud-columns#pro-column-types" class="badge badge-info text-white" data-toggle="tooltip" title="Easily show arrays, markdown, relationships, tables and videos!">31+ column types</a></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; - Filters</td>
      <td class="text-center">-</td>
      <td class="text-center"><a href="/docs/5.x-dev/crud-filters" class="badge badge-info text-white" data-toggle="tooltip" title="Help your admin easily filter their table view - by date, text, options, date range... and more!">10+ filter types</a></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; - Buttons</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; - Export Buttons</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; - Details Row</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; - Search</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; - Create & Update Operations</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; - Fields</td>
      <td class="text-center"><span class="badge badge-success">29 field types</span></td>
      <td class="text-center"><a href="/docs/5.x-dev/crud-fields#pro-field-types" class="badge badge-info text-white" data-toggle="tooltip" title="For relationships with a lot of entries, complex relationships, fields with subfields, WYSIWYGs, addresses, videos, images and A LOT more!">57+ field types</a></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; - Validation</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; - Multiple fields per line</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; - Split fields into tabs</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; - Translatable Models</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; - Save Actions</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; - Show Operation</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; - Columns</td>
      <td class="text-center"><span class="badge badge-success">25 column types</span></td>
      <td class="text-center"><a href="/docs/5.x-dev/crud-columns#pro-column-types" class="badge badge-info text-white" data-toggle="tooltip" title="Easily show arrays, markdown, relationships, tables and videos!">31+ column types</a></td>
    </tr>
    <tr>
      <td> &nbsp; - Reorder Operation</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; - Revise Operation</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; - Delete Operation</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; - BulkDelete Operation</td>
      <td class="text-center">-</td>
      <td class="text-center"><a href="/docs/5.x-dev/crud-operation-delete#delete-multiple-items-bulk-delete-pro" class="badge badge-info text-white" data-toggle="tooltip" title="Easily delete multiple entries in one go!">PRO</a></td>
    </tr>
    <tr>
      <td> &nbsp; - Clone Operation</td>
      <td class="text-center">-</td>
      <td class="text-center"><a href="/docs/5.x-dev/crud-operation-clone" class="badge badge-info text-white" data-toggle="tooltip" title="Easily duplicate an entry!">PRO</a></td>
    </tr>
    <tr>
      <td> &nbsp; - BulkClone Operation</td>
      <td class="text-center">-</td>
      <td class="text-center"><a href="/docs/5.x-dev/crud-operation-clone#clone-multiple-items-bulk-clone" class="badge badge-info text-white" data-toggle="tooltip" title="Easily duplicate multiple entries in one go!">PRO</a></td>
    </tr>
    <tr>
      <td> &nbsp; - Fetch Operation</td>
      <td class="text-center">-</td>
      <td class="text-center"><a href="/docs/5.x-dev/crud-operation-fetch" class="badge badge-info text-white" data-toggle="tooltip" title="Easily respond to AJAX requests from relationship, select2_from_ajax and select2_from_ajax_multiple fields!">PRO</a></td>
    </tr>
    <tr>
      <td> &nbsp; - InlineCreate Operation</td>
      <td class="text-center">-</td>
      <td class="text-center"><a href="/docs/5.x-dev/crud-operation-inline-create" class="badge badge-info text-white" data-toggle="tooltip" title="Empower your admins to add related entries in a modal, without leaving the main form!">PRO</a></td>
    </tr>
  </tbody>
</table>

<br><br>
Both `backpack/crud` and `backpack/pro` will keep receiving active attention, maintenance and care from us, for many years going forward - this is our job. But in principle, new features will _not_ be included in `backpack/crud`. We will keep adding new features to `backpack/pro`, though. If you have suggestions, please [open an issue here](https://github.com/laravel-backpack/ideas).

<a name="no-license-needed-on-localhost"></a>
## Add-ons

In addition the our main packages (`backpack\crud` and `backpack\pro`), whose features we've detailed above, we've also developed a series of single-purpose Backpack add-ons. Most developers won't need these, but those who do... will be grateful that we took the time to do it for them. These first-party add-ons are:
    - some free: `backpack\permissionmanager`, `backpack\settings`, `backpack\pagemanager`, `backpack\newscrud`, `backpack\menucrud`, `backpack\filemanager`, `backpack\logmanager`, `backpack\backupmanager`, `backpack\revise-operation` etc. <span class="badge badge-pill badge-success">FREE</span>
    - some paid: `backpack\devtools`, `backpack\figma-template` <span class="badge badge-pill badge-warning">PAID EXTRA</span>

We also encourage our community to build third-party add-ons (we've made it [super-easy](/docs/{{version}}/add-ons-tutorial-using-the-addon-skeleton)). Our plan is to create more and more add-ons, as we discover more problems we can solve for Laravel freelancers & development teams.

For more information, see:
- [our addons page](https://backpackforlaravel.com/addons) for more addons (including third-party addons);
- [our products page](https://backpackforlaravel.com/products) for the first-party addons that we think are _so good_, that they're worth paying for;
