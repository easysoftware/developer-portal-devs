# How to create new EasyQuery

This section provide basic information what needs to be implemented for new working easy query.

## Easy Query
Easy Query is tool which allows user to display and filter records of specific model within web page.
Each Easy Query has defined available filters and columns. User may use them, along with grouping and ordering,
combine it all together to achieve desired results and display records he wants to see.

Easy Query is also used by `EasyGraphql` extension to easily define GraphQL queries for various types.

Easy queries source codes are located in directory `app/easy_queries`. Easy query must be inherited from `EasyQuery` class.
New easy query name is name of model with suffix `EasyQuery`. For example `MyModelEasyQuery` for model `MyModel`. 
Every easy query need to be registered to be able use it within [dashboards](./easy_pages.md).

Although it is possible to namespace easy queries, for now `EasyGraphql::Extensions::EasyQuery` extension does not support it.
So it is recommended to keep corresponding with [controllers](./controllers.md) and use prefixes.

Base easy query definition:
```ruby
class MyModelQuery < EasyQuery
  self.queried_class = ::MyModel

  def initialize_available_filters
    add_available_filter "name"
    add_available_filter "status",
                         type: :list,
                         values: proc { queried_class.statuses.map { |key, value| [queried_class.human_enum_name("status", key), value] } }
    add_available_filter "collection_id",
                         type: :list_autocomplete,
                         source: "all_collections", source_root: "entities",
                         klass: ::Collection
    add_principal_autocomplete_filter "author_id"
  end

  def initialize_available_columns
    tbl = queried_class.table_name

    add_column :id,
               :integer,
               sortable: "#{tbl}.id"
    add_column :name,
               :string,
               sortable: "#{tbl}.name"
    add_column :status,
               :record,
               sortable: "#{tbl}.status"
    add_column :collection, :record,
               sortable: "#{::Collection.table_name}.name",
               groupable: "#{tbl}.collection_id",
               preload: [:collection]
    add_column :description, :text
    add_principal_column :author
  end

  def default_list_columns
    super.presence || %w[name status collection description]
  end

end
```

### Easy Query structure
In the begging is good practice to assign `queried_class` to easy query. It can be used within all query methods.

```ruby
class MyModel < EasyQuery
  self.queried_class = ::MyModel
  
  # ...
end
```

EasyQuery has two important methods to implement. `initialize_available_filters` and `initialize_available_columns`.

EasyQuery has two forms of appearance. First is default query which has static column cells. Second is called `dynamic query`.
Dynamic query allows user to edit values of columns within the table cell. Therefore, it has significant impact on the definition of columns.

For now Dynamic query supports only `EasyIssueQuery`, `ProjectQuery` and `UserQuery`.

#### initialize_available_filters

This method is used to define available filters for easy query. It is called only once during initialization of easy query.
To define filter, `add_available_filter` method is used. It takes two arguments - `name` of filter and `options` hash.
Name of filter is always same as name of column in database. Important option is `:tpye`. 
It determines list of available operators as well as input field. Default type is `:string`. The `:type` also determines the necessary options.

For `:list_autocomplete` or `tree` is essential to define source of available values. It is achieved by options `:source` and `:source_root`.
`Source` defines method, usually managed by `auto_completes_controller`. `Source root` determines how individual objects will behave. 
By `:source_options` is possible to define additional options, E.g.: `project_id`. If the filter is association (most of autocompletes are associations) and
name of column differs from class name, there is possibility to specify class by option `:klass`.

```ruby
add_available_filter "collection_id",
                     type: :list_autocomplete,
                     source: "all_collections", source_root: "entities",
                     klass: ::Collection
```

Due to high amount of options, EasyQuery also provides specific method `add_principal_autocomplete_filter` for `Principal` descendants.
Method contains pre-defined options to avoid big chunks of similar code. It is best practice to use this method to define `Principal` filters.
For example: `author_id`, `user_id`, `assignee_id`, etc. 

```ruby
add_principal_autocomplete_filter "author_id"
```

For `:list` or `:list_optional` is necessary to define list of possible values. It is achieved by option `:values`. Usually value of this option
is `proc` method, which retrieves defined values.

