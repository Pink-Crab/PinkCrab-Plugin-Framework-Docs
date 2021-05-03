---
description: >-
  At the core is the App it self
---

# App Hooks

The application it self comes with a selection of Actions and Filters which can be used to extend the plugin framework beyond its Interfaces and Abstract classes. Allows for hooking in additional code form other plugins and themes.

> All core hook handles can be accessed using constants defined in the `PinkCrab\Core\Application\Hooks` class.

## Pre Boot

Before the app is booted, this hook is fired giving access to DI_Container, Loader and App_Config objects. This allows you to hook in additional DI Rules and Hooks calls, before the app is booted. 

> Constant: **PinkCrab\Core\Application\Hooks::APP_INIT_PRE_BOOT**  
> Full Handle: *PinkCrab/App/Boot/pre_init_call*

```php

// In some other plugin
add_action(
    PinkCrab\Core\Application\Hooks::APP_INIT_PRE_BOOT, 
    /**
     * @param App_Config $app_config  Gives access to the internal App_Config
     * @param Loader $loader          The hook loader used during registation.
     * @param DI_Container $container The DI Container can be used to defined new rules and consturct objects
     */
    function(App_Config $app_config, Loader $loader, DI_Container $container ): void{
        
        // Bind additional rules to DI Container.
        $container->addRules([Some_Interface::class => 
            ['instanceOf' => Some_Implementation::class]
        ]);
        
        // Add aditional hook calls.
        $loader->add_action('save_post', [ $container::make( Some_Controller::class ), 'save_post' ] );
    },
    10,
    3
);
```
> This can also be used to override rules defined in the App itself from an additonal plugin.

## Pre Registration

Once the Application has been populated with all DI Rules, Hooks are populated and App_Congi

> Constant: **PinkCrab\Core\Application\Hooks::APP_INIT_PRE_REGISTRATION**  
> Full Handle: *PinkCrab/App/Boot/pre_registration*

```php