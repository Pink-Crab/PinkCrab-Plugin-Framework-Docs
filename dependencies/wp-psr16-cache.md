---
description: Provides a WP based implementation of the PSR16 (Simple)Cache
---

# WP PSR16 Cache

Comes in 2 flavours, File and Transient based caching options. Each comes with the means to prefix your cache to allow for simple and clean keys.

![ ](https://img.shields.io/badge/Current_Version-2.0.0-yellow.svg?style=flat) ![Open Source Love](https://badges.frapsoft.com/os/mit/mit.svg?v=102)![ ](https://img.shields.io/badge/PHPStan-level%208-brightgreen.svg?style=flat) ![ ](https://img.shields.io/badge/PHPUnit-PASSING-brightgreen.svg?style=flat) ![ ](https://img.shields.io/badge/PHPCS-WP_Extra-brightgreen.svg?style=flat)

### Requirements

Requires Composer and WordPress.

### Installation

```bash
$ composer require pinkcrab/wp-psr16-cache
```

### Getting Started

Once you have the package installed and your autoloader has been included.

```php
use PinkCrab\WP_PSR16_Cache\File_Cache;
use PinkCrab\WP_PSR16_Cache\Transient_Cache;

// FILE CACHE
// Creates directory at path passed, if it doesnt exist.
$cache = new File_Cache('path/to/dir');

// TRANSIENT CACHE 
// Created with optonal groups, adding a prefix to transient keys and set file extension.
$cache = new Transient_Cache('group_prefix', '.do' ); 

// Set single item to cache.
$cache->set( 'cache_key', $data, 24 * HOURS_IN_SECONDS );

// Gets the value, if not set or expired returns null
$cache->get( 'cache_key', 'fallback' );

// Returns if valid cache item exists.
$cache->has( 'cache_key' );

// Deletes a cache if it exists.
$cahe->delete( 'cache_key' );


// Set mutiple values, with a single expiry
$cache->setMultiple( ['key1' => 'Value1', 'key2' => 42], 1 * HOURS_IN_SECONDS );

// Get multiple values in a key => value array, with a shared default.
$cache->getMultiple( ['key1', 'key2'], 'FALLBACK' );

// Clears multiple keys.
$cache->deleteMultiple( ['key1', 'key2'] );

// Clear all cache items
$cache->clear();
```

### File\_Cache

> Will create the defined base directory when the object is created.

The constructor takes 2 properties the path and the file extension. By defualt the file extension is **.do**.

```php
$wp_uploads = wp_upload_dir();

$cache = new File_Cache($wp_uploads['basedir'] . '/my-cache', '.cache');

$cache->set('my_key', ['some', 'data']);

// Creates  /public/wp-content/uploads/my-cache/my_key.cache
```

If you plan on using this as a plugin and want to clean up after an install. You can just create an instance of the Class on activation and then run clear on uninstall

```php
/** 
 * Creates the cache directory
 */
function called_on_activation(){
    new File_Cache($wp_uploads['basedir'] . '/my-cache', '.cache');
}
/**
 * Clears all values form the cache directory.
 * Please note doesnt delete the folder.
 */
function called_on_uninstall(){
    (new File_Cache($wp_uploads['basedir'] . '/my-cache', '.cache'))->clear();
}
```

### Transient Cache

> Makes use of prefixed/grouped transient values. Preventing collisions while still allowing short and clean keys.

The constructor takes a single argument, this denotes the group that your transients will be created using. This can be omitted if you wanted no prefix on your keys.

```php
$cache = new Transient_Cache('my_cache');

$cache->set('my_key', ['some', 'data']);

// Will create a transient which can be recalled using either;
$value = get_transient('my_cache_my_key');
(new Transient_Cache('my_cache'))->get('my_key');
```

> PLEASE NOTE: Calling clear\(\) will use $wpdb to get all transients from the database and clear any which start with your prefix. If you have no prefix defined, this could clear all of your transients and create some unusual side effected.
>
> ALSO: Some mangaged hosts store transients outside of the regular Options table. This can lead to problems when fetching all transients with your prefix.

### Use with the PinkCrab Framework

You can easily inject your cache object as a constructor dependency.

```php
// file - config/dependecnies.php

return [
  // Creates a default implementation as Transient_Cache
	Transient_Cache::class  => array(
		'constructParams' => array( 'cache_prefix' ),
	),

	// Unless otherwise stated this will alway inject the standard rules (above)
	CacheInterface::class   => array(
		'instanceOf' => Transient_Cache::class,
		'shared'     => true,
		'inherit'    => true,
	),

	// Ensures the cache instance passed into SomeOtherSerive will
	// be custom File_Cache instance.
	SomeOtherService::class => array(
		'substitutions' => array(
			CacheInterface::class => new File_Cache( 'some/path' ),
		),
	),
		
		.......
];
```

Now we can just inject the _CacheInterface_ type hint and get **Transient\_Cache** for that instance. Unless we call this from the _SomeOtherService_, in which case we will get **File\_Cache**

```php
class SomeService{
  /**
  * @param Psr\SimpleCache\CacheInterface
  */
  protected $cache;

  public function __construct( CacheInterface $cache ) {
    $this->cache = $cache;
  }
}    
```

{% hint style="info" %}
In SomeService we would have access to transient cache with a prefix of _**cache\_prefix**_
{% endhint %}

```php
class SomeOtherService{
  /**
  * @param Psr\SimpleCache\CacheInterface
  */
  protected $cache;

  public function __construct( CacheInterface $cache ) {
    $this->cache = $cache;
  }
}    
```

{% hint style="info" %}
In SomeOtherService we would have access to our file cache in _**'some/path'**_
{% endhint %}

