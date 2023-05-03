# REST-like API

*In this section we will look into how to work with Easy Software "REST-like" API. We will cover the basics
such as authentication, accessible environments and other key topics.*

*You can access the API reference in the section below.*

---

## Authentication

For authentication you will use API key - `key`.
You can obtain your apiKey in Easy Software or Easy Redmine Account settings.
`[api-key]`
For API, you can decide whether you want to pass it over either as a header parameter or as a query parameter. There is
no difference between these cases so it is up to you which one you select:
`[header]`
or
`[query]`.

---

## Accessible environments

Access the API through the following URL for cloud solution:

```http
Easy Project
https://{your-domain}.easyproject.com/{endpoint}

Easy Redmine
https://{your-domain}.easyredmine.com/{endpoint}
```

In case you run an on-premise version then the URL will correspond with its URL or IP address that can be seen from the
Internet.

---

## When to use "REST-like API" and GraphQL

Since Easy Software offers both, HTTP API (json, xml) and GraphQL, it is important to mention why that is.
Regular API is used for back end to back end communication, so for the cases when some data needs to get from you system
to Easy Software and vice versa.
Contrary to that, GraphQL is used for communication between the front end and back end of EP/ER and thus can be used if
you wish to implement your own plugin for it.
---

## Rate limits

There is a limit of requests you can send to the cloud version of Easy Software. This limit is set to `10` requests per
minute which when reached triggers 60 second cooldown during which you will
receive `HTTP status code 429 - Rate limit exceeded`.

---

## Request and response formats

EP/ER API supports sending and receiving messages in JSON and XML format. The format you want to provide and consume has
to specified for every call as a path parameter just like this:

JSON → `https://dev-portal.dev5.easysoftware.com/easy_contacts.json`
or XML → `https://dev-portal.dev5.easysoftware.com/easy_contacts.xml`

Keep in mind that depending on the format you chose you have to send the request body in the same format (applicable for
POST and PUT request).

---

## Status and error codes

Easy Software API will always provide you with HTTP status codes that can tell you more about the response.

- `401` - `UNAUTHORIZED` - provided credentials were incorrect
- `403` - `FORBIDDEN` - you do not have the access rights for the resource
- `404` - `NOT FOUND` - the specified resource was not found
- `406` - `NOT ACCEPTABLE` - provided data do not match format acceptable by the server for the request
- `422` - `UNPROCESSABLE ENTITY` - request could not be processed even though it's syntactically correct
- `429` - `RATE LIMIT EXCEEDED` - were sent too many requests
