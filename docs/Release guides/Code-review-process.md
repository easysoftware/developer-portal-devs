# Code review process

*In this article we will look into the code review process through the eyes of the reviewer.*

---

## Code review best practices

Before conducting code reviews please read [this article](https://blog.palantir.com/code-review-best-practices-19e02780015f) about code review best practices.

## Essential Practices for Merge Requests (MRs)

* Assignment of Reviewers:

Reviewer Pool: Merge Requests (MR) should be assigned to a 'reviewer pool' rather than individual reviewers. This approach ensures a broader perspective and more efficient review process, as it allows any available and qualified reviewer within the pool to pick up the MR for review.

* Pipeline Status:

Mandatory Pipeline Success: A crucial requirement for any MR is the successful completion of all associated pipeline checks. MRs with failed pipeline checks will not be considered for merging. This rule is non-negotiable and ensures the integrity and stability of our codebase. It is imperative that all contributors ensure their code passes all pipeline checks before requesting a review.
 
* Handling Draft MRs:

Draft Status and Merging: MRs marked as 'Draft' are not eligible for merging. The primary purpose of a Draft MR is to seek early feedback. Contributors are encouraged to use this status for preliminary reviews and discussions. However, it's important to note that while in Draft status, the MR is solely for feedback purposes and will not be merged until it is marked as ready for review and meets all other criteria.

<!-- theme: warning -->

> #### If pipeline fails, fix it !
>
> If its failing for any reason, ask technical support or guild. But pipeline MUST passed.

---

## 'Code' of a good reviewer

As a reviewer, these are the guidelines you should live by:

- The goal is to increase the codebase quality so be thorough!
- Check if the code match our code style guidelines for either [FE](https://developers.easysoftware.com/docs/developer-portal-devs/ZG9jOjM5NzgxNzUy-frontend-best-practices) or [BE](https://developers.easysoftware.com/docs/developer-portal-devs/ZG9jOjM5NzgxNzU2-backend-best-practices).
- Be nice to the developer who submitted the MR.
- Ask questions if you are not sure about something.
- If possible, do the code review with the developer who created the MR.
- Spend **one hour a day** doing code review.
- If MR is assigned to you, you are responsible for doing the code review.

---

## Reviews of BE and FE code

In case you are BE developer you should not review FE code and vice versa.
Keep noted that FE developers should tag their MRs using tags like `vue` or `javascript`, depending on the case.
BE developers on the other hand tag their code with tags such as `bug`, `feature` or `change`.

---

## People responsible for merging

All **developers** are responsible for merging and releasing the Merge Requests.

<!-- theme: warning -->
> Remember the [general rules](https://developers.easysoftware.com/docs/developer-portal-devs/ZG9jOjM5NDMyMjQ4-how-to-contribute#merge-requests) before you merge.

---

## Common errors to watch out for

### New external dependencies

External dependencies should always be locked to a fixed version.
```ruby
# example 
gem "audited", "5.1.0"
```


### Rails find

- Calling `.find` should be rescued in case it throws an exception.

```ruby
begin
  @issue ||= Issue.find(params[:id])
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
