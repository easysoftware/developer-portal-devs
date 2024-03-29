# Form Structure Guides

## Structure

Form consists of fields, groups and pages. Page contains groups and individual fields. Group groups multiple fields
together.
Pages allow higher level of composition, allowing to present data as separate chunks, allowing navigation between them.
This should be used to present extremely large forms.
Displaying only five to seven input fields at a given time is a common practice. The navigation between pages is done at
presentation level, all form data on pages are available and stored i form state. Pages can both be used to create
“wizard” experience or to be presented as different form sections.
To improve navigation between pages use Table of Contents that enables transition between pages and also indicates form
completion progress.
Form sidebar is used to present form summary usually across pages.

### Basic structure

Whole form is marked with the class `form` is labeled by a title with a class `form__title`, has a content with a
class `form__content` and can have left or right sidebar `form__side--left` or `form__side--right`. Form ends
with `form__actions` action bar that holds button controls of the form.

```html

<div class="form">
  <h2 class="form__title">Title</h2>
  <div class="form__side--left">...</div>
  <div class="form__content">...</div>
  <div class="form__side--right">...</div>
  <div class="form__actions">...</div>
</div>
```

### Content Structure

Form content can either hold `form__field`, `form__group` or `form__page`

#### Form pages

Pages are used to chunk content of large forms down into human processable pieces as long forms tend to be overwhelming.
Contents of the page are wrapped in `form__page-content` and page can have `form__page-heading`.

```html

<div class="form__content">
  <div class="form__page">
    <fieldset class="form__page-content">
      <legend class="form__page-heading">...</legend>
      ...
    </fieldset>
  </div>
</div>
```

Pages can contain either `form__field` or `form__group`

#### Form Groups

Groups are chucks of `form__field`s that have a similar function, usually are appearing together, but are not so
numerous to become a page. Groups preferably have title `form__group-title`

```html

<fieldset class="form__group">
  <legend class="form__group-title">...</legend>
  ...
</fieldset>
```

Groups can contain only `form__field`s

#### Form fields

Form fields are basic building blocks of form and contain individual input fields. It is important to note
that `form__field` can consist of multiple inputs but has only one label

```html
<p class="form__field">
  <label class="form__field-label">...</label>
  ...
</p>
```

Forms use 12 columns grid for layout a are separated in 3 major zones: Table of Contents, Form Body and Form Sidebar.
TOC and Sidebar are used when
appropriate especially for long or multi-page forms. Small forms use only 6 colum grid of Form Body.
Minimal span of individual field is 2 columns so it allows for composition of full width, 50:50, 33:33:33, 33:66, 66:33
fields in one row.
Classes available to do so
are: `form__field--full` `form__field--half` `form__field--third` `form__field--two-thirds` `form__field--sixth`.
Without any class the field behaves like `form__field--half` and tries to be self adjusting - filling any space left.

```html
<p class="form__field form__field--full | form__field--half | form__field--third | ....">
  <label class="form__field-label">...</label>
  ...
</p>
```

#### Form action buttons

PRIMARY buttons are on the **LEFT** followed by a SECONDARY button and then a NEGATIVE button ("Close" || "Cancel" ||
etc.)

Special cases:

1. In _standalone confirm dialog_, PRIMARY and NEGATIVE buttons are switched
2. In _wizard-style dialog_, PRIMARY and NEGATIVE buttons are also switched ("Next" button is on the **RIGHT** side)

-> [Visual representation of the buttons in Figma](https://www.figma.com/proto/HYcYyhZFwwNfwgx94TfBsS/UI-Style-Guide?page-id=0%3A1&type=design&node-id=49-104&viewport=-1023%2C-964%2C0.35&t=FjYlLcwpLpjrVMlq-1&scaling=min-zoom)

### Sidebar Structure

There are multiple positions which can be occupied within a sidebar, influencing how it's content will
behave. `form__side-content` is a default one and will scroll away with the rest of the form. Others are sticky and will
follow the user when scrolling. `form__side-content-top` `form__side-content-main` `form__side-content-footer` note that
if you want anything to stick to bottom, `form__side-content-main` and `form__side-footer` are mandatory

```html

<div class="form__side--right | form__side--left">
  <div class="form__side-content">
    ... I will just scroll away ...
  </div>
  <div class="form__side-content form__side-content--sticky">
    <div class="form__side-content-top">
      ... Im a going to stick to the top ...
    </div>
    <div class="form__side-content-main">
      ... IM floating in the middle ...
    </div>
    <div class="form__side-content-footer">
      ... Im sticking to the bottom ...
    </div>
  </div>
</div>
```
