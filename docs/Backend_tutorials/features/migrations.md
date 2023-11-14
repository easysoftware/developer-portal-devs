# Generate migrations (database or data)
Each `ActiveRecord` [model](./models.md) must have a database table. Database tables are created and modified by migrations.
Migration is a Ruby class which inherits from `ActiveRecord::Migration` class (best practice is to specify version -> `ActiveRecord::Migration[6.1]`)
and is located in `db/migrate` directory. Migration class name should be in format `Action` + `ModelClassName`, E.g.: `CreateMyModel` or `AddMyColumnToMyModel`.
Migration class includes single `change` method. In some cases may be found in older format, containing two methods `up` and `down`.
All migration source files follows name convention `YYYYMMDDHHMMSS_migration_class_name.rb`.

Best way (and strongly recommended) is to create new migration is to use `rails` command:
```bash
rails g migration CreateMyModel author:belongs_to name:string description:text status:integer
```
This command will create new migration file with name `db/migrate/20210101000000_create_my_model.rb` and with following content:
```ruby
class CreateMyModel < ActiveRecord::Migration[6.1]
  
  def change
    create_table :my_models do |t|
      t.belongs_to :author, null: false
      t.belongs_to :collection, null: true
      t.string :name, null: false
      t.text :description, null: false
      t.integer :status, null: false, index: true

      t.timestamps
    end
  end
  
end
```
Some attributes are intended to use for search. Those attributes should be indexed. Indexes are defined by option `index: true` within column definition.
```ruby
t.string :name, null: false, index: true
```
or explicit by method `add_index`:
```ruby
add_index :my_models, :name
```

In some specific cases, existing table may be used. In that case, table name is specified in model definition.

### How to change existing table
After migration execution, migration source code shouldn't be altered. In case of any change needs, new migration should be created. For example:
```bash
rails generate migration RemoveDescriptionFromMyModels description:text
```
This command will create new migration with following content:
```ruby
class RemoveDescriptionFromMyModels < ActiveRecord::Migration[6.1]
  
  def change
    remove_column :my_models, :description, :text
  end
  
end
```
### Before release
Before first release of new model, all new model migrations should be merged into single migration, which will create table with all columns.

#### Development period best practice
During development period, when model is often changing, may be annoying to create new migration for each change, with expectations of merging all migrations into single one before release.
In this kind situation of situation is good to use option `force: true` within method `create_table`. This option will drop table before creation, so all changes will be applied.
```ruby
create_table :my_models, force: true do |t|
  # columns definitions
end
```
<!-- theme: info -->
> #### Tip
> 
> To ensure re-execution of migration within migrated environment, increment of timestamp by 1 second may help. (`20210101000000_create_my_model.rb` -> `20210101000001_create_my_model.rb`)

## Data migration
Data migrations are used to change data in database. They are located in `db/data` directory. Name convention of data migration stays the same as DB migration.
Data migrations are inherited from `EasyExtensions::EasyDataMigration` class and must implement two methods `up` and `down`.
Purpose of this kind of migrations is to prepare data before first release, or to alter existing data when crucial change was implemented.

```ruby
class DataChangeInDb < EasyExtensions::EasyDataMigration

  def up
    # data change implementation
  end

  def down
    # rollback implementation of changed data
  end

end
```
