---
title: Finsify API Guidelines

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

## Definantion

Please read the terms of Finsify Hub's documents 

`Customer` identify a user on Finsify system. Partners need to store this information because it is only provided once.

`Token` is one code to make the connection with Finsify. It is used when partner needs to connect with a service provided by Finsify.

`Login` could be known as an account of customer which was logged in on Finsify Hub system. 

`Account` One `login` could include several accounts. For example, one internet bank account could be accessed to different accounts such as current account, saving account, credit card which have different balance and transaction history.

`Transaction` it provides information of each transaction from each `account`.

`Service` is service provided by Finsify Hub. It could be an ecommerce website, financial institution such as Paypal, Vietcombank, Maybank, etc.

# Authentication

Authenticate your account when using the API by including your secret API key in the request. You can manage your API keys in the Dashboard. Your API keys carry many privileges, so be sure to keep them secret! Do not share your secret API keys in publicly accessible areas such GitHub, client-side code, and so forth.

## HTTP Header

To access to Finsify system, Header of HTTP request must contain 2 headers `Client-Id` and`Service-secret`(or `App-secret`).

### Mô tả

Header | Required | Description
------ | ---- | -----------
Client-Id | yes | Finsify identifies `partner` by client-id .
Service-secret | yes | Use for web-based service.
App-secret | yes | Use for mobile applications.

<aside class="notice">
In case developer uses Finsify for mobile application, please use App-secret instead of Service-secret. Notice that you can not use Service-secret and App-secret simultaneously in one request.
</aside>

## Sample

An example of a request to Finsify system:

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

1. Register for an Developer account 
2. Create a new customer to let Finsify know who is using service
3. Select a service 
4. Request to create a token to access to services provided by Finsify 
5. Enter login information of selected service 
6. Wait for process to finish and receive data via APIs

## Services

You can access to the list of service provided by Finsify.

### HTTP Request

`GET https://assets.finsify.com/service.json`

> Structure of a service

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

### Structure description 

Key | Type | Description
--- | ---- | -----------
id | integer | Service ID
name | string | Name of service provided by Finsify  Hub
provider | string | Name of Finsify's partner. `finsify` is deploy by Finsify Hub.
media_path | string | URL to link of service's images.
cover_file_name | string | Cover file name. A cover always has 3 sizes: `small`, `medium` và `large`.
logo_file_name | string | Logo file's name
color | object | Colour code of service.
type | string | The type of services provided by Finsify Hub. Finsify currently supports `bank`, `statement` and `other`.
country_code | string | Service's country code. 
has_balance | boolean | Does service have available balance?
call_to_action | object | If no `call_to_action`, value is `NULL`

> Example of Call to action 

```json
"call_to_action": {
    "prompt": "No account yet?",
    "text": "Register now!",
    "url": "https://example.com/register-account"
}
```

### Note about `media_path`

To have a complete url of cover or logo image, it should be merged string `media-path` with `cover_file_name` or `logo_file_name`. 

In order to have a valid URL, `cover_file_name` must contain size. Example: 

`https://assets.finsify.com/services/ambank-cover-medium.png`

`https://assets.finsify.com/services/westpac_bank-cover-large.jpg`
