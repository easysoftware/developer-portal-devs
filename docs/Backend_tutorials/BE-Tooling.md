# Backend tooling 

*In this article we will show you how we keep our code readable and understandable*

***
### Prerequisites
You have finished the first steps settings according to [this guide](
https://easysoftware.stoplight.io/docs/developer-portal-devs/docs/Getting_started/First-steps.md)

---

## Tooling

We want our code to be readable and understandable and, since our codebase is large and complex, it's important to follow some basic rules when writing code. To enforce these rules, we use [RUBOCOP](https://rubocop.org/).

### Installation and usage

You need to have `easy_style` gem in your `Gemfile/gemspec`, most projects should already have this one. If not, you can put it there.

To do so for platform and plugins, put this into `Gemfile`:

```ruby
gem 'easy_style'
```

For RYS plugins put this into `rys_name.gemspec`:

```ruby
Gem::Specification.new do |spec|
   spec.add_development_dependency 'easy_style'  
 end

```

### Dealing with offenses

#### Fixable offenses

Fix them or generate `.rubocop_todo.yml`, see `Notes` and known issuees at the end of this article. Also [check this article](https://docs.rubocop.org/rubocop/configuration.html#automatically-generated-configuration).

#### Unfixable offenses

The code will probably look differently, but you can try to refactor it.

Can you also try to [disable the cop](https://docs.rubocop.org/rubocop/configuration.html#disabling-cops-within-source-code) within source ode. 

#### Integration with your IDE

Most IDEs have some kind of support for RUBOCOP such as: 

1. Highlighting offenses
2. Suggesting fixes


#### Install your Rubymine

1. Make sure this is checked: `*Preferences >> Editor >> Inspections >> Ruby | Gems and gem management | RuboCop*` (you can type *Rubocop* to search box in preferences)

2. Make sure all gems are installed in your project, even for rys and plugin repositories >> run `bundle install/update` if not

> You can use Problems tool to see a list of rubocop offenses in a given file:
>
>*View >> Tool Windows >> Problems*.

>Activate suggestions for autocorrect feature by pressing `Alt + Enter`.

#### Notes and knows issues

For CI `inherit_from: .rubocop_todo.yml` is added to `.rubocop.yml` so we don't have to fix all old offences at once.

For generating `.rubocop_todo.yml` use this:

1. rys: 
```
alias cop_gen_rys="echo \"source 'https://rubygems.org'\" > gems.rb && echo \"gem 'easy_style'\" >> gems.rb && bundle update && bundle exec rubocop --auto-gen-config --auto-gen-only-exclude --exclude-limit 10000 && git restore .rubocop.yml && git restore gems.rb && bundle update"
```

2. rys regenerate:
```
alias cop_regen_rys="echo \"source 'https://rubygems.org'\" > gems.rb && echo \"gem 'easy_style'\" >> gems.rb && bundle update && bundle exec rubocop --regenerate-todo && git restore .rubocop.yml && git restore gems.rb && bundle update"
```

3. platform and plugins:
```
alias cop_gen_platform="echo \"source 'https://rubygems.org'\" > Gemfile && echo \"gem 'easy_style'\" >> Gemfile && bundle update && bundle exec rubocop --auto-gen-config --auto-gen-only-exclude --exclude-limit 10000 && git restore .rubocop.yml && git restore Gemfile && bundle update"
```

`Bundle exec rubocop` can have different result on your computer than on CI (*this will be hopefully resolved soon*), to achieve the same result you can do this:

- In rys repos: replace content of `gems.rb` with` source 'https://rubygems.org'` and `gem 'easy_style'`
- In platform: replace content of Gemfile with `source 'https://rubygems.org'` and `gem 'easy_style'`

