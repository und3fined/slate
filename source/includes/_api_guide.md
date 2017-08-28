
# API Guide

## Tạo khách hàng

```shell
curl -X POST \
  https://api.finsify.com/v2/customer \
  -H 'accept: application/json' \
  -H 'content-type: application/json' \
  -H 'client-id: sample-clientId' \
  -H 'service-secret: sample-clientSecret' \
  -d '{
    "identifier": "sample-identifier-user"
  }'
```

```javascript
const request = require('request');

var request = require("request");

var options = { method: 'POST',
  url: 'https://api.finsify.com/v2/customer',
  headers:
   { 'service-secret': 'sample-clientSecret',
     'client-id': 'sample-clientId',
     'content-type': 'application/json',
     accept: 'application/json' },
  body: { identifier: 'sample-identifier-user' },
  json: true };

request(options, function (error, response, body) {
  if (error) throw new Error(error);

  console.log(body);
});
```

> The above command returns JSON structured like this:

```json
{
    "data": {
        "customer_id": "115eff49-1323-4f09-9fc1-c8009d81777b",
        "identifier": "sample-identifier-user"
    },
    "version": "v2"
}
```

> Sample error

```json
{
    "error": "CustomerExists",
    "message": "Customer \"sample-identifier-user\" is exists",
    "extra": {
        "identifier": "sample-identifier-user"
    }
}
```

This endpoint allow create a customer.

### HTTP Request

`POST https://api.finsify.com/v2/customer`

### Body Parameters

Parameter | Type | Description
--------- | ------- | -----------
identifier | string | Định danh người dùng trên hệ thống của bạn.

<aside class="notice">
Please replace `sample-clientId` and `sample-clientSecret` with your real developer key.
</aside>

## Tạo token

```shell
curl -X POST \
  https://api.finsify.com/v2/token \
  -H 'client-id: sample-clientId' \
  -H 'content-type: application/json' \
  -H 'service-secret: sample-clientSecret' \
  -d '{
    "customer_id": "115eff49-1323-4f09-9fc1-c8009d81777b",
    "service_id": 8,
    "callback_url": "http://localhost:2000/v2"
}'
```

```javascript
var request = require("request");

var options = { method: 'POST',
  url: 'https://api.finsify.com/v2/token',
  headers: 
   { 'service-secret': 'sample-clientSecret',
     'client-id': 'sample-clientId',
     'content-type': 'application/json' },
  body: 
   { customer_id: '115eff49-1323-4f09-9fc1-c8009d81777b',
     service_id: 8,
     callback_url: 'http://localhost/finsify-callback' },
  json: true };

request(options, function (error, response, body) {
  if (error) throw new Error(error);

  console.log(body);
});

```

> The above command returns JSON structured like this:

```json
{
    "data": {
        "id": "fa03b249-3024-4564-a4bf-2661319e5a07",
        "connect_url": "https://api.finsify.com/v2/connect/term/fa03b249-3024-4564-a4bf-2661319e5a07"
    },
    "version": "v2"
}
```

Để có thể kết nối với các dịch vụ của Finsify cung cấp cần có một token.

### HTTP Request

`POST https://api.finsify.com/v2/token`

### Body Parameters

