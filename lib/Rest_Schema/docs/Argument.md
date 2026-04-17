# Argument (Base Class)

The `Argument` class is the foundation for all schema types. Every type (`String_Type`, `Integer_Type`, `Number_Type`, `Boolean_Type`, `Null_Type`, `Array_Type`, `Object_Type`) extends this class and inherits all of its methods.

**Namespace:** `PinkCrab\WP_Rest_Schema\Argument\Argument`

---

## Creating an Argument

All argument types support two static constructors: `on()` and `field()` (they are identical).

```php
use PinkCrab\WP_Rest_Schema\Argument\String_Type;

// Both are equivalent
$arg = String_Type::on( 'my_field' );
$arg = String_Type::field( 'my_field' );
```

An optional configuration callback can be passed as the second parameter:

```php
$arg = String_Type::field( 'name', function( String_Type $s ): String_Type {
    return $s->required()->min_length( 1 );
} );
```

> `Boolean_Type` and `Null_Type` extend `Argument` with no additional methods — they simply set their type to `'boolean'` or `'null'` respectively. All methods documented here are available on them.

---

## Shared Methods

These methods are available on **every** argument type.

### `type( string|array $type ): self`

Set the schema type. This is automatically set by each type class (e.g., `String_Type` sets `'string'`). You only need to call this manually if setting a union type.

```php
$arg = String_Type::field( 'value' )
    ->type( array( 'string', 'null' ) );
```

### `union_with_type( string $type ): self`

Add an additional type to create a union type. The original type is preserved.

```php
$arg = String_Type::field( 'value' )
    ->union_with_type( 'null' );
// type becomes ['string', 'null']
```

### Combinators vs unions — `One_Of_Type` / `Any_Of_Type`

Use `union_with_type()` when the value can be one of several primitive types but shares the SAME attributes (`minLength`, `minimum`, etc.). Use `One_Of_Type` / `Any_Of_Type` when each variant needs its OWN distinct attributes.

**Union — shared attributes, multi-primitive type array:**

```php
String_Type::on( 'slug' )
    ->min_length( 3 )
    ->union_with_type( 'null' );

// Emits: ['slug' => ['type' => ['string','null'], 'minLength' => 3]]
```

Here `minLength: 3` applies to both members of the union.

**Combinator — distinct per-variant attributes:**

```php
One_Of_Type::on( 'thing' )
    ->variant( String_Type::on( 'thing' )->min_length( 3 ) )
    ->variant( Integer_Type::on( 'thing' )->minimum( 1 ) );

// Emits: ['thing' => ['oneOf' => [
//     ['type' => 'string',  'minLength' => 3],
//     ['type' => 'integer', 'minimum'   => 1],
// ]]]
```

`minLength` only applies to the string variant; `minimum` only to the integer variant. Union can't express that.

`One_Of_Type` requires EXACTLY ONE variant to match (`rest_find_one_matching_schema`). `Any_Of_Type` accepts ANY variant that matches (`rest_find_any_matching_schema`). Both emit with no outer `type` — the combinator keyword sits at the schema root.

### `description( string $description ): self`

Set a human-readable description for the argument.

```php
$arg = String_Type::field( 'email' )
    ->description( 'The user email address.' );
```

### `default( mixed $default ): self`

Set a default value for the argument. Accepts `string`, `int`, `float`, `bool`, `array`, `object`, or `null` — whatever matches the schema's declared `type` (or one of its union members).

```php
$arg = Integer_Type::field( 'page' )
    ->default( 1 );
```

Falsy and null values are fully supported — `default(false)`, `default(0)`, `default('')`, `default(null)` all count as explicit defaults and emit as `'default' => <value>` in the parsed schema. `has_default()` will return `true` after any of them.

```php
$flag = Boolean_Type::field( 'enabled' )->default( false );
// Emits: ['enabled' => ['type' => 'boolean', 'default' => false]]

$nullable = String_Type::field( 'slug' )
    ->union_with_type( Argument::TYPE_NULL )
    ->default( null );
// Emits: ['slug' => ['type' => ['string','null'], 'default' => null]]
```

An argument with no `default()` call will NOT emit a `default` key at all.

### `required( bool $required = true ): self`

Mark the argument as required. Defaults to `true` when called without a parameter.

```php
$arg = String_Type::field( 'name' )->required();
$arg = String_Type::field( 'name' )->required( false );
```

### `readonly( bool $readonly = true ): self`

Mark the argument as readonly. Defaults to `true` when called without a parameter.

```php
$arg = Integer_Type::field( 'id' )->readonly();
```

### `title( string $title ): self`

Set a title for the argument.

```php
$arg = String_Type::field( 'name' )->title( 'Display Name' );
```

### `format( string $format ): self`

Set the format constraint. Use the provided constants for valid WordPress formats.

```php
$arg = String_Type::field( 'email' )
    ->format( Argument::FORMAT_EMAIL );
```

### `expected( mixed ...$values ): self`

Set the allowed values (maps to `enum` in the schema). Multiple calls are additive.

Accepts any JSON-comparable value — strings, ints, floats, booleans, `null`, arrays, and objects — matching WP's `in_array(..., $enum, true)` semantics.

