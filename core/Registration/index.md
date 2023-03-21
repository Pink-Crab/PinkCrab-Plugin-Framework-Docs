---
title: Registration
---

# Registration

* [Modules](Modules)
* [Hookable Middleware](Hookable)

The registration process is the main entry point for the entire plugin. A list of classes are instaniated and then passed through multiple **Regisration Middlewares**. These combined with the Loader allow for subscription of actions and filters with WordPress. 

Out of the box, Registerable is the only middleware included. Custom middleware can be written and more information of that will follow.

## Registerable (Interface)

Registerable is a simple interface with a single method ```public function register(Hook_Loader $loader): void```, this allows you to create classes which can "register" hook calls using the **Hook Loader**


```php
class Events_Post_Table_Controller implements Registerable {
    /**
     * Gives us access to our events
     *
     * @var Event_Repository
     */
    protected $events;
    
    /**
     * Create controller with the event repository injected
     * We can then use the repository to handle all of our basic set/get
     * interactions with the post type.
     * 
     * @param Event_Repository $events Access to events
     */
    public function __construct( Event_Repository $events ) {
		  $this->events = $events;
	  }
	
		/**
		 * Register all hooks
		 *
		 * @param Hook_Loader $loader
		 * @return void
		 */
	  public function register( Hook_Loader $loader ): void {
		  $loader->action( "manage_{$this->events->cpt_key}_posts_columns", [$this, 'add_columns'] );
		  $loader->action( "manage_{$this->events->cpt_key}_posts_custom_column", [$this, 'render_tickets_sold_cell'], 10, 2 );
	  }
	
		/**
		 * Add additional columns to post table.
		 *
		 * @param array $columns Current columns.
		 * @return array
		 */
		public function add_columns( array $columns ): array {
			$columns['tickets_sold'] = __( 'Sold', 'my_plugin' );
			return $columns;
		}
		
		/**
		 * Populates the custom columns cell.
		 *
		 * @param string $column Column being called from.
		 * @param int $post_id The post being displayed.
		 * @return void
		 */
		public function render_tickets_sold_cell( string $column, int $post_id ): void {
			if( $column === 'tickets_sold' ){
				printf(
					'<strong>%d</strong>',
					count($this->events->tickets_sold($post_id)
				);
			}
		}
	}
```



> Once a class has been created, its just a case of adding the class to the registration array found in `config/registration.php`

As all classes which are added to the registration array are called when the app is initialised (on **init** with a priority of **1**). You can also register other WP fixtures such as Post_Types, Taxonomies etc.

```php
class CPT_Mananger implements Registerable {

  // Used to call other methds
  public function register(Hook_Loader $loader): void{
    $this->register_movies_post_type();
  }

  // Registers the movies post type on init
  protected function register_movies_post_type(): void{
    register_post_type( 'movies',[
      'labels' => [
          'name' => __( 'Movies' ),
          'singular_name' => __( 'Movie' )
      ],
      'public' => true,
      'has_archive' => true,
      'rewrite' => array('slug' => 'movies'),
      'show_in_rest' => true,
 
    ]);
  }
}
```
## Registration Array

When the App is intialised, all classes which are listed in the Registration Array (found in `config/registration.php`) are created using the [DI Container](../dependency-injection/README.md) (allowing for the injection of dependencies) and passed through each defined Registaion Middlewares. This allows your Application to interact with the many WP API's.

```php 
// file config/registration.php

use My\Namespace\Foo;

return [
  Foo::class,
  'Full\Namespaced\Class\As\String'
];
```
> If you are planning to use the `::class` get the fully qualified class name. You must ensure you include the `use Class\Name;`.

## Registration Middleware

Through the creation of custom Middleware its possible to create custom interactions between your App and any API's you wish. To create additional processes, your middleware class must implement the `PinkCrab\Core\Interfaces\Registration_Middleware`.

```php
interface Registration_Middleware {

	/**
	 * Process the current class
	 *
	 * @param object $class
	 * @return object
	 */
	public function process( $class );
}

```
All middleware will be passed each class defined in the *Registartion Array* to be processed. This can be anything from creating custom WP Rest Endpoints, running remote calls, anything.

In the **Registerable Middlware** which is included. We check that the passed class implements the Registerable interface, if it does we pass the loader to the register() method.

```php

class Example_Registration_Middleware implements Registration_Middleware {

  protected $some_service;

  public function __construct(Some_Service $some_service){
    $this->some_service = $some_service;
  }

  public function process( $class ) {
		
    // Check the passed class implements some interface required
    if ( in_array( Example_Interface::class, class_implements( $class ) ?: array(), true ) ) {
      
      // Do whatever is needed.
      $this->some_service->do_something($class);
      if($class->something()){
        $this->some_service->do_something_else($class);
      }

    }

		return $class;
	}

}
```

> See the Registerable_Middleware for another example on [github](https://github.com/Pink-Crab/Plugin-Framework/blob/master/src/Services/Registration/Registration_Service.php)

To include any custom middleware with the App's instance, these can be added after creating the Apps isntance.

```php
// file: plugin.php

// After the app has been created
$app = App_Factory()
  ->......; // Do not run boot() yet.

// Create instance of custom middleware.
$middleware = $app::make(Custom_Middleware::class); // You can access the DI container to construct your Middleware, with needed dependencies.

// You can then add your middleware
$app->registration_middleware($middleware)
  ->registration_middleware($app::make(Some_Other_Middleware::class));

// Now the app can be booted, with the custom middleware.
$app->boot();

