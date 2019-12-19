---
title: Creating a Basic Widget
layout: tutorial
---

# Creating a Basic Widget

Apostrophe comes with many widgets out of the box, but you can do much more by creating your own.

Here's a common case: you want to build your own sitemap. We'll create custom widgets to accomplish this. You'll start with a simple widget that adds a link, and then enhance it so that it adds links programmatically in the next section.

## Creating a Modules

Just about everything new you create in Apostrophe will be a "module." Project-specific modules live in `lib/modules`.

FOLDER TREE

{% hint style='warning' %}
Your module name **should not start with** `apostrophe`. That's reserved for our official modules. Modules almost always have plural names. The name of a module that provides widgets should end in `-widgets`.
{% endhint %}

There are five basic steps for creating any widget:

1. Create a folder for the module.

2. Register the widget in `app.js`.

3. Create an `index.js` for the widget.

4. Create the `view` folder and create the display template for the widget.

5. Add the widget to a template to be displayed.


## Example: `link`, the simplest widget

For you first widget, we'll start simple. First, create the folder, `index.js` and register the widget.

1\. In the terminal, from the project root, enter to create the module folder:

```bash
mkdir -p lib/modules/link-widgets
```

2\. Then register the module in `app.js` by adding the following to the `modules` object:


{% code-tabs %}
{% code-tabs-item title="app.js" %}
```javascript
  modules: {
    // ...,
    'link-widgets': {}
  }
```
{% endcode-tabs-item %}
{% endcode-tabs %}

3\. Now create an `index.js` in `lib/modules/link-widgets/` and put some code in there:

{% code-tabs %}
{% code-tabs-item title="lib/modules/link-widgets/index.js" %}
```javascript
module.exports = {
  extend: 'apostrophe-widgets',
  label: 'Link to Anywhere',
  addFields: [
    {
      name: 'url',
      type: 'url',
      label: 'URL',
      required: true
    },
    {
      name: 'label',
      type: 'string',
      label: 'Label',
      required: true
    }
  ]
};
```
{% endcode-tabs-item %}
{% endcode-tabs %}


{% hint style='info' %}
#### `index.js` code explanation

_"What does_ `extend` _mean here?"_ Our module is extending the `apostrophe-widgets` module, which provides almost all the code you need. Yes, `extend` is the correct spelling. Apostrophe uses [moog](https://npmjs.org/package/moog) to handle extending or "subclassing" other modules.

_"What other field types can I add?"_ The `apostrophe-schemas` module provides a powerful way to build forms and structure data with almost no work. You just pass an array of field definitions as the `addFields` option. You'll introduce the details gradually. But if you're in a hurry, check out the [schema guide](/tutorials/schema-guide/schema-guide.md).

_"What does the_ `name` _property do?"_ Each field needs to be stored in the widget under its own property name. Play around with `aposDocs.find().pretty()` in the mongodb shell to see what it looks like after you save the widget.
{% endhint %}


4\. Create the `lib/modules/link-widgets/views/` folder, and `widget.html` inside of it:

{% code-tabs %}
{% code-tabs-item title="lib/modules/link-widgets/views/widget.html" %}
```markup
<h4><a href="{{ data.widget.url }}">{{ data.widget.label }}</a></h4>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style='info' %}
_"Hey, don't You need to escape the label before you output it as HTML?"_ No, Nunjucks does it automatically. If you need to output content that is already valid, safe markup, you must use the `| safe` filter to output it without escaping.
{% endhint %}

5\. Add the following to the `main` block of our `lib/modules/apostrophe-pages/views/pages/home.html` to render the widget:

{% code-tabs %}
{% code-tabs-item title="lib/modules/apostrophe-pages/views/pages/home.html" %}
```markup
{{
  apos.area(data.page, 'navigation', {
    widgets: {
      link: {}
    }
  })
}}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

You've just created a content area in which only `link` widgets are allowed. Each one has a `url` field and a `label` field, and they are always output in the same style.

This widget is nice, but the links will break each time a page is moved around the site, and it's possible for users to mess up the formatting. Next you'll look out how to leverage joins for more dynamic links.

