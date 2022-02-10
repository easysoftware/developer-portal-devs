# Patch management

*In this article you will learn how to use correctly Patch management tool*

---

## RYS Patch

Patching RYSes is quite easy. 

First of all you need to **register a path to director**.

>If you generated RYS via `rys:plugin` then skip the first step. 


```ruby
# extend your engine
include ::Rys::EngineExtensions

# or manually
Rys::Patcher.paths << PATH_TO_DIRECTORY
```


Next step is **definition of the patch:**

```ruby
Rys::Patcher.add(CLASS_TO_PATCH_IN_STRING) do

  # nothing
  #    patch is applied after redmine plugins are loaded
  # :earlier_to_prepare
  #    applied as soon as possible
  where WHERE

  # Applied once. Useful for classes which are not reloaded (like String)
  apply_only_once!

  # Applied if CONDITION is met
  apply_if do
    CONDITION
  end

  # Apply if redmine plugins exist and its installed
  apply_if_plugins :easy_crm_cases

  included do
    # This section is evaluated in CLASS_TO_PATCH_IN_STRING
  end

  instance_methods do
    # Your instance methods
  end

  instance_methods(feature: FEATURE) do
    # Your conditional methods
  end

  class_methods do
    # Your class methods
  end

end
```
<!-- theme: warning -->
>Be careful and try to avoid mixing patching with `alias_method_chain`.

So at the end your patch can look like this: 

```ruby
Rys::Patcher.add('IssuesController') do

  included do
    before_action :add_flash_notice, only: [:show]
  end

  instance_methods do

    def show
      flash.now[:notice] = 'Hello'
      super
    end

  end
end
```

<!-- theme: info -->
>Patches are applied via `.preload` so you can call `super`.
>
>Method insides `instance_methods(feature:)` must have super method
