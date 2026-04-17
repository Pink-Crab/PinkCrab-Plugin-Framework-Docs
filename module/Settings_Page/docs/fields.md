# Fields

How to define and configure every field type in Perique Settings Page.

Every field is created from within the `fields()` method on your `Abstract_Settings` subclass. Push instances into the `Setting_Collection`:

```php
use PinkCrab\Perique_Settings_Page\Setting\Abstract_Settings;
use PinkCrab\Perique_Settings_Page\Setting\Setting_Collection;
use PinkCrab\Perique_Settings_Page\Setting\Field\{ Text, Number, Select };

class My_Settings extends Abstract_Settings {

    protected function is_grouped(): bool {
        return true;
    }

    public function group_key(): string {
        return 'my_settings';
    }

    protected function fields( Setting_Collection $settings ): Setting_Collection {
        return $settings->push(
            Text::new( 'site_name' )->set_label( 'Site Name' )->set_required(),
            Number::new( 'limit' )->set_label( 'Limit' )->set_min( 1 )->set_max( 100 )
        );
    }
}
```

Every field uses a `::new( 'key' )` static constructor. All setters return `static` for fluent chaining.

Each section below shows a single comprehensive example with inline comments — chain only the methods you need; none are required beyond `set_label()`.

****

## Common methods

Available on every field.

| Method | Purpose |
|---|---|
| `set_label( string )` | Visible label above the input. |
| `set_description( string )` | Help text below the input. |
| `set_value( mixed )` | Default / stored value (normally set by the hydration layer, not by you). |
| `set_required( bool = true )` | Blocks submission when empty. |
| `set_read_only( bool = true )` | Renders as read-only. |
| `set_id( string )` | Override the auto-generated HTML `id`. |
| `add_class( string )` | Append a CSS class (call repeatedly to add more). |
| `set_icon( string )` | Icon URL or dashicon class. |
| `set_label_position( string )` / `label_before()` / `label_after()` | Position the label above (default) or below the input. |
| `set_before( string )` / `set_after( string )` | HTML rendered inside the wrapper before / after the input. |
| `set_attribute( string, mixed )` | Arbitrary HTML attribute. |
| `set_flag( string )` | Arbitrary HTML boolean flag (e.g. `autofocus`). |
| `set_sanitize( callable )` | Override the per-field default sanitiser. |
| `set_validate( callable \| Validator )` | Server-side validation — any callable returning truthy/falsy, **or** a `Respect\Validation\Validator`. |
| `set_config( callable )` | Escape hatch. Receives the final Form Component element at render time. |
| `clone_as( string )` | Clone the field definition under a different key. |

## Shared traits

Trait methods appear on the fields that include them. Each field section below lists its traits.

| Trait | Methods | Used by |
|---|---|---|
| `Placeholder` | `set_placeholder( string )` | Text, Email, Phone, Url, Password, Textarea, Number, WP_Editor, User_Picker, Post_Picker |
| `Data` | `set_data( string key, string value )` | Almost every field |
| `Pattern` | `set_pattern( string regex )` | Text, Email, Phone, Url |
| `Options` | `set_option( string value, string label, string group = '' )` | Text (datalist), Number (datalist), Select, Radio, Checkbox_Group |
| `Range` | `set_min( mixed )`, `set_max( mixed )`, `set_step( mixed )` | Number |
| `Disabled` | `set_disabled( bool = true )` | Checkbox, Checkbox_Group, Radio, User_Picker, Post_Picker, Repeater |
| `Multiple` | `set_multiple( bool = true )` | Select, User_Picker, Post_Picker |
| `Autocomplete` | `set_autocomplete( string )` | Colour |
| `Checked_Value` | `set_checked_value( string )` | Checkbox, Checkbox_Group |
| `Query` | `set_query_args( array )`, `set_option_label( callable )` | Post_Picker |

****

## Text inputs

### Text

`Setting\Field\Text` — `<input type="text">`. Default sanitiser: `sanitize_text_field`.

