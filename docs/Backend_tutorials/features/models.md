# How to create new Model

This section provide basic information what needs to be implemented for new working model.

## Model
Models source codes are located in directory `app/models`. 
Model must be inherited from `ActiveRecord::Base` and must be named in singular form. 

Namespace of model should reflect its path to the source code. Example: `EasyModule::MyModel` should be implemented in `app/models/easy_module/my_model.rb`. 
The name of the model should describe the real object it represents as accurately as possible.

```ruby title="Base model definition" lineNumbers
module EasyModule
  class MyModel < ActiveRecord::Base
    include Redmine::SafeAttributes
    
    belongs_to :author, class_name: "::User", inverse_of: :my_models
    belongs_to :collection, class_name: "::Collection", inverse_of: :my_models, optional: true

    has_many :items, class_name: "::EasyModule::MyItem", dependent: :destroy, inverse_of: :my_model

    before_validation :set_author, on: :create

    validates :name, presence: true, uniqueness: true
    validates :description, length: { maximum: 1000 }

    scope :visible, -> { all }
    scope :like, ->(q) { where(arel_table[:name].matches("%#{q}%")) }

    enum status: { active: 0, disabled: 1 }, _prefix: true

    safe_attributes "name", "description", "status", if: proc { editable? }

    # public methods implementation

    def editable?(_user = User.current)
      true
    end

    private

    # private methods implementation

    def set_author
      self.author_id ||= User.current.id
    end

  end
end
```

### Model structure
First line(s) of model implementation should contain including all necessary concerns like `Redmine::SafeAttributes`. 
With all includes should be also defined necessary model properties like `abstract_class, table_name, table_name prefix`.

```ruby
include Redmine::SafeAttributes
# more includes

self.table_name = "my_model_table_name"
self.table_name_prefix = "easy_module_"
self.abstract_class = true
```
Right after model properties definitions should be defined all model associations. First should be defined all `belongs_to` associations, then `has_many` and `has_one` associations.
Each association have own options to define. Some are shared for all associations, some are specific for each association type.
All of them should be defined with `inverse_of` option. Those which do not reflects exact class name should be also defined with `class_name` option.
`belongs_to` associations are required by default. `optional: true` option is used to change that. `dependent` option should be defined for all `has_many` associations.

```ruby
belongs_to :author, class_name: "::User", inverse_of: :my_models

has_many :items, class_name: "::EasyModule::MyItem", dependent: :destroy, inverse_of: :my_model

has_one :address, class_name: "::EasyModule::MyAddress", dependent: :destroy, inverse_of: :my_model
```

After associations should be defined callbacks and validations. Callbacks definition includes state, when they should be called and `before_` or `after_` prefix. 
They may also contains `on:` option to define on which action should be called. Validations use two types of methods `validate` and `validates`. `validate` is followed by custom method name.
`validates` use predefined validations types,  E.g.: `presence`, `uniqueness`, `length`, etc... If attribute has more validation types, they should be defined on the same line and separated by comma.
If multiple attributes have same validation(s) they should also be defined on the same line and separated by comma. 

```ruby
before_validation :set_author, on: :create

validates :name, presence: true, uniqueness: true, length: { maximum: 255 }
validates :description, :text, presence: true, length: { maximum: 1000 }

validate :custom_my_model_validation
```

Next lines define scopes and enums. Scopes are using keyword `scope`, which is followed by name of scope and lambda(proc) function. Most usual scopes are `visible`, `like` and `active`. 
Enums are defined with `enum` keyword, which is followed by name of enum and hash of possible values. If enum value is not defined explicitly, automatically starts with value `0`. 
With enum definition are also defined some key methods like scopes, getters, setters. Best practice to avoid collision with core methods is to use `_prefix` or `_suffix` options.

```ruby
scope :visible, -> { all }
scope :like, ->(q) { where(arel_table[:name].matches("%#{q}%")) }

enum status: { active: 0, disabled: 1 }, _prefix: true
```

Last lines before model methods definitions are reserved to call public methods from included concerns. E.g.: `safe_attributes` from `Redmine::SafeAttributes` concern, 
or any of `acts_as_something`. `safe_attributes` serves as white list of attributes. `acts_as_something` are used to define some specific behaviour of model. Usually they are defined with options.

```ruby
safe_attributes "name", "description", "status"
acts_as_searchable columns: %W[#{table_name}.name #{table_name}.description],
                   date_column: "#{table_name}.created_at",
                   permission: :view_my_models,
                   project_key: :project_id
```

Last part of model implementation are public and private methods. Public methods usually provide information about instance of model, or implement logic tight to model manipulation.
Some common public methods are:
```ruby
def to_s
  name # or some other instance meaningful representation
end

def visible?(user = User.current)
  # implementation
end

def editable?(user = User.current)
  # implementation
end

def deletable?(user = User.current)
  # implementation
end

```
Most usage of private methods is to define callback or validation methods.

## Migrations
See [How to create new migration](./migrations.md) section.

## Tests
Model tests are located in `spec/models` directory. Name convention of model test is `model_name_spec.rb`. Model spec should test all model methods (including public and private methods), 
scopes, validations and callbacks (including custom callbacks and callbacks defined within associations).

```ruby title="Example of my_model_spec.rb" lineNumbers
require "easy_extensions/spec_helper"

describe EasyModule::MyModel, type: :model do
  
  describe "#create" do
    subject { described_class.create(params) }
    # definition of necessary objects

    it "assign author" do
      # test implementation
    end
  end

  describe "#editable?" do
    subject { entity.editable? }
    # definition of necessary objects

    it "tests editable method" do
      # test implementation
    end
  end

  describe ".visible" do
    subject { described_class.visible }
    # definition of necessary objects

    it "tests visible scope" do
      # test implementation
    end
  end

  describe ".like" do
    subject { described_class.like(term) }
    # definition of necessary objects

    it "tests like scope" do
      # test implementation
    end
  end
end
```

### Factories
To achieve testing of all aspects of model, there is also needed to test behaviour of existing instances. Factories are used to prepare those instances.
Factories should be located in `spec/factories` directory. Name convention of factory is `model_name.rb`. Factory should be defined for each model. 

Example of `my_model.rb`:
```ruby title="spec/factories" lineNumbers
FactoryBot.define do
  factory :my_model, class: EasyModule::MyModel do
    sequence(:name) { |n| "MyModel #{n}" }
    description { "MyModel description" }
    status { :active }
    association :author, factory: :user
    association :collection, factory: :collection
  end
end
```
Uniq values should be defined as sequence, to avoid collision with existing instances. Associations should use other factories to create associated objects. 
To create objects with specific unusual properties or behaviour, should be used traits. Traits are defined with `trait` keyword and block of implementation.

Example of `my_model.rb` with trait:
```ruby title="example with trait" lineNumbers
FactoryBot.define do
  factory :my_model, class: EasyModule::MyModel do
    sequence(:name) { |n| "MyModel #{n}" }
    description { "MyModel description" }
    status { :active }
    association :author, factory: :user
    association :collection, factory: :collection

    trait :disabled do
      status { :disabled }
    end
  end
end
```
Now when factory with this trait is called, it will create instance of `MyModel` with status `:disabled` instead of `:active`.
```ruby
FactoryBot.create(:my_model, :disabled)
```
