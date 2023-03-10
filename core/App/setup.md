# Perique Setup

*Pages*
* [Setup](setup)
* [App_Config](app_config)

> ### REQUIRES COMPOSER

If you are planning to roll your own setup over using of the boilerplate packages we have. You can either use composer to install the core package using  

>`$ composer require pinkcrab/perique-framework-core`

Alternatively you can download files from [github](..)  
Once you have the base package in place, its just a case of running `$ composer install`

## App Initialisation

While you can construct the App Manually, we suggest using the Factory. If you want to create the App manually, please see the App_Factory source code or the test suites.

### Using the factory

```php
// Your main plugin file (plugin.php)
/**
 * @wordpress-plugin
 * Plugin Name: Achme Plugin
 * Version: 1.3.4
 */

use PinkCrab\Core\Application\App_Factory;

require_once __DIR__ . '/vendor/autoload.php';

( new App_Factory(__DIR__) )
    ->default_config( )
    ->di_rules( require __DIR__ . '/config/dependencies.php' )
    ->app_config( require __DIR__ . '/config/settings.php' )
    ->registration_classes( require __DIR__ . '/config/registration.php' )
    ->boot();
```
> In the above example, we are using separate files to define our configurations. But these can be replaced with just array if you wish *(but will get messy really quickly if you do)*


## Dependencies

All of you custom DI rules will need defining, this allows you to use Interfaces and Abstract Classes as dependencies. 
```php
// @file config/dependencies.php
<?php
/**
 * All custom rules for the DI Container.
 * See docs at https://perique.info/core/DI/
 */
return array(
    'Acme\Project\SomeInterface' array(
        'instanceOf' => Some_Class_That_Implements_Foo::class,
    ),
    Acme\Project\SomeOtherInterface::class => array(
        'substitutions' => array( Cache::class => DB_Cache::class ),
    ),
);
```
> When adding classes you can use `Class_Name::class`, so long as the full namespaced name is imported use `My\Namespace\Class_Name;`


## config/registration.php

```php
// @file config/registration.php
<?php

/**
 * List of classes passed through the registration service.
 * See docs at https://perique.info/core/Registration
 */

return array(
    'Acme\Project\SomeController',
    Acme\Project\SomeOtherController::class
);

```
>When adding classes you can use `Class_Name::class`, so long as the full namespaced name is imported `use My\Namespace\Class_Name;`

## config/settings.php

It is possible to define a range of values which can be accessed via the `App_Config` directory. This allows for the use of alias's for internal keys, paths for assets and namespaces for various applications. 

> For a full list of values which can be set, please see the [App_Config](app_config) details.


```php
    // @file config/settings.php
<?php

/**
 * Holds all custom app config values.
 * See docs at https://perique.info/core/App/app_config
 */

// Base path and urls
$base_path  = dirname( __DIR__, 1 );
$plugin_dir = basename( $base_path );

return array(
	'path'       => array(
		'assets'         => $base_path . '/assets',
	),
	'url'        => array(
		'assets'         => plugins_url( $plugin_dir ) . '/assets',
	),
	'post_types' => array(
		'events' => 'pc_prefix_events'
	),
	'plugin'     => array(
        'version' => '1.1.12'
	)
);
```
