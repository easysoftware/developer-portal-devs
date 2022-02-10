# Backend code styles

*In this article we will show you what is our BE code styles...*

---

We want our code to be readable and understandable and since our codebase is large and complex, it's important to follow some basic rules when writing code.

To ensure code style, we use Rubocop. 

---

## General rules

<!-- theme: warning -->
>**Do not change Redmine!**
>
>Do not change any Redmine files directly. We are a plugin for Redmine, not a fork.

<!-- theme: info -->
>**Try to use existing code.**
>
>Before adding something, inspire yourself with an existing code base. Try to find an applicable existing solution, copy and modify it. On the other hand, avoid blind copy-pasting.
>
>Don't be afraid to explore the source code. Look at models, controllers or views and do it similarly - syntax, code style. When you use a method, you have to know what it does.

<!-- theme: info -->
>**Comments**
>
>Comments are always welcome. Please use [YARD](https://yardoc.org/) docs style.
>
>Comments are required on complex codes. Otherwise you should write a *self-labeling code*. It means that a method's name describes what it does.

---

## Editor settings

1. **Two spaces for indentation**. Use two spaces for indenting your code and swear an oath to never mix tabs and spaces.
2. **Unix line-endings**. Use UNIX-style newlines (\n), and a newline character as the last character of a file. Windows-style newlines (\r\n) are forbidden inside any repository.
3. [**UTF-8**](https://es.easyproject.com/issues/8) source coding only
4. No whitespaces
5. Spaces around curly braces in hashes
6. Spaces around curly braces in blocks
7. Spaces around arrows
8. Do not indent `when` cases
9. Do not indent after private / protected
10. Single quotes `''` for non-interpolated strings

> RubyMine takes care of most of them automatically. 

## Conventions

### ActiveRecord migrations

We are using two types of migrations:

1. Schema migrations, located in db/migrate, handles changes to the database schema.
2. Data migrations, located in db/data, which handles only changes of data.

### Overwriting templates / partials

One plugin can completely replace a template or a partial of the platform or a plugin which was **loaded before it**. This pattern can be used to implement changes, but only if you are rewriting more than 50 % of the content.

In case of small additions, create new or use existing [hooks](https://www.redmine.org/projects/redmine/wiki/Hooks). Hooks must be created in the appropriate repository, generally in the platform.

### Prefixies

Prefixes are preferred in order to avoid naming conflicts. For plugin `Abc` for example, use:

1. `AbcHelper` instead of `Helper`

2. `label_abc_sum:` instead of `label_sum:` in langfiles

### Localisation

All text must use `I18n` and must be translated at least to English. Wording should be clear for developers and follow english translation. 

Common prefixes:

`button_` `error_` `field_` `label_` `text_` `title_`

<!-- theme: warning -->
> Do not overwrite lang keys from Redmine or EasyRedmine.


### Indentation and naming

```ruby
# BAD
issues.map{|x| x.project.versions.map{|y| y.css_classes}}.flatten

# GOOD
issues.flat_map { |issue|
  issue.project.versions.map { |version|
    version.css_classes
  }
}

```

### `belong_to` relations

Always specify `optional:true` when defining belongs_to associations which are not required. 

```ruby
class Issue < ApplicationRecord
  belongs_to :assigned_to, optional: true
end

```

### HTML & JS

Javascript use **ES6** from next/major (ES5 before in case you need some historical bugfix)
For the `class` attribute use use [BEM](https://en.bem.info/methodology/quick-start/) convention `- Block__Element_Modifier / multi-word-block-name__multi-word-element-name_modifier-name_modifier-value:`

```ruby
 <div class="easy-query__heading-title_state_selected"></div>
```

For the `id` attribute use `_` as separator:

```ruby
<div id="easy_entity_import_dialog"></div>
```

> If you haven't found what you're looking for, you can look at community guidelines: 
>
>For RUBY [try this](https://github.com/rubocop/ruby-style-guide). 
>
>For RAILS [try this](https://github.com/rubocop/rails-style-guide).

















