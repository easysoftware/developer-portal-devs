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
These releases are released weekly from branch `bug-fixing`
- Trials are not updated from bug-fixing.
- Clients - Cloud clients are always up-to-date, so when new bug-fixing is released, the update of clients is announced and scheduled.

All the changes that should get into the next patch version need to delivered till Wednesday's noon.
> Example: You push patch fixes on Wednesday 1st. Then there is a week of testing and your code will get into production on Friday 10th.

### Minor releases
These releases are released monthly from branch `release-candidate`.
The release is scheduled for **22nd day of the month**. Code freeze occurs on the **15th day of the month**.
Between 15th and 22nd is conducted end-to-end testing.

### Major releases
These are relased twice a year within no specific timeframe and deliver large features that were announced by Easy's marketing team. Major updates are released from branch `release-candidate`.
Before every major relase there is at least a month when focus on:
- marketing campaign
- extensive testing (end-to-end, integration)
- training of sales staff
- training of our support department

---

## RYS releases
RYSes are released and distributed through our [Gem server](gems.easysoftware.com). In order to get RYS there, it has to go through code review and be merged to `master` branch. In this case, change the RYS version accordigly to the [semantic versioning](https://semver.org/) used in Easy.

> Always keep in mind that Easy products use RYSes in specific major and minor versions. This is due to Gemfile specification and thus should be patch versions used without any changes.

### Guidelines to follow to successfully release RYS
Follow these guidelines for successful release:
- passed code review and merged into RYS `master` branch
- changelog has been updated accordingly through [easy_cli](https://git.easy.cz/internal/easy_cli#-changelog-merge)
- version has been updated accordingly
    - **major** = blocking changes
    - **minor** = smaller changes / new features and migrations
    - **patch** = fixes without migrations
- RYS released to [Gem server](gems.easysoftware.com)