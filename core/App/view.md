# View

View can be either injected as dependency into a class or called via the `App::view()` helper function.

The View object is wrapper class which holds the implementation of the `Renderable` interface. Out of the box Perique comes pre setup with the `PHP_Engine`, which uses PHP files as the template engine. However, you can easily create your own implementation of the `Renderable` interface and inject it into the `View` object.

Like most modern PHP Frameworks, templates can be rendered by defining a template and some data to populate the template with. The data is passed into the template as variables, allowing for cleaner HTML/PHP code.

## Using View as Dependency

To use View as a dependency to another class, it is just a case of injecting it into the constructor.

```php
class Test_Controller {
   /**
    * @param View $view
    */
    protected $view;

   public function __construct( View $view ) {
      $this->view = $view;
   }

   public function render_view() {
      $this->view->render('test/view',[
         'number'  => 5, 
         'colours' => ['red','green','blue'],
      ]);
   }
}
```

This can also be used in conjunction with the `Hookable` interface, allowing for the creation of Controllers that can render views based on hook calls.

```php
class Post_Controller implements Hookable {
   /**
    * @param View $view
    */
    protected $view;

   public function __construct( View $view ) {
      $this->view = $view;
   }

   /**
    * @param Loader $loader
    */
   public function register( Loader $loader ): void {
      $loader->filter( 'the_content', [ $this, 'render_content' ], 1, 20 );
   }

   /**
    * @param string $content Inital content
    * @return string Replaced with custom view template, passing in the post
    */
   public function render_content( string $content ): string {
      return is_single() && get_post_type() === 'post'
         ? $this->view->render( 
            'post/single', // for path "views/post/single.php"
            [ 'post' => get_post() ], 
            View::RETURN_VIEW // false, true(default) for print  
         )
         : $content;
   }
}
```
## Using View as Helper

To use View as a helper, you can call the `App::view()` helper function.

```php
class Test_Controller {
   public function render_view() {
      App::view()->render('test/view.php',[
         'number'  => 5, 
         'colours' => ['red','green','blue'],
      ]);
   }
}
```
> While this is useful, it is not recommended as it is not easily testable with a mock View instance.

## Template Path

When you create your instance of Perique a view path is either passed or assumed. When calling a template in the `render()` method, the base path is prepended to the defined template path. This allows for a cleaner template path to be defined.

```php
App::view()->render('test/view.php',['foo' => 'bar']);
```
This would render the template found in `/your/path/wp-content/plugins/acme-plugin/views/test/view.php`.

### Assumed filetype.

All templates (when using the PHP_Engine) must be `.php` files. However the file extension can be omitted from the template path.

```php
App::view()->render('test/view',['foo' => 'bar']);

// is the same as

App::view()->render('test/view.php',['foo' => 'bar']);
```

### Dot Notation

The template path can also be defined using dot notation, which is useful when you have a lot of nested templates.

```php
App::view()->render('test.view',['foo' => 'bar']);

// is the same as

App::view()->render('test/view.php',['foo' => 'bar']);
```

## Template Data

All data you wish to have access to can be passed into the `render()` methods as an array. The array keys are then cast into variables, allowing for cleaner HTML/PHP code. The standard PHP variable name rules apply, so all keys should be valid. Arrays and Objects can be passed into the views, allow for partials to be rendered too.

```php
App::view()->render('test/view',[
   'number'  => 5, 
   'colours' => ['red','green','blue'],
]);
```   
```php 
// file - views/test/view.php
<div class="test">
   <p>Number: <?php echo $number; ?></p>
   <ul>
      <?php foreach($colours as $colour): ?>
         <li><?php echo $colour; ?></li>
      <?php endforeach; ?>
   </ul>
</div>
```

> When in templates `$this` gives access to the current `Renderable` implementation. 

## Print or Return

It is possible to print the compiled template to the screen or return this back as a string. By default the template is printed to the screen. Choosing to print or return can be defined as the third parameter of the `render()` method.

* View::PRINT_VIEW (`TRUE`)- Prints the template to the screen
* View::RETURN_VIEW (`FALSE`) - Returns the template as a string

