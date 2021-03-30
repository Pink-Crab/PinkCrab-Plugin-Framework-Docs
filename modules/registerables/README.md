---
description: >-
  The Registerables modules brings a collection of Abstract and expandable
  classes, making registration of Post Types, Taxonomies and Ajax calls easy and
  clean thanks to the Registration process.
---

# Registerables

### Registerable Types

* Post Types
* Taxonomies
* Metaboxes
* WP\_Ajax Call

### Installation

```bash
composer require pinkcrab/registerables
```

If you are planning on using the Ajax abstracts, you will need to ensure that a valid PS7 ServerRequest is injected into your Ajax class. By default, the **Nyholm PS7** library is included and we have a helper method in our HTTP helper class. Just copy the example below into your dependencies file.

```php
// file config/dependencies.php

// Ajax Request Injection.
// Uses the PinkCrab HTTP helper to supply a Nyholm ServerRequest object.
// This can be swapped to [ServerRequest::fromGlobals] if you are using Guzzle
Ajax::class       => array(
    'constructParams' => array( HTTP_Helper::global_server_request() ),
    'shared'          => true,
    'inherit'         => true,
),
```

Alternatively, you can use Guzzle or any other HTTP library, so long as the instance passed in implements the PS7 ServerRequestInterface.

{% hint style="info" %}
Includes the following packages as dependencies 

PinkCrab/HTTP  
PinkCrab/Enqueue  
Nyholm/psr7  
Nyholm/psr7-server
{% endhint %}

### Examples

```php
// Creates a simple Post Type.

use PinkCrab\Registerables\Post_Type;

class Basic_CPT extends Post_Type {

	public $key      = 'basic_cpt';
	public $singular = 'Basic';
	public $plural   = 'Basics';
}
```

```php
// Creates a simple tag based taxonomy for posts.

use PinkCrab\Registerables\Taxonomy;

class Basic_Tag_Taxonomy extends Taxonomy {
	public $slug         = 'basic_tag_tax';
	public $singular     = 'Basic Tag Taxonomy';
	public $plural       = 'Basic Tag Taxonomies';
	public $description  = 'The Basic Tag Taxonomy.';
	public $hierarchical = false;
	public $object_type = array( 'post' );
}
```

```php
// Registers an ajax which uses PS7 Responses and validates
// its own nonce value. 
// Just handle the callback.

use PinkCrab\Registerables\Ajax;

class Simple_Ajax extends Ajax {
		// Nonce key
   	protected $nonce_handle = 'basic_ajax_get';
	
   	// WP Ajax action
   	protected $action = 'basic_ajax_get';

		// Handles the callback.
		public function callback( ResponseInterface $response ): ResponseInterface {
			$response_array = array( 'result' => 'Some data' );
			return $response->withBody(
				( new HTTP() )->create_stream_with_json( $response_array )
			);
		}
}
```

### Location and Namespace

The **Registerables** module must be installed in `Modules/Registerables` and uses the `PinkCrab\Modules\Registerables` namespace.