Traits: `Placeholder`, `Data`, `Pattern`, `Options` (rendered as a `<datalist>` of suggestions).

```php
use Respect\Validation\Validator as v;

Text::new( 'company_name' )
    // Visible label above the input.
    ->set_label( 'Company Name' )
    // Greyed-out hint shown while the field is empty.
    ->set_placeholder( 'Acme Ltd.' )
    // Help text rendered below the input.
    ->set_description( 'Registered company name as it appears on Companies House.' )
    // Blocks submission if the field is empty.
    ->set_required()
    // Renders the input as read-only (value still submitted).
    ->set_read_only()
    // Override the auto-generated HTML id.
    ->set_id( 'acme-company' )
    // Append one or more CSS classes to the input element.
    ->add_class( 'is-highlight' )
    // data-* attributes for JS hooks.
    ->set_data( 'validate', 'alphanumeric' )
    // HTML5 pattern attribute for client-side validation.
    ->set_pattern( '[A-Za-z0-9 \-&]+' )
    // Datalist suggestions (from the Options trait).
    ->set_option( 'acme', 'Acme Ltd.' )
    ->set_option( 'globex', 'Globex Corp.' )
    // HTML rendered inside the wrapper before / after the input.
    ->set_before( '<span class="prefix">Co:</span>' )
    ->set_after( '<small>Must match Companies House records.</small>' )
    // Arbitrary HTML attributes.
    ->set_attribute( 'autocomplete', 'organization' )
    // Boolean flag rendered as a bare HTML attribute.
    ->set_flag( 'autofocus' )
    // Override the default sanitiser (sanitize_text_field).
    ->set_sanitize( fn( $v ) => trim( (string) $v ) )
    // Server-side validation — callable or Respect\Validation\Validator.
    ->set_validate( v::stringType()->length( 1, 100 ) );
```

### Email

`Setting\Field\Email` — `<input type="email">`. Default sanitiser: `sanitize_email`.

Traits: `Placeholder`, `Data`, `Pattern`.

```php
use Respect\Validation\Validator as v;

Email::new( 'support_email' )
    // Visible label above the input.
    ->set_label( 'Support Email' )
    // Greyed-out hint shown while the field is empty.
    ->set_placeholder( 'help@example.com' )
    // Help text rendered below the input.
    ->set_description( 'Shown to customers in the site footer.' )
    // Blocks submission if the field is empty.
    ->set_required()
    // Override the auto-generated HTML id.
    ->set_id( 'acme-support-email' )
    // Append a CSS class.
    ->add_class( 'is-contact' )
    // data-* attributes for JS hooks.
    ->set_data( 'section', 'contact' )
    // HTML5 pattern attribute (runs before browser email validation).
    ->set_pattern( '.+@.+\..+' )
    // HTML rendered inside the wrapper before / after the input.
    ->set_after( '<small>This address receives every contact-form submission.</small>' )
    // Arbitrary HTML attributes.
    ->set_attribute( 'autocomplete', 'email' )
    // Override the default sanitiser (sanitize_email) with a WordPress callable.
    ->set_sanitize( 'sanitize_email' )
    // Server-side validation — a built-in WordPress function by name.
    ->set_validate( 'is_email' );
```

### Phone

`Setting\Field\Phone` — `<input type="tel">`. Default sanitiser: `sanitize_text_field`.

Traits: `Placeholder`, `Data`, `Pattern`.

