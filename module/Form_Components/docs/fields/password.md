# Password Input

Renders `<input type="password">`. Values are masked with dots in the browser.

**Class:** `PinkCrab\Form_Components\Element\Field\Input\Password`  
**Make helper:** `Make::password( 'name', fn(Password $f) => $f->... )`

---

## Basic Usage

```php
$this->component( new Input_Component(
		Password::make( 'password' )
			->label( 'Password' )
			->placeholder( 'Enter your password' )
	) )
```

![Basic](screenshots/password/basic.png)

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_password" class="pc-form__element pc-form__element--password_input">
    <label for="password" class="pc-form__label">Password</label>
        <input type="password" name="password" class="form-control password-input pc-form__element__field pc-form__element__field--password_input" placeholder="Enter your password" />
    </div>
```
</details>

---

## Using Make Helper

```php
use PinkCrab\Form_Components\Util\Make;

$this->component( Make::password( 'password', fn( $f ) => $f
    ->label( 'Password' )
    ->required( true )
) );
```

---

## Methods

### label( string $label )

Sets the visible label text above the input.

```php
Password::make( 'password' )->label( 'Password' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_password" class="pc-form__element pc-form__element--password_input">
    <label for="password" class="pc-form__label">Password</label>
    <input type="password" name="password"
        class="form-control password-input pc-form__element__field pc-form__element__field--password_input"
    />
</div>
```
</details>

### set_existing( mixed $value )

Sets the current value. The value is masked in the browser. Runs through the sanitizer if one is set.

```php
Password::make( 'current_pw' )
			->label( 'Current Password' )
			->set_existing( 'secretpass' )
```

![set_existing](screenshots/password/value.png)

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_current_pw" class="pc-form__element pc-form__element--password_input">
    <label for="current_pw" class="pc-form__label">Current Password</label>
        <input type="password" name="current_pw" class="form-control password-input pc-form__element__field pc-form__element__field--password_input" value="secretpass" />
    </div>
```
</details>

### placeholder( string $text )

Placeholder text shown when the field is empty.

```php
Password::make( 'password' )
    ->label( 'Password' )
    ->placeholder( 'Enter your password' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_password" class="pc-form__element pc-form__element--password_input">
    <label for="password" class="pc-form__label">Password</label>
    <input type="password" name="password"
        class="form-control password-input pc-form__element__field pc-form__element__field--password_input"
        placeholder="Enter your password"
    />
</div>
```
</details>

### required( bool $required = true )

Marks the field as required. The label displays a `*` indicator via CSS.

```php
Password::make( 'password' )
    ->label( 'Password' )
    ->required( true )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_password" class="pc-form__element pc-form__element--password_input">
    <label for="password" class="pc-form__label">Password</label>
    <input type="password" name="password"
        class="form-control password-input pc-form__element__field pc-form__element__field--password_input"
        required=""
    />
</div>
```
</details>

### disabled( bool $disabled = true )

Disables the input. Value is visible but cannot be changed or submitted.

```php
Password::make( 'locked_pw' )
    ->label( 'Locked Password' )
    ->set_existing( 'secretpass' )
    ->disabled( true )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_locked_pw" class="pc-form__element pc-form__element--password_input">
    <label for="locked_pw" class="pc-form__label">Locked Password</label>
    <input type="password" name="locked_pw"
        class="form-control password-input pc-form__element__field pc-form__element__field--password_input"
        disabled="" value="secretpass"
    />
</div>
```
</details>

### readonly( bool $readonly = true )

Makes the field read-only. Value can be selected and copied but not changed.

```php
Password::make( 'readonly_pw' )
    ->label( 'Read Only Password' )
    ->set_existing( 'secretpass' )
    ->readonly( true )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_readonly_pw" class="pc-form__element pc-form__element--password_input">
    <label for="readonly_pw" class="pc-form__label">Read Only Password</label>
    <input type="password" name="readonly_pw"
        class="form-control password-input pc-form__element__field pc-form__element__field--password_input"
        readonly="" value="secretpass"
    />
</div>
```
</details>

### pattern( string $regex )

HTML5 validation pattern (regex) the value must match.

```php
Password::make( 'password' )
    ->label( 'Password' )
    ->pattern( '(?=.*\d)(?=.*[a-z])(?=.*[A-Z]).{8,}' )
    ->placeholder( 'Min 8 chars, upper, lower and number' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_password" class="pc-form__element pc-form__element--password_input">
    <label for="password" class="pc-form__label">Password</label>
    <input type="password" name="password"
        class="form-control password-input pc-form__element__field pc-form__element__field--password_input"
        pattern="(?=.*\d)(?=.*[a-z])(?=.*[A-Z]).{8,}" placeholder="Min 8 chars, upper, lower and number"
    />
</div>
```
</details>

### minlength( int $min ) / maxlength( int $max )

Minimum and maximum character length constraints.

```php
Password::make( 'password' )
    ->label( 'Password' )
    ->minlength( 8 )
    ->maxlength( 64 )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_password" class="pc-form__element pc-form__element--password_input">
    <label for="password" class="pc-form__label">Password</label>
    <input type="password" name="password"
        class="form-control password-input pc-form__element__field pc-form__element__field--password_input"
        minlength="8" maxlength="64"
    />
</div>
```
</details>

### size( int $size )

Visible width of the input in characters.

```php
Password::make( 'password' )
    ->label( 'Password' )
    ->size( 30 )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_password" class="pc-form__element pc-form__element--password_input">
    <label for="password" class="pc-form__label">Password</label>
    <input type="password" name="password"
        class="form-control password-input pc-form__element__field pc-form__element__field--password_input"
        size="30"
    />
</div>
```
</details>

### autocomplete( string $value )

HTML `autocomplete` attribute to help browsers and password managers autofill.

```php
Password::make( 'password' )
    ->label( 'Password' )
    ->autocomplete( 'current-password' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_password" class="pc-form__element pc-form__element--password_input">
    <label for="password" class="pc-form__label">Password</label>
    <input type="password" name="password"
        class="form-control password-input pc-form__element__field pc-form__element__field--password_input"
        autocomplete="current-password"
    />
</div>
```
</details>

Common values:

| Value | Description |
|-------|-------------|
| `off` | Disable autocomplete |
| `on` | Enable autocomplete (browser decides) |
| `new-password` | New password (password managers) |
| `current-password` | Current password |
| `username` | Username |

### error_notification( string $message )

Displays an error message below the field.

```php
Password::make( 'pw_error' )
    ->label( 'Password' )
    ->required( true )
    ->error_notification( 'Password is required.' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_pw_error" class="pc-form__element pc-form__element--password_input notification-error">
    <label for="pw_error" class="pc-form__label">Password</label>
    <input type="password" name="pw_error"
        class="form-control password-input pc-form__element__field pc-form__element__field--password_input notification-error"
        required=""
    />
    <div class="pc-form__notification pc-form__notification--error">Password is required.</div>
</div>
```
</details>

### warning_notification( string $message )

Displays a warning message below the field.

```php
Password::make( 'pw_warning' )
    ->label( 'Password' )
    ->warning_notification( 'Password is weak.' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_pw_warning" class="pc-form__element pc-form__element--password_input notification-warning">
    <label for="pw_warning" class="pc-form__label">Password</label>
    <input type="password" name="pw_warning"
        class="form-control password-input pc-form__element__field pc-form__element__field--password_input notification-warning"
    />
    <div class="pc-form__notification pc-form__notification--warning">Password is weak.</div>
</div>
```
</details>

### success_notification( string $message )

Displays a success message below the field.

```php
Password::make( 'pw_success' )
    ->label( 'Password' )
    ->success_notification( 'Password is strong!' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_pw_success" class="pc-form__element pc-form__element--password_input notification-success">
    <label for="pw_success" class="pc-form__label">Password</label>
    <input type="password" name="pw_success"
        class="form-control password-input pc-form__element__field pc-form__element__field--password_input notification-success"
    />
    <div class="pc-form__notification pc-form__notification--success">Password is strong!</div>
</div>
```
</details>

### info_notification( string $message )

Displays an info message below the field.

```php
Password::make( 'pw_info' )
    ->label( 'Password' )
    ->info_notification( 'Must contain at least 8 characters.' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_pw_info" class="pc-form__element pc-form__element--password_input notification-info">
    <label for="pw_info" class="pc-form__label">Password</label>
    <input type="password" name="pw_info"
        class="form-control password-input pc-form__element__field pc-form__element__field--password_input notification-info"
    />
    <div class="pc-form__notification pc-form__notification--info">Must contain at least 8 characters.</div>
</div>
```
</details>

### pre_description( string $description )

Sets a description or hint displayed before the input.

```php
Password::make( 'password' )
    ->label( 'Password' )
    ->pre_description( 'Choose a strong password.' )
```

### post_description( string $description )

Sets a description or help text displayed after the input, before any notification.

```php
Password::make( 'password' )
    ->label( 'Password' )
    ->post_description( 'Must contain at least 8 characters.' )
```

### before( string $html ) / after( string $html )

HTML content before or after the input; renders whether or not the wrapper is shown.

```php
Password::make( 'wrapped_pw' )
			->label( 'Password' )
			->before( '<span style="color:#6b7280;font-size:13px;">Must be at least 8 characters</span>' )
			->after( '<span style="color:#6b7280;font-size:13px;">Include uppercase, lowercase and numbers</span>' )
```

![before/after](screenshots/password/before-after.png)

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_wrapped_pw" class="pc-form__element pc-form__element--password_input">
    <span style="color:#6b7280;font-size:13px">Must be at least 8 characters</span>
        <label for="wrapped_pw" class="pc-form__label">Password</label>
            <input type="password" name="wrapped_pw" class="form-control password-input pc-form__element__field pc-form__element__field--password_input" />
            <span style="color:#6b7280;font-size:13px">Include uppercase, lowercase and numbers</span>
            </div>
```
</details>

### id( string $id )

Sets a custom HTML `id` on the input element.

```php
Password::make( 'password' )
    ->id( 'my-custom-password-id' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_password" class="pc-form__element pc-form__element--password_input">
    <input type="password" name="password" id="my-custom-password-id"
        class="form-control password-input pc-form__element__field pc-form__element__field--password_input"
    />
</div>
```
</details>

### wrapper_id( string $id )

Sets a custom HTML `id` on the wrapper div.

```php
Password::make( 'password' )
    ->wrapper_id( 'my-custom-wrapper-id' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="my-custom-wrapper-id" class="pc-form__element pc-form__element--password_input">
    <input type="password" name="password"
        class="form-control password-input pc-form__element__field pc-form__element__field--password_input"
    />
</div>
```
</details>

### data( string $key, string $value )

Adds a `data-*` attribute to the input.

```php
Password::make( 'password' )
    ->data( 'strength', 'required' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_password" class="pc-form__element pc-form__element--password_input">
    <input type="password" name="password"
        class="form-control password-input pc-form__element__field pc-form__element__field--password_input"
        data-strength="required"
    />
</div>
```
</details>

### wrapper_data( string $key, string $value )

Adds a `data-*` attribute to the wrapper div.

```php
Password::make( 'password' )
    ->wrapper_data( 'section', 'auth' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_password" class="pc-form__element pc-form__element--password_input" data-section="auth">
    <input type="password" name="password"
        class="form-control password-input pc-form__element__field pc-form__element__field--password_input"
    />
</div>
```
</details>

### add_class( string $class )

Adds a CSS class to the input element.

```php
Password::make( 'password' )
    ->add_class( 'my-input-class' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_password" class="pc-form__element pc-form__element--password_input">
    <input type="password" name="password"
        class="form-control password-input pc-form__element__field pc-form__element__field--password_input my-input-class"
    />
</div>
```
</details>

### add_wrapper_class( string $class )

Adds a CSS class to the wrapper div.

```php
Password::make( 'password' )
    ->add_wrapper_class( 'my-wrapper-class' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_password" class="pc-form__element pc-form__element--password_input my-wrapper-class">
    <input type="password" name="password"
        class="form-control password-input pc-form__element__field pc-form__element__field--password_input"
    />
</div>
```
</details>

### show_wrapper( bool $show = true )

Controls whether the wrapping `<div>` is rendered.

```php
Password::make( 'bare' )
    ->show_wrapper( false )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<input type="password" name="bare"
    class="form-control password-input pc-form__element__field pc-form__element__field--password_input"
/>
```
</details>

### tabindex( int $index )

Sets the tab order of the input.

```php
Password::make( 'password' )
    ->tabindex( 3 )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_password" class="pc-form__element pc-form__element--password_input">
    <input type="password" name="password"
        class="form-control password-input pc-form__element__field pc-form__element__field--password_input"
        tabindex="3"
    />
</div>
```
</details>

### attribute( string $key, mixed $value )

Sets an arbitrary HTML attribute on the input.

```php
Password::make( 'password' )
    ->attribute( 'aria-label', 'Enter your password' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_password" class="pc-form__element pc-form__element--password_input">
    <input type="password" name="password"
        class="form-control password-input pc-form__element__field pc-form__element__field--password_input"
        aria-label="Enter your password"
    />
</div>
```
</details>

### attributes( array $attrs )

Sets multiple arbitrary HTML attributes at once.

```php
Password::make( 'password' )
    ->attributes( array(
        'title'    => 'Enter your password',
        'tabindex' => '3',
    ) )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_password" class="pc-form__element pc-form__element--password_input">
    <input type="password" name="password"
        class="form-control password-input pc-form__element__field pc-form__element__field--password_input"
        title="Enter your password" tabindex="3"
    />
</div>
```
</details>

### sanitizer( callable $fn )

Sets a sanitization callback applied when `set_existing()` is called. Accepts any `callable` - a built-in helper constant, a WordPress function name, a closure, or any invokable.

**Using a built-in helper:**

```php
use PinkCrab\Form_Components\Util\Sanitize;

Password::make( 'password' )
    ->sanitizer( Sanitize::TEXT )
    ->set_existing( '<script>alert("xss")</script>secret' ) // Stores: "secret"
```

**Using a custom callable:**

```php
Password::make( 'password' )
    ->sanitizer( function( $value ) {
        return trim( $value );
    } )
    ->set_existing( '  secretpass  ' ) // Stores: "secretpass"
```

**Using a WordPress function:**

```php
Password::make( 'password' )
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

Password::make( 'password' )
    ->validator( v::stringType()->length( 8, 64 ) )
```

### style( Style $style )

Sets a custom style for the field, overriding the default.

```php
use PinkCrab\Form_Components\Style\Default_Style;

Password::make( 'password' )
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
| Length | `minlength()`, `maxlength()`, `get_min_length()`, `get_max_length()` |
| Size | `size()`, `get_size()`, `has_size()` |
| Autocomplete | `autocomplete()`, `get_autocomplete()`, `has_autocomplete()` |
| Description | `pre_description()`, `post_description()`, `get_pre_description()`, `get_post_description()`, `has_pre_description()`, `has_post_description()` |
| Notification | `error_notification()`, `warning_notification()`, `success_notification()`, `info_notification()` |
