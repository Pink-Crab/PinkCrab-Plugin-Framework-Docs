---
description: >-
  A small collection of helper classes for tests used in various Modules and
  Dependency tests.
---

# PHP\_Unit Helpers

The Helper Library comes with the following classes. This is included with most of our libraries for their internal testing but is also included in the plugin boilerplate.

## Reflection

The Reflection Class gives quick and easy access to various Reflection functionality.

```php
use PinkCrab\PHPUnit_Helpers\Reflection;

/**
 * Gets the value of a property from a class
 * Can be public, protected or private
 */
Reflection::get_private_property(&$class, $property): mixed

/**
 * Allows the setting of a private/protected property
 */
Reflection::set_private_property( &$object, string $property, $value ): void

/**
 * Calls a private or protected method of a class
 */
Reflection::invoke_private_method( &$object, string $method_name, array $parameters = [] )


/** STATIC PROPERTIES */

/**
 * Sets a static property, if protected or private
 */
Reflection::set_private_static_property( $object, string $property, $value ): void

/**
 * Gets a static property, if protected or private
 */
Reflection::get_private_static_property( $object, string $property ): mixed
```

## Arrays

Provides a few helper functions for dealing with Arrays. Uses the FunctionConstructor library under the hood.

```php
use PinkCrab\PHPUnit_Helpers\Arrays;

/**
 * Returns the first item from an array filter, or null if none are pass
 * the filter.
 */
Arrays::filter_first(array $data, callable $callable): mixed
```

## Output

Series of methods used for handling the output of expressions.

```php
use PinkCrab\PHPUnit_Helpers\Output;

/**
 * Pass your expression into the callable, it will then be executed within a 
 * ob_*() and the any values printed will be returned.
 * A blank string will be returned if nothing captured by buffer
 */
Output::buffer(callable $action): string
```





