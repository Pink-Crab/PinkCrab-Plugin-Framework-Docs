# File Input

Renders `<input type="file">`. Allows users to select one or more files for upload. No sanitizer is applied by default.

**Class:** `PinkCrab\Form_Components\Element\Field\Input\File`  
**Make helper:** `Make::file( 'name', fn(File $f) => $f->... )`

---

## Basic Usage

```php
$this->component( new Input_Component(
        File::make( 'upload' )
            ->label( 'Upload File' )
    ) )
```

![Basic](screenshots/file/basic.png)

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_upload" class="pc-form__element pc-form__element--file_input">
    <label for="upload" class="pc-form__label">Upload File</label>
        <input type="file" name="upload" class="form-control file-input pc-form__element__field pc-form__element__field--file_input" />
    </div>
```
</details>

---

## Using Make Helper

```php
use PinkCrab\Form_Components\Util\Make;

$this->component( Make::file( 'avatar', fn( $f ) => $f
    ->label( 'Upload Avatar' )
    ->accept( 'image/*' )
    ->required( true )
) );
```

---

## Methods

### label( string $label )

Sets the visible label text above the input.

```php
File::make( 'document' )->label( 'Upload Document' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_document" class="pc-form__element pc-form__element--file_input">
    <label for="document" class="pc-form__label">Upload Document</label>
    <input type="file" name="document"
        class="form-control file-input pc-form__element__field pc-form__element__field--file_input"
    />
</div>
```
</details>

### accept( string $accept )

Sets the accepted MIME types or file extensions.

```php
File::make( 'photo' )
    ->label( 'Upload Photo' )
    ->accept( 'image/png, image/jpeg' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_photo" class="pc-form__element pc-form__element--file_input">
    <label for="photo" class="pc-form__label">Upload Photo</label>
    <input type="file" name="photo"
        class="form-control file-input pc-form__element__field pc-form__element__field--file_input"
        accept="image/png, image/jpeg"
    />
</div>
```
</details>

### capture( ?string $capture = null )

Sets the `capture` attribute for mobile camera access. Accepted values: `"user"` (front camera), `"environment"` (rear camera), or `null` (user agent decides). Throws `\InvalidArgumentException` for invalid values.

```php
File::make( 'selfie' )
    ->label( 'Take Selfie' )
    ->accept( 'image/*' )
    ->capture( 'user' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_selfie" class="pc-form__element pc-form__element--file_input">
    <label for="selfie" class="pc-form__label">Take Selfie</label>
    <input type="file" name="selfie"
        class="form-control file-input pc-form__element__field pc-form__element__field--file_input"
        accept="image/*" capture="user"
    />
</div>
```
</details>

### multiple( bool $multiple = true )

Allows selecting multiple files.

```php
File::make( 'attachments' )
    ->label( 'Attachments' )
    ->multiple( true )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_attachments" class="pc-form__element pc-form__element--file_input">
    <label for="attachments" class="pc-form__label">Attachments</label>
    <input type="file" name="attachments"
        class="form-control file-input pc-form__element__field pc-form__element__field--file_input"
        multiple=""
    />
</div>
```
</details>

### required( bool $required = true )

Marks the field as required. The label displays a `*` indicator via CSS.

```php
File::make( 'cv' )
    ->label( 'Upload CV' )
    ->required( true )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_cv" class="pc-form__element pc-form__element--file_input">
    <label for="cv" class="pc-form__label">Upload CV</label>
    <input type="file" name="cv"
        class="form-control file-input pc-form__element__field pc-form__element__field--file_input"
        required=""
    />
</div>
```
</details>

### disabled( bool $disabled = true )

Disables the input. The file picker cannot be opened.

```php
File::make( 'locked_upload' )
    ->label( 'Upload Disabled' )
    ->disabled( true )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_locked_upload" class="pc-form__element pc-form__element--file_input">
    <label for="locked_upload" class="pc-form__label">Upload Disabled</label>
    <input type="file" name="locked_upload"
        class="form-control file-input pc-form__element__field pc-form__element__field--file_input"
        disabled=""
    />
</div>
```
</details>

### set_existing( mixed $value )

Sets the current value. No sanitizer is applied by default. Note that for security reasons, browsers do not allow pre-populating file inputs with a file path.

```php
File::make( 'document' )
    ->label( 'Document' )
    ->set_existing( 'report.pdf' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_document" class="pc-form__element pc-form__element--file_input">
    <label for="document" class="pc-form__label">Document</label>
    <input type="file" name="document"
        class="form-control file-input pc-form__element__field pc-form__element__field--file_input"
        value="report.pdf"
    />
</div>
```
</details>

### error_notification( string $message )

Displays an error message below the field.

```php
File::make( 'invalid_upload' )
    ->label( 'Upload File' )
    ->error_notification( 'File type not supported.' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_invalid_upload" class="pc-form__element pc-form__element--file_input notification-error">
    <label for="invalid_upload" class="pc-form__label">Upload File</label>
    <input type="file" name="invalid_upload"
        class="form-control file-input pc-form__element__field pc-form__element__field--file_input notification-error"
    />
    <div class="pc-form__notification pc-form__notification--error">File type not supported.</div>
</div>
```
</details>

### warning_notification( string $message )

Displays a warning message below the field.

```php
File::make( 'large_upload' )
    ->label( 'Upload File' )
    ->warning_notification( 'Maximum file size is 5MB.' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_large_upload" class="pc-form__element pc-form__element--file_input notification-warning">
    <label for="large_upload" class="pc-form__label">Upload File</label>
    <input type="file" name="large_upload"
        class="form-control file-input pc-form__element__field pc-form__element__field--file_input notification-warning"
    />
    <div class="pc-form__notification pc-form__notification--warning">Maximum file size is 5MB.</div>
</div>
```
</details>

### success_notification( string $message )

Displays a success message below the field.

```php
File::make( 'ok_upload' )
    ->label( 'Upload File' )
    ->success_notification( 'File uploaded successfully.' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_ok_upload" class="pc-form__element pc-form__element--file_input notification-success">
    <label for="ok_upload" class="pc-form__label">Upload File</label>
    <input type="file" name="ok_upload"
        class="form-control file-input pc-form__element__field pc-form__element__field--file_input notification-success"
    />
    <div class="pc-form__notification pc-form__notification--success">File uploaded successfully.</div>
</div>
```
</details>

### info_notification( string $message )

Displays an info message below the field.

```php
File::make( 'info_upload' )
    ->label( 'Upload File' )
    ->info_notification( 'Accepted formats: PNG, JPG, PDF' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_info_upload" class="pc-form__element pc-form__element--file_input notification-info">
    <label for="info_upload" class="pc-form__label">Upload File</label>
    <input type="file" name="info_upload"
        class="form-control file-input pc-form__element__field pc-form__element__field--file_input notification-info"
    />
    <div class="pc-form__notification pc-form__notification--info">Accepted formats: PNG, JPG, PDF</div>
</div>
```
</details>

### pre_description( string $description )

Sets a description or hint displayed before the input.

```php
File::make( 'upload' )
    ->label( 'Upload File' )
    ->pre_description( 'Select a file to upload.' )
```

### post_description( string $description )

Sets a description or help text displayed after the input, before any notification.

```php
File::make( 'upload' )
    ->label( 'Upload File' )
    ->post_description( 'Accepted formats: PNG, JPG, PDF. Max 5MB.' )
```

### before( string $html ) / after( string $html )

HTML content before or after the input; renders whether or not the wrapper is shown.

```php
File::make( 'wrapped_upload' )
    ->label( 'Upload File' )
    ->before( '<span>Select a file to upload</span>' )
    ->after( '<span>Max size: 10MB</span>' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_wrapped_upload" class="pc-form__element pc-form__element--file_input">
    <span>Select a file to upload</span>
    <label for="wrapped_upload" class="pc-form__label">Upload File</label>
    <input type="file" name="wrapped_upload"
        class="form-control file-input pc-form__element__field pc-form__element__field--file_input"
    />
    <span>Max size: 10MB</span>
</div>
```
</details>

### id( string $id )

Sets a custom HTML `id` on the input element.

```php
File::make( 'upload' )->id( 'file-upload-input' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_upload" class="pc-form__element pc-form__element--file_input">
    <input type="file" name="upload" id="file-upload-input"
        class="form-control file-input pc-form__element__field pc-form__element__field--file_input"
    />
</div>
```
</details>

### wrapper_id( string $id )

Sets a custom HTML `id` on the wrapper div.

```php
File::make( 'upload' )->wrapper_id( 'file-upload-wrapper' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="file-upload-wrapper" class="pc-form__element pc-form__element--file_input">
    <input type="file" name="upload"
        class="form-control file-input pc-form__element__field pc-form__element__field--file_input"
    />
</div>
```
</details>

### data( string $key, string $value )

Adds a `data-*` attribute to the input.

```php
File::make( 'upload' )->data( 'max-size', '5242880' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_upload" class="pc-form__element pc-form__element--file_input">
    <input type="file" name="upload"
        class="form-control file-input pc-form__element__field pc-form__element__field--file_input"
        data-max-size="5242880"
    />
</div>
```
</details>

### wrapper_data( string $key, string $value )

Adds a `data-*` attribute to the wrapper div.

```php
File::make( 'upload' )->wrapper_data( 'section', 'uploads' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_upload" class="pc-form__element pc-form__element--file_input" data-section="uploads">
    <input type="file" name="upload"
        class="form-control file-input pc-form__element__field pc-form__element__field--file_input"
    />
</div>
```
</details>

### add_class( string $class )

Adds a CSS class to the input element.

```php
File::make( 'upload' )->add_class( 'custom-file' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_upload" class="pc-form__element pc-form__element--file_input">
    <input type="file" name="upload"
        class="form-control file-input pc-form__element__field pc-form__element__field--file_input custom-file"
    />
</div>
```
</details>

### add_wrapper_class( string $class )

Adds a CSS class to the wrapper div.

```php
File::make( 'upload' )->add_wrapper_class( 'file-wrapper' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_upload" class="pc-form__element pc-form__element--file_input file-wrapper">
    <input type="file" name="upload"
        class="form-control file-input pc-form__element__field pc-form__element__field--file_input"
    />
</div>
```
</details>

### show_wrapper( bool $show = true )

Controls whether the wrapping `<div>` is rendered.

```php
File::make( 'upload' )->show_wrapper( false )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<input type="file" name="upload"
    class="form-control file-input pc-form__element__field pc-form__element__field--file_input"
/>
```
</details>

### tabindex( int $index )

Sets the tab order of the input.

```php
File::make( 'upload' )->tabindex( 4 )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_upload" class="pc-form__element pc-form__element--file_input">
    <input type="file" name="upload"
        class="form-control file-input pc-form__element__field pc-form__element__field--file_input"
        tabindex="4"
    />
</div>
```
</details>

### attribute( string $key, mixed $value )

Sets an arbitrary HTML attribute on the input.

```php
File::make( 'upload' )->attribute( 'aria-label', 'Upload your document' )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_upload" class="pc-form__element pc-form__element--file_input">
    <input type="file" name="upload"
        class="form-control file-input pc-form__element__field pc-form__element__field--file_input"
        aria-label="Upload your document"
    />
</div>
```
</details>

### attributes( array $attrs )

Sets multiple arbitrary HTML attributes at once.

```php
File::make( 'upload' )->attributes( array(
    'title' => 'Click to select a file',
    'tabindex' => '4',
) )
```

<details markdown="1">
<summary>Generated HTML</summary>

```html
<div id="form-field_upload" class="pc-form__element pc-form__element--file_input">
    <input type="file" name="upload"
        class="form-control file-input pc-form__element__field pc-form__element__field--file_input"
        title="Click to select a file" tabindex="4"
    />
</div>
```
</details>

### sanitizer( callable $fn )

Sets a sanitization callback applied when `set_existing()` is called. Default: no sanitizer (`null`).

**Using a custom callable:**

```php
File::make( 'upload' )
    ->sanitizer( function( $value ) {
        return sanitize_file_name( $value );
    } )
    ->set_existing( 'my document.pdf' )
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

### validator( Validator $validator )

Sets a Respect\Validation validator for server-side validation.

```php
use Respect\Validation\Validator as v;

File::make( 'upload' )->validator( v::stringType()->notEmpty() )
```

### style( Style $style )

Sets a custom style for the field, overriding the default.

```php
use PinkCrab\Form_Components\Style\Default_Style;

File::make( 'upload' )->style( new Default_Style() )
```

---

## Traits

| Trait | Methods |
|-------|---------|
| Label | `label()`, `get_label()`, `has_label()` |
| Single_Value | `value()`, `get_value()`, `has_value()` |
| Multiple | `multiple()`, `is_multiple()` |
| Disabled | `disabled()`, `is_disabled()` |
| Required | `required()`, `is_required()` |
| Description | `pre_description()`, `post_description()`, `get_pre_description()`, `get_post_description()`, `has_pre_description()`, `has_post_description()` |
| Notification | `error_notification()`, `warning_notification()`, `success_notification()`, `info_notification()` |
