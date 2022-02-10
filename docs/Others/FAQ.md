# FAQ

*In this article we try to explain the most common questions...*

---
**TODO: Rozřadit do funkčních bloků, udělat osnovu všech otázek...**

## First steps


### What is RYS? 


--- 
## Development

### What to do when EASY VUE is not propagating changes I've made?

Probable causes:

1. The browser is caching old assets (using hard reload in the browser)
2. Webpack failed to build a new version (for example, on failure of linter, fix it)
3. You haven't linked the `easy_vue` plugin before starting the rails server (restart rails server).
4. Webpack doesn't recognize changes in the filesystem (restart webpack)
5. Problem between the chair and the keyboard.


### When is the right time for release? 

It depends.

If RYS is under heavy development - like a currently prepared feature which means lots of merge-requests there is no need to release RYS immediately after merge. But only once per "some period" after a big code-review.

On the other hand when you just fix langfile or do some small change (or maybe not so small) in RYS which is "stable" and there is no development - make sense to release it right after merge.

### How to prepare merge-request? 



### How to properly release?