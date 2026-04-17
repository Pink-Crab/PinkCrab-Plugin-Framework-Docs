# Number Types

`Number_Type` and `Integer_Type` are used to define numeric schema arguments. They share the same set of numeric-specific methods via the `Number_Attributes` trait. The only difference is the underlying schema type: `'number'` (accepts floats) vs `'integer'` (whole numbers only).

**Namespaces:**
- `PinkCrab\WP_Rest_Schema\Argument\Number_Type`
- `PinkCrab\WP_Rest_Schema\Argument\Integer_Type`

---

## Creating a Numeric Type

```php
use PinkCrab\WP_Rest_Schema\Argument\Number_Type;
use PinkCrab\WP_Rest_Schema\Argument\Integer_Type;

$price = Number_Type::field( 'price' );
$count = Integer_Type::field( 'count' );

// With configuration callback
$page = Integer_Type::field( 'page', function( Integer_Type $i ): Integer_Type {
    return $i->minimum( 1 )->default( 1 );
} );
```

---

## Methods

### `minimum( int|float $min ): self`

Set the minimum value. Maps to `minimum` in the schema.

```php
$arg = Integer_Type::field( 'age' )->minimum( 0 );
$arg = Number_Type::field( 'price' )->minimum( 0.01 );
```

> `Integer_Type::minimum()` accepts `int`, `Number_Type::minimum()` accepts `float`.

### `maximum( int|float $max ): self`

Set the maximum value. Maps to `maximum` in the schema.

```php
$arg = Integer_Type::field( 'age' )->maximum( 150 );
$arg = Number_Type::field( 'rating' )->maximum( 5.0 );
```

### `exclusive_minimum( bool $exclusive = true ): self`

Set whether the minimum is exclusive. Maps to `exclusiveMinimum` in the schema.

```php
$arg = Number_Type::field( 'price' )
    ->minimum( 0 )
    ->exclusive_minimum(); // Must be > 0, not >= 0
```

### `exclusive_maximum( bool $exclusive = true ): self`

Set whether the maximum is exclusive. Maps to `exclusiveMaximum` in the schema.

```php
$arg = Number_Type::field( 'discount' )
    ->maximum( 100 )
    ->exclusive_maximum(); // Must be < 100, not <= 100
```

### `multiple_of( float $multiple_of ): self`

Require the value to be a multiple of the given number. Maps to `multipleOf` in the schema.

```php
$arg = Number_Type::field( 'price' )->multiple_of( 0.01 );  // Penny precision
$arg = Integer_Type::field( 'quantity' )->multiple_of( 5 );  // Multiples of 5
```

---

## Getter Methods

| Setter | Getter | Returns |
|--------|--------|---------|
| `minimum()` | `get_minimum()` | `?int` or `?float` |
| `maximum()` | `get_maximum()` | `?int` or `?float` |
| `exclusive_minimum()` | `get_exclusive_minimum()` | `?bool` |
| `exclusive_maximum()` | `get_exclusive_maximum()` | `?bool` |
| `multiple_of()` | `get_multiple_of()` | `?float` |

---

## Examples

### Pagination parameter

```php
$page = Integer_Type::field( 'page' )
    ->minimum( 1 )
    ->default( 1 )
    ->description( 'Current page of the collection.' );
```

### Per-page parameter with range

```php
$per_page = Integer_Type::field( 'per_page' )
    ->minimum( 1 )
    ->maximum( 100 )
    ->default( 10 )
    ->description( 'Maximum number of items per page.' );
```

### Price field with precision

```php
$price = Number_Type::field( 'price' )
    ->minimum( 0 )
    ->multiple_of( 0.01 )
    ->required()
    ->description( 'Product price in the store currency.' );
```

### Score with exclusive bounds

```php
$score = Number_Type::field( 'score' )
    ->minimum( 0 )
    ->maximum( 100 )
    ->exclusive_minimum()
    ->exclusive_maximum()
    ->description( 'Score between 0 and 100 (exclusive).' );
```

### Nullable integer

```php
$parent = Integer_Type::field( 'parent_id' )
    ->union_with_type( 'null' )
    ->minimum( 1 )
    ->description( 'Parent post ID, or null for top-level.' );
```

### Readonly ID field

```php
$id = Integer_Type::field( 'id' )
    ->readonly()
    ->description( 'Unique identifier for the resource.' )
    ->context( 'view', 'edit', 'embed' );
```

### Route argument

```php
use PinkCrab\WP_Rest_Schema\Parser\Argument_Parser;

register_rest_route( 'my/v1', '/items', array(
    'methods'  => 'GET',
    'callback' => 'handle_items',
    'args'     => Argument_Parser::for_route(
        Integer_Type::field( 'page' )->minimum( 1 )->default( 1 ),
        Integer_Type::field( 'per_page' )->minimum( 1 )->maximum( 100 )->default( 10 )
    ),
) );
```
