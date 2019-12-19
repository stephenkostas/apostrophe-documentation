# Using Joins and Adding User Choice

The [`link` widget from the previous section](custom-widgets.md) is cool, but if the URL of a page changes, the link breaks. And if the title changes, that is not reflected in the widget. Let's solve the problem by allowing the user to pick a page on the site instead. Then you can access information about that page programmatically.

## Example: using joins in widgets with `page-link` 

In the last example we created a simple widget manually to help you understand the components of a module, but in most cases you'll want to use the [`apostrophe-cli` README](https://github.com/apostrophecms/apostrophe-cli/blob/master/README.md). From now on, we'll use save ourselves the extra trouble by using the CLI.

1\. Create your new widget using the CLI:

```bash
apostrophe create-widget page-link-widgets
```
2\. Now add this new widget to the `modules` object in our app.js:

{% code-tabs %}
{% code-tabs-item title="app.js" %}
```javascript
  modules: {
    /// ...,
    'link-widgets': {},
    'page-link-widgets': {}
  }
```
{% endcode-tabs-item %}
{% endcode-tabs %}

3\. Then write your `lib/modules/page-link-widgets/index.js` to define the schema for the widget:

{% code-tabs %}
{% code-tabs-item title="lib/modules/page-link-widgets/index.js" %}
```javascript
module.exports = {
  extend: 'apostrophe-widgets',
  label: 'Link to a Page',
  addFields: [
    {
      name: '_page',
      type: 'joinByOne',
      withType: 'apostrophe-page',
      label: 'Page',
      required: true,
      idField: 'pageId'
    }
  ]
};
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style='info' %}
#### `index.js` code explanation

_"What do_ `type: 'joinByOne'` _and_ `idField: 'pageId` _do?\`_ you want this widget to remember a connection to another page. To do that, you use the `joinByOne` field type and ask Apostrophe to store the MongoDB `_id` of the other page in a `pageId` property of the widget.

_"Why does the_ `name` _start with a_ `_`_?" Joins get fetched every time this widget is loaded. The relationship is dynamic_. Properties that are dynamic and should not be stored back to MongoDB as part of this widget must start with a `_` \(underscore\). Apostrophe automatically ignores them when saving the widget in the database.
{% endhint %}

4\. Create `lib/modules/page-link-widgets/views/widget.html` and add the following code to it:

{% code-tabs %}
{% code-tabs-item title="lib/modules/page-link-widgets/views/widget.html" %}
```markup
<h4><a href="{{ data.widget._page._url }}">{{ data.widget._page.title }}</a></h4>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style="success" %}
_Whoa! So I can access the other page in my template?"_ Yep. You can access any property of the other page. You can even make `apos.area` and `apos.singleton` calls with the other page object.
{% endhint %}

5\. Add the new widget, to `home.html` enabling both our widgets:

{% code-tabs %}
{% code-tabs-item title="lib/modules/page-link-widgets/views/home.html" %}
```markup
{{
  apos.area(data.page, 'navigation', {
    widgets: {
      link: {},
      'page-link': {}
    }
  })
}}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style='info' %}
It is also possible to join with more than one type. You can learn more about using [joins in our field reference](/other/fileds/joinByOne.md), and see a more robust example of joins in action in the [pieces section](../editable-content-pages/README.md).
{% endhint %}

## Example: Passing options to widgets

You probably noticed that our widgets don't take any options yet. You can use options to do cool things in our templates. Let's add a simple one to choose between two presentation styles.

To do this:

1\. Access `data.options` for `page-link-widgets` in your `widget.html`:

{% code-tabs %}
{% code-tabs-item title="lib/modules/page-link-widgets/views/widget.html" %}
```markup
<h4 class="{{ 'special' if data.options.special }}"><a href="{{ data.widget._page._url }}">{{ data.widget._page.title }}</a></h4>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

2\. Then pass the option to the `apos.area` in `home.html`:

{% code-tabs %}
{% code-tabs-item title="lib/modules/page-link-widgets/views/home.html" %}
```markup
{{
  apos.area(data.page, 'navigation', {
    widgets: {
      link: {},
      'page-link': {
        special: true
      }
    }
  })
}}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Now all the page links in the area will get the special class.

## Example: Giving choices to the user

You can also create your own options using `data.widget`. Use this to give the user a choice by adding a `boolean` field to the schema for `page-link-widgets` in its `index.js`.

* Create a new field named "special" in `index.js`.

* Access the "special" field through `data.widget` in `widget.html`.

1\. Add the new section to your `index.js` to create the `special` field:

{% code-tabs %}
{% code-tabs-item title="lib/modules/page-link-widgets/index.js" %}
```javascript
module.exports = {
  extend: 'apostrophe-widgets',
  label: 'Link to a Page',
  addFields: [
    {
      name: '_page',
      type: 'joinByOne',
      withType: 'apostrophe-page',
      label: 'Page',
      required: true,
      idField: 'pageId'
    },
    //begin new section
    {
      name: 'special',
      label: 'Special',
      type: 'boolean'
    }
    //end new section
  ]
};
```
{% endcode-tabs-item %}
{% endcode-tabs %}


2\. Add the call to `data.widget` in your template:

{% code-tabs %}
{% code-tabs-item title="lib/modules/page-link-widgets/views/widget.html" %}

```markup
<h4 class="{{ 'special' if data.widget.special }}"><a href="{{ data.widget._page._url }}">{{ data.widget._page.title }}</a></h4>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Performance note: limiting joins

Having access to the entire page object is a neat trick, but it can be very slow. That page might have its own widgets which load a lot of information you don't care about in this situation. Multiply that by 20 links and it starts to impact performance.

Indeed, all you really care about here is the title and the URL. So let's fetch only that information.

You can rewrite `index.js` to speed up the code:

{% code-tabs %}
{% code-tabs-item title="lib/modules/page-link-widgets/index.js" %}
```javascript
module.exports = {
  extend: 'apostrophe-widgets',
  label: 'Link to a Page',
  addFields: [
    {
      name: '_page',
      type: 'joinByOne',
      withType: 'apostrophe-page',
      label: 'Page',
      required: true,
      idField: 'pageId',
      filters: {
        projection: {
          title: 1,
          _url: 1
        }
      }
    }
  ]
};
```
{% endcode-tabs-item %}
{% endcode-tabs %}

The new bit is the `filters` option. By specifying a `projection` filter, you can limit Apostrophe to loading just the `title` and `_url` properties. Apostrophe needs `_url` to figure out the URL of a page. It's almost always a good idea to limit the projection to the fields you care about.

`_url`_,_ `slug`_... what's the difference?_ For most sites, nothing. But for sites with a `prefix` option, the `_url` property might have a folder name prepended to it. And there are other ways to transform `_url` to suit your needs. So always remember to use it instead of `slug` when you output page URLs. And use `_url` in your projection to fetch all the properties Apostrophe knows might be involved in calculating the `_url` property of the page.

_What else can I do with_ `filters`_?_ That's an intermediate topic, but you can do anything that [cursor filter methods](/tutorials/advanced-development/database/cursors.md) can do.
