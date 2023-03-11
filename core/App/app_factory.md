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

## Registration Middleware

Many of the modules for Perique make use of their own `Registration_Middleware`. This allows for additional functionality to be added via the registration process.

The middleware can be added one of two ways, either by passing an instance of a class which is extends `Registration_Middleware`, or by passing the class name as a string.

```php
// As instance
(new App_Factory(__DIR__))
    ->registration_middleware( new Some_Middleware() )
    ->boot();

// As class name
(new App_Factory(__DIR__))
    ->registration_middleware( Some_Middleware::class )
    ->boot();
```

## Boot

The `boot` method will create a new instance of the App, and register all the services. It will then call the `boot` method on the App, which will run the registration process.

```php
(new App_Factory(__DIR__))
    ->boot();
```

## Methods

### `public function __construct( $base_path = null )`
> @param string $base_path The base path for the application.  
> @return void

### `public function get_base_path(): string`
> @return string Returns the defined base path.

### `public function set_view base_path( string $base_view_path ): App_Factory`
> @param string $base_view_path The base path for the views.  
> @return App_Factory Returns the App_Factory instance.

### `public function get_base_view_path(): string`
> @return string Returns the defined base view path.

### `public function default_setup( bool $default_di_rules = true ): App_Factory`
> @param bool $default_di_rules Whether to use the default DI rules.  
> @return App_Factory Returns the App_Factory instance.

### `public function di_rules( array $di_rules ): App_Factory`
> @param array $di_rules The DI rules to be used.  
> @return App_Factory Returns the App_Factory instance.

### `public function app_config( array $app_config ): App_Factory`
> @param array $app_config The app config to be used.  
> @return App_Factory Returns the App_Factory instance.

### `public function registration_classes( array $registration_classes ): App_Factory`
> @param array $registration_classes The registration classes to be used.  
> @return App_Factory Returns the App_Factory instance.  
> *@throws App_Initialization_Exception Code 3 If the registration class does not exist*.

### `public function registration_middleware( Registration_Middleware $middleware ): App_Factory`
> @param Registration_Middleware $middleware The middleware to be used.  
> @return App_Factory Returns the App_Factory instance.  
> *@throws App_Initialization_Exception Code 3 If the registration class does not exist.* 

### `public function construct_registration_middleware(string $middleware): App`
> @param string $middleware The middleware to be used.  
> @return App_Factory Returns the App_Factory instance.  
> *@throws App_Initialization_Exception Code 1 If DI container not registered*  
> *@throws App_Initialization_Exception Code 3 If the registration class does not exist.*  
> *@throws App_Initialization_Exception Code 9 If class doesn't create as middleware.*

### `public function boot(): App`
> @return App Returns the App instance.  

### `public function app(): App`
> @return App Returns the App instance (can be called before or after boot.)


### `public function with_wp_dice(bool $include_default_rules): App_Config`
> @param bool $include_default_rules Whether to include the default DI rules. (default FALSE)  
> @return App_Config Returns the App_Config instance.
>
> **Please note this method is being deprecated in favour of the `default_setup` method. Left in place for legacy projects**