---
title: Finsify API Reference

language_tabs:
  - shell: cURL
  - javascript: NodeJS

toc_footers:
  - <a href='https://hub.finsify.com#get-in-touch'>Sign Up for a Developer Key</a>

includes:
  - api_guide
  - errors

search: true
---

# Giới thiệu

> ### API base

> Schema: `https`

> Host: `api.finsify.com`

> Base path: `/v2`

Welcome to the Finsify API! Our API allows you to easily access your Finsify Hub account, get information about your transaction history. In addition you may provide services for Finsify customers and access their accounts if certain conditions are met.

Our API is RESTful, we use JSON format.

## Định nghĩa

Finsify Hub có một số định nghĩa mà đối tác cần hiểu.

`Customer` Đây là định danh người dùng trên hệ thống của Finsify. Đối tác cần lưu trữ thông tin này, Finsify chỉ cấp một lần.

`Token` Là một mã duy nhất để thực hiện một kết nối với Finsify. Nó sử dụng khi khách hàng của đối tác cần liên kết với một dịch vụ mà Finsify cung cấp.

`Login` Có thể hiểu là một tài khoản đăng nhập của khách hàng trên hệ thống Finsify Hub.

`Account` Một `login` có thể có nhiều account, ví dụ như một tài khoản internet banking của ngân hàng, trong đó có thể chứa nhiều loại tài khoản ngân hàng, mỗi tài khoản trong đó lại có số dư và lịch sử giao dịch khác nhau.

`Transaction` Nó cung cấp các thông tin về giao dịch của mỗi `account.

`Service` Là các dịch vụ do Finsify Hub hỗ trợ, nó có thể là một dịch vụ thương mại điện tử, tổ chức tài chính. Ví dụ như Paypal, SeABank, VPBank...

# Authentication

Authenticate your account when using the API by including your secret API key in the request. You can manage your API keys in the Dashboard. Your API keys carry many privileges, so be sure to keep them secret! Do not share your secret API keys in publicly accessible areas such GitHub, client-side code, and so forth.

## HTTP Header

Để truy cập được vào hệ thống của Finsify trên Header của HTTP request luôn cần 2 header `Client-Id` và `Service-secret`(hoặc `App-secret`).

### Mô tả

Header | Required | Description
------ | ---- | -----------
Client-Id | yes | Finsify định danh `Developer` qua client-id này.
Service-secret | yes | Sử dụng cho các dịch vụ trên nền web.
App-secret | yes | Sử dụng cho các ứng dụng di động.

<aside class="notice">
Nếu developer sử dụng Finsify cho các ứng dụng di động thì sử dụng App-secret thay thế cho Service-secret và ngược lại. Không được sử dụng Service-secret và App-secret trong cùng 1 request.
</aside>

## Sample

Ví dụ một request lên hệ thống của Finsify.

> NodeJS code

```node

var request = require("request");

var options = { method: 'POST',
  url: 'https://api.finsify.com/v2/customer',
  headers:
   { 'service-secret': 'sample-service-secret',
     'client-id': 'sample-client-id',
     'content-type': 'application/json',
     accept: 'application/json' },
  body: { identifier: 'sample-authentication' },
  json: true };

request(options, function (error, response, body) {
  if (error) throw new Error(error);

  console.log(body);
});

```

> Php code

```php

$curl = curl_init();

curl_setopt_array($curl, array(
  CURLOPT_URL => "https://api.finsify.com/v2/customer",
  CURLOPT_RETURNTRANSFER => true,
  CURLOPT_ENCODING => "",
  CURLOPT_MAXREDIRS => 10,
  CURLOPT_TIMEOUT => 30,
  CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
  CURLOPT_CUSTOMREQUEST => "POST",
  CURLOPT_POSTFIELDS => "{\n    \"identifier\": \"sample-authentication\"\n}",
  CURLOPT_HTTPHEADER => array(
    "accept: application/json",
    "client-id: Tu5dvG07KVpx6b",
    "content-type: application/json",
    "service-secret: 75167f66-22dc-415d-b799-c0dd73b951ca"
  ),
));

$response = curl_exec($curl);
$err = curl_error($curl);

curl_close($curl);

if ($err) {
  echo "cURL Error #:" . $err;
} else {
  echo $response;
}
```

> Java OK HTTP

```java
OkHttpClient client = new OkHttpClient();

MediaType mediaType = MediaType.parse("application/json");
RequestBody body = RequestBody.create(mediaType, "{\n    \"identifier\": \"sample-authentication\"\n}");
Request request = new Request.Builder()
  .url("https://api.finsify.com/v2/customer")
  .post(body)
  .addHeader("accept", "application/json")
  .addHeader("content-type", "application/json")
  .addHeader("client-id", "sample-client-id")
  .addHeader("app-secret", "sample-app-secret")
  .build();

Response response = client.newCall(request).execute();

```

> Swift (NSURL)

```swift
import Foundation

