# Frontend Code Style

*Main goal of this document is to highlight rules and recommendations for creation nice and maintainable JavaScript code.*

---


## Default rules

Code style defined in this document is based on W3C standard conventions:
1. [***JavaScript Coding Conventions***](https://www.w3schools.com/js/js_conventions.asp)
2. [***JavaScript Best Practices***](https://www.w3schools.com/js/js_best_practices.asp)
3. [***Configure ESLint***](https://eslint.org/demo)

<!-- theme: info -->
>Exceptions from this coding styles will be described further.

---

## Rules strength

The rules described here come from three different strengths.

<!-- theme: warning -->
> **DO** or **DON'T** are mandatory for any Javascript code and it is a valid reason for rejection of a merge request if not followed.
>
>**PREFER** of **AVOID** is rules that should by followed but they are mandatory only for core implementation.
>
>**CONSIDERS** are not mandatory, but it makes the reviewer happy if they are followed.

---

## Naming

<!-- theme: warning -->
>**DO** name common variables with lowerCamelCase style. **DON'T** use snake_case or hyphens.
>
>**DO** name classes with UpperCamelCase.
>
>**DO** name variables with descriptive names, not by short variables like 'a'.
>
>**DO** use short names for integer cycle iterator - use i, j, k.
>
>**CONSIDER** using the $ prefix for jQuery element objects.
>
>**CONSIDER** naming Constant also with lowerCamelCase.

```javascript
var myImage = document.getElementById("my_image");
var $myImage = $("#my_image");

```

---

## Indentation + spaces

<!-- theme: warning -->
> **DO** use spaces instead of tabulators.
>
>**PREFER** using spaces around operators ( = + - * / ), and after commas.
>
>**PREFER** using 2 spaces as a 1 indentation step.
>
>**PREFER** using 4 spaces for continuation of previous line (if it is too long).


---

## Blocks

<!-- theme: warning -->
>**DO** use at most 4 levels of indentation. You can use control statements to flatten the structure:

```javascript
// BAD
var i,j;
for (i = 0; i < 10; i++) {
 if (i % 2 === 0) {
   for (j = 0; j < 10; j++) {
     if (j !== i) {
       // -- do something
     }
   }
 }
}
// GOOD
for (i = 0; i < 10; i++) {
 if (i % 2 !== 0) continue;
 for (j = 0; j < 10; j++) {
   if (j === i) continue;
   // -- do something
 }
}

```

<!-- theme: warning -->
>**AVOID** defining callback in callback.
>
>**AVOID** using very short blocks.

```javascript
var a = 5;
// BAD
if (a === 5) {
 return;
}
// GOOD
if (a === 5) return;

```

<!-- theme: warning -->
>**DON'T** use `if` statement without `{}`. 
>
>**AVOID** usage of lines longer than 100 characters

```javascript
  if (short_condition) break;
  if (short_condition) return variable||primitive;
  if (short_condition) continue;

```

---

## Code organization

*To facilitate faster page load, Javascript code should be compiled by pipeline and loaded and executed as late as possible.*

<!-- theme: warning -->
>**AVOID** .js files longer than 500 lines. The recommended line count is 300.
>
>**PREFER** using a declaration file for pipeline if multiple .js files are used. The declaration file should be named `{name_of_plugin}`.js and should be located in assets/javascripts in the plugin folder. Rules for are content of the file are specified by Rails Sprockets.

```javasciprt
/*
*   Example file
* = require external
* = require util
* = require_directory .
*/

```

<!-- theme: warning -->
>**PREFER** including .js files only in pages where the code is used. If code is necessary everywhere, use easy_extension.js hook: 

```ruby
class Hooks < Redmine::Hook::ViewListener
 def easy_extensions_javascripts_hook(context={})
   context[:template].require_asset('name_of_js_file')
 end
end
```

<!-- theme: warning -->
>**PREFER** using `window.EASY` as a only global variable where to define functions.
>
>**PREFER** defining functions only in .js files.
>
>**CONSIDER** specifying javascript including tag in `:body_bottom` part of layout.
>
>**AVOID** calling any functions in .js files, especially `$(document).ready()`. An exception is closure function `(function(){})();` for separation of local variables from global scope.
>
>**PREFER** to call init functions being in-lined in HTML.

The recommended way to organize Javascript code is to define functions in .js file as a part of `window.EASY.{name_of_plugin}` namespace and in HTML include `<script>` tag with `EASY.schedule.main` or `EASY.schedule.late` calling previously defined functions.

---

## Code execution

Due to performance optimization, `jQuery` was removed from the header and is now loaded as a deferred script. So any `jQuery` calls in inlined `<script>` will fail. Usage of `EASY.schedule` functions is recommended.

<!-- theme: warning -->
>**PREFER** registering an init function to any `EASY.schedule.main`, `EASY.schedule.late` or `EASY.schedule.require` function.

`EASY.schedule.main` and `EASY.schedule.late` accepts priority as a second parameter. Lower the priority, the latter function will be executed. 

`EASY.schedule.require` accepts function as a second parameter, which has to be fulfilled before the execution of the init function. 

```javascript
/** EASY.schedule.late - For functions, which should be executed after several loops after "DOMContentLoaded" event */
EASY.schedule.late(function() {
 // executed after EASY.sidebar.init();
 EASY.sidebar.loadData();
}, -5);
/** EASY.schedule.main - for functions, which should be executed right after "DOMContentLoaded" event. */
EASY.schedule.main(function() {
 EASY.sidebar.init();
});

```
and
```javascript
/** EASY.schedule.require - For functions, which should wait for [prerequisite] fulfillment */
EASY.schedule.require(function(sidebar) {
 sidebar.init();
},function() {
 return window.EASY.sidebar;
});
/** Advanced usage */
EASY.schedule.require(function(counter, $) {
 $("body").on("click", counter);
}, 'Counter', 'jQuery');
EASY.schedule.define('Counter', function () {
 var count = 0;
 return function () {
   console.log("Count: " + count++);
 }
});

```
Also in Ruby:

```ruby
late_javascript_tag("showFlashMessage('error', '#{@error.html_safe}');", priority: 5)
```

---

## Performance issues

To make the page perform fast, these rules have been set:

<!-- theme: warning -->
>**AVOID** binding context-less event listeners such as `$(document).on()`. Make the binders as narrow as possible.
>
>**PREFER** to pass a jQuery object as an argument rather than HTMLElement or selector.
>
>**DON'T** use multiple calls `$('my_selector')` where one is sufficient.

```javascript
// BAD 
$(".my-image").height($(".my-image").width());

// GOOD
var $myImage = $(".my-image");
$myImage.height($myImage.width());

```
<!-- theme: warning -->
>**AVOID** layout thrashing.

```javascript
// BAD
$table.find("td.first_column").each(function() {
 var $this = $(this);
 $this.next().width($this.width());
});
// GOOD
var widths=[];
$table.find("td.first_column")
.each(function() {
 widths.push($(this).width());
})
.each(function(index) {
 $(this).next().width(widths[index]);
});
// BEST 
$table.find("td.first_column").each(function() {
 EasyGem.readAndRender(function() {
   return this.width();
 }, function(width) {
   this.next().width(width);
 }, $(this));
});

```

---

## Architecture

### Namespacing in `easy_extensions`

<!-- theme: warning -->
>
>**DO** use short and expressive names. Keep in mind that the whole path is a name.
>
>**DO** make an EASY namespaces for every plugin with code.
>
>**DO** make an EASY namespaces for every common utility.
>
>**DO** make an EASY namespaces to aggregate little pieces of code.
>
>**DON'T** make EASY namespaces for subcategories.
>
>**PREFER** namespacing in file namespace.js.

```javascript
// GOOD
window.EASY = window.EASY || {};
EASY.query = EASY.query || {};
EASY.gantt = EASY.gantt || {};
EASY.dragAndDrop = EASY.dragAndDrop || {};
EASY.utils = EASY.utils || {};

// BAD
EASY.easyQuery = EASY.easyQuery || {};
EASY.easyGanttRow = EASY.easyGanttRow || {};
```

### Structure of modules

Predict the complexity of your task and choose the proper pattern from the ones shown below.

<!-- theme: warning -->
>**DO** use a little piece of code for a simple task.
>
>**DO** use proper namespacing.
>
>**PREFER** using closure for code separation.

```javascript
// GOOD
(function(){
 /**
 *  @param {jQuery} $menuElement
 */
 EASY.utils.toggleSubMenu = function($menuElement) {
   $menuElement.find('.subMenu').toggle(); 
 };
})();

// BAD
function toggleSubMenu(menu) {
 $(menu).find('.subMenu').toggle(); 
}

```

### **Middle complex usages**

<!-- theme: warning -->
> **DO** use `*jQueryUI*` widget for a middle complex usages, e.g. side menu with single ajax call `*jQueryUI*` widgets guidelines will be provided later.

### **Complex usages**

<!-- theme: warning -->
>**DO** separate models and views for complex usages, e.g. Agile.
>
>**DO** stack repaint on repaint frame.
>
>**DO** use mustache for client-side rendering.
>
>**DO** publish model changes by observer-subscriber pattern.
>
>**AVOID** to passing view reference to model.
>
>**PREFER** usage of observable `easyClasses.ActiveClass` and `easyClasses.ActiveCollection` for publishing events.
>
>**PREFER** using [***JSDoc***](https://jsdoc.app/), especially in model classes.
>
>**PREFER** new keywords, for instance creations.
>
>**PREFER** usage of `easyClasses.Widget` (*namespace will be moved to EASY in the future*).
