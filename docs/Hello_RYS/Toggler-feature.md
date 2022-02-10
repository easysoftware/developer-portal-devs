 # Feature Toggler

*In this article you will learn what is our new Toggler feature and how to use it*

---

Toggle feature is used for... **TODO NA DOPLNĚNÍ** 



--- 

## How to work with Toggler? 

You can toggle some code based on the database state or by custom definition. 

First of all you need to register features. Put this code into config/initializers.

```ruby
# Basic definition
# This feature will be active if record on the DB will have active == true
Rys::Feature.add('issue.show')

# For specific plugin
# Prefered way because you can controll features directly on plugin settings 
Rys::Feature.for_plugin(MyRys::Engine) do
  Rys::Feature.add('my_rys.show')
end
```
Features have tree structure so if parent is disabled all children are disabled too. For example:

If issue is disabled -> all issue.* are disabled too regardless to theirs state.

```ruby
Rys::Feature.add('issue.show')
Rys::Feature.add('issue.show.sidebar')

# issue
# `-- issue.show
#     `-- issue.show.sidebar
```
You can set default feature state via: 

```ruby
# issue.show is default activated
Rys::Feature.add('issue.show', default_db_status: true)

# issue.show is default deactivated
Rys::Feature.add('issue.show', default_db_status: false)
```

You can check feature state via: 

```ruby
Rys::Feature.on('issue.show') do
  # issue.show is active
end

if Rys::Feature.active?('issue.show')
  # issue.show is active
end
```
---

### Callbacks

You can define action which will be triggered after a status change.

```ruby
Rys::Feature.add('issue.show', status_changed: lambda { |active| ... })
```

---

### In routes

You can easily turn on/off routes. 

```ruby
get 'path', rys_feature: 'issue.show'

rys_feature 'issue.show' do
  get 'path'
end
```
---

### In patches

More details can be found at Patch management section.

```ruby
instance_methods(feature: 'issue.show') do
  def show
    # Something todo
    # if feature is active
    super
  end
end
```

---

### Custom condition

If you do not want to use DB records for state checking, you can define custom conditions.

```ruby
# Ruby block state
# Will be active on 10% of the case
Rys::Feature.add('issue.show') do
  rand(0..10) == 1
end
```

or you can use both approaches by: 

```ruby
Rys::Feature.add('issue.show') do
  rand(0..10) == 1 && RysFeatureRecord.active?('issue.show')
end
```

---

### Translations

All features have title and description- You can set them via language file or via options.

```ruby
Rys::Feature.add('issue.show')
# => I18n.t 'rys_features.issue_show.title'
# => I18n.t 'rys_features.issue_show.description'

Rys::Feature.add('issue.show', title: :feature_title,
                               description: :feature_description)
# => I18n.t 'feature_title'
# => I18n.t 'feature_description'
```




























