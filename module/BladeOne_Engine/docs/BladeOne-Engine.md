# BladeOne \[Engine]

The following methods can be used to configure the BladeOne_Engine class.


### **public function allow_pipe( bool $bool = true )**
> @param bool $bool Default true  
> @return self  

Calling this will allow you toggle piping `{%raw%}{{ $var | esc_html }}{%endraw%}` on or off. By default this is enabled.  

*Details*: https://github.com/EFTEC/BladeOne/wiki/Template-Pipes-\(Filter\)

---

### **public function directive( string $name, callable $handler )**
> @param string   $name  
> @param callable $handler  
> @return self  

Calling this will allow you to create custom directives
```php
// Directive Example
$bladeone_engine->directive('datetime', function ($expression) {
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

### **public function directive_rt( string $name, callable $handler)**
> @param string   $name  
> @param callable $handler  
> @return self  

Calling this will allow you to create custom directives
```php
// Directive at Run Time Example
$bladeone_engine->directive_rt('datetime', function ($expression) {
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

### **public function add_include( $view, $alias = null )**
> @param string      $view  example "folder.template"  
> @param string|null $alias example "mynewop". If null then it uses the name of the template.  
> @return self  

This will allow you to set alias for your templates, this is ideal for global variables (share()).
```php
// Directive at Run Time Example
$bladeone_engine->add_include('some.long.path.no.one.wants.to.type', 'longpath');

// This can then be used when rendering.
$class->render('longpath', ['data' => $data]);
```
---

### **public function add_alias_classes( $alias_name, $class_with_namespace )**
> @param string $alias_name  
> @param string $class_with_namespace  
> @return self  
 
This allows for the creation of simpler and short class names for use in templates.
```php
$bladeone_engine->add_alias_classes('MyClass', 'Namespace\\For\\Class\\MyClass');
```
```html
<!-- Called like so in your views. -->
{%raw%}{{MyClass::some_method()}}{%endraw%}
{!! MyClass::some_method() !!}
@MyClass::some_method()
```

---

### **public function share( string|array $var_name, $value = null )**
> @param string|array<string, mixed> $var_name It is the name of the variable or it is an associative array  
> @param mixed        $value  
> @return self

Allows fore the creation of globals variable. These can be set individually or as an array.
```php
// Calling a function from a class.
$bladeone_engine->share('GLOBAL_foo', [$this->injected_dep, 'method']);

// Single static value.
$bladeone_engine->share('GLOBAL_foo', 'bar');

// Array of values.
$bladeone_engine->share([
  'GLOBAL_foo' => 'bar',
  'GLOBAL_bar' => 'foo',
]);
```
```html
<!-- Called like so in your views. -->
{%raw%}{{ $GLOBAL_foo }}{%endraw%}
@include('some.path') 
<!-- Where some.path uses GLOBAL_foo, ideal for dynamic components like nav menus >
```
> You do not need to defined \$GLOBAL_foo when you are passing values to render `\$foo->render('template.path', [])`

*Details*: https://github.com/EFTEC/BladeOne/wiki/Methods-of-the-class#share

---

### **public function set_file_extension( string $file_extension )**
> @param string $file_extension Example: `.prefix.ext`  
> @return self  
 
Allows you to define a custom extension for your blade templates.
```php
$bladeone_engine->set_file_extension('.view.php');

// Can then be used to pass my.view.php as
$foo->render('my', ['data'=>'foo']);
```

---

### **public function set_compiled_extension( string $file_extension )**
> @param string $file_extension  Example: `.view_cache`   
> @return self  
 
Allows you to define a custom extension for your compiled views.
```php
$bladeone_engine->set_file_extension('.view_cache');
```
---

### **public function set_esc_function( callable $esc )**
> @param callable(mixed):string $esc (must be a named function or invokable class) 
> @return self  
 
Allows you to define a custom esc function for your views. By default this is set to `esc_html`.

> **must be a named function**

```php
$bladeone_engine->set_esc_function('esc_attr');
```
---
