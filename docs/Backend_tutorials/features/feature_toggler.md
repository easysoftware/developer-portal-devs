# What is feature toggler

Feature toggler is a mechanism that allows you to enable or disable certain features in your application without changing the code. 
This is useful when you want to disable some feature that is not ready for production.

Our internal guidelines prefer and support delivering small increments into the codebase to avoid huge merge requests. 
This could possibly come into the conflict with the release of the current state of code including incomplete feature.

Feature toggler should help to avoid this kind of situation.

# Easy Feature Toggler
Easy Feature Toggler is a simple feature toggler that allows you to enable or disable features in our application.
You can find it as a module `EasyFeature` located in `easy_lib/easy_feature.rb`.
It is based on using ENV variables and represents a way to generalize manipulation with these variables in one place.

Principles are very simple. Module contains one public method  `active?`. This method accepts one argument - ENV variable name.
This name is verified, that it matches settled naming convention, during the method process. If the name has incorrect form it will raise argument error.
If the name fits the convention, method will return true or false based on the value, `false` when it's not defined.

During the process of the method, it will also record currently active features into the list, that can be used for future debugging.

## Usage
Recommended way to use this module is in engine/plugin module definition, like `my_engine/lib/my_engine.rb`, which should contain a method corresponding to feature name with *active?* suffix,
e.g.: `my_feature_active?`. 

This module can also be used straight in the code, but with long feature names it could be distracting and unpleasant to read. So it is not recommended.

```ruby title="engine module" lineNumbers
module MyEngine
  class << self
    def my_feature_active?
      EasyFeature.active?("EASY_FEATURE_MY_FEATURE_ACTIVE")
    end
  end

end
```

```ruby title="usage in code"
class MyFeatureController < ApplicationController
  before_action :check_feature
  
  # ...
  
  private

  def check_feature
    render_404 unless MyEngine.my_feature_active?
    
    # possible usage (not recommended)
    # render_404 unless EasyFeature.active?("EASY_FEATURE_MY_FEATURE_ACTIVE")
  end
end
```

## Naming convention
Input for this feature toggler **should ALWAYS be String**. It should start with `EASY_FEATURE_` prefix followed by feature name in uppercase and underscored format.
This name should be followed by `_ACTIVE` suffix.

**Example of correct name: `EASY_FEATURE_MY_EASY_FEATURE_ACTIVE`.**

## ENV exceptions
There are some exceptions in usage of our feature toggler. So far there are only two of them:
- `EASY_FEATURE_ALL_ACTIVE` - this ENV variable is used to enable all features at once. It is useful for deployment purposes.
- `CI` - this ENV variable is used to enable all features at once in CI environment. To prevent frustrating situations when pipeline fails due to feature being toggled off.
It should be present by default in gitlab pipelines.

