//ADD ALL OF THIS TO YOUR FUNCTIONS.PHP FILE
//THIS WORKS FOR BOTH SINGLE INSTALL AND MULTISITE INSTALLS

function get_first_image( $postID ) {
    $args = array(
      'numberposts' => 1,
      'order' => 'ASC',
      'post_mime_type' => 'image',
      'post_parent' => $postID,
      'post_status' => null,
      'post_type' => 'attachment',
    );

    $attachments = get_children( $args );

    if ( $attachments ) {
      foreach ( $attachments as $attachment ) {
        $image_attributes = wp_get_attachment_metadata( $attachment->ID );
        return $image_attributes;
      }
    }
}

//ADD USER PROFILE FIELDS TO USERS ACCOUNT MENU
function add_contact_methods($profile_fields) {

    // Add new fields
    $profile_fields['twitter'] = 'Twitter Username';
    $profile_fields['twitter_id'] = 'Twitter ID';
    $profile_fields['facebook'] = 'Facebook URL';
    $profile_fields['fb_page_id'] = 'Facebook Page ID';
    $profile_fields['fb_admin'] = 'Facebook Admin';
    $profile_fields['gplus'] = 'Google+ URL';
    $profile_fields['pin_confirm'] = 'Pinterest Confirm';

    // Remove old fields
    //unset($profile_fields['aim']);

    return $profile_fields;
}
//TO EXTRACT THIS DATA USE "get_the_author_meta('twitter');"
add_filter('user_contactmethods', 'add_contact_methods');

//ADDING THE OPEN GRAPH IN THE LANGUAGE ATTRIBUTES
function doctype_opengraph($output) {
    return $output . '
    xmlns:og="http://opengraphprotocol.org/schema/"
    xmlns:fb="http://www.facebook.com/2008/fbml"';
}
add_filter('language_attributes', 'doctype_opengraph');

