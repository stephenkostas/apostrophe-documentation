# Custom Widgets Part 3: Enhancing Widgets with Browser-side Code

So far you've built your widgets entirely with server-side code. But sometimes you'll want to enhance them with JavaScript on the browser side.

Sure, you could just select elements in your widget markup with jQuery, but that's no good for a widget that was just added to an existing page. There's a better way. you'll create a widget player.

Let's say you want to offer some content in a collapsible "drawer." Clicking on the title of the drawer reveals an Apostrophe area with more information.

Your module's `index.js` file looks like this:

{% code-tabs %}
{% code-tabs-item title="lib/modules/drawer-widget/index.js" %}
```javascript
module.exports = {
  extend: 'apostrophe-widgets',
  label: 'Drawer',
  addFields: [
    {
      type: 'string',
      name: 'title',
      label: 'Title'
    },
    {
      type: 'area',
      name: 'content',
      label: 'Content',
      options: {
        widgets: {
          'apostrophe-rich-text': {
            toolbar: [ 'Bold', 'Italic' ]
          },
          'apostrophe-images': {}
        }
      }
    }
  ]
};
```
{% endcode-tabs-item %}
{% endcode-tabs %}

And your `widget.html` file looks like this:

{% code-tabs %}
{% code-tabs-item title="lib/modules/drawer-widgets/views/widget.html" %}
```javascript
<h4><a data-drawer-title class="drawer-title" href="#">{{ data.widget.title }}</a></h4>
<div data-drawer class="drawer-body">
  {{ apos.area(data.widget, 'content', { edit: false }) }}
</div>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Here you use `data.widget` where you would normally expect `data.page`. This allows access to areas nested inside the widget.

> Notice that nesting areas and singletons inside the templates of other widgets is allowed. You can use this feature to create widgets that give users flexible control over page layout, for instance a "two column" widget with two `apos.area` calls.

Now, in your default page template, let's create an area that allows a series of drawers to be created:

{% code-tabs %}
{% code-tabs-item title="lib/modules/apostrophe-pages/views/default.html" %}
```markup
{{
  apos.area(data.page, 'drawers', {
    widgets: {
      drawer: {}
    }
  })
}}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

And in `app.js`, don't forget to configure the widget:

{% code-tabs %}
{% code-tabs-item title="app.js" %}
```javascript
// in app.js
modules: {
  // other widgets, then...
  'drawer-widgets': {}
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Even if your widget doesn't require any options, you must configure it in `app.js` to `instantiate` it. This is how Apostrophe knows that you actually want to use this module directly. In many projects, some modules only exist to be extended by other modules.

So far, so good. you can create a whole column of drawer widgets and their titles and their content areas appear. But right now the "drawer" part is visible at all times.

First, you'll need to hide the content of the drawer by default. Let's push an `always.less` stylesheet specifically for this module.

In `index.js`, you'll extend the `pushAssets` method, which is already pushing JavaScript, to push a stylesheet as well:

{% code-tabs %}
{% code-tabs-item title="lib/modules/drawer-widgets/index.js" %}
```javascript
module.exports = {
  extend: 'apostrophe-widgets',
  addFields: [
    // ... see above ...
  ],
  construct: function(self, options) {
    var superPushAssets = self.pushAssets;
    self.pushAssets = function() {
      superPushAssets();
      self.pushAsset('stylesheet', 'always', { when: 'always' });
    };
  }
};
```
{% endcode-tabs-item %}
{% endcode-tabs %}

In Apostrophe modules, the `construct` function is called to add methods to the module. Here you are following the "super pattern," making a note of the original method you inherited from [apostrophe-widgets](/modules/apostrophe-widgets/README.md), creating your own replacement method, invoking the original from within it, and then pushing your own asset to the browser.

The [pushAsset method](/modules/apostrophe-module/README.md#push-asset) can push both stylesheets and scripts. The name `always` is a convention meaning "everyone sees this stylesheet, whether logged in or not." And you make sure of that by setting the `when` option to `always`.

Now you need to supply `always.less` in the right place: the `public/css` subdirectory of your module's directory.

{% code-tabs %}
{% code-tabs-item title="lib/modules/drawer-widgets/public/css/always.less" %}
```css
.drawer-title {
  padding: 2em 0;
  text-align: center;
}

.drawer-body {
  padding: 2em 0;
  display: none;
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

With these changes, your drawers are hidden. But you still need a way to toggle them open when the titles are clicked.

For that, you'll need an `always.js` file, in your `public/js` folder:

{% code-tabs %}
{% code-tabs-item title="lib/modules/drawer-widgets/public/js/always.js" %}
```javascript
// Example of a widget manager with a play method

apos.define('drawer-widgets', {
  extend: 'apostrophe-widgets',
  construct: function(self, options) {
    self.play = function($widget, data, options) {
      $widget.find('[data-drawer-title]').click(function() {
        $widget.find('[data-drawer]').toggle();
        // Stop bubbling and default behavior for jQuery event
        return false;
      });
    };
  }
});
```
{% endcode-tabs-item %}
{% endcode-tabs %}

What's happening in this code?

* You called `apos.define` to define a [moog type](/other/glossary.md#moog-type) for your "widget manager." A widget manager is an object that is responsible for directing everything related to widgets of that type in the browser. Think of it as the browser's half of your module.
* The first argument to `apos.define` is the name of your new type, which is the same as the name of your module. The second argument is an object that defines the type. Just like your `index.js` file on the server, it contains a `construct` function. That's because Apostrophe uses [moog](/other/glossary.md#moog-type) to manage object-oriented programming in both places. The only difference is that on the server, Apostrophe figures out what type is being defined automatically based on the module's name. Here in browser-land, it's up to you to call `apos.define`.
* The `extend` property indicates that you want to extend \("subclass" or "inherit from"\) the `apostrophe-widgets` type, which provides most of the plumbing for managing your widget. All you need to do is supply a `play` method.
* Inside `construct`, you attach a `play` method to the widget manager. Your `play` method accepts `$widget` \(a jQuery object referring to the widget's `div` element\), `data` \(an object containing the properties of your widget, like `title`\), and `options` \(an object containing options that were passed to the widget when `apos.area` or `apos.singleton` was called\).
* Apostrophe automatically calls the `play` method at appropriate times.
* The play function takes advantage of jQuery's [find method](https://api.jquery.com/find/) to locate the title and the drawer _inside the scope of this one widget._

_"Can't I just write some jQuery in a `$(function() { ... })` block and skip all this?"_ If you do, you forfeit the ability for your player to work for widgets that were just added to the page by the user, without refreshing the page. Requiring users to refresh the page is very 2005. You might even tease you about it.

Writing widget players that scope all of their jQuery selectors with `$widget.find()` helps you avoid the temptation to write code that will install the same handler twice, fail entirely for newly-added widgets, or become a problem later when you want to publish your module in npm.

## What's available in the browser?

Now is a good time to mention highlights of what you can access in the browser by default when working with Apostrophe:

* jQuery
* lodash \(version 3.x\)
* async \(version 1.x\)
* moment
* jquery.fileupload
* jquery.cookie

And as previously mentioned, you can use LESS in your stylesheets.

_"What if I want to use browserify, gulp, grunt, etc.?"_ Sure, go nuts. Just arrange your gulpfiles to build a file that is pushed as an asset by one of your modules.

We chose not to incorporate those frontend build tools into Apostrophe's core because the core set of features needed for good CMS-driven sites doesn't usually rise to that level of complexity. But if you need to build complex in-page JavaScript experiences, go for it.

