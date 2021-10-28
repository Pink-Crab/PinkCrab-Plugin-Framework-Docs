# BladeOne_Provider
A BladeOne Provider for the PinkCrab Renderable Interface.



![alt text](https://img.shields.io/badge/Current_Version-1.2.2-green.svg?style=flat " ") 
[![Open Source Love](https://badges.frapsoft.com/os/mit/mit.svg?v=102)](https://github.com/ellerbrock/open-source-badge/)
![](https://github.com/Pink-Crab/Loader/workflows/GitHub_CI/badge.svg " ")
[![codecov](https://codecov.io/gh/Pink-Crab/Perique-BladeOne-Provider/branch/master/graph/badge.svg?token=F7W4S9O5IR)](https://codecov.io/gh/Pink-Crab/Perique-BladeOne-Provider)


## Version ##
**Release 1.2.2**

> Supports and tested with the PinkCrab Perique Framework versions 0.5.* -> 1.*

*For support of the initial PinkCrab Plugin Frameworks (version 0.2.\*, 0.3.\* and 0.4.\*) please use BladeOne_Provider 1.0.3*


## Why? ##
The BladeOne implementation of the Renderable interface, allows the use of Blade within the PinkCrab Framework. 

## Setup ##

````bash 
$ composer require pinkcrab/bladeone-provider
````

The simplest way to enable BladeOne is to use the `BladeOne_Bootstrap` helper class, this will configure BladeOne fully for use as the `Renderable` implementation. To use this just include the following before Perique is setup in your plugin.php file for plugins or functions.php for themes.

```php
/**
 * Bootstrap Blade into Perique
 * 
 * @param string|array                             $views_path   The Path or paths used for the template files.
 * @param string                                   $cache_path   The path where all compiled/cached template files.
 * @param int|null                                 $blade_mode   The mode to start blade one using ( )
 * @param class_string | PinkCrab_BladeOne::class  $blade_class  The implementation of BladeOne to use
 */
BladeOne_Bootstrap::use( $views_path, $cache_path, $blade_mode, $blade_class );

// Bootstrap for Perique follows as normal.. 
$app = ( new App_Factory() )->with_wp_dice( true )
	->.....
```
> **\$blade_mode** :: For more details on the options please [see official docs](https://github.com/EFTEC/BladeOne/blob/d3e1efa1c6f776aa87fe47164d77e7ea67fc196f/lib/BladeOne.php#L208 )   
> **\$blade_class** :: This should be a the class name or instance of a class that extends `PinkCrab_BladeOne::class` this allows for the creation of custom components and extending BladeOne in general. For more details please [see official docs](https://github.com/EFTEC/BladeOne/wiki/Extending-the-class) Passing nothing or an invalid type will just use the default PinkCrab_BladeOne.

> If the cache directory doesn't exist, BladeOne will create it for you. It is however best to do this yourself to be sure of permissions etc.

## Included Components

Out of the box PinkCrab_BladeOne comes with the BladeOneHTML trait added, giving access all HTML components.
[BladeOneHTML Docs](https://github.com/EFTEC/BladeOneHtml)

## Configuring BladeOne ##

At its core BladeOne is a single class representation of Blade and most of its core functionality. To make configuration possible when being injected from DI Container we have a custom class you can extend and add to the registration array like any other Hookable class.

```php
class My_Blade_Config extends Abstract_BladeOne_Config {

	// Services can be injected using DI as normal (with Perique)
	protected $service;
	public function __construct( Mock_Service $service ) {
		$this->service = $service;
	}

	/**	
	 * This is the only method that must be implemented
	 * @param BladeOne_Provider $provider The instance of BladeOne being used.
	 */
	public function config( BladeOne_Provider $provider ): void {
		// Use this method to configure Blade 
		// Details of methods can be found below.		
		$provider->set_compiled_extension( $this->service->get_cache_file_extension() );
		$provider->directive( 'test', [ $this->service, 'some_method' ] );
		$provider->allow_pipe( false ); // Pipe is enabled by default, unlike standard BladeOne
	}
}
```
> You can have as many of these config classes as you want, allowing you to break up any custom directives, globals values and aliases etc.

## Public Methods ##
The BladeOne_Provider class has a number of methods which can be used to configure the underlying BladeOne implementation. This can be done using the `config()` method as part of the Config class above.

---

### **allow_pipe** ###
```php
/**
 * Sets if piping is enabled in templates.
 *
 * @param bool $bool
 * @return self
 */
public function allow_pipe( bool $bool = true ): self{}
```
Calling this will allow you toggle piping `{{ $var | esc_html }}` on or off. By default this is enabled.  
*Details*: https://github.com/EFTEC/BladeOne/wiki/Template-Pipes-\(Filter\)

---

### **directive** ###
```php
/**
 * Register a handler for custom directives.
 *
 * @param string   $name
 * @param callable $handler
 * @return self
 */
public function directive( string $name, callable $handler ): self{}
```
Calling this will allow you to create custom directives
```php
// Directive Example
$provider->directive('datetime', function ($expression) {
	// Return a valid PHP expression in php tags
    return "<?php echo ($expression)->format('m/d/Y H:i'); ?>";
});
```
```html
<!-- Called like so in your views. -->
<p class="date">@datetime($now)</p> 

<!-- Rendered as. -->
<p class="date">01/24/2021 14:34</p>
```
```php
// You will need to pass $now to your view
$class->render('path.to.view', ['now' => new DateTime()]);
```

*Details*: https://github.com/EFTEC/BladeOne/wiki/Methods-of-the-class#directive

> Don't forget our config class is loaded via the DI Container, so you can encapsulate your Directive callbacks into a class, with dependencies injected using the DI Container. (See above example)
---

### **directive_rt** ###
```php
/**
 * Register a handler for custom directives at runtime only.
 *
 * @param string   $name
 * @param callable $handler
 * @return self
 */
public function directive_rt( string $name, callable $handler ): self{}
```
Calling this will allow you to create custom directives
```php
// Directive at Run Time Example
$provider->directive_rt('datetime', function ($expression) {
	// Just print/echo the value.
	return "echo $expression->format('m/d/Y H:i');";
});
```
```html
<!-- Called like so in your views. -->
<p class="date">@datetime($now)</p> 

<!-- Rendered as. -->
<p class="date">01/24/2021 14:34</p>
```
```php
// You will need to pass $now to your view
$class->render('path.to.view', ['now' => new DateTime()]);
```
---

### **add_include** ###
```php
/**
 * Define a template alias
 *
 * @param string      $view  example "folder.template"
 * @param string|null $alias example "mynewop". If null then it uses the name of the template.
 * @return self
 */
public function add_include( $view, $alias = null ): self{}
```
This will allow you to set alias for your templates, this is ideal for global variables (share()).
```php
// Directive at Run Time Example
$provider->add_include('some.long.path.no.one.wants.to.type', 'longpath');

// This can then be used when rendering.
$class->render('longpath', ['data' => $data]);
```
---

### **add_alias_classes** ###
```php
/**
 * Define a class with a namespace
 *
 * @param string $alias_name
 * @param string $class_with_namespace
 * @return self
 */
public function add_alias_classes( $alias_name, $class_with_namespace ): self{}
```
This allows for the creation of simpler and short class names for use in templates.
```php
$provider->add_alias_classes('MyClass', 'Namespace\\For\\Class');
```
```html
<!-- Called like so in your views. -->
{{MyClass::some_method()}}
{!! MyClass::some_method() !!}
@MyClass::some_method()
```

---

### **share** ###
```php
/**
 * Adds a global variable. If <b>$var_name</b> is an array then it merges all the values.
 * <b>Example:</b>
 * <pre>
 * $this->share('variable',10.5);
 * $this->share('variable2','hello');
 * // or we could add the two variables as:
 * $this->share(['variable'=>10.5,'variable2'=>'hello']);
 * </pre>
 *
 * @param string|array<string, mixed> $var_name It is the name of the variable or it is an associative array
 * @param mixed        $value
 * @return $this
 */
public function share( $var_name, $value = null ): self{}
```
Allows fore the creation of globals variable. This is best set in the Config class (detailed above) as you can pass in dependencies.
```php
$provider->share('GLOBAL_foo', [$this->injected_dep, 'method']);
```
```html
<!-- Called like so in your views. -->
{{ $GLOBAL_foo }}
@include('some.path') <!-- Where some.path uses GLOBAL_foo, ideal for dynamic components like nav menus >
```
> You do not need to defined \$GLOBAL_foo when you are passing values to render `\$foo->render('template.path', [])`

*Details*: https://github.com/EFTEC/BladeOne/wiki/Methods-of-the-class#share

---

### **set_mode** ###
```php
/**
 * Set the compile mode
 *
 * @param int $mode 
 * 	Constants
 *	BladeOne::MODE_AUTO, 
 *	BladeOne::MODE_DEBUG, 
 *	BladeOne::MODE_FAST, 
 *	BladeOne::MODE_SLOW
 * @return self
 */
	public function set_mode( int $mode ): self{}
```
Allows for the setting of a custom rendering mode. used MODE_AUTO by default.
```php
$provider->set_mode(BladeOne::MODE_AUTO);
```

---

### **set_file_extension** ###
```php
/**
 * Set the file extension for the template files.
 * It must includes the leading dot e.g. .blade.php
 *
 * @param string $file_extension Example: .prefix.ext
 * @return $this
 */
public function set_file_extension( string $file_extension ): self{}
```
Allows you to define a custom extension for your blade templates.
```php
$provider->set_file_extension('.view.php');

// Can then be used to pass my.view.php as
$foo->render('my', ['data'=>'foo']);
```

---

### **set_compiled_extension** ###
```php
/**
 * Set the file extension for the compiled files.
 * Including the leading dot for the extension is required, e.g. .bladec
 *
 * @param string $file_extension
 * @return $this
 */
public function set_compiled_extension(( string $file_extension ): self{}
```
Allows you to define a custom extension for your compiled views.
```php
$provider->set_file_extension('.view_cache');
```

---

## Magic Call Methods ##

The BladeOne class has a large selection of Static and regular methods, these can all be accessed from BladeOne_Provider. These can be called as follows.
```php
// None static
$this->view->engine()->some_method($data);

// As static 
BladeOne_Provider::some_method($data);
```
> For the complete list of methods, please visit https://github.com/EFTEC/BladeOne/wiki/Methods-of-the-class


**If you want access none static methods using a static means, you can use**
```php 
// Using the App's View method to access none static methods on the fly.
App::view()->engine()->some_method($data);
```
> calling` engine()` on view, will return the underlying rendering engine used, in this case the BladeOne_Provider. 

> Of course you can set the engine it self as a global variable using `$provider->share('view_helper', [App::view(), 'engine'])`. Then you can use `{$view_helper->some_method(\$data)}` in your view.

***

## Dependencies ##
* [BladeOne 4.1](https://github.com/EFTEC/BladeOne)
* [BladeOne HTML 2.0](https://github.com/eftec/BladeOneHtml)

## Requires ##
* [PinkCrab Perique Framework V1.0.0 and above.](https://github.com/Pink-Crab/Perqiue-Framework)


## License ##

### MIT License ###
http://www.opensource.org/licenses/mit-license.html  

## Change Log ##
* 1.2.2 - Ensure that BladeOne is only loaded once wp is loaded. This avoids issues where template globals are registered before WP has finished loading. See issue #13
* 1.2.1 - Updated Readme and bumped BladeOne and BladeOneHTMl to the latest versions, now only compatible with Perique 1.\*.\*
* 1.2.0 - Comes with bootloader and ability to configure internal blade instance and use custom implementations to add directives, components and config in general
* 1.1.1 - Updated composer.json to support Perique 1.* and set github to run actions on PR & Merge to Dev
* 1.1.0 - Moved to the new Perique naming.
* 1.0.3 - Included the HTML extension by default.
* 1.0.2 - Bumped internal support for version 0.4.* of the Plugin Framework