//ADD OPEN GRAPH META INFO
function social_meta() {
    global $post;

    if( is_single() || is_page() || is_home() ) {
      if(has_post_thumbnail($post->ID)) {
        $featured_img = wp_get_attachment_metadata(get_post_thumbnail_id( $post->ID ));
        //THIS IMAGE SRC CODE IS SETUP FOR MULTISITE AS IS - IF YOU WANT THIS SETUP FOR SINGLE SITE USE
        //$img_src = get_site_url() . '/wp-content/uploads/' . '/' . $featured_img['file'];
        $img_src = get_site_url() . '/wp-content/uploads/sites/' . get_current_blog_id() . '/' . $featured_img['file'];
        $img_width = $featured_img['width'];
        $img_height = $featured_img['height'];
        } else {
        $img_atts = get_first_image( $post->ID );
        $img_src = get_site_url() . '/wp-content/uploads/sites/' . get_current_blog_id() . '/' . $img_atts['file'];
          $img_width = $img_atts['width'];
          $img_height = $img_atts['height'];
      }
      if( has_shortcode( $post->post_content, 'gallery' ) && ( $img_atts['file'] == NULL ) ) {
        unset($img_src); unset($img_width); unset($img_height);
        $img_post_id[] = preg_match_all( '/ids=([\'"])(.+?)\1/', ($post->post_content), $img_post_id );
        $img_ids = explode(',', $img_post_id[2][0]);
        foreach ( $img_ids as $img_id ) {
          $gallery_img  = wp_get_attachment_metadata( $img_id );
          $img_width = $gallery_img['width'];
          $img_height = $gallery_img['height'];
          $img_src[] = get_site_url() . '/wp-content/uploads/sites/' . get_current_blog_id() . '/' . $gallery_img['file'];
        }
      }

	$my_url = site_url();
	$content = preg_replace("/<h([1-6]{1})>.*?<\/h\\1>/si", '', ($post->post_content));
	$excerpt = strip_tags($content);
	$excerpt = preg_replace('/(\[(.*)\]|[[:cntrl:]])/', '', $excerpt);
	$charlength = 140;
        if ( mb_strlen( $excerpt ) > $charlength ) {
          $excerpt = mb_substr( $excerpt, 0, $charlength - 5 );
        }
	if ( $excerpt === '' ) {
	  $excerpt = $my_url . ' ... read more ';
	}

        $date = get_the_date( 'Y-m-d' );
        $author = get_the_author_meta( 'display_name', $post->post_author );
        $slugs = wp_get_post_tags( $post->ID, array( 'fields' => 'slugs' ) );
        $tags = implode(', ', $slugs);
        $facebook_url = get_the_author_meta('facebook', $post->post_author);
        $facebook_admin = get_the_author_meta('fb_admin', $post->post_author);
        $facebook_page_id = get_the_author_meta('fb_page_id', $post->post_author);
        $twitter_user = get_the_author_meta('twitter', $post->post_author);
        $twitter_id = get_the_author_meta('twitter_id', $post->post_author);
        $google_url = get_the_author_meta('gplus', $post->post_author);

    if (is_woocommerce() || is_product() || $post->post_type == 'product') {
      //$wc_product = wc_get_product( get_the_ID() );
      $wc_product = get_post_meta( get_the_ID() );
      $wc_currency = get_woocommerce_currency();
    }

    ?>

    <?php if ( !empty($facebook_admin) ): ?>
    <!-- Facebook -->
    <meta property="fb:app_id" content="<?php echo $facebook_page_id; ?>"/>
    <meta property="fb:admins" content="<?php echo $facebook_admin; ?>"/>
    <meta property="fb:smart_publish:robots" content="noauto"/>
    <?php endif; ?>
    <!-- Open Graph -->
    <meta property="og:website" content="<?php echo site_url(); ?>"/>
    <meta property="og:title" content="<?php echo the_title(); ?>"/>
    <meta property="og:description" content="<?php echo $excerpt . '...'; ?>"/>
    <?php if (!is_woocommerce() || !is_product() || !$post->post_type == 'product'): ?>
    <meta property="og:type" content="article"/>
    <meta property="og:article:published_time" content="<?php echo $date; ?>"/>
    <meta property="og:article:author" content="<?php echo $author; ?>"/>
    <meta property="og:article:tag" content="<?php echo $tags; ?>"/>
    <?php endif; ?>
    <meta property="og:url" content="<?php echo the_permalink(); ?>"/>
    <meta property="og:site_name" content="<?php echo get_bloginfo(); ?>"/>
    <meta property="og:image:width" content="<?php echo $img_width; ?>"/>
    <meta property="og:image:height" content="<?php echo $img_height; ?>"/>
    <meta property="og:image" content="<?php echo $img_src; ?>"/>
    <meta property="og:image:secure_url" content="<?php echo $img_src; ?>"/>
    <?php if ( !empty($twitter_id) ): ?>
    <!-- Twitter -->
    <meta name="twitter:app:id:iphone" content="<?php echo $twitter_id; ?>"/>
    <meta name="twitter:app:id:ipad" content="<?php echo $twitter_id; ?>"/>
    <meta name="twitter:site" content="<?php echo $twitter_user; ?>"/>
    <meta name="twitter:creator" content="<?php echo $twitter_user; ?>"/>
    <meta name="twitter:creator:id" content="<?php echo $twitter_id; ?>"/>
    <meta name="twitter:url" content="<?php echo the_permalink(); ?>"/>
    <meta name="twitter:title" content="<?php echo the_title(); ?>"/>
    <meta name="twitter:description" content="<?php echo $excerpt . '...'; ?>"/>
    <meta name="twitter:card" content="summary_large_image"/>
    <meta property="twitter:image" content="<?php echo $img_src; ?>"/>
    <meta property="twitter:image:alt" content="<?php echo the_title() . ' | ' . get_bloginfo(); ?>"/>
    <?php endif; ?>
    <?php if ( !empty($pin_confirm) ): ?>
    <!-- Pinterest -->
    <meta name="p:domain_verify" content="<?php echo $pin_confirm; ?>"/>
    <?php endif; ?>
    <?php if (is_product() || $post->post_type == 'product'): ?>
    <!-- Woocommerce -->
    <meta property="og:type" content="product"/>
    <meta property="og:product" content="<?php echo the_title(); ?>"/>
    <meta property="product:price:amount" content="<?php echo implode($wc_product[_price]); ?>"/>
    <meta property="product:price:currency" content="<?php echo $wc_currency; ?>"/>
    <meta property="og:availability" content="<?php echo implode($wc_product[_stock_status]); ?>"/>
    <?php endif; ?>

<?php
    } else {
      return;
    }
}
add_action('wp_head', 'social_meta', 5);
