---
description: >-
  A fluent PHP builder for WordPress REST API JSON schemas. Converts verbose
  nested array definitions into clean, chainable method calls.
---

# WP Rest Schema Builder

A fluent PHP builder for WordPress REST API JSON schemas. Converts verbose nested array definitions into clean, chainable method calls.

[![Latest Stable Version](http://poser.pugx.org/pinkcrab/wp-rest-schema/v)](https://packagist.org/packages/pinkcrab/wp-rest-schema) [![Total Downloads](http://poser.pugx.org/pinkcrab/wp-rest-schema/downloads)](https://packagist.org/packages/pinkcrab/wp-rest-schema) [![Latest Unstable Version](http://poser.pugx.org/pinkcrab/wp-rest-schema/v/unstable)](https://packagist.org/packages/pinkcrab/wp-rest-schema) [![License](http://poser.pugx.org/pinkcrab/wp-rest-schema/license)](https://packagist.org/packages/pinkcrab/wp-rest-schema) [![PHP Version Require](http://poser.pugx.org/pinkcrab/wp-rest-schema/require/php)](https://packagist.org/packages/pinkcrab/wp-rest-schema)
![GitHub contributors](https://img.shields.io/github/contributors/Pink-Crab/WP_Rest_Schema?label=Contributors)
![GitHub issues](https://img.shields.io/github/issues-raw/Pink-Crab/WP_Rest_Schema)
[![WordPress 6.6 Test Suite [PHP8.0-8.4]](https://github.com/Pink-Crab/WP_Rest_Schema/actions/workflows/WP_6_6.yaml/badge.svg)](https://github.com/Pink-Crab/WP_Rest_Schema/actions/workflows/WP_6_6.yaml)
[![WordPress 6.7 Test Suite [PHP8.0-8.4]](https://github.com/Pink-Crab/WP_Rest_Schema/actions/workflows/WP_6_7.yaml/badge.svg)](https://github.com/Pink-Crab/WP_Rest_Schema/actions/workflows/WP_6_7.yaml)
[![WordPress 6.8 Test Suite [PHP8.0-8.4]](https://github.com/Pink-Crab/WP_Rest_Schema/actions/workflows/WP_6_8.yaml/badge.svg)](https://github.com/Pink-Crab/WP_Rest_Schema/actions/workflows/WP_6_8.yaml)
[![WordPress 6.9 Test Suite [PHP8.0-8.4]](https://github.com/Pink-Crab/WP_Rest_Schema/actions/workflows/WP_6_9.yaml/badge.svg)](https://github.com/Pink-Crab/WP_Rest_Schema/actions/workflows/WP_6_9.yaml)
[![codecov](https://codecov.io/gh/Pink-Crab/WP_Rest_Schema/graph/badge.svg?token=KER9nANRi2)](https://codecov.io/gh/Pink-Crab/WP_Rest_Schema)
[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/Pink-Crab/WP_Rest_Schema/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/Pink-Crab/WP_Rest_Schema/?branch=master)

****

## Why?

Defining valid JSON rest schema in WordPress means writing deeply nested associative arrays. It's easy to make mistakes, hard to read, and painful to maintain. This library provides a fully fluent, type-safe builder API that mirrors every keyword from WordPress's `rest_get_allowed_schema_keywords()`.

**Instead of this:**

```php
register_post_meta( 'post', 'fixed_in', array(
    'type'         => 'string',
    'single'       => true,
    'show_in_rest' => array(
        'schema' => array(
            'type'        => 'string',
            'minLength'   => 10,
            'maxLength'   => 42,
            'required'    => true,
            'description' => 'Required string, 10-42 chars.',
        ),
    ),
) );
```

**Write this:**

```php
register_post_meta( 'post', 'fixed_in', array(
    'type'         => 'string',
    'single'       => true,
    'show_in_rest' => array(
        'schema' => Argument_Parser::for_meta_data(
            String_Type::field( 'fixed_in' )
                ->min_length( 10 )
                ->max_length( 42 )
                ->required()
                ->description( 'Required string, 10-42 chars.' )
        ),
    ),
) );
```

****

## Setup

```bash
$ composer require pinkcrab/wp-rest-schema
```

**Requirements:** PHP >= 8.0 | WordPress >= 6.6

****

## Available Types

| Type | Class | Type-Specific Methods |
|------|-------|----------------------|
| String | `String_Type` | `min_length()`, `max_length()`, `pattern()` |
| Integer | `Integer_Type` | `minimum()`, `maximum()`, `exclusive_minimum()`, `exclusive_maximum()`, `multiple_of()` |
| Number | `Number_Type` | `minimum()`, `maximum()`, `exclusive_minimum()`, `exclusive_maximum()`, `multiple_of()` |
| Boolean | `Boolean_Type` | *No type-specific methods* |
| Null | `Null_Type` | *No type-specific methods* |
| Array | `Array_Type` | `string_item()`, `number_item()`, `integer_item()`, `boolean_item()`, `array_item()`, `null_item()`, `object_item()`, `min_items()`, `max_items()`, `unique_items()`. Plus item-relationship toggles `any_of()` / `one_of()` (no args — change how multiple items combine; default is `allOf`) |
| Object | `Object_Type` | `string_property()`, `number_property()`, `integer_property()`, `boolean_property()`, `null_property()`, `array_property()`, `object_property()`, `additional_properties(bool)`, `additional_properties_schema(Argument)`, `min_properties()`, `max_properties()`, `required_properties(string ...)`, plus typed pattern-property variants `string_pattern_property()` / `number_pattern_property()` / … / `object_pattern_property()` (no flat `pattern_properties()` method) |
| OneOf | `One_Of_Type` | `variant( Argument )` — schema-root `oneOf` combinator (exactly one variant must match) |
| AnyOf | `Any_Of_Type` | `variant( Argument )` — schema-root `anyOf` combinator (any variant matches) |

> `Boolean_Type` and `Null_Type` inherit all shared methods from the base `Argument` class but have no additional type-specific methods. See [Argument](docs/Argument) for the full shared API.

All types share a common set of methods for `description`, `default`, `required`, `readonly`, `title`, `format`, `expected` (enum, accepts mixed), `context`, `validation`, `sanitization`, `arg_options` (WP controller pass-through), and `union types`. See [Argument (Base Class)](docs/Argument) for details.

****

## Quick Examples

### Post Meta Schema

```php
use PinkCrab\WP_Rest_Schema\Argument\String_Type;
use PinkCrab\WP_Rest_Schema\Parser\Argument_Parser;

register_post_meta( 'post', 'color', array(
    'type'         => 'string',
    'single'       => true,
    'show_in_rest' => array(
        'schema' => Argument_Parser::for_meta_data(
            String_Type::field( 'color' )
                ->format( String_Type::FORMAT_HEX )
                ->required()
                ->description( 'A hex colour value.' )
        ),
    ),
) );
```

### REST Route Arguments

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

### Full Item Schema (for `WP_REST_Controller::get_item_schema()`)

```php
use PinkCrab\WP_Rest_Schema\Schema;
use PinkCrab\WP_Rest_Schema\Argument\String_Type;
use PinkCrab\WP_Rest_Schema\Argument\Integer_Type;

public function get_item_schema() {
    return Schema::on( 'post' )
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
        ->string_property( 'status', function( String_Type $s ) {
            return $s->expected( 'publish', 'draft', 'pending' )
                ->context( 'view', 'edit' );
        } )
        ->additional_properties( false )
        ->to_array();
}
```

### Nested Object with Array

```php
use PinkCrab\WP_Rest_Schema\Argument\Object_Type;
use PinkCrab\WP_Rest_Schema\Argument\String_Type;
use PinkCrab\WP_Rest_Schema\Argument\Integer_Type;
use PinkCrab\WP_Rest_Schema\Argument\Array_Type;

Object_Type::field( 'metadata' )
    ->string_property( 'title', fn( String_Type $t ) => $t->required() )
    ->integer_property( 'score', fn( Integer_Type $i ) => $i->minimum( 0 )->maximum( 100 ) )
    ->array_property( 'tags', fn( Array_Type $a ) => $a->string_item()->unique_items() )
    ->additional_properties( false )
```

****

## Documentation

| Page | Description |
|------|-------------|
| [Argument (Base Class)](docs/Argument) | Shared methods available on all types: description, default, required, readonly, title, format, expected, context, validation, sanitization, union types, and all constants |
| [String Type](docs/String-Type) | `String_Type` — minLength, maxLength, pattern |
| [Number Types](docs/Number-Type) | `Number_Type` and `Integer_Type` — minimum, maximum, exclusiveMinimum, exclusiveMaximum, multipleOf |
| [Array Type](docs/Array-Type) | `Array_Type` — typed items, minItems, maxItems, uniqueItems, element relationships |
| [Object Type](docs/Object-Type) | `Object_Type` — properties, additionalProperties, patternProperties, minProperties, maxProperties, element relationships |
| [Schema Builder](docs/Schema) | Top-level `Schema` class for building `get_item_schema()` output |
| [Parser](docs/Parser) | `Argument_Parser` — converting builders to arrays for WordPress |

****

## Perique Integration

This library can be used with the [Perique Registerable](https://github.com/Pink-Crab/Perique-Registerables) library:

```php
$meta_data = ( new Meta_Data( 'fixed_in' ) )
    ->post_type( 'post' )
    ->type( 'string' )
    ->rest_schema(
        Argument_Parser::for_meta_data(
            String_Type::field( 'fixed_in' )
                ->min_length( 10 )
                ->max_length( 42 )
                ->required()
                ->description( 'Required string, 10-42 chars.' )
        )
    );
```

****

## License

### MIT License

http://www.opensource.org/licenses/mit-license.html

## Change Log

* 1.0.0 - Combinators (`One_Of_Type` / `Any_Of_Type`), null/falsy defaults, array items last-wins, `arg_options`, `required_properties`, mixed enums, `Schema::get_context_param()`, output-keys canary test; deprecated `name()` and `all_of()`.
* 1.0.0-RC1 - Added Schema builder, readonly/title support, field() alias, for_route() helper, additionalProperties redesign, FORMAT_URI fix, PHPStan level 9, WPCS 3, PHP 8.0+/WP 6.6+
* 0.1.0 - Initial version
