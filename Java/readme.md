// Java Sample

JAVA OFFLINE KIT.

Dyamic QR
/*
 * To change this license header, choose License Headers in Project Properties.
 * To change this template file, choose Tools | Templates
 * and open the template in the editor.
 */
package dynamic.qr;
import com.paytm.pg.merchant.CheckSumServiceHelper;
import java.io.BufferedReader;
import java.io.DataOutputStream;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;
import org.json.JSONObject;

public class DynamicQr {

/**
* @param args the command line arguments
* @throws java.lang.Exception
*/
public static void main( String[] args ) throws Exception
{
excutePost("https://trust-uat.paytm.in/wallet-merchant/v2/createQRCode");
}

public static String excutePost(String targetURL) throws Exception {
HttpURLConnection connection = null;

JSONObject jsonRequestObj = getJsonRequestObject();

JSONObject jsonContentObj = getJsonRequestObject();

   System.out.println("My Request is =" +jsonContentObj.toString());

          //Create connection
URL url = new URL(targetURL);

connection = (HttpURLConnection)url.openConnection();
connection.setRequestMethod("POST");


String urlParameters=jsonRequestObj.toString();


  String CHECKSUMHASH =  CheckSumServiceHelper.getCheckSumServiceHelper().genrateCheckSum("AES key", jsonRequestObj.toString());


try {
//Create connection


connection = (HttpURLConnection)url.openConnection();

            connection.setRequestProperty("mid","xxxxxxxxxx");

            connection.setRequestProperty("Content-Type","application/json");
            connection.setRequestProperty("merchantGuid", "xxxxxx-xxxx-xxxx-xxxx-xxxxxxxx");


            connection.setRequestProperty("checksumhash",CHECKSUMHASH);


     //  System.out.println("My checksumhash is =" +CHECKSUMHASH.toString());

            connection.setRequestProperty("Content-Length", Integer.toString(urlParameters.getBytes().length));
            connection.setUseCaches(false);

            connection.setDoOutput(true);


  try (DataOutputStream wr = new DataOutputStream (

          connection.getOutputStream())) {
      wr.writeBytes(urlParameters);
  }



            int responseCode = connection.getResponseCode();

            System.out.println(responseCode+"\tSuccess");

            InputStream is;

            if(responseCode == HttpURLConnection.HTTP_OK){

            is = connection.getInputStream();

            }else {

            is = connection.getErrorStream();

            }

                  StringBuilder response;
  try (BufferedReader rd = new BufferedReader(new InputStreamReader(is))) {
      response = new StringBuilder(); // or StringBuffer if not Java 5+
      String line = "";
      while((line = rd.readLine()) != null) {

          response.append(line);

          response.append('\r');

      }           } // or StringBuffer if not Java 5+

System.out.println("This is response string= " +response);

return response.toString();


} catch (Exception e) {

System.out.println(e);

e.printStackTrace();

}  finally {

if(connection != null) {

connection.disconnect(); 

}

}




return "";

}


public static JSONObject getJsonRequestObject() throws Exception {

JSONObject jsonContentObj = null, jsonRequestObj = null;
//  String skipRefill="true";
String totalAmount="0.01";
      String requestType="QR_ORDER";
String orderDetails ="ORDER";
String merchantGuid ="xxxxxxxx-xxxx-xxxx-xxxx-xxxx";
String orderId ="99999";
String industryType ="RETAIL";




String platformName ="PayTM";
String ipAddress ="192.168.40.11";
String operationType ="QR_CODE";


         jsonContentObj = new JSONObject();
jsonRequestObj = new JSONObject(); 
jsonRequestObj.put("requestType", requestType);             
jsonRequestObj.put("totalAmount", totalAmount);
jsonRequestObj.put("orderDetails", orderDetails);
jsonRequestObj.put("merchantGuid", merchantGuid);
jsonRequestObj.put("orderId", orderId);
jsonRequestObj.put("industryType", industryType);
// jsonRequestObj.put("posId", posId);
//  jsonRequestObj.put("comment", comment);


jsonContentObj.put("request", jsonRequestObj);
jsonContentObj.put("platformName", platformName);
jsonContentObj.put("ipAddress", ipAddress);
jsonContentObj.put("operationType", operationType);
//jsonContentObj.put("channel", channel);
// jsonContentObj.put("version",version);

return jsonContentObj;

}

}