```ruby
add_available_filter "status",
                     type: :list,
                     values: proc { queried_class.statuses.map { |key, value| [queried_class.human_enum_name("status", key), value] } }
```

#### initialize_available_columns

This method is used to define available columns for easy query. It is called only once during initialization of easy query.
To define column, `add_column` method is used. Method has three arguments `name`, `type` and `options`. Name defines name of column.
It should corresponds with entity property. For association is used name of association, not database_column. `Type` defines column cell behaviour.
It should correspond with the type of database column. In case of association or enum, `:record` is used.
`Options` are used to define additional column behaviour in both default and dynamic query.

For both type of queries, most used `options` are:
* `sortable`: Option that allows user to sort query by chosen column. Value of this option is chunk of SQL query. 
Most common is `table_name` with name of `database column`. Occasionally may contains complex chunk of SQL query. 
In case of multiple sorting criteria we use array of strings.
* `default_order`: Closely tight to `sortable`. Defines default ordering of specified column.
* `groupable`: Allows user to group query by values of specified column. Definition is similar to `sortable`.
Values of this options are also chunk of SQL containing `table_name` with name of `database column`. 
If the defined easy query column name corresponds with table name and database column of queried entity, value `true` is possible to use.
* `most_used`: Self describing option of boolean kind. Default value is `false`.
* `preload`/`includes`: Define additional load of associated classes for joins purpose. Value is array of symbols of snake_case class names.
If sortable trough another table is defined, it is necessary to define `includes` for that association as well.

```ruby

add_column :name,
           :string,
           sortable: "#{::MyModel.table_name}.name"
add_column :status,
           :record,
           sortable: "#{::MyModel.table_name}.status"
add_column :collection, :record,
           sortable:  "#{::Collection.table_name}.name",
           groupable: "#{::MyModel.table_name}.collection_id",
           preload: [:collection]
```

To be able to edit columns of associations within dynamic query, autocomplete is required. This is achieved by specific options.
These options for dynamic query are:
* `attribute`: column name of association
* `ref`: referenced class
* `source_options`: additional options for autocomplete
  * `source`: similar to `:source` in filters, method of possible values.
  * `source_root`: similar to `:source_root` in filters, determines how individual objects will behave.
  * `params`: similar to `:source_options` in filters, additional params for source method.

```ruby
add_column :project, :record, most_used: true,
           sortable: "#{Project.table_name}.name",
           groupable: "#{Issue.table_name}.project_id",
           includes: [:project],
           attribute: "project_id", ref: :itself,
           source_options: { source: "allowed_target_projects_on_move", source_root: "projects" }
```

Similar to filters, columns provides method `add_principal_column`. It is used to define columns of `Principal` descendants.
It is quiet new feature, so it is not used in many places. It is best practice to use this method to define `Principal` columns.

```ruby
add_principal_column :author
```

#### default_list_columns
Method purpose is obvious. Due to existence of query settings it prefers original method, 
but in case setting is missing it will fallback to this method. It content should be list of column names in string.

```ruby
def default_list_columns
  super.presence || %w[name status collection description]
end
```

#### EasyPage query methods
Any registered easy query can be used within `EasyPage` and `dashboards`. In use-case like that, 
query can take multiple graph forms instead of table. To provide allowed forms for various queries, 
each query implement methods to tell which forms specific query supports. Available forms (with default values) are:
* `table/list` - default: true,
* `report` - default: true,
* `tiles` - default: true,
* `chart` - default: false,
* `calendar` - default: false,
* `reactive` - default: false.

This methods are implemented for instance as well as class.
```ruby
def self.table_support?
  true
end

def table_support?
  self.class.table_support?
end
```

### Easy Query registration
To be able to use easy query within dashboards, it is necessary to register it. 
Query registration should be defined in one of initializers. For plugins its file `after_init.rb`.
For `easy_engines` and `rys` its file `config/initializers/08_others.rb`.
Registration should be defined in `ActiveSupport.on_load(:easy_project_start)` block

```ruby
ActiveSupport.on_load(:easy_project_start) do
  EasyQuery.map do |query|
    query.register "MyModelQuery"
  end
end
```