```php
$output = App::view()->render('test/view',[],View::RETURN_VIEW);
// same as
$output = App::view()->render('test/view',[],true);
```

```php
App::view()->render('test/view',[],View::PRINT_VIEW);
// same as
App::view()->render('test/view',[],false);
```

### Output Buffer

Its also possible to use the output buffer to capture the output of anything that is printed to the screen. 

```php
$output = View::print_buffer(function(){
   App::view()->render('test/view',[],View::PRINT_VIEW);
});
```

## Sub Views

It is possible to render a sub view within a template. This is useful when you have a lot of nested templates. The sub view is rendered using the same data as the parent view.

```php
App::view()->render('test/view',[
   'number'  => 5, 
   'colours' => ['red','green','blue'],
]);
```

```php 
// file - views/test/view.php
<div class="test">
   <p>Number: <?php echo $number; ?></p>
  <?php App::view()->render('test/colour-list', ['colours' => $colours]); ?>
</div>

// file - views/test/colour-list.php
<ul>
  <?php foreach($colours as $colour): ?>
     <li><?php echo $colour; ?></li>
  <?php endforeach; ?>
</ul>
```
## View Models

View Model classes allow data and a template to be packaged into a single class, this makes it easier to pass data and templates to partials and also the population of data to pass to views from a controller.

```php
function get_colours() {
   return new View_Model(
      'test/colour-list',
      'colours' => ['red','green','blue']
   );
}
App::view()->render('test/view',[
   'number'  => 5, 
   'colours' => ,
]);
```

```php
// file - views/test/view.php
<div class="test">
   <p>Number: <?php echo $number; ?></p>
   <?php $this->view_model( $colours ); ?>
</div>
```
Like `render()` the `view_model()` method can also be passed a second parameter to define whether to print or return the template using the same constants as `render()`.

> It is possible to extend the view model class to create even more resuable view models.

```php
class Colour_List extends View_Model {
   public function __construct( array $colours ) {
	  parent::__construct('test/colour-list',[
		 'colours' => $colours
	  ]);
   }
}

App::view()->render('test/view',[
   'number'  => 5, 
   'colours' => new Colour_List(['red','green','blue'])
]);
```

### Components

Components are a way of creating reusable view models. They are similar to view models, but they are not tied to a specific template. This allows for the creation of reusable components that can be used in multiple templates.

Components are processed by the Component_Compiler. This is created when `View` is used for the first time. Out of the box, the components base path is assumed as `{view_path}/components`. The path is defined in the DI rules for the `Component_Compiler` class, and can be changed by overriding the rule.

```php
// file: config/dependencies.php
return [
   Component_Compiler::class => array(
      'constructParams' => array( 'custom/path' ), // Path is relative to the views directory.
   ),
];
```

A component class can be extended from `PinkCrab\Perique\Services\View\Component\Component`. Any properties defined with the components, regardless of visibility, will be passed into the template.
The template path can be defined in a number of ways.

* Global set of template Aliases.
* Annotation `@view` on the class.
* Using the `public function template(): ?string` method.
* Falling back to the class name.

### Aliases
It is possible to define a full path to the template using an `Alias`. This allows for components to be loaded from a different directory to the default `views` directory. 

To set an alias, you can use the `Hooks::COMPONENT_ALIASES` filter. This takes in the current array of Aliases and returns the new array of Aliases. The array index is the full class name (inc namespace) and the value is the path to the template.

> Add the alias to the `Hooks::COMPONENT_ALIASES` filter.
```php
add_filter( Hooks::COMPONENT_ALIASES, function( array $aliases ): array {
   $aliases[Acme\Component\Contact:class] = '/full/path/to/component/contact.php';
   return $aliases;
});
```
> Define the component class.
```php
class Contact extends Component {
   public $name;
   protected $email;
   private $address;
   public function __construct( string $name, string $email, string $address ) {
	  $this->name = $name;
	  $this->email = $email;
	  $this->address = $address;
   }
}
```
> Use the component in a template.
```php
App::view()->render('test/view',[
   'number'  => 5, 
   'contact' => new Contact('John Doe','jd@me.com','123 Fake Street');
]);
```

### Annotation

