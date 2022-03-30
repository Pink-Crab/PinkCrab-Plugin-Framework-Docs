# Perique - Migration

A wrapper around various PinkCrab libraries which make it easier to run DB migrations from a plugin created using the Perique Framework.

![alt text](https://img.shields.io/badge/Current_Version-0.1.0-yellow.svg?style=flat " ")
[![Open Source Love](https://badges.frapsoft.com/os/mit/mit.svg?v=102)]()
![](https://github.com/Pink-Crab/Perique-Route/workflows/GitHub_CI/badge.svg " ")
[![codecov](https://codecov.io/gh/Pink-Crab/Perique-Route/branch/master/graph/badge.svg?token=4yEceIaSFP)](https://codecov.io/gh/Pink-Crab/Perique-Route)
[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/Pink-Crab/Perique_Migrations/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/Pink-Crab/Perique_Migrations/?branch=master)

# Version 0.1.0-RC2

****

# Why?

There already exists a WPDB Migrations system written by PinkCrab for use in any WordPress plugin or even theme. However working this into Perique required building  a small little isolated workflow due to the nature of how the Perique Registration Process works and how WordPress handles plugin events such as Activation, Deactivation and Uninstalling.

So to make it more seamless adding Database Migrations to Perique, we have created this library to help.

## Depends on 

As mentioned this library acts more of a bridge for the following packages.

* [WP DB Migrations](https://github.com/Pink-Crab/WP_DB_Migration)
* [WPDB Table Builder](https://github.com/Pink-Crab/WPDB-Table-Builder)
* [Perique Plugin Life Cycle](https://github.com/Pink-Crab/Perique_Plugin_Life_Cycle)

****

# Setup

```bash
$ composer require pinkcrab/perique-migrations
```

### Creation of Migrations

To create database migrations, the `Migration` abstract class must be extended.

> [Full Migration model references below](#migration-model)
> [Example Plugin](https://github.com/gin0115/Perique_Migrations_Example)

```php
class Acme_Migration extends Migration {
    
    /**
     * Returns the name of the table.
     *
     * @required
     * @return string Table name
     */
    protected function table_name(): string {
        return 'acme_migration_sample_table';
    }

    /**
     * Defines the schema for the migration.
     *
     * @param Schema $schema_config
     * @return void
     */
    public function schema( Schema $schema_config ): void {
        $schema_config->column( 'id' )
            ->unsigned_int( 11 )
            ->auto_increment();
    
        $schema_config->column( 'user_ref' )
            ->text( 11 );
    
        $schema_config->column( 'thingy_ref' )
            ->int( 11 );
    
        $schema_config->index( 'id' )
            ->primary();
    }

    /**
     * Defines the data to be seeded.
     *
     * @param array<string, mixed> $seeds
     * @return array<string, mixed>
     */
    public function seed( array $seeds ): array {
        return [
            [
                'user_ref'   => 'ghjuyitjkuiy'
                'thingy_ref' => 1325546
            ],
            [
                'user_ref'   => 'eouroipewrjhiowe'
                'thingy_ref' => 897456
            ]
        ];
    }
}
```

> Example of table using [Perique App Config](https://perique.info/core/App/app_config) for table names.

```php
class Use_Dependency_Migration extends Migration {
    protected $config;
    public function __construct( App_Config $config ) {
        $this->config = $config;
    }
    protected function table_name(): string {
        return $this->config->db_tables('from_app_config');
    }    
}
```

### Create the Migrations service

The Migrations service is created using an instance of the Plugin Life Cycle service.  
[Read more about Plugin Life Cycle](https://github.com/Pink-Crab/Perique_Plugin_Life_Cycle)

```php
// @file plugin.php

// Boot the app as normal and create an instance of Plugin_State_Controller
$app = (new App_Factory())
    // Rest of Perique setup
    ->boot();
$plugin_state_controller = new Plugin_State_Controller($app);

// Create instance of the Migrations instance
$migrations = new Migrations(
    $plugin_state_controller,
    'acme_plugin_migrations' // Migration log key 
);

// Add our migrations
$migrations->add_migration(new Acme_Migration());

// These can also be added as class names, which will then be constructed via Perique's DI Container. 
// Please see the Plugin Life Cycle for details of timings and limitations.
$migrations->add_migration(Some_Migration_With_Dependencies::class);

// Once all migrations are added, the service need to be finalised.
$migrations->done();

// You can add any additional Plugin Life Cycle events, before finalising that also.
$plugin_state_controller->event( new Some_Event( ));
$plugin_state_controller->finalise();

```

## Migration Model

> All models must extend from the ( `abstract` ) `PinkCrab\Perique\Migration\Migration` class

```php
class DI_Migration extends Migration {

    /** Services used */
    protected Some_Service $some_service;
    protected App_Config $config;

    /** These would be injected automatically via Perique DI */
    public function __construct(Some_Service $some_service, App_Config $config){
        $this->some_service = $some_service;
        $this->config = $config;
    }

    /** Gets the table name from the App_Config (Perique Config) */
    protected function table_name(): string {
        return $this->config->db_tables('from_app_config');
    }  

    /**Defines the schema for the migration. */
    public function schema( Schema $schema_config ): void {
        $schema_config->column( 'id' )->unsigned_int( 11 )->auto_increment();
        $schema_config->index( 'id' )->primary();    
        $schema_config->column( 'user_ref' )->text( 11 );
        $schema_config->column( 'thingy_ref' )->int( 11 );
    }

    /**
     * Defines the data to be seeded. */
    public function seed( array $seeds ): array {
        return $this->some_service->seed_data();
    }

    /** Is this table dropped on deactivation (Defaults to false). */
    public function drop_on_deactivation(): bool {
        return false;
    }

    /** Drop table on uninstall. (Defaults to false). */
    public function drop_on_uninstall(): bool {
        return true;
    }

    /** Should this migration be seeded on activation. (Defaults to true). */
    public function seed_on_inital_activation(): bool {
        return true;
    }  
}
```
## Methods
***

> ### __construct(...$deps)
> @param mixed ...$deps Injected via the DI Container

You can inject any dependencies you need, so long as they either be inferred by the Type or has the rules define in the DI Rules. [See the di container docs for more details](https://perique.info/core/DI/)

***
> ### table_name(): string  
> @return string  
> @required method

This should return the final name for the table. This can be defined as a string literal or via an injected dependency (such as ['App_Config'](https://perique.info/core/App/app_config))

```php
/**
 * Returns the table name from the apps config.
 *
 * @return string
 */
protected function table_name(): string {
    return $this->config->db_tables('from_app_config');
}  
```

***
> ### schema( Schema $schema_config ): void
> @param PinkCrab\Table_Builder\Schema $schema  
> @return null
> @required method

This allows for the definition of the tables schema. Please see the [WPDB Migration](https://github.com/Pink-Crab/WP_DB_Migration) and [WPDB Table Builder](https://github.com/Pink-Crab/WPDB-Table-Builder) libraries for more details

```php
/**
 * Defines the schema for the migration.
 *
 * @param Schema $schema
 * @return void
 */
public function schema( Schema $schema ): void{
    $schema->column('id')->unsigned_int(12)->auto_increment();
    // Define rest of schema
}
```

***
> ### seed( array $seeds ): array
> @param array<int, array<string, mixed>> $seeds  
> @return array<int, array<string, mixed>>

Returns the data used to populate the table with. Should be returned as an array or arrays with matching column (key) and value pairs.

```php
/**
 * Defines the schema for the migration.
 *
 * @param array<int, array<string, mixed>> $seeds  
 * @return array<int, array<string, mixed>>
 */
public function seed( array $seeds ): array {
    return [
        ['columnA' => 'value1', 'columnB' => 1.11],
        ['columnA' => 'value2', 'columnB' => 2.22],
    ];
}
```

***
> ### drop_on_deactivation(): bool
> @return bool

Depending on the return value, the table can be dropped on deactivation. Defaults to `FALSE`

```php
/**
 * Is this table dropped on deactivation (Defaults to false)
 * @return bool
 */
public function drop_on_deactivation(): bool {
    return true;
}
```

***
> ### drop_on_uninstall(): bool
> @return bool

Depending on the return value, the table can be dropped on uninstall. Defaults to `FALSE`

```php
/**
 * Is this table dropped on uninstall (Defaults to false)
 * @return bool
 */
public function drop_on_uninstall(): bool {
    return true;
}
```

***
> ### seed_on_inital_activation(): bool
> @return bool

Depending on the return value, will populate tables with defined seeded data Defaults to `TRUE`

> Tables are only seeded once, even in the event of a later update.

```php
/**
 * Should this migration be seeded on activation. (Defaults to true)
 * @return bool
 */
public function seed_on_inital_activation(): bool {
    return true;
}
```