Parameter | Type | Description
--------- | ---- |-----------
customer_id | string | Định danh của người dùng trên hệ thống Finsify, có được sau khi tạo khách hàng.
service_id | integer | Mã dịch vụ Finsify cung cấp. Xem thêm [Danh sách dịch vụ](#d-ch-v)
callback_url | string | Sau khi kết nối thành công, Finsify sẽ gửi dữ liệu kết nối của người dùng qua URL này.

### Response

Với response trả về Developer sử dụng `connect_url` để truy cập vào trang kết nối của Finsify.

<aside class="warning">
Token chỉ dược sử dụng một lần.
</aside>
<aside class="warning">
Token bị hết hạn sau 30 phút nếu không được sử dụng.
</aside>

## Connect response

Là dữ liệu được trả về qua `callback_url` khi thực hiện kết nối. `callback_url` được khai báo khi thực hiện tạo `token`.

> Sample connect successful

```json
{
    "status": "success",
    "login_id": 23456,
    "secret": "78cea84c-7eb8-4425-2312-3a1ac8383afe"
}
```

### Mô tả flow

Khi thực hiện kết nối thành công trên trang API connect của Finsify Hub, lúc này Finsify sẽ thực hiện gửi dữ liệu qua webview hoặc trình duyệt web bằng cách redirect tới `callback_url` được khai báo.

Ví dụ:

`yourapp://finsify-hub/{"status":"success","login_id":23456,"secret":"78cea84c-7eb8-4425-2312-3a1ac8383afe"}`


## Danh sách tài khoản

```shell
curl -X GET \
  https://api.finsify.com/v2/account \
  -H 'client-id: sample-clientId' \
  -H 'content-type: application/json' \
  -H 'login-secret: 78cea84c-7eb8-4425-2312-3a1ac8383afe' \
  -H 'service-secret: sample-secret'
```

```javascript
var request = require("request");

var options = { method: 'GET',
  url: 'https://api.finsify.com/v2/account',
  headers: 
   { 'login-secret': '78cea84c-7eb8-4425-2312-3a1ac8383afe',
     'service-secret': 'sample-serviceSecret',
     'client-id': 'sample-clientId',
     'content-type': 'application/json' } };

request(options, function (error, response, body) {
  if (error) throw new Error(error);

  console.log(body);
});

```

> Request successful. Response return JSON data:

```json
{
    "data": [
        {
            "id": 14324,
            "original_id": "mockup-data-123456",
            "login_id": 123456,
            "name": "Finsify sample account",
            "balance": "291087.00",
            "currency": "VND",
            "type": "current"
        }
    ],
    "version": "v2"
}
```

> Or request failed

```json
{
    "error": "LoginNotFound",
    "message": "Cannot find Login with login secret: 'd16bdfd0-5bd2-46d3-96c2-12313sdsda'.",
    "extra": {
        "loginSecret": "d16bdfd0-5bd2-46d3-96c2-12313sdsda"
    }
}
```

Với API này `developer` có thể lấy được danh sách tài khoản của dịch vụ mà người dùng vừa thực hiện kết nối. 

### HTTP Request

`GET https://api.finsify.com/v2/account`

### Header Parameters

Trên header của request lúc này thêm `login-secret`. Finsify sẽ dựa trên thông tin này để tìm danh sách tài khoản của người dùng.

Header | Description
------ | -----------
login-secret | Có được sau khi kết nối thành công.

### Response

Dữ liệu được gửi về có cấu trúc như bảng dưới:

Params | Type | Description
------ | ---- | -----------
data | Array | Finsify [account](#models-account) data
version | String | Finsify API version

## Get transactions

API này cho phép `developer` lấy danh sách giao dịch của một tài khoản.

```shell
curl -X GET \
  'https://api.finsify.com/v2/transaction?account_id=14324&from_date=2016-08-26&to_date=2017-10-25' \
  -H 'client-id: sample-clientId' \
  -H 'content-type: application/json' \
  -H 'login-secret: 78cea84c-7eb8-4425-2312-3a1ac8383afe' \
  -H 'service-secret: sample-service-secret'
```

```javascript
var request = require("request");

var options = { method: 'GET',
  url: 'https://api.finsify.com/v2/transaction',
  qs: 
   { account_id: '1226893',
     from_date: '2016-08-26',
     to_date: '2017-10-25' },
  headers: 
   { 'login-secret': '626fb63e-0543-4722-9e78-e9e42fca2e85',
     'service-secret': '75167f66-22dc-415d-b799-c0dd73b951ca',
     'client-id': 'Tu5dvG07KVpx6b',
     'content-type': 'application/json' } };

request(options, function (error, response, body) {
  if (error) throw new Error(error);

  console.log(body);
});

```

> Response if request is successful

```json
{
    "data": [
        {
            "id": 4116357,
            "original_id": "techcombank-0bd9d3f5d3406cf0b1d545ccab422fa2",
            "date": "2017-03-27",
            "amount": "-61299.00",
            "currency": "VND",
            "description": "GD THE QUA POS SO THE 422149...0052 NGAY 26/03/2017 TAI UBER VN MAR27 R5HKO HE help.uber.com NL APPCODE 579124 TID / FT17086406712347",
            "category": "auto_and_transport",
            "meta": null
        },
        {
            "id": 4116358,
            "original_id": "techcombank-1cef6959d463d8934115ce5e9a2a0c7c",
            "date": "2017-03-27",
            "amount": "-23450.00",
            "currency": "VND",
            "description": "GD THE QUA POS SO THE 422149...0052 NGAY 26/03/2017 TAI UBER VN MAR27 AQBOO HE help.uber.com NL APPCODE 576931 TID / FT17086103050008",
            "category": "auto_and_transport",
            "meta": null
        },
        {
            "id": 4116359,
            "original_id": "techcombank-a8dae967460a0881ecee8f849f408bd2",
            "date": "2017-03-27",
            "amount": "-127396.00",
            "currency": "VND",
            "description": "GD THE QUA POS SO THE 422149...0052 NGAY 26/03/2017 TAI UBER VN MAR26 PW7K5 HE help.uber.com NL APPCODE 535772 TID / FT17086522805934",
            "category": "auto_and_transport",
            "meta": null
        },
        {
            "id": 4116353,
            "original_id": "techcombank-5c03e6df3ee5381acf0dfa6ba320c9dc",
            "date": "2017-03-27",
            "amount": "-26467.00",
            "currency": "VND",
            "description": "GD THE QUA POS SO THE 422149...0052 NGAY 27/03/2017 TAI UBER VN MAR27 HCADN HE help.uber.com NL APPCODE 583730 TID / FT17086851032573",
            "category": "auto_and_transport",
            "meta": null
        },
        {
            "id": 4779956,
            "original_id": "techcombank-f8c88974d15f0beb80d7527256709c45",
            "date": "2017-07-04",
            "amount": "-26000.00",
            "currency": "VND",
            "description": "GD THE QUA POS SO THE 422149...0052 NGAY 04/07/2017 TAI UBER TRIP KLPVK HELP.U help.uber.com NL APPCODE 198836 TID / FT17185354419755",
            "category": "auto_and_transport",
            "meta": null
        },
        ...
    ],
    "version": "v2"
}
```

### HTTP Request

`GET https://api.finsify.com/v2/transaction`

### Query Parameters

Parameters | Type | Description
---------- | ---- | -----------
account_id | integer | Mã tài khoản của khách hàng.
from_date | date('YYYY-MM-DD') | Ngày bắt đầu lấy giao dịch.
end_date | date('YYYY-MM-DD') | Ngày kết thúc.

## Webhooks

Webhooks are notifications (HTTP callbacks) to customers that let them know an event has occurred. They can take relevant/useful actions as needed.

### Benefits of Webhooks

With webhooks developers don't have to poll continuously to know the status of a process. Finsify will keep customers informed about the event. Finsify webhooks let customers know:

* the MFA information is requested by the provider site to log in for aggregating data
* the Finsify system has performed the login on the provider site successfully
* the Finsify system has retrieved the account summary information from the provider site successfully
* the final status of account addition or update account APIs

### Webhooks APIs

Customers can use the following webhooks services...

### Webhooks Supported Events

* Notification new transaction.
* Notification login change status.

### New transaction

Khi có phát sinh giao dịch mới Finsify sẽ gửi thông tin giao dịch mới qua webhook endpoint đã được thiết lập trong trang Developer.

> Một message thông báo transaction mới.

```json
{
  "login_id": 21000,
  "timestamp": 1467331200000,
  "accounts": [{"id": 19316}],
  "new": 1,
  "referenceId": "hsSU83HnX"
}
```

### Login bị thay đổi trạng thái

* `login_wrong_credentials` - login changed password.
* `login_password_expired` - login password is expired.
* `login_account_locked` - login is locked.

> Ví dụ một message gửi qua webhook thông báo login bị thay đổi trạng thái

```json
{
  "status": "login_wrong_credentials",
  "login_id": 538
}

```

`login_wrong_credentials` xảy ra khi tài khoản của người dùng bị đổi mật khẩu.
`login_password_expired` mật khẩu của tài khoản đã bị hết hạn sử dụng, người dùng cần truy cập trang internet banking  hoặc gọi đến tổng tài của bank để thực hiện đổi mật khẩu
`login_account_locked` tài khoản của người dùng đã bị khoá. Người dùng cần liên hệ với ngân hàng để được hỗ trợ.



## Reconnect

Sử dụng khi người dùng cần đăng nhập lại tài khoản, ví dụ như người dùng đã đổi mật khẩu tài khoản internet banking.

```shell
curl -X POST \
  https://api.finsify.com/v2/token/reconnect \
  -H 'client-id: sample-clientId' \
  -H 'content-type: application/json' \
  -H 'login-secret: sample-loginSecret' \
  -H 'service-secret: sample-serviceSecret' \
  -d '{
    "callback_url": "http://localhost:2000/v2"
}'
```

```javascript

var request = require("request");

var options = { method: 'POST',
  url: 'https://api.finsify.com/v2/token/reconnect',
  headers: 
   { 'login-secret': 'sample-loginSecret',
     'service-secret': 'sample-serviceSecret',
     'client-id': 'sample-clientId',
     'content-type': 'application/json' },
  body: { callback_url: 'http://localhost:2000/v2' },
  json: true };

request(options, function (error, response, body) {
  if (error) throw new Error(error);

  console.log(body);
});
```

> Response if request is successful

```json
{
    "data": {
        "id": "def5ac74-3d17-46ef-a940-4b3dd1a874ab",
        "connect_url": "https://api.finsify.com/v2/connect/term/def5ac74-3d17-46ef-a940-4b3dd1a874ab"
    },
    "version": "v2"
}
```


### HTTP Request

`POST https://api.finsify.com/v2/token/reconnect`

### Body Parameters

Parameters | Type | Description
---------- | ---- | -----------
callback_url | string | Sau khi kết nối thành công, Finsify sẽ gửi dữ liệu kết nối của người dùng qua URL này.

## Active/Inactive login

API này hỗ trợ đối tác của Finsify Hub có thể chủ động thay đổi trạng thái của login của người dùng.

```shell
curl -X PUT \
  https://sandbox.zoostd.com/v2/login/deactivate \
  -H 'client-id: sample-clientId' \
  -H 'content-type: application/json' \
  -H 'login-secret: sample-loginSecret' \
  -H 'service-secret: sample-serviceSecret'

```

```javascript
var request = require("request");

var options = { method: 'PUT',
  url: 'https://sandbox.zoostd.com/v2/login/deactivate',
  headers: 
   { 'login-secret': 'sample-loginSecret',
     'service-secret': 'sample-serviceSecret',
     'client-id': 'sample-clientId',
     'content-type': 'application/json' },
  json: true };

request(options, function (error, response, body) {
  if (error) throw new Error(error);

  console.log(body);
});
```

> Response if active successfull

```json
{
  "data": {
    "id": 52345,
    "activate": true
  }
}
```

> Or deactive 

```json
{
  "data": {
    "id": 52345,
    "deActivate": true
  }
}
```

### HTTP Request

`PUT https://api.finsify.com/v2/login/activate`

or

`PUT https://api.finsify.com/v2/login/deactivate`

## Refresh login

Khi người dùng không được cập nhật giao dịch, `refresh login` sẽ hỗ trợ lấy lại giao dịch.

```shell
curl -X PUT \
  https://api.finsify.com/v2/login/refresh \
  -H 'client-id: sample-clientId' \
  -H 'content-type: application/json' \
  -H 'login-secret: sample-loginSecret' \
  -H 'service-secret: sample-ServiceSecret'
```

```javascript
var request = require("request");

var options = { method: 'PUT',
  url: 'https://api.finsify.com/v2/login/refresh',
  headers: 
   { 'login-secret': 'sample-loginSecret',
     'service-secret': 'sample-ServiceSecret',
     'client-id': 'sample-clientId',
     'content-type': 'application/json' },
  json: true };

request(options, function (error, response, body) {
  if (error) throw new Error(error);

  console.log(body);
});
```

> Response

```json
{
    "data": {
        "id": 51234,
        "refreshed": true,
        "last_refresh_at": "2017-08-13T09:19:08.073Z",
        "next_refresh_possible_at": "2017-08-13T09:24:08.073Z"
    },
    "version": "v2"
}
```

### HTTP Request

`PUT https://api.finsify.com/v2/login/refresh`