# Woocommerce Deposits
## Location
```
/wp-content/plugins/woocommerce-deposits/includes/admin/class-envato-items-update-client.php
```

```
<?php
namespace Webtomizer\WCDP;

if (!defined('ABSPATH')) {
    exit;
}

use stdClass;

/**
 * Automatic updater client
 */
class Envato_items_Update_Client
{
    /**
     * @var $item_id
     * @description Envato Item ID
     */
    private $item_id;
    /**
     * @var $purchase_code
     * @description Purchase code inserted by the customer
     */
    private $purchase_code;
    /**
     * @var
     * @description Plugin file
     */
    private $plugin_file;
    /**
     * @var $update_endpoint
     * @description update endpoint
     */
    private $update_endpoint;
    /**
     * @var $verify_purchase_endpoint
     * @description verify purchase endpoint
     */
    private $verify_purchase_endpoint;

    /**
     * Constructor
     * @param $item_id
     * @param $plugin_file
     * @param $update_endpoint
     * @param $verify_purchase_endpoint
     * @param $purchase_code
     */
    function __construct($item_id, $plugin_file, $update_endpoint, $verify_purchase_endpoint, $purchase_code)
    {
        $this->item_id = $item_id;
        $this->plugin_file = $plugin_file;
        $this->update_endpoint = $update_endpoint;
        $this->verify_purchase_endpoint = $verify_purchase_endpoint;
        $this->purchase_code = $purchase_code;
    }

    /**
     * Enable the client functionality
     * @return void
     */
    function enable()
    {
        // Since we are removing update check functionality, we won't add the filters here.
    }

    /**
     * Verify purchase code request
     * @param $purchase_code
     * @return bool|string|\WP_Error
     */
    function verify_purchase_code($purchase_code)
    {
        $args = array('timeout' => 10, 'body' => array('purchase_code' => $purchase_code, 'item_id' => $this->item_id));

        $request = wp_remote_post($this->verify_purchase_endpoint, $args);

        if (is_array($request) && $request['response']['code'] == 200) {
            $response = json_decode(wp_remote_retrieve_body($request), true);

            if (is_array($response) && isset($response['status']) && $response['status'] === 'success') {
                return 'valid';
            } else {
                return 'invalid';
            }
        } else {
            // An error occurred
            if (is_wp_error($request)) {
                return $request;
            }
            return false;
        }
    }
}

```
