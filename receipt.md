TO complete the IAP reconciliation, we need to go through the following steps:



## 1.Done by Unity

### 1.1 cloud moolah call UDP api (take UDP int environment for an example)

```
POST /udp/api/order-callbacks/cloudmoolah  HTTP/1.1
Host: connect-int.unity.com

status = "Success" or "Pending"
signature = MD5.md5(status + productid + orderid + currency + amount + country + bundleId + appSecretKey)
signature = MD5.md5("Success" + "iap01" + "123232314523" + "USD" + "2.99" + "MY" + "874d250e-034a-40cf-b5e5-afcb35d6cea6" + "xxxxxxxx")

Content-Type: application/json
Request Body
{
	"signature":"1234", 
	"payload": {
	    "status": "Success", // required
	    "productId": "iap01", // required
	    "clientId": "Evp2S_ew64Aq6Dq5pSc5fA", // required
	    "extension": "", // required when the developer payload from sdk is not empty
	    "payTime": "2018-09-28T06:43:20Z", // required
	    "cpOrderId": "123232314523",// required
	    "currency": "USD",// required
	    "amount": "2.99",// required
	    "country": "MY", // required

	    "cmOrderId": "811626476321551664719", //optional
	    "appId": "com.unity.testseven", // the package name , optional
	    "orgId":"18966594121784",
	    "bundleId": "874d250e-034a-40cf-b5e5-afcb35d6cea6" //optional
	}
}
```

---------------------------------------------------------------------------------


## 2 (Done by CloudMoolah Team)

cloud moolah 提供给udp 查询receipt 的接口, 包括 单个receipt的确认 和 receipt 集合的确认

### 2.1 single receipt query

(Done by CloudMoolah Team)
```
GET /api/App/iap/receipts/{cporderId}/{signature}
https://devapi.cloudmoolah.com/api/App/iap/receipts/783878525701551339075/0b519af6905564d482de9c0bfc1138f0
https://staging-devapi.cloudmoolah.com/api/App/iap/receipts/783878525701551339075/0b519af6905564d482de9c0bfc1138f0
http://localhost:53949/api/App/iap/receipts/783878525701551339075/0b519af6905564d482de9c0bfc1138f0

signature = md5(cporderId + app secretkey)

Response Body in json format
{
    "Data": {
	    "status": "Success", // required
	    "productId": "iap01", // required
	    "clientId": "Evp2S_ew64Aq6Dq5pSc5fA", // required
	    "extension": "", // required when the developer payload from sdk is not empty
	    "payTime": "2018-09-28T06:43:20Z", // required
	    "cpOrderId": "123232314523",// required
	    "currency": "USD",// required
	    "amount": "2.99",// required
	    "country": "MY", // required

	    "cmOrderId": "811626476321551664719", //optional
	    "appId": "com.unity.testseven", // the package name , optional
	    "orgId":"18966594121784",
	    "bundleId": "874d250e-034a-40cf-b5e5-afcb35d6cea6" //optional
	},
    "DataCount": 0,
    "StatusCode": 200,
    "Result": true,
    "ReasonCode": 0,
    "Message": "Success"
}
```
-------------------------------------------------------------------------------


### 2.2 batch receipts query

(Done by CloudMoolah Team)
```
GET /api/App/receipts?start_timestamp=xxx&end_timestamp=xxx&offset=1&limit=5&signature=xxx
https://devapi.cloudmoolah.com/api/App/receipts?start_timestamp=1551665225&end_timestamp=1551927868&offset=1&limit=5&signature=f9bc734caa2e6aac634e712944726838
https://staging-devapi.cloudmoolah.com/api/App/receipts?start_timestamp=1551665225&end_timestamp=1551927868&offset=1&limit=5&signature=f9bc734caa2e6aac634e712944726838
http://localhost:53949/api/App/receipts?start_timestamp=1551665225&end_timestamp=1551927868&offset=1&limit=5&signature=f9bc734caa2e6aac634e712944726838

TimeStamp	DateTime
1551665225	2019-03-04 10:07:05.117
1551927868	2019-03-07 11:04:28.140

signature = md5(UnityClientId + start_timestamp + end_timestamp + offset + limit + UnityClientSecret)

Response Body in json format
{
    "Data": [{
	    "status": "Success", // required
	    "productId": "iap01", // required
	    "clientId": "Evp2S_ew64Aq6Dq5pSc5fA", // required
	    "extension": "", // required when the developer payload from sdk is not empty
	    "payTime": "2018-09-28T06:43:20Z", // required
	    "cpOrderId": "123232314523",// required
	    "currency": "USD",// required
	    "amount": "2.99",// required
	    "country": "MY", // required

	    "cmOrderId": "811626476321551664719", //optional
	    "appId": "com.unity.testseven", // the package name , optional
	    "orgId":"18966594121784",
	    "bundleId": "874d250e-034a-40cf-b5e5-afcb35d6cea6" //optional
	},
	{
	    "status": "Success", // required
	    "productId": "iap01", // required
	    "clientId": "Evp2S_ew64Aq6Dq5pSc5fA", // required
	    "extension": "", // required when the developer payload from sdk is not empty
	    "payTime": "2018-09-28T06:43:20Z", // required
	    "cpOrderId": "123232314523",// required
	    "currency": "USD",// required
	    "amount": "2.99",// required
	    "country": "MY", // required

	    "cmOrderId": "811626476321551664719", //optional
	    "appId": "com.unity.testseven", // the package name , optional
	    "orgId":"18966594121784",
	    "bundleId": "874d250e-034a-40cf-b5e5-afcb35d6cea6" //optional
	}
    ],
    "DataCount": 2,
    "StatusCode": 200,
    "Result": true,
    "ReasonCode": 0,
    "Message": "Success"
}
```
