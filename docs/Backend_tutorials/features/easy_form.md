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
Source code is located in `app/east_design/easy_form_builder.rb`.

Builder includes concerns that represents components or inputs. 
Html structure, behaviour and usage of each method for individual form field/component are described
in [Easy Storybook](https://es.easyproject.com/easy_storybook).

## Components
To make form construction more modular and expressive, Easy Form supports the use of form components.
A component is a plain Ruby module that encapsulates the rendering logic for a specific part of the form — such as an input field, fieldset, label, or hint.
These components are structured as modular concerns and included directly into the custom form builder class. 
This allows components to define helper methods that extend the form builder’s public interface in a consistent and reusable way.

Each component defines one or more instance methods on the form builder (e.g., radio_button_fieldset, radio_button, etc.), 
which can be used directly in erb templates. This methods may optionally support yielding a block for advanced behavior.
Each module may includes additional private methods exclusive for component logic.

Components are organized in `app/east_design/easy_form/` directory. 
They are distributed into subdirectories by its nature, such as `inputs`, `components`.

Also there is module `EasyForm::BuilderUtils` designated for shared private method across multiple components.
