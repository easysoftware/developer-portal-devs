# The service object pattern

*We loved organized code so this article is about how and when we use services.*

We following https://dev.to/joker666/ruby-on-rails-pattern-service-objects-b19 basically. But we agreed on some follow roles and specifics.

## What is service
> execute one single action in your domain logic and do it well

Instead of large blocks of code in controller / model which do something, create new service for that. 

Service action is called by `.call` method and its inherited from `EasyExtensions::BaseService` or `EasyExtensions::Callable` module is prepended.

## Naming

Name of service class should be plain command like `CreateUser` or `RegisterAccount`.

Classes must be placed in `services` folder. No "Service" tail is needed. => So nothing like ~~`CreateUserService` or `RegisterAccountService`~~.

## Return object

`.call` method of service can return:
* Boolean `true`/`false`
* `self`
* or Object

We have no preference and result depends of use case. If service creating something, or retrieving some data it should return result object.

