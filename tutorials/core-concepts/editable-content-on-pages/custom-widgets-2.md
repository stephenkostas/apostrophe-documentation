# Custom Widgets Part 2: Using Joins and Adding User Choice

The [`link` widget from the previous section](custom-widgets.md) is cool, but if the URL of a page changes, the link breaks. And if the title changes, that is not reflected in the widget. Let's solve the problem by allowing the user to pick a page on the site instead. Then you can access information about that page programmatically.

## Example: using joins in widgets with `page-link` 

1\. Make another module and its views folder, this time in just step:

```bash
mkdir -p lib/modules/page-link-widgets/views
```

{% hint style='info' %}
While it's good to get some experience making all the folders and files yourself, the `apostrophe-cli` pacakage that you installed earlier can easily create basic module structures for you from templates. You can learn more in the [`apostrophe-cli` README](https://github.com/apostrophecms/apostrophe-cli/blob/master/README.md).
{% endhint %}

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

3\. And then write `lib/modules/page-link-widgets/index.js`:

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
It is also possible to join with more than one type. And once you check out [pieces](/tutorials/core-concepts/reusable-content-pieces/reusable-content-with-pieces.md), the benefit of doing so will be clear. To do that, set `withType` to an array of type names, which may include `apostrophe-pages`. The user is then able to use a tabbed interface to select items of several types for the same join. These "polymorphic joins" are primarily intended for navigation widgets like this one.
{% endhint %}

## Example: Passing options to widgets

You probably noticed that our widgets don't take any options yet. You can use options to do cool things in our templates. Let's add a simple one to choose between two presentation styles.

To do this:

* First, in your `widget.html`, access the `data.options` template for `page-link-widgets`.

* Then, in `home.html`, pass the option in the `apos.area` call.

1\. Add `data.options` in `widget.html`:

{% code-tabs %}
{% code-tabs-item title="lib/modules/page-link-widgets/views/widget.html" %}
```markup
<h4 class="{{ 'special' if data.options.special }}"><a href="{{ data.widget._page._url }}">{{ data.widget._page.title }}</a></h4>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

2\. Then pass the option  call in `home.html`:

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

* Create a `special` field in `index.js`.

* Access the `special` field through `data.widget` in `widget.html`.

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

{% hint style='info' %}
`data.widget` contains the form fields the user can edit. `data.options` contains the options passed to `apos.area` or `apos.singleton` by the frontend developer.
{% endhint %}

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

**Watch out for reverse joins! If you have** [**reverse joins**](/tutorials/schema-guide/schema-guide.md) **and your widget doesn't need them,** the `projection` filter can't help you avoid loading them, because they are loaded from "the other side" \(the ids are stored with the documents linking _to_ your documents\). Instead, use the `joins` filter, and specify an array of join field names your widget actually needs — if any.

_What else can I do with_ `filters`_?_ That's an intermediate topic, but you can do anything that [cursor filter methods](/tutorials/advanced-development/database/cursors.md) can do.
