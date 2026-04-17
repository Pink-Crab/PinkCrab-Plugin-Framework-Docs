# Custom Field

Renders arbitrary HTML content with the full field treatment: wrapper div, label, notifications, style classes, before/after content.

Use this when you need a custom-rendered field (colour pickers, date pickers, WYSIWYG editors, etc.) that still participates in the form's wrapper/label/notification system.

**Class:** `PinkCrab\Form_Components\Element\Custom_Field`  
**Make helper:** `Make::custom( 'name', fn(Custom_Field $f) => $f->... )`

---

## Basic Usage

```php
$this->component( new Custom_Field_Component(
    Custom_Field::make( 'bio' )
        ->label( 'Biography' )
        ->content( '<div class="wysiwyg-editor" data-field="bio">Editor loads here</div>' )
) );
```

---

## Using Make Helper

```php
use PinkCrab\Form_Components\Util\Make;

$this->component( Make::custom( 'widget', fn( $f ) => $f
    ->label( 'My Widget' )
    ->content( '<div class="custom-widget">...</div>' )
) );
```

---

## Methods

### content( string $html )

Sets the HTML content string to render in the field slot.

```php
Custom_Field::make( 'editor' )
    ->label( 'Content' )
    ->content( '<textarea class="wp-editor">Hello</textarea>' )
```

### content_callback( callable $callback )

Sets a callable that receives the field instance and returns HTML. This takes priority over `content()` if both are set.

```php
Custom_Field::make( 'dynamic' )
    ->label( 'Dynamic Field' )
    ->content_callback( function( Custom_Field $field ) {
        return sprintf(
            '<input type="text" name="%s" value="%s" class="custom-input">',
            esc_attr( $field->get_name() ),
            esc_attr( $field->get_value() ?? '' )
        );
    } )
```

### get_content()

Returns the rendered content. If a callback is set, it is called. Otherwise returns the string content.

### has_content()

Returns `true` if content (string or callback) has been set.

---

## Content Filtering (KSES)

By default, content is filtered through `wp_kses_post()` which strips `<script>`, `<style>`, `<template>` tags and unrecognised attributes. You can customise this behaviour.

### kses_rules( array $rules )

Sets custom allowed HTML rules. Uses `wp_kses()` with the provided rules instead of `wp_kses_post()`.

```php
Custom_Field::make( 'rich_editor' )
    ->content( '<div class="editor"><template id="tmpl">...</template></div>' )
    ->kses_rules( array(
        'div'      => array( 'class' => true, 'id' => true, 'data-*' => true ),
        'template' => array( 'id' => true ),
        'input'    => array( 'type' => true, 'name' => true, 'value' => true ),
        'span'     => array( 'class' => true ),
    ) )
```

### disable_kses()

Disables kses filtering entirely. Content is rendered as-is. Use with caution - only when you trust the content source.

```php
Custom_Field::make( 'trusted' )
    ->content( '<script>initWidget();</script><div id="widget"></div>' )
    ->disable_kses()
```

### enable_kses()

Re-enables kses filtering after it has been disabled.

### is_kses_enabled()

Returns `true` if kses filtering is active.

### get_kses_rules()

Returns the custom rules array, or `null` if using the default `wp_kses_post`.

---

## Shared Methods

Custom_Field extends Field and uses the Label, Single_Value, and Notification traits, so it supports all common field methods:

### label( string $label )

Sets the visible label text above the field.

```php
Custom_Field::make( 'picker' )
    ->label( 'Choose a colour' )
    ->content( '<input type="color" name="picker">' )
```

### set_existing( mixed $value )

Sets the current value on the field.

```php
Custom_Field::make( 'colour' )
    ->set_existing( '#ff0000' )
```

### error_notification( string $message )

Displays an error message below the field.

```php
Custom_Field::make( 'widget' )
    ->label( 'Widget' )
    ->content( '<div class="widget"></div>' )
    ->error_notification( 'Widget configuration is invalid.' )
```

### pre_description( string $description )

Sets a description or hint displayed before the custom content.

```php
Custom_Field::make( 'preview' )
    ->label( 'Preview' )
    ->pre_description( 'This is a preview of your content.' )
```

### post_description( string $description )

Sets a description or help text displayed after the custom content, before any notification.

```php
Custom_Field::make( 'preview' )
    ->label( 'Preview' )
    ->post_description( 'Content will be sanitized on save.' )
```

### before( string $html ) / after( string $html )

HTML content before or after the field within the wrapper.

### show_wrapper( bool $show = true )

Controls whether the wrapping `<div>` is rendered.

### id( string $id )

Sets a custom HTML `id` on the field element.

### wrapper_id( string $id )

Sets a custom HTML `id` on the wrapper div.

### add_class( string $class ) / add_wrapper_class( string $class )

Adds CSS classes to the field or wrapper elements.

### attribute( string $key, mixed $value )

Sets an arbitrary HTML attribute.

### sanitizer( callable $fn )

Sets a sanitization callback for `set_existing()`.

### validator( Validator $validator )

Sets a Respect\Validation validator for server-side validation.

### style( Style $style )

Sets a custom style, overriding the default.

---

## Traits

| Trait | Methods |
|-------|---------|
| Label | `label()`, `get_label()`, `has_label()` |
| Single_Value | `value()`, `get_value()`, `has_value()` |
| Notification | `error_notification()`, `warning_notification()`, `success_notification()`, `info_notification()` |
| Description | `pre_description()`, `post_description()`, `get_pre_description()`, `get_post_description()`, `has_pre_description()`, `has_post_description()` |

---

## Comparison with Raw_HTML

| Feature | Raw_HTML | Custom_Field |
|---------|----------|--------------|
| Wrapper div | No | Yes |
| Label | No | Yes |
| Notifications | No | Yes |
| Style classes | No | Yes |
| Before/after in wrapper | No | Yes |
| KSES filtering | `wp_kses_post` only | Configurable or disabled |
| Participates in form validation | No | Yes |
| Sanitizer support | No | Yes |
| Use case | Simple HTML snippets (headings, dividers) | Custom interactive fields |
