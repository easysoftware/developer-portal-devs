# Frontend best practices

*The main goal of this document is to explain basic guides and workflows to successfully develop the ER interface. ER UI utilizes the power of SASS to make the code reusable, extendable and easier to maintain into future.*

---

## Inspiration

When developing UI for ER developer should get a basic overview of the following concepts of modular UI design:

1. ***[Atomic design](http://atomicdesign.bradfrost.com/chapter-2/)*** is a well explained concept about how the final design comes together from each and every particle.  
2. ***[Object Oriented CSS](www.smashingmagazine.com/2011/12/an-introduction-to-object-oriented-css-oocss/)*** is a well accepted abstract methodology of implementing Atomic design onto stylesheets.
3. ***[Block Element Modifier](https://en.bem.info/)*** is a concrete implementation of OOCSS particularly known for its naming conventions.  
4. ***[SASS](http://sass-lang.com/documentation/file.SASS_REFERENCE.html)*** is CSS preprocessor that makes everything above fit together easily.

---

## Core file structure

The root of the stylesheets (namely the core) is located at:

```json
*-----------------------------------------------------------------------*
/plugins/easyproject/easy_plugins/easy_extensions/assets/stylesheet/
*-----------------------------------------------------------------------*

```
Here you can find the root `*.SCSS` files that link up the whole thing together. Besides files directly located in this folder, most of the code can be found in the SCSS sub-folder. The explanation follows:  

```json
--- easy_design.scss                    - main root link
--- typography.scss                     - contains only includes to render typography related styles into separate sheet that can be used in stand alon plugins. 
                                        - Like WYSIWYG editors etc.
--- easy_icons.scss                     - separate sheet to render icon related styles

--- scss/                               - folder to contain most of the styles. 
                                        - Files directly located in this folder contain variables that are used to generate the remaining stylesheets. 
                                        - ER utilises RoR Asset Pipeline that compiles the whole thing together. 
                                        - Using Easy Theme Designer plugin user can manually overide values asigned to these variable via interface in plugin settings. 
                                        - We belive that most of the customisation can be done with very little variables, the rest being derived from them. 
                                        - To keep that idea split the definition file into multiple, so we can hide the complexity at the first glance.
--- scss/variables.scss                 - sets the very basic parameters for customisation and loads more detailed vars
--- scss/_variables_primary.scss        - sets derived parameters that can be changed in Easy Theme Designer advanced settings
--- scss/_variables_secondary.scss      - sets more complex vars that a developer might change but are not subjected to user control
--- scss/_common_assets.scss            - collection of all reusable functions, mixins and placeholders from ER UI that can be loaded into any other file, on its own. 
                                        - It has no output, just makes those objects available
--- scss/_variables_secondary.scss      - sets more complex vars that a developer might change but are not subjected to user control
--- scss/_variables_icons.scss          - defines class names and character code as a key:value pair
--- scss/_functions.scss                - collection of ER SASS functions

--- scss/mixins/                        - collection of abstract particles - mixins and placeholders, that on their own have no output into actual stylesheets
--- scss/components/                    - collection of ER core styles - defines part of the actual output.  
                                        - It can be seen as collection of molecules in perspective of atomic design
--- scss/layouts/                       - collection of concrete implementatios of specific pages/layouts - pages from atomic design
--- scss/extensions/                    - collection of styles for specific ER extensions - pages from atomic design
--- scss/functions/                     - collection of third party functions

```

---

## Coding guidelines

Naming is often referred to as one of the biggest unknowns of programing. Picking the right class name is vital for several reasons:

1. **Consistency** - just by looking at HTML, even non-experienced developers should be able to say what is going on here 
2. **Performance** - vaguely specific classes lead to overuse of child selectors which negatively affects browser rendering speed 3.Ease of use - SASS provides nesting features which can greatly simplify code one needs to write and make it more modular

The **BEM convention** solves the above mentioned points: BEM is an abbreviation for *Block-Element-Modifier* which comes from the realization that every element on a page is a part of some higher block and also can be subject to a modification. So let's take an illustrative example of search: Search would normally consist of a text field and a button, both are HTML elements but also parts of a common search block, something like this:

```html
<form class="search">
  <input type="text">
  <button type="submit">
</form>

```

Now let's say we want to target the input and button an **apply some styling** to them:

```css
.search input, .search button{
  ...
}

```

These are selectors using parent - children relationships and are quite difficult for a browser to pull off, targeting the element with its own class is much faster. So what class should we put there? 

That is where naming conventions come in hand. To fast-forward, here is how the Block - Element relationship is working out:  

```css
<form class="search">
  <input type="text" class="search-input">
  <button type="submit" class="search-button">
</form>

.search-input, .search-button{
  ... //much faster selector
}

```

Now let's imagine that button should be disabled until there is some text in the text field. Again, we need to add some classes to distinguish between those two states. With **BEM** in mind, it would look like this:

```css
<form class="search">
  <input type="text" class="search-input">
  <button type="submit" class="search-button search-button-disabled">
</form>

.search-button-disabled{
  ... //again no children selector = fast
}

```

You might ask why not to add just the "`disabled`" class, because:  

```css
.search-button.disabled{
  ... //although no children, still not as fast as above
}

```

Using **SASS** nesting features enables us to write styles in a clean and extendable manner:

```sass
.search{
  ...
  &-input{
    ...
  }
  &-button{
    ...
    &-disabled{
      ...
    }
  }
}

```

Which compiles into a nice, fast **CSS** code where all the selectors have the same specificity:  

```css
.search{
  ...
}
.search-input{
    ...
}
.search-button{
    ...
}
.search-button-disabled{
  ...
}

```

<!-- theme: warning -->
>**REMEMBER!**
>
>Use Block-Element-Modificator to form class names.
>
>Use "-" hyphens as a separator.



### CSS Performance

Performance-wise, what is even worse than long-descendant selectors, are repetitive applications of the same style rules, or too many overrides. **SASS** has a neat directive @extend that is of great help when tackling these. Imagine that we have an element in class ="copy" that we want to look at exactly as a paragraph. We can either copy all style declarations, but that would be tedious work with bad performance influences, or we can use @extend:

```css
p{
  ...
}

...

.copy{
  @extend p;
}

```

Would compile into:

```css
p, .copy {
  ...
}

```

That means that a browser will first match all elements with a selector and then apply the rules to them all in one run.



### Mixins vs. Placeholders

In SASS we have two quite similar, yet quite different structures: 
**Placeholders** and **Mixins**.
**Mixin** is a "macro" that can be passed input arguments:

```css
@mixin text ($color: black){
  color: $color;
}

```
**Placeholder** on the other hand takes no arguments:

```css
%text{
  color: blue;
}

```

So, a placeholder is a kind of selector without actual output on its own, just sitting there, waiting to be extended.

The biggest difference between mixin and placeholder is how the rules would get rendered:

```css
.a{
  @include text;  // use of @mixin
}

.b{
  @include text;  // use of @mixin
}

.c{
  @extend %text   // use of placeholder
}

.d{
  @extend %text   // use of placeholder
}
```

compiles into:

```css
.a{
  color: black;
}

.b{
  color: black;
}

.c, .d{
  color: blue;
}

```

So mixins are great for customisation, but will always duplicate the rules they contain, which can have a bad performance influence.

For most UI elements, both mixins and placeholders are available. Then, if you want to make a new customisation, mixin should be used. When you just want to make an element look like another use @extension of placeholder.

---

## Layouts

*From a high perspective, the Easy Redmine application layout can be broken down into the following blocks:*

### Top menu

Top menu consist of: ***main menu, search area, user icon, menu more***.

Top menu provides the main mean of navigation in the application. 

### Service Bar

Consists of widgets that need to be at hand to any user at any moment. To preserve space, the service bar contains only icons that, when clicked, evoke separate panel. Such a panel would usually consist of a header, content and footer. Dependent on widget functionality, the header would usually contain search (i.e. search contacts), some blocks in content and footer actions ()

The base structure for the service bar widget is as follows:

```html
 <div id="easy_servicebar_component_body">
    <div>
        Header comes here ...
    </div>
    <div class="servicebar-content">
        Header comes here ...
    </div>
    <div class="servicebar-actions">
      <a class="icon icon-add button-positive" href="#">Arrrgh!</a>
    </div>
 </div>

```

### Sidebar

Sidebar contains actions relevant to current view, secondary info and other links that have relevancy to current page. Sidebar can be divided into following blocks:

**Primary actions** - contains the most important action on the page - one or two at max! Use to populate:

```html
content_for :easy_page_layout_service_box_primary_actions do
```
**Service box**- contains the rest of the actions on a given page and sometimes a quick statistics overview. Use to populate:

```html
content_for :easy_page_layout_service_box do
```

**Servicebar content** - contains the rest of the sidebar. Use to populate:

```html
content_for :sidebar do
```

---

## UI Components

Since forms are the only native way to input user information, they are heavily used throughout the application.

You can either write forms on your own, following lower guides, or you can leverage **EasyLabeledFormBuilder** as it will take care of all required syntax and semantics.

Not all forms are visible - some only serve as a backbone for ajax-triggered events. Those forms that are visible to the user should be emphasized, or that we use `class="form-box"` on the form element, which applies styling that makes form sand out more.

### User inputs

Individual inputs automatically receive some global styling. All `<input>`, `<textarea>` and `<select>` elements are set to `width: 100%;` except textarea which has `max-width: 350px;` by default.

```html
<input type="text" value="input..."/>
<select type="text" value="input...">
    <option>option 1</option>
</select>
<textarea></textarea>
```

This rule is overridden in the following cases:

1. When  element has a size attribute set, it overrides the default width behavior and sets the width according to the size specified. 
2. Since the size attribute on a select element controls how many of its options are visible (i.e. vertical size), ER uses `class="inline"` to set a given select element to expand only to the maximum width of its options. *An element with `class="block"` will expand for the whole width of its parent.

### Checkboxes and radios

Inputs of `type="checkbox"` or `type="radio"` should always be placed inside their respective label. That is important for both text and the respective input would break the line at the same time. The label should have resppective `class="checkbox"` or `class="radio"`.

```html
 <label class="checkbox">
  <input type="checkbox"/>
  Im a checkbox
</label>

<label class="radio">
  <input type="radio"/>
  Im a radio
</label>
```

### Tabular form

For positioning of labels next to the form elements, both **Redmine** and **Easy Redmine** use a tabular layout. 

`Class="tabular"` should be positioned on any of the parents of the form input elements that should be rendered in the tabular layout. Each line of such elements should be wrapped in separate element to create space for labels. Then every first label child element in every paragraph will be positioned next to the input. 

Tabular layouts originate in **Redmine**. Their syntax is thus predefined: Tabular consists of a set of elements as rows for tabular form. Labels in such rows have special positioning - being left to the element and with right aligned text.

```html
<form class="tabular">
  <p>
    <label>Label</label>
    <input type="text"/>
  </p>
  <p>
    <label>Label</label>
    <input type="text"/>
  </p>
</form>
```

In the tabular layout, radios and checkboxes break the layout rule of the label being positioned on the left of the input. That is because such elements have significantly longer labels than the input itself and doing otherwise would be just a waste of space. Thus, it is recommended to group similar checkboxes and radios into groups.

```html
<form class="tabular">
  <p>
    <label class="radio">Label</label>
    <label class="radio">
      <input type="radio"/>
      Im a radio
    </label>
  </p>
</form>

```

We can even spice it up a little by arranging such a group into aligned columns with `class="cols-X"` where X is a number from range 1 - 6.

```html
<form class="tabular">
  <p class="cols-2">
    <label class="radio">Label</label>
    <label class="radio">
      <input type="radio"/>
      Im a radio
    </label>
  </p>
</form>
```

### Help block

When some explanation of the form fields is in order, just append them with elements.  

```html
<em class="help">This is a cool hint!</em>
```

Explanations following elements with class="block" or having it set on their own will have block behavior, which means they will span 100% of the's width all the time.

### Form actions

Each form used to input user information should have a set of actions used to perform certain actions with form data (*i.e. Submit, Create, Cancel, Clear, …*). Elements performing that, should have corresponding appearance that is achieved by using the proper class name (*i.e. button performing submit action should have `class="button-positive"` among other classes see Buttons*) `<button>` and `<input type="button">` that dont have any classes, will be automatically styled as default button.

Forms that are meant to gather user input should be distinguished from the other elements on the page, so the user context will be changed (*from reading to writing*). That is achieved by giving a `class=”form-box”` to it. This will not only change its appearance but also its behavior (*see form-actions and JS form enhancements*).

For enhanced user experience, form actions should be accessible to the user all the time (*which is a problem especially with really long forms*), thus **Easy Redmine** applies styles to them that make it "sticky", meaning that form actions follow the user all the  time as he scrolls the form. That is simply achieved by enclosing form actions in a wrapping element with the `class="form-actions"` set. 

For backwards **Redmine** compatibility, it is preferable to use `<p>`as the wrapping element (*especially for the tabular layout*) along with the `class="buttons"`.

```html
<form class="form-box">
  ... form content ...
  <p class="form-actions buttons">
    <a class="button-positive" href="#">Submit</a>
    <a class="button" href="#">Cancel</a>
  </p>
</form>

```

### Grid system

ER includes a responsive up to 10 columns grid. For at least so-so backwards compatibility, we use a splitcontent system laid out in default **Redmine** as a base of the system. splitcontent consists of 2 columns each spanning 50% width, `splitcontentleft` floating left and `splitcontentright` floating right.

```html
<div class="splitcontent">
  <div class="splitcontenleft"></div>
  <div class="splitcontentright"></div>
</div>

```

It will produce content split into two columns, aligned to the right and the second to the left. It is important to note, that columns always need to be wrapped in `splitcontent` parent, as this adjust for the gutters. Such a layout can be further enhanced with size classes to achieve uneven sized.  

```html
<div class="splitcontent">
  <div class="splitcontenleft size-7"></div>
  <div class="splitcontentright size-3"></div>
</div>
```

Which will adjust the width of the first column in the set to 70% and the second to 30%. More columns can be added to the grid by simply adding more `splitcontentleft` or `splitcontentright` elements to the `splitcontent` element.

```html
<div class="splitcontent">
  <div class="splitcontenleft size-3"></div>
  <div class="splitcontentright size-2"></div>
  <div class="splitcontenleft size-3"></div>
  <div class="splitcontentright size-2"></div>
</div>
```

It is important to bear in mind that the total width of the columns should not exceed 10. 

Also, note the distinction between the behavior `splitcontentleft` and `splitcontentright` as it affects the ordering of the elements in the grid. This is especially important when planning the behavior of the grid between mobile and desktop devices, as on desktop devices the grid elements collapse underneath each other in the order they appear in the HTML of the document.  

```html
<div class="splitcontent">
  <div class="splitcontenleft size-3"></div>
  <div class="splitcontentright size-2"></div>
  <div class="splitcontenleft size-3"></div>
  <div class="splitcontentright size-2"></div>
</div>
```

Grids in general can be sub-gridded till infinity (*although that is not reasonable*).

```html
<div class="splitcontent">
  <div class="splitcontenleft size-7">
    <div class="splitcontent">
      <div class="splitcontenleft size-7">
      </div>
      <div class="splitcontentright size-3">
      </div>
    </div>
  </div>
  <div class="splitcontentright size-3">
  </div>
</div>

```

The last thing to add on account of responsiveness is the possibility to adjust the column sizes according to the size of the screen. For that, stylesheets of ER accommodate variations of size classes:` size-s`, `size-l`, `size-xl`.  

```html
<div class="splitcontent">
  <div class="splitcontenleft size-7 size-s-5 size-l-8 size-xl-9"></div>
  <div class="splitcontentright size-3 size-s-5 size-l-2 size-xl-1"></div>
</div>
```

 <!-- theme: warning -->
>When using these variations, remember that they respond to screen size and when used in sub-grids.
>
>Also, it is important to consider whether such a layout should apply to the ER only or also to Redmine in its default. Since Redmine does not know size classes, it will always render the columns at 50% width. In such multi-column layouts, the outcome is quite unpredictable.

### Tables

#### Lists

Lists are used to display lists of entities (e.g. projects, tasks, users …) with various attributes. The list of records is realized simply with the HTML table with` class="list"`.

```html
<table class="list">
  <thead>...</thead>
  <tbody>...</tbody>
</table>
```

#### Table with autoscroll

Both **Redmine** and **Easy Redmine** allow the customization of the attributes shown on the listed set of records. This can easily produce a case where the length of each row extends the space available to render the table.

Any table could be wrapped in a utility element with `class="autoscroll"`. Such element enables responsive features throughout the list. For example, a horizontal scrollbar, which appears whenever the list overflows the space given by the surrounding layout.

```html
<div class="autoscroll">
  <table class="list">...</table>
</div>
```

### Buttons

**Easy Redmine** defines three variations for both size and color. As for colors, it enhances the button with the sense of what ";comes after".

`class="button"` is a default one, with not much added value attached to it, just a button. 

`class="button-positive"`, means that whatever user does, he does it right, being it submitting the form or picking the primary action, i.e. creation of a new Task on a task list. 

`class="button-important"` signifies actions that require increased attention before taking the step, i.e. user probably wants to think twice before resetting the form, or deleting the contact.

```html
<span class="button">Normal button</span>
<span class="button-positive">Positive button</span>
<span class="button-important">Important button</span>
```

Similarly, the size of the button signifies the sheer importance of it.

`class="button-large"` symbolizes the most commonly used actions on a given page, be it adding new or editing selected records on the index page. Normal sized buttons represent the 2nd level actions that can be performed on the given page. Since breaking actions down to more levels would only complicate logic without many added benefits, class="button-mini" is meant for usage in inline context or narrow spaces.  

```html
<span class="button">Normal button</span>
<span class="button-large">Large button</span>
<span class="button-mini">Micro button</span>
```

There are exceptions, but each page should have exactly one `class="button-large button-important"` or `class="button-large button-positive"`. Multiple `class="button-positive"` or `class="button-important"` should be limited only to the necessary amount.

### Modules

Modules are the basic widgets that hold secondary but relevant information for a given page, like related tasks in task detail, or are the main building block of customisable page layouts. The base structure of the module is as follows:  

```html
<div class="module box">
  <div class="module-heading">
    <h>Heading...</h>
    <div class="module-heading-links">
      ... some actions ...
    </div>
  </div>
  <div class="module-content">
    ... module content ...
  </div>
</div>

```

Box a a class for backwards Redmine compatibility. It is highly recommended to use a helper class from Easy Redmine gem that takes care of all necessary syntax:

```html
 render_module_easy_box(id, heading, options = {}, &block)
```

### Tabs

Tabs are common interface pattern, to use them in ER follow this syntax:

```html
<div class="tabs">
  <ul>
    <li><a href="XXX"> Tab 1</a></li>
    <li><a href="XXX"> Tab 1</a></li>
    <li><a href="XXX"> Tab 1</a></li>
    <li><a href="XXX"> Tab 1</a></li>
    <li><a href="XXX"> Tab 1</a></li>
  </ul>
</div>

```

For better usability on small screens or if there are just too many tabs, you can use tab buttons to scroll through tabs:

```html
<div class="tabs">
  <ul> ... </ul>
  <div class="tabs-buttons" style="">
    <button class="tab-left" onclick="moveTabLeft(this);"></button>
    <button class="tab-right" onclick="moveTabRight2(this);"></button>
  </div>
</div>

```

### Tags

Tags are visual elements used to display saved filters and other fitting elements. To use them on their own, just add `class="tags"` to the parent containing links:
 
```html
<div class="tags">
  <a>I'm a tag</a>
  <a>I'm a tag</a>
  <a>I'm a tag</a>
</div>

```

### Alerts / Flashes

Alerts build upon basic Redmine syntax - being identified by class="flash". There are three types of notices: *notice, warning and error*.

```html
<div class="flash notice"> Notice ... </div>
<div class="flash warning"> Notice ... </div>
<div class="flash error"> Notice ... </div>

```

Optionally alerts have a closing element:

```html
<a href="#" onclick="$(this).closest('.flash').fadeOut(500, function(){$(this).remove()}); return false;" class="icon-close"></a>
```

### Icons

To add an icon to any HTML element, just add `class="icon-XXX"` where XXX is the icon name from the following list. It will be added as a content of :before element of the given element.

The list of all icons can be found [here](https://es.easyproject.com/easy_iconset).





















































