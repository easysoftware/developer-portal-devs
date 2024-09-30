# Easy GraphQL

We are using standard `graphql` ruby gem for our GraphQL implementation. It is a Ruby implementation of Facebook's GraphQL. https://graphql-ruby.org/getting_started

Implementation in Easy trying follow best practices and standards. We are using `graphql` gem for schema definition and `graphiql-rails` for development and testing.

We are using own schema `EasyGraphql` for all our GraphQL queries and mutations. This schema is defined in `easy_extensions/app/api/easy_graphql/app_schema.rb` directory in `easy_extensions` engines.

Lets introduce some our basics:

## Types
https://graphql-ruby.org/guides#type-definitions-guides

Our types are in folder `*/app/api/easy_graphql/types`. All types are inherited from `Types::BaseObject`. We are using `Types::Base` for all our types. This class is defined in `easy_extensions/app/api/easy_graphql/types/base.rb`.

Types are used for defining the structure of the data that can be queried. We are using `field` method for defining fields of the type. We are using `argument` method for defining arguments of the type.

> [!IMPORTANT]
> Every new type should inherit from `Base` !

### ID vs. Int vs. Integer
https://graphql-ruby.org/type_definitions/scalars.html

We are using `ID` for all our primary keys and relations. We are using `Integer` for all our integer values.

Do not use `Int` type, we are prefer `Integer` type instead for "integer" values.

### Enums
https://graphql-ruby.org/type_definitions/enums.html

Enums are used for defining a type with a limited set of possible values. We are using `enum` method for defining enums. We are using `Types::BaseEnum` for all our enums. This class is defined in `easy_extensions/app/api/easy_graphql/types/base_enum.rb`.

Enum represent one field => so use singular name for enum. For example `IssueStatus` or `IssueCategory`.

### Inputs (arguments)
https://graphql-ruby.org/type_definitions/input_objects.html

Inputs are subset of types. They are used for defining arguments of the type. We are using `argument` method for defining arguments of the type. In Easy we are using `Types::BaseInput` for all our inputs. This class is defined in `easy_extensions/app/api/easy_graphql/types/base_input.rb`. Input represents a hash of *entity* attributes, primarily for `Mutations`.

Place inputs into `*/app/api/easy_graphql/types/inputs` directory. Name convention of input is `model_name_attributes.rb`.

> [!WARNING]
> `_attrubtes` suffix is used to distinguish input from type.

### Best-practice
1. **Use `field` method for defining fields of the type.**
2. **Use `argument` method for defining fields of the type.** Do not forget specify `required` option for argument.
3. **Use `enum` method for defining enums.**
4. **Use `Types::Base` for all our types.**
5. **Use `Types::BaseEnum` for all our enums.**
6. **Use `ID` for all our primary keys and relations.**
7. **Use `Integer` for all our integer values.** Do not use `Int` shortcut.

## Resolvers
https://graphql-ruby.org/fields/resolvers.html

Resolvers are used for defining how to fetch the data. We are using `argument` method for defining params of the type. We are using `resolve` method for defining how to fetch the data.

We are using `Resolvers::Base` for all our resolvers. This class is defined in `easy_extensions/app/api/easy_graphql/resolvers/base.rb`.

Place resolvers into `*/app/api/easy_graphql/resolvers` directory. Name convention of resolver should describe "what resolve does" OR if its fetch entity, just use entity name in plural. For example `Issues` or `MarkAsRead`.

### Best-practice

1. **Use `resolve` method for fetching data.**
2. **Use `type` method for defining return type.**
3. **Use `argument` method for defining fields of the type.** Do not forget specify `required` option for argument.
4. If you find specify entity, use `find` method for fetching entity. It raise `ActiveRecord::RecordNotFound` if entity not found = This is correct.
5. Do not forget to check permissions for visibility.
6. Prefer use `arguments` method for accessing arguments.

#### Example

```ruby title="example usage of resolver" lineNumbers
module EasyGraphql
  module Resolvers
    class IssueDetail < Resolvers::Base
      description "Return Issue object based on given ID."

      type Types::Issue, null: false

      argument :id, ID, required: true

      def resolve(**_args)
        { issue: }
      end

      private
      
      def issue
        @issue ||= ::Issue.preload(journals: :details).visible.find(arguments[:id])
      end

    end
  end
end
```

## Mutations
https://graphql-ruby.org/mutations/mutation_classes.html

Mutations are used for defining how to change the data. We are using `argument` method for defining arguments of the type. We are using `resolve` method for defining how to change the data. We are using `field` method for defining fields of the type (return data of mutation).

We are using `Mutations::Base` for all our mutations. This class is defined in `easy_extensions/app/api/easy_graphql/mutations/base.rb`.

