---
title: Apostrophe Widgets
layout: tutorial
---

# Displaying Widgets

In order to display widgets, you first must define them in templates. [Remember from the previous chapter](../pages-and-navigation/widgets-singletons-and-areas.md) that widgets can be displayed as **singletons** or in **areas**. A singleton displays a single widget, while an area can display multiple widgets of different types.

## Singleton and Area Controls

Areas and singletons both accept useful options for managing their controls. Sometimes you'd like to change the edit button, sometimes you'd like to disallow removing a singleton entirely and so on.

* `options.addLabel` provides text for the initial add button.
* `options.editLabel` provides text for an edit button.
* `options.controls.movable` is true by default, but can be set to false, to prevent a from being dragged out of the singleton.
* `options.controls.removable` can be set to "true" or "false" to determine whether the button to remove a widget is available
* `options.controls.position` can be set to `top-left`, `top-right`, `bottom-left` or `bottom-right`, to reposition the widget controls \(edit, drag and remove\) are positioned accordingly. This can be helpful if the default location \(`top-left`\) doesn't play well with your widget's design.

## Example: Rich Text Widget Singleton with Label

Here is an example Rich Text widget singleton configured to provide bold and italic options on a toolbar. The `add` button will have a label that says "Add a description."


```markup
        {{ apos.singleton(data.page, 'textBlock', 'apostrophe-rich-text', {
          toolbar: [ 'Bold', 'Italic' ],
          addLabel: 'Add a description.'
        }) }}
    ```


## Example: Rich Text and Images Widget Area with Repositioned Controls

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

For more information on configuring some of the most common widgets, see the [Widget Configuration Quick Reference](/other/widget-quick-reference.md), or continue on to create a custom navigation widget.