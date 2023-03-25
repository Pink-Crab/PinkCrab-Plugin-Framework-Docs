{% include registration-sections.md %}

# Hookable Middleware

Out of the box, the `Hookable` middleware is already included, this allows for the registration of hooks using the [`Hook_Loader`](../../lib/Hook_Loader) class. As each class that is passed through the registration process is constructed via the `Container` class, you can also inject any dependencies you need into your class.

## Hookable (Interface)

Hookable is a simple interface with a single method `public function register(Hook_Loader $loader): void`, this allows you to create classes which can `register()` method calls using the [`Hook_Loader`](../../lib/Hook_Loader)


```php
class Events_Post_Table_Controller implements Hookable {

   private const TICKETS_SOLD_COLUMN = 'tickets_sold';

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
       $columns[self::TICKETS_SOLD_COLUMN] = __( 'Sold', 'my_plugin' );
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
       if( $column === self::TICKETS_SOLD_COLUMN ){
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
class CPT_Manager implements Hookable {

  protected App_Config $config;

   public function __construct(App_Config $config){
      $this->config = $config;
   }

   // Used to call other methods
   public function register(Hook_Loader $loader): void{
      $this->register_movies_post_type();
   }

   // Registers the movies post type on init
   protected function register_movies_post_type(): void{
      register_post_type( 
        $this->config->post_types('movies'),
        [
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
> In the above example, we can just invoke the `register_post_type` function as we are already on the **init** call.