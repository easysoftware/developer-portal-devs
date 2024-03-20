# Easy GraphQL

We are using standard `graphql` ruby gem for our GraphQL implementation. It is a Ruby implementation of Facebook's GraphQL. https://graphql-ruby.org/getting_started

Implementation in Easy trying follow best practices and standards. We are using `graphql` gem for schema definition and `graphiql-rails` for development and testing.

We are using own schema `EasyGraphql` for all our GraphQL queries and mutations. This schema is defined in `app/api/easy_graphql/app_schema.rb` directory in `easy_extensions` engines.

Lets introduce some our basics:

## Types
https://graphql-ruby.org/guides#type-definitions-guides

Our types are in folder `app/api/easy_graphql/types`. All types are inherited from `Types::BaseObject`. We are using `Types::Base` for all our types. This class is defined in `app/api/easy_graphql/types/base.rb`.

Types are used for defining the structure of the data that can be queried. We are using `field` method for defining fields of the type. We are using `argument` method for defining arguments of the type.

> [!IMPORTANT]
> Every new type should inherit from `Base` !

### Inputs (arguments)
https://graphql-ruby.org/type_definitions/input_objects.html

Inputs are subset of types. They are used for defining arguments of the type. We are using `argument` method for defining arguments of the type. In Easy we are using `Types::BaseInput` for all our inputs. This class is defined in `app/api/easy_graphql/types/base_input.rb`. Input represents a hash of *entity* attributes, primarily for `Mutations`.

Please inputs into `app/api/easy_graphql/types/inputs` directory. Name convention of input is `model_name_attributes.rb`.

> [!WARNING]
> `_attrubtes` suffix is used to distinguish input from type.

## Resolvers
https://graphql-ruby.org/fields/resolvers.html

Resolvers are used for defining how to fetch the data. We are using `argument` method for defining params of the type. We are using `resolve` method for defining how to fetch the data.

We are using `Resolvers::Base` for all our resolvers. This class is defined in `app/api/easy_graphql/resolvers/base.rb`.

Place resolvers into `app/api/easy_graphql/resolvers` directory. Name convention of resolver should describe "what resolve does" OR if its fetch entity, just use entity name in plural. For example `Issues` or `MarkAsRead`.

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

We are using `Mutations::Base` for all our mutations. This class is defined in `app/api/easy_graphql/mutations/base.rb`.

Place mutations into `app/api/easy_graphql/mutations` directory. Name convention of mutation should describe "what mutation does" OR if its change entity, just use entity name **with action suffix**. For example `IssueCreate` or `IssueUpdate`.

> [!CAUTION]
> NEVER ever do more actions in one mutations.
> 
> Its forbidden to combine `create` and `update` in one mutation ! Always use separate mutations for each logical action. 

### Error handling
https://graphql-ruby.org/mutations/mutation_classes.html#error-handling

We are using `Types::Errors` for all our errors. This class is defined in `app/api/easy_graphql/types/error.rb`. This class is used for defining errors of the mutation. Define `errors` on your mutation as new field:

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

1. **Use `resolve` method for changing data.**
2. **Use `field` errors aside your returns.**
3. **Always return `errors` field.** If there is no error, return empty array.
4. **Use `argument` method for defining fields of the type.** Do not forget specify `required` option for argument.
5. If you find specify entity, use `find` method for fetching entity. It raise `ActiveRecord::RecordNotFound` if entity not found = This is correct.
6. Do not forget `init_journal` if entity is journalized.
7. Use `safe_attributes` for assign attributes to entity.
8. If you create new entity and its not valid, return `errors` and `nil` for entity.
