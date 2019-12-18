---
title: Apostrophe Widgets
layout: tutorial
---

# Widget Quick Reference

In this section you can find a quick reference with configuration options for some of the most commonly used widgets in Apostrophe.

## `'apostrophe-rich-text'`

The rich text widget provides a space for to enter text, with various options for formatting that text in a WYSIWYG editor based on CKEditor.

```markup
        {{ apos.singleton(data.page, 'textBlock', 'apostrophe-rich-text', {
          toolbar: [ 'Bold', 'Italic' ]
        }) }}
    ```

### `'textBlock'`

The name of the area. This denotes what property the singleton will be saved to in the page object. In this case, it would be saved to `data.page['textBlock']`.

### `toolbar`

`toolbar` determines which controls appear in the editor's toolbar. Here are the frequently used controls:

`Styles`, `Bold`, `Italic`, `Link`, `Unlink`, `Anchor`, `Table`, `BulletedList`, `Blockquote`, `Strike`, `Subscript`, `Superscript`, `Split`

### `styles`

`styles` specifies an array of valid CKEditor styles, which will appear on the "Style" dropdown menu if it is included in the `toolbar`. Each style has a `name` property and an `element` property. Most semantic HTML5 elements are allowed here.

## `apostrophe-images`

The `apostrophe-images` widget lets you add one or more images. If multiple images are added in a single widget, they are presented as a slideshow.

Here is an example with the popular options:

```markup
  {{ apos.singleton(data.page, 'heroPic', 'apostrophe-images', {
    minSize: [ 700, 350 ],
    aspectRatio: [ 2, 1 ],
    limit: 1,
    size: 'full'
  }) }}
```

### `minSize`

`minSize` ensures the user can't pick images smaller than the given dimensions `[ width, height ]`.

### `aspectRatio`

`aspectRatio` will crop the images to display the largest portion that has the given aspect ratio `[ x, y ]`, throwing away some of the image if necessary. If the user clicks the cropping button while picking the image, the cropping controls will also enforce the aspect ratio.

### `limit`

`limit` prevents the user from selecting more than the given number of images. A `limit` of `1` is appropriate where you don't want a slideshow treatment.

### `size`

`size` controls the size of the image that gets displayed. To conserve bandwidth Apostrophe never sends the original image by default. Apostrophe resizes your image to the following sizes by default, always preserving the aspect ratio:

* `max`: no larger than 1600x1600.
* `full`: no larger than 1140x1140.
* `two-thirds`: no larger than 760x760.
* `one-half`: no larger than 570x700.
* `one-third`: no larger than 380x700.
* `one-sixth`: no larger than 190x350.
* `original`: the original file \(WARNING: educate your users about the downsides of sending huge files to browsers over 3G\).

If you don't specify a size, the `full` size is displayed.

You'll talk about adding custom image sizes in [`apostrophe-attachments`](/modules/apostrophe-attachments/README.md#addimagesizes) in a later tutorial.

## `apostrophe-files`

The `apostrophe-files` widget lets you add download links to access various documents, such as PDFs or Word files. If multiple files are added in a single widget, they are presented as a list.

Add this below the `apos.area` to add a simple `apostrophe-files` widget to the page:


{% code-tabs %}
{% code-tabs-item title="lib/modules/apostrophe-pages/views/home.html" %}
```markup
  {{ apos.singleton(data.page, 'resume', 'apostrophe-files', {
    limit: 1
  }) }}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### `limit`

`limit` prevents the user from selecting more than the given number of files.

### More about files and Apostrophe

Users editing these widgets have the option of uploading files or reusing them. By default the office-oriented file types accepted by Apostrophe are:

`[ 'txt', 'rtf', 'pdf', 'xls', 'ppt', 'doc', 'pptx', 'sldx', 'ppsx', 'potx', 'xlsx', 'xltx', 'csv', 'docx', 'dotx' ]`

In addition to GIF, JPEG and PNG files.

This can be adjusted by configuring the [apostrophe-attachments](/modules/apostrophe-attachments/README.md) module.

## `apostrophe-html`

There is an `apostrophe-html` widget. It allows users to paste raw HTML into your site. Don't use it. It tends to have a negative impact on well-designed sites.

But if you must turn it on, educate your end users to use it very sparingly, and never to use it in ways that wreck your beautiful, mobile-friendly design. Because nobody wants that.

It doesn't take any options.

## Singleton and Area Controls

Areas and singletons both have a number of options for configuring their controls. Sometimes you'd like to change the edit button, sometimes you'd like to disallow removing a singleton entirely and so on.

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