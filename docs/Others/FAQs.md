# FAQs

*In this article we try to explain the most frequently asked questions*

---
**TODO: Rozřadit do funkčních bloků, udělat osnovu všech otázek...**

## First steps

### `Bundle install` throws error when installing **mysql2 gem** `library not found for -lzstd` on my MacOS. What should I do? 

1. Check your `zstd` version that you have installed - `ls /opt/homebrew/Cellar/zstd/`
2. Install `mysql2` by running `gem install mysql2 -- --with-opt-dir=$(brew --prefix openssl) --with-ldflags=-L/opt/homebrew/Cellar/zstd/1.5.0/lib` using the correct version in the path at the end.

### What should I do if I have an installation error  cannot load such file `-- enumerator` when running easy project install? 

You are probably using ruby-2.7.5 or higher, switch to 2.7.2 and try again.

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


--- 

## Testing