```php
use Respect\Validation\Validator as v;

Phone::new( 'support_phone' )
    // Visible label above the input.
    ->set_label( 'Support Phone' )
    // Greyed-out hint shown while the field is empty.
    ->set_placeholder( '+44 7700 900000' )
    // Help text rendered below the input.
    ->set_description( 'Include the international dialling code.' )
    // Blocks submission if the field is empty.
    ->set_required()
    // Override the auto-generated HTML id.
    ->set_id( 'acme-support-phone' )
    // Append a CSS class.
    ->add_class( 'is-contact' )
    // data-* attributes for JS hooks.
    ->set_data( 'format', 'international' )
    // HTML5 pattern — digits, spaces and an optional leading +.
    ->set_pattern( '\+?[0-9\s]+' )
    // HTML rendered after the input.
    ->set_after( '<small>Office hours: Mon–Fri 9:00–17:00 GMT.</small>' )
    // Arbitrary HTML attributes.
    ->set_attribute( 'autocomplete', 'tel' )
    // Override the default sanitiser.
    ->set_sanitize( fn( $v ) => preg_replace( '/\s+/', ' ', trim( (string) $v ) ) )
    // Server-side validation — plain callable.
    ->set_validate( fn( $v ) => (bool) preg_match( '/^\+?[0-9\s]{7,}$/', (string) $v ) );
```

### Url

`Setting\Field\Url` — `<input type="url">`. Default sanitiser: `esc_url_raw`.

Traits: `Placeholder`, `Data`, `Pattern`.

```php
Url::new( 'website' )
    // Visible label above the input.
    ->set_label( 'Website' )
    // Greyed-out hint shown while the field is empty.
    ->set_placeholder( 'https://example.com' )
    // Help text rendered below the input.
    ->set_description( 'Must start with https:// or http://.' )
    // Blocks submission if the field is empty.
    ->set_required()
    // Override the auto-generated HTML id.
    ->set_id( 'acme-website' )
    // Append a CSS class.
    ->add_class( 'is-wide' )
    // data-* attributes for JS hooks.
    ->set_data( 'scheme', 'https-preferred' )
    // HTML5 pattern — only allow https://.
    ->set_pattern( 'https://.+' )
    // HTML rendered after the input.
    ->set_after( '<small>Linked from the footer.</small>' )
    // Arbitrary HTML attributes.
    ->set_attribute( 'autocomplete', 'url' )
    // Override the default sanitiser (esc_url_raw) with a closure.
    ->set_sanitize( fn( $e ) => esc_url_raw( trim( (string) $e ) ) )
    // Server-side validation — plain closure using filter_var.
    ->set_validate( fn( $e ) => false !== filter_var( $e, FILTER_VALIDATE_URL ) );
```

### Password

`Setting\Field\Password` — `<input type="password">`. Default sanitiser: `sanitize_text_field`.

Traits: `Placeholder`, `Data`.

```php
use Respect\Validation\Validator as v;

Password::new( 'api_secret' )
    // Visible label above the input.
    ->set_label( 'API Secret' )
    // Greyed-out hint shown while the field is empty.
    ->set_placeholder( 'sk_live_…' )
    // Help text rendered below the input.
    ->set_description( 'Paste the secret key from your dashboard.' )
    // Blocks submission if the field is empty.
    ->set_required()
    // Renders the input as read-only (value still submitted).
    ->set_read_only()
    // Override the auto-generated HTML id.
    ->set_id( 'acme-api-secret' )
    // Append one or more CSS classes.
    ->add_class( 'is-sensitive' )
    ->add_class( 'wide' )
    // data-* attributes for JS hooks.
    ->set_data( 'min-length', '32' )
    ->set_data( 'reveal-toggle', 'true' )
    // HTML rendered inside the wrapper before / after the input.
    ->set_before( '<span class="prefix">sk_</span>' )
    ->set_after( '<a href="https://example.com/keys" target="_blank">Find your key</a>' )
    // Arbitrary HTML attributes.
    ->set_attribute( 'autocomplete', 'new-password' )
    // Boolean flag rendered as a bare HTML attribute.
    ->set_flag( 'autofocus' )
    // Override the default sanitiser (sanitize_text_field).
    ->set_sanitize( fn( $v ) => trim( (string) $v ) )
    // Server-side validation — callable or Respect\Validation\Validator.
    ->set_validate( v::stringType()->length( 32, null ) );
```

### Textarea

`Setting\Field\Textarea` — multi-line text. Default sanitiser: `sanitize_textarea_field`.

Traits: `Placeholder`, `Data`.

