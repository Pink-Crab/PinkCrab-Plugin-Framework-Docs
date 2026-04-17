# URL Input

Renders `<input type="url">`. The browser provides built-in URL format validation. Default sanitizer: `Sanitize::TEXT`.

**Class:** `PinkCrab\Form_Components\Element\Field\Input\Url`  
**Make helper:** `Make::url( 'name', fn(Url $f) => $f->... )`

---

## Basic Usage

```php
$this->component( new Input_Component(
		Url::make( 'website' )
			->label( 'Website' )
			->placeholder( 'https://example.com' )
	) )
```

![Basic](screenshots/url/basic.png)

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_website" class="pc-form__element pc-form__element--url_input">
    <label for="website" class="pc-form__label">Website</label>
        <input type="url" name="website" class="form-control url-input pc-form__element__field pc-form__element__field--url_input" list="_website__list" placeholder="https://example.com" />
    </div>
```
</details>

---

## Using Make Helper

```php
use PinkCrab\Form_Components\Util\Make;

$this->component( Make::url( 'website', fn( $f ) => $f
    ->label( 'Website' )
    ->placeholder( 'https://example.com' )
) );
```

---

## Methods

### label( string $label )

Sets the visible label text above the input.

```php
Url::make( 'website' )->label( 'Website' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_website" class="pc-form__element pc-form__element--url_input">
    <label for="website" class="pc-form__label">Website</label>
    <input type="url" name="website"
        class="form-control url-input pc-form__element__field pc-form__element__field--url_input"
    />
</div>
```
</details>

### set_existing( mixed $value )

Sets the current value. Runs through the sanitizer if one is set.

```php
Url::make( 'website' )
    ->label( 'Website' )
    ->set_existing( 'https://example.com' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_website" class="pc-form__element pc-form__element--url_input">
    <label for="website" class="pc-form__label">Website</label>
    <input type="url" name="website"
        class="form-control url-input pc-form__element__field pc-form__element__field--url_input"
        value="https://example.com"
    />
</div>
```
</details>

### placeholder( string $text )

Placeholder text shown when the field is empty.

```php
Url::make( 'website' )
    ->label( 'Website' )
    ->placeholder( 'https://example.com' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_website" class="pc-form__element pc-form__element--url_input">
    <label for="website" class="pc-form__label">Website</label>
    <input type="url" name="website"
        class="form-control url-input pc-form__element__field pc-form__element__field--url_input"
        placeholder="https://example.com"
    />
</div>
```
</details>

### required( bool $required = true )

Marks the field as required. The label displays a `*` indicator via CSS.

```php
Url::make( 'website' )
    ->label( 'Website' )
    ->required( true )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_website" class="pc-form__element pc-form__element--url_input">
    <label for="website" class="pc-form__label">Website</label>
    <input type="url" name="website"
        class="form-control url-input pc-form__element__field pc-form__element__field--url_input"
        required=""
    />
</div>
```
</details>

### disabled( bool $disabled = true )

Disables the input. Value is visible but cannot be changed or submitted.

```php
Url::make( 'locked_url' )
    ->label( 'Website' )
    ->set_existing( 'https://example.com' )
    ->disabled( true )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_locked_url" class="pc-form__element pc-form__element--url_input">
    <label for="locked_url" class="pc-form__label">Website</label>
    <input type="url" name="locked_url"
        class="form-control url-input pc-form__element__field pc-form__element__field--url_input"
        disabled="" value="https://example.com"
    />
</div>
```
</details>

### readonly( bool $readonly = true )

Makes the field read-only. Value can be selected and copied but not changed.

```php
Url::make( 'readonly_url' )
    ->label( 'Website' )
    ->set_existing( 'https://example.com' )
    ->readonly( true )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_readonly_url" class="pc-form__element pc-form__element--url_input">
    <label for="readonly_url" class="pc-form__label">Website</label>
    <input type="url" name="readonly_url"
        class="form-control url-input pc-form__element__field pc-form__element__field--url_input"
        readonly="" value="https://example.com"
    />
</div>
```
</details>

### pattern( string $regex )

HTML5 validation pattern (regex) the value must match.

```php
Url::make( 'website' )
    ->label( 'Website' )
    ->pattern( 'https://.*' )
    ->placeholder( 'Must start with https://' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_website" class="pc-form__element pc-form__element--url_input">
    <label for="website" class="pc-form__label">Website</label>
    <input type="url" name="website"
        class="form-control url-input pc-form__element__field pc-form__element__field--url_input"
        pattern="https://.*" placeholder="Must start with https://"
    />
</div>
```
</details>

### minlength( int $min ) / maxlength( int $max )

Minimum and maximum character length constraints.

```php
Url::make( 'website' )
    ->label( 'Website' )
    ->minlength( 10 )
    ->maxlength( 255 )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_website" class="pc-form__element pc-form__element--url_input">
    <label for="website" class="pc-form__label">Website</label>
    <input type="url" name="website"
        class="form-control url-input pc-form__element__field pc-form__element__field--url_input"
        minlength="10" maxlength="255"
    />
</div>
```
</details>

### size( int $size )

Visible width of the input in characters.

```php
Url::make( 'website' )
    ->label( 'Website' )
    ->size( 40 )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_website" class="pc-form__element pc-form__element--url_input">
    <label for="website" class="pc-form__label">Website</label>
    <input type="url" name="website"
        class="form-control url-input pc-form__element__field pc-form__element__field--url_input"
        size="40"
    />
</div>
```
</details>

### autocomplete( string $value )

HTML `autocomplete` attribute to help browsers autofill.

```php
Url::make( 'website' )
    ->label( 'Website' )
    ->autocomplete( 'url' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_website" class="pc-form__element pc-form__element--url_input">
    <label for="website" class="pc-form__label">Website</label>
    <input type="url" name="website"
        class="form-control url-input pc-form__element__field pc-form__element__field--url_input"
        autocomplete="url"
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
Url::make( 'website' )
    ->label( 'Website' )
    ->inputmode( 'url' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_website" class="pc-form__element pc-form__element--url_input">
    <label for="website" class="pc-form__label">Website</label>
    <input type="url" name="website"
        class="form-control url-input pc-form__element__field pc-form__element__field--url_input"
        inputmode="url"
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
Url::make( 'website' )
    ->label( 'Website' )
    ->spellcheck( 'false' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_website" class="pc-form__element pc-form__element--url_input">
    <label for="website" class="pc-form__label">Website</label>
    <input type="url" name="website"
        class="form-control url-input pc-form__element__field pc-form__element__field--url_input"
        spellcheck="false"
    />
</div>
```
</details>

### datalist_items( array $items )

Autocomplete suggestions via an HTML `<datalist>` element.

```php
Url::make( 'website' )
    ->label( 'Website' )
    ->datalist_items( array( 'https://google.com', 'https://github.com', 'https://wordpress.org' ) )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_website" class="pc-form__element pc-form__element--url_input">
    <label for="website" class="pc-form__label">Website</label>
    <input type="url" name="website"
        class="form-control url-input pc-form__element__field pc-form__element__field--url_input"
        list="_website__list"
    />
    <datalist id="_website__list">
        <option value="https://google.com"></option>
        <option value="https://github.com"></option>
        <option value="https://wordpress.org"></option>
    </datalist>
</div>
```
</details>

### error_notification( string $message )

Displays an error message below the field.

```php
Url::make( 'url_error' )
    ->label( 'Website' )
    ->required( true )
    ->error_notification( 'A valid URL is required.' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_url_error" class="pc-form__element pc-form__element--url_input notification-error">
    <label for="url_error" class="pc-form__label">Website</label>
    <input type="url" name="url_error"
        class="form-control url-input pc-form__element__field pc-form__element__field--url_input notification-error"
        required=""
    />
    <div class="pc-form__notification pc-form__notification--error">A valid URL is required.</div>
</div>
```
</details>

### warning_notification( string $message )

Displays a warning message below the field.

```php
Url::make( 'url_warning' )
    ->label( 'Website' )
    ->set_existing( 'http://example.com' )
    ->warning_notification( 'URL does not use HTTPS.' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_url_warning" class="pc-form__element pc-form__element--url_input notification-warning">
    <label for="url_warning" class="pc-form__label">Website</label>
    <input type="url" name="url_warning"
        class="form-control url-input pc-form__element__field pc-form__element__field--url_input notification-warning"
        value="http://example.com"
    />
    <div class="pc-form__notification pc-form__notification--warning">URL does not use HTTPS.</div>
</div>
```
</details>

### success_notification( string $message )

Displays a success message below the field.

```php
Url::make( 'url_success' )
    ->label( 'Website' )
    ->set_existing( 'https://example.com' )
    ->success_notification( 'URL is valid and reachable.' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_url_success" class="pc-form__element pc-form__element--url_input notification-success">
    <label for="url_success" class="pc-form__label">Website</label>
    <input type="url" name="url_success"
        class="form-control url-input pc-form__element__field pc-form__element__field--url_input notification-success"
        value="https://example.com"
    />
    <div class="pc-form__notification pc-form__notification--success">URL is valid and reachable.</div>
</div>
```
</details>

### info_notification( string $message )

Displays an info message below the field.

```php
Url::make( 'url_info' )
    ->label( 'Website' )
    ->info_notification( 'Must include the protocol (https://).' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_url_info" class="pc-form__element pc-form__element--url_input notification-info">
    <label for="url_info" class="pc-form__label">Website</label>
    <input type="url" name="url_info"
        class="form-control url-input pc-form__element__field pc-form__element__field--url_input notification-info"
    />
    <div class="pc-form__notification pc-form__notification--info">Must include the protocol (https://).</div>
</div>
```
</details>

### pre_description( string $description )

Sets a description or hint displayed before the input.

```php
Url::make( 'website' )
    ->label( 'Website' )
    ->pre_description( 'Your public profile URL.' )
```

### post_description( string $description )

Sets a description or help text displayed after the input, before any notification.

```php
Url::make( 'website' )
    ->label( 'Website' )
    ->post_description( 'Must start with https://' )
```

### before( string $html ) / after( string $html )

HTML content before or after the input within the wrapper.

```php
Url::make( 'wrapped_url' )
			->label( 'Website' )
			->before( '<span style="color:#6b7280;font-size:13px;">Your public profile URL</span>' )
			->after( '<span style="color:#6b7280;font-size:13px;">Must start with https://</span>' )
```

![before/after](screenshots/url/before-after.png)

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_wrapped_url" class="pc-form__element pc-form__element--url_input">
    <span style="color:#6b7280;font-size:13px">Your public profile URL</span>
        <label for="wrapped_url" class="pc-form__label">Website</label>
            <input type="url" name="wrapped_url" class="form-control url-input pc-form__element__field pc-form__element__field--url_input" list="_wrapped_url__list" />
            <span style="color:#6b7280;font-size:13px">Must start with https://</span>
            </div>
```
</details>

### id( string $id )

Sets a custom HTML `id` on the input element.

```php
Url::make( 'website' )
    ->id( 'my-custom-url-id' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_website" class="pc-form__element pc-form__element--url_input">
    <input type="url" name="website" id="my-custom-url-id"
        class="form-control url-input pc-form__element__field pc-form__element__field--url_input"
    />
</div>
```
</details>

### wrapper_id( string $id )

Sets a custom HTML `id` on the wrapper div.

```php
Url::make( 'website' )
    ->wrapper_id( 'my-custom-wrapper-id' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="my-custom-wrapper-id" class="pc-form__element pc-form__element--url_input">
    <input type="url" name="website"
        class="form-control url-input pc-form__element__field pc-form__element__field--url_input"
    />
</div>
```
</details>

### data( string $key, string $value )

Adds a `data-*` attribute to the input.

```php
Url::make( 'website' )
    ->data( 'validate', 'url' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_website" class="pc-form__element pc-form__element--url_input">
    <input type="url" name="website"
        class="form-control url-input pc-form__element__field pc-form__element__field--url_input"
        data-validate="url"
    />
</div>
```
</details>

### wrapper_data( string $key, string $value )

Adds a `data-*` attribute to the wrapper div.

```php
Url::make( 'website' )
    ->wrapper_data( 'section', 'links' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_website" class="pc-form__element pc-form__element--url_input" data-section="links">
    <input type="url" name="website"
        class="form-control url-input pc-form__element__field pc-form__element__field--url_input"
    />
</div>
```
</details>

### add_class( string $class )

Adds a CSS class to the input element.

```php
Url::make( 'website' )
    ->add_class( 'my-input-class' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_website" class="pc-form__element pc-form__element--url_input">
    <input type="url" name="website"
        class="form-control url-input pc-form__element__field pc-form__element__field--url_input my-input-class"
    />
</div>
```
</details>

### add_wrapper_class( string $class )

Adds a CSS class to the wrapper div.

```php
Url::make( 'website' )
    ->add_wrapper_class( 'my-wrapper-class' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_website" class="pc-form__element pc-form__element--url_input my-wrapper-class">
    <input type="url" name="website"
        class="form-control url-input pc-form__element__field pc-form__element__field--url_input"
    />
</div>
```
</details>

### show_wrapper( bool $show = true )

Controls whether the wrapping `<div>` is rendered.

```php
Url::make( 'bare' )
    ->show_wrapper( false )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<input type="url" name="bare"
    class="form-control url-input pc-form__element__field pc-form__element__field--url_input"
/>
```
</details>

### tabindex( int $index )

Sets the tab order of the input.

```php
Url::make( 'website' )
    ->tabindex( 5 )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_website" class="pc-form__element pc-form__element--url_input">
    <input type="url" name="website"
        class="form-control url-input pc-form__element__field pc-form__element__field--url_input"
        tabindex="5"
    />
</div>
```
</details>

### attribute( string $key, mixed $value )

Sets an arbitrary HTML attribute on the input.

```php
Url::make( 'website' )
    ->attribute( 'aria-label', 'Enter your website URL' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_website" class="pc-form__element pc-form__element--url_input">
    <input type="url" name="website"
        class="form-control url-input pc-form__element__field pc-form__element__field--url_input"
        aria-label="Enter your website URL"
    />
</div>
```
</details>

### attributes( array $attrs )

Sets multiple arbitrary HTML attributes at once.

```php
Url::make( 'website' )
    ->attributes( array(
        'title'    => 'Enter website URL',
        'tabindex' => '5',
    ) )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_website" class="pc-form__element pc-form__element--url_input">
    <input type="url" name="website"
        class="form-control url-input pc-form__element__field pc-form__element__field--url_input"
        title="Enter website URL" tabindex="5"
    />
</div>
```
</details>

### sanitizer( callable $fn )

Sets a sanitization callback applied when `set_existing()` is called. Default: `Sanitize::TEXT`. Accepts any `callable` - a built-in helper constant, a WordPress function name, a closure, or any invokable.

**Using a built-in helper:**

```php
use PinkCrab\Form_Components\Util\Sanitize;

Url::make( 'website' )
    ->sanitizer( Sanitize::URL )
    ->set_existing( 'https://example.com/path?q=1' )
```

**Using a custom callable:**

```php
Url::make( 'website' )
    ->sanitizer( function( $value ) {
        return esc_url_raw( trim( $value ) );
    } )
    ->set_existing( '  https://example.com  ' ) // Stores: "https://example.com"
```

**Using a WordPress function:**

```php
Url::make( 'website' )
    ->sanitizer( 'esc_url_raw' )
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

Url::make( 'website' )
    ->validator( v::url() )
```

### style( Style $style )

Sets a custom style for the field, overriding the default.

```php
use PinkCrab\Form_Components\Style\Default_Style;

Url::make( 'website' )
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
