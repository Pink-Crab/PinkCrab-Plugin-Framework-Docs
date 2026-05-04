# Tel Input

Renders `<input type="tel">`. On mobile devices, the browser shows a telephone keyboard. Default sanitizer: `Sanitize::TEXT`.

**Class:** `PinkCrab\Form_Components\Element\Field\Input\Tel`  
**Make helper:** `Make::tel( 'name', fn(Tel $f) => $f->... )`

---

## Basic Usage

```php
$this->component( new Input_Component(
		Tel::make( 'phone' )
			->label( 'Phone Number' )
			->placeholder( '+44 7700 900000' )
	) )
```

![Basic](screenshots/tel/basic.png)

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_phone" class="pc-form__element pc-form__element--tel_input">
    <label for="phone" class="pc-form__label">Phone Number</label>
        <input type="tel" name="phone" class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input" list="_phone__list" placeholder="+44 7700 900000" />
    </div>
```
</details>

---

## Using Make Helper

```php
use PinkCrab\Form_Components\Util\Make;

$this->component( Make::tel( 'phone', fn( $f ) => $f
    ->label( 'Phone Number' )
    ->placeholder( '+44 7700 900000' )
) );
```

---

## Methods

### label( string $label )

Sets the visible label text above the input.

```php
Tel::make( 'phone' )->label( 'Phone Number' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_phone" class="pc-form__element pc-form__element--tel_input">
    <label for="phone" class="pc-form__label">Phone Number</label>
    <input type="tel" name="phone"
        class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input"
    />
</div>
```
</details>

### set_existing( mixed $value )

Sets the current value. Runs through the sanitizer if one is set.

```php
Tel::make( 'phone' )
    ->label( 'Phone Number' )
    ->set_existing( '+44 7700 900000' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_phone" class="pc-form__element pc-form__element--tel_input">
    <label for="phone" class="pc-form__label">Phone Number</label>
    <input type="tel" name="phone"
        class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input"
        value="+44 7700 900000"
    />
</div>
```
</details>

### placeholder( string $text )

Placeholder text shown when the field is empty.

```php
Tel::make( 'phone' )
    ->label( 'Phone Number' )
    ->placeholder( '+44 7700 900000' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_phone" class="pc-form__element pc-form__element--tel_input">
    <label for="phone" class="pc-form__label">Phone Number</label>
    <input type="tel" name="phone"
        class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input"
        placeholder="+44 7700 900000"
    />
</div>
```
</details>

### required( bool $required = true )

Marks the field as required. The label displays a `*` indicator via CSS.

```php
Tel::make( 'phone' )
    ->label( 'Phone Number' )
    ->required( true )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_phone" class="pc-form__element pc-form__element--tel_input">
    <label for="phone" class="pc-form__label">Phone Number</label>
    <input type="tel" name="phone"
        class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input"
        required=""
    />
</div>
```
</details>

### disabled( bool $disabled = true )

Disables the input. Value is visible but cannot be changed or submitted.

```php
Tel::make( 'locked_phone' )
    ->label( 'Phone Number' )
    ->set_existing( '+44 7700 900000' )
    ->disabled( true )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_locked_phone" class="pc-form__element pc-form__element--tel_input">
    <label for="locked_phone" class="pc-form__label">Phone Number</label>
    <input type="tel" name="locked_phone"
        class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input"
        disabled="" value="+44 7700 900000"
    />
</div>
```
</details>

### readonly( bool $readonly = true )

Makes the field read-only. Value can be selected and copied but not changed.

```php
Tel::make( 'readonly_phone' )
    ->label( 'Phone Number' )
    ->set_existing( '+44 7700 900000' )
    ->readonly( true )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_readonly_phone" class="pc-form__element pc-form__element--tel_input">
    <label for="readonly_phone" class="pc-form__label">Phone Number</label>
    <input type="tel" name="readonly_phone"
        class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input"
        readonly="" value="+44 7700 900000"
    />
</div>
```
</details>

### pattern( string $regex )

HTML5 validation pattern (regex) the value must match.

```php
Tel::make( 'phone' )
    ->label( 'Phone Number' )
    ->pattern( '\+?[0-9\s\-]+' )
    ->placeholder( '+44 7700 900000' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_phone" class="pc-form__element pc-form__element--tel_input">
    <label for="phone" class="pc-form__label">Phone Number</label>
    <input type="tel" name="phone"
        class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input"
        pattern="\+?[0-9\s\-]+" placeholder="+44 7700 900000"
    />
</div>
```
</details>

### minlength( int $min ) / maxlength( int $max )

Minimum and maximum character length constraints.

```php
Tel::make( 'phone' )
    ->label( 'Phone Number' )
    ->minlength( 7 )
    ->maxlength( 15 )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_phone" class="pc-form__element pc-form__element--tel_input">
    <label for="phone" class="pc-form__label">Phone Number</label>
    <input type="tel" name="phone"
        class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input"
        minlength="7" maxlength="15"
    />
</div>
```
</details>

### size( int $size )

Visible width of the input in characters.

```php
Tel::make( 'phone' )
    ->label( 'Phone Number' )
    ->size( 20 )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_phone" class="pc-form__element pc-form__element--tel_input">
    <label for="phone" class="pc-form__label">Phone Number</label>
    <input type="tel" name="phone"
        class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input"
        size="20"
    />
</div>
```
</details>

### autocomplete( string $value )

HTML `autocomplete` attribute to help browsers autofill.

```php
Tel::make( 'phone' )
    ->label( 'Phone Number' )
    ->autocomplete( 'tel' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_phone" class="pc-form__element pc-form__element--tel_input">
    <label for="phone" class="pc-form__label">Phone Number</label>
    <input type="tel" name="phone"
        class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input"
        autocomplete="tel"
    />
</div>
```
</details>

Common values:

| Value | Description |
|-------|-------------|
| `off` | Disable autocomplete |
| `on` | Enable autocomplete (browser decides) |
| `name` | Full name |
| `given-name` | First name |
| `family-name` | Last name |
| `email` | Email address |
| `username` | Username |
| `new-password` | New password (password managers) |
| `current-password` | Current password |
| `organization` | Company/organisation name |
| `street-address` | Street address |
| `address-line1` | Address line 1 |
| `address-line2` | Address line 2 |
| `address-level2` | City |
| `address-level1` | State/province/region |
| `country` | Country code |
| `country-name` | Country name |
| `postal-code` | Postcode / ZIP |
| `tel` | Full phone number |
| `tel-national` | Phone without country code |
| `url` | URL |
| `bday` | Full date of birth |
| `bday-day` | Day of birth |
| `bday-month` | Month of birth |
| `bday-year` | Year of birth |
| `sex` | Gender |
| `cc-name` | Cardholder name |
| `cc-number` | Card number |
| `cc-exp` | Card expiry |
| `cc-csc` | Card security code |

### inputmode( string $mode )

Hints to mobile browsers which keyboard to display.

```php
Tel::make( 'phone' )
    ->label( 'Phone Number' )
    ->inputmode( 'tel' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_phone" class="pc-form__element pc-form__element--tel_input">
    <label for="phone" class="pc-form__label">Phone Number</label>
    <input type="tel" name="phone"
        class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input"
        inputmode="tel"
    />
</div>
```
</details>

Valid values:

| Value | Keyboard |
|-------|----------|
| `none` | No virtual keyboard |
| `text` | Standard text keyboard (default) |
| `decimal` | Numbers with decimal point |
| `numeric` | Numbers only |
| `tel` | Telephone keypad |
| `search` | Search-optimised keyboard |
| `email` | Email-optimised keyboard |
| `url` | URL-optimised keyboard |

### spellcheck( string $value )

Enables or disables browser spell checking.

```php
Tel::make( 'phone' )
    ->label( 'Phone Number' )
    ->spellcheck( 'false' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_phone" class="pc-form__element pc-form__element--tel_input">
    <label for="phone" class="pc-form__label">Phone Number</label>
    <input type="tel" name="phone"
        class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input"
        spellcheck="false"
    />
</div>
```
</details>

### datalist_items( array $items )

Autocomplete suggestions via an HTML `<datalist>` element.

```php
Tel::make( 'phone' )
    ->label( 'Phone Number' )
    ->datalist_items( array( '+44 7700 900000', '+1 555 123 4567', '+33 1 23 45 67 89' ) )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_phone" class="pc-form__element pc-form__element--tel_input">
    <label for="phone" class="pc-form__label">Phone Number</label>
    <input type="tel" name="phone"
        class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input"
        list="_phone__list"
    />
    <datalist id="_phone__list">
        <option value="+44 7700 900000"></option>
        <option value="+1 555 123 4567"></option>
        <option value="+33 1 23 45 67 89"></option>
    </datalist>
</div>
```
</details>

### error_notification( string $message )

Displays an error message below the field.

```php
Tel::make( 'phone_error' )
    ->label( 'Phone Number' )
    ->required( true )
    ->error_notification( 'Phone number is required.' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_phone_error" class="pc-form__element pc-form__element--tel_input notification-error">
    <label for="phone_error" class="pc-form__label">Phone Number</label>
    <input type="tel" name="phone_error"
        class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input notification-error"
        required=""
    />
    <div class="pc-form__notification pc-form__notification--error">Phone number is required.</div>
</div>
```
</details>

### warning_notification( string $message )

Displays a warning message below the field.

```php
Tel::make( 'phone_warning' )
    ->label( 'Phone Number' )
    ->warning_notification( 'Number format may be incorrect.' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_phone_warning" class="pc-form__element pc-form__element--tel_input notification-warning">
    <label for="phone_warning" class="pc-form__label">Phone Number</label>
    <input type="tel" name="phone_warning"
        class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input notification-warning"
    />
    <div class="pc-form__notification pc-form__notification--warning">Number format may be incorrect.</div>
</div>
```
</details>

### success_notification( string $message )

Displays a success message below the field.

```php
Tel::make( 'phone_success' )
    ->label( 'Phone Number' )
    ->success_notification( 'Phone number verified.' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_phone_success" class="pc-form__element pc-form__element--tel_input notification-success">
    <label for="phone_success" class="pc-form__label">Phone Number</label>
    <input type="tel" name="phone_success"
        class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input notification-success"
    />
    <div class="pc-form__notification pc-form__notification--success">Phone number verified.</div>
</div>
```
</details>

### info_notification( string $message )

Displays an info message below the field.

```php
Tel::make( 'phone_info' )
    ->label( 'Phone Number' )
    ->info_notification( 'Include country code for international numbers.' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_phone_info" class="pc-form__element pc-form__element--tel_input notification-info">
    <label for="phone_info" class="pc-form__label">Phone Number</label>
    <input type="tel" name="phone_info"
        class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input notification-info"
    />
    <div class="pc-form__notification pc-form__notification--info">Include country code for international numbers.</div>
</div>
```
</details>

### pre_description( string $description )

Sets a description or hint displayed before the input.

```php
Tel::make( 'phone' )
    ->label( 'Phone Number' )
    ->pre_description( 'Include your country code.' )
```

### post_description( string $description )

Sets a description or help text displayed after the input, before any notification.

```php
Tel::make( 'phone' )
    ->label( 'Phone Number' )
    ->post_description( 'e.g. +44 7700 900000' )
```

### before( string $html ) / after( string $html )

HTML content before or after the input; renders whether or not the wrapper is shown.

```php
Tel::make( 'wrapped_tel' )
			->label( 'Phone' )
			->before( '<span style="color:#6b7280;font-size:13px;">Include country code</span>' )
			->after( '<span style="color:#6b7280;font-size:13px;">e.g. +44 7700 900000</span>' )
```

![before/after](screenshots/tel/before-after.png)

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_wrapped_tel" class="pc-form__element pc-form__element--tel_input">
    <span style="color:#6b7280;font-size:13px">Include country code</span>
        <label for="wrapped_tel" class="pc-form__label">Phone</label>
            <input type="tel" name="wrapped_tel" class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input" list="_wrapped_tel__list" />
            <span style="color:#6b7280;font-size:13px">e.g. +44 7700 900000</span>
            </div>
```
</details>

### id( string $id )

Sets a custom HTML `id` on the input element.

```php
Tel::make( 'phone' )
    ->id( 'my-custom-phone-id' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_phone" class="pc-form__element pc-form__element--tel_input">
    <input type="tel" name="phone" id="my-custom-phone-id"
        class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input"
    />
</div>
```
</details>

### wrapper_id( string $id )

Sets a custom HTML `id` on the wrapper div.

```php
Tel::make( 'phone' )
    ->wrapper_id( 'my-custom-wrapper-id' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="my-custom-wrapper-id" class="pc-form__element pc-form__element--tel_input">
    <input type="tel" name="phone"
        class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input"
    />
</div>
```
</details>

### data( string $key, string $value )

Adds a `data-*` attribute to the input.

```php
Tel::make( 'phone' )
    ->data( 'country', 'gb' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_phone" class="pc-form__element pc-form__element--tel_input">
    <input type="tel" name="phone"
        class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input"
        data-country="gb"
    />
</div>
```
</details>

### wrapper_data( string $key, string $value )

Adds a `data-*` attribute to the wrapper div.

```php
Tel::make( 'phone' )
    ->wrapper_data( 'section', 'contact' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_phone" class="pc-form__element pc-form__element--tel_input" data-section="contact">
    <input type="tel" name="phone"
        class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input"
    />
</div>
```
</details>

### add_class( string $class )

Adds a CSS class to the input element.

```php
Tel::make( 'phone' )
    ->add_class( 'my-input-class' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_phone" class="pc-form__element pc-form__element--tel_input">
    <input type="tel" name="phone"
        class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input my-input-class"
    />
</div>
```
</details>

### add_wrapper_class( string $class )

Adds a CSS class to the wrapper div.

```php
Tel::make( 'phone' )
    ->add_wrapper_class( 'my-wrapper-class' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_phone" class="pc-form__element pc-form__element--tel_input my-wrapper-class">
    <input type="tel" name="phone"
        class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input"
    />
</div>
```
</details>

### show_wrapper( bool $show = true )

Controls whether the wrapping `<div>` is rendered.

```php
Tel::make( 'bare' )
    ->show_wrapper( false )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<input type="tel" name="bare"
    class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input"
/>
```
</details>

### tabindex( int $index )

Sets the tab order of the input.

```php
Tel::make( 'phone' )
    ->tabindex( 4 )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_phone" class="pc-form__element pc-form__element--tel_input">
    <input type="tel" name="phone"
        class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input"
        tabindex="4"
    />
</div>
```
</details>

### attribute( string $key, mixed $value )

Sets an arbitrary HTML attribute on the input.

```php
Tel::make( 'phone' )
    ->attribute( 'aria-label', 'Enter your phone number' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_phone" class="pc-form__element pc-form__element--tel_input">
    <input type="tel" name="phone"
        class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input"
        aria-label="Enter your phone number"
    />
</div>
```
</details>

### attributes( array $attrs )

Sets multiple arbitrary HTML attributes at once.

```php
Tel::make( 'phone' )
    ->attributes( array(
        'title'    => 'Enter phone number',
        'tabindex' => '4',
    ) )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_phone" class="pc-form__element pc-form__element--tel_input">
    <input type="tel" name="phone"
        class="form-control tel-input pc-form__element__field pc-form__element__field--tel_input"
        title="Enter phone number" tabindex="4"
    />
</div>
```
</details>

### sanitizer( callable $fn )

Sets a sanitization callback applied when `set_existing()` is called. Default: `Sanitize::TEXT`. Accepts any `callable` - a built-in helper constant, a WordPress function name, a closure, or any invokable.

**Using a built-in helper:**

```php
use PinkCrab\Form_Components\Util\Sanitize;

Tel::make( 'phone' )
    ->sanitizer( Sanitize::TEXT )
    ->set_existing( '<script>alert("xss")</script>+44 7700 900000' ) // Stores: "+44 7700 900000"
```

**Using a custom callable:**

```php
Tel::make( 'phone' )
    ->sanitizer( function( $value ) {
        return preg_replace( '/[^0-9\+\s\-]/', '', $value );
    } )
    ->set_existing( 'call: +44 7700 900000!' ) // Stores: "+44 7700 900000"
```

**Using a WordPress function:**

```php
Tel::make( 'phone' )
    ->sanitizer( 'sanitize_text_field' )
    ->set_existing( $user_input )
```

**Built-in sanitizer helpers:**

| Constant | Function | Description |
|----------|----------|-------------|
| `Sanitize::TEXT` | `sanitize_text_field()` | Strips tags, removes extra whitespace |
| `Sanitize::TEXTAREA` | `sanitize_textarea_field()` | Like TEXT but preserves line breaks |
| `Sanitize::URL` | `esc_url_raw()` | Sanitises a URL for database storage |
| `Sanitize::EMAIL` | `sanitize_email()` | Strips invalid email characters |
| `Sanitize::HEX_COLOR` | `sanitize_hex_color()` | Validates hex colour (#fff or #ffffff) |
| `Sanitize::NUMBER` | Custom numeric parser | Parses to int or float |
| `Sanitize::NOOP` | Pass-through | No sanitization applied |

Each helper can also be called as a static method:

```php
$clean = Sanitize::text( '<b>Hello</b>' );    // "Hello"
$clean = Sanitize::email( 'bad@@email' );      // "bad@email"
$clean = Sanitize::number( '42.5abc' );         // 42.5
```

### validator( Validator $validator )

Sets a Respect\Validation validator for server-side validation.

```php
use Respect\Validation\Validator as v;

Tel::make( 'phone' )
    ->validator( v::phone() )
```

### style( Style $style )

Sets a custom style for the field, overriding the default.

```php
use PinkCrab\Form_Components\Style\Default_Style;

Tel::make( 'phone' )
    ->style( new Default_Style() )
```

---

## Traits

| Trait | Methods |
|-------|---------|
| Label | `label()`, `get_label()`, `has_label()` |
| Single_Value | `value()`, `get_value()`, `has_value()` |
| Placeholder | `placeholder()`, `get_placeholder()`, `has_placeholder()` |
| Required | `required()`, `is_required()` |
| Disabled | `disabled()`, `is_disabled()` |
| Read_Only | `readonly()`, `is_read_only()` |
| Pattern | `pattern()`, `get_pattern()`, `has_pattern()` |
| Datalist | `datalist_items()`, `get_datalist_key()`, `get_datalist_items()` |
| Length | `minlength()`, `maxlength()`, `get_min_length()`, `get_max_length()` |
| Size | `size()`, `get_size()`, `has_size()` |
| Autocomplete | `autocomplete()`, `get_autocomplete()`, `has_autocomplete()` |
| Input_Mode | `inputmode()`, `get_input_mode()`, `has_input_mode()` |
| Spellcheck | `spellcheck()`, `is_spellcheck()` |
| Description | `pre_description()`, `post_description()`, `get_pre_description()`, `get_post_description()`, `has_pre_description()`, `has_post_description()` |
| Notification | `error_notification()`, `warning_notification()`, `success_notification()`, `info_notification()` |
