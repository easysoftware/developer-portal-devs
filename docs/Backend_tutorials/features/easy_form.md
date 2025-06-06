# Easy Form

⚠️ **Warning:** Easy Form components require application code on version **v15 or higher**.

Easy Form is a new approach for building forms in ERB templates for application version V15 and higher. 
It aims to simplify form construction, reduce repetitive code, reduce level of customization 
and encourage consistent structure and styling across views accordingly to design system.

Implementation is included as helper module `easy_form_helper.rb` within `EasyExtensions`.
It contains two methods `easy_form_for` and `easy_form_tag`, which are used to create forms in views.

Both methods use `EasyExtensions::EasyFormBuilder` as a form builder to provide implementation of individual components.

## easy_form_for
`easy_form_for` is used to create forms for specific model. Mandatory argument for this method is `model` instance. 
Additional optional arguments are: 
- `local` - tells Rails whether the form should behave as a regular HTML form or a JavaScript-enhanced remote form,
- `url` - custom url for form submission, if not specified, it will use the default URL based on the model's routing,
- `method` - HTTP method for form submission,
- `format` - format of response,
- `html` - additional HTML options.

```erb title="example of method usage"
<%= easy_form_for(model: @my_model) do |f| %>
  <%= f.text_field :name %>
  <%= f.submit "Save" %>
<% end %>
```

## easy_form_tag
`easy_form_tag` is used to create forms without a specific model.
It is useful for forms that do not correspond to a single model or when you want to submit data that does not directly map to a model.
Mandatory arguments are:
- `scope` - scope for the form, which is used to generate unique IDs and names for form fields,
- `url` - URL for form submission,
Optional arguments are:
- `method` - HTTP method for form submission, default is `:post`,
- `local` - whether the form should behave as a regular HTML form or a JavaScript-enhanced remote form,
- `format` - format of response,
- `html` - additional HTML options.

```erb title="example of method usage"
<%= easy_form_tag(scope: "my_form", url: my_custom_path) do |f| %>
  <%= f.text_field :name %>
  <%= f.submit "Submit" %>
<% end %>
```

## EasyFormBuilder
`EasyFormBuilder` is a custom form builder that inherits from `ActionView::Helpers::FormBuilder` 
and provides interface for creating form fields with consistent styling and structure.
Source code is located in `app/utils/easy_extensions/easy_form_builder.rb`.

Builder methods either implements required html content or calls component to render it.
Html structure, behaviour and usage of each method for individual form field/component are described
in [Easy Storybook](https://es.easyproject.com/easy_storybook).

## Components
To make form construction more modular and expressive, Easy Form supports the use of form components. 
A form component is a plain Ruby class responsible for rendering a specific part of a form — such as an input field, field_set, etc.
Each component follows consistent interface, making it easy to integrate and extend.

Base interface provides class and instance method for rendering `render`. 
Instance method is responsible for rendering component content and accepts block as optional argument. 
Class method accepts any named arguments and block as well. Passes named arguments to instance constructor. 
With initialized instance, it calls `render` with (optional) block.

```ruby title="base component interface" lineNumbers
module EasyFormComponents
  class Base
    include ActionView::Helpers::TagHelper
    include ActionView::Context
    include Redmine::I18n

    def self.render(**, &)
      new(**).render(&)
    end

    def render(&_block)
      raise NotImplementedError
    end

  end
end
```

```Ruby title="example of component" lineNumbers
module EasyFormComponents
  class TextField < Base
    
    # @param [EasyExtensions::EasyFormBuilder] builder
    # @param [Symbol] field
    # @param [Hash] options
    def initialize(builder:, field:, options: {})
      @builder = builder
      @field = field
      @options = options
    end
    
    def render
      builder.text_field(field, options)
    end

    private

    attr_reader :builder, :field, :options
  end
end
```





