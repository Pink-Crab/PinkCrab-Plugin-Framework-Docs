# String Type

`String_Type` is used to define string schema arguments. It extends [Argument](Argument) with string-specific validation constraints.

**Namespace:** `PinkCrab\WP_Rest_Schema\Argument\String_Type`

---

## Creating a String Type

```php
use PinkCrab\WP_Rest_Schema\Argument\String_Type;

$arg = String_Type::field( 'name' );

// With configuration callback
$arg = String_Type::field( 'name', function( String_Type $s ): String_Type {
    return $s->required()->min_length( 1 );
} );
```

The type is automatically set to `'string'`.

---

## Methods

### `min_length( int $min ): self`

Set the minimum string length. Maps to `minLength` in the schema.

```php
$arg = String_Type::field( 'username' )
    ->min_length( 3 );
```

### `max_length( int $max ): self`

Set the maximum string length. Maps to `maxLength` in the schema.

```php
$arg = String_Type::field( 'username' )
    ->max_length( 50 );
```

### `pattern( string $pattern ): self`

Set a regular expression pattern the string must match. Maps to `pattern` in the schema.

```php
$arg = String_Type::field( 'slug' )
    ->pattern( '^[a-z0-9-]+$' );
```

---

## Getter Methods

| Setter | Getter | Returns |
|--------|--------|---------|
| `min_length()` | `get_min_length()` | `?int` |
| `max_length()` | `get_max_length()` | `?int` |
| `pattern()` | `get_pattern()` | `?string` |

---

## Examples

### Required string with length constraints

```php
$name = String_Type::field( 'display_name' )
    ->required()
    ->min_length( 1 )
    ->max_length( 100 )
    ->description( 'The user display name.' );
```

### Email field

```php
$email = String_Type::field( 'email' )
    ->format( String_Type::FORMAT_EMAIL )
    ->required()
    ->description( 'A valid email address.' )
    ->sanitization( 'sanitize_email' )
    ->validation( 'is_email' );
```

### Enum / expected values

```php
$status = String_Type::field( 'status' )
    ->expected( 'publish', 'draft', 'pending', 'trash' )
    ->default( 'draft' )
    ->context( 'view', 'edit' );
```

### URL field

```php
$url = String_Type::field( 'website' )
    ->format( String_Type::FORMAT_URI )
    ->description( 'A valid URI.' )
    ->sanitization( 'esc_url_raw' );
```

### Pattern-matched slug

```php
$slug = String_Type::field( 'slug' )
    ->pattern( '^[a-z0-9]+(?:-[a-z0-9]+)*$' )
    ->min_length( 1 )
    ->max_length( 200 )
    ->description( 'URL-friendly identifier.' );
```

### Nullable string (union type)

```php
$bio = String_Type::field( 'bio' )
    ->union_with_type( 'null' )
    ->max_length( 500 )
    ->description( 'User biography, or null if not set.' );
```

### Post meta registration

```php
use PinkCrab\WP_Rest_Schema\Parser\Argument_Parser;

register_post_meta( 'post', 'subtitle', array(
    'type'         => 'string',
    'single'       => true,
    'show_in_rest' => array(
        'schema' => Argument_Parser::for_meta_data(
            String_Type::field( 'subtitle' )
                ->max_length( 200 )
                ->description( 'An optional subtitle for the post.' )
        ),
    ),
) );
```
