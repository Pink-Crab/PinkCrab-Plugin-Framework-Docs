---
description: The PinkCrab Framework uses the DICE dependency injection container by Level2
---

# Dependency Injection

For a more detailed set of documentation on how to use DICE please visit the [Level2 GitHub](https://github.com/Level-2/Dice/) or on [Tom Butler \(Authors\) website](https://r.je/dice). 

At its core the PinkCrab Plugin Framework makes use of the DI container and the Reistration process, to register hooks and connect into WordPress. Through using the DI container, you can build complex dependency trees and ensure your code is not coupled and highly testable.

## Basic Useage

The primary way to use the DI container, is via the Registration process. All classes which are passed to the registration process are created and cached using the container. This means you can just inject all of your dependencies and use them in your callbacks.

```php

class Do_Something implements Registerable {

    protected $some_service;

    public function __construct(Some_Service $some_service){
        $this->some_service = $some_service;
    }

    public function register(Loader $loader): void{
        $loader->action('some_action', [$this, 'my_callback']);
    }

    public function my_callback(): void {
        // Now you can access the some_service in this callback.
        $this->some_service->do_something();
    }
}
```
Once this class is added to the ```config/registration.php``` file, it will be constructed and the action will be registered as a wp hook.

**Static Useage**

You can also access the DI container at any time, this is useful for quick calls or for when you want to create objects and have them cached/shared. While this is easier than injecting (especially if you already have a complex constructor), it can lead to messy, coupled code.

```php
<?php
$some_service = App::make(Some_Service::class);
$some_service->do_something();
```

## Interfaces and Abstracts

Obviously where using DI becomes really powerful is through the use of Interfaces and Abstract classes. This allows us to decide at runtime what class to use to implement/extend the Interfaces/Abstracts. 

The DI Container can not inferre which implemenation to use, so we need to define these in the DI rule set (```config/depenedencies.php```)

### Using Interfaces

The following example shows how you can use interfaces to make testing easier and your code more exendable, without rewriting everything if you want to use a new data store or process.
```php
// Customer Details Interface
interface Customer_Details{
    
    public function get_name(int $user_id): ?string;

}

// Using WP_User data.
class WP_User_Customer_Details implements Customer_Details{
    
    public function get_name(int $user_id): ?string{
        $user = get_userdata($user_id)
        if ( $user === false ){
            return null;
        }
        return $user->data->display_name;
    }

}

// Using a custom implementation for testing.
class In_Memory_Customer_Details implements Customer_Details{
    
    public function get_name(int $user_id): ?string{
        switch ($user_id) {
            case 1:
                return 'Customer 1';
            case 2:
                return 'Customer 2';
            default:
                return null; // Test for invalid ID.
        }
    }

}
```
Now we have our Customer_Details service setup, we can start using it. But first we need to set the rules, to denote what implementation we want.

```php 
class Customer_Requests {
    /** @var Customer_Details */
    protected $customer_details;

    public function __construct(Customer_Details $customer_details){
        $this->customer_details = $customer_details;
    }

    // Some code that uses the Customer_Details services
}
```
**Setting the DI Rules**

There are a few options on how to setup your rules, these can be stacked to allow different setups per class on top of global rules.

```php
<?php // file:config/depenencies.php

return [

    // Global Rule
    Customer_Details::class => [
        'instanceOf' => WP_User_Customer_Details::class
    ],

    // Class by class
    Other_Customer_Requests::class => [
        'substitutions' => [
            Customer_Details => In_Memory_Customer_Details::class
        ]
    ]
];
```
Using the rules defined above, any class which has **Customer_Details** as a dependency will be injected with an instance of the **WP_User_Customer_Details** object. Unless its the **Other_Customer_Requests** class, which will be injected with an instance of  **In_Memory_Customer_Details**

### Using Abstract Classes

Like Interfaces, we can do the same setup with Abstract Classes.

```php
abstract class Some_Thing {
    abstract public function foo(): void;
}

class Real_Foo extends Some_Thing {
    public function foo(): void {
        print 'Real Foo';
    }
}

class Mock_Foo extends Some_Thing {
    public function foo(): void {
        print 'Mock Foo';
    }
}
```
**Setting the DI Rules**
g
Like interfaces we can define the rules either globally or on a class by class basis.

```php
<?php // file:config/depenencies.php
return [
    // Global Rule
    Some_Thing::class => [
        'instanceOf' => Real_Foo::class
    ],

    // Class by class
    Some_Special_Case::class => [
        'substitutions' => [
            Some_Thing => Mock_Foo::class
        ]
    ]
];
```
Like before, any class which has **Some_Thing** as a dependency will be passed **Real_Foo**, unless its **Some_Special_Case** and then it will injected with **Mock_Foo**.

## Injected Instances

While the above is great for 90% of the usecases, we sometimes need to inject pre constructed dependencies. WPDB for example isnt constructed on the fly, its held as global which we need to fetch it from. While it might be tempting to use global $wpdb in your contstrutor, it is then very hard to test. So to get around this, we have a few options. 

### Global Instances.

Much like the definition of the classes to inject for dependencies we can set these at a global level or on a class by class basis.

```php 
<?php // file:config/depenencies.php
return [
    // Global (Any class)
    '*' => [
        'substitutions' => [
            \wpdb::class => $GLOBALS['wpdb']
        ]
    ],

    // Class by class
    Some_Special_Case::class => [
        'substitutions' => [
            \wpdb::class => new wpdb(..connection details..)
        ]
    ]
];
```
The above will then allow the passing of wpdb as a dependency, for all classes they will receive the same global instance used throughout WordPress. Unless you are using calling the **Some_Special_Case** class, in which case a custom instance of **WPDB** will be constructed (this allows you to have custom databases, for some use cases).

> By default the Plugin Framework will have the global wpdb rule applied. 

**You can only use instances of objects as part of a substitutions statment and only class names can be used with instanceOf**

### *Just In Time* Instances

Sometimes you might not want to create instances if they are not used, the above example will construct the custom instance of wpdb at runtime. We can however build this dependency as an when we need it using the **Call** functionality provided by *DICE*.

```php 
<?php // file:config/depenencies.php
return [
    // Using a JIT factory.
    Some_Special_Case::class => [
        'substitutions' => [
            \wpdb::class => [
                \Dice\Dice::INSTANCE => function() {
                    return new wpdb(..connection details..);
                }
            ]
        ]
    ]
];
```
Now our custom version of WPDB would only be constructed as and when its needed.

### Custom Setter Dependencies

Sometimes you will want to call various methods on an object before its passed as a dependncy. We can use the Call rules to do this.

```php 
<?php // file:config/depenencies.php
return [
    Some_Complex_Dependency::class => [
        'call' => [
            ['method_a', ['arg1', 1] ],
            ['method_b', [true] ],
            ['method_c', [] ],
        ]
    ]
];
```
Now whenever we pass Some_Complex_Dependency to be injected, the container will do the follwing before it is passsed.
```php
$class = new Some_Complex_Dependency();
$class->method_a('arg1', 1);
$class->method_b(true);
$class->method_c();
return $class; // This is what will be injected!
```

> For more details on construction object before passing them see the official [DICE documentation](https://r.je/dice).

## Constructor Properties

All of the examples above are bassed on passing objects to objects, this however can not be done using other scalar types such as string, arrays etc. If your depenedency has constuctor properties that need to be passed, you have a couple options.

```php 
<?php // file:config/depenencies.php
return [
    // Global pre constructed
    '*' => [
        'substitutions' => [
            \Some_Service::class => new Some_Service('prop1', 2, null)
        ]
    ],

    // Define Global constructor properties to be passed when called.
    Some_Service::class => [
        'constructParams' => ['prop3', 12, [999,10125]]
    ],

    // Allow for custom values on a class by class basis
    Service_Consumer_A::class => [
        'substitutions' => [
            \Some_Service::class => new Some_Service('prop2', 999, [1,4,5])
        ]
    ],

    // Using a JIT factory.
    Service_Consumer_B::class => [
        'substitutions' => [
            \Some_Service::class => [
                \Dice\Dice::INSTANCE => function() {
                    return new Some_Service('JIT', 1, null);
                }
            ]
        ]
    ],

    
];
