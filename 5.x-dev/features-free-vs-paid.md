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

Everywhere in our docs, you'll see the <span class="badge badge-pill badge-info">PRO</span> label if that needs the `backpack\pro` package. Everything else... is free, as part of `backpack\crud`. But here's a breakdown of all features, so you can easily understand know what's a good fit for you:

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
      <td class="text-center"><span class="badge badge-success">9 widgets</span> + <span class="badge badge-info">chart</span></td>
    </tr>
    <tr>
      <td><strong>CRUD Panels</strong></td>
      <td class="text-center"></td>
      <td class="text-center"></td>
    </tr>
    <tr>
      <td> &nbsp; - Operations</td>
      <td class="text-center"></td>
      <td class="text-center"></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; - List Operation</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; - Columns</td>
      <td class="text-center"><span class="badge badge-success">25 column types</span></td>
      <td class="text-center"><span class="badge badge-info">31+ column types</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; - Filters</td>
      <td class="text-center">-</td>
      <td class="text-center"><span class="badge badge-info">10+ filter types</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; - Buttons</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; - Export Buttons</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; - Details Row</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; - Search</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; - Create & Update Operations</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; - Fields</td>
      <td class="text-center"><span class="badge badge-success">29 field types</span></td>
      <td class="text-center"><span class="badge badge-info">57+ field types</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; - Validation</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; - Multiple fields per line</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; - Split fields into tabs</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; - Translatable Models</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; - Save Actions</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; - Show Operation</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; - Columns</td>
      <td class="text-center"><span class="badge badge-success">25 column types</span></td>
      <td class="text-center"><span class="badge badge-info">31+ column types</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; - Reorder Operation</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; - Revise Operation</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; - Delete Operation</td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
      <td class="text-center"><span class="badge badge-success">FREE</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; - BulkDelete Operation</td>
      <td class="text-center">-</td>
      <td class="text-center"><span class="badge badge-info">PRO</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; - Clone Operation</td>
      <td class="text-center">-</td>
      <td class="text-center"><span class="badge badge-info">PRO</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; - BulkClone Operation</td>
      <td class="text-center">-</td>
      <td class="text-center"><span class="badge badge-info">PRO</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; - Fetch Operation</td>
      <td class="text-center">-</td>
      <td class="text-center"><span class="badge badge-info">PRO</span></td>
    </tr>
    <tr>
      <td> &nbsp; &nbsp; &nbsp; - InlineCreate Operation</td>
      <td class="text-center">-</td>
      <td class="text-center"><span class="badge badge-info">PRO</span></td>
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
