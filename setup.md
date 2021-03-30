---
description: Required and options files
---

# Setup \(Files\)

### Bootstrap.php

Main bootstrap file, this can be given any name, so long as its called from your plugin.php or functions.php file.

```php
// @file - bootstrap.php

<?php

declare(strict_types=1);

/**
 * Used to bootload the application.
 *
 * @package Your Plugin
 * @author Awesome Devs <awesome.devs@rock.com>
 * @since 1.2.3
 */

use Dice\Dice;
use PinkCrab\Core\Application\App;
use PinkCrab\Core\Services\Dice\WP_Dice;
use PinkCrab\Core\Application\App_Config;
use PinkCrab\Core\Services\Registration\Loader;
use PinkCrab\Core\Services\ServiceContainer\Container;
use PinkCrab\Core\Services\Registration\Register_Loader;

// Populate Config with settings, if file exists.
$settings = file_exists( 'config/settings.php' )
    ? require 'config/settings.php'
    : array();
$config   = new App_Config( $settings );

// Load hook loader, DI & container.
$loader    = Loader::boot();
$di        = WP_Dice::constructWith( new Dice() );
$container = new Container();

// Setup the service container .
$container->set( 'di', $di );
$container->set( 'config', $config );

// Boot the app.
$app = App::init( $container );

// Add all DI rules and register the actions from loader.
add_action(
    'init',
    function () use ( $loader, $app, $config ) {

        // If the dependencies file exists, add rules.
        if ( file_exists( 'config/dependencies.php' ) ) {
            $dependencies = include 'config/dependencies.php';
            $app->get( 'di' )->addRules( $dependencies );
        }

        // Add all registerable objects to loader, if file exists.
        if ( file_exists( 'config/registration.php' ) ) {
            $registerables = include 'config/registration.php';
            Register_Loader::initalise( $app, $registerables, $loader );
        }

        // You can hook in with the $loader here to add any other setup hook calls.

        // Initalise all registerable classes.
        $loader->register_hooks();
    },
    1
);
```

### composer.json

This is an example of the composer.json file used in our boilerplate. If you do not plan on running any tests \(BUT YOU SHOULD!!!!\), you can remove the require-dev packags and the script listings as they will not be needed.

```javascript
{
    "name": "##PACKAGE_NAME##",
    "type": "library",
    "description": "##DESCRIPTION##",
    "keywords": [],
    "homepage": "https://pinkcrab.co.uk",
    "license": "MIT",
    "authors": [{
        "name": "Glynn Quelch",
        "email": "glynn.quelch@pinkcrab.co.uk",
        "homepage": "http://pinkcrab.co.uk",
        "role": "Developer"
    }],
    "autoload": {
        "psr-4": {
            "PinkCrab\\Test_Plugin\\": "src",
            "PinkCrab\\WP\\": "wp"
        },
        "files": []
    },
    "minimum-stability": "dev",
    "prefer-stable": true,
    "autoload-dev": {
        "psr-4": {
            "PinkCrab\\##NAMESPACE##\\Tests\\": "tests/"
        }
    },
    "repositories": [{
            "url": "https://github.com/Pink-Crab/PHP_Unut_Helpers.git",
            "type": "git"
        }
    ],
    "require-dev": {
        "phpunit/phpunit": "^7.0",
        "roots/wordpress": "^5.5",
        "wp-phpunit/wp-phpunit": "^5.0",
        "yoast/phpunit-polyfills": "^0.1.0",
        "symfony/var-dumper": "^5.0",
        "phpstan/phpstan": "^0.12.6",
        "szepeviktor/phpstan-wordpress": "^0.7.2",
        "php-stubs/wordpress-stubs": "^5.5.0",
        "pinkcrab/phpunit-helpers": "dev-master"
    },
    "require": {
        "php": ">=7.1.0",
        "pinkcrab/plugin-framework": "0.3.*"
    },
    "scripts": {
        "test": "phpunit",
        "analyse": "vendor/bin/phpstan analyse src -l8"
    }
}
```

If you are planning to give all of your vendor libraries custom namespaces using Php Scoper \(more details below\), to use the new mapped namespaces.

### plugin.php

Once you have your bootstrap file created, it's just a case of hooking it up in your plugin.php file.

```php
<?php
// @file plugin.php

/**
 * @wordpress-plugin
 * Plugin Name:     ##PLUGIN NAME##
 * Plugin URI:      ##YOUR URL##
 * Description:     ##YOUR PLUGIN DESC##
 * Version:         ##VERSION##
 * Author:          ##AUTHOR##
 * Author URI:      ##YOUR URL##
 * License:         GPL-2.0+
 * License URI:     http://www.gnu.org/licenses/gpl-2.0.txt
 * Text Domain:     ##TEXT DOMAIN##
 */

if ( ! defined( 'ABSPATH' ) ) {
 die;
}

require_once __DIR__ . '/vendor/autoload.php';
require_once __DIR__ . '/bootstrap.php';

// Optional activation hooks

use PinkCrab\WP\Activation;
use PinkCrab\WP\Deactivation;
use PinkCrab\Core\Application\App;

register_activation_hook( __FILE__, array( App::make( Activation::class ), 'activate' ) );
register_deactivation_hook( __FILE__, array( App::make( Deactivation::class ), 'deactivate' ) );
```

