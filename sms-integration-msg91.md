# SMS Integration : MSG91

## Concepts
### Promotional Message

* Message sent with an objective of promoting product or services.
* These messages can only be send from 9AM to 9PM.
* Mesages can be send only to numbers that are not the DND numbers.

As a thumb rule, SMS which has details related to offers, coupons, etc can be considered fit for these type of messages.

### Transactional Message

* These are SMS's sent to your registered users.
* These contains information necessary for using the product or service.
* Ability to send messages to DND numbers.
* Delivering urgent information to people is possible with transactional Bulk SMS.
* Unlike promotional SMS which suffer from time constraint, these SMS can be sent at anytime, without any time constraint.

**Use** : Companies can send important notifications to the public. For example, Railways can use these Alert SMS for sending PNR status, banks and other financial institutions can notify people about the balance left in their account and can also intimate about other necessary information, educational institutions can utilize the transactional messaging service for sending urgent notifications to students and parents.

For details on Transactional messages [click here...](https://msg91.com/transactional-sms)

## Pricing
For SMS packages and other pricing details [click here...](https://msg91.com/sms-pricing/).

## Terms and Condition
For refund and cancellation related details [click here...](https://msg91.com/terms).

## Features
* **Powerful sending infrastructure** : Easy API integration and a simple. HTTP or JSON/XML API available. Scale quickly, whether it’s a matter of 10 SMS or 10 million SMS(haven't tried sending millions).
* **Robust Infra** : The architecture of server is designed to scale tens of millions of SMSs in a go. They deliver 50 million SMSs per day. They have gatekeepers servers for handling extra traffic in case of sudden spike.
* **Tracking and Analytics** : Searchable logs means we always know what is happening to our SMS, while tags make it easy to report on your data.
* **Report Card** : MSG91 is a tracker. You can practically keep track of everything and anything from delivery time to sent SMS logs to frequently failed numbers. Same type of texts can be kept under one Campaign name. There are graphs showing illustrated delivery reports.
* **API** : They provide HTTP, JSON/XML API Documentation for implementation giudelines along with sample codes.
* **AddOns** : Possibilities of sending SMS with a variety of add-ons. Use Excel plugin, Google spreadsheets and more.
* **Coverage** : MSG91 is global. They cover 90+ countries, with deliverability to each and every mobile number.
* **Support** : Call our Support superheroes 18x7 in case of any query over live chat or over phone. They are your friends and consultants in need!
* **Current Customers** (10000+) : Axis Bank, coal india, ISRO, myntra, make-my-trip, PNB, etc.
* **START-UP** : Add logo on website , get upto 25000 SMS/Month free.

For terms related to start-up offer [click here...](http://help.msg91.com/article/204-how-to-avail-for-msg91-start-up-offer).

## Introduction to MSG91-User Interface
Here short explanation of all the side menus is mentioned. Their implementation can be seen in test cases below.

### Custom look of UI
![MSG91 UI](sms-integration-res/ui)

### Understanding Side Menu

#### Add Group
* Provides facility of creating a group of people to whom you can directly send messages.
* Similar to groups in phonebook of our cell phones.
* After group is created, it will automatically appear under the `Add Group` menu. For eg: You can see *College* and *Intern* group created in above sample picture.

For group related queries [click here...](http://help.msg91.com/search?collectionId=55939e0de4b0f73dcad82dcb&query=group)

#### Blacklist Number
* Add no's from whom you want to stop receiving messages.

For related queries [click here...](http://help.msg91.com/search?collectionId=&query=blacklist)

#### Export ALL
* This function can be used to export all the contact details of all the respective groups.
* Resultant is a `.csv` file consisting of contact details of all the people belonging to a group.

For exporting related queries [click here...](http://help.msg91.com/search?collectionId=&query=export+all)

#### Send SMS

* Choose Route type of message you want to send through top panel : [`Promotional`](file:///home/ms/Desktop/sms-integration.md#concepts) or [`Transactional`](file:///home/ms/Desktop/sms-integration.md#concepts). 
* Different parameters : 
	* *[Sendor ID](http://help.msg91.com/article/40-what-is-a-sender-id-how-to-select-a-sender-id)* : Name that will be displayed to receiver on receiving a message
	* *Mobile No* : Comma separated list of contact no's
	* *Message*   : Enter message to be sent. Message can be as long as you need and charges will be applied on 160char/sms basis
	* *[Campaign name](http://help.msg91.com/article/113-what-is-campaign-and-how-i-can-use-it)* : Add category of message for grouping by topic
	* *Send SMS* : Send the SMS
	* *[Schedule SMS](http://help.msg91.com/search?collectionId=55939e0de4b0f73dcad82dcb&query=schedule+sms)* : Enter time after which message is to be sent. On clicking it : 
	![Schedule sms pop-up](sms-integration-res/schedule_sms)

#### Send Custom SMS

* Use to send sms to multiple people mentioned in a spreadsheet.
* Can be directly send from spreadsheet using a Excel Plugin.

For details on custom sms [click here...](http://help.msg91.com/article/121-how-can-i-send-customized-personalized-sms)

#### Delivery Report

* Monitor the reports of SMSs.
* Reports of SMS sent to users and ones which are scheduled to be send.
* Searching reports on basis of Campaigns and and other attributes is also available.

For details on delievery reports [click here...](http://help.msg91.com/search?collectionId=55939e0de4b0f73dcad82dcb&query=report)

#### API

* Details of our Authentication Keys and their names is displayed here.
* New API can be created here and existing ones can be enabled or disabled.
* `Create New` helps in creating new auth key.
* In [`WebHooks`](http://control.msg91.com/user/index.php#api) sub-menu, we can define a url where our delievery reports will get posted.
* In [`API Security`](http://help.msg91.com/article/153-what-do-you-mean-by-api-security) sub-menu
	* It is an additional security. An option that allows you to send your messages only through whitelisted IPs. It will also show IPs from where the APIs are being called.
	* If API security is enabled and you try to send the SMS via any other IP then those requests will be rejected .

For details on API menu [click here...](http://help.msg91.com/category/4-api) 

#### SendOTP

* Enable 2-Factor security/authentication.
* Provides SMS and Voice call based support.

For OTP related queries [click here...](http://help.msg91.com/category/168-sendotp)

#### Campaign
`TODO : Under Development right now`

#### Virtual Number
* Virtual Number is a 10 Digit Mobile Number (sometimes also called as Longcode Number) to which your clients or members can send a text SMS starting with a keyword. 
* If someone sends an SMS starting with your keyword, you will be able to receive the messages in your MSG91 inbox, on your email or also on your callback URL.
* For eg. in Kaun Banega Crorepati, you are supposed to send an SMS to 56565 with KBC<SPACE>A/B/C/D

For details on Virtual no [click here...](http://help.msg91.com/article/45-what-is-virtual-number-keyword-inbox-balance)


## Test Cases
### Method 1 : Using Online User Interface
For information related to online control panel [click here...](file:///home/ms/Desktop/sms-integration.md#introduction-to-msg91-user-interface)
#### Test Case 1 : Sending a simple Text message
* SMS Creation	
![ui-simple-sms](sms-integration-res/ui-simple-sms)
* SMS Cost : 1 Unit
* Delivery Report : Select `Delivery Report` from side menu. Here you will find all the delivery reports.
![delivery-report-menu](sms-integration-res/ui-delivery-report-menu)
You can see reports by clicking on a specific one.
![delivery-report-menu-sms](sms-integration-res/ui-delivery-report-menu-sms1)
![delivery-report-menu-sms](sms-integration-res/ui-delivery-report-menu-sms2)


#### Test Case 2 : Sending one SMS to multiple recipients 
* SMS Creation : Manually enter comma separated phone no's or add a group from phonebook.
![ui-multi-user-sms](sms-integration-res/ui-multi-user-sms)
* SMS Cost : No of recipients * Cost of single SMS.
* Delivery Report
![ui-delivery-report-menu-multi-user-sms](sms-integration-res/ui-delivery-report-menu-multi-user-sms1)
![ui-delivery-report-menu-multi-user-sms](sms-integration-res/ui-delivery-report-menu-multi-user-sms2)
	* Since two messages are not yet delivered, you can see the change in graph.
	* Same changes are reflected back in the report.
	* User whose phone no is saved in phonebook, there name appears in reports.

#### Test Case 3 : Sending BULK SMS
Here goal is to send different SMS to different recipient.
* SMS Creation : `Send Custom SMS` menu from side bar.
	* Create a Excel sheet consisting of details of user phone no and message that is to be sent.
	* Convert this file to `.csv` format. 
	* Go to `Send Custom SMS` menu and upload the file. For sample of excel sheet format [click here..](sms-integration-res/custom-sms.csv)
	![ui-send-custom-sms](sms-integration-res/ui-send-custom-sms)
	* Create message as shown in pic below. Don't forget to select the column where user phone no's are present (column B here). For message content, select the columns which you want to include by clicking on `Select` button.
	![ui-send-custom-sms-content](sms-integration-res/ui-send-custom-sms-content)
* SMS Cost : No of user whose details are mentioned in sheet uploaded * Cost of single SMS.
* Delivery Report : SMS content area will not show the actual SMS's detail.
![](sms-integration-res/ui-delivery-report-menu-send-custom-sms1)![](sms-integration-res/ui-delivery-report-menu-send-custom-sms2)

#### Test Case 4 : Sending a simple SMS of 1200 characters
* SMS Creation
	* MSG91 charges 1Unit for every 160 characters.
	* Will try sending SMS of Size 1200 characters (approx 8 SMS).
* SMS Cost : SMS cost of 8 Unit.
* Delivery Report : Similar to that of `Sending a simple Text SMS` 


#### Test Case 5 : Sending SMS on wrong No
* SMS Creation : Send Simple message from `Send SMS` sub menu in side bar.
* SMS Cost : No Cost
* Delivery Report
![ui-delivery-report-menu-wrong-no](sms-integration-res/ui-delivery-report-menu-wrong-no)

MSG91 error codes [click here...](http://help.msg91.com/article/67-why-am-i-getting-error-code-in-msg91-api-and-what-does-it-mean)

#### Test Case 6 : Sending Null SMS
* SMS Creation : Try to send a SMS without any content will not be allowed.
![ui-null-sms](sms-integration-res/ui-null-sms)
* SMS Cost : No cost as message will not be sent
* Delivery Report : No SMS report generated



### Method 2 : Using Java XML API
#### About XML API
* It is not possible for a URL to take too much data in GET method and have to use POST method
* It enables few extra features like sending custom SMS
* Sending 500 SMS will take 5 loop in HTTP API but none in XML, thus saving our resources

* Information that can be sent in a SMS

|XML node name|	Description|
|-|-|
|AUTHKEY *|	User authentication key|
|TEXT *|	It contains the URL encoded message content to send|
|SENDER *|	It contains sender ID|
|TO *|	It contain mobile numbers|
|SCHEDULE DATE TIME|	It contains scheduled date and time|
|FLASH|1|
|UNICODE|1|
|ROUTE|Route name if you have more than one route available in your account (eg. 4 for Transactional and 1 for Promotional)|
|CAMPAIGN|	It contains campaign name|
|COUNTRY|0 for international, 91 for India, 1 for USA|

> '*' marked data cannot be left blank

* Response : returns json response with success if all parameters are correct or error with appropriate error message.

|Response type	|	Response messages	|	Description	|
|-|-|-|
|success|XML code is correct|Returns success for validate XML API call|
|error|XML code should not be blank|Returns error in case of 'testingsXml' parameter is missing or empty|
|error|Specific error message|Returns error in case of ill constructed XML API and displays appropriate error message|

**Note** : Output will be request ID which is alphanumeric and contains 24 characters like mentioned above. With this request ID, delivery Report can be viewed. If request not sent sucessfully, you will get the appropriate error message.

MSG91 error codes [click here...](http://help.msg91.com/article/67-why-am-i-getting-error-code-in-msg91-api-and-what-does-it-mean)

#### Sample base code for test cases
* Create a class and define a function (here `hitUrl()`) to handle SMS sending. This function will be same for all the test cases in examples mentioned here for reference:
```
public static String hitUrl(String urlToHit, String param)
	{
    	try
    	{
            URL url = new URL(urlToHit);
            HttpURLConnection http = (HttpURLConnection)url.openConnection();
            http.setDoOutput(true);
            http.setDoInput(true);
            http.setRequestMethod("POST");

            DataOutputStream wr = new DataOutputStream(http.getOutputStream());
            wr.writeBytes(param);
            // use wr.write(param.getBytes("UTF-8")); for unicode message's instead of wr.writeBytes(param);

            wr.flush();
            wr.close();
            http.disconnect();

            BufferedReader in = new BufferedReader(new InputStreamReader(http.getInputStream()));
            String inputLine;
            if ((inputLine = in.readLine()) != null)
            {       in.close();
                    return inputLine;
            }else{
                    in.close();
                    return "-1";
            }
        }
        catch(Exception e)
        {
            System.out.println("Exception Caught..!!!");
            e.printStackTrace();
            return "-2";
        }
    }
``` 

* Create a function (here `main()`) to handle different type of data which will be sent. This function will be slightly different for every test case.
```
public static void main(String...s){
		String strUrl = "https://control.msg91.com/api/postsms.php";

		//message to be sent will be added here
		String xmlData = "";	

	String output = SendSMS.hitUrl(strUrl, xmlData);
	System.out.println("Output is: "+output);		
}
```
**Note** : We will be changing content of `xmlData` variable in test cases below.


#### Test Case 1 : Sending a simple text SMS
* SMS Creation : `main()` function will pass message in `xml` form to `hitUrl()` which will send this message. Update variable( here `xmlData`) containing actual SMS content as follows in `main()` function. 
```
String xmlData="data="
				+ "<MESSAGE>"
				+ "		<AUTHKEY>173688AEUZr5aqK59b25cb5</AUTHKEY>"
				+ "		<ROUTE>4</ROUTE>"
				+ "		<SMS TEXT='Hello Nitin' FROM='MuseNi'>"
				+ "			<ADDRESS TO='9999999999'></ADDRESS>"
				+ "		</SMS>"
				+ "</MESSAGE>";
```
* SMS Cost : 1 Unit
* Returned Response : `Output is: 37696e6f3670313535303730`

Since message sending was successful, API will return a alphanumeric `request Id` (here `37696e6f3670313535303730`) which can then be used to track our delivery reports.

#### Test Case 2 : Sending one SMS to multiple recipients
* SMS Creation : Update variable( here `xmlData`) containing actual SMS content as follows in `main()` function. 
```
String xmlData = "data="
				+ "<MESSAGE>"
				+ "		<AUTHKEY>173688AEUZr5aqK59b25cb5</AUTHKEY>"
				+ "		<ROUTE>4</ROUTE>"
				+ " 	<SCHEDULEDATETIME>2016-03-31 11:17:39 </SCHEDULEDATETIME>"
				+ "		<SMS TEXT='Hello Nitin' FROM='MuseNi'>"
				+ "			<ADDRESS TO='987*******'></ADDRESS>"
				+ "			<ADDRESS TO='940*******'></ADDRESS>"
				+ "		</SMS>"
				+ "</MESSAGE>";
```
* SMS Cost : No of recipients * cost of 1 SMS
* Returned Response : `Output is: 37696e707643323435353835`

Since message sending was successful, API will return a alphanumeric `request Id` which can then be used to track our delivery reports.

#### Test Case 3 : Sending Bulk SMS
Here goal is to send different SMS to different recipient.
* SMS Creation : Update variable( here `xmlData`) containing actual SMS content as follows in `main()` function.
```
String xmlData = "data="
				+ "<MESSAGE>"
				+ "<AUTHKEY>173688AEUZr5aqK59b25cb5</AUTHKEY>"
				+ "<ROUTE>4</ROUTE>"
				+ "	<SMS TEXT='Hello Team Back-end' FROM='MuseNi'>"
				+ "		<ADDRESS TO='987*******'></ADDRESS>"
				+ "		<ADDRESS TO='940*******'></ADDRESS>"
				+ "	</SMS>"
				+ "	<SMS TEXT='Hello Team Front-End' FROM='MuseNi'>"
				+ "		<ADDRESS TO='798*******'></ADDRESS>"
				+ "		<ADDRESS TO='832*******'></ADDRESS>"
				+ "	</SMS>"
				+ "</MESSAGE>";
```
* SMS Cost : No of recipients * cost of 1 SMS
* Returned Response : `Output is: 37696e704a58363537373030`

Since message sending was successful, API will return a alphanumeric `request Id` which can then be used to track our delivery reports.

#### Test Case 4 : Sending SMS with NULL('') message
* SMS Creation : Update variable (here `xmlData`) containing actual SMS content as follows in `main()` function.
```
String xmlData = "data="
				+ "<MESSAGE>"
				+ "<AUTHKEY>173688AEUZr5aqK59b25cb5</AUTHKEY>"
				+ "<ROUTE>4</ROUTE>"
				+ "	<SMS TEXT='' FROM='MuseNi'>"
				+ "		<ADDRESS TO='987*******'></ADDRESS>"
				+ "	</SMS>"
				+ "</MESSAGE>";
```
* SMS Cost : No Cost
* Returned Response : `Output is: 37696e70376e353237353634`

API will return a alphanumeric `request Id` which can then be used to track our delivery reports.

Here Since there is not any syntax problem, message request will be successful from API. But when we open `Delivery Report` menu from online user Interface of MSG91, it will show `API failed : check`.
![API failed](sms-integration-res/api-failed-using-ui)

On Clicking `check`, we will see `Failed API` tab showing `Reason` for not delivery of sms as `missing message`.
![API Failed](sms-integration-res/api-failed-using-ui-report)

MSG91 error codes [click here...](http://help.msg91.com/article/67-why-am-i-getting-error-code-in-msg91-api-and-what-does-it-mean)

#### Test Case 5 : Sending SMS with NULL('') Phone No
* SMS Creation : Update variable (here `xmlData`) containing actual SMS content as follows in `main()` function.
```
String xmlData = "data="
				+ "<MESSAGE>"
				+ "<AUTHKEY>173688AEUZr5aqK59b25cb5</AUTHKEY>"
				+ "<ROUTE>4</ROUTE>"
				+ "	<SMS TEXT='screw you : no phone' FROM='MuseNi'>"
				+ "		<ADDRESS TO=''></ADDRESS>"
				+ "	</SMS>"
				+ "</MESSAGE>";
```

* SMS Cost : No Cost
* Returned Response : `Output is: code: 202`

Since we have a blank field (phone no) here, we will recieve an error of code 202.
> code 202 : Invalid mobile number. You must have entered either less than 10 digits or there is an alphabetic character in the mobile number field in API.

MSG91 error codes [click here...](http://help.msg91.com/article/67-why-am-i-getting-error-code-in-msg91-api-and-what-does-it-mean)

#### Test Case 6 : Sending SMS on wrong no
* SMS Creation : Update variable (here `xmlData`) containing actual SMS content as follows in `main()` function.
```
String xmlData = "data="
				+ "<MESSAGE>"
				+ "<AUTHKEY>173688AEUZr5aqK59b25cb5</AUTHKEY>"
				+ "<ROUTE>4</ROUTE>"
				+ "	<SMS TEXT='Hey there Wrong no...' FROM='MuseNi'>"
				+ "		<ADDRESS TO='0000000000'></ADDRESS>"
				+ "	</SMS>"
				+ "</MESSAGE>";
```
* SMS Cost : No Cost
* Returned Response : `Output is: 37696e715968343538343637`

API will return a alphanumeric `request Id` which can then be used to track our delivery reports.

Here Since there is not any syntax problem, message request will be successful from API. But when we open `Delivery Report` menu from online user Interface of MSG91, it will show `campaign freeze Code : 202` error.
![ui-delivery-report-menu-wrong-no](sms-integration-res/ui-delivery-report-menu-wrong-no)
> code 202 : Invalid mobile number. You must have entered either less than 10 digits or there is an alphabetic character in the mobile number field in API.

MSG91 error codes [click here...](http://help.msg91.com/article/67-why-am-i-getting-error-code-in-msg91-api-and-what-does-it-mean)

### Summary of Information Gathered
* Maximum no of char per msg : 160 characters
* Different routes of messaging
	* `1` for Promotional messages
	* `4` for Transactional messages
* Maximum no of Phone no's to whom message can be send at once (using excel sheet) : Size of excel file containing phone records should not be greater than 20MB.
* Support for WhatsApp ? : No
* Sending 1200 characters (Around 8 messages)
Takes almost similar time as sending a message 8 times. However result finally gets converted into one single message(till then random characters are shown). 
* Sending message to 5 users at a time : No extra time needed
* Sending message to wrong no
	* Sending online through UI
		* In Delievery report shows Blocked no.
		* No Message Cost
	* Sending through API
		* No message cost
		* `API Campaign Freezed code: 202 `. Where, `Code: 202 -Invalid or no mobile numbers to process.`
* Sending NULL message
	* In online User Interface : shows "Please Enter Message to Send"
	* Using API : "Few parameters value are empty : message"
* Sending Different message to different people at once.

## References

* [Text Local API](https://www.textlocal.in/simple-developer-sms-api)
* [MSG91](https://msg91.com/features/)
* [MSG91 Documentation](http://help.msg91.com/article/153-what-do-you-mean-by-api-security)
* [MSG91 Pricing Details](https://msg91.com/sms-pricing/)
* [MSG91 terms and conditions](https://msg91.com/terms)
* [MSG91 Startup Offer](http://help.msg91.com/article/204-how-to-avail-for-msg91-start-up-offer)
* [Reasons behind failed SMS](http://help.msg91.com/article/38-what-are-the-reasons-behind-fail-failed-sms)
* [XML API Details](http://control.msg91.com/apidoc/textsms/xml-send-sms.php#xmlcontent)
* [XMl API implementation](http://control.msg91.com/apidoc/samplecode/java-sample-code-send-sms-xml.php)
* [MSG91 Questionaires](http://help.msg91.com/category/4-api)
* [MSG91 Error Codes](http://help.msg91.com/article/67-why-am-i-getting-error-code-in-msg91-api-and-what-does-it-mean)