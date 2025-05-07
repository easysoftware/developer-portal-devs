# Backend code styles

*In this article we will show you what code style to follow when developing BE.*

---
## Rubocop & Easy Style
We want our code to be readable and understandable and since our codebase is large and complex, it's important to follow some basic rules when writing code.

To ensure code style, we use Rubocop and our `easy_style` gem.

To deal with the styles you, preferably, needs both of them in your Gemfile/gemspec file. Most of our projects already have this installed.
Please follow the instruction below.

#### For platform and plugins put this into Gemfile:
```ruby
gem "easy_style"
```

After this run
```
bundle update
bundle exec rubocop
```

### Dealing with offenses
#### Fixable offenses
Every time you see the possibility to fix some offenses - please do it.
We keep two ways of exempting specific offenses:
1. `.rubocop_todo.yml`
   - keeps offenses that are fixable and should be fixed eventually.
   - to regenerate use `bundle exec rubocop --regenerate-todo`
2. `.rubocop_ignore.yml`
   - keeps offenses that we want to keep and should be ignored.
   - exceptions are added manually

For more info, see https://docs.rubocop.org/rubocop/configuration.html#automatically-generated-configuration

#### Unfixable offenses
If it is not possible to easily fix offenses - refactor the code. It will probably make it looking different, but that's fine.
If it is too difficult to refactor OR it is not possible for some reason you can disable the cop within source code
https://docs.rubocop.org/rubocop/configuration.html#disabling-cops-within-source-code

<!-- theme: info -->
> Please do not overuse disabling!

---

## General rules

<!-- theme: warning -->
>**Do not change Redmine!**
>
> Do not change any Redmine files directly. Easy Project is a plugin for Redmine, not a fork.
> As we mentioned in [Repository structure]() Redmine is the main Rails app on which our system is built. 
> It's the forbidden zone, because if a new version of Redmine is released, we replace our Redmine with the new version and all changes are overwritten.
> That means that your code will be lost.

<!-- theme: info -->
>**Try to use existing code.**
>
>Before adding something, inspire yourself within the existing code base. Try to find an applicable existing solution, copy and modify it. On the other hand, avoid blind copy-pasting. When you use a method, you have to know what it does.
>
>Don't be afraid to explore the source code. Look at models, controllers or views and do it similarly - try to follow current syntax or code style. We want our codebase to be consistent.

<!-- theme: info -->
>**Comments**
>
>Comments are always welcome. Please use [YARD](https://yardoc.org/) docs style.
>
>Comments are required on complex codes. Otherwise you should write a *self-labeling code*. It means that a method's name describes what it does.

---

## Recommended editor settings

1. **Two spaces for indentation**. Use two spaces for indenting your code and swear an oath to never mix tabs and spaces.
2. **Unix line-endings**. Use UNIX-style newlines (\n), and a newline character as the last character of a file. Windows-style newlines (\r\n) are forbidden inside any repository.
3. **UTF-8** source coding only
4. No whitespaces
5. Spaces around curly braces in hashes
6. Spaces around curly braces in blocks
7. Spaces around arrows
8. Do not indent `when` cases
9. Do not indent after `private` / `protected`
10. Use double quotes `"` for strings, we prefer consistency

> [RubyMine](https://www.jetbrains.com/ruby/) takes care of most of them automatically. 

## Conventions

### ActiveRecord migrations

We are using two types of migrations:

1. Schema migrations, located in `db/migrate`, handles changes to the database schema.
2. Data migrations, located in `db/data`, which handles only changes of data.

### Overwriting templates / partials

One plugin can completely replace a template or a partial of the platform or a plugin which was **loaded before it**. This pattern can be used to implement changes, but only if you are rewriting more than 50 % of the content.

In case of small additions, create new or use existing [hooks](https://www.redmine.org/projects/redmine/wiki/Hooks). Hooks must be created in the appropriate repository, generally in the platform.

### Prefixes

Prefixes are preferred in order to avoid naming conflicts. For plugin `Abc` for example, use:

1. `AbcHelper` instead of `Helper`

2. `label_abc_sum:` instead of `label_sum:` in langfiles

### Localisation

All text must use `I18n` and must be translated at least to English. Wording should be clear for developers and follow english translation. 

Common prefixes:

`button_` `error_` `field_` `label_` `text_` `title_`

<!-- theme: warning -->
> Do not overwrite lang keys from Redmine or Easy Redmine.


### Indentation and naming

```ruby
# BAD
issues.map{|x| x.project.versions.map{|y| y.css_classes}}.flatten

# GOOD
issues.flat_map do |issue|
  issue.project.versions.map do |version|
    version.css_classes
  end
end
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

```html
 <div class="easy-query__heading-title_state_selected"></div>
```

For the `id` attribute use `_` as separator:

```html
<div id="easy_entity_import_dialog"></div>
```

> If you haven't found what you're looking for, you can look at community guidelines: 
>
>For RUBY [try this](https://github.com/rubocop/ruby-style-guide). 
>
>For RAILS [try this](https://github.com/rubocop/rails-style-guide).
