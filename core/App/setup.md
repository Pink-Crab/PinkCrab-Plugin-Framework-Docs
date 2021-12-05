HI
# Setup \(Files\)

The framework on requires a few files to be in place, most of these are just for populating the App with its required details.

## composer.json

This is an example of the composer.json file used in our boilerplate. If you do not plan on running any tests \(BUT YOU SHOULD!!!!\), you can remove the require-dev packages and the script listings as they will not be needed.

```javascript
{
    "name": "##PACKAGE_NAME##",
    "type": "library",
    "description": "##DESCRIPTION##",
    "keywords": [],
    "homepage": "##YOUR URL##",
    "license": "MIT",
    "authors": [{
        "name": "##AUTHOR##",
        "email": "##YOUR EMAIL##",
        "homepage": "##YOUR URL##",
        "role": "Developer"
    }],
    "autoload": {
        "psr-4": {
            "##NAMESPACE##\\": "src"
        },
        "files": []
    },
    "autoload-dev": {
        "psr-4": {
            "##NAMESPACE##\\Tests\\": "tests"
        }
    },
    "require-dev": {
        "phpunit/phpunit": "^7.0",
        "roots/wordpress": "^5.5",
        "wp-phpunit/wp-phpunit": "^5.0",
        "symfony/var-dumper": "4.*",
        "phpstan/phpstan": "^0.12.6",
        "szepeviktor/phpstan-wordpress": "^0.7.2",
        "php-stubs/wordpress-stubs": "^5.6.0",
        "dealerdirect/phpcodesniffer-composer-installer": "*",
        "wp-coding-standards/wpcs": "*",
        "object-calisthenics/phpcs-calisthenics-rules": "*",
        "jetbrains/phpstorm-stubs": "dev-master",
        "kimhf/woocommerce-stubs": "^0.2.0",
        "kimhf/advanced-custom-fields-pro-stubs": "^5.9"
    },
    "require": {
        "php": ">=7.1.0",
        "pinkcrab/plugin-framework": "0.4.*"
    },
    "scripts": {
        "test": "phpunit --coverage-clover coverage.xml --testdox",
        "coverage": "phpunit --coverage-html coverage-report --testdox",
        "analyse": "vendor/bin/phpstan analyse src -l8",
        "sniff": "./vendor/bin/phpcs src/ -v",
        "all": "composer test && composer analyse && composer sniff"
    },
    "minimum-stability": "dev",
    "prefer-stable": true
}
```

If you are planning to prefix all of your namespaces with PHP-Scoper, please see the [PHP-Scoper docs](application/php-scoper.md)

## plugin.php

Once you have your bootstrap file created, it's just a case of hooking it up in your plugin.php file.

```php
<?php

/**
 * @wordpress-plugin
 * Plugin Name:     ##PLUGIN NAME##
 * Plugin URI:      ##YOUR URL##
 * Description:     ##DESCRIPTION##
 * Version:         ##VERSION##
 * Author:          ##AUTHOR##
 * Author URI:      ##YOUR URL##
 * License:         GPL-2.0+
 * License URI:     http://www.gnu.org/licenses/gpl-2.0.txt
 * TextDomain:      ##TEXT DOMAIN##
 */

use PinkCrab\Core\Application\App_Factory;

require_once __DIR__ . '/vendor/autoload.php';

( new App_Factory() )->with_wp_dice( true )
	->di_rules( require __DIR__ . '/config/dependencies.php' )
	->app_config( require __DIR__ . '/config/settings.php' )
	->registration_classes( require __DIR__ . '/config/registration.php' )
	->boot();
```

>If you using this in a theme, ensure these are added to the top of your functions.php file.

The framework requires 3 config files, these are usually placed in the /config directory but can be placed elsewhere. If you do use these elsewhere, please update the paths in the ```plugin.php``` file.

## config/dependencies.php 

Registers all custom rules for the DI Container which can be assumed from the types on the constructor.

```php
// @file config/dependencies.php
<?php

declare(strict_types=1);

/**
 * All custom rules for the DI Container.
 * See docs at https://perique.info/core/DI/
 */

return array();
```
?When adding classes you can use Class_Name::class, so long as the full namespaced name is imported ```use My\Namespace\Class_Name;```

## config/registration.php

```php
// @file config/registration.php
<?php

declare(strict_types=1);

/**
 * List of classes passed through the registration service.
 * See docs at https://perique.info/core/Registration
 */

return array(
	/** Include all your classes which implement Registerable here */
);

```
>When adding classes you can use Class_Name::class, so long as the full namespaced name is imported ```use My\Namespace\Class_Name;```

## config/settings.php

It is possible to define a range of values which can be accessed via the `App_Config` directory. This allows for the use of alias's for internal keys, paths for assets and namespaces for various applications. 

> For a full list of values which can be set, please see the [App_Config](app_config) details.


```php
    // @file config/settings.php
<?php

declare(strict_types=1);

/**
 * Holds all custom app config values.
 * See docs at https://perique.info/core/App/app_config
 */

// Base path and urls
$base_path  = \dirname( __DIR__, 1 );
$plugin_dir = \basename( $base_path );

return array(
	'path'       => array(
		'plugin'         => $base_path,
		'view'           => $base_path . '/views',
		'assets'         => $base_path . '/assets',
	),
	'url'        => array(
		'plugin'         => \plugins_url( $plugin_dir ),
		'view'           => \plugins_url( $plugin_dir ) . '/views',
		'assets'         => \plugins_url( $plugin_dir ) . '/assets',
	),
	'post_types' => array(
        'events' => 'pc_prefix_events'
    ),
	'plugin'     => '1.1.12',
	)
);
```
