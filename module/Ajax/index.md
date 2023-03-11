# Perique - Ajax

A simple but powerful Ajax library for the PinkCrab Perique framework. Allows for the creation of object based Ajax calls that handle all basic Nonce validation, WP Actions and makes use of the HTTP PSR Interfaces.

[![Latest Stable Version](http://poser.pugx.org/pinkcrab/ajax/v)](https://packagist.org/packages/pinkcrab/ajax) [![Total Downloads](http://poser.pugx.org/pinkcrab/ajax/downloads)](https://packagist.org/packages/pinkcrab/ajax) [![Latest Unstable Version](http://poser.pugx.org/pinkcrab/ajax/v/unstable)](https://packagist.org/packages/pinkcrab/ajax) [![License](http://poser.pugx.org/pinkcrab/ajax/license)](https://packagist.org/packages/pinkcrab/ajax) [![PHP Version Require](http://poser.pugx.org/pinkcrab/ajax/require/php)](https://packagist.org/packages/pinkcrab/ajax)
[![codecov](https://codecov.io/gh/Pink-Crab/Perique-Ajax/branch/master/graph/badge.svg?token=NEZOz6FsKK)](https://codecov.io/gh/Pink-Crab/Perique-Ajax)
[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/Pink-Crab/Perique-Ajax/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/Pink-Crab/Perique-Ajax/?branch=master)
[![WordPress 5.9 Test Suite [PHP7.2-8.1]](https://github.com/Pink-Crab/Perique-Ajax/actions/workflows/WP_5_9.yaml/badge.svg?branch=master)](https://github.com/Pink-Crab/Perique-Ajax/actions/workflows/WP_5_9.yaml)
[![WordPress 6.0 Test Suite [PHP7.2-8.1]](https://github.com/Pink-Crab/Perique-Ajax/actions/workflows/WP_6_0.yaml/badge.svg?branch=master)](https://github.com/Pink-Crab/Perique-Ajax/actions/workflows/WP_6_0.yaml)
[![WordPress 6.1 Test Suite [PHP7.2-8.1]](https://github.com/Pink-Crab/Perique-Ajax/actions/workflows/WP_6_1.yaml/badge.svg?branch=master)](https://github.com/Pink-Crab/Perique-Ajax/actions/workflows/WP_6_1.yaml)
[![Maintainability](https://api.codeclimate.com/v1/badges/7534ee9d3ab6a5785386/maintainability)](https://codeclimate.com/github/Pink-Crab/Perique-Ajax/maintainability)


****

## Why? ##
Writing Ajax scripts for WordPress can get messy really quickly, with the need to define up to 2 actions with a shared callback. The Perique Ajax Module makes use of the registration and dependency injection aspects of the framework. This allows for the injection of services into your callback, allowing for clean and testable code.

****

## Setup ##

>*Requires the PinkCrab Perique Framework and Composer*

**Install the Module using composer**
```bash 
$ composer require pinkcrab/ajax
```
**Include the custom Ajax Middleware**
```php
// file:plugin.php

// Include all DI rules
Ajax_Bootstrap::use();

// Boot the app as normal.
$app = ( new App_Factory )      
    ->with_wp_dice( true )
    ->boot();

// Include the custom middleware.
$app->construct_registration_middleware( Ajax_Middleware::class );
```
## Usage ##

**Create your Ajax Models**
```php
use PinkCrab\Ajax\Ajax;
use PinkCrab\Ajax\Dispatcher\Response_Factory;
use PinkCrab\Ajax\Ajax_Helper;
use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;

class My_Ajax extends Ajax {

    /**
     * Define the action to call.
     * @var string
     */
    protected $action = 'my_ajax_action';

    /**
     * The ajax calls nonce handle.
     * @var string
     */
    protected $nonce_handle = 'my_ajax_nonce';

    /** 
     * Some service which handles the logic of the call.
     * @var Some_Service 
     */
    protected $my_service;

    /**
     * Constructs the object
     * My_Service will be injected when this is created by the DI Container
     */
    public function __construct( Some_Service $my_service ) {
        $this->my_service = $my_service;
    }

    /**
     * The callback
     *
     * @param \Psr\Http\Message\ServerRequestInterface $request
     * @param \PinkCrab\Ajax\Dispatcher\Response_Factory $response_factory
     * @return \Psr\Http\Message\ResponseInterface
     */
    public function callback(
        ServerRequestInterface $request,
        Response_Factory $response_factory
    ): ResponseInterface {
        
        // Extract the args from the request, you can also do this manually
        $args = Ajax_Helper::extract_server_request_args( $request );

        // Do something with the request args, ideally in a service class
        $data_to_return = array_key_exists('foo', $args)
            ? $this->my_service->do_something($args['foo'])
            : 'Foo not found!';
        
        // Return with a valid PSR Response. 
        return $response_factory->success( $data_to_return );
    }
}

```
> This would have an ajax call with `my_ajax_action` action assigned. 

**Add all your Ajax Models to `registration.php`**
```php
// file:registration.php

return [
    ....
    My_Ajax_Call::class,
    ....
];
```

****

## Perique Ajax Documentation
* [Ajax Model](Ajax_Model)
* [Ajax Helper](Ajax_Helper)
* [Response Factory](Response_Factory)
* [Hooks](Hooks)
* [Examples](Examples)

***


## License ##

### MIT License ###
http://www.opensource.org/licenses/mit-license.html  

## Pre-Release ##

* For Perique 1.3.*, use version 1.0.4
* For Perique 1.0.* - 1.2.*, use version 1.0.3

## Change Log ##
* 1.1.0 - Bump support for Perique 1.4.0
* 1.0.4 - Update dev deps to wp6.1 and PinkCrab/HTTP 1.*, Drop Support for PHP 7.1
* 1.0.3 - Update dev deps, update GH Pipeline and improve conditional on checking if doing ajax.
* 1.0.2 - Added in Ajax_Bootstrap class with ::use() method, for simpler inclusion with Perique. Docs improved as part of Perique.info site
* 1.0.1 - Update yoast/phpunit-polyfills requirement from ^0.2.0 to ^0.2.0 || ^1.0.0 by @dependabot in #13
* 1.0.0 - Supports Perique 1.0.0 and includes checks to ensure only added when wp_ajax called
* 0.1.0 Extracted from the Registerables module. Now makes use of a custom Registration_Middleware service for dispatching all Ajax calls.
