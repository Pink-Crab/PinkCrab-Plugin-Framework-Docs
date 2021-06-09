# Perique Setup

> ### REQUIRES COMPOSER

If you are planning to roll your own setup over using of the boilerplate packages we have. You can either use composer to install the core package using  

>`$ composer require pinkcrab/perique-framework-core`

Alternatively you can download files from [github](..)  
Once you have the base package in place, its just a case of running `$ composer install`

## App Initialisation

You have 2 options when it comes to booting the application, you can either use the provided factory, or by building the application manually.

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

( new App_Factory() )->with_wp_dice( true )
	->di_rules( require __DIR__ . '/config/dependencies.php' )
	->app_config( require __DIR__ . '/config/settings.php' )
	->registration_classes( require __DIR__ . '/config/registration.php' )
	->boot();
```
> In the above example, we are using separate files to define our configurations. But these can be replaced with just array if you wish *(but will get messy really quickly if you do)*

### Manual Setup
```php
// Your main plugin file (plugin.php)
/**
 * @wordpress-plugin
 * Plugin Name: Achme Plugin
 * Version: 1.3.4
 */

use PinkCrab\Core\Application\App_Factory;

$app = new App();
$loader = new Hook_Loader();
$container = new PinkCrab_Dice( new Dice() );

// Set the container
$container->add_rules( require __DIR__ . '/config/dependencies.php' );
$app->set_container( $container );