```php
Textarea::new( 'bio' )
    // Visible label above the input.
    ->set_label( 'Biography' )
    // Greyed-out hint shown while the field is empty.
    ->set_placeholder( 'Tell us about yourself...' )
    // Help text rendered below the input.
    ->set_description( 'Plain text only. Shown on the team page.' )
    // Blocks submission if the field is empty.
    ->set_required()
    // Visible row / column counts.
    ->set_rows( 8 )
    ->set_cols( 60 )
    // Override the auto-generated HTML id.
    ->set_id( 'acme-bio' )
    // Append a CSS class.
    ->add_class( 'is-wide' )
    // data-* attributes for JS hooks.
    ->set_data( 'max-words', '250' )
    // HTML rendered after the input.
    ->set_after( '<small>Maximum 250 words.</small>' )
    // Arbitrary HTML attributes.
    ->set_attribute( 'spellcheck', 'true' )
    // Override the default sanitiser (sanitize_textarea_field) with a string callable.
    ->set_sanitize( 'sanitize_textarea_field' )
    // Server-side validation — plain closure capping length.
    ->set_validate( fn( $e ) => strlen( (string) $e ) <= 2000 );
```

Field-specific methods:

| Method | Purpose |
|---|---|
| `set_rows( int )` | Visible row count. |
| `set_cols( int )` | Visible column count. |

### Hidden

`Setting\Field\Hidden` — `<input type="hidden">`. Default sanitiser: `sanitize_text_field`.

No traits. Use when you need to carry a value alongside the editable fields (a token, a schema version, a reference ID).

```php
Hidden::new( 'form_version' )
    // The stored value.
    ->set_value( 'v2' )
    // Override the auto-generated HTML id.
    ->set_id( 'acme-form-version' )
    // Append a CSS class (typically unused on hidden fields).
    ->add_class( 'acme-hidden' )
    // data-* attributes for JS hooks.
    ->set_data( 'purpose', 'version-token' )
    // Arbitrary HTML attributes.
    ->set_attribute( 'autocomplete', 'off' )
    // Override the default sanitiser.
    ->set_sanitize( fn( $v ) => trim( (string) $v ) );
```

****

## Number

### Number

`Setting\Field\Number` — `<input type="number">`. Default sanitiser casts to `int` when `decimal_places ≤ 1`, otherwise rounds the `float` to the configured precision.

Traits: `Placeholder`, `Data`, `Range`, `Options` (datalist suggestions).

```php
Number::new( 'price' )
    // Visible label above the input.
    ->set_label( 'Price (GBP)' )
    // Greyed-out hint shown while the field is empty.
    ->set_placeholder( '9.99' )
    // Help text rendered below the input.
    ->set_description( 'Excludes VAT.' )
    // Blocks submission if the field is empty.
    ->set_required()
    // Range constraints (from the Range trait).
    ->set_min( 0 )
    ->set_max( 10000 )
    ->set_step( 0.01 )
    // Number of decimal places — 0–1 stores as int, higher rounds a float.
    ->set_decimal_places( 2 )
    // Override the auto-generated HTML id.
    ->set_id( 'acme-price' )
    // Append a CSS class.
    ->add_class( 'is-monetary' )
    // data-* attributes for JS hooks.
    ->set_data( 'currency', 'GBP' )
    // Datalist suggestions (from the Options trait).
    ->set_option( '9.99', 'Starter' )
    ->set_option( '19.99', 'Pro' )
    // HTML rendered inside the wrapper before / after the input.
    ->set_before( '<span class="prefix">£</span>' )
    ->set_after( '<small>Excl. VAT.</small>' )
    // Arbitrary HTML attributes.
    ->set_attribute( 'inputmode', 'decimal' )
    // Override the default sanitiser.
    ->set_sanitize( fn( $e ) => round( (float) $e, 2 ) )
    // Server-side validation — WordPress/PHP built-in by name.
    ->set_validate( 'is_numeric' );
```

Field-specific methods:

