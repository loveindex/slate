---
title: V3 API Integration Spec.
language_tabs: #
  - javascript
 

toc_footers: # 
  - <a href='#'>Current version V 1.0</a>
  

includes:
  

search: true

code_clipboard: true

meta:
  - name: description
    content: V3 API Integration Spec.
---







# Revision History 

|  Version   | Date/Author  | Updates | 
|  ----  | ----  |----  |
| V1.0  | Oct.11, 2021 | First Draft|
|    |  |  |

# Abstract

The API is organized around Bindo Payment Gateway. Our API has predictable resource-oriented URLs, accepts form-encoded request bodies, returns JSON-encoded responses, and uses standard HTTP response codes, authentication.

The chapter describes the API of online service which is based on HTTPS protocol.

Target audience: Experienced payment developer.

The online service system includes a developing and production environment. Obviously, developers should develop on the &quot;develop&quot; environment during the development and test.

The environment is listed below:

# Revision History

# 1. Communication

Https is used in online service.

## 1.1 Https Header

1.1.1 Common request header
<aside class="notice">
All the requests are required to send same header items as below.
</aside>

| **Parameters**        | **Type** | **Usage** | **Description**                                                                                                                                                                                                                        |
|:-----------------------|:----------|:-----------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Content-Type          | String   | M         | Constant<br>Value example：“application/json; charset=UTF-8”                                                                                                                                                                            |
| X-USER-ACCESS-TOKEN   | String   | M         | Access token, could be null while doing Login<br>Value example: “q8s4qg53s8g99grszad9wusds72z9n6”                                                                                                                                      |
| X-CLIENT-IP           | String   | O         | It is client ip and the field must be required for pre-create api. Pre-create and called JS SDK should be from the same client ip.                                                                                                     |
| X-USER-TIME-ZONE      | String   | O         | Time zone.<br>Value example：“Asia/Hong_Kong”                                                                                                                                                                                           |
| X-USER-DATE-TIME      | String   | O         | Date and time<br>Value example: “2021/03/25 17:06:31”                                                                                                                                                                                  |
| X-CLIENT-SECRET       | String   | O         | It’s for pre-create transaction, if have it, that needn't access token.<br>Value example:<br>“HxEAHKIAAAHH7eG3IyS8VLSMbpc_ZGKtBCCgVWXSiX3o_6qSqGsho15Xow1Gvrsic9Hv2LeztZ1fc5Yt436wQ8yXZWUpf_I-nvH7CG5YDMbYG21Paaj4bk3H0rHcRiwWDD7KjIs” |
| X-USER-APP-TYPE       | String   | O         | It’s required for Native Payment.<br>Application type, could be customized.<br>Value example: “Payment APP Terminal”                                                                                                                   |
| X-USER-APP-VERSION    | String   | O         | It’s required for Native Payment.<br>Application version, could be customized.<br>Value example:“v1.0.0”                                                                                                                               |
| X-USER-DEVICE-TYPE    | String   | O         | It’s required for Native Payment.<br>Device type, could be customized.<br>Value example:“PAYMENT_POS”                                                                                                                                  |
| X-USER-DEVICE-ID      | String   | O         | It’s required for Native Payment.<br>Device SN. <br>Value example:“L8188VCA8V6249”                                                                                                                                                     |
| X-USER-DEVICE-MODEL   | String   | O         | For Native Payment.<br>Device model, could be customized. Suggested from device vendor.<br>Value example:“APOS A8”                                                                                                                     |
| X-USER-SYSTEM-VERSION | String   | O         | Device system version.<br>Value example:“Android 5.1.1”                                                                                                                                                                                |
| X-SIGNATURE           | String   | O         | Body Signature<br>Value example: “q8s4qg53s8g99grszad9wusds72z9n6”                                                                                                                                                                     |



# 2. Login(Authentication)

## Description
> The Login command request like this:

```json
POST https://stg.bindo.co/api/v2/login     
Request headers:
Content-Type: application/json; charset=UTF-8

Request body:
{
	
   "username":"abcdtest@bindo.com",
	"password":"123456",
	"grant_type":"password",
	"client_id":"hy89x4gh7b82y9ymhl7s75ucog6j9b",
	"client_secret":"acp02fndjef4ablir1ug1imy6ao6cgm"
	
}
```
> The Login command response like this:

```json
Response headers:
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
...
Response body：
{
    "user": {
        "belongs_to_courier": false,
        "is_staff": true,
        "belongs_to_internal": false,
        "first_login": false,
        "access_token": "i2ctfruzrda0efqlu47dnu2zwha97o6",  
        //* The access_token value is very important and needs to be saved
        "is_inventory_manager": false,
        "is_admin": true,
        "full_name": "abcd",
        "phone": "",
        "name": "abcd",
        "id": 23266,
        "email": "abcdtest@bindo.com",
        "belongs_to_reseller": false
    }

```


