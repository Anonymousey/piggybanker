# Piggybanker

A tool to apply data from your Sky Mobile Piggybank account on a monthly basis.

**THIS TOOL IS A WORK IN PROGRESS**

Task List : 

* **Authentication** and **JSON requests for applying piggybank data to account** Early Progress - see **Progress**
* **Tool to automatically apply data** - Not started.
* **SMS Confirmation Feature** - Early idea to send out bulk SMS from my ZTE router. Not started.

## Progress

Logon

```raw 
POST https://skyidapp.sky.com/signin/service/skycom HTTP/1.1
Host: skyidapp.sky.com
Connection: keep-alive
Content-Length: 134
sec-ch-ua: "Chromium";v="91", " Not;A Brand";v="99"
sec-ch-ua-mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.77 Safari/537.36
Content-Type: application/x-www-form-urlencoded
Accept: */*
Origin: https://www.sky.com
Sec-Fetch-Site: same-site
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://www.sky.com/
Accept-Encoding: gzip, deflate, br
Accept-Language: en,en-GB;q=0.9
Cookie: [GENERATED ON LOGON]
userIdentifier=[SKY EMAIL]&password=[SKY PASSWORD]&rememberMe=false&forgotUsername=&submitButton=&signUp=&privacyNotice=
```

(Account contained in the cookies?) this request might be used to ask for your account information.
```raw 
POST / HTTP/1.1
Host: skyport.sky.com
Connection: keep-alive
Content-Length: 2439
sec-ch-ua: "Chromium";v="91", " Not;A Brand";v="99"
Accept-Language: en-GB
sec-ch-ua-mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.77 Safari/537.36
content-type: application/json
accept: */*, application/json
Sky-Sales-Version: 2
Sky-Domain: mobile-service
x-api-token: [GENERATED ON LOGIN?]
Origin: https://www.sky.com
Sec-Fetch-Site: same-site
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://www.sky.com/
Accept-Encoding: gzip, deflate, br
Cookie: [GENERATED ON LOGIN]
```

```json

[{"operationName":"piggybankRoll","variables":{},"query":"fragment piggybankRollBalanceFragment on Customer {\n  billingAccount(type: mobile) {\n    piggybank {\n      canRoll\n      currentBalance {\n        data(unit: GB, decimalPlaces: 2)\n        __typename\n      }\n      __typename\n    }\n    __typename\n  }\n  __typename\n}\n\nfragment piggybankRollRemainingFragment on Mobile {\n  nextBill {\n    usage {\n      sims(sortBy: SERVICE_INSTANCE_ID) {\n        serviceInstanceIdentifier\n        allowances(type: \"DATA\") {\n          formattedRemainingAllowance {\n            amount\n            unit\n            __typename\n          }\n          accumulatedUsagePercentage\n          __typename\n        }\n        __typename\n      }\n      __typename\n    }\n    __typename\n  }\n  __typename\n}\n\nquery piggybankRoll {\n  me {\n    ...piggybankRollBalanceFragment\n    mobile {\n      sims(sortBy: SERVICE_INSTANCE_ID) {\n        friendlyName\n        phoneNumber\n        serviceInstanceIdentifier\n        status\n        subStatus\n        __typename\n      }\n      ...piggybankRollRemainingFragment\n      __typename\n    }\n    __typename\n  }\n  mobileCatalogue {\n    addonsProducts(type: DATA, paymentType: PIGGYBANK) {\n      id\n      name\n      __typename\n    }\n    __typename\n  }\n}\n"},{"operationName":"piggybankRewards","variables":{},"query":"query piggybankRewards {\n  me {\n    billingAccount(type: mobile) {\n      piggybank {\n        hasRedemptionOptions\n        currentBalance {\n          data(unit: GB, decimalPlaces: 2)\n          __typename\n        }\n        __typename\n      }\n      __typename\n    }\n    __typename\n  }\n}\n"},{"operationName":"piggybankActivity","variables":{},"query":"query piggybankActivity {\n  me {\n    mobile {\n      sims {\n        phoneNumber\n        serviceInstanceIdentifier\n        friendlyName\n        __typename\n      }\n      __typename\n    }\n    billingAccount(type: mobile) {\n      piggybank {\n        transactionHistory {\n          closingBalanceValue(unit: GB, decimalPlaces: 2)\n          transactions {\n            serviceInstanceId\n            date\n            activityType\n            data(unit: GB, decimalPlaces: 2)\n            newBalanceValue(unit: GB, decimalPlaces: 2)\n            __typename\n          }\n          __typename\n        }\n        __typename\n      }\n      __typename\n    }\n    __typename\n  }\n}\n"}]
```

This might be how the 1GB data is applied to an account.

```raw
POST https://skyport.sky.com/ HTTP/1.1
Host: skyport.sky.com
Connection: keep-alive
Content-Length: 488
sec-ch-ua: "Chromium";v="91", " Not;A Brand";v="99"
Accept-Language: en-GB
sec-ch-ua-mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.77 Safari/537.36
content-type: application/json
accept: */*, application/json
Sky-Sales-Version: 2
Sky-Domain: mobile-service
x-api-token: be4e7130-7a20-0137-0000-3ee5354efa8f
Origin: https://www.sky.com
Sec-Fetch-Site: same-site
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://www.sky.com/
Accept-Encoding: gzip, deflate, br
Cookie: [COOKIE PROBABLY GENERATED ON LOGON]
```

REQUEST IN JSON
```json
[{"operationName":"OrderMobileProduct","variables":{"complete":true,"multiple":1,"productId":"14309","serviceInstanceIdentifier":"[SEE COMMENT BELOW]"},"query":"mutation OrderMobileProduct($complete: Boolean!, $multiple: Int!, $productId: String!, $serviceInstanceIdentifier: String!) {\n  orderMobileProduct(completeOrder: $complete, multiple: $multiple, productId: $productId, serviceInstanceIdentifier: $serviceInstanceIdentifier) {\n    orderReference\n    __typename\n  }\n}\n"}]
```

serviceInstanceIdentifier =  This is a 23 digit number. might be an account ID or related to your own personal piggybank ID (if such a thing exists)

Possible security flaw. May not require Authentication to complete the above request as long as you know the serviceInstanceIdentifier. Will investigate.

RESPONCE IN JSON

```json
[{"data":{"orderMobileProduct":{"orderReference":"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","__typename":"MobileProductOrderType"}}}]
```                                                   
The request seems to complete with an orderReference number. Perhaps this is used by customer services to look up a transaction internally.


## How It Works

Not complete yet.

## Usage

Not complete yet.

[Website]: 
[Contact Info]: 

