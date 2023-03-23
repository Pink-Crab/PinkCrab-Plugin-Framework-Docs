{% include DI-sections.md %}

# Dependency Injection - Existing Definitions

## Interfaces and Pre Defined Classes

There are a number of definitions that are already defined in the container, these are:

| Class | Description |
| :------------- | :------------- |
| `wpdb` | The global WordPress database object |
| `PinkCrab\Perique\Interfaces\DI_Container` | The Perique DI Container |
| `PinkCrab\Perique\Services\View\View` | The Perique View Service |
| `PinkCrab\Perique\Application\App` | The Perique App Instance |
| `PinkCrab\Perique\Application\App_Config` | The Apps config |

Any of these can be used and you will get a shared instance of the class.

```php
class Foo{
    public function __construct(\wpdb $wpdb, App_Config $app_config){
        /** @var wpdb */
        $this->wpdb = $wpdb;
        /** @var App_Config */
        $this->app_config = $app_config;
    }
}
```

## Injectables

There are a number of `Interfaces` which can be added to a class and a defined method will be called, with a shared instance passed in. These are:

| Interface | Method | Description |
| :------------- | :------------- | :------------- |
| `PinkCrab\Perique\Interfaces\Inject_DI_Container` | `set_di_container` | Injects the DI Container |
| `PinkCrab\Perique\Interfaces\Inject_App_Config` | `set_app_config` | Injects the App_Config Instance |
| `PinkCrab\Perique\Interfaces\Inject_Hook_Loader` | `set_hook_loader` | Injects the Hook_Loader Instance |

We have a matching set of traits which can also be used, these defined a setter and property for the class.

| Trait | Property | Description |
| :------------- | :------------- | :------------- |
| `PinkCrab\Perique\Services\Container_Aware_Traits\Inject_DI_Container_Aware` | `$di_container` | DI Container Instance |
| `PinkCrab\Perique\Services\Container_Aware_Traits\Inject_App_Config_Aware` | `$app_config` | App_Config Instance |
| `PinkCrab\Perique\Services\Container_Aware_Traits\Inject_Hook_Loader_Aware` | `$hook_loader` | Hook_Loader Instance |

```php
class Foo implements Inject_App_Config, Inject_Hook_Loader{
    use Inject_App_Config_Aware, Inject_Hook_Loader_Aware;

    public function bar(){
        $this->hook_loader->add('init', function(){
            echo $this->app_config->additional('foo');
        });
    }
}
```