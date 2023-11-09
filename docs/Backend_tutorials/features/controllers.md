# How to create new Controller

This section provide basic information what needs to be implemented for new working controller.

## Controller
Controllers source codes are located in directory `app/controllers`. Controller must be inherited from `ApplicationController`.
Usual controller name is pluralized name of model, which is used in controller, with suffix `Controller`. 
For example `MyModelsController` for model `MyModel`. There are some special occasions, when controller doesn't work with model,
but some other object instead. In this case, controller name is name of object with suffix `Controller`. For example EasyPages.

Namespacing of controllers does not work well with routes in redmine. If controller works with model in namespace, prefix should be used.
For example `MyNamespaceMyModelsController` for model `MyNamespace::MyModel`.

Base controller definition:
```ruby
class MyModelsController < ApplicationController
  before_action :authorize_global
  before_action :custom_before_action, only: %i[index show]

  rescue_from(ActiveRecord::RecordNotFound) { |_exception| render_404 }
  rescue_from(Unauthorized) { |_exception| render_403 }
  
  helper :attachments
  include AttachmentsHelper
  include_query_helpers

  def index
    index_for_easy_query MyModelQuery
  end
  
  def show
    my_model
    respond_to do |format|
      format.html
    end
  end

  def new
    @my_model = MyModel.new
    @my_model.safe_attributes = params[:my_model]
    respond_to do |format|
      format.html
    end
  end
  
  def create
    @my_model = MyModel.new(params[:my_model])
    @my_model.safe_attributes = params[:my_model]
    respond_to do |format|
      format.html do
        if @my_model.save
          flash[:notice] = l(:notice_successful_create)
          redirect_back_or_default my_model_path(@my_model)
        else
          render action: :new
        end
      end
    end
  end
  
  def edit
    # implementation
  end

  def update
    my_model.safe_attributes = params[:my_model]
    # implementation
  end

  def destroy
    # implementation
  end
  
  # custom actions
  
  private
  
  def my_model
    @my_model ||= MyModel.find(params[:id])
  end
  
  def custom_before_action
    # implementation
  end
end
```

### Controller structure
First properties to define within a new controller are before actions. Most common before actions for easy controllers are `authorize_global` and `find_entity`.
Definition of `before_action` contains name of method to be executed and options to restrict execution to specific actions. Possible options are `only` and `except`.

```ruby
before_action :authorize_global
before_action :custom_before_action, only: %i[index show]
```

Next to define are `rescue_from` blocks. These blocks are used to handle exceptions thrown by controller actions.
They can be used to render 404 or 403 pages whenever controller encounters such an error.

```ruby
rescue_from(ActiveRecord::RecordNotFound) { |_exception| render_404 }
rescue_from(Unauthorized) { |_exception| render_403 }
```
Thanks to that, theres is no need to use `find_entity` before action anymore. Instead, some kind of resource retrieval method may be used within all controller actions.

```ruby
def update
  my_model.safe_attributes = params[:my_model]
  # implementation
end

# ...

private

def my_model
  @my_model ||= MyModel.find(params[:id])
end
```

After that, there are helpers to be included. There are two ways to include helper. 
First is to include helper module (`include AttachmentsHelper`) directly into controller. 
Second is to use `helper` method. This allow to use helper methods within views. 
If controller uses queries, is good to include query helper.

```ruby
helper :attachments
include AttachmentsHelper
include_query_helpers
```

Next to define are controller actions. Each action is represented by public controller method. 
Each method should be ended by block `respond_to`, which defines various response formats.

```ruby
def my_action
  # request process implementation
  respond_to do |format|
    format.html do
      flash[:notice] = l(:notice)
      redirect_to path
    end
    format.js { render_api_ok }
  end
end
```

Last to define are private methods. These methods usually serves as before_actions or define logic necessary to request handling.

### Views
Action views are defined in `app/views/my_models` directory. Each action view name must correspond to action name.
Each GET action use action view by default. To achieve otherwise, `render` or `redirect_to` methods must be used within action.

### Routes
One way how to define endpoints for actions are routes. Routes are defined in `config/routes.rb` file.
Default actions routes for controller are defined by `resources` method, which should correspond with name of object/model_plural within controller name. 
To restrict default routes, `only` or `except` options can be used. There are several ways how to define custom routes. 
Within `resources` block is possible to use `member` or `collection` block. Each custom route must have defined REST method and name. 
If action is using multiple REST methods, `match` method in combination with option `via: %i[get put]` can be used.

It is also possible to define custom routes outside mentioned blocks. This kind of definition follows template `method` + `path` + `options`.
Method is a REST method or `match`. Options can be:
* `to` - controller#action
* `as` - route name
* `via` - additional REST methods
* `controller` - controller name
* `action` - action name

```ruby
resources :my_models do
    member do
      match :my_model_action, via: %i[get put]
    end
    collection do
        get :my_models_action
    end
end

match 'my_models/:id/my_model_action', to: 'my_models#my_model_action', via: %i[get put]
get 'my_models/my_models_action', controller: 'my_models', action: 'my_models_action', via: %i[get put]
```

<!--
  -- Is this enough? Should be more described here or in own section?
-->

### Tests

<!--
  -- TODO: describe controller tests & request tests
-->