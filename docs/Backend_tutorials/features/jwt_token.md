# What is a JWT Token
A JWT (JSON Web Token) is a compact, URL-safe token format used for securely transmitting information between parties as a JSON object. 
It is commonly used for authentication and authorization purposes. The token is signed using a secret or public/private key to prevent tampering, 
allowing the server to trust the data it contains without needing to store session state. Each token has expiration time and becomes invalid after time passes.

# Easy JWT Module
Easy JWT is a simple module that allows you to transform a payload into a JWT token and vice versa. 
This is achieved by `encode` and `decode` methods. Module also defines method `secret` that returns a secret key used for encoding and decoding.
This key is stored in the `EASY_JWT_SECRET` environment variable to prevent hardcoding this information into code, 
while also achieve sharing it among other services within docker environment. This variable is mandatory for the module to work properly.
If the variable is not set, the module will raise `EasyExtensions::JWT::SecretNotSetError`.

Method `encode` accepts two arguments - `payload` (hash) and `expiration_time` (integer) and transform payload into JWT token. 
**Default expiration time is 15 minutes.**

```ruby title="usage of EasyExtensions::JWT.encode"
# with default expiration time
payload = { key: "value" }
token = EasyExtensions::JWT.encode(payload)

# with custom expiration time
payload = { key: "value" }
expiration_time = 5.minutes.from_now.to_i
token = EasyExtensions::JWT.encode(payload, expiration_time:)
```

Method `decode` accepts one argument - `token` (string). It decodes token and returns payload. In case of invalid token, it raises `JWT::DecodeError`.

```ruby title="usage of EasyExtensions::JWT.decode"
# with default expiration time
token = "token_generated_by_my_secret"
payload_as_hash = EasyExtensions::JWT.decode(token)
```

# User authentication with JWT token
EasyExtensions::JWT::GenerateAuthToken is service that use Easy JWT module to generate user JWT token. Input for this service is instance of user. By current standards `User.current` is taken by default. 
On call service prepares payload for JWT token and requests for encode. This token allows user to pass through authentication process without api key or other credentials. 

```ruby title="usage of EasyExtensions::JWT::GenerateAuthToken"
# token for specific user
my_user = User.find("id")
token = EasyExtensions::JWT::GenerateAuthToken.call(user: my_user)

# token for current user
token = EasyExtensions::JWT::GenerateAuthToken.call
```

# Usage of user JWT token
Generated token can be used in headers of request. To achieve authentication, it is necessary to use it in `Authorization` header with `Bearer` prefix.

```ruby title="usage of user JWT token"
token = EasyExtensions::JWT::GenerateAuthToken.call
request_headers = {
  "Content-Type" => "application/json",
  "Authorization" => "Bearer #{token}",
}
```

It is mandatory to define `accept_api_auth` for selected endpoints in controllers to enable authentication by JWT token. No further implementation is required.
Retrieving user from token is already implemented within `application_controller_patch.rb` method `find_current_user` in `easy_extensions` engine.
