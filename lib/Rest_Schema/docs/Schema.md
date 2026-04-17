# Schema Builder

The `Schema` class is a top-level builder for producing the complete array format expected by `WP_REST_Controller::get_item_schema()`. It wraps an internal `Object_Type` and adds the required `$schema`, `title`, and `type` keys.

**Namespace:** `PinkCrab\WP_Rest_Schema\Schema`

---

## Creating a Schema

```php
use PinkCrab\WP_Rest_Schema\Schema;

$schema = Schema::on( 'post' );

// or using the field() alias
$schema = Schema::field( 'post' );
```

The `title` parameter becomes the schema's `title` value and is required.

---

## Methods

### `description( string $description ): self`

Set a description for the schema.

```php
$schema = Schema::on( 'post' )
    ->description( 'A blog post object.' );
```

### Property Methods

All property methods delegate to the internal `Object_Type`. They follow the same pattern as [Object_Type](Object-Type) properties.

```php
->string_property( string $name, ?callable $config = null ): self
->integer_property( string $name, ?callable $config = null ): self
->number_property( string $name, ?callable $config = null ): self
->boolean_property( string $name, ?callable $config = null ): self
->null_property( string $name, ?callable $config = null ): self
->array_property( string $name, ?callable $config = null ): self
->object_property( string $name, ?callable $config = null ): self
```

### `additional_properties( bool $allowed ): self`

Set whether additional properties are allowed.

```php
$schema = Schema::on( 'post' )
    ->additional_properties( false );
```

### `additional_properties_schema( Argument $schema ): self`

Set a schema for any additional properties.

```php
$schema = Schema::on( 'settings' )
    ->additional_properties_schema( String_Type::on( 'value' ) );
```

### `required_properties( string ...$names ): self`

Mark property names as required at the parent-object level. Forwards to [`Object_Type::required_properties()`](Object-Type#required_properties-string-names-self). Emits a draft-4 style `required: ['a','b']` array in the parsed schema.

```php
$schema = Schema::on( 'user' )
    ->integer_property( 'id' )
    ->string_property( 'email' )
    ->required_properties( 'id', 'email' );
```

### `get_context_param( array $args = array() ): array`

Build the `context` collection param descriptor, mirroring `WP_REST_Controller::get_context_param()`. Derives `enum` from the union of `context` values set on the schema's properties (unique, reverse-sorted) and returns the standard param shape.

```php
$schema = Schema::on( 'post' )
    ->integer_property( 'id', fn( $p ) => $p->context( 'view', 'edit', 'embed' ) )
    ->string_property( 'title', fn( $p ) => $p->context( 'view', 'edit' ) );

$param = $schema->get_context_param();
// [
//   'description'       => 'Scope under which the request is made; ...',
//   'type'              => 'string',
//   'sanitize_callback' => 'sanitize_key',
//   'validate_callback' => 'rest_validate_request_arg',
//   'enum'              => ['view', 'embed', 'edit'],
// ]

// With overrides:
$param = $schema->get_context_param( array( 'default' => 'view' ) );
```

### `to_array(): array`

Convert the schema to the array format WordPress expects.

```php
$array = Schema::on( 'post' )->to_array();
```

---

## Getter Methods

| Method | Returns |
|--------|---------|
| `get_title()` | `string` |
| `get_description()` | `string` |
| `get_object()` | `Object_Type` |

---

## Output Format

`to_array()` produces an array matching the WordPress `get_item_schema()` format:

```php
array(
    '$schema'    => 'http://json-schema.org/draft-04/schema#',
    'title'      => 'post',
    'type'       => 'object',
    'description' => '...',           // Only if set
    'properties' => array( ... ),     // Only if properties defined
    'additionalProperties' => ...,    // Only if set
)
```

The `$schema` and `type` keys are always included. The `description`, `properties`, and `additionalProperties` keys are only included when set.

---

## Examples

### Basic item schema

```php
use PinkCrab\WP_Rest_Schema\Schema;
use PinkCrab\WP_Rest_Schema\Argument\String_Type;
use PinkCrab\WP_Rest_Schema\Argument\Integer_Type;

public function get_item_schema() {
    return Schema::field( 'post' )
        ->description( 'A blog post object.' )
        ->integer_property( 'id', function( Integer_Type $id ) {
            return $id->readonly()
                ->description( 'Unique identifier.' )
                ->context( 'view', 'edit', 'embed' );
        } )
        ->string_property( 'title', function( String_Type $t ) {
            return $t->required()
                ->description( 'The post title.' )
                ->context( 'view', 'edit' );
        } )
        ->string_property( 'content', function( String_Type $t ) {
            return $t->description( 'The post content.' )
                ->context( 'view', 'edit' );
        } )
        ->string_property( 'status', function( String_Type $s ) {
            return $s->expected( 'publish', 'draft', 'pending' )
                ->context( 'view', 'edit' );
        } )
        ->additional_properties( false )
        ->to_array();
}
```

### Schema with nested objects

```php
use PinkCrab\WP_Rest_Schema\Schema;
use PinkCrab\WP_Rest_Schema\Argument\Object_Type;
use PinkCrab\WP_Rest_Schema\Argument\Array_Type;

$schema = Schema::field( 'product' )
    ->description( 'A product in the catalogue.' )
    ->integer_property( 'id', fn( Integer_Type $i ) => $i->readonly()->context( 'view', 'embed' ) )
    ->string_property( 'name', fn( String_Type $s ) => $s->required()->context( 'view', 'edit' ) )
    ->number_property( 'price', fn( Number_Type $n ) => $n->required()->minimum( 0 )->context( 'view', 'edit' ) )
    ->array_property( 'tags', fn( Array_Type $a ) => $a->string_item()->unique_items()->context( 'view', 'edit' ) )
    ->object_property( 'dimensions', function( Object_Type $o ) {
        return $o
            ->number_property( 'width' )
            ->number_property( 'height' )
            ->number_property( 'depth' )
            ->additional_properties( false );
    } )
    ->additional_properties( false )
    ->to_array();
```

### Accessing the internal Object_Type

If you need to work with the underlying `Object_Type` directly:

```php
$schema = Schema::on( 'post' );
$object = $schema->get_object();

// $object is an Object_Type instance — you can use it independently
$object->min_properties( 1 );
```
