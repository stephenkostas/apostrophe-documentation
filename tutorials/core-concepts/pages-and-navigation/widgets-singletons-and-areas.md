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

## Example: Creating a Rich Text Editor as a Singleton

To add a widget to a page template, you first need to have [created a page template](page-templates.md). You can use the `default.html` template from the previous article, the default `home.html`, or another template file you created.

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

Refresh a page associated with the template (make sure you're logged in), and you'll see a gray block with a button prompting you to add rich text. Once you start adding text, your edits save automatically.

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

## More Widgets

For more information on configuring some of the most common widgets, see the [Widget Configuration Quick Reference](/other/standard-widgets.md).