| Method | Purpose |
|---|---|
| `set_decimal_places( int )` | `0`–`1` stores as `int`; higher values round a `float` to that precision. |

****

## Selection

### Select

`Setting\Field\Select` — `<select>` dropdown, single or multiple. Default sanitiser handles both scalar and array values.

Traits: `Multiple`, `Data`, `Options`.

```php
Select::new( 'role' )
    // Visible label above the input.
    ->set_label( 'Role' )
    // Help text rendered below the input.
    ->set_description( 'Controls which admin screens this user can access.' )
    // Blocks submission if nothing is selected.
    ->set_required()
    // Allow multiple selections (value becomes an array).
    ->set_multiple()
    // Options. The third argument creates / reuses an <optgroup>.
    ->set_option( 'admin',  'Administrator', 'Staff' )
    ->set_option( 'editor', 'Editor',        'Staff' )
    ->set_option( 'author', 'Author',        'Contributors' )
    // Override the auto-generated HTML id.
    ->set_id( 'acme-role' )
    // Append a CSS class.
    ->add_class( 'is-role-picker' )
    // data-* attributes for JS hooks.
    ->set_data( 'default', 'editor' )
    // HTML rendered after the input.
    ->set_after( '<small>Hold Ctrl/Cmd to select multiple.</small>' )
    // Arbitrary HTML attributes.
    ->set_attribute( 'size', '5' )
    // Override the default sanitiser.
    ->set_sanitize( fn( $e ) => is_array( $e ) ? array_map( 'sanitize_key', $e ) : sanitize_key( (string) $e ) )
    // Server-side validation — closure covering both scalar and array values.
    ->set_validate(
        fn( $e ) => is_array( $e )
            ? array_diff( $e, array( 'admin', 'editor', 'author' ) ) === array()
            : in_array( $e, array( 'admin', 'editor', 'author' ), true )
    );
```

### Radio

`Setting\Field\Radio` — single-choice radio group.

Traits: `Disabled`, `Data`, `Options`.

```php
use Respect\Validation\Validator as v;

Radio::new( 'plan' )
    // Visible label above the group.
    ->set_label( 'Plan' )
    // Help text rendered below the group.
    ->set_description( 'Choose one.' )
    // Blocks submission if nothing is selected.
    ->set_required()
    // Options.
    ->set_option( 'free',       'Free' )
    ->set_option( 'pro',        'Pro' )
    ->set_option( 'enterprise', 'Enterprise' )
    // Default value.
    ->set_value( 'free' )
    // Disable the whole group.
    ->set_disabled( false )
    // Override the auto-generated HTML id.
    ->set_id( 'acme-plan' )
    // Append a CSS class.
    ->add_class( 'is-plan-picker' )
    // data-* attributes for JS hooks.
    ->set_data( 'default', 'free' )
    // HTML rendered after the group.
    ->set_after( '<small>Upgrade any time.</small>' )
    // Server-side validation.
    ->set_validate( v::in( array( 'free', 'pro', 'enterprise' ) ) );
```

### Checkbox

`Setting\Field\Checkbox` — single on/off checkbox.

Traits: `Disabled`, `Data`, `Checked_Value`.

```php
Checkbox::new( 'newsletter' )
    // Visible label beside the checkbox.
    ->set_label( 'Subscribe to newsletter' )
    // Help text rendered below the checkbox.
    ->set_description( 'We send one email per month — unsubscribe any time.' )
    // Render the label after the input (default is before).
    ->label_after()
    // Value stored when checked (default is 'on').
    ->set_checked_value( 'subscribed' )
    // Default state.
    ->set_value( 'subscribed' )
    // Disable the checkbox.
    ->set_disabled( false )
    // Override the auto-generated HTML id.
    ->set_id( 'acme-newsletter' )
    // Append a CSS class.
    ->add_class( 'is-optin' )
    // data-* attributes for JS hooks.
    ->set_data( 'consent', 'marketing' )
    // Server-side validation — accept the checked value or an empty string.
    ->set_validate( fn( $v ) => in_array( $v, array( '', 'subscribed' ), true ) );
```

