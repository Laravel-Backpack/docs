---
title: "Features"
excerpt: ""
---
Backpack/Base kickstarts your admin panel building by:
- pulling in the AdminLTE HTML theme, based on Bootstrap 3;
- overwriting the default Laravel views for authentication with new ones with AdminLTE design;
- integrating [prologue/alerts](https://github.com/prologuephp/alerts) and and [pnotify](https://github.com/sciactive/pnotify) for showing notification bubbles upon error/success/warning/info;
- providing pretty error pages for most common errors;
- providing a horizontal menu and a side menu you can customize;
[block:api-header]
{
  "title": "Triggering Notification Bubbles in PHP"
}
[/block]
We use [prologue/alerts](https://github.com/prologuephp/alerts#adding-alerts-through-alert-levels) to trigger notifications. Check out its documentation for the entire syntax.

Basic example:
[block:code]
{
  "codes": [
    {
      "code": "<?php\n\npublic function foo() \n{\n  \\Alert::info('This is a blue bubble.');\n  \\Alert::warning('This is a yellow/orange bubble.');\n  \\Alert::error('This is a red bubble.');\n  \\Alert::success('This is a green bubble.');\n  \n  // the layout will make sure to show your notifications\n  return view('some_view');\n}\n\npublic function bar()\n{\n  \\Alert::success('You have successfully logged in')->flash();\n \n  // please note the above flash() method; this will store your notification in a session variable, so that you can redirect to another page, but the notification will still be shown (on the page you redirect to)\n  return Redirect::to('some-url');\n}",
      "language": "php"
    }
  ]
}
[/block]

[block:api-header]
{
  "title": "Triggering Notification Bubbles in JavaScript"
}
[/block]
We use [PNotify](https://sciactive.com/pnotify/) to show notifications from JavaScript, on the same page. Check out its page for more detailed use.

Basic example:
[block:code]
{
  "codes": [
    {
      "code": "new PNotify({\n  title: \"Operation successful\",\n  text: \"You have deleted the internet.\",\n  type: \"success\"\n});\n\n// available types: success, info, warning, error\n// PLEASE NOTE it's \"error\" here, not \"danger\"",
      "language": "javascript"
    }
  ]
}
[/block]