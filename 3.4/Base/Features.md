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
We use [prologue/alerts](https://github.com/prologuephp/alerts#adding-alerts-through-alert-levels) to trigger notifications. Check out its documentation for the entire syntax. Basic examples:
[block:code]
{
  "codes": [
    {
      "code": "<?php\n\npublic function foo() \n{\n  \\Alert::info('This is a blue bubble.');\n  \\Alert::warning('This is a yellow/orange bubble.');\n  \\Alert::error('This is a red bubble.');\n  \\Alert::success('This is a green bubble.');\n  \\Alert::success('<strong>Got it</strong><br>This is an HTML message.');\n\n  \n  // the layout will make sure to show your notifications\n  return view('some_view');\n}\n\npublic function bar()\n{\n  \\Alert::success('You have successfully logged in')->flash();\n \n  // please note the above flash() method; this will store your notification in a session variable, so that you can redirect to another page, but the notification will still be shown (on the page you redirect to)\n  return Redirect::to('some-url');\n}",
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
We use [PNotify](https://sciactive.com/pnotify/) to show notifications from JavaScript, on the same page. Check out its page for more detailed use. Basic example:
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

[block:api-header]
{
  "title": "Helpers"
}
[/block]
You can use these helpers anywhere in your app (models, views, controllers, requests, etc), except the config files, since the config files are loaded _before_ the helpers.
[block:parameters]
{
  "data": {
    "h-0": "Helper",
    "h-1": "Details",
    "0-0": "**```backpack_url($path)```** ",
    "0-1": "Use this helper instead of ```url()``` to generate paths with the admin prefix prepended.",
    "1-0": "```backpack_authentication_column()```",
    "1-1": "Returns the username column. The Laravel and Backpack default is ```email```.",
    "2-0": "```backpack_users_have_email()```",
    "2-1": "Tests that the ```email``` column exists on the users table and returns true/false.",
    "3-0": "```backpack_avatar($user)```",
    "3-1": "Receives a user object and returns a path to an avatar image, according to the preferences in the config file (gravatar, placeholder or custom).",
    "4-0": "**```backpack_middleware()```** ",
    "4-1": "Returns the key for the admin middleware. Default is ```admin```.",
    "5-0": "```backpack_guard_name()```",
    "5-1": "Returns the guard used for Backpack authentication.",
    "6-0": "**```backpack_auth()```**",
    "6-1": "Returns the Auth facade, using the current Backpack guard. Basically a shorthand for ```\\Auth::guard(backpack_guard_name())```.",
    "7-0": "```backpack_user()```",
    "7-1": "Returns the current Backpack user, if logged in. Basically a shorthand for ```\\Auth::guard(backpack_guard_name())->user()```"
  },
  "cols": 2,
  "rows": 8
}
[/block]