When using an annotation, the template path is defined in the docblock of the class. The annotation is `@view` and the value is the path to the template. This is relative to the `component` path, which is usually `{view_path}/components`, but can be changed using the using a DI Container rule.

> Define the component class.
```php
/**
 * @view contact.php
 */
class Contact extends Component {
   public $name;
   protected $email;
   private $address;
   public function __construct( string $name, string $email, string $address ) {
	  $this->name = $name;
	  $this->email = $email;
	  $this->address = $address;
   }
}
```

### Template Method

The template method is a public method that returns the path to the template. This is useful when you want to define the template path based on the state of the component.

> Define the component class.
```php
class Contact extends Component {
   public $name;
   protected $email;
   private $address;
   public function __construct( string $name, string $email, string $address ) {
	  $this->name = $name;
	  $this->email = $email;
	  $this->address = $address;
   }
   public function template(): ?string {
	  return $this->email ? 'contact.php' : 'contact-no-email.php';
   }
}
```

### Class Name

If the path can be obtained from the other methods, the class name will be used to define the template path. The class name is converted to kebab case and the `.php` extension is added. This is relative to the `component` path, which is usually `{view_path}/components`, but can be changed using the using a DI Container rule.

**Examples**

| Class Name | Template Path |
|------------|---------------|
| Contact    | contact.php   |
| Contact_No_Email  | contact-no-email.php|

> Define the component class.
```php
class Contact extends Component {
   public $name;
   protected $email;
   private $address;
   public function __construct( string $name, string $email, string $address ) {
	  $this->name = $name;
	  $this->email = $email;
	  $this->address = $address;
   }
}
```

### Using Components

Components can be used just similar to view models. They can be passed to templates in the data array like any other view model.

```php
App::view()->render('test/view',[
   'number'  => 5, 
   'contact' => new Contact('John Doe','john@doe.name', '123 Fake Street')
]);
```

```php
// file: views/test/view.php
<div id="<?php echo $number; ?>">
   $this->component( $contact );
</div>
```

```php
// file: views/components/contact.php
<div class="contact">
   <h1><?php echo $name; ?></h1>
   <p><?php echo $email; ?></p>
   <p><?php echo $address; ?></p>
</div>
```

## View Methods

These methods can be called against `View` instances of the underlying `Renderable` implementation.

### `render( string $view, array $data = array(), bool $print = true ): string`

Renders a view and either prints or returns the output.

### `component( Component $component, bool $print = true ): string`

Renders a component and either prints or returns the output.

### `view_model( View_Model $view_model, bool $print = true ): string`

Renders a view model and either prints or returns the output.

### `engine(): Renderable`

Returns the underlying `Renderable` implementation.

### `base_path(): string`

Returns the base path of the view files.

### static function print_buffer( callable $to_buffer ): string`

Prints the output of a function to a buffer and returns the output.

## Custom Render Engines

We offer a [BladeOne](../../module/BladeOne_Engine/index.md) implementation which should give a good example of how to not only create a custom Render Engine but also how to create workable configurations with Perique and its workflow. [BladeOne Engine Github](https://github.com/Pink-Crab/Perique-BladeOne-Engine)

### Creating a Render Engine

To create your own implementation of a render engine, you need to create a class that implements the `PinkCrab\Perique\Interfaces\Renderable` interface. This interface has 5 methods that need to be implemented.

```php
interface Renderable {
   /**
    * Display a view and its context.
    *
    * @param string $view
    * @param iterable<string, mixed> $data
    * @param bool $print
    * @return void|string
    */
   public function render( string $view, iterable $data, bool $print = true );

   /**
    * Renders a component.
    *
    * @param Component $component
    * @return string|void
    */
   public function component( Component $component, bool $print = true );

   /**
    * Renders a view Model
    *
    * @param View_Model $view_model
    * @return string|void
    */
   public function view_model( View_Model $view_model, bool $print = true );

   /**
    * Sets the component compiler.
    *
    * @param Component_Compiler $compiler
    * @return void
    */
   public function set_component_compiler( Component_Compiler $compiler ): void;

   /**
    * Returns the base view path.
    *
    * @return string
    * @since 1.4.0
    */
   public function base_view_path(): string;
}
```