TO complete the IAP reconciliation, we need to go through the following steps:



## 1.Done by Unity

### 1.1 cloud moolah call UDP api (take UDP int environment for an example)

```
POST /udp/api/order-callbacks/cloudmoolah  HTTP/1.1
Host: connect-int.unity.com

status = "Success" or "Pending"
signature = base64.encode(MD5.md5(paylod + appSecretKey))
signature = MD5.md5({
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
	},appSecretKey)
```

```go
key := "a1af0e03de5248d79a61f5862a59ae65"
str := `{"status":"Success","productId":"com.test18.1.com","clientId":null,"extension":"Testing from localhost","payTime":"2019-02-18 12:19:56.627","cpOrderId":"000000","currency":"USD","amount":"10.00","country":"MY","cmOrderId":"227174095691550463597","appId":"1880","orgId":null,"bundleId":"bfeac227-e904-4294-bb98-148d61b60353"}}`

hash := md5.New()
hash.Write([]byte(str))
hash.Write([]byte(key))
sign := base64.StdEncoding.EncodeToString(hash.Sum(nil))
fmt.Println(sign) //rRzipjg6sX8GGnRq98JGoA==
```

```
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
GET /api/App/iap/receipts/{cporderId}?signature={signature}

https://devapi.cloudmoolah.com/api/App/iap/receipts/997fee0e-b34a-4743-839e-ebdc77854c99?signature=MGRmNzhmOTU4ZGNkY2RiYTJlMjI1ODIxZTIxYWMxYTg=
https://staging-devapi.cloudmoolah.com/api/App/iap/receipts/997fee0e-b34a-4743-839e-ebdc77854c99?signature=MGRmNzhmOTU4ZGNkY2RiYTJlMjI1ODIxZTIxYWMxYTg=
http://localhost:53949/api/App/iap/receipts/997fee0e-b34a-4743-839e-ebdc77854c99?signature=MGRmNzhmOTU4ZGNkY2RiYTJlMjI1ODIxZTIxYWMxYTg=

signature =  base64.encode( md5(cporderId + app secretkey))
!! after base64 encode , url encode still needed.
```
```go
key := "a1af0e03de5248d79a61f5862a59ae65"
cpOrderId:="123232314523"
hash := md5.New()
hash.Write([]byte(cpOrderId))
hash.Write([]byte(key))
fmt.Println(sign) //p8CAJegD415W5VP6muD58g==
```
```
Response Body in json format
{
    "Data": {
        "status": "Pending",
        "productId": "product_6_full_game",
        "clientId": null,
        "extension": "",
        "payTime": "2019-02-26 15:06:45.527",
        "cpOrderId": "997fee0e-b34a-4743-839e-ebdc77854c99",
        "currency": "USD",
        "amount": "2.99",
        "country": "MY",
        "cmOrderId": "297053764081551164806",
        "appId": "596",
        "orgId": null,
        "bundleId": "214d5633-9a17-4a33-8e05-4482f8f4cc22"
    },
    "DataCount": 1,
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

https://devapi.cloudmoolah.com/api/App/receipts?start_timestamp=1551665225&end_timestamp=1551927868&offset=1&limit=2&signature=6un6eO10SVXWD6q2gI3obQ==
https://staging-devapi.cloudmoolah.com/api/App/receipts?start_timestamp=1551665225&end_timestamp=1551927868&offset=1&limit=2&signature=6un6eO10SVXWD6q2gI3obQ==
http://localhost:53949/api/App/receipts?start_timestamp=1551665225&end_timestamp=1551927868&offset=1&limit=2&signature=6un6eO10SVXWD6q2gI3obQ==

TimeStamp	DateTime
1551665225	2019-03-04 10:07:05.117
1551927868	2019-03-07 11:04:28.140

signature = base64.encode(md5(start_timestamp + end_timestamp + offset + limit + UnityClientSecret))
!! after base64 encode , url encode still needed.
```

```go

	key := "a1af0e03de5248d79a61f5862a59ae65"
	start_timestamp:="1551665225"
	end_timestamp:="1551927868"
	offset:="1"
	limit:="5"
	hash := md5.New()
	hash.Write([]byte(start_timestamp))
	hash.Write([]byte(end_timestamp))
	hash.Write([]byte(offset))
	hash.Write([]byte(limit))
	hash.Write([]byte(key))
	sign := base64.StdEncoding.EncodeToString(hash.Sum(nil))
	fmt.Println(sign)//qYgJcHOJkuqslOJwtHDquA==

```

```
Response Body in json format
{
    "Data": [
        {
            "status": "Pending",
            "productId": "com.black.sunflower.01",
            "clientId": null,
            "extension": "Testing from localhost",
            "payTime": "2019-03-04 09:38:46.670",
            "cpOrderId": "000000",
            "currency": "USD",
            "amount": "18.00",
            "country": "MY",
            "cmOrderId": "561278146321551663532",
            "appId": "465",
            "orgId": null,
            "bundleId": "34634734"
        },
        {
            "status": "Pending",
            "productId": "com.golden.rose.01",
            "clientId": null,
            "extension": "{\"AnyKeyYouWant:\" : \"AnyValueYouWant\"}",
            "payTime": "2019-03-04 09:58:38.603",
            "cpOrderId": "81d1c761-fba3-4088-8331-3d5fc24ac203",
            "currency": "USD",
            "amount": "0.10",
            "country": "MY",
            "cmOrderId": "811626476321551664719",
            "appId": "465",
            "orgId": null,
            "bundleId": "34634734"
        }
    ],
    "DataCount": 2,
    "StatusCode": 200,
    "Result": true,
    "ReasonCode": 0,
    "Message": "Success"
}
```