//TOTP/Barcode

/*
* To change this license header, choose License Headers in Project Properties.
* To change this template file, choose Tools | Templates
* and open the template in the editor.
*/
package totp2;
import com.google.gson.Gson;
import com.google.gson.JsonObject;
import com.google.gson.JsonPrimitive;

import com.paytm.pg.merchant.CheckSumServiceHelper;

import java.io.BufferedReader;
import java.io.DataOutputStream;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;

public class totp2 {
public static String paytmTOTPTestUrl = "https://trust-uat.paytm.in/wallet-web/v7/withdraw";

public static String paytmMerchantGuid = "xxxxxx-xxxx-xxxx-xxxx-xxxxxxxx";

public static String paytmMerchantKey = "aes key";

public static String getJsonRequest(){
JsonObject mainBody = new JsonObject();
JsonObject requestBody = new JsonObject();


requestBody.add("merchantGuid", new JsonPrimitive(paytmMerchantGuid));
requestBody.add("merchantOrderId",new JsonPrimitive("Order000000000199"));

requestBody.add("totalAmount",new JsonPrimitive("1"));
requestBody.add("currencyCode",new JsonPrimitive("INR"));


mainBody.add("request", requestBody);
mainBody.add("metadata", new JsonPrimitive("Testing Data"));
mainBody.add("ipAddress", new JsonPrimitive("192.168.0.1"));
mainBody.add("platformName", new JsonPrimitive("Paytm"));
mainBody.add("operationType", new JsonPrimitive("WITHDRAW_MONEY"));
String jsonRes = new Gson().toJson(mainBody);
return jsonRes;
}


public static String TOTPRequest(String targetURL) throws Exception {
HttpURLConnection connection = null;
URL url = new URL(targetURL);
String requestBody = getJsonRequest();

connection = (HttpURLConnection)url.openConnection();
connection.setRequestMethod("POST");

String CHECKSUMHASH =  CheckSumServiceHelper.getCheckSumServiceHelper().genrateCheckSum(paytmMerchantKey, requestBody);
System.out.println(CHECKSUMHASH);

try {	
connection = (HttpURLConnection) url.openConnection();
connection.setRequestProperty("Content-Type","application/json");
connection.setRequestProperty("mid", paytmMerchantGuid);
              connection.setRequestProperty("phone","xxxxxxxxxx");
              connection.setRequestProperty("otp","xxxxxx");
  connection.setRequestProperty("checksumhash",CHECKSUMHASH);
connection.setRequestProperty("Content-Length", Integer.toString(requestBody.getBytes().length));
  connection.setUseCaches(false);
connection.setDoOutput(true);

      try (DataOutputStream wr = new DataOutputStream (connection.getOutputStream())) {
          wr.writeBytes(requestBody);
      }

InputStream is;
try{
is = connection.getInputStream();
}catch(Exception e){
is = connection.getErrorStream();
}

              StringBuilder response;
      try (BufferedReader rd = new BufferedReader(new InputStreamReader(is))) {
          int statusCode = connection.getResponseCode();
          System.out.println("The http header code is = " +statusCode);
          response = new StringBuilder();
          String line="";
          while((line = rd.readLine()) != null) {
              response.append(line);
              response.append('\r');
          }
      }
  System.out.println("This is response string= " +response);
  return response.toString();
} catch (Exception e) {
System.out.println("This is response string= " +e.getMessage());
}  finally {
if(connection != null) {
connection.disconnect(); 
}
}
return "";
}


public static void main( String[] args ) throws Exception
{
TOTPRequest(paytmTOTPTestUrl);
}

}			
