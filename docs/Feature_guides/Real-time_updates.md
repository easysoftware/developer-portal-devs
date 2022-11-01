## Introduction

This guide goes over the implementation of "real-time updates" using GraphQL subscriptions.

What it means is that when a client(browser) goes to a page with a **subscription** it get subscribed to it using a **websocket**. Javascript is then listening to any message coming through this **subscription** and can change the page content based on it.

We take our implementation of product backlog board as an example, which, to simplify, displays multiple columns of ordered cards.\
Let's say multiple users are looking at the same board, moving cards around. Their "update" requests are sent to the server, as mutations if using graphql. If they're **successful** they trigger subscriptions and the changes are **broadcast** to every subscriber. That way everyone sees the same state of cards without needing to refresh the page. That's what we call "real-time updates".

The guide is divided into front-end(FE) and back-end(BE) sections and describes requirements, implementation and recommended _PATTERNS_ used.

## Requirements

### Front-end

- `apollo-client`
- `apollo-link-http`
- `graphql-ruby-client/subscriptions/ActionCableLink`

Main configuration is done in apollo client - `app/frontend/src/shared/plugins/apolloClient.js`

### Back-end

- `gem 'graphql'`
- working websockets (one is enough) -> GraphQL subscriptions use [ActionCable](https://guides.rubyonrails.org/action_cable_overview.html) internally so `cable.yml` needs to be configured

## Implementation

_PATTERN:_ Use multiple context-oriented subscriptions instead of a single big one. They're easier to manage and customize avoiding `if` conditions.\
In our backlog board example we use 5 different subscriptions:

- `easyProductBacklogBoard` (all actions not covered by the other ones)
- `easyProductBacklogBoardInbox` (actions in inbox)
- `pbiSplitter` (split action)
- `easyProductBacklogBoardMovePbi` (reordering action)
- `movePbiIntoSprintBoardSubscription` (moving into sprint board action)

Further code examples will focus on `easyProductBacklogBoardMovePbi` subscription which handles actions that change positions of entities, product backlog items(PBIs) in this case.

### Front-end

General guide for implementing subscriptions via apollo-client - https://www.apollographql.com/docs/kotlin/tutorial/11-subscriptions
and https://graphql-ruby.org/javascript_client/apollo_subscriptions.html.

Subscriptions are defined in a single `subscriptions.ts` file and the graphql query is customized for each subscription.

```ts
// app/frontend/src/easy_agile/src/api/subscriptions.ts
export const useProductBacklogBoardMovePbi = (easyProductBacklogBoardId: string) => {
  return useSubscription(
    gql`
      subscription easyProductBacklogBoardMovePbi($easyProductBacklogBoardId: ID!) {
        easyProductBacklogBoardMovePbi(easyProductBacklogBoardId: $easyProductBacklogBoardId) {
          result {
            mutationName
            pbi {
              id
              name
              color
              icon
              status
              storyPoints
              parent {
                id
                name
                status
              }
              children: activeChildren {
                id
                name
                color
                icon
                status
                storyPoints
                parent {
                  id
                }
                prevId
              }
              issues {
                id
                subject
              }
              prevId
            }
            pbiFollower {
              id
              status
              prevId
            }
            oldPbiFollower {
              id
              status
              prevId
            }
          }
        }
      }
    `,
    {
      easyProductBacklogBoardId,
    }
  );
};
```

### Back-end

##### Define the subscription class

```ruby
# plugins/easyproject/easy_plugins/easy_agile/api/easy_graphql/subscriptions/easy_product_backlog_board_move_pbi.rb
module EasyGraphql
  module Subscriptions
    class EasyProductBacklogBoardMovePbi < ::EasyGraphql::Subscriptions::EasyProductBacklogBoard

      description 'Subscription for receiving updates about PBI position/status in EasyProductBacklogBoard'

      field :result, Subscriptions::Results::MovePbiResult, null: true

    end
  end
end
```

##### Define subscription result type

_PATTERN:_ Create a graphql type to be sent as the **result** to the websocket instead of naming individual fields in the subscriptions class. It's simpler to extend and can be reused using inheritance for example.

```ruby
# plugins/easyproject/easy_plugins/easy_agile/api/easy_graphql/subscriptions/results/move_pbi_result.rb
module EasyGraphql
  module Subscriptions
    module Results
      class MovePbiResult < Types::Base
        field :pbi, Types::EasyProductBacklogItem, null: true
        field :pbi_follower, Types::EasyProductBacklogItem, null: true
        field :old_pbi_follower, Types::EasyProductBacklogItem, null: true
        field :mutation_name, String, null: false

      end
    end
  end
end
```

##### Add the subscription to graphql subscriptions

```ruby
# plugins/easyproject/easy_plugins/easy_agile/api/easy_graphql/init.rb
EasyGraphql.patch('EasyGraphql::Types::Subscription') do
  field :easy_product_backlog_board_move_pbi, subscription: EasyGraphql::Subscriptions::EasyProductBacklogBoardMovePbi do
    argument :easy_product_backlog_board_id, GraphQL::Types::ID, required: true
  end
end
```

##### Trigger the subscription

A subscription can be triggered from anywhere by calling `EasyGraphql::AppSchema.subscriptions.trigger()`.\
We usually stay in graphql and trigger subscriptions as a part of a mutation. Occasionally it's called from a model callback.

```ruby
# plugins/easyproject/easy_plugins/easy_agile/api/easy_graphql/mutations/update_pbi_position.rb
EasyGraphql::AppSchema.subscriptions.trigger('easy_product_backlog_board_move_pbi',
                                             { easy_product_backlog_board_id: board_id },
                                             { result: {
                                               mutation_name: 'updatePbiPosition',
                                               pbi: entity,
                                               pbi_follower: @new_follower,
                                               old_pbi_follower: @old_follower
                                             } })
```

The first parameter is the name of the subscription as seen in the previous step, the second is a hash of arguments also defined there. The third is the result object which will be sent to the websocket.

## Automated Testing

### Back-end

Testing that the trigger was called is the minimum.

```ruby
expect_any_instance_of(GraphQL::Subscriptions::ActionCableSubscriptions)
  .to receive(:trigger).with('easy_product_backlog_board_move_pbi', anything, anything)
```

Optionally you can test the payload content.
