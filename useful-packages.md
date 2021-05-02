# Additional Packages

While these packages are not extensions for the plugin framework, they are packages which are mostly built to support it. They can be used in any composer driven WordPress codebase.
****
## Enqueue

This is a simple, fluent wrapper for wp_enqueue_scripts and wp_enqueue_style (inc admin versions)
```php
// Enqueue a script
Enqueue::script('My_Script')
    ->src('https://url.tld/wp-content/plugins/my_plugn/assets/js/my-script.js')
    ->deps('jquery')
    ->lastest_version()
    ->register();

// Enqueue a stylesheet
Enqueue::style('My_Stylesheet')
    ->src('https://url.tld/wp-content/plugins/my_plugn/assets/css/my-stylesheet.css')
    ->media('all and (min-width: 1200px)')
    ->lastest_version()
    ->register();
```
> Dependency for: **Registerables**  
> [View on github](https://github.com/Pink-Crab/Enqueue)  
> **How to install**  
> $ `composer require pinkcrab/enqueue`  

****

## HTTP
Wrapper around Nyholm\Psr7 library with a few helper methods and a basic emitter. For use in WordPress during ajax calls. Is really useful when you want to inject the global server request to a controller.

> Can be used to create both PS7 and core WP; **Request**, **Responses**

```php

class My_Controller {

    protected ServerRequestInterface $request;

    public function __construct (ServerRequestInterface $request){
        $this->request = $request;
    }
}

// DI rule definition in populate interface with current request using Nyholm\Psr7
[
    ServerRequestInterface::class => HTTP_Helper::global_server_request(),
];
```
> Dependency for: **Registerables**  
> [View on github](https://github.com/Pink-Crab/HTTP)  
> **How to install**  
> $ `composer require pinkcrab/http`  

****

## Memoize
This little trait allows the caching of objects within a class, using the Lazy Memoize approach. 

```php
class Something{
    use PinkCrab\Memoize\Memoizable;

    /**
     * Either returns from cache is arguments already calculated
     * or calcuates if not set.
     */
    public function calcualte(int $val1, int $val2, int $val3): int{
        return $this->memoize(
            $this->generateHash($val1, $val2, $val3),
            fn() => $this->someting_expensive($val1, $val2, $val3)
        );
    }

    /**
     * Not called directly, but via a callable.
     */
    private function someting_expensive(int $val1, int $val2, int $val3): int{
        return ($val1 * $val2) - $val3; // "expensive"
    }
}

$something = new Something();
$something->calcualte(1,3,5); // Has to calculate
$something->calcualte(8,9,12); // Has to calculate
$something->calcualte(8,9,12); // From cache as already called.
```
> [View on github](https://github.com/Pink-Crab/Memoize)  
> **How to install**  
> $ `composer require pinkcrab/memoize-trait`

****

## Table Builder
Makes the definition and creation of database tables in WordPress cleaner and without the hassle of dbDelta.

```php
// Define a tables schema
$schema = new Schema('my_table', function(Schema $schema){
    // Set columns
    $schema->column('id')->unsigned_int(11)->auto_increment();
    $schema->column('user')->int(11);
    
    // Set indexes.
    $schema->index('id')->primary();
    $schema->index('user')->unique();
});

// Create table builder useing the dbDelta engine.
$builder = new Builder( new DB_Delta_Engine( $wpdb ) );

// Create or drop table(s) accordinly.
$builder->create_table($schema);
$builder->drop_table($schema);

```
> Dependency for: **WP_DB_Migration**  
> [View on github](https://github.com/Pink-Crab/WPDB-Table-Builder)  
> **How to install**  
> $ `composer require pinkcrab/pinkcrab/table_builder`  


****

## WP_DB_Migration
Allows for the creation of basic database migrations in line with pluign and theme lifecycles. Makes use of the **PinkCrab Table Builder** for easy creation of tables and indexes, which is extended to handle any database types.

```php
// Create a migration definition.
class Foo_Migration extends Database_Migration {

    // Define the tables name.
    protected $table_name = 'foo_table';

    // Define the tables schema
    public function schema( Schema $schema_config ): void {
        $schema_config->column('id')->unsigned_int(12)->auto_increment()
        $schema_config->index('id')->primary();

        $schema_config->column('column1')->text()->nullable();
        $schema_config->column('column2')->text()->nullable();
    }

    // Add all data to be seeded 
    public function seed( array $seeds ): array {
        $seeds[] = [
            'column1' => 'seed1-1',
            'column2' => 'seed1-2',
        ];

        $seeds[] = [
            'column1' => 'seed2-1',
            'column2' => 'seed2-2',
        ];
        
        return $seeds;
    }
}

// Create migration manager for standard wpdb/dbDelta and include all migrations.
$manager = PinkCrab\DB_Migration\Factory::manager_with_db_delta('migration_log_key');
$manager->add_migration(new Foo_Migration());

// Operations
$manager->create_tables('skip_table');
$manager->seed_tables('skip_table');
$manager->drop_tables('skip_table');
```
> [View on github](https://github.com/Pink-Crab/WP_DB_Migration)  
> **How to install**  *includes WPDB_Table_Builder as a dependency*  
> $ `composer require pinkcrab/wp-db-migrations`  
****

## WP Nonce
A simple object based wrapper for wp_nonce.

```php
$nonce = new Nonce('my_none_key');

$nonce->token(); // To get the current nonce token
$nonce->nonce_field('nonce_field'); // Returns HTML for hidden text field, with passed argument as 'name'
$nonce->validate($_POST['nonce']); // To validate (true/false)
$url = $nonce->as_url('http://www.url.com', 'my_nonce'); // To add to url : http://www.url.com?my_nonce={nonce_value}
$nonce->admin_referer('my_nonce'); // To validate in url (true/false)
```
> [View on github](https://github.com/Pink-Crab/Nonce)  
> **How to install**   
> $ `composer require pinkcrab/wp-nonce`  
****

## WP PSR16 Simple Cache

A file and transient implementation of the PSR16 Simple Cache interface. 
```php

// Cache isntances.
$file_cache = new File_Cache('path/to/cache/file/directory');
$transient_cache = new Transient_Cache('my_prefix');

// Single Operations
$cache->set('my_key', ['some', 'data'], 24 * HOURS_IN_SECONDS);
$cache->get( 'my_key', [/*empty*/]);
$cache->has( 'my_key' );
$cahe->delete( 'my_key' );

// Multiple Operations
$cache->setMultiple( ['key1' => 'Value1', 'key2' => 42], 1 * HOURS_IN_SECONDS );
$cache->getMultiple( ['key1', 'key2'], [/*empty*/] );
$cache->deleteMultiple( ['key1', 'key2'] );
```
> As they use the SimpleCache interface, they can be injected

```php 
class Something{
    protected CacheInterface $cache;
    
    public function __construct(CacheInterface $cache){
        $this->cache = $cache;
    }
}

// Defining which implementation to use (example using Dice)
return [
    '*' => [
        'subsitutions' => [
            CacheInterface::class = new File_Cache('path/to/cache/file/directory'),
            // CacheInterface::class = new Transient_Cache('my_prefix'),
        ]
    ]
]
```
> [View on github](https://github.com/Pink-Crab/WP_PSR16_Cache)  
> **How to install**   
> $ `composer require pinkcrab/wp-psr16-cache`  
****

