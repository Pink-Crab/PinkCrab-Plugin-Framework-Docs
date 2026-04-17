# PinkCrab Form Components

A Perique framework library for rendering form fields as View Components. Build forms with a fluent PHP API, automatic HTML rendering, built-in sanitization and validation.

---

## Installation

```bash
composer require pinkcrab/form-components
```

Register the module in your Perique bootstrap:

```php
use PinkCrab\Form_Components\Module\Form_Components;

( new App_Factory() )
    ->default_setup()
    ->module( Form_Components::class )
    ->boot();
```

---

## Quick Start

Render a field in any Perique view template:

```php
use PinkCrab\Form_Components\Component\Field\Input_Component;
use PinkCrab\Form_Components\Element\Field\Input\Text;

$this->component( new Input_Component(
    Text::make( 'username' )
        ->label( 'Username' )
        ->placeholder( 'Enter your username' )
        ->required( true )
) );
```

Or use the `Make` helper for a more concise syntax:

```php
use PinkCrab\Form_Components\Util\Make;

$this->component( Make::text( 'username', fn( $f ) => $f
    ->label( 'Username' )
    ->placeholder( 'Enter your username' )
    ->required( true )
) );
```

---

## Field Types

### Text Inputs

| Field | Description |
|-------|-------------|
| [Text](fields/text) | Standard text input |
| [Email](fields/email) | Email with browser validation |
| [Password](fields/password) | Masked password input |
| [Search](fields/search) | Search input |
| [Tel](fields/tel) | Telephone number |
| [URL](fields/url) | URL with browser validation |

### Numeric Inputs

| Field | Description |
|-------|-------------|
| [Number](fields/number) | Numeric input with spinners |
| [Range](fields/range) | Slider control |

### Date & Time Inputs

| Field | Description |
|-------|-------------|
| [Date](fields/date) | Date picker |
| [Time](fields/time) | Time picker |
| [Datetime](fields/datetime) | Combined date and time |
| [Month](fields/month) | Month and year picker |
| [Week](fields/week) | Week picker |

### Special Inputs

| Field | Description |
|-------|-------------|
| [Color](fields/color) | Colour picker |
| [File](fields/file) | File upload |
| [Hidden](fields/hidden) | Hidden value field |
| [Checkbox](fields/checkbox) | Single checkbox |
| [Radio](fields/radio) | Single radio button |

### Selection Groups

| Field | Description |
|-------|-------------|
| [Select](fields/select) | Dropdown / multi-select |
| [Checkbox Group](fields/checkbox-group) | Multiple checkboxes |
| [Radio Group](fields/radio-group) | Multiple radio buttons |

### Other Elements

| Element | Description |
|---------|-------------|
| [Textarea](fields/textarea) | Multi-line text |
| [Button](fields/button) | Button element |

### Structural Elements

| Element | Description |
|---------|-------------|
| [Form](fields/form) | Form wrapper with method, action, nonce |
| [Fieldset](fields/fieldset) | Fieldset with optional legend |

---

## Architecture

- [Make Utility](make) - Factory helper for one-line field creation
- [Style System](style) - Customising CSS classes
- [Sanitization](sanitize) - Built-in sanitizers
- [Components](components) - How elements become renderable components
