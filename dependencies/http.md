---
description: >-
  Wrapper around Nyholm\Psr7 library with a few helper methods and a basic
  emitter. This is mostly used as a library for our other modules
---

# HTTP

 [![Open Source Love](https://badges.frapsoft.com/os/mit/mit.svg?v=102)](https://github.com/ellerbrock/open-source-badge/)![ ](https://img.shields.io/badge/PHPStan-level%208-brightgreen.svg?style=flat) ![ ](https://img.shields.io/badge/PHPUnit-PASSING-brightgreen.svg?style=flat) ![ ](https://img.shields.io/badge/PHCBF-WP_Extra-brightgreen.svg?style=flat)

### Why?

Throughout a few of our modules, we need to handle HTTP requests and responses. The WP\_HTTP\* classes are great, but PS7 compliant libraries have a lot more to offer.

So this small module acts as a wrapper for the **Nyholm\Psr7** and **Nyholm\Psr7Serve**r libraries and gives a few helper methods. You can easily create and emit either Response that extends **WP\_HTTP\_RESPONSE** or implements **ResponseInterface**

### Examples

#### Creates a WP\_HTTP\_Response

```php
use PinkCrab\HTTP\HTTP;
use PinkCrab\HTTP\HTTP_Helper;

$http = new HTTP();
$response = $http->wp_response(
    ['some_key'=>'some_value'], 
    200, 
    ['Content-Type' => 'application/json; charset=UTF-8;']
);

// OR 

$response = HTTP_Helper::wp_response(
    ['some_key'=>'some_value'], 
    200, 
    ['Content-Type' => 'application/json; charset=UTF-8;']
);

// Emit to client
$http->emit_response($response);
```

As both have the same signatures, you can interchange at will. Obviously, the PS7 Response has more functionality to fine-tune the response.

#### Creates a PS7 Response

```php
use PinkCrab\HTTP\HTTP;
use PinkCrab\HTTP\HTTP_Helper;

$http = new HTTP();
$response = $http->ps7_response(
    ['some_key'=>'some_value'], 
    200, 
    ['Content-Type' => 'application/json; charset=UTF-8;']
);

// OR 

$response = HTTP_Helper::response(
    ['some_key'=>'some_value'], 
    200, 
    ['Content-Type' => 'application/json; charset=UTF-8;']
);

// Emit to client
$http->emit_response($response);
```

#### Creates a PS7 Request

```php
use PinkCrab\HTTP\HTTP;
use PinkCrab\HTTP\HTTP_Helper;

$http = new HTTP();
$request = $http->psr7_request(
    'GET',
    'https://google.com'
);

// OR 

$request = HTTP_Helper::request(
    'GET',
    'https://google.com'
);
```

#### Get ServerRequest fromGlobals

Returns a populated instance of ServerRequestInterface.

```php
use PinkCrab\HTTP\HTTP;
use PinkCrab\HTTP\HTTP_Helper;

$server = (new HTTP())->request_from_globals();
// OR 
$server = HTTP_Helper::global_server_request();
```

#### Create Stream

The PSR7 HTTP objects work with streams for the body, you can wrap all scalars values which can cast to JSON in a stream.

```php
use PinkCrab\HTTP\HTTP;
use PinkCrab\HTTP\HTTP_Helper;

$stream = (new HTTP())->stream_from_scalar($data);
// OR 
$stream = HTTP_Helper::stream_from_scalar($data);
```

> The `(new HTTP())->create_stream_with_json()` has been marked as deprecated since 0.2.3. use `(new HTTP())->stream_from_scalar()` in its place.

