# Perique Setup

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

( new App_Factory() )->with_wp_dice( true )
	->di_rules( require __DIR__ . '/config/dependencies.php' )
	->app_config( require __DIR__ . '/config/settings.php' )
	->registration_classes( require __DIR__ . '/config/registration.php' )
	->boot();
```
> In the above example, we are using separate files to define our configurations. But these can be replaced with just array if you wish *(but will get messy really quickly if you do)*


## Dependencies

All of you custom DI rules will need defining, this allows you to use Interfaces and Abstract Classes as dependencies. 
```php
// file::config 