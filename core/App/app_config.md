---
title: App Config
parent: Setup
---


# App\_Config

{% include setup-sections.md %}


WordPress development involves lots of keys, slugs, namespaces and paths, lots of them. App_Config gives an injectable container for holding your keys.

{: .bg-blue-000 .text-grey-lt-000 .p-2 .rounded .mb-4 .text-sm .font-mono  .leading-normal}
> Both the **plugin** and **view** paths/urls are defined when the App is setup, they can be overwritten in the settings.php file, but this will not effect internal operations of the plugin. These ideally should be only be configured using the **App_Factory**


## Example Setup

Sets a custom set of asset and view paths/urls. And sets various aliases which you can use to set actual Keys and Slugs used controlled in a single location.

```php
// file - config/settings.php

// Base path and urls
$base_path  = dirname( __DIR__, 1 );
$plugin_dir = basename( $base_path );

// Need to get the current table prefix
global $wpdb;

return array(
    'path' => array(
        'assets'   => $base_path . '/build/assets',
    ),
    'url' => array(
        'assets'   => plugins_url($plugin_dir) . '/build/assets',
    ),
    'post_types' => array(
        'alias' => 'verbose_value'
    ),
    'taxonomies' => array(
        'alias' => 'verbose_value'
    ),
    'meta' => array(
        App_Config::POST_META => array(
            'alias' => 'verbose_value'
        ),
        App_Config::USER_META => array(
            'alias' => 'verbose_value'
        ),
        App_Config::TERM_META => array(
            'alias' => 'verbose_value'
        ),
    ),
    'plugin' => array(
        'version' => SOME_CONSTANT_FOR_PLUGIN_VERSION
    ),
    'db_tables' => array(
        'alias' => "{$wpdb->prefix}verbose_table_name"
    ),
    'namespaces' => array(
        'rest'  => 'pinkcrab/boilerplate',
        'cache' => 'pinkcrab_boilerplate',
    ),
    'additional' => array(
        'alias' => 'verbose_value'
    )
);

```

> If you do not wish to change any of the path or url details, you can remove them from your settings.php file, as they will be added.

## Paths

The values set by default \(plugin, view, assets, upload\_root & upload\_current\) are the only values that can be called. Any additional keys added, will be stripped, use Additional for any extra values.

### Default Values
* `$path['plugin'] = {$base_path}`
* `$path['view'] = {$base_path} . '/views`
* `$path['assets'] = {$base_path} . '/assets`
* `$path['upload_root'] = \wp_upload_dir()['basedir']`
* `$path['upload_current'] = \wp_upload_dir()['path']`

Paths can either be retrieved as a full array or by key.

```php
/**
 * Gets a path with trailing slash.
 *
 * @param string|null $path
 * @return array<string, mixed>|string|null
 */
public function path( ?string $path = null )
```

* If called with no arguments `path()`Will return the paths array. 
* If called with any of the defined keys `path('view')` will return the full path

```php
// Via Dependency Injection 
class Foo {
    protected $config;
    public function __construct(App_Config $config){
        $this->config = $config;
    }
    public function something(){
        $this->config->path(); // all paths in array
        $this->config->path('plugin'); // "/path/to/wp-content/plugins/my-plugin/"
        $this->config->path('upload_root'); // "/path/to/wp-content/uploads/"
    }
}

// Via Config Proxy Class
Config::path(); // all paths in array
Config::path('plugin'); // "/path/to/wp-content/plugins/my-plugin/"
Config::path('upload_root'); // "/path/to/wp-content/uploads/"

// Via App Helper
App::config('path'); // all paths in array
App::config('path','plugin'); // "/path/to/wp-content/plugins/my-plugin/"
App::config('path','upload_root'); // "/path/to/wp-content/uploads/"

```

## URLs

The values set by default \(plugin, view, assets, upload\_root & upload\_current\) are the only values that can be called. Any additional keys added, will be stripped, use Additional for any extra values.

### Default Values
* `$url['plugin'] = {$base_url}`
* `$url['view'] = {$base_url} . '/views`
* `$url['assets'] = {$base_url} . '/assets`
* `$url['upload_root'] = \wp_upload_dir()['baseurl']`
* `$url['upload_current'] = \wp_upload_dir()['url']`

