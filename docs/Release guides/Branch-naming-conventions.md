# Branch naming conventions

*In this article you will learn branch naming conventions used in Easy for platform, Easy plugins and RYSes.*

---

## Branch naming for platform

The following branches are default for the **Platform** repository. The flow goes as follows: `devel` -> if applicable `release-candidate` -> `bug-fixing` -> `master`.
Each of these stages has its specifics that have to be followed.

**Devel**
- Main development branch for new features
- Contains all features which are planned for release in the next major version
- All code here should be working and tests passing

**Release-candidate (optional)**
- Used to "freeze" and test new features before final release
- Only fixes for issues found during testing are accepted here
- Before the release of a major version, this branch is tested by QA, and their feedback is processed directly into this branch

**Bug-fixing**
- Bug fixes for released version
- Bugs which have to be tested or are not critical
- New features don't belong to this branch

**Master**
- Stable released version, code which is currently in production
- It's the most stable version of a plugin
- Only critical fixes are allowed

---

## Branch naming for Easy plugins and RYSes
The following branches are default for the **Easy plugins** and `RYS` repositories. Development of new features starts by creating a new branch from your selected branch. This schema is used for naming newly created features in Platform and Easy plugins.
- **next/major** - used for features to be released in the next major upate
    - **next/major-rc** - used for **code freeze** for features to be released in the next major upate
- **next/minor** - used for features to be released in the next minor upate
    - **next/minor-rc** - used for **code freeze** for features to be released in the next major upate
- **next/bugs** - used for features to be released in the next patch upate
- **master** - default branch to which is merged

If you are about to start working on a new feature for the next major release, you create a branch from `next/major`.

> Keep in mind that all of the `next/*` branches are protected and it's forbidden to push commits directly into them.

---

## Feature and build branches

### Feature branches
In some cases we use different convention for separate complex features, such as epics, which need to be built and we haven't decided in which version they'll be released.
For that you create a branch named `feature/*`.

In this case `*` represents the issue ID of your task and its description.

Branch named this way could read like this: `feature/425-add_author_to_project`.
This also allows us to easily combine branches from multiple repositories if they use the same branch name.

### Build branches
In some cases you need to deploy your code to be tested of interconnected features. For that we use branches named `build/*`, again `*` stands for issued ID and your description, just like this `build/425-add_author_to_project`.

These branches can be created on-demand for one purpose or they can be deleted and re-created with different features (like for Esko).

<!-- theme: warning -->
> Build branches are marked as protected and it's forbidden to push commits directly into them.