### Checkbox_Group

`Setting\Field\Checkbox_Group` — multiple checkboxes sharing one key. Stored as an array.

Traits: `Disabled`, `Data`, `Options`, `Checked_Value`.

```php
Checkbox_Group::new( 'features' )
    // Visible label above the group.
    ->set_label( 'Enabled Features' )
    // Help text rendered below the group.
    ->set_description( 'Tick any the plugin should activate on boot.' )
    // Options.
    ->set_option( 'search',   'Full-text search' )
    ->set_option( 'api',      'Public API' )
    ->set_option( 'webhooks', 'Webhooks' )
    // Value stored for each checked item (default is 'on').
    ->set_checked_value( '1' )
    // Default checked items.
    ->set_value( array( 'search' ) )
    // Disable the group.
    ->set_disabled( false )
    // Override the auto-generated HTML id.
    ->set_id( 'acme-features' )
    // Append a CSS class.
    ->add_class( 'is-feature-list' )
    // data-* attributes for JS hooks.
    ->set_data( 'section', 'features' )
    // Server-side validation — only allow known option keys.
    ->set_validate(
        fn( $v ) => is_array( $v )
            && array_diff( $v, array( 'search', 'api', 'webhooks' ) ) === array()
    );
```

****

## Specialised

### Colour

`Setting\Field\Colour` — HTML5 `<input type="color">`. Default sanitiser: `sanitize_text_field`.

Traits: `Data`, `Autocomplete`.

```php
use Respect\Validation\Validator as v;

Colour::new( 'brand_colour' )
    // Visible label above the input.
    ->set_label( 'Brand Colour' )
    // Help text rendered below the input.
    ->set_description( 'Used for buttons and links in the theme.' )
    // Default colour (hex).
    ->set_value( '#3858e9' )
    // Required on submit.
    ->set_required()
    // Override the auto-generated HTML id.
    ->set_id( 'acme-brand-colour' )
    // Append a CSS class.
    ->add_class( 'is-brand' )
    // data-* attributes for JS hooks.
    ->set_data( 'token', 'brand-primary' )
    // Disable browser autofill (from the Autocomplete trait).
    ->set_autocomplete( 'off' )
    // HTML rendered after the input.
    ->set_after( '<small>Stored as a hex code.</small>' )
    // Override the default sanitiser — accept only #rrggbb.
    ->set_sanitize( fn( $v ) => sanitize_hex_color( (string) $v ) ?: '#000000' )
    // Server-side validation — must be a hex colour.
    ->set_validate( v::regex( '/^#[0-9a-f]{6}$/i' ) );
```

### Media_Library

`Setting\Field\Media_Library` — opens the WordPress Media Library modal and stores the selected attachment's ID.

No traits. Read the value back with `wp_get_attachment_image()` / `wp_get_attachment_url()`.

```php
use Respect\Validation\Validator as v;

Media_Library::new( 'logo' )
    // Visible label above the picker.
    ->set_label( 'Site Logo' )
    // Help text rendered below the picker.
    ->set_description( 'Recommended size: 512×512. PNG with transparency preferred.' )
    // Required on submit.
    ->set_required()
    // Override the auto-generated HTML id.
    ->set_id( 'acme-logo' )
    // Append a CSS class.
    ->add_class( 'is-branding' )
    // data-* attributes for JS hooks.
    ->set_data( 'mime', 'image' )
    // HTML rendered after the picker.
    ->set_after( '<small>Stored as the attachment ID — render with wp_get_attachment_image().</small>' )
    // Server-side validation — must resolve to a real attachment.
    ->set_validate( fn( $v ) => (bool) wp_get_attachment_url( (int) $v ) );
```

### WP_Editor

`Setting\Field\WP_Editor` — full `wp_editor()` instance (TinyMCE + Quicktags). Default sanitiser: `wp_kses_post`.

Traits: `Placeholder`.