The API is used to logging in online service with a valid username and password. An access token will be returned it is mandatory to save it for future use. Any new HTTPS requests should contain the access token in Https headers, or 401 error occurs.
<aside class="notice">
Validity of Access Token: Default permanent validity. If doing Login to get new access token, the new access token will be valid, the old access token will be invalid.
</aside>



`Http request url：https://{login_url}/api/v2/login
Http request method: POST
Http request headers: The required parameters is "Content-Type".Please refer to "1.1 Https Header".
Request parameter format: JSON
Request body：Please refer to "Parameter Description" and Example code.`


**Parameters Description**


| **Parameter**           | **Type**   | **Usage** | **Description**                                                                                                                                                                            |                                                                                                                                          
|:---------------------|:--------|:-------|:----------------------|
|**Requeset Parameters** |
| username            | String | M     | ID / E-mail that applied from vendor.                                                                                                                                                     |
| password            | String | M     | Password                                                                                                                                                                                  |
| client_secret       | String | M     | Constant.<br>Note: For develop ENV, client_secret shall be “acp02fndjef4ablir1ug1imy6ao6cgm“.<br>For production ENV, client_secret shall be “ly286rorphahzz6wm3su79u0vpw7ly6". |
| client_id           | String | M     | Constant.<br>Note: For develop ENV,  client_id shall be “hy89x4gh7b82y9ymhl7s75ucog6j9b“.<br>For production ENV, client_id shall be “r2e329dajqd22u4bdqce1g08ptu5hp0“.          |
| grant_type          | String | M     | Constants: “password”                                                                                                                                                                     |  
| **Response Parameters** |
| name                | String | O     | User Name                                                                                                                                                                                 |
| full_name           | String | O     | Full Name                                                                                                                                                                                 |
| id                  | int    | M     | User ID                                                                                                                                                                                   |
| access_token        | String | M     | Access token returned from server. The filed is need?                                                                                                                                     |
| email               | String | O     | E-mail                                                                                                                                                                                    |
| …                   | String | O     | Other information that the developers may ignore.                                                                                                                                         |



#  Sale/Payment


##Sale/Payment Flow

<!--这里要放流程图-->



##  Common Parameters Description<!--（公共参数）-->


