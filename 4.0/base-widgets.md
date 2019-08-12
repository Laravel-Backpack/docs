# Widgets (aka cards, aka charts, aka graphs)

---

<a name="about"></a>
## About

Widgets provide a simple way to insert blade files into admin panel pages. You can use them to insert cards, charts, notices or custom content into pages.


<a name="how-to-use-widgets"></a>
### How to Use

The default layout that Backpack uses has two widget sections:
- ```before_content```
- ```after_content```

You can easily push widgets to these sections, by making sure a ```$widgets['before_content']``` or ```$widgets['after_content']``` variable will be present in the main view.

1) From the controller:
```php
     /**
     * Show the admin dashboard.
     *
     * @return \Illuminate\Http\Response
     */
    public function dashboard()
    {
        $this->data['title'] = trans('backpack::base.dashboard'); // set the page title
        $this->data['widgets']['before_content'] = [
            [
                'type' => 'card',
                'wrapperClass' => 'col-sm-6 col-md-4',
                'content' => [
                    'header' => 'Some card title',
                    'body' => 'Lorem ipsum dolor sit amet, consectetur adipiscing elit. Duis non mi nec orci euismod venenatis. Integer quis sapien et diam facilisis facilisis ultricies quis justo. Phasellus sem <b>turpis</b>.',
                ]
            ],
        ];

        return view(backpack_view('dashboard'), $this->data);
    }
```

2) From the view:
```php
@extends(backpack_view('blank'))

@php
    $widgets['before_content'][] = [
        'type' => 'card',
        'wrapperClass' => 'col-sm-6 col-md-4',
        'content' => [
            'header' => 'Some card title',
            'body' => 'Lorem ipsum dolor sit amet, consectetur adipiscing elit. Duis non mi nec orci euismod venenatis. Integer quis sapien et diam facilisis facilisis ultricies quis justo. Phasellus sem <b>turpis</b>.',
        ]
    ];
@endphp

@section('content')
@endsection
```

Both of the options above will produce the same result. You can use both options above together, pushing some widgets from the controller, and some from the view.


<a name="mandatory-attributes"></a>
### Mandatory Attributes

When passing a widget array, you need to specify at least these attributes:
```php
[
   'type' => 'card' // the kind of widget to show
   'content' => null // the content of that widget (some are string, some are array) 
],
```

<a name="optional-attributes"></a>
### Optional Attributes

Most widget types also have these attributes present, which you can use to tweak how the widget looks inside the page:
```php
        'wrapperClass' => 'col-sm-6 col-md-4', // customize the class on the parent element (wrapper)
```


<a name="widget-types"></a>
## Default Widget Types

<a name="alert"></a>
## Alert

Shows a notification bubble, with the heading and text you specify:

```php
[
    'type'         => 'alert',
    'class'        => 'alert alert-danger mb-2',
    'heading'      => 'Important information!',
    'content'      => 'Lorem ipsum dolor sit amet, consectetur adipisicing elit. Corrupti nulla quas distinctio veritatis provident mollitia error fuga quis repellat, modi minima corporis similique, quaerat minus rerum dolorem asperiores, odit magnam.',
    'close_button' => true, // show close button or not
]
```

For different colors, you can use the following classes: ```alert-success```, ```alert-warning```, ```alert-info```, ```alert-danger``` ```alert-primary```, ```alert-secondary```, ```alert-light```, ```alert-dark```.

Widget Preview:

![Backpack alert widget](https://backpackforlaravel.com/uploads/v4/widgets/alert.png)


<a name="overwriting-default-widget-types"></a>
## Overwriting Default Widget Types

You can overwrite a widget type by placing a file with the same name in your ```resources\views\vendor\backpack\base\widgets``` directory. When a file is there, Backpack will pick that one up, instead of the one in the package. You can do that from command line using ```php artisan backpack:base:publish widgets/widget-name```

Examples:
- creating a ```resources\views\vendor\backpack\base\widgets\card.blade.php``` file would overwrite the ```card``` widget functionality;
- ```php artisan backpack:base:publish widgets/card``` will take the view from the package and copy it to the directory above, so you can edit it;

>Keep in mind that when you're overwriting a default widget type, you're forfeiting any future updates for that widget. We can't push updates to a file that you're no longer using.

<a name="creating-a-custom-widget-type"></a>
## Creating a Custom Widget Type

Widgets consist of only one file - a blade file with the same name as the widget type (ex: ```card.blade.php```). You can create one by placing a new blade file inside ```resources\views\vendor\backpack\base\widgets```. Be careful to choose a distinctive name, otherwise you might be overwriting a default widget type (see above).

For example, you can create a ```well.blade.php```:
```php
<div class="{{ $widget['wrapperClass'] ?? 'col-sm-6 col-md-4' }}">
  <div class="{{ $widget['class'] ?? 'well mb-2' }}">
    {!! $widget['content'] !!}
  </div>
</div>
```

You can then use the ```well``` widget in a Controller or View:
```php
@extends(backpack_view('blank'))

@php
    $widgets['before_content'][] = [
        'type' => 'well',
        'wrapperClass' => 'col-sm-12',
        'content' => 'This text will be in a div with the class "<i>well</i>".',
    ];
@endphp

@section('content')
@endsection
```

To use information from the database, you can:
- use the full namespace for your models, like ```\App\Models\Product::count()```;
- load all your dashboard information using AJAX calls, if you're loading charts, reports, etc, and the DB queries might take a long time;
- [use view composers](https://laravel.com/docs/5.7/views#view-composers) to push variables inside this view, when it's loaded;

Inside the widget blade files, you include custom CSS and JS, by pushing to the stacks in the layout:
```php
<div class="{{ $widget['wrapperClass'] ?? 'col-sm-6 col-md-4' }}">
  <div class="{{ $widget['class'] ?? 'well mb-2' }}">
    {!! $widget['content'] !!}
  </div>
</div>

@push('after_styles')
  <link href="{{ asset('packages/select2/dist/css/select2.min.css') }}" rel="stylesheet" type="text/css" />
  <style>
    .some_class {
      color: red;
    }
  </style>
@endpush


@push('after_scripts')
  <script src="{{ asset('packages/select2/dist/js/select2.min.js') }}"></script>
  <script>
      jQuery(document).ready(function($) {
          // trigger select2 for each untriggered select2 box
          $('.select2_field').each(function (i, obj) {
              if (!$(obj).hasClass("select2-hidden-accessible"))
              {
                  $(obj).select2({
                      theme: "bootstrap"
                  });
              }
          });
      });
  </script>
@endpush
```


<a name="using-widget-types-from-packages"></a>
## Using a Widget Type from a Pacakge

You can choose the view namespace when loading a widget:

```diff
        $this->data['widgets']['after_content'] = [
            [
                'type' => 'card',
+               'viewNamespace' => 'package::widgets',
                'wrapperClass' => 'col-sm-6 col-md-4',
                'class' => 'card text-white bg-primary text-center',

                'content' => [
                    // 'header' => 'Another card title',
                    'body' => 'Lorem ipsum dolor sit amet, consectetur adipiscing elit. Duis non mi nec orci euismod venenatis. Integer quis sapien et diam facilisis facilisis ultricies quis justo. Phasellus sem <b>turpis</b>, ornare quis aliquet ut, volutpat et lectus. Aliquam a egestas elit.',
                ]
            ],
        ];
```

Similarly, if you want to create widgets somewhere else than in ```resources/views/vendor/backpack/base/widgets```, you can pass that directory as the namespace of your widget. For example, ```resources/views/admin/widgets``` would have ```admin.widgets``` as the namespace.