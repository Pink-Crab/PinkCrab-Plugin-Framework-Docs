{% include DI-sections.md %}

# Dependency Injection - Rules

## Interfaces

It is possible to define rules for which instance will be injected when a class has an interface as a dependency.

### Global (fallback) Rule

We can easily set a global rule for this interface, this will allow us to define which class or instance we want to inject whenever we request the class. 

```php
// @file config/dependencies.php

return array(
    Foo_Interface::class => array(
        'instanceOf' => Some_Class_That_Implements_Foo::class,
    ),
)
```

Now whenever any class that has `Foo_Interface` as a dependency, `Some_Class_That_Implements_Foo` will be passed. Any class may be used, or another interface may be used, as long as it is bound to a class in the container.

> Please note the order in which rules are added is important, if you plan to use an interface as the instance, it must be defined before the rule is added.

### Class by Class

Having a single implementation might be useful for sending emails and logging events within your application, it doesn't really give you that much granular control. You are able to define rules for specific classes, to allow specific implementations on a class by class basis. 

```php
class Class_A{
    public function __construct(Cache $foo){
        $this->foo = $foo;
    }
}

class Class_B{
    public function __construct(Cache $foo){
        $this->foo = $foo;
    }
}
```
If we wanted to use different caching methods here we have 2 options.

**Define all instances**
```php
// @file config/dependencies.php

return array(
    Class_A::class => array(
        'substitutions' => array(
            Cache::class => DB_Cache::class,
        ),
    ),
    Class_B::class => array(
        'substitutions' => array(
            Cache::class => File_Cache::class,
        ),
    ),
)
```
> Now whenever we inject `Class_A` as a dependency, we will get `DB_Cache` and for `Class_B` we will get `File_Cache`. 
 
Of course any other dependency we create will need to be defined as above, which while giving us a very verbose representation of our dependencies, its no ideal for development.

**Using a global fallback**
To get around having to define every unique implementation, we can use a mix of global and class definitions. To do this, we just need to define our fallback as a global and any class which doesnt have a custom rule, will use this.

```php
// @file config/dependencies.php

return array(
    // Unless otherwise defined, use Fallback Cache
    Cache::class => array(
        'instanceOf' => Fallback_Cache::class,
    ),
    Class_A::class => array(
        // When we inject Class A, use DB Cache for Cache (not Fallback Cache)
        'substitutions' => array(
            Cache::class => DB_Cache::class,
        ),
    ),
)
```
> Now whenever we inject `Class_A` as a dependency, we will get `DB_Cache` and for `Class_B` or any future class that has `Cache` as a dependency we will get `Fallback_Cache`. 

### Using constructed instances

When defining a rule for an interface, you can also define an instance to be used. This is useful for classes that require arguments to be passed to the constructor.

```php
// @file config/dependencies.php

return array(
   Class_A::class => array(
        'substitutions' => array(
            Cache::class => new DB_Cache('some_setup),
        ),
    ),
)
```

You can also choose to use a closure to define the instance. This will be created only if the class is requested.

```php
// @file config/dependencies.php

return array(
   Class_A::class => array(
        'substitutions' => array(
            Cache::class => array(
                \Dice\Dice::INSTANCE => fn() => new DB_Cache('some_setup'),
            )
        ),
    ),
)
```



### Using Abstract Classes

Injecting dependencies that are `Abstract` Classes works exactly the same as `Interfaces`. You can use a mix or either/or `Global` or `Class by Class` Rules

## Sharing & Inheritance

It is possible to define a rule which will cache the instance used and share this any other time the class is requested. This is useful for classes that are expensive to instantiate, such as a database connection.

```php
// @file config/dependencies.php

return array(
    DB::class => array(
        'instanceOf' => DB::class,
        'shared' => true,
    ),
)
```
Now any time we request `DB` as a dependency, we will get the same instance.

### Inheritance

By default when a rule is shared, it is shared with all child classes. This is useful for classes that have a lot of dependencies, but you want to share the same instance of a class. This can be turned off (from the parent class) by setting `inherit` to false.

```php
// @file config/dependencies.php

return array(
    DB::class => array(
        'instanceOf' => DB::class,
        'shared' => true,
        'inherit' => false,
    ),
)
```
Now any time we request `DB` as a dependency, we will get the same instance, but any class extending `DB` will get a new instance.

## Constructor Arguments

It is possible to define arguments that will be passed to the constructor when the class is instantiated. This is useful for classes that require arguments to be passed to the constructor.

```php
class Class_C{
    public function __construct($arg1, $arg2){
        $this->arg1 = $arg1;
        $this->arg2 = $arg2;
    }
}
```

Rules can be set for each argument, allowing you to define which instance will be used.

```php
// @file config/dependencies.php

return array(
    Class_C::class => array(
        'substitutions' => array(
            $arg1 => 'some_value',
            $arg2 => 'some_other_value',
        ),
    ),
);
```

## Calling Methods

It is possible to define methods that will be called when the class is instantiated. This is useful for classes that require methods to be called to set up the class.

```php
class Class_D{
    public function __construct($arg1, $arg2){
        $this->arg1 = $arg1;
        $this->arg2 = $arg2;
    }
    
    public function setup(){
        // Do some setup
    }

    public function setup2($arg1, $arg2){
        // Do some other setup
    }
}
```
Any method can be called, as long as it is public.

```php
// @file config/dependencies.php

return array(
    Class_D::class => array(
        'substitutions' => array(
            $arg1 => 'const_value',
            $arg2 => 'const_other_value',
        ),
        'call' => array(
            array( 'setup' => array() ),
            array( 'setup2' => array('some_value', 'some_other_value') ),
        ),
    ),
);
```
If `Class_D` is requested as a dependency, it will be constructed with the arguments defined, then `setup` and `setup2` will be called with the arguments supplied.

### Handling Fluent Methods

If the method you are calling returns `$this`, you can use the `Dice::CHAIN_CALL` constant to chain the calls together.

```php
class Class_D{
    public function __construct($arg1, $arg2){
        $this->arg1 = $arg1;
        $this->arg2 = $arg2;
    }
    
    public function setup(){
        // Do some setup
        return $this;
    }

    public function setup2($arg1, $arg2){
        // Do some other setup
        return $this;
    }
}
```

```php
// @file config/dependencies.php

return array(
    Class_D::class => array(
        'substitutions' => array(
            $arg1 => 'const_value',
            $arg2 => 'const_other_value',
        ),
        'call' => array(
            array( 'setup' => array(), \Dice\Dice::CHAIN_CALL ),
            array( 'setup2' => array('some_value', 'some_other_value'), \Dice\Dice::CHAIN_CALL ),
        ),
    ),
);
```

