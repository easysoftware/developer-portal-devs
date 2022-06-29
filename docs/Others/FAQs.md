# FAQs

*In this article we try to explain the most frequently asked questions.*

---
## First steps

### `Bundle install` throws error when installing **mysql2 gem** `library not found for -lzstd` on my MacOS. What should I do? 

1. Check your `zstd` version that you have installed - `ls /opt/homebrew/Cellar/zstd/`
2. Install `mysql2` by running `gem install mysql2 -- --with-opt-dir=$(brew --prefix openssl) --with-ldflags=-L/opt/homebrew/Cellar/zstd/1.5.0/lib` using the correct version in the path at the end.

--- 
## Development

### What to do when EASY VUE is not propagating changes I've made?

Probable causes:

1. The browser is caching old assets (using hard reload in the browser)
2. Webpack failed to build a new version (for example, on failure of linter, fix it)
3. You haven't linked the `easy_vue` plugin before starting the rails server (restart rails server).
4. Webpack doesn't recognize changes in the filesystem (restart webpack)
5. Problem between the chair and the keyboard.

### How to prepare Merge Request?
1. Make sure you added link to task which is related to your merge request.
2. In description of your merge request add everything which may be helpful for others to understand why is your changes about.
3. Make sure you assigned your merge request to correct branch. See which branch to select [here](https://easysoftware.stoplight.io/docs/developer-portal-devs/bb960b922e208-branch-naming-conventions#branch-naming-for-easy-plugins-and-ryses)
4. Assign merge request to correct group/person
5. Make sure your pipeline is green and branch is up to date with the branch you want to merge your changes

### When is the right time for release? 

It depends.

If RYS is under heavy development - like a currently prepared feature which means lots of merge-requests there is no need to release RYS immediately after merge. But only once per "some period" after a big code-review.

On the other hand when you just fix langfile or do some small change (or maybe not so small) in RYS which is "stable" and there is no development - make sense to release it right after merge.

### How to properly release?
See our [release guidelines](https://easysoftware.stoplight.io/docs/developer-portal-devs/06d14d61da9fc-release-guidelines)