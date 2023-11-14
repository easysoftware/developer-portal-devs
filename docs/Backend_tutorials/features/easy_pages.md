# How to create new Easy Page - dashboard

This section provide basic information what needs to be implement for working Easy Page - new dashboard.

## Controller
Controller just call registration EasyPageHandler which provide all necessary DSL for future dashboard.

```ruby title="Example of dashboard controller" lineNumbers
class DashboardController < ApplicationController
  EasyExtensions::EasyPageHandler.register_for(self, {
    page_name: 'my-dashboard',
    path: proc { my_dashboard_index_path(t: params[:t]) },
    show_action: :index,
    edit_action: :index_layout
  })
end

```

### arguments
* `page_name` - internal name of EasyPage corresponding to this dashboard. EasyPage must exists in database.
* `path` - must be `proc` and its route to dashboard. 
* `show_action` - is default "view" - its dashboard itself
* `edit_action` - is "edit" part of dashboard. In this action user can add modules...

For complete list see method definition.

## Routes
Routes are defined in `config/routes.rb`.

```ruby
resources :my_dashboard, only: [:index], path: "/super-dashboard" do
  match :layout, action: :index_layout, via: %i[get post], on: :collection
end
```
## Views
Views are defined in `app/views/my_dashboard` folder. There must exists `index.html.erb` and `index_layout.html.erb` files.

## Migration
Generate new **data** migration which creates EasyPage object in database.

```ruby lineNumbers
class CreateMyDashboard < EasyExtensions::EasyDataMigration

  def up
    EasyPage.create!(page_name: 'my-dashboard', layout_path: 'easy_page_layouts/two_column_header_three_rows_right_sidebar')
  end

  def down
    EasyPage.where(page_name: 'my-dashboard').destroy_all
  end

end
```
## Tests
in specs you can define fixtures with your new easy_page and of course test itself. My recommandation:

### Fixtures
in `spec/fixtures/easy_pages.yml`
```yaml
easy_page_069:
  id: 69
  page_name: my-dashboard
  layout_path: easy_page_layouts/two_column_header_first_wider

```

in `spec/fixtures/easy_available_zones.yml`
```yaml
easy_page_available_zone_0069:
  id: 69
  easy_pages_id: 69
  easy_page_zones_id: 2
  position: 1
```

### Test
in `spec/requests/dashboard_controller_spec.rb`

```ruby title="requests spec example" lineNumbers
require "easy_extensions/spec_helper"

RSpec.describe "My Dashboard", logged: true do
  subject { response }

  describe "GET /super-dashboard" do
    let!(:request) { get "/super-dashboard" }

    it { is_expected.to have_http_status(:forbidden) }

    context "with admin", logged: :admin do
      it { is_expected.to have_http_status(:ok) }
    end
  end

  describe "GET /super-dashboard/layout" do
    let!(:request) { get "/super-dashboard/layout" }

    it { is_expected.to have_http_status(:forbidden) }

    context "with admin", logged: :admin do
      it { is_expected.to have_http_status(:ok) }
    end
  end
end

```