Paths can either be retrieved as a full array or by key.

```php
/**
 * Gets a path with trailing slash.
 *
 * @param string|null $path
 * @return array<string, mixed>|string|null
 */
public function url( ?string $path = null )
```

* If called with no arguments `url()`, will return the paths array. 
* If called with any of the defined keys `url('plugin')` will return the full path

```php
// Via Dependency Injection 
class Foo {
    protected $config;
    public function __construct(App_Config $config){
        $this->config = $config;
    }
    public function something(){
        $this->config->url(); // all urls in array
        $this->config->url('assets'); // "https://url.com/wp-content/plugins/my-plugin/assets/"
        $this->config->url('view'); // "https://url.com/wp-content/plugins/my-plugin/views/"
    }
}

// Via Config Proxy Class
Config::url(); // all urls in array
Config::url('assets'); // "https://url.com/wp-content/plugins/my-plugin/assets/"
Config::url('view'); // "https://url.com/wp-content/plugins/my-plugin/views/"

// Via App Helper
App::config('url'); // all urls in array
App::config('url','assets'); // "https://url.com/wp-content/plugins/my-plugin/assets/"
App::config('url','view'); // "https://url.com/wp-content/plugins/my-plugin/views/"
```

> Returns null if the key passed doesn't exist.

## Namespaces

Out of the box only cache and rest are defined \(and come with helper methods\), if not defined will be set as **rest = pinkcrab** and **cache = pc\_cache.** As many additional key & value pairs can be added and accessed using the `namespace( $key )` method.

```php
/**
 * Return a namespace by its key.
 *
 * @param string $key
 * @return string|null
 */
public function namespace( string $key ): ?string
```

Can only be called with a key, but will just return null if the key is not defined.

```php
// file - config/settings.php
return array(
    ....
    'namespaces' => array(
        'rest'        => 'my_plugin',
        'cache'       => 'file_cache',
        'some_prefix' => 'dcv_'
    ),
);

// Usage

// Via Dependency Injection 
class Foo {
    protected $config;
    public function __construct(App_Config $config){
        $this->config = $config;
    }
    public function something(){
        $this->config->namespace('rest'); // "my_plugin"
        $this->config->namespace('some_prefix'); // "dcv_"
    }
}

// Via Config Proxy Class
Config::namespace('rest'); // "my_plugin"
Config::namespace('some_prefix'); // "dcv_"

// Via App Helper
App::config('namespace','rest'); // "my_plugin"
App::config('namespace','some_prefix'); // "dcv_"
```

### cache\(\) & rest\(\) helpers

Both cache and rest have helpers, these require no arguments and can be called as.

```php
// Via Dependency Injection 
class Foo {
    protected $config;
    public function __construct(App_Config $config){
        $this->config = $config;
    }
    public function something(){
        $this->config->rest(); // "my_plugin"
        $this->config->cache(); // "file_cache"
    }
}

// Via Config Proxy Class
Config::rest(); // "my_plugin"
Config::cache(); // "file_cache"

// Via App Helper
App::config('rest'); // "my_plugin"
App::config('cache'); // "file_cache"
```

## DB\_Tables

A simple key =&gt; value set can be added for database tables, allowing for dynamic values. As many values can be added and called out via their key. 

```php
/**
 * Returns a table name based on its key.
 *
 * @param string $name
 * @return string
 * @throws OutOfBoundsException
 */
public function db_tables( string $name ): string
```

If a key is called which is not defined, it will throw and OutOfBoundsException.

```php
// file - config/settings.php
return array(
    ....
    'db_tables' => array(
        'cache'     => 'my_plugin_cache',
        'email_log' => 'my_plugin_email_log',
    ),
);

// Usage  Via Dependency Injection 
 class Foo {
    protected $config;
    public function __constuct(App_Config $config){
        $this->config = $config;
    }
    public function something(){
        $this->config->db_tables('cache'); // "my_plugin_cache"
        $this->config->db_tables('email_log'); // "my_plugin_email_log"
    }
}

// Via Config Proxy Class
Config::db_tables('cache'); // "my_plugin_cache"
Config::db_tables('email_log'); // "my_plugin_email_log"

// Via App Helper
App::config('db_tables','cache'); // "my_plugin_cache"
App::config('db_tables','email_log'); // "my_plugin_email_log"
```