{% hint style="info" %}
If you using this in a theme, ensure the autoload.php and bootstrap.php files are added to the top of your functions.php file.
{% endhint %}

The framework requires 3 config files, these are usually placed in the /config directory but can be placed elsewhere. If you do use these elsewhere, please update the paths in the bootstrap.php file.

### config/dependencies.php 

```php
    <?php
    // @file config/dependencies.php

    /**
     * Handles all depenedency injection rules and config.
     *
     * @package Your Plugin
     * @author Awesome Devs <awesome.devs@rock.com>
     * @since 1.2.3
     */

    use PinkCrab\Core\Application\App;
    use PinkCrab\Core\Interfaces\Renderable;
    use PinkCrab\Core\Services\View\PHP_Engine;

    return array(
    // Gloabl Rules
    '*'         => array(
        'substitutions' => array(
            App::class        => App::get_instance(),
            Renderable::class => PHP_Engine::class,
        ),
    ),

    // Use wpdb as an injectable object.
    wpdb::class => array(
        'shared'          => true,
        'constructParams' => array( \DB_USER, \DB_PASSWORD, \DB_NAME, \DB_HOST ),
    ),

    /** ADD YOUR CUSTOM RULES HERE */
);
```

### config/registration.php

```php
// @file config/registration.php
<?php

declare(strict_types=1);

/**
 * Holds all classes which are to be loaded on initalisation.
 *
 * @package Your Plugin
 * @author Awesome Devs <awesome.devs@rock.com>
 * @since 1.2.3
 */

return array(
  /** Include all your classes which implemenet Registerable here */
);
```

### config/settings.php

```php
    // @file config/settings.php
    <?php

    declare(strict_types=1);

    /**
     * Handles all the data used by App_Config
     *
     * @package Your Plugin
     * @author Awesome Devs <awesome.devs@rock.com>
     * @since 1.2.3
     */

    // Get the path of the plugin base.
    $base_path  = \dirname( __DIR__, 1 );
    $plugin_dir = \basename( $base_path );
    $wp_uploads = \wp_upload_dir();

    return array(
        'additional' => array(
            // Register your custom config data.
        ),

    );
```

## Optional Plugin Activation 

If you wish to fire the activation/deactivation and/or uninstall hooks, you can use these 3 wrapper classes. They are currently in a seperate directory and will be to be added to the composer.json namespace list.

```javascript
    "autoload": {
        "psr-4": {
            "PinkCrab\\Test_Plugin\\": "src",
            "PinkCrab\\WP\\": "wp" <-- ADD THIS
        },
        "files": []
    },
```

{% hint style="info" %}
You can have these inside your src/ directory and part of the main namespace, just remeber to alter the use statments in plugin.php
{% endhint %}

### wp/Activation.php

```php
<?php

declare(strict_types=1);
/**
 * Actiation hook event.
 *
 * @author Glynn Quelch <glynn.quelch@gmail.com>
 * @license http://www.opensource.org/licenses/mit-license.html  MIT License
 * @package PinkCrab\WP
 */

namespace PinkCrab\WP;

use PinkCrab\WP\Uninstalled;
use PinkCrab\Core\Application\App;

class Activation {

	/**
	 * Entry point for action hook call.
	 *
	 * @return void
	 */
	public function activate() {
		// Register unistall hook.
		register_uninstall_hook( __FILE__, array( App::make( Uninstalled::class ), 'uninstall' ) );
	}
}

```

### wp/Deactivation.php

```php
<?php

declare(strict_types=1);

/**
 * Deactivate hook event.
 *
 * @author Glynn Quelch <glynn.quelch@gmail.com>
 * @license http://www.opensource.org/licenses/mit-license.html  MIT License
 * @package PinkCrab\WP
 */

namespace PinkCrab\WP;

class Deactivation {

	/**
	 * Entry point for the deactivation hook.
	 *
	 * @return void
	 */
	public function deactivate() {
	}
}

```

### Unintalled.php

```php
<?php

declare(strict_types=1);

/**
 * Uninstall hook event.
 *
 * @author Glynn Quelch <glynn.quelch@gmail.com>
 * @license http://www.opensource.org/licenses/mit-license.html  MIT License
 * @package PinkCrab\
 */

namespace PinkCrab\WP;

class Uninstalled {

	/**
	 * Entry points for the uninstall hook call.
	 *
	 * @return void
	 */
	public function uninstall() {
	}
}

```

