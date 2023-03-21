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
 * Allows for the modification of all internal states before the apps Registration process is run.
 */
add_action(
    PinkCrab\Core\Application\Hooks::APP_INIT_PRE_BOOT, 
     /**
     * @param App_Config $app_config  Gives access to the internal App_Config
     * @param Hook_Loader $loader     The hook loader used during registration.
     * @param DI_Container $container The DI Container can be used to defined new rules and construct objects
     * @return void
     */
    function(App_Config $app_config, Hook_Loader $loader, DI_Container $container ): void {},
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
 * Allows for the modification of all internal states before registration is run.
 *
 */
add_action(
    PinkCrab\Core\Application\Hooks::APP_INIT_PRE_REGISTRATION, 
    /**
     * @param App_Config $app_config  Gives access to the internal App_Config
     * @param Hook_Loader $loader          The hook loader used during registration.
     * @param DI_Container $container The DI Container can be used to defined new rules and consturct objects
     * @return void
     */
    function(App_Config $app_config, Hook_Loader $loader, DI_Container $container ): void {},
    10,
    3
);

```

### Post Registration

> Constant: **PinkCrab\Core\Application\Hooks::APP_INIT_POST_REGISTRATION**
> Full Handle: *PinkCrab/App/Boot/post_registration*
>
> Once the registration process has been run, the *APP_INIT_POST_REGISTRATION* hook is fired, but before the hooks are dispatched. This allows one final chance to modify any of the hooks calls. 

```php
/**
 * Allows for the modification of all internal states after registration is run.
 */
add_action(
    PinkCrab\Core\Application\Hooks::APP_INIT_POST_REGISTRATION, 
    /**
     * @param App_Config $app_config  Gives access to the internal App_Config
     * @param Hook_Loader $loader          The hook loader used during registration.
     * @param DI_Container $container The DI Container can be used to defined new rules and consturct objects
     * @return void
     */
    function(App_Config $app_config, Hook_Loader $loader, DI_Container $container ): void {},
    10,
    3
);
```

### App Config

> Constant: **PinkCrab\Core\Application\Hooks::APP_INIT_CONFIG_VALUES**
> Full Handle: *PinkCrab/App/Boot/config_values*
>
> When any array of values is passed to the App_Config, this hook is fired. This allows for the modification of any values passed to the App_Config. This can be used to override any values defined in the App itself from an additional plugin.

```php
/**
 * Allows for the modification of all internal states after registration is run.
 */
add_filter(
    PinkCrab\Core\Application\Hooks::APP_INIT_CONFIG_VALUES, 
    /**
     * @param array<string, string|array<string, string>> $config  The values passed to the App_Config
     * @return array<string, string|array<string, string>>
     */
    function(array $config): array {},
    10,
    1
);
```

### DI Rules

> Constant: **PinkCrab\Core\Application\Hooks::APP_INIT_SET_DI_RULES**
> Full Handle: *PinkCrab/App/Boot/set_di_rules*
>
> When any array of DI Rules is passed to the DI_Container, this hook is fired. This allows for the modification of any rules passed to the DI_Container. This can be used to override any rules defined in the App itself from an additional plugin.

```php
/**
 * Allows for the modification of all internal states after registration is run.
 */
add_filter(
    PinkCrab\Core\Application\Hooks::APP_INIT_SET_DI_RULES, 
    /**
     * @param array $rules  The rules passed to the DI_Container
     * @return array
     */
    function(array $rules): array {},
    10,
    1
);
```

### Registration Class List

> Constant: **PinkCrab\Core\Application\Hooks::APP_INIT_REGISTRATION_CLASS_LIST**
> Full Handle: *PinkCrab/App/Boot/registration_class_list*
>
> When an array of classes is passed to the Registration service, this hook is fired. This allows for the modification of any classes passed to the Registration service. This can be used to override any classes defined in the App itself from an additional plugin.

```php
/**
 * Allows for the modification of all internal states after registration is run.
 */
add_filter(
    PinkCrab\Core\Application\Hooks::APP_INIT_REGISTRATION_CLASS_LIST, 
    /**
     * @param class-string[] $classes  The classes passed to the Registration service
     * @return class-string[]
     */
    function(array $classes): array {},
    10,
    1
);
```

### Module Manager

> Constant: **PinkCrab\Core\Application\Hooks::MODULE_MANAGER**
> Full Handle: *PinkCrab/App/Boot/module_manager*
>
> Before the module manager is processed and any middleware is defined, this hook is fired. This allows for the modification of the module manager before it is processed. This can be used to override any modules defined in the App itself from an additional plugin.

```php
/**
 * Allows for the modification of all internal states after registration is run.
 */
add_action(
    PinkCrab\Core\Application\Hooks::MODULE_MANAGER, 
    /**
     * @param Module_Manager $module_manager  The module manager passed to the Registration service
     * @return void
     */
    function(Module_Manager $module_manager): void {},
    10,
    1
);
```

### Component Alias

> Constant: **PinkCrab\Core\Application\Hooks::COMPONENT_ALIAS**
> Full Handle: *PinkCrab/App/Boot/component_alias*
>
> It is possible to define the full paths for a components template, this allow for the definition of components to be installed from composer libs or external plugins.

```php
/**
 * Allows for the modification of all internal states after registration is run.
 */
add_filter(
    PinkCrab\Core\Application\Hooks::COMPONENT_ALIAS, 
    /**
     * @param array<string, string> $alias  The alias passed to the Registration service
     * @return array<string, string>
     */
    function(array $alias): array {},
    10,
    1
);
```