## Post Types

**Post Types** are added as a key value pair for the post types slug.

```php
return [
    ....
    'post_types' => [
        'order' => 'acme_plugin_order',
        'product' => 'acme_plugin_product'
    ],
    ....
];
```

Now anywhere in our code, we can access these values using the post\_type\(\) 

```php
/**
 * Returns the key for a post type.
 *
 * @param string $key
 * @return string
 * @throws OutOfBoundsException
 */
public function post_types( string $key ): string
```

Whatever key you set your slug, will be the key used to access it. 


```php
// Via Dependency Injection
class Foo {
    protected $config;
    
    public function __constuct(App_Config $config){
        $this->config = $config;
    }
    
    public function something(){
        $this->config->post_type('product'); // "acme_plugin_product"
    }
}

// Via Config Proxy Class
Config::post_type('product'); // "acme_plugin_product"


// Via App Helper
App::config('post_type','product'); // "acme_plugin_product"
```

> OutOfBoundsException will be thrown if a post type is called, that doesnt exist.


## Taxonomies

**Taxonmies** are added as a key value pair for the taxonomy slug.
```php
return [
    ....
    'taxonmies' => [
        'order_type' => 'acme_plugin_order_type'
    ],
    ....
];
```

Now anywhere in our code, we can access these values using the taxonmies\(\) 

```php
/**
 * Returns keys for taxonomies.
 *
 * @param string $key
 * @return string
 * @throws OutOfBoundsException
 */
public function taxonomies( string $key ): string
```

Whatever key you set your slug and will be the key used to access it. 

```php
// Via Dependency Injection
class Foo {
    protected $config;
    
    public function __constuct(App_Config $config){
        $this->config = $config;
    }
    
    public function something(){
        $this->config->taxonomies('order_type'); // "acme_plugin_order_type"
    }
}

// Via Config Proxy Class
Config::taxonomies('order_type'); // "acme_plugin_order_type"

// Via App Helper
App::config('taxonomies','order_type'); // "acme_plugin_order_type" 
```

> OutOfBoundsException will be thrown if a taxonomy is called, that doesnt exist.

## Meta

It is possible to define post, term and user meta keys. This allows for the prefixing and shortenting keys.

### Post Meta
```php
return [
    ....
    'meta' => [
       'post' => [
           'foo' => 'acme_plugin_foo'
       ]
    ],
    ....
];
```
Now we have access to these value anywhere in our codebase (as above)

```php
/**
 * Return the verbose post meta key from an alias
 *
 * @param string $key
 * @return mixed
 * @throws OutOfBoundsException if key doesnt exist
 */
public function post_meta( string $key )
```


```php
// Via Dependency Injection
class Foo {
    protected $config;
    
    public function __constuct(App_Config $config){
        $this->config = $config;
    }
    
    public function something($post_id){
        return get_post_meta($post_id, $this->config->post_meta('foo'), true);
    }
}

// Via Config Proxy Class
Config::post_meta('foo'); // "acme_plugin_foo"

// Via App Helper
App::config('post_meta','foo'); // "acme_plugin_foo"
```
### Term Meta
```php
return [
    ....
    'meta' => [
       'term' => [
           'bar' => 'acme_plugin_bar'
       ]
    ],
    ....
];
```
Now we have access to these value anywhere in our codebase (as above)

```php
/**
 * Returns a term meta key 
 *
 * @param string $key
 * @return string
 * @throws OutOfBoundsException if key doesnt exist
 */
public function term_meta( string $key )
```


```php
// Via Dependency Injection
class Foo {
    protected $config;
    
    public function __constuct(App_Config $config){
        $this->config = $config;
    }
    
    public function something($term_id){
        return get_term_meta($term_id, $this->config->term_meta('bar'), true);
    }
}

// Via Config Proxy Class
Config::term_meta('bar'); // "acme_plugin_bar"

// Via App Helper
App::config('term_meta','bar'); // "acme_plugin_bar"
```

