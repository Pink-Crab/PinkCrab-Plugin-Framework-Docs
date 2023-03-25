---
title: Registration
---
<script src="https://cdnjs.cloudflare.com/ajax/libs/mermaid/8.0.0/mermaid.min.js"></script>
{% include registration-sections.md %}

# Registration

* [Modules](Modules)
* [Hookable Middleware](Hookable)

The Registration process is the main way to interact with the WordPress API's. When a class is added to the registration array, it is passed through a series of middlewares. These middlewares can be used to add additional functionality to the registration process.

Out of the box, Perique comes with the `Hookable` middleware. This allows for the registration of actions and filters, for any class which implements the `Hookable` interface.

## Registration Process

The order in which Perique is initialised is as follows:

1. Define Perique
2. Add Modules
3. Add Classes to the Registration Class list
4. Boot Application
5. *init hook call*
6. Modules and accompanying Registration Middleware are created
7. All classes are created and then passed through the Registration Middlewares

> As all modules and the classes are created after the the application has booted and `init` has been fired, this allows 3rd party plugins to add additional functionality to the registration process.

## Registration Class List

When the App is initialised, all classes which are listed in the Registration Array (found in `config/registration.php`) are created using the [DI Container](../DI/index) (allowing for the injection of dependencies) and passed through each defined Registaion Middlewares. This allows your Application to interact with the many WP API's.

```php 
// file config/registration.php
use My\Namespace\Foo;

return [
  Foo::class,
  \My\Namespace\Bar::class,
  'Full\Namespaced\Class\As\String'
];
```
> If you are planning to use the `::class` get the fully qualified class name. You must ensure you include the `use Class\Name;`.

## Hooks for 3rd Party Plugins

Additional functionality can be added using the following hooks:

* `Hooks::MODULE_MANAGER` - Allows for adding additional modules to the application.
* `Hooks::APP_INIT_REGISTRATION_CLASS_LIST` - Allows for adding additional classes to the registration list.

### Hooks::MODULE_MANAGER

This hook allows for the addition of additional modules to the application. This is useful if you wish to add additional functionality to the registration process.

```php
// file: acme-plugin.php
add_action( Hooks::MODULE_MANAGER, function( Module_Manager $module_manager ) {
   $module_manager->push_module( 
      Acme_Module::class, 
      function( Acme_Module $module ): Acme_Module{
         $module->config('something');
         return $module;
      }
   );
});
```
> For more details about adding modules, see the [Modules](Modules) section.

### Hooks::APP_INIT_REGISTRATION_CLASS_LIST

This hook allows for the addition of additional classes to the registration list. This is useful if you wish to add additional functionality to the registration process.

```php
// file: acme-plugin.php
add_filer( Hooks::APP_INIT_REGISTRATION_CLASS_LIST, function( array $classes ): array {
   $classes[] = Acme_Class::class;
   return $classes;
});
```

The registration process is the main entry point for the entire plugin. A list of classes are instaniated and then passed through multiple **Regisration Middlewares**. These combined with the Loader allow for subscription of actions and filters with WordPress. 
