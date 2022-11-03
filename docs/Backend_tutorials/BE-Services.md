# The Service object pattern

*We love organized code and so this article is about how and when we use Services.*

We are following https://dev.to/joker666/ruby-on-rails-pattern-service-objects-b19 with some changes specific to our codebase.

## What is a Service
> it executes a single action in your domain logic and does it well

Instead of large blocks of code in controllers / models, which do something, you can create a Service class for that. 

Service action is executed by calling the `.call` method which is inherited from `EasyExtensions::BaseService` where the `EasyExtensions::Callable` module is included.

## Naming

The name of the service class should have the form of a plain command like `CreateUser` or `RegisterAccount`.

Classes must be placed in `services/` folder. No "Service" suffix is needed -> ~~`CreateUserService` or `RegisterAccountService`~~.

## Return value

The `.call` method should return one of the following:
* Boolean `true`/`false`
* `Object` or `self`

The result depends on specific use case. For example, if a service is creating something, or retrieving some data, it should return an Object.