### User Meta
```php
return [
    ....
    'meta' => [
       'user' => [
           'baz' => 'acme_plugin_baz'
       ]
    ],
    ....
];
```
Now we have access to these value anywhere in our codebase (as above)

```php
/**
 * Returns a user meta key 
 *
 * @param string $key
 * @return string
 * @throws OutOfBoundsException if key doesnt exist
 */
public function user_meta( string $key )
```


```php
// Via Dependency Injection
class Foo {
    protected $config;
    
    public function __constuct(App_Config $config){
        $this->config = $config;
    }
    
    public function something($user_id){
        return get_user_meta($user_id, $this->config->user_meta('baz'), true);
    }
}

// Via Config Proxy Class
Config::user_meta('baz'); // "acme_plugin_baz"

// Via App Helper
App::config('user_meta','baz'); // "acme_plugin_baz"
```
### Costants
There are 3 constants that can be used when defining meta data. These constants are used when retriving the value, so using these constants avoids the risk of mistyping.

```php
return [
    'meta' => array(
        App_Config::POST_META => array(
            'alias' => 'verbose_value'
        ),
        App_Config::USER_META => array(
            'alias' => 'verbose_value'
		),
        App_Config::TERM_META => array(
            'alias' => 'verbose_value'
		),
	),
];
```

## Additional 

The Additional group allows for creating any custom set of values. This allows for creating custom values which you otherwise would use constants for. Each value is added as key => value pair to the config/settings.php file.

```php
return [
    ....
    'additional' => [
       'key1' => 'value1',
       'key2' => 'value2',
    ],
    ....
];
```
Now we have access to these value anywhere in our codebase (as above)

```php
/**
 * Return a additional by its key.
 *
 * @param string $key
 * @return mixed
 */
public function additional( string $key )
```

* Calling $app_config->additional('key1'); // 'value1'
* Calling $app_config->key1; // 'value' *Please note this only works with unique keys which are not **paths**, **namespaces**, **plugin**, **taxonomies**, **post_types**, **meta**, **db_table** or **additional***

```php
// Via Dependency Injection
class Foo {
    protected $config;
    
    public function __constuct(App_Config $config){
        $this->config = $config;
    }
    
    public function something(){
        $data = get_transient( $this->config->additional('key1'), $this->config->key2 );
    }
}

// Via Config Proxy Class
Config::additional('key1'); // "value1"

// Via App Helper
App::config('additional','key2'); // "value2"

// Via magic __get() method.
$app_config->key; // "value1"
 
```

## Plugin 

This allows the setting of the plugin details to the app config. Version will default to 0.1.0 if not set, and wpdb_prefix is automatically set based on the `$GLOBAL` wpdb instance.

*At present only version can be accessed, this will be extended in later versions*

```php
return [
    ....
    'plugin'     => array(
        'version' => is_array( $plugin_data ) && array_key_exists( 'Version', $plugin_data )
            ? $plugin_data['Version'] 
            : '0.1.0', // Set this as a fallback.
    ),
    ....
];
```
Now we have access to these value anywhere in our codebase (as above)

```php
/**
 * Returns the current set plugin version
 *
 * @return string
 */
public function version(): string
```

* Calling $app_config->version(); // '0.2.6'

```php
// Via Dependency Injection
class Foo {
    protected $config;
    
    public function __constuct(App_Config $config){
        $this->config = $config;
    }
    
    public function something(){
        wp_enqueue_script('handle', 'src', ['jquery'], $this->config->version(), true);
    }
}

// Via Config Proxy Class
Config::version(); // '0.2.6'

// Via App Helper
App::config('version'); // '0.2.6'
 
```

```php
/**
 * Returns the wpdb prefix
 *
 * @return string
 */
public function wpdb_prefix(): string
```

* Calling $app_config->wpdb_prefix(); // 'wp_'

```php
// Via Dependency Injection
class Foo {
    protected $config;
    
    public function __constuct(App_Config $config){
        $this->config = $config;
    }
    
    public function something(){
        $wpdb->query("SELECT * FROM {$this->config->wpdb_prefix()}posts");
    }
}

// Via Config Proxy Class
Config::wpdb_prefix(); // 'wp_'

// Via App Helper
App::config('wpdb_prefix'); // 'wp_'
 
```
