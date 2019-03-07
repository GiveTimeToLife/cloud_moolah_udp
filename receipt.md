TO complete the IAP reconciliation, we need to go through the following steps:

(Done by Unity)
1.cloud moolah call UDP api (take UDP int environment for an example)
our proposal:
POST /udp/api/order-callbacks/cloudmoolah  HTTP/1.1
Host: connect-int.unity.com

status = "Success" or "Pending"
signature = MD5.md5(status + productid + orderid + currency + amount + country + bundleId + appSecretKey)
signature = MD5.md5("Success" + "iap01" + "123232314523" + "USD" + "2.99" + "MY" + "874d250e-034a-40cf-b5e5-afcb35d6cea6" + "xxxxxxxx")

Content-Type: application/json
Request Body
{
	"signature":"1234", 
	"payload":{
		"status":"Success",
		"productId":"iap01",
		"extension":"",
		"payTime":"2018-09-28T06:43:20Z",
		"cpOrderId":"123232314523",		
		"currency":"USD",
		"amount":"2.99",
		"country":"MY",		
		"bundleId":"874d250e-034a-40cf-b5e5-afcb35d6cea6"
	}
}

---------------------------------------------------------------------------------

(Done by CloudMoolah Team)
2.cloud moolah 提供给udp 查询receipt 的接口, 包括 单个receipt的确认 和 receipt 集合的确认

2.1 单个receipt 查询（可选）
GET /api/App/iap/receipts/{cporderId}/{signature}
https://devapi.cloudmoolah.com/api/App/iap/receipts/783878525701551339075/0b519af6905564d482de9c0bfc1138f0
https://staging-devapi.cloudmoolah.com/api/App/iap/receipts/783878525701551339075/0b519af6905564d482de9c0bfc1138f0
http://localhost:53949/api/App/iap/receipts/783878525701551339075/0b519af6905564d482de9c0bfc1138f0

signature = md5(cporderId + app secretkey)

Response Body in json format
{
    "Data": {
        "cmorderid": "783878525701551339075",
        "currency": "USD",
        "amount": "118.00",
        "payment_status": "Success",
        "payment_date": "2019-02-28 15:31:14.057",
        "productid": "com.blue.violet.01",
        "clientid": null,
        "orgid": null
    },
    "DataCount": 0,
    "StatusCode": 200,
    "Result": true,
    "ReasonCode": 0,
    "Message": "Success"
}

-------------------------------------------------------------------------------

(Done by CloudMoolah Team)
2.2 receipt 集合的查询 (必需)
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
    "Data": [
        {
            "cmorderid": "561278146321551663532",
            "currency": "USD",
            "amount": "18.00",
            "payment_status": "Pending",
            "payment_date": "2019-03-04 09:38:46.670",
            "productid": "com.black.sunflower.01",
            "clientid": null,
            "orgid": null
        },
        {
            "cmorderid": "811626476321551664719",
            "currency": "USD",
            "amount": "0.10",
            "payment_status": "Pending",
            "payment_date": "2019-03-04 09:58:38.603",
            "productid": "com.golden.rose.01",
            "clientid": null,
            "orgid": null
        }
    ],
    "DataCount": 2,
    "StatusCode": 200,
    "Result": true,
    "ReasonCode": 0,
    "Message": "Success"
}

