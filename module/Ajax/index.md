<!-- pagenav[start] -->
## Perique Ajax Documentation
* [Ajax](index.md)
* [Ajax Model](Ajax_Model.md)
* [Ajax Helper](Ajax_Helper.md)
* [Response Factory](Response_Factory.md)
* [Hooks](Hooks.md)
* [Examples](Examples.md)

***
<!-- pagenav[end] -->
<!-- content[start] -->
# Perique - Ajax

A simple but powerful Ajax library for the PinkCrab Perique framework. Allows for the creation of object based Ajax calls that handle all basic Nonce validation, WP Actions and makes use of the HTTP PSR Interfaces.

![alt text](https://img.shields.io/badge/Current_Version-1.0.0-yellow.svg?style=flat " ") 
[![Open Source Love](https://badges.frapsoft.com/os/mit/mit.svg?v=102)]()
![](https://github.com/Pink-Crab/Perique-Ajax/workflows/GitHub_CI/badge.svg " ")
[![codecov](https://codecov.io/gh/Pink-Crab/Perique-Ajax/branch/master/graph/badge.svg?token=NEZOz6FsKK)](https://codecov.io/gh/Pink-Crab/Perique-Ajax)

## Version 1.0.0 ##

****

## Why? ##
Writing Ajax scripts for WordPress can get messy really quickly, with the need to define upto 2 actions with a shared callback. The Perique Ajax Module makes use of the registration and dependecny injection aspects of the framework. This allows for the injection of services into your callback, allowing for clean and testable code.

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
$app->construct_registration_middleware(
    Ajax_Middleware::class
);
```

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


## License ##

### MIT License ###
http://www.opensource.org/licenses/mit-license.html  

## Change Log ##
* 1.0.0 - Supports Perique 1.0.0 and includes checks to ensure only added when wp_ajax called
* 0.1.0 Extracted from the Registerables module. Now makes use of a custom Registration_Middleware service for dispatching all Ajax calls.
<!-- content[end] -->