# Store entity history & changes

`journals` are list of comments and changes of an entity. They are stored in the `journals` table with `journal_details` records of changed attributes.

We are use journals for display history of changes usually in detail of some entity.

## Model

First of all you need add `acts_as_easy_journalized` concern into you model. It allows stores and works with journals.
For more datails see `EasyPatch::ActsAsEasyJournalized::ClassMethods#acts_as_easy_journalized`

```ruby title="Example of acts_as_easy_journalized usage" lineNumbers
module EasyModule
  class MyModel < ActiveRecord::Base
    include Redmine::SafeAttributes
    acts_as_easy_journalized non_journalized_columns: %w[uuid author_id]

    after_save :create_journal
  end
end
```

For journalize changes (changes of attributes) you need call `init_journal` method first - BEFORE attributes are set. Then `save` automatically create `JournalDetail` objects.
## Controller

We are using usually `init_journal` in update action. For show history / journals we are using standalone action which render journals on ajax.

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

```ruby title="Example of journals action for render history" lineNumbers
helper :easy_journal

def journals
  tab_manager = EasyExtensions::JournalsTabsManager.new my_model, tab: params[:tab], no_limit: params[:journals] == "all"
  @journals = tab_manager.journals
  @journals_count = tab_manager.journals_count
  render partial: "easy_journals/entity_history", locals: { entity: my_model }
end
```

## Routes

You need define route for journals action. For example:

```ruby title="Example of routes" lineNumbers
resources :my_models do
  member do
    get :journals
  end
end
```

## Views

There are 2 approaches how to render journals. First is use tabs - so you need some helper method with tabs and render tabs which load journals by ajax. Second approach is render journals directly on page.
```rhtml
  <div id="entity-detail-bottom">
    <%= render partial: "common/entity_tabs", locals: { tabs: my_model_tabs(@my_model), tabs_container: "entity-detail-bottom" } %>
  </div>
```

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

Or render directly

TODO: add example