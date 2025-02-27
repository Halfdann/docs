---
title: MB Frontend Submission
---

## Overview

MB Frontend Submission is a Meta Box extension which lets developers create custom forms so users can submit posts on the front end of a website.

The extension supports both traditional post fields and custom fields, and you can also embed forms anywhere on your site using shortcodes — with no extra coding required.

Here is a screenshot of a front-end form:

![submission form](https://i.imgur.com/jfXHqSc.png)

MB Frontend Submission brings the meta boxes and custom fields from the back end to the front end. It supports all field types and all Meta Box extensions (columns, group, conditional logic, etc.).

Using this extension, you can create powerful forms for users to submit posts on the front end and control how it work effortlessly.

For more information, please see the [extension page](https://metabox.io/plugins/mb-frontend-submission/)

{% include installation.html %}

**Important:** This extension requires Meta Box version 4.11+. If you're using an older version of the plugin, please update.

## Creating frontend forms

To create a frontend form, you need to [create a meta box first](/creating-meta-boxes/). Don't forget to set the meta box `id`. You need that to embed the form in the frontend.

Then you can show the form in the frontend using the following shortcode:

```php
[mb_frontend_form id="Meta box ID"]
```

You can put the shortcode inside a page or post content to show it. If you want to embed the form using code, please use the following code:

```php
$form = '[mb_frontend_form id="Meta box ID"]';
echo do_shortcode( $form );
```

## Shortcode attributes

Attributes|Description
---|---
`id`|Meta box ID(s). If multiple meta boxes, enter their IDs separated by commas. Required.
`edit`|Allo users to edit the post after submitting. `true` or `false` (default). Optional. Added in v1.5.0.
`allow_delete`|Allo users to delete the submitted post. `true` or `false` (default). Optional. Added in v1.6.0.
`post_type`|The submitted post type. Optional. Default is the first post type defined in the meta box. If meta box is made for multiple post types, you should set this attribute to the correct one.
`post_id`|The post ID. Optional. Used only when you want to update an existing post. If you want to pass the ID of the current post, set it to `current`.
`post_status`|The status for submitted posts. See [the list here](https://codex.wordpress.org/Post_Status).
`post_fields`|List of post fields you want to show in the frontend, separated by comma. Supported following fields: `title`, `content`, `excerpt`, `date`, `thumbnail`.
`label_title`| Label for post title field.
`label_content` | Label for post content field.
`label_excerpt`| Label for post excerpt field.
`label_date` | Label for post date field.
`label_thumbnail` | Label for post thumbnail field.
`submit_button`|The submit button text.
`delete_button`|The delete button text.
`confirmation`|The text for the confirmation message when the form is successfully submitted.
`delete_confirmation`|The text for the confirmation message when the post is deleted.



## Dynamic population

In order to make the frontend form flexible, sometimes it's more convenient to set the shortcode attributes via code or something else rather than fixed it in above format.

The dyanamic population feature in MB Frontend Submission extension allows you to dynamically populate a shortcode attribute with a value. This value can be passed via query string and/or hook.

### Query string

You can populate a shortcode attribute via the query string by appending the dynamic population parameter for the attribute to the end of your form URL along with your custom value.

```
http://siteurl.com/form-url/?rwmb_frontend_field_post_id=123
```

The query parameter is `rwmb_frontend_field_{$attribute}`.

Assuming that a frontend form is on the page at this URL, then the `post_id` attribute would be populated with the value `123`.

### Hooks

Shortcode attributes can also be populated via WordPress hooks. This example below change the `post_id` to `123`:

```php
add_filter( 'rwmb_frontend_field_value_post_id', 'my_custom_population_function', 10, 2 );
function my_custom_population_function( $value, $args ) {
    if ( $args['id'] === 'your_meta_box_id' ) { // Only filter for a specific form.
        $value = 123;
    }
    return $value;
}
```

This snippet would be pasted in your theme's `functions.php` file or your plugin's PHP file.

The filter has the following format:

```php
$value = apply_filters( "rwmb_frontend_field_value_{$attribute}", $value, $args );
```

The callback function accepts 2 parameters: the attribute value and the array of all attributes. You should use `$args['id]` to check if you're filter for the right form.


## Post template files

The plugin allows you to use a custom template files for post fields and the confirmation message (the fields defined by Meta Box is controlled by the Meta Box plugin and can't be changed).

The plugin will look for a template file with the following order:

- Inside a folder `mb-frontend-submission` of your child theme
- Inside a folder `mb-frontend-submission` of your parent theme
- In the plugin's `templates` folder

In order to overwrite the output of post fields, please following the steps below:

- Create a folder `mb-frontend-submission` in your theme.
- Copy a template file that you want to change from plugins's `templates` folder to the new `mb-frontend-submission` folder, keeping the same folder structure.
- Modify the new template file.

## Validation

There are 2 ways to validate fields: on the front end with JavaScript and on the back end with PHP.

For front-end validation with JavaScript, please see [this documentation](https://docs.metabox.io/validation/).

To validate on the back end with PHP, please use the `rwmb_frontend_validate` filter as follows:

```php
add_filter( 'rwmb_frontend_validate', function( $validate, $config ) {
    // Check only if you're on the right form.
    if ( 'your-meta-box-id' !== $config['id'] ) {
        return $validate;
    }
    // Check if users have selected files for an image upload field.
    if ( empty( $_POST['image_upload_field'] ) ) {
        $validate = false; // Return false to show an error message.
    }
    return $validate;
}, 10, 2 );
```

If you want to show a custom error message, simply return a string for the filter, like this:


```php
add_filter( 'rwmb_frontend_validate', function( $validate, $config ) {
    if ( 'your-meta-box-id' !== $config['id'] ) {
        return $validate;
    }
    if ( empty( $_POST['image_upload_field'] ) ) {
        $validate = 'Please select at least one image to upload'; // Return a custom error message
    }
    return $validate;
}, 10, 2 );
```

## Hooks

In order to allow developers to do other things when the form is submitted, we have created some actions and filters.

### General hooks

`rwmb_frontend_redirect`

This filter allows you to change the URL of the redirect page after form is submitted. It accepts 1 parameter `$config` - the shortcode attributes.

```php
$redirect = apply_filters( 'rwmb_frontend_redirect', $redirect, $config );
```

Note that `$config['post_id']` has the submitted post ID.

### Form actions

`rwmb_frontend_before_process`

This action fires before the form is processed, e.g. saved or updated. It accepts one parameter `$config` - the form configuration, taken from the shortcode attributes.

`rwmb_frontend_after_process`

This action fires after the form is processed, e.g. saved or updated. It accepts 2 parameters:

- `$config` - the form configuration, taken from the shortcode attributes.
- `$post_id` - the submitted post ID

You can use this action to do a custom redirect to your Thank you page or send an email notification.

```php
add_action( 'rwmb_frontend_after_process', function( $config, $post_id ) {
    if ( 'my-meta-box' === $config['id'] ) {
        wp_mail( 'admin@domain.com', 'New submission', 'A new post has been just submitted.' );

        wp_safe_redirect( 'thank-you' );
        die;
    }
}, 10, 2 );
```

`rwmb_frontend_before_form`

This action fires before form output. It accepts one parameter `$config` - the form configuration, taken from the shortcode attributes.

`rwmb_frontend_after_form`

This action fires after form output. It accepts one parameter `$config` - the form configuration, taken from the shortcode attributes.

`rwmb_frontend_before_display_confirmation`

This action fires before the confirmation message is displayed. It accepts one parameter `$config` - the form configuration, taken from the shortcode attributes.

`rwmb_frontend_after_display_confirmation`

This action fires after the confirmation message is displayed. It accepts one parameter `$config` - the form configuration, taken from the shortcode attributes.

`rwmb_frontend_before_submit_button`

This action fires before the submit button is displayed. It accepts one parameter `$config` - the form configuration, taken from the shortcode attributes.

`rwmb_frontend_after_submit_button`

This action fires after the submit button is displayed. It accepts one parameter `$config` - the form configuration, taken from the shortcode attributes.

### Form filters

`rwmb_frontend_validate`

This filter is used to check if the form is validated. You can use this filter to add custom check for the data before it's processed.

```php
apply_filters( 'rwmb_frontend_validate', $validate, $config );
```

The filter has 2 parameter:

- `$validate`: the returned value of validation. If `true`, then the validation is successful. `false` - if not and the plugin will show a default error message. If you want to show a custom error message, just return it as a string. See the **Validation** section above.
- `$config`: the form configuration.

### Post data filters

`rwmb_frontend_insert_post_data`

This filter is used to modify the submitted post data before it's passed to the `wp_insert_post` function to **create a new post**. It accepts 2 parameters: the array of post data and the shortcode parameters.

```php
$data = apply_filters( 'rwmb_frontend_insert_post_data', $data, $config );
```

`rwmb_frontend_update_post_data`

This filter is used to modify the submitted post data before it's passed to the `wp_update_post` function to **update an existing post**. It accepts 2 parameters: the array of post data and the shortcode parameters.

```php
$data = apply_filters( 'rwmb_frontend_update_post_data', $data, $config );
```

### Post field filters

Sometimes you want to add more things to the post fields, such as make post title required, add description for post content, or even add a custom HTML before the post thumbnail. You can do that with the following post filter:

```php
// Post title
$field = apply_filters( 'rwmb_frontend_post_title', array(
    'type' => 'text',
    'name' => 'Title',
    'id'   => 'post_title',
) );

// Post thumbnail
$field = apply_filters( 'rwmb_frontend_post_thumbnail', array(
    'type'             => 'image',
    'name'             => 'Thumbnail',
    'id'               => '_thumbnail_id',
    'max_file_uploads' => 1,
) );

// Post excerpt
$field   = apply_filters( 'rwmb_frontend_post_excerpt', array(
    'type' => 'textarea',
    'name' => 'Excerpt',
    'id'   => 'post_excerpt',
) );

// Post date
$field = apply_filters( 'rwmb_frontend_post_date', array(
    'type' => 'datetime',
    'name' => 'Date',
    'id'   => 'post_date',
) );

// Post content
$field   = apply_filters( 'rwmb_frontend_post_content', array(
    'type' => 'wysiwyg',
    'name' => 'Content',
    'id'   => 'post_content',
) );
```

Each post field is treated as a normal Meta Box field (see here for full list of [field attributes](/docs/field-settings/)).

So, to make the post title required, you can do like this:

```php
add_filter( 'rwmb_frontend_post_title', function( $field ) {
    $field['required'] = true;
    return $field;
} );
```

### Post actions

`rwmb_frontend_before_save_post`

This action fires before the post is created or updated.

```
do_action( 'rwmb_frontend_before_save_post', $object );
```

The action accepts 1 parameter: the instance of the `MB_Frontend_Post` class, which handles the submission. It has the following public properties that you can use:

- `$post_type`: The current post type
- `$post_id`: The submitted post ID
- `$fields`: The post fields
- `$config`: The configuration, taken from the shortcode attributes

`rwmb_frontend_after_save_post`

This action fires after the post is created or updated. At that time, all the custom fields in the meta box are already saved into the post meta.

```
do_action( 'rwmb_frontend_after_save_post', $object );
```

The action accepts 1 parameter: the instance of the `MB_Frontend_Post` class, which handles the submission. It has the following public properties that you can use:

- `$post_type`: The current post type
- `$post_id`: The submitted post ID
- `$fields`: The post fields
- `$config`: The configuration, taken from the shortcode attributes

## Notes

### Upload files / images

To be able to upload files or images (via fields `file_advanced`, `file_upload`, `image_advanced`, `image_upload`), users have to login and proper capability `upload_files` to access the Media Library. If your users don't have that capability (if they have subscriber role), then the upload fields don't work. In that case, you can add the capability for that role as follows:

```php
function mb_allow_subscriber_uploads() {
    if ( is_admin() ) {
        return;
    }

    // Replace 'subscriber' with the required role to update, can also be contributor.
    $subscriber = get_role( 'subscriber' );
    $subscriber->add_cap( 'upload_files' );
}
add_action( 'init', 'mb_allow_subscriber_uploads' );
```

Another solution is using `file` or `image` fields. Both of them works similar. They just don't have a nice UI, but they do the job very well.

### Styling

The extension outputs the default fields' HTML with CSS comes from Meta Box plugin. The style is basic and works in most cases. However, to make it match perfectly with your theme's style, you need to do some work with CSS.

### Caching

As you might know, Meta Box uses [nonces](https://codex.wordpress.org/WordPress_Nonces) to prevent misuse or malicious requests. As the nonce fields are outputted directly in the HTML, they might be cached by caching plugins such as W3 Total Cache, WP Super Cache, etc. And thus, the verification of the nonce might not work properly and break the form submission. In this case, please do not cache the page where the form is embeded (both caching plugins allow you to do that). For more information, please read this [technical article](https://myatus.com/p/wordpress-caching-and-nonce-lifespan/).
