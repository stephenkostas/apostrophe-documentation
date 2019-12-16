---
title: Widgets, Singletons, and Areas
layout: tutorial
---

# Widgets, Singletons, and Areas

Central to Apostrophe is the philosophy that editors can edit their content in context. In order for you to enable this functionality in your templates, Apostrophe provides `singleton` and `area` helpers. A widget can be defined as a *singleton* which is a single widget displayed on a page, or the widget can be defined in an *area* where one or more widgets can be added to a page in an assigned space. A widget can be added to a page multiple times with different configurations, using different combinations of singletons and areas.

## Singleton

A singleton will display the selected widget on the page with the options you configured in the template.

## Area

An area can display any number of widgets, and it can display multiples of each time of widget assigned to the area. When you configure widgets to display in an area, and view it on a page, nothing will appear at first. A logged in editor or administrator will see a "+" button which opens a selection for adding any widgets that are configured for that area.

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

Let's deconstruct the arguments we are passing here.

#### `data.page`

The page object you want to save the singleton to. In this case, we are editing content on the current page, so the content should be saved to that page.

#### `'textBlock'`

The name of the area. This denotes what property the singleton will be saved to in the page object. In this case, it would be saved to `data.page['textBlock']`.

#### `'apostrophe-rich-text'`

The widget type we want to allow in the singleton. In this case, we are using the standard rich text widget.

#### `{ toolbar: [ 'Bold', 'Italic' ] }`

An "options" object that allows us to pass type-specific options to the widget. Rich text widgets support a `toolbar` option.

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

With this, you've learned how to complete two of the most important tasks in any CMS: creating new pages and adding content to those pages. Next you'll learn how to connect pages with Navigation.
