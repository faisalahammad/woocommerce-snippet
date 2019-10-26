# WooCommerce Smart Snippet
Customize your WooCommerce store using those quick snippets — no need to create a child theme or technical knowledge. Just follow our videos on YouTube.

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

### Add Content Under “Place Order” Button
```php
add_action( 'woocommerce_review_order_after_submit', 'our_privacy_message_below_checkout_button' );
 
function our_privacy_message_below_checkout_button() {
   echo '<p><small>Your personal data will help us create your account and to support your user experience throughout this website. Please have a look at our <a href="/privacy-policy" target=_blank">Privacy Policy</a> for more information on how we use your personal data</small></p>';
}
```

