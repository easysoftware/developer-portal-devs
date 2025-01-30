# Work with entity history

`journals` are a list of comments and changes of an entity. They are stored in the `journals` table with `journal_details` records of changed attributes.

We use journals to display history of changes usually in the detail of an entity. The number of displayed journals is limited by `EasySetting.value("easy_extensions_journal_history_limit")` or a default of 10. To load all journals, a "Show all" button is displayed at the bottom.

## Model

First of all you need to add `acts_as_easy_journalized` concern into you model. It add functionality to store and work with journals.
For more details see `EasyPatch::ActsAsEasyJournalized::ClassMethods#acts_as_easy_journalized`

```ruby title="Example of acts_as_easy_journalized usage" lineNumbers
module EasyModule
  class MyModel < ActiveRecord::Base
    include Redmine::SafeAttributes
    acts_as_easy_journalized non_journalized_columns: %w[uuid author_id]

    after_save :create_journal
  end
end
```

To journalize changes of attributes you need to call `init_journal` method first - BEFORE attributes are set. Then, calling `save` automatically creates `JournalDetail` objects.

## Controller

We usually call `init_journal` in update action. To show history / journals we use standalone action which renders journals using AJAX.

```ruby title="Example of update action" lineNumbers
def update
  my_model.init_journal(User.current, params[:notes])
  my_model.safe_attributes = params[:my_model]
  respond_to do |format|
    format.html do
      if my_model.save
        flash[:notice] = l(:notice_successful_update)
        redirect_back_or_default my_model_path(my_model)
      else
        render action: :edit
      end
    end
  end
end
```

Include the `EasyControllersConcerns::EasyJournals` concern in your controller and define the `journals` method as below.

```ruby title="Example of journals action for render history" lineNumbers
include EasyControllersConcerns::EasyJournals

def journals
  render_easy_journals(my_model)
end
```

## Users permissions

Every action in the controller should have proper permissions. Do not forget to extend the `view_` permission with your new action "journals".

```ruby title="Example of permissions" lineNumbers
permission :view_my_models, { my_models: %i[index show journals] }
```
## Routes

You need to define a route for the `journals` action. For example:

```ruby title="Example of routes" lineNumbers
resources :my_models do
  member do
    get :journals
  end
end
```

## Views

There are two approaches to render journals:

### Async loading using tabs (recommended)

Render partial with tabs.

```rhtml
<div id="entity-detail-bottom">
  <%= render partial: "common/entity_tabs", locals: { tabs: my_model_tabs(@my_model), tabs_container: "entity-detail-bottom" } %>
</div>
```

Define a helper method that holds an array of tabs, specifying a trigger with AJAX loading.

```ruby title="Example of helper method for tabs" lineNumbers
module MyModelHelper

  def my_model_tabs(entity)
    tabs = []
    tabs << { name: "history", label: l(:label_history), trigger: "EntityTabs.showAjaxTab(this, '#{journals_my_models_path(my_model, tab: 'history')}')" }

    call_hook(:helper_my_model_tabs, tabs:, entity:)

    tabs
  end

end
```

### Direct rendering

TODO: add example

## User reactions

Keep in mind that users can react to comments with emojis. To fully support this feature, you need to ensure the proper loading of journals past the basic limit (clicking the "Show all" button). This is done using GraphQL and all you need is to define a GraphQL `Query` field and `Type` for your entity.

For the `Type` just use the `has_journals`. You can also override the default methods it adds as needed.
```ruby title="Example of journals usage in GraphQL" lineNumbers
module EasyGraphql
  module Types
    class MyModel < Base
      has_journals

    end
  end
end
```

Then, extend `Query` with a new field for fetching journals.

```ruby title="Example of extending EasyGraphql::Types::Query" lineNumbers
EasyGraphql.register_patch("EasyGraphql::Types::Query") do
  field :my_model, EasyGraphql::Types::MyModel, null: true do
    description "Find MyModel by ID"
    argument :id, GraphQL::Types::ID, required: true
  end

  def my_model(id:)
    MyModel.visible.find_by(id:)
  end
end
```
