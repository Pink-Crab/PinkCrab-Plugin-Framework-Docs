![logo](./Perique-Hook-Sub-Card.jpg "PinkCrab Perique Hook Subscriber")

# Perique Hook Subscriber

Creates a single subscriber for a hook, part of the PinkCrab Plugin Framework

[![Latest Stable Version](http://poser.pugx.org/pinkcrab/wp-hook-subscriber/v)](https://packagist.org/packages/pinkcrab/wp-hook-subscriber) [![Total Downloads](http://poser.pugx.org/pinkcrab/wp-hook-subscriber/downloads)](https://packagist.org/packages/pinkcrab/wp-hook-subscriber) [![Latest Unstable Version](http://poser.pugx.org/pinkcrab/wp-hook-subscriber/v/unstable)](https://packagist.org/packages/pinkcrab/wp-hook-subscriber) [![License](http://poser.pugx.org/pinkcrab/wp-hook-subscriber/license)](https://packagist.org/packages/pinkcrab/wp-hook-subscriber) [![PHP Version Require](http://poser.pugx.org/pinkcrab/wp-hook-subscriber/require/php)](https://packagist.org/packages/pinkcrab/wp-hook-subscriber)
![GitHub contributors](https://img.shields.io/github/contributors/Pink-Crab/Perique-Hook-Subscriber?label=Contributors)
![GitHub issues](https://img.shields.io/github/issues-raw/Pink-Crab/Perique-Hook-Subscriber)
[![WordPress 5.9 Test Suite [PHP7.2-8.1]](https://github.com/Pink-Crab/Perique-Hook-Subscriber/actions/workflows/WP_5_9.yaml/badge.svg)](https://github.com/Pink-Crab/Perique-Hook-Subscriber/actions/workflows/WP_5_9.yaml)
[![WordPress 6.0 Test Suite [PHP7.2-8.1]](https://github.com/Pink-Crab/Perique-Hook-Subscriber/actions/workflows/WP_6_0.yaml/badge.svg)](https://github.com/Pink-Crab/Perique-Hook-Subscriber/actions/workflows/WP_6_0.yaml)
[![WordPress 6.1 Test Suite [PHP7.2-8.2]](https://github.com/Pink-Crab/Perique-Hook-Subscriber/actions/workflows/WP_6_1.yaml/badge.svg)](https://github.com/Pink-Crab/Perique-Hook-Subscriber/actions/workflows/WP_6_1.yaml)
[![WP6.2 [PHP7.2-8.2] Tests](https://github.com/Pink-Crab/Perique-Hook-Subscriber/actions/workflows/WP_6_2.yaml/badge.svg)](https://github.com/Pink-Crab/Perique-Hook-Subscriber/actions/workflows/WP_6_2.yaml)
[![codecov](https://codecov.io/gh/Pink-Crab/Perique-Hook-Subscriber/branch/master/graph/badge.svg?token=EYM4QX2CQ9)](https://codecov.io/gh/Pink-Crab/Perique-Hook-Subscriber)
[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/Pink-Crab/Perique-Hook-Subscriber/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/Pink-Crab/Perique-Hook-Subscriber/?branch=master)
[![Maintainability](https://api.codeclimate.com/v1/badges/8ac18bb04673f4a0dfa4/maintainability)](https://codeclimate.com/github/Pink-Crab/Perique-Hook-Subscriber/maintainability)


## Requirements

Requires PinkCrab Perique Framework V2.0.*

## Installation

``` bash
$ composer require pinkcrab/wp-hook-subscriber
```

This module allows for the creation of single Hook Subscriptions for creating an interface with WordPress. Under the hood it still uses the same registration process, the PinkCrab framework is built on, but gives a clean abstraction for single calls.

Each class which extends the provided base class, will have its hook added to the loader on either the defined action or differed. Allowing full use of the DI container.

Due to the way the Loader registers hook calls, classes are instanced on the init hook. Which can be problematic for WooCommerce and other extendable plugins, where some globals are populated later. The Hook_Subscriber allows for late construction, so your callback will be created in the global scope at that time.

### None Deferred Subscriber

``` php
class On_Single_Hook extends Abstract_Hook_Subscription {

   /**
    * The hook to register the subscriber
    * @var string
    */
   protected ?string $hook = 'some_hook';

   /**
       * Some service
       * @param My_Service
       */
      protected $my_service;

   public function __construct( My_Service $my_service ) {
      $this->my_service = $my_service;
   }

   /**
    * Callback
    * @param mixed ...$args
    */
   public function execute( ...$args ): void {
      // Args are accessed in the order they are passed.
      // do_action('foo', 'first','second','third',.....);
      //$args[0] = first, $args[1] = second, $args[2] = third, .....

      if ( $args[0] === 'something' ) {
         $this->my_service->do_something( $args[1] );
      }
   }
}

// Would be called by
do_action('some_hook', 'something', ['some','data','to do','something']);
```

### Deferred Subscriber

``` php
class Deferred_Hook extends Abstract_Hook_Subscription {

   /**
    * The hook to register the subscriber
    * @var string
    */
   protected ?string $hook = 'some_hook';

   /**
    * Deferred hook to call
    *
    * @var string|null
    */
   protected ?string $deferred_hook = 'some_global_populated';

   /**
    * Our global data
    * @param Some_Global|null
    */
   protected $some_global;

   public function __construct() {
      global $some_global;
      $this->some_global = $some_global;
   }

   /**
    * Callback
    * @param mixed ...$args
    */
   public function execute( ...$args ): void {
      // Depends on a global which is set later than init.
      if ( $args[0] === 'something' && ! empty( $this->some_global ) ) {
         do_something( $this->some_global->some_property, $args[1] );
      }
   }
}
```

> Somewhere in another plugin or wp-core $some_global is populated, we can then hook in anytime from when thats created and our hook is actually called.

``` php
function acme_plugin_function(){
    global $some_global; // Currently empty/null
    $some_global = new Some_Global();

    do_action('some_global_populated', ['some', 'data']);
}  
```

> When some_global_populated is fired, a new instance of Deferred_Hook is created and the callback is registered. This gives us access to Some_Global no matter whenever some_global_populated(). We end up creating 2 instances of our deferred hooks, once on init to register the first call, then again on our deferred hook, for the actual hook call.


## Previous Versions

* For Perique V1.0.* use Version 1.0.*
* For Perique V0.4.* use Version 0.2.2
* For Perique V0.3.* use Version 0.2.1

## Changelog
* 2.0.0 - Drops support for PHP 7.2 & 7.3 and adds support for Perique V2.0.*
* 1.0.1 - Drops support for PHP 7.1, adds PHP8 support, updates all dependencies and adds 3rd party quality checks (Scrutinizer & CodeClimate)
* 1.0.0 - Now supports Perique and its move from Registerable to Hookable interface naming.
* **---- Core renamed from PinkCrab Plugin Framework to Perique ----**
* 0.2.2 Updated tests and code to reflect changes in Framework 0.4.*
* 0.2.1 Added in a extra tests and coverage reports.
* 0.2.0 - Moved from the initial Event_Hook naming and made a few minor changes to how deferred hooks are added, using DI to recreate an new instance, over resetting state.
