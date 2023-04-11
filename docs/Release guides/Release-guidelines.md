# Release guidelines

*In this article we will look closer at release guidelines for platform, Easy plugins and RYSes.*

---

## Release naming

All releases use [semantic versioning](https://semver.org/).

**MAJOR** level - changes are release specific
- whole new features
- refactoring
- breaking changes

**MINOR** level - includes new small features
- migrations
- minor changes of internal logic

**PATCH** (or BUG-FIX) level - only for bug-fixing or harmless changes and thus **cannot include migrations and breaking changes**
- documentation
- readme

---

## Platform and plugins releases
Releases for platform and Easy plugins are triggered manually, through www.gitmakers.com.

### Patch releases
These releases are released weekly from branch `next/bugs`
- Trials are not updated from next/bugs.
- Clients - Cloud clients are always up-to-date, so when new next/bugs is released, the update of clients is announced and scheduled.

All the changes that should get into the next patch version need to delivered till Wednesday's noon.
> Example: You push patch fixes on Wednesday 1st. Then there is a week of testing and your code will get into production on Friday 10th.

### Minor releases
These releases are released monthly from branch `release-candidate`.
The release is scheduled for **last week of month**. Code freeze occurs around the **15th day of the month**.
After that there is time for end-to-end testing.

### Major releases
These are released once a year within no specific timeframe and deliver large features that were announced by Easy's marketing team. Major updates are released from branch `release-candidate`.
Before every major release there is at least a month when focus on:
- marketing campaign
- extensive testing (end-to-end, integration)
- training of sales staff
- training of our support department

---

## RYS releases
RYSes are released and distributed through our [Gem server](gems.easysoftware.com) or [Ruby Gems](https://rubygems.org/). In order to get a RYS there, it has to go through code review and be merged into the `master` branch. Once it's in `master` and you want to release it you need to (locally):
1. **Checkout into and pull the latest `master`**
2. **Increase version**, according to the [semantic versioning](https://semver.org/)
   - using EasyCLI
     ```
     easy increase-version
     ```
   - manually - inside `/lib/RYS_NAME/version.rb`
2. **Merge changelogs**
   - using EasyCLI
     ```
     easy changelog-merge
     ```
   - manually - copy changes from all files inside `/changelogs` into appropriate sub-sections inside the `Unreleased` section of `CHANGELOG.MD` file in RYS ROOT
3. **Commit and Push the changes**
4. **Release**
    > Most RYSes have _release_ as a part of their Gitlab-ci pipeline for `master` and will be released automatically _once the pipeline passes_.
   - using EasyCLI
     ```
     easy gems-release
     ```

> Keep in mind that Easy products lock RYS versions, usually to a minor version, so you should increase the version in the relevant `Gemfile` in platform if needed (when releasing minor version and above, patch versions should be fine).

> A git **tag** in format `v2.5.5` with the released version is created on release.

### Releasing an older version of RYS
Sometimes you want to add changes to an older version, typically if there is a new one released for the next major platform version, but the previous one is still being developed/bugfixed before the major comes out.

This gets a little complicated so stay with me. Let's say the latest version in `master` is `2.0.0` and you need to get a bugfix into `1.5.3`. Follow these steps:
1. **Checkout a tag** with the version you want to update
    ```
    git checkout tags/v1.5.3 -b 111111_my_new_bugfix_branch
    ```
2. Make your changes, write a changelog, commit, push
3. **Create a merge request** marked as **_draft_** (DON'T MERGE IT!). It's only for code review and CI pipeline
4. Once it's ready, follow the standard RYS release steps above, but **stay in your branch (NOT `master` this time)**
5. Hurray! New bugfix version `1.5.4` is out. Now it's time to clean up
6. **Checkout into master** and **create a new branch**, ideally in the format `forward_1_5_4`
7. Your original branch will never be merged into `master` so you need to **grab and forward your changes**, You can do it manually but I recommend downloading a plain diff from your "fake" MR and applying it to your new branch, and:
   - revert version change (keep `2.0.0`)
   - clean up changelog - there might be a conflict, might not, just make sure the changes in there follow chronological order
8. Write a changelog
    ```
    easy changelog changed --body "forwarding changes from 1.5.4"
    ```
9. Commit, push, create a merge request into `master`
10. Once it's merged, follow the standard RYS release steps above, but be careful to
    - raise the appropriate version, at least the one you raised before
    - sometimes there are other changes in `master`, in that case communicate about the release with their authors
