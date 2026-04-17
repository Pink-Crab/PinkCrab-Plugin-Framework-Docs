# Object Type

`Object_Type` is used to define object schema arguments with named properties, additional properties, pattern properties, and element relationships.

**Namespace:** `PinkCrab\WP_Rest_Schema\Argument\Object_Type`

---

## Creating an Object Type

```php
use PinkCrab\WP_Rest_Schema\Argument\Object_Type;

$arg = Object_Type::field( 'address' );

// With configuration callback
$arg = Object_Type::field( 'address', function( Object_Type $o ): Object_Type {
    return $o
        ->string_property( 'street' )
        ->string_property( 'city' )
        ->additional_properties( false );
} );
```

The type is automatically set to `'object'`.

---

## Properties

Define named properties on the object. Each method creates a typed child property. An optional callback lets you configure the property further.

### `string_property( string $name, ?callable $config = null ): self`

```php
$obj = Object_Type::field( 'user' )
    ->string_property( 'name' );

// With configuration
$obj = Object_Type::field( 'user' )
    ->string_property( 'name', fn( String_Type $s ) => $s->required()->min_length( 1 ) );
```

### `integer_property( string $name, ?callable $config = null ): self`

```php
$obj = Object_Type::field( 'user' )
    ->integer_property( 'age', fn( Integer_Type $i ) => $i->minimum( 0 ) );
```

### `number_property( string $name, ?callable $config = null ): self`

```php
$obj = Object_Type::field( 'product' )
    ->number_property( 'price', fn( Number_Type $n ) => $n->minimum( 0 ) );
```

### `boolean_property( string $name, ?callable $config = null ): self`

```php
$obj = Object_Type::field( 'settings' )
    ->boolean_property( 'enabled' );
```

### `null_property( string $name, ?callable $config = null ): self`

```php
$obj = Object_Type::field( 'data' )
    ->null_property( 'deleted_at' );
```

### `array_property( string $name, ?callable $config = null ): self`

```php
$obj = Object_Type::field( 'post' )
    ->array_property( 'tags', fn( Array_Type $a ) => $a->string_item()->unique_items() );
```

### `object_property( string $name, ?callable $config = null ): self`

Nested object properties.

```php
$obj = Object_Type::field( 'user' )
    ->object_property( 'address', function( Object_Type $o ) {
        return $o
            ->string_property( 'street' )
            ->string_property( 'city' );
    } );
```

---

## Additional Properties

Control whether properties beyond those defined are allowed.

### `additional_properties( bool $allowed ): self`

Set as a boolean to allow or disallow extra properties.

```php
$obj = Object_Type::field( 'user' )
    ->string_property( 'name' )
    ->additional_properties( false ); // No extra properties allowed
```

### `additional_properties_schema( Argument $schema ): self`

Set a schema that any additional property must conform to. This is used when you want to allow extra properties but constrain their type.

```php
$obj = Object_Type::field( 'settings' )
    ->additional_properties_schema( String_Type::on( 'value' ) );
// Any extra property must be a string
```

---

## Pattern Properties

Define property schemas that apply to properties matching a regex pattern.

### `string_pattern_property( string $pattern, ?callable $config = null ): self`

```php
$obj = Object_Type::field( 'meta' )
    ->string_pattern_property( '^\w+$' );
```

### `integer_pattern_property( string $pattern, ?callable $config = null ): self`
### `number_pattern_property( string $pattern, ?callable $config = null ): self`
### `boolean_pattern_property( string $pattern, ?callable $config = null ): self`
### `null_pattern_property( string $pattern, ?callable $config = null ): self`
### `array_pattern_property( string $pattern, ?callable $config = null ): self`
### `object_pattern_property( string $pattern, ?callable $config = null ): self`

All pattern property methods follow the same signature. The pattern is the regex and the optional callback configures the type.

```php
$obj = Object_Type::field( 'data' )
    ->integer_pattern_property( '^count_', fn( Integer_Type $i ) => $i->minimum( 0 ) )
    ->string_pattern_property( '^label_' );
```

---

## Property Count Constraints

### `min_properties( int $min ): self`

Set the minimum number of properties. Maps to `minProperties` in the schema.

```php
$obj = Object_Type::field( 'settings' )
    ->min_properties( 1 );
```

### `max_properties( int $max ): self`

Set the maximum number of properties. Maps to `maxProperties` in the schema.

```php
$obj = Object_Type::field( 'settings' )
    ->max_properties( 10 );
```

### `required_properties( string ...$names ): self`

Mark specific property names as required at the parent-object level, producing the draft-4 style `required: ['a','b']` array used by most WP core `get_item_schema()` implementations.

