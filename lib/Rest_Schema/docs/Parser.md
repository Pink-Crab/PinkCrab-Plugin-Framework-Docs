# Parser

The `Argument_Parser` class converts fluent builder objects into the array format WordPress expects. It is the bridge between the builder API and WordPress functions like `register_rest_route()`, `register_post_meta()`, and `WP_REST_Controller::get_item_schema()`.

**Namespace:** `PinkCrab\WP_Rest_Schema\Parser\Argument_Parser`

---

## Static Methods

### `Argument_Parser::for_route( Argument ...$arguments ): array`

Accepts multiple arguments and returns a merged keyed array suitable for the `args` parameter of `register_rest_route()`.

```php
use PinkCrab\WP_Rest_Schema\Argument\String_Type;
use PinkCrab\WP_Rest_Schema\Argument\Integer_Type;
use PinkCrab\WP_Rest_Schema\Parser\Argument_Parser;

register_rest_route( 'my/v1', '/search', array(
    'methods'  => 'GET',
    'callback' => 'handle_search',
    'args'     => Argument_Parser::for_route(
        String_Type::field( 'query' )->required()->min_length( 1 ),
        Integer_Type::field( 'page' )->minimum( 1 )->default( 1 ),
        String_Type::field( 'order' )->expected( 'asc', 'desc' )->default( 'desc' )
    ),
) );
```

**Output format:**

```php
array(
    'query' => array(
        'type'      => 'string',
        'required'  => true,
        'minLength' => 1,
    ),
    'page' => array(
        'type'    => 'integer',
        'minimum' => 1,
        'default' => 1,
    ),
    'order' => array(
        'type'    => 'string',
        'enum'    => array( 'asc', 'desc' ),
        'default' => 'desc',
    ),
)
```

---

### `Argument_Parser::for_meta_data( Argument $argument ): array`

Returns the array format suitable for the `schema` key inside `show_in_rest` when using `register_post_meta()`.

```php
register_post_meta( 'post', 'subtitle', array(
    'type'         => 'string',
    'single'       => true,
    'show_in_rest' => array(
        'schema' => Argument_Parser::for_meta_data(
            String_Type::field( 'subtitle' )
                ->max_length( 200 )
                ->description( 'An optional subtitle.' )
        ),
    ),
) );
```

**Output format:**

```php
array(
    'type'        => 'string',
    'maxLength'   => 200,
    'description' => 'An optional subtitle.',
)
```

---

### `Argument_Parser::as_array( Argument $argument ): array`

Returns a keyed array where the argument's key is the array key. This is the most common format.

```php
$result = Argument_Parser::as_array(
    String_Type::field( 'name' )->required()
);
```

**Output format:**

```php
array(
    'name' => array(
        'type'     => 'string',
        'required' => true,
    ),
)
```

---

### `Argument_Parser::as_single( Argument $argument ): array`

Returns just the argument's properties without the outer key. Used internally by the object and array parsers when building nested schemas.

```php
$result = Argument_Parser::as_single(
    String_Type::field( 'name' )->required()
);
```

**Output format:**

```php
array(
    'type'     => 'string',
    'required' => true,
)
```

---

### `Argument_Parser::as_list( Argument $argument ): array`

Returns the argument as a flat list (same as `as_single()`). This is what `for_meta_data()` calls internally.

```php
$result = Argument_Parser::as_list(
    Integer_Type::field( 'count' )->minimum( 0 )
);
```

---

## Parsed Schema Keywords

The parser maps builder methods to WordPress schema keywords:

| Builder Method | Schema Key |
|----------------|------------|
| `type()` | `type` |
| `format()` | `format` |
| `description()` | `description` |
| `name()` | `name` |
| `default()` | `default` |
| `required()` | `required` |
| `readonly()` | `readonly` |
| `title()` | `title` |
| `expected()` | `enum` |
| `context()` | `context` |
| `validation()` | `validate_callback` |
| `sanitization()` | `sanitize_callback` |
| `min_length()` | `minLength` |
| `max_length()` | `maxLength` |
| `pattern()` | `pattern` |
| `minimum()` | `minimum` |
| `maximum()` | `maximum` |
| `exclusive_minimum()` | `exclusiveMinimum` |
| `exclusive_maximum()` | `exclusiveMaximum` |
| `multiple_of()` | `multipleOf` |
| `*_item()` | `items` |
| `min_items()` | `minItems` |
| `max_items()` | `maxItems` |
| `unique_items()` | `uniqueItems` |
| `*_property()` | `properties` |
| `additional_properties()` | `additionalProperties` |
| `*_pattern_property()` | `patternProperties` |
| `min_properties()` | `minProperties` |
| `max_properties()` | `maxProperties` |
| `any_of()` | `anyOf` |
| `one_of()` | `oneOf` |
| `all_of()` | `allOf` |

---

## Which Method to Use

| WordPress Context | Parser Method |
|-------------------|---------------|
| `register_rest_route()` `args` | `Argument_Parser::for_route()` |
| `register_post_meta()` `show_in_rest.schema` | `Argument_Parser::for_meta_data()` |
| `WP_REST_Controller::get_item_schema()` | Use [Schema builder](Schema) instead |
| Custom / manual usage | `Argument_Parser::as_array()` or `as_single()` |

---

## Examples

### Multiple route arguments

```php
$args = Argument_Parser::for_route(
    String_Type::field( 'search' )
        ->required()
        ->min_length( 1 )
        ->sanitization( 'sanitize_text_field' ),
    Integer_Type::field( 'page' )
        ->minimum( 1 )
        ->default( 1 ),
    Integer_Type::field( 'per_page' )
        ->minimum( 1 )
        ->maximum( 100 )
        ->default( 10 ),
    String_Type::field( 'orderby' )
        ->expected( 'date', 'title', 'relevance' )
        ->default( 'relevance' ),
    String_Type::field( 'order' )
        ->expected( 'asc', 'desc' )
        ->default( 'desc' )
);
```

### Object meta data

```php
register_post_meta( 'product', 'dimensions', array(
    'type'         => 'object',
    'single'       => true,
    'show_in_rest' => array(
        'schema' => Argument_Parser::for_meta_data(
            Object_Type::field( 'dimensions' )
                ->number_property( 'width', fn( Number_Type $n ) => $n->minimum( 0 ) )
                ->number_property( 'height', fn( Number_Type $n ) => $n->minimum( 0 ) )
                ->number_property( 'depth', fn( Number_Type $n ) => $n->minimum( 0 ) )
                ->additional_properties( false )
        ),
    ),
) );
```

### Array meta data

```php
register_post_meta( 'post', 'gallery', array(
    'type'         => 'array',
    'single'       => true,
    'show_in_rest' => array(
        'schema' => Argument_Parser::for_meta_data(
            Array_Type::field( 'gallery' )
                ->object_item( function( Object_Type $o ) {
                    return $o
                        ->integer_property( 'id', fn( Integer_Type $i ) => $i->minimum( 1 ) )
                        ->string_property( 'url', fn( String_Type $s ) => $s->format( String_Type::FORMAT_URI ) )
                        ->string_property( 'alt' );
                } )
                ->min_items( 1 )
        ),
    ),
) );
```
