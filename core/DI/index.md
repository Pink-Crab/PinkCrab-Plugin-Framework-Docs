# Dependency Injection

For a more detailed set of documentation on how to use DICE please visit the [Level2 GitHub](https://github.com/Level-2/Dice/) or on [Tom Butler \(Authors\) website](https://r.je/dice). 

At its core the Perique Plugin Framework makes use of the DI container and the Registration process, to register hooks and connect into WordPress. Through using the DI container, you can build complex dependency trees and ensure your code is not coupled and highly testable.

## Basic Usage

The primary way to use the DI container, is via the Registration process. All classes which are passed to the registration process are created and cached using the container. This means you can just inject all of your dependencies and use them in your callbacks.

```php
class Do_Something_Using_DI implements Hookable {

    protected $some_service;

    /** Some_Service will be auto injected by the container */
    public function __construct(Some_Service $some_service){
        $this->some_service = $some_service;
    }

    public function register(Hook_Hook_Loader $loader): void{
        $loader->action('some_action', [$this, 'my_callback']);
    }

    public function my_callback(): void {
        // Now you can access the some_service in this callback.
        $this->some_service->do_something();
    }
}
```
Once this class is added to the ```config/registration.php``` file, it will be constructed and the action will be registered as a wp hook.

## Static Usage

You can also access the DI container at any time, this is useful for quick calls or for when you want to create objects and have them cached/shared. While this is easier than injecting (especially if you already have a complex constructor), it can lead to messy, coupled code.

```php
class Do_Something_Without_DI implements Hookable {

    public function register(Hook_Hook_Loader $loader): void{
        $loader->action('some_action', [$this, 'my_callback']);
    }

    public function my_callback(): void {
        // Call this using the App helper (statically)
        $some_service = App::make(Some_Service::class);
        $some_service->do_something();
    }
}
```

## Using Interfaces

Where DI really comes into its own is using `Interfaces`, this allows you to write code where implementations are are not tightly bound. For an example of this, please see the [Message Example](examples/message_example).

When using interfaces as dependencies, a custom rule will have to be defined, as the implementation can be not be inferred through type hints. There are a few ways to define these rules, for a more detailed explanation, see the [Rules page](Rules)

```php
class Example_With_Interface_Dependency{
    public function __construct(Foo_Interface $foo){
        $this->foo = $foo;
    }
}
```

### Global (fallback) Rule

We can easily set a global rule for this interface, this will allow us to define which class or instance we want to inject whenever we request the class. 

```php
// @file config/dependencies.php

return array(
    '*' => array(
        'substitutions' => array(
            // Either by class name to be constructed by container
            Foo_Interface::class => Some_Class_That_Implements_Foo::class,
            // Or as an instance.
            Foo_Interface::class => (new Foo('with', 'some'))->setup('required'),
        ),
    )
)
```

### Class by Class