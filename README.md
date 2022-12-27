An easy example for custom post type and meta box for custom post type in WordPress. 
Copy & paste the below code into your functions.php. 
Then feel the magic. :)

```
function create_custom_post_type()
{
    $args = array(
        'labels' => array(
            'name' => 'Books',
            'singular_name' => 'Book',
        ),
        'public' => true,
        'has_archive' => true,
        'supports' => array('title', 'editor', 'author', 'thumbnail'),
    );
    register_post_type('book', $args);
}
add_action('init', 'create_custom_post_type');

function add_custom_fields_to_custom_post_type()
{
    add_meta_box('custom_fields_meta_box', 'Custom Fields', 'display_custom_fields_meta_box', 'book', 'normal', 'high');
}
add_action('add_meta_boxes', 'add_custom_fields_to_custom_post_type');

function display_custom_fields_meta_box($post)
{
    ?>
<div class="hcf_box">
    <style scoped>
        .hcf_box{
            display: grid;
            grid-template-columns: max-content 1fr;
            grid-row-gap: 10px;
            grid-column-gap: 20px;
        }
        .hcf_field{
            display: contents;
        }
    </style>
    <p class="meta-options hcf_field">
        <label for="hcf_author">Author</label>
        <input id="hcf_author" type="text" name="hcf_author" value="<?php echo esc_attr(get_post_meta(get_the_ID(), 'hcf_author', true)); ?>">

        <ul>
    <li><strong>Author: </strong><?php echo esc_attr(get_post_meta(get_the_ID(), 'hcf_author', true)); ?></li>
</ul>
    </p>
</div>
  <?php
}

function save_custom_fields($post_id)
{
    if (defined('DOING_AUTOSAVE') && DOING_AUTOSAVE) {
        return;
    }

    if ($parent_id = wp_is_post_revision($post_id)) {
        $post_id = $parent_id;
    }
    $fields = [
        'hcf_author',
    ];
    foreach ($fields as $field) {
        if (array_key_exists($field, $_POST)) {
            update_post_meta($post_id, $field, sanitize_text_field($_POST[$field]));
        }
    }
}
add_action('save_post', 'save_custom_fields');

?>
```
