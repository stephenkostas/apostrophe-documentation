---
title: Apostrophe Widgets
layout: tutorial
---

# Displaying Widgets

In order to display widgets, you first must define them in templates. A widget can be defined as a *singleton* which is a single widget displayed on a page, or the widget can be defined in an *area* where one or more widgets can be added to a page in an assigned space. A widget can be added to a page multiple times with different configurations, using different combinations of singletons and areas.

## Singleton

A singleton will display the selected widget on the page with the options you configured in the template.

## Area

An area can display any number of widgets, and it can display multiples of each time of widget assigned to the area. When you configure widgets to display in an area, and view it on a page, nothing will appear at first. A logged in editor or administrator will see a "+" button which opens a selection for adding any widgets that are configured for that area.

## Singleton and Area Controls

Areas and singletons both accept useful options for managing their controls. Sometimes you'd like to change the edit button, sometimes you'd like to disallow removing a singleton entirely and so on.

* `options.addLabel` provides text for the initial add button.
* `options.editLabel` provides text for an edit button.
* `options.controls.movable` is true by default, but can be set to false, to prevent a from being dragged out of the singleton.
* `options.controls.removable` can be set to "true" or "false" to determine whether the button to remove a widget is available
* `options.controls.position` can be set to `top-left`, `top-right`, `bottom-left` or `bottom-right`, to reposition the widget controls \(edit, drag and remove\) are positioned accordingly. This can be helpful if the default location \(`top-left`\) doesn't play well with your widget's design.

## Example: Rich Text Widget Singleton with Label

To add a Rich Text widget as a single in your template, add this to the main content block:

```markup
        {{ apos.singleton(data.page, 'textBlock', 'apostrophe-rich-text', {
          toolbar: [ 'Bold', 'Italic' ]
        }) }}
    ```

In this case, you're providing the Rich Text widget configured to provide bold and italic options on a toolbar.

## Example: Rich Text and Images Widget Area with Repositioned Controls





Here's a complete example:

```markup
{{ apos.singleton(data.page, 'logo', 'apostrophe-images', {
  limit: 1,
  minSize: [ 1200, 600 ],
  addLabel: 'Set the logo',
  editLabel: 'Change the logo',
  controls: {
    movable: false,
    removable: false,
    position: 'bottom-right'
  }
}) }}
```


## More Widgets

For more information on configuring some of the most common widgets, see the [Widget Configuration Quick Reference](/other/widget-quick-reference.md), or continue on to create a custom navigation widget.