```php
$arg = String_Type::field( 'status' )
    ->expected( 'publish', 'draft', 'pending' );

// Null + string mix (for union schemas that include 'null'):
$arg = String_Type::field( 'slug' )
    ->union_with_type( 'null' )
    ->expected( 'home', 'about', null );

// Can also be called multiple times
$arg = String_Type::field( 'status' )
    ->expected( 'publish' )
    ->expected( 'draft', 'pending' );
```

### `context( string ...$contexts ): self`

Set the contexts where this field is visible. Standard WordPress contexts are `'view'`, `'edit'`, and `'embed'`. Multiple calls are additive.

```php
$arg = Integer_Type::field( 'id' )
    ->context( 'view', 'edit', 'embed' );
```

### ~~`name( string $name ): self`~~ *(deprecated)*

> **Deprecated in 1.0.0** — calling `name()` triggers `E_USER_DEPRECATED` and does nothing.
>
> `name` was never a valid JSON Schema / WP REST keyword and was leaking from internal child-indexing into the parsed output. Removed from emission; the setter is kept as a no-op for backwards compatibility and will be removed in a future release. For user-facing display labels use [`title()`](#title-string-title-self) instead.

### `arg_options( array $options ): self`

Attach a raw `arg_options` array to the argument. The array is emitted verbatim under the `arg_options` key in the parsed schema, letting WP REST controllers override `sanitize_callback` / `validate_callback` (or any other WP arg option) per property without replacing the callbacks on the Argument itself.

```php
$arg = String_Type::field( 'slug' )
    ->arg_options(
        array(
            'sanitize_callback' => 'sanitize_title',
            'validate_callback' => 'rest_validate_request_arg',
        )
    );
```

See `WP_REST_Controller::add_additional_fields_schema()` in WP core for how this merges into route args.

### `validation( callable $callback ): self`

Set a custom validation callback. Maps to `validate_callback` in the schema.

```php
$arg = String_Type::field( 'slug' )
    ->validation( function( $value, $request, $key ) {
        return preg_match( '/^[a-z0-9-]+$/', $value );
    } );
```

### `sanitization( callable $callback ): self`

Set a custom sanitization callback. Maps to `sanitize_callback` in the schema.

```php
$arg = String_Type::field( 'title' )
    ->sanitization( 'sanitize_text_field' );
```

### `set_attributes( array $attributes ): self`

Set custom attributes as a key-value array. Useful for extending with non-standard properties.

```php
$arg = String_Type::field( 'name' )
    ->set_attributes( array( 'custom_key' => 'value' ) );
```

---

## Getter Methods

Every setter has a corresponding getter:

| Setter | Getter | Returns |
|--------|--------|---------|
| `type()` | `get_type()` | `string\|array\|null` |
| `description()` | `get_description()` | `string` |
| `default()` | `get_default()` | `mixed` |
| — | `has_default()` | `bool` |
| `required()` | `is_required()` | `bool` |
| `required()` | `get_required()` | `?bool` |
| `readonly()` | `get_readonly()` | `?bool` |
| `title()` | `get_title()` | `?string` |
| `format()` | `get_format()` | `?string` |
| `expected()` | `get_expected()` | `?array` |
| `context()` | `get_context()` | `string[]` |
| `name()` | `get_name()` | `?string` |
| `validation()` | `get_validation()` | `?callable` |
| `sanitization()` | `get_sanitization()` | `?callable` |
| — | `get_key()` | `string` |
| `set_attributes()` | `get_attributes()` | `array` |

---

## Constants

### Type Constants

| Constant | Value |
|----------|-------|
| `Argument::TYPE_STRING` | `'string'` |
| `Argument::TYPE_INTEGER` | `'integer'` |
| `Argument::TYPE_NUMBER` | `'number'` |
| `Argument::TYPE_BOOLEAN` | `'boolean'` |
| `Argument::TYPE_NULL` | `'null'` |
| `Argument::TYPE_ARRAY` | `'array'` |
| `Argument::TYPE_OBJECT` | `'object'` |

### Format Constants

| Constant | Value | Description |
|----------|-------|-------------|
| `Argument::FORMAT_DATE_TIME` | `'date-time'` | ISO 8601 date-time |
| `Argument::FORMAT_EMAIL` | `'email'` | Email address |
| `Argument::FORMAT_IP` | `'ip'` | IPv4 or IPv6 address |
| `Argument::FORMAT_URI` | `'uri'` | URI |
| `Argument::FORMAT_UUID` | `'uuid'` | UUID |
| `Argument::FORMAT_HEX` | `'hex-color'` | Hex colour value |
| `Argument::FORMAT_TEXT_FIELD` | `'text-field'` | Single-line text |
| `Argument::FORMAT_TEXTAREA_FIELD` | `'textarea-field'` | Multi-line text |

---

## Method Chaining

All setters return `self`, so every method can be chained:

```php
$arg = String_Type::field( 'email' )
    ->required()
    ->format( Argument::FORMAT_EMAIL )
    ->description( 'A valid email address.' )
    ->context( 'view', 'edit' )
    ->sanitization( 'sanitize_email' )
    ->validation( 'is_email' );
```
