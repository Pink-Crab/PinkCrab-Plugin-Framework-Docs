---
description: >-
  At the core is the App it self
---

# App Hooks

The application it self comes with a selection of Actions and Filters which can be used to extend the plugin framework beyond its Interfaces and Abstract classes. Allows for hooking in additional code form other plugins and themes.

> All core hook handles can be accessed using constants defined in the `PinkCrab\Core\Application\Hooks` class.

## Pre Boot

> Constant: **PinkCrab\Core\Application\Hooks::APP_INIT_PRE_BOOT**  
> Full Handle: *PinkCrab/App/Boot/pre_init_call*
> 
Before the app is booted, this hook is fired giving access to DI_Container, Loader and App_Config objects. This allows you to hook in additional DI Rules and Hooks calls, before the app is booted. 

### **THIS IS RUN WHEN THE PLUGIN IS FIRST INITIALISED AND CAN NOT BE USED BY 3rd PARTY CODE, UNLESS IT BE ASSURED THAT ITS RUN PRIOR**



```php

/**
 * Allows for the modification of all intneral states before the apps Registation process is run.
 * 
 * @param App_Config $app_config  Gives access to the internal App_Config
 * @param Loader $loader          The hook loader used during registation.
 * @param DI_Container $container The DI Container can be used to defined new rules and consturct objects
 * @return void
 */
add_action(
    PinkCrab\Core\Application\Hooks::APP_INIT_PRE_BOOT, 
    function(App_Config $app_config, Loader $loader, DI_Container $container ): void {        
        // Alter DI Rules at runtime, based on some conditonal.
        if(! $container->has(Some_Interface::class)){
            $container->addRules([
                Some_Interface::class => [
                    'instanceOf' => Some_Implementation::class
                ]
            ]);
        }        
    },
    10,
    3
);
```

## Pre Registration

> Constant: **PinkCrab\Core\Application\Hooks::APP_INIT_PRE_REGISTRATION**  
> Full Handle: *PinkCrab/App/Boot/pre_registration*  
> 
Once the Application's internal states has been populated with all DI Rules, Hooks and App_Configuration values, the Registration process is run on the *init* action. By using the *APP_INIT_PRE_REGISTRATION* hook, we can make last minute changes to the classes ready to be processed.


```php
/**
 * Allows for the modification of all internal states before registation is run.
 * 
 * @param App_Config $app_config  Gives access to the internal App_Config
 * @param Loader $loader          The hook loader used during registation.
 * @param DI_Container $container The DI Container can be used to defined new rules and consturct objects
 * @return void
 */
add_action(
    PinkCrab\Core\Application\Hooks::APP_INIT_PRE_BOOT, 
    function(App_Config $app_config, Loader $loader, DI_Container $container ): void {
        
        // Bind additional rules to DI Container.
        $container->addRules([External_Interface::class => 
            ['instanceOf' => Some_External_Implementation::class]
        ]);
        
        // Add aditional hook calls, using the DI Container.
        $loader->add_action('save_post', [ $container::make( Some_External_Controller::class ), 'save_post' ] );
    },
    10,
    3
);

```
> This can also be used to override rules defined in the App itself from an additonal plugin.