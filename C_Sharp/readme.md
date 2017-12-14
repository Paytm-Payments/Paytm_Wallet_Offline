//Creating C# sampple code

//TOTP/Barcode

Dictionary<string, string> innerrequest = new Dictionary<string, string>();
Dictionary<string, string> outerrequest = new Dictionary<string, string>();

string merchant_guid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx";


innerrequest.Add("totalAmount", "0.01");
innerrequest.Add("currencyCode", "INR");
innerrequest.Add("merchantGuid", "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx");
innerrequest.Add("merchantOrderId", "TEST_12346999d9999");
//innerrequest.Add("industryType", "Retail");
innerrequest.Add("posId", "POS_12");
innerrequest.Add("comment", "Test");
//innerrequest.Add("subWalletAmount", subWallet);
innerrequest.Add("uniqueReferenceLabel", "custom");
innerrequest.Add("uniqueReferenceValue", "12345");
//  innerrequest.Add("promoCode", "IPL50");


String first_jason = new JavaScriptSerializer().Serialize(innerrequest);

outerrequest.Add("request", first_jason);
outerrequest.Add("ipAddress", "192.168.40.11");
//outerrequest.Add("platformName", "PayTM");
outerrequest.Add("operationType", "WITHDRAW_MONEY");
// outerrequest.Add("version", "1.0");
outerrequest.Add("channel", "WEB");

String Second_jason = new JavaScriptSerializer().Serialize(outerrequest);

Second_jason = Second_jason.Replace("\\", "").Replace(":\"{", ":{").Replace("}\",", "},");

try
{

string Check = paytm.CheckSum.generateCheckSumByJson("AES KEY", Second_jason);

String url = "https://trust.paytm.in/wallet-web/v7/withdraw";

System.Net.HttpWebRequest request = (System.Net.HttpWebRequest)System.Net.WebRequest.Create(url);

request.Headers.Add("mid", merchant_guid);
request.Headers.Add("phone", "xxxxxxxxxx");
// request.Headers.Add("code", "xxxxxxxxx");
request.Headers.Add("otp", "xxxxxx");
request.Headers.Add("Checksumhash", Check);
request.Headers.Add("ContentType", "application/json");
request.Method = "POST";

using (System.IO.StreamWriter requestWriter2 = new System.IO.StreamWriter(request.GetRequestStream()))
{
  requestWriter2.Write(Second_jason);

}

string responseData = string.Empty;

using (System.IO.StreamReader responseReader = new System.IO.StreamReader(request.GetResponse().GetResponseStream()))
{
  responseData = responseReader.ReadToEnd();

  Response.Write(responseData);
}
}
catch (System.Net.WebException ex)
{
Stream s = ex.Response.GetResponseStream();
StreamReader sr = new StreamReader(s);
string m = sr.ReadToEnd();


Response.Write("webex=" + m);
}



// Github Link for dlls

https://github.com/Paytm-Payments/Paytm_Web_Sample_Kit_dotNet

