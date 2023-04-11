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
1. **Increase version**, according to the [semantic versioning](https://semver.org/)
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

> Keep in mind that Easy products lock RYS versions, usually to a minor version, so you should increase the version in relevant Gemfile in platform if needed (when releasing minor version and above, patch versions should be fine).
