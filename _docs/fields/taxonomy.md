---
title: Taxonomy
---

## Overview

The taxonomy field allows you to select one or multiple taxonomy terms. This field has several settings that can be displayed as a: simple select dropdown, checkbox list, or beautiful select dropdown with select2 library.

If the taxonomy is hierarchical, you are able to display the field as a select or checkbox tree, e.g. show childen terms when parent term is selected.

## Screenshots

Beautiful select with select2 library (default):

![taxonomy select](https://i.imgur.com/PzPkqur.png)

Checkbox list:

![taxonomy checkbox list](https://i.imgur.com/Ook0RwT.png)

Checkbox tree:

![taxonomy checkbox tree](https://i.imgur.com/iLMFVBY.png)

Radio list:

![radio list](https://i.imgur.com/D7Q5mUA.png)

Radio list inline:

![radio list inline](https://i.imgur.com/XMhMjoN.png)

Select tree:

![taxonomy select tree](https://i.imgur.com/orDECYA.png)

## Settings

Besides the [common settings](/field-settings/), this field has the following specific settings:

Name | Description
--- | ---
`taxonomy` | Taxonomy slug(s). Can be string for single taxonomy or array for taxonomies. Required.
`query_args` | Query arguments for getting taxonomy terms. Uses same arguments as [get_terms()](https://developer.wordpress.org/reference/functions/get_terms/). Optional.
`placeholder` | The placeholder for the select box. Default is "Select a {taxonomy label}". Applied only when the `field_type` is a select field.
`add_new` | Allow users to create a new term when submitting the post (`true` or `false`). Added in v4.18.0.
`remove_default` | Remove the default WordPress taxonomy meta box. Added in v4.18.3.
`field_type` | How the terms are displayed? See below.

This field inherits the look and field (and settings) from other fields, depending on the `field_type`, which accepts the following value:

Field type | Description | Settings inherited from
--- | ---
`select` | Simple select dropdown. | [select](/fields/select/)
`select_advanced` | Beautiful select dropdown using the select2 library. This is the default value. | [select_advanced](/fields/select_advanced/)
`select_tree` | Hierachical list of select boxes which allows to select multiple items (select/deselect parent item will show/hide child items). Applied only when the taxonomy type is hierarchical (like pages). | [select](/fields/select/)
`checkbox_list` | Flatten list of checkboxes which allows to select multiple items. | [checkbox_list](/fields/checkbox-list/)
`checkbox_tree` | Hierachical list of checkboxes which allows to select multiple items (select/deselect parent item will show/hide child items). Applied only when the taxonomy type is hierarchical (like pages). | [checkbox_list](/fields/checkbox-list/)
`radio_list` | Flatten list of radio boxes which allows to select only 1 item. | [radio](/fields/radio/)

Note that for `select_tree`, `checkbox_list`, `checkbox_tree`, the `multiple` setting is always set to `true`.

## Sample code

This code shows pages in select2 dropdown:

```php
array(
    'name'       => 'Taxonomy',
    'id'         => 'taxonomy',
    'type'       => 'taxonomy',

    // Taxonomy slug.
    'taxonomy'   => 'category',

    // How to show taxonomy.
    'field_type' => 'select_advanced',
),
```

The code below shows pages in a checkbox tree format:

```php
array(
    'name'       => 'Taxonomy',
    'id'         => 'taxonomy',
    'type'       => 'taxonomy',

    // Taxonomy slug.
    'taxonomy'   => 'category',

    // How to show taxonomy.
    'field_type' => 'checkbox_tree',
),
```

## Data

This field **does not save any value** in the database. Instead of that, it **sets the taxonomy terms for the current being edited post**. In short, it behaves exactly like the "Category" and "Tags" meta boxes.

The purpose of this field is to replace the default WordPress meta box for taxonomy and offer more options to control how it displays.

For this reason, if you have 2 `taxonomy` field, and select different values for them, after saving, they still show the same value.

If you prefer saving data, check out [taxonomy advanced](/fields/taxonomy-advanced/).

## Template usage

Getting value of this field can be done via the helper function:

```php
$terms = rwmb_meta( $field_id );
if ( ! empty( $terms ) && ! is_wp_error( $terms ) ) {
    foreach ( $terms as $term ) {
        echo '<p>', $term->name, '</p>';
    }
}
```

If the field is neither cloneable or multiple, e.g. it has single value, then the [rwmb_meta()](/rwmb-meta/) function returns the single term object:

```php
$term = rwmb_meta( $field_id );
echo '<p>', $term->name, '</p>';
```

The code above shows the term name only. If you want to show term link, use this code:

```php
$term = rwmb_meta( 'pub_type_field' );
printf( '<a href="%s">%s</a>', get_term_link( $term ), $term->name );
```

If you just want to output selected terms in an unordered list, use this code:

```php
rwmb_the_value( $field_id );
```

Read more about [rwmb_meta()](/rwmb-meta/) and [rwmb_the_value()](/rwmb-the-value/).
