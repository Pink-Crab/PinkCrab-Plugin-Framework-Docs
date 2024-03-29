![logo](../../assets/cards/Plugin-Lifecyle.jpg "Pink Crab")

# Perique - Plugin Life Cycle 

A module for the PinkCrab Perique Framework which makes it easy to add subscribers which are triggered during various events within a plugins life cycle(Activation, Deactivation, Uninstall etc)

[![Latest Stable Version](http://poser.pugx.org/pinkcrab/perique-plugin-lifecycle/v)](https://packagist.org/packages/pinkcrab/perique-plugin-lifecycle) [![Total Downloads](http://poser.pugx.org/pinkcrab/perique-plugin-lifecycle/downloads)](https://packagist.org/packages/pinkcrab/perique-plugin-lifecycle) [![Latest Unstable Version](http://poser.pugx.org/pinkcrab/perique-plugin-lifecycle/v/unstable)](https://packagist.org/packages/pinkcrab/perique-plugin-lifecycle) [![License](http://poser.pugx.org/pinkcrab/perique-plugin-lifecycle/license)](https://packagist.org/packages/pinkcrab/perique-plugin-lifecycle) [![PHP Version Require](http://poser.pugx.org/pinkcrab/perique-plugin-lifecycle/require/php)](https://packagist.org/packages/pinkcrab/perique-plugin-lifecycle)
![GitHub contributors](https://img.shields.io/github/contributors/Pink-Crab/Perique_Plugin_Life_Cycle?label=Contributors)
![GitHub issues](https://img.shields.io/github/issues-raw/Pink-Crab/Perique_Plugin_Life_Cycle)

[![WordPress 5.9 Test Suite [PHP7.4-8.1]](https://github.com/Pink-Crab/Perique_Plugin_Life_Cycle/actions/workflows/WP_5_9.yaml/badge.svg)](https://github.com/Pink-Crab/Perique_Plugin_Life_Cycle/actions/workflows/WP_5_9.yaml)
[![WordPress 6.0 Test Suite [PHP7.4-8.1]](https://github.com/Pink-Crab/Perique_Plugin_Life_Cycle/actions/workflows/WP_6_0.yaml/badge.svg)](https://github.com/Pink-Crab/Perique_Plugin_Life_Cycle/actions/workflows/WP_6_0.yaml)
[![WordPress 6.1 Test Suite [PHP7.4-8.2]](https://github.com/Pink-Crab/Perique_Plugin_Life_Cycle/actions/workflows/WP_6_1.yaml/badge.svg)](https://github.com/Pink-Crab/Perique_Plugin_Life_Cycle/actions/workflows/WP_6_1.yaml)
[![WordPress 6.2 Test Suite [PHP7.4-8.2]](https://github.com/Pink-Crab/Perique_Plugin_Life_Cycle/actions/workflows/WP_6_2.yaml/badge.svg)](https://github.com/Pink-Crab/Perique_Plugin_Life_Cycle/actions/workflows/WP_6_2.yaml)

[![codecov](https://codecov.io/gh/Pink-Crab/Perique_Plugin_Life_Cycle/branch/master/graph/badge.svg?token=Xucv38xrsa)](https://codecov.io/gh/Pink-Crab/Perique_Plugin_Life_Cycle)
[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/Pink-Crab/Perique_Plugin_Life_Cycle/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/Pink-Crab/Perique_Plugin_Life_Cycle/?branch=master)
[![Maintainability](https://api.codeclimate.com/v1/badges/27aa086ac22f0996516a/maintainability)](https://codeclimate.com/github/Pink-Crab/Perique_Plugin_Life_Cycle/maintainability)


> Requires  
> [Perique Plugin Framework 2.0.*](https://perique.info)  
> Wordpress 5.9+ (tested from WP5.9 to WP6.2)  
> PHP 7.4+ (tested from PHP7.4 to PHP8.2)  

****

## Why? ##

Makes for a simple OOP approach to handling WordPress Plugin Life Cycle events such as Activation, Deactivation and Uninstallation.

Connects to an existing instance of the Perique Plugin Framework to make use of the DI container and other shared services. (Please note due to the way these hooks are fired, you may not have full access to your DI Custom Rules, please [read below for more details](#Timings).)

****

## Setup ##

To install, you can use composer
```bash
$ composer require pinkcrab/perique-plugin-lifecycle
```

## Installing the Module

This must be bootstrapped with Perique to be used. This can easily be done on your main plugin file.

```php
// file ../wp-content/plugins/acme_plugin/plugin.php

// Boot the app as normal
$app = (new App_Factory())
    ->default_setup()
    ->module(
        Plugin_Life_Cycle::class, 
        fn(Plugin_Life_Cycle $module): Plugin_Life_Cycle => $module
            ->plugin_base_file(__FILE__) // Optional
            ->event(SomeEvent::class)
            ->event('Foo\Some_Class_Name')
    )
    ->boot();
```

You can either pass the `plugin_base_file` as the path to the main plugin file, or if left empty will assume its the file used to create the app instance.

All events can be passed as there class name, should be full namespace, or as a string of the class name.

## Event Types ##

There are 3 events which you can write Listeners for. Each of these listeners will implement an interface which requires a single `run()` method.

### Activation

All classes must implement the `PinkCrab\Plugin_Lifecycle\State_Event\Activation` interface.

```php
class Create_Option_On_Activation implements Activation {
    public function run(): void{
        update_option('plugin_activated', true);
    }
}
```
> This would then be run whenever the plugin is activated

### Deactivation

All classes must implement the `PinkCrab\Plugin_Lifecycle\State_Event\Deactivation` interface.

> These events will fail silently when called, so if you wish to catch and handle any errors/exceptions, this should be done within the events run method.

```php
class Update_Option_On_Deactivation implements Deactivation {
    public function run(): void{
        try{
            update_option('plugin_activated', false);
        } catch( $th ){
            Something::send_some_error_email("Deactivation event 'FOO' threw exception during run()", $th->getMessage());
        }
    }
}
```
> This would then be run whenever the plugin is deactivated

### Uninstall

All classes must implement the `PinkCrab\Plugin_Lifecycle\State_Event\Uninstall` interface.

> We automatically catch any exceptions and silently fail. If you wish to handle this differently, please catch them in your own code.


```php
class Delete_Option_On_Uninstall implements Uninstall {
    public function run(): void{
        try{
            delete_option('plugin_activated');
        } catch( $th ){
            // Do something rather than let it be silently caught above!
        }
    }
}
```
> This would then be run whenever the plugin is uninstalled

## Timings

The events are triggered before the `init` hook is called, so Perique is only partially booted when these are run. This means that you will have access to the `DI_Container` and `App_Config` but only custom rules that have been added directly, any rules added via 3rd parties using hooks, will not be available. Try to make events as simple as possible to avoid errors. 

## Extending 

It is possible to create other modules which can be used to add additional events and trigger other actions both before and after the finalise() method is called. This is useful if you wish to add additional events, or trigger other actions.

### Adding events

You can use the `Plugin_Life_Cycle::STATE_EVENTS` filter to add additional methods.
> Const `Plugin_Life_Cycle::STATE_EVENTS` = 'PinkCrab\Plugin_Lifecycle\State_Events'

```php
add_filter(
    Plugin_Life_Cycle::STATE_EVENTS,
    function( array $events ): array {
        $events[] = SomeEvent::class;
        return $events;
    }
);
```

You can also use the `Plugin_Life_Cycle::EVENT_LIST` filter to add additional events, after they have been constructed with the DI container.
> Const `Plugin_Life_Cycle::EVENT_LIST` = 'PinkCrab\Plugin_Lifecycle\Event_List'

```php
add_filter(
    Plugin_Life_Cycle::EVENT_LIST,
    function( array $events ): array {
        $events[] = new SomeEventInstance();
        $events[] = $this->container->create(SomeOtherInstance::class);
        return $events;
    }
);
```

### Triggering actions before finalise

You can use the `Plugin_Life_Cycle::PRE_FINALISE` filter to trigger actions before the finalise() method is called.

> Const `Plugin_Life_Cycle::PRE_FINALISE` = 'PinkCrab\Plugin_Lifecycle\Pre_Finalise'

```php
add_action(
    Plugin_Life_Cycle::PRE_FINALISE,
    function( Plugin_Life_Cycle $module ): void {
        // Do something before finalise is called.
    }
);
```

### Triggering actions after finalise

You can use the `Plugin_Life_Cycle::POST_FINALISE` filter to trigger actions after the finalise() method is called.

> Const `Plugin_Life_Cycle::POST_FINALISE` = 'PinkCrab\Plugin_Lifecycle\Post_Finalise'

```php
add_action(
    Plugin_Life_Cycle::POST_FINALISE,
    function( Plugin_Life_Cycle $module ): void {
        // Do something after finalise is called.
    }
);
```


## Change Log ##
* 2.0.1 - Reintroduced the getting the base path from the plugin file, if not defined (thanks @hibernius) and updated dev dependencies.
* 2.0.0 - Updated for Perique V2 and implements the new Module system.
* 1.0.0 - *skipped*
* 0.2.1 - Updated dev dependencies and GH pipeline.
* 0.2.0 - Improved the handling of Uninstall events and updated all dev dependencies.
* 0.1.1 - Added get_app() to main controller
* 0.1.0 - Inital version
