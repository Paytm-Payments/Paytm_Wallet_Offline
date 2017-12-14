//PHP OFFLINE

//DynamicQr

<?php
header("Pragma: no-cache");
header("Cache-Control: no-cache");
header("Expires: 0");

// following files need to be included

require_once("./lib/encdec_paytm.php");

$checkSum = "";
$paramList = array();


$paramlist['request'] = array('requestType'=>'QR_ORDER','merchantContactNO'=>'8588847241','posId'=>'AJ324',
'channelId'=>'abcd','customerInfo'=>'122','amount'=>'1.76','merchantGuid'=>'xxxxxx-xxxxx-xxx-xx-xxxxxxxx','orderId'=>time(),'industryType'=>'RETAIL','orderDetails'=>'Ajay order');
$paramlist['platformName'] = 'PayTM';
$paramlist['ipAddress'] = '192.168.40.11';
$paramlist['operationType'] = 'QR_CODE';
//$paramlist['version'] = '1.0';

$data_string = json_encode($paramlist); 

echo $data_string;

$checkSum = getChecksumFromString($data_string ,"AES KEY");



$url = 'https://trust-uat.paytm.in/wallet-merchant/v2/createQRCode';

$headers = array('Content-Type:application/json','merchantGuid:xxxx-xxxx-xxxx-xxxx-xxxx','mid:xxxxxxxxxx','checksumhash:'.$checkSum);

$ch = curl_init();  // initiate curl
curl_setopt($ch, CURLOPT_URL,$url);
curl_setopt($ch, CURLOPT_POST, 1);  // tell curl you want to post something
curl_setopt($ch, CURLOPT_POSTFIELDS,$data_string); // define what you want to post
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true); // return the output in string format
curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);     
curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, false);
$output = curl_exec ($ch); // execute
echo $output;

?>

// Github url for checksum php file.

https://github.com/Paytm-Payments/Paytm_App_Checksum_Kit_PHP/tree/master/lib