let headers = [
  "accept": "application/json",
  "content-type": "application/json",
  "client-id": "sample-client-id",
  "app-secret": "sample-app-secret"
]
let parameters = ["identifier": "sample-authentication"] as [String : Any]

let postData = JSONSerialization.data(withJSONObject: parameters, options: [])

let request = NSMutableURLRequest(url: NSURL(string: "https://api.finsify.com/v2/customer")! as URL,
                                        cachePolicy: .useProtocolCachePolicy,
                                    timeoutInterval: 10.0)
request.httpMethod = "POST"
request.allHTTPHeaderFields = headers
request.httpBody = postData as Data

let session = URLSession.shared
let dataTask = session.dataTask(with: request as URLRequest, completionHandler: { (data, response, error) -> Void in
  if (error != nil) {
    print(error)
  } else {
    let httpResponse = response as? HTTPURLResponse
    print(httpResponse)
  }
})

dataTask.resume()
```

> Objective-C (NSURL)

```objective_c
#import <Foundation/Foundation.h>

NSDictionary *headers = @{ @"accept": @"application/json",
                           @"content-type": @"application/json",
                           @"client-id": @"sample-client-id",
                           @"app-secret": @"sample-app-secret" };
NSDictionary *parameters = @{ @"identifier": @"sample-authentication" };

NSData *postData = [NSJSONSerialization dataWithJSONObject:parameters options:0 error:nil];

NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:@"https://api.finsify.com/v2/customer"]
                                                       cachePolicy:NSURLRequestUseProtocolCachePolicy
                                                   timeoutInterval:10.0];
[request setHTTPMethod:@"POST"];
[request setAllHTTPHeaderFields:headers];
[request setHTTPBody:postData];

NSURLSession *session = [NSURLSession sharedSession];
NSURLSessionDataTask *dataTask = [session dataTaskWithRequest:request
                                            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                                                if (error) {
                                                    NSLog(@"%@", error);
                                                } else {
                                                    NSHTTPURLResponse *httpResponse = (NSHTTPURLResponse *) response;
                                                    NSLog(@"%@", httpResponse);
                                                }
                                            }];
[dataTask resume];
```

# Getting started

### Flow

1. Đăng ký một tài khoản nhà phát triển.
2. Tạo một khách hàng để Finsify có thể xác định ai đang sử dụng dịch vụ.
3. Chọn một dịch vụ Finsify cung cấp.
4. Yêu cầu tạo một token để có thể truy cập vào dịch vụ mà Finsify cung cấp.
5. Nhập thông tin đăng nhập của dịch vụ.
6. Chờ tiến trình xử lý, và nhận dữ liệu thông qua các API

## Dịch vụ

Danh sách dịch vụ mà Finsify Hub cung cấp có thể truy cập qua URL:

### HTTP Request

`GET https://assets.finsify.com/service.json`

> Cấu trúc của 1 service

```json
{

    "id": 67,
    "name": "AmBank",
    "provider": "Finsify",
    "media_path": "https://assets.finsify.com/services/",
    "cover_file_name": "ambank-cover.png",
    "logo_file_name": "ambank-logo.png",
    "color": {
        "primary": "ED1B24",
        "secondary": "FEF200"
    },
    "type": "bank",
    "country_code": "MY",
    "has_balance": true,
    "call_to_action": null
}
```

### Mô tả cấu trúc

Key | Type | Description
--- | ---- | -----------
id | integer | Mã dịch vụ
name | string | Tên của dịch vụ mà Finsify Hub cung cấp
provider | string | Tên đối tác Finsify Hub hợp tác. `Finsify` là do chính Finsify Hub triển khai.
media_path | string | Đường dẫn tới các file ảnh của dịch vụ.
cover_file_name | string | tên file cover. Một cover luôn có 3 size là `small`, `medium` và `large`.
logo_file_name | string | tên file logo
color | object | mã màu của dịch vụ.
type | string | loại dịch vụ mà Finsify Hub cung cấp. Hiện tại Finsify hỗ trợ các loại là `bank` và `other`
country_code | string | đây là mã quốc gia của dịch vụ
has_balance | boolean | dịch vụ này có sẵn số dư hay không
call_to_action | object | nếu không có call to action thì value là `NULL`

> Ví dụ một call to action

```json
"call_to_action": {
    "prompt": "Chưa có tài khoản?",
    "text": "Đăng ký ngay!",
    "url": "https://example.com/register-account"
}
```

### Lưu ý về `media_path`

Để có được 1 url hoàn chỉnh của ảnh cover hoặc logo thì cần ghép chuỗi `media_path` với `cover_file_name` hoặc `logo_file_name`.

Với `cover_file_name` để có một URL hợp lệ sẽ bao gồm cả kích thước. Ví dụ:

`https://assets.finsify.com/services/ambank-cover-medium.png`

`https://assets.finsify.com/services/westpac_bank-cover-large.jpg`