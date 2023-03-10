---
title: Dependency Injection
parent: Perique Core
---

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

## 

## Built in Constructor Dependency Rules

Perique comes with a selection of predefined rules, which can be used out of the box, or changed by replacing the rules from within your own custom rules.

### App

You can inject access to the Perique instance, just pass App $app as a dependency and you will have access to the core App instance
```php
class Foo{
    public function __construct(App $app){
        /** @var PinkCrab\Perique\Application\App */
        $this->app = $app;
    }
}
```

### Container

If you need access to the DI Container, you can pass the `DI_Container` interface and you will be passed an instance of the container populated with all defined rules.
```php
class Foo{
    public function __construct(DI_Container $container){
        // Set container to use later
        $this->container = $container;
    }

    public function do_something($data){
        $thingy = $this->container->create(Thingy::class);
        $thingy->action($data);
    }
}
```
> The above is useful if you need to delay the creation of your class until its needed, rather than straight away. 

### WPDB

Lets be honest, no one like using globals, they make code unpredictable and can cause massive headaches when debugging. To not only avoid having to do `global $wpdb` every time we need access, but also make test mocking easier. You can pass the current global instance of WPDB to any class.
```php
class Foo{
    public function __construct(\wpdb $wpdb){
        /** @var wpdb */
        $this->wpdb = $wpdb;
    }
}
```

## Built in Method Dependency Rules

While these are more useful when writing modules for Perique, these can be used in projects, especially if you are writing your own reusable implementations that should have empty constructors.

### App_Config

If you would like to have access to App Config without passing it as a dependency, you can have your class implement the `Inject_App_Config` interface. This requires a single method `public function set_app_config( App_Config $app_config ): void;`

```php
abstract Abstract_Foo implements Inject_App_Config {
    
    protected App_Config $app_config
    
    public function set_app_config( App_Config $app_config ): void{
        $this->app_config = $app_config;
    }
    
    abstract public function needed_value():string;
    
    public function run(){
        do_something_with_assets(
            $this->app_config->url('assets'),
            $this->needed_value()
        );
    }
}

// Custom implementation
class Foo extends Abstract_Foo{
    protected Service $service;
    public function __construct(Service $service){
        $this->service = $service;
    }

    public function needed_value():string{
        return $this->service->get_value(
            // We of course also have access to App Config too.
            $this->app_config->additional('something')
        );
    }
}
```
> In the above example we didn't need to worry about remembering to pass `App_Config` and `Service` to `Foo` and then calling `parent::__construct($app_config)`

**Container Aware Trait**

We have a simple trait which can be used with this Interface to remove the need to manually add the method and properties.

```php
class Has_Config implements Inject_App_Config{
    use Inject_App_Config_Aware;
}
```
This adds `protected $app_config;` and populates using `public function set_app_config( App_Config $app_config ): void`

***

### DI_Container

You can inject the DI Container without the needing the constructor using the `Inject_DI_Container` interface which requires  `public function set_di_container( DI_Container $container ): void;`   


```php
abstract class Some_Group implements Inject_DI_Container {
    
    protected DI_Container $di_container
    // Array of built items
    protected array $items;

    public function set_di_container( DI_Container $di_container ): void{
        $this->di_container = $di_container;
    }

    abstract protected function items(): array;

    public function create_items(): void {
        $item_class_names = $this->items();

        foreach( $item_class_names as $item_class_name ) {
            $item = $this->di_container->create($item_class_name);
            $this->items[] = $item;
        }
    }

}

// Implementation
class My_Group extends Some_Group {

    protected My_Item_Repository $repository;

    public function __construct(My_Item_Repository $repository){
        $this->repository = $repository;
    }

    protected function items(): array{
        return $this->repository->where( 'foo', 'bar', '=' );
    }

}

// This allow sub child dependencies, access to the container for dependency hirarcy 
class My_Item {
    protected Translations $translations;
    public function __construct(Translations $translations){
        $this->translations = $translations;
    }
}
```

> The above example shows how you can use the container when creating services that allow access to the container with a empty constructor used only for services needed by each implementation. We use this frequently in our modules and as part of our Registration Middleware system.

**Container Aware Trait**

We have a simple trait which can be used with this Interface to remove the need to manually add the method and properties.

```php
class Has_Container implements Inject_DI_Container{
    use Inject_DI_Container_Aware;
}
```
This adds `protected $di_container;` and populates using `public function set_di_container( DI_Container $container ): void`
