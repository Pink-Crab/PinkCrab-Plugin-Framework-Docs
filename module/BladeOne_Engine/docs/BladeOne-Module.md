# BladeOne \[Module]

The BladeOne Module is the primary class used to register and configure the use of BladeOne as the templating engine for your plugin.

## Usage

When creating an instance of Perique, the BladeOne module can be defined and also configured using the 2nd argument of the constructor.

```php

// Bootstrap for Perique follows as normal..
$app = ( new App_Factory('path/to/project/root') )
   ->default_config()
   ->module(BladeOne::class, function( BladeOne $blade ) {
      // Module config.
      $blade->mode( BladeOne::MODE_DEBUG );

      // BladeOne_Engine config.
      $blade->config( 
        fn( BladeOne_Engine $engine => $engine->directive('test', fn($e) =>'test') 
      );

      // Ensure you return the instance.
      return $blade;
   })
   // Rest of setup
   
```
> BladeOne and BladeOne_Engine make us fluent apis, so many of the methods can be chained together.

## Methods

### **public function template_path( string $template_path )**
> @param string $template_path  
> @return self  

Allows the setting of the template path, this will be used for all templates run through BladeOne.

****

### **public function compiled_path( string $compiled_path )**
> @param string $compiled_path  
> @return self

Allows the setting of the compiled path, this will be used for all templates run through BladeOne.

****
### **public function mode( int $mode )**
> @param int $mode  
> @return self

Allows the setting of the mode, this will be used for all templates run through BladeOne.

**Modes:**
* **PinkCrab_BladeOne::MODE_DEBUG** - Debug mode, the compiled templates will not be cached.
* **PinkCrab_BladeOne::MODE_AUTO** - Auto mode, the compiled templates will be cached.
* **PinkCrab_BladeOne::MODE_SLOW** - Slow mode, the compiled templates will be cached, but the cache will be checked on every request.
* **PinkCrab_BladeOne::MODE_FAST** - Fast mode, the compiled templates will be cached, and the cache will only be checked if the original template has been modified.

****
### **public function config(\callable $config)**
> @param callable(BladeOne_Engine $engine) $config  
> @return self

Allows for fine tuning of the BladeOne_Engine. This allows for the setting of how the runs. There are a number of helper methods available via the [BladeOne_Engine](BladeOne-Engine.md) class. While also having full access to the methods available in the EFTEC [BladeOne]([https://](https://github.com/EFTEC/BladeOne) library itself.
  