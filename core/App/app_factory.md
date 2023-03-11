# App Factory 

{% include setup-sections.md %}

The App_Factory is used to create an instance of Perique, with a series of defined values. 

## Using the factory

```php
// Your main plugin file (plugin.php)
/**
 * @wordpress-plugin
 * Plugin Name: Achme Plugin
 * Version: 1.3.4
 */
require_once __DIR__ . '/vendor/autoload.php';

(new App_Factory(__DIR__))
    ->default_setup( )
    ->boot();
```

> In the above example we are defining the base path of the plugin, and using the default config. This will create a new instance of the App, and register all the default services.

## Base Path

You can pass the base path as the main argument to the factory, this allows you to use relative paths in your config. Not passing a path to the constructor will default to the directory of which the factory is called from.

```php
// Your main plugin file (plugin.php)
 (new App_Factory())
    ->default_setup()
    ->boot();
```

## Base View Path

By default the view path is set as `views` in the base path. You can change this by calling `set_base_view_path` on the factory. This should be called before using the `default_setup` method.

```php
(new App_Factory(__DIR__))
    ->set_base_view_path(__DIR__ . '/templates')
    ->default_setup( )
    ->boot();
```

## Default Setup

By calling the `default_setup()` method on the factory, you will get the following setup:  
* The Hook Loader Service  
* DI Container Service (Powered by DICE)  
* Default DI Rules (View) __passing `FALSE` to this method will disable default DI Rules__  
* Registration Service  
* Registration Middleware Service  

> If no view path is set, View will make use of `{base_path}/views` as the template route.

## DI Rules

All of you custom DI rules will need defining, this allows you to use Interfaces and Abstract Classes as dependencies. 

```php
(new App_Factory(__DIR__))
    ->di_rules( require __DIR__ . '/config/dependencies.php' )
    ->boot();
```
> This can be called inline as an array, but we suggest using a separate file to define your DI rules. [See the example](setup#dependencies)

## App Config

All of the custom App_Config can be added to the App using the `app_config` method. This will be passed to the App_Config class, and can be accessed using the `config` method on the App.

```php
(new App_Factory(__DIR__))
    ->app_config( require __DIR__ . '/config/settings.php' )
    ->boot();
```
> This can be called inline as an array, but we suggest using a separate file to define your app config. [See the example](setup#app-config)

## Registration Classes

All of the custom Registration classes can be added to the App using the `registration_classes` method. This will be passed to the Registration class, and can be accessed using the `registration` method on the App.

```php
(new App_Factory(__DIR__))
    ->registration_classes( require __DIR__ . '/config/registration.php' )
    ->boot();
```

> This can be called inline as an array, but we suggest using a separate file to define your registration classes. [See the example](setup#registration)

