# Operations Lifecycle 

--

<a name="about"></a>
## About

At important points in the CRUD Lifecycle, Backpack triggers what we call "lifecycle events". You can hook into those events - by registering custom code that will run when that lifecycle event happens. This allows you to customize the process, without having to override any of the core files for CRUD or an Operation.

For example, in a Backpack CRUD all routes are setup on the **CrudController** using methods like `setupModerateOperationRoutes()`. Before those methods are called, Backpack calls `LifecycleEvent::trigger('crud:before_all_route_setup')`. If you want to add your own code that runs there, you can do:

```php
LifecycleEvent::hookInto('crud:before_setup_routes', function($controller) {
    // do something before the routes are setup
});
```
<a name="hooks-usage"></a>
Here are all the general Lifecycle Events we currently have:

`crud:before_setup_routes` - before any operation routes are registered
`crud:after_setup_routes` - after all operation routes have been registered
`crud:before_setup_defaults` - before all defaults are setup
`crud:after_setup_defaults` - after all defaults have been setup
`crud:before_setup` - before any operation is set up
`crud:after_setup` - after that operation has been set up


In addition to the general Lifecycle events above, each operation can trigger its own lifecycle events. For example, here are the lifecycle events triggered by the Create operation:

`create:before_setup` - exposes parameters: $crud
`create:after_setup` - exposes parameters: $crud

You can hook into those events using a similar syntax to the general lifecycle events:

```php
LifecycleEvent::hookInto(['create:before_setup'], function() {
    $this->crud->addButton('top', 'create', 'view', 'crud::buttons.create');
});
```

Note that when using the hooks for specific operations, the hook is prefixed with the operation name followed by the hook name. This allow you to hook into specific operation events, or even to multiple events at the same time:

```php
LifecycleEvent::hookInto(['create:before_setup', 'list:before_setup'], function() {
    // do something before the create operation and the list operation are setup
});
```

<a name="how-to-add-your-own-hooks"></a>

As a developer you may have had the need to create custom operations, and while creating a "one time use" operation may not require/demand the usage of lifecycle events, creating a reusable operation that you may want to share with the community, or use in multiple projects, may benefit from the usage of lifecycle events to allow other developers to hook into your operation and customize its behavior.

You can add your own lifecycle events to your custom operations by calling the `LifecycleEvent::trigger()` method at the appropriate points in your operation. For example, if you have a custom operation that need to do something after some action happen in te operation, you can trigger a lifecycle event like this:

```php
public function moderate() {
    // do something to "moderate" the entry and register the hook
    LifecycleEvent::trigger('moderate:after_moderation', [
        'controller' => $this,
        'operation' => 'moderate',
    ]);
}
```

Then, other developers can hook into that event like this:

```php
LifecycleEvent::hookInto(['moderate:after_moderation'], function($controller, $operation) {
    // do something after the moderate operation has been executed
});
```

