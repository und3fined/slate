# Errors

The Finsify API uses the following error codes:



Status Code | Error | Description
----------- | ----- | -----------
400 | BadRequest | Invalid Request
404 | AccountNotFound | Account is not found - Your account request not found in server
404 | ClientNotFound | Can't find client with your id
503 | ConnectError | We're temporarily offline. Please try again later.
400 | ContentInputInvalid | Only allow data type application/json
400 | CredentialInvalid | Credentials is invalid
500 | CustomerCreateError | Can't create new customer. Please try later.
400 | CustomerExists | Your customer is exists
404 | CustomerNotFound | Can't find your customer.
400 | DataInvalid | Data is invalid 
500 | InternalError | Sorry server is crash. Please try again.
404 | LoginNotFound | Login is not found - Can't find your login in server
503 | ServerMaintenance | Servers are currently undergoing maintenance. Please try later
404 | ServiceNotFound | Service is not found - Your service request is not define
500 | TokenCreateError | Can't create a token. Please try again.
400 | TokenExpire | Your token is expired. Please create new token.


`Status Code` is status in HTTP request.