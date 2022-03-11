# GraphQL reference

*In this section we will look into how to successfully work with Easy Software GraphQL API. We will cover the basics such as authentication, accessible environments and other necessary topics.*

---

## GraphQL documentation and schema builder
For your convenience we have prepared Easy Project GraphQL interface, where you can query your custom built queries that you can predefine using a visual editor. It is called GraphiQL and you can find it sitting at:

```http
// EasyProject 
https://{your-domain}.easyproject.com/grapihql

// EasyRedMine
https://{your-domain}.easyredmine.com/grapihql
```

> GraphiQL is a powerful tool to help you browse schema and write and test your queries.

This interface provides you with full capabilities for introspection so you can browse the complete schema, its type definition and possible ways to reach data you need. In order to fully take advantage of it, we will go through the elements it provides.

In its default state there are two columns - the top part of the left one is for typing in your query, the bottom left is for passing over variables for your queries. When you hit send button, the right column will populate with query results.

This interface has one more very important feature which is schema documentation. It is accessible after hitting the Docs button in the top right corner. From here you can browse schema queries (types that you can access in reading mode), mutations (types that you use in order to pass and change data on the server) and subscriptions which is a GraphQL way of notifying the client without the need of the client to trigger any action.

Schema documentation will provide you with types you can query and their definitions - other types or attributes you can query.

---

## Authentication
For authentication you will use `apiKey`.
You can obtain your apiKey in Easy Software or Easy Redmine Account settings.
[api-key]
Pass over this api key as a header for every API call you do as follows:
`apiKey` = `{your-api-key}`

---

## Accessible environments
Access the API through the following URL for cloud solution. It depends on what environment you are currently running. For example: 

```http
// EasyProject 
https://{your-domain}.easyproject.com/graphql

// EasyRedMine
https://{your-domain}.easyredmine.com/graphql
```

In case you run an on-premise version then the URL will correspond with its URL or IP address that can be seen from the Internet.

---

## When to use REST and GraphQL
Since Easy Software offers both, REST API and GraphQL, it is important to mention why that is.
REST API is used for back end to back end communication, so for the cases when some data needs to get from you system to Easy Software and vice versa.
Contrary to that, GraphQL is used **only** for communication between the front end and back end of Easy Software and thus can be used if you wish to implement your own plugin for it.

---

## Rate limits
There are no limits for how many requests you can send via GraphQL calls.

---


## GraphQL status and error codes
Easy Software API will always provide you with HTTP status codes that can tell you more about the response.

- `401` - `UNAUTHORIZED` - provided credentials were incorrect
- `403` - `FORBIDDEN` - you do not have the access rights for the resource
- `404` - `NOT FOUND` - the specified resource was not found
- `406` - `NOT ACCEPTABLE` - provided data do not match format acceptable by the server for the request
- `422` - `UNPROCESSABLE ENTITY` - request could not be processed even though it's syntactically correct
- `429` - `RATE LIMIT EXCEEDED` - were sent too many requests