# WooCommerce Smart Snippet
Customize your WooCommerce store using those quick snippets — no need to create a child theme or technical knowledge. Use [Code Snippets](https://wordpress.org/plugins/code-snippets) Plugin to add those code without `Child Theme`.

### WooCommerce discount on 2nd cart item
```php
add_action( 'woocommerce_cart_calculate_fees', 'discount_on_2nd_cart_item', 10, 1 );
function discount_on_2nd_cart_item( $cart ) {

if ( is_admin() && ! defined( 'DOING_AJAX' ) )
    return;

    // Initialising
$count = 0;
    $percentage = 50; // 3 %

    // Iterating though each cart items
    foreach ( $cart->get_cart() as $cart_item ) {
        $count++;
        if( 2 == $count){ // Second item only
            $price = $cart_item['data']->get_price(); // product price
            $discount = $price * $percentage / 100; // calculation
            $second_item = true;
            break; // stop the loop
        }
    }
    if( isset($discount) && $discount > 0 )
        $cart->add_fee( __("2nd item 50% discount", 'woocommerce'), -$discount );
}
```

--- 

### Disable Out of Stock Variations
```php
function bbloomer_grey_out_variations_out_of_stock( $is_active, $variation ) {
    if ( ! $variation->is_in_stock() ) return false;
    return $is_active;
}

add_filter( 'woocommerce_variation_is_active', 'bbloomer_grey_out_variations_out_of_stock', 10, 2 )
```

---

### Add Content Under “Place Order” Button
```php
add_action( 'woocommerce_review_order_after_submit', 'our_privacy_message_below_checkout_button' );
 
function our_privacy_message_below_checkout_button() {
   echo '<p><small>Your personal data will help us create your account and to support your user experience throughout this website. Please have a look at our <a href="/privacy-policy" target=_blank">Privacy Policy</a> for more information on how we use your personal data</small></p>';
}
```

---

### Product short description / excerpt on Cart / Checkout page
```php
function wc_checkout_description_on_cart_checkout( $other_data, $cart_item ) {
    $post_data = get_post( $cart_item['product_id'] );
    $other_data[] = array( 'name' =>  $post_data->post_excerpt );
    return $other_data;
}

add_filter( 'woocommerce_get_item_data', 'wc_checkout_description_on_cart_checkout', 10, 2 );

// This one much batter
function add_excerpt_in_cart_item_name( $item_name,  $cart_item,  $cart_item_key ){
    $excerpt = get_the_excerpt($cart_item['product_id']);
    $style = ' style="font-size:14px; line-height:normal;"';
    $excerpt_html = '<br>
        <p name="short-description"'.$style.'>'.$excerpt.'</p>';

    return $item_name . $excerpt_html;
}
add_filter( 'woocommerce_cart_item_name', 'add_excerpt_in_cart_item_name', 10, 3 );
```

---

### Remove `SKIP LOGIN` from Multi-Step Checkout page
```js
if( $('#timeline-login').hasClass('active') ) {
  $('#form_actions').hide();
}
```

---

### Rename Coupon Code to Discount Code on `Cart & Checkout` page
:point_right: jQuery Code
```js
$('.checkout_coupon.woocommerce-form-coupon p:first-child').text('If you have a discount code, please apply it below.');
```

:point_right: PHP Code
```php
add_filter('gettext', 'woocommerce_rename_coupon_field_on_cart', 10, 3 );
add_filter('gettext', 'woocommerce_rename_coupon_field_on_cart', 10, 3 );
add_filter('woocommerce_coupon_error', 'rename_coupon_label', 10, 3);
add_filter('woocommerce_coupon_message', 'rename_coupon_label', 10, 3);
add_filter('woocommerce_cart_totals_coupon_label', 'rename_coupon_label',10, 1);
add_filter('woocommerce_checkout_coupon_message', 'woocommerce_rename_coupon_message_on_checkout' );


function woocommerce_rename_coupon_field_on_cart( $translated_text, $text, $text_domain ) {
    // bail if not modifying frontend woocommerce text
    if ( is_admin() || 'woocommerce' !== $text_domain ) {
        return $translated_text;
    }
    if ( 'Coupon:' === $text ) {
        $translated_text = 'Discount Code:';
    }

    if ('Coupon has been removed.' === $text){
        $translated_text = 'Discount code has been removed.';
    }

    if ( 'Apply coupon' === $text ) {
        $translated_text = 'Apply Discount';
    }

    if ( 'Coupon code' === $text ) {
        $translated_text = 'Discount Code';
    
    } 

    return $translated_text;
}


// rename the "Have a Coupon?" message on the checkout page
function woocommerce_rename_coupon_message_on_checkout() {
    return 'Have an Discount Code?' . ' ' . __( '<a href="#" class="showcoupon">Click here to enter your code</a>', 'woocommerce' ) . '';
}


function rename_coupon_label($err, $err_code=null, $something=null){
    $err = str_ireplace("Coupon","Discount Code ",$err);
    return $err;
}
```


---

### Change `Return to shop` text & URL
```php
// URL
add_filter( 'woocommerce_return_to_shop_redirect', 'bbloomer_change_return_shop_url' );
 
function bbloomer_change_return_shop_url() {
    return "/subscribe";
}

// Text
add_filter( 'gettext', 'change_woocommerce_return_to_shop_text', 20, 3 );
function change_woocommerce_return_to_shop_text( $translated_text, $text, $domain ) {
    switch ( $translated_text ) {
        case 'Return to shop' :
            $translated_text = __( 'SUBSCRIBE', 'woocommerce' );
            break;
    }
    return $translated_text; 
}
```

---

### Block specific states on checkout
```php
add_filter( 'woocommerce_states', 'custom_ca_states', 10, 1 );
function custom_ca_states( $states ) {
    $non_allowed_ca_states = array( 'AB', 'BC', 'MB', 'NB', 'NL', 'NS', 'PE', 'QC', 'SK', 'NT', 'NU', 'YT'); 

    // Loop through your non allowed CA states and remove them
    foreach( $non_allowed_ca_states as $state_code ) {
        if( isset($states['CA'][$state_code]) )
            unset( $states['CA'][$state_code] );
    }
    return $states;
}
```

---

### Display the discount percentage on the sale badge
```php
add_filter( 'woocommerce_sale_flash', 'add_percentage_to_sale_badge', 20, 3 );
function add_percentage_to_sale_badge( $html, $post, $product ) {
    if( $product->is_type('variable')){
        $percentages = array();

        // Get all variation prices
        $prices = $product->get_variation_prices();

        // Loop through variation prices
        foreach( $prices['price'] as $key => $price ){
            // Only on sale variations
            if( $prices['regular_price'][$key] !== $price ){
                // Calculate and set in the array the percentage for each variation on sale
                $percentages[] = round(100 - ($prices['sale_price'][$key] / $prices['regular_price'][$key] * 100));
            }
        }
        // We keep the highest value
        $percentage = max($percentages) . '%';
    } else {
        $regular_price = (float) $product->get_regular_price();
        $sale_price    = (float) $product->get_sale_price();

        $percentage    = round(100 - ($sale_price / $regular_price * 100)) . '%';
    }
    return '<span class="onsale">' . esc_html__( 'SALE', 'woocommerce' ) . ' ' . $percentage . '</span>';
}
```