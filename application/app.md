---
description: >-
  At the core is the App it self
---

# App Initialisation

You can kick off the Application by either constucting it manually and having control over some of the implementations or use the provided **App_Factory**. To make full use of the features provided, you will need to pass the dependencies.php, registration.php, settings.php [ (see setup for more details) ](../setup.md)

## *Manual Setup* ##
```php
<?php

$app = new App();

// Set the di container (must implent the DI_Container interface);
$container = PinkCrab_Dice::withDice( new Dice() ); // Example using Dice wrapper.
$app->set_container($container);

// Include custom DI rules
$app->container_config(
  function( DI_Container $container ): void {
    $container->addRules( include __DIR__ . '/config/dependencies.php' );
  }
);

// Set hook loader
$loader = new Loader();
$app->set_loader( $loader );

// Setup registration and add registerables middleware
$app->set_registration_services( new Registration_Service() );
$app->registration_middleware(
	new Registerable_Middleware( $loader )
);

// Include registration classes.
$app->registration_classses(include __DIR__ . '/config/registration.php' );

// Set the App_Config data.
$app->app_config( include __DIR__ . '/config/settings.php' );

// Boot the App()
$app->boot();
```
## *Factory Setup* ##
Created with PinkCrab_Dice (DICE wrapper) container and including default App, App_Config, WPDB, Renderable DI Rules.
```php
<?php

$app = ( new App_Factory() )->with_wp_dice( true )
	->di_rules( require __DIR__ . '/config/dependencies.php' )
	->app_config( require __DIR__ . '/config/settings.php' )
	->registration_classses( require __DIR__ . '/config/registration.php' )
	->boot();

```


# Static Helpers #

The App object has a few helper methods, which can be called statically (either from an instance, or from its name). 

## App::make(string $class, array $args = array()): object ##
* @param string $class Fully namespaced class name
* @param array<string, mixed> $args Constcutor params if needed
* @return object Object instance
* @throws App_Initialization_Exception Code 4 If app isnt intialised.

```make()``` can be used to access the Apps DI Container to fully resuolve the depenecies of an object. 

```php 
$emailer = App::make(Customer_Emailer::class);
$emailer->mail(ADMIN_EMAIL, 'Some Report', $email_body);
$emailer->send();
```

---

## App::config(string $key, ...$child): mixed ##
* @param string $key The config key to call
* @param ...string $child Additional params passed.
* @return mixed
* @throws App_Initialization_Exception Code 4 If app isnt intialised.

Once the app has been booted, you can access the App_Config values by either passing App_Config as a dependency, or by using the Apps helper.

```php

// Get post type slug
$args = ['post_type' => App::config('post_types', 'my_cpt')];

// Get current plugin version.
$version = App::config('version');
```

> For more details on App_Config and its various use cases, [click here](app_config).

---

## App::view(): View ##
* @return View
* @throws App_Initialization_Exception Code 4

If you need to render or return a template, you can use the ```view()``` helper. Returns an instance of the View class, populated with the current defined engine (use PHP by default).

```php
App::view()->render('signup/form', ['user' => wp_get_current_user(), 'nonce' => $nonce]);
```

> While the View and Config helpers are useful at times, its always better to inject them (App_Config::class or View::class).

---

## App::is_booted(): bool ##
* @return bool

Retruns true if the App has already been booted.

---

## App::has_app_config(): bool ##
* @return bool

Retruns true if App_Config has been defined.

---

# Setup Methods #

## $app->set_container( DI_Container $container ): App
* @param \PinkCrab\Core\Interfaces\DI_Container $container
* @return App
* @throws App_Initialization_Exception Code 2 If already set.
Sets the DI Container to the App. Any class which implements the DI_Container interface, can be used. 

---

## $app->container_config( callable $callback ): App
* @param callable(DI_Container):void $callback
* @return self
* @throws App_Initialization_Exception Code 1 If Container not set.
Allows access to the internal container, used for setting DI rules.

---

## $app->set_app_config( array $settings ): App
* @param array<string, mixed> $settings
* @return App
* @throws App_Initialization_Exception Code 5 If settings already defined

Sets the App_Config based on the array passed.

---

## $app->set_loader( Loader $loader ): App
* @param \PinkCrab\Loader\Loader $loader
* @return App
* @throws App_Initialization_Exception Code 8 If Loader has already been set.
Sets the hook loader used by Registerable to register all hooks subscriptions.

---

## $app->set_registration_services( Registration_Service $registration ): App
* @param \PinkCrab\Core\Services\Registration\Registration_Service $registration
* @return App
* @throws App_Initialization_Exception Code 7 If Registration_Service has already been set.
Sets the internal Registration service, which takes in the array of classes and passes to every middleware declared.