Place mutations into `*/app/api/easy_graphql/mutations` directory. Name convention of mutation should describe "what mutation does" OR if its change entity, just use entity name **with action prefix**. For example `CreateIssue` or `UpdateIssue` or `DestroyIssue`.

> [!CAUTION]
> NEVER ever do more actions in one mutations.
> 
> Its forbidden to combine `create` and `update` in one mutation ! Always use separate mutations for each logical action. 

### Error handling
https://graphql-ruby.org/mutations/mutation_classes.html#error-handling

We are using `Types::Errors` for all our errors. This class is defined in `easy_extensions/app/api/easy_graphql/types/error.rb`. This class is used for defining errors of the mutation. Define `errors` on your mutation as new field:

```ruby
field :errors, [Types::Error], null: true
```

> [!WARNING]
> `_errors` must be always array of `Types::Error` !

In mutation there is method `error` which return `Errors` object for store validation errors. (see easy_engines/easy_extensions/app/api/easy_graphql/mutations/errors.rb)

Object have public methods: 

```ruby title="easy_engines/easy_extensions/app/api/easy_graphql/mutations/errors.rb" lineNumbers
# Add an error to the errors array.
# @param attribute [Symbol, String] attribute associated with the error
# @param messages [Array<String>] error messages
def add_error(attribute:, messages:)
  errors << { attribute:, messages: }
end

# Add errors from an entity to the errors array.
# @param entity [ActiveRecord::Base] entity to add errors from
def add_entity_errors(entity)
  entity_errors = EasyExtensions::ParseActiveRecordErrors.call(entity)
  errors.concat(entity_errors)
end
```

So when you are updating standard ActiveRecord object in mutation, use 
```ruby title="Example of resolve method with validation"
entity.init_journal(User.current, "My changes")
errors.add_entity_errors(entity) unless entity.save

{ entity:, errors: errors }
```

### Best-practice

1. **Use `prefix` in mutation name for action.** (like `Create`, `Update`, `Destroy`)
2. **Use `resolve` method for changing data.**
3. **Use `field` errors aside your returns.**
4. **Always return `errors` field.** If there is no error, return empty array.
5. **Use `argument` method for defining fields of the type.** Do not forget specify `required` option for argument.
6. If you find specify entity, use `find` method for fetching entity. It raise `ActiveRecord::RecordNotFound` if entity not found = This is correct.
7. **Always authorize manipulation with object** If actor has not permission raise `Unauthorized` exception.
8. Do not forget `init_journal` if entity is journalized.
9. Use `safe_attributes` for assign attributes to entity.
10. If you create new entity and it's not valid, return `errors` and `nil` for entity.

### Mutation Example

```ruby title="example of mutation" lineNumbers
module EasyGraphql
  module Mutations
    # Please replace "entity" for specific object,
    # to avoid usage of mutation for multiple entities.
    class UpdateEntity < Mutations::Base
      description "Update an Entity."

      argument :id, ID, required: true
      argument :attributes, Types::Inputs::EntityAttributes, required: true

      field :entity, Types::Entity, null: false
      field :errors, [Types::Error]

      def resolve(**_args)
        raise Unauthorized unless entity.editable?

        entity.init_journal(User.current)
        entity.safe_attributes = arguments[:attributes].to_hash

        unless entity.save
          errors.add_entity_errors(entity)
        end

        { entity:, errors: }
      end

      private

      def entity
        @entity ||= Entity.visible.find(arguments[:id])
      end

    end
  end
end
```

### Delete Mutations
Delete mutations are special case of mutations. Their response slightly differs from other mutations. Best practice often suggest that, 
this kind of mutation should return deleted object, or at least its ID. 

In Easy we are using graphql to support our front-end solutions, instead of official API. So we are trying to keep mutations response as simple as possible, to minimize possibility of failure. 
If front end does not need the object or its ID we just return errors to inform front-end about the result of the operation.

Error handling is also slightly different. While action in other mutations should check validation errors, in **ActiveRecord destroy method will success even with invalid object**.
Failed destroy will raise `ActiveRecord::RecordNotDestroyed` exception. This exception must be handled and returned like validation error to front-end.

#### Additional Best-practice

1. **Return object or its ID only if necessary.**
2. **To indicate success or failure use `errors` field.**
3. **Destroy method doesn't rely on object validity.** Invalid object will be destroyed as well.
4. **Use `ActiveRecord::RecordNotDestroyed` exception for error handling.** Return exception message as error message of attribute `:base`.

#### Delete Mutation Example

```ruby title="example of delete mutation" lineNumbers
module EasyGraphql
  module Mutations
    # Please replace "entity" for specific object,
    # to avoid usage of mutation for multiple entities.
    class DeleteEntity < Mutations::Base
      description "Destroys entity."

      argument :id, ID, required: true

      field :errors, [Types::Error], null: true

      def resolve(**_args)
        raise Unauthorized unless entity.deletable?

        entity.destroy
        { errors: }
      rescue ActiveRecord::RecordNotDestroyed => e
        errors.add_error(attribute: :base, messages: [e.message])
        { errors: }
      end

      private

      def entity
        @entity ||= Entity.visible.find(arguments[:id])
      end

    end
  end
end
```

