---
title: Widgets, Singletons, and Areas
layout: tutorial
---

# Widgets, Singletons, and Areas

Central to Apostrophe is the philosophy that editors can edit their content in context. In Apostrophe, we do this with **widgets**. A widget is a content block that you add to a page. You can add widgets to your template using **singletons** or **areas**. 

**Singleton**
: A singleton displays a single widget. The widget and its options are defined in the page template.

**Area**
: An area can display any number of widgets, and it can display multiples of each type of widget assigned to the area. When you configure widgets to display in an area, and view it on a page, nothing will appear at first. When logged in as an editor or administrator you will see a plus ("+") button which opens a menu for adding any widgets that are configured for that area.

### Example: Creating a Rich Text Editor as a Singleton

If you're following along, you created a `default.html` template in the previous article. As an example, we'll add a rich text singleton to your `default.html` template. If you're not following along, you can use `home.html` or another file in your local project.

1. Open `default.html`.

2. Replace the `main` block with this: 

{% code-tabs %}
{% code-tabs-item title="lib/modules/apostrophe-pages/views/default.html" %}
    ```markup
    {% block main %}
      <div class="main-content">
        {{ apos.singleton(data.page, 'textBlock', 'apostrophe-rich-text', {
          toolbar: [ 'Bold', 'Italic' ]
        }) }}
      </div>
    {% endblock %}
    ```
{% endcode-tabs-item %}
{% endcode-tabs %}

If we restart our server and refresh our site's home page while logged in, we'll see a gray block with a button prompting us to add rich text. Once you start adding text, your edits save automatically. **There is no "save" button because you don't need one.** All edits are saved in the background.

![](/.gitbook/assets/boilerplate_singleton.png)

### Example: Creating an Area with Multiple Widgets

To see this in action, add an `apos.area` to a page template.

1. Open `default.html`

2. Add this to `default.html` below the Singleton you just added inside the main `div`:

{% code-tabs %}
{% code-tabs-item title="lib/modules/apostrophe-pages/views/default.html" %}
    ```markup
    {{ apos.area(data.page, 'body', {
      widgets: {
        'apostrophe-rich-text': {
          toolbar: [ 'Bold', 'Italic' ]
        },
        'apostrophe-images': {
          size: 'full'
        }
      }
    }) }}
    ```
{% endcode-tabs-item %}
{% endcode-tabs %}

The first two arguments for this helper work the same way as for `apos.singleton`. The third option is an options object. We can see that that options object supports a `widgets` option, which takes a key-value map of available widget names and their respective options.

{% hint style='info' %}
Not sure how to add a second widget? After you're finished editing rich text, click anywhere outside of the rich text widget. Then hover over it and you'll see the "+" signs in each position where you're allowed to add a new widget.
{% endhint %}

## Singleton and Area Controls

Areas and singletons both accept useful options for managing their controls. Sometimes you'd like to change the edit button, sometimes you'd like to disallow removing a singleton entirely and so on.

* `options.addLabel` provides text for the initial add button.
* `options.editLabel` provides text for an edit button.
* `options.controls.movable` is true by default, but can be set to false, to prevent a from being dragged out of the singleton.
* `options.controls.removable` can be set to "true" or "false" to determine whether the button to remove a widget is available
* `options.controls.position` can be set to `top-left`, `top-right`, `bottom-left` or `bottom-right`, to reposition the widget controls \(edit, drag and remove\) are positioned accordingly. This can be helpful if the default location \(`top-left`\) doesn't play well with your widget's design.

### Example: Rich Text Widget Singleton with Label

Here is an example Rich Text widget singleton configured to provide bold and italic options on a toolbar. The `add` button will have a label that says "Add a description."


```markup
        {{ apos.singleton(data.page, 'textBlock', 'apostrophe-rich-text', {
          toolbar: [ 'Bold', 'Italic' ],
          addLabel: 'Add a description.'
        }) }}
    ```


### Example: Rich Text and Images Widget Area with Repositioned Controls

Here is an example area with two widgets. In this configuration:

* The Images Widget
  
  * Can only be added once.

  * Specifies that the image added must be 1200x600 or larger

  * The "edit" button has the label "Change the Logo"

  * The controls cannot be moved or removed, and are positioned in the bottom right

* The Rich Text Widget

  * Has a toolbar with the styles, bold, italic, link, and unlink options

  * Provides Heading, Subheading, and Paragraph styles 


```markup
{{ apos.area(data.page, 'body', {
      widgets: {
        'apostrophe-images': {
          limit: 1,
          minSize: [ 1200, 600 ],
          
          editLabel: 'Change the logo',
          controls: {
            movable: false,
            removable: false,
            position: 'bottom-right'
        }
        'apostrophe-rich-text': {
          toolbar: [ 'Styles', 'Bold', 'Italic', 'Link', 'Unlink' ],
          styles: [
            { name: 'Heading', element: 'h3' },
            { name: 'Subheading', element: 'h4' },
            { name: 'Paragraph', element: 'p' }
          ]
        }
}}
```

## More Widgets

For more information on configuring some of the most common widgets, see the [Widget Configuration Quick Reference](/other/widget-quick-reference.md).

With this, you've learned how to complete two of the most important tasks in any CMS: creating new pages and adding content to those pages. Next you'll learn how to connect pages with Navigation.
