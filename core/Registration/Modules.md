---
title: Modules
---

# Modules

It is possible to add additional functionality to your application. Modules have access to the internal boot process of the application, this allows modules to chnage and enhance the internal operations of the application.

In addition to having access to the App, Modules can also add additional Registration Middleware. This allows you to add additional functionality to the registration process.

## Module Registration & Config

The most basic modules just need to be included with the application before it is booted. This can be done using the App_Factory or the App directly.

```php
// Using App Factory
(new App_Factory(__DIR__))
   ->default_setup()
   ->module('Some/Project/Module')
   ->boot();

// Directly to App
$app = new App();
$app->push_module('Some/Project/Module');
$app->boot();
```

*With config*

Some modules allow for additional configuration. This can be done uby passing a callback to the second argument of the `module` method.

```php
(new App_Factory(__DIR__))
   ->module('Some/Project/Module', function(Some_Project_Module $module){
      $module->some_setting(42);
      return $module;
   })
   ->boot();
```

*If the module comes with middleware the callback get passed this as the second parameter.*

```php
(new App_Factory(__DIR__))
   ->module('Some/Project/Module', function(Some_Project_Module $module, $middleware){
      $module->some_setting(42);
      $middleware->config('Apple');
      return $module;
   })
   ->boot();
```
> Please ensure you return the $module from the callback.  

## Registration Middleware

Modules can also add additional Registration Middleware. This allows you to add additional functionality to the registration process.

Usually if a module contains middleware it will need no additional setup, just adding a compatible class to the registration class list will be enough.

```php
// If this is a modules middleware, it will have every class in the registration list passed to it.
class Foo_Middleware{
   public function process($class){
      if($class instanceof SomeClassInterface){
         $class->some_method();
      }
   }
}

$registration_list = [ 
   SomeClass::class, // Implements SomeClassInterface
   OtherClass::class // Does not implement SomeClassInterface 
];
```

In the example above, `SomeClass` would be constructed by the DI Container and its `some_method()` method would be called. `OtherClass` would be constructed by the DI Container, but its `some_method()` method would not be called.

## Creating a custom module

If you wish to create your own module, that can be used in any application, you will need at least a main module file, this must implement the `PinkCrab\Perique\Interfaces\Module` interface.

As all instance of `Module` are constructed via the `DI_Container`, you can have dependencies injected into your module.

Required fields.

### get_middleware

> @return class-string\<Middleware_Registration\>\|null

```php
public function get_middleware(): ?string;
```

Returning `NULL` will denote that there is no middleware for this module. If you return a class name, this class must implement the `PinkCrab\Perique\Interfaces\Middleware_Registration` interface and will be constructed via the DI Container. So can have dependencies injected.

### pre_boot

> @param App_Config    $config    Access to the App_Config object.  
> @param Hook_Loader   $loader    Access to the Hook_Loader object for adding hook calls.   
> @param DI_Container  $container Access to the DI_Container object for constructing objects.  
> @return void  

```php
public function pre_boot( App_Config $config, Hook_Loader $loader, DI_Container $container ): void;
```

This method is called before the application is booted on the `init` hook. This is the first point where you can add additional functionality to the application. This is called after all `Registration` middleware has been created and added to the app.

### pre_registration

> @param App_Config    $config    Access to the App_Config object.  
> @param Hook_Loader   $loader    Access to the Hook_Loader object for adding hook calls.   
> @param DI_Container  $container Access to the DI_Container object for constructing objects.  
> @return void  

```php
public function pre_registration( App_Config $config, Hook_Loader $loader, DI_Container $container ): void;
```

This method is called before the registration process is run, so no classes have been registered yet. This is called after all `Registration` middleware has been created and added to the app.

### post_registration

> @param App_Config    $config    Access to the App_Config object.  
> @param Hook_Loader   $loader    Access to the Hook_Loader object for adding hook calls.   
> @param DI_Container  $container Access to the DI_Container object for constructing objects.  
> @return void  

```php
public function post_registration( App_Config $config, Hook_Loader $loader, DI_Container $container ): void;
```

This is called after the registration process has been run, so all classes have been registered. But before the `Hook_Loader` has been run. This is called after all `Registration` middleware has been created and added to the app.

## Creating a custom Registration Middleware

If you wish to create your own middleware, that can be used in any application, you will need at least a main middleware file, this must implement the `PinkCrab\Perique\Interfaces\Middleware_Registration` interface.

As all instance of `Middleware_Registration` are constructed via the `DI_Container`, you can have dependencies injected into your middleware. 

> You are able to use the `Inject_Hook_Loader`, `Inject_DI_Container` and `Inject_App_Config` interfaces with there matching `Inject_Hook_Loader_Aware` and `Inject_DI_Container_Aware` traits to inject the `Hook_Loader`, `DI_Container` and `App_Config` objects into your middleware at runtime.

Required fields.

### process

> @param object $class The class to be processed by the middleware.  
> @return object  

```php
public function process( object $class ): object;
```

This is the main method of the middleware, it is called for every class in the registration list. The middleware should check if the class is compatible with its functionality, and if so, process it. The middleware should return the class, so it can be passed to the next middleware.

### setup

> @return void  

```php
public function setup(): void;
```

This method is called before the list of classes are passed to the `process()` method of the Middleware. This is useful for constructing any objects that are required for the middleware to function via the DI Container.

### tear_down

> @return void  

```php
public function tear_down(): void;
```

This method is called after the list of classes have been passed to the `process()` method of the Middleware. This is useful for destructing any objects that are required for the middleware to function via the DI Container.

## Example of a Custom Module with Registration Middleware

```php
<?php

class Foo_Module implements Module {

   public function get_middleware(): ?string {
     return Foo_Middleware::class;
   }

   public function pre_boot( App_Config $config, Hook_Loader $loader, DI_Container $container ): void {
     $container->addRule(
       SomeClass::class,
       [
         'shared' => true,
         'constructParams' => [ $config->additional('some_setting')]
       ]
     );
   }

   public function pre_registration( App_Config $config, Hook_Loader $loader, DI_Container $container ): void {
     // noop
   }

   public function post_registration( App_Config $config, Hook_Loader $loader, DI_Container $container ): void {
     $loader->action('some_hook', [$container->create(SomeClass::class), 'finalise']);
   }
}
```

```php
<?php
class Foo_Middleware implements Middleware_Registration, Inject_DI_Container {
   use Inject_DI_Container_Aware;

   private SomeClass $some_class;

   public function process( object $class ): object {
     if($class instanceof SomeClassInterface){
      $class->some_method($this->some_class);
     }
     return $class;
   }

   public function setup(): void {
     $this->some_class = $this->di_container->create(SomeClass::class);
   }

   public function tear_down(): void {
     // noop
   }
}
```

> `Inject_DI_Container_Aware` populates `$this->di_container` with `DI_Container` at runtime.