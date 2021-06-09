---
description: >-
  Supplies a Renderable implementation for BladeOne, a slimline version of
  Laravels Blade templating language.
---

# BladeOne\_Provider

### Installation

```bash
composer require pinkcrab/bladeone-provider
```

You will need to either replace the Renderable rules in `config/dependencies.php` or define BladeOne as the implementation on a class by class basis.

```php
// file config/dependencies.php
use PinkCrab\BladeOne\BladeOne_Provider;

return array(
	// Update the Renderable Global Rule.
	'*'               => array(
		'substitutions' => array(
			App::class        => App::get_instance(),
			Renderable::class => BladeOne_Provider::class,
		),
	),

    // If you do not plan on using the PHP_Engine you can remove this. 
	  /*PHP_Engine::class => array(
			'shared'          => true,
			'constructParams' => array( App::config( 'path', 'view' ) ),
		), */
		
    // We define our view and file paths for the instance of BladeOne
    BladeOne_Provider::class => array(
		'shared'          => true,
		'constructParams' => array( 
			App::config( 'path', 'view' ), 
			App::config( 'path', 'upload_root' ) . 'blade_cache' 
		),
	)
```

### Accessing BladeOne

### Examples

#### Render blade template.

```php
class Some_Controller implements Registerable {
    protected $view;
    
    public __construct(Renderable $view){
        $this->view = $view;
    }
    
    public function register(Hook_Loader $loader){ ... } 
    
    public function render_view(){
        $this->view->render('some_path', ['foo' => 'bar']);
    }
}

// @file views/some_path.blade.php
{{ $foo }} // will render "bar"
```

#### Explicitly use Blade while PHP\_Engine is the default.

```php
class Some_Controller implements Registerable {
    protected $blade;
    protected $php_engine;
    
    public __construct(BladeOne_Provider $blade, Renderable $php_engine ){
        $this->blade = $blade;
        $this->php_engine = $php_engine;
    }
    
    public function register(Hook_Loader $loader){ ... } 
    
    public function render_view_with_blades(){
        // file located views/some_path.blade.php
        $this->blade->render('some_path', ['foo' => 'bar']);
    }
    
    public function render_view_with_php(){
        // file located views/some_other_path.php
        $this->php_engine->render('some_other_path', ['foo' => 'bar']);
    }
}
```

### Bound to App

If you are planning to use BladeOne for all your templates, you can bind to the App container so it can be easily accessed throughout the application. This must be set after the Dependency Rules have been created but before the registration has been added. This is to ensure BladeOne is ready when you need it.

```php
// @file bootstrap.php

.....

// Add all DI rules and register the actions from loader.
add_action(
	'init',
	function () use ( $loader, $app, $config ) {
	
		// If the dependencies file exists, add rules.
		if ( file_exists( __DIR__ . '/config/dependencies.php' ) ) {
			$dependencies = include 'config/dependencies.php';
			$app->get( 'di' )->addRules( $dependencies );
		}
		
		## HERE ##
		// Add view to container. 
		$app->set('view', $app::make(BladeOne_Provider::class));
		## HERE ##
		
		// Add this before, to ensure all is registered.
		if ( file_exists( __DIR__ . '/config/registration.php' ) ) {
			$registerables = include 'config/registration.php';
			Register_Loader::initalise( $app, $registerables, $loader );
		}
	},
	1
);

// You can then access BladeOne any where, at any time by calling, either
App::view()->render('template', ['data' => NAN]);
App::retrive('view')->render('template', ['data' => NAN]);
```

#### Accessing BladeOne helper methods

If you need to access the instance of BladeOne which is being used \(shared instance, unless set otherwise in config/dependencies.php. You can call `$this->view->get_blade()` from within your class which has an instance.

You can also make use of the magic `__call()` and `__callStatic()` methods we have, this allows the calling of BladeOne functions with a shorter syntax. Also if you bind the **view** to the Apps container you can call them from anywhere. `App::view()->isQuoted($maybe_has_quotes);`

```php
class Some_Controller {
    protected $view;
    
    public __construct(Renderable $view){
        $this->view = $view;
    }
    
    
    public function get_blade_then_call(){
        // From dependency.
        $blade = $this->view->get_blade();
        $blade->isQuoted('"some_path"'); // True
        
        // From App Container
        $blade = App::view()->get_blade();
        $blade::e('<p>NO</p>Yes'); // Yes
    }
    
    
    public function directly_calling_methods(){
        // Works with all public methods
        $this->view->isQuoted('"some_path"'); // True
        App::view()->isQuoted('"some_path"'); // True
        
        // Including the static ones.
        $this->view::e('<p>NO</p>Yes'); // Yes
        App::view()::e('<p>NO</p>Yes'); // Yes
    }
}
```

