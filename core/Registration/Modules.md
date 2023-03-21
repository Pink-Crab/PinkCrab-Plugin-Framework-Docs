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