## Subscriptions
https://graphql-ruby.org/subscriptions/subscription_classes.html

Subscriptions could be considered as specific type of Resolvers. They are supposed to fetch data resolver-like, 
but the main difference is in the way of fetching the data. 

Resolvers are used to fetch data on demand by client side of application. Subscriptions on the other hand, are just initialized by client side of application as channel that provides information when data change. 
Data fetching part is triggered on server side, when specific event occurs (trigger is called).

We are using `Subscriptions::Base` to inherit for all our subscriptions. This class is defined in `easy_extensions/app/api/easy_graphql/subscriptions/base.rb`.

Place subscriptions into `*/app/api/easy_graphql/subscriptions` directory. Name convention of subscription should describe triggering event and associated object in pattern: `object_event`, e.g.: IssueUpdated.

Subscription classes do not use own implementation of `resolve` method, but `subscribe` and `update` methods instead. Method `resolve` is implemented within `graphql` gem and its role is to decide what method should be called, `subscribe` or `update`.

Subscription use `arguments` to define inputs and these arguments are necessary to determine which subscription(s) should be updated when trigger is called.

Thanks to similarity with resolvers, we can use either `type` method to define output type, or we can use `field` as well to define fields of subscriptions.

### Subscribe
Method `subscribe` is executed when client side of application initializes subscription. Basically, it is executed only for the first time we touch the subscription. 
By default this method does **no response** and just establishes the channel. It can be overriden to provide some initial data for client side of application.

### Update
Method `update` is executed when server side of application triggers the subscription. It is executed every time the trigger is called and fires data to the client side. 
By default this method returns **object** passed to `trigger` method. It can be overriden as well to execute custom logic.

```ruby title="Default update method"
def update(args = {})
  object
end
```

### Trigger
https://graphql-ruby.org/subscriptions/triggers

Trigger is the way we notify client side of application about expected event has occurred. Trigger method has three mandatory arguments: 
`event_name`, `object` and `arguments` and two optional arguments: `scope` and `context`. 

Combination of `event_name` and `arguments` is used to determine which subscription(s) should be updated.
To determine correct subscription(s) with `event_name`, we have to pass exactly the same `arguments` as were used when subscribe was executed. In case of multiple arguments, all of them have to match.

Argument `object` is passed to `update` method of subscription and by default it is fired to client side. If we stick to the default implementation of `update` method, `object` have to match output type of subscription.

```ruby title="Example of subscription trigger"
EasyGraphql::AppSchema.subscriptions.trigger(event_name, argumnents, object, scope:, context:)
```

### Best-practice

1. **Use `type` for definition of subscription response if subscription returns ActiveRecord object**
2. **Use `subscribe` method to define initial subscription response.**
3. **Try to avoid overriding `update` method and pass data through `object` method.**
4. **Use `arguments` to define inputs for subscriptions.** To determine correct subscription all arguments have to match first call. Do not forget specify `required` option for argument where needed.
5. **Subscription should fetch data, not change them.**
6. **Use `find` method for fetching entity.** It raise `ActiveRecord::RecordNotFound` if entity not found = This is correct.
7. **Avoid `field` errors if possible.**

```ruby title="example default update aubscription and their trigger" lineNumbers
module EasyGraphql
  module Subscriptions
    class IssueUpdated < Subscriptions::Base
      description "Return updated Issue object based on given ID."

      type Types::Issue, null: false

      argument :id, ID, required: true

      def subscribe(**_args)
        issue
      end

      private
      
      def issue
        @issue ||= ::Issue.preload(journals: :details).visible.find(arguments[:id])
      end

    end
  end
end

# Trigger
EasyGraphql::AppSchema.subscriptions.trigger("issue_updated", { id: 1 }, issue)
```

```ruby title="example custom update aubscription and their trigger" lineNumbers
module EasyGraphql
  module Subscriptions
    class IssueUpdated < Subscriptions::Base
      description "Return updated Issue object based on given ID."

      field :issue, Types::Issue, null: false
      field :issue_editable, Boolean, null: false

      argument :id, ID, required: true

      def subscribe(**_args)
        issue
      end

      # if we need to prepare some data after trigger was called, we can override update method.
      # def update(**_args)
      #   # CUSTOM LOGIC
      # end

      private
      
      def issue
        @issue ||= ::Issue.preload(journals: :details).visible.find(arguments[:id])
      end

    end
  end
end

# Trigger
response = { issue:, issue_editable: issue.editable? }
EasyGraphql::AppSchema.subscriptions.trigger("issue_updated", { id: 1 }, response)
```
