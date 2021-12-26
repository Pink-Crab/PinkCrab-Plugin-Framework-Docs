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
    Foo_Interface::class => array(
        'instanceOf' => Some_Class_That_Implements_Foo::class,
    ),
)
```

Now whenever any class that has `Foo_Interface` as a dependency, will be passed whatever `substitution` rule we have defined.

### Class by Class

Having a single implementation might be useful for sending emails and logging events within your application, it doesn't really give you that much granular control. You are able to define [rules](Rules) for specific classes, to allow specific implementations on a class by class basis. 

```php
class Class_A{
    public function __construct(Cache $foo){
        $this->foo = $foo;
    }
}

class Class_B{
    public function __construct(Cache $foo){
        $this->foo = $foo;
    }
}
```
If we wanted to use different caching methods here we have 2 options.

**Define all instances**
```php
// @file config/dependencies.php

return array(
    Class_A::class => array(
        'substitutions' => array(
            Cache::class => DB_Cache::class,
        ),
    ),
    Class_B::class => array(
        'substitutions' => array(
            Cache::class => File_Cache::class,
        ),
    ),
)
```
> Now whenever we inject `Class_A` as a dependency, we will get `DB_Cache` and for `Class_B` we will get `File_Cache`. 
 
Of course any other dependency we create will need to be defined as above, which while giving us a very verbose representation of our dependencies, its no ideal for development.

**Using a global fallback**
To get around having to define every unique implementation, we can use a mix of global and class definitions. To do this, we just need to define our fallback as a global and any class which doesnt have a custom rule, will use this.

```php
// @file config/dependencies.php

return array(
    // Unless otherwise defined, use Fallback Cache
    Cache::class => array(
        'instanceOf' => Fallback_Cache::class,
    ),
    Class_A::class => array(
        // When we inject Class A, use DB Cache for Cache (not Fallback Cache)
        'substitutions' => array(
            Cache::class => DB_Cache::class,
        ),
    ),
)
```
> Now whenever we inject `Class_A` as a dependency, we will get `DB_Cache` and for `Class_B` or any future class that has `Cache` as a dependency we will get `Fallback_Cache`. 

## Using Abstract Classes

Injecting dependencies that are `Abstract` Classes works exactly the same as `Interfaces`. You can use a mix or either/or `Global` or `Class by Class` Rules