```php
$obj = Object_Type::field( 'user' )
    ->integer_property( 'id' )
    ->string_property( 'email' )
    ->string_property( 'nickname' )
    ->required_properties( 'id', 'email' );

// Emits:
// ['user' => [
//     'type'       => 'object',
//     'properties' => [ 'id' => [...], 'email' => [...], 'nickname' => [...] ],
//     'required'   => [ 'id', 'email' ],
// ]]
```

Coexists with per-property `->required(true)` booleans — both forms are honoured by WP and may appear side-by-side if both are set.

---

## Element Relationships

Control how properties relate to each other. The default relationship is `allOf`.

### ~~`all_of(): self`~~ *(deprecated)*

> **Deprecated in 1.0.0** — no-op. `allOf` is already the default relationship, and WP's REST validator does not honour `allOf` as a combinator anyway. Use `one_of()` / `any_of()` or the root-level [`One_Of_Type` / `Any_Of_Type`](Argument#combinators-vs-unions--one_of_type--any_of_type) classes instead.

### `any_of(): self`

At least one property schema must match.

### `one_of(): self`

Exactly one property schema must match.

```php
$obj = Object_Type::field( 'contact' )
    ->string_property( 'email' )
    ->string_property( 'phone' )
    ->one_of(); // Must have exactly one of email or phone
```

---

## Getter Methods

| Setter | Getter | Returns |
|--------|--------|---------|
| `*_property()` | `get_properties()` | `array<string, Argument>` |
| — | `has_properties()` | `bool` |
| — | `count_properties()` | `int` |
| `additional_properties()` | `get_additional_properties()` | `bool\|Argument\|null` |
| — | `has_additional_properties()` | `bool` |
| `*_pattern_property()` | `get_pattern_properties()` | `array<string, Argument>` |
| — | `has_pattern_properties()` | `bool` |
| — | `count_pattern_properties()` | `int` |
| `min_properties()` | `get_min_properties()` | `?int` |
| `max_properties()` | `get_max_properties()` | `?int` |
| `all_of()` / `any_of()` / `one_of()` | `get_relationship()` | `string` |

---

## Examples

### User profile object

```php
use PinkCrab\WP_Rest_Schema\Argument\Object_Type;
use PinkCrab\WP_Rest_Schema\Argument\String_Type;
use PinkCrab\WP_Rest_Schema\Argument\Integer_Type;

$profile = Object_Type::field( 'profile' )
    ->string_property( 'name', fn( String_Type $s ) => $s->required()->min_length( 1 ) )
    ->string_property( 'email', fn( String_Type $s ) => $s->required()->format( String_Type::FORMAT_EMAIL ) )
    ->integer_property( 'age', fn( Integer_Type $i ) => $i->minimum( 0 )->maximum( 150 ) )
    ->string_property( 'bio', fn( String_Type $s ) => $s->max_length( 500 ) )
    ->additional_properties( false );
```

### Settings with string values only

```php
$settings = Object_Type::field( 'settings' )
    ->additional_properties_schema( String_Type::on( 'value' ) )
    ->min_properties( 1 )
    ->max_properties( 50 )
    ->description( 'Key-value settings where all values must be strings.' );
```

### Nested object

```php
$order = Object_Type::field( 'order' )
    ->integer_property( 'id', fn( Integer_Type $i ) => $i->readonly() )
    ->object_property( 'billing', function( Object_Type $o ) {
        return $o
            ->string_property( 'name', fn( String_Type $s ) => $s->required() )
            ->string_property( 'address', fn( String_Type $s ) => $s->required() )
            ->string_property( 'city', fn( String_Type $s ) => $s->required() )
            ->string_property( 'postcode' )
            ->additional_properties( false );
    } )
    ->array_property( 'items', function( Array_Type $a ) {
        return $a->object_item( function( Object_Type $o ) {
            return $o
                ->integer_property( 'product_id', fn( Integer_Type $i ) => $i->required()->minimum( 1 ) )
                ->integer_property( 'quantity', fn( Integer_Type $i ) => $i->required()->minimum( 1 ) )
                ->number_property( 'price', fn( Number_Type $n ) => $n->minimum( 0 ) );
        } )->min_items( 1 );
    } );
```

### Post meta with object schema

```php
use PinkCrab\WP_Rest_Schema\Parser\Argument_Parser;

register_post_meta( 'post', 'location', array(
    'type'         => 'object',
    'single'       => true,
    'show_in_rest' => array(
        'schema' => Argument_Parser::for_meta_data(
            Object_Type::field( 'location' )
                ->number_property( 'lat', fn( Number_Type $n ) => $n->required() )
                ->number_property( 'lng', fn( Number_Type $n ) => $n->required() )
                ->string_property( 'label' )
                ->additional_properties( false )
        ),
    ),
) );
```
