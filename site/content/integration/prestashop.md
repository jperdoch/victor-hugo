+++
title = "Presta Shop"
date = "2016-06-24T19:20:04-07:00"
description = "Description"
type = "integration"
id = "prestashop"
orderid = 1
+++

Presta Shop
--
Fast, efficient and easy to use, Presta Shop's free e-commerce solution provides everything you need to open, operate and maintain a successful online store.

The easiest way how to integrate  Presta Shop with Post Affiliate Pro or Post Affiliate Network is to use this integration module (downloadable, free module by Quality Unit, version 3.12) **It is a standard Presta Shop module, you can install it as any other modules, then configure it and you are done.** The plugin automatically integrates click and sale tracking code.

NOTE: If you are using performance setting *"Move JavaScript to the end"* in your Presta Shop, please turn it off as it breaks down the tracking functionality.

#### 1. Localization of file and add tracking code (older versions of Presta Shop)
Connect to your FTP and find the file **order-confirmation.php**. This file process orders. Download it, open it in any text editor and find this line:
`'HOOK_PAYMENT_RETURN' =&gt; Hook::paymentReturn(intval($id_order), intval($id_module))));`
Add this tracking code after the line mentioned above. The code is for tracking total cost and order ID:

<textarea class="TextBox" readonly>
?><script id="pap_x2s6df8d" src="http://URL_TO_PostAffiliatePro/scripts/salejs.php" type="text/javascript"> </script>
<script type="text/javascript">
var sale = PostAffTracker.createSale();
sale.setTotalCost('<?php echo $order->total_paid_real; ?>');
sale.setOrderID('<?php echo $id_order;?>');
PostAffTracker.register();
</script><?php</textarea>

Now save the file and upload it back to FTP.


#### 2. PayPal part 2
Next thing to do is to find file validation.php, also inside `shop/modules/paypal/`. Edit this file and find this line of code:
`$ch = curl_init('https://' . $paypalServer . '/cgi-bin/webscr');`
Put following code right after that line of code:

<textarea class="TextBox">/* PAP4 integration */
 $ch = curl_init();
 curl_setopt($ch, CURLOPT_URL, "http://URL_TO_PostAffiliatePro/plugins/PayPal/paypal.php");
 curl_setopt($ch, CURLOPT_POST, 1);
 curl_setopt($ch, CURLOPT_POSTFIELDS, $_POST);
 curl_exec($ch);
/* end of PAP4 integration */
</textarea>

Also, you have to activate PayPal IPN plugin inside of your `Merchant panel` -> `Plugins`. Edit the plugin settings and change separator to `||`

#### 3. PayPal part 3
Last thing to do is to find this line of code inside `validation.php`:
`if ($result == 'VERIFIED') {`
Put following code right after that line of code:

<textarea class="TextBox">
/* PAP INTEGRATION */
$separator = '||';
    if ($_POST['custom'] != '') {
      $explodedCustomValue = explode($separator, $_POST['custom'], 2);
          if (count($explodedCustomValue) == 2) {
            $_REQUEST['custom'] = $_POST['custom'] = $explodedCustomValue[0];   
      }
    }
/* END PAP INTEGRATION */
</textarea>

That's all necessary in order to integrate Presta Shop + PayPal.
