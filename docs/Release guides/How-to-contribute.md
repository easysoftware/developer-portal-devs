# How to contribute
Your work starts in Esko by having a task assigned to you. Either you already have one or you can pick one from sprint backlog, assign it to yourself and change the status to `Realisation`.

Update it on a daily basis to give the author and coworkers information about your progress or problems you are facing. Communication with other coworkers should also be done through the comments in the task. If you agree on something outside of the task, please note it in the comments (for example the results from the discussion through IM).

## Repositories
Based on the task, you will have to decide to which repository you will perform commits. Most of the repositories are in [Easy GitLab](https://git.easy.cz). In case you are working on an entirely new Plugin or a RYS you might need to create a separate repository.

>If you don't have access to a specific repository, you should ask a superior, it's probably a permissions issue.

## Push into the branch
Select a branch from which you will start your development process and create a new branch.
Depending on what you work on, either `platform`, `Easy plugins` or `RYSes`, you will have to use a different branching mechanism described in article about [branch naming]().

Checkout your new branch:
```bat
git checkout -b 1234-add_author_to_project
```
> Always use one branch for one issue in order to speed up delivery. An exception would be if the task requires changes in multiple repositories. In that case use the same name for branches in each repository.

When you are done, commit your changes into the branch.
```bat
git commit -m "refs #1234 add migration for author_id"
git commit -m "refs #1234 add select to projects form"
```
Following this naming convention will automatically link merge requests with tasks in Esko using the task ID.

### Keep this in mind
- Avoid non-descriptive commit messages as fix, patch or edit. Correct commit messages are important for review of logs. In case that issue is not provided, more detailed description is required.
- Do not commit & push node_modules folder! Its already in .gitignore, but not all RYS and plugins have updated .gitignore...
- Do not commit & push local cache or indexed database. Especially xapian DB in db/ folder
- Do not commit & push any garbage from you local environment - like packages, images, etc.

## Merge requests
> In order to maintain prepare your code for successful code review, read [this article](https://blog.palantir.com/code-review-best-practices-19e02780015f) about best practices.

Whenever you feel like you have progressed and want to validate your code with other team members, then go on and create a merge request.
If you want your merge request review as soon as possible, follow these rules:
- For that follow the same naming convention as used for branches - `ISSUE_ID-your_feature_in_a_few_words`. If there is no linked task, describe it in more detail in the description since this comes handy for QA department.
- Always consider using labels to describe the overall state of your task - labels like `Important` will be prioritized over `WIP` ones.
- Assign the merge request to Merge Request Pool [@gitmakers](https://git.easy.cz/gitmakers)
- Make sure that CI tests have succesfully passed
- Everything has been tested by QA
- Code has passed the code style requirements either for BE or FE

When you have met all of these criteria, great! Just way for **two approvals** up from reviewers!

> In case of integrating RYS into the platform, there should be prepared MR in the platform repository as well, where you  update the version that is compatible with the given platform version.
> If it's a new rys then it should be disabled in production, new plugin for RYS is required. (Just new easy_plugin with Gemfile definition)

## Changelogs
Whenever you merge new changes into code (RYSes, platform or plugins), you have to update changelog.
The easiest way to do is using easy_cli with the following command:

```bash
$ easy changelog TYPE --body "my message" --breaking
```
where
- `--body BODY` - Main body of the changelog entry
- `--breaking` - Specify if this can break things in other repositories

When you feel the changelogs are ready, use this command to merge your changelog entries:

```bash
$ easy changelog-merge
```