```php
WP_Editor::new( 'about' )
    // Visible label above the editor.
    ->set_label( 'About' )
    // Greyed-out hint shown while the field is empty.
    ->set_placeholder( 'Tell visitors about your site...' )
    // Help text rendered below the editor.
    ->set_description( 'Rich text. Shown on the About page.' )
    // Required on submit.
    ->set_required()
    // Options forwarded to wp_editor() — see _WP_Editors::parse_settings().
    ->set_options( array(
        'media_buttons' => true,
        'textarea_rows' => 12,
        'teeny'         => false,
        'tinymce'       => array(
            'toolbar1' => 'bold italic underline | bullist numlist | link unlink | undo redo',
        ),
    ) )
    // Override the auto-generated HTML id (used as the editor id too).
    ->set_id( 'acme-about' )
    // Append a CSS class to the wrapper.
    ->add_class( 'is-wysiwyg' )
    // data-* attributes for JS hooks.
    ->set_data( 'section', 'about' )
    // HTML rendered after the editor.
    ->set_after( '<small>HTML allowed — sanitised through wp_kses_post.</small>' )
    // Override the default sanitiser (wp_kses_post).
    ->set_sanitize( fn( $v ) => wp_kses_post( (string) $v ) );
```

Field-specific methods:

| Method | Purpose |
|---|---|
| `set_options( array )` | Options forwarded to `wp_editor()` — see [`_WP_Editors::parse_settings()`](https://developer.wordpress.org/reference/classes/_wp_editors/parse_settings/). |

### User_Picker

`Setting\Field\User_Picker` — type-ahead search-and-pick for WordPress users. The picker calls a bundled REST endpoint as the user types and stores the selected user's ID.

Traits: `Multiple`, `Data`, `Disabled`, `Placeholder`.

```php
User_Picker::new( 'site_owner' )
    // Visible label above the picker.
    ->set_label( 'Site Owner' )
    // Greyed-out hint shown inside the empty search box.
    ->set_placeholder( 'Search users...' )
    // Help text rendered below the picker.
    ->set_description( 'Contact details shown in the footer.' )
    // Required on submit.
    ->set_required()
    // Allow multiple users (value becomes an array of IDs).
    ->set_multiple()
    // Restrict the search to a single role slug.
    ->set_role( 'administrator' )
    // Disable the picker.
    ->set_disabled( false )
    // Override the auto-generated HTML id.
    ->set_id( 'acme-owner' )
    // Append a CSS class.
    ->add_class( 'is-owner-picker' )
    // data-* attributes for JS hooks.
    ->set_data( 'min-search', '2' )
    // Override the label shown for each matched user.
    // Callback signature: fn( WP_User $u ): string — defaults to $u->display_name.
    ->set_option_label(
        fn( WP_User $u ) => sprintf( '%s <%s>', $u->display_name, $u->user_email )
    )
    // Override the value stored for each matched user.
    // Callback signature: fn( WP_User $u ): string — defaults to (string) $u->ID.
    ->set_option_value( fn( WP_User $u ) => (string) $u->ID )
    // Server-side validation — must resolve to a real user.
    ->set_validate( fn( $v ) => (bool) get_userdata( (int) $v ) );
```

Read the value back:

```php
$owner_id = (int) $settings->get( 'site_owner' );
$owner    = get_userdata( $owner_id );
```

Field-specific methods:

| Method | Purpose |
|---|---|
| `set_role( string )` | Restrict search to a single role slug. |
| `set_option_label( callable )` | `fn( WP_User $u ): string` — controls the displayed label. |
| `set_option_value( callable )` | `fn( WP_User $u ): string` — controls the stored value. |

### Post_Picker

`Setting\Field\Post_Picker` — type-ahead search-and-pick for posts. The picker calls a bundled REST endpoint as the user types and stores the selected post's ID.

Traits: `Multiple`, `Data`, `Disabled`, `Placeholder`, `Query`.

```php
Post_Picker::new( 'featured' )
    // Visible label above the picker.
    ->set_label( 'Featured Products' )
    // Greyed-out hint shown inside the empty search box.
    ->set_placeholder( 'Search products...' )
    // Help text rendered below the picker.
    ->set_description( 'Shown on the home page carousel.' )
    // Required on submit.
    ->set_required()
    // Allow multiple selections (value becomes an array of IDs).
    ->set_multiple()
    // Shortcut — restrict the search to a single post type.
    ->set_post_type( 'product' )
    // Full WP_Query args — takes precedence over set_post_type().
    ->set_query_args( array(
        'post_type'   => 'product',
        'post_status' => 'publish',
        'meta_query'  => array(
            array( 'key' => '_featured', 'value' => 'yes' ),
        ),
    ) )
    // Disable the picker.
    ->set_disabled( false )
    // Override the auto-generated HTML id.
    ->set_id( 'acme-featured' )
    // Append a CSS class.
    ->add_class( 'is-product-picker' )
    // data-* attributes for JS hooks.
    ->set_data( 'source', 'featured-products' )
    // Override the label shown for each matched post.
    // Callback signature: fn( WP_Post $p ): string — defaults to $p->post_title.
    ->set_option_label(
        fn( WP_Post $p ) => sprintf( '%s (#%d)', $p->post_title, $p->ID )
    )
    // Server-side validation — must resolve to a published post.
    ->set_validate( fn( $v ) => 'publish' === get_post_status( (int) $v ) );
```

Read the value back:

```php
$post_id = (int) $settings->get( 'featured' );
$post    = get_post( $post_id );
```

Field-specific methods:

| Method | Purpose |
|---|---|
| `set_post_type( string )` | Shortcut for `set_query_args( [ 'post_type' => … ] )`. |
| `set_query_args( array )` | Full `WP_Query` arg list. Takes precedence over `set_post_type()`. |
| `set_option_label( callable )` | `fn( WP_Post $p ): string` — controls the displayed label. |
| `set_option_value( callable )` | `fn( WP_Post $p ): string` — controls the stored value. |

****

## Composite

### Repeater

`Setting\Field\Repeater` — repeating set of sub-fields. Values are stored as an array of arrays. Repeaters **cannot be nested** inside another repeater.

Traits: `Disabled`, `Data`.

```php
Repeater::new( 'social_links' )
    // Visible label above the repeater.
    ->set_label( 'Social Links' )
    // Help text rendered below the repeater.
    ->set_description( 'Shown in the footer. Drag to reorder.' )
    // Label on the "add row" button (default: 'Add').
    ->set_add_to_group_label( 'Add Link' )
    // Layout of each row — 'row' (default) or 'columns'.
    ->set_layout( 'columns' )
    // CSS class applied to each row wrapper (default: 'repeater-group').
    ->set_group_class( 'acme-social-row' )
    // Child fields — any Field except another Repeater.
    ->add_field(
        Text::new( 'platform' )
            ->set_label( 'Platform' )
            ->set_placeholder( 'Twitter' )
            ->set_required()
    )
    ->add_field(
        Url::new( 'url' )
            ->set_label( 'URL' )
            ->set_placeholder( 'https://twitter.com/acme' )
            ->set_required()
    )
    // Disable the whole repeater (adds / removes / edits all blocked).
    ->set_disabled( false )
    // Override the auto-generated HTML id.
    ->set_id( 'acme-social' )
    // Append a CSS class to the outer wrapper.
    ->add_class( 'is-repeater' )
    // data-* attributes for JS hooks.
    ->set_data( 'max-rows', '10' )
    // Server-side validation — must have at least one row.
    ->set_validate( fn( $v ) => is_array( $v ) && count( $v ) > 0 );
```

Field-specific methods:

| Method | Purpose |
|---|---|
| `add_field( Field )` | Add a child field. Throws if another repeater is passed. |
| `set_add_to_group_label( string )` | Label on the "add row" button. Default `'Add'`. |
| `set_layout( string )` | `'row'` (default) or `'columns'`. |
| `set_group_class( string )` | CSS class applied to each row wrapper. Default `'repeater-group'`. |