| **Request Parameters**   | **Type**       | **Usage** | **Description**                                                                                                                                                                                                                                                                                                                  |
|:--------------------------|:----------------|:-----------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| uuid                     | String         | M         | Transaction UUID.<br>It is the unique identifier of the transaction. And new transaction should be requested with a new UUID.                                                                                                                                                                                                    |
| transaction_type         | String         | M         | Transaction type(“sale“). Referring to Appendix                                                                                                                                                                                                                                                                                  |
| sn                       | String         | O         | It’s required for Native Payment.<br>Device serial number. If transaction happen in POS deivce(Card Presented) that must include this field.                                                                                                                                                                                     |
| payment_method           | String         | M         | Payment method(“credit_card“). Referring to Appendix                                                                                                                                                                                                                                                                             |
| amount                   | String         | M         | Transaction amount                                                                                                                                                                                                                                                                                                               |
| tips_amount              | String         | O         | Transaction tips amount                                                                                                                                                                                                                                                                                                          |
| currency                 | String         | M         | Transaction currency. Example: “HKD“                                                                                                                                                                                                                                                                                             |
| dcc_amount               | String         | O         | Transaction amount (Dynamic currency convert), null if it is not a DCC transaction.                                                                                                                                                                                                                                              |
| dcc&#95;tips&#95;amount          | String         | O         | Transaction tips amount (Dynamic currency convert), null if it is not a DCC transaction.                                                                                                                                                                                                                                         |
| dcc_rate                 | String         | O         | Dynamic currency convert rate, null if it is not a DCC transaction.                                                                                                                                                                                                                                                              |
| dcc_currency             | String         | O         | Transaction currency. Example: “USD“ (Dynamic currency convert), null if it is not a DCC transaction.                                                                                                                                                                                                                            |
| success_url              | String         | O         | It is required for 3DS transaction. After input the message PIN code, redirect the validate result page                                                                                                                                                                                                                          |
| fail_url                 | String         | O         |  It is required for 3DS transaction. After input the message PIN code, redirect the validate result page                                                                                                                                                                                                                         |
| credit_card              | JSON           | M         | Credit card info.                                                                                                                                                                                                                                                                                                                |
| txn&#95;exp&#95;at               | String         | O         | The expiration time of the order. and this parameter is optional.<br>If it is not used, the default expiration time for order is 15 minutes.<br>If “txn_exp_at“ is used, the value format is RFC3339 and it should be an absolute time of 15 minutes less than the create time.<br>Eg: "txn_exp_at":"2021-09-28T14:41:00+08:00", |
| notify_url               | String         | O         | WebHook notify url. The “notify_url” is required if use WebHook.                                                                                                                                                                                                                                                                 |
| pay&#95;redirect&#95;url         | String         | M         | Pay redirect url, is a required parameter to call the JS SDK.                                                                                                                                                                                                                                                                    |
| Param(credit_card)       |
| card&#95;reader&#95;mode         | String         | M         | Card reader mode. Referring to Appendix                                                                                                                                                                                                                                                                                          |
| card_exp&#95;date            | String         | M         | Card expired date                                                                                                                                                                                                                                                                                                                |
| card_fallback            | boolean        | O         | Whether it is fallback                                                                                                                                                                                                                                                                                                           |
| card_number              | String         | M         | Card number                                                                                                                                                                                                                                                                                                                      |
| card_sn                  | String         | O         | Card SN. If card_reader_mode is contact or contactless, must include this field.                                                                                                                                                                                                                                                 |
| card_track1              | String         | O         | Track 1 magnetic data                                                                                                                                                                                                                                                                                                            |
| card_track2              | String         | O         | Track 2 magnetic data. If card_reader_mode is swipe, contact, or contactless, must include this field.                                                                                                                                                                                                                           |
| card_track3              | String         | O         | Track 3 magnetic data                                                                                                                                                                                                                                                                                                            |
| card&#95;holder&#95;name         | String         | O         | Cardholder name                                                                                                                                                                                                                                                                                                                  |
| is&#95;msd&#95;card              | boolean        | O         | Whether it is an MSD card                                                                                                                                                                                                                                                                                                        |
| cvv                      | String         | O         | CVV                                                                                                                                                                                                                                                                                                                              |
| icc_request              | String         | O         | EMV ICC request data. If card_reader_mode is contact or contactless, must include this field.                                                                                                                                                                                                                                    |
| pin                      | String         | O         |
| Param(credit&#95;card&#95;token) |
| token                    | String         | M         | Credit Card Token<br>It's required for bind credit card and payment                                                                                                                                                                                                                                                              |
| Param(billing_address)   | O              | AVS       |
| zip_code                 | String         | O         | Zip Code                                                                                                                                                                                                                                                                                                                         |
| address                  | String         | O         | Address                                                                                                                                                                                                                                                                                                                          |
| city                     | String         | O         | City                                                                                                                                                                                                                                                                                                                             |
| state                    | String         | O         | Address State                                                                                                                                                                                                                                                                                                                    |
| country_code             | String         | O         | Country Code                                                                                                                                                                                                                                                                                                                     |
| Param(3DS)               |
| enable                   | boolean        | O         | Enable 3ds.<br>true or false                                                                                                                                                                                                                                                                                                     |
| Param(installment)       | O              | Not used  |
| location_token           | String         | O         | Location Info.<br>Value Example:<br>f72945b6-001f-4972-a606-da9c25da5089                                                                                                                                                                                                                                                         |
| **Response Parameters**     |
| uuid                     | String         | M         | Transaction UUID                                                                                                                                                                                                                                                                                                                 |
| transaction_type         | String         | M         | Transaction type(“sale“). Referring to Appendix                                                                                                                                                                                                                                                                                  |
| sn                       | String         | O         | Device serial number                                                                                                                                                                                                                                                                                                             |
| payment_method           | String         | M         | Payment method(“credit_card“). Referring to Appendix                                                                                                                                                                                                                                                                             |
| amount                   | String         | M         | Transaction amount                                                                                                                                                                                                                                                                                                               |
| tips_amount              | String         | O         | Transaction tips amount                                                                                                                                                                                                                                                                                                          |
| currency                 | String         | M         | Transaction currency. Example: “HKD“                                                                                                                                                                                                                                                                                             |
| dcc_amount               | String         | O         | Transaction amount (Dynamic currency convert), null if it is not DCC transaction.                                                                                                                                                                                                                                                |
| dcc&#95;tips&#95;amount          | String         | O         | Transaction tips amount (Dynamic currency convert), null if it is not DCC transaction.                                                                                                                                                                                                                                           |
| dcc_rate                 | String         | O         | Dynamic currency convert rate, null if it is not DCC transaction.                                                                                                                                                                                                                                                                |
| dcc_currency             | String         | O         | Transaction currency. Example: “USD“ (Dynamic currency convert), null if it is not DCC transaction.                                                                                                                                                                                                                              |
| transaction_state        | String         | M         | Transaction state. Referring to Appendix<br>Field value - Description<br>success - Transaction success<br>pending - Pending，waiting for processing<br>failed - Transaction failed                                                                                                                                                |
| error_code               | String         | O         | Error code. If transaction state is “failed“                                                                                                                                                                                                                                                                                     |
| error_desc               | String         | O         | Error description. If transaction state is “failed“                                                                                                                                                                                                                                                                              |
| date_time                | String         | M         | Transaction date time.                                                                                                                                                                                                                                                                                                           |
| merchant_id              | String         | O         | Merchant ID                                                                                                                                                                                                                                                                                                                      |
| terminal_id              | String         | O         | Terminal ID                                                                                                                                                                                                                                                                                                                      |
| rrn                      | String         | M         | Reference number                                                                                                                                                                                                                                                                                                                 |
| brn                      | String         | M         | Bill reference number                                                                                                                                                                                                                                                                                                            |
| acquirer_name            | String         | O         | Acquirer name, Bank name. Referring to Appendix                                                                                                                                                                                                                                                                                  |
| acquirer_type            | String         | O         | Acquirer type. Referring to Appendix                                                                                                                                                                                                                                                                                             |
| redirect_url             | String         | O         | redirect url                                                                                                                                                                                                                                                                                                                     |
| credit_card              | JSON           | O         | Credit card info                                                                                                                                                                                                                                                                                                                 |
| pay&#95;redirect&#95;url         | String         | M         | Pay redirect url, is a required parameter to call the JS SDK.                                                                                                                                                                                                                                                                    |
| Param(credit_card)       |
| card&#95;reader&#95;mode         | String         | O         | Card reader mode. Referring to Appendix                                                                                                                                                                                                                                                                                          |
| card&#95;exp&#95;date            | String         | O         | Card expired date                                                                                                                                                                                                                                                                                                                |
| card_fallback            | boolean        | O         | Whether it is fallback                                                                                                                                                                                                                                                                                                           |
| card_number              | String         | O         | Card number                                                                                                                                                                                                                                                                                                                      |
| card_sn                  | String         | O         | Card SN                                                                                                                                                                                                                                                                                                                          |
| card&#95;holder&#95;name         | String         | O         | Cardholder name                                                                                                                                                                                                                                                                                                                  |
| icc_request              | String         | O         | EMV ICC request data                                                                                                                                                                                                                                                                                                             |
| icc_response             | String         | O         | EMV ICC response data(TLV string)                                                                                                                                                                                                                                                                                                |
| trace_no                 | String         | O         | Trace number                                                                                                                                                                                                                                                                                                                     |
| batch_no                 | String         | O         | Batch number                                                                                                                                                                                                                                                                                                                     |
| invoice_no               | String         | O         | Invoice number if existed                                                                                                                                                                                                                                                                                                        |
| auth_code                | String         | O         | Auth code                                                                                                                                                                                                                                                                                                                        |
| response_code            | String         | O         | Response code                                                                                                                                                                                                                                                                                                                    |
| extra                    | JSON           | O         |
| Param(3DS)               |
| enrolled                 | String         | O         | enrolled                                                                                                                                                                                                                                                                                                                         |
| Param(installment)       | ||For future use |




## Online Payment

<aside class="notice">
1. Transaction result rely mainly on WebHook, assisted by Query API.<br>
1. We suggest that the interval between calls to the query API is 20 seconds.
</aside>


### Credit Card（Manual、3DS、credit\_card\_token）
> The command request like this:   

```json
POST request url :https://stg-payment.bindo.co/gateway/v3/stores/8527/payment/transactions

Request Headers:  
Content-Type: application/json; charset=UTF-8
X-USER-ACCESS-TOKEN: i2ctfruzrda0efqlu47dnu2zwha97o6  //*login 获取的access token.

Request Body:
{
    "transaction": {
        "amount": "2.0",
        "currency": "HKD",
        "payment_method": "credit_card",    
        "transaction_type": "sale",
        "uuid": "fde16844-5db4-4e62-b5ce-f848814b5d4b",
        
//*"credit card"  如果手输银行卡支付， If payment by credit card，the "credit card" is rquired.
        "credit_card": {
            "card_exp_date": "2412",
            "card_number": "4384375620640049",
            "cvv": "880",
            "card_reader_mode": "manual"
        },
        
//* If payment is by the card number has been bind, the "credit_card_token" is required.
         "credit_card_token": {
             "token":"a61aab03-8c78-415b-97f7-8b338fce5af5"
        },
        
//* If payment is by the 3DS flow, the"3ds", the"success_url" and "fail_url" are required.
        
        "3ds":{
             "enable":true
            },
        "success_url":"https://alpha.stg.gobindo.com/payment-results?status=success",
        "fail_url":"https://alpha.stg.gobindo.com/payment-results?status=failed",
  
       },
        
// * If used WebHook, the "notify_url" is required        
      "notify_url":"https://www.baidu.com",
        
              
      "billing_address":{
      "zip_code":"12345",
      "address": "simple address",
      "city": "city name",
      "state": "address state",
      "country_code": "JP"
       }
     
    }
    
}

```

> The  command response like this:
  
```
Response headers:
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
...

Response body：
{
    "transaction": {
        "amount": "0.2",
        "merchant_id": "987654321",
        "transaction_type": "sale",
        "uuid": "2d852f27-c5a4-4f64-9ab7-5c5e42493589",
        "rrn": "2370549374775197696",
        "acquirer_name": "fake",
//* 交易成功，会返回success,
                
        "transaction_state": "success", //* 交易成功
        
//* 交易失败时，"error_desc"、"transaction_state" and "error_code" are required. 
        "error_desc": "Internal Error 3",
        "transaction_state": "failed",
        "error_code": "199997",
        
//* 交易等待支付时，，返回pending
        "transaction_state": "pending",
        
              
        
        "credit_card": {
            "response_code": "00",
            "card_number": "451461******4854",
            "card_reader_mode": "manual",
            "card_exp_date": "0925",
            "auth_code": "2370549374775197699"
        },
        
        
        "date_time": "2021-10-22T08:49:13.406434762Z",
        "total_amount": "0.2",
        "dcc_amount": "0",
        "transaction_state": "success",
        "currency": "HKD",
        "payment_method": "credit_card",
        "brn": "2370549374607450112",
        "acquirer_type": "visa"
        
        
    }
}



```


`Http request url：https://{base\_url}/gateway/v3/stores/{store\_id}/payment/transactions
Http request method: POST
Http request headers: The required parameters are &quot;Content-Type&quot; and &quot;X-USER-ACCESS-TOKEN&quot;. //\*Please refer to &quot;1.1 Https Header&quot;.
Request parameter format: JSON
Request body： //\*Please refer to &quot;Parameter Description&quot; and Example code.`




### Online Payment by in\_web and in\_app

> The command request like this:

```json
POST Request URL:https://stg-payment.bindo.co/gateway/v3/stores/8527/payment/transactions

Request Headers:  
Content-Type: application/json; charset=UTF-8
X-USER-ACCESS-TOKEN: i2ctfruzrda0efqlu47dnu2zwha97o6  //*login 获取的access token.

Request body:
{
    "transaction": {
    	 "uuid": "d4bcfeff-77fd-4ec5-b931-33a3c4a9d17c",
        "transaction_type": "sale",
       
        "amount": "0.10",   
        "currency": "HKD",  
 //*  If pay by alipay inwerb      
       "payment_method": "alipay",   
       "alipay": {
           "in_web": {
            "return_url": "https://dev-main.wonderpayment.io/return_url",
            "payment_inst": "ALIPAYHK"
           }
        }
        
        
//*        
        "payment_method": "alipay",
        "alipay": {
           "in_app": {
               "appenv": "system=android^version=3.0.1.2",
               "payment_inst": "ALIPAYHK"
           }
           
//*           
      "payment_method": "wechat_pay",  
      "wechat_pay": {
      "in_app": {
        "app_id": ""
      }        
        
//*           
      "payment_method": "wechat_pay",        
      "wechat_pay": {
      "in_web": {
      }
        
    }
}

```
> The command response like this:

```


  
```



Online payment by Alipay or Wechat wallet. This chapter contains 2 different modes, which are in-app andin-web.

in-app means call application SDK to complete payment. The precondition is the device shall install the acquirer application. For example: Call Alipay SDK.

in-web means using acquirer&#39;s URL to complete payment. The response will return a URL and the user shall pay with the URL.


`Http request url：https://{base\_url}/gateway/v3/stores/{store\_id}/payment/transactions
Http request method: POST
Http request headers: The required parameters are &quot;Content-Type&quot; and &quot;X-USER-ACCESS-TOKEN&quot;. //\*Please refer to &quot;1.1 Https Header&quot;.
Request parameter format: JSON Request body： //\*Please refer to &quot;Parameter Description&quot; and Example code.
`

| **Request Parameters**       | **Type** | **Usage** | **Description**                                              |
|:------------------------------|:----------|:-----------|:----|
| payment_method               | String   | M         | Payment method(“alipay“/”wechat_pay”). Referring to Appendix |
| in-app                       | JSON     | O         | In-app request parameters                                    |
| in-web                       | JSON     | O         | In-web request parameters                                    |
| Parameter(Alipay in-app)     |
| appenv                       | String   | O         | App environment. <br>“system=android^version=3.0.1.2”        |
| payment_inst                 | String   | O         | Alipay institution. “ALIPAYHK“ or “ALIPAYCN“                 |
| Parameter(Alipay in-web)     |
| return_url                   | String   | O         | URL to go after the Alipay payment                           |
| payment_inst                 | String   | O         | Alipay institution. “ALIPAYHK“ or “ALIPAYCN“                 |
| Parameter(WeChat Pay in-app) |
| app_id                       | String   | O         | Your APP ID                                                  |
| open_id                      | String   | O         | WeChat open ID                                               |
| Parameter(WeChat Pay in-web) |


### Pre-create
This is for third-party customers and JS SDK to used.


#### location\_client:


> The command request like this:

```json
Request URL: https://stg-payment.bindo.co/gateway/v3/location_client

Request Headers:  
Content-Type: application/json; charset=UTF-8
X-APP-ID: {appid}      //*The appid is provided by Bindo when applying for merchants



```
> The command response like this:

```
Response headers:
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
...

Response body：
{
    "transaction": {               
        "transaction_state": "success",
        "location_token":"f72945b6-001f-4972-a606-da9c25da5089"
        
    }
}

  
```

The API is used to get the field &quot;location\_token&quot;, When location information is required for online payment.

`Http request url：https://{base\_url}/gateway/v3/location\_client
Http request method: POST
Http request headers: The required parameters are &quot;Content-Type&quot; and &quot;X-APP-ID&quot;. //\*Please refer to &quot;1.1 Https Header&quot;.
Request parameter format: JSON Request body： //\*Please refer to &quot;Parameter Description&quot; and Example code.`

**Parameters Description**

| **Request Parameters** | **Type** | **Usage** | **Description**                                                          |
|:-----------|:----------|:-----------|:---------|
| X-APP-ID               | String   | M         | The appid is provided by Bindo when applying for merchants               |
| location_token         | String   | O         | Location Info.<br>Value Example:<br>f72945b6-001f-4972-a606-da9c25da5089 |



#### Pre Create

## Pre-Create
> The Pre-Create command request like this:

```json
POST https://stg-payment.bindo.co/gateway/v3/stores/8527/payment/precreate

Request headers:
Content-Type: application/json; charset=UTF-8
X-USER-ACCESS-TOKEN: i2ctfruzrda0efqlu47dnu2zwha97o6 //*The Value is returned by the Login API
X-CLIENT-IP:  27.38.213.255 

Request body: 
{
  "transaction": {
    "uuid": "d676903f-70b4-4d75-9b76-7c15811faf41", //*New transaction should be requested with a new UUID.
    "amount": "2.00",
    "currency": "HKD",  
    "transaction_type":"sale"
  },
  
// * If used WebHook, the "notify_url" is required        
   "notify_url":"https://www.baidu.com"
}


```
> The Pre-Create command response like this:

```

Reponse body:
{
    "transaction":{
        "uuid":"d676903f-70b4-4d75-9b76-7c15811faf41",
        "brn":"2299382715599882241",
        "total_amount":"2",
        "amount":"2",
        "currency":"HKD",
        "dcc_amount":"0",
        "transaction_state":"pending",
        "pay_redirect_url":"https://oms-dev.bindo.io/gateway/v3/payment/precreate/link?X-CLIENT-SECRET=HxEAHKIAAAHi5vJUrXGORLSMbpc_ZGKtBCCgVWXSiX3o_6qSqGsho15XowxJsL0qcNTq3rq8u5dXeJMr636wQ8yXZWUpf_I-nvH7CG5cDcLYGWJGadLHxCpr16URR8NNJYRsSN0%3D"
        //** The pay_redirect_url is a required parameter to call the JS SDK.
    }
}
  
```

The API is used for processing pre-created orders to get the  "pay_redirect_url required for the call JS SDK for payment.

**HTTP Request:**`Post https://{base_url}/gateway/v3/stores/{store_id}/payment/precreate` 

**Example:**`POST https://stg-payment.bindo.co/gateway/v3/stores/8527/payment/precreate` 

**Parameter Description**

| **Parameters**  | **Type**   | **Usage** | **Description**                                                           |
|:----------|:--------|:------|:-------|
| **Request Parameters**  |        |      |                                                                   |
| uuid                | String | M    | Transaction UUID.<br>It is the unique identifier of the transaction. And new transaction should be requested with a new UUID.                                                 |
| amount              | String | M    | Transaction amount                                                |
| currency            | String | M    | Transaction currency. Example: “HKD“                              |
| transaction_type    | String | M    | Transaction Type. Example “sale“                                  |
| txn&#95;exp&#95;at         | String | O    | The expiration time of the order. and this parameter is optional. <br>If it is not used, the default expiration time for order is 15 minutes.<br>If “txn_exp_at“ is used, the value format is RFC3339 and it should be an absolute time  of 15 minutes less than the create time.<br>Eg: "txn_exp_at":"2021-09-28T14:41:00+08:00",  |
| notify_url          | String | O    | WebHook notify url. The “notify_url” is required if use WebHook.  |
| **Response Parameters** |        |      |                                                                   |
| uuid                | String | M    | Transaction UUID                                                  |
| amount              | String | M    | Transaction amount                                                |
| currency            | String | M    | Transaction currency. Example: “HKD“                              |
| transaction_state   | String | M    | Transaction state. Referring to Appendix<br>Field value - Description<br>success   - Transaction success<br>pending   - Pending，waiting for processing<br>failed    -  Transaction failed                          |
| error_code          | String | O    | Error code. If transaction state is “failed“                      |
| error_desc          | String | O    | Error description. If transaction state is “failed“               |
| brn                 | String | M    | Bill reference number                                             |
| pay_redirect&#95;url   | String | M    | Pay redirect url, is a required parameter to call the JS SDK.     |
| transaction_type    | String | M    | Transaction Type. Example “sale“                                  |



#### Pay For Pre-Create Transaction
> The command request like this:

```json
POST request url :https://stg-payment.bindo.co/gateway/v3/payment/precreate/transactions

Request Headers:
X-CLIENT-SECRET: HxEAHKIAAAGWivMFAgjhnbSMbpc_ZGKtBCCgVWXSiX3o_6qSqGsho15Xow1Av7YhdNfs3Leztp9ZfZEo6X6wQ8yXZWUpf_I-nvH7CG5dC87cFGxHaWJAsnK7HN-K98kV8fWoTfg
Content-Type: application/json; charset=UTF-8

Request Body:
{
    "transaction": {
        "amount": "2.0",
        "credit_card": {
            "card_exp_date": "2412",
            "card_number": "4384375620640049",
            "cvv": "880",
            "card_reader_mode": "manual"
        },
      "billing_address":{
      "zip_code":"12345",
      "address": "simple address",
      "city": "city name",
      "state": "address state",
      "country_code": "JP"
    },
        "currency": "HKD",
        "payment_method": "credit_card",
        "transaction_type": "sale",
        "uuid": "fde16844-5db4-4e62-b5ce-f848814b5d4b"
    }
    
}



```
> The command response like this:

```

{
    "transaction":{
        "uuid":"fde16844-5db4-4e62-b5ce-f848814b5d4b",
        "brn":"2306836477864067073",
        "total_amount":"2",
        "amount":"2",
        "currency":"HKD",
        "dcc_amount":"0",
        "transaction_type":"sale",
        "payment_method":"credit_card",
        "transaction_state":"success",
        "date_time":"2021-09-08T09:56:35.086482619Z",
        "merchant_id":"987654321",
        "rrn":"2306836918567960576",
        "acquirer_name":"fake",
        "acquirer_type":"visa",
        "credit_card":{
            "card_reader_mode":"manual",
            "card_exp_date":"2412",
            "card_fallback":false,
            "card_number":"4384370000010049",
            "auth_code":"2306836918567960579",
            "response_code":"00"
        }
    }
}
  
```



The API is used to pay for pre-create.

`Http request url：https://{base\_url}/gateway/v3/payment/precreate/transactions
Http request method: POST
Http request headers: The required parameters are &quot;Content-Type&quot; and &quot;X-CLIENT-SECRET&quot;. //\*Please refer to &quot;1.1 Https Header&quot;.
Request parameter format: JSON
Request body： //\*Please refer to &quot;Parameter Description&quot; and Example code.`

**Example**

#### **Applepay/ googlepay** in app、 in web \_sandy要api

## 3.4. Native Payment

### Native Payment Flow

sequenceDiagram

participant N as Native Payment Client

participant B as Bindo Server

participant Bank as Bank

N-\&gt;\&gt;B: Native payment request

B-\&gt;\&gt;Bank: Payment request

loop If trans status is &quot;pending&quot;

N--\&gt;\&gt;B: Query Transaction Reslut

end

Bank--\&gt;\&gt;B: Response Payment Result

B--\&gt;\&gt;N: Response Payment status

N-\&gt;\&gt;N:Show Transaction Result

### Credit card

The API used for native payment by credit card

Http request url：https://{base\_url}/gateway/v3/stores/{store\_id}/payment/transactions

Http request method: POST

Http request headers: The required parameters are &quot;Content-Type&quot; and &quot;X-USER-ACCESS-TOKEN&quot;. //\*Please refer to &quot;1.1 Https Header&quot;.

Request parameter format: JSON

Request body： //\*Please refer to &quot;Parameter Description&quot; and Example code.

**Example code**

### QR code

The API is used for Payment by scanning a QR-Code(or barcode). WeChat pay, Alipay, UnionPay QR-Code are supported so far.

Http request url：https://{base\_url}/gateway/v3/stores/{store\_id}/payment/transactions

Http request method: POST

Http request headers: The required parameters are &quot;Content-Type&quot; and &quot;X-USER-ACCESS-TOKEN&quot;. //\*Please refer to &quot;1.1 Https Header&quot;.

Request parameter format: JSON

Request body： //\*Please refer to &quot;Parameter Description&quot; and Example code.

Parameters

**Example code**

### Octopus payment

Native payment by Octopus. The request will return Octopus token and ticket, which shall be used for Octopus Framework.

Http request url：https://{base\_url}/gateway/v3/stores/{store\_id}/payment/transactions

Http request method: POST

Http request headers: The required parameters are &quot;Content-Type&quot; and &quot;X-USER-ACCESS-TOKEN&quot;. //\*Please refer to &quot;1.1 Https Header&quot;.

Request parameter format: JSON

Request body： //\*Please refer to &quot;Parameter Description&quot; and Example code.

### FPS payment

Native Payment by FPS. The FPS QR-Code will be returned and users shall call API to check the payment result.

Http request url：https://{base\_url}/gateway/v3/stores/{store\_id}/payment/transactions

Http request method: POST

Http request headers: The required parameters are &quot;Content-Type&quot; and &quot;X-USER-ACCESS-TOKEN&quot;. //\*Please refer to &quot;1.1 Https Header&quot;.

Request parameter format: JSON

Request body： //\*Please refer to &quot;Parameter Description&quot; and Example code.

# 6.WebHook

WebHook is a callback function. If the payment request has a &quot;notity\_url&quot; parameter, Bindo server will automatically return the transaction result to the notify url after the transaction is completed. When the third party receives the WebHook, it needs to return a response with HTTP status code =200 OK.

**Parameters Description**** ：**Refer to the parameter description of Query a transaction by BRN or UUID.

# 7 void

The api is used for Void sale, pre-authorization and pre-authorization completion.

Note:

- Only unsettled transactions can be voided.
- It is a full amount void transaction

Http request url：https://{base\_url}/gateway/v3/stores/{store\_id}/payment/void

Http request method: POST

Http request headers: The required parameters are &quot;Content-Type&quot; and &quot;X-USER-ACCESS-TOKEN&quot;. //\*Please refer to &quot;1.1 Https Header&quot;.

Request parameter format: JSON

Request body： //\*Please refer to &quot;Parameter Description&quot; and Example code.

参数描述：

Example:

# 8 Refund

The API is used for refund transactions. Full and partial amount refund are supported.

Http request url：https://{base\_url}/gateway/v3/stores/{store\_id}/payment/refund

Http request method: POST

Http request headers: The required parameters are &quot;Content-Type&quot; and &quot;X-USER-ACCESS-TOKEN&quot;. //\*Please refer to &quot;1.1 Https Header&quot;.

Request parameter format: JSON

Request body： //\*Please refer to &quot;Parameter Description&quot; and Example code.

Example

# 9.tips

The API is used for tips adjust for sale transaction

Http request url：https://{base\_url}/gateway/v3/stores/{store\_id}/payment/tips

Http request method: POST

Http request headers: The required parameters are &quot;Content-Type&quot; and &quot;X-USER-ACCESS-TOKEN&quot;. //\*Please refer to &quot;1.1 Https Header&quot;.

Request parameter format: JSON

Request body： //\*Please refer to &quot;Parameter Description&quot; and Example code.

# 10. query

# 11.settlement for native transaction

# 12.credit\_card\_bind

# 13.Option (payment method)

# 14.credit\_card\_bind

# 15 Upload offline transaction（如、FDMS）

- Sale Offline Upload
- Void Offline Upload
- Refund Offline Upload

# Card Number Block List

The api is used for add or delete the black and white list of store card numbers.

**Note:**

**1.It must confirm that the store has been set blacklist or whitelist before call the** card\_block **API, if not the API will response failed.**

**2.The add or delete card number API supports up to 1000 card numbers at a time.**

## **Add the card numbers to store**** ：**

Http request url：https://{base\_url}/gateway/v3/stores/{store\_id}/card\_block/add

Http request method: POST

Http request headers: The required parameters are &quot;Content-Type&quot; and &quot;X-USER-ACCESS-TOKEN&quot;. //\*Please refer to &quot;1.1 Https Header&quot;.

Request parameter format:

## **Delete the card numbers from store**** ：**

Http request url：https://{base\_url}/gateway/v3/stores/{store\_id}/card\_block/del

Http request method: POST

Http request headers: The required parameters are &quot;Content-Type&quot; and &quot;X-USER-ACCESS-TOKEN&quot;. //\*Please refer to &quot;1.1 Https Header&quot;.

Request parameter format:

**Example**

# Appendix

###




