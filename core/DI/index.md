{% include DI-sections.md %}

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
Once this class is added to the `config/registration.php` file, it will be constructed with an instance of `Some_Service` passed in. Then a callback for the `some_action` hook will be created and added to the `Hook_Loader` which will be run when the hook is triggered.

### Dependencies of Dependencies

The DI container will also resolve dependencies of dependencies, so you can inject as many dependencies as you like. This is useful for creating complex dependency trees, which can be easily mocked for testing.

```php
class Some_Service {

    protected $another_service;

    public function __construct(Another_Service $another_service){
        $this->another_service = $another_service;
    }

    public function do_something(): void {
        $this->another_service->do_something_else();
    }
}
```
*Which itself has a dependency...*

```php

class Another_Service {

    protected $wpdb;
    protected $config;

    public function __construct(wpdb $wpdb, App_Config $config){
        $this->wpdb = $wpdb;
        $this->config = $config;
    }

    public function do_something_else(): void {
        return $this->wpdb->get_results($this->config->additional('some_query'));
    }
}
```
While this example is a bit contrived, it shows how you can create complex dependency trees. This is useful for creating reusable classes, which can be easily mocked for testing.

## Working with Interfaces

While the above example works great for concrete classes, it can be a bit more tricky when working with interfaces. This is because the DI container cannot infer the implementation from the type hint. To get around this, you will need to [define a custom rule](Rules) for the container.

```php
interface Foo_Interface {
    public function do_something(): void;
}

// Implementation 1 of Foo_Interface
class Foo_Implementation_1 implements Foo_Interface {
    public function do_something(): void {
        echo 'Do something from Foo_Implementation_1';
    }
}

// Implementation 2 of Foo_Interface
class Foo_Implementation_2 implements Foo_Interface {
    public function do_something(): void {
        echo 'Do something from Foo_Implementation_2';
    }
}

```

It is now possible to explicitly define which implementation of the interface should be used. This can be done in the `config/registration.php` file, or in a custom rule.

```php
// Rules.
return [
    Class_With_Foo1::class => [
        'substitutions' => [
            Foo_Interface::class => Foo_Implementation_1::class
        ]
    ],
    Class_With_Foo2::class => [
        'substitutions' => [
            Foo_Interface::class => Foo_Implementation_2::class
        ]
    ]
];
```
Now we can create two classes, which both have a dependency on `Foo_Interface`, but will use different implementations.

```php
class Hookable_Foo_1 implements Hookable {

    protected $foo;

    public function __construct(Class_With_Foo1 $foo){
        $this->foo = $foo;
    }

    public function register(Hook_Hook_Loader $loader): void{
        $loader->action('some_action', [$this, 'my_callback']);
    }

    public function my_callback(): void {
        $this->foo->do_something();
    }
}
```

## Static Usage

You can also access the DI container at any time, this is useful for quick calls or for when you want to create objects and have them cached/shared. While this is easier than injecting (especially if you already have a complex constructor), it can lead to messy, coupled code.

```php
$instance_1 = App::make(Class_With_Foo1::class);
$instance_2 = App::make(Class_With_Foo2::class);

$instance_1->do_something(); // Will output 'Do something from Foo_Implementation_1'
$instance_2->do_something(); // Will output 'Do something from Foo_Implementation_2'
```

This can even be used as part of the registration process, to create objects using the container.

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

You can add a range of rule types, more details can be found on the [Rules page](Rules).

There is a number of `Injectable` interfaces and pre populated dependencies which can be used to inject to your classes. More details can be found on the [Existing Definitions page](Existing-Definitions).
