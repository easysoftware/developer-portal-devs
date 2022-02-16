# Code review process
*In this article we will look into the code review process through the eyes of the reviewer.*

---

## Code review best practices
Before conducting code reviews please read [this article](https://blog.palantir.com/code-review-best-practices-19e02780015f) about code review best practices.

---

## 'Code' of a good reviewer
As a reviewer, these are the guidelines you should live by:
- The goal is to increase the codebase quality so be thorough!
- Check if the code is on par with code style guidelines for either [FE]() or [BE]().
- Be nice to the developer who submitted the MR.
- Ask questions if you are not sure about something.
- If possible, do the code review with the developer who created the MR.
- Spend **one hour a day** doing code review - log this time under [Development project](https://es.easyproject.com/projects/lwc/).
- If MR is assigned to you, you are responsible for doing the code review.

---

## Reviews of BE and FE code
In case you are BE developer you should not review FE code and vice versa.
Keep noted that FE developers should tag their MRs using tags like `vue` or `javascript`, depending on the case.
BE developers on the other hand tag their code with tags such as `bug`, `feature` or `change`.

---

## People responsible for merging
Each repositry has different person or people responsible for the review code to be merged.
- **developers** are responsible for `feature/*` branches and RYS repositories
- for platform merges is responsible [Petr](https://git.easy.cz/petr)

---

## Common errors to watch out for

### Rails find

- Calling `.find` should be rescued in case it throws an exception.

```ruby
begin
  @issue = Issue.find(params[:id])
rescue ActiveRecord::RecordNotFound
  render_404
end
```

- Alternatively use`.find_by` without the need to rescue. In this case you must handle nil.

```ruby
@issue = Issue.find_by(id: params[:id])
@project = @issue&.project
```

### Nils

The most common problem. Always keep in mind that a variable or a parameter could be nil.

```ruby
# On Hash
hash[:key1]
hash[:key1][:key2]

# On Array
[].flatten!.map(&:to_i)

# On model
@issue.assigned_to.name
```

### Reload in after_save

Method `.reload` should not be used in callbacks, because it resets attributes in [Active Model Dirty](http://api.rubyonrails.org/classes/ActiveModel/Dirty.html).

### Magic constants

Don't use numbers or strings as status/type/property identifiers. Define constants that describe their meaning, and use them throughout the code in place of some meaningless symbols.