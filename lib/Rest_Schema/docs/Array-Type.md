# Array Type

`Array_Type` is used to define array schema arguments. It supports typed items, length constraints, uniqueness, and element relationships (`allOf`, `anyOf`, `oneOf`).

**Namespace:** `PinkCrab\WP_Rest_Schema\Argument\Array_Type`

---

## Creating an Array Type

```php
use PinkCrab\WP_Rest_Schema\Argument\Array_Type;

$arg = Array_Type::field( 'tags' );

// With configuration callback
$arg = Array_Type::field( 'tags', function( Array_Type $a ): Array_Type {
    return $a->string_item()->unique_items();
} );
```

The type is automatically set to `'array'`.

---

## Item Methods

Define the type of items the array contains. Each method creates a typed child argument. An optional callback lets you configure the item further.

### `string_item( ?callable $config = null ): self`

```php
$tags = Array_Type::field( 'tags' )->string_item();

// With configuration
$tags = Array_Type::field( 'tags' )
    ->string_item( fn( String_Type $s ) => $s->min_length( 1 ) );
```

### `integer_item( ?callable $config = null ): self`

```php
$ids = Array_Type::field( 'ids' )->integer_item();
```

### `number_item( ?callable $config = null ): self`

```php
$scores = Array_Type::field( 'scores' )->number_item();
```

### `boolean_item( ?callable $config = null ): self`

```php
$flags = Array_Type::field( 'flags' )->boolean_item();
```

### `null_item( ?callable $config = null ): self`

```php
$nullable = Array_Type::field( 'values' )->null_item();
```

### `array_item( ?callable $config = null ): self`

Nested array items.

```php
$matrix = Array_Type::field( 'matrix' )
    ->array_item( fn( Array_Type $a ) => $a->integer_item() );
```

### `object_item( ?callable $config = null ): self`

Object items.

```php
$users = Array_Type::field( 'users' )
    ->object_item( fn( Object_Type $o ) => $o
        ->string_property( 'name' )
        ->string_property( 'email' )
    );
```

### `item( Argument $item ): self`

Add a pre-built item directly.

```php
$custom = String_Type::field( 'value' )->min_length( 1 );
$list = Array_Type::field( 'items' )->item( $custom );
```

### Multiple item calls — last wins or combinator

WP treats `items` as a SINGLE schema applied to every element; JSON Schema tuple form is not honoured. The library therefore handles repeated `*_item()` calls as follows:

- **No combinator set (default `allOf`):** the LAST-added item wins and `items` is emitted as a single schema. Prior item calls are discarded.
- **Combinator set (`one_of()` / `any_of()`):** all item schemas are kept and emitted as a list under the combinator key, producing valid WP output such as `'items' => [ 'oneOf' => [ {schemaA}, {schemaB} ] ]`.

For heterogeneous arrays at the SCHEMA ROOT (not nested inside `items`), use [`One_Of_Type`](Argument#combinators-vs-unions--one_of_type--any_of_type) / [`Any_Of_Type`](Argument#combinators-vs-unions--one_of_type--any_of_type) instead.

---

## Constraint Methods

### `min_items( int $min ): self`

Set the minimum number of items. Maps to `minItems` in the schema.

```php
$tags = Array_Type::field( 'tags' )->min_items( 1 );
```

### `max_items( int $max ): self`

Set the maximum number of items. Maps to `maxItems` in the schema.

```php
$tags = Array_Type::field( 'tags' )->max_items( 10 );
```

### `unique_items( bool $unique = true ): self`

Require all items to be unique. Maps to `uniqueItems` in the schema.

```php
$tags = Array_Type::field( 'tags' )->unique_items();
```

---

## Element Relationships

Control how multiple items relate to each other when more than one item type is defined. The default relationship is `allOf`.

### `all_of(): self`

All item schemas must match (default).

```php
$list = Array_Type::field( 'items' )
    ->string_item()
    ->integer_item()
    ->all_of();
```

### `any_of(): self`

At least one item schema must match.

```php
$list = Array_Type::field( 'items' )
    ->string_item()
    ->integer_item()
    ->any_of();
```

### `one_of(): self`

Exactly one item schema must match.

```php
$list = Array_Type::field( 'items' )
    ->string_item()
    ->integer_item()
    ->one_of();
```

---

## Getter Methods

| Setter | Getter | Returns |
|--------|--------|---------|
| `*_item()` | `get_items()` | `?array<int, Argument>` |
| — | `has_items()` | `bool` |
| — | `item_count()` | `int` |
| `min_items()` | `get_min_items()` | `?int` |
| `max_items()` | `get_max_items()` | `?int` |
| `unique_items()` | `get_unique_items()` | `?bool` |
| `all_of()` / `any_of()` / `one_of()` | `get_relationship()` | `string` |

---

## Examples

### Simple string array

```php
$tags = Array_Type::field( 'tags' )
    ->string_item()
    ->unique_items()
    ->description( 'A list of unique tag names.' );
```

### Array of IDs with constraints

```php
$ids = Array_Type::field( 'post_ids' )
    ->integer_item( fn( Integer_Type $i ) => $i->minimum( 1 ) )
    ->min_items( 1 )
    ->max_items( 50 )
    ->unique_items()
    ->required()
    ->description( 'A list of 1-50 unique post IDs.' );
```

### Array of objects

```php
use PinkCrab\WP_Rest_Schema\Argument\Object_Type;
use PinkCrab\WP_Rest_Schema\Argument\String_Type;

$addresses = Array_Type::field( 'addresses' )
    ->object_item( function( Object_Type $o ) {
        return $o
            ->string_property( 'street', fn( String_Type $s ) => $s->required() )
            ->string_property( 'city', fn( String_Type $s ) => $s->required() )
            ->string_property( 'postcode' );
    } )
    ->max_items( 5 )
    ->description( 'Up to 5 addresses.' );
```

### Nested arrays (matrix)

```php
$matrix = Array_Type::field( 'grid' )
    ->array_item( fn( Array_Type $a ) => $a->number_item() )
    ->description( 'A 2D grid of numbers.' );
```

### Post meta with array schema

```php
use PinkCrab\WP_Rest_Schema\Parser\Argument_Parser;

register_post_meta( 'post', 'related_ids', array(
    'type'         => 'array',
    'single'       => true,
    'show_in_rest' => array(
        'schema' => Argument_Parser::for_meta_data(
            Array_Type::field( 'related_ids' )
                ->integer_item( fn( Integer_Type $i ) => $i->minimum( 1 ) )
                ->unique_items()
                ->description( 'Related post IDs.' )
        ),
    ),
) );
```
