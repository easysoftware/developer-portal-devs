## Introduction

This guide goes over the implementation of real-time updates using GraphQL subscriptions.

What it means is that when a client(browser) goes to a page with a **subscription** it opens a **subscription** using a **websocket**. Javascript is then listening to any message
coming through this **subscription** and can change the page content based on it.

We take our implementation of product backlog board as an example, which, to simplify, displays multiple columns of ordered cards.\
If multiple users are looking at the same board moving cards around, all of their changes are **broadcast** to everyone else so everyone sees the same state of cards without
needing to refresh the page.

The guide is divided into front-end(FE) and back-end(BE) sections and describes requirements, implementation and recommended _PATTERNS_ used.

## Requirements

### Front-end

### Back-end

- `gem 'graphiql-rails'`
- `gem 'graphql'`
- working websockets (one is enough) -> GraphQL subscriptions use [ActionCable](https://guides.rubyonrails.org/action_cable_overview.html) internally so `cable.yml` needs to be
  configured

## Implementation

_PATTERN:_ Create multiple context-oriented subscriptions instead of a single big one.\
In our backlog board example we use 5 different subscriptions:

- `easyProductBacklogBoard` (all actions not covered by the other ones)
- `easyProductBacklogBoardInbox` (actions in inbox)
- `pbiSplitter` (split action)
- `easyProductBacklogBoardMovePbi` (reordering action)
- `movePbiIntoSprintBoardSubscription` (moving into sprint board action)

Further code examples will focus on `easyProductBacklogBoardMovePbi` subscription which handles actions that change positions of entities, product backlog items(PBIs) in this case.

### Front-end

General guide for implementing subscriptions via apollo-client can be found here - https://www.apollographql.com/docs/kotlin/tutorial/11-subscriptions.

Our subscriptions are defined in their `subscriptions.ts` file.

### Back-end

- Define the subscription class
  - _PATTERN:_ subscribe return object -> do we ever use it?????
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

- Add the subscription to graphql subscriptions
```ruby
# plugins/easyproject/easy_plugins/easy_agile/api/easy_graphql/init.rb
EasyGraphql.patch('EasyGraphql::Types::Subscription') do
  field :easy_product_backlog_board_move_pbi, subscription: EasyGraphql::Subscriptions::EasyProductBacklogBoardMovePbi do
    argument :easy_product_backlog_board_id, GraphQL::Types::ID, required: true
  end
end
```

- Trigger the subscription\
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
## Automated Testing
### Back-end
Testing that the trigger was called is the minimum.
```ruby
expect_any_instance_of(GraphQL::Subscriptions::ActionCableSubscriptions).
  to receive(:trigger).with('easy_product_backlog_board_move_pbi', anything, anything)
```
Optionally you can test the payload content.
